---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-09-03-Sat, 10:08:06 pm","date-modified":"2023-09-02-Sat, 7:51:54 pm","permalink":"/programming/front-end/framework/react/redux-basic/","dgPassFrontmatter":true}
---


# Redux 理解

## 学习文档

- 英文文档: <https://redux.js.org/>
- 中文文档: <http://www.redux.org.cn/>
- <https://cn.redux.js.org/docs/react-redux/api.html>
- Github: <https://github.com/reactjs/redux>

## 概述

redux 是一个独立专门用于做状态管理的 JS 库 (不是 react 插件库)

它可以用在 react, angular, vue 等项目中, 但基本与 react 配合使用

作用: 集中式管理 react 应用中多个组件共享的状态

## Redux 工作流程

![1547027805153](/img/user/programming/front-end/framework/react/redux-basic/1547027805153.png)

Action Creators 是一个工厂函数

## 什么情况下需要使用 Redux

总体原则: 能不用就不用, 如果不用比较吃力才考虑使用

1. 某个组件的状态，需要共享
2. 某个状态需要在任何地方都可以拿到
3. 一个组件需要改变全局状态
4. 一个组件需要改变另一个组件的状态

# Redux 三个核心概念

## Action

标识要执行的行为的对象, 包含 2 个方面的属性

  - type: 标识属性, 值为字符串, 唯一, 必要属性
  - data / playload: 数据属性, 值类型任意, 可选属性

  ```js
  const action = {
    type: 'INCREMENT',
    data: 2
  }
  
  // Action Creator(创建Action的工厂函数)
  const increment = (number) => ({type: 'INCREMENT', data: number})
  // 返回一个action对象，所以需要小括号包装一下
  ```

**注意**：每次执行 action，都会把所有的 reducer 都跑一遍

## Reducer

根据旧的 `state` 和 `action`, 产生新的 `state` 的 **纯函数**

可以在 reducer 定义 state 的初始值（以形参默认值的形式），其实 reducer 就是 store 对象的属性，因此每一个 reducer 函数的返回值就是 store 对象对应的属性值

  ```js
  export default function counter(state = 0, action) {
    switch (action.type) {
      case 'INCREMENT':
        return state + action.data
      case 'DECREMENT':
        return state - action.data
      default:
        return state
  	}
  }
  ```

