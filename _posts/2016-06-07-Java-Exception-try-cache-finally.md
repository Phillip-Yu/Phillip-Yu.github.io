---
layout: post
title: Java的异常处理之捕获异常
date: 2016-6-7
categories: blog
tags:
  - Java
  - Exception
  - try..catch..finally
description: Java的异常处理机制之捕获异常
---

# Exception基本知识

异常使我们可以将每件事都当作一个事务去考虑，而异常可以看护着这些事务的底线。我们还可以将异常看作是一种内建的恢复系统，因为(在细心使用的情况下)我们在程序中可以拥有的各种不同的恢复点。如果程序的某一部分失败了，异常将"恢复"到程序中某个已知存在的稳定点上。

# 异常的体系

异常是程序运行过程中出现的错误。Java语言的异常处理框架，是Java语言健壮性的一个重要体现。

Java把异常当作对象来处理，并定义一个基类java.lang.Throwable作为所有异常的超类。

在Java API中已经定义了许多异常类，这些异常类分为两大类，错误Error和异常Exception。

Java异常体系结构呈树状，其层次结构图如图 1所示：

![Exception体系结构层次图](/img/1354020417_5176.jpg "Exception继承体系")

在 Java 中，所有的异常都有一个共同的祖先 Throwable（可抛出）。Throwable 指定代码中可用异常传播机制通过 Java 应用程序传输的任何问题的共性。

## Exception 和 Error

`Throwable`：有两个重要的子类：`Exception`（异常）和`Error`（错误），二者都是Java异常处理的重要子类，各自都包含大量子类。

`Error`（错误）:是程序无法处理的错误，表示运行应用程序中较严重问题。大多数错误与代码编写者执行的操作无关，而表示代码运行时JVM（Java 虚拟机）出现的问题。例如，Java虚拟机运行错误（Virtual MachineError），当JVM不再有继续执行操作所需的内存资源时，将出现OutOfMemoryError。这些异常发生时，Java虚拟机（JVM）一般会选择线程终止。

这些错误表示故障发生于虚拟机自身、或者发生在虚拟机试图执行应用时，如Java虚拟机运行错误（Virtual MachineError）、类定义错误（NoClassDefFoundError）等。这些错误是不可查的，因为它们在应用程序的控制和处理能力之 外，而且绝大多数是程序运行时不允许出现的状况。对于设计合理的应用程序来说，即使确实发生了错误，本质上也不应该试图去处理它所引起的异常状况。在Java中，错误通过Error的子类描述。

Exception（异常）:是程序本身可以处理的异常。

Exception 类有一个重要的子类 RuntimeException。RuntimeException类及其子类表示"JVM常用操作"引发的错误。例如，若试图使用空值对象引用、除数为零或数组越界，则分别引发运行时异常（NullPointerException、ArithmeticException）和 ArrayIndexOutOfBoundException。

`注意：异常和错误的区别：异常能被程序本身可以处理，错误是无法处理。`

## 可查异常 和 不可查异常

通常，Java的异常(包括Exception和Error)分为可查的异常（checked exceptions）和不可查的异常（unchecked exceptions）。

`可查异常（编译器要求必须处置的异常）`：正确的程序在运行中，很容易出现的、情理可容的异常状况。可查异常虽然是异常状况，但在一定程度上它的发生是可以预计的，而且一旦发生这种异常状况，就必须采取某种方式进行处理。

除了RuntimeException及其子类以外，其他的Exception类及其子类都属于可查异常。这种异常的特点是Java编译器会检查它，也就是说，当程序中可能出现这类异常，要么用`try-catch`语句捕获它，要么用`throws`子句声明抛出它，否则编译不会通过。

`不可查异常(编译器不要求强制处置的异常)`:包括运行时异常（RuntimeException与其子类）和错误（Error）。

Exception 这种异常分两大类`运行时异常`和`非运行时异常(编译异常)`。程序中应当尽可能去处理这些异常。

