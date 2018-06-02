---
title: 快速上手--react
date: 2018-05-11 11:37:29
tags: [项目,前端,react]
categories: 前端
---

# 项目快速上手

 作为一个实习生或者新人或者 ，第一次用 **react** 进行项目开发时，会遇到不同程度的困难，我在本文就 `丰农` 项目的心得结合一些 react 编程思想做一个小小的总结，以便于  学习或者利于后来新人快速上手。

<!--more-->


主要从三个个方面展开：

1.   数据驱动流思想
2.  团队开发
3.  React + Redux

## 1.数据驱动流思想

>  如果之前有前端 **MV\*** 框架项目开发经验，可以略过此节

何为数据驱动流，个人觉得只有很好的理解了这个概念，才能才开发过程中得心应手。 假设数据（d 表示）与视图（V 表示）之间存在一种特殊关系，可以是这样：

$$V=f(d)$$

数据变化时时：

$$V + \Delta V =f(d+\Delta d)$$

相应的  视图也也发生了变化，$f$ 即为之间的映射关系。关于$\Delta d$,它是由某个操作引起的，可以把这个操作类比为 `reduce`, 每一次的状态变更综合起来就会渲染最后的视图。

所有我们的项目不管任何组件，我们需要维护的只是数据和如何操作数据。

## 2.React + Redux

### React

#### 组件

* 无状态组件（Stateless Functional Component）
  * 1.不提供 state，refs，生命周期  等属性方法
  * 2. 一般用作展示组件
* 有状态组件（Component）
  * 1.`render()`函数中  尽量  只保留 return 语句和 this.props。

#### 开发 tips

*  对于公共函数封装，可先查看@utils 文件件，如果有使用即可，如果没有可对其补充，如果需要新建，在此之前需要思考可行性，斟酌代码量和可读性的基础上进行封装 📦。
*  一般的项目目录（components,container,store 的文件夹名称一般是一一对应。）

  ```
    src
    |-assets/   // 静态资源包  
    |-components/   // 视图组件
    |-container/    // 容器组件
    |-layouts/  // 页面结构组件
    |-store/    // redux
    |-utils/    //  工具函数包
    |-app.js    // 路由
    |-index.js  // 项目入口
    |-index.scss    // 全局样式
    |-theme.js  // antd 主题文件
  ```

> [展示和容器组件](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)由 react 开发者 [dan](https://github.com/gaearon)提出

### Redux

[基本概念](https://cn.redux.js.org/)，快速入门可以 [参考这里](https://github.com/berwin/Blog/issues/4)  

 例如丰农项目中使用了`react-redux`,需要理解  以下几个概念

* Provider 组件保存 store 给子组件中的 connect 使用
* Connect 把 State 和 dispatch 转换成 props 传递给子组件。

#### tips

* 保持 fetch 参数的纯洁性，提升代码可读性
* fetch 之后的相同操作可以进行处理 例如：loading 操作....

## 3.团队开发

###  代码风格

关于代码风格，已经有了很完善的 lint 工具，例如：ESlint。

 我想补充的就是如果你使用的  编辑器  是 VScode，可以在拓展市场寻找适合自己的格式化工具，比如 Prettier， 它在格式化的时候数据源是 .eslintrc。

### 软技能

1.  沟通

