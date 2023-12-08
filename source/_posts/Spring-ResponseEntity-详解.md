---
title: Spring ResponseEntity 详解
date: 2023-12-08 10:38:06
tags: [Spring, JAVA]
categories:
- [技术, JAVA]
---

Spring ResponseEntity 是一个类，它表示整个 HTTP 响应，包括状态码，标头和正文。我们可以使用它来完全配置 HTTP 响应。如果我们想使用它，我们必须从端点返回它；Spring 会处理其余的部分。

ResponseEntity 继承了 HttpEntity 类，HttpEntity 代表一个 HTTP 请求或者响应实体，其内部有两个成员变量：header 和 body，代表 HTTP 请求或响应的 header 和 body，其中的 body 是泛型的。

ResponseEntity 扩展了 HttpEntity 类，新增了 status 成员变量，这样，一个 ResponseEntity 基本可以代表完整的 HTTP 的请求或响应了。我们可以使用它来完全配置 HTTP 响应，包括状态码，标头和正文。

当我们在控制器层返回 ResponseEntity 对象时，Spring MVC 框架会使用 **HttpEntityMethodProcessor** 类来处理它。**HttpEntityMethodProcessor** 是 AbstractMessageConverterMethodProcessor 的一个子类，它专门处理返回值类型是 HttpEntity 或 ResponseEntity 的控制器方法。

HttpEntityMethodProcessor 的处理过程如下：

- 首先，检查返回值是否为空，如果为空，就直接返回。
- 然后，创建 ServletServerHttpRequest 和 ServletServerHttpResponse 对象，用于读取请求和写入响应。
- 接着，断言返回值是 HttpEntity 类型的，并将其强制转换为 HttpEntity 或 ResponseEntity 对象。
- 然后，获取输出消息的标头和实体标头，并将实体标头复制到输出标头中。
- 接着，判断返回值是否是 ResponseEntity 类型的，并获取其状态码，并设置到输出消息中。
- 最后，调用父类的 writeWithMessageConverters 方法，根据实体类型和请求内容协商选择合适的消息转换器，并将实体内容写入到输出消息中。

handleReturnValue 是HttpEntityMethodProcessor类的一部分，用来处理返回值类型是 HttpEntity 或 ResponseEntity 的控制器方法，源码说明如下：

```
public void handleReturnValue(@Nullable Object returnValue, MethodParameter returnType, ModelAndViewContainer mavContainer, NativeWebRequest webRequest) throws Exception {
        // 设置 mavContainer 的 requestHandled 属性为 true，表示请求已经被处理，不需要再渲染视图。
        mavContainer.setRequestHandled(true);
        if (returnValue != null) {
            // 创建 ServletServerHttpRequest 和 ServletServerHttpResponse 对象，用于读取请求和写入响应
            ServletServerHttpRequest inputMessage = this.createInputMessage(webRequest);
            ServletServerHttpResponse outputMessage = this.createOutputMessage(webRequest);
            // 断言返回值是 HttpEntity 类型的，并将其强制转换为 HttpEntity对象
            Assert.isInstanceOf(HttpEntity.class, returnValue);
            HttpEntity<?> responseEntity = (HttpEntity)returnValue;
            HttpHeaders outputHeaders = outputMessage.getHeaders();
            HttpHeaders entityHeaders = responseEntity.getHeaders();
            if (!entityHeaders.isEmpty()) {
                // 获取输出消息的标头和实体标头，并将实体标头复制到输出标头中。
                // 如果有 Vary 标头，它会特殊处理，避免重复或冲突
                entityHeaders.forEach((key, value) -> {
                    if ("Vary".equals(key) && outputHeaders.containsKey("Vary")) {
                        List<String> values = this.getVaryRequestHeadersToAdd(outputHeaders, entityHeaders);
                        if (!values.isEmpty()) {
                            outputHeaders.setVary(values);
                        }
                    } else {
                        outputHeaders.put(key, value);
                    }

                });
            }

            // 判断返回值是否是 ResponseEntity 类型的，并获取其状态码，并设置到输出消息中
            if (responseEntity instanceof ResponseEntity) {
                int returnStatus = ((ResponseEntity)responseEntity).getStatusCodeValue();
                outputMessage.getServletResponse().setStatus(returnStatus);
                // 根据状态码和请求方法进行一些判断。
                // 如果状态码是 200，并且请求方法是 GET 或 HEAD，并且资源没有被修改（根据 ETag 或 Last-Modified 标头），那么它会直接返回空响应。
                if (returnStatus == 200) {
                    HttpMethod method = inputMessage.getMethod();
                    if ((HttpMethod.GET.equals(method) || HttpMethod.HEAD.equals(method)) && this.isResourceNotModified(inputMessage, outputMessage)) {
                        outputMessage.flush();
                        return;
                    }
                } else if (returnStatus / 100 == 3) {
                    // 如果状态码是 3xx，并且有 location 标头，那么它会重定向到 location 的地址。
                    String location = outputHeaders.getFirst("location");
                    if (location != null) {
                        this.saveFlashAttributes(mavContainer, webRequest, location);
                    }
                }
            }

            this.writeWithMessageConverters(responseEntity.getBody(), returnType, inputMessage, outputMessage);
            outputMessage.flush();
        }
    }
```

