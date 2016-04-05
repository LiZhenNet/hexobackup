title: "设计模式五大原则-SOLID" 
date: 2016-04-04 20:53:43 
categories: 设计模式 
description: "理解设计模式五个原则：单一职责原则、开闭原则、里氏代替原则、接口隔离原则、依赖倒置原则"
tags:
- 设计模式
- 面向对象
---
## S-单一职责原则(Single Responsibility Principle)

> 单一职责原则：一个类应该只有一个发生变化的原因。(There should never be more than one reason for a class to change.)

  说到单一职责原则，很多人都会不屑一顾。因为它太简单了，但是《设计模式之禅》中有这么一句话：
“只要你想和别人争执、怄气或者是吵架，这个原则是屡试不爽的。看到一个接口或类是这样或那样设计的，你就问一句：‘你设计的类符合单一职责原则吗？’保准
对方立马‘萎缩’掉”,可见基本上很少人能做到自己写的类符合单一职责原则。同样以该书中所举的例子：我们设计一个IPhone的接口包含三个方法：拨号、通话、挂机
，我们可能写出来的代码如下：
``` csharp
public interface IPhone {
    //拨通电话
    public void dial(String phoneNumber);
    //通话
    public void chat(Object o);
    //通话完毕，挂电话
    public void hangup();
}
```
上面是书中的反例，因为实际Iphone包含了两个职责：一个是协议管理，一个是数据传送。dial()和hangup()两个方法实现的是协议管理，分别负责拨号接通和挂机；chat()实现
的是数据的传送，可能需要再分离出两个接口：IConnectionManager和IDataTransfer。但是我觉得80%以上才程序员会写出上面代码,:-D


待续...