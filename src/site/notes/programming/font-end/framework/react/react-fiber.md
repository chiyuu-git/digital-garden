---
{"dg-publish":true,"permalink":"/programming/font-end/framework/react/react-fiber/"}
---


[手写简易版 React 来彻底搞懂 fiber 架构 - 知乎](https://zhuanlan.zhihu.com/p/466164101)

# 概述

React 是通过 jsx 来描述界面结构的，会把 jsx 编译成 render function，然后执行 render function 产生 vdom：

![img](/img/user/programming/font-end/framework/react/react-fiber/imageDownloadAddress.png)

在 v16 之前的 React 里，是直接递归遍历 vdom，通过 dom api 增删改 dom 的方式来渲染的。但当 vdom 过大，频繁调用 dom api 会比较耗时，而且递归又不能打断，所以有性能问题。

![img](/img/user/programming/font-end/framework/react/react-fiber/imageDownloadAddress-20230817120405365.png)

后来就引入了 fiber 架构，先把 vdom 树转成 fiber 链表，然后再渲染 fiber。

![img](/img/user/programming/font-end/framework/react/react-fiber/imageDownloadAddress-20230817120405223.png)

本来 vdom 里通过 children 关联父子节点，而 fiber 里面则是通过 child 关联第一个子节点，然后通过 sibling 串联起下一个，所有的节点可以 return 到父节点。

vdom 转 fiber 的过程叫做 reconcile，是可打断的，React 加入了 schedule 的机制在空闲时调度 reconcile，reconcile 的过程中会做 diff，打上增删改的标记（effectTag），并把对应的 dom 创建好。然后就可以一次性把 fiber 渲染到 dom，也就是 commit。

这个 schedule、reconcile、commit 的流程就是 fiber 架构。注意：对应的这个数据结构也叫 fiber。

既能够映射真实的 dom 也能作为分割的单元

# Schedule

## requestIdleCallback

那么 fiber 是如何被时间片异步执行的呢，提供一种思路，示例如下:

```js
let firstFiber
let nextFiber = firstFiber
let shouldYield = false

/**
 * vDOM 转 fiber
 */
function performUnitOfWork(nextFiber) {
    //...
    return nextFiber.next
}

function workLoop(deadline) {
    while (nextFiber && !shouldYield) {
        nextFiber = performUnitOfWork(nextFiber)
        shouldYield = deadline.timeReaming < 1
    }
    // 全部处理完了就 commit
    if (!nextFiber) {
        commitRoot();
    }

    requestIdleCallback(workLoop)
}

requestIdleCallback(workLoop)
```

我们知道浏览器有一个 api 叫做**requestIdleCallback**，它可以在浏览器空闲的时候执行一些任务，我们用这个 api 执行 react 的更新，让高优先级的任务优先响应。对于 [requsetIdleCallback函数](https://www.zhihu.com/search?q=requsetIdleCallback%E5%87%BD%E6%95%B0&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A2325987853%7D)，下面是其原理。

```js
const temp = window.requestIdleCallback(callback[, options]);
```

对于普通的用户交互，上一帧的渲染到下一帧的渲染时间是属于系统空闲时间，Input 输入，最快的单字符输入时间平均是 33ms(通过持续按同一个键来触发)，相当于，上一帧到下一帧中间会存在大于 16.4ms 的空闲时间，就是说任何离散型交互，最小的系统空闲时间也有 16.4ms，也就是说，离散型交互的最短帧长一般是 33ms。

requestIdleCallback 回调调用时机是在回调注册完成的上一帧渲染到下一帧渲染之间的空闲时间执行

callback 是要执行的 [回调函数](https://www.zhihu.com/search?q=%E5%9B%9E%E8%B0%83%E5%87%BD%E6%95%B0&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A2325987853%7D)，会传入 deadline 对象作为参数，deadline 包含：

1. timeRemaining：剩余时间，单位 ms，指的是该帧剩余时间。
2. didTimeout：布尔型，true 表示该帧里面没有执行回调，超时了。

options 里面有个重要参数 timeout，如果给定 timeout，那到了时间，不管有没有剩余时间，都会立刻执行回调 callback。

但事实是**requestIdleCallback 存在着浏览器的兼容性和触发不稳定的问题**，所以我们需要用 js 实现一套时间片运行的机制，在 react 中这部分叫做 [scheduler](https://www.zhihu.com/search?q=scheduler&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A2325987853%7D)。同时 React 团队也没有看到任何浏览器厂商在正向的推动 requestIdleCallback 的覆盖进程，所以 React 只能采用了偏 hack 的 [polyfill方案](https://www.zhihu.com/search?q=polyfill%E6%96%B9%E6%A1%88&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A2325987853%7D)。

## requestIdleCallback Polyfill 方案 (Scheduler)

上面说到 requestIdleCallback 存在的问题，在 react 中实现的时间片运行机制叫做 scheduler，了解时间片的前提是了解通用场景下页面渲染的整个流程被称为一帧，浏览器渲染的一次完整流程大致为

执行 JS--->计算 Style--->构建布局模型 (Layout)--->绘制图层样式 (Paint)--->组合计算渲染呈现结果 (Composite)

**帧的特性：**

- 帧的渲染过程是在 JS 执行流程之后或者说一个事件循环之后
- 帧的渲染过程是在一个独立的 [UI线程](https://www.zhihu.com/search?q=UI%E7%BA%BF%E7%A8%8B&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22article%22%2C%22sourceId%22%3A%2248254036%22%7D) 中处理的，还有 GPU 线程，用于绘制 3D 视图
- 帧的渲染与帧的更新呈现是异步的过程，因为屏幕刷新频率是一个固定的刷新频率，通常是 60 次/秒，就是说，渲染一帧的时间要尽可能的低于 16.6 毫秒，否则在一些高频次交互动作中是会出现丢帧卡顿的情况，这就是因为渲染帧和刷新频率不同步造成的
- 用户通常的交互动作，不要求一帧的渲染时间低于 16.6 毫秒，但也是需要遵循**谷歌的 RAIL 模型**的

那么 Polyfill 方案是如何在固定帧数内控制任务执行的呢，究其**根本是借助 requestAnimationFrame 让一批扁平的任务恰好控制在一块一块的 33ms 这样的时间片内执行**

# Lane

以上是我们的异步调度策略，但是仅有异步调度，我们怎么确定应该调度什么任务呢，哪些任务应该被先调度，哪些应该被后调度，这就引出了类似于微任务宏任务的**Lane**

有了异步调度，我们还需要细粒度的管理各个任务的优先级，让高优先级的任务优先执行，各个 Fiber 工作单元还能比较优先级，相同优先级的任务可以一起更新

关于 [lane](https://www.zhihu.com/search?q=lane&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A2325987853%7D) 的设计可以看下这篇：

[https://github.com/facebook/react/pull/18796​github.com/facebook/react/pull/18796](https://link.zhihu.com/?target=https%3A//github.com/facebook/react/pull/18796)

# 应用场景

有了上面所介绍的这样一套异步可中断分配机制，我们就可以实现 batchUpdates 批量更新等一系列操作

![img](react-fiber/v2-3b86397b70d7ba02d24be9aed9bf4f24_1440w.webp)

更新 fiber 前

![img](react-fiber/v2-7990b44228167f74529526a0bec06c43_1440w.webp)

更新 fiber 后

以上除了 cpu 的瓶颈问题，还有一类问题是和副作用相关的问题，比如获取数据、文件操作等。不同设备性能和网络状况都不一样，react 怎样去处理这些副作用，让我们在编码时最佳实践，运行应用时表现一致呢，这就需要 react 有分离副作用的能力。

# FAQ

#faq/framework

## React 到底从哪里 Fiber 开始 "diff"

[react 到底从哪里 fiber 开始 "diff" - 掘金](https://juejin.cn/post/7107815018741268510)

## 如何手动创建立即执行（高优先级）的任务？

scheduler 是 react 内部的调度过程, 不知道如何创建, 难道要引用 react 暴露出来的 scheduleCallback 吗?
