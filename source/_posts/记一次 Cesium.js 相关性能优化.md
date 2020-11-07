---
title: 记一次 Cesium.js 相关性能优化
date: 2020-11-07 11:27:52
tags: [Gis,前端,可视化,性能优化]
categories: 前端
---

在公司项目的综合概览-电子地图模块，为了达到离线部署的目的，使用了 Cesium.js 做了地图渲染引擎。但是在开发结束之后发现地图渲染过程中有明显卡顿，尤其是四周的几个使用了 <transition /> 的数据图框实际帧数不到10FPS，简直是肉眼可见的卡。
### 性能分析
首先使用 Chrome 的 devtools 进行了一下性能分析。如下图：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fed5ae17ca434924846d4df84dc65eef~tplv-k3u1fbpfcp-watermark.image)

这是这个地图模块从点击菜单路由开始到全部资源和动画加载执行完成的时间，抛开时间不说，几个动画执行时的 fps 只有不到10，简直就是几张ppt。而且大部分时间都消耗在了 JS 运行上。于是进一步查看 Call Tree，果不其然，那几个红标的长耗时基本上都是 Cesium.js 的渲染流程。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/98d2d441af2447e688fe23c28e08d401~tplv-k3u1fbpfcp-watermark.image)

### 数据劫持
因为 Cesium.js 是第三方库，所以直接分析代码的执行流程也是不可行的。这时候只能去 Google 碰碰运气，发现 Vue-数据劫持 这个名词出现频率比较高。其实数据劫持就是通过 Object.defineProperty 去劫持的对象的 setter 和 getter操作，比如在数据更新的时候同时更新 DOM。就是双向绑定的原理。

这个时候我再回去看了一眼代码。

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0086ed223321460ab1dabb930dc7a063~tplv-k3u1fbpfcp-watermark.image)

果然把 viewer (Cesium.js 初始化的地图对象) 保存在了 Vue 实例上，而且在各种事件操作中大量调用。

后来只能把这个对象保存在 window 上，即使不是一个很优雅的办法。

### 闹鬼了
这一套操作结束之后，发现并没有什么卵用，虽然进入页面时间有所改善，但是那几个 <transition /> 还是肉眼可见的卡，尤其是手动显示隐藏的操作基本上没有什么变化。

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4ec07128b8084b48bf8f42f873c09ba9~tplv-k3u1fbpfcp-watermark.image)

我只好针手动显示隐藏这个过程再进行一次分析，然后发现整个渲染时长基本没什么变化，还是很久，最长的依然是 JS 运行。再次查看 Call Tree 大部分时间都耗费在地图的 resize() 方法上。查看代码发现我只是控制了几个 div 的显示隐藏，为什么会出发地图的重新渲染呢。然后只能点点点，最后我的注意力落在了一个滚动条上面。

滚动条的出现改变了页面主体的宽度，容器宽度的变化导致 Cesium 出发 resize() 方法，长耗时的 JS 阻塞了动画的执行造成了卡顿。

机智的我赶紧给 div#app 添加了一行 overflow-x: hidden; 再刷新一遍，完美解决。

### 总结
整个分析过程也没持续多长时间，主要原因还是因为开发过程还是太相信 CPU 的执行效率和第三方库对性能处理。真的遇到性能问题的时候，大部分原因还是在业务代码的不合理上。而且作为前端，大部分代码还是运行在客户的机器上，还是要尽量的“向下兼容”。
