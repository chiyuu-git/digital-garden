---
{"dg-publish":true,"permalink":"/programming/font-end/framework/vue/mini-vue3/"}
---


响应式的核心就是一套依赖搜集和发布订阅的机制而已

TODO: 给加上 ts 类型, 方便注释重用, 以及耦合逻辑的代码解释

vue 最有价值的就是响应式系统, 然后核心逻辑串联一下就好了

模板做的更好的是 svelte

dom 响应式 api 做的更好的是 solid

react 就是 hooks 的理念

https://gitee.com/zmym/mini-vue-ts/tree/master/packages/reactivity#https://gitee.com/link?target=https%3A%2F%2Flink.juejin.cn%2F%3Ftarget%3Dhttps%253A%252F%252Fv3.cn.vuejs.org%252Fapi%252Fbasic-reactivity.html%2523reactive

[index.js](https://github.com/liuweiGL/mini-vue/blob/main/index.js)

[GitHub - cuixiaorui/mini-vue: 实现最简 vue3 模型( Help you learn more efficiently vue3 source code )](https://github.com/cuixiaorui/mini-vue)


# 总结

vue3 源码好像也没有多么优美, 好多 api , 都是在擦屁股?

effet 和 reactive 就构成了响应式的核心了, 依赖搜集 + 响应式触发副作用

ref 和 computed 都是基于上面的两个实现的而已

# Reactive

## createReactiveObject

通过 proxy 实现 get 和 set

### Getter

createReactiveObject 只 proxy 了一层, 嵌套的对象的 proxy 是在对象的 getter 里再处理的

当 getter 获取到的是一个对象时, 会继续调用 createReactiveObject

+ 如果已经创建好了就直接返回
+ 如果没有创建好就再次创建

如果 getter 拿到的是一个对象, 那么这个对象也会是响应式的

## Readonly

只设置 get, set 进行 warning

> 暂时还不太清楚有啥必须的场景, 重点还是 reactive()

## isProxy() isReadonly() isReactive() toRow()

这几个方法都是通过 reactive 的 getter 实现的

在 getter 中给固定的属性做特殊处理, 从而实现了给所有 proxy 对象都统一添加属性的作用

![es-next-2](programming/font-end/primitive/es/es-next-2.md#通过%20Handler%20给%20Proxy%20对象添加属性)

# Effect

effect 会马上执行一次开发者传入的 fn, 完成依赖搜集

## Track

### targetMap 和 targetEffectMap

所有的要监听的对象都放在 targetMap 中, 通过 reactive getter track 传入的 target 来确认. 每个 target 都会通过 targetEffectMap 存储所有的副作用.

这里的 target 是单个对象, 如果是嵌套的对象的话, 子子孙孙都会一起出现在 targetMap 里

### keyEffectSet

一个 target 会有多个 key

单个 key 的副作用通过 target.key 标识

存放在 set 中, 避免重复搜集

### activeEffect

一个全局对象, 引用着当前正在执行的 ReactiveEffect 对象.

因为 track 依赖搜集行为是在 reactive 对象的 getter 当中触发的. 虽然可以通过调用栈向上获取到正在执行的 effect, 但是一个全局变量也是很简单直接的做法.

## Trigger

通过 target 和 key 取出 target key 对应的所有的副作用, 遍历执行

## Scheduler

Scheduler 没有什么特别的, 就是用户通过 options 传入的一个函数, 挂在 `ReactiveEffect` 的实例上

如果 effect 存在 `options.scheduler` 就不会触发 `ReactiveEffect.run()`, 而是触发用户定义的 scheduler

用户可以自己在 scheduler 内, 控制在合适的时机真实的执行 `ReactiveEffect.run()`

## stop()

停止 `ReactiveEffect` 实例的所有功能

清理会触发该 effect 的所有观察者

还提供了一个 onStop 钩子, 就这样

## 源码中 Dep 的概念

源码中的 dep 其实都是 effect. 因为 reactive 实例是负责触发副作用的, 在源码中把这些成为依赖. 但是实际上并不是 reactive 实例依赖他们, 而是他们依赖 reactive 实例.

这个过程虽然叫做: 依赖搜集, 但其实搜集的都是副作用函数

是副作用函数依赖 reactive 对象

在日常的前端开发中, npm 已经固化了依赖的概念, 是我依赖的东西, 而不是依赖我的东西

总之我觉得把 dep 都直接理解成 effect 就好了

# Ref

> vue2 的时候, 原始值是怎么实现响应式的呢? 直接监听的 vm 的 data, 能够处理原始类型的数据

最主要的作用就是处理原始值的响应性, 如果传入的是 object, 就会调用 `createReactiveObject`

通过 value 属性包装了一层 getter 和 setter, 从而在原始值上实现响应式.

还是那一套: getter 搜集依赖, setter 触发副作用

## 为啥 ref 有 object value diff 而 reactive 没有?

不清楚, 反正不太影响

## proxyRefs

帮助 ref 解构用的, 没必要细看