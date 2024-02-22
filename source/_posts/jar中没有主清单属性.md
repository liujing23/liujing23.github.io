---
title: jar中没有主清单属性
date: 2023-12-12 11:30:03
tags: [bug, JAVA, maven]
categories:
- [技术, JAVA, 实践]
---

问题：cmd窗口，运行jar包时，出现如下报错

![jar中没有主清单属性](https://raw.githubusercontent.com/liujing23/FigureBed/main/blog/img/jar%E4%B8%AD%E6%B2%A1%E6%9C%89%E4%B8%BB%E6%B8%85%E5%8D%95%E5%B1%9E%E6%80%A7.png)
<<<<<<< HEAD

)
=======
>>>>>>> 1e53a288686ade6f5657df5c1b138ce1abac08f2

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
