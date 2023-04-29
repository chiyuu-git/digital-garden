---
{"dg-publish":true,"permalink":"/programming/font-end/framework/react/redux-advanced/"}
---


# Keyword

+ 数据请求、业务逻辑
+ 容器组件、展示组件
+ ADSV
+ dispatcher action store controller-view view
+ thunk 函数
+ layouts views components

# 4. 认识 Flux 架构模式

+ Flux 是由 Facebook 在 2014 年开源的一款用于构建用户界面的应用程序架构（Application Architecture for Building User Interface）。随着师出同门的 React 越来越火爆，Flux 也受到了越来越多的关注。
+ 那么，Flux 究竟是怎么一回事呢？Flux 与我们常说的前端 MVC 架构有什么区别呢？我们将在本章中一起探索。

## React 独立架构

+ 在第 1 章中，我们就提到 React 是自带 View 和 Controller 的库。自然，我们在实现应用时不需要任何其他库也可以自运行。
+ 为了更好地理解从 React 到 React+Flux 的演进路线，我们就以前几章学习的内容来实现一个类似论坛评论功能的 App，从而开始讲述实际应用中的实现过程。
+ 首先，我们先看评论功能包括哪些部分。基本的评论功能由以下两个部分组成：
  + 评论内容区域，它是一个从服务端读取的列表，每一条评论都包括用户名、时间和内容；
  + 评论编辑区域，除了显示自己的用户名外，需要一个待编辑的文本框以及“发布”按钮。

```tsx
  import React, { Component, PropTypes } from 'react'; 
  class CommentList extends Component { 
    constructor(props) { 
      super(props); 
      this.state = { 
        loading: true, 
        error: null, 
        value: null, 
      }; 
    } 
    componentDidMount() { 
      this.props.promise.then(response => response.json()) 
        .then(value => this.setState({ loading: false, value })) 
        .catch(error => this.setState({ loading: false, error })); 
    } 
    render() { 
      if (this.state.loading) { 
        return <span>Loading...</span>; 
      } else if (this.state.error !== null) { 
        return <span>Error: {this.state.error.message}</span>; 
      } else { 
        const list = this.state.value.commentList; 
        return ( 
          <ul className="comment-box"> 
            {list.map((entry, i) => ( 
              <li key={reponse-${i}} className="comment-item"> 
                <p className="comment-item-name">{entry.name}</p> 
                <p className="comment-item-content">{entry.content}</p> 
              </li> 
            ))} 
          </ul> 
        ); 
      } 
    } 
  } 
  ReactDOM.render( 
    <CommentList promise={fetch('/api/response.json')} />, 
    document.getElementById('root')); 
  
  ```

+ 看一下这个组件构成，可以发现它把 **数据请求与业务逻辑** 混合在了一起。
+ 显然，业务逻辑不需要关心数据是从哪里来的，只需要定义好传入的接口就行了，数据应该抽象到其他地方去做。
+ 为了不引入更多概念，把数据请求抽象到父组件中。含有抽象数据而没有业务逻辑的组件，我们称之为 **容器型组件**（container component）；
+ 而没有数据请求逻辑只有业务逻辑的组件，我们称之为 **展示型组件**（presentational component）。
+ 在 5.5 节中，我们会综合讲述两者的不同意义。
+ 接着，我们就来改造一下。抽象 CommentListContainer 组件：

  ```tsx
  import React, { Component, PropTypes } from 'react'; 
  class CommentListContainer extends Component { 
    constructor(props) { 
      super(props); 
      this.state = { 
        loading: true, 
        error: null, 
        value: null, 
      }; 
    } 
    componentDidMount() { 
      this.props.promise.then(response => response.json()) 
        .then(value => this.setState({ loading: false, value })) 
        .catch(error => this.setState({ loading: false, error })); 
    } 
    render() { 
      if (this.state.loading) { 
        return <span>Loading...</span>; 
      } else if (this.state.error !== null) { 
        return <span>Error: {this.state.error.message}</span>; 
      } else { 
        const list = this.state.value.commentList; 
        return ( 
          <CommentList comments={list} /> 
        ); 
      } 
    } 
  } 
  ```

+ 然后我们的业务逻辑就变得非常轻量级。用无状态组件即可实现，此时代码变得非常简洁：

  ```tsx
  import React, { Component, PropTypes } from 'react'; 
  function CommentList({ comments }) { 
    return ( 
      <ul className="comment-box"> 
        {comments.map((entry, i) => ( 
          <li key={`reponse-${i}`} className="comment-item"> 
            <p className="comment-item-name">{entry.name}</p> 
            <p className="comment-item-content">{entry.content}</p> 
          </li> 
        ))} 
      </ul> 
    ); 
  } 
  ```

+ 再来看下请求的容器组件，是否看到了很多重复代码的痕迹？**异步请求** 的过程每个组件都有可能存在，因此不得不写很多重复代码。当然，这个过程可以被抽象，我们继续使用 2.5 节中介绍的方便提取组件的“公共行为”，让组件可以进一步抽象：

## MV* 与 Flux

+ 响应式网页设计（Responsive Web design）①简称 RWD，是 2011 年提出的概念，并从 2012 年开始成为公认的网页设计主流方向。它是一种网页设计的技术做法，该设计可使网站在多种浏览设备（从台式机显示器到移动电话或其他移动产品设备）上获得体验类似的阅读与导航功能，同时减少用户缩放、平移与滚动等操作。
+ 这就要求网站需要设计成 SPA（Single-Page Application，单页应用）。尽管说响应式网页设计的核心理念是从交互以及样式上体现的，但对于整个跨平台的兼容性，拥有良好的分层解耦及响应速度已经成为应用架构的必要条件。
+ 而这之中，以 BackboneJS、AngularJS 为代表的 MVC/MVVM 和 Flux 渐渐成为了主流选择。

### MVC/MVVM

+ MVC/MVVM 简称 MC* 模式，其中 MVVM 是从 MVC 演进而来的。要理解这之间的关系，还得从 MVC 的概念说起。
+ MVC 是一种 **架构设计模式**，它通过关注 **数据界面分离**，来鼓励改进应用程序结构。具体地说，MVC 强制将业务数据（Model）与用户界面（View）隔离，用控制器（Controller）管理逻辑和用户输入。这种模式是 Smalltalk 在 20 世纪 80 年代研究设计出来的，如图 4-1 所示。
+ ![1552128187991](/img/user/programming/font-end/framework/react/redux-advanced/1552128187991.png)

#### Model

+ Model 负责保存应用数据，和后端交互同步应用数据，或校验数据。
+ Model 不涉及用户界面，也不涉及表示层，而是代表应用程序可能需要的独特形式的数据。
+ 当 Model 改变时，它会通知它的观察者（如视图）作出相应的反应。
+ 总的来说，Model 主要与 **业务数据** 有关，与 **应用内交互状态** 无关。

#### View

+ View 是 Model 的可视化表示，表示当前状态的视图。
+ 前端 View 负责 **构建和维护 DOM 元素**。
+ View 对应用程序中的 Model 和 Controller 的了解是有限的，更新 Model 的实际任务都是在 Controller 上。
+ 用户可以与 View 交互，包括读取和编辑 Model，在 Model 中获取或设置属性值。
+ 一个 View 通常对应一个 Model，并在 Model 更改时进行通知，使 View 本身能够进行相应的更新。但在实际应用开发中，还会面临多个 View 对应多个 Model 的情况。
+ 在前端 MVC 体系中，**View 对应的是 JavaScript 模板语言**，它用于将 View 定义为包含模板变量的标记，使用变量语法，接受 JSON 数据格式的数据。
+ 而 React 本身具备模板这一特性，这一点在第 1 章中已经提过。

#### Controller

+ 负责连接 View 和 Model，Model 的任何改变会应用到 View 中，View 的操作会通过 Controller 应用到 Model 中。
+ 在前端 MVC 框架中，Controller 的设计和传统 MVC 中的概念还是不太一样。如 Backbone，包含 Model 和 View，但它实际上并没有真正的 Controller。其 View 和路由的行为与 Controller 有些类似，但它们实际上都不是 Controller.
+ 总的来说，**Controller 管理了应用程序中 Model 和 View 之间的逻辑和协调**。

#### MVVM 的演变

+ MVVM 出现于 2005 年，最大变化在于 VM（ViewModel）代替了 C（Controller）。其关键“改进”是数据绑定（Data Binding），也就是说，View 的数据状态发生变化可以直接影响 VM，反之亦然。这也可以说是 AngularJS 的核心特色之一。MVVM 模型如图 4-2 所示。
+ ![1552128482471](/img/user/programming/font-end/framework/react/redux-advanced/1552128482471.png)

#### MVC 的问题

+ MVC 乍一看似乎没有特别值得诟病的地方，但是它存在一个致命的缺点，这个缺点在你的项目越来越大、逻辑越来越复杂的时候就非常明显，那就是混乱的数据流动方式，如图 4-3 所示。
+ ![1552128533405](/img/user/programming/font-end/framework/react/redux-advanced/1552128533405.png)
+ 以 Backbone 为例，由于 Model 对外直接暴露了 set 和 on 方法，导致 View 层可以随意改变 Model 中的值，也可以随意监听 Model 中值的变化。这样的设定最终会导致一个庞大的 Model 中某个字段变化后，可能触发无数个 change 事件。在这些 change 事件的回调中，可能还有新的 set 方法调用，导致更多的 change 事件触发。
+ 更糟糕的是，一个 Model 还能改变另一个 Model 的值，整个数据流动的方式变得更加混乱，不可捉摸。可以预见，在这种复杂的监听和触发的关系中，梳理数据的流动方式，甚至调试业务逻辑都成了一种奢望。
+ 对于增、删、改来说，MVC 都需要编写 View 渲染处理函数。当业务逻辑变复杂后，可能会有很多 Model 需要做增、删、改。与之对应的是，我们需要精心构建 View 渲染处理函数。尽管局部更新模式是高性能的关键所在，但这点会导致更新逻辑复杂，并需要编写大量的局部渲染函数，也会导致问题定位困难。页面的当前状态是由数据和局部更新函数来确定的。
+ 在实际应用中，前端 MVC 模式的实现各有各的理解。在 Google Images 中搜索“前端 MVC”，从得到的结果可以看到，几乎每个人对 Model、View 和 Controller 都有自己的理解，而它们之间的连线更是千奇百怪。

#### 解决方案

+ 如果 **渲染函数** 只有一个，统一放在 Controller 中，每次更新重渲染页面，这样的话，任何数据的更新都只用调用重渲染就行，并且数据和当前页面的状态是唯一确定的。这样又要保证数据的流动清晰，不能出现交叉分路的情况。
+ 然而重渲染会带来严重的性能与用户体验问题。重渲染和局部渲染各有好坏，对 MVC 来说这是一个两难的选择，无法做到鱼和熊掌兼得。

### Flux 的解决方案

+ 与 React 相同，Flux 同样由一群 Facebook 工程师提出，它的名字是拉丁语的 Flow。Flux 的提出主要是针对现有前端 MVC 框架的局限总结出来的一套基于 **dispatcher** 的前端应用架构模式。如果用 MVC 的命名习惯，它应该叫 ADSV（Action Dispatcher Store View）。
+ 那么 Flux 是如何解决 MVC 存在的问题呢？正如其名，Flux 的核心思想就是数据和逻辑永远 **单向流动**。其模型图如图 4-4 所示。
+ ![1552128763749](/img/user/programming/font-end/framework/react/redux-advanced/1552128763749.png)
+ 在介绍 React 的时候，我们也提到它推崇的核心也是单向数据流，Flux 中单向数据流则是在整体架构上的延伸。在 Flux 应用中，数据从 action 到 dispatcher，再到 store，最终到 view 的路线是 **单向不可逆** 的，各个角色之间不会像前端 MVC 模式中那样存在交错的连线。
+ 然而想要做到单向数据流，并不是一件容易的事情。好在 Flux 的 dispatcher 定义了严格的规则来限定我们对数据的修改操作。同时，store 中不能暴露 setter 的设定也强化了数据修改的纯洁性，保证了 store 的数据确定应用唯一的状态。
+ 再使用 React 作为 Flux 的 view，虽然 **每次 view 的渲染都是重渲染**，但并不会影响页面的性能，因为重渲染的是 Virtual DOM，并由 Pure Render 保障从重渲染到局部渲染的转换。意味着完全不用关心渲染上的性能问题，增、删、改的渲染都和初始化渲染一样快。
+ Flux 看起来非常完美，那么它真得比 MVC 好吗？事实上，在前端领域，Flux 还是一个处于非常早期的架构方式。
+ 对于一些逻辑复杂的前端应用（比如 Firefox 中的调试器），Flux 已经证明了自己确实能够极大地降低复杂度。但是对于许多原本使用 MVC 方式架构都绰绰有余的项目来说，**Flux 看起来像是杀鸡用牛刀**。
+ 我们现在无法断言 Flux 一定在任何场景下都优于 MVC，甚至在某些简单的应用中，你会发现使用 Backbone 等传统 MVC 框架解决起来会更加顺手。
+ 甚至在开源社区中，有人认为前端的 Flux 架构与早期 Win32 中的 WndProc() 窗口过程函数的设计颇为类似①。
+ 但是这些都不能掩盖 Flux 作为一种全新的前端架构方式给我们带来的思想上的冲击与转变。Flux 强调单向数据流，强调谨慎可追溯的数据变动，这些设计和约束都使得前端应用在越来越复杂的今天不会失去清晰的逻辑和架构。

## Flux 基本概念

+ 了解了为什么我们会选择 Flux 模式之后，下面来讲述它的基本概念和组成。
+ 一个 Flux 应用由 3 大部分组成——dispatcher、store 和 view，其中
  + **dispatcher 负责分发事件**；
  + **store 负责保存数据，同时响应事件并更新数据**；
  + **view 负责订阅 store 中的数据，并使用这些数据渲染相应的页面**。
+ 尽管它看起来和 MVC 架构有些像，但其中并 **没有一个职责明确的 controller**。事实上，Flux 中存在一个 **controller-view** 的角色，但它的职责是 **将 view 与 store 进行绑定**，并没有传统 MVC 中 controller 需要承担的复杂逻辑。
+ 图 4-5 是 Flux 应用的简化执行流程，下面我们将依次介绍各个节点的作用。
  + ![1552129065774](/img/user/programming/font-end/framework/react/redux-advanced/1552129065774.png)

### Dispatcher 与 Action

+ 如果你熟悉 Backbone 的话，肯定对 Backbone 的事件机制印象深刻。与 Backbone 的 **发布/订阅** 模式不同，Flux 中的事件会由若干个 **中央处理器来进行分发**，这就是 **dispatcher**。
+ dispatcher 是 Flux 中 **最核心的概念**，也是 flux 这个 npm 包中的核心方法。
+ 事实上，dispatcher 的实现非常简单，我们只需要关心 **.register(callback)** 和 **.dispatch(action)** 这两个 API 即可。
+ register 方法用来注册一个监听器，而 dispatch 方法用来分发一个 action。
+ action 是一个普通的 JavaScript 对象，一般包含 type、payload 等字段，**用于描述一个事件以及需要改变的相关数据**。比如点击了页面上的某个按钮，可能会触发如下 action：
  + ```js
    { 
      "type": "CLICK_BUTTON" 
    } 
    
    ```
