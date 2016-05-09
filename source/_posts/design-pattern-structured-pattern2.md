title: "设计模式-结构型模式2(享元、桥接、装饰)"
date: 2016-05-09 12:05:56
categories: 设计模式 
description: "设计模式-创建型模式2：享元模式、桥接模式、装饰模式"
tags:

- 享元模式
- 桥接模式
- 装饰模式
---

设计模式-创建型模式：享元模式、桥接模式、装饰模式
<!-- more -->

## 享元模式

### 定义

>使用共享对象可有效地支持大量的细粒度的对象。(Use sharing to support large numbers of fine-grained objects efficiently.)

### 实现

```csharp
    public abstract class Flyweight
    {
        //内部状态
        public string intrinsic { get; set; }
        //外部状态
        protected readonly string Extrinsic;
        //要求享元角色必须接受外部状态
        public Flyweight(string _Extrinsic)
        {
            this.Extrinsic = _Extrinsic;
        }
        //定义业务操作
        public abstract void operate();
    }
    public class ConcreteFlyweight1 : Flyweight
    {
        //接受外部状态
        public ConcreteFlyweight1(string _Extrinsic) :base(_Extrinsic)
        {
        }
        public override void operate()
        {
            Console.WriteLine(Extrinsic);
        }
    }
    public class FlyweightFactory
    {
        //定义一个池容器
        private static Dictionary<string, Flyweight> pool = new Dictionary<string, Flyweight>();
        //享元工厂
        public static Flyweight getFlyweight(string Extrinsic)
        {
            //需要返回的对象
            Flyweight flyweight = null;
            //在池中没有该对象
            if (pool.ContainsKey(Extrinsic))
            {
                flyweight = pool[Extrinsic];
            }
            else
            {
                //根据外部状态创建享元对象
                flyweight = new ConcreteFlyweight1(Extrinsic);
                //放置到池中
                pool.Add(Extrinsic, flyweight);
            }
            return flyweight;
        }
    }
```

### 使用场景

1. 系统中存在大量的相似对象。
2. 细粒度的对象都具备较接近的外部状态，而且内部状态与环境无关，也就是说对象没有特定身份。
3. 需要缓冲池的场景。

## 桥接模式

### 定义

> 将抽象和实现解耦，使得两者可以独立地变化。(Decouple an abstraction from its implementation so that the two can vary independently.)

### 实现

```csharp
    /// <summary>
    /// 实现化角色
    /// </summary>
    public interface Implementor
    {
        //基本方法
        void doSomething();
    }
    /// <summary>
    /// 具体实现化角色
    /// </summary>
    public class ConcreteImplementor : Implementor
    {
        public void doSomething()
        {            
            //业务逻辑处理
            Console.WriteLine("doSomething");
        }
    }
    /// <summary>
    /// 抽象化角色
    /// </summary>
    public abstract class Abstraction
    {
        //定义对实现化角色的引用
        private Implementor imp;
        //约束子类必须实现该构造函数
        public Abstraction(Implementor _imp)
        {
            this.imp = _imp;
        }
        //自身的行为和属性
        public void request()
        {
            this.imp.doSomething();
        }
        //获得实现化角色
        public Implementor getImp()
        {
            return imp;
        }
    }
    /// <summary>
    /// 具体抽象化角色
    /// </summary>
    public class RefinedAbstraction : Abstraction
    {
        //覆写构造函数
        public RefinedAbstraction(Implementor _imp) : base(_imp)
        {

        }

        //修正父类的行为
        public void request()
        {
            /*
            * 业务处理...
            */
            base.request();
            base.getImp().doSomething();
        }
    }
    //定义一个实现化角色
    Implementor imp = new ConcreteImplementor();
    //定义一个抽象化角色
    Abstraction abs = new RefinedAbstraction(imp);
    //执行行文
    abs.request();
    
```

### 优点

1. 抽象和实现分离， 它完全是为了解决继承的缺点而提出的设计模式。在该模式下，实现可以不受抽象的约束，不用再绑定在一个固定的抽象层次上
2. 优秀的扩充能力
3. 实现细节对客户透明

### 使用场景
1. 接口或抽象类不稳定的场景
2. 重用性要求较高的场景，设计的颗粒度越细，则被重用的可能性就越大，而采用继承则受父类的限制，不可能出现太细的颗粒度。


## 装饰模式

### 定义

> 动态地给一个对象添加一些额外的职责。就增加功能来说，装饰模式相比生成子类更为灵活。(Attach additional responsibilities to an object dynamically keeping the same interface.Decorators provide a flexible alternative to subclassing for extending functionality.)

### 实现

```csharp
    /// <summary>
    /// Component抽象构件
    /// </summary>
    public abstract class Phone
    {
        public abstract void call();
    }
    /// <summary>
    /// ConcreteComponent 具体构件
    /// </summary>
    public class AndroidPhone : Phone
    {
        //具体实现
        public override void call()
        {
            Console.WriteLine("打电话");
        }
    }
    /// <summary>
    /// Decorator装饰角色
    /// </summary>
    public abstract class PhoneDecorator : Phone
    {
        private Phone phone = null;
        //通过构造函数传递被修饰者
        public PhoneDecorator(Phone _phonet)
        {
            this.phone = _phonet;
        }
        //委托给被修饰者执行
        public override void call()
        {
            this.phone.call();
        }
    }
    /// <summary>
    /// 具体装饰角色
    /// </summary>
    public class ConcreteDecorator : PhoneDecorator
    {
        //定义被修饰者
        public ConcreteDecorator(Phone _phone) : base(_phone)
        {
        }
        //定义自己的修饰方法 获取通话记录
        private void LogPhoneCall()
        {
            Console.WriteLine("获取通话记录");
        }

        public override  void call()
        {
            LogPhoneCall();
            base.call();
        }
    }
    
    Phone phone = new AndroidPhone();
    phone = new ConcreteDecorator(phone);
    phone.call();
```
### 优点

1. 装饰类和被装饰类可以独立发展，而不会相互耦合。换句话说，Component类无须知道Decorator类，Decorator类是从外部来扩展Component类的功能，而Decorator也不用知道具体的构件。
2. 装饰模式是继承关系的一个替代方案。我们看装饰类Decorator，不管装饰多少层，返回的对象还是Component，实现的还是is-a的关系。
3. 装饰模式可以动态地扩展一个实现类的功能。
