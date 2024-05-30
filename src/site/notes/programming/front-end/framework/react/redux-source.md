---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-09-02-Sat, 7:50:31 pm","date-modified":"2023-09-02-Sat, 7:51:45 pm","permalink":"/programming/front-end/framework/react/redux-source/","dgPassFrontmatter":true}
---


# Redux 源码

参考：<https://juejin.im/post/5b9617835188255c781c9e2f#heading-5>

redux 本身有哪些作用？我们先来快速的过一下 redux 的核心思想（工作流程）：

+ 将状态统一放在一个 state 中，由 store 来管理这个 state。
+ 这个 store 按照 reducer 的“shape”（形状）创建。
+ reducer 的作用是接收到 action 后，输出一个新的状态，对应地更新 store 上的状态。
+ 根据 redux 的原则指导，外部改变 state 的最佳方式是通过调用 store 的 dispatch 方法，触发一个 action，这个 action 被对应的 reducer 处理，完成 state 更新。
+ 可以通过 subscribe 在 store 上添加一个监听函数。每当调用 dispatch 方法时，会执行所有的监听函数。
+ 可以添加中间件，处理副作用。

在这个工作流程中，redux 需要提供的功能是：

+ 创建 store，即：`createStore()`
+ 创建出来的 store 提供 `subscribe`，`dispatch`，`getState` 这些方法。
+ 将多个 reducer 合并为一个 reducer，即：`combineReducers()`
+ 应用中间件，即 `applyMiddleware()`

## createStore()

这个函数的大致结构是这样：

  ```js
function createStore(reducer, preloadedState, enhancer) {
    if(enhancer是有效的){  // 这个我们后面会解释，可以先忽略
        return enhancer(createStore)(reducer, preloadedState)
    } 
    
    let currentReducer = reducer // 当前store中的reducer，是一个函数，聚合了所有子reducer
    let currentState = preloadedState // 传入的默认值
    let currentListeners = [] // 当前store中放置的监听函数
    let nextListeners = currentListeners // 下一次dispatch时的监听函数
    // 注意：当我们新添加一个监听函数时，只会在下一次dispatch的时候生效。
    
    //...
    
    // 获取state
    function getState() {
        //...
    }
    
    // 添加一个监听函数，每当dispatch被调用的时候都会执行所有监听函数
    function subscribe() {
        //...
    }
    
    // 触发一个action，调用reducer，得到的新的state，并且执行所有添加到store中的监听函数。
    function dispatch() {
        //...
    }
   
    //...
    
    //dispatch一个用于初始化的action，相当于调用一次reducer
    //然后将reducer中的子reducer的初始值也获取到
    //详见下面reducer的实现。
    
    
    return {
        dispatch,
        subscribe,
        getState,
        //下面两个是主要面向库开发者的方法，暂时先忽略
        //replaceReducer,
        //observable
    }
}
  ```

可以看出，createStore 方法创建了一个 store，但是并没有直接将这个 store 的状态 state 返回，而是返回了一系列方法，外部可以通过这些方法（getState）获取 state，或者间接地（通过调用 dispatch）改变 state。

至于 state 呢，被存在了闭包中。

我们再来详细的看看每个模块是如何实现的（为了让逻辑更清晰，省略了错误处理的代码）：

### getState()

```js
  function getState() {
      return currentState
  }
```

简单到发指。其实这很像面向对象编程中封装只读属性的方法，只提供数据的 getter 方法，而不直接提供 setter。（虽然这里返回的是一个 state 的引用，你可以直接修改 state，但是一般来说，redux 不建议这样做。）

### subscribe()

```js
  /** 
   * @param {function} listener 
   */
  function subscribe(listener) {
      // 添加到监听函数数组，
      // 注意：我们添加到了下一次dispatch时才会生效的数组
      nextListeners.push(listener)
      
      let isSubscribe = true //设置一个标志，标志该监听器已经订阅了
      // 返回取消订阅的函数，即从数组中删除该监听函数
      return function unsubscribe() {
          if(!isSubscribe) {
              return // 如果已经取消订阅过了，直接返回
          }
          
          isSubscribe = false
          // 从下一轮的监听函数数组（用于下一次dispatch）中删除这个监听器。
          const index = nextListeners.indexOf(listener)
          nextListeners.splice(index, 1)
      }
  }
```

subscribe 返回的是一个取消订阅的方法。取消订阅是非常必要的，当添加的监听器没用了之后，应该从 store 中清理掉。不然每次 dispatch 都会调用这个没用的监听器。

### dispatch() @@@

