---
{"dg-publish":true,"permalink":"/programming/font-end/framework/react/react-hook/"}
---


react-hook 重新学习指南：

1. 重新学习 react 文档
2. 整理目前 reat 的相关内容
3. 查阅知乎上的单个知识点的深入文章
4. Dan 的文章都看一看 https://overreacted.io/zh-hans/writing-resilient-components/

搭配 typescript hook 类型：

[TypeScript 中使用React Hook - 掘金 (juejin.cn)](https://juejin.cn/post/6844903846607585293)

[好想用Typescript+React hooks开发啊!(建议收藏) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/112298757)

# useContext

```jsx
const value = useContext(MyContext);
```

接收一个 context 对象（`React.createContext` 的返回值）并返回该 context 的当前值。当前的 context 值由上层组件中距离当前组件最近的 `<MyContext.Provider>` 的 `value` prop 决定。

当组件上层最近的 `<MyContext.Provider>` 更新时，该 Hook 会触发重渲染，并使用最新传递给 `MyContext` provider 的 context `value` 值。

别忘记 `useContext` 的参数必须是 *context 对象本身*：1

- **正确：** `useContext(MyContext)`
- **错误：** `useContext(MyContext.Consumer)`
- **错误：** `useContext(MyContext.Provider)`

> 如果你在接触 Hook 前已经对 context API 比较熟悉，那应该可以理解，`useContext(MyContext)` 相当于 class 组件中的 `static contextType = MyContext` 或者 `<MyContext.Consumer>`。
>
> `useContext(MyContext)` 只是让你能够*读取* context 的值以及订阅 context 的变化。你仍然需要在上层组件树中使用 `<MyContext.Provider>` 来为下层组件**提供** context。

对先前 [Context 高级指南](https://zh-hans.reactjs.org/docs/context.html) 中的示例使用 hook 进行了修改，你可以在链接中找到有关如何 Context 的更多信息。

## Context Memoization

调用了 `useContext` 的组件总会在 context 值变化时重新渲染。如果重渲染组件的开销较大，你可以 [通过使用 memoization 来优化](https://github.com/facebook/react/issues/15156#issuecomment-474590693)

### 拆分 Context

If we just need `appContextValue.theme` in many components but `appContextValue` itself changes too often, we could split `ThemeContext` from `AppContext`.

```ts
function Button() {
  let theme = useContext(ThemeContext);
  // The rest of your rendering logic
  return <ExpensiveTree className={theme} />;
}
```

Now any change of `AppContext` won't re-render `ThemeContext` consumers.

This is the preferred fix. Then you don't need any special bailout.

### 拆分组件

If for some reason you can't split out contexts, you can still optimize rendering by splitting a component in two, and passing more specific props to the inner one. You'd still render the outer one, but it should be cheap since it doesn't do anything.

```ts
function Button() {
  let appContextValue = useContext(AppContext);
  let theme = appContextValue.theme; // Your "selector"
  return <ThemedButton theme={theme} />
}

const ThemedButton = memo(({ theme }) => {
  // The rest of your rendering logic
  return <ExpensiveTree className={theme} />;
});
```

### 使用 useMemo

Finally, we could make our code a bit more verbose but keep it in a single component by wrapping return value in `useMemo` and specifying its dependencies. Our component would still re-execute, but React wouldn't re-render the child tree if all `useMemo` inputs are the same.

```ts
function Button() {
  let appContextValue = useContext(AppContext);
  let theme = appContextValue.theme; // Your "selector"

  return useMemo(() => {
    // The rest of your rendering logic
    return <ExpensiveTree className={theme} />;
  }, [theme])
}
```

There might be more solutions in the future but this is what we have now.

Still, note that **option 1 is preferable — if some context changes too often, consider splitting it out**.

# Extra Hook

## useRef

```ts
const refContainer = useRef(initialValue);
```

`useRef` 返回一个可变的 ref 对象，其 `.current` 属性被初始化为传入的参数（`initialValue`）。返回的 ref 对象在组件的整个生命周期内保持不变。

一个常见的用例便是命令式地访问子组件：

```react
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` points to the mounted text input element
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

本质上，`useRef` 就像是可以在其 `.current` 属性中保存一个可变值的“盒子”。

### 用于访问 DOM 实例

你应该熟悉 ref 这一种 [访问 DOM](https://react.docschina.org/docs/refs-and-the-dom.html) 的主要方式。如果你将 ref 对象以 `<div ref={myRef} />` 形式传入组件，则无论该节点如何改变，React 都会将 ref 对象的 `.current` 属性设置为相应的 DOM 节点。

### 用于代替 Class 实例属性

然而，`useRef()` 比 `ref` 属性更有用。它可以 [很方便地保存任何可变值](https://react.docschina.org/docs/hooks-faq.html#is-there-something-like-instance-variables)，**其类似于在 class 中使用实例字段的方式**

这是因为它创建的是一个普通 Javascript 对象。而 `useRef()` 和自建一个 `{current: ...}` 对象的唯一区别是，`useRef` 会在每次渲染时返回同一个 ref 对象。

### 注意

请记住，当 ref 对象内容发生变化时，`useRef` 并不会通知你。变更 `.current` 属性不会引发组件重新渲染。如果想要在 React 绑定或解绑 DOM 节点的 ref 时运行某些代码，则需要使用 [回调 ref](https://react.docschina.org/docs/hooks-faq.html#how-can-i-measure-a-dom-node) 来实现。

参数 null，用于初始化对象的值，当获取到 DOM 节点后，对象的值也会自动更新

## useReducer

```ts
const [state, dispatch] = useReducer(reducer, initialArg, init);
```

[`useState`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usestate) 的替代方案。它接收一个形如 `(state, action) => newState` 的 reducer，并返回当前的 state 以及与其配套的 `dispatch` 方法。（如果你熟悉 Redux 的话，就已经知道它如何工作了。）

在某些场景下，`useReducer` 会比 `useState` 更适用，例如 state 逻辑较复杂且包含多个子值，或者下一个 state 依赖于之前的 state 等。并且，使用 `useReducer` 还能给那些会触发深更新的组件做性能优化，因为 [你可以向子组件传递 `dispatch` 而不是回调函数](https://zh-hans.reactjs.org/docs/hooks-faq.html#how-to-avoid-passing-callbacks-down) 。

以下是用 reducer 重写 [`useState`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usestate) 一节的计数器示例：

```tsx
const initialState = {count: 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```

**注意：**React 会确保 `dispatch` 函数的标识是稳定的，并且不会在组件重新渲染时改变。这就是为什么可以安全地从 `useEffect` 或 `useCallback` 的依赖列表中省略 `dispatch`。

### 指定初始 State

有两种不同初始化 `useReducer` state 的方式，你可以根据使用场景选择其中的一种。将初始 state 作为第二个参数传入 `useReducer` 是最简单的方法：

```ts
  const [state, dispatch] = useReducer(
    reducer,
    {count: initialCount}
  );
```

注意：React 不使用 `state = initialState` 这一由 Redux 推广开来的参数约定。有时候初始值依赖于 props，因此需要在调用 Hook 时指定。如果你特别喜欢上述的参数约定，可以通过调用 `useReducer(reducer, undefined, reducer)` 来模拟 Redux 的行为，但我们不鼓励你这么做。

### 惰性初始化

你可以选择惰性地创建初始 state。为此，需要将 `init` 函数作为 `useReducer` 的第三个参数传入，这样初始 state 将被设置为 `init(initialArg)`。

```tsx
function init(initialCount) {
  return {count: initialCount};
}

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    case 'reset':
      return init(action.payload);
    default:
      throw new Error();
  }
}

function Counter({initialCount}) {
  const [state, dispatch] = useReducer(reducer, initialCount, init);
  return (
    <>
      Count: {state.count}
      <button
        onClick={() => dispatch({type: 'reset', payload: initialCount})}>
        Reset
      </button>
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```

### 跳过 Dispatch

如果 Reducer Hook 的返回值与当前 state 相同，React 将跳过子组件的渲染及副作用的执行。（React 使用 [`Object.is` 比较算法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is#Description) 来比较 state。）

需要注意的是，React 可能仍需要在跳过渲染前再次渲染该组件。不过由于 React 不会对组件树的“深层”节点进行不必要的渲染，所以大可不必担心。如果你在渲染期间执行了高开销的计算，则可以使用 `useMemo` 来进行优化。

### Type

```ts
type Reducer<S, A> = (state: S, action: A) => S;

// Dispatch<A> 会检查 dispatch(action) 中 action 的类型永远为 Reducer<S, A> 中的 A。
type Dispatch<A> = (action: A) => void;

// 伪代码，这里有 R 满足 R extends Reducer<S, A>
type UseReducer<R, S, A> = (reducer: R, initialArg: S) => [S, Dispatch<A>];
```

参考：https://juejin.cn/post/6844904154595328014

## useCallback

```ts
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

返回一个 [memoized](https://en.wikipedia.org/wiki/Memoization) 回调函数。

把内联回调函数及依赖项数组作为参数传入 `useCallback`，它将返回该回调函数的 memoized 版本，该回调函数仅在某个依赖项改变时才会更新。当你把回调函数传递给经过优化的并使用引用相等性去避免非必要渲染（例如 `shouldComponentUpdate`）的子组件时，它将非常有用。

`useCallback(fn, deps)` 相当于 `useMemo(() => fn, deps)`。

## useMemo

```ts
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

返回一个 [memoized](https://en.wikipedia.org/wiki/Memoization) 值。

把“创建”函数和依赖项数组作为参数传入 `useMemo`，它仅会在某个依赖项改变时才重新计算 memoized 值。这种优化有助于避免在每次渲染时都进行高开销的计算。

记住，传入 `useMemo` 的函数会在渲染期间执行。请不要在这个函数内部执行与渲染无关的操作，诸如副作用这类的操作属于 `useEffect` 的适用范畴，而不是 `useMemo`。

如果没有提供依赖项数组，`useMemo` 在每次渲染时都会计算新的值。

**你可以把 `useMemo` 作为性能优化的手段，但不要把它当成语义上的保证。**将来，React 可能会选择“遗忘”以前的一些 memoized 值，并在下次渲染时重新计算它们，比如为离屏组件释放内存。先编写在没有 `useMemo` 的情况下也可以执行的代码 —— 之后再在你的代码中添加 `useMemo`，以达到优化性能的目的。

**优化的关键点：**找出是哪些 state 变化导致的重新渲染，通过 react 插件应该可以分析

## useImperativeHandle

父组件调用子组件的方法

参考 AnimationController.tsx

# Hook 闭包陈旧值问题

因为 hook 都是函数，很容易导致闭包问题。特别是 event handler 内部引用的 state 永远是初始化时的 state

可以通过 useRef 模拟 class 实例属性解决这个问题

也可以通过 useReducer 解决这个问题

# 什么时候该用到 Memo 去做优化？

就算重新执行一次 hook 函数，性能很慢吗？和创建一个 memo 性能相比？无意义的 ”shallow compare“ 会更加消耗性能？

等到应用卡了再去做 memo 优化？

memo 的优化是否应该收到框架内部？ vue ？

不要过早优化

## 陷阱

https://zhuanlan.zhihu.com/p/455317250

https://www.zhihu.com/question/378861485/answer/1125724740

## useStateRef

依赖中引入，stateRef，即可，当更新了 s ta te Ref.value 可以触发 rerender

https://juejin.cn/post/6962196899579297828#comment

```less

```

## 需要一个默认可以统计 Hook 执行次数和执行原因的插件

每次 re-render，记录导致 re-render 的 state，hook re-render 的次数

## useMemo 和 useCallback 的正确使用姿势

![](/img/user/programming/font-end/framework/react/react-hook/image-20220831150522415.png)

![](/img/user/programming/font-end/framework/react/react-hook/image-20220831150514614.png)

![](/img/user/programming/font-end/framework/react/react-hook/image-20220831150506533.png)

![](/img/user/programming/font-end/framework/react/react-hook/image-20220831150456967.png)

![](/img/user/programming/font-end/framework/react/react-hook/image-20220831150440788.png)

![](/img/user/programming/font-end/framework/react/react-hook/image-20220831150447748.png)

![](/img/user/programming/font-end/framework/react/react-hook/image-20220831150425562.png)

![](/img/user/programming/font-end/framework/react/react-hook/image-20220831150351378.png)

![](/img/user/programming/font-end/framework/react/react-hook/image-20220831150413807.png)

![](/img/user/programming/font-end/framework/react/react-hook/image-20220831150239928.png)

![](/img/user/programming/font-end/framework/react/react-hook/image-20220831150220434.png)

![](/img/user/programming/font-end/framework/react/react-hook/image-20220831150226581.png)

# 重新思考 React Hook

React Hooks 要求你用**声明式**的方式去写组件。简单粗暴地理解就是，你不应该纠结“引用会不会变，会不会重新触发 render，这个依赖要不要加”，而是应该这样想，只要我声明了我的组件，在什么状态下是什么样子的，那么不论引用变不变，重新 render 1 遍还是 100 遍，都不会出现预期之外的效果。

# Object 类型的 State

是否应该存在 object 类型的 state，shallow compare 是否有意义？

保持 state 都是基础数据类型，这样可以保证 re-render 的准确性

useReducer 究竟解决了什么问题？

# 优秀的 Hook 工具库

三和二类似，自定义 hooks 提供了更加灵活的逻辑复用机制。自定义 hooks 带来了更多的可能性，例如 [swr](https://link.zhihu.com/?target=https%3A//github.com/zeit/swr) 是一个异步数据加载的自定义 hooks，提供了 Fast page navigation、Revalidation on focus、Interval polling、Request deduplication 等特性，这个在 class 的写法下几乎是无法实现的。又如像 [react-use](https://link.zhihu.com/?target=https%3A//github.com/streamich/react-use) 这样的类库，若能适当在业务中用起来，可以显著提升开发效率。

我遇到逻辑需要复用的场景，倾向于用封装成纯函数，异步相关的就是纯函数返回 promise，上下文信息通过参数或高阶函数注入，其实就满足了大部分需要复用的需求，而且无依赖，喜欢这种纯粹的做法

是的，实际项目中使用 hook 来封装复杂逻辑会遇到很多问题，倒不如用纯函数来封装可复用的逻辑，然后 hook 只是充当胶水语言。
