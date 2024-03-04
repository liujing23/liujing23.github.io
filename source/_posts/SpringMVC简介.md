---
title: SpringMVC简介
date: 2024-02-28 16:30:33
tags:
---

# 一.MVC

## 1.定义

一种软件架构思想，将软件按照模型，视图，控制器来划分

M：Model，模型层，指工程中的JavaBean，作用是处理数据

V：View，视图层，指工程中的html或jsp等页面，作用是与用户进行交互，展示数据

C：Controller，控制层，指工程中的servlet，作用是接受请求和相应浏览器



## 2.工作流程

用户通过视图层发送请求到服务器，在服务器中请求被Controller接受，Controller调用相应的Model层处理请求，处理完毕将结果返回到Controller，Controller再根据请求处理的结果找到对应的View视图，渲染数据后最终响应给浏览器。



# 二.SpringMVC

springMVC是spring的一个子项目，是spring为表述层（前台页面+servlet）提供的一整套完备的解决方案。

