title: "设计模式-创建型模式(单例、工厂、创建者、原型)"
date: 2016-04-06 15:31:11
categories: 设计模式 
description: "设计模式-创建型模式：单例模式、简单工厂模式、抽象工厂、创建者模式、原型模式"
tags:
- 设计模式
- 面向对象
- 单例模式
- 工厂模式
- 创建者模式
- 原型模式
---
## 单例模式

### 定义
> 确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例。

### 实现

```csharp
    public class Singleton
    {
        private static Singleton instance { get; set; }
        private Singleton() { }
        public static Singleton Instance
        {
            get
            {
                if (instance == null)
                {
                    instance = new Singleton();
                }
                return instance;
            }
        }
    }
```
上述代码为单例最简单的实现，但是如果执行过程的不同线程同时进入 Instance 属性 get 方法，那么可能会创建多个 Singleton 对象实例。  
下面利用 Double-Check Locking 实现线程安全的单例：
```csharp
    public class Singleton 
    { 
    private static volatile Singleton instance; 
    private static object syncRoot = new Object(); 
    private Singleton() {} 
    public static Singleton Instance 
    { 
        get  
        { 
            if (instance == null)  
            { 
                lock (syncRoot)  
                { 
                if (instance == null)  
                    instance = new Singleton(); 
                } 
            } 
            return instance; 
        } 
    } 
    } 
```
### 好处
1. 单例模式在内存中只有一个实例，减少了内存开支

## 简单工厂模式

### 定义
> 简单工厂模式是由一个工厂对象决定创建出哪一种产品类的实例。
### 实现
```csharp
    public abstract class DbConnection
    {
        public abstract void Connection();
    }
    public class MsConnection : DbConnection
    {
        public override void Connection()
        {
            Console.WriteLine("SQL Server is connecting...");
        }
    }
    public class OracleConnection : DbConnection
    {
        public override void Connection()
        {
            Console.WriteLine("Oracle is connecting...");
        }
    }
    public static class DbFactory
    {
        public static DbConnection GetConnection(string DbType)
        {
            switch (DbType)
            {
                case "MS":
                    return new MsConnection();
                case "Oracle":
                    return new OracleConnection();
                default:
                    throw new ArgumentException("DbType 无效");
            }
        } 
    }
```
上述代码为简单工厂模式的实现，DbConnection为抽象产品，MsConnection和OracleConnection为具体产品，由DbFactory(工厂)来统一创建。  
但是由于工厂类集中了所有产的创建，一旦工厂类出现问题，整个系统也出现了问题，其次新增产品就必须要修改工厂类，扩展性比较差。
### 好处
1. 客户端通过工厂来创建对象的方式进行解耦，高层模块值需要知道产品的抽象类，其他的实现类都不用关心，符合迪米特法则；
只依赖产品类的抽象也符合依赖倒置原则；使用产品子类替换产品父类也符合里氏替换原则。

## 工厂方法模式

### 定义
> 定义一个用于创建对象的接口，让子类决定实例化哪一个类。工厂方法使一个类的实例化延迟到其子类。

### 实现
```csharp
  public abstract class DbConnection
    {
        public abstract void Connection();
    }
    public class MsConnection : DbConnection
    {
        public override void Connection()
        {
            Console.WriteLine("SQL Server is connecting...");
        }
    }
    public class OracleConnection : DbConnection
    {
        public override void Connection()
        {
            Console.WriteLine("Oracle is connecting...");
        }
    }
    public interface IDbConnectionFactory
    {
         DbConnection GetConnection();
    }
    public class MsFactory : IDbConnectionFactory
    {
        public  DbConnection GetConnection()
        {
            return new MsConnection();
        }
    }
    public class OracleFactory : IDbConnectionFactory
    {
        public  DbConnection GetConnection()
        {
            return new OracleConnection();
        }
    }
```
对比上面的简单工厂模式，工厂方法模式将产品的创建交给对应的工厂，解决简单工厂存在的问题。

### 好处
1. 由工厂类对客户端和产品进行解耦，同时易于扩展，符合开放封闭原则。

