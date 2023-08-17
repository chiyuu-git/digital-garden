---
{"dg-publish":true,"permalink":"/programming/font-end/framework/san/san-store/"}
---


整体的用法和 redux 类似

# 数据流转

Store：驱动应用的真实数据源头

View：渲染时页面从 store 中获取数据渲染展现给用户。

Action：当页面想改变 store 里的数据，通过 dispatch 方法派发一个 action 给 store，这里的 action 是 store 唯一的信息来源，做为一个信息的载体存在，以及计算下一个状态。

![](/img/user/programming/font-end/framework/san/san-store/image-20221205141923864.png)

state 在单向流中：

1. store 中存放的 state 来描述应用程序在特定时间点的状况
2. 基于 state 来渲染出 View
3. 当发生某些事情时（例如用户单击按钮），state 会根据发生的事情进行更新，生成新的 state
4. 基于新的 state 重新渲染 View

可以看到，在整个流程中数据都是单向流动的，这种方式保证了流程的清晰。

# 为什么需要状态管理？

> Web 应用是一个状态机，包含组件树和状态树，状态树和组件树存在对应关系。

前端复杂性的根本原因是大量无规律的交互和异步操作。

变化和异步操作的相同作用都是改变了当前 View 的状态，但是它们的无规律性导致了前端的复杂，而且随着代码量越来越大，我们要维护的状态也越来越多。

我们很容易就对这些状态何时发生、为什么发生以及怎么发生的失去控制。

![|500](/img/user/programming/font-end/framework/san/san-store/image-20221205142407408.png)

如上图 1 所示，如果我们的页面比较复杂，又没有用任何数据层框架的话，就是图片上这个样子：交互上存在父子、子父、兄弟组件间通信，数据也存在跨层、反向的数据流。这样的话，我们维护起来就会特别困难，那么我们理想的应用状态是什么样呢？看图 2

![|500](/img/user/programming/font-end/framework/san/san-store/image-20221205142423738.png)

通过定义和分离 state 管理中涉及的概念并强制执行维护 view 和 state 之间独立性的规则，代码变得更结构化和易于维护。

这也是很多状态管理库背后的基本思想：应用中使用集中式的全局状态来管理，并明确更新状态的模式，以便让代码具有可预测性。

架构层面上讲，我们希望 UI 跟数据和逻辑分离，UI 只负责渲染，业务和逻辑交由其它部分处理，从数据流向方面来说, 单向数据流确保了整个流程清晰。

综上，通过状态管理可以做到：

- 每个 State 变化可预测。
- 动作与状态统一管理。

# 状态管理前需要考虑的问题

## 应用包含哪些业务场景？

订单管理、资金管理、角色管理、数据看板（订单查询、交易流水查询）

## 应用和每个业务场景有哪些应用状态？

### 域数据（Domain data）

应用需要展示、使用或者修改的数据（比如 " 从服务器检索到的所有 todos ")，直接来源于服务端对领域模型的抽象。

通常，前端对 Domain data 最大的管理需求是和服务端保持同步，不会有频繁和复杂的变更——如果有的话请考虑合并批处理和转移复杂度到服务端。

甚至有不少页面仅在初始化时获取一次 Domain data，从此就再无瓜葛，直到跳转到下一个页面。

### UI 状态（UI state）

控制 UI 如何展示的数据（比如 “编写 TODO 模型的弹窗现在是展开的”）。

和 Domain data 的简单、稳定不同，UI state 是多变，不稳定的——不同的页面有不同、甚至相似但又细微不同的展现和交互。

同时，UI state 之间也是互相影响的，比如选择列表中的元素 (选中状态是 ui state )，当选中数量低于 N 时禁用提交按钮 (按钮是否禁用也是 ui state)。这是前端工作中非常常见的需求，整个场景中没有 Domain data 出现。

### 应用状态（App state）

特定于应用某个行为的数据（比如 “订单#1，现在核销的状态”，或者 “正在进行一个获取订单详情的请求”）

store 应该保存那些状态？Redux 社区的主流实践：