+ 这是 action 最简单的一种形式。在实际应用中，一个 action 还可能包含更多的信息，比如某个操作对应的用户 ID、当前操作是否出现错误的标志位等。
+ 在开源社区中，有一套关于 Flux 中 action 对象该如何定义的规范，称为 FSA（Flux Standard Action）①。该规范定义了一个 Flux action 必须拥有一个 type 字段，可以拥有 error、payload 或 meta 字段。除此之外，不能有其他额外的字段。
+ > Flux Standard Action，详见 https://github.com/acdlite/flux-standard-action

### Store

+ 在 Flux 中，store 负责保存数据，并定义 **修改数据的逻辑**，同时 **调用 dispatcher 的 register 方法将自己注册为一个监听器**。这样每当我们使用 dispatcher 的 dispatch 方法分发一个 action 时，store 注册的监听器就会被调用，同时得到这个 action 作为参数。
+ store 一般会根据 action 的 type 字段来确定是否响应这个 action。**若需要响应，则会根据 action 中的信息修改 store 中的数据，并触发一个更新事件**。
+ 需要特别说明的是，在 Flux 中，store 对外 **只暴露 getter（读取器）而不暴露 setter（设置器）**，这意味着在 store 之外你只能读取 store 中的数据而不能进行任何修改。

### Controller-view

+ 虽然说 Flux 的 3 大部分是 dispatcher、store 和 view，但是在这三者之间存在着一个简单却不可或缺的角色——controller-view。顾名思义，它既像 controller，又像 view，那么 controller-view 究竟在 Flux 中发挥什么样的作用呢？
+ 一般来说，controller-view 是整个 **应用最顶层的 view**，这里不会涉及具体的业务逻辑，主要进行 store 与 React 组件（即 view 层）之间的 **绑定**，**定义数据更新及传递的方式**。
+ controller-view 会调用 store 暴露的 getter 获取存储其中的数据并 **设置为自己的 state**，在 render 时以 props 的形式传给自己的子组件（this.props.children）。
+ 介绍 store 时我们说过，当 store 响应某个 action 并更新数据后，会触发一个 **更新事件**,这个更新事件就是在 controller-view 中进行监听的。当 store 更新时，controller-view 会重新获取 store 中的数据，然后 **调用 setState 方法** 触发界面重绘。这样所有的子组件就能获得更新后 store 中的数据了。

### View

+ 在绝大多数的例子里，view 的角色都由 React 组件来扮演，但是 Flux 并没有限定 view 具体的实现方式。因此，其他的视图实现依然可以发挥 Flux 的强大能力，例如结合 Angular、Vue 等。
+ 在 Flux 中，view 除了显示界面，还有一条特殊的约定：如果界面操作需要修改数据，则 **必须使用 dispatcher 分发一个 action**。事实上，除了这么做，没有其他方法可以在 Flux 中修改数据。
+ 这条限制对刚接触 Flux 的开发者来说难以理解。因为在 React 中需要修改数据的时候，直接调用 this.setState 方法即可。如果需要分发 action，那么 action 是什么样的，分发到哪里，由谁来处理，View 层如何更新？这些疑问我们会在 4.4 节中一一讲解。目前只需要知道 F**lux 中的 view 层不能直接修改数据就可以了。**
  + 这里指的是 store 中的数据

### Action Creator

+ 与 controller-view 一样，actionCreator 并不是 Flux 的核心概念，但在许多关于 Flux 的例子和文章中都会看到这个名词，因此有必要解释一下。action Creator，顾名思义，就是用来创造 action 的。为什么需要 actionCreator 呢？因为在 **很多时候我们在分发 action 的时候代码是冗余的**。
+ 考虑一个点赞的操作，如果用户给某条微博点了赞，可能会分发一个这样的 action：

  ```js
  { 
    type: 'CLICK_UPVOTE', 
    payload: { 
      weiboId: 123, 
    }, 
  } 
  ```

+ 而包含完整分发逻辑的代码更加复杂：

  ```js
  import appDispatcher from '../dispatcher/appDispatcher'; 
  // 响应点赞的 on Click 方法 
  ... 
  handleClickUpdateVote(weiboId) { 
    appDispatcher.dispatch({ 
      type: 'CLICK_UPVOTE', 
      payload: { 
        weiboId: weiboId, 
      }, 
    }); 
  } 
  ... 
  
  ```

+ 事实上，在分发 action 的 6 行代码中，只有 1 行是变化的，其余 5 行都固定不变，这时我们可以创建一个 actionCreator 来帮减少冗余的代码，同时方便重用逻辑：

  ```js
  // actions/AppAction.js
  import appDispatcher from '../dispatcher/appDispatcher'; 
  function upvote(weiboId) { 
    appDispatcher.dispatch({ 
      type: 'CLICK_UPVOTE', 
      payload: { 
        weiboId: weiboId, 
      }, 
    }); 
  } 
  // components/Weibo.js
  import { upvote } from '../actions/AppAction'; 
  ... 
  handleClickUpdateVote(weiboId) { 
    upvote(weiboId); 
  } 
  ... 
  
  ```

+ 可以看到，在 view 中，分发 action 变得异常简洁。同时当我们需要修改 upvote 的逻辑时，只需要在 action Creator 中进行修改即可，所有调用 upvote 的 view 都无需变动。

## Flux 应用实例

+ 在 4.1 节中，我们实现了一个评论框组件，这个组件包含了已有评论的列表和评论表单两个组件。接下来，我们会对它进行简单的修改，让它成为我们实现的第一个 Flux App。
+ ![1552133219822](/img/user/programming/font-end/framework/react/redux-advanced/1552133219822.png)
+ 可以看到，在目录结构的 **最顶层**，我们按照传统的方式区分了 JavaScript 文件和 CSS 文件。
+ 不过在后面的例子中，你会发现对于 React 组件来说，把 JavaScript 和 CSS 放在一起是更高效和方便的方式。在本例中，为了理解方便，依然使用传统的划分方式。
+ 在 JavaScript 中，基本上就是按照我们在 4.3 节中讲到的各种概念划分了目录结构，不过有些许不同。
+ 首先，没有名为 view 的文件夹，取而代之的是 components 文件夹。**所有视图相关的组件都放在 components 中**，包括之前提到的与 store 进行绑定的 controller-view。
+ 其次，多了一个名为 constants 的文件夹。为什么需要 constants 呢？不知你是否注意到，在

  Flux 中是通过 type 字段来区分不同的 action 的，那么这些 type 字段势必会成为字符串常量。既

  然是常量，在所有 actionCreator 出现时，都应该保持一致。因此，抽出常量统一放在 constants 文

  件夹中便是一个不错的选择。

+ 而在 **app.js** 中，我们会把 components 中的 **controller-view** 使用 ReactDOM 的 render 方法渲染到真实的 DOM 中。

### 设计 Store

+ 在之前的例子中，所有的数据都是保存在组件的 state 中。而在 Flux 模式下，数据需要被迁移到 store 里：

  ```js
  // store/CommentStore.js 
  let comment = []; 
  ```

+ 可以看到，store 里保存的数据被直接定义在模块中，没有调用任何方法，它们是原生的 JavaScript 变量。
+ 有了初始化的变量还不够，我们还需要定义数据的修改逻辑。在这个例子中，store 中修改数据的逻辑十分简单，就是从服务器取出最新的评论列表即可：

  ```js
  // store/CommentStore.js
  function loadComment(newComment) { 
    comment = newComment; 
  } 
  ```

+ 与数据一样，修改数据的方法也是平淡无奇的方法，接受新的评论列表作为参数，并将它赋给 store 中保存的 comment。
+ 如果熟悉 Node 中模块的概念，会发现我们的 store 中没有导出任何内容。这意味着其他模块在引入 store 时，将会得到 undefined。因此，我们的 store 需要一个统一的出口：

  ```js
  // store/CommentStore.js
  import { EventEmitter } from 'events'; 
  import assign from 'object-assign'; 
  const CommentStore = assign({}, EventEmitter.prototype, { 
    getComment() { 
      return comment; 
    }, 
    emitChange() { 
      this.emit('change'); 
    }, 
    addChangeListener(callback) { 
      this.on('change', callback); 
    }, 
    removeChangeListener(callback) { 
      this.removeListener(callback); 
    } 
  }); 
  export default CommentStore; 
  ```

+ 在这部分 store 的设计中，我们引入了 Node 自带的 events 模块中的 EventEmitter，并使用 assign 方法将 EventEmitter 的功能混入 CommentStore 中，这样 store 就拥有了事件触发和监听的功能。当然，从更优雅的角度来讲，也可以用类来实现。
+ 而在具体的 store 中，getComment 方法会返回 store 中保存的所有评论数据，也就是前面提到的 store 中对外暴露的 getter。此外，还有 emitChange、addChangeListener 和 removeChangeListener 这 3 个方法，我们会在 controller-view 中看到它们的具体作用。
+ 最后，我们将 store 暴露出去，这样使用 store 的地方就可以调用 CommentStore.getComment() 方法获取 store 中保存的评论数据了。
+ 似乎还少点什么？是的，store 中的数据如何修改呢？没错，我们还需要调用 dispatcher 的 register 方法注册一个监听器，用于响应具体的 action：

  ```js
  import AppDispatcher from '../dispatcher/AppDispatcher'; 
  import CommentConstants from '../constants/CommentConstants'; 
  const CommentStore = { 
    // 具体实现见前面 
  }; 
  AppDispatcher.register((action) => { 
    switch (action.type) { 
      case CommentConstants.LOAD_COMMENT_SUCCESS: { 
        comment = action.payload.comment.commentList; 
        CommentStore.emitChange(); 
      } 
    } 
  }); 
  export default CommentStore; 
  ```

+ 在我们注册的回调中，针对 LOAD_COMMENT_SUCCESS 这个类型的事件修改了 store 中的数据，这也是例子中唯一一处数据的修改逻辑。

### 设计 actionCreator

+ 在 Flux 中，一个 action 的触发意味着需要修改数据，那么在我们的评论框中，有哪些数据

  要修改呢？一个是提交新的评论，一个是从服务端获取评论列表。下面让我们分别为这两个方法

  设计 actionCreator：

  ```js
  // actions/CommentActions.js
  import AppDispatcher from '../dispatcher/AppDispatcher'; 
  import CommentConstants from '../constants/CommentConstants'; 
  const CommentActions = { 
    loadComment() { 
      AppDispatcher.dispatch({ 
        type: CommentConstants.LOAD_COMMENT, 
      }); 
      fetch('/api/response.json') 
        .then((res) => { 
        return res.json(); 
      }) 
        .then((value) => { 
        AppDispatcher.dispatch({ 
          type: CommentConstants.LOAD_COMMENT_SUCCESS, 
          payload: { 
            comment: value, 
          }, 
        }); 
      }) 
        .catch((err) => { 
        AppDispatcher.dispatch({ 
          type: CommentConstants.LOAD_COMMENT_ERROR, 
          error: err, 
        }); 
      }); 
    }, 
    addComment(text) { 
      AppDispatcher.dispatch({ 
        type: CommentConstants.ADD_COMMENT, 
      }); 
      fetch('/api/submit.json', { 
        method: 'POST', 
        body: JSON.stringify({value: encodeURI(text)}), 
        headers: { 
          'Accept': 'application/json', 
          'Content-Type': 'application/json', 
        }, 
      }) 
        .then((res) => { 
        return res.json(); 
      }) 
        .then((value) => { 
        if (value.ok) { 
          AppDispatcher.dispatch({ 
            type: CommentConstants.ADD_COMMENT_SUCCESS, 
            payload: { 
              comment: value, 
            }, 
          }); 
          this.loadComment(); 
        } 
      }) 
        .catch((err) => { 
        AppDispatcher.dispatch({ 
          type: CommentConstants.ADD_COMMENT_ERROR, 
          error: err, 
        }); 
      }); 
    } 
  }; 
  export default CommentActions; 
  
  ```

+ actionCreator 中的代码看起来似乎比 store 中复杂了不少。仔细观察会发现，我们定义的两个 action-Creator 有着同样的套路，都使用了 fetch 来发送 Ajax 请求，在发送请求前分发了一个 action，在请求成功响应后分发了一个 action，在请求出现异常时同样分发了 action。
+ 你可能觉得这种设计有些冗余，但是它却能给用户界面带来更高的可控制性。在应用开发中，经常存在一些耗时比较久的异步操作。为了更好的用户体验，我们需要知道什么时候应该展示加载中的图标，什么时候又应该把它隐藏以免影响用户的正常操作。这些状态的变化，都在 actionCreator 中得以体现。这里为了保持例子简单，我们并没有在 store 和 view 中处理这些状态，但是你应该了解并尽可能地显示这些状态。

### 构建 Controller-view

+ 设计好了 store 和 action，是时候把它们拼装到一起了：

  ```react
  // components/CommentBox.js
  import React, { Component } from 'react'; 
  import CommentStore from '../stores/CommentStore'; 
  import CommentList from './CommentList'; 
  import CommentForm from './CommentForm'; 
  class CommentBox extends Component { 
    constructor(props) { 
      super(props); 
      this._onChange = this._onChange.bind(this); 
      this.state = { 
        comment: CommentStore.getComment(), 
      }; 
    } 
    _onChange() { 
      this.setState({ 
        comment: CommentStore.getComment(), 
      }); 
    } 
    componentDidMount() { 
      CommentStore.addChangeListener(this._onChange); 
    } 
    componentWillUnmount() { 
      CommentStore.removeChangeListener(this._onChange); 
    } 
    render() { 
      return ( 
        <div> 
          <CommentList comment={this.state.comment} /> 
          <CommentForm /> 
        </div> 
      ); 
    } 
  } 
  export default CommentBox; 
  
  ```

+ 在我们的 controller-view 中，有 3 处值得注意的地方。
  + 定义了组件初始化的状态——使用 store 暴露给我们的 getComment 方法从 CommentStore

    中获取评论列表。

  + 在 componentDidMount 和 componentWillUnmount 中分别对 store 的 change 事件作了绑定及

    解绑，这也解释了为什么我们的 store 需要 EventEmitter。

  + 定义了一个 store 变化的回调函数，在这个回调函数里，**重新调用 store 的 getComment 方**

    **法获取最新的评论并调用 this.setState 方法更新 controller-view 自己的 state**。当然，这

    些 state 最终会作为 components 的 props 传递给各个子组件。

### 重构 View

