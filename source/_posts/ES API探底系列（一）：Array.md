---
title: ES API探底系列（一）：Array
date: 2018-05-23 17:07:17
tags: [JavaScript,MDN,前端]
categories: 前端
---
这段时间工作严重不饱和，趁现在补补基础，写这个系列的目的只有一个就是复习。   
毕竟写博客的过程在于纪录和成长，结果只是次要的。
不包含已经废弃掉的方法。
<!-- more -->

JS 中的数组是类似于列表的一种高阶对象
## 属性
### Array.length
length 属性的值是一个 0 到 2^32-1 的整数。
`length`是 Array 实例的一个属性，它可以被显式设置，如果设置超过数组长度的值，多出来的部分会被设置为`undefined`，如果设置为小于数组长度的值则数组会被截断。
### Array.prototype
是`Array`构造函数的原型，通过给它设置方法或者属性可供所有数组使用。
```js
Array.isArray(Array.prototype); 
// true
```
## 方法
### Array.from()
`Array.from(arrayLike[, mapFn[, thisArg]])`从一个类似数组或可迭代对象中创建一个新的数组。
```js
console.log(Array.from('foo'));
// expected output: Array ["f", "o", "o"]

console.log(Array.from([1, 2, 3], x => x + x));
// expected output: Array [2, 4, 6]

//数组去重
function combine(){ 
    let arr = [].concat.apply([], arguments);  //没有去重复的新数组 
    return Array.from(new Set(arr));
} 
var m = [1, 2, 2], n = [2,3,3]; 
console.log(combine(m,n));                     // [1, 2, 3]
```
### Array.isArray()
`Array.isArray(obj)`确定传的值是不是一个 `Array`。
```js
// 下面的函数调用都返回 true
Array.isArray([]);
Array.isArray([1]);
Array.isArray(new Array());
// 鲜为人知的事实：其实 Array.prototype 也是一个数组。
Array.isArray(Array.prototype); 

// 下面的函数调用都返回 false
Array.isArray();
Array.isArray({});
Array.isArray(null);
Array.isArray(undefined);
Array.isArray(17);
Array.isArray('Array');
Array.isArray(true);
Array.isArray(false);
Array.isArray({ __proto__: Array.prototype });
```
尽量使用`isArray`而不是`instanceof`,前者可以检测 iframes。
### Array.of()
