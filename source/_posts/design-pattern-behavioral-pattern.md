title: "设计模式-行为型模式(模板方法模式、命令模式、迭代器模式、观察者模式、中介者模式)"
date: 2016-05-10 09:00:15
categories: 设计模式
description: "设计模式-行为型模式：模板方法模式、命令模式、迭代器模式、观察者模式、中介者模式"
tags:
- 设计模式
- 面向对象
---

设计模式-行为型模式：模板方法模式、命令模式、迭代器模式、观察者模式、中介者模式
<!-- more -->

## 模板方法模式

### 定义
> 定义一个操作中的算法的框架，而将一些步骤延迟到子类中。使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。(Define the skeleton of an algorithm in an operation,deferring some steps to subclasses.Template Method lets subclasses redefine certain steps of an algorithm without changing the algorithm's structure.)

### 实现

```csharp
public abstract class Car
{
    //基本方法
    public abstract void start();
    public abstract void stop();
    //模板方法
    public void drive()
    {
        this.start();
        this.stop();
    }
}
public class BanzCar : Car
{
    public override void start()
    {
        Console.WriteLine("banz启动");
    }

    public override void stop()
    {
        Console.WriteLine("banz停止");
    }
}
public class BmwCar : Car
{
    public override void start()
    {
        Console.WriteLine("bmw启动");
    }

    public override void stop()
    {
        Console.WriteLine("bmw停止");
    }
}
Car banzCar = new BanzCar();
Car bmwCar = new BmwCar();
banzCar.drive();
bmwCar.drive();
```
### 优点
1.  封装不变部分，扩展可变部分
2.  提取公共部分代码，便于维护
3. 行为由父类控制，子类实现 ,符合开闭原则

## 命令模式

### 定义

> 将一个请求封装成一个对象，从而让你使用不同的请求把客户端参数化，对请求排队或者记录请求日志，可以提供命令的撤销和恢复功能。(Encapsulate a request as an object,thereby letting you parameterize clients with different requests, queue or log requests,and support undoable operations.)

###  实现

```csharp
public interface IBankCustomer
{
    void add(decimal amount);
    void subtract(decimal amount);
}
public class Customer1 : IBankCustomer
{
    private static decimal balance = 10000;
    public void add(decimal amount)
    {
        balance += amount;
        Console.WriteLine("Customer1余额：" + balance);
    }
    public void subtract(decimal amount)
    {
        balance -= amount;
        Console.WriteLine("Customer1余额：" + balance);
    }
}
public class Customer2 : IBankCustomer
{
    private static decimal balance = 10000;
    public void add(decimal amount)
    {
        balance += amount;
        Console.WriteLine("Customer2余额：" + balance);
    }
    public void subtract(decimal amount)
    {
        balance -= amount;
        Console.WriteLine("Customer2余额：" + balance);
    }
}

public abstract class Command
{
    protected Customer1 Customer1 = new Customer1();
    protected Customer2 Customer2 = new Customer2();
    public abstract void execute();
}
public class TransferToCustomer1Command : Command
{
    public override void execute()
    {
        this.Customer2.subtract(1000);
        this.Customer1.add(1000);
    }
}
public class TransferToCustomer2Command : Command
{
    public override void execute()
    {
        this.Customer1.subtract(100);
        this.Customer2.add(100);
    }
}

public class Invoker
{
    private Command command;
    //客户发出命令
    public void setCommand(Command command)
    {
        this.command = command;
    }
    //执行客户的命令
    public void action()
    {
        this.command.execute();
    }
}

Invoker invoker = new Invoker();
Command command = new TransferToCustomer1Command();
invoker.setCommand(command);
invoker.action();
command = new TransferToCustomer2Command();
invoker.setCommand(command);
invoker.action();
```

### 优点

1.  类间解耦，调用者角色与接收者角色之间没有任何依赖关系，调用者实现功能时只需调用Command抽象类的execute方法就可以，不需要了解到底是哪个接收者执行。
2. 可扩展性，Command的子类可以非常容易地扩展，而调用者Invoker和高层次的模块Client不产生严
重的代码耦合。

## 迭代器模式

###  定义

>它提供一种方法访问一个容器对象中各个元素，而又不需暴露该对象的内部细节。(Provide a way to access the elements of an aggregate object sequentially without exposing its underlying representation)

### 实现

```csharp
public interface IListCollection
{
    Iterator GetIterator();
}
public class ConcreteIntList : IListCollection
{
    private  int[] collection;
    public ConcreteIntList()
    {
        collection = new int[] { 0,1,2,3,4,5 };
    }
    public Iterator GetIterator()
    {
        return new ConcreteIterator(this);
    }
    public int Length
    {
        get { return collection.Length; }
    }
    public int GetElement(int index)
    {
        return collection[index];
    }
}

public interface Iterator
{
    bool MoveNext();
    Object GetCurrent();
    void Next();
    void Reset();
}
public class ConcreteIterator : Iterator
{
    private ConcreteIntList _list;
    private int _index=  0;
    public ConcreteIterator(ConcreteIntList list)
    {
        _list = list;
    }
    public bool MoveNext()
    {
        if (_index < _list.Length)
        {
            return true;
        }
        return false;
    }
    public Object GetCurrent()
    {
        return _list.GetElement(_index);
    }
    public void Reset()
    {
        _index = 0;
    }
    public void Next()
    {
        if (_index < _list.Length)
        {
            _index++;
        }

    }
}
IListCollection list = new ConcreteIntList();
Iterator iterator = list.GetIterator();
while (iterator.MoveNext())
{
    int i = (int)iterator.GetCurrent();
    Console.WriteLine(i.ToString());
    iterator.Next();
}
```
## 观察者模式

###  定义

>定义对象间一种一对多的依赖关系，使得每当一个对象改变状态，则所有依赖于它的对象都会得到通知并被自动更新。 (Define a one-to-many dependency between objects so that when one object changes state,all its dependents are notified and updated automatically.)

### 实现

```csharp
public delegate void NotifyEventHandler(object sender);
public class Notify
{
    public string message { get; set; }
    public Notify(string message)
    {
        this.message = message;
    }
    private NotifyEventHandler NotifyEvent;
    public void AddObserver(NotifyEventHandler ob)
    {
        NotifyEvent += ob;
    }
    public void RemoveObserver(NotifyEventHandler ob)
    {
        NotifyEvent -= ob;
    }
    public void Publish()
    {
        NotifyEvent?.Invoke(this);
    }
}
public class PayNotify : Notify
{
    public PayNotify(string message) : base(message)
    {
    }
}
public class Subscriber
{
    public string Name { get; set; }
    public Subscriber(string name)
    {
        this.Name = name;
    }

    public void Receive(Object obj)
    {
        PayNotify PayNotify = obj as PayNotify;

        if (PayNotify != null)
        {
            Console.WriteLine(this.Name+"接收到消息"+PayNotify.message);
        }
    }
}
PayNotify notify = new PayNotify("支付消息");
Subscriber subscriber1 = new Subscriber("小明");
Subscriber subscriber2 = new Subscriber("小强");
notify.AddObserver(new NotifyEventHandler(subscriber1.Receive));
notify.AddObserver(new NotifyEventHandler(subscriber2.Receive));
notify.Publish();
```
### 优点

1.  事件可以多次触发

## 中介者模式

### 定义

>用一个中介对象封装一系列的对象交互，中介者使各对象不需要显示地相互作用，从而使其耦合松散，而且可以独立地改变它们之间的交互。(Define an object that encapsulates how a set of objects interact.Mediator promotes loose coupling by keeping objects from referring to each other explicitly,and it lets you vary their interaction independently.)

### 实现

```csharp
public abstract class AbstractBankCustomer
{
    public decimal balance = 10000;
    public abstract void Transfer(decimal amount, AbstractMediator mediator);
}
public class Customer1 : AbstractBankCustomer
{
    public override void Transfer(decimal amount, AbstractMediator mediator)
    {
        mediator.TransferToCustomer2(amount);
        Console.WriteLine("Customer1余额：" + balance);
    }
}
public class Customer2 : AbstractBankCustomer
{

    public override void Transfer(decimal amount, AbstractMediator mediator)
    {
        mediator.TransferToCustomer1(amount);
        Console.WriteLine("Customer2余额：" + balance);
    }
}
public abstract class AbstractMediator
{
    protected AbstractBankCustomer Customer1;
    protected AbstractBankCustomer Customer2;
    public AbstractMediator(AbstractBankCustomer Customer1, AbstractBankCustomer Customer2)
    {
        this.Customer1 = Customer1;
        this.Customer2 = Customer2;
    }
    public abstract void TransferToCustomer1(decimal amount);
    public abstract void TransferToCustomer2(decimal amount);
}
public class MediatorPater : AbstractMediator
{
    public MediatorPater(AbstractBankCustomer Customer1, AbstractBankCustomer Customer2)
        : base(Customer1, Customer2)
    {
    }
    public override void TransferToCustomer1(decimal amount)
    {
        Customer2.balance -= amount;
        Customer1.balance += amount;
    }

    public override void TransferToCustomer2(decimal amount)
    {
        Customer1.balance -= amount;
        Customer2.balance += amount;
    }
}
AbstractBankCustomer Customer1 = new Mediator.Customer1();
AbstractBankCustomer Customer2 = new Mediator.Customer2();
AbstractMediator mediator = new MediatorPater(Customer1, Customer2);
Customer1.Transfer(1000, mediator);
Customer2.Transfer(100, mediator);
```