+ 在 4.1 节的例子中，我们已经写好了 CommentList 和 CommentBox 两个组件，但是在 Flux 架构中，我们需要做一些简单的调整：
+ 对于 CommentList 来说，去除了原有的从 promise 中获取数据的逻辑。因为所有的数据都在 controller-view 中传递了进来，所以 CommentList 便可以作为一个与具体逻辑无关的 **纯展示组件**。
+ 但是 store 中默认的评论数据是一个空数组，怎样才能 **从服务器获取到已有的评论** 呢？很简单，我们之前在 CommentAction 中定义了从服务器获取数据的 loadComment 方法，直接在 CommentList 组件中调用该方法即可。
+ 同理，我们在 CommentForm 中引入了在 CommentAction 中定义的 addComment 方法来处理评论提交的问题。（服务器）
+ 你可能会好奇，Flux 中不是强调改变数据一定要分发 action 吗？为什么修改 textarea 中的值依然使用了 setState。实际上，这是一个设计上的权衡。如果明确地知道某个局部状态不会影响整个应用中的其他部分，也不需要初始化的时候进行赋值，那么出于简化实现的考虑，可以把这个 **状态保存在组件** 中。

### 添加单元测试

+ 在 2.8 节中，我们讲到了使用 Jest 或 Enzyme 对 React 组件进行单元测试。而在 Flux 应用中，除了 React 组件外，更核心的功能当属 store。那么我们如何为 Flux 中的 store 添加单元测试呢？
+ 我们知道，Flux 中的 store 为了限制对数据的随意更改，并没有对外暴露任何直接修改数据的接口。要想让 store 中的数据发生改变，store 必须使用 dispatcher 注册一个监听器，这样每当 dispatcher 分发一个 action 的时候，store 才会得到通知并相应地修改数据。
+ 为了在测试中模拟这种机制，我们需要用到一点技巧。
+ 在 2.8 节中我们说到，Jest 会自动模拟所有的依赖，这意味着在 Jest 测试用例中，require 的所有文件默认都是 Jest 帮我们伪造的。实际上，每一个 Jest 伪造的对象除了原本拥有的属性和方法外，还会给每一个被伪造的方法添加一个名为 **mock 的属性**，以方便我们在测试用例中观察测试的执行情况及执行相关的断言。在 mock 属性中，最重要的则是 calls 字段，这个字段用来表示当前被模拟的方法被调用时的参数。
+ 比如，应用有一个模块名为 myDeps，对外暴露了 foo 方法和 bar 属性：

  ```js
  const myDeps = { 
   foo() { 
     console.log('a'); 
    }, 
    bar: 1, 
  }; 
  module.exports = myDeps; 
  ```

+ 因此，在测试用例中，只要引用 myDeps，我们就可以使用 Jest 为我们伪造出来的新对象。而 deps.foo.mock 则是 Jest 额外为 foo 方法添加的属性，通过这个属性我们可以追踪 foo 方法被调用的情况。举个具体的例子，deps.foo.mock.calls[0] 可以获取到 foo 方法第一次被调用时传入的所有参数，这是一个数组。因此，显而易见的是，deps.foo.mock.calls\[0][0] 是该方法第一次被调用时传入的第一个参数。
+ 了解了 Jest 的这一特点后，回到 Flux store 的测试用例中。首先引入应用的 dispatcher，Jest 会自动对 dispatcher 的所有方法和属性进行模拟，然后拿到调用 dispatcher.register 方法时传入的第一个参数，即 store 的监听器：

  ```js
  import mockDispatcher from '../dispatcher/AppDispatcher'; 
  const listener = mockDispatcher.register.mock.calls[0][0]; 
  ```

+ 回想一下，在 store 中我们就是通过注册这个监听器来获取发生的变更——某一 action 被分发了。因此，我们只需要给 listener 传入特定的 action，再断言 store 中的数据变更符合预期即可。
+ 假设我们的应用是一个计数器，store 中保存的数据每当“加 1”的 action 发生时则自动加 1，并能通过 store.getCount() 方法获取。一个完整的例子如下：

  ```react
  jest.dontMock('../stores/MyStore'); 
  import AppDispatcher from '../dispatcher/AppDispatcher'; 
  import MyStore from '../store/MyStore'; 
  describe('MyStore', () => { 
    it('should add 1', () => { 
      const listener = AppDispatcher.register.mock.calls[0][0]; 
      // 模拟类型为 INCREMENT 的 action 被触发，断言 store 能够正常响应这个 action
      lisenter({ 
        type: 'INCREMENT' 
      }); 
      expect(MyStore.getCount()).to.equal(1); 
    }); 
  }); 
  
  ```

+ 到此，第一个 Flux App 已经基本完成。可以看到，Flux 强调的单向数据流在很大程度上让应用的逻辑变得更加清晰，而渲染的视图组件也让关注分离（Separation of Concerns）的设计思想得到了很好的贯彻执行。

## 解读 Flux

+ 当我们明白了 Flux 的设计思想和工作原理后，可能会感叹原来 Flux 如此简单。
+ 正如在介绍 Flux 基本概念时说的那样，Facebook 提供的 flux 依赖包里，核心只有一个 dispatcher。当然，在后续版本中，又增加了一些方便函数式编程的 utils 方法与语法糖。可以说，Flux 更像是一种 **设计模式**，而不是一个具体的框架或者库。

### Flux 核心思想

+ Flux 架构是优雅、简洁的，它合理利用了一些优秀的架构思维。
+ Flux 的中心化控制让人称道。中心化控制让所有的请求与改变都只能通过 action 发出，统一由 dispatcher 来分配。好处是 View 可以保持高度简洁，它不需要关心太多的逻辑，只需要关心传入的数据；中心化还控制了所有数据，发生问题时可以方便查询。比起 MVC 架构下数据或逻辑的改动可能来自多个完全不同的源头，Flux 架构追查问题的复杂度和困难度显然要小得多。
+ 此外，Flux 把 action 做了统一归纳，提高了系统抽象程度。不论 action 是由用户触发的，从服务端发起的，还是应用本身的行为，对于我们而言，它都只是一个动作而已。与 MVC 架构下不同的触发方式管理混乱相比，Flux 要优雅许多。

### Flux 的不足

+ 尽管 Flux 是刚刚推出不久的设计模式，开发者们已经开始发现 Flux 存在或多或少的设计缺陷。
+ 其中最令人诟病的就是 Flux 的 **冗余代码** 太多。虽然 Flux 源码中几乎只有 dispatcher 的现实，但是在每个应用中都需要手动创建一个 dispatcher 的示例，这还是让很多开发者觉得烦恼。
+ 说到底，Flux 给开发者提供的还是它的思想。由于 Flux 在很大程度上是一种很松散的设计约定，不同的开发者对 Flux 都会有自己的理解。因此，在这几年开源社区的讨论中，到处都充斥着各种各样对于 Flux 思想的不同实现，它们都在尝试解决 Flux 中没有提到或未解决的问题。

# 5. 深入 Reudx 应用架构

+ 从 Flux 身上我们领略到数据在 store、action creator、dispatcher 及 React 组件之间单向流动的美妙特性，但在它受到越来越多关注的同时，我们也发现了 Flux 的一些问题与不足。因此，优化和扩展 Flux 架构的方案不断涌现，其中不乏许多高质量的作品，如 reflux、fluxxor 等。但是很快，一个后起之秀脱颖而出，短短数月就在 GitHub 上收获近万 star，它就是 Redux。
+ 为什么 Redux 在短时间内就受到了如此高的追捧？Redux 和 Flux 相比有什么异同？如何从零开始搭建一个 Redux 应用？这些问题将在本章中一一为你揭晓。

## Redux 简介

+ 现在我们就从 Redux 是什么、Redux 的三大原则和 Redux 的核心 API 开始介绍 Redux，并说明 Redux 如何与 React 结合使用，以及它在 Flux 基础上的改变。

### Redux 是什么

+ 我们都知道 Flux 本身既不是库，也不是框架，而是一种应用的架构思想。而 Redux 呢，它的核心代码可以理解成一个库，但同时也强调与 Flux 类似的架构思想。
+ 从设计上看，Redux 参考了 Flux 的设计，但是对 Flux 许多冗余的部分（如 dispatcher）做了简化，同时将 Elm 语言中 **函数式编程** 的思想融合其中。
+ 非常有意思的是，Redux 是从一个实验开始的，作者 Dan Abramov 并没有想到 Redux 会变得如此重要又被广泛使用，他只是为了通过 Flux 思想解决他的 **热重载及时间旅行** 的问题而已。
+ Redux 本身非常简单，它的设计思想与 React 有异曲同工之妙，均是希望用最少的 API 实现最核心的功能。
+ 图 5-1 是 Redux 的核心运作流程，看起来比 Flux 要简单不少。因为 Redux 本身只把自己定位成一个**“可预测的状态容器”**，所以图 5-1 只能算是这个容器的运行过程。而一个完整的 Redux 应用的运作流程，远比图 5-1 复杂得多。
+ ![1552135393252](/img/user/programming/font-end/framework/react/redux-advanced/1552135393252.png)
+ “Redux”本身指 redux 这个 npm 包，它提供若干 API 让我们 **使用 reducer 创建 store**，并能够更新 store 中的数据或获取 store 中最新的状态。
+ 而“Redux 应用”则是指使用了 redux 这个 npm 包并结合了视图层实现（如 React）及其他前端应用必备组件（路由库、Ajax 请求库）组成的完整的类 Flux 思想的前端应用。

### Redux 三大原则

+ 想要理解 Redux，必须要知道 Redux 设计和使用的三大原则。

#### 单一数据源

+ 在传统的 MVC 架构中，我们可以根据需要创建无数个 Model，而 Model 之间可以互相监听、触发事件甚至循环或嵌套触发事件，这些在 Redux 中都是不允许的。
+ 因为在 Redux 的思想里，一个应用永远只有唯一的数据源。我们的第一反应可能是：如果有一个复杂应用，强制要求唯一的数据源岂不是会产生一个特别庞大的 JavaScript 对象。
+ 实际上，使用单一数据源的好处在于整个应用状态都保存在一个对象中，这样我们随时可以提取出整个应用的状态进行 **持久化**（比如实现一个针对整个应用的即时保存功能）。此外，这样的设计也为服务端渲染提供了可能。
+ 至于我们担心的数据源对象过于庞大的问题，可以在 5.6.8 节中看到 Redux 提供的工具函数 combine-Reducers 是如何化解的。

#### 状态是只读的

+ 这一点和 Flux 的思想不谋而合，不同的是在 Flux 中，**因为 store 没有 setter 而限制了我们直接修改应用状态的能力，而在 Redux 中，这样的限制被执行得更加彻底，因为我们压根没有 store**。
+ 在 Redux 中，我们并不会自己用代码来定义一个 store。取而代之的是，我们定义一个 reducer，它的功能 **是根据当前触发的 action 对当前应用的状态（state）进行迭代**，这里我们并没有直接修改应用的状态，而是 **返回了一份全新的状态**。
+ Redux 提供的 create Store 方法会根据 reducer 生成 store。
+ 最后，我们可以利用 store. dispatch 方法来达到修改状态的目的。

#### 状态修改均由纯函数完成

+ 这 是 Redux 与 Flux 在表现上的最大不同。 在 Flux 中 ，我们在 actionCreator 里调用 AppDispatcher.dispatch 方法来触发 action，这样不仅有冗余的代码，而且因为直接修改了 store 中

  的数据，将导致无法保存每次 **数据变化前后** 的状态。

+ 在 Redux 里，我们通过定义 reducer 来确定状态的修改，而每一个 reducer 都是纯函数，这意味着它没有副作用，即接受一定的输入，必定会得到一定的输出。
+ 这样设计的好处不仅在于 reducer 里对状态的修改变得简单、纯粹、可测试，更有意思的是，Redux 利用每次新返回的状态生成酷炫的时间旅行（time travel）调试方式，让跟踪每一次因为触发 action 而改变状态的结果成为了可能。

### Redux 核心 API

#### Reducer

+ Redux 的核心是一个 store，这个 store 由 Redux 提供的 **create Store(reducers[, initialState])** 方法生成。
+ 从函数签名看出，要想生成 store，必须要传入 reducers，同时也可以传入第二个可选参数初始化状态（initial State）。
+ 在继续了解 createStore 之前，让我们先认识一下 reducers。在上一章介绍 Flux 时我们说到，Flux 的核心思想之一就是不直接修改数据，而是分发一个 action 来描述发生的改变。那么，在 Redux 里由谁来修改数据呢？
+ 在 Redux 里，负责 **响应 action 并修改数据** 的角色就是 reducer。reducer 本质上是一个函数，其函数签名为 reducer(previousState, action) => newState。
+ 可以看出，reducer 在处理 action 的同时，还需要接受一个 previousState 参数。所以，reducer 的职责就是 **根据 previousState 和 action 计算出新的 newState**。
+ 在实际应用中，reducer 在处理 previousState 时，还需要有一个特殊的非空判断。很显然，reducer 第一次执行的时候，并没有任何的 previousState，而 reducer 的最终职责是返回新的 state，因此需要在这种特殊情况下 **返回一个定义好的 initialState**：

  ```js
  // My Reducer.js
  const initialState = { 
    todos: [], 
  }; 
  // 我们定义的 todos 这个 reducer 在第一次执行的时候，会返回 { todos: [] } 作为初始化状态 
  function todos(previousState = initialState, action) { 
    switch (action.type) { 
      case 'XXX': { 
        // 具体的业务逻辑 
      } 
      default: 
        return previousState; 
    } 
  } 
  ```

+ 根据 Dan Abramov 的说法，Redux 这个名字就是来源于 Reduce+Flux，可见 reducer 在整个 Redux 架构中拥有举足轻重的作用。

#### creatStore

+ 下面就是 Redux 中 **最核心的 API**——createStore：

  ```js
  import { createStore } from 'redux'; 
  const store = createStore(reducers); 
  ```

+ 通过 createStore 方法创建的 store 是一个对象，它本身又包含 4 个方法
  + getState()：获取 store 中当前的状态。
  + dispatch(action)：分发一个 action，并返回这个 action，这是唯一能改变 store 中数据的

    方式。

  + subscribe(listener)：注册一个监听者，它在 store 发生变化时被调用。
  + replaceReducer(nextReducer)：更新当前 store 里的 reducer，一般只会在开发模式中调用

    该方法。

+ 在实际使用中，我们最常用的是 getState() 和 dispatch() 这两个方法。至于 subscribe() 和 replaceReducer() 方法，一般会在 Redux 与某个系统（如 React）做桥接的时候使用。
+ 关于这 4 个方法的具体作用和实现，请参考 6.5 节。

### 与 React 绑定

+ 前面说到 Redux 的核心只有一个 createStore() 方法，但是仅仅使用这个方法还不足以让 Redux 在我们的 React 应用中发挥作用。我们还需要 react-redux 库——Redux 官方提供的 React 绑定。
+ 很多刚刚接触 React 和 Redux 的开发者可能会好奇，明明有了 Redux，为什么还需要 react-redux，为什么不把它们放在一起？事实上，这是一种前端框架或类库的架构趋势，即尽可能做到平台无关（platform agnostic）。
+ 我们在第 1 章中也提到过，即便是 React，也在 0.14 版本之后拆分了 React 和 React DOM 两个库。这样拆分的好处在于，一个类库从核心逻辑上、具体与平台相关的实现上这两个层面做了拆分，能保证核心功能做到最大程度的跨平台复用。
+ react-redux 提供了 **一个组件和一个 API** 帮助 Redux 和 React 进行绑定
  + 一个是 React 组件 <Provider/>
  + 一个是 connect()。
