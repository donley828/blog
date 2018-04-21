---
title: Javascript模块化
date: 2017-10-16 17:17:34
tags: [Javascript,模块化,前端]
categories: 前端
---

# Javascript 模块化

当今的网站已经逐渐变成的 Web 应用，网页引入的 Javascript 文件也越来越多，越来越复杂。网页就像桌面程序一样，需要一系列软件工程的方法，来管理网页的业务逻辑。所以 Javascript 模块化就成为了一种迫切的需求。但是 Javascript 又没有像 JAVA 语言中的类特性，所以如何实现模块化就成了一个难题。令人欣慰的是，经过 Javascript 的一系列努力，Javascript 模块化已经趋于成熟，本文将会总结模块化的几种方法以及模块化的规范。

<!-- more -->

## 如何实现模块化

### 原始写法

模块就是实现某一功能的一个程序块。比如下面这段代码就组成了一个模块

```javascript
function a() {
  //dosomething
}
function b() {
  //dosomething
}
```

上面程序块中的函数 a() 和 b()，组成了一个模块。但是这种做法暴漏出了一个明显的缺点，“污染”了全局变量，在多人开发过程中，无法保证其他人与你定义的变量名不会发生冲突，而且模块成员之间看不出直接关系。

### 对象写法

为了解决这个问题，我们就可以把模块写成对象的形式，模块成员作为对象的属性存在。例如

```javascript
var m1 = {
  _count: 0,
  a: function() {
    //dosomething
  },
  b: function() {
    //dosomething
  }
};
```

这种写法又存在一种问题就是，模块的成员被“暴露”在外部可以被直接修改，不能以“私有变量”的形式存在。

### 立即调用函数

严格来讲，Javascript 中没有私有变量的概念，所有的对象属性都是公开的。不过，任何在函数中定义的变量，都可以认为是私有变量，因为你不能在函数的外部访问它们。

```javascript
var m1 = (function() {
  var _count = 0;
  var a = function() {
    //dosomething
  };
  var b = function() {
    //dosomething
  };
  return {
    a: a,
    b: b
  };
})();
```

这样书写之后，代码外部无法读取内部的\_count 变量，只能使用模块提供的 a 和 b 特权函数。这就是 Javascript 模块的基本写法。

### 放大模式

```javascript
var m1 = (function(mod) {
  mod.c = function() {
    //dosomething
  };
})(m1);
```

上面的代码为 m1 模块添加了一个新方法 c()，然后返回新的 m1 模块。

### 宽放大模式

浏览器环境中，模块的每个部分可能是从网上获取的，有时无法知道哪个部分先加载。如果采用上一节的写法，第一个执行的部分有可能加载一个不存在的空对象，这时就要采用“宽放大模式”。

```javascript
var m1 = (function(mod) {
  //dosomething
  return mod;
})(window.m1 || {});
```

宽放大模式就是允许立即调用函数可以是空对象。

### 全局变量注入

```javascript
var m1 = (function(mod) {
  //dosomething
})(window.model);
```

这个时候的 mod 其实就是全局的 model

## 模块化规范

### CommonJs

官方是这样解释 CommonJS

> The CommonJS API will fill that gap by defining APIs that handle many common application needs, ultimately providing a standard library as rich as those of Python, Ruby and Java.

它提供了一个类似于 Ruby 和 Java 的标准库，有了这个，我们就可以使用 CommonJS APIs 构建能够运行在不同的主机环境中的应用程序。比如，NodeJS 的模块系统就是参照 CommonJS 规范的实现，Webpack 也是以 CommonJS 新式来书写的。

```javascript
var math = require('math');
math.add(2, 3); //5
```

这行代码就是在程序中引入了一个数学模块

### AMD

在 CommonJS 出现以后，服务端的模块概念已经形成，自然而然客户端也需要模块化。如果两者能够兼容，一个模块能够同时在服务端和客户端运行。

```javascript
var math = require('math');
math.add(2, 3); //5
```

还是这行代码，如果在浏览器中运行，它会出现一个及其严重的问题，就是 math.add(2, 3) 这行代码必须等待 math 模块加载结束之后才能运行。如果加载时间过长，应用就会出现明显的卡顿。<br>
因此浏览器端的模块加载就应该采用异步(Async)的形式，这就是 AMD 规范诞生的原因。require.js 库实现了 AMD 规范。之后会进一步研究这个规范。

### CMD

玉伯写的 sea.js 就是遵循他提出的 CMD 规范，与 AMD 类似。

> Reference: <a href="http://www.ruanyifeng.com/blog/2012/10/javascript_module.html" target="_blank">Ruanyf</a>
