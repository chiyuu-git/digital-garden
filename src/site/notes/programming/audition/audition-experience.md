---
{"aliases":["面经"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-08-15-Tue, 8:37:21 pm","date-modified":"2023-09-20-Wed, 5:27:30 pm","permalink":"/programming/audition/audition-experience/","dgPassFrontmatter":true}
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

![css-2.1](../front-end/primitive/css/css-2.1.md#可替换元素和不可替换元素)

### 行高

vertical-align baseline 你是怎么理解的

### 层叠上下文

![css-2.1](../front-end/primitive/css/css-2.1.md#层叠上下文%20https%20developer%20mozilla%20org%20zh-CN%20docs%20Web%20CSS%20CSS_Positioning%20Understanding_z_index%20The_stacking_context)

### 为什么行内级元素可以包裹块级元素

![css-2.1](../front-end/primitive/css/css-2.1.md#^vkanjg)

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

![ts-advanced](../front-end/framework/typescript/ts-advanced.md#型变和结构化类型)

### TS Compose

## JS

### 事件机制

![browser-thread](../front-end/browser-core/browser-thread.md#线程机制与事件机制)

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

## 自定义 Hook 的实际使用经历

## 为什么 Hook 不能在条件上去使用

## 跨域问题

## 缓存机制

## XSS 攻击与富文本

## 从浏览器输入网址到

## 缩短白屏时间

## Less 预处理器

## React 状态管理

## React-router

### 实现 Keep-alive

## Qiankun

## 页面卡顿问题如何分析和解决

## Table 10w

## Webpack 配置

# 得物一面 2023-0829

商家和国际产品团队, 中后台为主. 国际业务分为进出口, 包含 B 端 和 C 端, 得物在进口做了挺久了. 出口是新的.

技术栈是以 react.

## 协同编辑器算法

## 选择 Tiptap 的原因

1. slate
2. quill
3. tailwind、ts
4. tiptap-san

## Tiptap-san 具体功能

## 小程序 Js 与 Native 通信的方式

[!hydrid](../hydrid/!hydrid.md)

## 请求并发任务队列

# 小红书一面 2023-08-30

## 聊项目

三个都聊了一下

## 实现 Event-bus

on emit off emit 支持传参数

## Leetcode 14

# 富途一面 2023-08-31

## 同构字符串

## This 关键字

## Bind Call Apply

## 垃圾回收和内存泄漏

## 堆外的内存泄漏?

## 数据库查询优化

涉及十几个 sql 语句. 在不改变表结构的前提下去优化.

用视图.

## 下划线转换成驼峰

要考虑各种边界 case

不信任, 健壮, 避免 error

## 从输入网址的到...

# 滴滴一面 2023-09-01

## 二分 + 防抖 + 截流

## 事件机制

## Promise Then Catch

## 富文本的序列化于反序列化

## Webpack 运行机制

也讲了一下 tsup 打包工具

## Tiptap-san Api 如何设计

## 小程序 Native 主要是为了解决什么问题

## 跨域

## 缓存

# 滴滴二面 2023-09-05

## Event-bus 如何改造成 Promise 的接口

所有的 on 都需要返回一个 promise

emit 的时候调用 resolve

但是 promise 只能 resolve 一次呀? 没办法反复 resolve

# 得物三面 2023-09-07

# 拼多多一面 2023-09-07

## 场景题: 监控掉帧情况

[!performance-optimization](programming/FAQ/performance-optimization/!performance-optimization.md#场景题%20如何监测页面的掉帧情况)

## 前端动画技术方案

## 前端性能优化

## 线上问题的排查思路

## React createProtal

## React Image reTry

## Promise. race()

# 支付宝 一面 2023-09-08

支付宝开放平台, 提供低码工具, 编辑器, 主要的投入. 定协议标准, 阿里低代码引擎

编辑器还要服务于具体的业务平台, 会接我们的 open api, 有点基于 open sumi, monaco 的 IOC DI 的机制

全栈团队, node 服务、db

DDD 领域驱动设计

微服务系统, 控制, 负责 B 端流程、数据、页面发布.

对 C 流量的, 流量集群, SSR

## Npm 依赖分析, 是否有循环依赖

dfs 回溯、递归

## 然后就是项目为主

# 支付宝 终面 2023-09-11

基本都是问项目

# 富途二面 2023-09-11

## 问个人项目

可能是比较看重后端经历吧

## Mysql 如何建立索引、什么建立索引

对哪些字段建立索引

a = xxx AND b = xxx AND c > xxx

## Leetcode 重复子串构成

## Leetcode 概率题, 有 x() 50% 生成 y() 10%

# 快手一面 2023-09-12

## 闭包的理解

## 三次握手、四次挥手

## Http 状态码

## 跨域问题

## Post 和 Get 的区别

## Props, State, Ref 区别

## React 生命周期

## React 高阶组件

## Webpack 的理解

## Ts 和 Js 的区别

# 飞猪一面 2023-09-12

## 深克隆

## 下划线转驼峰

## Npm 版本号比较大小

## 封装 useFecth 钩子

要有 loading error 态, 要支持 get 和 post 方法

# 腾讯一面 2023-09-13

## Flutter 和 Rn 的区别

# 字节二面 2023-09-13

确实都是根据简历上写的点来延伸, 来问的

## Nlp 相关的概念

## Editor 的沿革

## 对象属性描述符

## Ts Any 和 Unkown 的区别

## React Hook 的理解

# 富途三面 2023-09-15

写一个函数，将一个字符串进行反转。反转规则如下：“sg.com.moomoo.account”反转成“account.moomoo.com.sg”，要求：不借助任何库函数 (只能使用类似 a[i]=a[j] 这种赋值操作)，空间复杂度要求是 O(1)。编程语言不限，如果使用的编程语言无法操作字符串，可以把字符串转成数组之后再实现题目要求。

有两个城市，分别是 A 和 B，A 城市有 20W 人，B 城市有 80W 人，假设一天内两个城市会产生 50w 通电话，每通电话都是随机打的，请问跨城市打的有多少？

//20* 80/100

//80* 20/100

// 25W

// 2/10*8/10 + 8/10*2/10

// 32/100

// 16w

# 美团二面 2023-09-18

## 场景题

假设你是一名前端工程师, 我现在有一个问题需要你帮忙解决. 你在深圳开发了一个 h5 页面, 但是一个在北京的用户发现该页面白屏了. 而且周围的同事用相同的机型, 也没能复现该问题. 你有什么排查思路吗?

localstorage 大家都去写, 很容易就写满了. 如何处理呢? 其实可以用 clear 清除掉.

## Leetcode

给定数组 nums = [-3, -1, -2, 0, 2,3]，和 target = 0。

给出所有的情况

# 阿里飞猪二面 2023-09-18

酒店前端团队:

1. 做一些国际化, 老挝、新加坡, 给 lazada 开一个口子
2. 多语言、小语种, 比想象中的需求量要大很多
3. 有技术方向, aigc, 怎么通过 stable fussion 赋能业务. 翻译

## sessionStorage 和 localStorage 的区别

## 和 IndexedDB 又有什么区别

## Proxy 了解吗? 有哪些拦截方法

# 拼多多二面 2023-09-20

复杂动画的经验

css 合成层

全局的错误捕获和单个错误捕获有什么区别

v8 是如何执行 js 的代码的
