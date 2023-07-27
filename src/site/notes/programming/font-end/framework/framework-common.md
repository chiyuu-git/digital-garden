---
{"dg-publish":true,"permalink":"/programming/font-end/framework/framework-common/"}
---


# 前端路由

## 什么是前端路由

前端路由就是把不同路由对应不同的内容或页面的任务交给前端来做，之前是通过服务端根据 url 的不同返回不同的页面实现的。

## 什么时候使用前端路由

在单页面应用，大部分页面结构不变，只改变部分内容的使用

## 前端路由有什么优点和缺点

优点：用户体验好，不需要每次都从服务器全部获取，快速展现给用户

缺点：单页面无法记住之前滚动的位置，无法在前进，后退的时候记住滚动的位置

前端路由一共有两种实现方式，一种是通过 hash 的方式，一种是通过使用 pushState 的方式。

详细资料可以参考： [《什么是“前端路由”》](https://segmentfault.com/q/1010000005336260) [《浅谈前端路由》](https://github.com/kaola-fed/blog/issues/137) [《前端路由是什么东西？》](https://www.zhihu.com/question/53064386)

# NextTick

因为 js 是单线程的，在 setData 之后，到底什么时候派发 nextTickReach 是可以有选择的

1. data 真正更新之后马上 reach，此时框架控制的节点树都还没更新呢，别说是真实的 dom 树了
2. 等框架控制的节点树更新完之后派发，对于 san 就是 changes 传导到了每个节点的 _update 方法之后，对于 vue 和 react 就是 vDom 更新完毕之后

讨论的 issue：https://github.com/baidu/san/issues/141

vue 最新的源码：https://github.com/vuejs/vue/blob/dev/dist/vue.js

把 na 组件的插入操作放在 nextTick 中，可以延后执行 na 组件的插入操作，可以提升组件性能

> san 的视图更新是异步的。组件数据变更后，将在下一个时钟周期更新视图。如果你修改了某些数据，想要在 DOM 更新后做某些事情，则需要使用 `nextTick` 方法。
>
> san.dev.js 搜索 nextTick 即可

只能是 attached 之后了，感觉是类似 FMP 的一个逻辑，可以 san 组件实例已经走到 attached 了，再插入 NA 组件本身

用 cts 实例测试发现好几个 cover-view 都 attached 了 之后，此时很多 h5 的内容都已经上屏了，才进去第一个 nextTick

> 假如我有 1w 个 na 组件，也会在全部 attached 之后才到 nextTick 的轮次吗？感觉有可能哦
>
> 从上面的表现来看，我们这里的 nextTick 不就是宏任务的吗？不一定哦，因为 debugger 是可以挂起 js 线程的，alert 会占用 js 线程，并没有让给 gui，反而是 debugger 是让给了 gui 的
>
> 可以测试一下 nextTick 的微任务和宏任务了，无论是微任务还是宏任务，反正端能力都会异步的，除非是宏任务，可以做到保证在 h5 的组件渲染完成
>
> 我们现在 setImmediate 和 messageChannel 都是有的，所以是宏任务

san 的数据更新操作是异步的，会被打包在一个 nextTick 中。因此我们再使用 nextTick 可以获取到数据更新的 nextTick 之后的 data，微任务队列

还有一个点就是 dom 的更新和 UI 渲染是两个概念，因为 js 线程和渲染线程是互斥的，所以 UI 渲染之前，dom 就已经是更新完的了。

> 这就涉及到我们 dom api 获取的究竟是？肯定是从 dom 树入手的呀，render 树究竟是怎么样的，确实有部分 dom api 是从 render 树 入手的，但是有部分的 dom api 会强制渲染，这个时候会发生什么？

如果是 promise 这种微任务实现，当我们在 nextTick 中再次 setData，那么会在 UI 渲染前成功 setData

如果选择宏任务实现会在 UI 渲染之后，导致重新渲染，因此微任务应该是更好的选择，但是为什么 san 中优先选择了宏任务呢？vue 目前也是 promise 优先

> 我们 slave 触发的是哪个 nextTick 呢？我们组件代码运行在 slave 上，是 webview 环境，有 js 解释器 bom api dom api

我自己也是可以测试的，无论是 san 还是 vue，我需要有一个方法知道 dom 到底更新了没有，还需要明确一点 dom 更新和 UI 渲染的关系

网上的文章并没有错，nextTick 的初衷是为了获取更新后的 dom？ 那问题就只剩下上一个了，dom 的更新和 ui 的渲染，肯定是分开的吧，不可能说 UI 渲染完了 dom 才更新了，肯定是更新后的 dom 被用于 UI 渲染了

我只要整一个函数更新 dom，然后看看实际的 dom 结构更新和渲染的先后关系

> 采用 alert 语句进行提示，alert 语句会 block 住 js 线程，将执行权让给 gui 渲染线程，执行 alert 之后浏览器会把这个语句之前的所有对 dom 的操作都进行体现。
>
> 感觉打断点的话也会被 js 线程也会被吊起，ui 渲染会直接进行，因此没办法捕捉到 dom 更新了，但是 UI 没渲染的那个瞬间
>
> 体验 dom 更新了，但是 UI 没有渲染的过程：https://blog.csdn.net/qdmoment/article/details/83657410