+ 关于它们，只需要知道的是， <Provider/> 接受一个 store 作为 props，它是整个 Redux 应用的 **顶层组件**，而 connect() 提供了在整个 React 应用的 **任意组件中获取 store 中数据的功能**。
+ 关于这两个方法，在 6.6 节中会有更详细的介绍。

### 增强 Flux 的功能

+ 我们在上一章中提到，Flux 的一个很大的不足在于定义的模式太过松散，这导致许多采用了 Flux 模式的开发者在实际开发过程中遇到一个很纠结的问题：**在哪里发请求，如何处理异步流？**
+ 在 Redux 中，这种异步 action 的需求可以通过 Redux 原生的 middleware 设计来实现。在 5.2 节中，我们将看到更多关于 Redux middleware 的介绍与使用。
+ 正如 Redux 官方代码库的介绍中所说，Redux 是一个可预测的状态容器（predictable state container）。
+ 简单地说，在摒弃了传统 MVC 的发布/订阅模式并通过 Redux 三大原则强化对状态的修改后，使用 Redux 可以让你的应用状态管理变得可预测、可追溯。在 5.6 节中，我们会以一个完整的例子来展示 Redux 应用是如何帮助我们 **优化数据修改过程** 以及 **梳理数据流动方式** 的。

## Redux Middleware

+ “It provides a third-party extension point between dispatching an action, and the moment it reaches

  the reducer.”

+ 这是 Dan Abramov 对 middleware 的描述。它提供了一个 **分类处理 action 的机会**。在 middleware 中，你可以检阅每一个流过的 action，挑选出特定类型的 action 进行相应操作，给你一次改变 action 的机会。

### Middleware 的由来

+ 图 5-2 表达的是 Redux 中一个简单的同步数据流动场景，点击 button 后，在回调中分发一个 action， reducer 收到 action 后，更新 state 并通知 view 重新渲染。单向数据流，看着没什么问题。
+ 但是，如果需要打印每一个 action 信息来调试，就得去改 dispatch 或者 reducer 实现，使其具有打印日志的功能。
+ 又比如，点击 button 后，需要先去服务端请求数据，只有等数据返回后，才能重新渲染 view，此时我们希望 dispatch 或 reducer 拥有异步请求的功能。
+ 再比如，需要异步请求数据返回后，打印一条日志，再请求数据，再打印日志，再渲染。
+ ![1552142075689](/img/user/programming/font-end/framework/react/redux-advanced/1552142075689.png)
+ 面对多样的业务场景，单纯地修改 dispatch 或 reducer 的代码显然不具有普适性，我们需要的是可以组合的、自由插拔的插件机制，这一点 Redux 借鉴了 Koa （它是用于构建 Web 应用的 Node.js 框架）里 middleware 的思想，详情可查阅附录 A。另外，Redux 中 reducer 更关心的是数据的转化逻辑，所以 **middleware 就是为了增强 dispatch 而出现的**。
+ 图 5-3 展示了应用 middleware 后 Redux 处理事件的逻辑，每一个 middleware 处理一个相对独立的业务需求，通过串联不同的 middleware 实现变化多样的功能。那么，后续我们就来讨论 middleware 是怎么写的，以及 **Redux 是如何让 middleware 串联起来** 的。
+ ![1552142128445](/img/user/programming/font-end/framework/react/redux-advanced/1552142128445.png)

### 理解 Middleware 机制

+ Redux 提供了 applyMiddleware 方法来 **加载** middleware，该方法的源码如下：

  ```js
  import compose from './compose'; 
  export default function applyMiddleware(...middlewares) { 
    return (next) => (reducer, initialState) => { 
      let store = next(reducer, initialState); 
      let dispatch = store.dispatch; 
      let chain = []; 
      var middlewareAPI = { 
        getState: store.getState, 
        dispatch: (action) => dispatch(action), 
      }; 
      chain = middlewares.map(middleware => middleware(middlewareAPI)); 
      dispatch = compose(...chain)(store.dispatch); 
      return { 
        ...store, 
        dispatch, 
      }; 
    } 
  } 
  ```

+ 然后再来看 logger middleware 的实现：

  ```js
  export default store => next => action => { 
    console.log('dispatch:', action); 
    next(action); 
    console.log('finish:', action); 
  } 
  ```

+ 接下来，我们就分 4 步来深入解析 middleware 的运行原理。

#### 函数式编程思想设计

+ middleware 的设计有点特殊，**是一个层层包裹的匿名函数**，这其实是函数式编程中的 currying，它是一种使用 **匿名单参数函数** 来实现多参数函数的方法。applyMiddleware 会对 logger 这个 middleware 进行层层调用，动态地将 store 和 next 参数赋值。
+ currying 的 middleware 结构的好处主要有以下两点。
  + 易串联：currying 函数具有延迟执行的特性，通过不断 currying 形成的 middleware 可以累积参数，再配合组合（compose）的方式，很容易 **形成 pipeline 来处理数据流**。
  + 共享 store：在 applyMiddleware 执行的过程中，store 还是旧的，但是因为闭包的存在，applyMiddleware 完成后，所有的 middleware 内部拿到的 store 是最新且相同的。
+ 另外，我们会发现 applyMiddleware 的结构也是一个多层 currying 的函数。借助 compose，applyMiddleware 可以用来和其他插件加强 createStore 函数：

  ```js
  import { createStore, applyMiddleware, compose } from 'Redux'; 
  import rootReducer from '../reducers'; 
  import DevTools from '../containers/DevTools'; 
  const finalCreateStore = compose( 
    // 在开发环境中使用的 middleware
    applyMiddleware(d1, d2, d3), 
    // 它会启动 Redux DevTools
    DevTools.instrument() 
  )(createStore); 
  ```

#### 给 Middleware 分发 Store

+ 通过如下方式创建一个普通的 store ：

  ```js
  let newStore = applyMiddleware(mid1, mid2, mid3, ...)(createStore)(reducer, null); 
  ```

+ 上述代码执行完后，applyMiddleware 方法 **陆续获得了 3 个参数**，第一个是 middlewares 数组

  [mid1, mid2, mid3, ...]，第二个是 Redux 原生的 createStore ，最后一个是 reducer。然后，我们可以看到 applyMiddleware 利用 createStore 和 reducer **创建了一个 store**。而 store 的 getState 方法和 dispatch 方法又分别被直接和间接地赋值给 middlewareAPI 变量 store：

  ```react
  const middlewareAPI = { 
    getState: store.getState, 
    dispatch: (action) => dispatch(action), 
  }; 
  chain = middlewares.map(middleware => middleware(middlewareAPI)); 
  
  ```

+ 然后，让每个 middleware 带着 middlewareAPI 这个参数分别执行一遍。执行完后，获得 chain 数组 [f1, f2, ... , fx, ..., fn]，它保存的对象是第二个箭头函数返回的匿名函数。因为是闭包，每个匿名函数都可以访问相同的 store，即 middlewareAPI。
+ middlewareAPI 中的 dispatch 为什么要用匿名函数包裹呢？
+ 我们用 applyMiddleware 是为了改造 dispatch，所以 applyMiddleware 执行完后，dispatch 是变化了的，而 middlewareAPI 是 applyMiddleware 执行中分发到各个 middleware 的，所以必须用匿名函数包裹 dispatch，这样只要 dispatch 更新了，middlewareAPI 中的 dispatch 应用也会发生变化。

#### 组合串联 Middleware

+ 这一层只有一行代码，却是 applyMiddleware 精华之所在：

  ```js
  dispatch = compose(...chain)(store.dispatch); 
  ```

+ 其中 compose 是函数式编程中的组合，它将 chain 中的所有匿名函数 [f1, f2, ... , fx, ..., fn] 组装成一个新的函数，即新的 dispatch。当新 dispatch 执行时，[f1, f2, ... , fx, ..., fn]，从右到左依次执行。Redux 中 compose 的实现是下面这样的，当然实现方式并不唯一：

  ```react
  function compose(...funcs) { 
   return arg => funcs.reduceRight((composed, f) => f(composed), arg); 
  } 
  ```

+ compose(...funcs) 返回的是一个匿名函数，其中 funcs 就是 chain 数组。当调用 reduceRight 时，依次从 funcs 数组的右端取一个函数 fx 拿来执行，fx 的参数 composed 就是前一次 fx+1 执行的结果，而第一次执行的 fn（n 代表 chain 的长度）的参数 arg 就是 store.dispatch。所以，当 compose 执行完后，我们得到的 dispatch 是这样的，假设 n = 3：

  ```react
  dispatch = f1(f2(f3(store.dispatch)))); 
  ```

+ 这时调用新 dispatch，每一个 middleware 就依次执行了。

#### 在 Middleware 中调用 Dispatch 会发生什么

+ 经过 compose 后，所有的 middleware 算是串联起来了。可是还有一个问题，在分发 store 时，我们提到过每个 middleware 都可以访问 store，即 middlewareAPI 这个变量，也可以拿到 store 的 dispatch 属性。那么，在 middleware 中调用 store.dispatch() 会发生什么，和调用 next() 有区别吗？现在我们来说明两者的不同：

  ```react
  const logger = store => next => action => { 
   console.log('dispatch:', action); 
    next(action); 
    console.log('finish:', action); 
  }; 
  const logger = store => next => action => { 
   console.log('dispatch:', action); 
    store.dispatch(action); 
    console.log('finish:', action); 
  }; 
  ```

+ 在分发 store 时我们解释过，middleware 中 store 的 dispatch 通过匿名函数的方式和最终 compose 结束后的新 dispatch 保持一致，所以，在 middleware 中调用 store.dispatch() 和在其他任何地方调用的效果一样。而在 middleware 中调用 next()，效果是进入下一个 middleware，如图 5-4 所示。
+ ![1552143763720](/img/user/programming/font-end/framework/react/redux-advanced/1552143763720.png)
+ 正常情况下，如图 5-4 左图所示，当我们分发一个 action 时，middleware 通过 next(action) 一层层处理和传递 action 直到 Redux 原生的 dispatch。如果某个 middleware 使用 store.dispatch (action) 来分发 action，就发生了如图 5-4 右图所示的情况，这相当于重新来一遍。假如这个 middleware 一 直简单 粗暴 地调用 store.dispatch(action) ， 就会形成无限循环了 。那么 store.dispatch(action) 的用武之地在哪里呢？
+ 假如我们需要发送一个异步请求到服务端获取数据，成功后弹出一个自定义的 message。这里我们用到了 Redux Thunk：

  ```js
  const thunk = store => next => action => 
  typeof action === 'function' ? 
        action(store.dispatch, store.getState) : 
  next(action) 
  ```

+ Redux Thunk 会判断 action 是否是函数。如果是，则执行 action，否则继续传递 action 到下一个 middleware。针对于此，我们设计了以下 action：

  ```js
  const getThenShow = (dispatch, getState) => { 
    const url = 'http://xxx.json'; 
    fetch(url) 
      .then((response) => { 
      dispatch({ 
        type: 'SHOW_MESSAGE_FOR_ME', 
        message: response.json(), 
      }); 
    }) 
      .catch(() => { 
      dispatch({ 
        type: 'FETCH_DATA_FAIL', 
        message: 'error', 
      }); 
    }); 
  }; 
  ```

+ 这时候只要在应用中调用 store.dispatch(getThenShow)，Redux Thunk 就会执行 getThenShow 方法。getThenShow 会先请求数据，如果成功，分发一个显示 message 的 action；否则，分发一个请求失败的 action。而这里的 dispatch 就是通过 Redux Thunk middleware 传递进来的。
+ 在 middleware 中使用 dispatch 的场景一般是接受到一个定向 action，这个 action 并不希望到达原生的分发 action，往往用在异步请求的需求里。在下一节中，我们会详细讨论如何在 Redux 中实现异步流。

## Redux 异步流

+ 曾经前端的革新是以 Ajax 的出现为分水岭，现代应用中绝大部分页面渲染会以异步流的方式进行。我们还记得在 Flux 中，并没有定义在哪里发异步请求，那么 Redux 是如何解决这个问题的呢？

### 使用 Middleware 简化异步请求

+ 在这一节中，我们通过介绍最常用的 3 个 middleware 来介绍 Redux 怎样发异步请求。

#### Redux-thunk

+ 我们试想，如果要发异步请求，在 Redux 定义中，最合适的位置是在 action creator 中实现。但我们之前了解到的 action 都是同步情况，那么怎样让 action 支持异步情况呢？
+ 这里引入了 redux-thunk middleware。首先我们需要知道什么是 thunk？其实在学习 Node.js 时，已经接触并熟悉 Thunk 函数了。比如：

  ```js
  fs.readFile(fileName, callback); 
  var readFileThunk = Thunk(fileName); 
  readFileThunk(callback); 
  var Thunk = function(fileName) { 
   return function(callback) { 
     return fs.readFile(fileName, callback); 
    }; 
  }; 
  ```

+ **Thunk 函数实现上就是针对多参数的 currying 以实现对函数的惰性求值**。任何函数，只要参数有回调函数，就能写成 Thunk 函数的形式。
+ 我们再来看看 redux-thunk 的源代码：

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

+ 我们很清楚地看到，当 action 为函数的时候，我们并没有调用 next 或 dispatch 方法，而是返回 action 的调用。这里的 action 即为一个 Thunk 函数，以达到将 dispatch 和 getState 参数传递到函数内的作用。
+ 了解 redux-thunk 的原理后，这里我们模拟请求一个天气的异步请求。action 通常可以这么写：

  ```js
  function getWeather(url, params) { 
    return (dispatch, getState) => { 
      fetch(url, params) 
        .then(result => { 
        dispatch({ 
          type: 'GET_WEATHER_SUCCESS', 
          payload: result, 
        }); 
      }) 
        .catch(err => { 
        dispatch({ 
          type: 'GET_WEATHER_ERROR', 
          error: err, 
        }); 
      }); 
    }; 
  } 
  ```

+ 我们顺利地把同步 action 变成了异步 action。
+ 尽管我们利用 Thunk 可以完成各种复杂的异步 action，但是对于某些复杂但是又有规律的场景，抽离出更合适的、目标更明确的 middleware 来解决会是更好的方案，而异步请求绝对是其一。

#### Redux-promise

+ 我们发现，异步请求其实都是利用 promise 来完成的，那么为什么不通过抽象 promise 来解决异步流的问题呢？
+ 这里再引入 redux-promise middleware，然后通过源码来分析一下它是怎么做的：

  ```js
  import { isFSA } from 'flux-standard-action'; 
  function isPromise(val) { 
    return val && typeof val.then === 'function'; 
  } 
  export default function promiseMiddleware({ dispatch }) { 
    return next => action => { 
      if (!isFSA(action)) { 
        return isPromise(action) 
          ? action.then(dispatch) 
        : next(action); 
      } 
      return isPromise(action.payload) 
        ? action.payload.then( 
        result => dispatch({ ...action, payload: result }), 
        error => { 
          dispatch({ ...action, payload: error, error: true }); 
          return Promise.reject(error); 
        } 
      ) 
      : next(action); 
    }; 
  } 
  ```

