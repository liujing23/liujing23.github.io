---
title: Mysql使用技巧
date: 2024-02-28 09:57:26
tags: [Mysql, JAVA]
categories:
- [技术, JAVA]
---

# 一.内置函数

1.isnull(exper) 判断exper是否为空，是则返回1，否则返回0

2.ifnull(exper1,exper2)判断exper1是否为空，是则用exper2代替

3.nullif(exper1,exper2)如果expr1= expr2 成立，那么返回值为NULL，否则返回值为  expr1。



limit num offset num 

如果没有，什么都不返回，如果要求返回null，则加个临时表



存储过程

查找第n高的薪水

```mysql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
DECLARE M INT; 
SET M = N-1;
  RETURN (
      # Write your MySQL query statement below.
        SELECT DISTINCT salary
      FROM Employee
      ORDER BY salary DESC
      LIMIT M, 1
 );
END
```

