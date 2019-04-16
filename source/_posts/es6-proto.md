---
title: es6_proto新方法
date: 2017-12-28 15:44:52
tags:
  - es6_proto
  - 原型新方法
categories: es6
---

## 1.Object.setPrototypeOf()
作用与__proto__相同，用来设置一个对象的prototype对象，返回参数对象本身。用来设置对象的原型对象，使原型对象的属性可以从对象上读取出来，并返回第一个参数obj
> 用法：

Object.setPrototypeOf(obj,proto),    其中obj是对象，proto是原型对象，将proto对象设为obj对象的原型。
若第一个参数不是对象，那么会自动转化为对象，但是最后还是返回的第一个参数，不会改变数据类型。但在这个过程中obj不能为undefined或者null，因为它们不能转化为对象，所以此时会报错。
## 2.Object.getPrototypeOf()
用来读取一个对象的原型对象，例子如下：
```javascript
function Rectangle() {
  // ...
}
//创建一个构造函数
const rec = new Rectangle();

Object.getPrototypeOf(rec) === Rectangle.prototype===rec._proto_
```
同样会将obj转化为对象，机制同上。



***********************
最后，给一个完整克隆一个对象的方法，包括拷贝对象原型的属性，有以下 **三种方法：**
```javascript
// 写法一
const clone1 = {
  __proto__: Object.getPrototypeOf(obj),
  ...obj
};

// 写法二
const clone2 = Object.assign(
  Object.create(Object.getPrototypeOf(obj)),
  obj
);

// 写法三
const clone3 = Object.create(
  Object.getPrototypeOf(obj),
  Object.getOwnPropertyDescriptors(obj)
)
```
写法二和写法三的通用性更强，推荐！写法一可能__proto__属性在非浏览器环境下，不一定会被部署~~~~
