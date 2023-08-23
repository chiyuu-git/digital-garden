---
{"dg-publish":true,"permalink":"/programming/font-end/framework/framwork-common/state-management/"}
---


# State Management

[2022年，我们还需要全局状态管理吗？ - 知乎](https://zhuanlan.zhihu.com/p/554997023)

[各流派 React 状态管理对比和原理实现 - 知乎](https://zhuanlan.zhihu.com/p/394106764)

各流派 React 状态管理对比和原理实现 https://zhuanlan.zhihu.com/p/394106764

简单的项目就用 useReducer 和 useContext 就好了

# 使用原生 JavaScript 构建状态管理系统

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

# Zustand

[谈谈复杂应用的状态管理（上）：为什么是 Zustand - 知乎](https://zhuanlan.zhihu.com/p/591981209)
[react的状态库最佳实践非zustand莫属 - 掘金](https://juejin.cn/post/7232726594208759867)

# FAQ

#faq/framework

## 拥抱响应式

只有 react 和 redux 是非响应式的

新出的状态管理工具也都是精确更新的, 可以认为是响应式的理念

## 如何实现状态共享

为啥 redux 和 context api 需要 provider

为啥 zustand 直接用 useStore 就完事了? useStore 默认单实例. zustand 也提供了对应的 Provider 的书写方式，这种方式在组件库中比较常用。ProEditor 也是用的这种方式做到了多实例。

此外，zustand 的 store 状态既可以在 react 世界中消费，也可以在 react 世界外消费。

## 状态变更

为啥 redux 要一堆模板代码, 而 zustand 可以直接写, 不区分同步异步?

## 状态派生
