---
layout: post
title: Java理论基础 对象
date: 2016-6-1
categories: blog
tags: [Java,Basis,Object]
description: Java理论基础
---
 Java理论基础 对象
 ================
>万物皆对象     ——《Thinking in Java》

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
>在实例化子类对象的时候，首先调用了子类对象的构造方法，而子类构造函数中第一行隐式加载一个surper()访问父类的构造方法，所以优先初始化了父类的对象。
(不用尝试使用this()来阻止隐式的super()了，this()要求调用本类的另一个构造函数，依然会调用隐式的surper()来先初始化父类对象)

使用final修饰类可以阻止类被继承，这个类将成为最终类，而无法被继承。

#### 重写
子类可以重写(覆盖)父类的方法，使用子类的方式实现这个方法。
>父类：几何体
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
#### 多态的局限性
1. 两个类必须有关联，要么是继承关系，要么是实现关系
2. 只能调用父类引用有的方法，无法调用子类特有的方法。

### 抽象
修饰符：`abstract`
用来修饰类和方法，被修饰的类称为抽象类。`抽象类不能实例化`
如果一个类中的方法被修饰为抽象方法，则这个类也需要修饰为抽象类。只有抽象类的抽象方法被其子类重写提供了具体的方法体内容之后才能进行实例化。
``如果子类继承的是抽象类，那么子类要么重写抽象类的抽象方法，要么变为一个抽象的子类。``