`运行时异常`：都是`RuntimeException类及其子类异常`，如NullPointerException(空指针异常)、IndexOutOfBoundsException(下标越界异常)等，这些异常是不检查异常，程序中可以选择捕获处理，也可以不处理。这些异常一般是由程序逻辑错误引起的，程序应该从逻辑角度尽可能避免这类异常的发生。

运行时异常的特点是`Java编译器不会检查它`，也就是说，当程序中可能出现这类异常，即使没有用try-catch语句捕获它，也没有用throws子句声明抛出它，也会编译通过。

`非运行时异常（编译异常）`：是RuntimeException以外的异常，类型上都属于Exception类及其子类。从程序语法角度讲是必须进行处理的异常，如果不处理，程序就不能编译通过。如IOException、SQLException等以及用户自定义的Exception异常，一般情况下不自定义检查异常。

# 异常的处理机制

在Java应用程序中，异常处理机制为：`抛出异常`，`捕捉异常`。

抛出异常：当一个方法出现错误引发异常时，方法创建异常对象并交付运行时系统，异常对象中包含了异常类型和异常出现时的程序状态等异常信息。运行时系统负责寻找处置异常的代码并执行。

捕获异常：在方法抛出异常之后，运行时系统将转为寻找合适的异常处理器（exception handler）。潜在的异常处理器是异常发生时依次存留在调用栈中的方法的集合。当异常处理器所能处理的异常类型与方法抛出的异常类型相符时，即为合适 的异常处理器。运行时系统从发生异常的方法开始，依次回查调用栈中的方法，直至找到含有合适异常处理器的方法并执行。当运行时系统遍历调用栈而未找到合适的异常处理器，则运行时系统终止。同时，意味着Java程序的终止。

对于运行时异常、错误或可查异常，Java技术所要求的异常处理方式有所不同。

由于运行时异常的不可查性，为了更合理、更容易地实现应用程序，Java规定，`运行时异常将由Java运行时系统自动抛出，允许应用程序忽略运行时异常`。

对于方法运行中可能出现的Error，当运行方法不欲捕捉时，Java允许该方法不做任何抛出声明。因为，大多数Error异常属于永远不能被允许发生的状况，也属于合理的应用程序不该捕捉的异常。

对于所有的可查异常，Java规定：`一个方法必须捕捉，或者声明抛出方法之外`。也就是说，当一个方法选择不捕捉可查异常时，它必须声明将抛出异常。

能够捕捉异常的方法，需要提供相符类型的异常处理器。所捕捉的异常，可能是由于自身语句所引发并抛出的异常，也可能是由某个调用的方法或者Java运行时系统等抛出的异常。也就是说，一个方法所能捕捉的异常，一定是Java代码在某处所抛出的异常。简单地说，异常总是先被抛出，后被捕捉的。

> 任何Java代码都可以抛出异常，如：自己编写的代码、来自Java开发环境包中代码，或者Java运行时系统。无论是谁，都可以通过Java的throw语句抛出异常。

> 从方法中抛出的任何异常都必须使用throws子句。

> 捕捉异常通过try-catch语句或者try-catch-finally语句实现。

> 总体来说，Java规定：对于可查异常必须捕捉、或者声明抛出。允许忽略不可查的RuntimeException和Error。

## 异常的捕获与处理

### try-catch

监控区域：一段可能产生异常的代码，并且后面跟着处理这些异常的代码。

抛出异常必须在异常处理程序处得到处理。而且要针对每个要捕获的异常，得准备相应的处理程序，异常处理程序紧跟着try块之后，以关键词catch表示。

关键词try后的一对大括号将一块可能发生异常的代码包起来，称为监控区域。Java方法在运行过程中出现异常，则创建异常对象。将异常抛出监控区域之 外，由Java运行时系统试图寻找匹配的catch子句以捕获异常。若有匹配的catch子句，则运行其异常处理代码，try-catch语句结束。

匹配的原则是：如果抛出的异常对象属于catch子句的异常类，或者属于该异常类的子类，则认为生成的异常对象与catch块捕获的异常类型相匹配。