```js
function dispatch(action) {
    //调用reducer，得到新state，得到了却没有使用，没有用，仅仅是更新了state
    currentState = currentReducer(currentState, action);
  
    //更新监听数组
    currentListeners = nextListeners;
    //调用监听数组中的所有监听函数
    for(let i = 0; i < currentListeners.length; i++) {
      const listener = currentListeners[i];
      listener();
    }
    // return action 的意义是什么呢？
    return action
  }
```

createStore 这个方法的基本功能我们已经实现了，但是调用 createStore 方法需要提供 reducer，让我们来思考一下 reducer 的作用。

## Redux 最基本的实现

### action、reducer

由于 action 和 reducer 都没有任何魔法，我们直接定义即可

  ```js
/*
action对象的type常量名称模块
*/
export const INCREMENT = 'increment'
export const DECREMENT = 'decrement'

/*
action creator
*/
export const increment = number => ({type: INCREMENT, data:number})
export const decrement = number => ({type: DECREMENT, data:number})

/*
根据老的state和指定action, 处理返回一个新的state
 */
export function count(state = 0, action) {
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

### Store

为了根据 reducers 生成 store，我们需要定义 creatStore 方法

src/lib/redux/creatStore.js

  ```js
/**
 * @param {Function} reducer 
 * @param {any} [preloadedState] 
 * @param {Function} [enhancer] 
 * @returns {Store}
 */
export default function createStore(reducer, preloadedState) {

  let currentReducer = reducer // 当前store中的reducer，是一个函数，聚合了所有子reducer
  let currentState = preloadedState // 当前store中存储的状态，state默认值
  let currentListeners = [] // 当前store中放置的监听函数
  let nextListeners = currentListeners // 下一次dispatch时的监听函数
  // 注意：当我们新添加一个监听函数时，只会在下一次dispatch的时候生效。

  // 获取state
  function getState() {
    return currentState
  }

/**
 * @param {function} listener 
 */
  function subscribe(listener) {
    // 添加到监听函数数组，
    // 注意：我们添加到了下一次dispatch时才会生效的数组
    nextListeners.push(listener)

    let isSubscribe = true //设置一个标志，标志该监听器已经订阅了
    // 返回取消订阅的函数，即从数组中删除该监听函数
    return function unsubscribe() {
      if (!isSubscribe) {
        return // 如果已经取消订阅过了，直接返回
      }

      isSubscribe = false
      // 从下一轮的监听函数数组（用于下一次dispatch）中删除这个监听器。
      const index = nextListeners.indexOf(listener)
      nextListeners.splice(index, 1)
    }
  }

  function dispatch(action) {
    //调用reducer，得到新state
    currentState = currentReducer(currentState, action);

    //更新监听数组
    currentListeners = nextListeners;
    //调用监听数组中的所有监听函数
    for (let i = 0; i < currentListeners.length; i++) {
      const listener = currentListeners[i];
      listener();
    }
  }

  // dispatch一个用于初始化的action，相当于调用一次reducer
  // 因为reducer的switch定义了default值，所以可以成功返回
  dispatch({ type: 'init' })

  return {
    dispatch,
    subscribe,
    getState,
    //下面两个是主要面向库开发者的方法，暂时先忽略
    //replaceReducer,
    //observable
  }
}
  ```

- redux/store.js

  ```js
  import {createStore} from '../lib/redux'
  import {count} from './redux/reducers'
  
  // 根据counter函数创建store对象
  const store = createStore(count)
  // 此时store已经有值了，0
  
  export default store
  ```

### 监听

仅仅是 store 的值发生了变化是没有意义的，我们必须要通知 react 重新渲染

  ```jsx
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
  ```

每次 dispatch 了 action，render 就会重新执行一次，重新渲染

子组件想要获取到 store，直接引入 store 即可，在这里我们使用 context 传输 store

通过 container 给组件传递 state 和 store，以及相应的 action，组件通过 props 调用

layouts/Frame.jsx

  ```jsx
import React from 'react';
import { Switch, Route, HashRouter } from 'react-router-dom'; 

import ReduxDemo from '../containers/ReduxDemo'; 

export let StoreContext = React.createContext(null)
const Frame = (props) => {
  return (
    <StoreContext.Provider value={props.store}>
      <HashRouter>
        <Switch>
          <Route exact path='/redux-demo' component={ReduxDemo}></Route>
        </Switch>
      </HashRouter>
    </StoreContext.Provider>
  );
};

export default Frame;
  ```

containers/ReduxDemo.jsx

  ```jsx
import React,{useContext} from 'react';
import Count from '../components/ReduxDemo/Count'
import Counter from '../components/ReduxDemo/Counter'
import {StoreContext} from '../layouts/Frame'
import * as actions from '../redux/actions.js'
const ReduxDemo = () => {
  const store = useContext(StoreContext)
  return (
    <div>
      <Count state={store.getState()}></Count>
      <Counter store = {store} actions={actions}/>
    </div>
  );
};

