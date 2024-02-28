---
title: 输入n打印菱形
date: 2021-01-30 11:36:50
tags: [算法, JAVA]
categories:
- [技术, JAVA]
---

![打印菱形](https://raw.githubusercontent.com/liujing23/FigureBed/main/blog/img/20240228113723.png)

```
class Solution {
    public int demo(int n) {
        for (int i = 0; i < n; i++) {
            for (int a = 0; a < n-i-1; a++){
                System.out.print(" ");
            }
            for (int b = 0; b < 2 * i + 1; b++) {
                System.out.print("*");
            }
            System.out.println();
        }

        for (int i = 1; i < n; i++){
            for (int a = 0; a < i; a++){
                System.out.print(" ");
            }
            for (int b = 0; b < 2*(n-i-1)+1; b++){
                System.out.print("*");
            }
            System.out.println();
        }
    }
}
```

