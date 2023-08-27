---
{"dg-publish":true,"permalink":"/programming/faq/framwork-faq/"}
---


# Vue 和 React 有哪些区别，你更喜欢哪个

这是个开放题，不建议背答案，可以从**生态、语法、性能、原理、开发体验**等方面去试着比较一下。

## React Vs Vue

todo: vue2-mvvm

比较适合测试驱动实现, 每一步都写好对应的单元测试, 来逐步实现 feature, 弄一个云 vscode 来编写简易的代码仓库

有没有能代替 git 的版本管理工具呢? 能逐步体现代码的添加功能的, 管理起来要方便的点 , 方便对比每个版本的差异, 修改低版本不能整天 rebase

[如流知识库](https://ku.baidu-int.com/knowledge/HFVrC7hq1Q/pKzJfZczuc/7hcVZOKfCL/NYZvkWunJSP2Uf)

### React Vs Vue

React 从诞生之日的设计哲学就是，**当状态发生变化时，重新创建所有视图。**

即使你只是修改了组件树最深处的一个小小的状态，React 仍然需要自顶重新创建整个 vdom 树并且 Diff

如今的前端应用越来越复杂，屏幕刷新率也都越来越高。对于一个复杂的前端项目来说，组件的数量和组件树的层级都非常深。重新创建整个树的开销也会变大，如果超过了 16.7ms（刷新率 120hz 的话就是 8.4ms）就会导致掉帧、用户交互响应延迟。引入 fiber 机制后使得 React 的渲染过程可以中断，让出主线程响应用户事件，或者允许插入更高优先级的更新。

而 Vue 有一套细粒度的响应式机制，框架的更新粒度是组件级别，除此之外还有 Block Tree 等分离静态/动态模板的优化手段，更新的效率是足够高的，也就没有必要引入 [fiber](https://www.zhihu.com/search?q=fiber&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A2903903827%7D) 这样的机制

其实设计哲学的优劣并不重要, 重要的是我们去理解他的设计思想, 更好的使用他, 以及相应的生态. react 和 vue 究竟哪个更优秀, 时间自然会给出他的答案. 也许胜者会出现在他们两个之外也说不定呢? 就算是 jQuery 也要把他用好才行

### React Api. 和 Vue Api

[JavaScript.md](https://github.com/CavsZhouyou/Front-End-Interview-Notebook/blob/master/JavaScript/JavaScript.md#148-vue-%E7%BB%84%E4%BB%B6%E9%97%B4%E7%9A%84%E5%8F%82%E6%95%B0%E4%BC%A0%E9%80%92%E6%96%B9%E5%BC%8F)

[2023前端之VUE面试题汇总 - 知乎](https://zhuanlan.zhihu.com/p/610576255)

# 有了解过哪些类 React 框架，谈谈你对它们的看法

Preact：可以理解为简易版 React，但是和 React 有一样的 API，性能比 React 还好，甚至也实现了并发模式，对于想要阅读 React 源码又觉得难的同学，可以看一看 Preact 的源码。

Svelte：无虚拟 Dom，依靠编译器和纯响应式的轻量级框架，然而性能却非常好。

SolidJS：和 Svelte 类似，但是 SolidJS 的语法更接近于 React，Svelte 的语法接近 Vue。

总结一下：

1. 这些类 React 或者类 Vue 框架，可能在某一方面或者某些方面表现很出色。
2. 在开发成熟项目时，还是尽量选择 Vue 和 React，因为毕竟生态和解决方案更多。
3. 时间允许的话推荐阅读这些框架的源码，它们的代码量相对少，容易阅读，能让你有一个更广的视野来看待前端框架的原理和设计思路。
