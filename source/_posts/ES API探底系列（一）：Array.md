---
title: ES API探底系列（一）：Array
date: 2018-05-23 17:07:17
tags: [JavaScript,MDN,前端]
categories: 前端
---
这段时间工作不太饱和，趁现在补补基础，写这个系列的目的只有一个就是复习。   
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
`Array.of(num)` 返回一个长度为 num 的数组，是空位不是 undefined。
### Array.prototype.contact()
`contact(arr1,arr2..)`用于合并一个或多个数组。会返回一个新的数组。
```js
var array1 = ['a', 'b', 'c'];
var array2 = ['d', 'e', 'f'];

console.log(array1.concat(array2));
// expected output: Array ["a", "b", "c", "d", "e", "f"]
```
### Array.prototype.copyWithin()
`copyWithin(target, start, end)`浅复制数组的一部分到同一数组的另一个位置，并且返回，原数组会被修改，被移动的部分大小不会被改变。这个方法的三个参数都是整数。   
如果`target`大于数组的`length`，不会被拷贝。如果`target`在`start`之后，将会被修改。   
如果`start`或者`end`为负数，会从末尾开始计算。
```js
var array1 = [1, 2, 3, 4, 5];

// place at position 0 the element between position 3 and 4
console.log(array1.copyWithin(0, 3, 4));
// expected output: Array [4, 2, 3, 4, 5]
[1, 2, 3, 4, 5].copyWithin(-2, -3, -1);
// [1, 2, 3, 3, 4]
```
### Array.prototype.entries()
`entries()`返回一个新的**Array Iterator**对象，该对象包含调用这个方法的数组中每个索引的键值。
```js
var array1 = ['a', 'b', 'c'];

var iterator1 = array1.entries();
console.log(iterator1.next().value);
// expected output: Array [0, "a"]

console.log(iterator1.next().value);
// expected output: Array [1, "b"]
```
### Array.prototype.every()
`every(callback, thisArg)`方法测试数组的所有元素是否都通过了指定函数的测试。   
如果数组的所有元素都通过了`callback`，这个方法则返回`true`，反之则返回`false`。   
`callback`参数为测试函数，`thisArg`参数可以指定执行`callback`时`this`的值。   
```js
function isBigEnough(element, index, array) {
  return (element >= 10);
}
var passed = [12, 5, 8, 130, 44].every(isBigEnough);
// passed is false
passed = [12, 54, 18, 130, 44].every(isBigEnough);
// passed is true
```
### Array.prototype.fill()
`fill(value, start, end)`用一个固定值填充数组的某一部分，不包括`end`索引。   
参数`start`和`end`默认值分别为0和数组的`length`。
```js
[1, 2, 3].fill(4);               // [4, 4, 4]
[1, 2, 3].fill(4, 1);            // [1, 4, 4]
[1, 2, 3].fill(4, -3, -2);       // [4, 2, 3]
```
### Array.prototype.filter()
`filter(callback(element, index, array))[thisArg]`方法创建一个新数组, 其包含通过所提供函数实现的测试的所有元素。   
`callback`参数是用来测试的函数。`element`当前正在处理的元素。`index`正在处理的元素的索引。`array`调用了filter的数组。`thisArg`执行`callback`时的用于this的值。
### Array.prototype.find()
### Array.prototype.findIndex()
### 🔍 Array.prototype.flat()
### 🔍 Array.prototype.flatMap()
### Array.prototype.forEach()
### Array.prototype.includes()
### Array.prototype.indexOf()
### Array.prototype.join()
### Array.prototype.keys()