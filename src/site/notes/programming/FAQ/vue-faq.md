---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-08-20-Sun, 5:07:45 pm","date-modified":"2023-08-27-Sun, 12:43:50 pm","permalink":"/programming/FAQ/vue-faq/","dgPassFrontmatter":true}
---


[13.彻底搞懂Vue中keep-alive的魔法-上 · 深入剖析Vue源码](https://ocean1509.github.io/In-depth-analysis-of-Vue/src/%E5%BD%BB%E5%BA%95%E6%90%9E%E6%87%82Vue%E4%B8%ADkeep-alive%E7%9A%84%E9%AD%94%E6%B3%95-%E4%B8%8A.html)

# V-if 和 V-show 的区别

V-if 和 V-show 的区别;;[](https://digital-garden.chiyuu.top/programming/faq/vue-faq/#v-if-v-show)<!--SR:!2023-09-06,8,250-->

[v-if和v-show的区别 - 掘金](https://juejin.cn/post/6844903767553359885)

v-show 不管条件是真还是假，第一次渲染的时候都会编译出来，也就是标签都会添加到 DOM 中。之后切换的时候，通过 display: none; 样式来显示隐藏元素。可以说只是改变 css 的样式，几乎不会影响什么性能

在首次渲染的时候，如果条件为假，什么也不操作，页面当作没有这些元素。当条件为真的时候，开始局部编译，动态的向 DOM 元素里面添加元素。当条件从真变为假的时候，开始局部编译，卸载这些元素，也就是删除

使用场景:

1. 因为 v-show 无论如何都会渲染，如果在一些场景下很难出现，那么使用 v-if
2. 如果是一些固定的，条件内容都不怎么会改变的，频繁切换的，使用 v-show 会比较省性能
3. 如果是子组件，每次切换子组件不执行生命周期，使用 v-show，如果子组件需要重新执行生命周期，那么使用 v-if 才能触发。

性能方面:

1. 4 个 v-if, 只有其中一个会执行渲染, 4 个 v-show 全部都会执行渲染, 只有其中一个根据条件显示
2. 所以性能还是看具体的场景.

# Keep-alive 适用场景是什么，实现原理是什么

Keep-alive 适用场景是什么，实现原理是什么;;[](https://digital-garden.chiyuu.top/programming/faq/vue-faq/#keep-alive)<!--SR:!2023-09-15,13,250-->

## 适用场景

keep-alive 是 Vue 提供的一个内置组件，用于在组件之间缓存不活动的组件实例，从而提高组件的渲染性能。keep-alive 的使用场景主要是那些需要频繁切换的组件，比如 tab 切换、列表滚动等。

当一个组件被包裹在 keep-alive 组件中时，这个组件实例将会被缓存起来。当组件被切换出去时，它的状态会被保存下来，而不是被销毁。当组件被再次渲染时，它的状态将被恢复，从而避免了重新渲染和重新创建组件实例的开销。

> keep-alive 更像是带缓存的 v-if, 而不是 v-show

```vue
<template>
  <div>
    <keep-alive>
      <router-view v-if="$route.meta.keepAlive"></router-view>
    </keep-alive>
    <router-view v-if="!$route.meta.keepAlive"></router-view>
  </div>
</template>
```

## 实现原理

1. 抽象组件：keep-alive 组件是一个抽象组件，没有模板，只包含逻辑。
2. LRU 缓存算法：几乎只要有缓存的地方，就有 LRU 缓存算法的影子。keep-alive 的实现是通过在组件内部维护一个缓存对象来实现的。这个缓存对象是一个类似于 LRU 缓存的数据结构，它会自动缓存最近使用的**组件实例**，并根据缓存大小限制自动释放最不常用的组件实例。当 keep-alive 组件内部渲染时，它会检查缓存对象中是否已经存在对应的组件实例，如果存在则直接取出并渲染，否则就创建一个新的组件实例并添加到缓存中。

# Computed 和 Watch 使用场景有什么不同，实现原理呢

## Computed

1. computed 是值，依赖于其它的状态。比如购物车的总价格，可以根据其它几个价格算得。
2. computed 有缓存特性，只要依赖的状态没有改变，computed 的值就会被缓存起来。当依赖发生变化时，才会重新计算。

## Watch

1. watch 用于监听状态的变化，比方说监听路由，一旦监听的状态发生变化，就执行某个函数。
2. watch 有两个参数也是面试常考点，
    - immediate：当我们希望在组件初始化时执行一次 watch 函数，就可以开启 immediate 选项
    - deep：深度监听，开启此选项当监听的对象的某个属性值发生变化，也会触发 watch 监听函数

## 实现原理

1. computed 和 watch 都是基于 Vue 响应式原理，首先通过 initWatcher 和 initComputed 来解析 watch 和 computed 选项，然后遍历，给每个 watch 和 computed 添加 Watcher 对象。
2. 不同的是给 computed 添加的 Watcher 对象是 lazy Watcher，默认不执行，取值的时候才执行。
3. computed 的缓存特性是通过 Watcher 对象的 dirty 属性来实现的。

# Vue 模板编译原理

## 模板编译流程

模板编译的目标是要把 template 模板转换成渲染函数，主要分成 3 个步骤，parse -> optimize -> generate ，记住这三个单词。

1. parse(解析模板)：首先会用正则等方式将模板解析为 AST（抽象语法树），这个过程包括词法分析和语法分析两个过程。在这个过程中，模板会被分解成一些列的节点，包括普通元素节点、文本节点、注释节点等，同时也会解析出这些节点的标签名、属性、指令等信息。
2. optimize(静态分析)：静态分析是指分析模板中的所有节点，找出其中的静态节点和静态属性，并将其标记出来。所谓静态节点是指节点的内容不会发生变化的节点，例如纯文本节点、含有静态属性的节点等，而静态属性则是指节点上的属性值不会改变的属性，例如 class 和 style 属性。标记静态节点和静态属性可以帮助 Vue 在后续的更新中跳过这些节点的比对和更新过程，从而提高应用的性能。
3. generate(代码生成)：将 AST 转换为可执行的渲染函数。

# Vue3 和 Vue2 有哪些区别

这是个开放题，不建议背答案，可以结合自己的理解和项目经历展开来讲。如果聊的好的话，没准这一个话题就能帮你获得面试官的青睐。

## 性能提升方面

1. Proxy 替代 Object.defineProperty，性能提升，监听更完备。
2. 模板编译的优化，基于 Block 的静态标记。
3. 优化 slot 的生成，优化渲染函数的生成。
4. 更好的 Tree Shaking，对于没有用到的 API，打包时会剔除，减小包体积。

## 组合 API 和 Reactive API

1. 可以从逻辑复用、自定义组合 API 展开讲解。
2. 可以讲讲 ref 这个 API 的故事和争论。
3. 可以讲讲 Vue3 和 Vue2 响应式原理实现方式的不同。

## TypeScript 支持方面

Vue3 源码用 TypeScript 重写，Vue3 的 API 也提供了更好的 TS 类型定义。

## 自定义渲染器

可以讲一讲自定义渲染器能做的一些好玩的事情，比如将 Vue 用于 WebGL 或 Canvas 的渲染。

## 更好的源码架构

1. Monorepo 把模块拆分开，逻辑更清晰，源码更容易维护。
2. 模块划分也使得更多 API 被解耦出来，比如 Reactive API。
3. 源码更容易阅读，方便社区维护。

## 新的功能和组件

1. Teleport：可以将一个组件的内容渲染到任何地方，而不必受到父组件的限制，一般用于封装弹框组件。
2. 更好用的组件递归。
