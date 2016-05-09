title: "JavaScript面向对象编程"
date: 2016-02-22 10:25:52
description: "Javascript是一种基于对象（object-based）的语言，你遇到的所有东西几乎都是对象。但是，它又不是一种真正的面向对象编程（OOP）语言，因为它的语法中没有class（类）。"
categories: JavaScript
tags: 
- JavaScript
---
Javascript是一种基于对象（object-based）的语言，你遇到的所有东西几乎都是对象。但是，它又不是一种真正的面向对象编程（OOP）语言，因为它的语法中没有class（类）。
<!-- more -->
**主要是提取了阮一峰老师文章，欢迎大家点击下面的参考文章阅读原文**

Javascript是一种基于对象（object-based）的语言，你遇到的所有东西几乎都是对象。但是，它又不是一种真正的面向对象编程（OOP）语言，因为它的语法中没有class（类）。
## 了解constructor&&prototype的用途
### 原始模式
假定我们把猫看成一个对象，它有"名字"和"颜色"两个属性,我们需要根据这个原型对象的规格（schema），生成两个实例对象。
```javascript
    var cat1 = {};
　　　　cat1.name = "大毛"; 
　　　　cat1.color = "黄色";
    var cat2 = {};
　　　　cat2.name = "二毛";
　　　　cat2.color = "黑色";
```
这样的写法如果在实例和属性非常多的情况下，代码重复，写起来非常麻烦，而且还容易遗漏掉属性。
### 原始模式改进
我们设计一个函数来生成对象
```javascript
    function Cat(name,color){
    　　　　return {
    　　　　　　name:name,
    　　　　　　color:color
    　　　　}
    　　}
  　var cat1 = Cat("大毛","黄色");
　　var cat2 = Cat("二毛","黑色");
```
但是这样写还是存在一个问题：没有办法来体现出cat1和cat2是同一个原型对象的实例。
### constructor属性的引入
为了解决上面的问题，javascript提供了constructor属性，在使用new运算符生成新实例的时候会将构造函数赋值给constructor属性。
```javascript
　　function Cat(name,color){
　　　　this.name=name;
　　　　this.color=color;
　　}
　　var cat1 = new Cat("大毛","黄色");
　　var cat2 = new Cat("二毛","黑色");
　　alert(cat1.constructor == Cat); 
　　//true
　　alert(cat2.constructor == Cat); 
　　//true
```
Javascript还提供了一个instanceof运算符，验证原型对象与实例对象之间的关系。
```javascript
　　alert(cat1 instanceof Cat); 
　　//true
　　alert(cat2 instanceof Cat);
　　//true
```
### new运算符的缺点
我们现在为Cat对象添加一个不变的属性"type"（种类），再添加一个方法eat（吃老鼠）：
```javascript
　function Cat(name,color){
　　　　this.name = name;
　　　　this.color = color;
　　　　this.type = "猫科动物";
　　　　this.eat = function(){alert("吃老鼠");};
　　}
　　var cat1 = new Cat("大毛","黄色");
　　var cat2 = new Cat ("二毛","黑色");
　　alert(cat1.eat == cat2.eat); 
　　//false
```
为什么cat1.eat不等于cat2.eat呢？因为每一个实例对象，都有自己的属性和方法的副本。
这也是new运算符的缺点：无法共享属性和方法。这一缺点造成了内存的浪费。
### prototype属性的引入
为了解决上面的问题javascript的设计者添加了prototype属性，该包含一个对象（以下简称"prototype对象"），所有实例对象需要共享的属性和方法，都放在这个对象里面；那些不需要共享的属性和方法，就放在构造函数里面。实例对象一旦创建，将自动引用prototype对象的属性和方法。也就是说，实例对象的属性和方法，分成两种，一种是本地的，另一种是引用的。
我们可以把上面的代码改写为：
```javascript
　　function Cat(name,color){
　　　　this.name = name;
　　　　this.color = color;
　　}
　　Cat.prototype.type = "猫科动物";
　　Cat.prototype.eat = function(){alert("吃老鼠")};
　　var cat1 = new Cat("大毛","黄色");
　　var cat2 = new Cat("二毛","黑色");
　　alert(cat1.eat == cat2.eat); 
　　//true
```
为了配合prototype属性，Javascript定义了一些辅助方法，帮助我们使用它:
1.isPrototypeOf()用来判断某个proptotype对象和某个实例之间的关系
```javascript
    alert(Cat.prototype.isPrototypeOf(cat1)); 
    //true
```
2.hasOwnProperty()用来判断某一个属性到底是本地属性，还是继承自prototype对象的属性。
```javascript
    alert(cat1.hasOwnProperty("name"));
    // true
　　alert(cat1.hasOwnProperty("type")); 
　　// false
```
3. in运算符用来判断某个实例是否含有某个属性，不管是不是本地属性。
```javascript
　　alert("name" in cat1); 
　　// true
　　alert("type" in cat1); 
　　// true
　　
　　//用来遍历某个对象的所有属性
　　for(var prop in cat1){
　　  alert("cat1["+prop+"]="+cat1[prop]);
　　}
```
## 构造函数的继承
### 构造函数绑定
第一种方法也是最简单的方法，使用call或apply方法，将父对象的构造函数绑定在子对象上.
```javascript
　　function Animal(){
　　　　this.species = "动物";
　　}
　　function Cat(name,color){
　　　　Animal.apply(this, arguments);
　　　　this.name = name;
　　　　this.color = color;
　　}
　　var cat1 = new Cat("大毛","黄色");
　　alert(cat1.species); 
　　// 动物
```
### prototype模式
如果"猫"的prototype对象，指向一个Animal的实例，那么所有"猫"的实例，就能继承Animal了。
```javascript
    Cat.prototype = new Animal();
　　Cat.prototype.constructor = Cat;
　　var cat1 = new Cat("大毛","黄色");
　　alert(cat1.species); 
　　// 动物
```
> Cat.prototype.constructor = Cat;这行的作用是什么？

