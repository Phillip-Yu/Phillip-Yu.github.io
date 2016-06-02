---
layout: post
title: Java理论基础 对象
date: 2016-6-1
categories: blog
tags: [Java,Basis,Object]
description: Java理论基础
---
# Java理论基础 对象
>万物皆对象     ——《Thinking in Java》

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Java理论基础 对象](#java理论基础-对象)
	- [面向对象基础](#面向对象基础)
		- [封装](#封装)
			- [访问权限修饰符](#访问权限修饰符)
		- [继承](#继承)
			- [实例化过程](#实例化过程)
			- [重写](#重写)
		- [多态](#多态)
			- [多态的条件](#多态的条件)
			- [多态的局限性](#多态的局限性)
			- [多态的好处](#多态的好处)
		- [抽象](#抽象)
			- [抽象类的多态](#抽象类的多态)
			- [抽象类的特点](#抽象类的特点)
			- [抽象类的细节问题](#抽象类的细节问题)
	- [surper 与 this的用法](#surper-与-this的用法)
		- [使用super&this应该注意些什么？](#使用superthis应该注意些什么)

<!-- /TOC -->

## 面向对象基础

面向对象的三大特性（有些认为是四大特性）：

+ ``封装``:隐藏对象具体的属性和实现细节，之对外提供公共的访问方式。

+ ``继承``:从已知类中派生出新类，新类吸收了已有类所有的数据属性和行为，并扩展了新功能(Java只允许单继承)。

+ ``多态``:分为:

  * `编译时多态`:方法的重载

  * `运行时多态`:Java运行时根据调用该方法的实例类型来决定调用哪个方法则被称为运行时多态。

+ ``抽象``:只定义方法名、参数和返回值，不定义方法的具体实现，交由子类进行重写。(有些说法认为不算特性)

### 封装
```Java
class Student{
    //成员变量
    private String name;
    private int age;
    private int ID;

    //构造方法(无参)
    public Student(){  
    }

    //构造方法(有参)
    public Student(String name, int age, int ID){  
        this.name = name;
        this.age = age;
        this.ID = ID;
    }

    //变异器set方法
    public void setName(String name){
      this.name = name;
    }

    //访问器get方法
    public String getName(){
      return name;
    }
}
```

#### 访问权限修饰符

Java通过访问权限修饰符来控制外部对对象的访问。
Java一共有四种访问权限修饰符(按访问权限由大到小)：

|  访问权限修饰符| 作用域|  权限 |
| :------------- | :------------- | :------------- |
| `public`| 公共访问权限。|公共范围|
| `default`| 默认包访问权限，不写修饰符。|包范围|
| `protected`| 继承访问权，只有继承的类和本身可以访问。|子类范围|
| `private`| 私有访问权，只有本身可以访问。|自身|

### 继承
关键词：`extend`

```Java
public class Father{
  //protected修饰的方法和属性 子类可继承
  protected String XingShi;
}
public class Sun extend Father{
  //子类可以继承父类用protected、和public修饰的属性和方法
}
```
Java只允许单继承，也就是只能单独继承一个父类。

#### 实例化过程
当子类进行实例化时，先实例化父类对象，再实例化子类对象。

>
在实例化子类对象的时候，首先调用了子类对象的构造方法，而子类构造函数中第一行隐式加载一个surper()访问父类的构造方法，所以优先初始化了父类的对象。
(不用尝试使用this()来阻止隐式的super()了，this()要求调用本类的另一个构造函数，依然会调用隐式的surper()来先初始化父类对象)

使用final修饰类可以阻止类被继承，这个类将成为最终类，而无法被继承。

#### 重写
子类可以重写(覆盖)父类的方法，使用子类的方式实现这个方法。

>
父类：几何体
子类：圆形、矩形、三角形
子类的面积方法需要覆盖掉父类的面积方法，而且每个子类对于这个方法的具体实现都不同(圆形有圆形的面积计算公式，矩形有矩形的，三角形有三角形的)。

父类可以使用final来修饰一个方法，使这个方法变为最终方法无法被覆盖。

### 多态
编译期多态：是指方法的重载。

通常说的多态主要是指运行期多态。`父类引用 引用名 = new 子类对象;`

```Java
//以collection为例
Collection<Object> arrayCollection = new ArrayList<>();
Collection<Object> setCollection = = new HashSet<>();
```

当使用setCollection.add()方法时得到的是Set方法的add()方法，编译器会将setCollection看作一个HashSet对象。不允许重复元素存入setCollection。
同理arrayCollection也是如此，编译器会将其当作是ArrayList对象，调用了ArrayList的add()方法。

#### 多态的条件
1. 要有要有子类继承关系或者接口实现关系。
2. 要有要有重写方法。(并非必备条件，但是不重写干嘛用多态....)
3. 父类引用指向子类对象。

#### 多态的局限性
1. 两个类必须有关联，要么是继承关系，要么是实现关系。
2. 只能调用父类引用有的方法，无法调用子类特有的方法。

#### 多态的好处

>
多态的好处：
 1. 可替换性（substitutability）。多态对已存在代码具有可替换性。例如，多态对圆Circle类工作，对其他任何圆形几何体，如圆环，也同样工作。
 2. 可扩充性（extensibility）。多态对代码具有可扩充性。增加新的子类不影响已存在类的多态性、继承性，以及其他特性的运行和操作。实际上新加子类更容易获得多态功能。例如，在实现了圆锥、半圆锥以及半球体的多态基础上，很容易增添球体类的多态性。
 3. 接口性（interface-ability）。多态是超类通过方法签名，向子类提供了一个共同接口，由子类来完善或者覆盖它而实现的。如图8.3 所示。图中超类Shape规定了两个实现多态的接口方法，computeArea()以及computeVolume()。子类，如Circle和Sphere为了实现多态，完善或者覆盖这两个接口方法。
 4. 灵活性（flexibility）。它在应用中体现了灵活多样的操作，提高了使用效率。
 5. 简化性（simplicity）。多态简化对应用软件的代码编写和修改过程，尤其在处理大量对象的运算和操作时，这个特点尤为突出和重要。

### 抽象
修饰符：`abstract`

用来修饰类和方法，被修饰的类称为抽象类。`抽象类不能实例化`

如果一个类中的方法被修饰为抽象方法，则这个类也需要修饰为抽象类。只有抽象类的抽象方法被其子类重写提供了具体的方法体内容之后才能进行实例化。

``如果子类继承的是抽象类，那么子类要么重写抽象类的抽象方法，要么变为一个抽象的子类。``


#### 抽象类的多态

抽象类也可以作为引用，但是new对象需要使用子类，抽象方法调用的是子类重写过的方法。

#### 抽象类的特点

1. 抽象方法只能定义在抽象类中，抽象类和抽象方法必须由abstract关键字修饰（可以描述类和方法，不可以描述变量）。
2. 抽象方法只定义方法声明，并不定义方法实现。
3. 抽象类不可以被创建对象(实例化)。
4. 只有通过子类继承抽象类并覆盖了抽象类中的所有抽象方法后，该子类才可以实例化。否则，该子类还是一个抽象类。
5. 抽象类只能单继承。

#### 抽象类的细节问题

1. 抽象类是否有构造函数？
抽象类本身就是一个类，只不过其中有未实现的方法(添加了abstract的方法)。所以抽象类具有构造函数，用于提供给子类进行初始化使用。

2. 抽象类能不能定义非抽象方法？
抽象类可以定义非抽象方法，也可以整个类都不定义抽象方法，这种情况主要是为了禁止使用这个类创建对象。

3. 抽象关键字abstract和哪些关键字不可共存？

`final`作用：禁止继承，禁止重写，与abstract互斥。

`private`作用：私有化禁止继承，与abstract互斥。

`static`作用：在程序中仅存在一个被static描述的静态方法，需要在程序执行前进行初始化，抽象方法没有方法体所以无法进行初始化。


## surper 与 this的用法

`surper关键字代表父类`，
`this关键字代表对象本身`。

>
使用super&this调用成员变量和方法
1. 可以使用super访问父类被子类隐藏的变量或覆盖的方法。当前类如果是从超类继承而来的，当调用super.XX()就是调用基类版本的XX（）方法。见示例1。
2. 当类中有两个同名变量，一个属于类（类的成员变量），而另一个属于某个特定的方法（方法中的局部变量），使用this区分成员变量和局部变量。见示例2。


示例1：


```Java
class Person {
    protected void print() {
       System.out.println("The print() in class Person.");
    }
}

public class DemoSuper extends Person {
    public void print() {
       System.out.println("The print() in class DemoSuper.");
       super.print();// 调用父类的方法
    }

    public static void main(String[] args) {
       DemoSuper ds = new DemoSuper();
       ds.print();
    }
}
```


示例2：


```Java
public class DemoThis {
    private String name;

    public void setName(String name) {
       this.name = name;// 前一个name是private name；后一个name是setName中的参数。
    }
}
```

>
使用this表示当前调用方法的对象引用
假设你希望在方法的内部获得对当前对象的引用，可使用关键字this。this关键字只能在方法内部使用，表示对“调用方法的那个对象”的引用。见示例3。

示例3


```Java
Button bn;
…
bn.addActionListener(this);
```

>
使用super&this调用构造函数
super（参数）：调用基类中的某一个构造函数（应该为构造函数中的第一条语句）。见示例4。
this（参数）：调用本类中另一种形成的构造函数（应该为构造函数中的第一条语句）。 见示例5。

示例4


```Java
class Person {
    public static void prt(String s) {
       System.out.println(s);
    }

    Person() {
       prt("A Person.");
    }

    Person(String name) {
       prt("A person name is:" + name);
    }
}

public class Chinese extends Person {
    Chinese() {
       super();// 调用父类构造函数。
       prt("A chinese.");
    }

    Chinese(String name) {
       super(name);// 调用父类具有相同形参的构造函数。
       prt("his name is:" + name);
    }

    public static void main(String[] args) {
       Chinese cn = new Chinese();
       cn = new Chinese("kevin");
    }
}
结果：
> A Person.
> A chinese.
> A person name is:kevin
> his name is:kevin
```


示例5

```Java
Point(int a,int b){
    x=a;
    y=b;
}
Point(){
    this(1,1); //调用point(1,1),必须是第一条语句。
}
```


### 使用super&this应该注意些什么？

>
 1. 调用super()必须写在子类构造方法的第一行，否则编译不通过。每个子类构造方法的第一条语句，都是隐含地调用super()，如果父类没有这种形式的构造函数，那么在编译的时候就会报错。
 2. super()和this()类似,区别是，super从子类中调用父类的构造方法，this()在同一类内调用其它方法。
 3. super()和this()均需放在构造方法内第一行。
 4. 尽管可以用this调用一个构造器，但却不能调用两个。
 5. this和super不能同时出现在一个构造函数里面，因为this必然会调用其它的构造函数，其它的构造函数必然也会有super语句的存在，所以在同一个构造函数里面有相同的语句，就失去了语句的意义，编译器也不会通过。
 6. this()和super()都指的是对象，所以，均不可以在static环境中使用。包括：static变量,static方法，static语句块。
 7. 从本质上讲，this是一个指向本对象的指针, 然而super是一个Java关键字。


两者主要应用在方法中，在构造函数中只能被放置在第一行，所用分别为：
`surper()表示调用父类的构造方法`，
`this()表示调用本类的其他构造方法`。

当使用在方法体中时，
`surper.方法名表示调用父类被子类覆盖的这个方法`，
`this.变量名表示调用自身成员变量`。
