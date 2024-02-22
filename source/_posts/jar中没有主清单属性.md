---
title: jar中没有主清单属性
date: 2023-12-12 11:30:03
tags: [bug, JAVA, maven]
categories:
- [技术, JAVA, 实践]
---

问题：cmd窗口，运行jar包时，出现如下报错

![jar中没有主清单属性](https://raw.githubusercontent.com/liujing23/FigureBed/main/blog/img/jar中没有主清单属性.png)

解决方法：

```
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
```

repackage：多打了源码Jar包，并将其他代码和环境加入原来jar包（其中加入了主启动类信息，解决了此次报错）
