title: "设计模式-行为型模式(状态模式、策略模式、责任链模式、访问者模式、备忘录模式、解释器模式)"
date: 2016-05-11 09:55:29
categories: 设计模式
description: "设计模式-行为型模式：状态模式、策略模式、责任链模式、访问者模式、备忘录模式、解释器模式"
tags:
- 设计模式
- 面向对象
---

设计模式-行为型模式：状态模式、策略模式、责任链模式、访问者模式、备忘录模式、解释器模式
<!-- more -->

## 状态模式

### 定义

>当一个对象内在状态改变时允许其改变行为，这个对象看起来像改变了其类。(Allow an object to alter its behavior when its internal state changes.The object will appear to change its class.)

### 实现
```csharp
/// <summary>
/// Context 环境角色
/// </summary>
public class Context
{
    public readonly static OpenningState openningState = new OpenningState();
    public readonly static ClosingState closeingState = new ClosingState();
    private DoorState State;
    public DoorState getState()
    {
        return State;
    }
    public void setState(DoorState State)
    {
        this.State = State;
        this.State.setContext(this);
    }
    public void open()
    {
        this.State.open();
    }
    public void close()
    {
        this.State.close();
    }
}
/// <summary>
/// State 抽象状态角色
/// </summary>
public abstract class DoorState
{
    protected Context context;
    public void setContext(Context _context)
    {
        this.context = _context;
    }
    public abstract void open();
    public abstract void close();

}
/// <summary>
/// ConcreteState 具体状态角色
/// </summary>
public class OpenningState : DoorState
{
    public override void close()
    {
        base.context.setState(Context.closeingState);
        base.context.getState().close();
    }
    public override void open()
    {
        Console.WriteLine("门已经打开.");
    }
}
public class ClosingState : DoorState
{
    public override void close()
    {
        Console.WriteLine("门已经关闭.");
    }
    public override void open()
    {
        base.context.setState(Context.openningState);
        base.context.getState().open();
    }
}
Context context = new Context();
context.setState(new ClosingState());
context.open();
context.close();
```

## 策略模式

### 定义

>定义一组算法，将每个算法都封装起来，并且使它们之间可以互换。(Define a family of algorithms,encapsulate each one,and make them interchangeable.)

### 实现
```csharp
public interface IPriceStrategy
{
    decimal GetSalePrice(decimal price);
}
public class NormalPriceStrategy : IPriceStrategy
{
    public decimal GetSalePrice(decimal price)
    {
        return price;
    }
}
public class HalfPriceStrategy : IPriceStrategy
{
    public decimal GetSalePrice(decimal price)
    {
        return price/2;
    }
}
public class Context
{
    //抽象策略
    private IPriceStrategy strategy = null;
    //构造函数设置具体策略
    public Context(IPriceStrategy _strategy)
    {
        this.strategy = _strategy;
    }
    //封装后的策略方法
    public decimal GetSalePrice(decimal Price)
    {
       return this.strategy.GetSalePrice(Price);
    }
}
IPriceStrategy strategy = new HalfPriceStrategy();
Strategy.Context context = new Strategy.Context(strategy);
var price =context.GetSalePrice(1000);
```
### 好处

使用策略模式后，可以由其他模块决定采用何种策略，策略家族对外提供的访问接口就是封装类，简化了操作，同时避免了条件语句判断。

## 责任链模式

### 定义

>使多个对象都有机会处理请求，从而避免了请求的发送者和接受者之间的耦合关系。将这些对象连成一条链，并沿着这条链传递该请求，直到有对象处理它为止。(Avoid coupling the sender of a request to its receiver by giving more than one object a chance to handle the request.Chain the receiving objects and pass the request along the chain until an object handles it.)

