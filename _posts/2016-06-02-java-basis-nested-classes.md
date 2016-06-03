---
layout: post
title: Java理论基础 内部类
date: 2016-6-2
categories: blog
tags: [Java,Basis,NestedClasses]
description: Java理论基础
---
# Java理论基础 内部类

>整理自《Thinking in Java》

## 内部类基础知识

可以将一个类的定义放在另一个类的定义内部，就是内部类。
当生成一个内部类的对象时，此对象与制造它的外围对象之间就有了某种联系，所以它能访问其外围对象的所有成员，而不需要任何特殊条件。

内部类还拥有其外围类的所有元素的访问权限。
>当某个外围类的对象创建一个内部类对象时，次你不累对象必定会秘密的捕获一个指向那个外围类对象的引用，当访问此外围类的成员时，就是用那个引用来选择外围类的成员(由编译器完成，如果编译器访问不到这个引用就会报错)。

## 使用.this与.new

如果你需要生成对外部类对象的引用，可以使用外部类的名字后面紧跟一个`.this`。
要告知某些其他对象，去创建某个内部类的对象。必须在new表达式中提供对其他外部类对象的引用，需要使用`.new`语法。

```
外部类名.内部类名 内部对象名 = 外围对象名.new 内部类名();
```

在拥有外部类对象之前是不可能创建内部类对象的。这是因为内部类对象会暗暗的连接到创建它的外部类对象上，但是，如果创建的是嵌套类(静态内部类)就不需要对外部类对象的引用。

## 内部类与向上转型

当将内部类向上转型为其基类，尤其是转型为一个接口的时候，内部类就有了用武之地。（从实现某个接口的对象，得到对此接口的引用，与向上转型为这个对象的基类，实质上效果是一样得。）这是因为此内部类——某个接口的实现——对于其他人来说能够完全不可见，并且不可用。所得到的只是指向基类或接口的引用，所以能够很方便地隐藏实现细节。

```java
interface Destination{
   String readLabel();
}
interface Contents{
   int value();
}
class Parce1{
   private class PContents implements Contents{
      private int i = 2012;
      public int value() {
        return i;
      }
   }
   protected class PDestination implements Destination{
      private String label;
      private PDestination(String label){
        this.label = label;
      }
      public String readLabel() {
        return label;
      }
   }

   public  Destinationdest(String label){
      return new PDestination(label);
   }
   public Contents cont(){
      return new PContents();
   }
}
public class TestParcel{
   public static void main(String[] args){
      Parce1 p = new Parce1();
      Contents c = p.cont();
      System.out.println(c.value());
      Destination d = p.dest("Cannel_2020");
      System.out.println(d.readLabel());
   }
}


>2012
>Cannel_2020
```
>1. PContent是private的，所以除了Parcel，没有人能访问它。
2. Pdestination是protected，所以只有Parcel及其子类、还有与Parcel用包的类能访问。
3. 这意味着，如果客户端程序员想了解或访问这些成员，那是要受限制的。
4. 实际上，甚至不能向下转型成private内部类（或protected内部类，除非是继承自它的子类），因为不能访问其名字。
5. 所以通过类似如上的方式，可以完全阻止任何依赖类型的编码，并且完全隐藏了实现的细节。
6. 此外，从客户端程序员的角度来看，由于不能访问（为什么？）任何新增加的、原本不属于公共接口的方法，所以扩展接口是没有价值的。这也给Java编译器提供了生成更高效代码的机会。
7. 以上是内部类的典型用途。


在一个方法或者任意的作用域内定义内部类的两个理由：
1. 如前所示，你实现了某个类型的接口，于是可以创建并返回对其的引用。
2. 你要解决一个复杂的问题，想创建一个类来辅助你的解决方案，但是又不希望这个类是公共可用的。

## 局部内部类
在方法的作用域内（而不是在其他类的作用域内）创建一个完整的类。方法的执行完毕，并不意味该局部内部类就不可用了。另外，局部内部类不能有访问说明符。

## 匿名内部类

```Java
public Contents cont(){  
     return new Contents(){  
        private int i = 2012;  
        public int value(){  
           return i;  
        }  
     };  
}  
```
其上是下述形式的简化形式：

```Java
public Contents cont(){
      class MyContents implements Contents{
        private int i = 2012;
        public int value(){
           return i;
        }
      }
      return new MyContents();
}
```