**那么我们要如何使用ResponseEntity**？

Spring ResponseEntity 是一个泛型类型。因此，我们可以使用任何类型作为响应正文：

```
@GetMapping ("/hello")
ResponseEntity<String> hello() {
  return new ResponseEntity <> ("Hello World!", HttpStatus.OK);
}
```

由于我们以编程方式指定响应状态，因此我们可以根据不同的情况返回不同的状态码：

```
@GetMapping ("/age")
ResponseEntity<String> age(
  @RequestParam ("yearOfBirth") int yearOfBirth) {
  if (isInFuture (yearOfBirth)) {
    return new ResponseEntity <> ("年份不正确", HttpStatus.BAD_REQUEST);
  }
  return new ResponseEntity <> ("你的年龄是" + calculateAge (yearOfBirth), HttpStatus.OK);
}
```

此外，我们还可以设置 HTTP 标头：

```
@GetMapping ("/customHeader")
ResponseEntity<String> customHeader() {
  HttpHeaders headers = new HttpHeaders ();
  headers.add ("Custom-Header", "foo");
  return new ResponseEntity <> ("自定义头部", headers, HttpStatus.OK);
}
```

此外，ResponseEntity 还提供了两个嵌套的构建器接口：HeadersBuilder 和它的子接口 BodyBuilder。因此，我们可以通过 ResponseEntity 的静态方法访问它们的功能。

最简单的情况是具有正文和 HTTP 200 响应码的响应：

```
@GetMapping ("/hello")
ResponseEntity<String> hello() {
  return ResponseEntity.ok ("Hello World!");
}
```

对于最常见的 HTTP 状态码，我们得到静态方法：

```
BodyBuilder accepted();
BodyBuilder badRequest();
BodyBuilder created(java.net.URI location);
HeadersBuilder<?> noContent();
HeadersBuilder<?> notFound();
BodyBuilder ok();
```

另外，我们可以使用 BodyBuilder status(HttpStatus status) 和 BodyBuilder status(int status) 方法来设置任何 HTTP 状态。最后，使用 ResponseEntity BodyBuilder.body(T body) 我们可以设置 HTTP 响应正文：

```
@GetMapping ("/age")
ResponseEntity<String> age(@RequestParam ("yearOfBirth") int yearOfBirth) {
  if (isInFuture (yearOfBirth)) {
    return ResponseEntity.badRequest()
      .body("年份不正确");
  }
  return ResponseEntity.status(HttpStatus.OK)
    .body("你的年龄是 " + calculateAge (yearOfBirth));
}
```

### **ResponseEntity替代方案**