由 Redux 库贡献者之一维护的 [recipes](https://link.segmentfault.com/?enc=1PnUYgbyDEkect6ldg5ing%3D%3D.P13MwaQ0syl9zrjlwHXrQs1Yu0z8b2Cuu8wnHGHXAozPV0s%2BnN4kY%2B7YUU579tIQtS0myqGvnA2Mhbi4VWb44pU5bwrneSQpAGK9zi1frlnfr4JI4C1lf7WUCxUTq5ZLKCiWX%2BGpyUPSceoQrrXIAvadaOPTt1eGRpaoTuv2Ohw%3D) 提到了

> Because the store represents the core of your application, you should define your state shape in terms of your domain data and app state, not your UI component tree.

这基本代表了如今 Redux 社区的主流实践，它包含了两个主要观点：

1. Store 代表了 **应用** 的状态 (store represents the core of your application)
2. 使用 domain data 和 app state 作为 store 的主要抽象依据
3. 哪些状态适合用 store 管理？Store 代表着应用核心，你应该 **用域数据（Domain data）和应用状态数据（App state）定义 State，而不是用 UI 组件树（UI state）。**

## 不同业务场景之间的应用状态是否有相通复用的？

登录态信息，权限信息，资质信息等基础的公共信息是存在相通复用的，此外也存在多个页面操作同一实体的情况，如订单实体，订单管理、券码核销和售后退款都能对它进行操作

## 应用状态数据应该保存成什么结构？

树结构，以此来保证数据只在一个方向上流动

- 产生中心节点
- 所有节点变化流向中心节点
- 由中心节点重新分发至各节点
- **数据由中心节点持有**
- **数据有且只有一份**
- **节点引用数据，但不持有数据**

# 场景应用

## 场景一：订单改查

订单管理、券码核销和售后退款都能对订单进行操作，同一实体能在多处被修改并同步更新的场景需要泛式化

原则：对于包含多个消费端的数据，需要 Normalization - 泛式化，规范化数据存储在对象而不是数组中，以 ID 作为键

![](/img/user/programming/font-end/framework/san/san-store/image-20221205143706400.png)

Model：

```ts
// store state

interface OrderEntity {
    [orderId: OrderId]: Order;
}

// 订单管理
interface OrderModel {
	// 中心仓库
	orderEntity: OrderEntity;
	// 视图模型
	orderListViews: {
		// 持有的 订单id
		orderMatches: OrderId[];
	}
}

// 券码核销
interface CouponModal {
	// 视图模型
	couponShopsViews: {
        // 持有的 订单id
		orderId: OrderId;
	}
}

// 售后退款
interface RefundModel {
    // 视图模型
    refundViews: {
        // 持有的 订单id
        orderId: OrderId;
    }
}
```

视图操作订单后，多处使用同一个订单实体的视图会同步更新：撤销核销后，订单状态都变成了待核销

实践总结：

- 每个数据项只在一个地方定义，如果数据项需要更新的话不用在多处改变。
- action 逻辑不用处理深层次的嵌套，使数据更新操作看起来更加简单清晰。
- 检索或者更新给定数据项的逻辑变得简单与一致。给定一个数据项的 type 和 ID，不必挖掘其他对象而是通过几个简单的步骤就能查找到它。

### 是否都需要范式化（_Normalization_）呢？

当然是不能离开业务逻辑谈架构的，用不用 Normalization 要看你的 store 设计模式能不能支撑目前的业务逻辑以及未来的功能扩充。

我个人认为按需使用即可，对于展示性的没有太多的交互的页面，state 完全可以根据页面 UI 来设计。

需要前端做建模，设计类图（电路图编辑器，《领域驱动设计 软件核心复杂性应对之道》作者就曾经用这类项目举例）。这种项目里面有大量的类和对象（这些类和对象都是前端处理），这时候我们其实想要一个前端关系型数据库，而且还得能放进 store 里面，该数据库要有索引（加快查询速度），符合数据库范式（增删改查数据不会产生副作用）。此时，Normalization 就需要使用了

### 两个页面都使用了同一 Order 实体，如何安全的使用和更新？

> 对于 web 端应用，我们无法控制用户的操作路径，很可能用户在直接访问某个页面的时候，我们 store 中并没有准备好数据，这可能会导致一些问题。

Order 实体，是按领域模型划分的，在使用 store.order 这样的数据时不可能知道这个数据是否可靠（数据在内存中，刷新即无），最终要么花费额外的精力去确认，要么给应用留下隐患——显然后者会是更常见的情况。

Store 这个名字给人以 "Storage" 的错觉，并且面向领域模型的设计使得这种错觉被进一步巩固。

我们可以在 store 中标明期望共享 state 和不期望共享的 state，需要让开发者意识到当前的修改操作是全局范围内的

## 场景二：首次进入应用

登录态判断 → 权限判断 → 商家资质 … 需要使用很多的 state，这里涉及的 state 是如何更新的？

基于 san-store 的应用程序中最常见的 state 结构是一个简单的 JavaScript 对象，可以认为它最外层的每个 key 中拥有特定域的数据的切片。

原则：每个 action 函数独立负责管理该特定切片 state 的更新。

> 显然，将所有的更新逻辑都放入到单个 action 函数中都将会让程序变得不可维护。但一般来讲，函数应该比较短，并且只做一件特定的事。因此，把很长的，同时负责很多事的代码拆分成容易理解的小片段是一个很好的编程方式。

上文中根据域数据和应用状态数据划分了 state，每一个 state 会有一个 action 负责更新，如登录相关的 action、资质管理相关的 action 等。

```ts
const App = connect.san(
    {
        isLogin: 'loginInfo.isLogin', // 是否登录
        showEnterStatusBar: 'showEnterStatusBar',
        pageErrorType: 'pageErrorType'
    },
    {
				// 登录模块的 action
        logout: LOGIN_ACTION.userLogOut,
        fetchUserInfo: LOGIN_ACTION.fetchUserInfo, // 获取用户信息
				// 导航栏模块的 action
        getSideNavTree: SIDE_NAV_ACTIONS.getSideNavTree,
        updateSideNavSelectedKey: SIDE_NAV_ACTIONS.updateSideNavSelectedKey,
				// 资质管理模块的 action
        initEnterStatusBar: ENTER_ACTIONS.initEnterStatusBar,
        updateEnterStatusBar: ENTER_ACTIONS.updateEnterStatusBar,
        updateEnterStatusBarByRoutes: ENTER_ACTIONS.updateEnterStatusBarByRoutes,
        onRoutesChanged: SIDE_NAV_ACTIONS.onRoutesChanged
    }
)(san.defineComponent({
```

实践总结：action 应该被拆分以使它们更易于阅读

- action 通常根据顶级状态键或状态“ slices ”进行拆分，action 和 state 一一对应
- 可单独抽象出 actions 层，组织成文件，统一存放 action
- action name 字段应该是一个可读的字符串，最好是常量

# 状态树（_state tree_）改善

这是多利熊的平台界面，和很多 b 端平台类似，会有 slid nav、top bar 和 content page，slid nav 和 top bar 在整个应该周期内会一直存在在，而 content page 则会根据用户的选择展示不同内容，且 page 间是互斥的，即同时只有一个 page 被展示。

根据业务场景和功能抽象出不同模块，每个模块都会有自己的四层模型：

- model：定义相关域数据和视图模型
- server：直接使用 server api 获取数组，并进行 Normalization 并转换为 model 层定义的结构
- action：定义操作 model 的 action
- view：视图层

实践中发现的一些问题：

1. Domain data 与 UI state 混搭，页面间几乎不会共享 UI state，可能会共享领域模型
2. 理论上页面有无穷多个，未来 UI state 会有无数多，root store 会膨胀，变的混乱和无序
3. root store 具有全局性，而页面、组件通常是局部的，修改全局去服务局部是不好的编码范式（因为对 global store 的修改容易引起本非修改目标的 part 出现意外错误，所以为了尽可能减少这种）

假设组件树的根结点是页面，那么页面 A 和 B 的状态树必然是不同的，而 store **却需要用唯一一个状态树，去满足整个应用——N 个组件树 (页面) 的需求**，这显然是有问题的。

虽然可以通过 normalize、mapStateToProps，这些去缓冲解决，但它们面临的本质问题：状态树到组件树如何映射，这个因为状态树与组件树一对 N 的关系并没有改变。

对应到我们的平台来看，所以可以从状态树中划分出子树，page 在设计子树 store 时 **不用考虑其它页面，仅服务当前页**，以满足和组件树（content page）一一对应的关系。

## 状态树（_state tree_）设计

以此为依据划分出三级 store model，并没有从物理结构上完成三级的分层，仅从命名上进行约定，这样是为了防止嵌套层级过深，因为 store 本就是共享和中心化的，在物理结构上加深层级没有意义。

- 领域模型为一级，使用 xxxEntity 命名，无命名前缀。
- 应用状态数据通过 xxxViews 命名，如页面状态、查询条件等可放在此 key 下，命名前缀为模块名。
- 只将普通的可序列化对象、数组和原始类型放入 store 中。

```ts
interface GlobalStore {
	//---------- slid nav (一级) -------------
		// 导航目录树
    sideNavList: SideNavTree;

    // 当前的路由名称，用于导航展示默认选中态（高亮）
    currentSelectPath: string;
		...
	//-----------------------------------
	
    
	//---------- login user (一级) -------------
		// 登录相关信息
    loginInfo?: LoginInfo;
    // 用户相关
    userInfo: UserInfo;
    // 用户授权相关
    stokenInfo: StokenInfo; // 授权
    // 门店相关信息
    shopInfo: Shop;
    // 登录视图相关
    loginView?: LoginView;
	//-----------------------------------
    
    
	//---------- page (二级、三级) -------------
	orderViews:{}; 
	orderSummaryViews: {};
	orderListViews: {};
}
```

# 新趋势 - 数据获取和缓存的整体解决方案

[https://cn.redux.js.org/tutorials/essentials/part-7-rtk-query-basics](https://cn.redux.js.org/tutorials/essentials/part-7-rtk-query-basics)

[https://cloud.tencent.com/developer/article/1887827](https://cloud.tencent.com/developer/article/1887827)

[https://cloud.tencent.com/developer/article/1801737?from=article.detail.1887827](https://cloud.tencent.com/developer/article/1801737?from=article.detail.1887827)

react-query：在过去的几年里，React 社区已经意识到 **“数据获取和缓存” 实际上是一组不同于 “状态管理” 的关注点**。虽然你可以使用 Redux 之类的状态管理库来缓存数据，但用例差异较大，因此值得使用专门为数据获取用例构建的工具。react-query 提供了与网络请求相关的开箱即用工具，例如重新获取、预取和缓存。

# 其他的问题

san-store 如何影响性能

很多重复的模板代码如何收敛 or 沉淀固定模式（如抽象查询模板）

action、view、model，代码分散，影响专注力

# 强制要求

 使用 store 单向状态流转

 router 参与单向状态流转

 对于 store 中的 state，如果遇到以下写法，就需要考虑是否合理，我们严格限制不能使用以下写法，如有特例需说明

- connect 的组件中操作 `this.data.set`
- connect 的组件中声明 `computed`
- connect 的组件中声明 `this.watch`

> 如果遇到不记录 store 的组件状态操作怎么办？ -- 封装 store 无关组件。将无关状态封装在这些组件内部。

# Entity

```
│   ├── entity
│   │   ├── order // 可页面间共享使用，无视图
│   │   │   ├── action // 单向数据流更新 action 定义
│   │   │   ├── model // 实体类型定义
│   │   │   └── service // 实体相关的请求接口逻辑
```

## 什么样的数据进入 Entity

域数据（Domain data）应用需要展示、使用或者修改的数据（比如 " 从服务器检索到的所有 todos ")，直接来源于服务端对领域模型的抽象。

通常，前端对 Domain data 最大的管理需求是和服务端保持同步，不会有频繁和复杂的变更——如果有的话请考虑合并批处理和转移复杂度到服务端。

## 是否范式化

对于包含多个消费端的数据，需要 Normalization - 泛式化，规范化数据存储在对象而不是数组中，以 ID 作为键。

如果认为未包含多个消费端，对于范式化也不强做要求

# storeModel

定义默认 store 的类型：

- 每个页面不再单独 declare，统一 declare store
- 每个页面视图模型以 xxxViews 命名，xxxViews 为 root key，可以存放页面的各类状态

```ts
// 具体类型状态从各 models 中导出，此文件内可不做详细定义
import {RefundViews} from './refund/models';
import {MemberViews} from './member/models';

declare module 'san-store' {
    interface GlobalStore {
        orderEnitity: { // 实体类型
            [orderId: OrderId]: Order;
        };
        user: User; 
        refundViews: RefundViews; // 视图状态，每个 page 限导出一个,可用于存放 ui state、app state
        memberViews: MemberViews;
    }
}

```

# 四层模型

各层职责：

- action 只处理业务逻辑和状态变更
- service 获取数据、api 数据转换（输入和输出）、可能涉及缓存
- model 模型定义
- views：视图层

# 路由管理

路由参与状态流转，使用 route 实现路由监听，典型的是查询类页面

单向数据流：onChange（用户事件） -> this.$router.push（修改路由） -> this.route（监听路由变化） -> action（操作状态） -> view（更新视图）

```ts
connect.san(
    orderId: 'orderId'
)(san.defineComponent({
    template: `<select id="{{orderId}}" on-change="onChange"></select>`,
    onChange(id) {
        this.$router.push({
            query: {
                orderId: id
            }
        });
    },
    route() {
        const query = this.date.get('route.query');
        ...
        this.actions.update(query)
    }
}));
```

# Best Practice

添加一个 action 调用 service 获取后台数据并更新 store

[如流知识库](https://ku.baidu-int.com/knowledge/HFVrC7hq1Q/pKzJfZczuc/-7bTQ_XhDc/lL2pZPIPmjXaR_)