+ redux-promise 兼容了 FSA 标准，也就是说将返回的结果保存在 payload 中。实现过程非常容易理解，即判断 action 或 action.payload 是否为 promise，如果是，就执行 then，返回的结果再发送一次 dispatch。
+ 我们利用 ES7 的 async 和 await 语法，可以简化上述异步过程：

  ```js
  const fetchData = (url, params) => fetch(url, params); 
  async function getWeather(url, params) { 
    const result = await fetchData(url, params); 
    if (result.error) {
      return { 
        type: 'GET_WEATHER_ERROR', 
        error: result.error, 
      }; 
    } 
    return { 
      type: 'GET_WEATHER_SUCCESS', 
      payload: result, 
    }; 
  } 
  ```

#### Redux-composable-fetch

+ 在实际应用中，我们还需要加上 loading 状态。结合上述讨论的两个开源 middleware，我们完全可以自己实现一个更贴合工程需要的 middleware，这里将其命名为 redux-composable-fetch。
+ 在理想情况下，我们不希望通过复杂的方法去请求数据，而希望通过如下形式一并完成在异步请求过程中的不同状态：

  ```js
  { 
    url: '/api/weather.json', 
    params: { 
      city: encodeURI(city), 
    }, 
    types: ['GET_WEATHER', 'GET_WEATHER_SUCESS', 'GET_WEATHER_ERROR'], 
  } 
  
  ```

+ 可以看到，异步请求 action 的格式有别于 FSA。它并没有使用 type 属性，而使用了 types 属性。types 其实是三个普通 action type 的集合，分别代表请求中、请求成功和请求失败。
+ 在请求 middleware 中，会对 action 进行格式检查，若存在 url 和 types 属性，则说明这个 action 是一个用于发送异步请求的 action。此外，并不是所有请求都能携带参数，因此 params 是可选的。
+ 当请求 middleware 识别到这是一个用于发送请求的 action 后，首先会分发一个新的 action，这个 action 的 type 就是原 action 里 types 数组中的第一个元素，即请求中。分发这个新 action 的目的在于让 store 能够同步当前请求的状态，如 **将 loading 状态置为 true**，这样在对应的界面上可以展示一个友好的加载中动画。
+ 然后，请求 middleware 会根据 action 中的 url、params、method 等参数发送一个异步请求，并在请求响应后根据结果的成功或失败分别分发请求成功或请求失败的新 action。
+ 请求 middleware 的简化实现如下，我们可以根据具体的场景对此进行改造：

  ```js
  const fetchMiddleware = store => next => action => { 
    if (!action.url || !Array.isArray(action.types)) { 
      return next(action); 
    } 
    const [LOADING, SUCCESS, ERROR] = action.types; 
    next({ 
      type: LOADING, 
      loading: true, 
      ...action, 
    }); 
    fetch(action.url, { params: action.params }) 
      .then(result => { 
      next({ 
        type: SUCCESS, 
        loading: false, 
        payload: result, 
      }); 
    }) 
      .catch(err => { 
      next({ 
        type: ERROR, 
        loading: false, 
        error: err, 
      }); 
    }); 
  } 
  ```

+ 这样我们的确一步就完成了异步请求的 action。

### 使用 Middleware 处理复杂异步流

+ 在实际场景中，我们不但有短连接请求，还有轮询请求、多异步串联请求，或是在异步中加入同步处理的逻辑。这时候，使用 redux-composable-fetch 就显得力不从心了。

#### 轮询

+ 轮询是长连接的一种实现方式，它能够在一定时间内重新启动自身，然后再次发起请求。基于这个特性，我们可以在 redux-composable-fetch 的基础上再写一个 middleware，这里命名为 redux-polling：

  ```js
  import setRafTimeout, { clearRafTimeout } from 'setRafTimeout'; 
  export default ({ dispatch, getState }) => next => action => { 
    const { pollingUrl, params, types } = action; 
    const isPollingAction = pollingUrl && params && types; 
    if (!isPollingAction) { 
      return next(action); 
    } 
    let timeoutId = null; 
    const startPolling = (timeout = 0) => { 
      timeoutId = setRafTimeout(() => { 
        const { pollingUrl, ...others } = action; 
        const pollingAction = { 
          ...others, 
          url: pollingUrl, 
          timeoutId, 
        }; 
        dispatch(pollingAction).then(data => { 
          if (data && data.interval && typeof data.interval === 'number') { 
            startPolling(data.interval * 1000); 
          } else { 
            console.error('pollingAction should fetch data contain interval'); 
          } 
        }); 
      }, timeout); 
    }; 
    startPolling(); 
  } 
  export const clearPollingTimeout = (timeoutId) => { 
    if (timeoutId) { 
      clearRafTimeout(timeoutId); 
    } 
  }; 
  
  ```

+ 在这个 middleware 的实现中，我们用到了 raf 函数，在 2.7 节中我们已经提到过它。raf 是实现中的关键点之一，它可以让请求在一定时间内重新发起。
+ 另外，在 API 的设计上，我们还暴露了 clearPollingTimeout 方法，以便我们在需要时手动停止轮询。
+ 最后，调用 action 来发起轮询：

  ```js
  { 
   pollingUrl: '/api/weather.json', 
   params: { 
   	city: encodeURI(city), 
   }, 
   types: [null, 'GET_WEATHER_SUCESS', null], 
  } 
  ```

+ 至于长连接，还有其他多种实现方式，最好的方式是对其整体做一次封装，在内部实现诸如轮询和 WebSocket。

#### 多异步串联

+ 多异步串联是我们在应用场景中常见的逻辑，根据以往的经验，是不是很快就想到用 promise 去实现。
+ 我们试想，通过对 promise 封装是不是能够做到不论是否是异步请求，都通过 promise 来传递以达到一个统一的效果。的确，这一点非常容易就可以实现：

  ```js
  const sequenceMiddleware = ({dispatch, getState}) => next => action => { 
    if (!Array.isArray(action)) { 
      return next(action); 
    } 
    return action.reduce( (result, currAction) => { 
      return result.then(() => { 
        return Array.isArray(currAction) ? 
          Promise.all(currAction.map(item => dispatch(item))) : 
        dispatch(currAction); 
      }); 
    }, Promise.resolve()); 
  } 
  ```

+ 这里我们定义了一个名为 sequenceMiddleware 的 middleware。在构建 action creator 时，会传递一个数组，数组中每一个值都将是按顺序执行的步骤。这里的步骤既可以是异步的，也可以是同步的。
+ 在实现过程中，我们非常巧妙地使用 Promise.resolve() 来初始化 action.reduce 方法，然后始终使用 Promise.then() 方法串联起数组，达到了串联步骤的目的。
+ 这里还是使用之前的例子。假设我们的应用初始化时会先获取当前城市，然后获取当前城市的天气信息，那么就可以这么写：

  ```js
  function getCurrCity(ip) { 
    return { 
      url: '/api/getCurrCity.json', 
      params: { ip }, 
      types: [null, 'GET_CITY_SUCCESS', null], 
    } 
  } 
  function getWeather(cityId) { 
    return { 
      url: '/api/getWeatherInfo.json', 
      params: { cityId }, 
      types: [null, 'GET_WEATHER_SUCCESS', null], 
    }; 
  } 
  function loadInitData(ip) { 
    return [ 
      getCurrCity(ip), 
      (dispatch, state) => { 
        dispatch(getWeather(getCityIdWithState(state))); 
      }, 
    ]; 
  } 
  ```

+ 这种方法利用了数组的特性。可以看到，它已经覆盖了大部分场景。当然，如果串联过程中有不同的分支，就无能为力了。

#### Redux-saga

+ 在 Redux 社区，还有一个处理异步流的后起之秀，名为 redux-saga。它与上述方法最直观的不同就是用 generator 替代了 promise，我们通过 Babel 可以很方便地支持 generator：

  ```js
  function* getCurrCity(ip) { 
    const data = yield call('/api/getCurrCity.json', { ip }); 
    yield put({ 
      type: 'GET_CITY_SUCCESS', 
      payload: data, 
    }); 
  } 
  function* getWeather(cityId) { 
    const data = yield call('/api/getWeatherInfo.json', { cityId }); 
    yield put({ 
      type: 'GET_WEATHER_SUCCESS', 
      payload: data, 
    }); 
  } 
  function loadInitData(ip) { 
    yield getCurrCity(ip); 
    yield getWeather(getCityIdWithState(state)); 
    yield put({ 
      type: 'GET_DATA_SUCCESS', 
    }); 
  } 
  
  ```

+ redux-saga 的确是最优雅的通用解决方案，它有着灵活而强大的协程机制，可以解决任何复杂的异步交互。要想深入学习 redux-sage，请参考官方文档。

## Redux 与路由

+ 要开发一个富客户端应用，有一样东西是必不可少的——路由（router）系统。
+ 在过去，路由是服务端专有的部分。自从富客户端应用越来越广泛地出现在 Web 上，我们已经不能忽视前后端之间发生的巨大变化。SPA 应用也不例外，可以说，所有 SPA 都必然会由一个路由系统作为整个系统的入口。
+ 在 React 的生态环境中，React Router 是公认的最优秀的路由解决方案。它提供了与 React 思想十分贴合的声明式的路由系统。我们可以通过 <Router> 、 <Route> 这两个标签以及一系列属性定义整个 React 应用的路由方案。
+ 然而在 Redux 应用中，我们遇到了一些新的问题。其中最迫切的问题是，应用程序的所有状态都应该保存在一个单一的 store 中，而当前的路由状态很明显也属于应用状态的一部分。如果直接使用 React Router，就意味着所有路由相关的信息脱离了 Redux store 的控制，这样就违背了 Redux 的设计思想，也给我们的应用带来了更多的不确定性。
+ 所以，我们需要一个这样的路由系统，它既能利用 React Router 的声明式特性，又能将路由信息整合进 Redux store 中。
+ 本节中，我们将详细为大家介绍 React Router 和 react-router-redux 的使用方式、工作原理及最佳实践。

### React Router

+ 我们知道，React 不是一个前端应用框架，因此不像 Angular.js 或者 Ember.js 等集成了开发者可能需要的各种各样的功能，你必须选择符合自己需求且必要的部件才能打造一个完整的前端单页应用。
+ 而说到和 React 应用搭配的路由系统，非 React Router 莫属。事实上，React Router 在 Git Hub 上的代码库已经和 React 一样都归属于 reactjs Group 下。从某种意义上来说，React Router 已经成为官方认证的路由库了。

#### 路由的基本原理

+ 简单地说，路由的基本原理即是保证 View 和 URL 同步，而 View 可以看成是资源的一种表现。当用户在 Web 界面中进行操作时，应用会在若干个交互状态中切换，路由则会记录下某些重要的状态，比如在博客系统中用户是否登录、访问哪一篇文章、位于文章归档列表的第几页等。
+ 这些变化同样会被记录在浏览器的历史中，用户可以通过浏览器的“前进”、“后退”按钮切换状态，同样可以将 URL 分享给好友。简单地说，用户可以通过手动输入或者与页面进行交互来改变 URL，然后通过同步或者异步的方式向服务端发送请求获取资源，重新绘制 UI，如图 5-5 所示。
+ ![1552181022008](/img/user/programming/font-end/framework/react/redux-advanced/1552181022008.png)
+ 那么，React Router 和其他前端路由有什么区别呢？

#### React Router 的特性

+ React Router 中的很多特性都与 React 保持一致。回想一下，在 React 中，组件就是一个方法。props 作为方法的参数，当它们发生变化时会触发方法执行，进而帮助我们重新绘制 View。在 React Router 中，我们同样可以把 Router 组件看成一个方法，location 作为参数，返回的结果同样是 View，如图 5-6 所示。
+ ![1552192995737](/img/user/programming/font-end/framework/react/redux-advanced/1552192995737.png)

**声明式的路由**

+ 从图 5-6 中，我们很自然就可以联想到，React 带给我们最特别的编程体验就是声明式编程，所有的交互逻辑都在 render 返回的 JSX 标签中得到体现。而 React Router 很好地继承了 React 的这一特点，允许开发者使用 JSX 标签来书写声明式的路由。下面是一个简单的例子：

  ```react
  import { HashRouter, Route } from 'react-router'; 
  const routes = ( 
    <HashRouter history={browserHistory}> 
      <Route path="/" component={App} /> 
    </HashRouter> 
  ); 
  
  ```

+ 不用过多解释，我们就可以看出当前页面 url 为 / 时，React Router 会帮我们渲染 App 这个组件。

### 坑

- React Router 与 Redux 一起使用时大部分情况下都是正常的，但是偶尔会出现路由更新但是子路由或活动导航链接没有更新。这个情况发生在：

  1. 组件通过 `connect()(Comp)` 连接 redux。
  2. 组件不是一个“路由组件”，即组件并没有像 `<Route component={SomeConnectedThing} />` 这样渲染。

- 这个问题的原因是 Redux 实现了 `shouldComponentUpdate`，当路由变化时，该组件并没有接收到 props 更新。

  ```react
  // before
  export default connect(mapStateToProps)(Something)
  
  // after
  import { withRouter } from 'react-router-dom'
  export default withRouter(connect(mapStateToProps)(Something))
  
  作者：Mello_Z
  链接：https://juejin.im/post/5b4de4496fb9a04fc226a7af
  来源：掘金
  著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
  ```

### React Router Redux @3

+ 它的职责主要是将应用的 **路由信息与 Redux 中的 store 绑定在一起**。你可能会好奇为什么要这么做？
+ 答案很简单。因为对于前端应用来说，路由状态（当前切换到了哪个页面，当前页面的参数有哪些，等等）也是应用状态的一部分。在很多情况下，我们的业务逻辑与路由状态有很强的关联关系。比如，最常见的一个列表页中，分页参数、排序参数可能都会在路由中体现，而这些参数的改变必然导致列表中的数据发生变化。
+ 因此，当我们采用 Redux 架构时，所有的应用状态必须放在一个单一的 store 中管理，路由状态也不例外。而这就是 React Router Redux 为我们实现的主要功能。
+ 在 React Router@4 版本中，路由状态的绑定方式发生了巨大的变化

#### 将 React Router 与 Redux Store 绑定 @3

+ React Router Redux 提供了简单直白的 API——syncHistoryWithStore 来完成与 Redux store 的绑定工作。我们只需要传入 React Router 中的 history（前面提到的 browserHistory 或 hashHistory，甚至是自己创建的 history），以及 Redux 中的 store，就可以获得一个增强后的 history 对象。
+ 将这个 history 对象传给 React Router 中的 <Router> 组件作为 props，就给 React Router Redux 提供了观察路由变化并改变 store 的能力（反之亦然）：

  ```react
  import { browserHistory } from 'react-router' 
  import { syncHistoryWithStore } from 'react-router-redux' 
  import reducers from '<project-path>/reducers' 
  const store = createStore(reducers); 
  const history = syncHistoryWithStore(browserHistory, store); 
  ```

