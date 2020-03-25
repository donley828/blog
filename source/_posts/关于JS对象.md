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