---
layout: post
title: Java理论基础 接口
date: 2016-6-2
categories: blog
tags: [Java,Basis,Interface]
description: Java理论基础
---
# Java理论基础 接口

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Java理论基础 接口](#java理论基础-接口)
	- [接口的基础知识](#接口的基础知识)
	- [接口的实现](#接口的实现)
	- [接口主要的作用](#接口主要的作用)
	- [接口与抽象类的区别](#接口与抽象类的区别)
		- [例子](#例子)

<!-- /TOC -->

## 接口的基础知识

关键词: `interface`

```Java
public interface Flying{
  //abstract 可以省略
  //public 也可以省略，省略默认就是public
  abstract public void run();
  abstract public void fly();
}
```

接口不是类，而是对象的一组需求的描述，这些类要遵从接口描述来统一格式进行定义。
当要实现一个接口时，在接口中被定义的方法必须被定义为public，否则它们将只能得到默认的包访问权限，这样的方法被继承的过程中，访问权限就被降低了，Java编译器不允许发生。
接口中的权限修饰符是固定的，只能是public。

>修饰符：可选，用于指定接口的访问权限，可选值为public。如果省略则使用默认的访问权限。

>接口名：必选参数，用于指定接口的名称，接口名必须是合法的Java标识符。一般情况下，要求首字母大写。

>extends 父接口名列表：可选参数，用于指定要定义的接口继承于哪个父接口。当使用extends关键字时，父接口名为必选参数。

>方法：接口中的方法只有定义而没有被实现。


```Java
public interface CalInterface
{
    final float PI=3.14159f;//定义用于表示圆周率的常量PI
    float getArea(float r);//定义一个用于计算面积的方法getArea()
    float getCircumference(float r);//定义一个用于计算周长的方法getCircumference()
}
```

## 接口的实现
 接口在定义后，就可以在类中实现该接口。在类中实现接口可以使用关键字`implements`，其基本格式如下：

```Java
[修饰符] class <类名> [extends 父类名] [implements 接口列表]{
}
```

 ``修饰符``：可选参数，用于指定类的访问权限，可选值为public、abstract和final。

 ``类名``：必选参数，用于指定类的名称，类名必须是合法的Java标识符。一般情况下，要求首字母大写。

 `extends 父类名`：可选参数，用于指定要定义的类继承于哪个父类。当使用extends关键字时，父类名为必选参数。

`implements 接口列表`：可选参数，用于指定该类实现的是哪些接口。当使用implements关键字时，接口列表为必选参数。当接口列表中存在多个接口名时，各个接口名之间使用逗号分隔。
在类中实现接口时，方法的名字、返回值类型、参数的个数及类型必须与接口中的完全一致，并且必须实现接口中的所有方法。例如，编写一个名称为Cire的类，该类实现定义的接口Calculate，具体代码如下：

```Java
public class Cire implements CalInterface
{
    public float getArea(float r)
    {
        float area=PI*r*r;//计算圆面积并赋值给变量area
        return area;//返回计算后的圆面积
    }
    public float getCircumference(float r)
    {
        float circumference=2*PI*r;      //计算圆周长并赋值给变量circumference
        return circumference;           //返回计算后的圆周长
    }
    public static void main(String[] args)
    {
        Cire c = new Cire();
        float f = c.getArea(2.0f);
        System.out.println(Float.toString(f));
    }
}
```

>在类的继承中，只能做单重继承，而实现接口时，一次则可以实现多个接口，每个接口间使用逗号“,”分隔。这时就可能出现常量或方法名冲突的情况，解决该问题时，如果常量冲突，则需要明确指定常量的接口，这可以通过“接口名.常量”实现。如果出现方法冲突时，则只要实现一个方法就可以了。下面通过一个具体的实例详细介绍以上问题的解决方法。


## 接口主要的作用

提供设计规范，为多态提供前提条件。

## 接口与抽象类的区别

语法层面上的区别

>1. 抽象类可以提供成员方法的实现细节，而接口中只能存在public abstract 方法；
2. 抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是public static final类型的；
3. 接口中不能含有静态代码块以及静态方法，而抽象类可以有静态代码块和静态方法；
4. 一个类只能继承一个抽象类，而一个类却可以实现多个接口。

设计层面上的区别

>1. 抽象类是对一种事物的抽象，即对类抽象，而接口是对行为的抽象。抽象类是对整个类整体进行抽象，包括属性、行为，但是接口却是对类局部（行为）进行抽象。举个简单的例子，飞机和鸟是不同类的事物，但是它们都有一个共性，就是都会飞。那么在设计的时候，可以将飞机设计为一个类Airplane，将鸟设计为一个类Bird，但是不能将 飞行 这个特性也设计为类，因此它只是一个行为特性，并不是对一类事物的抽象描述。此时可以将 飞行 设计为一个接口Fly，包含方法fly( )，然后Airplane和Bird分别根据自己的需要实现Fly这个接口。然后至于有不同种类的飞机，比如战斗机、民用飞机等直接继承Airplane即可，对于鸟也是类似的，不同种类的鸟直接继承Bird类即可。从这里可以看出，继承是一个 "是不是"的关系，而 接口 实现则是 "有没有"的关系。如果一个类继承了某个抽象类，则子类必定是抽象类的种类，而接口实现则是有没有、具备不具备的关系，比如鸟是否能飞（或者是否具备飞行这个特点），能飞行则可以实现这个接口，不能飞行就不实现这个接口。
2. 设计层面不同，抽象类作为很多子类的父类，它是一种模板式设计。而接口是一种行为规范，它是一种辐射式设计。什么是模板式设计？最简单例子，大家都用过ppt里面的模板，如果用模板A设计了ppt B和ppt C，ppt B和ppt C公共的部分就是模板A了，如果它们的公共部分需要改动，则只需要改动模板A就可以了，不需要重新对ppt B和ppt C进行改动。而辐射式设计，比如某个电梯都装了某种报警器，一旦要更新报警器，就必须全部更新。也就是说对于抽象类，如果需要添加新的方法，可以直接在抽象类中添加具体的实现，子类可以不进行变更；而对于接口则不行，如果接口进行了变更，则所有实现这个接口的类都必须进行相应的改动。

### 例子

门和警报的例子：门都有open( )和close( )两个动作，抽象类：

```Java
abstract class Door {
    public abstract void open();
    public abstract void close();
}
```

接口：

```Java
interface Door {
    public abstract void open();
    public abstract void close();
}
```

但是现在如果我们需要门具有报警alarm( )的功能，那么该如何实现？下面提供两种思路：

　　1. 将这三个功能都放在抽象类里面，但是这样一来所有继承于这个抽象类的子类都具备了报警功能，但是有的门并不一定具备报警功能；

　　2. 将这三个功能都放在接口里面，需要用到报警功能的类就需要实现这个接口中的open( )和close( )，也许这个类根本就不具备open( )和close( )这两个功能，比如火灾报警器。

　　从这里可以看出， Door的open() 、close()和alarm()根本就属于两个不同范畴内的行为，open()和close()属于门本身固有的行为特性，而alarm()属于延伸的附加行为。因此最好的解决办法是单独将报警设计为一个接口，包含alarm()行为,Door设计为单独的一个抽象类，包含open和close两种行为。再设计一个报警门继承Door类和实现Alarm接口。

```Java
interface Alram {
    void alarm();
}

abstract class Door {
    void open();
    void close();
}

class AlarmDoor extends Door implements Alarm {
    void oepn() {
      //....
    }
    void close() {
      //....
    }
    void alarm() {
      //....
    }
}
```