export default ReduxDemo;
  ```

## combineReducers()

在理解 combineReducers 之前，我们先来想想 reducer 的功能：reducer 接受一个旧的状态和一个 action，当这个 action 被触发的时候，reducer 处理后返回一个新状态。

也就是说 ，reducer 负责状态的管理（或者说更新）。在实际使用中，我们应用的状态是可以分成很多个模块的，比如一个典型社交网站的状态可以分为：用户个人信息，好友列表，消息列表等模块。理论上，我们可以用一个 reducer 去处理所有状态的维护，但是这样做的话，我们一个 reducer 函数的逻辑就会太多，容易产生混乱。

因此我们可以将逻辑（reducer）也按照模块划分，每个模块再细分成各个子模块，开发完每个模块的逻辑后，再将 reducer 合并起来，这样我们的逻辑就能很清晰的组合起来。

对于我们的这种需求，redux 提供了 combineReducers 方法，可以把子 reducer 合并成一个总的 reducer。

来看看 redux 源码中 combineReducers 的主要逻辑：

  ```js
/** 
 * @param {Object} reducers 
 */

function combineReducers(reducers) {
  //先获取传入reducers对象的所有key
  const reducerKeys = Object.keys(reducers)
  const finalReducers = {} // 最后真正有效的reducer存在这里
  
  //下面从reducers中筛选出有效的reducer
  for(let i = 0; i < reducerKeys.length; i++){
      const key  = reducerKeys[i]
      
      if(typeof reducers[key] === 'function') {
          finalReducers[key] = reducers[key] 
      }
  }
  const finalReducerKeys = Object.keys(finalReducers);
  
    // 这里assertReducerShape函数做的事情是：
    // 检查finalReducer中的reducer接受一个初始action或一个未知的action时，是否依旧能够返回有效的值。
    let shapeAssertionError
  	try {
    	assertReducerShape(finalReducers)
  	} catch (e) {
    	shapeAssertionError = e
  	}
  
  //返回合并后的reducer
  return function combination(state= {}, action){
    //这里的逻辑是：
    //取得每个子reducer对应的state，与action一起作为参数给每个子reducer执行
    let hasChanged = false //标志state是否有变化
    let nextState = {}
    for(let i = 0; i < finalReducerKeys.length; i++) {
      //得到本次循环的子reducer
      const key = finalReducerKeys[i]
      const reducer = finalReducers[key]
      //得到该子reducer对应的旧状态
      const previousStateForKey = state[key]
      //调用子reducer得到新状态，如果reducer没有定义默认值，且action不匹配就会返回undefined
      const nextStateForKey = reducer(previousStateForKey, action)
      //存到nextState中（总的状态）
      nextState[key] = nextStateForKey
      //到这里时有一个问题:
      //就是如果子reducer不能处理该action，那么会返回previousStateForKey
      //也就是旧状态，当所有状态都没改变时，我们直接返回之前的state就可以了。
      hasChanged = hasChanged || previousStateForKey !== nextStateForKey
    }
    return hasChanged ? nextState : state
  }
} 
  ```

## 使用 combineReducers()

+ reducers.js

  ```js
  export default combineReducers({
    count,
  })
  ```

+ store.js

  ```js
  import {createStore} from '../lib/redux'
  // import {counter} from './reducers'
  import combineCounter from './reducers'
  
  // 根据counter函数创建store对象
  const store = createStore(combineCounter)
  
  export default store
  ```

+ count.jsx

  ```jsx
  import React from 'react';
  
  const Count = (props) => {
    console.log(props.state);
    return (
      <div>
        {props.state.count}
      </div>
    );
  };
  
  export default Count;
  ```

reducer 实际上就是对应着 state 的一个个键名，因为 combineReducers 之后被合成到了一个大的对象里，所以需要以 reducer 函数名作为属性名去访问

  ```jsx
