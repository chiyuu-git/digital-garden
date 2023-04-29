---
{"dg-publish":true,"permalink":"/programming/font-end/framework/react/react-advanced/"}
---


6/14

# setState() 的意义

## 不要直接修改 State

+ React 抽象来说，就是一个公式：$UI=f(state)$
+ 我们把最终绘制出来的**UI**当做一个函数**f**运行的结果，**f**就是 React 和我们基于 React 写的代码，而**f**的输入参数就是**state**。
+ 在 React 中，一个组件中要读取当前状态用是访问 `this.state`，但是更新状态却是用 `this.setState()`，不是直接在 `this.state` 上修改，为什么呢？
+ 因为 `state` 说到底只是一个对象，单纯去修改一个对象的值是没有意义的，**去驱动组件的更新**才是有意义的，想想看，如果只是改了 `state` 这个对象，但是没有让 React 组件重新绘制一遍，那有什么用？

    > 你可以尝试在代码中直接修改 this.state 的值，会发现的确能够改变状态，但是却不会引发重新渲染

+ 所以，需要用一个函数去更改状态，这个函数就是 `setState()`，当 setState 被调用时，能驱动组件的更新过程，引发 `render`、`componentDidUpdate` 等一系列函数的调用。

    > 当然，如果使用 Object 的 setter 功能，实际上也可以通过对 this.state 对象的直接修改来实现 setState 一样的功能，但是，如果 React 真的这么设计的话，我敢肯定，那样的 API 设计会更让人晕头转向，因为不管是谁，第一眼也看不出来修改一个 this.state 对象居然会引发重新渲染的副作用

+ 因此：**不要直接修改 `state`，通过 `setState()` 去更改**

## State 的更新是可能异步的

+ 可以通过 `setState()` 可以驱动组件更新，但是我们都知道 DOM 的操作是十分消耗性能的，出于性能的考虑 React 可能会将多个 setState() 调用合并成一个批处理更新操作。通过一次传递更新多个组件。
+ 因此：React 并不会保证 state 的变更会立即生效，**state 的更新可能是异步的，state 的更新可能会被合并**
+ 既然 `setState` 不会立即修改 `state` 的值，那在什么时候修改 `state` 的值呢？这就要说一下 React 的更新生命周期。
+ `setState` 调用引起的 React 的更新生命周期函数 5 个函数：

    - getDerivedStateFromProps
    - shouldComponentUpdate
    - render
    - getSnapshotBeforeUpdate
    - componentDidUpdate

+ 直到 `render` 函数被调用的时候，`state` 才得到更新

    > 因为要将新的 state 渲染到页面中，所以 state 的更新肯定是在 render() 被调用一开始，在虚拟 DOM 生成之前

+ 或者，当 `shouldComponentUpdate` 函数返回 false，这时候更新过程就被中断了，render 函数也不会被调用了，这时候 React 不会放弃掉对 this.state 的更新的，所以虽然不调用 render，依然会更新 this.state。
+ 我们应该将 `setState()` **视为更新组件的请求而不是立即更新组件的命令**

    > setState() 返回的时候，还没有进行更新，mean：界面的更新，已经脱离了 setState 的调用栈，所以 setState() 更像是一个请求

+ 因此：**setState() 是一个请求，一个异步操作，state 的更新可能会被合并**

# Confused

- 因为 setState 并不会立刻修改 this.state 的值，而且会对多个 state 进行合并，所以下面的 code 可能产生很不直观的结果：

    ```js
    function incrementMultiple() {  this.setState({count: this.state.count + 1});  this.setState({count: this.state.count + 1});  this.setState({count: this.state.count + 1});}
    ```

    > 直观上来看，当上面的 incrementMultiple 函数被调用时，组件状态的 count 值被增加了 3 次，每次增加 1，那最后 count 被增加了 3，但是，实际上的结果只给 state 增加了 1。

- 原因并不复杂，就是因为调用 this.setState 时，并没有立即更改 this.state，所以 this.setState 只是在反复设置同一个值而已，上面的 code 等同下面这样：

    ```js
    /*this.state = {  count:1}*/nextState = Object.assign({}, previousState,   {count:this.state.count+1},  {count:this.state.count+1},  {count:this.state.count+1}, )console.log(this.state) // Object {count: 2}
    ```

+ 想要解决这个问题，我们需要进一步了解 setState()

# setState(updater | stateChange[,callback])

`setState()` 将对组件 state 的更改**排入队列**，并通知 React 需要使用更新后的 state 重新渲染此组件及其子组件。这是用于更新用户界面以响应事件处理器和处理服务器数据的主要方式

`setState()` 是异步的，并且在同一周期内会对多个 `setState` 进行队列批处理。

**参数**

+ 我们先来看看 `setState()` 的第二个参数
+ `setState()` 的第二个参数为可选的回调函数，它将在 `setState` 完成合并并重新渲染组件后执行
+ 通常，我们建议使用 `componentDidUpdate()` 来代替此方式。

    > 即使 `shouldComponentUpdate()` 返回 `false`，state 也会更新，同时回调函数也会执行

**描述**

- 除非 `shouldComponentUpdate()` 返回 `false`，否则 `setState()` 将始终执行重新渲染操作。如果可变对象被使用，且无法在 `shouldComponentUpdate()` 中实现条件渲染，那么仅在新旧状态不一时调用 `setState()` 可以避免不必要的重新渲染

## setState(updater[, callback])

参数一可以是带有形式参数的 `updater` 函数：

```js
(state, props) => stateChange
```

`state` 是对应用**变化时**组件状态的引用。当然，它不应直接被修改。你应该使用基于**参数** `state` 和 `props` 构建的新对象来表示变化。例如，假设我们想根据 `props.step` 来增加 state：

```js
this.setState((state, props) => {  return {counter: state.counter + props.step};});
```

updater 函数中接收的 `state` 和 `props` 都保证为最新

updater 的返回值会与 `state` 进行浅合并

## setState(stateChange[, callback])

`setState()` 的第一个参数除了接受函数外，还可以接受对象类型：

```js
this.setState({quantity: 2})
```

造成 confused 的原因正是因为我们第一个参数传递的是一个 `stateChange`，这种情况下后调用的 `setState()` 将覆盖同一周期内先调用 `setState` 的值

```js
nextState = Object.assign({}, previousState,   {count:this.state.count+1},  {count:this.state.count+1},  {count:this.state.count+1}, )
```

**示例**

+ 当 state 是一个**数组**的时候

    ```js
    setState({ 'arrary': [...this.state.array, newItem]})
    ```

+ state 更新过后，必须重新获取 this.state 才可以拿到新值，再函数初始时获得的时不会变的。为了防止无法及时获取到新的 state，应该保留 state 的值，最后再一起 setState

**注意** ：setState() 的工作方式。你传递一个对象，其中包含你要更新的**状态的一部分**。换句话说，传递的对象将具有与组件状态中的键相对应的键，然后 setState() 通过将对象合并到状态来更新或设置状态。因此叫“状态设置（set-State）”。

> 相对的概念：replaceState

## 对比

无论第一个参数是 `updater` 还是 `stateChange`，`setState()` 都是异步的，并且在同一周期内会对多个 `setState` 进行队列批处理。

并且如果**只调用一次**`setState` 或者调用多次 `setState` 设置不同的属性，两者的表现都是一致的

区别在于调用多次 setState 并且依赖 `state` 和 `porps` 来更新**同一个状态**

如果是 `updater`，react 会逐个调用队列里的 `updater`，并且把前一个 `updater`**执行后的结果**传入下一个 `updater` 成为其实参，这样做的结果就是，`updater` 的执行始终是依据与最新的 `state`。

> 因为 updater 的返回值会与 `this.state` 进行浅合并 (this.state 并没有被改变)，所以执行后的结果就是最新的 state，对于第一个执行的 updater 而言，就是组件本来的 state
>
> 这种连续调用 updater 并传递参数的行为，也是合并 setState 操作的一种表现

如果是 `stateChange`，那么就会执行类似下面的操作：

```js
nextState = Object.assign({}, previousState,   {count:this.state.count+1},  {count:this.state.count+1},  {count:this.state.count+1}, )
```

结果就是后调用的 setState()，覆盖了先调用的 setState()，造成了 confused。因此

```jsx
state = {    count:1,  }  handleClick = () => {    this.setState({count:5})    this.setState({count:-1})  }
```

相当于是直接 `setState({count:-1})`，会发生一次 dom diff ，一次渲染？ @@@

值得一提的是，在 `updater` 函数被调用时，`this.state` 并没有被改变，依然，要等到 render 函数被重新执行时（或者 shouldComponentUpdate 函数返回 false 之后）才被改变。

nextState 会传递给 showComponentUpdate() 作为参数

> nextState 不可以使用 this.state 代替，因为 Object.assign() 会影响到第一个参数，所以是一个空的对象，返回值也不可以赋值给 this.state，
>
> previousState 可以使用 this.state 代替

最后一个 updater 的执行结果也会传递给 showComponentUdpate() 作为参数

## setState 异步带来的好处

可以合并短时间内的多次 setState，减少 DOM diff 的次数，减少页面重新渲染的次数

```jsx
state = {  count:1,}handleClick = () => {  this.setState({count:5})  this.setState({count:1})}
```

先赋值为 5，再赋值为 1，由于异步 setState，合并了操作，最后的 state 是没有发生改变的，因此如果是 pureComponent 是不会再次渲染的，但是如果是普通的 Component 还是会再次渲染

每次 setState 会发生多少次的 dom diff 和 render ？？？ @@@

**注意**：dom diff 是在 render 之后进行的，因为 render 实际上返回的只是一个 jsx 对象，react 通过比对两次 render 的结果，再去进行实际的渲染，特别要注意 react 组件的 render 方法和 浏览器绘制的区别

不论 state 有没有改变，render 方法都会执行，一次 dom diff 发现 UI 没有改变 于是 不会有浏览器的渲染

dom diff 也是有成本的 react 会不会做一些条件判断呢？

既然 state 去驱动 dom，不存在 state 没有变化但是 dom 却发生了变化的情况吧，那为什么是 dom diff 而不是 state diff？因为 state 没必要 diff，setState 就默认了 state 的改变？ state 的 diff 的就是 should compumentupdate 和 purecompment 做的优化啊，如果 state 没有改变，render 就不会执行，就不会有 dom diff

## 为什么 setState 不会同步更新组件状态

假设，我们现在有机会来对 React 做一个重大设计调整，把 setState 的功能设定为同步更改 this.state，也就是说，当 setState 函数返回的时候，this.state 已经体现了状态的改变。

那就有两个设计的问题就直接摆在我们面前。

1. setState 更新状态之后要不要触发一次更新过程？
2. 如何去触发更新过程？

对这两个问题，我们有三个选择答案。

1. setState 自动触发**同步**的组件更新过程；
2. setState 自动触发**异步**的组件更新过程；
3. 干脆，setState 根本**不触发**组件更新过程，让开发者显示驱动更新过程。

我们逐个看看各种选择，看他们这些选择是不是行得通，有什么优劣。

**第一个选择：setState 自动触发同步的组件更新过程**

- 每一次 setState 调用都走一圈生命周期，光是想一想也会觉得会带来性能的问题，生命周期函数都是纯函数，性能应该还好，但是 render 函数返回的结果会拿去做 Virtual DOM 比较和更新 DOM 树，这个就比较费时间。
- 目前 React 会将 setState 的效果放在队列中，积攒着一次引发更新过程，为的就是把 Virtual DOM 和 DOM 树操作降到最小，用于提高性能。

**第二个选择：setState 自动触发异步的组件更新过程**

- 这种选择下，setState 返回时，this.state 已经被改变了，但是并没有立即引发更新过程，React 依然将 setState 产生的结果放在队列里，等到时机合适时走更新过程。
- 这样肯定不行啊，如果 setState 把 this.state 改了，那 shouldComponentUpdate 咋办？这个函数一直就假设执行时 this.state 并没有被改变啊。
- 这种选择无疑是行不通的。

**第三个选择：setState 根本不触发组件更新过程**

- 前两个选择都不怎样，那就看这第三个选择，setState 只修改 this.state，并不出发组件更新过程，那我们就需要另外一个函数用来主动触发更新状态，可是……如果真的这样的话，还需要 setState 干吗？
- 你看，setState 既然和组件的更新过程没有关系，那我们直接操作 this.state 好了，对不对？
- 如果你真的喜欢这种方式，其实都不用重新设计 React，现在的 React 就可以这么玩：直接操作 this.state 来同步修改组件状态，让后调用 this.setState，不用任何参数，相当于空放一枪，唯一的目的就是主动触发一次更新状态。

    ```js
     incrementMultiple() {    this.state.count = this.state.count + 1;    this.state.count = this.state.count + 1;    this.state.count = this.state.count + 1;    this.setState();  }
    ```

- 三次都是直接读取 this.state.count，三次都是直接修改 this.state.count，结果正确，每次调用 incrementMultiple 真的能让 this.state 上的 count 值增加 3，而且组件重行绘制。
- 觉得怎么样？觉得这是一个高招，还是一个阴招？
- 反正我看到自己写出来的这招，反应也是：呵呵。

    > **如果用这种方法来写 code，那么 React 也就不够 React 了，算不上 Reactive**。
    >
    > React 何以称为 React：
    >
    > 如果我们通过直接修改 this.state，然后调用一次 setState，就像是改变了 Excel 表格里 A1 的值，然后还要再按一个按钮去改变 A2 的值……看起来怎么样？很不 Reactive。
    >
    > 所以，要我说怎么看上面直接修改 this.state.count 的方法，就是：**咱们都玩上 React 了，就不要再回到解放前了。**

## setState 何时同步更新状态

在 React 中，**如果是由 React 引发的事件处理（比如通过 onClick 引发的事件处理、在生命周期函数中调用 setState），调用 setState 不会同步更新 this.state，除此之外的 setState 调用会同步执行 this.state**。所谓“除此之外”，指的是绕过 React 通过 addEventListener 直接添加的事件处理函数，还有通过 setTimeout/setInterval 产生的异步调用。

在 React 的 setState 函数实现中，会根据一个变量**isBatchingUpdates**判断是直接更新 this.state 还是放到队列中回头再说，而***isBatchingUpdates***默认是 false，也就表示 setState 会同步更新 this.state，但是，有一个函数**batchedUpdates**，这个函数会把***isBatchingUpdates***修改为 true，而当 React 在调用事件处理函数之前就会调用这个**batchedUpdates**，造成的后果，就是由 React 控制的事件处理过程 setState 不会同步更新 this.state。

```jsx
class Detail extends Component {  constructor(props) {    super(props)    this.onClick = this.onClick.bind(this);    this.onClickLater = this.onClickLater.bind(this);    this.state = {      count: 0    };  }  onClick() {    this.setState({ count: this.state.count + 1 })    console.log('# this.state', this.state);  }  onClickLater() {    setTimeout(() => {      this.onClick();    });  }  componentDidMount() {    document.querySelector('#btn-raw').addEventListener('click', this.onClick);  }  render() {    console.log('#enter render');    return (      <div>        <div>{this.state.count}          <button onClick={this.onClick}>Increment</button>          <button id="btn-raw">Increment Raw</button>          <button onClick={this.onClickLater}>Increment Later</button>        </div>      </div>    )  }}
```

点击 Increment，先输出没有更新的 this.state，然后 render 函数被执行，可见 this.state 的更新是异步的，更新过程也是在 setState 执行之后才引发。

![1556672496378](/img/user/programming/font-end/framework/react/react-advanced/1556672496378.png)

但是如果点击 Increment Raw 或者 Increment Later，就是先执行 render 函数，然后输出更新过的 this.state，可见，this.state 被同步更新了，而且在 setState 函数执行过程中，页面进行更新，当 setState 函数返回的时候，页面已经更新完成了，this.state 也已经修改

![1556672483194](/img/user/programming/font-end/framework/react/react-advanced/1556672483194.png)

你还希望 setState 同步更新 this.state 吗？

上面的试验很清楚地显示，同步更新 this.state 的话，每一次调用 setState 都会引发同步的更新过程，这会更新过程很频繁，也就会导致性能问题。

所以说，虽然 React 具有让 setState 同步更新 this.state 的功能，我们还是避免这种使用方式。

别用这招，我们可以了解一种工具，但是并不表示我们就应该使用它。

