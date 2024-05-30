---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-08-20-Sun, 1:07:11 pm","date-modified":"2024-04-02-Tue, 4:03:52 pm","permalink":"/programming/FAQ/react-faq/","dgPassFrontmatter":true}
---


# API 层面

## setState 是有副作用的

```ts
  const isInQueue =
    !enableCreateVideo && accountService?.hasLogin && !isOverseaLimitedCountry && hasFinishVideoQuestion;

  const [createVideoBtnKey, setCreateVideoBtnKey] = useState(
    isInQueue ? 'dreamina_ai_video_joining_waitlist' : 'frame_change_start_create',
  );

  useEffect(() => {
    setCreateVideoBtnKey(isInQueue ? 'dreamina_ai_video_joining_waitlist' : 'frame_change_start_create');
  }, [isInQueue]);
```

isInQueue 值的变化, 并不会更新 key 的值. useState 也是一个有副作用的函数. 每次重渲染的时候, useState 虽然都会执行. 但是 useState 只会持有最初的 initialState 和后续 setState 更新的值.

在函数式编程中，纯函数的定义是给定相同的输入，始终产生相同的输出，并且不会产生副作用。从这个角度来看，`useState` 似乎不符合纯函数的定义，因为尽管每次组件重新渲染时 `useState` 被调用，但它却每次都返回相同的值，并且会影响组件的状态。

然而，在 React 中，`useState` 被设计为一种特殊的钩子函数，用于在函数组件中管理状态。虽然它每次都返回相同的值，但这并不违反 React 的设计原则。在 React 中，状态的变化并不依赖于函数的输入，而是由 React 自身的更新机制管理的。当状态发生变化时，React 会负责重新渲染组件，并在必要时更新状态。

因此，虽然从函数式编程的角度来看，`useState` 可能不符合纯函数的定义，但在 React 中，它仍然被认为是一种纯函数的钩子函数，因为它的行为是可预测的，并且不会产生副作用，而状态的变化和更新是由 React 控制和管理的。

> 当然, 你也可以理解为, 所有不同的输入都得到了相同的输出, 所以对于所有不同的输入而言, 每次 rerender 都是相同的输出, 也就可以说 useState 还是纯函数... 有点无语了

## *setState 是同步还是异步？

### V18 之前

1. React 是希望 setState 表现为异步的，因为批量更新可以优化性能。因此在 React 能够管控到的地方，比如生命周期钩子和合成事件回调函数内，表现为异步。
2. 在定时器和原生事件里，因为 React 管控不到，所以表现为同步。
3. 在某些情况下，我们需要立即获取更新后的状态，这时可以使用第二个可选参数 callback，在状态更新后立即执行回调函数来获取更新后的状态。例如：

```javascript
this.setState({ counter: this.state.counter + 1 }, () => {
  console.log(this.state.counter); // 输出更新后的值
});
```

### V18 之后

1. 从 `react 18` 开始, 使用了 `createRoot` 创建应用后, 所有的更新都会自动进行批处理 (也就是异步合并).使用 `render` 的应用会保持之前的行为.
2. 如果你想保持同步更新行为, 可以使用 `ReactDOM.flushSync()`.
我们知道, `react` 的 `setState` 最终会走到 `scheduleUpdateOnFiber` 来进行更新, 之前最关键的一段代码就在这里

```js
// react 17.x
if (executionContext === NoContext) {
  // Flush the synchronous work now, unless we're already working or inside
  // a batch. This is intentionally inside scheduleUpdateOnFiber instead of
  // scheduleCallbackForFiber to preserve the ability to schedule a callback
  // without immediately flushing it. We only do this for user-initiated
  // updates, to preserve historical behavior of legacy mode.
  resetRenderTimer();
  flushSyncCallbackQueue();
}
```

`executionContext` 代表了 `react` 当前的调度状态, 如果退出了 `react` 的调度这个值就会重新变成 `NoContext`. 也就是说, 如果你调用 `setState` 的时候并不处于 `react` 的调度状态中, 那么就会同步的去执行你的 `setState`.这也是为什么一旦我们使用一些异步操作就会导致 `setState` 变成同步的原因, 而在 `react 18` 中这段代码变成了这样

