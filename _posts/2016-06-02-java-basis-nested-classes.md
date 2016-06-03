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

## 为什么需要内部类

1. 内部类最吸引人注意的原因是：每个内部类都能独立的继承自一个（接口的）实现，所以无论外围类是否已经继承了某个就（接口的）实现，对于内部类都没有影响。
2. 一个类中以某种方式实现两个接口。由于接口的灵活性，你有两种选择：使用单一类，或者使用内部类。从实现观点看，以下俩种方式没什么区别（例子2-1）。
3. 如果拥有的是抽象类或具体的类，而不是接口，那就只能使用内部类才能实现多重继承。
4. 内部类还可以获得其他一些特性（不是很理解！！）：(例子2-2)
	+ 内部类可以有多个实例，每个实例都有自己的状态信息，并且与外围类对象的信息相互独立。
	+ 在单个的外围类中，可以让多个内部类以不用的方式实现同一接口，或继承同一个类。
	+ 创建内部类对象的时刻并不依赖外围类对象的创建。
	+ 内部类并没有令人迷惑的“is-a”关系；它就是一个独立的实体。

例子2-1
```Java
interface A{}
interface B{}

class X implements A, B{}

class Y implements A{
   B makeB(){
      return new B(){
      };
   }
}

public class MultiInterfaces{
   static void takesA(A a){}
   static void takesB(B b){}
   public static void main(String[] args){
      X x = new X();
      Y y = new Y();
      takesA(x);
      takesA(y);
      takesB(x);
      takesB(y.makeB());
   }
}
```

例子2-2
```Java
interface Selector{
   boolean end();
   Object current();
   void next();
}
public class Sequence{
   private Object[] objects;
   private int next = 0;
   public Sequence(int size){
      objects = new Object[size];
   }
   public void add(Object x){
      if(next < objects.length)
        objects[next++] = x;
   }
   private class SSelector implements Selector{
      private int  i = 0;
      public boolean end(){
        return i == objects.length;
      }
      public Object current(){
        return objects[i];
      }
      public void next(){
        if(i < objects.length)
           i++;
      }
   }
   public Selector getSelector(){
      return new SSelector();
   }
   public static void main(String[] args){
      Sequence sequence = new Sequence(10);
      for(int i = 0; i < 10; i++)
        sequence.add(Integer.toString(i));
      Selector selector = sequence.getSelector();
      while(!selector.end()){
        System.out.println(selector.current());
        selector.next();
      }
   }
}
```
如果Sequence不适用内部类，就必须声明“Sequence是一个Selector”,对于某个特定的Sequence只能又一个Selector。同时，使用内部类很容易就能拥有另一个方法getRSelector(),用它来生成一个反方向遍历的Selector。只有内部类才有这种灵活性。


``闭包``是一个可调用的对象，它记录了一些信息，这些信息来自于创建它的作用域。内部类是面向对象的闭包。因为它不仅包含外围类对象（“创建内部类的作用域”）的信息，还自动拥有一个指向此外围对象的引用，在此作用域内，内部类操作所有成员，包括“private”成员。

Java最具有争议的问题之一就是，人们认为Java应该包含某种类似指针的机制，以允许回调。通过回调，对象能够携带一些信息，这些信息允许它在稍后的某个时刻调用初始的对象。

通过内部类提供闭包的功能是完美的解决方案，它比指针更灵活、更安全。见下例：
```Java
interface Incrementable{
   void increment();
}
//外部类实现接口
class Callee1 implements Incrementable{
   private int  i = 0;
   public void increment(){
      i++;
      System.out.println(i);
   }
}
class MyIncrement{
   void increment(){
      System.out.println("MyIncrement.increment()");
   }
   static void func(MyIncrement myIncrement){
      myIncrement.increment();
   }

}

class Callee2 extends MyIncrement{
   private int i = 0;
   private void incr(){
      i++;
      System.out.println(i);
   }
   //内部类实现接口
   public class Closureimplements Incrementable{
      public void increment(){
        incr();
      }
   }
   Incrementable getCallBackReference(){
      return new Closure();//向上转型
   }
}
class Caller{
   private Incrementable callBackReference;
   Caller(Incrementable callBackReference){
      this.callBackReference = callBackReference;
   }
   void go(){
      callBackReference.increment();
   }
}
public class Callbacks{
   public static void main(String[] args){
      Callee1 c1 = new Callee1();
      Callee2 c2 = new Callee2();
      MyIncrement.func(c2);
      Caller caller1 = new Caller(c1);
      Caller caller2 = new Caller(c2.getCallBackReference());
      caller1.go();
      caller1.go();
      caller2.go();
      caller2.go();

   }
}
```

Callee2继承自MyIncrement,有一个与Incrementable接口相同名字的increment()方法,但是两者的Increment()方法行为不同。所以如果Callee2继承了MyIncrement，就不能为了Incrementable的用途而覆盖increment()方法，于是只能使用内部类独立的实现Incrementable。还要注意，当创建了一个内部类时，``并没有在外围类的接口中添加东西，也没有修改外围类的接口``。

Callee2中内部类Closure 里的getCallBackReference()方法，返回一个Incrementable的引用，无论谁获得此引用，都只能调用increment()，除此之外没有其他功能（不像指针那样，允许你做很多事情）。

``回调的价值在于它的灵活性——可以在运行时动态决定需要调用什么方法。``

### 内部类与框架

1. 应用程序框架(applicationframeword)就是被设计用来解决某类特定问题的一个类或一组类。
2. 要运行某个应用程序框架，通常是继承一个或多个类，并覆盖某个方法。
3. 控制框架是一类特殊的应用程序框架，它用来解决响应事件的需求。
4. 主要用来响应事件的系统被称作事件驱动系统。

