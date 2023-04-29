---
{"dg-publish":true,"permalink":"/programming/font-end/framework/react/react-engineering/"}
---


# 代码分割

# 错误边界

# 严格模式

# 静态类型检查

# 性能优化

# React-router

## 概述

react 的一个插件库

- 专门用来实现一个 SPA 应用
- 基于 react 的项目基本都会用到此库

### SPA

单页 Web 应用（single page web application，SPA）

整个应用只有一个完整的页面

点击页面中的链接不会刷新页面, 本身也不会向服务器发请求

当点击**路由链接**时, 只会做页面的局部更新

数据都需要通过 ajax 请求获取, 并在前端异步展现

### 路由

什么是路由?

- 一个路由就是一个映射关系 `(key:value)`
- key 为路由路径, value 可能是 `function/component`

路由分类

- 后端路由: node 服务器端路由，value 是 `function`，用来处理客户端提交的请求并返回一个响应数据
  - 注册路由: router.get(path, function(req, res))
  - 当 node 接收到一个请求时, 根据请求路径找到匹配的路由, 调用路由中的函数来处理请求, 返回响应数据
- 前端路由: 浏览器端路由，value 是 `componen`，当请求的是路由 path 时，浏览器端前没有发送 http 请求，但界面会更新显示对应的组件
  - 注册路由: <Route path="/about" component={About}>
  - 当浏览器的 hash 变为#about 时, 当前路由组件就会变为 About 组件

### 前端路由的实现

每种路由都会创建 history 对象，用来追踪当前的 location[1]，并在网站变化的时候重新渲染。React Router 提供的其它的组件都依赖于上下文中的 history 对象，所以必须在组件内部渲染。

**history 库**

+ <https://github.com/ReactTraining/history>

- 管理浏览器会话历史 (history) 的工具库
- 包装的是原生 BOM 中 window.history 和 window.location.hash

**history API**

- History.createBrowserHistory(): 得到封装 window.history 的管理对象
- History.createHashHistory(): 得到封装 window.location.hash 的管理对象
- history.push(): 添加一个新的历史记录
- history.replace(): 用一个新的历史记录替换当前的记录
- history.goBack(): 回退到上一个历史记录
- history.goForword(): 前进到下一个历史记录
- history.listen(function(location){}): 监视历史记录的变化

```js
// 方式一:h5新增history
let history = History.createBrowserHistory()
// 方式二：哈希
let history = History.createHashHistory()
```

`#号`：锚点（哈希值），虽然没有发请求，但是还是留下了历史记录，记录了哈希值

## React-router 相关 API

### 组件

在 react-router v4 中 react-router 被划分为三个包：react-router，react-router-dom 和 react-router-native，区别如下：

- react-router：提供核心路由组件和函数
- react-router-dom：供浏览器使用的 react router
- react-router-native：供 react native 使用的 react router

![1552287113620](/img/user/programming/font-end/framework/react/react-engineering/1552287113620.png)

> 实际渲染之后，自动补充了 Router 组件，这是一个@3 遗留的组件

**一般情况下，我们直接使用的只有 react-router-dom**

两种类型的路由器：

+ <BrowserRouter>
+ <HashRouter>

路由组件：

+ <Route>
+ <Redirect>
+ <Link> 点击之后不会有active的class
+ <NavLink> activeClass
+ <Switch>：用于包裹<Route>

### 其它

history 对象：`this.props.history.push/replace/goforword/goback`

match 对象

- this.props.match.params
- 通过路由参数，向**路由组件传递参数**

withRouter 函数

- 包装普通组件，使得普通的组件也可以使用路由组件的方法
- 常用于导航等公共组件 PropTypes

## 基本路由使用

### Init @link Project-init

### 默认路由

**方法一**

```react
// index.js，注册路由
import React from 'react';
import ReactDOM from 'react-dom';
import {HashRouter, Switch, Route} from 'react-router-dom'

import './index.less';
import App from './App';
import Play from './Play'

ReactDOM.render(
(
  <HashRouter>
    <Switch> {/*需要紧紧跟着Route，中间插入了其他则不行*/}
      {/* 默认路由，path='/' 必须加上exact，否则任何时候都是匹配的*/}
      <Route exact path='/' component={App}/>
      <Route path='/play' component={Play} />
      <Route path='/app' component={App}/>
    </Switch>
  </HashRouter>
), 
document.getElementById('root'));
```

App,jsx，等子组件，通过 Link 组件进行路由跳转

```react
<NavLink to='/play'><i className='iconfont icon-play'></i></NavLink>
```

方法二 @deprecated