#### 用 Redux 的方式改变路由 @3

+ 无论是 Flux 还是 Redux，想要改变数据，必须要分发一个 action。前面又讲到了，路由状态作为应用状态数据的必要性。那么，在 Redux 应用中需要改变路由时，是不是也要分发一个 action 呢？答案是肯定的。
+ 但是在这之前，我们需要对 Redux 的 store 进行一些增强，以便分发的 action 能被正确识别：

  ```react
  import { browserHistory } from 'react-router'; 
  import { routerMiddleware } from 'react-router-redux'; 
  const middleware = routerMiddleware(browserHistory); 
  const store = createStore( 
    reducers, 
    applyMiddleware(middleware) 
  ); 
  ```

+ 首先，我们引入了 React Router Redux 提供的 routerMiddleware，它实际上是一个 **middleware 工厂**，传入 history 对象，返回一个真正的 Redux middleware。最终，在创建 Redux store 时，我们将这个 middleware 启用并作为第二个参数传入 createStore 方法，获得被 React Router Redux 加工过的新 store。
+ 最后，就可以用 store.dispatch 来分发一个路由变动的 action 了：

  ```react
  import { push } from 'react-router-redux'; 
  // 切换路由到 /home 
  store.dispatch(push('/home')); 
  ```

+ React Router 是一个“多变”的路由库，在 1.0 正式版之前，每一个小版本都会有大量的 API 变动，这也对开发者们的开发体验造成了极大的痛苦。但不论它怎么变，只要我们熟练掌握了其中的原理，就可以以不变应万变。

- 将 router 的数据与 store 同步，并且从 store 访问
- 通过 dispatch actions 导航
- 在 redux devtools 中支持路由改变的时间旅行调试
- 这些可以通过 connected-react-router 和 history 两个库将 react-router 与 redux 进行深度整合实现。
- > 官方文档中提到的是 react-router-redux，并且它已经被整合到了 react-router v4 中，但是根据 react-router-redux 的文档，该仓库不再维护，推荐使用 connected-react-router。

### 将 React Router 与 Redux Store 绑定 @4

- 首先安装 connected-react-router 和 history 两个库：

  ```json
  $ npm install --save connected-react-router
  $ npm install --save history
  ```

#### 合成组件 Reducer 和路由 Reducer

+ ```js
  import home from '../views/HomeRedux';
  import { combineReducers } from 'redux';
  import { connectRouter } from 'connected-react-router'
  
  // 暴露 createRootReducer 函数
  export default (history) => combineReducers({
    router: connectRouter(history),
    home,
  })
  ```
+ 暴露 createRootReducer 函数，用于在 store.js 接受 history 生成整合后的 reducer

#### 配置 Store

- 然后给 store 添加如下配置：
  - 创建 `history` 对象，因为我们的应用是浏览器端，所以使用 `createBrowserHistory` 创建
  - 提供我们创建的 `history` 对象给 `createRootRudecer` 函数，获得新的 root reducer
  - 使用 `routerMiddleware(history)` 实现使用 dispatch history actions，这样就可以使用 `push('/path/to/somewhere')` 去改变路由（这里的 push 是来自 connected-react-router 的）

  ```js
  /*
  redux最核心的管理对象模块
   */
  // Redux
  import {createStore, applyMiddleware} from 'redux'
  // middle
  import {composeWithDevTools} from 'redux-devtools-extension'
  import thunk from 'redux-thunk'
  import { createBrowserHistory } from 'history'
  import { connectRouter, routerMiddleware } from 'connected-react-router'
  // self
  import createRootReducer from './reducers'
  
  export const history = createBrowserHistory()
  
  // 向外暴露 createStore 函数
  export default function configureStore() {
    const store = createStore(
      createRootReducer(history), // root reducer with router state
      composeWithDevTools(
        applyMiddleware(
          thunk,
          routerMiddleware(history), // for dispatching history actions
          // ... other middlewares ...
        ),
      )
    )
    return store
  }
  ```

#### 配置根组件

- 在根组件中，我们添加如下配置：
  - 使用 `ConnectedRouter` 包裹路由，并且将 store 中创建的 `history` 对象引入，作为 props 传入应用
  - `ConnectedRouter` 组件要作为 `Provider` 的子组件

  ```react
  import React from 'react'
  import ReactDOM from 'react-dom'
  import { Provider } from 'react-redux';
  import { ConnectedRouter } from 'connected-react-router'
  
  import configureStore ,{ history } from './redux/configureStore';
  import Frame from './layouts/Frame'
  
  const store = configureStore(/* provide initial state if any */)
  
  ReactDOM.render(
    (
    <Provider store={store}>
      <ConnectedRouter history={history}>
        <Frame/>
      </ConnectedRouter>
    </Provider>
    ), 
    document.getElementById('root'));
  
  ```

- 这样我们就将 redux 与 react-router 整合完毕。
- layouts 组件下还有 Switch 等常规路由组件,但是不需要 HashRouter 两个了，已经配置好了 Router 和 history 属性

+ 参考 npm connected-react-router

#### 使用 Dispatch 切换路由

+ ![1552365822225](/img/user/programming/font-end/framework/react/redux-advanced/1552365822225.png)
+ 自动分发 action，不用像 v3 那样使用 store.dispatch(push('/about')) 方法了
+ 最后还有一点比较奇怪，那段 ?_k=0e9k95 是什么？我们知道在 URL 中 ? 之后表示的是 query，这段 query 事实上是 React Router 为了提供 **每一条路由记录持久化数据而生成的唯一标识**。

## Redux 与组件

+ 我们在 4.1 节中提到了两种类型的组件，一种是容器型组件，这种命名在第 1 章中提到过，另一种是展示型组件。要区分它们，主要是看是否有数据操作。
+ 在早期 Redux 版本中，作者将上述两种组件定义为 Smart 和 Dumb 组件，但是这个名字过于晦涩。为了可以通过名字清晰地区分出两者的不同，新名字就应运而生了。
+ 本节中，我们就来讨论一下这两种组件的定义与应用场景，以及与 Redux 的关系。

### 容器型组件

+ 容器型组件，意为组件是怎么工作的，更具体一些就是数据是怎么更新的。它不会包含任何 Virtual DOM 的修改或组合，也不会包含组件的样式。
+ 如果映射到 Flux 上，那么容器型组件就是与 store 作绑定的组件。如果映射到 Redux 上，那么容器型组件就是 **使用 connect 的组件**。因此，我们都在这些组件里作了数据更新的定义。

### 展示型组件

+ 展示型组件，意为组件是怎么渲染的。它包含了 **Virtual DOM 的修改或组合，也可能包含组件的样式**。同时，它不依赖任何形式的 store。一般可以写成无状态函数，但实际上展示型组件并不一定都是无状态的组件，因为很多展示型组件里依然存在生命周期方法。
+ 这样做区分的目的是为了可以使用相同的展示型组件来配合不同的数据源作渲染，可以做到更好的可复用性。另外，展示型组件可以让设计师不用关心应用的逻辑，去随时尝试不同的组合。

### Redux 中的组件

+ 关于容器型组件和展示型组件，Redux 官方文档给出了对比结果，如表 5-1 所示。
+ ![1552194163551](/img/user/programming/font-end/framework/react/redux-advanced/1552194163551.png)
+ 从布局的角度来看，在 Redux 中，**强调了 3 种不同类型的布局组件：Layouts、Views 和 Components**。它们与容器型组件和展示型组件有着怎样的对应关系呢？

#### Layouts

+ Layouts 指的是页面布局组件，描述了 **页面的基本结构**，目的是将主框架与页面主体内容分离。它常常是 **无状态函数**，传入主体内容的 children 属性。结合 5.4 节的内容，Layout 组件就是设置在最外层 Route 中的 component 里。一般 Layout 的写法如下：

  ```react
  const Layout = ({ children }) => ( 
    <div className='container'> 
      <Header /> 
      <div className="content"> 
        {children} 
      </div> 
    </div> 
  ); 
  
  ```

#### Views

+ Views 指的是 **子路由入口组件**，描述了子路由入口的基本结构，包含此路由下所有的展示型组件。为了保持子组件的纯净，我们在这一层组件中定义了数据和 action 的入口，从这里开始将它们分发到子组件中去。因此，**Views 就是 Redux 中的容器型组件**。一般 Views 的写法如下：

  ```react
  @connect((state) => { 
    //... 
  }) 
  class HomeView extends Component { 
    render() { 
      const { sth, changeType } = this.props; 
      const cardProps = { sth, changeType }; 
      return ( 
        <div className="page page-home"> 
          <Card {...cardProps} /> 
        </div> 
      ); 
    } 
  } 
  ```

### Components

+ 顾名思义，Components 就是末级渲染组件，描述了从路由以下的子组件。它们包含具体的业务逻辑和交互，但所有的数据和 action 都是由 Views 传下来的，这也意味着它们是可以完全脱离数据层而存在的展示型组件。一般由路由传下来的 Components 的写法如下：

  ```react
  class Card extends Components { 
    constructor(props) { 
      super(props); 
      this.handleChange = this.handleChange.bind(this); 
    } 
    handleChange(opts) { 
      const { type } = opts; 
      this.props.changeType(type); 
    } 
    render() { 
      const { sth } = this.props; 
      return ( 
        <div className="mod-card"> 
          <Switch onChange={this.handleChange}> 
            // ... 
          </Switch> 
          {sth} 
        </div> 
      ); 
    } 
  } 
  ```

+ 通过上述 3 种组件的定义，我们看到 Redux 中对页面布局的区分，以及页面的基本结构与数据传递的形式。这么做是为了更好地利用 React 组件的可复用性。
+ 从第 2 章到本节，我们对于 React 组件的解释终于趋于完整。对于我们来说，分清楚这些概念尤为重要。除了上述的容器型组件和展示型组件外，还有有状态（stateful）组件和无状态（stateless）组件、类（class）和方法（function）、纯（pure）组件和非纯（impure）组件。

## Redux 应用实例

### 初始化 Redux 项目

+ `$ npm install --save react react-dom redux react-router react-redux react-router-redux whatwg-fetch `
+ 可以看到，除了最基本的 React、Redux 和 react-redux 外，我们还安装了讨论过的路由库 react-router 和 react-router-redux。此外，还有 Ajax 请求兼容库 whatwg-fetch。

### 准备首页的数据

+ 当访问我们的博客时，用户希望看到的自然是文章列表，这也是 Home 组件需要显示的数据。
+ 在 5.6.2 节中我们说到，views/ 文件夹下放着的是所有的 **路由入口页**，而 components/ 下放着的是每个入口页需要的组件、样式以及 Redux 相关的文件。
+ 因此，我们需要在 src/components/Home/ 文件夹下添加几个新文件：
+ src/

  ├── components

  │ ├── Detail

  │ └── Home

  │ ├── Preview.css

  │ ├── Preview.js

  │ ├── PreviewList.js

  │ └── PreviewListRedux.js

  ├── layouts

  ├── routes

  └── views

+ 其中 Preview.js 中定义了一个纯渲染、无状态的文章预览组件：

  ```react
  import React, { Component } from 'react'; 
  import './Preview.css'; 
  class Preview extends Component { 
    static propTypes = { 
      title: React.PropTypes.string, 
      link: React.PropTypes.string, 
    }; 
  render() { 
    return ( 
      <article className="article-preview-item"> 
        <h1 className="title">{this.props.title}</h1> 
        <span className="date">{this.props.date}</span> 
        <p className="desc">{this.props.description}</p> 
      </article> 
    ); 
  } 
  } 
  ```

+ 我们在这个组件中引入了一个名为 Preview.css 样式文件。顾名思义，这是 Preview 组件依赖的样式文件。但 JavaScript 中怎么能引入 CSS 呢？实际上，**其实这是 webpack 的一个插件 css-loader 所做的**。css-loader 会识别到所有引入 CSS 的语句，并解析出对应的 CSS 文件地址，以 \<style> 标签的形式动态插入到 DOM 节点中。
+ 接下来的内容会稍显复杂。首先看看 Preview List.js：

  ```react
  import React, { Component } from 'react'; 
  import Preview from './Preview'; 
  class PreviewList extends Component { 
   static propTypes = { 
     articleList: React.PropTypes.arrayOf(React.PropTypes.object) 
    }; 
   render() { 
   return this.props.articleList.map(item => ( 
    <Preview {...item} key={item.id} /> 
     )); 
    } 
  } 
  ```

+ 可以看出，PreviewList 也是一个无状态组件，它引入了 Preview 组件，并将传入的 articleList 遍历渲染出若干个对应的 Preview 组件。
+ PreviewList 本身并没有什么特别难以理解的地方，但是和它名字很相似的 PreviewList- Redux.js 则是本节内容的关键。在介绍 Redux 应用目录结构时，我们提到过，*Redux.js 里包含了 *.js 这个组件需要的 reducer、action creator 和 constants。
+ 现在让我们揭开它们的神秘面纱：

  ```js
  const initialState = { 
    loading: true, 
    error: false, 
    articleList: [], 
  }; 
  const LOAD_ARTICLES = 'LOAD_ARTICLES'; 
  const LOAD_ARTICLES_SUCCESS = 'LOAD_ARTICLES_SUCCESS'; 
  const LOAD_ARTICLES_ERROR = 'LOAD_ARTICLES_ERROR'; 
  export function loadArticles() { 
    return { 
      types: [LOAD_ARTICLES, LOAD_ARTICLES_SUCCESS, LOAD_ARTICLES_ERROR], 
      url: '/api/articles.json', 
    }; 
  } 
  function previewList(state = initialState, action) { 
    switch (action.type) { 
      case LOAD_ARTICLES: { 
        return { 
          ...state, 
          loading: true, 
          error: false, 
        }; 
      } 
      case LOAD_ARTICLES_SUCCESS: { 
        return { 
          ...state, 
          loading: false, 
          error: false, 
          articleList: action.payload.articleList, 
        }; 
      } 
      case LOAD_ARTICLES_ERROR: { 
        return { 
          ...state, 
          loading: false, 
          error: true, 
        }; 
      } 
      default: 
        return state; 
    } 
  } 
  export default previewList; 
  
  ```

