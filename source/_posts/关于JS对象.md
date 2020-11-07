---
title: 关于JS对象
date: 2020-03-24 17:04:00
tags: [前端，JavaScript]
categories: 前端
---

# 关于 JS 对象

JS 中的对象是一组无序的键值对

对象属性一般有两类属性，数据属性和访问器属性

数据属性包括四个特征：

- value：属性的值
- writable：决定属性能否被修改
- enumerable：决定 for in 能否枚举该属性
- configurable：决定属性能否被删除或者修改其特征值

访问器属性：

- getter：undefined或者函数，取属性值时调用
- setter：undefined或者函数，设置属性值时调用
- enumerable：决定 for in 能否枚举该属性
- configurable：决定该属性能否被删除或者改变特征值

这两种对象属性的特征值，可以通过 Object.getOwnPropertyDescriptor() 获得，

```javascript
var o = { a: 1 }
Object.getOwnPropertyDescriptor(o, 'a') // {value: 1, writable: true, enumerable: true, configurable: true}
```

ES5 的 Object.defineProperty 方法可以定义这些特征值

```javascript
var o = { a: 1 }
Object.defineProperty(o, 'a', {
  value: 5,
  ···
})
o.a // 5
```

## 对象的原型

原型在 JS 中就是一个对象，我们创建的每一个对象都有私有字段 [[prototype]]，就是对象的原型

```javascript
var o = {}
o.__proto__
```

在读取一个对象的属性时，如果当前对象没有此属性，会继续访问对象的原型，直到原型为空或者找到为止。

