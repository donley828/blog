---
title: Koa@2 中间件加载机制解析
date: 2018-08-30 00:28:40
tags: [JavaScript, Nodejs, Koa]
categories: Node.js
---

Koa 是 tj 主导的一个相比于 Express 很小的 Nodejs Web 框架。它支持大部分 es7 的语法比如 async，await 函数。  
提供了主要为`use()`的函数库，通过自己编写中间件实现一个健壮的 web 应用。

<!-- more -->

## 与 Exprees 对比

本文主要探究它的中间件加载机制，所以主要讨论中间件的内容。

### Express

就拿 Express 来说，它是类似这样的：

> Request -> middleware1 ->...-> middleware2 -> Response

而且在中间件执行过程中，如果没有终结或者说返回 `Response`时，就必须调用`next()`方法直到请求处理结束，否则这个请求会被挂起。

### Koa

关于 Koa 中间件的执行过程，就譬如下面这一段代码：

```js
···
app.use(async (ctx, next) => {
    console.log(1);
    await next();
    console.log(4);
});
app.use(async (ctx, next) => {
    console.log(2);
    await next();
    console.log(3);
});
···
```

假如这是应用注册的两个中间件，请求的时候这段代码的输出结果将会是  
1，2，3，4
为什么呢？
因为当 Koa 的中间件在遇到`await next()`的时候就会跳过其后的代码去执行下一个中间件的代码，依次一直到有了请求返回也就是中间件执行到最后一个的时候就会按照倒序返回执行`await next()`之后的代码。这样说可能不好理解。网上有一张洋葱圈模型可以很好的解释这个流程。
![](http://ojxko64c5.bkt.clouddn.com/%E6%B4%8B%E8%91%B1%E5%9C%88%E6%A8%A1%E5%9E%8B-koajs.png)

## 源码

有了这个机制，我们可以很方便的封装一些 cookie 处理、服务响应时间等有用的中间件。
但是带来便利的同时为什么可以完美的按照这个顺序执行呢？带着疑问我打开了 koajs 的源码。  
（不得不说 tj 大神写的代码真是既优雅又实用）

### 中间件流程

 大概说一下，koa 内部声明了一个叫`middleware`的空数组，每次执行`app.use`的时候，都会有一个`push`方法把中间件的函数存进去。
接着我们可以在`/lib/application.js`的 127 行左右找到关于`middleware`的处理，原来是传入了一个叫`compose`的方法内。

### koa-compose

原来中间件的处理单独放在了一个 repo 里，找到之后只有一个`index.js`文件。
打开如下：

```js
function compose(middleware) {
  if (!Array.isArray(middleware))
    throw new TypeError("Middleware stack must be an array!");
  for (const fn of middleware) {
    if (typeof fn !== "function")
      throw new TypeError("Middleware must be composed of functions!");
  }

  /**
   * @param {Object} context
   * @return {Promise}
   * @api public
   */

  // 返回一个dispatch函数,dispatch会返回内部 Promosie.resolve，可以通过.then方法接收
  return function(context, next) {
    // last called middleware #
    let index = -1;
    return dispatch(0); // 0为第一个中间件
    function dispatch(i) {
      if (i <= index)
        return Promise.reject(new Error("next() called multiple times"));
      index = i;
      let fn = middleware[i];
      if (i === middleware.length) fn = next;
      if (!fn) return Promise.resolve();
      try {
        // 递归执行
        return Promise.resolve(fn(context, dispatch.bind(null, i + 1)));
      } catch (err) {
        return Promise.reject(err);
      }
    }
  };
}
```

### 简化

可能大部人看到这里还是不明白为什么会按照“洋葱”的顺序执行。
我们可以把这幅代码简化一下

```js
function compose(middleware) {
  return function(context, next) {
    let index = -1;
    return dispatch(0);

    function dispatch(i) {
      index = i;
      let fn = middleware[i];
      if (i === middleware.length) {
        fn = next;
      }
      if (!fn) return;
      return fn(context, function next() {
        return dispatch(i + 1);
      });
    }
  };
}
var ms = [
  function foo(ctx, next) {
    console.log("1");
    next();
    console.log("3");
  },
  function bar(ctx, next) {
    console.log("2");
    next();
    console.log("3");
  }
];
compose(ms)();
```

我们会惊奇的发现这段代码的执行结果竟然和 koajs 的中间件顺序一模一样。  
观察发现我们是把 `Promise` 对象相关的内容去掉，换为原始的回调函数实现。

So,这个时候我们回到了最最最初的起点就是回调函数与异步。对于中间件数组来说，从`index`=0 开始执行，当在某个中间件中执行`next()`的时候，其实就行在执行下一个中间件，依次一直执行到这个次序结束。那么在这个过程中`next()`之后的代码在 js 的事件机制来说就会被放入一个事件“队列”里。  
然而，这个队列本质上是一个栈，遵循先进后出和后进先出的原则，所以到最后一个的时候又会按照逆序执行一遍。
这时候再回头去看之前的源码就会发现简单多了

## 结语

总之还是感觉是对新特性的掌握度不够，同样的道理加一些干扰就会影响理解上的进度。
欢迎吐槽。