```js
// react 18.x
if (
  lane === SyncLane && 
  executionContext === NoContext && 
  (fiber.mode & ConcurrentMode) === NoMode && 
  // Treat `act` as if it's inside `batchedUpdates`, even in legacy mode.
  !( ReactCurrentActQueue$1.isBatchingLegacy)) {
  // Flush the synchronous work now, unless we're already working or inside
  // a batch. This is intentionally inside scheduleUpdateOnFiber instead of
  // scheduleCallbackForFiber to preserve the ability to schedule a callback
  // without immediately flushing it. We only do this for user-initiated
  // updates, to preserve historical behavior of legacy mode.
  resetRenderTimer();
  flushSyncCallbacksOnlyInLegacyMode();
}
```

可以看到我们多出了好几个判断条件, 除了之前的 `executionContext === NoContext` 之外, 还多了三个判断条件, 我们一个一个来看看.

[React18 setState: 消失的面试题 - 知乎](https://zhuanlan.zhihu.com/p/460668459)

## Class 写法和 Hook 写法的区别

Class 写法和 Hook 写法的区别;;[react-faq](https://digital-garden.chiyuu.top/programming/faq/react-faq/#class-hook) <!--SR:!2023-09-13,11,250-->

### 差异点

1. 首先，生命周期的用法不同，Class 的生命周期钩子直接在类中定义即可，可以直接使用， 但是在函数式组件我们只能通过 Hooks 的副作用函数来模拟声明周期，useEffect 可以实现多个生命周期钩子
2. 在性能优化方面：在类组件我们使用纯组件 PureComponent 和 shouldComponentUpdate 来控制组件的更新， 在函数组件中我们可以使用 memo 实现纯组件的浅比较，同时可以使用 useMemo 和 useCallback 两 个 hook 来对属性和函数进行优化，需要子组件搭配 memo 或者 pureComponent
3. 但是实际的性能上应该是差不多的, 都是基于相同的 fiber 算法

### Class 组件存在的问题

1. 复杂组件变得难以理解, 比如: componentDidMount 中，可能就会有大量逻辑代码，包括网络请求，一些事件的监听（还需要在 componentWiIIUnmount 中移除） 而对于这样的 class 实际上很难拆分，因为这些逻辑往往混在一起，强行拆分反而会造成过度设计，增加代码的复杂度。
2. 难以理解的 class：ES6 中 class 相当于 React 的一个障碍； 在 class 中，我们必须搞清楚 this 的指向到底是谁，所以需要花很多的精力去学习 this；虽然掌握 this 是必要，但是处理起来依然很麻烦
3. 组件复用状态很难：在之前为了一些状态的复用，我们需要通过高阶组件或 renderprops； 像 redux 中 connect 者 react-route 中的 withRouter, 这些高阶组件设计的目的就是为了状态的复 用。 或者类似于 Provider,Consumer 来共享一些状态，但是多次使用 Consumer 时，就会有很多嵌 套

### Hook 的优点

1. 更好的复用逻辑, 通过自定义 hook 进行复用
2. 业务代码更加聚合, 避免了意大利面条式的代码分散在各种生命周期中
3. 更加贴合 react 的设计理念 [!framework-common](../front-end/framework/framwork-common/!framework-common.md#C%20响应原理%20和%20D%20模型风格%20的关系%20（这是重点！！！）)

### Hook 的缺点

Class 相比函数式组件的优势, 但是实际中这些都是被忽略不计

1. class 组件内部可以定义自己的 state,用来保存组件自己内部的状态；函数式组件不可以，因为函数每次调用都会产生新的临时变量, 需要使用 useRef 实现类似的功能
2. class 组件有自己的生命周期，可以在对应的生命周期中完成自己的逻辑；比如在 componentDidMount 中发送网络请求，并且该生命周期函数只会执行一次；函数式组件在学习 hooks 之前，如果在函数中发送网络请求，意味着每次重新渲染都会重新发送一次网络请求。
3. class 组件可以在状态改变时只重新执行 render 函数以及我们希望重新调用的生命周期函数 componentDidUpdate 等；函数式组件在重新渲染时，整个函数都会被执行，似乎没有什么地方可以只让它们调用一次；

## 自定义 Hook

实际上，我们只能使用对 React 提供的 Hooks 或者其他自定义 hook 进行一层逻辑的封装，并不能自己创造 Hooks。也就是说，实际上我们可以把那些自定义 Hooks 都拍平，拍平之后还是那几个 React 提供的 Hooks。

这是因为这些 Hooks 是依赖 React 执行流程和内部数据结构的，想要增加 Hooks 要动 React 本身的代码。

自定义 Hook 共享的只是状态逻辑而不是状态本身。对 Hook 的每个调用完全独立于对同一个 Hook 的其他调用

> 看到的自定义 hook 都是一些很脑残的封装, 如果不是实际中有多处要使用, 根本不会这样特地处理

## 使用 Hooks 有踩过哪些坑？

使用 Hooks 有踩过哪些坑？;;[](https://digital-garden.chiyuu.top/programming/faq/react-faq/#hooks)<!--SR:!2023-09-12,10,250-->

1. useEffect 中没有正确设置依赖数组导致死循环
2. useEffect 中没有清除副作用导致内存泄漏
3. 在条件语句和循环中使用 Hooks 导致报错 [react-hook-advanced](../front-end/framework/react/react-hook-advanced.md#必须按照相同的顺序调用%20Hooks)
4. 闭包陷阱。

```JS
import React, { useState, useEffect } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      // 这里的 count 变量是捕获的初始值，而不是最新的状态
      setCount(count + 1);
    }, 1000);

    return () => clearInterval(interval);
  }, []); // 依赖数组为空，只会执行一次

  return <div>{count}</div>;
}

export default Counter;

```

为了避免闭包陷阱，可以使用 useCallback 和 useMemo 来确保回调函数和依赖项都在正确的上下文中更新。另外，也可以将涉及到状态更新的函数移到 useEffect 内部，以保证使用正确的状态值。

总之，闭包陷阱是在使用 React Hooks 时需要特别注意的一个问题，理解它可以帮助你避免在组件中出现奇怪的问题。

## Hooks 实现原理？

Hooks 实现原理？;;[react-hook-advanced](https://digital-garden.chiyuu.top/programming/font-end/framework/react/react-hook-advanced/#hooks) <!--SR:!2023-09-11,9,250-->

## JSX 和模板引擎有什么区别？

1. JSX：更加灵活，既可以写标签，也可以使用 JS 语法和表达式，在做复杂渲染时更得心应手。
2. 模板引擎：更简单易上手，开发效率高，结合指令的可读性也比较好。
3. JSX 太灵活就导致没法给编译器提供太多的优化线索，不好做静态优化，模板引擎可以在编译时做静态标记，性能更好。
4. JSX 只是个编译工具，Vue 经过一定的配置也可以使用。

## 如何优雅的实现弹窗

jsx props children

https://zh-hans.react.dev/learn/passing-props-to-a-component#passing-jsx-as-children

createPortal

https://zh-hans.react.dev/reference/react-dom/createPortal

利用 createRoot

https://zh-hans.react.dev/reference/react-dom/client/createRoot

## Props, State, Ref 区别

## useLayoutEffect

useLayoutEffect 如何做到阻塞浏览器渲染是一个有趣的问题，涉及到 React 中的调度机制。

在 React 中，组件的渲染是一个多阶段过程，其中包括：

Render（渲染）阶段：React 构建虚拟 DOM 树并计算要进行的更新，但尚未进行实际的 DOM 操作。

Layout（布局）阶段：React 计算布局，这涉及计算元素的大小和位置等信息。这是 React 中 useLayoutEffect 执行的阶段。

Paint（绘制）阶段：React 将最终的虚拟 DOM 变化应用于实际的 DOM，并触发浏览器的绘制操作。

useLayoutEffect 是在 Render 阶段和 Paint 阶段之间执行的，因此它可以访问到虚拟 DOM 树在 Render 阶段计算的布局信息。这使得 useLayoutEffect 能够执行同步的、直接影响布局的操作，而且在操作期间不会触发多次绘制。这就是为什么 useLayoutEffect 被称为“同步版本的 useEffect”。

要注意的是，由于 useLayoutEffect 是同步执行的，如果在其中执行的操作非常耗时，可能会导致页面卡顿，因为它会阻塞 Paint 阶段。因此，在使用 useLayoutEffect 时，应该确保操作足够快速，以避免对用户体验产生负面影响。

总之，useLayoutEffect 之所以能够阻塞浏览器渲染，是因为它在 React 的渲染过程中具有特殊的执行时机，允许它在布局阶段执行同步操作，而不会触发额外的绘制。这为一些需要精确布局信息的操作提供了便利。

# 开发实践

## React 逻辑复用方式有哪些？

组件封装和逻辑复用，是前端进阶必备的，小伙伴们可以多花点时间深入研究，这里只是简要总结。

1. Mixin：有很多缺点，已被弃用，可以不考虑。
2. HOC(高阶组件)：高阶组件是一个函数，它接收一个组件作为参数并返回一个新的组件。高阶组件可以将一些通用的逻辑（如：数据获取、权限验证、错误处理等）封装到一个函数中，并将其作为高阶组件的参数传递给其他组件使用，HOC 一般以 withXxx 命名，并可以结合装饰器优雅地使用。
3. Render Props：通过在组件中传递一个函数作为 prop，该函数将用于渲染组件的内容。这个函数可以接收组件需要的数据和方法，并返回 React 元素。
4. Hooks：自定义 Hooks，将通用逻辑封装到 useXxx 函数中，可以在多个组件内使用，常见的像数据请求、表单、防抖节流、拖拽等。

## React 代码层面有哪些性能优化的方式？

1. React.memo()：可以缓存组件的渲染结果，避免不必要的重渲染。它接受一个函数组件，并返回一个新的组件，新组件将只在 props 发生变化时才重新渲染。
2. useMemo 和 useCallback。
4. 使用 React.lazy() 和 Suspense 进行组件懒加载。

### React Rerender

其实大部分问题确实不是性能问题, 而是影响调试. 打一个 log 结果出现几十条, 打一个断点结果一直在原地打转.

# 源码分析

## Redux 中间件是什么？实现原理？

中间件的本质就是个函数，在 Redux 每次写数据的时候执行，用来实现一些通用的功能。

常见的中间件功能包括异步中间件、持久化中间件、log 中间件。

Redux 中间件的实现原理和 Koa 中间件、Axios 拦截器类似，数组里面存函数，然后 compose 调用中间件函数，并传递参数给中间件。

## Fiber 是什么？

1. Fiber 是一种数据结构，由 VDOM 转化生成。
2. Fiber 的思想是将组件树的遍历过程拆分成多个小的、可中断的任务，以实现更细粒度的控制和优化。
3. 具体来说，Fiber 将每个组件看作是一个执行单元，并将组件树转换成一棵 Fiber 树。每个 Fiber 节点都包含了组件的状态和一些额外的信息，例如优先级、副作用等。
4. 在更新过程中，React 会根据 Fiber 节点的优先级，将 Fiber 树转换成一个任务队列，然后按照优先级进行调度和执行。React 还会利用浏览器提供的 requestIdleCallback API 来分配空闲时间，以避免阻塞渲染线程。
5. 由于 Fiber 将组件树的遍历过程拆分成了多个小的、可中断的任务，因此 React 可以在需要更新的部分进行优化，从而提高渲染和更新的性能。例如，在执行更新任务时，React 可以根据优先级调整任务的执行顺序，避免低优先级任务阻塞高优先级任务的执行，提高了应用程序的响应速度和性能。

## 怎么理解 Fiber 和并发模式？

## 为什么要设计并发模式？

在 React 的旧版本中，当组件状态发生变化时，React 会将整个组件树进行递归遍历，生成新的虚拟 DOM 树，并与旧的虚拟 DOM 树进行比较，找出需要更新的部分，然后将这些部分更新到 DOM 中。这种遍历方式虽然简单，但是在组件树变得非常大、复杂的情况下，会导致渲染和更新性能下降，造成页面卡顿甚至无法响应用户操作的情况。为了解决这个问题，React 引入了并发模式。

## 源码八股

[同志们，来点时下最流行的react八股文呀- 脉脉](https://maimai.cn/web/gossip_detail/32602821?egid=d2446e771bd843c29090d356837d2ecc&gid=32602821&operation_id=iSNQh5eObJ8B5JZa96hyY&share_channel=5&share_euid=mMbSOT69TGwmoP5IWbOvwJ8EwnobXE_oF2rjmFgjaFI6CgXyB1-RSQoOVi9Qgb8ZV4vB4riWyb-fYJ6UGRm9jg)