## 数据是向下流动的

不管是父组件或是子组件都无法知道某个组件是有状态的还是无状态的，并且它们也并不关心它是函数组件还是 class 组件。

这就是为什么称 state 为局部的或是封装的的原因。除了拥有并设置了它的组件，其他组件都无法访问。

组件可以选择把它的 state 作为 props 向下传递到它的子组件中：

```jsx
<FormattedDate date={this.state.date} />
```

`FormattedDate` 组件会在其 props 中接收参数 `date`，但是组件本身无法知道它是来自于 `Clock` 的 state，或是 `Clock` 的 props，还是手动输入的：

```jsx
function FormattedDate(props) {  return <h2>It is {props.date.toLocaleTimeString()}.</h2>;}
```

这通常会被叫做“自上而下”或是**“单向”的数据流**。任何的 state 总是从属于特定的组件，而且从该 state 派生的任何数据或 UI 只能影响树中“低于”它们的组件。

如果你把一个以组件构成的树想象成一个 props 的数据瀑布的话，那么每一个组件的 state 就像是在任意一点上给瀑布增加额外的水源，但是它只能向下流动。

# React 组件动画 #

+ 入场动画，通过 animation 可以配合条件渲染，只要渲染了就会执行一次
+ 那么入场动画怎么用 transition 来做呢？
  + 方案一：从负到正
+ 那么退场动画呢？出场动画必须在组件移除之前
  + 方案一：添加类名，动画结束之后，移除组件
  + 方案二：待补充
+ 如何做到给组件动态添加类名呢？传递 props，组件内部再根据 props 判断即可
+ 在外面包一层 div，通过控制 div 的渲染来控制组件的渲染，通过 props.children 属性在动画组件内渲染出目标组件
+ 添加一个默认样式，包含 transition，然后只要切换样式类即可
+ context 变化，每次组件都是重新生成，导致没有过渡效果

## React Transition Group

参考：https://github.com/Iixianjie/blog/blob/master/2019/4/react-transition-group%E4%B8%AD%E6%96%87%E6%8C%87%E5%8D%97.md#appear

http://reactcommunity.org/react-transition-group/with-react-router

https://zhuanlan.zhihu.com/p/33748302

http://www.cnblogs.com/qq120848369/p/6066837.html

https://github.com/chenglou/react-motion

## 概述

+ react-transition-group 与其他 react 动画框架相比优势还是比较明显的，体积非常小，gzip 之后只有 5k 不到、只提供**动画状态**，具体的动画实现完全由自己掌控，对于业务开发这点是很重要的，在只需要一些基础的页面、组件基础动效时直接用 js 和 css 实现，涉及一些交互效果比较多的页面时可以通过提供的钩子很好的接入 TweenMax 这样的动画库，可以说是非常有弹性了。
+ 'entering' -> 进场动画正在进行
+ 'entered' -> 进场动画执行完成
+ 'exiting' -> 离场动画正在进行
+ 'exited' -> 离场动画完成

## Transtion 组件