+ 这是实例中截至目前为止最复杂的文件，下面让我们分 3 部分来理解它。
+ 首先，它定义了 initialState。可以看到，它在文件末尾的 previewList 函数（目前我们暂且叫它函数）中作为第一个参数 state 的默认值。也就是说，当传入的 state 为空时，state 将使用 initialState 的值。
+ 定义 initialState 是为了 Redux 初始化并确定每个 reducer 的结构。而当初始化完成后，每次响应 action 时，reducer 将获得上一次计算出的 state 作为参数，这时 initialState 将不再发挥作用。
+ 接下来的 3 个常量定义和一个函数定义在逻辑上属于一个整体，但是分别有不同的意义。
+ LOAD_ARTICLES 等 3 个常量就是我们说的 constants，也就是一个 action 中的 type 字段，它们用来标识 Redux 应用中一个独立的 action。
+ 而 loadArticles() 就是一个 action creator。因为每次调用 loadArticles() 函数时，它都会返回一个 action，所以 action creator 之名恰如其分。
+ 至于这个 action creator 返回的 action，我们已经在 5.2 节里提到过，它是由 redux-composable- fetch 这个 middleware 所定义的格式。
+ 在 PreviewListRedux.js 的最后，则是我们定义的 reducer。可以看到，我们的 reducer 会响应 3 种类型的 action——LOAD_ARTICLES、LOAD_ARTICLES_SUCCESS 和 LOAD_ARTICLES_ERROR，这也是我们在之前刚刚定义的 action creator 可能会触发的 action 类型。
+ 看到这里，我们应该大概明白了一个 *Redux.js 文件所包含的内容与各自的职责。接下来，我们继续看看它们是怎么在整个 Redux 应用中发挥作用的。

### 连接 Redux

+ 在 5.6.7 节中，我们终于见识了 reducer 和 action creator 的真面目，而在这一节中，我们将学习怎么将这些 reducer 和 action creator 整合起来，最终变成应用中的一部分。
+ 在 5.5 节中，我们详解了两类组件——容器型组件和展示型组件，这两类组件最直观的区别在于是否感知 Redux 的存在，或者说，是否使用 connect 方法让组件从 Redux 的状态树中获取数据。

#### 让容器型组件关联数据

+ 我们现在已经熟悉了 views/文件夹和 components/文件夹的职责区别。很显然，views/ HomeRedux.js 包含了 Home 页面所有组件相关的 reducer 及 actionCreator：

  ```react
  import { combineReducers } from 'redux'; 
  // 引入 reducer 及 actionCreator 
  import list from '../components/Home/PreviewListRedux'; 
  export default combineReducers({ 
    list, 
  }); 
  export * as listAction from '../components/Home/PreviewListRedux'; 
  ```

+ 可以看到，views/ 目录下的 *Redux.js 文件在更大程度上只是起到一个 **整合分发** 的作用。和 components/ 目录下的 *Redux.js 文件一样，它默认导出的是当前路由需要的所有 reducer 的集合。
+ 这里我们引入了 Redux 官方提供的 combineReducers 方法，通过这个方法，我们可以方便地将多个 reducer 合并为一个。
+ **此外，HomeRedux.js 还将 PreviewListRedux.js 中所有导出的对象合并后，导出一个 listAction 对象。稍后，就会看到我们为什么要这么组织文件。**
+ 先对 views/Home.js 做一些修改，让它与 Redux 进行第一次亲密接触：

  ```react
  import React, { Component } from 'react'; 
  import { bindActionCreators } from 'redux'; 
  import { connect } from 'react-redux'; 
  import PreviewList from '../components/Home/PreviewList'; 
  import { listActions } from './HomeRedux'; 
  class Home extends Component { 
    render() { 
      return ( 
        <div> 
          <h1>Home</h1> 
          <PreviewList 
            {...this.props.list} 
            {...this.props.listActions} 
            /> 
        </div> 
      ); 
    } 
  } 
  export default connect(state => { 
    return { 
      list: state.home.list, 
    }; 
  }, dispatch => { 
    return { 
      listActions: bindActionCreators(listActions, dispatch), 
    }; 
  })(Home); 
  
  ```

+ 这里我们引入了 Redux 提供的工具方法 bindActionCreators、 react-redux 提供的 connect 方法以及在 components/Home/ 下的 PreviewList 组件。
+ 另一个值得关注的修改点是，我们不再默认导出一个 React 组件，而是导出了将 Home 组件传入 connect 函数调用的结果后最终生成的组件。
+ 事实上，调用 connect 函数返回了一个 **高阶组件生成器**，而这个生成器会基于原始组件生成一个全新的组件，并给这个组件添加额外的 props。
+ 在构造一个高阶组件生成器时，connect 最多接受 4 个参数，分别如下。
+ 关于 connect 函数的更多用法及背后的原理，请参考 6.5 节。
+ 在我们的例子中，我们暂时只关心前两个参数，即 mapStateToProps 和 mapDispatchToProps。
+ 在 mapStateToProps 中，我们从整棵 Redux 状态树中选取了 state.home.list 分支作为当前组件的 props，并将其命名为 list。这样，在 Home 组件中，就可以使用 this.props.list 来获取到所有 PreviewListRedux 中定义的状态。
+ 而在 mapDispatchToProps 中，我们从前面提到的 HomeRedux.js 中引入了 listActions，并使用 Redux 提供的工具函数将 listActions 中的每一个 action creator（目前只有一个）与 dispatch 进行绑定，最终我们可以在 Home 组件中使用 this.props.listActions 来获取到绑定之后的 action creator。
+ 最后，需要特别说明的是 Home 组件的 render 方法。我们将在 connect 中对生成的 this.props.list 和 this.props.list Actions 分别进行解构，然后传给 Preview List 组件作为 props。

#### 让展示型组件使用数据

+ 相比于容器型组件与 Redux 的复杂交互，展示型组件实现起来则简单得多，毕竟一切需要的东西都已经通过 props 传进来了：

  ```react
  import React, { PropTypes, Component } from 'react'; 
  import Preview from './Preview'; 
  class PreviewList extends Component { 
    static propTypes = { 
      loading: PropTypes.bool, 
      error: PropTypes.bool, 
      articleList: PropTypes.arrayOf(PropTypes.object), 
      loadArticles: PropTypes.func, 
    }; 
    componentDidMount() { 
      this.props.loadArticles(); 
    } 
    render() { 
      const { loading, error, articleList } = this.props; 
      if (error) { 
        return <p className="message">Oops, something is wrong.</p>; 
      } 
      if (loading) { 
        return <p className="message">Loading...</p>; 
      } 
      return articleList.map(item => (<Preview {...item} key={item.id} />)); 
    } 
  } 
  ```

+ 首先，我们扩充了原本定义的 propTypes，新增了 loading、error 以及 loadArticles 的定义。
+ 其次，我们添加了 componentDidMount 生命周期方法。在 PreviewList 组件加载完成后，我们调用了 this.props.loadArticles() 来加载文章列表。
+ 最后，我们在 render 方法中针对不同的状态渲染出了友好的提示信息。
+ 注意，在 PreviewList 组件中，所有的数据都来自 this.props。展示型组件自身不维护任何状态，也不知道 Redux 的存在。

#### 注入 Redux

+ 在“让容器型组件关联数据 ”一节中，我们学习了如何使用 connect 方法关联 Redux 状态树中的部分状态。问题是，完整的 Redux 状态树是哪里来的呢？这一节将解答你的疑惑。
+ 按照我们的目录约定，所有与 Redux **自身配置** 相关的代码都放在 src/redux/ 文件夹下，下面让我们来初始化这些文件：
+ src/

  ├── app.js

  ├── components

  ├── layouts

  ├── redux

  │ ├── configureStore.js

  │ └── reducers.js

  ├── routes

  └── views

