title: "设计模式-结构型模式(适配器、组合、代理、外观)"
date: 2016-04-12 09:43:01
categories: 设计模式 
description: "设计模式-创建型模式：适配器、组合、代理、外观"
tags:
- 适配器模式
- 组合模式
- 代理模式
- 外观模式

---

设计模式-创建型模式：适配器、组合、代理、外观
<!-- more -->

## 适配器模式

### 定义

> 将一个类的接口变换成客户端所期待的另一种接口，从而使原本因接口不匹配而无法在一起工作的两个类能够在一起工作。（Convert the interface of a class into another interface clients expect.Adapter lets classes work together that couldn't otherwise because of incompatible interfaces）

### 实现
```csharp
    /// <summary>
    /// Target目标接口
    /// </summary>
    public interface IGetData
    {
        List<string> GetData();
    }
    /// <summary>
    /// Target目标
    /// </summary>
    public class Db : IGetData
    {
        public List<string> GetData()
        {
           return new List<string>() {"DB1", "DB2" };
        }
    }
    /// <summary>
    /// Adaptee源
    /// </summary>
    public class Excel
    {
        public  List<Object> GetDataFromExcel()
        {
            return  new List<object>() {"Excel1","Excel2"};
        } 
    }
    /// <summary>
    /// Adapter适配器
    /// </summary>
    public class DbAdapter : Excel, IGetData
    {
        public List<string> GetData()
        {
            var result= base.GetDataFromExcel();
            return result.Select(x => x.ToString()).ToList();
        }
    }
    //原来的实现
    IGetData GetData= new Db();
    var result= GetData.GetData();
    //适配器实现
    IGetData GetData=new DbAdapter();

```
适配器模式是使用场景就像上面的例子一样，当你想要把一个已有的接口替换成另一个已有或新建立的接口，但是这个接口又不符合系统的原有接口。

## 组合模式

### 定义

>将对象组合成树形结构以表示“部分-整体”的层次结构，使得用户对单个对象和组合对象的使用具有一致性。(Compose objects into tree structures to represent part-whole hierarchies.Composite lets clients treat individual objects and compositions of objects uniformly.)


### 描述

#### 组合模式的几个角色
```java
public class Composite extends Component {
//构件容器
private ArrayList<Component> componentArrayList = new ArrayList<Component>();
//增加一个叶子构件或树枝构件
public void add(Component component){
this.componentArrayList.add(component);
}
//删除一个叶子构件或树枝构件
public void remove(Component component){
this.componentArrayList.remove(component);
}
//获得分支下的所有叶子构件和树枝构件
public ArrayList<Component> getChildren(){
return this.componentArrayList;
}
}
```
1. Component抽象构件角色
定义参加组合对象的共有方法和属性，可以定义一些默认的行为或属性
2. Leaf叶子构件
叶子对象，其下再也没有其他的分支，也就是遍历的最小单位。
3. Composite树枝构件
树枝对象，它的作用是组合树枝节点和叶子节点形成一个树形结构。

#### 优点

1. 高层模块调用简单，一棵树形机构中的所有节点都是Component，局部和整体对调用者来说没有任何区别。
2. 节点自由增加，如果想增加一个树枝节点、树叶节点是不是都很容易，只要找到它的父节点就成，非常容易扩展，符合开闭原则。  

#### 使用场景
维护和展示部分-整体关系的场景，如树形菜单、文件和文件夹管理。

## 代理模式

### 定义

>为其他对象提供一种代理以控制对这个对象的访问。(Provide a surrogate or placeholder for another object to control access to it)

### 实现
```csharp
    /// <summary>
    /// Subject抽象主题
    /// </summary>
    public interface IBuyMeal
    {
        string BuyMeal();
    }

    /// <summary>
    /// RealSubject具体主题
    /// </summary>
    public class LazyMan : IBuyMeal
    {
        public string BuyMeal()
        {
            return "自己买饭";
        }
    }

    /// <summary>
    /// 代理
    /// </summary>
    public class Proxy: IBuyMeal
    {
        //要代理哪个实现类
        private IBuyMeal subject = null;
        public Proxy(IBuyMeal _subject)
        {
            subject = _subject;
        }
        public string BuyMeal()
        {        
            //do something:取钱等等
            return subject.BuyMeal();
        }
    }
```
真实的角色就是实现实际的业务逻辑，不用关心其他非本职责的事务,例如例子中的取钱操作等等。

## 外观模式

### 定义

> 要求一个子系统的外部与其内部的通信必须通过一个统一的对象进行。门面模式提供一个高层次的接口，使得子系统更易于使用。(Provide a unified interface to a set of interfaces in a subsystem.Facade defines a higher-level interface that makes the subsystem easier to use.)

### 好处

1.  减少系统的相互依赖，如果我们不使用门面模式，外界访问直接深入到子系统内部，相互之间是一种强耦合关系。
2.  提高了灵活性，不管子系统内部如何变化，只要不影响到门面对象，任你自由活动。
3.  提高安全性，想让你访问子系统的哪些业务就开通哪些逻辑，不在门面上开通的方法，你休想访问到。