若匿名内部类需要一个有参数的构造器：
```Java
class Wrapping{
   int i = 0;
   Wrapping(int i){
      this.i = i;
   }
   public int value() {
      return i;
   }
}
public class Parcel{
   public Wrapping wrap(int i){
      return new Wrapping(i){
        public int value(){
           return super.value()*10;
        }
      };
   }
   public static void main(String[] args){
      Wrapping w = new Parcel().wrap(2012);
      System.out.println(w.value());
   }
}
```

匿名内部类没有命名的构造器，但通过实例初始化，能够达到为一个构造器的效果。
```Java
abstract class Base{
   public Base(int i){
      System.out.println("基类的构造函数内, i = " + i);
   }
   public abstract void f();
}
public class AnonymousConstructor{
   public static Base getBase(int i){
      return new Base(i){
        {
           System.out.println("实例初始化器内");
        }
        public void f(){
           System.out.println("在匿名内部类里的f()");
        }
      };
   }
   public static void main(String[] args){
      Base base = getBase(2012);
      base.f();
   }
}

>基类的构造函数内, i = 2012
>实例初始化器内
>在匿名内部类里的f()
```

匿名内部类中使用一个在外部定义的对象，那么编译器会要求其参数引用是final的。

## 嵌套类
如果不需要内部类对象与其外围类对象之间的联系，那么可以将内部类声明为static。这通常称为嵌套类。

1. 要创建嵌套类的对象，并不需要其外围类的对象。
2. 不能从嵌套类的对象中访问非静态外围对象。

普通内部类与嵌套类的区别：``普通内部类不能有static数据和static字段，也不能包含嵌套类。但是嵌套类可以包含所有这些东西``。

普通内部类（非static）可以通过一个特殊的this引用可以链接到其外围类对象。嵌套类就没有这个特殊的this引用，这使得它类似一个static方法。

内部类的继承存在的问题：``那个指向外围类对象的“秘密的”引用必须被初始化，而在导出类中不再存在可连接的缺省对象``。例子如下：
```Java
class Outter{
   Outter(){
      System.out.println("Outter()");
   }
   class Inner{
      Inner(){
        System.out.println("Inner()");
      }
   }
}
public class InheritInner extends Outter.Inner{
   InheritInner(Outter outter){
      outter.super();
      System.out.println("InheritInner()");
   }
   public static void main(String[] args){
      Outter outter = new Outter();
      InheritInner inheritInner = new InheritInner(outter);
   }
}
```
虽然是继承内部类，但是当生产一个构造器时，缺省的构造器并不算好，而且不能只是传递一个指向外围类对象的引用，必须在构造器内使用如下语法：

`outter.super();`

这样才提供了必要的引用，然后程序才能编译通过。若缺省上面语句，会提示“由于某些中间构造函数调用，没有任何类型 Outter 的外层实例可用”的错误。

## 内部类的覆盖

第一个例子：

```Java
class Outter{
   private Inner inner;
   Outter(){
      System.out.println("基类Outter()");
      inner = new Inner();
   }
   protected class Inner{
      Inner(){
        System.out.println("基类的内部类Inner()");    
      }
   }
}
public class InheritInner extends Outter{
   public class Inner{
      public Inner(){
        System.out.println("InheritInner重新定义的内部类Inner()");
      }
   }
   public static void main(String[] args){
      new InheritInner();
   }
}

>基类Outter()
>基类的内部类Inner()
```

第二个例子：

```Java
class Outter{
   private Inner inner;
   Outter(){
      System.out.println("Outter()");
      inner = new Inner();
   }
   public void insertInner(Inner inner){
      this.inner = inner;
   }
   public void outterFunc(){
      inner.func();
   }
   protected class Inner{
      Inner(){
        System.out.println("Outter.Inner()");
      }
      public void func(){
        System.out.println("Outter.Inner().func()");
      }
   }
}
public class InheritInner extends Outter{
   public InheritInner(){
      System.out.println("InheritInner()");
      //下面语句先Outter.Inner()，再是InheritInner.Inner();
      Inner inner = new Inner();
      insertInner(inner);
   }
   public class Inner extends Outter.Inner{
      public Inner(){
        System.out.println("InheritInner.Inner()");
      }
      public void func(){
        System.out.println("InheritInner.Inner().func()");
      }
   }
   public static void main(String[] args){
      Outter outter = new InheritInner();
      outter.outterFunc();//动态绑定
   }
}

>Outter()
>Outter.Inner()
>InheritInner()
>Outter.Inner()
>InheritInner.Inner()
>InheritInner.func()
```
