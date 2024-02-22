---
title: Java数据结构LinkedList，ArrayList，HashMap，TreeMap等集合的类实现
date: 2022-01-05 09:40:52
tags: [JAVA, 底层, 数据结构]
categories:
- [技术, JAVA]
---

# 一.概述

list集合

​	ArrayList（有序，可重复，底层结构数组）

​	LinkedList（有序，可重复，底层结构链表，双向链表）

Set集合

​	HashSet（无序，不可重复，底层HashMap） 

​	LinkedHashSet （有序，不可重复，底层链表 和 哈希表）

​	TreeSet（无序，不可重复，可排序，底层TreeMap）

Map集合：

​    HashMap（无序，不可重复，哈希表结构）

​    LinkedHashMap（有序，可重复，哈希表结构+链表结构，单向链表）

​    TreeMap（有序，不可重复，可排序，默认升序，底层结构红黑树）



# 二.map集合的使用

​		1、一般情况下，我们用的最多的是HashMap。HashMap里面存入的键值对在取出的时候是随机的，它根据键的HashCode值存储数据，根据键可以直接获取它的值，具有很快的访问速度。在Map 中**插入、删除和定位元素**，HashMap 是最好的选择。
　　2、TreeMap取出来的是排序后的键值对。但如果您要**按自然顺序或自定义顺序遍历键**，那么TreeMap会更好。
　　3、LinkedHashMap 是HashMap的一个子类，它内部有一个链表，保持Key插入的顺序。如果需要**输出的顺序和输入的相同**,那么用LinkedHashMap可以实现,它还可以按读取顺序来排列，像连接池中可以应用。

