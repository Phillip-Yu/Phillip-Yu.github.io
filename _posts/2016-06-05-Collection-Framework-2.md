---
layout: post
title: Java集合框架
date: 2016-6-3
categories: blog
tags:
  - Java
  - Collection
  - Iterator
description: 'Collection接口,以及迭代器'
---

# Collection框架(二)

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Collection框架(二)](#collection框架二)
	- [List集合](#list集合)
		- [ArrayList](#arraylist)
			- [底层结构](#底层结构)
			- [ArrayList遍历](#arraylist遍历)
				- [for语句循环遍历](#for语句循环遍历)
				- [foreach进行遍历](#foreach进行遍历)
				- [迭代器进行遍历](#迭代器进行遍历)

<!-- /TOC -->

 ![集合框架继承关系图](/img/collectionFramework.png)

## List集合

```
List (序列)
  |——ArrayList
  |——Vector(已被替换掉，不再使用了)
  |——LinkedList(链表)
```

### ArrayList

具有优势查询的特点，最常用的集合种类之一。

#### 底层结构

1. ArrayList 实际上是通过一个数组去保存数据的。当我们构造ArrayList时；若使用默认构造函数，则ArrayList的默认容量大小是10。
2. 当ArrayList容量不足以容纳全部元素时，ArrayList会重新设置容量：新的容量="(原始容量x3)/2 + 1"。
3. ArrayList的克隆函数，即是将全部元素克隆到一个数组中。
4. ArrayList实现java.io.Serializable的方式。当写入到输出流时，先写入"容量"，再依次写入"每一个元素"；当读出输入流时，先读取"容量"，再依次读取"每一个元素"。

#### ArrayList遍历

ArrayList支持三种遍历方式。

##### for语句循环遍历

通过for循环通过索引角标进行遍历。

```java
ArrayList<Integer> arrayList = new ArrayList<>();

for (int i = 0 ; i < arrayList.size() ; i++ ) {
    ArrayList.get(i);
}
```

##### foreach进行遍历

通过foreach语句进行遍历。实际上使用了迭代器进行遍历元素。

```java
ArrayList<Integer> arrayList = new ArrayList<>();

for ( int element : ArrayList) {
    element;
}
```

##### 迭代器进行遍历

使用Iterator迭代ArrayList集合。

```java
ArrayList<Integer> arrayList = new ArrayList<>();

Iterator<Integer> iterator = arrayList.Iterator;
while(Iterator.hasNext()){
    iterator.next();
}
```
