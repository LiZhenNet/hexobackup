title: "设计模式六大原则-SOLID" 
date: 2016-04-04 20:53:43 
categories: 设计模式 
description: "理解设计模式六个原则：单一职责原则、开闭原则、里氏代替原则、接口隔离原则、依赖倒置原则"
tags:
- 设计模式
- 面向对象
---
## S-单一职责原则(Single Responsibility Principle)

### 定义
> 单一职责原则：一个类应该只有一个发生变化的原因。  
> There should never be more than one reason for a class to change.

### 解释
  说到单一职责原则，很多人都会不屑一顾。因为它太简单了，但是《设计模式之禅》中有这么一句话：
“只要你想和别人争执、怄气或者是吵架，这个原则是屡试不爽的。看到一个接口或类是这样或那样设计的，你就问一句：‘你设计的类符合单一职责原则吗？’保准
对方立马‘萎缩’掉”,可见基本上很少人能做到自己写的类符合单一职责原则。同样以该书中所举的例子：我们设计一个IPhone的接口包含三个方法：拨号、通话、挂机
，我们可能写出来的代码如下：
``` csharp
public interface IPhone 
{
    //拨通电话
    public void dial(String phoneNumber);
    //通话
    public void chat(Object o);
    //通话完毕，挂电话
    public void hangup();
}
```
上面是书中的反例，因为实际Iphone包含了两个职责：一个是协议管理，一个是数据传送。dial()和hangup()两个方法实现的是协议管理，分别负责拨号接通和挂机；chat()实现
的是数据的传送，可能需要再分离出两个接口：IConnectionManager和IDataTransfer。     
但是我觉得80%以上才程序员会写出上面代码,:-D，因为，考虑拆分职责浪费时间，同时拆分职责增加系统类文件数量，系统复杂度增加，考虑到有些业务的不可变性，拆分职责带来的收益可能微乎其微。

### 好处
1. 职责更明确，可读性提高。
2. 职责分离，可维护性提高，变更引起的风险降低。

## O-开闭原则(Open-Closed Principle)

### 定义
> 开闭原则：软件实体(类、模块、函数等)应该对扩展开放，对修改关闭。   
> Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification.

### 解释
   很多人看到上面的定义会有些疑问，特别是“对修改关闭”，其实对修改关闭并不是禁止修改，而是禁止通过修改已有的代码来实现变化，通过扩展来实现变化。
例如：
```csharp
public interface IBook
{
    //获取价格
    public int getPrice();
}
```
我们创建接口IBook，通过getPrice方法来获取当前数的价格，声明Book类来实现IBook接口，当我们需要打折时，只需增加子类OffBook，覆写getPrice方法，   
替换Book类即可，当我们那天不需要打折时只需再替换回来，同时我们没有修改之前的类，也保持了系统历史的纯洁性。

### 好处
1. 提高可维护性，维护人员只需重新建个新子类来实现变更，无需修改原有代码。
2. 提高复用性

## L-里氏替换原则(Liskov Substitution Principle)

### 定义
> 如果对每一个类型为S的对象o1，都有类型为T的对象o2，使得以T定义的所有程序P在所在的对象o1都代换为o2时，
> 程序P的行为没有发生变化，那么类型S是类型T的子类型。   
> If for each object o1 of type S there is an object o2 of type T such that for all programs P defined in terms of T, 
> the behavior of P is unchanged when o1 is substituted for o2 then S is a subtype of T

### 解释
  简单的解释上面的定义就是程序P中含有类型T，当我们使用T的子类型S，替换类型T时，程序P的行为没有变化，也就是说，在软件开发过程中，
子类替换父类后，程序的行为是一样的。

## L-迪米特法则(Law of Demeter)

### 定义
> 一个对象应该对其他对象有最少的了解
> Only talk to your immediate friends
### 解释
 迪米特法则也叫最少知识原则，指的是一个对象应当对其他对象有尽可能少的了解。
也就是说，一个对象应尽量少的与其他对象之间发生相互作用，使得对象相对独立，这样当一个对象需要修改时，影响其他对象就会越少，扩展起来更加容易。

## I-接口隔离原则(Interface Segregation Principle)

### 定义
> 客户端不应该依赖它不需要的接口。
> Clients should not be forced to depend upon interfaces that they don't use.

### 解释
  接口隔离原则的含义是将一庞大的接口细化成多个接口，一个类实现一个庞大的接口不如实现多个接口零活。
很多人会觉的接口隔离原则跟单一职责原则很相似，其实有所不同单一职责原则原注重的是职责分离；而接口隔离原则注重对接口依赖的隔离。

## D-依赖倒置原则(Dependence Inversion Principle)

### 定义
> 高层模块不应该依赖低层模块，二者都应该依赖其抽象；抽象不应该依赖细节；细节应该依赖抽象。
> High level modules should not depend upon low level modules.Both should depend upon abstractions.
Abstractions should not depend upon details.Details should depend upon abstractions.

### 解释
高层模块与底层模块：每一个逻辑的实现都是由原子逻辑组成的，不可分割的原子逻辑就是低层模块，原子逻辑的再组装就是高层模块。  
抽象与细节：在C#(Java)语言中，抽象就是指接口或抽象类，两者都是不能直接被实例化的；细节就是接口或抽象类的实现类。  
依赖倒置原则更加精简的定义就是“面向接口编程”，接口相比实现更加稳定，以接口为基础搭建起来的架构比以实现为基础搭建起来的架构要稳定的多。  
