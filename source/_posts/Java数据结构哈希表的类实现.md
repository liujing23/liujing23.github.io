---
title: Java数据结构哈希表的类实现
date: 2021-12-25 10:26:16
tags: [JAVA, 底层, 数据结构]
categories:
- [技术, JAVA]
---

# 1.定义

哈希表是将一组关键字根据散列算法，确定存放位置的数据结构



# 2.用途

哈希表由于是算法计算出的存储位置，可以不用遍历，直接根据关键字获取vaule，大大提升查询效率

在HashMap，HashSet，LinkedHashSet ，LinkedHashMap中(带hash)都可以找到哈希表的实现



# 3.使用哈希表带来的问题

hash冲突，关键字不一样，但是f(key)一样

## 1）如何减少hash冲突

使用更好的散列算法

## 2）如何处理hash冲突

再哈希法即选取若干个不同的哈希函数，在产生哈希冲突的时候计算另一个哈希函数，直到不再发生冲突为止。



# 4.HashMap

HashMap在jdk1.7，底层实现是数组和链表。首先根据key的hashcode值进行散列算法，计算出在数组中的位置，如果数组中没有元素，则直接push。反之则要把该节点进行头插，将next属性赋值当前头节点，然后头节点赋值给数组。

HashMap的entry数据

```
Node(int hash, K key, V value, Node<K,V> next) {
    this.hash = hash;
    this.key = key;
    this.value = value;
    this.next = next;
}
```

## 4.1 HashMap的懒加载

在创建HashMap时，并不创建具体数组，只创建一个空对象{}，这就是HashMap的懒加载。

## 4.2 HashMap的扩容

HashMap的参数

```java
// 初始化数组容量，16
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
// 最大容量，2的30次方
static final int MAXIMUM_CAPACITY = 1 << 30;
// 加载因子
static final float DEFAULT_LOAD_FACTOR = 0.75f;
```

当哈希表中的条目数超出了加载因子与当前容量的乘积时，则要对该哈希表进行扩容、rehash操作（即重建内部数据结构），扩容后的哈希表将具有两倍的原容量。

加载因子为什么是0.75呢？作为一般规则，默认负载系数 （0.75） 提供良好的在时间和空间成本之间进行权衡。值越高，越少空间开销，但增加了查找成本（反映在 HashMap 类的大部分操作中，包括 get 和 put）。

具体扩容过程，一般不重新计算hashcode值，在不计算hashcode值的情况下，元素的位置可能变化，也可能不变，具体和hashcode值相关。如果权衡查询速率和空间效率不合适，则可以启动项目时，修改属性值，则重新生成hashcode值。
