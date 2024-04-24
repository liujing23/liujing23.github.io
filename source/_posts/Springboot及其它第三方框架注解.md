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
