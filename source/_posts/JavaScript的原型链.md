title: "'JavaScript的原型链'"
date: 2016-02-19 14:16:09
description: "涉及到继承这一块，Javascript只有一种结构，那就是：对象。在 javaScript中，每个对象都有一个指向它的原型（prototype）对象的内部链接。这个原型对象又有自己的原型，直到某个对象的原型为 null 为止（也就是不再有原型指向），组成这条链的最后一环。这种一级一级的链结构就称为原型链（prototype chain）。"
categories: JavaScript
tags: 
- JavaScript
---
涉及到继承这一块，Javascript只有一种结构，那就是：对象。在 javaScript中，每个对象都有一个指向它的原型（prototype）对象的内部链接。这个原型对象又有自己的原型，直到某个对象的原型为 null 为止（也就是不再有原型指向），组成这条链的最后一环。这种一级一级的链结构就称为原型链（prototype chain）。
## prototype和__proto__
- prototype是函数的一个属性，这个属性表示该对象的原型对象。
- 为了方便查看一个对象的原型，Firefox和Chrome中提供了"__proto__"这个非标准（不是所有浏览器都支持）的访问器（ECMA引入了标准对象原型访问器"Object.getPrototype(object)"）
```javascript
    function People(){};
    var x = new People();
    console.log(x.__proto__);
    /*
    Object{}
      ->constructor:function People()
      ->__proto__:Object
    */
    console.log(x.__proto__ === People.prototype);
    //true   
```
在JavaScript中，每个函数都有一个prototype属性，当一个函数被用作构造函数来创建实例时，该函数的prototype属性值将被作为原型赋值给所有对象实例（实例的__proto__属性），也就是说，所有实例的原型引用的是函数的prototype属性。
>我们看到__proto__还包含一个叫constructor的属性，该属性指定创建对象原型的函数。注意该属性值是一个指向函数自身的引用，而不是一个包含函数名称的字符串。
## 原型链
继续上面的代码
```javascript
    console.log(People.prototype.__proto__ === Object.prototype);
    //true
    console.log(Object.prototype);
    //Object{}
    console.log(Object.prototype.__proto__);
    //null
    console.log(Object.prototype.constructor);
    //function Object(){[native code]}
```
> 在JavaScript中，所有的对象都是基于Object；所有的对象都继承了Object.prototype的属性和方法
因为每个对象和原型都有原型，对象的原型指向对象的父，而父的原型又指向父的父，这种原型层层连接起来的就构成了原型链。
## 使用不同的方法来创建对象和生成原型链
### 使用普通语法创建对象
```javascript

    var o = {a: 1};
    // o这个对象继承了Object.prototype上面的所有属性
    // Object.prototype的原型为null。
    // 原型链如下:
    // o ---> Object.prototype ---> null
    
    var a = ["yo", "whadup", "?"];
    // 数组都继承于Array.prototype
    // 原型链如下:
    // a ---> Array.prototype ---> Object.prototype ---> null
    
    function f(){
      return 2;
    }
    // 函数都继承于Function.prototype
    // 原型链如下:
    // f ---> Function.prototype ---> Object.prototype ---> null
```
### 使用构造器创建对象
```javascript
    function Graph() {
      this.vertexes = [];
      this.edges = [];
    }
    
    Graph.prototype = {
      addVertex: function(v){
        this.vertexes.push(v);
      }
    };
    
    var g = new Graph();
    // g是生成的对象,他的自身属性有'vertexes'和'edges'.
    // 在g被实例化时,g.[[Prototype]]指向了Graph.prototype.
```
### 使用 Object.create 创建对象
```javascript
    var a = {a: 1}; 
    // a ---> Object.prototype ---> null
    
    var b = Object.create(a);
    // b ---> a ---> Object.prototype ---> null
    console.log(b.a); // 1 (继承而来)
    
    var c = Object.create(b);
    // c ---> b ---> a ---> Object.prototype ---> null
    
    var d = Object.create(null);
    // d ---> null
    console.log(d.hasOwnProperty); // undefined, 因为d没有继承Object.prototype
```
## 其他
- >在原型链上查找属性比较耗时，对性能有副作用，**试图访问不存在的属性时会遍历整个原型链**。
- >检测对象的属性是定义在自身上还是在原型链上，有必要使用hasOwnProperty 方法，所有继承自Object.proptotype的对象都包含这个方法。hasOwnProperty是JavaScript中唯一一个只涉及对象自身属性而不会遍历原型链的方法。

## 参考文章
1.[田小计划：彻底理解JavaScript原型](http://www.cnblogs.com/wilber2013/p/4924309.html)