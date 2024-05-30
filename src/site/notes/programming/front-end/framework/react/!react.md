---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-08-16-Wed, 7:57:53 pm","date-modified":"2023-12-03-Sun, 11:39:10 am","permalink":"/programming/front-end/framework/react/!react/","dgPassFrontmatter":true}
---


只需要记录一些深入的内容, 如果是 api 性质的和 quick start 性质的直接看官网就行

[Quick Start – React](https://react.dev/learn)

[快速入门 – React 中文文档](https://zh-hans.react.dev/learn)

# 使组件保持存粹

[保持组件纯粹 – React 中文文档](https://zh-hans.react.dev/learn/keeping-components-pure)

# 将 UI 视为树

[Understanding Your UI as a Tree – React 中文文档](https://zh-hans.react.dev/learn/understanding-your-ui-as-a-tree)

# React Forget

[2年过去了，React Forget 凉了么？ - 知乎](https://zhuanlan.zhihu.com/p/663197428)

# ForwardRef 和 useImperativeHandle

做深层节点的暴露工作

和把 ref 传下去有什么不同呢? 正常的 ref 的用法是不会作为 props 传下去的. 所以应该没有办法传, 只能有 forwardRef 实现这个功能

然后为了进一步约束能力, 可以配合 useImperativeHandle 指定一个更小的能力集

[forwardRef – React 中文文档](https://zh-hans.react.dev/reference/react/forwardRef#usage)

**不要滥用 refs**。只有对于作为 props 无法表达的 **命令式** 行为才应该使用 ref：例如滚动到节点、将焦点放在节点上、触发动画，以及选择文本等等。

**如果可以将某些东西使用 props 表达，那就不应该使用 ref**。例如，不要从一个 Modal 组件中暴露一个命令式的句柄，如 `{ open, close }`，更好的做法是像这样使用 `isOpen` 作为一个像 `<Modal isOpen={isOpen} />` 的 prop。[Effects](https://zh-hans.react.dev/learn/synchronizing-with-effects) 可以帮助你通过 props 暴露命令式行为。

# Portal

[你真的知道 React Portal 吗？ - 掘金](https://juejin.cn/post/6844904024378982413)
[javascript - Deprecation notice: ReactDOM.render is no longer supported in React 18 - Stack Overflow](https://stackoverflow.com/questions/71668256/deprecation-notice-reactdom-render-is-no-longer-supported-in-react-18)
只能是原本就在组件树的组件去使用, 让一个在树中的 react 组件, 渲染到指定位置去

无法动态的插入一个 react 组件到 dom 树中.
