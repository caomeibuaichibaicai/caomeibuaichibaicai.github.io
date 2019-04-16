---
title: Js浮点数
date: 2017-03-26 11:23:04
categories: Js基础
tags: 浮点数
---
## Js浮点数小计
1. 浮点数出现的**原因**
js在进行计算时，需要将数字先转化为二进制，然后进行加减乘除的计算，得到的结果仍然是二进制，最后再转化为十进制，从而得到结果，在这个过程中就产生了 ==浮点数==，在小数位产生了误差。
2. js的计算 **精度准确** 的最大值是2^53^-1
最小值为其对应的**负数**-2^53^-1
若大于这个值就会出现精度丢失的情况，在2^53^至2^54^的范围内，只能计算出==偶数==；在2^54^至2^55^的范围内，只能四个数选一个，即==四的倍数==，后面以此类推，精度越来越小
3. js能 **表示的** 最大整数是2^1024^-1
从2^1024^往后，后面的数字都只能被表示为Infinity
4. 解决js浮点数中的加减乘除运算**误差**问题
- ==单纯展示数据方法:==
```javascript
function strip(num, precision = 12) {
  return +parseFloat(num.toPrecision(precision));
}
```
先使用toPrecision对数字进行浮点数的处理，这个函数对超过指定精度的部分，进行一个凑整处理。(*精度部分从最左边第一个不为0的位数开始计算*)，通过经验计算，这个精度一般会选择12位。（当然也可以更高）
之后会返回一个String类型的数字字符串，使用parseFloat对字符串进行转化为数字。


- ==浮点进行运算方法：==
这个时候的思路是把小数转化为整数，然后在进行运算.
乘*：
```javascript
const Mul = (num1, num2) => {
  let m = 0
  const s1 = `${num1}`
  const s2 = `${num2}`
  try {
    // m += s1.split('.')[1].length
    m += get(s1.split('.'), '[1].length', 0)
  } catch (e) {
    console.error(e)
  }
  try {
    // m += s2.split('.')[1].length
    m += get(s2.split('.'), '[1].length', 0)
  } catch (e) {
    console.error(e)
  }
  return Number(s1.replace('.', '')) *
    Number(s2.replace('.', '')) /
    Math.pow(10, m)
}
```
除/:
```javascript
// 两个浮点数相除
const Div = (num1, num2) => {
  let t1
  let t2
  try {
    // t1 = `${num1}`.split('.')[1].length
    t1 = get(`${num1}`.split('.'), '[1].length', 0)
  } catch (e) {
    t1 = 0
  }
  try {
    // t2 = `${num2}`.toString().split('.')[1].length
    t2 = get(`${num2}`.toString().split('.'), '[1].length', 0)
  } catch (e) {
    t2 = 0
  }
  const r1 = Number(`${num1}`.replace('.', ''))
  const r2 = Number(`${num2}`.toString().replace('.', ''))
  return (r1 / r2) * Math.pow(10, t2 - t1)
}
```
加+：
```javascript
// 两个浮点数相加
const Add = (num1, num2) => {
  let r1
  let r2
  try {
    r1 = `${num1}`.split('.')[1].length
  } catch (e) {
    r1 = 0
  }
  try {
    r2 = `${num2}`.split('.')[1].length
  } catch (e) {
    r2 = 0
  }
  const m = Math.pow(10, Math.max(r1, r2))
  return Math.round(num1 * m + num2 * m) / m
}
```
减-：
```javascript
// 两个浮点数相减
const Sub = (num1, num2) => {
  let r1
  let r2
  try {
    r1 = `${num1}`.split('.')[1].length
  } catch (e) {
    r1 = 0
  }
  try {
    r2 = `${num2}`.split('.')[1].length
  } catch (e) {
    r2 = 0
  }
  const m = Math.pow(10, Math.max(r1, r2))
  const n = (r1 >= r2) ? r1 : r2
  return toFixed((Math.round(num1 * m - num2 * m) / m), n)
}
```

如果觉得很麻烦的话，当然也有现成的插件来用啊~~~
比如：[number-precision](https://github.com/nefe/number-precision)，这个库的优点在于体积小，只有不到1K的大小。
还有我们熟知的[math.js](https://mathjs.org/download.html),里面的方法较全，代码可维护性也较高。
*************************************

今天对浮点数的小计差不多就到这里啦💗