//使用combine之前
props.state // count本身的值，这里的话就是number类型的数值
// 使用combine之后
props.state // {count:<number>}
  ```

### bindActionCreators()

<https://juejin.im/post/5b13fe155188257d6c046728>

# 中间件

相对于 Express 或者 Koa 的 middleware，Redux middleware 被用于解决不同的问题，但其中的概念是类似的。**它提供的是位于 action 被发起之后，到达 reducer 之前的扩展点。** 你可以利用 Redux middleware 来进行日志记录、创建崩溃报告、调用异步接口或者路由等等。

## 理解中间件 @@@

正因为 middleware 可以完成包括异步 API 调用在内的各种事情，了解它的演化过程是一件相当重要的事。我们将以记录日志和创建崩溃报告为例，引导你体会从分析问题到通过构建 middleware 解决问题的思维过程。

### 问题: 记录日志

使用 Redux 的一个益处就是它让 state 的变化过程变的可预知和透明。每当一个 action 发起完成后，新的 state 就会被计算并保存下来。State 不能被自身修改，只能由特定的 action 引起变化。

  ![img](http://i.imgur.com/BjGBlES.png)

我们如何通过 Redux 实现它呢？

### 尝试 #1: 手动记录

最直接的解决方案就是在每次调用 [`store.dispatch(action)`](https://www.redux.org.cn/docs/api/Store.html#dispatch) 前后手动记录被发起的 action 和新的 state。这称不上一个真正的解决方案，仅仅是我们理解这个问题的第一步。

假设，你在创建一个 Todo 时这样调用：

  ```js
store.dispatch(addTodo('Use Redux'))
  ```

为了记录这个 action 以及产生的新的 state，你可以通过这种方式记录日志：

  ```js
let action = addTodo('Use Redux')

console.log('dispatching', action)
store.dispatch(action)
console.log('next state', store.getState())
  ```

虽然这样做达到了想要的效果，但是你并不想每次都这么干。

### 尝试 #2: 封装 Dispatch

可以将上面的操作抽取成一个函数：

  ```js
function dispatchAndLog(store, action) {
  console.log('dispatching', action)
  store.dispatch(action)
  console.log('next state', store.getState())
}
  ```

然后用它替换 `store.dispatch()`:

  ```js
dispatchAndLog(store, addTodo('Use Redux'))
  ```

你可以选择到此为止，但是每次都要导入一个外部方法总归还是不太方便。

### 尝试 #3: Monkeypatching Dispatch

如果我们直接替换 store 实例中的 `dispatch` 函数会怎么样呢？Redux store 只是一个包含 [一些方法](https://www.redux.org.cn/docs/api/Store.html) 的普通对象，同时我们使用的是 JavaScript，因此我们可以这样实现 `dispatch` 的 monkeypatch：

  ```js
let next = store.dispatch
store.dispatch = function dispatchAndLog(action) {
  console.log('dispatching', action)
  // dispatch的返回值是action
  let result = next(action)
  console.log('next state', store.getState())
  // 返回action result 的意义是什么
  return result
}
  ```

这离我们想要的已经非常接近了！无论我们在哪里发起 action，保证都会被记录。Monkeypatching 令人感觉还是不太舒服，不过利用它我们做到了我们想要的。

### 问题: 崩溃报告

如果我们想对 `dispatch` 附加 **超过一个** 的变换，又会怎么样呢？

我脑海中出现的另一个常用的变换就是在生产过程中报告 JavaScript 的错误。全局的 `window.onerror` 并不可靠，因为它在一些旧的浏览器中无法提供错误堆栈，而这是排查错误所需的至关重要信息。

试想当发起一个 action 的结果是一个异常时，我们将包含调用堆栈，引起错误的 action 以及当前的 state 等错误信息通通发到类似于 [Sentry](https://getsentry.com/welcome/) 这样的报告服务中，不是很好吗？这样我们可以更容易地在开发环境中重现这个错误。

然而，将日志记录和崩溃报告分离是很重要的。理想情况下，我们希望他们是两个不同的模块，也可能在不同的包中。否则我们无法构建一个由这些工具组成的生态系统。

如果按照我们的想法，日志记录和崩溃报告属于不同的模块，他们看起来应该像这样：

  ```js
function patchStoreToAddLogging(store) {
  let next = store.dispatch
  store.dispatch = function dispatchAndLog(action) {
    console.log('dispatching', action)
    let result = next(action)
    console.log('next state', store.getState())
    return result
  }
}

function patchStoreToAddCrashReporting(store) {
  // 保存的引用可能是其他中间件包装过后的
  let next = store.dispatch
  store.dispatch = function dispatchAndReportErrors(action) {
    try {
      return next(action)
    } catch (err) {
      console.error('捕获一个异常!', err)
      Raven.captureException(err, {
        extra: {
          action,
          state: store.getState()
        }
      })
      throw err
    }
  }
}
  ```

如果这些功能以不同的模块发布，我们可以在 store 中像这样使用它们：

  ```js
patchStoreToAddLogging(store)
patchStoreToAddCrashReporting(store)
  ```

尽管如此，这种方式看起来还是不是够令人满意。

### 尝试 #4: 隐藏 Monkeypatching

Monkeypatching 本质上是一种 hack。“将任意的方法替换成你想要的”，此时的 API 会是什么样的呢？现在，让我们来看看这种替换的本质。 在之前，我们用自己的函数替换掉了 `store.dispatch`。如果我们不这样做，而是在函数中 **返回** 新的 `dispatch` 呢？

  ```js