## 抽象工厂

### 定义

> 为创建一组相关或相互依赖的对象提供一个接口，而且无须指定它们的具体类。

### 实现
```csharp
    public abstract class picture
    {
        public abstract void show();
    }
    public abstract class video
    {
        public abstract void show();
    }
    public class Windowspicture : picture
    {
        public override void show()
        {
            Console.WriteLine("Windows下展示图片方法");
        }
    }
    public class Linuxpicture : picture
    {
        public override void show()
        {
            Console.WriteLine("Linux下展示图片方法");
        }
    }
    public class Windowsvideo : video
    {
        public override void show()
        {
            Console.WriteLine("Windows下播放视频方法");
        }
    }
    public class Linuxvideo : video
    {
        public override void show()
        {
            Console.WriteLine("Linux下播放视频方法");
        }
    }

    public interface AbstractFactory
    {
         picture GetPicture();
         video GetVideo();
    }
    public class WindowsFactory : AbstractFactory
    {
        public picture GetPicture()
        {
            return new Windowspicture();
        }
        public video GetVideo()
        {
            return new Windowsvideo();
        }
    }
    public class LinuxFactory : AbstractFactory
    {
        public picture GetPicture()
        {
            return new Linuxpicture();
        }
        public video GetVideo()
        {
            return new Linuxvideo();
        }
    }
    
    AbstractFactory windowsFactory = new WindowsFactory();
    picture windowsPicture = windowsFactory.GetPicture();
    windowsPicture.show();
    video windowsVideo = windowsFactory.GetVideo();
    windowsVideo.show();
    AbstractFactory linuxFactory = new LinuxFactory();
    picture linuxPicture = linuxFactory.GetPicture();
    linuxPicture.show();
    video linuxVideo = linuxFactory.GetVideo();
    linuxVideo.show();
    
```
抽象方法的实现是跟上述的工厂方法模式的升级版，当产品有多个业务品种、业务分类时，就需要通过抽象工厂模式来实现。

## 创建者模式

### 定义

> 将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。

### 实现
```csharp
    public class Product
    {
        public List<string> parts = new List<string>();
    }

    public abstract class AbstractBuilder
    {
        public abstract void addpartA();
        public abstract void addpartB();
        public abstract Product GetProduct();
    }

    public class ConcreteBuilder : AbstractBuilder
    {
        Product product = new Product();
        public override void addpartA()
        {
            product.parts.Add("partA");
        }
        public override void addpartB()
        {
            product.parts.Add("partB");
        }

        public override Product GetProduct()
        {
            return product;
        }
    }

    public class Director
    {
        public void Construct(AbstractBuilder builder)
        {
            builder.addpartA();
            builder.addpartB();
        }
    }
    
   Director director = new Director();
   AbstractBuilder builder = new ConcreteBuilder();
   director.Construct(builder);
   Product product = builder.GetProduct();
```
### 好处
1. 客户端不必知道产品内部创建的细节，符合迪米特法则
2. 在产品类中的创建顺序不同产生了不同的影响时，易于扩展

## 原型模式

### 定义
> 用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。
### 实现
```csharp
    [Serializable]
    public class Prototype : ICloneable
    {
        public string PrototypeName { get; set; }
        public int PrototypeLength { get; set; }
        public object Clone()
        {
            MemoryStream ms = new MemoryStream();
            BinaryFormatter bf = new BinaryFormatter();
            bf.Serialize(ms, this);
            ms.Seek(0, 0);
            object obj = bf.Deserialize(ms);
            ms.Close();
            return obj;
        }
    }
    Prototype prototype = new Prototype();
    prototype.PrototypeLength = 10;
    prototype.PrototypeName = "clone";
    Prototype ConcretePrototype = prototype.Clone() as Prototype;
    prototype.PrototypeName = "prototype";
    Console.WriteLine(ConcretePrototype.PrototypeName);//"clone"深拷贝;
```
### 好处
1. 类初始化需要消化非常多的资源时，使用原型模式创建更省资源。
2. 直接在内存中拷贝，构造函数是不会执行，逃避构造函数的约束。