+ 对于于基本的转换效果，使用 transtion 已经足够
+ Most commonly it's used to animate the mounting and unmounting of a component, but can also be used to describe in-place transition states as well.

  > **Note**: `Transition` is a platform-agnostic base component. If you're using transitions in CSS, you'll probably want to use [`CSSTransition`](https://reactcommunity.org/react-transition-group/css-transition) instead. It inherits all the features of `Transition`, but contains additional features necessary to play nice with CSS transitions (hence the name of the component)

### 基本示例

+ Hooks ver

  ```jsx
  import React, { useState } from 'react';
  import { Transition } from 'react-transition-group';
  
  const duration = 300;
  
  const defaultStyle = {
    transition: `opacity ${duration}ms ease-in-out`,
    opacity: 0,
  }
  
  const transitionStyles = {
    entering: { opacity: 1 },
    entered: { opacity: 1 },
    exiting: { opacity: 0 },
    exited: { opacity: 0 },
  };
  
  export default function transition() {
    const [show, setShow] = useState(false);
    return (
      <div>
        <Transition in={show} timeout={duration}>
          {status  => ( //state 如 entering\enterd...
            <div 
              // 疑惑：我要怎么把这个style传递给自定义组件呢？
              style={{...defaultStyle,...transitionStyles[state]}}
            >
              I'm a fade Transition!
            </div>
          )}
        </Transition>
        <button onClick={() => setShow(!show)}>
          Click to Enter
        </button>
      </div>
    );
  }
  ```

+ Transition status is toggled via the `in` prop. When `true` the component begins the "Enter" stage. During this stage, the component will shift from its current transition status to `'entering'` for the duration of the transition and then to the `'entered'` status once it's complete.

  > When the button is clicked the component will shift to the `'entering'` status and stay there for 500ms (the value of `timeout`) before it finally switches to `'entered'`.

+ When `in` is `false` the same thing happens except the state moves from `'exiting'` to `'exited'`.
+ Transition 组件通过 `in` 属性的变化触发 status 的改变，children 根据不同的 status ，切换不同的 style
+ Class ver

  ```jsx
  export default class Parent extends React.Component{
    state={
      show:false,
    }
    render(){
      return (
        <div>
          <Transition in={this.state.show} timeout={duration}>
            {status  => (
              <div 
                style={{...defaultStyle,...transitionStyles[state]}}
              >
                I'm a fade Transition!
              </div>
            )}
          </Transition>
          <button onClick={() => this.setState({show:!this.state.show})}>
            Click to Enter
          </button>
        </div>
      );
    }
  }
  ```

### Props

#### Children

+ type: `Function | element`
+ required
+ A `function` child can be used instead of a React element. This function is called with the current transition status (`'entering'`, `'entered'`, `'exiting'`, `'exited'`), which can be used to apply context specific props to a component.
+ 接收一个 function 作为 children，当转换状态发生转换时调用此 function，并将当前状态作为参数传入

  ```jsx
  <Transition in={this.state.in} timeout={150}>
    {status => (
      <MyComponent className={`fade fade-${state}`} />
    )}
  </Transition>
  ```

+ 疑惑：仅仅传递一个 react 元素的话，没有办法切换状态

#### In

+ type: `boolean`
+ default: `false`
+ 触发进入或退出状态

#### mountOnEnter

+ type: `boolean`
+ default: `false`
+ By default the child component is mounted immediately along with the parent `Transition` component. If you want to "lazy mount" the component on the first `in={true}` you can set `mountOnEnter`. After the first enter transition the component will stay mounted, even on "exited", unless you also specify `unmountOnExit`.
+ 默认情况下，子组件与 transtion 组件一起加载

  > 即使 in 属性为 false，组件也会先以隐藏状态 (exited) 正常加载
  >
  > 类似的，当 in 属性切换到 false，子组件仍然是以隐藏状态 (exited) 存在，不会真正的卸载子组件
  >
  > 想要完全卸载子组件需要使用：`unmountOnExit` 属性
  >
  > 推论：默认情况下，切换 `in` 属性，不会触发子组件的 Mount 和 UnMont 生命周期函数

+ 如果你想要实现懒加载，即在 `in` 属性第一次为 `true` 时才加载子组件，可以设置 `mountOnEnter` 为 `true`
+ 当然，如果 `in` 的初始值为 true，那么就会直接显示子组件 (entered)

#### unmountOnExit

+ type: `boolean`
+ default: `false`
+ 在过渡结束后卸载组件, 测试发现这里确实卸载了子组件生成的 dom 节点，但是并不会触发 `componentWillUnmount` 钩子，在子组件重新进入 entered 状态时也不会重新触发 `componentDidMount` 等创建阶段钩子

  > 即使真正的卸载了也不触发生命周期函数？疑惑，源码会告诉你答案

#### Appear

+ type: `boolean`
+ default: `false`
+ Normally a component is not transitioned if it is shown when the `<Transition>` component mounts. If you want to transition on the first mount set `appear` to `true`, and the component will transition in as soon as the `<Transition>` mounts.

  > Note: there are no specific "appear" states. `appear` only adds an additional `enter` transition.

+ 子组件将在 `Transtion` 组件加载后立即执行一次转换
+ apear：出现，在 `Transition` 组件，失效

  ```jsx
  import React, { useState } from 'react';
  import { Transition } from 'react-transition-group';
  
  const duration = 5000;
  
  const defaultStyle = {
    transition: `opacity ${duration}ms ease-in-out`,
    opacity: 0,
  }
  
  const transitionStyles = {
    entering: { opacity: 1 },
    entered: { opacity: 1 },
    exiting: { opacity: 0 },
    exited: { opacity: 0 },
  };
  
  export default function transition() {
    const [show, setShow] = useState(false);
    return (
      <div>
        <Transition in={true} timeout={duration} appear={true}>
          {state => (
            <div 
              style={{...defaultStyle,...transitionStyles[state]}}
            >
              I'm a fade Transition!
            </div>
          )}
        </Transition>
        <button onClick={() => setShow(!show)}>
          Click to Enter
        </button>
      </div>
    );
  }
  ```

#### Enter

- type: `boolean`
- default: `true`
- Enable or disable enter transitions.
- 是否开启进入转换 (关闭后不进入 entering 状态，直接进入 entered 状态)
- 表现为：从 entering 状态到 entered 状态的时间内，样式都是 entered 状态的样式，也即是跳过了 entering 阶段的样式，而不是跳过 entering 阶段
- 注意：当 appear 为 true 时此设置不会生效

  > 即使 appear 是 true，仍然生效了

#### Exit

- type: `boolean`
- default: `true`
- Enable or disable exit transitions.
- 是否开启退出转换，与 enter 相反
- 表现为：从 entered 样式过渡为 exited 样式，而不是跳过了 exiting 阶段

#### Timeout

- type: `number | { enter?: number, exit?: number, appear?: number }`
- The duration of the transition, in milliseconds. Required unless `addEndListener` is provided.
- You may specify a single timeout for all transitions:

  ```jsx
  timeout={500}
  ```

- or individually:

  ```jsx
  timeout={{
   appear: 500,
   enter: 300,
   exit: 500,
  }}
  ```

  - `appear` defaults to the value of `enter`
  - `enter` defaults to `0`
  - `exit` defaults to `0`

#### addEventListner

- type: `Function`
- Add a custom transition end trigger. Called with the transitioning DOM node and a `done` callback. Allows for more fine grained transition end logic.
- **Note:**Timeouts are still used as a fallback if provided.

  ```jsx
  addEndListener={(node, done) => {
    // use the css transitionend event to mark the finish of a transition
    node.addEventListener('transitionend', done, false);
  }}
  ```

- 用于手动触发动画结束状态的事件以允许你更细粒度的控制动画状态，传入改属性时，timeout 属性扔作为后备属性生效。
- ？如何解除事件绑定？通过 ev.target
- done 参数又是什么东西，一个函数，在函数内部会转换 state，以及执行我们传入的 callback，那为什么要显式设置这个参数呢？

  ```jsx
  const finish = (ev) => {
    console.log('done');
    ev.target.removeEventListener('transitionend',finish)
  }
  
  addEndListener={(node, done) => {
    node.addEventListener('transitionend',finish, false)}}
  ```

  ```jsx
  ƒ (event) {
        if (active) {
          active = false;
          _this4.nextCallback = null;
          callback(event);
        }
      }
  ```

- 分别在 enterd 和 exited 会触发一次 transitionend 事件

#### onEnter

- <fn(node, isAppearing)>

进入 entering 状态之前触发的回调, 在第一次 mount 时会传入 isAppearing 判断是否开启 appear 选项

#### onEntering *

- <fn(node, isAppearing)>
- 进入 entering 状态后触发的回调（也就是开始调用 entering 后触发）

#### onEntered

- <fn(node, isAppearing)>
- 进入 entered 状态后触发的回调

#### onExit

- <fn(node)>
- 在 exit 状态前触发的回调

#### onExiting

- <fn(node)>
- 在进入 exiting 状态后触发的回调

#### onExited

- <fn(node)>
- 在进入 exited 状态后触发的回调

## Transition 组件源码解析

+ ```jsx
  import React, { useState } from 'react';
  import Transition from './Transition';
  import ClassInHook from './ClassInHook'
  
  const duration = 1000;
  
  const defaultStyle = {
    transition: `opacity ${duration}ms ease-in-out`,
    opacity: 0,
  }
  
  const transitionStyles = {
    entering: { opacity: 1 ,color:'green'},
    entered: { opacity: 1 ,color:'red'},
    exiting: { opacity: 0 ,color:'grey'},
    exited: { opacity: 0 },
  };
  
  export default function transition() {
    const [show, setShow] = useState(true);
    return (
      <div>
        <Transition in={show} timeout={duration} >
          {state => (
            <div 
              style={{...defaultStyle,...transitionStyles[state]}}
            >
              I'm a fade Transition!
            </div>
          )}
        </Transition>
        <button onClick={() => setShow(!show)}>
          Click to Enter
        </button>
        <ClassInHook show={show}/>
      </div>
    );
  }
  ```
+ Transition 组件运作的机制其实是一个状态机。对于状态机的流程，我们需要掌握好：初始状态，以及状态的转移

## CSSTransition

+ 例: 当 classNames="fade" 时适用于以下类

  ```
  .fade-appear    // 初次进入前的状态, 注意在初次进入时，appear和 enter 相关的类名会在第一次动画时同时生效
  .fade-appear-active    // 初次进入中的状态
  .fade-appear-done    // 初次进入后的状态
  
  .fade-enter            // 开始过渡时生效,元素被插入前
  .fade-enter-active     // 开始过渡时的状态
  .fade-enter-done       // 过渡结束后的状态
  
  .fade-exit             // 离开过渡的开始状态,元素被插入前
  .fade-exit-active      // 离开过渡生效时的状态
  .fade-exit-done        // 离开过渡的结束状态
  ```

## 自定义 CSSTranstion

+ 默认样式，定义初始状态
+ entered 如果没有定义则和 entering 一致即可，exit 类似
+ transitionClasses，如果没有定义则默认是 -entring -enterd -exiting -exited
+ 再 componentDidMount() 调用 children() 获取生成的 DOM 节点，标记为 node，对 node 添加类名，
+ 为了保持封装性 findDomNode() 的作用无可替代

# React Spring

在下面 App.js 文件中，使用了 react-spring 库中的 UseSpring 和 animated，UseSpring 是一个可以设置样式的自定义钩子，它接受一个对象，该对象具有 from 和 to 值作为开始和结束状态，react-spring 正是用这两个状态来处理过渡的动画效果。from 和 to 几乎可以设置所有的 CSS 属性对象：颜色，大小，transform，甚至滚动条。只需要在 HTML 标签上增加 animated 就可以应用 spring 动画。默认情况下，动画会在组件挂载的时候立即执行

从一个值过渡到另外一个值可能有点单调，但 react-spring 可以使用数组来渲染具有多个阶段的动画。只需记住始终将起始状态包含在要添加的任何属性里面即可。

# Context

+ Context 提供了一个无需为每层组件手动添加 props，就能在组件树间进行数据传递的方法。
+ 在一个典型的 React 应用中，数据是通过 props 属性自上而下（由父及子）进行传递的，但这种做法对于某些类型的属性而言是极其繁琐的（例如：地区偏好，UI 主题），这些属性是应用程序中许多组件都需要的。Context 提供了一种在组件之间共享此类值的方式，而不必显式地通过组件树的逐层传递 props。
+ 必读：https://juejin.im/post/5c8918ca6fb9a049f572023e#heading-2
+ https://juejin.im/post/5cee43ad518825526b294a32

## 何时使用 Context

+ Context 设计目的是为了共享那些对于**一个组件树**而言是**“全局”**的数据，例如当前认证的用户、主题或首选语言。举个例子，在下面的代码中，我们通过一个 “theme” 属性手动调整一个按钮组件的样式：

  ```jsx
  class App extends React.Component {
    render() {
      return <Toolbar theme="dark" />;
    }
  }
  
  function Toolbar(props) {
    // Toolbar 组件接受一个额外的“theme”属性，然后传递给 ThemedButton 组件。
    // 如果应用中每一个单独的按钮都需要知道 theme 的值，这会是件很麻烦的事，
    // 因为必须将这个值层层传递所有组件。
    return (
      <div>
        <ThemedButton theme={props.theme} />
      </div>
    );
  }
  
  class ThemedButton extends React.Component {
    render() {
      return <Button theme={this.props.theme} />;
    }
  }
  ```

+ 使用 context, 我们可以避免通过中间元素传递 props：

  ```jsx
  // Context 可以让我们无须明确地传遍每一个组件，就能将值深入传递进组件树。
  // 为当前的 theme 创建一个 context（“light”为默认值）。
  const ThemeContext = React.createContext('light');
  
  class App extends React.Component {
    render() {
      // 使用一个 Provider 来将当前的 theme 传递给以下的组件树。
      // 无论多深，任何组件都能读取这个值。
      // 在这个例子中，我们将 “dark” 作为当前的值传递下去。
      return (
        <ThemeContext.Provider value="dark">
          <Toolbar />
        </ThemeContext.Provider>
      );
    }
  }
  
  // 中间的组件再也不必指明往下传递 theme 了。
  function Toolbar(props) {
    return (
      <div>
        <ThemedButton />
      </div>
    );
  }
  
  class ThemedButton extends React.Component {
    // 指定 contextType 读取当前的 theme context。
    // React 会往上找到最近的 theme Provider，然后使用它的值。
    // 在这个例子中，当前的 theme 值为 “dark”。
    static contextType = ThemeContext;
    render() {
      return <Button theme={this.context} />;
    }
  }
  ```

## 基本使用

### React.createContext()

+ ```jsx
  export const MyContext = React.createContext(defaultValue);
  ```
+ 创建一个 Context 对象。当 React 渲染一个**订阅**了这个 Context 对象的组件，这个组件会从组件树中离自身**最近**的那个匹配的 `Provider` 中读取到当前的 context 值。
+ 只有当组件所处的树中没有匹配到 Provider 时，其 `defaultValue` 参数**才**会生效。这有助于在不使用 Provider 包装组件的情况下对组件进行测试。
+ **注意：**将 `undefined` 传递给 Provider 时，消费组件的 `defaultValue` 不会生效

### Context.Provider

+ ```jsx
  <MyContext.Provider value={/* 某个值 */}>
  ```
+ 每个 Context 对象都会返回一个 **Provider React 组件**，它允许消费组件**订阅 context 的变化**。
+ Provider 接收一个 `value` 属性，传递给消费组件。一个 Provider 可以和多个消费组件有对应关系。多个 Provider 也可以嵌套使用，里层的会覆盖外层的数据。
+ 当 Provider 的 `value` 值发生变化时，它内部的所有消费组件都会**重新渲染**。Provider 及其内部 consumer 组件都不受制于 `shouldComponentUpdate` 函数，因此当 consumer 组件在其祖先组件退出更新的情况下也能更新。
+ 通过新旧值检测来确定变化，使用了与 [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is#Description) 相同的算法。

**注意：**

+ 当传递对象给 `value` 时，检测变化的方式会导致一些问题：详见 [注意事项](https://zh-hans.reactjs.org/docs/context.html#caveats)。

### Class.contextType

+ ```jsx
  import MyContext from './Mycontext'
  class MyClass extends React.Component {
    componentDidMount() {
      let value = this.context;
      /* 在组件挂载完成后，使用 MyContext 组件的值来执行一些有副作用的操作 */
    }
    componentDidUpdate() {
      let value = this.context;
      /* ... */
    }
    componentWillUnmount() {
      let value = this.context;
      /* ... */
    }
    render() {
      let value = this.context;
      /* 基于 MyContext 组件的值进行渲染 */
    }
  }
  MyClass.contextType = MyContext;
  ```
+ **引入创建的 context**
+ 挂载在 class 上的 `contextType` 属性会被重赋值为一个由 [`React.createContext()`](https://zh-hans.reactjs.org/docs/context.html#reactcreatecontext) 创建的 Context 对象。
+ 这能让你使用 `this.context` 来消费最近 Context 上的那个值。你可以在任何生命周期中访问到它，包括 render 函数中。

**注意：**

+ 你只通过该 API 订阅**单一** context。如果你想订阅多个，阅读 [使用多个 Context](https://zh-hans.reactjs.org/docs/context.html#consuming-multiple-contexts) 章节
+ 如果你正在使用实验性的 [public class fields 语法](https://babeljs.io/docs/plugins/transform-class-properties/)，你可以使用 `static` 这个类属性来初始化你的 `contextType`。

  ```jsx
  class MyClass extends React.Component {
    static contextType = MyContext;
    render() {
      let value = this.context;
      /* 基于这个值进行渲染工作 */
    }
  }
  ```

### Context.Consumer

+ ```jsx
  <MyContext.Consumer>
    {value => /* 基于 context 值进行渲染*/}
  </MyContext.Consumer>
  ```
+ 这里，React 组件也可以订阅到 context 变更。这能让你在 [函数式组件](https://zh-hans.reactjs.org/docs/components-and-props.html#function-and-class-components) 中完成订阅 context。
+ 这需要 [函数作为子元素（function as a child）](https://zh-hans.reactjs.org/docs/render-props.html#using-props-other-than-render) 这种做法。这个函数接收当前的 context 值，返回一个 React 节点。传递给函数的 `value` 值等同于往上组件树离这个 context 最近的 Provider 提供的 `value` 值。如果没有对应的 Provider，`value` 参数等同于传递给 `createContext()` 的 `defaultValue`。

## 示例

### 动态 Context

+ 对于上面的 theme 例子，使用动态值（dynamic values）后更复杂的用法：
+ **theme-context.js**

  ```jsx
  export const themes = {
    light: {
      foreground: '#000000',
      background: '#eeeeee',
    },
    dark: {
      foreground: '#ffffff',
      background: '#222222',
    },
  };
  
  export const ThemeContext = React.createContext(
    themes.dark // 默认值
  );
  ```

+ **themed-button.js**

  ```jsx
  import {ThemeContext} from './theme-context';
  
  class ThemedButton extends React.Component {
    render() {
      let props = this.props;
      let theme = this.context;
      return (
        <button
          {...props}
          style={{backgroundColor: theme.background}}
        />
      );
    }
  }
  ThemedButton.contextType = ThemeContext;
  
  export default ThemedButton;
  ```

+ **app.js**

  ```jsx
  import {ThemeContext, themes} from './theme-context';
  import ThemedButton from './themed-button';
  
  // 一个使用 ThemedButton 的中间组件
  function Toolbar(props) {
    return (
      <ThemedButton onClick={props.changeTheme}>
        Change Theme
      </ThemedButton>
    );
  }
  
  class App extends React.Component {
    constructor(props) {
      super(props);
      this.state = {
        theme: themes.light,
      };
  
      this.toggleTheme = () => {
        this.setState(state => ({
          theme:
            state.theme === themes.dark
              ? themes.light
              : themes.dark,
        }));
      };
    }
  
    render() {
      // 在 ThemeProvider 内部的 ThemedButton 按钮组件使用 state 中的 theme 值，
      // 而外部的组件使用默认的 theme 值
      return (
        <Page>
          <ThemeContext.Provider value={this.state.theme}>
            <Toolbar changeTheme={this.toggleTheme} />
          </ThemeContext.Provider>
          <Section>
            <ThemedButton />
          </Section>
        </Page>
      );
    }
  }
  
  ReactDOM.render(<App />, document.root);
  ```

### 在嵌套组件中更新 Context

+ 从一个在组件树中嵌套很深的组件中更新 context 是很有必要的。在这种场景下，你可以通过 context 传递一个函数，使得 consumers 组件更新 context：
+ **theme-context.js**

  ```jsx
  // 确保传递给 createContext 的默认值数据结构是调用的组件（consumers）所能匹配的！
  export const ThemeContext = React.createContext({
    theme: themes.dark,
    toggleTheme: () => {},
  });
  ```

+ **theme-toggler-button.js**

  ```jsx
  import {ThemeContext} from './theme-context';
  
  function ThemeTogglerButton() {
    // Theme Toggler 按钮不仅仅只获取 theme 值，它也从 context 中获取到一个 toggleTheme 函数
    return (
      <ThemeContext.Consumer>
        {({theme, toggleTheme}) => (
          <button
            onClick={toggleTheme}
            style={{backgroundColor: theme.background}}>
  
            Toggle Theme
          </button>
        )}
      </ThemeContext.Consumer>
    );
  }
  
  export default ThemeTogglerButton;
  ```

+ **app.js**

  ```jsx
  import {ThemeContext, themes} from './theme-context';
  import ThemeTogglerButton from './theme-toggler-button';
  
  class App extends React.Component {
    constructor(props) {
      super(props);
  
      this.toggleTheme = () => {
        this.setState(state => ({
          theme:
            state.theme === themes.dark
              ? themes.light
              : themes.dark,
        }));
      };
  
      // State 也包含了更新函数，因此它会被传递进 context provider。
      this.state = {
        theme: themes.light,
        toggleTheme: this.toggleTheme,
      };
    }
  
    render() {
      // 整个 state 都被传递进 provider
      return (
        <ThemeContext.Provider value={this.state}>
          <Content />
        </ThemeContext.Provider>
      );
    }
  }
  
  function Content() {
    return (
      <div>
        <ThemeTogglerButton />
      </div>
    );
  }
  
  ReactDOM.render(<App />, document.root);
  ```

### 消费多个 Context

+ 为了确保 context 快速进行重渲染，React 需要使每一个 consumers 组件的 context 在组件树中成为一个单独的节点。

  ```jsx
  // Theme context，默认的 theme 是 “light” 值
  const ThemeContext = React.createContext('light');
  
  // 用户登录 context
  const UserContext = React.createContext({
    name: 'Guest',
  });
  
  class App extends React.Component {
    render() {
      const {signedInUser, theme} = this.props;
  
      // 提供初始 context 值的 App 组件
      return (
        <ThemeContext.Provider value={theme}>
          <UserContext.Provider value={signedInUser}>
            <Layout />
          </UserContext.Provider>
        </ThemeContext.Provider>
      );
    }
  }
  
  function Layout() {
    return (
      <div>
        <Sidebar />
        <Content />
      </div>
    );
  }
  
  // 一个组件可能会消费多个 context
  function Content() {
    return (
      <ThemeContext.Consumer>
        {theme => (
          <UserContext.Consumer>
            {user => (
              <ProfilePage user={user} theme={theme} />
            )}
          </UserContext.Consumer>
        )}
      </ThemeContext.Consumer>
    );
  }
  ```

+ 如果两个或者更多的 context 值经常被一起使用，那你可能要考虑一下另外创建你自己的渲染组件，以提供这些值。

## 注意事项

+ 因为 context 会使用参考标识（reference identity）来决定**何时进行渲染**，这里可能会有一些陷阱，当 provider 的父组件进行重渲染时，可能会在 consumers 组件中触发意外的渲染。
+ 举个例子，当每一次 Provider 重渲染时，以下的代码会重渲染所有下面的 consumers 组件，因为 `value` 属性总是被赋值为新的对象：

  ```jsx
  class App extends React.Component {
    render() {
      return (
        <Provider value={{something: 'something'}}>
          <Toolbar />
        </Provider>
      );
    }
  }
  ```

+ 为了防止这种情况，将 value 状态提升到父节点的 state 里：

  ```jsx
  class App extends React.Component {
    constructor(props) {
      super(props);
      this.state = {
        value: {something: 'something'},
      };
    }
  
    render() {
      return (
        <Provider value={this.state.value}>
          <Toolbar />
        </Provider>
      );
    }
  }
  ```

+ 使用 redux 把所有的状态都集中到一个 container，然后通过 context 传递下去，这样就可以在一个组件内看到所有数据的流动，这样做好不好呢？

### 在 Hooks 中使用

+ 因为 class 是在类的外部定义的 context 所以不存在问题
+ 但是如果在函数组件内部定义 context，那么无论 props 是否发生变化，都会导致函数组件的重新渲染

  ```jsx
  export let ChildProps = null
  
  const Cascader = (props) => {
    const [value,setValue] = useState("")
    ChildProps = React.createContext({value})
  }
  ```

+ 所以默认值为 null 是最好的，这样 context 就可以提升到函数的外部

## 使用 Context 之前的考虑

- Context 主要应用场景在于*很多*不同层级的组件需要访问同样一些的数据。请谨慎使用，因为这会使得组件的复用性变差。
- **如果你只是想避免层层传递一些属性，组件组合（component composition）有时候是一个比 context 更好的解决方案。**

### **示例**

- 比如，考虑这样一个 `Page` 组件，它层层向下传递 `user` 和 `avatarSize` 属性，从而深度嵌套的 `Link` 和 `Avatar` 组件可以读取到这些属性：

  ```jsx
  <Page user={user} avatarSize={avatarSize} />
  // ... 渲染出 ...
  <PageLayout user={user} avatarSize={avatarSize} />
  // ... 渲染出 ...
  <NavigationBar user={user} avatarSize={avatarSize} />
  // ... 渲染出 ...
  <Link href={user.permalink}>
    <Avatar user={user} size={avatarSize} />
  </Link>
  ```

- 如果在最后只有 `Avatar` 组件真的需要 `user` 和 `avatarSize`，那么层层传递这两个 props 就显得非常冗余。而且一旦 `Avatar` 组件需要更多从来自顶层组件的 props，你还得在中间层级一个一个加上去，这将会变得非常麻烦。

### 方案一

- 一种**无需 context** 的解决方案是 [将 `Avatar` 组件自身传递下去](https://zh-hans.reactjs.org/docs/composition-vs-inheritance.html#containment)，因而中间组件无需知道 `user` 或者 `avatarSize` 等 props：

  ```jsx
  function Page(props) {
    const user = props.user;
    const userLink = (
      <Link href={user.permalink}>
        <Avatar user={user} size={props.avatarSize} />
      </Link>
    );
    return <PageLayout userLink={userLink} />;
  }
  
  // 现在，我们有这样的组件：
  <Page user={user} avatarSize={avatarSize} />
  // ... 渲染出 ...
  <PageLayout userLink={...} />
  // ... 渲染出 ...
  <NavigationBar userLink={...} />
  // ... 渲染出 ...
  {props.userLink}
  ```

- 这种变化下，只有最顶部的 Page 组件需要知道 `Link` 和 `Avatar` 组件是如何使用 `user` 和 `avatarSize` 的。
- 这种对组件的*控制反转*减少了在你的应用中要传递的 props 数量，这在很多场景下会使得你的代码更加干净，使你对根组件有更多的把控。
- **但是**，这并不适用于每一个场景：这种将逻辑提升到组件树的更高层次来处理，会使得这些高层组件变得更复杂，并且会强行将低层组件适应这样的形式，这可能不会是你想要的。
- 而且你的组件并不限制于接收单个子组件。你可能会传递多个子组件，甚至会为这些子组件（children）封装多个单独的“接口（slots）”，[正如这里的文档所列举的](https://zh-hans.reactjs.org/docs/composition-vs-inheritance.html#containment)

  ```jsx
  function Page(props) {
    const user = props.user;
    const content = <Feed user={user} />;
    const topBar = (
      <NavigationBar>
        <Link href={user.permalink}>
          <Avatar user={user} size={props.avatarSize} />
        </Link>
      </NavigationBar>
    );
    return (
      <PageLayout
        topBar={topBar}
        content={content}
        />
    );
  }
  ```

- 这种模式足够覆盖很多场景了，在这些场景下你需要将子组件和直接关联的父组件解耦。如果子组件需要在渲染前和父组件进行一些交流，你可以进一步使用 [render props](https://zh-hans.reactjs.org/docs/render-props.html)。

### 方案二

- 但是，有的时候在组件树中很多不同层级的组件需要访问同样的一批数据。Context 能让你将这些数据向组件树下所有的组件进行“广播”，所有的组件都能访问到这些数据，也能访问到后续的数据更新。使用 context 的通用的场景包括管理当前的 locale，theme，或者一些缓存数据，这比替代方案要简单的多。

# Fragments

+ React 中的一个常见模式是一个组件返回多个元素。Fragments 允许你将子列表分组，而无需向 DOM 添加额外节点。

## 动机

+ 一种常见模式是组件返回一个子元素列表。以此 React 代码片段为例：

  ```jsx
  class Table extends React.Component {
    render() {
      return (
        <table>
          <tr>
            <Columns />
          </tr>
        </table>
      );
    }
  }
  ```

+ `<Columns />` 需要返回多个 `<td>` 元素以使渲染的 HTML 有效。如果在 `<Columns />` 的 `render()` 中使用了父 div，则生成的 HTML 将无效。

  ```jsx
  class Columns extends React.Component {
    render() {
      return (
        <div>
          <td>Hello</td>
          <td>World</td>
        </div>
      );
    }
  }
  ```

+ 会得到得到一个**错误的** `<Table />` 输出：

  ```jsx
  <table>
    <tr>
      <div>
        <td>Hello</td>
        <td>World</td>
      </div>
    </tr>
  </table>
  ```

+ Fragments 解决了这个问题。

## 基本使用

+ ```jsx
  class Columns extends React.Component {
    render() {
      return (
        <React.Fragment>
          <td>Hello</td>
          <td>World</td>
        </React.Fragment>
      );
    }
  }
  ```
+ 这样可以正确的输出 `<Table />`：

  ```jsx
  <table>
    <tr>
      <td>Hello</td>
      <td>World</td>
    </tr>
  </table>
  ```

### 短语法

+ 你可以使用一种新的，且更简短的语法来声明 Fragments。它看起来像空标签：

  ```jsx
  class Columns extends React.Component {
    render() {
      return (
        <>
          <td>Hello</td>
          <td>World</td>
        </>
      );
    }
  }
  ```

+ 你可以像使用任何其他元素一样使用 `<> </>`，除了它不支持 key 或属性。
+ 请注意 **目前许多工具尚不支持** ，所以直到工具支持之前，你可能要显式的使用 `<React.Fragment>`。

### 带 Key 的 Fragments

+ 使用显式 `<React.Fragment>` 语法声明的片段可能具有 key。一个使用场景是将一个集合映射到一个 Fragments 数组 - 举个例子，创建一个描述列表：

  ```jsx
  function Glossary(props) {
    return (
      <dl>
        {props.items.map(item => (
          // 没有`key`，React 会发出一个关键警告
          <React.Fragment key={item.id}>
            <dt>{item.term}</dt>
            <dd>{item.description}</dd>
          </React.Fragment>
        ))}
      </dl>
    );
  }
  ```

+ `key` 是**唯一**可以传递给 `Fragment` 的属性。未来我们可能会添加对其他属性的支持，例如**事件**。

# Higher-Order Components

参考：http://react.html.cn/docs/higher-order-components.html

+ 高阶组件是为了更好的**复用组件逻辑**，更准确的说：**JS 逻辑**
+ 高阶组件不是 React API 的一部分，只是一种模式，这种模式是由 react 自身的组合性质必然产生的。
+ 具体来说：高阶组件是一个**函数**，它接受一个组件作为参数，然后返回一个新的组件

  ```jsx
  const EnhancedComponent = higherOrderComponent(WrappedComponent);
  ```

  > enhanced 增强的 wrapped 被包裹的

+ 相较于普通组件，把 props 转换成 UI ，高阶组件是把一个组件转换成另一个组件
+ **高阶组件是用于转换的函数，增强组件是转换之后的结果**
+ HOCs are common in third-party React libraries, such as Redux’s [`connect`](https://github.com/reduxjs/react-redux/blob/master/docs/api/connect.md#connect) and Relay’s [`createFragmentContainer`](http://facebook.github.io/relay/docs/en/fragment-container.html).

## 使用高阶组件（HOC）解决横切关注点

> **注意**
>
> 我们曾经介绍了混入（mixins）技术来解决横切关注点。现在我们意识到混入（mixins）技术产生的问题要比带来的价值大。[更多资料](https://react.docschina.org/blog/2016/07/13/mixins-considered-harmful.html) 介绍了为什么我们要移除混入（mixins）技术以及如何转换你已经使用了混入（mixins）技术的组件。

+ 在 React 中，组件是代码复用的主要单元。然而你会发现，一些模式用传统的组件并不直白。
+ 例如，假设你有一个 `CommentList` 组件，该组件从外部数据源订阅数据并渲染评论列表：

  ```jsx
  class CommentList extends React.Component {
    constructor(props) {
      super(props);
      this.handleChange = this.handleChange.bind(this);
      this.state = {
        // "DataSource" is some global data source
        comments: DataSource.getComments()
      };
    }
  
    componentDidMount() {
      // Subscribe to changes
      DataSource.addChangeListener(this.handleChange);
    }
  
    componentWillUnmount() {
      // Clean up listener
      DataSource.removeChangeListener(this.handleChange);
    }
  
    handleChange() {
      // Update component state whenever the data source changes
      this.setState({
        comments: DataSource.getComments()
      });
    }
  
    render() {
      return (
        <div>
          {this.state.comments.map((comment) => (
            <Comment comment={comment} key={comment.id} />
          ))}
        </div>
      );
    }
  }
  ```

+ 然后，你又写了一个订阅单个博客文章的组件，该组件遵循类似的模式：

  ```jsx
  class BlogPost extends React.Component {
    constructor(props) {
      super(props);
      this.handleChange = this.handleChange.bind(this);
      this.state = {
        blogPost: DataSource.getBlogPost(props.id)
      };
    }
  
    componentDidMount() {
      DataSource.addChangeListener(this.handleChange);
    }
  
    componentWillUnmount() {
      DataSource.removeChangeListener(this.handleChange);
    }
  
    handleChange() {
      this.setState({
        blogPost: DataSource.getBlogPost(this.props.id)
      });
    }
  
    render() {
      return <TextBlock text={this.state.blogPost} />;
    }
  }
  ```

+ `CommentList` 和 `BlogPost` 组件并不相同——他们调用 `DataSource` 的方法不同，并且他们渲染的输出也不相同。但是，他们有很多实现是相同的：
  + 挂载组件时， 向 `DataSource` 添加一个改变监听器。
  + 在监听器内， 每当数据源发生改变时，调用 `setState`。
  + 卸载组件时， 移除改变监听器。
+ 设想一下，在一个大型的应用中，这种从 `DataSource` 订阅数据并调用 `setState` 的模式将会一次又一次的发生。我们希望一个抽象允许我们定义这种逻辑，在单个地方，并且许多组件都可以共享它，这就是高阶组件的杰出所在。
+ 我们可以写一个创建组件的函数，创建的组件类似 `CommonList` 和 `BlogPost` 一样订阅到 `DataSource`。该函数接受它的参数之一作为一个子组件，子组件又接受订阅的数据作为一个属性 (prop)。让我们称这个函数为 `withSubscription`：

  ```jsx
  const CommentListWithSubscription = withSubscription(
    CommentList,
    (DataSource) => DataSource.getComments()
  );
  
  const BlogPostWithSubscription = withSubscription(
    BlogPost,
    (DataSource, props) => DataSource.getBlogPost(props.id)
  );
  ```

+ 第一个参数是被包裹的组件，第二个参数是一个函数，根据给定的 `DataSource` 和当前 props 属性中，返回所需要的数据
+ 当渲染 `CommentListWithSubscription` 和 `BlogPostWithSubscription` 时, 会向 `CommentList` 和 `BlogPost` 传递一个 `data` 属性，该 `data` 属性带有从 `DataSource` 检索的最新数据：

  ```jsx
  // 接受一个组件作为参数
  function withSubscription(WrappedComponent, selectData) {
    // 返回一个新的组件
    return class extends React.Component {
      constructor(props) {
        super(props);
        this.handleChange = this.handleChange.bind(this);
        this.state = {
          data: selectData(DataSource, props)
        };
      }
  
      componentDidMount() {
        // ... that takes care of the subscription...
        DataSource.addChangeListener(this.handleChange);
      }
  
      componentWillUnmount() {
        DataSource.removeChangeListener(this.handleChange);
      }
  
      handleChange() {
        this.setState({
          data: selectData(DataSource, this.props)
        });
      }
  
      render() {
        // ... and renders the wrapped component with the fresh data!
        // Notice that we pass through any additional props
        return <WrappedComponent data={this.state.data} {...this.props} />;
      }
    };
  }
  ```

+ **注意：**高阶组件既不会修改输入组件，也不会使用继承拷贝它的行为。高阶组件是一个没有副作用的纯函数。
+ 高阶组件通过用一个容器组件 *包裹着（wrapping）* 原始组件。
+ 被包裹的组件接收容器的所有 props 属性以及一个新属性 `data` 用于渲染输出。高阶组件并不关心数据使用的方式和原因，而被包裹的组件也不关心数据来自何处。
+ 因为 `withSubscription` 就是一个普通函数，你可以按需添加可多可少的参数。例如，你或许会想使 `data` 属性的名字是可配置的，以进一步从被包裹的组件隔离高阶组件。或者你想要接收一个参数用于配置 `shouldComponentUpdate`，或配置数据源。所有的这些都是可以的，因为高阶组件充分地控制新组件定义的方式。
+ 和普通组件一样，`withSubscription` 和被包裹的组件之间的合约是完全基于 props 属性的。这使得易于替换一个高阶组件到另一个，只要他们提供相同的 props 属性给被包裹的组件即可。举例来说，这可以用于你改变获取数据的库时

## 不要改变原始组件，使用组合

+ 抵制诱惑，不要在高阶组件内修改一个组件的原型（或以其它方式修改组件）。

  ```jsx
  function logProps(InputComponent) {
    InputComponent.prototype.componentWillReceiveProps = function(nextProps) {
      console.log('Current props: ', this.props);
      console.log('Next props: ', nextProps);
    };
    // The fact that we're returning the original input is a hint that it has
    // been mutated.
    return InputComponent;
  }
  
  // EnhancedComponent will log whenever props are received
  const EnhancedComponent = logProps(InputComponent);
  ```

+ 上面的示例有一些问题。首先就是，输入组件不能独立于增强型组件（enhanced component）被重用。更致命的是，如果你在 `EnhancedComponent` 上应用另一个高阶组件，同样也去改变 `componentWillReceiveProps`，第一个高阶组件的功能就会被覆盖。这样的高阶组件对没有生命周期方法的函数式组件也是无效的。
+ 修改高阶组件泄露了组件的抽象性——使用者必须知道他们的实现方式，才能避免与其它高阶组件的冲突。
+ 与修改组件相反，高阶组件应该使用组合技术，将输入组件包裹到一个容器组件中：

  ```jsx
  function logProps(WrappedComponent) {
    return class extends React.Component {
      static getDerivedStateFromProps(nextProps,prevState) {
        console.log('Next props: ', nextProps);
      }
      render() {
        // 用容器包裹输入组件，不要修改它，漂亮！
        return <WrappedComponent {...this.props} />;
      }
    }
  }
  ```

+ 这个高阶组件和那个更改型版本有着同样的功能，但却避免了潜在的冲突。它对类组件和函数式组件适用性同样好。而且，因为它是纯函数，它是可组合的，可以和其它高阶组件，甚至和它自身组合。

**redux 相关**

+ 你可能发现了高阶组件和**容器组件**模式的相似之处。容器组件是专注于在高层和低层关注之间进行责任分离的策略的一部分。容器管理的事情诸如订阅和状态，传递 props 属性给某些组件。这些组件处理渲染 UI 等事情。高阶组件使用容器作为他们实现的一部分。你也可以认为高阶组件就是参数化的容器组件定义。

## 约定：只给被包裹组件传递相关的属性 (Props)

+ 高阶组件可以向组件添加功能。他不应该大幅度地改变功能。期望地是高阶组件返回的组件和被包裹组件具有相似的界面。
+ 高阶组件应该通过 props 传递那些与**高阶函数自身的特定功能无**关的特性。大多数的高阶组件包含如下的 render 函数:

  ```jsx
  render() {
    // 过滤掉与高阶函数功能相关的props属性，
    // 不再传递
    const { extraProp, ...passThroughProps } = this.props;
  
    // 向包裹组件注入props属性，一般都是高阶组件的state状态
    // 或实例方法
    const injectedProp = someStateOrInstanceMethod;
  
    // 向包裹组件传递props属性
    return (
      <WrappedComponent
        injectedProp={injectedProp}
        {...passThroughProps}
      />
    );
  }
  ```

+ 这个约定帮助确定高阶组件能够足够灵活和可以被重用。

## 约定: 最大化组合 (Maximizing Composability)

+ 不是所有的高阶组件看起来都是一样的。有时候，它接受包裹组件作为单一参数：

  ```jsx
  const NavbarWithRouter = withRouter(Navbar);
  ```

+ 通常情况下，高阶组件接受其他的参数。在 Relay 这个例子中，配置对象用来指定组件的数据依赖关系：

  ```jsx
  const CommentWithRelay = Relay.createContainer(Comment, config);
  ```

+ 高阶组件最常见的签名如下：

  ```jsx
  // React Redux's `connect`
  const ConnectedComment = connect(commentSelector, commentActions)(CommentList);
  ```

+ **什么?!**，如果你把它分开，就更容易看到发生了什么。

  ```jsx
  // connect是一个返回函数的函数（译者注：就是个高阶函数）
  const enhance = connect(commentListSelector, commentListActions);
  // 返回的函数就是一个高阶组件，该高阶组件返回一个与Redux store
  // 关联起来的新组件
  const ConnectedComment = enhance(CommentList);
  ```

+ 总的来说，`connect` 是一个返回高阶组件的高阶函数！
+ 这种形式看起来是混乱的或者是没有必要的，但是它是一个有用的属性。单参数的高阶组件类似于 `connect` 函数所返回的函数，其签名为 `Component => Component`。返回的函数的输出类型和输入类型是相同的，很容易相互组合。

  ```jsx
  // 不要这样做……
  const EnhancedComponent = connect(commentSelector)(withRouter(WrappedComponent))
  
  // ……你可以使用一个功能组合工具
  // compose(f, g, h) 和 (...args) => f(g(h(...args)))是一样的
  const enhance = compose(
    // 这些都是单参数的高阶组件
    connect(commentSelector),
    withRouter
  )
  const EnhancedComponent = enhance(WrappedComponent)
  ```

+ (这个相同的属性还允许 `connect` 和其他增强型高阶属性作为装饰器 (decorators)，这是一个实验性的 JavaScript 提案)。
+ 许多第三方库提供 `compose` 实用功能，包括 lodash (例如 [lodash.flowRight](https://lodash.com/docs/4.17.4#flowRight))、[Redux](http://redux.js.org/docs/api/compose.html) 和 [Ramda](http://ramdajs.com/docs/#compose)。

## 约定: 为了方便调试包装显示名称 (display name)

+ 由高阶属性创建的容器组件在 [React Developer Tools](https://github.com/facebook/react-devtools) 中显示同其他的组件相似。为了方便调试，选择一个显示名称 (display name)，表示它是高阶组件的结果。
+ 最常见的方法是给**被包裹元素**包裹一个显示名称 (display name)。因此，如果你的高阶组件名字为 `withSubscription`，被包裹的元素名称为 `CommentList`，那就选择名称为 `WithSubscription(CommentList)`。

  ```jsx
  function withSubscription(WrappedComponent) {
    class WithSubscription extends React.Component {/* ... */}
    WithSubscription.displayName =         `WithSubscription(${getDisplayName(WrappedComponent)})`;
    return WithSubscription;
  }
  
  function getDisplayName(WrappedComponent) {
    return WrappedComponent.displayName || WrappedComponent.name || 'Component';
  }
  ```

## 警告

+ 高阶组件有以下几个警告，如果你是刚接触 React，这些警告可能不会立刻就被发现。

### 不要在 Render 函数中使用高阶组件

+ React 的 diff 算法 (又称为 reconciliation) 使用组件标识符 (component identity) 来决定是否应该更新已有的子树或者将其抛出并安装一个新的子树。如果从 `render` 返回的组件等同于 (`\===`) 之前 render 函数返回的组件，React 将会迭代地通过 diff 算法更新子树到新的子树。如果不相等，则先前的子树将会完全卸载。
+ 通常情况下，你不需要考虑这些。但是这对高阶组件非常重要，因为这意味你在组件的 render 方法中不能通过高阶组件产生组件:

  ```jsx
  render() {
    // 每一次render函数调用都会创建一个新的EnhancedComponent实例
    // EnhancedComponent1 !== EnhancedComponent2
    const EnhancedComponent = enhance(MyComponent);
    // 每一次都会使子对象树完全被卸载或移除
    return <EnhancedComponent />;
  }
  ```

+ 这个问题不仅仅关乎于性能，卸载组件会造成组件状态和其子元素全部丢失。
+ 相反地，在组件定义外应用高阶组件，以便生成的组件只会被创建一次。然后，它的标识符在每次渲染中都是相同的。无论如何，这才是你想要的。
+ 在一些极少的例子中你需要动态地引用高阶组件，你可以在组件的声明周期函数中使用或者在构造函数中使用。

### 静态方法必须复制

+ 有时候，在 React 组价中定义静态方法是非常有用的。例如，Relay 容器对外暴露一个静态方法 `getFragment`，来帮助组合 GraphQL 代码。
+ 当你将一个组件应用于高阶组件式，虽然原有的组件被容器组件所包裹，但这以为这新的组件没有之前组件的静态函数。

  ```jsx
  // 定义静态方法
  WrappedComponent.staticMethod = function() {/*...*/}
  // 使用高阶组件
  const EnhancedComponent = enhance(WrappedComponent);
  
  // 增强型组件没有静态方法
  typeof EnhancedComponent.staticMethod === 'undefined' // true
  ```

+ 为了解决这个问题，在返回之前，可以向容器组件中复制原有的静态方法：

  ```jsx
  function enhance(WrappedComponent) {
    class Enhance extends React.Component {/*...*/}
    // 必须得知道要拷贝的方法 :(
    Enhance.staticMethod = WrappedComponent.staticMethod;
    return Enhance;
  }
  ```

+ 然而，这需要你明确地知道哪些方法需要别复制。你可以使用 [hoist-non-react-statics](https://github.com/mridgway/hoist-non-react-statics) 来自动复制非 React 的静态方法。

  ```jsx
  import hoistNonReactStatic from 'hoist-non-react-statics';
  function enhance(WrappedComponent) {
    class Enhance extends React.Component {/*...*/}
    hoistNonReactStatic(Enhance, WrappedComponent);
    return Enhance;
  }
  ```

+ 另一个有效的方法是将静态方法与组件本身相分离：

  ```jsx
  // 替代……
  MyComponent.someFunction = someFunction;
  export default MyComponent;
  
  // ……分别导出……
  export { someFunction };
  
  // ……在要使用的组件中导入
  import MyComponent, { someFunction } from './MyComponent.js';
  ```

### Refs 不会被传递

+ 虽然高阶组件的惯例是将所有 属性 (props) 传递给包裹的组件，但是对 refs 不起作用。 那是因为 `ref` 不是一个真正的属性 (props) 。React 对它进行了特殊处理。 如果你向一个由高阶组件创建的组件的元素添加 ref 应用，那么 ref 指向的是最外层容器组件实例的，而不是包裹组件。
+ 解决这个问题的方法是使用 `React.forwardRef` API（在 React 16.3 中引入）。 [在 refs 转发章节了解更多信息](http://react.html.cn/docs/forwarding-refs.html)。

# Render Props

## 概述

+ 术语 [“render prop”](https://cdb.reacttraining.com/use-a-render-prop-50de598f11ce) 是指一种在 React 组件之间使用一个**值为函数的 prop 共享代码**的简单技术
+ 具有 render prop 的组件接受一个函数并调用它而不是实现自己的渲染逻辑。该函数返回一个 React 元素

  ```jsx
  <DataProvider render={data => (
    <h1>Hello {data.target}</h1>
  )}/>
  ```

+ 使用 render prop 的库有 [React Router](https://reacttraining.com/react-router/web/api/Route/render-func) 和 [Downshift](https://github.com/paypal/downshift).
+ 在这个文档中，我们将讨论为什么 render prop 是有用的，以及如何写一个自己的 render prop 组件。

## 使用 Render Props 解决来横切关注点（Cross-Cutting Concerns）

+ 组件是 React 代码复用的主要单元，但如何分享一个组件的**状态或行为**到其他由相同需求的组件并不总是很容易
+ 例如，以下组件跟踪 Web 应用程序中的鼠标位置：

  ```jsx
  class MouseTracker extends React.Component {
    constructor(props) {
      super(props);
      this.handleMouseMove = this.handleMouseMove.bind(this);
      this.state = { x: 0, y: 0 };
    }
  
    handleMouseMove(event) {
      this.setState({
        x: event.clientX,
        y: event.clientY
      });
    }
  
    render() {
      return (
        <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
          <h1>移动鼠标!</h1>
          <p>当前的鼠标位置是 ({this.state.x}, {this.state.y})</p>
        </div>
      );
    }
  }
  ```

+ 现在的问题是：我们如何在另一个组件中复用这个行为？换个说法，若另一个组件需要知道鼠标位置，我们能否封装这一行为，以便轻松地与其他组件共享它？？
+ 由于组件是 React 中最基础的代码复用单元，现在尝试重构一部分代码使其能够在 `<Mouse>` 组件中封装我们需要共享的行为

  ```jsx
  // <Mouse> 组件封装了我们需要的行为...
  class Mouse extends React.Component {
    constructor(props) {
      super(props);
      this.handleMouseMove = this.handleMouseMove.bind(this);
      this.state = { x: 0, y: 0 };
    }
  
    handleMouseMove(event) {
      this.setState({
        x: event.clientX,
        y: event.clientY
      });
    }
  
    render() {
      return (
        <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
  
          {/* ...但我们如何渲染 <p> 以外的东西? */}
          <p>The current mouse position is ({this.state.x}, {this.state.y})</p>
        </div>
      );
    }
  }
  
  class MouseTracker extends React.Component {
    render() {
      return (
        <div>
          <h1>移动鼠标!</h1>
          <Mouse />
        </div>
      );
    }
  }
  ```

+ 现在 <Mouse> 组件封装了所有关于监听 mousemove 事件和存储鼠标 (x, y) 位置的行为，但其仍不是真正的可复用。
+ 举个例子，假设我们有一个 组件，它可以呈现一张在屏幕上追逐鼠标的猫的图片。我们或许会使用 `<Cat mouse={{ x, y }} />` 来告诉组件鼠标的坐标以让它知道图片应该在屏幕哪个位置。
+ 首先, 你或许会像这样，尝试在 `<Mouse>` 内部的渲染方法渲染 `<Cat>` 组件：:

  ```jsx
  class MouseTracker extends React.Component {
    render() {
      return (
        <div>
          <h1>移动鼠标!</h1>
          <MouseWithCat />
        </div>
      );
    }
  }
  
  class MouseWithCat extends React.Component {
    constructor(props) {
      super(props);
      this.handleMouseMove = this.handleMouseMove.bind(this);
      this.state = { x: 0, y: 0 };
    }
  
    handleMouseMove(event) {
      this.setState({
        x: event.clientX,
        y: event.clientY
      });
    }
  
    render() {
      return (
        <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
  
          {/*
            我们可以在这里换掉 <p> 的 <Cat>   ......
            但是接着我们需要创建一个单独的 <MouseWithSomethingElse>
            每次我们需要使用它时，<MouseWithCat> 不是真的可以重复使用.
          */}
          <Cat mouse={this.state} />
        </div>
      );
    }
  }
  
  class Cat extends React.Component {
    render() {
      const mouse = this.props.mouse;
      return (
        <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: mouse.y }} />
      );
    }
  }
  ```

+ 这种方法适用于我们的特定用例，但我们还没有达到以可复用的方式真正封装行为的目标。现在，每当我们想要**鼠标位置用于不同的用例时**，我们必须创建一个新的组件（本质上是另一个 `<MouseWithCat>` ），它专门为该用例呈现一些东西.
+ 这也是 render prop 的来历：我们可以提供一个带有函数 prop 的 `<Mouse>` 组件，它能够动态决定什么需要渲染的，而不是将 `<Cat>` 硬编码到 `<Mouse>` 组件里，并有效地改变它的渲染结果。

  ```jsx
  class MouseTracker extends React.Component {
    render() {
      return (
        <div>
          <h1>移动鼠标!</h1>
          <Mouse render={mouse => (
            <Cat mouse={mouse} />
          )}/>
        </div>
      );
    }
  }
  
  class Mouse extends React.Component {
    constructor(props) {
      super(props);
      this.handleMouseMove = this.handleMouseMove.bind(this);
      this.state = { x: 0, y: 0 };
    }
  
    handleMouseMove(event) {
      this.setState({
        x: event.clientX,
        y: event.clientY
      });
    }
  
    render() {
      return (
        <div style={{ height: '100%' }} onMouseMove={this.handleMouseMove}>
  
          {/*
            Instead of providing a static representation of what <Mouse> renders,
            use the `render` prop to dynamically determine what to render.
          */}
          {this.props.render(this.state)}
        </div>
      );
    }
  }
  
  class Cat extends React.Component {
    render() {
      const mouse = this.props.mouse;
      return (
        <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: mouse.y }} />
      );
    }
  }
  
  
  ```

+ 现在，我们提供了一个 `render` 方法 让 `<Mouse>` 能够动态决定什么需要渲染，而不是克隆 `<Mouse>` 组件然后硬编码来解决特定的用例。
+ 更具体地说，**render prop 是一个用于告知组件需要渲染什么内容的函数 prop。**
+ 这项技术使我们共享行为非常容易。要获得这个行为，只要渲染一个带有 `render` prop 的 `<Mouse>` 组件就能够告诉它当前鼠标坐标 (x, y) 要渲染什么。
+ 关于 render prop 一个有趣的事情是你可以使用带有 render prop 的常规组件来实现大多数 [高阶组件](https://zh-hans.reactjs.org/docs/higher-order-components.html) (HOC)。 例如，如果你更喜欢使用 `withMouse` HOC 而不是 `<Mouse>` 组件，你可以使用带有 render prop 的常规 `<Mouse>` 轻松创建一个：

  ```jsx
  // 如果你出于某种原因真的想要 HOC，那么你可以轻松实现
  // 使用具有 render prop 的普通组件创建一个！
  function withMouse(Component) {
    return class extends React.Component {
      render() {
        return (
          <Mouse render={mouse => (
            <Component {...this.props} mouse={mouse} />
          )}/>
        );
      }
    }
  }
  ```

+ 因此，你可以将任一模式与 render prop 一起使用。

## 使用 Props 而非 Render

+ 重要的是要记住，render prop 是因为模式才被称为 *render* prop ，你不一定要用名为 `render` 的 prop 来使用这种模式。事实上， [*任何*被用于告知组件需要渲染什么内容的函数 prop 在技术上都可以被称为 “render prop”](https://cdb.reacttraining.com/use-a-render-prop-50de598f11ce).
+ 尽管之前的例子使用了 `render`，我们也可以简单地使用 `children` prop！

  ```jsx
  <Mouse children={mouse => (
    <p>鼠标的位置是 {mouse.x}，{mouse.y}</p>
  )}/>
  ```

+ 记住，`children` prop 并不真正需要添加到 JSX 元素的 “attributes” 列表中。相反，你可以直接放置到元素的**内部**！

  ```jsx
  <Mouse>
    {mouse => (
      <p>鼠标的位置是 {mouse.x}，{mouse.y}</p>
    )}
  </Mouse>
  ```

+ 然后通过 props.children 即可使用
+ 你将在 [react-motion](https://github.com/chenglou/react-motion) 的 API 中看到此技术。
+ 由于这一技术的特殊性，当你在设计一个类似的 API 时，你或许会要直接地在你的 propTypes 里声明 children 的类型应为一个函数。

  ```jsx
  Mouse.propTypes = {
    children: PropTypes.func.isRequired
  };
  ```

## 注意事项

+ 将 Render Props 与 React.PureComponent 一起使用时要小心
+ 如果你在 render 方法里创建函数，那么使用 render prop 会抵消使用 [`React.PureComponent`](https://zh-hans.reactjs.org/docs/react-api.html#reactpurecomponent) 带来的优势。因为浅比较 props 的时候总会得到 false，并且在这种情况下每一个 `render` 对于 render prop 将会生成一个新的值。
+ 例如，继续我们之前使用的 `<Mouse>` 组件，如果 `Mouse` 继承自 `React.PureComponent` 而不是 `React.Component`，我们的例子看起来就像这样：

  ```jsx
  class Mouse extends React.PureComponent {
    // 与上面相同的代码......
  }
  
  class MouseTracker extends React.Component {
    render() {
      return (
        <div>
          <h1>Move the mouse around!</h1>
  
          {/*
            这是不好的！
            每个渲染的 `render` prop的值将会是不同的。
          */}
          <Mouse render={mouse => (
            <Cat mouse={mouse} />
          )}/>
        </div>
      );
    }
  }
  ```

+ 在这样例子中，每次 `<MouseTracker>` 渲染，它会生成一个新的函数作为 `<Mouse render>` 的 prop，因而在同时也抵消了继承自 `React.PureComponent` 的 `<Mouse>` 组件的效果！
+ 为了绕过这一问题，有时你可以定义一个 prop 作为实例方法，类似这样：

  ```jsx
  class MouseTracker extends React.Component {
    // 定义为实例方法，`this.renderTheCat`始终
    // 当我们在渲染中使用它时，它指的是相同的函数
    renderTheCat(mouse) {
      return <Cat mouse={mouse} />;
    }
  
    render() {
      return (
        <div>
          <h1>Move the mouse around!</h1>
          <Mouse render={this.renderTheCat} />
        </div>
      );
    }
  }
  ```

+ 如果你无法静态定义 prop（例如，因为你需要关闭组件的 props 和/或 state），则 `<Mouse>` 应该继承 `React.Component`。

# 高阶组件、render Props 、props 通信 、props.children

## props.children 属性\活用标签体

- 通过 children 属性可以像函数一样运用组件

  ```jsx
  // 调用子元素回调 numTimes 次，来重复生成组件
  function Repeat(props) {
    let items = [];
    for (let i = 0; i < props.numTimes; i++) {
      items.push(props.children(i));
    }
    return <div>{items}</div>;
  }
  
  function ListOfTenThings() {
    return (
      <Repeat numTimes={10}>
        123
      </Repeat>
    );
  }
  ```

- repeat 组件接受标签体，就像函数接受参数一样，然后 repeat 组件根据 numTimes 重复渲染 标签体的内容

# Portals

## 概述

+ Portal 提供了一种将子节点渲染到存在于父组件以外的 DOM 节点的优秀的方案。
+ 通常来讲，当你从组件的 render 方法返回一个元素时，该元素将被挂载到 DOM 节点中离其最近的父节点：

  ```jsx
  render() {
    // React 挂载了一个新的 div，并且把子元素渲染其中
    return (
      <div>
        {this.props.children}
      </div>
    );
  }
  ```

+ 然而，有时候将子元素插入到 DOM 节点中的不同位置也是有好处的：

  ```jsx
  render() {
    // React 并*没有*创建一个新的 div。它只是把子元素渲染到 `domNode` 中。
    // `domNode` 是一个可以在任何位置的有效 DOM 节点。
    return ReactDOM.createPortal(
      this.props.children,
      domNode
    );
  }
  ```

+ 一个 portal 的典型用例是当父组件有 `overflow: hidden` 或 `z-index` 样式时，但你需要子组件能够在视觉上“跳出”其容器。例如，**对话框、悬浮卡以及提示框**。

**注意：**

+ 当在使用 portal 时, 记住 [管理键盘焦点](https://zh-hans.reactjs.org/docs/accessibility.html#programmatically-managing-focus) 就变得尤为重要。
+ 对于模态对话框，通过遵循 [WAI-ARIA 模态开发实践](https://www.w3.org/TR/wai-aria-practices-1.1/#dialog_modal)，来确保每个人都能够运用它。

## 用法

+ ```js
  ReactDOM.createPortal(child, container)
  ```
+ 第一个参数（`child`）是任何 [可渲染的 React 子元素](https://zh-hans.reactjs.org/docs/react-component.html#render)，例如一个元素，字符串或 fragment。第二个参数（`container`）是一个 DOM 元素。

## 通过 Portal 进行事件冒泡

+ 尽管 portal 可以被放置在 DOM 树中的任何地方，但在任何其他方面，其行为和普通的 React 子节点行为一致。由于 portal 仍存在于 **React 树**， 那么无论其子节点是否是 portal，像 context 这样的功能特性都是不变的。
+ 这包含事件冒泡。一个从 portal 内部触发的事件会一直冒泡至包含 **React 树 的祖先**，即便这些元素并不是 DOM 树 中的祖先。假设存在如下 HTML 结构：

  ```jsx
  <html>
    <body>
      <div id="app-root"></div>
      <div id="modal-root"></div>
    </body>
  </html>
  ```

+ 在 `#app-root` 里的 `Parent` 组件能够捕获到未被捕获的从兄弟节点 `#modal-root` 冒泡上来的事件。

  ```jsx
  // 在 DOM 中有两个容器是兄弟级 （siblings）
  const appRoot = document.getElementById('app-root');
  const modalRoot = document.getElementById('modal-root');
  
  class Modal extends React.Component {
    constructor(props) {
      super(props);
      this.el = document.createElement('div');
    }
  
    componentDidMount() {
      // 在 Modal 的所有子元素被挂载后，
      // 这个 portal 元素会被嵌入到 DOM 树中，
      // 这意味着子元素将被挂载到一个分离的 DOM 节点中。
      // 如果要求子组件在挂载时可以立刻接入 DOM 树，
      // 例如衡量一个 DOM 节点，
      // 或者在后代节点中使用 ‘autoFocus’，
      // 则需添加 state 到 Modal 中，
      // 仅当 Modal 被插入 DOM 树中才能渲染子元素。
      modalRoot.appendChild(this.el);
    }
  
    componentWillUnmount() {
      modalRoot.removeChild(this.el);
    }
  
    render() {
      return ReactDOM.createPortal(
        this.props.children,
        this.el,
      );
    }
  }
  
  class Parent extends React.Component {
    constructor(props) {
      super(props);
      this.state = {clicks: 0};
      this.handleClick = this.handleClick.bind(this);
    }
  
    handleClick() {
      // 当子元素里的按钮被点击时，
      // 这个将会被触发更新父元素的 state，
      // 即使这个按钮在 DOM 中不是直接关联的后代
      this.setState(state => ({
        clicks: state.clicks + 1
      }));
    }
  
    render() {
      return (
        <div onClick={this.handleClick}>
          <p>Number of clicks: {this.state.clicks}</p>
          <p>
            Open up the browser DevTools
            to observe that the button
            is not a child of the div
            with the onClick handler.
          </p>
          <Modal>
            <Child />
          </Modal>
        </div>
      );
    }
  }
  
  function Child() {
    // 这个按钮的点击事件会冒泡到父元素
    // 因为这里没有定义 'onClick' 属性
    return (
      <div className="modal">
        <button>Click</button>
      </div>
    );
  }
  
  ReactDOM.render(<Parent />, appRoot);
  ```

+ 在父组件里捕获一个来自 portal 冒泡上来的事件，使之能够在开发时具有不完全依赖于 portal 的更为灵活的抽象。例如，如果你在渲染一个 `<Modal />` 组件，无论其是否采用 portal 实现，父组件都能够捕获其事件。

# 类型检查 PropTypes

+ 自 React v15.5 起，`React.PropTypes` 已移入另一个包中。请使用 [`prop-types` 库](https://www.npmjs.com/package/prop-types) 代替。
+ 随着你的应用程序不断增长，你可以通过类型检查捕获大量错误。对于某些应用程序来说，你可以使用 [Flow](https://flow.org/) 或 [TypeScript](https://www.typescriptlang.org/) 等 JavaScript 扩展来对整个应用程序做类型检查。但即使你不使用这些扩展，React 也内置了一些类型检查的功能。要在组件的 props 上进行类型检查，你只需配置特定的 `propTypes` 属性：

  ```jsx
  import PropTypes from 'prop-types';
  
  class Greeting extends React.Component {
    render() {
      return (
        <h1>Hello, {this.props.name}</h1>
      );
    }
  }
  
  Greeting.propTypes = {
    name: PropTypes.string
  };
  ```

+ `PropTypes` 提供一系列验证器，可用于确保组件接收到的数据类型是有效的。在本例中, 我们使用了 `PropTypes.string`。当传入的 `prop` 值类型不正确时，JavaScript 控制台将会显示警告。出于性能方面的考虑，`propTypes` 仅在**开发模式下**进行检查。

## PropTypes

+ 以下提供了使用不同验证器的例子：

  ```jsx
  import PropTypes from 'prop-types';
  
  MyComponent.propTypes = {
    // 你可以将属性声明为 JS 原生类型，默认情况下
    // 这些属性都是可选的。
    optionalArray: PropTypes.array,
    optionalBool: PropTypes.bool,
    optionalFunc: PropTypes.func,
    optionalNumber: PropTypes.number,
    optionalObject: PropTypes.object,
    optionalString: PropTypes.string,
    optionalSymbol: PropTypes.symbol,
  
    // 任何可被渲染的元素（包括数字、字符串、元素或数组）
    // (或 Fragment) 也包含这些类型。
    optionalNode: PropTypes.node,
  
    // 一个 React 元素。
    optionalElement: PropTypes.element,
  
    // 你也可以声明 prop 为类的实例，这里使用
    // JS 的 instanceof 操作符。
    optionalMessage: PropTypes.instanceOf(Message),
  
    // 你可以让你的 prop 只能是特定的值，指定它为
    // 枚举类型。
    optionalEnum: PropTypes.oneOf(['News', 'Photos']),
  
    // 一个对象可以是几种类型中的任意一个类型
    optionalUnion: PropTypes.oneOfType([
      PropTypes.string,
      PropTypes.number,
      PropTypes.instanceOf(Message)
    ]),
  
    // 可以指定一个数组由某一类型的元素组成
    optionalArrayOf: PropTypes.arrayOf(PropTypes.number),
  
    // 可以指定一个对象由某一类型的值组成
    optionalObjectOf: PropTypes.objectOf(PropTypes.number),
  
    // 可以指定一个对象由特定的类型值组成
    optionalObjectWithShape: PropTypes.shape({
      color: PropTypes.string,
      fontSize: PropTypes.number
    }),
  
    // 你可以在任何 PropTypes 属性后面加上 `isRequired` ，确保
    // 这个 prop 没有被提供时，会打印警告信息。
    requiredFunc: PropTypes.func.isRequired,
  
    // 任意类型的数据
    requiredAny: PropTypes.any.isRequired,
  
    // 你可以指定一个自定义验证器。它在验证失败时应返回一个 Error 对象。
    // 请不要使用 `console.warn` 或抛出异常，因为这在 `onOfType` 中不会起作用。
    customProp: function(props, propName, componentName) {
      if (!/matchme/.test(props[propName])) {
        return new Error(
          'Invalid prop `' + propName + '` supplied to' +
          ' `' + componentName + '`. Validation failed.'
        );
      }
    },
  
    // 你也可以提供一个自定义的 `arrayOf` 或 `objectOf` 验证器。
    // 它应该在验证失败时返回一个 Error 对象。
    // 验证器将验证数组或对象中的每个值。验证器的前两个参数
    // 第一个是数组或对象本身
    // 第二个是他们当前的键。
    customArrayProp: PropTypes.arrayOf(function(propValue, key, componentName, location, propFullName) {
      if (!/matchme/.test(propValue[key])) {
        return new Error(
          'Invalid prop `' + propFullName + '` supplied to' +
          ' `' + componentName + '`. Validation failed.'
        );
      }
    })
  };
  ```

## 限制单个元素

+ 你可以通过 `PropTypes.element` 来确保传递给组件的 children 中只包含一个元素。

  ```jsx
  import PropTypes from 'prop-types';
  
  class MyComponent extends React.Component {
    render() {
      // 这必须只有一个元素，否则控制台会打印警告。
      const children = this.props.children;
      return (
        <div>
          {children}
        </div>
      );
    }
  }
  
  MyComponent.propTypes = {
    children: PropTypes.element.isRequired
  };
  ```

## 默认 Prop 值

+ 您可以通过配置特定的 `defaultProps` 属性来定义 `props` 的默认值：

  ```jsx
  class Greeting extends React.Component {
    render() {
      return (
        <h1>Hello, {this.props.name}</h1>
      );
    }
  }
  
  // 指定 props 的默认值：
  Greeting.defaultProps = {
    name: 'Stranger'
  };
  
  // 渲染出 "Hello, Stranger"：
  ReactDOM.render(
    <Greeting />,
    document.getElementById('example')
  );
  ```

+ 如果你正在使用像 [transform-class-properties](https://babeljs.io/docs/plugins/transform-class-properties/) 的 Babel 转换工具，你也可以在 React 组件类中声明 `defaultProps` 作为静态属性。此语法提案还没有最终确定，需要进行编译后才能在浏览器中运行。要了解更多信息，请查阅 [class fields proposal](https://github.com/tc39/proposal-class-fields)。

  ```jsx
  class Greeting extends React.Component {
    static defaultProps = {
      name: 'stranger'
    }
  
    render() {
      return (
        <div>Hello, {this.props.name}</div>
      )
    }
  }
  ```

+ `defaultProps` 用于确保 `this.props.name` 在父组件没有指定其值时，有一个默认值。`propTypes` 类型检查发生在 `defaultProps` 赋值后，所以类型检查也适用于 `defaultProps`。

# Hooks

https://juejin.im/post/5be3ea136fb9a049f9121014

https://juejin.im/post/5bfcbc83e51d450fb3263a35

https://juejin.im/post/5be6150551882511a8526955

- Hook 无法在类组件内部使用，但是类却可以在 Hook 内部使用
- 这里说的其实是 useState 等钩子，只可以在函数组件内部使用，不可以在 class 组件内部使用，类组件和函数组件的嵌套式 ok 的？没错，本质还是 class 组件和函数组件而已，不会排斥
- ![preview](/img/user/programming/font-end/framework/react/react-advanced/v2-844a3ea91aa9ba8ca1f9f77bc7cbc1d9_r.jpg)

## 但是什么是钩子（Hook）?

- 钩子是可以让你与**React 状态以及函数式组件的生命周期特性**“挂钩”的函数。钩子是为了让你**抛弃**类使用 React 的，**所以它不能在类中运行**。（我们 [不推荐](https://react.docschina.org/docs/hooks-intro.html#gradual-adoption-strategy) 你立即重写已经存在的组件，但是如果你喜欢的话可以在新的组件中开始使用钩子。）
- React 提供了少量内置的钩子，如 `useState`。你也可以创建自己的钩子在不同的组件之间复用有状态的行为。我们先来看一下内置的钩子。
- *Hooks*是 React v16.7.0-alpha 中加入的新特性。它可以让你在 class 以外使用 state 和其他 React 特性。
- 有了 Hooks，你可以以一种更加强大的方式将 props, state, context, refs 和生命周期整合起来。
- **Hooks 允许您根据相关部分 (例如设置订阅或获取数据) 将一个组件分割成更小的函数**，而不是强制基于生命周期方法进行分割。您还可以选择使用一个 reducer 来管理组件的本地状态，以使其更加可预测。

# 无状态组件

+ 无状态组件是普通的函数，返回值是 dom 结构，没有形成闭包去保存函数的内部变量，所以函数内的局部变量没有闭包的特性，
+ 参考录音库，仅仅是吧 recorder 作为局部变量，不同的函数之间是没有办法引用的。不同函数引用的是同一个 recorder 嘛？实验一下吧
+ 因此需要 state

# 状态钩子（State Hook）

- ```react
  import { useState } from 'react';
  
  function Example() {
    // Declare a new state variable, which we'll call "count"
    // a pair 数组的解构赋值，count 的初始值由 useState 初始化
    const [count, setCount] = useState(0);
  
    return (
      <div>
        <p>You clicked {count} times</p>
        <button onClick={() => setCount(count + 1)}>
          Click me
        </button>
      </div>
    );
  }
  ```
- ![1552531385655](/img/user/programming/font-end/framework/react/react-advanced/1552531385655.png) you clicked 8 times
- 在这里, `useState` 是一个*钩子（Hook）* （稍后我们将会谈及它的含义）。
- 我们在一个函数式组件中调用它，为这个组件增加一些内部的状态。React 将会在**下一次渲染前保存此状态**。
  - 函数式组件没有生命周期函数，但是不代表它没有生命周期
- **What does calling useState do?** It declares a “state variable”. Our variable is called `count`but we could call it anything else, like `banana`. This is a way to “**preserve**” some values between the function calls — `useState` is a new way to use the exact same capabilities that `this.state` provides in a class. Normally, variables “disappear” when the function exits but state variables are preserved by React.
- `useState` 唯一的**参数就是初始状态（initial state）**。在上面的例子中,因为我们的计数器从零开始所以它是 `0`。这里的状态与 `this.state` 不同，它不必是一个对象— 如果你想这么做，当然也可以。初始状态参数只在第一次渲染中被使用。
- `useState` 返回一对值：当前的状态（state value）和一个可以更新状态的函数。你可以在事件处理程序（event handler）中或其他地方调用这个函数。 它与类组件中的 `this.setState` 类似，但不能将新旧状态进行合并。（我们在 [使用状态钩子](https://react.docschina.org/docs/hooks-state.html) 中展示了一个将 `useState` 和 `this.state` 进行对比的例子。）
- You might be wondering: why is `useState` not named `createState` instead?
  - “Create” wouldn’t be quite accurate because the state is only created the first time our component renders. During the next renders, `useState` gives us the current state. Otherwise it wouldn’t be “state” at all! There’s also a reason why Hook names *always* start with `use`. We’ll learn why later in the [Rules of Hooks](https://react.docschina.org/docs/hooks-rules.html).

## 声明多个状态变量

- 你可以在一个组件中多次使用状态钩子:

  ```REACT
  function ExampleWithManyStates() {
    // 声明多个状态变量！
    const [age, setAge] = useState(42);
    const [fruit, setFruit] = useState('banana');
    const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
    // ...
  }
  ```

- 通过调用 `useState` 我们声明了一些状态变量，我们可以使用 [数组解构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Array_destructuring) 语法赋予这些状态变量不同的名字。
- When we declare a state variable with `useState`, it returns a pair — an array with two items. The first item is the current value, and the second is a function that lets us update it. Using `[0]`and `[1]` to access them is a bit confusing because they have a specific meaning. This is why we use array destructuring instead.
- 这些名字不是 `useState` API 的一部分。 相反，当你多次调用 `useState` 时，**React 假定你在每一次渲染中以相同的顺序调用它们**。我们会在之后再来解释为什么这样可以运行以及在什么时候起作用。
- You might be curious how React knows which component `useState` corresponds to since we’re not passing anything like `this` back to React. We’ll answer [this question](https://react.docschina.org/docs/hooks-faq.html#how-does-react-associate-hook-calls-with-components) and many others in the FAQ section.
- You **don’t have to** use many state variables. State variables can hold objects and arrays just fine, so you can still group related data together. **However, unlike** `this.setState` in a class, updating a state variable always *replaces* it instead of merging it.@

## Reading State

- When we want to display the current count in a class, we read `this.state.count`
- In a function, we can use `count` directly，因为 state 就是函数内部的一个变量，作用域内可以随意使用

## Updating State

- In a class, we need to call `this.setState()` to update the `count` state
- In a function, we already have `setCount` and `count` as variables so we don’t need `this`:

  ```react
  <button onClick={() => setCount(count + 1)}>
    Click me
  </button>
  ```

  - 在作用域内调用 setXXX()，传入 state，即可更新
- 同时更新多个 state 只能分别调用
- 类似与 setState，直接更改 state 的值，组件不会重新渲染，也不会触发相应的 useEffect
  - 因此，useEffect 里面 setState 有可能会导致无线循环
- 而且，虽然使用 const 解构，但是 state 和 setState 都是可以直接更改的
- 如果 state 是一个对象，先使用 assign 方法更新闭包里的 state，再 setState
  - setXxx 钩子 不会自动合并对象，而是会直接覆盖
  - 即使 state 不是闭包内的，而是真正的 state 也 ok，直接改变 state 的值，然后 setState 一个相同的值，通知渲染，这样是行不通的，再次 setState 时，引用是相同，不会进行渲染

### 当 State 是 Object 时@

- ```react
  const [obj,setObj] = useState({
    name:'mary',
    age:20
  })
   useEffect(() => {
    const newObj = Object.assign(obj,{age:29}) // newObj 和 obj 引用相同
    newObj.age = 30
    console.log(newObj)
    console.log(obj) // 传递一个属性不同，引用相同的obj，不会重新渲染
    setObj(obj)
  },[])  
  
   useEffect(() => {
    
    setObj({name:'mary',age:29}) // 一个属性完全相同的新obj，会重新渲染
    console.log(obj)
  },[]) 
  
  useEffect(() => {
    console.log(obj)
    setObj({...obj,age:29}) // 使用...运算符简化克隆
    console.log(obj)
  },[])
  ```

## useState 回调函数

setState 是一个异步的过程，在 class component 时我们可以通过 setState 的第二个参数实现这个功能。

在 Hooks 中，我们可以通过 useEffect 模拟实现

# 副作用钩子（Effect Hook）

- 你可能之前已经在 React 中执行过获取数据，订阅或者手动改变 DOM。我们称这些操作为“副作用（side effects）”（或者简称为“作用（effects）”），因为它们可以**影响其他的组件并且不能在渲染中完成**。
- 副作用钩子, `useEffect`, 为函数式组件带来执行副作用的能力。它与类组件中的 `componentDidMount` ，`componentDidUpdate` 和 `componentWillUnmount` 具有相同的用途，但是**被统一为一个 API**。（我们在 [使用副作用钩子](https://react.docschina.org/docs/hooks-effect.html) 中展示了一个将 `useEffect` 和这些方法进行对比的例子。）
- 举个例子，这个组件在 React 更新 DOM 之后设置文档的标题：

  ```REACT
  import { useState, useEffect } from 'react';
  
  function Example() {
    const [count, setCount] = useState(0);
  
    // 类似于 componentDidMount 和 componentDidUpdate:
    useEffect(() => {
      // 使用浏览器API更新文档标题
      document.title = `You clicked ${count} times`;
    });
  
    return (
      <div>
        <p>You clicked {count} times</p>
        <button onClick={() => setCount(count + 1)}>
          Click me
        </button>
      </div>
    );
  }
  ```

- 当你调用 `useEffect`,就是告诉 React 在**刷新 DOM 之后运行你的副作用函数**。副作用函数在组件中声明，所以可以使用组件的状态（state）和属性（props）。**React 默认在每一次渲染后运行副作用函数**——*包括*第一次渲染。(与类组件的生命周期函数的对比请看 [使用副作用钩子](https://react.docschina.org/docs/hooks-effect.html)。)

## 不需要清理的 Effects

- 有时我们想要 **在 React 更新过 DOM 之后执行一些额外的操作。** 比如网络请求、手动更新 DOM 、以及打印日志都是常见的不需要清理的 effects。
- 在 **class 组件**中，`render` 方法本身不应该导致 side effects。`render` 方法太早了————我们通常会在 React 更新过 DOM *之后* 再执行 effect。
- 这也是我们在 class 组件中，把 side effects 放在 `componentDidMount` 和 `componentDidUpdate` 中的原因。
- **useEffect 做了什么？** 通过这个 Hook，React 知道你想要这个组件在每次 render 之后做些事情。React 会记录下你传给 `useEffect` 的这个方法（我们可以把它看做我们的 `effect` ），然后在进行了 DOM 更新之后调用这个方法。在这个 effect 中，我们设置 document title，但我们同样也可以进行数据获取或是调用其它必要的 API。
- **为什么 useEffect 在组件内部调用？** 将 `useEffect` 放在一个组件内部，可以让我们在 effect 中，即可获得对 `count` state（或其它 props）的访问，而不是使用一个特殊的 API 去获取它。Hooks 使用了 JavaScript 的闭包，从而避免了引入 React 特有的 API 来解决 JavaScript 已经提供解决方案。
- **useEffect 是不是在每次 render 之后都会调用？** 是的！默认情况下，它会在第一次 render *和*之后的每次 update 后运行。（我们会在之后讨论如何 [优化](https://react.docschina.org/docs/hooks-effect.html#tip-optimizing-performance-by-skipping-effects)。）比起 “mounting” 和 “updating”，effect 在“每次 render”之后调用，想必会更容易理解。React 保证每次运行 effects 之前 DOM 已经更新了。
- 有经验的 JavaScript 开发者也许已经发现，在每次 render 的时候，我们传递给 `useEffect` 的方法都是全新的。这是故意的。事实上，这正是我们可以在 effect 内部读取到 `count` 值，并且不用担心 `count` 值过期的原因。每当我们重新 render 的时候，我们都会使用一个 *不同的* effect，替换掉之前的哪一个。在某种程度上，这使得 effect 表现得更像是 render 结果的一部分————每个 effect “属于”一个特定的 render。我们会在 [这一节的后面](https://react.docschina.org/docs/hooks-effect.html#explanation-why-effects-run-on-each-update) 更清晰地了解到这么做的作用。
- 不像 `componentDidMount` 或者 `componentDidUpdate`，`useEffect` 中使用的 effect **并不会阻滞浏览器渲染页面**。这让你的 app 看起来更加流畅。尽管大多数 effect 不需要同步调用。但是在一些不常见的情况下你也许需要他们同步调用（比如计算元素尺寸），我们提供了一个单独的 [`useLayoutEffect`](https://react.docschina.org/docs/hooks-reference.html#uselayouteffect) 来达成这样的效果。它的 API 和 `useEffect` 是相同的。

## 需要清理的 Effects

- 在 React class 中，典型的做法是在 `componentDidMount` 里创建订阅，然后在 `componentWillUnmount` 中清除它。

  ```react
    componentDidMount() {
      ChatAPI.subscribeToFriendStatus(
        this.props.friend.id,
        this.handleStatusChange
      );
    }
  
    componentWillUnmount() {
      ChatAPI.unsubscribeFromFriendStatus(
        this.props.friend.id,
        this.handleStatusChange
      );
    }
  ```

- 注意 `componentDidMount` 和 `componentWillUnmount` 中我们需要重复同一段代码。生命周期要求我们不得不拆分这段逻辑，就算从概念上讲他们是从属于同一个 effect 的。
- 副作用函数可以**通过返回一个函数来指定如何“回收”它们**，如果你的 effect 返回了一个函数，React 将会在清理时运行它：举个例子，这个组件使用了一个副作用函数来订阅一个朋友的在线状态，通过取消订阅来回收：

  ```react
  import { useState, useEffect } from 'react';
  
  function FriendStatus(props) {
    const [isOnline, setIsOnline] = useState(null);
  
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
  
    useEffect(() => {
      ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
  
      return () => {
        ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
      };
    });
  
    if (isOnline === null) {
      return 'Loading...';
    }
    return isOnline ? 'Online' : 'Offline';
  }
  ```

- 在这个例子中，**当组件被卸载时，React 会在由随后的渲染引起的副作用函数运行之前取消对 `ChatAPI` 的订阅。**（如果有需要的话，可以用这个方法 [告诉React跳过重订阅](https://react.docschina.org/docs/hooks-effect.html#tip-optimizing-performance-by-skipping-effects) 当传给 `ChatAPI` 的 `props.friend.id` 没有改变时。）
- **React 究竟在什么时候清理 effect？** React 在每次组件 unmount 的时候执行清理。然而，正如我们之前了解的那样，effect 会在每次 render 时运行，而不是仅仅运行一次。这也就是为什么 React *也* 会在下次运行 effect 之后清理上一次 render 中的 effect。我们会在接下来讨论 [为什么这可以帮助避免 bug](https://react.docschina.org/docs/hooks-effect.html#explanation-why-effects-run-on-each-update) 以及 [如何有选择的运行 effect 以避免出现性能问题](https://react.docschina.org/docs/hooks-effect.html#tip-optimizing-performance-by-skipping-effects)
- 我们没必要在 effect 中返回一个具名函数。我们在这里称它为 `清理` 就可以表明它的目的，但你也可以返回一个箭头函数或者给它起一个名字。

## 使用多个 userEffect

- 像使用 `useState` 一样,你可以在一个组件中使用多个副作用：

  ```react
  function FriendStatusWithCounter(props) {
    const [count, setCount] = useState(0);
    useEffect(() => {
      document.title = `You clicked ${count} times`;
    });
  
    const [isOnline, setIsOnline] = useState(null);
    useEffect(() => {
      ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
      return () => {
        ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
      };
    });
  
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    // ...
  
  ```

- 有了钩子，你可以在组件中按照代码块的相关性组织副作用，而不是基于生命周期方法强制进行切分。

## Why Effects Run on Each Update

- If you’re used to classes, you might be wondering why the effect cleanup phase happens after every re-render, and not just once during unmounting. Let’s look at a practical example to see why this design helps us create components with fewer bugs.

  ```react
    componentDidMount() {
      ChatAPI.subscribeToFriendStatus(
        this.props.friend.id,
        this.handleStatusChange
      );
    }
  
    componentWillUnmount() {
      ChatAPI.unsubscribeFromFriendStatus(
        this.props.friend.id,
        this.handleStatusChange
      );
    }
  ```

- **But what happens if the friend prop changes** while the component is on the screen? Our component would continue displaying the online status of a different friend. This is a bug. We would also cause a memory leak or crash when unmounting since the unsubscribe call would use the wrong friend ID.
- In a class component, we would need to add `componentDidUpdate` to handle this case:

  ```react
    componentDidMount() {
      ChatAPI.subscribeToFriendStatus(
        this.props.friend.id,
        this.handleStatusChange
      );
    }
  
    componentDidUpdate(prevProps) {
      // Unsubscribe from the previous friend.id
      ChatAPI.unsubscribeFromFriendStatus(
        prevProps.friend.id,
        this.handleStatusChange
      );
      // Subscribe to the next friend.id
      ChatAPI.subscribeToFriendStatus(
        this.props.friend.id,
        this.handleStatusChange
      );
    }
  
    componentWillUnmount() {
      ChatAPI.unsubscribeFromFriendStatus(
        this.props.friend.id,
        this.handleStatusChange
      );
    }
  ```

- Forgetting to handle `componentDidUpdate` properly is a common source of bugs in React applications.
- Now consider the version of this component that **uses Hooks**:@

  ```react
  function FriendStatus(props) {
    // ...
    useEffect(() => {
      ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
      return () => {
        ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
      };
    });
  ```

- There is no special code for handling updates because `useEffect` handles them **by default**.
- It cleans up the previous effects before applying the next effects. To illustrate this, here is a sequence of subscribe and unsubscribe calls that this component could produce over time:

  ```js
  // Mount with { friend: { id: 100 } } props
  ChatAPI.subscribeToFriendStatus(100, handleStatusChange);     // Run first effect
  
  // Update with { friend: { id: 200 } } props
  ChatAPI.unsubscribeFromFriendStatus(100, handleStatusChange); // Clean up previous effect
  ChatAPI.subscribeToFriendStatus(200, handleStatusChange);     // Run next effect
  
  // Update with { friend: { id: 300 } } props
  ChatAPI.unsubscribeFromFriendStatus(200, handleStatusChange); // Clean up previous effect
  ChatAPI.subscribeToFriendStatus(300, handleStatusChange);     // Run next effect
  
  // Unmount
  ChatAPI.unsubscribeFromFriendStatus(300, handleStatusChange); // Clean up last effect
  ```

## Optimizing Performance by Skipping Effects

- In some cases, cleaning up or applying the effect after every render might create a performance problem. In class components, we can solve this by writing an extra comparison with `prevProps` or `prevState` inside `componentDidUpdate`:

  ```react
  componentDidUpdate(prevProps, prevState) {
    if (prevState.count !== this.state.count) {
      document.title = `You clicked ${this.state.count} times`;
    }
  }
  ```

- **This requirement is common enough that it is built into the `useEffect` Hook API.** You can tell React to **skip** applying an effect if certain values haven’t changed between re-renders. To do so, pass an array as an optional second argument to `useEffect`:

  ```react
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  }, [count]); // Only re-run the effect if count changes
  ```

  - In the example above, we pass `[count]` as the second argument. What does this mean? If the `count` is `5`, and then our component re-renders with `count` still equal to `5`, React will compare `[5]` from the previous render and `[5]` from the next render. Because **all items in the array** are the same (`5 === 5`), React would skip the effect. That’s our optimization.
  - When we render with `count` updated to `6`, React will compare the items in the `[5]` array from the previous render to items in the `[6]` array from the next render. This time, React will re-apply the effect because `5 !== 6`. **If there are multiple items in the array, React will re-run the effect even if just one of them is different.**
  - 深度比较还是浅度比较？浅，不用问，都是浅，性能问题
- This also works for effects that have a cleanup phase:

  ```react
  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  }, [props.friend.id]); // Only re-subscribe if props.friend.id changes
  ```

- In the future, the second argument might get added automatically by a build-time transformation.
- If you use this optimization, make sure the array includes **any values from the outer scope that change over time and that are used by the effect**. Otherwise, your code will reference stale values from previous renders. We’ll also discuss other optimization options in the [Hooks API reference](https://react.docschina.org/docs/hooks-reference.html).
- If you want to run an effect and clean it up **only once** (on mount and unmount), you can pass an empty array (`[]`) as a second argument. This tells React that your effect doesn’t depend on *any* values from props or state, so it never needs to re-run.
  - This isn’t handled as a special case — it follows directly from how the inputs array always works. While passing `[]` is closer to the familiar `componentDidMount` and `componentWillUnmount` mental model, we suggest not making it a habit because it often leads to bugs, [as discussed above](https://react.docschina.org/docs/hooks-effect.html#explanation-why-effects-run-on-each-update). Don’t forget that React defers running `useEffect` until after the browser has painted, so doing extra work is less of a problem.

# Hooks 规范

- 钩子就是强制实现了两条额外规则的 Javascript 函数：
  - 只能在**顶层**调用钩子。不要在循环，控制流和嵌套的函数中调用钩子。
    - Instead, always use Hooks at the top level of your React function. By following this rule, you ensure that Hooks are called in the same order each time a component renders. That’s what allows React to correctly preserve the state of Hooks between multiple `useState` and `useEffect` calls. (If you’re curious, we’ll explain this in depth [below](https://react.docschina.org/docs/hooks-rules.html#explanation).)
  - 只能从 React 的**函数式组件**中调用钩子。不要在常规的 JavaScript 函数中调用钩子。（此外，你也可以在你的自定义钩子中调用钩子。我们马上就会讲到它。）
    - By following this rule, you ensure that all stateful logic in a component is clearly visible from its source code.

## Explanation

- 正如我们 [之前学到](https://react.docschina.org/docs/hooks-state.html#tip-using-multiple-state-variables) 的，我们可以在单个组件中使用多个 State Hook 或 Effect Hook

  ```react
  function Form() {
    // 1. Use the name state variable
    const [name, setName] = useState('Mary');
  
    // 2. Use an effect for persisting the form
    useEffect(function persistForm() {
      localStorage.setItem('formData', name);
    });
  
    // 3. Use the surname state variable
    const [surname, setSurname] = useState('Poppins');
  
    // 4. Use an effect for updating the title
    useEffect(function updateTitle() {
      document.title = name + ' ' + surname;
    });
  
    // ...
  }
  ```

- 那么 React 怎么知道哪个 state 对应哪个 `useState`？答案是 React 靠的是 Hook 调用的顺序。因为我们的示例中，Hook 的调用顺序在每次渲染中都是相同的，所以它能够正常工作：
- ```react
  // ------------
  // First render
  // ------------
  useState('Mary')           // 1. Initialize the name state variable with 'Mary'
  useEffect(persistForm)     // 2. Add an effect for persisting the form
  useState('Poppins')        // 3. Initialize the surname state variable with 'Poppins'
  useEffect(updateTitle)     // 4. Add an effect for updating the title
  
  // -------------
  // Second render
  // -------------
  useState('Mary')           // 1. Read the name state variable (argument is ignored)
  useEffect(persistForm)     // 2. Replace the effect for persisting the form
  useState('Poppins')        // 3. Read the surname state variable (argument is ignored)
  useEffect(updateTitle)     // 4. Replace the effect for updating the title
  
  // ...
  ```
- 只要 Hook 的调用顺序在多次渲染之间保持一致，React 就能正确地将内部 state 和对应的 Hook 进行关联。但如果我们将一个 Hook (例如 `persistForm` effect) 调用放到一个条件语句中会发生什么呢？

  ```react
  // 🔴在条件语句中使用 Hook 违反第一条规则
    if (name !== '') {
      useEffect(function persistForm() {
        localStorage.setItem('formData', name);
      });
    }
  ```

- 在第一次渲染中 `name !== ''` 这个条件值为 `true`，所以我们会执行这个 Hook。但是下一次渲染时我们可能清空了表单，表达式值变为 `false`。此时的渲染会跳过该 Hook，Hook 的调用顺序发生了改变：

  ```react
  useState('Mary')           // 1. Read the name state variable (argument is ignored)
  // useEffect(persistForm)  // 🔴 This Hook was skipped!
  useState('Poppins')        // 🔴 2 (but was 3). Fail to read the surname state variable
  useEffect(updateTitle)     // 🔴 3 (but was 4). Fail to replace the effect
  ```

- React 不知道第二个 `useState` 的 Hook 应该返回什么。React 会以为在该组件中第二个 Hook 的调用像上次的渲染一样，对应得是 `persistForm` 的 effect，但并非如此。从这里开始，后面的 Hook 调用都被提前执行，导致 bug 的产生。
- **这就是为什么 Hook 需要在我们组件的最顶层调用。**如果我们想要有条件地执行一个 effect，可以将判断放到 Hook 的*内部*：

  ```react
    useEffect(function persistForm() {
      // 👍 We're not breaking the first rule anymore
      if (name !== '') {
        localStorage.setItem('formData', name);
      }
    });
  ```

- **注意：如果使用了提供的 lint 插件，就无需担心此问题。** 不过你现在知道了为什么 Hook 会这样工作，也知道了这个规则是为了避免什么问题。

# 自定义 Hooks

- 有时你希望在组件之间复用一些状态逻辑。在之前有两种流行的解决方案：[高阶组件](https://react.docschina.org/docs/higher-order-components.html) and [渲染属性](https://react.docschina.org/docs/render-props.html)。现在你可以利用自定义钩子做到这些而不用在你的组件树中添加更多的组件。@
- 在此之前，我们展示了一个 `FriendStatus` 组件，它可以调用 `useState` 和 `useEffect` 钩子来订阅一个朋友的在线状态。假设我们想要在其他的组件中复用这个订阅逻辑。
- 首先，我们要把这个逻辑抽取到名为 `useFriendStatus` 的自定义钩子中：

  ```react
  import { useState, useEffect } from 'react';
  
  function FriendStatus(props) {
    const [isOnline, setIsOnline] = useState(null);
  
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
  
    useEffect(() => {
      ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
      return () => {
        ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
      };
    });
  
    if (isOnline === null) {
      return 'Loading...';
    }
    return isOnline ? 'Online' : 'Offline';
  }
  ```

- 这个钩子需要一个 `friendID` 作为参数，返回你的朋友是否在线。
- 现在我们假设聊天应用中有一个联系人列表，当用户在线时需要把名字设置为绿色。我们可以把上面类似的逻辑复制并粘贴到 `FriendListItem` 组件中来，但这并不是理想的解决方案：

  ```react
  import { useState, useEffect } from 'react';
  
  function FriendListItem(props) {
    const [isOnline, setIsOnline] = useState(null);
  
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
  
    useEffect(() => {
      ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
      return () => {
        ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
      };
    });
  
    return (
      <li style={{ color: isOnline ? 'green' : 'black' }}>
        {props.friend.name}
      </li>
    );
  }
  ```

- 相反，我们希望在 `FriendStatus` 和 `FriendListItem` 之间共享逻辑。
- 目前为止，在 React 中有两种流行的方式来共享组件之间的状态逻辑: [render props](https://react.docschina.org/docs/render-props.html) 和 [高阶组件](https://react.docschina.org/docs/higher-order-components.html)，现在让我们来看看 Hook 是如何在让你不增加组件的情况下解决相同问题的。

## 提取自定义 Hook

- 当我们想在两个函数之间共享逻辑时，我们会把它提取到第三个函数中。而组件和 Hook 都是函数，所以也同样适用这种方式。
- **自定义 Hook 是一个函数，其名称以 “use” 开头，函数内部可以调用其他的 Hook。** 例如，下面的 `useFriendStatus` 是我们第一个自定义的 Hook:

  ```react
  import { useState, useEffect } from 'react';
  
  function useFriendStatus(friendID) {
    const [isOnline, setIsOnline] = useState(null);
  
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
  
    useEffect(() => {
      ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
      return () => {
        ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
      };
    });
  
    return isOnline;
  }
  ```

- 此处并未包含任何新的内容——逻辑是从上述组件拷贝来的。与组件中一致，请确保只在自定义 Hook 的顶层无条件地调用其他 Hook。
- 与 React 组件不同的是，自定义 Hook 不需要具有特殊的标识。我们可以自由的决定它的参数是什么，以及它应该返回什么（如果需要的话）。换句话说，它就像一个正常的函数。但是它的名字应该始终以 `use` 开头，这样可以一眼看出其符合 [Hook 的规则](https://react.docschina.org/docs/hooks-rules.html)。
- 此处 `useFriendStatus` 的 Hook 目的是订阅某个好友的在线状态。这就是我们需要将 `friendID` 作为参数，并返回这位好友的在线状态的原因。

  ```jsx
  function useFriendStatus(friendID) {
    const [isOnline, setIsOnline] = useState(null);
  
    // ...
  
    return isOnline;
  }
  ```

## 使用自定义 Hook

- 我们一开始的目标是在 `FriendStatus` 和 `FriendListItem` 组件中去除重复的逻辑，即：这两个组件都想知道好友是否在线。
- 现在，我们可以同时在两个组件中使用它：

  ```react
  // 函数式组件一
  function FriendStatus(props) {
    const isOnline = useFriendStatus(props.friend.id);
  
    if (isOnline === null) {
      return 'Loading...';
    }
    return isOnline ? 'Online' : 'Offline';
  }
  // 函数式组件二
  function FriendListItem(props) {
    const isOnline = useFriendStatus(props.friend.id);
  
    return (
      <li style={{ color: isOnline ? 'green' : 'black' }}>
        {props.friend.name}
      </li>
    );
  }
  ```

- **这段代码等价于原来的示例代码吗?**等价，它的工作方式完全一样。如果你仔细观察，你会发现我们没有对其行为做任何的改变，我们只是将两个函数之间一些共同的代码提取到单独的函数中。**自定义 Hook 是一种自然遵循 Hook 设计的约定，而并不是 React 的特性。**
- 自定义钩子更多的**是一个约定 (convention)**而不是特性。如果一个函数的名字以 ”`use`” 开头并且**调用了其他的钩子**，我们就称它为自定义钩子。`useSomething` 的命名约定方便语法检查插件找到代码中钩子的错误使用。
- **在两个组件中使用相同的 Hook 会共享 state 吗?**，NO，两个组件中的状态是完全独立的。钩子只复用状态逻辑而不是状态本身。事实上，每一次调用钩子都会得到一个完全孤立的状态——所以你甚至可以在同一个组件中使用两次相同的自定义钩子。
- **自定义 Hook 如何获取独立的 state?**每次*调用* Hook，它都会获取独立的 state。由于我们直接调用了 `useFriendStatus`，从 React 的角度来看，我们的组件只是调用了 `useState` 和 `useEffect`。 正如我们在 [之前章节](https://react.docschina.org/docs/hooks-effect.html#tip-use-multiple-effects-to-separate-concerns) 中 [了解到的](https://react.docschina.org/docs/hooks-state.html#tip-using-multiple-state-variables) 一样，我们可以在一个组件中多次调用 `useState` 和 `useEffect`，它们是完全独立的。
- 自定义钩子可以覆盖非常多的用例，像**表单处理，动画，声明式订阅，定时器**，还有很多我们还没有考虑到的。我们非常激动能够看到 React 社区提出的自定义钩子。

## 在多个 Hook 之间传递信息

+ 由于 Hook 本身就是函数，因此我们可以在它们之间传递信息。

- 我们将使用聊天程序中的另一个组件来说明这一点。这是一个聊天消息接收者的选择器，它会显示当前选定的好友是否在线:

  ```react
  const friendList = [
    { id: 1, name: 'Phoebe' },
    { id: 2, name: 'Rachel' },
    { id: 3, name: 'Ross' },
  ];
  
  function ChatRecipientPicker() {
    const [recipientID, setRecipientID] = useState(1);
    const isRecipientOnline = useFriendStatus(recipientID);
  
    return (
      <>
        <Circle color={isRecipientOnline ? 'green' : 'red'} />
        <select
          value={recipientID}
          onChange={e => setRecipientID(Number(e.target.value))}
        >
          {friendList.map(friend => (
            <option key={friend.id} value={friend.id}>
              {friend.name}
            </option>
          ))}
        </select>
      </>
    );
  }
  ```

- 我们将当前选择的好友 ID 保存在 `recipientID` 状态变量中，并在用户从 `<select>` 中选择其他好友时更新这个 state。
- 由于 `useState` 为我们提供了 `recipientID` 状态变量的最新值，因此我们可以将它作为参数传递给自定义的 `useFriendStatus` Hook：

  ```react
    const [recipientID, setRecipientID] = useState(1);
    const isRecipientOnline = useFriendStatus(recipientID);
  ```

- 如此可以让我们知道*当前选中*的好友是否在线。当我们选择不同的好友并更新 `recipientID` 状态变量时，`useFriendStatusHook` 将会取消订阅之前选中的好友，并订阅新选中的好友状态。

## useYourImagination()

- 自定义 Hook 解决了以前在 React 组件中无法灵活共享逻辑的问题。你可以创建涵盖各种场景的自定义 Hook，如表单处理、动画、订阅声明、计时器，甚至可能还有其他我们没想到的场景。更重要的是，创建自定义 Hook 就像使用 React 内置的功能一样简单。
- 尽量避免过早地增加抽象逻辑。既然函数组件能够做的更多，那么代码库中函数组件的代码行数可能会剧增。这属于正常现象 —— 不必立即将它们拆分为 Hook。但我们仍鼓励你能通过自定义 Hook 寻找可能，以达到简化代码逻辑，解决组件杂乱无章的目的。
- 例如，有个复杂的组件，其中包含了大量以特殊的方式来管理的内部状态。`useState` 并不会使得集中更新逻辑变得容易，因此你可能更愿意使用 [redux](http://redux.js.org/) 中的 reducer 来编写。

  ```js
  function todosReducer(state, action) {
    switch (action.type) {
      case 'add':
        return [...state, {
          text: action.text,
          completed: false
        }];
      // ... other actions ...
      default:
        return state;
    }
  }
  ```

- Reducers 非常便于单独测试，且易于扩展，以表达复杂的更新逻辑。如有必要，您可以将它们分成更小的 reducer。但是，你可能还享受着 React 内部 state 带来的好处，或者可能根本不想安装其他库。
- 那么，为什么我们不编写一个 `useReducer` 的 Hook，使用 reducer 的方式来管理组件的内部 state 呢？其简化版本可能如下所示：

  ```react
  function useReducer(reducer, initialState) {
    const [state, setState] = useState(initialState);
  
    function dispatch(action) {
      const nextState = reducer(state, action);
      setState(nextState);
    }
  
    return [state, dispatch];
  }
  ```

- 在组件中使用它，让 reducer 驱动它管理 state：

  ```react
  function Todos() {
    const [todos, dispatch] = useReducer(todosReducer, []);
  
    function handleAddClick(text) {
      dispatch({ type: 'add', text });
    }
  
    // ...
  }
  ```

- 在复杂组件中使用 reducer 管理内部 state 的需求很常见，我们已经将 `useReducer` 的 Hook 内置到 React 中。你可以在 [Hook API 索引](https://react.docschina.org/docs/hooks-reference.html) 中找到它使用，搭配其他内置的 Hook 一起使用。

# 其他钩子

- built-in Hooks in React
- [Basic Hooks](https://react.docschina.org/docs/hooks-reference.html#basic-hooks)
  - [`useState`](https://react.docschina.org/docs/hooks-reference.html#usestate)
  - [`useEffect`](https://react.docschina.org/docs/hooks-reference.html#useeffect)
  - [`useContext`](https://react.docschina.org/docs/hooks-reference.html#usecontext)
- [Additional Hooks](https://react.docschina.org/docs/hooks-reference.html#additional-hooks)
  - [`useReducer`](https://react.docschina.org/docs/hooks-reference.html#usereducer)
  - [`useCallback`](https://react.docschina.org/docs/hooks-reference.html#usecallback)
  - [`useMemo`](https://react.docschina.org/docs/hooks-reference.html#usememo)
  - [`useRef`](https://react.docschina.org/docs/hooks-reference.html#useref)
  - [`useImperativeMethods`](https://react.docschina.org/docs/hooks-reference.html#useimperativemethods)
  - [`useMutationEffect`](https://react.docschina.org/docs/hooks-reference.html#usemutationeffect)
  - [`useLayoutEffect`](https://react.docschina.org/docs/hooks-reference.html#uselayouteffect)
- 使用 [`useContext`](https://react.docschina.org/docs/hooks-reference.html#usecontext) 可以订阅 React context 而不用引入嵌套：

  ```react
  function Example() {
    const locale = useContext(LocaleContext);
    const theme = useContext(ThemeContext);
    // ...
  }
  ```

- [`useReducer`](https://react.docschina.org/docs/hooks-reference.html#usereducer) 则允许你使用一个 reducer 来管理一个复杂组件的局部状态（local state）：

  ```react
  function Todos() {
    const [todos, dispatch] = useReducer(todosReducer);
    // ...
  ```

# Hooks FAQ

- https://segmentfault.com/a/1190000017057144#articleHeader9
- https://juejin.im/post/5be6150551882511a8526955
- https://juejin.im/post/5c230aa2e51d4529355bc2e0
- https://medium.com/@unbug/ive-completely-rewritten-two-projects-with-react-hooks-here-is-the-good-and-the-ugly-48c28a103f52

# Hooks 实践

## 指导思想

- <https://zhuanlan.zhihu.com/p/85969406>

## 接受 Props

- ```react
  import React from 'react';
  import {connect} from 'react-redux'
  import PropTypes from 'prop-types';
  
  import './Header.less'
  
  function Header (props){
    return (
      <header className='n-back_header'>
        DUAL N-BACK N={props.playLevel}
      </header>
    );
  }
  
  Header.propTypes = {
    playLevel:PropTypes.number.isRequired
  };
  
  export default connect(
    (state)=>({
      playLevel:state.playInfo.playLevel,
    }),
    {}
  )(Header)
  ```
- 以参数形式接受即可，class 的 props 自动变成组件的属性， 函数组件的 props 则是以参数形式，自动传入

## Ref

- 函数式组件，无法像 class 那样使用 ref ， 需要使用 useRef()
- **你不能在函数式组件上使用 ref 属性**，因为它们没有实例，无法把 ref 对象作为实例的属性保存下来
- 可能是为了保持一致性，useRef() 返回一个对象，对象的.current 属性才真是的 DOM 节点
- ![1552564808864](/img/user/programming/font-end/framework/react/react-advanced/1552564808864.png)

## State

- 同时更新多个 state 只能分别调用？
- state 都是 constent 不可以使用 state++
- // 这样是否 ok 呢？state 是常量，使用了 shift() 方法
- 传入一个新的 state

## useEffect

- 逻辑要放在一个函数里面执行，虽然 setTimeout 也是要给函数，但还是要用箭头函数包一层，一方面结构更加清晰，另一方面可以保证正确的运行，因为 setTimeout 是一个运行函数，而不是定义函数
- ```react
  useEffect(() => {
    // 准备时间 1000ms
    setTimeout(() => {
      randomActive()
      // 3000ms
      let randomInterval = setInterval(() => {
        
        // 判断是否需要继续游戏
        if(matchTime>4){
          // 游戏结束，清除定时器
          clearInterval(randomInterval)
  
          console.log(visualMatchCorrect,visualMatchMistake);
  
          // 调用游戏结算函数
          const playResult = {visualMatchCorrect,visualMatchMistake}
          const {playResultList} = props
          playSettlement(playResultList,playResult,playLevel)
          
          // 跳转到result界面
          props.history.push('/result')
        }else{
          // 继续游戏
          randomActive()
        }
      },3000)
    },1000)
  },[]) 
  ```

## Interval

- https://overreacted.io/zh-hans/making-setinterval-declarative-with-react-hooks/
- https://zhuanlan.zhihu.com/p/57239068
- https://cloud.tencent.com/developer/article/1398858
- 通过控制 useInterval 的 delay 可以实现各种延时 定时 贝塞尔速度等
- 如果需要使用多个 setState，由于，对于 state 的引用一直时在闭包里的，如果使用...运算符会覆盖前面的 set 操作，所以需要在一开始 用...运算符做一个潜复制，然后每次 set 就改变复制的对象，最后在统一 set 给全局的 state
- 又或者是直接解构赋值，分别操作每个属性，最后合成新的对象，用于 set
- <https://zhuanlan.zhihu.com/p/84697185>