function logger(store) {
  let next = store.dispatch

  // 我们之前的做法:
  // store.dispatch = function dispatchAndLog(action) {

  return function dispatchAndLog(action) {
    console.log('dispatching', action)
    let result = next(action)
    console.log('next state', store.getState())
    return result
  }
}
  ```

我们可以在 Redux 内部提供一个可以将实际的 monkeypatching 应用到 `store.dispatch` 中的辅助方法：

  ```js
function applyMiddlewareByMonkeypatching(store, middlewares) {
  middlewares = middlewares.slice()
  middlewares.reverse()

  // 在每一个 middleware 中变换 dispatch 方法。
  middlewares.forEach(middleware =>
    // 该middleware执行时，内部的next变量保存了上一个middleware执行过后store.dispatch的值，经过包装的dispacth函数，所以最终的输出结果是嵌套的
    store.dispatch = middleware(store)
  )
}
  ```

然后像这样应用多个 middleware：

  ```js
applyMiddlewareByMonkeypatching(store, [ logger, crashReporter ])
  ```

最后 dispatch 看起来是这样的

  ```js
// middlewares[0]
store.dispatch = logger(store)
/* 
	store.dispatch = function dispatchAndLog(action) {
    console.log('dispatching', action)
    let result = next(action) // 此处的next就是最原始的store.dispatch
    console.log('next state', store.getState())
    return result
  }
*/
// middlewares[1]
store.dispatch = logger2(store)
/*
	store.dispatch = function dispatchAndLog(action) {
    console.log('dispatching2', action)
    let result = next(action) // 此处的next就是上一个logger(store)的返回值
    console.log('next state2', store.getState())
    return result
  }
*/

// 最终看起来是这样的
store.dispatch = function (action){
  console.log('dispatching2', action)
  let result = (function(action){
    console.log('dispatching', action)
    let result = next(action) // 此处的next就是最原始的store.dispatch
    console.log('next state', store.getState())
    return result
  })(action)
  console.log('next state2', store.getState())
  return result
}
  ```

尽管我们做了很多，实现方式依旧是 monkeypatching。

因为我们仅仅是将它隐藏在我们的框架内部，并没有改变这个事实。

### 尝试 #5: 移除 Monkeypatching

为什么我们要替换原来的 `dispatch` 呢？当然，这样我们就可以在后面直接调用它，但是还有另一个原因：就是每一个 middleware 都可以操作（或者直接调用）前一个 middleware 包装过的 `store.dispatch`：

  ```js
// store.dispatch = middleware(store)
function logger(store) {
  // 这里的 next 指向前一个 middleware 返回的函数：经过包装之后的store.dispatch
  let next = store.dispatch

  return function dispatchAndLog(action) {
    console.log('dispatching', action)
    let result = next(action)
    console.log('next state', store.getState())
    return result
  }
}
  ```

将 middleware 串连起来的必要性是显而易见的。

如果 `applyMiddlewareByMonkeypatching` 方法中没有在第一个 middleware 执行时立即替换掉 `store.dispatch`，那么 `store.dispatch` 将会一直指向原始的 `dispatch` 方法。也就是说，第二个 middleware 依旧会作用在原始的 `dispatch` 方法。

但是，还有另一种方式来实现这种链式调用的效果。可以让 middleware 以方法参数的形式接收一个 `next()` 方法，而不是通过 store 的实例去获取。

  ```js
function logger(store) {
  // 此处的next是一个改造后的dispatch
  return function wrapDispatchToAddLogging(next) {
    return function dispatchAndLog(action) {
      console.log('dispatching', action)
      let result = next(action)
      console.log('next state', store.getState())
      return result
    }
  }
}
  ```

现在是 [“我们该更进一步”](http://knowyourmeme.com/memes/we-need-to-go-deeper) 的时刻了，所以可能会多花一点时间来让它变的更为合理一些。这些串联函数很吓人。ES6 的箭头函数可以使其 [柯里化](https://en.wikipedia.org/wiki/Currying) ，从而看起来更舒服一些:

  ```js
const logger = store => next => action => {
  console.log('dispatching', action)
  let result = next(action)
  console.log('next state', store.getState())
  return result
}

