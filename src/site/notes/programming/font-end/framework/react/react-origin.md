---
{"dg-publish":true,"permalink":"/programming/font-end/framework/react/react-origin/"}
---


# 合成事件与原生事件

## API 一览

+ <https://zh-hans.reactjs.org/docs/events.html>

## 为什么有合成事件的抽象？

+ 如果 DOM 上绑定了过多的事件处理函数，整个页面响应以及内存占用可能都会受到影响。React 为了避免这类 DOM 事件滥用，同时屏蔽底层不同浏览器之间的事件系统差异，实现了一个中间层——SyntheticEvent。

## 原理

+ React 中，如果需要绑定事件，我们常常在 jsx 中这么写：

  ```jsx
  <div onClick={this.onClick}>
  	react事件
  </div>
  ```

+ **实际上**：React 并不是将 click 事件绑在该 div 的真实 DOM 上，而是在 `document` 处监听所有支持的事件，当事件发生并冒泡至 document 处时，React 将事件内容封装并交由真正的处理函数运行。
+ 以上面的代码为例，整个事件生命周期示意如下：

  ![img](https://user-gold-cdn.xitu.io/2017/10/9/8792eeae6dc6011274986acf42a76b15?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

+ 其中，由于 event 对象是复用的，事件处理函数执行完后，属性会被清空，所以 event 的属性无法被异步访问，详情请查阅 [event-pooling](https://link.juejin.im/?target=https%3A%2F%2Freactjs.org%2Fdocs%2Fevents.html%23event-pooling)。

## 在 React 中使用原生事件

+ 虽然 React 封装了几乎所有的原生事件，但诸如：

  - Modal 开启以后点其他空白区域需要关闭 Modal
  - 引入了一些以原生事件实现的第三方库，并且相互之间需要有交互

+ 等等场景时，不得不使用原生事件来进行业务逻辑处理。
+ 由于原生事件需要绑定在真实 DOM 上，所以一般是在 `componentDidMount阶段`/`ref的函数执行阶段` 进行绑定操作，在 `componentWillUnmount阶段` 进行解绑操作以避免内存泄漏。
+ 示例如下：

  ```jsx
  class Demo extends React.PureComponent {
    componentDidMount() {
      const $this = ReactDOM.findDOMNode(this)
      $this.addEventListener('click', this.onDOMClick, false)
    }
  
    onDOMClick = evt => {
      // ...
    }
  
    render() {
      return (
        <div>Demo</div>
      )
    }
  }
  
  ```

  > `ReactDOM.findDOMNode` 已经不推荐使用了，直接使用原生的？但是原生的无法通过实例去找到真实 DOM

# 合成事件和原生事件混合使用

+ 如果业务场景中需要混用合成事件和原生事件，那使用过程中需要注意如下几点：

## 响应顺序

+ 先看个简单例子，下面例子中点击**Demo**以后，控制台输出会是怎样的？

  ```jsx
  class Demo extends React.PureComponent {
    componentDidMount() {
      const $this = ReactDOM.findDOMNode(this)
      $this.addEventListener('click', this.onDOMClick, false)
    }
  
    onDOMClick = evt => {
      console.log('dom event')
    }
  
    onClick = evt => {
      console.log('react event')
    }
  
    render() {
      return (
        <div onClick={this.onClick}>Demo</div>
      )
    }
  }
  
  ```

+ 我们来分析一下：首先 DOM 事件监听器被执行，然后事件继续冒泡至 document，合成事件监听器再被执行。即，最终控制台输出为：

  ```
  dom event react event
  ```

## 阻止冒泡

+ 那，如果在 `onDOMClick` 中调用 `evt.stopPropagation()` 呢？
+ 由于 DOM 事件被阻止冒泡了，无法到达 document，所以合成事件自然不会被触发，控制台输出就变成了：

  ```js
  dom event
  ```

+ 简单例子都比较容易理解，例子再复杂一些：

  ```jsx
  class Demo extends React.PureComponent {
    componentDidMount() {
      const $parent = ReactDOM.findDOMNode(this)
      const $child = $parent.querySelector('.child')
  
      $parent.addEventListener('click', this.onParentDOMClick, false)
      $child.addEventListener('click', this.onChildDOMClick, false)
    }
  
    onParentDOMClick = evt => {
      console.log('parent dom event')
    }
  
    onChildDOMClick = evt => {
      console.log('child dom event')
    }    
  
    onParentClick = evt => {
      console.log('parent react event')
    }
  
    onChildClick = evt => {
      console.log('child react event')
    }
  
    render() {
      return (
        <div onClick={this.onParentClick}>
          <div className="child" onClick={this.onChildClick}>
            Demo
          </div>
        </div>
      )
    }
  }
  ```

+ 如果在 `onChildClick` 中调用 `evt.stopPropagtion()`，则控制台输出变为：

  ```js
  child dom event 
  parent dom event 
  child react event
  ```

+ 这样的结果是因为 React 给合成事件封装的 stopPropagation 函数在调用时给自己加了个 isPropagationStopped 的标记位来确定后续监听器是否执行。
+ 源码如下

  ```js
  //https://github.com/facebook/react/blob/v15.6.1/src/renderers/shared/stack/event/EventPluginUtils.js
  for (var i = 0; i < dispatchListeners.length; i++) {
    if (event.isPropagationStopped()) {
      break;
    }
    // Listeners and Instances are two parallel arrays that are always in sync.
    if (dispatchListeners[i](event, dispatchInstances[i])) {
      return dispatchInstances[i];
    }
  }
  ```

## nativeEvent 在 React 事件体系中的尴尬位置

+ 有人或许有疑问，虽然响应顺序上合成事件晚于原生事件，那在合成事件中是否可以影响原生事件的监听器执行呢？答案是（几乎）不可能。
+ 我们知道，React 事件监听器中获得的入参并不是浏览器原生事件，原生事件可以通过 `evt.nativeEvent` 来获取。但令人尴尬的是，nativeEvent 的作用非常小。

### stopPropagation

+ 在使用者的期望中，`stopPropagation` 是用来阻止当前 DOM 的原生事件冒泡。
+ 但通过上一节合成事件的原理可知，实际上该方法被调用时，实际作用是在 DOM 最外层阻止冒泡，并不符合预期。

### stopImmediatePropagation

+ `stopImmediatePropagation` 常常在多个第三方库混用时，用来阻止多个事件监听器中的非必要执行。
+ 但 React 体系中，一个组件只能绑定一个同类型的事件监听器（重复定义时，后面的监听器会覆盖之前的），所以合成事件甚至都不去封装 `stopImmediatePropagation`。
+ 事实上 nativeEvent 的 `stopImmediatePropagation` 只能阻止绑定在 document 上的事件监听器。此外，由于 [事件绑定的顺序问题](https://link.juejin.im?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FEvent%2FstopImmediatePropagation)，需要注意，如果是在**react-dom.js**加载前绑定的 document 事件，`stopImmediatePropagation` 也是无法阻止的。

## 事件原件对象中的 `target` 进行判断

+ 因为合成事件无法阻止原生事件的冒泡，所以通过原生的做处理
+ ```jsx
  componentDidMount() {
    document.body.addEventListener('click', e => {
      if (e.target && e.target.matches('div.code')) {
        return;
      }
      this.setState({
        active: false,
      });
    });
  }
  ```

## 结论

1. 合成事件的监听器是统一注册在 document 上的，且仅有冒泡阶段。所以原生事件的监听器响应总是比合成事件的监听器早
2. 阻止原生事件的冒泡后，会阻止合成事件的监听器执行
3. 合成事件的 nativeEvent 在本文场景中，没毛用

# React Fiber

参考

https://zhuanlan.zhihu.com/p/26027085

## 同步更新过程的局限

- 在以前的 React 中，更新过程是同步的，这可能会导致性能问题。
- 当 React 决定要加载或者更新组件树时，会做很多事，比如调用各个组件的生命周期函数，计算和比对 Virtual DOM，最后更新 DOM 树，这整个过程是**同步进行**的，也就是说只要一个加载或者更新过程开始，那 React 就以不破楼兰终不还的气概，一鼓作气运行到底，中途绝不停歇。

  > 表面上看，这样的设计也是挺合理的，因为更新过程不会有任何 I/O 操作嘛，完全是 CPU 计算，所以无需异步操作，的确只要一路狂奔就行了，但是，当组件树比较庞大的时候，问题就来了。
  >
  > 假如更新一个组件需要 1 毫秒，如果有 200 个组件要更新，那就需要 200 毫秒，在这 200 毫秒的更新过程中，浏览器那个唯一的主线程都在专心运行更新操作，无暇去做任何其他的事情。想象一下，在这 200 毫秒内，用户往一个 input 元素中输入点什么，敲击键盘也不会获得响应，因为渲染输入按键结果也是浏览器主线程的工作，但是浏览器主线程被 React 占着呢，抽不出空，最后的结果就是用户敲了按键看不到反应，等 React 更新过程结束之后，咔咔咔那些按键一下子出现在 input 元素里了。
  >
  > 这就是所谓的界面卡顿，很不好的用户体验。

- 以前的 React 版本，当组件树很大的时候就会出现这种问题，因为更新过程是同步地一层组件套一层组件，逐渐深入的过程，在更新完所有组件之前不停止，函数的调用栈就像下图这样，调用得很深，而且很长时间不会返回

  ![img](/img/user/programming/font-end/framework/react/react-origin/v2-d8f4598c70df94d69825f11dfbf2ca2c_hd.png)

- 因为 JavaScript 单线程的特点，每个同步任务不能耗时太长，不然就会让程序不会对其他输入作出相应，React 的更新过程就是犯了这个禁忌，而 React Fiber 就是要改变现状。

## React Fiber 的方式

- 破解 JavaScript 中同步操作时间过长的方法其实很简单——分片。
- 把一个耗时长的任务分成很多小片，每一个小片的运行时间很短，虽然总时间依然很长，但是在每个小片执行完之后，都给其他任务一个执行的机会，这样唯一的线程就不会被独占，其他任务依然有运行的机会。
- React Fiber 把更新过程碎片化，执行过程如下面的图所示，每执行完一段更新过程，就把控制权交还给 React 负责任务协调的模块，看看有没有其他紧急任务要做，如果没有就继续去更新，如果有紧急任务，那就去做紧急任务。
- 维护每一个分片的**数据结构**，就是 Fiber。
- 有了分片之后，更新过程的调用栈如下图所示，中间每一个波谷代表深入某个分片的执行过程，每个波峰就是一个分片执行结束交还控制权的时机。

  ![img](https://pic1.zhimg.com/80/v2-78011f3365ab4e0b6184e1e9201136d0_hd.png)

## 为什么叫 Fiber 呢？

- 大家应该都清楚进程（Process）和线程（Thread）的概念，在计算机科学中还有一个概念叫做 Fiber，英文含义就是“纤维”，意指比 Thread 更细的线，也就是比线程 (Thread) 控制得更精密的并发处理机制。
- 上面说的 Fiber 和 React Fiber 不是相同的概念，但是，我相信，React 团队把这个功能命名为 Fiber，含义也是更加紧密的处理机制，比 Thread 更细。

## React Fiber 对现有代码的影响

- 在 React Fiber 中，一次更新过程会分成多个分片完成，所以完全有可能一个更新任务还没有完成，就被另一个更高优先级的更新过程打断，这时候，优先级高的更新任务会优先处理完，而低优先级更新任务所做的工作则会**完全作废，然后等待机会重头再来**。
- 因为一个更新过程可能被打断，所以 React Fiber 一个更新过程被分为两个阶段 (Phase)：第一个阶段 Reconciliation Phase 和第二阶段 Commit Phase。
- 在第一阶段 Reconciliation Phase，React Fiber 会**找出需要更新哪些 DOM**，这个阶段是可以被打断的；但是到了第二阶段 Commit Phase，那就一鼓作气把 DOM 更新完，绝不会被打断。
- 这两个阶段大部分工作都是 React Fiber 做，和我们相关的也就是生命周期函数。
- 以 render 函数为界，第一阶段可能会调用下面这些生命周期函数，说是“可能会调用”是因为不同生命周期调用的函数不同。
  - ~~componentWillMount~~
  - ~~componentWillReceiveProps~~
  - shouldComponentUpdate
  - ~~componentWillUpdate~~
- 下面这些生命周期函数则会在第二阶段调用。
  - componentDidMount
  - componentDidUpdate
  - componentWillUnmount
- ![img](/img/user/programming/font-end/framework/react/react-origin/v2-d2c7de3c408badd0abeef40367d3fb19_hd.png)
- 因为第一阶段的过程会被打断而且“重头再来”，就会造成意想不到的情况。

  > 比如说，一个低优先级的任务 A 正在执行，已经调用了某个组件的 componentWillUpdate 函数，接下来发现自己的时间分片已经用完了，于是冒出水面，看看有没有紧急任务，哎呀，真的有一个紧急任务 B，接下来 React Fiber 就会去执行这个紧急任务 B，任务 A 虽然进行了一半，但是没办法，只能完全放弃，等到任务 B 全搞定之后，任务 A 重头来一遍，注意，是重头来一遍，不是从刚才中段的部分开始，也就是说，componentWillUpdate 函数会被再调用一次。

- 在现有的 React 中，每个生命周期函数在一个加载或者更新过程中绝对只会被调用一次；**在 React Fiber 中，不再是这样了，第一阶段中的生命周期函数在一次加载和更新过程中可能会被多次调用！**
- 使用 React Fiber 之后，一定要检查一下第一阶段相关的这些生命周期函数，看看有没有逻辑是假设在一个更新过程中只调用一次，有的话就要改了。
- 我们挨个看一看这些可能被重复调用的函数。
- componentWillReceiveProps，即使当前组件不更新，只要父组件更新也会引发这个函数被调用，所以多调用几次没啥，通过！
- shouldComponentUpdate，这函数的作用就是返回一个 true 或者 false，不应该有任何副作用，多调用几次也无妨，通过！
- render，应该是纯函数，多调用几次无妨，通过！
- 只剩下 componentWillMount 和 componentWillUpdate 这两个函数往往包含副作用，所以当使用 React Fiber 的时候一定要重点看这两个函数的实现。
- **为了减少代码的影响，16.4 全面更新了生命周期函数，这些影响都已经不存在了**

# setState 做了什么

<https://www.jianshu.com/p/e00378bb7a89>

<https://zhuanlan.zhihu.com/p/52052584>

<https://juejin.im/post/5c32a89d6fb9a049e93ce30c>

# Diffing 算法

- <https://juejin.im/post/5cb5b4926fb9a068b52fb823>

## 概念

- 在某一时间节点调用 React 的 `render()` 方法，会创建一棵由 React 元素组成的树。在下一次 state 或 props 更新时，相同的 `render()` 方法会返回一棵不同的树。React 需要基于这两棵树之间的差别来判断如何有效率的更新 UI 以保证当前 UI 与最新的树保持同步。
- 这个算法问题有一些通用的解决方案，即生成将一棵树转换成另一棵树的最小操作数。 然而，即使在 [最前沿的算法中](http://grfia.dlsi.ua.es/ml/algorithms/references/editsurvey_bille.pdf)，该算法的复杂程度为 O(n^3^ )，其中 n 是树中元素的数量。
- 如果在 React 中使用了该算法，那么展示 1000 个元素所需要执行的计算量将在十亿的量级范围。这个开销实在是太过高昂。于是 React 在以下两个假设的基础之上提出了一套 O(n) 的**启发式算法**：

  1. 两个不同类型的元素会产生出不同的树；
  2. 开发者可以通过 `key` prop 来暗示哪些子元素在不同的渲染下能保持稳定；

- 在实践中，我们发现以上假设在几乎所有实用的场景下都成立，这个算法就是 React Diffing 算法
- 接下来我们会详细介绍该算法

  ![1546677654628](/img/user/programming/font-end/framework/react/react-origin/1546677654628.png)

  ![1546678000961](/img/user/programming/font-end/framework/react/react-origin/1546678000961.png)

  ![1546678020758](/img/user/programming/font-end/framework/react/react-origin/1546678020758.png)

  ![1546678041632](/img/user/programming/font-end/framework/react/react-origin/1546678041632.png)

## 算法详解 (根节点)

- 当对比两颗树时，React 首先比较两棵树的**根节点**。不同类型的根节点元素会有不同的形态。

### 比对不同类型的元素

- 当**根节点**为不同类型的元素时，React 会拆卸原有的树并且建立起新的树。
- 当拆卸一颗树时，对应的 DOM 节点也会被销毁。组件实例将执行 `componentWillUnmount()` 方法。当建立一颗新的树时，对应的 DOM 节点会被创建以及插入到 DOM 中。组件实例将执行 `static getDerivedStateFromPorps` 方法，紧接着是 `render()` 和 `componentDidMount()` 方法。所有跟之前的树所关联的 state 也会被销毁。

**示例**

- 当一个元素从 `<a>` 变成 `<img>`，从 `<Article>` 变成 `<Comment>`，或从 `<Button>` 变成 `<div>` 都会触发一个完整的重建流程

  ```jsx
  <div>
    <Counter />
  </div>
  
  <span>
    <Counter />
  </span>
  ```

- React 会销毁 `Counter` 组件并且重新装载一个新的组件。
- 在处理完当前节点之后，React 继续对子节点进行递归。

### 比对同一类型的 Html 元素

- 当比对两个相同类型的 React 元素时，React 会保留 DOM 节点，仅比对及更新有改变的属性。
- 比如：

  ```jsx
  <div className="before" title="stuff" />
  
  <div className="after" title="stuff" />
  ```

- 通过比对这两个元素，React 知道只需要修改 DOM 元素上的 `className` 属性。
- 当更新 `style` 属性时，React 仅更新有所更变的属性。比如：

  ```jsx
  <div style={{color: 'red', fontWeight: 'bold'}} />
  
  <div style={{color: 'green', fontWeight: 'bold'}} />
  ```

- 通过比对这两个元素，React 知道只需要修改 DOM 元素上的 `color` 样式，无需修改 `fontWeight`。
- 在处理完当前节点之后，React 继续对子节点进行递归。

### 比对同类型的组件元素

- 当一个组件更新时，组件实例保持不变，这样 state 在跨越不同的渲染时保持一致。React 将更新该组件实例的 props 以跟最新的元素保持一致，并且调用该实例的 `static getDerivedStateFromProps() ` 和 `shouldComponentUpdate()` 方法。
- 下一步，调用 `render()` 方法，diff 算法将在之前的结果以及新的结果中进行递归。

## 对子节点进行递归

- 在默认条件下，当递归 DOM 节点的子元素时，React 会同时遍历两个子元素的列表；当产生差异时，生成一个 mutation。
- 在子元素列表末尾新增元素时，更变开销比较小。比如：

  ```jsx
  <ul>
    <li>first</li>
    <li>second</li>
  </ul>
  
  <ul>
    <li>first</li>
    <li>second</li>
    <li>third</li>
  </ul>
  ```

- React 会先匹配两个 `<li>first</li>` 对应的树，然后匹配第二个元素 `<li>second</li>` 对应的树，最后插入第三个元素的 `<li>third</li>` 树。
- 如果简单实现的话，那么在列表头部插入会很影响性能，那么更变开销会比较大。比如：

  ```jsx
  <ul>
    <li>Duke</li>
    <li>Villanova</li>
  </ul>
  
  <ul>
    <li>Connecticut</li>
    <li>Duke</li>
    <li>Villanova</li>
  </ul>
  ```

- React 会针对每个子元素 mutate 而不是保持相同的 `<li>Duke</li>` 和 `<li>Villanova</li>` 子树完成。这种情况下的低效可能会带来性能问题。

## Keys

- 为了解决以上问题，React 支持 `key` 属性。当子元素拥有 key 时，React 使用 key 来匹配原有树上的子元素以及最新树上的子元素。以下例子在新增 `key` 之后使得之前的低效转换变得高效：

  ```jsx
  <ul>
    <li key="2015">Duke</li>
    <li key="2016">Villanova</li>
  </ul>
  
  <ul>
    <li key="2014">Connecticut</li>
    <li key="2015">Duke</li>
    <li key="2016">Villanova</li>
  </ul>
  ```

- 现在 React 知道只有带着 `'2014'` key 的元素是新元素，带着 `'2015'` 以及 `'2016'` key 的元素仅仅移动了。
- 现实场景中，产生一个 key 并不困难。你要展现的元素可能已经有了一个唯一 ID，于是 key 可以直接从你的数据中提取
- 当以上情况不成立时，你可以新增一个 ID 字段到你的模型中，或者利用一部分内容作为哈希值来生成一个 key。这个 key 不需要全局唯一，但在列表中需要保持唯一。
- 最后，你也可以使用元素在数组中的下标作为 key。这个策略在元素不进行重新排序时比较合适，但一旦有顺序修改，diff 就会变得慢。
- 当基于下标的组件进行重新排序时，组件 state 可能会遇到一些问题。由于组件实例是基于它们的 key 来决定是否更新以及复用，如果 key 是一个下标，那么修改顺序时会修改当前的 key，导致非受控组件的 state（比如输入框）可能相互篡改导致无法预期的变动。

  > [这是](https://zh-hans.reactjs.org/redirect-to-codepen/reconciliation/index-used-as-key) 在 Codepen 上的例子，展示使用下标作为 key 时导致的问题，以及 [这里](https://zh-hans.reactjs.org/redirect-to-codepen/reconciliation/no-index-used-as-key) 是一个不使用下标作为 key 的例子的版本，修复了重新排列，排序，以及在列表头插入的问题。
  >
  > 可以观察到：当在头部插入一个 TodoItem 时，已经输入的内容和之前的 index 发生了错位

## 权衡

- 请谨记协调算法是一个实现细节。React 可以在每个 action 之后对整个应用进行重新渲染，得到的最终结果也会是一样的。在这个 context 下，重新渲染表示在所有组件内调用 `render` 方法，这不代表 React 会卸载或装载它们。React 只会基于以上提到的规则来决定如何进行差异的合并。
- 我们定期探索优化算法，让常见用例更高效地执行。在当前的实现中，可以理解为一棵子树能在其兄弟之间移动，但不能移动到其他位置。在这种情况下，算法会重新渲染整棵子树。
- 由于 React 依赖探索的算法，因此当以下假设没有得到满足，性能会有所损耗。
  1. 该算法不会尝试匹配不同组件类型的子树。如果你发现你在两种不同类型的组件中切换，但输出非常相似的内容，建议把它们改成同一类型。在实践中，我们没有遇到这类问题。
  2. Key 应该具有稳定，可预测，以及列表内唯一的特质。不稳定的 key（比如通过 `Math.random()` 生成的）会导致许多组件实例和 DOM 节点被不必要地重新创建，这可能导致性能下降和子组件中的状态丢失。

# Diff 实现

<https://segmentfault.com/a/1190000016647776>

<https://juejin.im/post/5c8e5e4951882545c109ae9c>
