---
title: Springboot及其它第三方框架注解
date: 2024-04-24 11:01:40
tags: [Spring, JAVA]
categories:
- [技术, JAVA, 实践]
---

## @JsonFormat

从mongo里面读出来的时间类型，在实体类里面用Date接收，然后将bean传给前端的时候json中是Sun Apr 28 16:02:56 CST 2019这么显示的，但是前端要的是2019-4-28 16:02:56这样子的显示格式，而且mongo中的时间类型的时区转换到实体类里面还不一样。

主要就是解决实体类和json之间互相转换的时候时间类型格式和时区不一致的问题，当然也可以用在枚举中和集合里面。

```java
public class Param {
    @JsonFormat(shape = JsonFormat.Shape.STRING , pattern = "yyyy-MM-dd HH:mm:ss", timezone = "Asia/Shanghai")
    private Date date;

    public Date getDate() {
        return date;
    }

    public void setDate(Date date) {
        this.date = date;
    }
}
```



## @DateTimeFormat

与@JsonFormat类似，也是对时间格式进行规范的。主要处理前端时间类型与后端pojo对象中的成员变量进行数据绑定，所约束的时间格式并不会影响后端返回前端的时间类型数据格式。



## @Autowired

启用注解注入后，可以在属性、Setter 和构造器上使用自动装配。

### 一.@Autowired使用

#### 1.在属性上使用 @Autowired

使用 `@Autowired` 对属性进行注解。这样就不需要使用 Getter 和 Setter 了。

#### 2.在 Setter 方法上使用 @Autowired

#### 3.在构造函数上使用 @Autowired

#### 4.@Autowired 和可选依赖

在构建 Bean 时，`@Autowired` 依赖应该可用。否则，如果 Spring 无法解析用于装配的 Bean，它就会阻止 Spring 容器成功启动，并抛出 `NoSuchBeanDefinitionException` 异常：

```java
Caused by: org.springframework.beans.factory.NoSuchBeanDefinitionException: 
No qualifying bean of type [com.autowire.sample.FooDAO] found for dependency: 
expected at least 1 bean which qualifies as autowire candidate for this dependency. 
Dependency annotations: 
{@org.springframework.beans.factory.annotation.Autowired(required=true)}
```

要解决这个问题，需要声明一个 `required` 类型的 Bean：

```java
public class FooService {
    @Autowired(required = false)
    private FooDAO dataAccessor; 
}
```

### 二.自动装配消岐

默认情况下，Spring 按类型解析 `@Autowired` 依赖。如果容器中存在多个相同类型的 Bean，框架将抛出异常。

要解决这一冲突，需要明确告诉 Spring 要注入哪个 Bean。

#### 1.使用 @Qualifier 进行自动装配

```java
@Component("fooFormatter")
public class FooFormatter implements Formatter {
    public String format() {
        return "foo";
    }
}
@Component("barFormatter")
public class BarFormatter implements Formatter {
    public String format() {
        return "bar";
    }
}
```

此时由于有两种类型的bean对象，Spring会抛出`NoUniqueBeanDefinitionException` 异常，此时使用@Qualifier注解缩小实现范围来避免这种情况。有多个相同类型的bean，最好使用@Qualifier注解。

```java
public class FooService {
    @Autowired
    @Qualifier("fooFormatter")
    private Formatter formatter;
}
```

#### 2.根据 Bean 名称装配

Spring 使用 Bean 的名称作为默认 Qualifier value。它会检查容器，并查找与要自动装配的属性名称完全相同的 Bean 来进行装配。

因此，在如下示例中，Spring 将 `fooFormatter` 属性名与 `FooFormatter` 实现相匹配。因此，在构建 `FooService` 时，它会注入该特定实现：

```java
public class FooService {
 @Qualifier 
 private Formatter fooFormatter; 
}
```



## @Resource

@Resource注解默认通过byName方式注入，如果没有匹配则通过byType注入。**byName就是变量名去匹配bean的id属性，而byType则是变量类型去匹配bean的class属性**。

```java
// 1. 默认方式：byName
@Resource  
private UserService userDao; 

// 2. 指定byName
@Resource(name="userService")  
private UserService userService; 

// 3. 指定byType
@Resource(type=UserService.class)  
private UserService userService; 

// 4. 指定byName和byType
@Resource(name="userService",type=UserService.class)  
private UserService userService; 

```

1. 既没指定name属性，也没指定type属性：默认通过byName方式注入，如果byName匹配失败，则使用byType方式注入（也就是上面的那个例子）
2. 指定name属性：通过byName方式注入，把变量名和IOC容器中的id去匹配，匹配失败则报错
3. 指定type属性：通过byType方式注入，在IOC容器中匹配对应的类型，如果匹配不到或者匹配到多个则报错
4. 同时指定name属性和type属性：在IOC容器中匹配，名字和类型同时匹配则成功，否则失败
   