### 实现
```csharp
public enum Level
{
    level1 = 1,
    level2 = 2
}
public class Request
{
    private Level level { get; set; }
    public Request(Level level)
    {
        this.level = level;
    }
    public Level getRequestLevel()
    {
        return this.level;
    }
}
public class Response
{

}
public abstract class Handler
{
    private Handler nextHandler;
    public Response handleMessage(Request request)
    {
        if (this.getHandlerLevel() == request.getRequestLevel())
        {
            return this.HandlerMethod(request);
        }
        else
        {
           return this.nextHandler.handleMessage(request);
        }
    }
    protected abstract Level getHandlerLevel();
    protected abstract Response HandlerMethod(Request request);
    public void setNextHandler(Handler _handler)
    {
        this.nextHandler = _handler;
    }
}
public class Level1Handler : Handler
{
    protected override Level getHandlerLevel()
    {
        return  Level.level1;
    }

    protected override Response HandlerMethod(Request request)
    {
        Console.WriteLine("Level处理");
        return new Response();
    }
}
public class Level2Handler : Handler
{
    protected override Level getHandlerLevel()
    {
        return Level.level2;
    }

    protected override Response HandlerMethod(Request request)
    {
        Console.WriteLine("Leve2处理");
        return new Response();
    }
}
Handler handler1 =new Level1Handler();
Handler handler2 =new Level2Handler();
handler1.setNextHandler(handler2);
Response response = handler1.handleMessage(new Request(Level.level1));
response = handler1.handleMessage(new Request(Level.level2));
```
### 优点

责任链模式非常显著的优点是将请求和处理分开。请求者可以不用知道是谁处理的，处理者可以不用知道请求的全貌

## 访问者模式

### 定义

>封装一些作用于某种数据结构中的各元素的操作，它可以在不改变数据结构的前提下定义作用于这些元素的新的操作。(Represent an operation to be performed on the elements of an object structure. Visitor lets you define a new operation without changing the classes of the elements on which it operates.)

### 实现

```csharp
public abstract class Element
{
    public abstract void doSomething();
    public abstract void accept(IVisitor visitor);
}
public class ConcreteElement1 : Element
{
    public override void doSomething()
    {
        throw new NotImplementedException();
    }

    public override void accept(IVisitor visitor)
    {
        visitor.visit(this);
    }
}
public class ConcreteElement2 : Element
{
    public override void doSomething()
    {
        throw new NotImplementedException();
    }

    public override void accept(IVisitor visitor)
    {
        visitor.visit(this);
    }
}
public interface IVisitor
{
    void visit(ConcreteElement1 el1);
    void visit(ConcreteElement2 el2);
}
public class Visitor : IVisitor
{
    public void visit(ConcreteElement1 el1)
    {
        el1.doSomething();
    }
    public void visit(ConcreteElement2 el2)
    {
        el2.doSomething();
    }
}
public class ObjectStruture
{
    public static Element createElement()
    {
        Random rand = new Random();
        if (rand.Next(100) > 50)
        {
            return new ConcreteElement1();
        }
        else
        {
            return new ConcreteElement2();
        }
    }
}
for (int i = 0; i < 10; i++)
{
    Element el = ObjectStruture.createElement();
    el.accept(new Visitor());
}
```

## 备忘录模式

###  定义

>在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。这样以后就可将该对象恢复到原先保存的状态。(Without violating encapsulation,capture and externalize an object's internal state so that the  object can be restored to this state later.)

### 实现

```csharp
public class Originator
{
    //内部状态
    private String state = "";
    public String getState()
    {
        return state;
    }
    public void setState(String state)
    {
        this.state = state;
    }
    public Memento createMemento()
    {
        return new Memento(this.state);
    }
    public void restoreMemento(Memento _memento)
    {
        this.setState(_memento.getState());
    }
}
public class Memento
{
    private String state = "";
    public Memento(String _state)
    {
        this.state = _state;
    }
    public String getState()
    {
        return state;
    }
    public void setState(String state)
    {
        this.state = state;
    }
}
public class Caretaker
{
    //备忘录对象
    private Memento memento;
    public Memento getMemento()
    {
        return memento;
    }
    public void setMemento(Memento memento)
    {
        this.memento = memento;
    }
}
//定义出发起人
Originator originator = new Originator();
//定义出备忘录管理员
Caretaker caretaker = new Caretaker();
//创建一个备忘录
caretaker.setMemento(originator.createMemento());
//恢复一个备忘录
originator.restoreMemento(caretaker.getMemento());

```

## 解释器模式

### 定义

>给定一门语言，定义它的文法的一种表示，并定义一个解释器，该解释器使用该表示来解释语言中的句子。(Given a language, define a representation for its grammar along with an interpreter that uses the representation to interpret sentences in the language.)