```react
<Switch>
  <Route path='/about' component={About} />
  <Route path='/home' component={Home} />
  <Redirect path='/' to='/about' />
</Switch>
```

使用重定向会刷新页面，方法一更好

### 嵌套路由

由于 react-router 规定：

+ Router 组件下只能有一个子元素
+ 所有 react-router 的组件必须是 Router 子孙元素

为了实现嵌套路由有三种方法

**方法一：**公共部分不含路由组件，直接把 Router 嵌入即可

```react
<div className='n-back_container'>
  <Header ref={Header=>{this.Header=Header}}></Header>
  <HashRouter>
    <Switch>
      <Route exact path='/start' component={Start}/>
      <Route path='/play' component={Play} />
      <Route path='/result' component={Result} />
      <Redirect path="/" to={{pathname: '/start'}} />
    </Switch>
  </HashRouter>
</div>
```

**方法二：**公共部分含有路由组件，比如底部导航，在 Rouer 下再包裹一层即可

```react
<HashRouter>
  <div>
    <Nav/>
    <Switch>
      <Route exact path='/' component={Home}></Route>
      <Route path='/detail/:id' component={Detail}></Route>
    </Switch>
  </div>
</HashRouter>
```

**方法三：**通用，但是逻辑表达不清晰，也可用在条件显示组件，但是不如判断语法明了

```react
<ConnectedRouter history={history}>
  <div>
    <Route exact path="/login" component={LoginPage} />
    <Route exact path="/" component={privateRoute(App)} />
    <Route path="/app" component={privateRoute(App)} />
    <Route path="/app/users" component={privateRoute(UserListPage)} />
    <Route path="/app/chpwd" component={privateRoute(ChpwdPage)} />
    <Route path="/app/user" component={privateRoute(User)} />
    <Route path="/logout"  onEnter={props.logout} />
  </div>
</ConnectedRouter>
```

### 向路由组件传递参数

问题：路由组件在<Route>标签内部，以属性的形式存在，怎么传递数据呢？

#### props.match.params

```
//二级路由组件: views/message.jsx
const path = this.props.match.path
// 路由定义
<Route path={`${path}/:id`} component={MessageDetail}></Route>
// 
<NavLink to={`${path}/${m.id}`}>{m.title}</NavLink>

//三级路由组件: views/message-detail.jsx
const id = props.match.params.id
```

<img src="react-engineering/image-20211005154011267.png" alt="image-20211005154011267" style="zoom: 67%;" />

上面的方法可以传递一个或多个值，但是每个值的类型都是字符串，没法传递一个对象。如果要传的话可以将 json 对象转换为字符串，传递过去之后再将 json 字符串转换为对象。

#### **props.location.query**

`query` 方式可以传递任意类型的值，但是页面的 `url` 也是由 `query` 的值拼接的，`url` 很长且是明文传输。

```jsx
//定义路由
<Route path='/user' component={UserPage}></Route>
 
//数据定义
let data = {id:3,name:sam,age:36};
let path = {
    pathname: '/user',
    query: data,
}
 
//页面跳转
<Link to={path}>用户</Link>
this.props.history.push(path);
 
//页面取值
let data = this.props.location.query;
let {id,name,age} = data;
```

<img src="react-engineering/image-20211005154105222.png" alt="image-20211005154105222" style="zoom:67%;" />

#### **props.location.state**

`state` 方式类似于 `post`，依然可以传递任意类型的数据，而且可以不以明文方式传输。

```js
//定义路由
<Route path='/user' component={UserPage}></Route>
 
//数据定义
let data = {id:3,name:sam,age:36};
let path = {
    pathname: '/user',
    state: data,
}
 
//页面跳转
<Link to={path}>用户</Link>
this.props.history.push(path);
 
//页面取值
let data = this.props.location.state;
let {id,name,age} = data;
```

#### 区别

params 无法持久化，其他两种都可以

路由连接与非路由连接：为什么 a 标签页会发请求

https://juejin.im/entry/5b50518bf265da0f6436c34a

https://reacttraining.com/react-router/web/api/Link/to-object

### 路由跳转的两种方式

1. `Link` 和 `NavLink` 组件
2. `this.props.history` 的方法

```react
handleTouch = () =>{
   this.props.history.push('/play')
}

<i className='iconfont icon-play' onTouchStart={this.handleTouch}></i>
```

### 包装现有的 Link 组件

