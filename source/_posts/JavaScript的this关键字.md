title: "'JavaScript的this关键字'"
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
函数的最通常用法，属于全局性调用，因此this就代表全局对象Global。
### 作为对象方法调用时
```
        var name="global";
        var people={
            name:"obj",
            showname:function(){
                alert(this.name);  
            }
        };
       // obj
```
 showname作为people的方法调用时,方法的所有者为people,所以this.name为people的name属性"obj"
#### 参考文章：

1. [laruence: 深入理解Javascript之this关键字](http://www.laruence.com/2009/09/08/1076.html) 
2. [阮一峰：Javascript的this用法](http://www.ruanyifeng.com/blog/2010/04/using_this_keyword_in_javascript.html)
