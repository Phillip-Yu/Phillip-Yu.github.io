---
layout: post
title: Java集合框架(二)
date: 2016-6-3
categories: blog
tags:
  - Java
  - List
  - ArrayList
description: List集合
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
			- [关于同步](#关于同步)
		- [LinkedList 链表](#linkedlist-链表)
			- [链表的基本介绍](#链表的基本介绍)
			- [链表的数据结构原理](#链表的数据结构原理)
			- [私有属性](#私有属性)
			- [LinkedList的构造方法](#linkedlist的构造方法)
			- [添加元素add()和addAll()](#添加元素add和addall)
				- [双向链表添加元素的原理](#双向链表添加元素的原理)
			- [清除数据clear()](#清除数据clear)
			- [数据包含 contains(Object o)](#数据包含-containsobject-o)
			- [删除数据remove()](#删除数据remove)
			- [数据获取get()](#数据获取get)
			- [数据复制clone()与toArray()](#数据复制clone与toarray)
				- [clone()](#clone)
				- [toArray()](#toarray)
				- [toArray(T[] a)](#toarrayt-a)
			- [遍历数据：Iterator()](#遍历数据iterator)
				- [ListItr](#listitr)
		- [关于LinkedList实现Stack(堆栈)和Queue(队列)](#关于linkedlist实现stack堆栈和queue队列)
			- [堆栈](#堆栈)

<!-- /TOC -->

 ![集合框架继承关系图](/img/collectionFramework.png)

## List集合

List框架图： ![List框架图](/img/20131105104229.jpg "List集合框架图")

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

#### 关于同步

ArrayList的最初设定是线程不安全的，Vector是线程安全的，但是Vector的效率特别低，所以被放弃了。

如果使用ArrayList.Synchronized方法返回的实例，那么就不用考虑线程同步的问题，这个实例本身就是线程安全的，实际上ArrayList内部实现了一个保证线程同步的内部类，ArrayList.Synchronized返回的就是这个类的实例，它里面的每个属性都是用了lock关键字来保证线程同步。

> 但是，使用这个方法（ArrayList.Synchronized）并不能保证枚举的同步，例如，一个线程正在删除或添加集合项，而另一个线程同时进行枚举，这时枚举将会抛出异常。所以，在枚举的时候，你必须明确使用 SyncRoot 锁定这个集合。

Hashtable与ArrayList关于线程安全性的使用方法类似。

### LinkedList 链表

LinkedList是由每个独立的数组通过分别存储前一个数组所在地址和后一个数组所在的地址形成的链状结构。

LinkedList 是非常好用的学习数据结构的工具。可以用来模拟二叉树（next记忆两个地址）。

LinkedList是采用链表的方式来实现List接口的,它本身有自己特定的方法，如: `addFirst()`,`addLast()`,`getFirst()`,`removeFirst()`等. 由于是采用链表实现的,因此在进行insert和remove动作时在效率上要比ArrayList要好得多!`适合用来实现Stack(堆栈)与Queue(队列),前者先进后出，后者是先进先出`.

#### 链表的基本介绍

LinkedList 是一个继承于AbstractSequentialList的双向链表。它也可以被当作堆栈、队列或双端队列进行操作。

LinkedList 实现 List 接口，能对它进行队列操作。

LinkedList 实现 Deque 接口，即能将LinkedList当作双端队列使用。

LinkedList 实现了Cloneable接口，即覆盖了函数clone()，能克隆。

LinkedList 实现java.io.Serializable接口，这意味着LinkedList支持序列化，能通过序列化去传输。

LinkedList 是非同步的。

#### 链表的数据结构原理

LinkedList底层数据结构是基于双向循环链表的，且头节点不存放元素。

![链表的数据结构](/img/050153010163922.jpg)

既然是双向链表，那么必定存在一种数据结构----我们可以称之为节点，节点实例保存业务数据，前一个节点的位置信息和后一个节点位置信息，如下图所示：

![链表的节点](/img/050101321255262.png)

#### 私有属性

LinkedList中之定义了两个属性：

```java
private transient Entry<E> header = new Entry<E>(null, null, null);
private transient int size = 0;
```

header是双向链表的头节点，它是双向链表节点所对应的类Entry的实例。Entry中包含成员变量： previous, next, element。其中，previous是该节点的上一个节点，next是该节点的下一个节点，element是该节点所包含的值。

size是双向链表中节点实例的个数。

首先来了解节点类Entry类的代码。

```java
private static class Entry<E> {
   E element;
    Entry<E> next;
    Entry<E> previous;

    Entry(E element, Entry<E> next, Entry<E> previous) {
        this.element = element;
        this.next = next;
        this.previous = previous;
   }
}
```

节点类很简单，element存放业务数据，previous与next分别存放前后节点的信息（在数据结构中我们通常称之为前后节点的指针）。

#### LinkedList的构造方法

```java
public LinkedList() {
    header.next = header.previous = header;
}
public LinkedList(Collection<? extends E> c) {
    this();
   addAll(c);
}
```

LinkedList提供了两个构造方法。

1. 第一个构造方法不接受参数，将header实例的previous和next全部指向header实例（注意，`这个是一个双向循环链表，如果不是循环链表，空链表的情况应该是header节点的前一节点和后一节点均为null`），这样整个链表其实就只有header一个节点，用于表示一个空的链表。执行完构造函数后，header实例自身形成一个闭环，如下图所示：

  ![空链表的闭环](/img/050104598139479.png "空链表的闭环")

2. 第二个构造方法接收一个Collection参数c，调用第一个构造方法构造一个空的链表，之后通过addAll将c中的元素全部添加到链表中。

#### 添加元素add()和addAll()

```java
public boolean addAll(Collection<? extends E> c) {
    return addAll(size, c);
}
// index参数指定collection中插入的第一个元素的位置
public boolean addAll(int index, Collection<? extends E> c) {
    // 插入位置超过了链表的长度或小于0，报IndexOutOfBoundsException异常
    if (index < 0 || index > size)
        throw new IndexOutOfBoundsException("Index: "+index+
                                                ", Size: "+size);
    Object[] a = c.toArray();
   int numNew = a.length;
   // 若需要插入的节点个数为0则返回false，表示没有插入元素
    if (numNew==0)
        return false;
    modCount++;//否则，插入对象，链表修改次数加1
    // 保存index处的节点。插入位置如果是size，则在头结点前面插入，否则在获取index处的节点插入
    Entry<E> successor = (index==size ? header : entry(index));
    // 获取前一个节点，插入时需要修改这个节点的next引用
    Entry<E> predecessor = successor.previous;
    // 按顺序将a数组中的第一个元素插入到index处，将之后的元素插在这个元素后面
    for (int i=0; i<numNew; i++) {
        // 结合Entry的构造方法，这条语句是插入操作，相当于C语言中链表中插入节点并修改指针
        Entry<E> e = new Entry<E>((E)a[i], successor, predecessor);
        // 插入节点后将前一节点的next指向当前节点，相当于修改前一节点的next指针
        predecessor.next = e;
        // 相当于C语言中成功插入元素后将指针向后移动一个位置以实现循环的功能
        predecessor = e;
  }
    // 插入元素前index处的元素链接到插入的Collection的最后一个节点
    successor.previous = predecessor;
    // 修改size
    size += numNew;
    return true;
}
```

构造方法中的调用了addAll(Collection<? extends E> c)方法，而在addAll(Collection<? extends E> c)方法中仅仅是将size当做index参数调用了addAll(int index,Collection<? extends E> c)方法。

```java
private Entry<E> entry(int index) {
        if (index < 0 || index >= size)
            throw new IndexOutOfBoundsException("Index: "+index+
                                                ", Size: "+size);
        Entry<E> e = header;
        // 根据这个判断决定从哪个方向遍历这个链表
        if (index < (size >> 1)) {
            for (int i = 0; i <= index; i++)
                e = e.next;
        } else {
            // 可以通过header节点向前遍历，说明这个一个循环双向链表，header的previous指向链表的最后一个节点，这也验证了构造方法中对于header节点的前后节点均指向自己的解释
            for (int i = size; i > index; i--)
                e = e.previous;
       }
        return e;
    }
```

##### 双向链表添加元素的原理

```java
// 将元素(E)添加到LinkedList中
     public boolean add(E e) {
         // 将节点(节点数据是e)添加到表头(header)之前。
         // 即，将节点添加到双向链表的末端。
         addBefore(e, header);
         return true;
     }

     public void add(int index, E element) {
         addBefore(element, (index==size ? header : entry(index)));
     }

    private Entry<E> addBefore(E e, Entry<E> entry) {
         Entry<E> newEntry = new Entry<E>(e, entry, entry.previous);
         newEntry.previous.next = newEntry;
         newEntry.next.previous = newEntry;
         size++;
         modCount++;
         return newEntry;
    }
```

`addBefore(E e,Entry<E> entry)`方法是个私有方法，所以无法在外部程序中调用（当然，这是一般情况，你可以通过反射上面的还是能调用到的）。

`addBefore(E e,Entry<E> entry)`先通过Entry的构造方法创建e的节点newEntry（包含了将其下一个节点设置为entry，上一个节点设置为entry.previous的操作，相当于修改newEntry的"指针"），之后修改插入位置后newEntry的前一节点的next引用和后一节点的previous引用，使链表节点间的引用关系保持正确。之后修改和size大小和记录modCount，然后返回新插入的节点。

下面分解"添加第一个数据"的步骤：

第一步：初始化后LinkedList实例的情况：

![初始化的空链表LinkedList](/img/050127401729281.png)

第二步：初始化一个预添加的Entry实例（newEntry）。

```java
Entry newEntry = newEntry(e, entry, entry.previous);
```

![添加新节点](/img/050128265474547.png)

第三步：调整新加入节点和头结点（header）的前后指针。

```java
newEntry.previous.next = newEntry;
//newEntry.previous即header，newEntry.previous.next即header的next指向newEntry实例。在上图中应该是“4号线”指向newEntry。
newEntry.next.previous = newEntry;
//newEntry.next即header，newEntry.next.previous即header的previous指向newEntry实例。在上图中应该是“3号线”指向newEntry。
```

调整后如下图所示：

图----加入第一个节点后LinkedList示意图

![添加后的新节点](/img/050129480783272.png)

下面分解"添加第二个数据"的步骤：

第一步：新建节点。

图----添加第二个节点

![添加节点](/img/050130517199296.png)

第二步：调整新节点和头结点的前后指针信息。

图----调整前后指针信息

![调整前后指针信息](/img/050131207351831.png)

添加后续数据情况和上述一致，LinkedList实例是没有容量限制的。

总结，`addBefore(E e,Entry<e> entry)`实现在entry之前插入由e构造的新节点。而add(E e)实现在header节点之前插入由e构造的新节点。为了便于理解，下面给出插入节点的示意图。

![插入节点](/img/050124104386124.jpg)

```java
public void addFirst(E e) {
     addBefore(e, header.next);
 }

 public void addLast(E e) {
     addBefore(e, header);
 }
```

看上面的示意图，结合`addBefore(E e,Entry<E> entry)`方法，很容易理解addFrist(E e)只需实现在header元素的下一个元素之前插入，即示意图中的一号之前。addLast(E e)只需在实现在header节点前（因为是循环链表，所以header的前一个节点就是链表的最后一个节点）插入节点（插入后在2号节点之后）。

#### 清除数据clear()

```java
public void clear() {
    Entry<E> e = header.next;
    // e可以理解为一个移动的“指针”，因为是循环链表，所以回到header的时候说明已经没有节点了
     while (e != header) {
       // 保留e的下一个节点的引用
        Entry<E> next = e.next;
        // 解除节点e对前后节点的引用
        e.next = e.previous = null;
        // 将节点e的内容置空
        e.element = null;
        // 将e移动到下一个节点
        e = next;
 }
    // 将header构造成一个循环链表，同构造方法构造一个空的LinkedList
    header.next = header.previous = header;
    // 修改size
    size = 0;
    modCount++;
}
```

#### 数据包含 contains(Object o)

```java
public boolean contains(Object o) {
     return indexOf(o) != -1;
 }
 // 从前向后查找，返回“值为对象(o)的节点对应的索引”  不存在就返回-1
 public int indexOf(Object o) {
      int index = 0;
      if (o==null) {
          for (Entry e = header.next; e != header; e = e.next) {
              if (e.element==null)
                  return index;
              index++;
         }
      } else {
         for (Entry e = header.next; e != header; e = e.next) {
             if (o.equals(e.element))
                 return index;
             index++;
        }
    }
     return -1;
 }
```

indexOf(Object o)判断o链表中是否存在节点的element和o相等，若相等则返回该节点在链表中的索引位置，若不存在则放回-1。

contains(Object o)方法通过判断indexOf(Object o)方法返回的值是否是-1来判断链表中是否包含对象o。

#### 删除数据remove()

几个remove方法最终都是调用了一个私有方法：remove(Entry

<e> e)，只是其他简单逻辑上的区别。下面分析<code>remove(Entry&lt;E&gt; e)</code>方法。</e>

```java
private E remove(Entry<E> e) {
    if (e == header)
        throw new NoSuchElementException();
    // 保留将被移除的节点e的内容
    E result = e.element;
   // 将前一节点的next引用赋值为e的下一节点
    e.previous.next = e.next;
   // 将e的下一节点的previous赋值为e的上一节点
    e.next.previous = e.previous;
   // 上面两条语句的执行已经导致了无法在链表中访问到e节点，而下面解除了e节点对前后节点的引用
   e.next = e.previous = null;
  // 将被移除的节点的内容设为null
  e.element = null;
  // 修改size大小
  size--;
  modCount++;
  // 返回移除节点e的内容
  return result;
}
```

由于删除了某一节点因此调整相应节点的前后指针信息，如下：

```java
e.previous.next = e.next;//预删除节点的前一节点的后指针指向预删除节点的后一个节点。
e.next.previous = e.previous;//预删除节点的后一节点的前指针指向预删除节点的前一个节点。
```

清空预删除节点：

```java
e.next = e.previous = null;
e.element = null;
```

交给gc完成资源回收，删除操作结束。

与ArrayList比较而言，LinkedList的删除动作不需要"移动"很多数据，从而效率更高。

#### 数据获取get()

get(int)方法的实现在remove(int)中已经涉及过了。首先判断位置信息是否合法（大于等于0，小于当前LinkedList实例的Size），然后遍历到具体位置，获得节点的业务数据（element）并返回。

注意：为了提高效率，需要根据获取的位置判断是从头还是从尾开始遍历。

```java
// 获取双向链表中指定位置的节点    
    private Entry<E> entry(int index) {    
        if (index < 0 || index >= size)    
            throw new IndexOutOfBoundsException("Index: "+index+    
                                                ", Size: "+size);    
        Entry<E> e = header;    
        // 获取index处的节点。    
        // 若index < 双向链表长度的1/2,则从前先后查找;    
        // 否则，从后向前查找。    
        if (index < (size >> 1)) {    
            for (int i = 0; i <= index; i++)    
                e = e.next;    
        } else {    
            for (int i = size; i > index; i--)    
                e = e.previous;    
        }    
        return e;    
    }
```

注意细节：位运算与直接做除法的区别。先将index与长度size的一半比较，如果index

<size 2，就只从位置0往后遍历到位置index处，而如果index="">size/2，就只从位置size往前遍历到位置index处。这样可以减少一部分不必要的遍历</size>

#### 数据复制clone()与toArray()

##### clone()

```java
public Object clone() {
    LinkedList<E> clone = null;
    try {
        clone = (LinkedList<E>) super.clone();
    } catch (CloneNotSupportedException e) {
        throw new InternalError();
   }
    clone.header = new Entry<E>(null, null, null);
    clone.header.next = clone.header.previous = clone.header;
    clone.size = 0;
    clone.modCount = 0;
    for (Entry<E> e = header.next; e != header; e = e.next)
       clone.add(e.element);
    return clone;
}
```

调用父类的clone()方法初始化对象链表clone，将clone构造成一个空的双向循环链表，之后将header的下一个节点开始将逐个节点添加到clone中。最后返回克隆的clone对象。

##### toArray()

```java
public Object[] toArray() {
    Object[] result = new Object[size];
    int i = 0;
    for (Entry<E> e = header.next; e != header; e = e.next)
        result[i++] = e.element;
    return result;
}
```

创建大小和LinkedList相等的数组result，遍历链表，将每个节点的元素element复制到数组中，返回数组。

##### toArray(T[] a)

```java
public <T> T[] toArray(T[] a) {
    if (a.length < size)
        a = (T[])java.lang.reflect.Array.newInstance(
                               a.getClass().getComponentType(), size);
    int i = 0;
    Object[] result = a;
    for (Entry<E> e = header.next; e != header; e = e.next)
        result[i++] = e.element;
    if (a.length > size)
        a[size] = null;
    return a;
}
```

先判断出入的数组a的大小是否足够，若大小不够则拓展。这里用到了反射的方法，重新实例化了一个大小为size的数组。之后将数组a赋值给数组result，遍历链表向result中添加的元素。最后判断数组a的长度是否大于size，若大于则将size位置的内容设置为null。返回a。

从代码中可以看出，数组a的length小于等于size时，a中所有元素被覆盖，被拓展来的空间存储的内容都是null；若数组a的length的length大于size，则0至size-1位置的内容被覆盖，size位置的元素被设置为null，size之后的元素不变。

为什么不直接对数组a进行操作，要将a赋值给result数组之后对result数组进行操作？

#### 遍历数据：Iterator()

LinkedList的Iterator

除了Entry，LinkedList还有一个内部类：ListItr。

ListItr实现了ListIterator接口，可知它是一个迭代器，通过它可以遍历修改LinkedList。

在LinkedList中提供了获取ListItr对象的方法：listIterator(int index)。

```java
public ListIterator<E> listIterator(int index) {
     return new ListItr(index);
}
```

该方法只是简单的返回了一个ListItr对象。

LinkedList中还有通过集成获得的listIterator()方法，该方法只是调用了listIterator(int index)并且传入0。

##### ListItr

```java
private class ListItr implements ListIterator<E> {
// 最近一次返回的节点，也是当前持有的节点
    private Entry<E> lastReturned = header;
    // 对下一个元素的引用
    private Entry<E> next;
    // 下一个节点的index
    private int nextIndex;
    private int expectedModCount = modCount;
    // 构造方法，接收一个index参数，返回一个ListItr对象
    ListItr(int index) {
        // 如果index小于0或大于size，抛出IndexOutOfBoundsException异常
        if (index < 0 || index > size)
        throw new IndexOutOfBoundsException("Index: "+index+
                            ", Size: "+size);
        // 判断遍历方向
        if (index < (size >> 1)) {
        // next赋值为第一个节点
        next = header.next;
        // 获取指定位置的节点
        for (nextIndex=0; nextIndex<index; nextIndex++)
            next = next.next;
        } else {
// else中的处理和if块中的处理一致，只是遍历方向不同
        next = header;
        for (nextIndex=size; nextIndex>index; nextIndex--)
            next = next.previous;
       }
   }
    // 根据nextIndex是否等于size判断时候还有下一个节点（也可以理解为是否遍历完了LinkedList）
    public boolean hasNext() {
        return nextIndex != size;
   }
    // 获取下一个元素
    public E next() {
       checkForComodification();
        // 如果nextIndex==size,则已经遍历完链表，即没有下一个节点了（实际上是有的，因为是循环链表，任何一个节点都会有上一个和下一个节点，这里的没有下一个节点只是说所有节点都已经遍历完了）
        if (nextIndex == size)
        throw new NoSuchElementException();
        // 设置最近一次返回的节点为next节点
        lastReturned = next;
        // 将next“向后移动一位”
        next = next.next;
        // index计数加1
        nextIndex++;
        // 返回lastReturned的元素
        return lastReturned.element;
   }

    public boolean hasPrevious() {
        return nextIndex != 0;
   }
    // 返回上一个节点，和next()方法相似
    public E previous() {
        if (nextIndex == 0)
        throw new NoSuchElementException();

        lastReturned = next = next.previous;
        nextIndex--;
       checkForComodification();
        return lastReturned.element;
   }

    public int nextIndex() {
        return nextIndex;
   }

    public int previousIndex() {
        return nextIndex-1;
   }
    // 移除当前Iterator持有的节点
    public void remove() {
           checkForComodification();
            Entry<E> lastNext = lastReturned.next;
            try {
                LinkedList.this.remove(lastReturned);
            } catch (NoSuchElementException e) {
                throw new IllegalStateException();
           }
        if (next==lastReturned)
                next = lastNext;
            else
        nextIndex--;
        lastReturned = header;
        expectedModCount++;
   }
    // 修改当前节点的内容
    public void set(E e) {
        if (lastReturned == header)
        throw new IllegalStateException();
       checkForComodification();
        lastReturned.element = e;
   }
    // 在当前持有节点后面插入新节点
    public void add(E e) {
       checkForComodification();
        // 将最近一次返回节点修改为header
        lastReturned = header;
       addBefore(e, next);
        nextIndex++;
        expectedModCount++;
   }
    // 判断expectedModCount和modCount是否一致，以确保通过ListItr的修改操作正确的反映在LinkedList中
    final void checkForComodification() {
        if (modCount != expectedModCount)
        throw new ConcurrentModificationException();
   }
}
```

ListItr的使用实例:

```java
LinkedList<String> list = new LinkedList<String>();
        list.add("First");
        list.add("Second");
        list.add("Thrid");
       System.out.println(list);
        ListIterator<String> itr = list.listIterator();
        while (itr.hasNext()) {
           System.out.println(itr.next());
       }
        try {
            System.out.println(itr.next());// throw Exception
        } catch (Exception e) {
            // TODO: handle exception
       }
        itr = list.listIterator();
       System.out.println(list);
       System.out.println(itr.next());
        itr.add("new node1");
       System.out.println(list);
        itr.add("new node2");
       System.out.println(list);
       System.out.println(itr.next());
        itr.set("modify node");
       System.out.println(list);
       itr.remove();
        System.out.println(list);
```

```
结果：
[First, Second, Thrid]
First
Second
Thrid
[First, Second, Thrid]
First
[First, new node1, Second, Thrid]
[First, new node1, new node2, Second, Thrid]
Second
[First, new node1, new node2, modify node, Thrid]
[First, new node1, new node2, Thrid]
```

LinkedList还有一个提供Iterator的方法：descendingIterator()。该方法返回一个DescendingIterator对象。DescendingIterator是LinkedList的一个内部类。

```java
public Iterator<E> descendingIterator() {
    return new DescendingIterator();
}
```

下面分析详细分析DescendingIterator类。

```java
private class DescendingIterator implements Iterator {
   // 获取ListItr对象
final ListItr itr = new ListItr(size());
// hasNext其实是调用了itr的hasPrevious方法
   public boolean hasNext() {
       return itr.hasPrevious();
   }
// next()其实是调用了itr的previous方法
   public E next() {
       return itr.previous();
   }
   public void remove() {
       itr.remove();
   }
}
```

从类名和上面的代码可以看出这是一个反向的Iterator，代码很简单，都是调用的ListItr类中的方法。

### 关于LinkedList实现Stack(堆栈)和Queue(队列)

#### 堆栈

`特点:存入的元素先进后出。`

```java
public class StringStack {
    private LinkedList<String> linkedList
    = new LinkedList<String>();

    /**
     * 将元素加入LinkedList容器
     * (即插入到链表的第一个位置)
     */
    public void push(String name){
        linkedList.addFirst(name);
    }
    /**
     * 取出堆栈中最上面的元素
     * (即取出链表linkedList的第一个元素)
     * @return
     */
    public String getTop(){
        return linkedList.getFirst();
    }
    /**
     * 取出并删除最上面的元素
     * (即移出linkedList的第一个元素)
     * @return
     */
    public String pop(){
        return linkedList.removeFirst();
    }
    /**
     * 获取元素个数
     * @return
     */
    public int size(){
        return linkedList.size();
    }

    /**
     * 判断堆栈是否为空
     * (即判断 linkedList是否为空)
     * @return
     */
    public boolean isEmpty(){
        return linkedList.isEmpty();
    }
    //测试
    public static void main(String[] args) {
        StringStack stack = new StringStack();
        stack.push("九天惊落");
        stack.push("星海回流");
        stack.push("九天惊落，星海回流");
        System.out.print("第一个元素是:\t");
        System.out.println(stack.getTop());
        System.out.println();
        System.out.println("全部元素:");
        while(!stack.isEmpty()){
            System.out.println("\t"+stack.pop());
        }
    }
}
```

输出结果是:

```
第一个元素是: 九天惊落，星海回流

全部元素:  
    九天惊落，星海回流
    星海回流
    九天惊落
```

`知识提示`:

1. LinkedList的特有方法(本身定义的方法)如:addFirst()、addLast()、getFirst()、getLast()、removeFirst()、removeLast()等
2. 实现队列类似,在此不再举例
3. 如果要使用队列的功能,由于LinkedList也实现了java.util.Queue接口,所以可以直接使用LinkedList的实例来实现。

```java
public class QueueDemo {
    public static void main(String[] args) {
        //父类引用queue指向子类对象
        Queue<String> queue = new LinkedList<String>();
        //offer()方法是往队列加入元素
        queue.offer("九天惊落");
        queue.offer("星海回流");
        queue.offer("九天惊落，星海回流");
        String element = null;
        while((element=queue.poll())!=null){
            System.out.println(element+"\t");
        }
    }
}
```

输出结果是:

```
九天惊落    
星海回流
九天惊落，星海回流
```