```react
//包装NavLink组件: components/my-nav-link.jsx
import React from 'react'
import {NavLink} from 'react-router-dom'

//把所有传过来的props都定义为NavLink的属性，组件类形式：{...this.props}
//关键是新增了所有的NavLink都需要的一个样式：activeClassName
export default function MyNavLink(props) {
  return <NavLink {...props} activeClassName='activeClass'/>
}

<MyNavLink className="list-group-item" to='/about' >About</MyNavLink>
<MyNavLink className="list-group-item" to='/home'>Home</MyNavLink>
```

```react
//入口JS: index.js

import React from 'react'
import ReactDOM from 'react-dom'
import {BrowserRouter, HashRouter} from 'react-router-dom'
import App from './components/app'

import './index.css'

ReactDOM.render(
  (
    <BrowserRouter>
      <App />
    </BrowserRouter>
    /*<HashRouter>
      <App />
    </HashRouter>*/
  ),

  document.getElementById('root')
)
```

# React-UI

## 最流行的开源 React UI 组件库

### material-ui(国外)

- 官网: <http://www.material-ui.com/#/>
- github: <https://github.com/callemall/material-ui>

### ant-design(国内蚂蚁金服)

- PC 官网: <https://ant.design/index-cn>
- 移动官网: <https://mobile.ant.design/index-cn>
- Github: <https://github.com/ant-design/ant-design/>
- Github: <https://github.com/ant-design/ant-design-mobile/>

### 搭建 Antd-mobile 的基本开发环境

- 下载 `npm install antd-mobile --save`

### 实现按需打包 (组件 js/css)

1. 下载依赖包

`npm react-app-rewired --save -dev`

`npm babel-plugin-import --save -dev`

2. 修改默认配置:

- /package.json
- ```json
  "scripts": {
    "start": "react-app-rewired start",
    "build": "react-app-rewired build",
    "test": "react-app-rewired test --env=jsdom"
  }
  
  ```
- 创建 config-overrides.js
- ```js
  const {injectBabelPlugin} = require('react-app-rewired');
  module.exports = function override(config, env) {
    config = injectBabelPlugin(['import', {libraryName: 'antd-mobile', style: 'css'}], config);
    return config;
  };
  
  ```

# Antd Design

+ 按需引入配置

  ```js
  test:/\.less$/,
    use:['style-loader','css-loader',{
      loader:'less-loader',
      options:{
        javascriptEnabled: true
      }
    }],
  ```

# React 高级应用

## 模态框

+ 函数组件在被调用的时候，不是返回一个实例，而是简单的执行，而类组件，在被调用的时候会返回一个类的实例。
+ 但是函数组件无法访问父组件的变量，因为在编译组件的时候，函数组件内不存在父组件的变量，导致报错
+ 所以对于函数组件，无法像类那样，调用实例的方法实现模态框

  > 如果是类，所有的方法都定义在子组件上，父组件通过 ref 获取子组件调用子组件的方法实现模态框的显式与关闭
  >
  > https://gitee.com/hjm100/codes/qig7phnjarduwxtz8l46y58

# React 编程规范

1. 组件名必须大写
2. 渲染的时候会自动去执行定义组件函数，不用另外调用

![1546604795348](/img/user/programming/font-end/framework/react/react-engineering/1546604795348.png)

5. 页面最终显示的不是组件标签，而是组件标签内部包含的 h2

   - 也就是为什么组件标签的名字可以随便定义

6. constructor 写在最上
7. render 写在最下面，因为 render 最后重要
8. **复杂组件定了组件类（名），我们操作的（this）是组件对象**

   - 在组件类外部对 MyComponent 做的操作，是在类上的，当然对象也能用
   - 在内部，对 this 添加的只有该**组件对象**能够使用
   - ```react
     class CommentList extends React.Component {
     	static propTypes = {
         comments: PropTypes.array.isRequired,
         delete: PropTypes.func.isRequired
     	}
     	state={}
     }
     //CommentList 是组件类
     //state是组件对象（this）的属性，通过staic关键字，propTypes添加在了组件类上
     ```
   - **通过 static 关键字，在组件类内部，给组件类添加属性**
   - **不加 static 关键字，就是组件对象的属性**

## 强烈注意

- 组件内置的方法中的 this 为组件对象
- 在组件类中自定义的方法中 this 为 null
- 强制绑定 this: 通过函数对象的 bind()
- 箭头函数 (ES6 模块化编码时才能使用)
- 自己调用自己的标签，就不用设置 ref 属性了，直接用 event.target 即可
- 指定的回调函数**不可以传参数**，需要用箭头函数包装一层
  - `<button onClick={() => this.ShowDetail(m.id)}>查看详情(push)</button>`
- react 也可以使用原生的事件绑定，但是要注意给事件解绑，防止内存泄漏
  - > https://www.cnblogs.com/hit-tml/p/8204597.html
