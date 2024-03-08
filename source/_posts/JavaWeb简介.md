---
title: JavaWeb简介
date: 2022-02-23 09:25:21
tags: [Spring, JAVA]
categories:
- [技术, JAVA]
---

# 一.基本概念

## 1.定义

静态web：数据始终不会发生变化

动态web：数据在不同时间不同地点发生变化

在Java中，动态web资源开发的技术统称JavaWeb



## 2.web应用

一个web应用应该由多部分组成：

- html，css，js
- jsp,servlet
- Java程序
- jar包
- 配置文件



## 3.静态web

![静态web](https://raw.githubusercontent.com/liujing23/FigureBed/main/blog/img/20240229112731.png)

缺点：

1. 页面无法动态更新
2. 无法和数据库交互



## 4.动态web

![动态web](https://raw.githubusercontent.com/liujing23/FigureBed/main/blog/img/20240229111501.png)

缺点：

1. 动态web资源出现错误，重新编写后台程序，重新发布



# 二. web服务器

服务器是一个被动的操作，用来处理用户请求和返回响应，常见的有IIS和tomcat（实际运行JSP和Servlet）

## 1. 一个具体的servlet实现

### 1.1 继承servlet实现类HttpServlet，重写doGet方法，doPost方法

```java
package com.example;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

/**
 * @author user 
 */
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // get方法操作逻辑
        PrintWriter writer = resp.getWriter();
        writer.println("hello");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
```

### 1.2 在web.xml文件里注册servlet，并映射路径

原理：浏览器发送请求到web服务器，为了能找到需要的资源，在web服务中注册Servlet并配置访问路径

```java
  <servlet>
    <servlet-name>hello</servlet-name>
    <servlet-class>com.example.HelloServlet</servlet-class>
  </servlet>
  
  <servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/hello</url-pattern>
  </servlet-mapping>
```

### 1.3 配置tomcat

![配置tomcat](https://raw.githubusercontent.com/liujing23/FigureBed/main/blog/img/20240304094943.png)

需要在Depolyment中添加项目发布路径

### 1.4 发送请求，访问路径

![请求结果](https://raw.githubusercontent.com/liujing23/FigureBed/main/blog/img/20240304095259.png)

## 2. Servlet原理

Servlet由web服务器调用

![Servlet原理](https://raw.githubusercontent.com/liujing23/FigureBed/main/blog/img/20240304101917.png)

## 3. Servlet的

### 3.1 ServletContext

web容器启动的时候，会为每个web程序都创建一个对应的ServletContext对象，他代表当前的web应用

- 共享数据 -> session来做
- 请求转发（dispacher）-> request来做
- 读取资源文件（properties）
- 获取初始化参数

### 3.2 Cookie,Session

#### 3.2.1 会话

会话：用户打开一个浏览器，点击了很多超链接，访问多个web资源，关闭浏览器，这个过程称之为会话。

有状态会话

#### 3.2.2 保存会话的两种技术

Cookie

- 客户端技术

Session

- 服务器技术

#### 3.2.3 Cookie

![Cookie](https://raw.githubusercontent.com/liujing23/FigureBed/main/blog/img/20240307182027.png)

- 保存到本地目录下appdata
- 一个cookie只能保存一个信息
- 一个web站点可以给浏览器发送多个cookie
- 删除cookie：

1. 不设置过期时间，关闭浏览器，自动失效
2. 设置过期时间为0

```java
package com.example;

import javax.servlet.ServletException;
import javax.servlet.http.*;
import java.io.IOException;

/**
 * @author user liujing
 */
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Cookie[] cookies = req.getCookies();
        for (Cookie cookie : cookies){
            resp.getWriter().println(cookie.toString());
        }

        Cookie cookie = new Cookie("name", "name");
        resp.addCookie(cookie);
        
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }

}
```



#### 3.2.4 Session

![Session](https://raw.githubusercontent.com/liujing23/FigureBed/main/blog/img/20240307181453.png)

服务器给每个用户（浏览器）创建一个Session对象，浏览器不关，Session就存在

object类型，保存用户信息

```java
package com.example;

import javax.servlet.ServletException;
import javax.servlet.http.*;
import java.io.IOException;

/**
 * @author user 
 */
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        HttpSession session = req.getSession();
        session.setAttribute("name", "name");

        if (session.isNew()){
            resp.getWriter().print("session新创建" + session.getId());
        }else {
            resp.getWriter().print("session已经创建" + session.getId());
        }
        
        // 注销session，但是马上会生成一个新的
        session.invalidate();

    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
```

