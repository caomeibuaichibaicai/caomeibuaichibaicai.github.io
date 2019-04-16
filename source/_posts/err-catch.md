---
title: error处理语法
date: 2017-02-11 10:13:15
tags:
  - 错误处理语法
categories: Js基础
---
### 1.try-catch语法解释用法
try是定义用来测试代码块出现错误的语句，**必须和catch或者finally连用，也可以三个都一起用**。
catch只有当发生错误时才会执行，finally无论有无异常都会执行。
还有一个throw语句是用来创建自定义错误语句，抛出js语句停止运行时的错误，可通过此自定义错误信息，如在try语句里面去自定义throw抛出的语句，在catch里面可接受自定义的err错误对象。
### 2.用法举例
```javascript
try {
    adddlert("欢迎光临！");
}
catch(err) {
    document.getElementById("demo").innerHTML = err.message;
}
```