const crashReporter = store => next => action => {
  try {
    return next(action)
  } catch (err) {
    console.error('Caught an exception!', err)
    Raven.captureException(err, {
      extra: {
        action,
        state: store.getState()
      }
    })
    throw err
  }
}
  ```

**这正是 Redux middleware 的样子。**

Middleware 接收了一个 `next()` 的 dispatch 函数，并返回一个 dispatch 函数，返回的函数会被作为下一个 middleware 的 `next()`，以此类推。由于 store 中类似 `getState()` 的方法依旧非常有用，我们将 `store` 作为顶层的参数，使得它可以在所有 middleware 中被使用。

### 尝试 #6: “单纯”地使用 Middleware

我们可以写一个 `applyMiddleware()` 方法替换掉原来的 `applyMiddlewareByMonkeypatching()`。在新的 `applyMiddleware()` 中，我们取得最终完整的被包装过的 `dispatch()` 函数，并返回一个 store 的副本：

  ```js
// 警告：这只是一种“单纯”的实现方式！
// 这 *并不是* Redux 的 API.

function applyMiddleware(store, middlewares) {
  middlewares = middlewares.slice()
  middlewares.reverse()

  let dispatch = store.dispatch
  middlewares.forEach(middleware =>
    dispatch = middleware(store)(dispatch) //传入的dispatch即是next
  )

  return Object.assign({}, store, { dispatch })
}
  ```

这与 Redux 中 [`applyMiddleware()`](https://www.redux.org.cn/docs/api/applyMiddleware.html) 的实现已经很接近了，但是 **有三个重要的不同之处**：

  - 它只暴露一个 [store API](https://www.redux.org.cn/docs/api/Store.html) 的子集给 middleware：[`dispatch(action)`](https://www.redux.org.cn/docs/api/Store.html#dispatch) 和 [`getState()`](https://www.redux.org.cn/docs/api/Store.html#getState)。
  - 它用了一个非常巧妙的方式，以确保如果你在 middleware 中调用的是 `store.dispatch(action)` 而不是 `next(action)`，那么这个操作会再次遍历包含当前 middleware 在内的整个 middleware 链。这对异步的 middleware 非常有用，正如我们在 [之前的章节](https://www.redux.org.cn/docs/advanced/AsyncActions.html) 中提到的。
  - 为了保证你只能应用 middleware 一次，它作用在 `createStore()` 上而不是 `store` 本身。因此它的签名不是 `(store, middlewares) => store`， 而是 `(...middlewares) => (createStore) => createStore`。
- 由于在使用之前需要先应用方法到 `createStore()` 之上有些麻烦，`createStore()` 也接受将希望被应用的函数作为最后一个可选参数传入。

### 最终的方法

这是我们刚刚所写的 middleware：

  ```js
const logger = store => next => action => {
  console.log('dispatching', action)
  let result = next(action)
  console.log('next state', store.getState())
  return result
}

const crashReporter = store => next => action => {
  try {
    return next(action)
  } catch (err) {
    console.error('Caught an exception!', err)
    Raven.captureException(err, {
      extra: {
        action,
        state: store.getState()
      }
    })
    throw err
  }
}
  ```

然后是将它们引用到 Redux store 中：

  ```js
import { createStore, combineReducers, applyMiddleware } from 'redux'

let todoApp = combineReducers(reducers)
let store = createStore(
  todoApp,
  // applyMiddleware() 告诉 createStore() 如何处理中间件
  applyMiddleware(logger, crashReporter)
)
  ```

就是这样！现在任何被发送到 store 的 action 都会经过 `logger` 和 `crashReporter`：

  ```js
