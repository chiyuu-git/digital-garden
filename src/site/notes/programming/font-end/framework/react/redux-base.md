---
{"dg-publish":true,"permalink":"/programming/font-end/framework/react/redux-base/"}
---


# State Management

[2022年，我们还需要全局状态管理吗？ - 知乎](https://zhuanlan.zhihu.com/p/554997023)

[各流派 React 状态管理对比和原理实现 - 知乎](https://zhuanlan.zhihu.com/p/394106764)

# Valtio

# Redux-toolkit

https://redux-toolkit.js.org/

各流派 React 状态管理对比和原理实现 https://zhuanlan.zhihu.com/p/394106764

简单的项目就用 useReducer 和 useContext 就好了

# PubSub @@@

参考： https://juejin.im/post/5b763528e51d45559e3a5b64

pubsub-js： https://github.com/mroderick/PubSubJS/blob/master/src/pubsub.js

## constructor()

```js
  export default class PubSub {
    constructor() {
      this.eventList = {};
    }
  }
  ```

我们得到了一个全新的类，我们将 `this.eventList` 默认设置为空对象。`this.eventList` 对象将保存我们的具名事件。

## subscribe()

给类的原型添加一个 `subscribe()` 方法

类似：添加事件监听

**参数**

1. eventName：String ，事件名
2. handler：Func ，处理该事件的回调函数

**返回值** : 当前事件绑定的回调函数的个数

**实现**

```js
  /**
   * @param  {String} event 
   * @param  {Function} handler 
   *
   * @api private
   */
  subscribe(event, handler) {
    // 如果该事件不存在，则创建该事件
    if(!this.eventList.hasOwnProperty(event)) {
      this.eventList[event] = [];
    }
    // 将回调函数添加到该事件维护的列表中
    return this.eventList[event].push(handler);
  }
```

**作用** : 将回调函数推入事件队列

## publish()

给类的原型添加一个 `publish()` 方法

类似：触发事件，需要我们 **主动触发**

**参数**

1. eventName：String ，事件名
2. ...rest

**异常**: 如果该事件没有订阅，报错

**实现**

```js
  /**
   * @param  {String} event 
   * @api private
   */
  	publish(event,...rest) {
    if(!this.eventList.hasOwnProperty(event)) {
      // return error
      return new Error('error')
    }
    // 遍历执行该事件的回调函数列表
    this.eventList[event].forEach(callback => callback(...rest));
  }
```

**作用**: 遍历执行该事件的回调函数列表

## unsubscribe()

给类的原型添加一个 `unsubscribe()` 方法

类似：解除事件绑定

**参数**

+ eventName：String ，事件名
+ handler：Func，回调函数的引用

**实现**

```js
  /**
   * @param  {String} event 
   * @param  {Function} handler 
   * @api private
   */
  	unsubscribe(event,handler){
    const handlers = this.eventList[event]
    if(handlers===undefined) return
  
    const handlerIndex = handlers.indexOf(handler)
    handlers.splice(handlerIndex,1)
  }
```

## once()

subscribe()，包装一个函数，unsubscribe()

```js
  once(event,handler){
    const self = this
    function func(...rest){
      handler.apply(,rest)
      self.unsubscribe(event,fuc)
    }
    this.subscribe(event,func)
  }
```

## 完整代码

```js
  export default class PubSub {
    constructor() {
      this.eventList = {};
    }
    /**
     * @param  {String} event 
     * @param  {Function} handler 
     * @api private
     */
    subscribe(event, handler) {
      if(!this.eventList.hasOwnProperty(event)) {
        this.eventList[event] = [];
      }
      return this.eventList[event].push(handler);
    }
    /**
     * @param  {String} event 
     * @api private
     */
    publish(event,...rest) {
      if(!this.eventList.hasOwnProperty(event)){
        console.log('事件未定义');
        return
      }
  
      this.eventList[event].forEach(handler => handler(...rest));
    }
    /**
     * @param  {String} event 
     * @param  {Function} handler 
     * @api private
     */
    unsubscribe(event,handler){
      const handlers = this.eventList[event]
      if(handlers===undefined) return
  
      const handlerIndex = handlers.indexOf(handler)
      handlers.splice(handlerIndex,1)
      console.log('成功解除事件绑定');
    }
  }
```

## 测试

```js
  import PubSub from './lib/pubsub/index.js'
  
  let pubSub = new PubSub()
  
  const testHandler = (...rest) => {
    rest.forEach((val) => {console.log(val)})
  }
  
  pubSub.subscribe('test',testHandler)
  
  pubSub.publish('test','hello','world')
  
  pubSub.unsubscribe('test',testHandler)
  
  pubSub.publish('test2','hello','world')
  
  pubSub.once('once',testHandler)
  pubSub.publish('once','once','once')
  pubSub.publish('once','two','two')
```

# Flux

<a href='F:\OneDrive\JS\demo\flux-demo' >demo</a>

## Store 对象（核心）

我们现在已经有了 `PubSub` 模块，我们这个小应用程序的核心模块 Store 类有了它的唯一依赖。现在我们开始完善它:

+ Store 是我们的核心对象
+ 它将包含一个 `state` 对象，该对象又包含我们的应用程序状态
+ 一个 `commit` 方法，它将调用我们的 **mutations** , mutaions，修改 state
+ 一个 `dispatch` 方法，它将调用我们的 **actions** , some action，调用 commit，驱动 mutaions
+ 在这个应用和 `Store` 对象的核心之间，将有一个基于代理的系统，它将使用我们的 `PubSub` 模块监视和广播状态变化

 ```js
  import PubSub from '../pubsub/index.js';
```

### constructor()

```js
  export default class Store {
    constructor(params) {
      this.actions = {};
      this.mutations = {};
      this.state = {};
      this.status = 'resting';
    }
  }
```

我们将为 `state`，`actions` 和 `mutations` 添加默认对象。我们还添加了一个 `status` 属性，我们将用它来确定对象在任意给定时间正在做什么。

之后，我们将创建一个新的 `PubSub` 实例，它将作为 `store` 的 `events` 属性的值：

```js
  this.events = new PubSub();
```

接下来，我们将搜索传入的 `params` 对象以查看是否传入了任何 `actions` 或 `mutation`。当实例化 `Store` 对象时，我们可以传入一个数据对象。其中包括 `actions` 和 `mutation` 的集合，它们控制着我们 store 中的数据流。在你添加的最后一行代码后面添加以下代码：

```js
if(params.hasOwnProperty('actions')) {
    self.actions = params.actions;
}
  
if(params.hasOwnProperty('mutations')) {
    self.mutations = params.mutations;
}
```

这就是我们所有的默认设置和几乎所有潜在的参数设置。让我们来看看我们的 `Store` 对象如何跟踪所有的变化。我们将使用 [Proxy（代理）](https://link.juejin.im?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FProxy) 来完成此操作。

### 配置代理

Proxy（代理）所做的工作主要是代理 state 对象。如果我们添加一个 `get` 拦截方法，我们可以在每次询问对象数据时进行监控。与 `set` 拦截方法类似，我们可以密切关注对象所做的更改。这是我们今天感兴趣的主要部分。在你添加的最后一行代码之后添加以下内容，我们将讨论它正在做什么：

```js
self.state = new Proxy((params.state || {}), {
	set: function(state, key, value) {
	
	  state[key] = value;
	
	  console.log(`stateChange: ${key}: ${value}`);
	
	  self.events.publish('stateChange', self.state);
	
	  if(self.status !== 'mutation') {
		console.warn(`You should use a mutation to set ${key}`);
	  }
	
	  self.status = 'resting';
	
	  return true;
	}
});
```

这部分代码说的是我们正在捕获状态对象 `set` 操作。这意味着当运行类似于 `state.name ='Foo'` 时，这个拦截器会在它被设置之前捕获它，并为我们提供了一个机会来处理更改甚至完全拒绝它。但在我们的上下文中，我们将会设置变更，然后将其记录到控制台。然后我们用 `PubSub` 模块发布一个 `stateChange` 事件。任何订阅了该事件的回调将被调用。最后，我们检查 `Store` 的状态。如果它当前不是一个 `mutation`，则可能意味着状态是手动更新的。我们在控制台中添加了一点警告，以便给开发人员一些提示。

这里做了很多事，但我希望你们开始看到这一切是如何结合在一起的，重要的是，我们如何能够集中维护状态，这要归功于 Proxy（代理）和 Pub/Sub。

### Dispatch 和 Commit

现在我们已经添加了 `Store` 的核心部分，让我们添加两个方法。

一个是将调用我们 `actions` 的 `dispatch`

另一个是将调用我们 `mutation` 的 `commit`

让我们从 `dispatch` 开始，在 `store.js` 中的 `constructor` 之后添加这个方法：

  ```js
  dispatch(actionKey, payload) {
    let self = this;
    if(typeof self.actions[actionKey] !== 'function') {
      console.error(`Action "${actionKey} doesn't exist.`);
      return false;
    }
    console.groupCollapsed(`ACTION: ${actionKey}`);
    self.status = 'action';
    self.actions[actionKey](self, payload);
    console.groupEnd();
    return true;
  }
  ```

此处的过程是：查找 action，如果存在，则设置状态并调用 action，同时创建日志记录组以使我们的所有日志保持良好和整洁。记录的任何内容（如 mutation 或 Proxy（代理）日志）都将保留在我们定义的组中。如果未设置任何 action，它将记录错误并返回 false。

`commit` 方法更加直截了当，在 `dispatch` 方法之后添加：

```js
commit(mutationKey, payload) {
    let self = this;
    if(typeof self.mutations[mutationKey] !== 'function') {
      console.log(`Mutation "${mutationKey}" doesn't exist`);
      return false;
    }
    self.status = 'mutation';
    let newState = self.mutations[mutationKey](self.state, payload);
    self.state = Object.assign(self.state, newState);
    return true;
}
```

这种方法非常相似，但无论如何我们都要自己了解这个过程。如果可以找到 mutation，我们运行它并从其返回值获得新状态。然后我们将新状态与现有状态合并，以创建我们最新版本的 state。

### Store 实例

store 状态管理库，我们已经搭建好了，现在我们需要新建我们的 store 实例，以便在项目中使用它

```js
import Store from '../../lib/flux/index'

import state from './state'
import mutations from './mutations'
import actions from './actions'

export default new Store({
	state,
	mutations,
	actions,
})
```

## 组件

### 基础组件

其他组件继承于该组件

lib/components/index.js

  ```js
  import Store from '../flux/index.js';
  
  export default class Component {
    constructor(props = {}) {
      this.render = this.render || function () { };
  
      if (props.store instanceof Store) {
        props.store.events.subscribe('stateChange', () => this.render());
      }
      
      if (props.hasOwnProperty('element')) {
        this.element = props.element;
      }
    }
  }
  ```

首先，我们要导入 `Store` **类**。这不是因为我们想要它的实例，而是更多用于检查 `constructor` 中的一个属性。

在 `constructor` 中我们要看看我们是否有一个 render 方法。如果这个 `Component` 类是另一个类的父类，那么子类可能会为 `render` 设置自己的方法。如果没有设置方法，我们创建一个空方法来防止事情出错。

在此之后，我们像上面提到的那样对 `Store` 类进行检查。我们这样做是为了确保 `store` 属性是一个 `Store` 类实例，这样我们就可以放心地使用它的方法和属性。说到这一点，我们订阅了全局 `stateChange` 事件，所以我们的对象可以做到 **响应式**。每次状态改变时都会调用 `render` 函数。@@@

这就是我们需要为该类所要写的全部内容。它将被用作其他组件类 `extend` 的父类。让我们一起来吧！

### 自定义组件

我们要完成三个组件，它们都通过 `extend` 关键字，继承了基类 `Component`。让我们从最大的一个组件开始开始：项目清单！

#### **list 组件**

src/components/list.js

```js
import Component from '../lib/components/index.js';
import Store from '../lib/flux/index.js';
  
const store = new Store()
  
export default class List extends Component {
    constructor() {
      super({
          store,
          element: document.querySelector('.js-items')
      });
    }
    render() {
  
      if(store.state.items.length === 0) {
        this.element.innerHTML = `<p class="no-items">You've done nothing yet &#x1f622;</p>`;
          return;
      }
  
      this.element.innerHTML = `
        <ul class="app__items">
          ${store.state.items.map(item => {
            return `
            <li>${item}<button aria-label="Delete this item">×</button></li>
            `
          }).join('')}
        </ul>
      `;
  
      this.element.querySelectorAll('button').forEach((button, index) => {
        button.addEventListener('click', () => {
        	store.dispatch('clearItem', { index });
        });
      });
    }
}
```

我们先将 `Store` 实例传递给我们继承的 `Component` 父类。就是我们刚刚编写的 `Component` 类。

在那之后，我们声明了 render 方法，每次触发 Pub/Sub 的 `stateChange` 事件时都会调用的这个 render 方法。在这个 `render` 方法中，我们会生成一个项目列表，或者是没有项目时的通知。你还会注意到每个按钮都附有一个事件，并且它们会触发一个 action，然后由我们的 store 处理 action。这个 action 还不存在，但我们很快就会添加它。

接下来，再创建两个文件。虽然是两个新组件，但它们很小 —— 所以我们只是向其中粘贴一些代码即可，然后继续完成其他部分。

#### Count 组件

```js
import Component from '../lib/components/index.js';
import Store from '../lib/flux/index.js';

const store = new Store()
  
export default class Count extends Component {
    constructor() {
      super({
        store,
        element: document.querySelector('.js-count')
      });
    }
  
    render() {
      let suffix = store.state.items.length !== 1 ? 's' : '';
      let emoji = store.state.items.length > 0 ? '&#x1f64c;' : '&#x1f622;';
  
      this.element.innerHTML = `
        <small>You've done</small>
        ${store.state.items.length}
        <small>thing${suffix} today ${emoji}</small>
      `;
    }
}
```

#### Status 组件

```js
import Component from '../lib/components/index.js';
import Store from '../lib/flux/index.js';
  
const store = new Store()
  
export default class Status extends Component {
    constructor() {
      super({
          store,
          element: document.querySelector('.js-status')
      });
    }
  
    render() {
      let suffix = store.state.items.length !== 1 ? 's' : '';
      this.element.innerHTML = `${store.state.items.length} item${suffix}`;
    }
}
```

#### 目录结构

 ```js
  │  index.html
  │  index.js
  │
  ├─lib
  │  ├─components
  │  │      index.js
  │  │
  │  ├─flux
  │  │      index.js
  │  │
  │  └─pubsub
  │          index.js
  │
  ├─src
  │  ├─components
  │  │      count.js
  │  │      list.js
  │  │      status.js
  │  │
  │  └─store
  │          actions.js
  │          index.js
  │          mutaions.js
  │          state.js
  ```

## Store 实例

我们已经让 store 系统和组件通过数据来渲染和交互。现在让我们把应用程序的两个独立部分联系起来，让整个项目一起协同工作。我们需要添加一个初始状态，一些 `actions` 和一些 `mutations`。在 `store` 目录中，添加一个名为 `state.js` 的新文件。

### state.js

  ```js
  export default {
      items: [
      'I made this',
      'Another thing'
      ]
  };
  ```

### actions.js

```js
//src/store/actions.js
export default {
    addItem(context, payload) {
    context.commit('addItem', payload);
    },
    clearItem(context, payload) {
    context.commit('clearItem', payload);
    }
};
```

这个应用程序中的 actions 非常少。本质上，每个 action 都会将 payload（关联数据）传递给 mutation，而 mutation 又将数据提交到 store。正如我们之前所了解的那样，`context` 是 `Store` 类的实例，`payload` 是触发 action 时传入的。

  > 在 redux 和 vuex 中，都只有一个 store 实例，所以不需要传递 context

### mutaions.js

```js
// src/store/mutaions.js
export default {
      addItem(state, payload) {
      state.items.push(payload);
  
      return state;
      },
      clearItem(state, payload) {
      state.items.splice(payload.index, 1);
  
      return state;
      }
};
```

与 actions 一样，这些 mutations 很少。在我看来，你的 mutations 应该保持简单，因为他们有一个工作：改变 store 的 state。因此，这些例子就像它们最初一样简单。任何适当的逻辑都应该发生在你的 `actions` 中。正如你在这个系统中看到的那样，我们返回新版本的 state，以便 `Store` 的 `<code>commit` 方法可以发挥其魔力并更新所有内容。

有了这个，store 系统的主要模块就位。

## 连接 index.html

我们需要做的最后一件事是添加 `index.html` 中包含的 `main.js` 文件。

src/main.js

### 绑定监听

 ```js
  import store from './store/index.js'; 
  
  import Count from './components/count.js';
  import List from './components/list.js';
  import Status from './components/status.js';
  
  const formElement = document.querySelector('.js-form');
  const inputElement = document.querySelector('#new-item-field');
  
  formElement.addEventListener('submit', evt => {
    evt.preventDefault();
  
    let value = inputElement.value.trim();
  
    if(value.length) {
      store.dispatch('addItem', value);
      inputElement.value = '';
      inputElement.focus();
    }
  });
  ```

我们在这里做的是向表单添加一个事件监听器并阻止它提交。

然后我们获取文本框的值并修剪它两端的空格。我们这样做是因为我们想检查下一步是否会有任何内容传递给 store。

最后，如果有内容，我们将使用该内容作为 payload（关联数据）触发我们的 `addItem` action，并且让我们闪亮的新 `store` 为我们处理它。

### 组合组件

让我们在 `main.js` 中再添加一些代码。在事件监听器下，添加以下内容：

  ```js
  const countInstance = new Count();
  const listInstance = new List();
  const statusInstance = new Status();
  
  countInstance.render();
  listInstance.render();
  statusInstance.render();
  ```

我们在这里所做的就是创建组件的新实例并调用它们的每个 `render` 方法，以便我们在页面上获得初始状态。

# Redux

## Redux 理解

### 学习文档

- 英文文档: <https://redux.js.org/>
- 中文文档: <http://www.redux.org.cn/>
- <https://cn.redux.js.org/docs/react-redux/api.html>
- Github: <https://github.com/reactjs/redux>

### 概述

redux 是一个独立专门用于做状态管理的 JS 库 (不是 react 插件库)

它可以用在 react, angular, vue 等项目中, 但基本与 react 配合使用

作用: 集中式管理 react 应用中多个组件共享的状态

### Redux 工作流程

![1547027805153](/img/user/programming/font-end/framework/react/redux-base/1547027805153.png)

Action Creators 是一个工厂函数

### 什么情况下需要使用 Redux

总体原则: 能不用就不用, 如果不用比较吃力才考虑使用

1. 某个组件的状态，需要共享
2. 某个状态需要在任何地方都可以拿到
3. 一个组件需要改变全局状态
4. 一个组件需要改变另一个组件的状态

## Redux 三个核心概念

### Action

标识要执行的行为的对象, 包含 2 个方面的属性

  - type: 标识属性, 值为字符串, 唯一, 必要属性
  - xxx: 数据属性, 值类型任意, 可选属性

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

### Reducer

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

### 处理 Reducer 关系时的注意事项

开发复杂的应用时，不可避免会有一些数据相互引用。建议你尽可能地把 state 范式化，不存在嵌套。把所有数据放到一个对象里，每个数据以 ID 为主键，不同实体或列表间通过 ID 相互引用数据。把应用的 state 想像成数据库。这种方法在 [normalizr](https://github.com/gaearon/normalizr) 文档里有详细阐述。例如，实际开发中，在 state 里同时存放 `todosById: { id -> todo }` 和 `todos: array<id>` 是比较好的方式，本文中为了保持示例简单没有这样处理。

### Store

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

## Redux 三大原则

### 单一数据源

**整个应用的 state 被储存在一棵 object tree 中，并且这个 object tree 只存在于唯一一个 store 中。**

### State 是只读的

**唯一改变 state 的方法就是触发 action，action 是一个用于描述已发生事件的普通对象。**

**注意，action 没有任何魔法**，强制使用 action 来描述所有变化带来的好处是可以清晰地知道应用中到底发生了什么，action 就像是描述发生了什么的指示器。

### 使用纯函数来执行修改

**为了描述 action 如何改变 state tree ，你需要编写 reducers。**

**再次地，没有任何魔法**，reducer 只是一个接收 state 和 action，并返回新的 state 的函数。

### 总结

这差不多就是 Redux 思想的全部。我们没有使用任何 Redux 的 API。Redux 里有一些工具来简化这种模式，但是主要的想法是如何根据这些 action 对象来更新 state，而且 90% 的代码都是纯 JavaScript，没用 Redux、Redux API 和其它魔法

  > 如果你以前使用 Flux，那么你只需要注意一个重要的区别。Redux 没有 Dispatcher 且不支持多个 store。相反，只有一个单一的 store 和一个根级的 reduce 函数（reducer）。随着应用不断变大，你应该把根级的 reducer 拆成多个小的 reducers，分别独立地操作 state 树的不同部分，而不是添加新的 stores。这就像一个 React 应用只有一个根级的组件，这个根组件又由很多小组件构成。
  >
  > 用这个架构开发计数器有点杀鸡用牛刀，但它的美在于做复杂应用和庞大系统时优秀的扩展能力。由于它可以用 action 追溯应用的每一次修改，因此才有强大的开发工具。如录制用户会话并回放所有 action 来重现它。

## Redux 的核心 API

### createStore()

作用: 创建包含指定 reducer 的 store 对象

  ```js
  import {createStore} from 'redux'
  import counter from './reducers/counter'
  const store = createStore(counter)
  ```

[`createStore()`](https://www.redux.org.cn/docs/api/createStore.html) 的第二个参数是可选的, 用于设置 state 初始状态。这对开发同构应用时非常有用，服务器端 redux 应用的 state 结构可以与客户端保持一致, 那么客户端可以将从网络接收到的服务端 state 直接用于本地数据初始化。

  ```js
  let store = createStore(todoApp, window.STATE_FROM_SERVER)
  ```

### Store 对象

redux 库最核心的管理对象, 它内部维护着:

  - state
  - reducer
核心方法:

  ```JS
  store.getState()
  store.dispatch({type:'INCREMENT', number})
  store.subscribe(render)
  ```

### applyMiddleware()

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

### combineReducers()

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

## 参考模板

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

- 工厂函数创建的 action 对象：`this.props.store.dispatch(actions.increment(number))`.
- 直接定义的 action 对象：`this.props.store.dispatch({type:INCREMENT,data:number})`

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

### 缺点

1. redux 与 react 组件的代码耦合度太高
2. 编码不够简洁

## Redux 插件库

https://www.redux.org.cn/docs/introduction/Ecosystem.html

示例：https://www.redux.org.cn/docs/introduction/Examples.html

## Redux 异步编程

### 异步数据流

默认情况下，[`createStore()`](https://www.redux.org.cn/docs/api/createStore.html) 所创建的 Redux store 没有使用 [middleware](https://www.redux.org.cn/docs/advanced/Middleware.html)，所以只支持 [同步数据流](https://www.redux.org.cn/docs/basics/DataFlow.html)。

你可以使用 [`applyMiddleware()`](https://www.redux.org.cn/docs/api/applyMiddleware.html) 来增强 [`createStore()`](https://www.redux.org.cn/docs/api/createStore.html)。虽然这不是必须的，但是它可以帮助你 [用简便的方式来描述异步的 action](https://www.redux.org.cn/docs/advanced/AsyncActions.html)。

像 [redux-thunk](https://github.com/gaearon/redux-thunk) 或 [redux-promise](https://github.com/acdlite/redux-promise) 这样支持异步的 middleware 都包装了 store 的 [`dispatch()`](https://www.redux.org.cn/docs/api/Store.html#dispatch) 方法，以此来让你 dispatch 一些除了 action 以外的其他内容，例如：函数或者 Promise。你所使用的任何 middleware 都可以以自己的方式解析你 dispatch 的任何内容，并继续传递 actions 给下一个 middleware。比如，支持 Promise 的 middleware 能够拦截 Promise，然后为每个 Promise 异步地 dispatch 一对 begin/end actions。

当 middleware 链中的最后一个 middleware 开始 dispatch action 时，这个 action 必须是一个普通对象。这是 [同步式的 Redux 数据流](https://www.redux.org.cn/docs/basics/DataFlow.html) 开始的地方（译注：这里应该是指，你可以使用任意多异步的 middleware 去做你想做的事情，但是需要使用普通对象作为最后一个被 dispatch 的 action ，来将处理流程带回同步方式）

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

## Redux 调试工具

npm install --save-dev redux-devtools-extension

  ```js
  // index.js
  import { composeWithDevTools } from 'redux-devtools-extension'
  
  const store = createStore(
    counter,
    composeWithDevTools(applyMiddleware(thunk)) 
  )
  ```

![1547045446583](/img/user/programming/font-end/framework/react/redux-base/1547045446583.png)

左边可以查看到 reducer 的调用

## Redux 实战

### 改造 Comments

shift() 会影响原数组，所以用 `[action.data,...state]`

splice 返回的是被删除的元素，所以用 `state.filter((comment,index)=> index!==action.data)`

### 多个 Reducer 怎么处理

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

### 发起异步请求拿到数据之后执行函数

方式一：通过 state 更新之后，重新渲染页面的时候执行函数，但是初始渲染的时候也会执行一次，只能再通过条件判断避免

尝试失败：因为异步 action 返回的函数内部是没有 return 的，所以异步 action 只能拿到 undefined

尝试成功：要记住每一层函数都要 return，不然就会 undefined

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

- 让所有组件都可以得到 state 数据

  ```jsx
  <Provider store={store}>
       <App />
  </Provider>
  ```

## connect()

- 用于包装 UI 组件生成容器组件

  ```jsx
  import { connect } from 'react-redux'
     connect(
       mapStateToprops,
       mapDispatchToProps
  )(Counter)
  ```

- 调用 connect 函数返回了一个 **高阶组件生成器**，而这个生成器会基于原始组件生成一个全新的组件，并给这个组件添加额外的 props。
- 在构造一个高阶组件生成器时，connect 最多接受 4 个参数，分别如下
  - `[mapStateToProps(state, [ownProps]): stateProps]`（类型：函数）：接受完整的 Redux 状态树作为参数，返回当前组件相关部分的状态树，返回对象的所有 key 都会成为组件的 props。
  - `[mapDispatchToProps(dispatch, [ownProps]): dispatchProps]` （类型：对象或函数）：接受 Redux 的 dispatch 方法作为参数，返回当前组件相关部分的 action creator，并可以在这里将 action creator 与 dispatch 绑定，减少冗余代码。
  - `[mergeProps(stateProps, dispatchProps, ownProps): props] `（类型：函数）：如果指定这个函数，你将分别获得 mapStateToProps、mapDispatchToProps 返回值以及当前组件的 props 作为参数，最终返回你期望的、完整的 props。
  - `[options]`（类型：对象）：可选的额外配置项，有以下两项。
    - [pure = true] （类型：布尔）：该值设为 true 时，将为组件添加 shouldComponentUpdate()
      生命周期函数，并对 mergeProps 方法返回的 props 进行浅层对比。
    - [withRef = false]（类型：布尔）：若设为 true，则为组件添加一个 ref 值，后续可
      以使用 getWrappedInstance() 方法来获取该 ref，默认为 false。

## mapStateToprops()

- 将外部的数据（即 state 对象）转换为 UI 组件的标签属性
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

参考模板

- 下载依赖包

`npm install --save react-redux`

- redux/action-types.js
  - 不变
- redux/actions.js
  - 不变
- redux/reducers.js
- 不变
- index.js
  - 不需要订阅监听了
  - 把 store 传给 provider

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

- components/counter.jsx
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

- containters/app.jsx
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

![1547037866613](/img/user/programming/font-end/framework/react/redux-base/1547037866613.png)

- provider 有 store 属性，里面有着 store 的全部方法
- App 组件，经过 connect() 包装，获得了传入的属性，类比上次包装 MyNavLink 组件，通过包装组件，传入自己定义的属性

## 问题

1. redux 默认是不能进行异步处理的
2. 应用中又需要在 redux 中执行异步任务 (ajax, 定时器)

## 注意

+ connect 之后的 state 和 action 都需要通过 props 来获取

# React-Redux 源码

+ redux 作为大型应用的状态管理工具，如果想配合 react 使用，需要借助 react-redux。 redux 主要完成两件事情：
  + 负责应用的状态管理，保证单向数据流
  + 当应用状态发生变化，触发监听器。
+ 那么，如果想要将 react 和 redux 搭配使用，就需要 react 组件可以根据 redux 中所存储的状态（store）更新 view。 并且可以改变 store。
+ 其实 react-redux 主要就是完成了这两件事情：
  + 第一，通过将 store 传入 root 组件的 context，使子节点可以获取到 state
  + 第二，通过 store.subscribe 订阅 store 的变化，更新组件
  + 另外还有对于性能的优化，减少不必要的渲染
+ 首先，我们先回忆一下 react-redux 的基本用法。react-redux 使用非常简单，我们仅仅使用了两个 API，`Provider` 与 `connect`。
+ Provider： 接收从 redux 而来的 store，以供子组件使用。
+ connect： 高阶组件，当组件需要获取或者想要改变 store 的时候使用。可以接受四个参数：

  - mapStateToProps：将 state 映射为组件的 props
  - mapDispatchToProps：将 dispatch(action) 映射为组件的 props
  - mergeProps：可以在其中对 mapStateToProps， mapDispatchToProps 的结果进一步处理
  - options：一些配置项，例如 pure.当设置为 true 时，会避免不必要的渲染

## Provider

```jsx
  import React, { Component } from 'react'
  import PropTypes from 'prop-types'
  import { ReactReduxContext } from './Context'
  import Subscription from '../utils/Subscription'
  
  class Provider extends Component {
    constructor(props) {
      super(props)
  		// 获取传入的store
      const { store } = props
  		// 监听相关
      this.notifySubscribers = this.notifySubscribers.bind(this)
      const subscription = new Subscription(store)
      subscription.onStateChange = this.notifySubscribers
  
      this.state = {
        store,
        subscription
      }
  
      this.previousState = store.getState()
    }
  
    componentDidMount() {
      this._isMounted = true
  
      this.state.subscription.trySubscribe()
  
      if (this.previousState !== this.props.store.getState()) {
        this.state.subscription.notifyNestedSubs()
      }
    }
  
    componentWillUnmount() {
      if (this.unsubscribe) this.unsubscribe()
  
      this.state.subscription.tryUnsubscribe()
  
      this._isMounted = false
    }
  
    componentDidUpdate(prevProps) {
      if (this.props.store !== prevProps.store) {
        this.state.subscription.tryUnsubscribe()
        const subscription = new Subscription(this.props.store)
        subscription.onStateChange = this.notifySubscribers
        this.setState({ store: this.props.store, subscription })
      }
    }
  
    notifySubscribers() {
      this.state.subscription.notifyNestedSubs()
    }
  
    render() {
      const Context = this.props.context || ReactReduxContext
  		// 通过context传递store
      return (
        <Context.Provider value={this.state}>
          {this.props.children}
        </Context.Provider>
      )
    }
  }
  
  Provider.propTypes = {
    store: PropTypes.shape({
      subscribe: PropTypes.func.isRequired,
      dispatch: PropTypes.func.isRequired,
      getState: PropTypes.func.isRequired
    }),
    context: PropTypes.object,
    children: PropTypes.any
  }
  
  export default Provider
  ```