+ 先来看看 reducers.js，这个文件里汇总了 **整个应用所有的 reducer**，而汇总的方法则十分简单。
+ 因为我们在 views/ 文件夹中已经对各个路由需要的 reducer 做过一次整理聚合，所以在 reducers.js 中直接引用 views/*Redux.js 中默认导出的 reducer 即可。
+ 而 configureStore.js 则是生成 Redux store 的关键文件，其中将看到 5.1 节中提到的 Redux 的核心 API——createStore 方法：

  ```react
  import { createStore, combineReducers, compose, applyMiddleware } from 'redux'; 
  import { routerReducer } from 'react-router-redux'; 
  import ThunkMiddleware from 'redux-thunk'; 
  import rootReducer from './reducers'; 
  const finalCreateStore = compose( 
    applyMiddleware(ThunkMiddleware) 
  )(createStore); 
  const reducer = combineReducers(Object.assign({}, rootReducer, { 
    routing: routerReducer, 
  })); 
  export default function configureStore(initialState) { 
    const store = finalCreateStore(reducer, initialState); 
    return store; 
  } 
  ```

+ 在 configureStore.js 中，并没有直接使用 Redux 提供的原始 createStore 方法来创建 store，而是利用 compose 方法对 createStore 方法进行了增强，并生成了新的 createStore 方法—— finalCreateStore。
+ applyMiddleware 是 Redux 提供的另一个 API，也是 Redux 具有高度可扩展性的重要保障。使用 middleware，可以让 Redux 解析各种类型的 action。除了最原始的对象外，我们的 action 还可以是方法、 promise，以及任何你能想象的类型。
+ 此外，我们还在初始化 Store 时引入了 react-router-redux 提供的 routerReducer，这个 reducer 帮助我们实现了路由状态与 Redux store 的统一。
+ 在完成 store 的配置后，我们需要在某个地方新建一个实例，即 app.js：

  ```react
  import ReactDOM from 'react-dom'; 
  import React from 'react'; 
  import configureStore from './redux/configureStore'; 
  import { Provider } from 'react-redux'; 
  import { syncHistoryWithStore } from 'react-router-redux'; 
  import { hashHistory } from 'react-router'; 
  import routes from './routes'; 
  const store = configureStore(); 
  const history = syncHistoryWithStore(hashHistory, store); 
  ReactDOM.render(( 
   <Provider store={store}> 
     {routes(history)} 
    </Provider> 
  ), document.getElementById('root')); 
  ```

+ 首先，我们引入了刚刚定义的 configureStore 方法。接着，引入 Redux 提供的 Provider 组件，它将成为整个 Redux 应用的根组件。
+ 接下来的两个依赖是为了完善我们的路由系统。我们将原本在 src/routes/index.js 中引入的 React Router 中的 hashHistory 改为在 app.js 中引入，因为 react-router-redux 需要对这个 history 对象进行强化，以此保证 React Router 与 Redux store 的一致和统一。
+ ![1552218496074](/img/user/programming/font-end/framework/react/redux-advanced/1552218496074.png)
+ 虽然界面看起来只是多了一个 Loading 状态，但实际上我们已经完成了 Redux 应用绝大部分功能的连线搭桥，剩下的只是丰富样式和业务逻辑。

### 引入 Redux Devtools

- 在丰富业务逻辑之前，我们有必要先在项目中引入 Redux 应用的大杀器——Redux Devtools。在 Redux 中，所有的数据变化都来源一个个的 action，因此，如果有一个工具能方便我们查看 action 的触发记录以及数据的更改情况，我们就可以非常方便地对应用进行调试。好消息是，Redux 本身就提供了这样强大的功能。
- 由于 Devtools 并没有打包到 Redux 包中，我们需要单独下载这些依赖：
- ` $ npm install --save-dev redux-devtools redux-devtools-log-monitor redux-devtools-dock-monitor `
  - 这里我们不仅下载了 redux-devtools，同时还下载了 redux-devtools-log-monitor 和 redux-devtools-
    dock-monitor，后面两个其实是 React 组件。Redux 作者在设计 Devtools 时，特意将模块进行了清晰的划分，这样你可以根据自己的需要选择合适的 monitor。

- 现在将 DevTools 初始化的相关代码统一放在 src/redux/DevTools.js 中：

  ```react
  import React from 'react'; 
  import { createDevTools } from 'redux-devtools'; 
  import LogMonitor from 'redux-devtools-log-monitor'; 
  import DockMonitor from 'redux-devtools-dock-monitor'; 
  const DevTools = createDevTools( 
    <DockMonitor toggleVisibilityKey='ctrl-h' 
      changePositionKey='ctrl-q'> 
      <LogMonitor theme='tomorrow' /> 
    </DockMonitor> 
  ); 
  export default DevTools; 
  
  ```

- DockMonitor 决定了 DevTools 在屏幕上显示的位置，我们可以按 Control+Q 键切换位置，或者按 Control+H 键隐藏 Dev Tool。而 LogMonitor 决定了 DevTools 中显示的内容，默认包含了 action 的类型、完整的 action 参数以及 action 处理完成后新的 state。效果如图 5-10 所示。
- ![1552263732335](/img/user/programming/font-end/framework/react/redux-advanced/1552263732335.png)
- 引入 Redux DevTools 后，极大地简化了我们对于整个应用状态的推导工作。因为我们能看到每次 action 的完整信息，以及 action 处理之后的 state，而这些 state 又被 connect 后用于 React 组件的渲染，最终呈现在用户界面上。

### 利用 Middleware 实现 Ajax 请求发送

- 事实上，当在浏览器中预览我们的 Redux 应用时，你会发现这样一个错误信息：
- Uncaught Error: Actions may not have an undefined "type" property. Have you misspelled a constant?
- 这是因为 Redux 没有正确识别我们在 views/Home/Preview List Redux.js 中定义的 load Articles() 方法返回的 action。
- 在 5.2 节中，我们已经介绍了如何使用 redux-composable-fetch 这个 middleware 实现异步请求，现在只需要引入这个 middleware 并把它传给 store 增强器即可。 让我们回顾一下 configureStore 的实现：
- ```react
  import { createStore, combineReducers, compose, applyMiddleware } from 'redux'; 
  import { routerReducer } from 'react-router-redux'; 
  import ThunkMiddleware from 'redux-thunk'; 
  // 引入请求 middleware 的工厂方法
  import createFetchMiddleware from 'redux-composable-fetch'; 
  import rootReducer from './reducers'; 
  // 创建一个请求 middleware 的示例
  const FetchMiddleware = createFetchMiddleware(); 
  const finalCreateStore = compose( 
    applyMiddleware( 
      ThunkMiddleware, 
      // 将请求 middleware 注入 store 增强器中
      FetchMiddleware 
    ) 
  )(createStore); 
  const reducer = combineReducers(Object.assign({}, rootReducer, { 
    routing: routerReducer, 
  })); 
  export default function configureStore(initialState) { 
    const store = finalCreateStore(reducer, initialState); 
    return store; 
  } 
  ```
- 这样，我们的应用就能正确识别任何异步请求的 action 了。

### 请求本地的数据

- 由于我们的博客系统需要通过异步请求获取数据，而为了减少不必要的干扰，我们并不会具体实现一个服务端程序来响应这些数据。因此，一个可行的方式是在本地模拟这些结果。
- 前面说到，我们可以利用 webpack-dev-server 在本地启动一个简单的 http 服务器来响应页面，这里同样可以利用这一特性伪造一些本地数据：
- src/
  api/
    articles.json 文章列表
    article/
  1.json id 为 1 的文章信息，以此类推
  2.json
  3.json
- 这样我们在访问 http://127.0.0.1:8080/api/articles.json 时，其实访问的是我们在本地定义的 JSON 文件，而不是远程服务器的接口。这个技巧在本地开发前端代码时会经常用到。
- 如果你不能通过上述链接正常访问到存储在本地的 JSON 文件，请确保执行 webpack-dev- server 命令时添加了 --content-base . 参数。
- 我们在 articles.json 中模拟了如下的数据结构：
  {
    [
   "id": 1,
   "title": "Angular2 中那些我看不懂的地方 ",
     "description": " 博客停更了近 3 个月，实在是愧对很多在微博上推荐的同学。因为最近大部分时间都投入
   在公司里一个比较复杂的项目中，直到本周才算正式发布，稍得解脱。说这个项目复杂，不仅是因为需求设
   计复杂，更是因为在这个 [...]",
     "date": "2016-04-17"
    ],
    ...
  }
- 看看自动刷新的浏览器里面，一个博客的雏形是否已经展示出来了？如图 5-11 所示。
- ![1552264177448](/img/user/programming/font-end/framework/react/redux-advanced/1552264177448.png)

### 页面之间的跳转

- 现在博客的首页已经初见效果，下一步就是要实现文章详情页了。首先要考虑的问题是，用户怎么进入文章详情页？当然是点击链接。
- 前面讲到 Nav.js 时，已经领略了使用 React Router 提供了 <Link> 组件模拟链接的做法，但是在 Redux 应用中，路由状态也属于整个应用状态的一部分，所以更合理的方案应该是通过分发 action 来更新路由。
- 在这一节中，我们会讲述怎样实现通过分发 action 的方法完成 Redux 应用的路由更新。
- 由于 React Router 本身是一个独立的路由处理库，要想把 React Router 中维持的状态暴露给 Redux 应用，或是在 Redux 应用中修改 React Router 的状态，我们需要某种手段将这二者结合起来，这就要说到 react-router-redux 中提供的 routerMiddleware 了：

  ```react
  // redux/configureStore.js 
  import { hashHistory } from 'react-router'; 
  import { routerMiddleware } from 'react-router-redux'; 
  import rootReducer from './reducers'; 
  const finalCreateStore = compose( 
    applyMiddleware( 
      // 引入其他 middleware
      // ... 
      // 引入 react-router-redux 提供的 middleware
      routerMiddleware(hashHistory) 
    ) 
  )(createStore); 
  
  ```

- 引入了新的 middleware 之后，就可以像下面这样简单修改当前路由了：

  ```react
  import { push } from 'react-router-redux'; 
  // 在任何可以拿到 store.dispatch 方法的环境中 
  store.dispatch(push('/')); 
  ```

- 既然做好了准备工作，让我们对文章列表页组件进行小小的修改，以便完成路由跳转：

  ```react
  // components/Home/Preview.js
  import React, { Component, PropTypes } from 'react'; 
  class Preview extends Component { 
    static propTypes = { 
      title: PropTypes.string, 
      link: PropTypes.string, 
      push: PropTypes.func, 
    }; 
  handleNavigate(id, e) { 
    // 阻止原生链接跳转 
    e.preventDefault();
    // 使用 react-router-redux 提供的方法跳转，以便更新对应的 store
    this.props.push(id); 
  } 
  render() { 
    return ( 
      <article className="article-preview-item"> 
        <h1 className="title"> 
          <a href={`/detail/${this.props.id}`} onClick{this.handleNavigate.bind(this,                                                                        this.props.id)}> 
            {this.props.title} 
          </a> 
        </h1> 
        <span className="date">{this.props.date}</span> 
        <p className="desc">{this.props.description}</p> 
      </article> 
    ); 
  } 
  } 
  
  ```

- 显而易见的变化是，我们在原本的标题中添加了链接，并指定了点击链接时由 handle Navigate 方法来响应。在 handle Navigate 方法中，首先执行 e.prevent Default() 来阻止原始的链接跳转，
- 然后调用了一个之前并不存在的 this.props.push 方法，它就是用来处理路由的更新的。
- 由于 PreviewList 本身是一个对 Redux 无感知的展示型组件，所以它并不能直接获取到 store 的引用，也就无法使用 store.dispatch 方法来随意分发 action。因此，我们需要给 PreviewList 传递一个绑定好 dispatch 的 push 方法，让它直接调用即可。那么，哪里可以直接拿到 store.dispatch 呢？很简单，所有使用 connect 方法的组件都可以感知 Redux：

  ```react
  // views/Home.js 
  import React, { Component } from 'react'; 
  import { bindActionCreators } from 'redux'; 
  import { connect } from 'react-redux'; 
  import PreviewList from '../components/Home/PreviewList'; 
  import { listActions } from './HomeRedux'; 
  import { push } from 'react-router-redux'; 
  class Home extends Component { 
    render() { 
      return ( 
        <div> 
          <h1>Home</h1> 
          <PreviewList 
            {...this.props.list} 
            {...this.props.listActions} 
            push={this.props.push} 
            /> 
        </div> 
      ); 
    } 
  } 
  export default connect(state => { 
    return { 
      list: state.home.list, 
    }; 
  }, dispatch => { 
    return { 
      listActions: bindActionCreators(listActions, dispatch), 
      push: bindActionCreators(push, dispatch), 
    }; 
  })(Home); 
  
  ```

- 我们在 Home.js 中引入了 react-router-redux 提供的 push 方法，将其和 store.dispatch 绑定后，作为 props 传给了 PreviewList。因为在 PreviewList 里并没有任何直接修改路由的需要，所以 PreviewList 又将 push 传递给了 Preview。这样，我们在 Preview 里就可以通过 this.props.push() 来修改路由了。
- 现在点击其中一个链接，看看 Redux Devtools 中记录了怎样的 action，如图 5-12 所示。
- ![1552264496803](/img/user/programming/font-end/framework/react/redux-advanced/1552264496803.png)
- 可以看到，在地址栏中我们的路由已经发生了改变，Redux Devtools 也为我们记录下了这次 action。但是，界面看起来不太正常吧？那是因为我们还没有在 Detail 组件中写任何逻辑。
- 当我们点击一篇博文的链接进入详情页时，应用应该根据当前路由中博文的 id 请求对应的详细数据。由于详情页中的逻辑与列表页并没有太大的差别，这里就不再给出详细的代码了。

### 优化与改进

- Redux DevTools 虽然功能强大，但是这样的工具绝对不应该出现在生产环境中。因为它不仅增加了最终打包 JavaScript 文件的大小，更会很大程度地影响整个应用的性能。
- 所以，我们希望调整代码以及构建脚本，最终实现在开发环境中加载 Redux DevTools，而在生产环境中不进行任何加载。
- 要实现这样的需求，首先需要添加一款 webpack 的插件——DefinePlugin，这款插件允许我们定义任意的字符串，并将所有文件中包含这些字符串的地方都替换为指定值。
- 其次，我们需要了解一种常见的定义 Node.js 应用环境的方法——环境变量。一般意义上来说，我们习惯使用 process.env.NODE_ENV 这个变量的值来确定当前是在什么环境中运行应用。当读取不到该值时，默认当前是开发环境；而当 process.env.NODE_ENV=production 时，我们认为当前是生产环境。
- 掌握这两点知识后，只需要在代码中添加合适的判断语句，最终 webpack 会根据不同的环境帮我们将判断语句中的条件换为可以直接求值的表达式。而在生产环境中，配合另一款插件 UglifyJS 的无用代码移除功能，可以方便地将任何不必要的依赖统统移除。比如，下面的代码在开发环境是这样的：

  ```react
  if (process.env.NODE_ENV === 'production') { 
    // 这里的代码只会在生成环境执行 
  } else { 
    // 这里的代码只会在开发环境执行 
  } 
  当在生产环境构建时，代码将先被转化为： 
  if (true) { 
    // 这里的代码只会在生成环境执行 
  } else { 
    // 这里的代码只会在开发环境执行 
  } 
  并最终进一步转化为： 
  // 这里的代码只会在生成环境执行 
  这样既保证了不同环境加载不同代码的灵活性，又保证了在生产环境打包时最小程度地引入
  依赖。 
  ```

### 添加单元测试

- 在上一章中，我们学习了如何使用 Jest 测试 Flux 中的 store。虽然借助 Jest 强大的 mock 功能可以实现我们的最终目的，但是那些技巧确实会让新手们感觉有点摸不着头脑。
- 这种疑惑在我们测试 Redux 中的 reducer 时将不复存在，因为 reducer 就是最常见也是最纯洁的函数。
- 因此，我们将不需要任何额外的模拟或设置，只需要选择自己喜欢的测试运行框架和断言库，直接完成测试用例并执行即可。
- 这里以测试 previewList 这个 reducer 为例，让我们看看具体的测试代码该如何编写：

  ```react
  import previewList, { LOAD_ARTICLES_SUCCESS } from '../src/components/Home/PreviewListRedux'; 
  describe('Preview List Reducer', () => { 
    it('should propagate new articles when loaded', () => { 
      const data = [{id: 1, title: 'test'}]; 
      const result = previewList({ 
        type: LOAD_ARTICLES_SUCCESS, 
        payload: data, 
      }); 
      expect(result.articleList).to.deep.equal(data); 
    }); 
  }); 
  
  ```

- 更有意思的是，借助 Redux 神奇的 Devtools 以及其优秀的扩展能力，在开源社区中出现了一个可以“帮助我们写测试”的 Redux Devtools——redux-test-recorder。在开启这个 Devtools 之后，我们只需要按照页面的交互方式操作一遍应用，就能自动生成 reducer 的单元测试代码。 到这里，Redux 应用的核心概念我们已经熟悉得差不多了。接下来，暂时忘掉 Redux 的 API、酷炫的 DevTools，思考一下我们为什么需要 Redux？

# 6. 使用 Hooks 重新实现 Redux

https://juejin.im/post/5c230aa2e51d4529355bc2e0

+ 我们先将理想的特征列举出来，完成这些特性才算是替代了 react-redux：
  + 全局维护一个 store。
  + 任何组件都可以获取到 store，最好 props 可以定制（mapStatetoProps）。
  + 提供可以派发 action 的能力（mapDispatchtoProps）。

## useRudecer

+ 先看一下内置 useRudecer 的官方实例能给我们带来一些什么启示：

  ```react
  const initialState = {count: 0};
  
  function reducer(state, action) {
    switch (action.type) {
      case 'reset':
        return initialState;
      case 'increment':
        return {count: state.count + 1};
      case 'decrement':
        return {count: state.count - 1};
      default:
        // A reducer must always return a valid state.
        // Alternatively you can throw an error if an invalid action is dispatched.
        return state;
    }
  }
  
  function Counter({initialCount}) {
    const [state, dispatch] = useReducer(reducer, {count: initialCount});
    return (
      <div>
          Count: {state.count}
          <button onClick={() => dispatch({type: 'reset'})}>
              Reset
          </button>
          <button onClick={() => dispatch({type: 'increment'})}>+</button>
          <button onClick={() => dispatch({type: 'decrement'})}>-</button>
        </div>
    );
  }
  ```

+ 乍一看好像 react 利用 hook 已经可以使用 redux 的机制了， 状态由派发的 action 改变，单向数据流。但是 hook 不会让状态共享，也就是每次 useReducer 保持的数据都是独立的。比如下面这个例子：

  ```react
  function CountWrapper() {
      return (
          <section>
              <Counter initialCount={1}/>
              <Counter initialCount={1}/>
          </setion>
          )
  }
  ```

+ 两个 Counter 组件内部的数据是独立的，无法互相影响，状态管理也就无从说起。 究其原因，useReducer 内部也是用 useState 实现的

  ```react
  function useReducer(reducer, initialState) {
    const [state, setState] = useState(initialState);
  
    function dispatch(action) {
      const nextState = reducer(state, action); // 常规的reducer，返回一个新的state
      setState(nextState);
    }
  
    return [state, dispatch];
  }
  ```

## StoreProvider

+ useReducer 看来并不能帮上忙。解决全局状态的问题可以参照 react-redux 的做法，提供一个 Provider，使用 context 的方式来做。 这里可以使用 useContext，这个内置的 hook。
+ **useContext**：Accepts a context object (the value returned from React.createContext) and returns the current context value, as given by the nearest context provider for the given context. When the provider updates, this Hook will trigger a rerender with the latest context value.
+ 它接受一个由 React.createContext 返回的上下文对象， 当 provider 更新时，本文中这里理解为 **传入的 store 更新时**，useContext 就可以返回最新的值。那么我们就有了下面的代码

  ```react
  import {createContext, useContext} from 'react';
  
  const context = createContext(null);
  export const StoreProvider = context.provider;
  
  const store = useContext(context);
  // do something about store.
  ```

## useDispatch

+ 到这里我们提供了一个根组件来接受 store。当 store 有更新时，我们也可以利用 useContext 也可以拿到最新的值。 这个时候暴露出一个 hook 来返回 store 上的 dispatch 即可派发 action，来更改 state

  ```react
  export function useDispatch() {
    const store = useContext(Context);
    return store.dispatch;
  }
  ```

## useStoreState

+ 接下来着眼于组件拿到 store 上数据的问题。这个其实也很简单，我们都把 store 拿到了，编写一个自定义的 hook 调用 store.getStore() 即可拿到全局的状态，

  ```react
  export function useStoreState(mapState){
      const store = useContext(context);
      return mapState(store.getStore());
  }
  ```

+ 这里虽然是把状态拿到了，但忽略了一个非常重要的问题， 当 store 上的数据变化时，如何通知组件再次获取新的数据。当 store 变化过后，并没有和视图关联起来。另一个问题是没有关注 mapState 变化的情况。 针对第一个问题，我们可以利用 useEffect 这个内置 hook，在组件 mount 时完成在 store 上的订阅，并在 unmont 的时候取消订阅。 mapState 的变更可以使用 useState 来监听， 每次有变更时就执行向对应的 setter 方法。代码如下

  ```react
  export function useStoreState(mapState) {
    const store = useContext(context);
  
    const mapStateFn = () => mapState(store.getState());
  
    const [mappedState, setMappedState] = useState(() => mapStateFn());
  
    // If the store or mapState change, rerun mapState
    const [prevStore, setPrevStore] = useState(store);
    const [prevMapState, setPrevMapState] = useState(() => mapState);
    if (prevStore !== store || prevMapState !== mapState) {
      setPrevStore(store);
      setPrevMapState(() => mapState);
      setMappedState(mapStateFn());
    }
  
    const lastRenderedMappedState = useRef();
    // Set the last mapped state after rendering.
    useEffect(() => {
      lastRenderedMappedState.current = mappedState;
    });
  
    useEffect(
      () => {
        // Run the mapState callback and if the result has changed, make the
        // component re-render with the new state.
        const checkForUpdates = () => {
          const newMappedState = mapStateFn();
          if (!shallowEqual(newMappedState, lastRenderedMappedState.current)) {
            setMappedState(newMappedState);
          }
        };
  
        // Pull data from the store on first render.
        checkForUpdates();
  
        // Subscribe to the store to be notified of subsequent changes.
        const unsubscribe = store.subscribe(checkForUpdates);
  
        // The return value of useEffect will be called when unmounting, so
        // we use it to unsubscribe from the store.
        return unsubscribe;
      },
      [store, mapState],
    );
    return mappedState
  }
  
  
  ```

+ # **redux-react-hook**
+ 现在还需要 redux 的开发工具，先用着，搞清楚原理
