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

# Collection框架(一)

![集合框架继承关系图](/img/collectionFramework.png)

## Collection接口

集合框架的顶层接口 ![集合框架继承关系图](/img/collectionExtends.jpg)

### 特点

1. `可重复，唯一。`
2. `有序、无序。顺序：指存和取的顺序。存取顺序一致 为有序。存取顺序不一致为无序。`

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

## 关于collection与泛型

集合框架为了能够存储多种的数据类型，使用了泛型计数。所以当元素进入集合中被存储起来时，元素的数据类型属性会被全部擦除，变为Object对象。 只有在开始时验证要存入元素的数据类型与要存储的是否一致，取出元素时正确的将取出的元素转换为存入前的数据类型。

```
Collection<Integer> collectionArray = new ArrayList<Integer>();
```

`因为泛型不支持基本数据类型int、char等等，所以基础类型的元素想要存入集合中需要将其转换为对应的基础数据类型的包装器类型`

八种基本数据类型的包装器类：

Header One | Header Two
:--------- | :---------
byte       | Byte
short      | Short
int        | Integer
long       | Long
float      | Float
double     | Double
char       | Character
boolean    | Boolean

`在使用时，基础类型的包装器类型具有自动装箱和自动拆箱的特性`

> 自动装箱：基本类型自动转为包装类（int >> Integer）

> 自动拆箱：包装类自动转为基本类型（Integer >> int）

所以基础类型的元素从集合中取出直接使用即可（自动拆箱），不需要做强制类型转换的操作。

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

ListIterater 提供了遍历时添加元素的方法add()。 List接口中remove()的应用。

ListIterator可以双向移动，使用hasPrevious()和previous()，但是只能在使用完hasNext()和next()之后（因为指针要到最后才能使用previous()向前移动）。

### foreach：增强for循环

```java
for(Object element : 容器名){
   element
   //element直接取出元素内容
   //实质依然是使用迭代器完成，受到迭代器的限制。
}
```