// 将经过 logger 和 crashReporter 两个 middleware！
store.dispatch(addTodo('Use Redux'))
  ```

## applyMiddleware() @@@

### **示例**

```js
  export default createStore(reducers,(applyMiddleware(thunk,logger))
```

### createStore.js

```js
  function createStore(reducer, preloadedState, enhancer) {
    // 这里的enhancer就是applyMiddleware(...middleware)的返回值
    if(enhancer是有效的){  // 包括对arguments位置的兼容和有效性的检验
      return enhancer(createStore)(reducer, preloadedState)
    } 
```

### applyMiddleware.js

```js
  import compose from './compose'
  
  /**
   * @param {...Function} middlewares The middleware chain to be applied.
   * @returns {Function} A store enhancer applying the middleware.
   */
  export default function applyMiddleware(...middlewares) {
    // args = [reducer,preloadedState]
    return createStore => (...args) => {
      // 普通地创建store，根本目的是对dispatch的改造
      const store = createStore(...args)
      let dispatch = () => {
        throw new Error(
          'Dispatching while constructing your middleware is not allowed. ' +
            'Other middleware would not be applied to this dispatch.'
        )
      }
  
      const middlewareAPI = {
        getState: store.getState,
        dispatch: (...args) => dispatch(...args)
      }
      // 逐个传入sotre，获取dispatch的改造函数
      const chain = middlewares.map(middleware => middleware(middlewareAPI))
      // dispatch的改造函数以dispatch为参数，函数组合，然后传入未经改造的dispatch
      dispatch = compose(...chain)(store.dispatch)
      // 本质：dispatch = f1(f2(f3(store.dispatch))))
  
      return {
        ...store,
        dispatch
        // 改造后的dispatch会覆盖store的dispatch
      }
    }
  }
```

### middleware.js

 ```js
 export const logger = store => next => action => {
   console.log('dispatching', action)
   // next 就是上一个middleware包装后的dispatch
   let result = next(action)
   console.log('next state', store.getState())
   return result
 }
 
 export const thunk = ({dispatch,getState}) => next => action =>{
   if (typeof action === 'function') {
     return action(dispatch, getState);
   }
   return next(action);
 }
 ```

### compose.js

 ```js
 /**
  * Composes single-argument functions from right to left. The rightmost
  * function can take multiple arguments as it provides the signature for
  * the resulting composite function.
  *
  * @param {...Function} funcs The functions to compose.
  * @returns {Function} A function obtained by composing the argument functions
  * from right to left. For example, compose(f, g, h) is identical to doing
  * (...args) => f(g(h(...args))).
  */
 // 这里个的args就是dispatch
 export default function compose(...funcs) {
   if (funcs.length === 0) {
     return arg => arg
   }
 
   if (funcs.length === 1) {
     return funcs[0]
   }
 
   return funcs.reduce((a, b) => (...args) => a(b(...args)))
 }
 ```

### store.js

```js
  import {createStore,applyMiddleware} from '../lib/redux'
  import combineCounter from './reducers'
  import {logger} from './middlewares'
  
  // 根据counter函数创建store对象
  const store = createStore(combineCounter,applyMiddleware(logger,thunk))
  
  export default store
```

## 在 Redux 中使用中间件

还记得 redux 的 `createStore()` 方法的第三个参数 `enhancer` 吗：

  ```js
function createStore(reducer, preloadedState, enhancer) {
    if(enhancer是有效的){  
        return enhancer(createStore)(reducer, preloadedState)
    } 
    
    //...
}
  ```

在这里，我们可以看到，enhancer（可以叫做强化器）是一个函数，这个函数接受一个「普通 createStore 函数」作为参数，返回一个「加强后的 createStore 函数」。

这个加强的过程中做的事情，其实就是改造 dispatch，添加上中间件。

redux 提供的 `applyMiddleware()` 方法返回的就是一个 enhancer。

applyMiddleware，顾名思义，「应用中间件」。输入为若干中间件，输出为 enhancer。下面来看看它的源码：

  ```js
function applyMiddleware(...middlewares) {
    // 返回一个函数A，函数A的参数是一个createStore函数。
    // 函数A的返回值是函数B，其实也就是一个加强后的createStore函数，大括号内的是函数B的函数体
    return createStore => (...args) => {
        //用参数传进来的createStore创建一个store
        const store  = createStore(...args)
        //注意，我们在这里需要改造的只是store的dispatch方法
        
        let dispatch = () => {  //一个临时的dispatch
            					//作用是在dispatch改造完成前调用dispatch只会打印错误信息
            throw new Error(`一些错误信息`)
        } 
        //接下来我们准备将每个中间件与我们的state关联起来（通过传入getState方法），得到改造函数。
        const middlewareAPI = {
            getState: store.getState,
          	// 为了可以报错，防止中间件。。。所以再套了一层
            dispatch: (...args) => dispatch(...args)
        }
        //middlewares是一个中间件函数数组，中间件函数的返回值是一个改造dispatch的函数
        //调用数组中的每个中间件函数，得到所有的改造函数
        const chain = middlewares.map(middleware => middleware(middlewareAPI))
        
        //将这些改造函数compose（翻译：构成，整理成）成一个函数
        //用compose后的函数去改造store的dispatch
        dispatch = compose(...chain)(store.dispatch)
        // compose方法的作用是，例如这样调用：
        // compose(func1,func2,func3)
        // 返回一个函数: (...args) => func1( func2( func3(...args) ) )
        // 即传入的dispatch被func3改造后得到一个新的dispatch，新的dispatch继续被func2改造...
        
        // 返回store，用改造后的dispatch方法替换store中的dispatch
        return {
            ...store,
            dispatch
        }
    }
}
  ```

总结一下，applyMiddleware 的工作方式是：

  + 调用（若干个）中间件函数，获取（若干个）改造函数
  + 把所有改造函数 compose 成一个改造函数
  + 改造 dispatch 方法

间件的工作方式是：

  + 中间件是一个函数，不妨叫做中间件函数
  + 中间件函数的输入是 store 的 `getState` 和 `dispatch`，输出为改造函数（改造 `dispatch` 的函数）
  + 改造函数输入是一个 `dispatch`，输出「改造后的 `dispatch`」

源码中用到了一个很有用的方法：`compose()`，将多个函数组合成一个函数。理解这个函数对理解中间件很有帮助，我们来看看它的源码：

  ```js
function compose(...funcs) {
    // 当未传入函数时，返回一个函数：arg => arg
    if(funcs.length === 0) {
        return arg => arg
    }
    
    // 当只传入一个函数时，直接返回这个函数
    if(funcs.length === 1) {
        return funcs[0]
    }
    
    // 返回组合后的函数
    return funcs.reduce((a, b) => (...args) => a(b(...args)))
    
    //reduce是js的Array对象的内置方法
    //array.reduce(callback)的作用是：给array中每一个元素应用callback函数
    //callback函数：
    /*
     *@参数{accumulator}：callback上一次调用的返回值
     *@参数{value}：当前数组元素
     *@参数{index}：可选，当前元素的索引
     *@参数{array}：可选，当前数组
     *
     *callback( accumulator, value, [index], [array])
    */
}
  ```

画一张图来理解 compose 的作用：

在 applyMiddleware 方法中，我们传入的「参数」是原始的 dispatch 方法，返回的「结果」是改造后的 dispatch 方法。通过 compose，我们可以让多个改造函数 **抽象成** 一个改造函数。

### 中间件的实现

下面我们以 redux-thunk 为例，看看一个中间件是如何实现的。

### Redux-thunk 的功能

你可能没用过 redux-thunk，所以在阅读源码前，我先简要的讲一下 redux-thunk 的作用：

正常的 dispatch 函数的参数 action 应该是一个纯对象。像这样：

  ```js
store.dispatch({
    type:'REQUEST_SOME_THING',
    payload: {
        from:'bob',
    }
})
  ```

使用了 thunk 之后，我们可以 dispatch 一个函数:

  ```js
function logStateInOneSecond(name) {
    return (dispatch, getState, name) => {  // 这个函数会在合适的时候dispatch一个真正的action
        setTimeout({
            console.log(getState())
            dispatch({
                type:'LOG_OK',
                payload: {
                    name,
                }
            })
        }, 1000)
    }
}

store.dispatch(logStateInOneSecond('jay')) //dispatch的参数是一个函数
  ```

+ 为什么需要这个功能？或者说「dispatch 一个函数」能解决什么问题？
+ 从上面的例子中你会发现，如果 dispatch 一个函数，我们可以在这个函数内做任何我们想要的操作（异步处理，调用接口等等），不受任何限制，为什么？
+ 因为我们「还没有 dispatch 一个真正的 action」，所以不会调用 reducer，**我们并没有将副作用放在 reducer 中，而是在使用 reducer 之前就处理了副作用**。

### Redux-thunk 的实现

如何实现 redux-thunk 中间件呢？

首先中间件肯定是改造 dispatch 方法，改造后的 dispatch 应该具有这样的功能：

  + 如果传入的参数是函数，就执行这个函数。
  + 否则，就认为传入的是一个标准的 action，就调用「改造前的 dispatch」方法，dispatch 这个 action。

现在我们来看看 redux-thunk 的源码（8 行有效代码）：

  ```js
function createThunkMiddleware(extraArgument) {
  return ({ dispatch, getState }) => next => action => {
    if (typeof action === 'function') {
      return action(dispatch, getState, extraArgument);
    }

    return next(action);
  };
}
  ```

如果三个箭头函数让你有点头晕，我来帮你展开一下：

  ```js
function createThunkMiddleware(...args) {
    
    return function({ dispatch, getState }) { // 这是「中间件函数」
        //参数是store中的dispatch和getState方法
        
        return function(next) { // 这是中间件函数创建的「改造函数」
            //参数next是被当前中间件改造前的dispatch
            //因为在被当前中间件改造之前，可能已经被其他中间件改造过了，所以不妨叫next
            
            return function(action) { // 这是改造函数「改造后的dispatch方法」
                if (typeof action === 'function') {
                  //如果action是一个函数，就调用这个函数，并传入参数给函数使用
                  return action(dispatch, getState, ...args);
                }
                
                //否则调用用改造前的dispatch方法
                return next(action);
            }
        } 
    }
}
  ```

next 参数其实就是传进来的 dispatch

讲完了。可以看出 redux-thunk 严格遵循了 redux 中间件的思想：在原始的 dispatch 方法触发 reducer 处理之前，处理副作用。

#