**注意：** 返回一个新的状态，不要修改原来的状态

  > 使用 [`Object.assign()`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) 新建了一个副本。不能这样使用 `Object.assign(state, { visibilityFilter: action.filter })`，因为它会改变第一个参数的值。你 **必须** 把第一个参数设置为空对象。你也可以开启对 ES7 提案 [对象展开运算符](https://www.redux.org.cn/docs/recipes/UsingObjectSpreadOperator.html) 的支持, 从而使用 `{ ...state, ...newState }` 达到相同的目的。

老 DOM 树和新 DOM 树，对比更新局部渲染，所以都是用的 setState，没有必要，也不应该覆盖旧的数据

## Normalize

开发复杂的应用时，不可避免会有一些数据相互引用。建议你尽可能地把 state 范式化，不存在嵌套。把所有数据放到一个对象里，每个数据以 ID 为主键，不同实体或列表间通过 ID 相互引用数据。把应用的 state 想像成数据库。这种方法在 [normalizr](https://github.com/gaearon/normalizr) 文档里有详细阐述。例如，实际开发中，在 state 里同时存放 `todosById: { id -> todo }` 和 `todos: array<id>` 是比较好的方式，本文中为了保持示例简单没有这样处理。

## Store

将 `action` 与 `state`,`reducer` 联系在一起的对象

如何得到此对象?

```react
import {createStore} from 'redux'
import reducer from './reducers'
const store = createStore(reducer)
```

> createStore 内部会第一次调用 reducer，得到初始的 state，所以：state 的初始值可以在 reducer 内部定义的。
>
> 也可以通过传递第二个参数 preloadedState 定义

**此对象的功能：**

- 维持应用的 state
- getState(): 得到 state
- dispatch(action): 分发 action, 触发 reducer 调用, 产生新的 state
- subscribe(listener): 注册监听, 当产生了新的 state 时, 自动调用监听 **函数**（render）

# Redux 三大原则

## 单一数据源

整个应用的 state 被储存在一棵 object tree 中，并且这个 object tree 只存在于唯一一个 store 中

## State 是只读的

唯一改变 state 的方法就是触发 action，action 是一个用于描述已发生事件的普通对象

**注意，action 没有任何魔法**，强制使用 action 来描述所有变化带来的好处是可以清晰地知道应用中到底发生了什么，action 就像是描述发生了什么的指示器。

## 使用纯函数来执行修改

为了描述 action 如何改变 state tree ，你需要编写 reducers

**再次地，没有任何魔法**，reducer 只是一个接收 state 和 action，并返回新的 state 的函数。

## 总结

这差不多就是 Redux 思想的全部。我们没有使用任何 Redux 的 API。Redux 里有一些工具来简化这种模式，但是主要的想法是如何根据这些 action 对象来更新 state，而且 90% 的代码都是纯 JavaScript，没用 Redux、Redux API 和其它魔法

> 如果你以前使用 Flux，那么你只需要注意一个重要的区别。Redux 没有 Dispatcher 且不支持多个 store。相反，只有一个单一的 store 和一个根级的 reduce 函数（reducer）。随着应用不断变大，你应该把根级的 reducer 拆成多个小的 reducers，分别独立地操作 state 树的不同部分，而不是添加新的 stores。这就像一个 React 应用只有一个根级的组件，这个根组件又由很多小组件构成。
>
> 用这个架构开发计数器有点杀鸡用牛刀，但它的美在于做复杂应用和庞大系统时优秀的扩展能力。由于它可以用 action 追溯应用的每一次修改，因此才有强大的开发工具。如录制用户会话并回放所有 action 来重现它。

# Redux 的核心 API

## createStore()

作用: 创建包含指定 reducer 的 store 对象

  ```js
  import {createStore} from 'redux'
  import counter from './reducers/counter'
  const store = createStore(counter)
  ```

createStore() 的第二个参数是可选的, 用于设置 state 初始状态。这对开发同构应用时非常有用，服务器端 redux 应用的 state 结构可以与客户端保持一致, 那么客户端可以将从网络接收到的服务端 state 直接用于本地数据初始化。

  ```js
  let store = createStore(todoApp, window.STATE_FROM_SERVER)
  ```

## Store 对象

redux 库最核心的管理对象, 它内部维护着:

  - state
  - reducer

核心方法:

```JS
store.getState()
store.dispatch({type:'INCREMENT', number})
store.subscribe(render)
```

## applyMiddleware()

作用：应用上基于 redux 的中间件 (插件库)

```js
import {createStore, applyMiddleware} from 'redux'
import thunk from 'redux-thunk'  // redux异步中间件
const store = createStore(
	counter,
	applyMiddleware(thunk) // 应用上异步中间件
)
```

  ```js
  if (typeof preloadedState === 'function' && typeof enhancer === 'undefined') {
    enhancer = preloadedState
    preloadedState = undefined
  }
  ```

## combineReducers()

作用：合并多个 reducer 函数

  ```js
  export default combineReducers({
    user,
    chatUser,
    chat
  })
  ```

原生 JS 的写法

  ```js
  function todoApp(state = {}, action) {
    return {
      todos: todos(state.todos, action),
      visibilityFilter: visibilityFilter(state.visibilityFilter, action)
    }
  }
  ```

# 参考模板

npm install --save redux

```js
/*
action对象的type常量名称模块
redux/action-types.js
*/
export const INCREMENT = 'increment'
export const DECREMENT = 'decrement'
```

```js
/*
action creator模块
redux/actions.js
*/
import {INCREMENT, DECREMENT} from './action-types'

export const increment = number => ({type: INCREMENT, data:number})
export const decrement = number => ({type: DECREMENT, data:number})
```

```js
/*
根据老的state和指定action, 处理返回一个新的state
redux/reducers.js
*/
import {INCREMENT, DECREMENT} from './action-types'

export function counter(state = 0, action) {
	console.log('counter', state, action)
	switch (action.type) {
	  case INCREMENT:
		return state + action.data
	  case DECREMENT:
		return state - action.data
	  default:
		return state
	}
}
```

```js
// redux/store.js
import {createStore} from 'redux'
import {counter} from './reducers'

// 根据counter函数创建store对象
const store = createStore(counter)

export default store
```

订阅监听，一旦状态发生改变, 自动重新渲染

```js
// index.js
import React from 'react'
import ReactDOM from 'react-dom'

import App from './components/app'
import store from './redux/store'

// 定义渲染根组件标签的函数
const render = () => {
    ReactDOM.render(
      <App store={store}/>,
      document.getElementById('root')
    )
  }
  // 初始化渲染
  render()
  
  // 注册(订阅)监听, 一旦状态发生改变, 自动重新渲染
  store.subscribe(render)
}
```

数据在 app 内，要进行 redux 的更新流程：引入 store

得到 state：`count = this.props.store.getState()`

更新 state：

工厂函数创建的 action 对象：`this.props.store.dispatch(actions.increment(number))`

直接定义的 action 对象：`this.props.store.dispatch({type:INCREMENT,data:number})`

```jsx
  /*
  应用组件
   */
  import React, {Component} from 'react'
  import PropTypes from 'prop-types'
  //取出所有的子模块
  import * as actions from '../redux/actions'
  
  export default class App extends Component {
    static propTypes = {
      store: PropTypes.object.isRequired,
    }
  
    increment = () => {
      const number = this.refs.numSelect.value * 1
      this.props.store.dispatch(actions.increment(number))
    }
  
    decrement = () => {
      const number = this.refs.numSelect.value * 1
      this.props.store.dispatch(actions.decrement(number))
    }
  
    incrementIfOdd = () => {
      const number = this.refs.numSelect.value * 1
  
      let count = this.props.store.getState()
      if (count % 2 === 1) {
        this.props.store.dispatch(actions.increment(number))
      }
    }
  
    incrementAsync = () => {
      const number = this.refs.numSelect.value * 1
      setTimeout(() => {
        this.props.store.dispatch(actions.increment(number))
      }, 1000)
    }
  
    render() {
      return (
        <div>
          <p>
            click {this.props.store.getState()} times {' '}
          </p>
          <select ref="numSelect">
            <option value="1">1</option>
            <option value="2">2</option>
            <option value="3">3</option>
          </select>{' '}
          <button onClick={this.increment}>+</button>
          {' '}
          <button onClick={this.decrement}>-</button>
          {' '}
          <button onClick={this.incrementIfOdd}>increment if odd</button>
          {' '}
          <button onClick={this.incrementAsync}>increment async</button>
        </div>
      )
    }
  }
```

## 缺点

1. redux 与 react 组件的代码耦合度太高
2. 编码不够简洁

# Redux 异步编程

## 异步数据流

默认情况下，[`createStore()`](https://www.redux.org.cn/docs/api/createStore.html) 所创建的 Redux store 没有使用 [middleware](https://www.redux.org.cn/docs/advanced/Middleware.html)，所以只支持 [同步数据流](https://www.redux.org.cn/docs/basics/DataFlow.html)。

你可以使用 [`applyMiddleware()`](https://www.redux.org.cn/docs/api/applyMiddleware.html) 来增强 [`createStore()`](https://www.redux.org.cn/docs/api/createStore.html)。虽然这不是必须的，但是它可以帮助你 [用简便的方式来描述异步的 action](https://www.redux.org.cn/docs/advanced/AsyncActions.html)。

像 [redux-thunk](https://github.com/gaearon/redux-thunk) 或 [redux-promise](https://github.com/acdlite/redux-promise) 这样支持异步的 middleware 都包装了 store 的 [`dispatch()`](https://www.redux.org.cn/docs/api/Store.html#dispatch) 方法，以此来让你 dispatch 一些除了 action 以外的其他内容，例如：函数或者 Promise。你所使用的任何 middleware 都可以以自己的方式解析你 dispatch 的任何内容，并继续传递 actions 给下一个 middleware。比如，支持 Promise 的 middleware 能够拦截 Promise，然后为每个 Promise 异步地 dispatch 一对 begin/end actions。

你可以使用任意多异步的 middleware 去做你想做的事情，但是需要使用普通对象作为最后一个被 dispatch 的 action ，来将处理流程带回同步方式

下载 redux 插件 (异步中间件)

`npm install --save redux-thunk`

  ```jsx
  import {createStore, applyMiddleware} from 'redux'
  import thunk from 'redux-thunk'
  // 根据counter函数创建store对象
  const store = createStore(
    counter,
    applyMiddleware(thunk) // 应用上异步中间件
  )
  ```

同步的 actions 返回的都是一个对象, 默认只能返回一个对象

异步的 actions 返回的是一个函数

- 在异步 actoin 函数的最后，调用一个匹配的同步 action
- 有了 thunk 中间件，才可以支持 dispatch 一个函数
- 本质上：是函数的形式是为了执行异步操作，最后还是通过 dispatch 分发同步 action

  ```jsx
  // 异步`action creator`(返回一个函数)
  export const incrementAsync = number => {
    return dispatch => {
      setTimeout(() => {
        dispatch(increment(number))
      }, 1000)
    }
  }
  
  // 传入的dispatch参数是为了可以在返回值内部dispatch一个普通的action
  // if (typeof action === 'function') {
  //   return action(dispatch, getState, extraArgument);
  // }
  
  //小括号简洁语法，但是因为结构本身就比较复杂，一般还是特地加一个return，用于强调
  export const incrementAsync = number => （
    dispatch => {
      setTimeout(() => {
        dispatch(increment(number))
      }, 1000)
    }
  ）
  ```

  ```jsx
  incrementAsync = () => {
    const number = this.refs.numSelect.value*1
    this.props.incrementAsync(number)
  }
  ```

  ```jsx
  import {increment, decrement, incrementAsync} from '../redux/actions'
  // 向外暴露连接App组件的包装组件
  export default connect(
    state => ({count: state}),
    {increment, decrement, incrementAsync}
  )(Counter)
  ```

# Redux 调试工具

npm install --save-dev redux-devtools-extension

  ```js
  // index.js
  import { composeWithDevTools } from 'redux-devtools-extension'
  
  const store = createStore(
    counter,
    composeWithDevTools(applyMiddleware(thunk)) 
  )
  ```

![1547045446583](/img/user/programming/front-end/framework/react/redux-basic/1547045446583.png)

左边可以查看到 reducer 的调用

# Redux 实战

## 改造 Comments

shift() 会影响原数组，所以用 `[action.data,...state]`

splice 返回的是被删除的元素，所以用 `state.filter((comment,index)=> index!==action.data)`

## 多个 Reducer 怎么处理

- redux/reducers.js

  ```js
  import {combineReducers} from 'redux'
  
  import {...} from './action-types'
  
  const initComments = []
  
  function comments(state = initComments, action) {
    switch ...
  }
  function counter(state = initComments, action) {
    switch ...
  }  
  
  export default combineReducers({
    comments,
    counter
  })
    
  //redux向外暴露的state结构：  
  //{counter:2,comments:[]}
  //所以app也要做响应的改变  
  ```

- redux/store.js

  ```js
  //视作形参
  import reducers from './reducers'
  
  // 根据counter函数创建store对象
  export default createStore(
    reducers,
    composeWithDevTools(applyMiddleware(thunk)) // 应用上异步中间件
  )
  ```

- app.js

  ```js
  export default connect(
    state=>{comments:state.comments},
    {addComments,deleteComments,getComments}
  )(App)
  ```

## 发起异步请求拿到数据之后执行函数

方式一：通过 state 更新之后，重新渲染页面的时候执行函数，但是初始渲染的时候也会执行一次，只能再通过条件判断避免

尝试失败：因为异步 action 返回的函数内部是没有 return 的，所以异步 action 只能拿到 undefined

尝试成功：要记住每一层函数都要 return，不然就会 undefined

# React-Redux

## 理解

- <https://react-redux.js.org/api/batch>
- <https://cn.redux.js.org/docs/react-redux/api.html>
- <https://juejin.im/post/5c230aa2e51d4529355bc2e0>
- 一个 react 插件库
- 专门用来简化 react 应用中使用 redux

## React-Redux 将所有组件分成两大类

**UI 组件**

- 只负责 UI 的呈现，不带有任何业务逻辑
- 通过 props 接收数据 (一般数据和函数)
- 不使用任何 Redux 的 API
- 一般保存在 components 文件夹下

**容器组件**

- 负责管理数据和业务逻辑，不负责 UI 的呈现
- 使用 Redux 的 API
- 一般保存在 containers 文件夹下

## 相关 API

## Provider

所有组件都可以得到 state 数据

```jsx
<Provider store={store}>
   <App />
</Provider>
```

## connect()

用于包装 UI 组件生成容器组件

```jsx
  import { connect } from 'react-redux'
     connect(
       mapStateToprops,
       mapDispatchToProps
  )(Counter)
```

调用 connect 函数返回了一个 **高阶组件生成器**，而这个生成器会基于原始组件生成一个全新的组件，并给这个组件添加额外的 props。

在构造一个高阶组件生成器时，connect 最多接受 4 个参数，分别如下

- `[mapStateToProps(state, [ownProps]): stateProps]`（类型：函数）：接受完整的 Redux 状态树作为参数，返回当前组件相关部分的状态树，返回对象的所有 key 都会成为组件的 props。
- `[mapDispatchToProps(dispatch, [ownProps]): dispatchProps]` （类型：对象或函数）：接受 Redux 的 dispatch 方法作为参数，返回当前组件相关部分的 action creator，并可以在这里将 action creator 与 dispatch 绑定，减少冗余代码。
- `[mergeProps(stateProps, dispatchProps, ownProps): props] `（类型：函数）：如果指定这个函数，你将分别获得 mapStateToProps、mapDispatchToProps 返回值以及当前组件的 props 作为参数，最终返回你期望的、完整的 props。
- `[options]`（类型：对象）：可选的额外配置项，有以下两项。
	- [pure = true] （类型：布尔）：该值设为 true 时，将为组件添加 shouldComponentUpdate()
	  生命周期函数，并对 mergeProps 方法返回的 props 进行浅层对比。
	- [withRef = false]（类型：布尔）：若设为 true，则为组件添加一个 ref 值，后续可
	  以使用 getWrappedInstance() 方法来获取该 ref，默认为 false。

## mapStateToprops()

将外部的数据（即 state 对象）转换为 UI 组件的标签属性

- 接受默认参数 state
- 将 state 的属性，转换为标签的属性

```jsx
  const mapStateToprops = function (state) {
    return {
    value: state
    }
  }
  
  const mapStateToprops = state => ({count: state}) //小括号不能忘了
```

## mapDispatchToProps()

- 将分发 action 的函数转换为 UI 组件的标签属性
- 简洁语法可以直接指定为 actions 对象或包含多个 action 方法的对象

```jsx
  // 向外暴露连接App组件的包装组件
  export default connect(
    state => ({count: state}), //自动结构变成Counter的属性
    {increment, decrement}
    //increment：increment，一个是属性名（与组件引入的prop一致），一个是属性值（与action一致）
  )(Counter)
```

## 参考模板

下载依赖包

`npm install --save react-redux`

redux/action-types.js - 不变

redux/actions.js - 不变

redux/reducers.js - 不变

index.js - 不需要订阅监听了

把 store 传给 provider

```jsx
  import React from 'react'
  import ReactDOM from 'react-dom'
  import {createStore} from 'redux'
  import {Provider} from 'react-redux'
  
  import App from './containers/app'
  import {counter} from './redux/reducers'
  
  // 根据counter函数创建store对象
  const store = createStore(counter)
  
  // 定义渲染根组件标签的函数
  ReactDOM.render(
    (
      <Provider store={store}>
        <App />
      </Provider>
    ),
    document.getElementById('root')
```

components/counter.jsx

- UI 组件: 不包含任何 redux API
- 不在需要 store 的 dispatch 了，直接 `this.props.increment(number)`

```jsx
  /*
  UI组件: 不包含任何redux API
   */
  import React from 'react'
  import PropTypes from 'prop-types'
  
  export default class Counter extends React.Component {
  
    static propTypes = {
      count: PropTypes.number.isRequired,
      increment: PropTypes.func.isRequired,
      decrement: PropTypes.func.isRequired
    }
  
    increment = () => {
      const number = this.refs.numSelect.value * 1
      this.props.increment(number)
    }
  
    decrement = () => {
      const number = this.refs.numSelect.value * 1
      this.props.decrement(number)
    }
  
    incrementIfOdd = () => {
      const number = this.refs.numSelect.value * 1
      let count = this.props.count
      if (count % 2 === 1) {
        this.props.increment(number)
      }
    }
  
    incrementAsync = () => {
      const number = this.refs.numSelect.value * 1
      setTimeout(() => {
        this.props.increment(number)
      }, 1000)
    }
  
    render() {
      return (
        <div>
          <p>
            click {this.props.count} times {' '}
          </p>
          <select ref="numSelect">
            <option value="1">1</option>
            <option value="2">2</option>
            <option value="3">3</option>
          </select>{' '}
          <button onClick={this.increment}>+</button>
          {' '}
          <button onClick={this.decrement}>-</button>
          {' '}
          <button onClick={this.incrementIfOdd}>increment if odd</button>
          {' '}
          <button onClick={this.incrementAsync}>increment async</button>
        </div>
      )
    }
  }
```

containters/app.jsx

- connect() 方法传递的对象，自动解构赋值变成 Counter 的属性
- {...运算符}
- 类比一下：`ReactDOM.render(<Person {...person}/>, document.getElementById('example1'))`

```jsx
  /*
  包含Counter组件的容器组件
   */
  import React from 'react'
  // 引入连接函数
  import {connect} from 'react-redux'
  // 引入action函数
  import {increment, decrement} from '../redux/actions'
  
  import Counter from '../components/counter'
  
  // 向外暴露连接App组件的包装组件
  export default connect(
    state => ({count: state}), //自动结构变成Counter的属性
    {increment, decrement}
    //increment：increment，一个是属性名（与组件引入的prop一致），一个是属性值（与action一致）
  )(Counter)
```

## 结果：

![1547037866613](/img/user/programming/front-end/framework/react/redux-basic/1547037866613.png)

provider 有 store 属性，里面有着 store 的全部方法

App 组件，经过 connect() 包装，获得了传入的属性，类比上次包装 MyNavLink 组件，通过包装组件，传入自己定义的属性

## 问题

1. redux 默认是不能进行异步处理的
2. 应用中又需要在 redux 中执行异步任务 (ajax, 定时器)

## 注意

connect 之后的 state 和 action 都需要通过 props 来获取
