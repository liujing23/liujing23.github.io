---
title: maven初级
date: 2023-12-08 14:39:27
tags: [maven, JAVA, 项目管理]
categories:
- [技术, JAVA]
---

# 一.概述

maven的两大功能，依赖管理和构建项目



# 二.依赖管理

## 1.仓库

- 本地仓库：在setting.xml文件配置的，本机存放依赖的仓库
- 私服：公司搭建的局域网仓库，包含中央仓库+私有
- 远程仓库：中央仓库，由maven 官方管理，具体地址在setting.xml文件中配置

依赖的获取顺序，本地仓库 -》私服 -》 远程仓库

## 2.坐标

```
<dependency>
       <groupId>组织名</groupId>
       <artifactId>模块名</artifactId>
       <version>版本号</version>
</dependency>
```

## 3.依赖传递

### 1）原则

项目引用时，依赖也被引用

### 2）依赖传递

![image-20231208145517446](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20231208145517446.png)

### 3）解决依赖冲突

总原则：不是我用的就调

#### （1）scope标签

1.设定依赖使用范围，范围包括：

- 主程序范围内有效（main文件夹范围内）
- 测试程序范围内有效（test文件夹范围内）
- 是否参与打包（pacakage指令范围内）

|     scope     | 主代码 | 测试代码 | 打包 |    示例     |
| :-----------: | :----: | :------: | :--: | :---------: |
| compile(默认) |   Y    |    Y     |  Y   |    log4j    |
|     test      |        |    Y     |      |    junit    |
|   provided    |   Y    |    Y     |      | servlet_api |
|    runtime    |        |          |  Y   |    jdbc     |

2.依赖范围传递

| 横轴是直接依赖，纵轴是简介依赖 | compile | test | provided | runtime |
| ------------------------------ | ------- | ---- | -------- | ------- |
| compile                        | compile | test | provided | runtime |
| test                           |         |      |          |         |
| provided                       |         |      |          |         |
| runtime                        | runtime | test | provided | runtime |

3.示例

```
<dependency>
      <groupId>org.dromara.hutool</groupId>
      <artifactId>hutool-bom</artifactId>
      <version>${hutool.version}</version>
      <scope>import</scope>
</dependency>
```

#### （2）exclude标签

在引用项目使用，主动断开被引用项目

## 二.项目构建

根据插件来实现对项目生命周期的操作

- mvn compile 编译
- mvn clean 清理
- mvn test 测试
- mvn package 打包
- mvn install 安装到本地仓库
- mvn depoly 部署到私服

插件从上至下执行，和生命周期相关

除了生命周期插件，在pom文件中还可以引入其他插件，比如maven打包插件，tomcat插件等