```java
try{
  //方法体内出现抛出异常，方法将在抛出异常的过程中结束，可以在方法内设置try块来捕获异常。
}catch(Type1 id1){
  // 捕获并处置try抛出的异常类型Type1  
}  
catch (Type2 id2){  
  //捕获并处置try抛出的异常类型Type2  
}
```

需要注意的是，`一旦某个catch捕获到匹配的异常类型，将进入异常处理代码。一经处理结束，就意味着整个try-catch语句结束。其他的catch子句不再有匹配和捕获异常类型的机会`。

Java通过异常类描述异常类型，异常类的层次结构如图1所示。`对于有多个catch子句的异常程序而言，应该尽量将捕获底层异常类的catch子句放在前面`，`同时尽量将捕获相对高层的异常类的catch子句放在后面`。否则，捕获底层异常类的catch子句将可能会被屏蔽。

RuntimeException异常类包括运行时各种常见的异常，ArithmeticException类和ArrayIndexOutOfBoundsException类都是它的子类。因此，RuntimeException异常类的catch子句应该放在 最后面，否则可能会屏蔽其后的特定异常处理或引起编译错误。

### try-catch-finally

```java
try {  
// 可能会发生异常的程序代码  
} catch (Type1 id1) {  
// 捕获并处理try抛出的异常类型Type1  
} catch (Type2 id2) {  
// 捕获并处理try抛出的异常类型Type2  
} finally {  
// 无论是否发生异常，都将执行的语句块  
}
```

## 小结

try块：用于捕获异常。其后可接零个或多个catch块，如果没有catch块，则必须跟一个finally块。

catch块：用于处理try捕获到的异常。

finally块：无论是否捕获或处理异常，finally块里的语句都会被执行。当在try块或catch块中遇到return语句时，finally语句块将在方法返回之前被执行。在以下4种特殊情况下，finally块不会被执行：

1. 在finally语句块中发生了异常。
2. 在前面的代码中用了System.exit()退出程序。
3. 程序所在的线程死亡。
4. 关闭CPU。

### try-catch-finally 规则(异常处理语句的语法规则）

1. 必须在try之后添加catch或finally块。try块后可同时接catch和finally块，但至少有一个块。
2. 必须遵循块顺序：若代码同时使用catch和finally 块，则必须将catch块放在try块之后。
3. catch块与相应的异常类的类型相关。
4. 一个try块可能有多个catch块。若如此，则执行第一个匹配块。即Java虚拟机会把实际抛出的异常对象依次和各个catch代码块声明的异常类型匹配，如果异常对象为某个异常类型或其子类的实例，就执行这个catch代码块，不会再执行其他的catch代码块
5. 可嵌套try-catch-finally结构。
6. 在try-catch-finally结构中，可重新抛出异常。
7. 除了下列情况，总将执行finally做为结束：

  > JVM 过早终止（调用System.exit(int)）;

  > 在finally块中抛出一个未处理的异常；计算机断电、失火、或遭遇病毒攻击。

### try、catch、finally语句块的执行顺序

1. 当try没有捕获到异常时：try语句块中的语句逐一被执行，程序将跳过catch语句块，执行finally语句块和其后的语句；

2. 当try捕获到异常，catch语句块里没有处理此异常的情况：当try语句块里的某条语句出现异常时，而没有处理此异常的catch语句块时，此异常将会抛给JVM处理，finally语句块里的语句还是会被执行，但finally语句块后的语句不会被执行；

3. 当try捕获到异常，catch语句块里有处理此异常的情况：在try语句块中是按照顺序来执行的，当执行到某一条语句出现异常时，程序将跳到catch语句块，并与catch语句块逐一匹配，找到与之对应的处理程序，其他的catch语句块将不会被执行，而try语句块中，出现异常之后的语句也不会被执行，catch语句块执行完后，执行finally语句块里的语句，最后执行finally语句块后的语句；

![try、catch、finally语句块的执行](/img/1354022670_6403.jpg "try、catch、finally语句块的执行")
