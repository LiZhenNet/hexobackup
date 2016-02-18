title: "JavaScript的this关键字"
date: 2016-02-17 15:16:59
categories: JavaScript 
description: "this关键字指向谁呢?   JavaScript中的this含义要丰富得多，它可以是全局对象、当前对象或者任意对象，这完全取决于函数的调用方式。"
tags: 
- JavaScript

---
### 结论
不多说,想给出结论 关于this到底指向谁这个问题，我很同意鸟哥给出这么一个结论 **”This关键字永远都指向函数(方法)的所有者”**(参考文章1).


### 作为函数调用时
```
       var name="global";
       function showname(){
           alert(this.name);
       }
       //global
```
直接调用函数属于全局性调用，因此this指向全局对象(window对象)，this.name为"global"。

### 作为构造函数调用时
```
        var name="global";
        function People(name){
            this.name=name;
        }
        var p = new People('people');
        alert(name);
        //global
        alert(p.name);
        //people
```
JavaScript很特殊，并没有类的概念，JavaScript中的构造函数也很特殊，如果不使用new调用，则和普通函数一样，为了跟上一种情况区别，分别alert全局的name和新创建的对象p的name，可以看出this指向构造函数的所有者--新创建的对象p。

### 作为对象方法调用时
```
        var name="global";
        var people={
            name:"obj",
            showname:function(){
                alert(this.name);  
            }
        };
        people.showname();
        // obj
```
 showname作为people的方法调用时,方法的所有者为people,所以this.name为people的name属性"obj"。
### apply(call)调用时 
 在 JavaScript 中函数也是对象,apply和call就是函数对象的方法。他们允许改变this关键字的指向。
 ```
           var name="global";
           function showname(){
               alert(this.name);
           }
           var people={};
           people.name="people";
           people.showname=showname;
           people.showname.apply(people);
           //people
           people.showname.apply();
           //global
 ```
 apply(call)的第一个参数就表示改变后的调用这个函数的对象,所以people.showname.apply(people);this指向函数所有者people对象,所以第一次输出为people,apply()的参数为空时，默认调用全局对象，所以第二次输出为global。
#### 参考文章：

1. [laruence: 深入理解Javascript之this关键字](http://www.laruence.com/2009/09/08/1076.html) 
2. [阮一峰：Javascript的this用法](http://www.ruanyifeng.com/blog/2010/04/using_this_keyword_in_javascript.html)
