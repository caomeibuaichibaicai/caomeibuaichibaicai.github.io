---
title: es6_解构赋值
date: 2018-01-02 11:29:12
tags:
- es6
- 解构赋值
- 扩展运算符
categories: es6
---
>1.解构赋值要求等号右边必须是一个对象，若为null或者undefined就会报错

>2.解构赋值必须是最后一个参数，不然报错，例如：

```javascript
let { ...x, y, z } = obj; // 句法错误
let { x, ...y, ...z } = obj; // 句法错误
let {x, y, …z} = obj; //正确
```
>3.解构赋值即扩展运算符的拷贝是浅拷贝，只能实现一维的深拷贝，多维是浅拷贝。拷贝对象时，相当于Object.assign({},obj1)方法

>4.扩展运算符的解构赋值，不能复制继承自原型对象的属性。

即只复制自身的属性，却不能复制它的原型对象上_proto_的属性。如Object.create()创建的空对象上被绑定的_proto原型属性，扩展运算符是无法复制到的，🌰如下:
```javascript
const o = Object.create({ x: 1, y: 2 });
o.z = 3;
let { x, ...newObj } = o;
let { y, z } = newObj;

x // 1
y // undefined
z // 3
```
>5.解构赋值的扩展运算符之后必须是一个变量名，而不能是一个表达式

所以要引用一个中间变量，放在三个点点之后，故:
```javascript
let { x, ...{ y, z } } = o;
// SyntaxError: ... must be followed by an identifier in declaration contexts。。。报错
```


>6.另外，扩展运算符可以扩展某个函数的参数，使函数能够接受多余的参数，并且保留原始函数的行为

>7.如果用户对象自定义的属性，放在扩展运算符后面，则扩展运算符内部的同名属性会被覆盖掉。

>8.对象的扩展运算符后面可以跟表达式

例如：
```javascript
const obj = {
  ...(x > 1 ? {a: 1} : {}),
  b: 2,
};
```
>9.扩展运算符的参数对象之中，如果有取值函数get，这个函数是会执行的。

**大致总结到这里啦~**