#### 用来了解内部类的价值的例子

```Java
public abstract class Event{
   private long eventTime;
   protected final long delayTime;
   public Event(long delayTime){
      this.delayTime = delayTime;
      start();
   }
   public void start(){
      eventTime = System.currentTimeMillis() + delayTime;
   }
   public boolean ready(){
      return System.currentTimeMillis() >= eventTime;
   }
   public abstract void action();
}
```

start()是个独立的方法，而没有包含在构造器内，因为这样就可以在时间运行以后重新启动计数器，也就是能够重复使用Event对象。例如，如果想重复一个事件，只需要简单地在action()中调用start()方法。

```Java
public class Controller {
   private List<Object> eventList = new ArrayList<Object>();
   public void addEvent(Event event){
      eventList.add(event);
   }
   public void run(){
      while(eventList.size() > 0){
        for(int i = 0; i < eventList.size(); i++){
           Event event = (Event)eventList.get(i);
           if(event.ready()){
              System.out.println(event);
              event.action();
              eventList.remove(i);
           }
        }
      }
   }
}
```

`Controller`包含了一个用来管理并触发事件的实际控制框架。
设计的关键在于：使变化的事物与不变的事物互相分离。“变化的事物”指的是不同的Event对象所具有的不同的行为，这可以通过不同的Event子类来变现。
这正是内部类所要做的事情，内部类允许：

1. 用单一的类完整地实现控制框架，从而将实现的细节封装起来。内部类用来表示解决问题所不许的各种不同的action()。
2. 内部类能够很容易地访问外围类的任意成员，具有很大的灵活性。

```Java
public class GreenhouseControls extends Controller{
	private boolean light = false;//默认灯是关着的。
	private boolean water = false;//默认水源是关着的。
	private String thermostat = "Day";//默认在白天
	//开灯事件
	public class LightOn extends Event{
		public LightOn(long delayTime) {
			super(delayTime);
		}
		public void action() {
			light = true;
		}
		public String toString(){
			return "Light is on";
		}
	}
	//关灯事件
	public class LightOff extends Event{
		public LightOff(long delayTime) {
			super(delayTime);
		}
		public void action() {
			light = false;
		}
		public String toString(){
			return "Light is off";
		}
	}
	//开水事件
	public class WaterOn extends Event{
		public WaterOn(long delayTime) {
			super(delayTime);
		}
		public void action() {
			water = true;
		}
		public String toString(){
			return "Water is on";
		}
	}
	//关水事件
	public class WaterOff extends Event{
		public WaterOff(long delayTime) {
			super(delayTime);
		}
		public void action() {
			water = false;
		}
		public String toString(){
			return "Water is off";
		}
	}
	//温度调节于晚上的事件
	public class ThermostatNight extends Event{
		public ThermostatNight(long delayTime) {
			super(delayTime);
		}
		public void action() {
			thermostat = "Night";
		}
		public String toString(){
			return "Therostat on night setting";
		}
	}
	//温度调节于白天的事件
	public class ThermostatDay extends Event{
		public ThermostatDay(long delayTime) {
			super(delayTime);
		}
		public void action() {
			thermostat = "Day";
		}
		public String toString(){
			return "Therostat on day setting";
		}
	}
	//响铃事件
	public class Bell extends Event{
		public Bell(long delayTime) {
			super(delayTime);
		}
		//响完铃之后又把一个new Bell(delayTime)加入都eventList
		public void action() {
			addEvent(new Bell(delayTime));
		}
		public String toString(){
			return "Bing!";
		}
	}
	//重启事件
	public class Restart extends Event{
		private Event[] eventList;
		public Restart(long delayTime, Event[] eventList) {
			super(delayTime);
			this.eventList = eventList;
			for(int i = 0; i < eventList.length; i++){
				addEvent(eventList[i]);
			}
		}
		public void action() {
			for(int i = 0; i < eventList.length; i++){
				eventList[i].start();//重新启动每个事件。
				addEvent(eventList[i]);
			}
			start();//启动当前Restart事件
			addEvent(this);//把当前的Restart事件加入到eventList中，循环启动。
		}
		public String toString(){
			return "Restarting system!";
		}
	}
	//终止事件
	public class Terminate extends Event{
		public Terminate(long delayTime) {
			super(delayTime);
		}
		public void action() {
			System.exit(0);
		}
		public String toString(){
			return "Terminating!";
		}
	}
}
```

创建GreenhouseControls对象（“命令”设计模式的一个例子）：

```Java
public class GreenhouseController {
	public static void main(String[] args) {
		GreenhouseControls gc = new GreenhouseControls();
		gc.addEvent(gc.new Bell(900));
		Event[] eventList = {
			gc.new ThermostatNight(0),
			gc.new LightOn(200),
			gc.new LightOff(400),
			gc.new WaterOn(600),
			gc.new WaterOff(800),
			gc.new ThermostatDay(1400)
		};
		gc.addEvent(gc.new Restart(2000, eventList));
		if(args.length == 0){
			gc.addEvent(gc.new Terminate(5000));
		}
		gc.run();
	}
}

>Therostat on night setting
>Light is on
>Light is off
>Water is on
>Water is off
>Bing!
>Therostat on day setting
>Bing!
>Restarting system!
>Therostat on night setting
>Light is off
>Light is on
>Water is on
>Bing!
>Water is off
>Therostat on day setting
>Bing!
>Restarting system!
>Therostat on night setting
>Light is on
>Light is off
>Bing!
>Water is on
>Water is off
>Terminating!
```