任何一个prototype对象都有一个constructor属性，指向它的构造函数。当Cat.prototype = new Animal(); 时 Cat.prototype.constructor指向Animal。这显然会导致继承链的紊乱（cat1明明是用构造函数Cat生成的），因此我们必须手动纠正，将Cat.prototype对象的constructor值改为Cat。
### 直接继承prototype
第三种方法是对第二种方法的改进。由于Animal对象中，不变的属性都可以直接写入Animal.prototype。所以，我们也可以让Cat()跳过 Animal()，直接继承Animal.prototype。
```javascript
　　function Animal(){ }
　　Animal.prototype.species = "动物";
　  Cat.prototype = Animal.prototype;
　　Cat.prototype.constructor = Cat;
　　var cat1 = new Cat("大毛","黄色");
　　alert(cat1.species); 
　　// 动物
```
与前一种方法相比，这样做的优点是效率比较高（不用执行和建立Animal的实例了），比较省内存。缺点是 Cat.prototype和Animal.prototype现在指向了同一个对象，那么任何对Cat.prototype的修改，都会反映到Animal.prototype。
### 利用空对象作为中介
由于"直接继承prototype"存在上述的缺点，所以就有第四种方法，利用一个空对象作为中介。
```javascript
  　var F = function(){};
　　F.prototype = Animal.prototype;
　　Cat.prototype = new F();
　　Cat.prototype.constructor = Cat;
```
我们将上面的方法，封装成一个函数，便于使用。
```javascript
　function extend(Child, Parent) {
　　　　var F = function(){};
　　　　F.prototype = Parent.prototype;
　　　　Child.prototype = new F();
　　　　Child.prototype.constructor = Child;
　　　　Child.uber = Parent.prototype;
　　}
　　extend(Cat,Animal);
　　var cat1 = new Cat("大毛","黄色");
　　alert(cat1.species); 
　　// 动物
```
这个extend函数，就是YUI库如何实现继承的方法。
>　　Child.uber = Parent.prototype;意思是为子对象设一个uber属性，这个属性直接指向父对象的prototype属性。（uber是一个德语词，意思是"向上"、"上一层"。）这等于在子对象上打开一条通道，可以直接调用父对象的方法。这一行放在这里，只是为了实现继承的完备性，纯属备用性质。
### 拷贝继承
上面是采用prototype对象，实现继承。我们也可以换一种思路，纯粹采用"拷贝"方法实现继承。简单说，如果把父对象的所有属性和方法，拷贝进子对象
```javascript
   //把Animal的所有不变属性，都放到它的prototype对象上
   function Animal(){}
 　Animal.prototype.species = "动物";
 　function extend2(Child, Parent) {
　　　var p = Parent.prototype;
　　　var c = Child.prototype;
　　　for (var i in p) {
　　　　　c[i] = p[i];
　　　　　}
　　　c.uber = p;
　　}
　　extend2(Cat, Animal);
　　var cat1 = new Cat("大毛","黄色");
　　alert(cat1.species); 
　　// 动物
```
extend2函数的作用，就是将父对象的prototype对象中的属性，一一拷贝给Child对象的prototype对象。
## 非构造函数的继承
### 什么是"非构造函数"的继承？
比如，现在有一个对象，叫做"中国人"。
```javascript
　  var Chinese = {
　　　　nation:'中国'
　　};
```
还有一个对象，叫做"医生"。
```javascript
    var Doctor ={
　　　　career:'医生'
　　}
```
请问怎样才能让"医生"去继承"中国人"，也就是说，我怎样才能生成一个"中国医生"的对象？
这里要注意，这两个对象都是普通对象，不是构造函数，无法使用构造函数方法实现"继承"。
### object()方法
json格式的发明人Douglas Crockford，提出了一个object()函数，可以做到这一点。
```javascript
　　function object(o) {
　　　　function F() {}
　　　　F.prototype = o;
　　　　return new F();
　　}
　　var Doctor = object(Chinese);
　　Doctor.career = '医生';
　　alert(Doctor.nation); 
　　//中国
```
### 浅拷贝
把父对象的属性，全部拷贝给子对象，也能实现继承。
下面这个函数，就是在做拷贝：
```javascript
    function extendCopy(p) {
　　　　var c = {};
　　　　for (var i in p) { 
　　　　　　c[i] = p[i];
　　　　}
　　　　c.uber = p;
　　　　return c;
　　}
　　var Doctor = extendCopy(Chinese);
　　Doctor.career = '医生';
　　alert(Doctor.nation); 
　　// 中国
```
样的拷贝有一个问题。那就是，如果父对象的属性等于数组或另一个对象，那么实际上，子对象获得的只是一个内存地址，而不是真正拷贝，因此存在父对象被篡改的可能。
### 深拷贝
所谓"深拷贝"，就是能够实现真正意义上的数组和对象的拷贝。它的实现并不难，只要递归调用"浅拷贝"就行了。
```javascript
    function deepCopy(p, c) {
　　　　var c = c || {};
　　　　for (var i in p) {
　　　　　　if (typeof p[i] === 'object') {
　　　　　　　　c[i] = (p[i].constructor === Array) ? [] : {};
　　　　　　　　deepCopy(p[i], c[i]);
　　　　　　} else {
　　　　　　　　　c[i] = p[i];
　　　　　　}
　　　　}
　　　　return c;
　　}
```
目前，jQuery库使用的就是这种继承方法。

## 参考文章
1.[Javascript 面向对象编程（一）：封装](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_encapsulation.html)
2.[Javascript面向对象编程（二）：构造函数的继承](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance.html)
3.[Javascript面向对象编程（三）：非构造函数的继承](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance_continued.html)
4.[Javascript继承机制的设计思想](http://www.ruanyifeng.com/blog/2011/06/designing_ideas_of_inheritance_mechanism_in_javascript.html)