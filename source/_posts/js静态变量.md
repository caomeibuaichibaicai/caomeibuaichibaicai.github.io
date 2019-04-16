---
title: js静态变量以及静态方法(内含ts小笔记)
date: 2018-03-16 15:04:55
tags:
  - 静态变量
  - 原型上实例方法
  - typescript
categories: Js基础
---
### 1.js中的静态方法是无法被实例对象所调用的
例子如下：
```javascript
var BaseClass = function() {}; // var BaseClass=new Function();  
BaseClass.f1 = function(){//定义静态方法  
      alert(' This is a static method ');  
 }  
 BaseClass.f1();//This is a static method  
 var instance1 = new BaseClass();  
 instance1.f1();//instance1.f1 is not a function 
 ```
 这里实例化的对象instance就无法调用原型上面绑定的方法，若想要调用原型上的方法，即创建非静态的实例方法，这里有三种方法：
 **(1)在原型上面通过prototype来进行实例方法的绑定，如下：**
 ```javascript
 var BaseClass = function() {};  
 BaseClass.prototype.method1 = function(){  
       alert(' This is a instance method ');  
 }  
 var instance1 = new BaseClass();  
 instance1.method1();//This is a instance method
 ```
 **（2）在实例上直接定义方法，如下：**
 ```javascript
var BaseClass = function() {};  
var instance1 = new BaseClass();  
instance1.method1 = function(){  
     alert(' This is a instance method too ');  
 }   
instance1.method1();//This is a instance method too
```
**（3）在构造函数内部，通过this定义实例方法，如下：**
```javascript
var BaseClass = function() {  
  this.method1 = function(){  
    alert(' Defined by the "this" instance method');  
  }  
 };  
var instance1 = new BaseClass();  
instance1.method1();//Defined by the "this" instance method 
```
这三种定义实例方法的方法，若定义三个相同名字的实例方法，三种方法的优先级从大到小依次是：**实例直接定义>内部this定义>原型prototype绑定定义**，即实例上直接定义的实例方法会覆盖掉其他两种同时定义的实例方法，并且这三种创建的实例方法必须通过类的实例来进行访问。
在类（即构造函数）里面若使用var进行声明变量或者声明方法时，声明的都是局部的，局部变量或者方法是无法通过声明类的实例来进行访问的，其作用域只在内部可见。
### 2.js中明确来说没有真正意义上的静态变量
类对象上的prototype可以作为实例对象共同拥有的“静态变量”而存在，实例对象都可以访问到该变量，并且一个实例对象改变了该变量的值，其他实例对象访问到的“静态变量”都是改变后的值（真正意义上的静态变量具有此特性），但是呢，这个“静态变量”呢，类对象是直接访问不到的，而要通过类对象.prototype.变量名，这种方法来访问这个变量，所以本质上这个“静态变量”还是个实例变量。
### 3.typescript推荐使用
typescript是一种js的超集，可以完美解决js中没有静态变量的问题，并且支持所有的js语法，学习typescript简直就是大势所趋。
**ts个人学习初期基础笔记：**
1.在文件中创建.ts文件，写一些ts语法。
2.在命令行使用 tsc -init，创建出自动编译js文件的配置文件tsconfig.json，之后使用 tsc -w 生成自动编译并且监视原ts文件的js文件，启用并排视图模式，即可自动编译为js文件。
3.tsconfig.json中可以配置生成监听对应的js文件位置，launch.json中修改program的值为要打断点调试的编译后的js文件。
