---
{"dg-publish":true,"permalink":"/programming/audition/audition-experience/"}
---


# 百度一面

先约面试, 然后重点拆解一下他的简历, 再从题库上问问.

实际应该问哪些问题? 题库从哪里出?

打分怎么打?

## 自我介绍

我是百度的前端工程师, 我们团队主要是做小程序框架,   提供给百度小程序的开发者使用, 但是也会承接一些常规前端业务. 现在在进行的是暑期实习的一面, 对于面试中的问题希望你可以尽可能详细的解答, 也可以延伸到相关的知识点, 面试过程中我需要随时记录一些信息, 而且也可能会打断你. 接下来, 请你先自我介绍一下吧.

## 项目

### 遇到了哪些难点都是怎么解决的

不光是技术上的, 项目上的也可以分享一下.

## HTML

### 可替换元素和不可替换元素

![css-2.1](../font-end/primitive/css/css-2.1.md#可替换元素和不可替换元素)

### 行高

vertical-align baseline 你是怎么理解的

### 层叠上下文

![css-2.1](../font-end/primitive/css/css-2.1.md#层叠上下文%20https%20developer%20mozilla%20org%20zh-CN%20docs%20Web%20CSS%20CSS_Positioning%20Understanding_z_index%20The_stacking_context)

### 为什么行内级元素可以包裹块级元素

![css-2.1](programming/font-end/primitive/css/css-2.1.md#^vkanjg)

## CSS

### Flex-grow Flex-shrink Flex-basis Flex

怎么理解和使用的, 在实际开发中解决过什么问题

### 为什么没有 Justify-items

grid 布局中是有的

因为 flex 布局是一维布局的方式, 所有 item 已经是默认沿着主轴排列了, 只需要使用 justify-content 整体控制即可

[In CSS Flexbox, why are there no "justify-items" and "justify-self" properties? - Stack Overflow](https://stackoverflow.com/questions/32551291/in-css-flexbox-why-are-there-no-justify-items-and-justify-self-properties)

### 移动端适配

rem 响应式布局

## TS

### 型变和结构化数据类型

![ts-advanced](../font-end/framework/typescript/ts-advanced.md#型变和结构化类型)

### TS Compose

## JS

### 事件机制

![browser-thread](../font-end/browser-core/browser-thread.md#线程机制与事件机制)

### 反转链表

两种方法

题目描述：

给定一个单向链表的头节点 head，将该链表反转，并返回反转后的链表头节点。

示例：

输入：1 -> 2 -> 3 -> 4 -> 5 -> null

输出：5 -> 4 -> 3 -> 2 -> 1 -> null

解题思路：

使用迭代或递归的方式都可以实现链表的反转，这里介绍迭代的方式。迭代的思路是，遍历链表中的每一个节点，将当前节点的 next 指针指向前一个节点，然后将当前节点作为前一个节点，继续遍历下一个节点，直到遍历完整个链表。

代码实现：

```JS
function reverseList(head) {
  let prev = null;
  let curr = head;
  while (curr !== null) {
    let next = curr.next;
    curr.next = prev;
    prev = curr;
    curr = next;
  }
  return prev;
}
```

在上面的代码中，我们定义了一个 `reverseList` 函数，该函数接受链表的头节点作为参数，并返回反转后的链表头节点。在函数中，我们使用了三个指针，分别指向前一个节点、当前节点和下一个节点。在循环中，首先将当前节点的 next 指针指向前一个节点，然后将前一个节点更新为当前节点，将当前节点更新为下一个节点。最后，返回反转后的链表头节点即可。

递归方式实现链表反转，可以将链表的反转看作是对每个节点进行反转的过程。对于每个节点，我们将它的下一个节点作为子问题，递归地反转后，将当前节点作为子问题的下一个节点的 next 指针，即完成了对当前节点的反转。最终返回反转后的链表头节点。

代码实现：

```TS
function reverseList(head) {
  if (head === null || head.next === null) {
    return head;
  }
  let newHead = reverseList(head.next);
  head.next.next = head;
  head.next = null;
  return newHead;
}
```

在上面的代码中，我们定义了一个 `reverseList` 函数，该函数接受链表的头节点作为参数，并返回反转后的链表头节点。在函数中，首先判断链表是否为空或只有一个节点，如果是，则直接返回链表头节点。否则，将链表的反转看作是对链表的后续部分进行反转的过程，递归地反转后，将当前节点的下一个节点的 next 指针指向当前节点，然后将当前节点的 next 指针设为 null，完成对当前节点的反转。最后返回反转后的链表头节点即可。

## Sql

### WHERE 关键字和 HAVING 关键字的区别

使用 WHERE 可以做到在分组之前进行筛选，且 WHERE 只能筛选表中原本就存在的列

HAVING 只能选择在 SELECT 语句中出现的列

## Http

### HTTPS 加密过程

### 浏览器的缓存策略

![http-cache](../basic/cs-basic/network-protocol/http-cache.md#HTTP缓存)

### HTTP 访问控制

![http-session](../basic/cs-basic/network-protocol/http-session.md#HTTP%20访问控制（CORS）)

## Vue

### Vue3 中响应式数据是如何实现的？与 Vue2 相比有什么变化？

在 Vue3 中，响应式数据的实现使用了 Proxy 对象，该对象能够劫持对象属性的访问和修改操作，并在发生变化时通知相关的依赖。具体来说，当组件中访问一个响应式数据时，会触发该数据的 get 操作，这时会收集依赖，将该数据与组件的 Watcher 实例关联起来；当该数据被修改时，会触发 set 操作，这时会通知相关的 Watcher 实例进行更新。

相比于 Vue2，Vue3 中响应式数据的实现更加高效和灵活。使用 Proxy 对象的方式替换了 Vue2 中使用的 Object.defineProperty 方法，使得可以劫持更多的对象操作，并且不需要事先知道对象的属性，使得响应式数据更加灵活。此外，Vue3 中使用了更加精细的依赖收集和更新策略，避免了不必要的更新，从而提升了性能。

相比于 Object.defineProperty 方法，使用 Proxy 对象的方案具有以下几个优势：

1. 功能更强大：Proxy 可以拦截更多的操作，包括读取、赋值、删除、枚举等，而 Object.defineProperty 只能劫持对象的属性读取和赋值操作。
2. 可监听数组变化：使用 Proxy 对象可以监听数组的变化，而 Object.defineProperty 无法监听数组的 push、pop、splice 等操作。
3. 可以不需要预先定义属性：使用 Proxy 对象可以劫持整个对象的访问，而不需要事先定义所有属性的 get 和 set 方法。
4. 性能更好：Proxy 的性能比 Object.defineProperty 更好，因为 Proxy 可以直接劫持整个对象，而 Object.defineProperty 需要逐个定义属性。

### nextTick

Vue2 和 Vue3 都有一个名为 `nextTick` 的 API，用于在 DOM 更新后执行异步操作。虽然它们的目的相同，但在实现上有些区别。

#### Vue2 中的 nextTick

在 Vue2 中，`nextTick` 是一个在全局 Vue 对象上定义的方法，它接受一个回调函数作为参数，并将这个回调函数推入一个队列中，在下一个 DOM 更新循环结束之后执行。

具体来说，Vue2 中的 `nextTick` 实现依赖于浏览器的 `setTimeout` API 或者 `setImmediate` API（在支持该 API 的浏览器中）。当使用 `nextTick` 时，Vue2 会检测当前环境，如果支持 `setImmediate`，就使用它来异步执行回调函数；否则，使用 `setTimeout(fn, 0)` 来异步执行回调函数。

#### Vue3 中的 nextTick

Vue3 中的 `nextTick` 与 Vue2 相比有所变化，它不再是一个全局方法，而是作为 `vue` 模块的一部分被导出。它仍然接受一个回调函数作为参数，但是在实现上，它使用了一个新的 `scheduler` API 来管理异步更新。

`scheduler` API 是 Vue3 中新引入的调度器，它负责管理异步更新的过程，并将其转化为微任务或宏任务。在 `nextTick` 的实现中，Vue3 使用了 `scheduler` API 来异步执行回调函数，并将其推入到一个队列中，在下一个微任务或宏任务中执行。这样可以保证回调函数在当前事件循环结束后立即执行，而不需要等待下一个事件循环。

需要注意的是，与 Vue2 不同，Vue3 中的 `nextTick` 返回一个 Promise 对象，它会在回调函数执行完毕后 resolve。这样可以更方便地使用 `async/await` 语法来处理异步操作。同时，由于 `scheduler` API 的引入，Vue3 的 `nextTick` 在性能和可靠性上也比 Vue2 更加优秀。

综上所述，Vue2 和 Vue3 中的 `nextTick` 在实现上有所不同，但它们的目的都是为了在 DOM 更新后执行异步操作，并提供了一种方便的方式来处理异步任务。

## React Vs Vue

https://www.zhihu.com/question/468249924/answer/1965942197

https://zhuanlan.zhihu.com/p/133819602

https://zhuanlan.zhihu.com/p/139548169

https://zhuanlan.zhihu.com/p/158734246

https://www.zhihu.com/question/301860721/answer/545031906

为什么 vue 需要组合 api 呢？composite api solve what kind of problem . hook solve what kind of problem it is that i need to care?

TODO: 完善, react vs vue, 逐步引导

### 优点

### 异同

### 缺点

## Nestjs

为什么选择 nest, 做了什么, 具体的技术选型

WHERE 关键字和 HAVING 关键字的区别

## 依赖注入模式

控制反转, IOC 容器

有没有思考过为什么 nest 代码逻辑会更加简洁清晰?

# 招银一面 2023-08-25

## Class 写法和 Hook 写法的区别, 做了哪些优化

这里其实可以答一下 react 的本质是没有改变的, 只是写法改变了

hook 相比 class 的优化, 主要是代码逻辑组织上的优化.

## 自定义 Hook 的使用 useRanger

怎么使用, 描述的不够充分

## 为什么 Hook 不能在条件上去使用

## 跨域问题

提到了 canvas 的跨域问题

DNS 映射

生产环境 前后端不在一个域名, nginx 的配置, 其实可以吹一下, 我本地的项目就是通过 nginx 配置去转发的. DNS 污染

## 缓存机制

## XSS 攻击与富文本

## 从浏览器输入网址到

## 缩短白屏时间

其实就是各种性能优化的手段

react 里面要如何优先加载首屏

SSR 忘记提了

## Less 预处理器

## React 状态管理

可以简单说一下 redux 的思想

## React-router

history 和 hash 的区别

### 实现 Keep-alive

## Qiankun

## 页面卡顿问题如何分析和解决

## Table 10w

## Webpack 配置
