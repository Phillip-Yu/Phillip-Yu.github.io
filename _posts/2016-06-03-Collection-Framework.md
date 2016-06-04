---
layout: post
title: 集合框架
date: 2016-6-3
categories: blog
tags:
  - Java
  - Collection
  - List
description: 实现了Collection接口的子接口List接口的类
---

# Collection框架(一)

![集合框架继承关系图](/img/collectionFramework.png)

## Collection接口

集合框架的顶层接口 ![集合框架继承关系图](/img/collectionExtends.jpg)

### 特点

`1\. 可重复，唯一。`

`2\. 有序、无序。顺序：指存和取的顺序。存取顺序一致 为有序。存取顺序不一致为无序。`

Collection接口结构图：

```
collection(集合)
    |——Set ()
    |    |——HashSet(哈希)
    |    |      |——LinkedHashSet
    |    |
    |    |——TreeSet
    |
    |——List (序列)
         |——ArrayList
         |——Vector(已被替换掉，不再使用了)
         |——LinkedList(链表)
```

Collection接口方法

```
boolean add() 添加
void clear() 清空
boolean contains(Object o) 包含
boolean IsEmpty() 判断是否为空
Object[] toArray() 变成数组
boolean remove(Object o) 删除
Iterator iterator() 迭代器
```

## 迭代器

关键词：`Iterator`

Iterator是一个接口，用于遍历collection的元素。 迭代器也称作轻量级对象：创建代价小。

迭代器，(也是一种设计模式)是一个对象，它的工作是遍历并选择序列中的对象，而客户端可以不必知道或关心序列的结构。

迭代器Iterator的使用（重点）,迭代器 用来迭代集合的类。

`Java的Iterator只能单向移动。`

```
boolean hasNext() 若还有元素，则返回true。
boolean nest() 迭代下一个元素
void remove() 从迭代器指向的collection中移除迭代器返回的最后一个元素
```

### 迭代器使用

> 迭代器不能在声明之后依然向集合中添加元素。会抛出"并发修改异常" ConcurrentModificationException 并发修改异常，在遍历集合的使用，使用迭代器，但是不能不告诉迭代器，操作里面的元素。 只要是在迭代中没有对元素的个数进行操作就可以执行。 remove 可以 但是不允许调用集合的remove()方法，只能调用迭代器当中的remove(),只能移除当前迭代目标。

### ListIterator

ListIterator可以双向移动，使用hasPrevious()和previous()，但是只能在使用完hasNext()和next()之后（因为指针要到最后才能使用previous()向前移动）。

### foreach：增强for循环

```java
for(数据类型 element ： 容器名){
 element（element直接取出元素内容）
//实质依然是使用迭代器完成，受到迭代器的限制。
}
```

## List序列

ListIterater 提供了遍历时添加元素的方法add()。 List接口中remove()的应用。