虽然ResponseEntity很强大，但我们不应该滥用它。在简单的情况下，有其他的选项可以满足我们的需求，而且它们会产生更清晰的代码。

### **@ResponseBody**

在传统的Spring MVC应用中，端点通常返回渲染的HTML页面。如果我们想要返回JSON或XML格式的数据，我们可以使用@ResponseBody注解：

```
@GetMapping("/hello")
@ResponseBody
public String hello() {
    return "Hello World!";
}
```

这样，Spring会自动将返回值转换为JSON或XML，并将其写入响应正文。

我们也可以使用@ResponseStatus注解来设置响应状态码：

```
@GetMapping("/age")
@ResponseBody
@ResponseStatus(HttpStatus.OK)
public String age(@RequestParam("yearOfBirth") int yearOfBirth) {
    if (isInFuture(yearOfBirth)) {
        throw new IllegalArgumentException("年份不正确");
    }
    return "你的年龄是" + calculateAge(yearOfBirth);
}
```

如果我们想要处理异常并返回不同的状态码，我们可以使用@ControllerAdvice和@ExceptionHandler注解：

```text
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(IllegalArgumentException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    @ResponseBody
    public String handleIllegalArgumentException(IllegalArgumentException ex) {
        return ex.getMessage();
    }
}
```

### **@RestController**

如果我们的控制器类只返回数据而不是视图，我们可以使用@RestController注解来替代@Controller注解。这样，我们就不需要在每个方法上添加@ResponseBody注解了：

```text
@RestController
public class HelloController {

    @GetMapping("/hello")
    public String hello() {
        return "Hello World!";
    }

    @GetMapping("/age")
    @ResponseStatus(HttpStatus.OK)
    public String age(@RequestParam("yearOfBirth") int yearOfBirth) {
        if (isInFuture(yearOfBirth)) {
            throw new IllegalArgumentException("年份不正确");
        }
        return "你的年龄是 " + calculateAge(yearOfBirth);
    }
}
```

## **ResponseEntity 和 @ResponseBody 的区别**

ResponseEntity 和 @ResponseBody 都是用于处理 HTTP 响应的，但是有一些不同点：

- **ResponseEntity** 是一个类，它表示整个 HTTP 响应，包括状态码，标头和正文。我们可以使用它来完全配置 HTTP 响应。如果我们想使用它，我们必须从端点返回它；Spring 会处理其余的部分。
- **@ResponseBody** 是一个注解，它告诉 Spring 将返回值转换为 JSON 并写入到响应体中。我们可以在方法或类上使用它，Spring 会自动选择合适的转换器。
- **ResponseEntity** 的优点是可以灵活地控制响应的各个方面，比如设置不同的状态码，标头和正文。它也可以用于一些特殊的场景，比如下载文件，需要设置 content-type 和特殊的状态（比如 206）。
- **@ResponseBody** 的优点是可以简化代码，让方法直接返回 JSON 结果，不需要构造 ResponseEntity 对象。它也可以和 @ResponseStatus 注解配合使用，来设置响应状态码。
- **ResponseEntity 的优先级高于 @ResponseBody**。如果返回值是 ResponseEntity 类型，那么 Spring 会直接使用它，不会检查有没有 @ResponseBody 注解。如果返回值不是 ResponseEntity 类型，那么 Spring 会检查有没有 @ResponseBody 注解，如果有就转换为 JSON。

因此，我们可以根据不同的需求和场景选择使用 ResponseEntity 或者 @ResponseBody。

对于大多数情况，我们可以使用 @ResponseBody 注解来直接返回 JSON 结果，或者使用 @ResponseStatus 注解来设置响应状态码。对于一些需要更多定制的情况，我们可以使用 ResponseEntity 来完全控制 HTTP 响应。

### **结语**

这篇文章我们主要介绍了Spring ResponseEntity的原理、使用及其替代方案，并对比了ResponseEntity 和 @ResponseBody。
