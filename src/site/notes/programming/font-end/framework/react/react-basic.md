---
{"dg-publish":true,"permalink":"/programming/font-end/framework/react/react-basic/"}
---


5/8

6/12

8/13

21-08-18

2022-03-01

# React 的基本认识

## 官网

英文官网:[https://reactjs.org/](https://reactjs.org/)

中文官网: <https://reactjs.bootcss.com/>

## 介绍描述

用于构建用户界面的 JavaScript 库（只关注于 View）

a javascript library to create interactive website

## React 的特点

Declarative(声明式编码)

Component-Based(组件化编码)

Learn Once, Write Anywhere(支持客户端与服务器渲染)

高效

单向数据流

## React 高效的原因

虚拟 (virtual)DOM, 不总是直接操作 DOM

DOM Diff 算法, 最小化页面重绘

- 虚拟 DOM 结构与页面实际的 DOM 结构一一对应
- 在修改 DOM 结构时，先修改虚拟 DOM 结构，操作完成后，再更新实际 DOM
- 批量更新，且 Diff 算法，只更新变化的部分
- 对比：以前改一个更新一个

## 相关 Js 库

react.js: React 的核心库

react-dom.js: 提供操作 DOM 的 react 扩展库

babel.min.js: 解析 JSX 语法代码转为纯 JS 语法代码的库

```html
<script type="text/javascript" src="../js/react.development.js"></script><script type="text/javascript" src="../js/react-dom.development.js"></script><script type="text/javascript" src="../js/babel.min.js"></script>
```

## 编码示例

```html
<script type="text/babel"> //必须声明babel
  // 创建虚拟DOM元素
  const vDom = <h1 id={myId}>Hello React</h1>
  // 渲染虚拟DOM到页面真实DOM容器中
  ReactDOM.render(vDom, document.getElementById('test'))
  //did
</script>
```

jsx 语法，不可以也不需要加引号

# JSX

## [为什么使用JSX](https://reactjs.bootcss.com/docs/introducing-jsx.html#why-jsx)

## JSX 标签

### JSX 标签名

JSX 标签的第一部分指定了 React 元素的类型。

大写字母开头的 JSX 标签意味着它们是 React 组件。这些标签会被编译为对命名变量的直接引用，所以，当你使用 JSX `<Foo />` 表达式时，`Foo` 必须包含在作用域内。

当元素类型以小写字母开头时，它表示一个内置的组件，如 `<div>` 或 `<span>`，将导致字符串 `'div'` 或 `'span'` 传递给 `React.createElement`。

以大写字母开头的类型，如 `<Foo />` 编译为 `React.createElement(Foo)`，并且它正对应于你在 JavaScript 文件中定义或导入的组件。

> 我们建议使用大写字母开头命名自定义组件。如果你确实需要一个以小写字母开头的组件，则在 JSX 中使用它之前，必须将它赋值给一个大写字母开头的变量。

### 动态标签名

你不能将通用表达式作为 React 元素类型。如果你想通过通用表达式来（动态）决定元素类型，你需要首先将它赋值给大写字母开头的变量。这通常用于根据 prop 来渲染不同组件的情况下:

```jsx
import React from 'react';
import { PhotoStory, VideoStory } from './stories';

const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  // 错误！JSX 类型不能是一个表达式。
  return <components[props.storyType] story={props.story} />;
}
```

要解决这个问题, 需要首先将类型赋值给一个大写字母开头的变量：

```jsx
import React from 'react';
import { PhotoStory, VideoStory } from './stories';

const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  // 正确！JSX 类型可以是大写字母开头的变量。
  const SpecificStory = components[props.storyType];
  return <SpecificStory story={props.story} />;
}
```

### JSX 标签名 点语法

在 JSX 中，你也可以使用点语法来引用一个 React 组件。当你在一个模块中导出许多 React 组件时，这会非常方便。例如，如果 `MyComponents.DatePicker` 是一个组件，你可以在 JSX 中直接使用：

```jsx
import React from 'react';

const MyComponents = {
  DatePicker: function DatePicker(props) {
    return <div>Imagine a {props.color} datepicker here.</div>;
  }
}

function BlueDatePicker() {
  return <MyComponents.DatePicker color="blue" />;
}
```

### JSX 标签 属性绑定

你可以通过使用引号，来将属性值指定为字符串字面量：

```jsx
const element = <div tabIndex="0"></div>;
```

也可以使用**大括号**，来在属性值中插入一个 JavaScript **表达式**：

在 JSX 语法中，你可以在**大括号内**放置任何有效的 JavaScript 表达式。例如，`2 + 2`，`user.firstName` 或 `formatName(user)` 都是有效的 JavaScript 表达式。

```jsx
const element = <img src={user.avatarUrl}></img>;
```

style 属性接受一个**样式对象** @@@

```jsx
 <div style={{color: 'red', fontWeight: 'bold'}} />
```

如果你没有给属性传值，它默认为 `true`。因此下面两个 JSX 是等价的：

```jsx
<MyTextBox autocomplete /><MyTextBox autocomplete={true} />
```

### 动态样式类名@link

我想使用 {__plain && 'plain'}

但是 san 只能 {{ __plain ? 'plain' : ''}}

返回一个 {{false}} 时 无法渲染

jsx 是否支持这个逻辑，我没有做好对比分类

react san vue 三者的文档要义相同的结构去梳理，才能方便对比

### 无法使用的 Js 语法

`if` 语句以及 `for` 循环不是 JavaScript 表达式，所以不能在 JSX 中直接使用。但是，你可以用在 JSX 以外的代码中。比如：

```jsx
function NumberDescriber(props) {
  let description;
  if (props.number % 2 == 0) {
    description = <strong>even</strong>;
  } else {
    description = <i>odd</i>;
  }
  return <div>{props.number} is an {description} number</div>;
}
```

**直观来看，JSX 指的是标签的部分，包括标签名、标签体、标签的属性**

## JSX 标签体 @@@

在既包含开始标签又包含结束标签的 JSX 表达式中，这两个标签之间的内容被传递为专门的属性：`props.children`。有几种不同的方法来传递子代：

### 字符串字面量

你可以在开始和结束标签之间放入一个字符串，则 `props.children` 就是那个字符串。这对于许多**内置 HTML 元素**很有用。例如：

```react
<MyComponent>Hello world!</MyComponent>
<div>This is valid HTML &amp; JSX at the same time.</div>
```

JSX 会移除空行和开始与结尾处的空格。标签邻近的新行也会被移除，字符串常量内部的换行会被压缩成一个空格，所以下面这些都等价：

```react
<div>Hello World</div>

<div>
  Hello World
</div>

<div>
  Hello
  World
</div>

<div>

  Hello World
</div>
```

使用空格

```jsx
<span className='exhibit_strut'>llx</span>{" "}
<span className='exhibit_text'>txt</span>&ensp;
<img src={imgMiddle} alt="you" title='you'/>&thinsp;
<span className='exhibit_square'></span>&nbsp;
<span className='exhibit_square'>x</span>
```

### 布尔值、Null 和 Undefined 被忽略

`false`、`null`、`undefined` 和 `true` 都是有效的子代，只是它们不会被渲染。下面的 JSX 表达式将渲染为相同的东西：

```react
<div />

<div></div>

<div>{false}</div>

<div>{null}</div>

<div>{undefined}</div>

<div>{true}</div>
```

这有助于依据特定条件来渲染其他的 React 元素。例如，在以下 JSX 中，仅当 `showHeader` 为 `true` 时，才会渲染 `<Header />`：

```jsx
<div>
  {showHeader && <Header />}
  <Content />
</div>
```

一个告诫是 JavaScript 中的一些 [“falsy” 值](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)(比如数字 `0`)，它们依然会被 React 渲染。当 `props.message` 为空数组时，它会打印 `0`。

```react
<div>
  {props.messages.length &&
    <MessageList messages={props.messages} />
  }
</div>
```

要解决这个问题，请确保 `&&` 前面的表达式始终为布尔值：

```jsx
<div>
  {props.messages.length > 0 &&
    <MessageList messages={props.messages} />
  }
</div>
```

相反，如果你想让类似 `false`、`true`、`null` 或 `undefined` 出现在输出中，你必须先把它 [转换成字符串](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String#String_conversion) :

```react
<div>
  My JavaScript variable is {String(myVariable)}.
</div>
```

### JSX 标签嵌套

```react
<MyContainer>
  <MyFirstComponent />
  <MySecondComponent />
</MyContainer>
```

### JavaScript 表达式

```react
<MyContainer>
 {todos.map((message) => <Item key={message} message={message} />)}
</MyContainer>
```

map 函数的返回值是一个数组，数组的每个元素都是 JSX 标签，react 会自动把数组的每个元素都渲染出来

```jsx
<div>  {[1,2,3,4,5]}</div>
```

渲染成：“12345”

### props.children 函数

通常，JSX 中的 JavaScript 表达式将会被计算为字符串、React 元素或者是列表。不过，`props.children` 和其他 prop 一样，它可以传递任意类型的数据，而不仅仅是 React 已知的可渲染类型。例如，如果你有一个自定义组件，你可以把回调函数作为 `props.children` 进行传递：

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
      {(index) => <div key={index}>This is item {index} in the list</div>}
    </Repeat>
  );
}
```

你可以将任何东西作为子元素传递给自定义组件，只要确保在该组件渲染之前能够被转换成 React 理解的对象。这种用法并不常见，但可以用于扩展 JSX。

## JSX 本质

Babel 会把 JSX 转译成一个名为 `React.createElement()` 函数调用。

以下两种示例代码完全等效：

```jsx
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

```js
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

`React.createElement()` 会预先执行一些检查，以帮助你编写无错代码，但实际上它创建了一个这样的对象：

```js
// 注意：这是简化过的结构
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```

这些对象被称为 “React 元素”。它们描述了你希望在屏幕上看到的内容。React 通过读取这些对象，然后使用它们来构建 DOM 以及保持随时更新。

如果没有子节点，你还可以使用自闭合的标签形式，如：

```jsx
<div className="sidebar" />
```

会编译为:

```jsx
React.createElement(  'div',  {className: 'sidebar'},  null)
```

### JSX 标签是一个表达式

在编译之后，JSX 表达式会被转为普通 JavaScript 函数调用，并且对其取值后得到 JavaScript 对象。

也就是说，你可以在 `if` 语句和 `for` 循环的代码块中使用 JSX，将 JSX 赋值给变量，把 JSX 当作参数传入，以及从函数中返回 JSX：

```jsx
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```

### React 必须在作用域内

由于 JSX 会编译为 `React.createElement` 调用形式，所以 `React` 库也必须包含在 JSX 代码作用域内。

在 17 版本这个限制被取消了

如果你不使用 JavaScript 打包工具而是直接通过 `<script>` 标签加载 React，则必须将 `React` 挂载到全局变量中。

## 注意

- JSX 必须要有闭合标签，否则无法识别，不像 html 浏览器会自动加上
- **只能有一个根元素**，因为最终会调用 React.createElement 方法
- 因为 JSX 语法上更接近 JavaScript 而不是 HTML，所以 React DOM 使用 `camelCase`（小驼峰命名）来定义属性的名称，而不使用 HTML 属性名称的命名约定。

    > 例如，JSX 里的 `class` 变成了 `className`，而 `tabindex` 则变为 `tabIndex`。
    >
    > 绑定事件也是驼峰的

- 生成的列表需要添加 key 以作标志，方便 vDOM 的快速比对
- 在一个组件的子元素位置使用注释要用 {} 包起来
- 省略 Boolean 属性值会导致 JSX 认为 bool 值设为了 true。要传 false 时，必须使用属性表达式。这常用于表单元素中，比如 disabled、required、checked 和 readOnly 等。
- ```jsx
    const data = { name: 'foo', value: 'bar' }; 
    const component = <Component {...data} />;  
    ```

    ```jsx
    <div dangerouslySetInnerHTML={{__html: 'cc &copy; 2015'}} />
    ```

- 创建的虚拟 DOM 对象，属性和方法比实际 DOM 少得多

    ![1546602926375](/img/user/programming/font-end/framework/react/react-basic/1546602926375.png) ![1546602959571](/img/user/programming/font-end/framework/react/react-basic/1546602959571.png)

### JSX 防止注入攻击 @link XSS 安全

你可以安全地在 JSX 当中插入用户输入内容：

```jsx
const title = response.potentiallyMaliciousInput;// 直接使用是安全的：const element = <h1>{title}</h1>;
```

React DOM 在渲染所有输入内容之前，默认会进行 [转义](https://stackoverflow.com/questions/7381974/which-characters-need-to-be-escaped-on-html)。它可以确保在你的应用中，永远不会注入那些并非自己明确编写的内容。所有的内容在渲染之前都被转换成了字符串。这样可以有效地防止 [XSS（cross-site-scripting, 跨站脚本）](https://en.wikipedia.org/wiki/Cross-site_scripting) 攻击。

# 元素渲染

元素是构成 React 应用的最小砖块。

与浏览器的 DOM 元素不同，React 元素是创建开销极小的普通对象。React DOM 会负责更新 DOM 来与 React 元素保持一致。

## 将一个元素渲染为 DOM

假设你的 HTML 文件某处有一个 `<div>`：

```jsx
<div id="root"></div>
```

我们将其称为“根” DOM 节点，因为该节点内的所有内容都将由 React DOM 管理。

仅使用 React 构建的应用通常只有**单一的根 DOM 节点**。如果你在将 React 集成进一个已有应用，那么你可以在应用中包含任意多的独立根 DOM 节点。

想要将一个 React 元素渲染到根 DOM 节点中，只需把它们一起传入 `ReactDOM.render()`：

```jsx
const element = <h1>Hello, world</h1>;ReactDOM.render(element, document.getElementById('root'));
```

## 更新已渲染的元素

React 元素是 [不可变对象](https://en.wikipedia.org/wiki/Immutable_object)。一旦被创建，你就无法更改它的子元素或者属性。一个元素就像电影的单帧：它代表了某个特定时刻的 UI。

根据我们已有的知识，**更新 UI 唯一的方式**是创建一个全新的元素，并将其传入 `ReactDOM.render()`。

考虑一个计时器的例子：

```jsx
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(element, document.getElementById('root'));
}

setInterval(tick, 1000);
```

这个例子会在 `setInterval()` 回调函数，每秒都调用 `ReactDOM.render()`。

> **注意：**
>
> 在实践中，大多数 React 应用只会调用一次 `ReactDOM.render()`。在下一个章节，我们将学习如何将这些代码封装到 [有状态组件](https://zh-hans.reactjs.org/docs/state-and-lifecycle.html) 中。

### React 只更新它需要更新的部分

React DOM 会将元素和它的子元素与它们之前的状态进行比较，并只会进行必要的更新来使 DOM 达到预期的状态。

尽管每一秒我们都会新建一个描述整个 UI 树的元素，React DOM 只会更新实际改变了的内容，也就是例子中的文本节点。

![DOM inspector showing granular updates](/img/user/programming/font-end/framework/react/react-basic/granular-dom-updates-c158617ed7cc0eac8f58330e49e48224.gif)

根据我们的经验，考虑 UI 在任意给定时刻的状态，而不是随时间变化的过程，能够消灭一整类的 bug。

# 组件

组件允许你将 UI 拆分为独立可复用的代码片段，并对每个片段进行独立构思。本指南旨在介绍组件的相关理念。你可以 [参考详细组件 API](https://zh-hans.reactjs.org/docs/react-component.html)。

组件，从概念上类似于 JavaScript 函数。它接受任意的入参（即 “props”），并返回用于描述页面展示内容的 React 元素。

## 基础

### 函数组件

定义组件最简单的方式就是编写 JavaScript 函数：

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

该函数是一个有效的 React 组件，因为它接收唯一带有数据的 “props”（代表属性）对象与并返回一个 React 元素。这类组件被称为“函数组件”，因为它本质上就是 JavaScript 函数。

### **class 组件**

你同时还可以使用 [ES6 的 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes) 来定义组件：

```jsx
class Welcome extends React.Component {
  // 通过以下方式将 props 传递到父类的构造函数中：
  constructor(props) {
    super(props);
  }
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

上述两个组件在 React 里是等效的。

我们将在 [下一章节](https://zh-hans.reactjs.org/docs/state-and-lifecycle.html) 中讨论关于 class 的额外特性。在那之前，为了保持示例简单明了，将使用函数组件。

![1546605248667](/img/user/programming/font-end/framework/react/react-basic/1546605248667.png)

### 渲染组件

之前，我们遇到的 React 元素都只是 DOM 标签：

```jsx
const element = <div />;
```

不过，React 元素也可以是用户自定义的组件：

```jsx
const element = <Welcome name="Sara" />;
```

当 React 元素为用户自定义组件时，它会将 JSX 所接收的属性（attributes）转换为单个对象传递给组件，这个对象被称之为 “props”。

**示例**

例如，这段代码会在页面上渲染 “Hello, Sara”：

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

### 组合组件

组件可以在其输出中引用其他组件。这就可以让我们用同一组件来抽象出任意层次的细节。按钮，表单，对话框，甚至整个屏幕的内容：在 React 应用程序中，这些通常都会以组件的形式表示。

通常来说，每个新的 React 应用程序的顶层组件都是 `App` 组件。但是，如果你将 React 集成到现有的应用程序中，你可能需要使用像 `Button` 这样的小组件，并自下而上地将这类组件逐步应用到视图层的每一处。

### 提取组件

将组件拆分为更小的组件。

最初看上去，提取组件可能是一件繁重的工作，但是，在大型应用中，构建可复用组件库是完全值得的。

根据经验来看，如果 UI 中有一部分被多次使用（`Button`，`Panel`，`Avatar`），或者组件本身就足够复杂（`App`，`FeedStory`，`Comment`），那么它就是一个可复用组件的候选项。

## Props

`npm install --save prop-types`

**组件标签的所有属性都保存在 props 对象中**

通过标签属性从组件外向组件内传递变化的数据

如果 props 是引用类型，则会直接传递引用，因为 react 组件是一个纯函数，所以我们不会去改写传入的参数，所以 react 思想指导下不会存在问题。即使，父组件传递的是它的 state，如果没有同时传递 setState 函数，则不会引起更新

> 如果传递 setState() 给子组件，可以在子组件控制父组件的更新吗？
>
> ![1557323541570](/img/user/programming/font-end/framework/react/react-basic/1557323541570.png)
>
> 可以看到，传递的父组件的 setState 方法，无法在子组件触发父组件的更新，这其实是因为 this 没有绑定
>
> BUT：in hooks is ok，因为 hook 不依赖 this

### Props 默认值

如果你没给 prop 赋值，它的默认值是 `true`。以下两个 JSX 表达式是等价的：

```jsx
<MyTextBox autocomplete />

<MyTextBox autocomplete={true} />
```

通常，我们不建议这样使用，因为它可能与 [ES6 对象简写](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Object_initializer#New_notations_in_ECMAScript_2015) 混淆，`{foo}` 是 `{foo: foo}` 的简写，而不是 `{foo: true}`。这样实现只是为了保持和 **HTML** 中标签属性的行为一致。

### Props 的只读性

组件无论是使用函数声明还是通过 class 声明，都决不能修改自身的 props。来看下这个 `sum` 函数：

```jsx
function sum(a, b) {
  return a + b;
}
```

这样的函数被称为 [“纯函数”](https://en.wikipedia.org/wiki/Pure_function)，因为该函数不会尝试更改入参，且多次调用下相同的入参始终返回相同的结果

> 修改父组件的 state，然后再通过 props 传递修改后的结果，不算是违背纯函数

相反，下面这个函数则不是纯函数，因为它更改了自己的入参：

```jsx
function withdraw(account, amount) {
  account.total -= amount;
}
```

React 非常灵活，但它也有一个严格的规则：**所有 React 组件都必须像纯函数一样保护它们的 props 不被更改。**

当然，应用程序的 UI 是动态的，并会伴随着时间的推移而变化。在 [下一章节](https://zh-hans.reactjs.org/docs/state-and-lifecycle.html) 中，我们将介绍一种新的概念，称之为 “state”。在不违反上述规则的情况下，state 允许 React 组件随用户操作、网络响应或者其他变化而动态更改输出内容。

### propTypes

```js
//引入prop-types包
Person.propTypes = {
  name: PropTypes.string.isRequired,
  sex: PropTypes.string,
  age: PropTypes.number
  children: PropTypes.oneOfType([
  PropTypes.arrayOf(PropTypes.node),
  PropTypes.node,
  PropTypesobject,
  ]),
}
```

使用 oneOfType() 方法，参数是一个 propTypes 数组

func 而不是 function，bool 而不是 boolean

> 这是因为 function 和 boolean 在 JavaScript 里是关键词

PropTypes 有很多类型支持，不仅有基本类型，还包括枚举和自定义类型。

组件的静态属性 propTypes 小写，引入的 函数 PropTypes 大写

PropTypes 仅仅是对 JS 弱类型的补充，只是做类型检查而已，props 在传入的时候 就已经可以通过 this.props 直接使用了

### 与 Typescript 配合 @link

## State & setState

参考：

<https://zhuanlan.zhihu.com/p/25954470>

<https://www.oschina.net/translate/functional-setstate-is-the-future-of-react>

<https://segmentfault.com/q/1010000011580045>

<https://zhuanlan.zhihu.com/p/25990883>

本页面介绍了 React 组件中 state 和生命周期的概念。你可以查阅 [详细的组件 API 参考文档](https://zh-hans.reactjs.org/docs/react-component.html)。

state 与 props 类似，但是 state 是私有的，并且完全受控于当前组件。

每次 class 组件更新时 `render` 方法都会被调用，但只要在相同的 DOM 节点中渲染 `<Clock />` ，就仅有一个 `Clock` 组件的 class 实例被创建使用。这就使得我们可以使用如 state 或生命周期方法等很多其他特性。

组件被称为 " 状态机 ", 通过更新组件的 state 来更新对应的页面显示 (重新渲染组件)

### Init State

添加一个 [class 构造函数](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes#Constructor)，然后在该函数中为 `this.state` 赋初值：

```js
class Clock extends React.Component {
  // 通过以下方式将 props 传递到父类的构造函数中：
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
}
```

Class 组件应该始终使用 `props` 参数来调用父类的构造函数。

### Update State

## forceUpdat

默认情况下，当组件的 state 或 props 发生变化时，组件将重新渲染。如果 `render()` 方法依赖于其他数据，则可以调用 `forceUpdate()` 强制让组件重新渲染。

调用 `forceUpdate()` 将致使组件调用 `render()` 方法，此操作会跳过该组件的 `shouldComponentUpdate()`。但其子组件会触发正常的生命周期方法，包括 `shouldComponentUpdate()` 方法。如果标记发生变化，React 仍将只更新 DOM。

通常你应该避免使用 `forceUpdate()`，尽量在 `render()` 只使用 `this.props` 和 `this.state`

forceUpdate 在批量与否的表现上，和 setState 是一样的。在 React 有控制权的函数里，是批量的。

## Class 属性

### defaultProps

`defaultProps` 可以为 **Class 组件** 添加默认 props。这一般用于 props 未赋值，但又不能为 null 的情况。例如：

```js
 class CustomButton extends React.Component {  // ...}CustomButton.defaultProps = {  color: 'blue'};
```

如果未提供 `props.color`，则默认设置为 `'blue'`

```js
render() {  return <CustomButton /> ; // props.color 将设置为 'blue'}
```

如果 `props.color` 被设置为 `null`，则它将保持为 `null`

```js
render() {  return <CustomButton color={null} /> ; // props.color 将保持是 null}
```

### displayName

`displayName` 字符串多用于调试消息。通常，你不需要设置它，因为它可以根据函数组件或 class 组件的名称推断出来。如果调试时需要显示不同的名称或创建高阶组件，请参阅 [使用 displayname 轻松进行调试](https://zh-hans.reactjs.org/docs/higher-order-components.html#convention-wrap-the-display-name-for-easy-debugging) 了解更多

# 组件的生命周期 @@@

参考

<https://juejin.im/post/5b6f1800f265da282d45a79a#heading-13>

<http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/>

<https://zhuanlan.zhihu.com/p/38030418>

## 概述

- 组件对象从创建到死亡它会经历特定的生命周期阶段
- React 组件对象包含一系列的勾子函数 (生命周期回调函数), 在生命周期特定时刻回调
- 我们在定义组件时, 可以重写特定的生命周期回调函数, 做特定的工作
- 生命周期在 16.4 版本趋于稳定

### 生命周期流程图

- <http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/>

    ![1556520105804](/img/user/programming/font-end/framework/react/react-basic/1556520105804.png)

- 组件的三个生命周期状态:
  - Mount：插入真实 DOM
  - Update：被重新渲染
  - Unmount：被移出真实 DOM

## 挂载阶段

### 概述

- 挂载阶段，也可以理解为组件的初始化阶段，就是将我们的组件插入到 DOM 中，只会发生一次
- 这个阶段的**生命周期函数**调用如下：
  - constructor
  - ~~getDefaultProps()~~：设置默认的 props，也可以用 dufaultProps 设置组件的默认属性。
  - ~~getInitialState()~~：初始化 state，可以直接在 constructor 中定义 this.state
  - static getDerivedStateFromProps
  - ~~componentWillMount/UNSAVE_componentWillMount~~
  - render
  - componentDidMount

### Constructor

- 组件构造函数，第一个被执行
- 如果没有显示定义它，我们会拥有一个默认的构造函数
- 如果显示定义了构造函数，我们必须在构造函数第一行执行 super(props)，否则我们无法在构造函数里拿到 this 对象，这些都属于 ES6 的知识
- 在构造函数里面我们一般会做两件事：

    1. 初始化 state 对象
    2. 给自定义方法绑定 this

    ```js
    constructor(props) {    super(props)        this.state = {      select,      height: 'atuo',      externalClass,      externalClassText    }    this.handleChange1 = this.handleChange1.bind(this)    this.handleChange2 = this.handleChange2.bind(this)}
    ```

**this.setState**

- 禁止在构造函数中调用 setState，可以直接给 state 设置初始值
- 构造函数是唯一可以给 `this.state` 赋值的地方，如需在其他方法中赋值，你应使用 `this.setState()` 替代。

**注意**

- **避免将 props 的值复制给 state！这是一个常见的错误：**

    ```js
    constructor(props) {super(props);// 不要这样做this.state = { color: props.color };}
    ```

- 如此做毫无必要（你可以直接使用 `this.props.color`），同时还产生了 bug（更新 prop 中的 `color` 时，并不会影响 state）
- **只有在你刻意忽略 prop 更新的情况下使用。**此时，应将 prop 重命名为 `initialColor` 或 `defaultColor`。必要时，你可以 [修改它的 `key`](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-uncontrolled-component-with-a-key)，以强制“重置”其内部 state。
- 请参阅关于 [避免派生状态的博文](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html)，以了解出现 state 依赖 props 的情况该如何处理。 @@@

### Static getDerivedStateFromProps(nextProps, prevState)

- 一个静态方法，所以不能在这个函数里面使用 this
- 一个纯函数

**参数**

- props，接收到的新参数
- state，当前的 state 对象

    > state 有两种情况，如果是挂载阶段，prevState 就是初始化的 state；如果是更行阶段就是 updater 运行之后的 state

**返回值**

- 返回一个对象用来更新当前的 state 对象
- 如果不需要更新可以返回 null

**描述**

- 该函数会在**挂载**时，**接收到新的 props**，**调用了 setState**和**forceUpdate**时被调用

    > 在 React v16.3 时只有在挂载时和接收到新的 props 被调用，据说这是官方的失误，在 16.4 修复了

    > 每当父组件引发当前组件的渲染过程时，getDerivedStateFromProps 会被调用，这样我们有一个机会可以根据新的 props 和之前的 state 来调整新的 state
    >
    > 父组件的重新渲染，都会因此子组件的重新渲染

- 这个方法就是为了取代之前的~~componentWillMount~~、~~componentWillReceiveProps~~和~~componentWillUpdate~~
- 用一个静态函数 getDerivedStateFromProps 来取代被 deprecate 的几个生命周期函数，就是强制开发者在 render 之前只做无副作用的操作，而且能做的操作局限在根据 props 和 state 决定新的 state，而已。
- 此方法适用于 [罕见的用例](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#when-to-use-derived-state)，即 state 的值在任何时候都取决于 props。例如，实现 `<Transition>` 组件可能很方便，该组件会比较当前组件与下一组件，以决定针对哪些组件进行转场动画。
- 派生状态会导致代码冗余，并使组件难以维护。 [确保你已熟悉这些简单的替代方案：](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html)
  - 如果你需要**执行副作用**（例如，数据提取或动画）以响应 props 中的更改，请改用 [`componentDidUpdate`](https://zh-hans.reactjs.org/docs/react-component.html#componentdidupdate)。
  - 如果只想在 **prop 更改时重新计算某些数据**，[请使用 memoization helper 代替](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#what-about-memoization)。
  - 如果你想**在 prop 更改时“重置”某些 state**，请考虑使组件 [完全受控](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-controlled-component) 或 [使用 `key` 使组件完全不受控](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-uncontrolled-component-with-a-key) 代替。
- 此方法无权访问组件实例。如果你需要，可以通过提取组件 props 的纯函数及 class 之外的状态，在 `getDerivedStateFromProps()` 和其他 class 方法之间重用代码。

**this.setState**

- 因为是静态方法，所以无法，而且返回值就是更新 state 的，没有必要

**示例**

- 当我们接收到新的属性想去修改我们 state，可以使用 getDerivedStateFromProps

    ```js
    class ExampleComponent extends React.Component {  state = {    isScrollingDown: false,    lastRow: null  }  static getDerivedStateFromProps(nextProps, prevState) {    if (nextProps.currentRow !== prevState.lastRow) {      return {        isScrollingDown:        nextProps.currentRow > prevState.lastRow,        lastRow: nextProps.currentRow      }    }    return null  }}
    ```

### Render

- React 中最核心的方法，一个组件中必须要有这个方法

**返回值**

- 原生的 DOM，如 div
- React 组件
- Fragment（片段）
- Portals（插槽）
- 字符串和数字，被渲染成 text 节点
- Boolean 和 null，不会渲染任何东西

    > Fragment 和 Portals 是 React16 新增的

**描述**

- render 函数是纯函数，里面只做一件事，就是返回需要渲染的东西，不应该包含其它的业务逻辑，如数据请求，对于这些业务逻辑请移到 componentDidMount 和 componentDidUpdate 中
- `render()` 方法是 class 组件中唯一必须实现的方法。
- 当 `render` 被调用时，它会检查 `this.props` 和 `this.state` 的变化并返回以下类型之一：
  - **React 元素**。通常通过 JSX 创建。例如，`<div />` 会被 React 渲染为 DOM 节点，`<MyComponent />` 会被 React 渲染为自定义组件，无论是 `<div />` 还是 `<MyComponent />` 均为 React 元素。
  - **数组或 fragments**。 使得 render 方法可以返回多个元素。欲了解更多详细信息，请参阅 [fragments](https://zh-hans.reactjs.org/docs/fragments.html) 文档。
  - **Portals**。可以渲染子节点到不同的 DOM 子树中。欲了解更多详细信息，请参阅有关 [portals](https://zh-hans.reactjs.org/docs/portals.html) 的文档
  - **字符串或数值类型**。它们在 DOM 中会被渲染为文本节点
  - **布尔类型或 null**。什么都不渲染。（主要用于支持返回 `test && <Child />` 的模式，其中 test 为布尔类型。)
- `render()` 函数应该为纯函数，这意味着在不修改组件 state 的情况下，每次调用时都返回相同的结果，并且它不会直接与浏览器交互。
- 如需与浏览器进行交互，请在 `componentDidMount()` 或其他生命周期方法中执行你的操作。保持 `render()` 为纯函数，可以使组件更容易思考。

**注意**

- 如果 `shouldComponentUpdate()` 返回 false，则不会调用 `render()`。

**this.setState**

- 不要使用，可能造成无限循环

### componentDidMount

- 组件装载之后调用，此时我们可以：
  - 获取到 DOM 节点并操作，比如对 canvas，svg 的操作
  - 服务器请求，订阅都可以写在这个里面，但是记得在 componentWillUnmount 中取消订阅

**this.setState**

- 在 componentDidMount 中调用 setState 会触发一次额外的渲染，多调用了一次 render 函数，但是用户对此没有感知，因为它是**在浏览器刷新屏幕前**执行的，但是我们应该在开发中避免它，因为它会带来一定的性能问题，我们应该在 constructor 中初始化我们的 state 对象，而不应该在 componentDidMount 调用 state 方法
- 但实际情况是，有一些场景不得不需要 setState，比如**计算组件的位置或宽高时，就不得不让组件先渲染，更新必要的信息后，再次渲染。**
- 如果你的渲染依赖于 DOM 节点的大小或位置，比如实现 modals 和 tooltips 等情况下，你可以使用此方式处理

## 更新阶段

### 概述

- 更新阶段，当**父组件向下传递 props 导致 props 发生改变**，或组件内部调用了 setState 或者 forceUpdate 发生，会发生多次
- 这个阶段的生命周期函数调用如下：
  - ~~componentWillReceiveProps/UNSAFE_componentWillReceiveProps~~
  - static getDerivedStateFromProps
  - shouldComponentUpdate
  - ~~componentWillUpdate/UNSAFE_componentWillUpdate~~
  - render
  - getSnapshotBeforeUpdate
  - componentDidUpdate

### Static getDerivedStateFromProps

- 这个方法在装载阶段已经讲过了，这里不再赘述，记住在更新阶段，无论我们接收到新的属性，调用了 setState 还是调用了 forceUpdate，这个方法都会被调用

### shouldComponentUpdate(nextProps, nextState)

**参数**

- nextProps，表示新的属性
- nextState，表示变化之后的 state

    > 由最后一个 updater 和 static getDerivedStateFromProps 共同决定

**返回值**

- 返回一个布尔值
- true 表示会触发重新渲染，false 表示不会触发重新渲染
- 默认返回 true

**描述**

- **注意：**当我们调用 forceUpdate 并不会触发此方法
- 因为默认是返回 true，也就是只要接收到新的属性和调用了 setState 都会触发重新的渲染，这会带来一定的性能问题，所以我们需要将**this.props 与 nextProps**以及**this.state 与 nextState**进行比较来决定是否返回 false，来减少重新渲染
- **注意：**返回 `false` 并不会阻止子组件在 state 更改时重新渲染。
- shouldComponentUpdate 的本质是用来进行**正确的组件渲染**。怎么理解呢？我们需要先从初始化组件的过程开始说起，假设有如图 1-8 所示的组件关系，它呈三级的树状结构，其中空心圆表示已经渲染的节点

    ![1551871616353](/img/user/programming/font-end/framework/react/react-basic/1551871616353.png)

- 当父节点 props 改变的时候，在理想情况下，只需渲染在**一条链路**上有相关 props 改变的节点即可，如图 1-9 所示

    ![1551871640682](/img/user/programming/font-end/framework/react/react-basic/1551871640682.png)

- 而默认情况下，React 会**渲染所有的节点**，因为 shouldComponentUpdate **默认返回 true**。正确的组件渲染从另一个意义上说，也是性能优化的手段之一。

    > 值得注意的是，**无状态组件是没有生命周期方法的**，这也意味着它没有 shouldComponent- Update。渲染到该类组件时，**每次都会重新渲染**。当然，不少开发者在使用无状态组件时会纠结这一点。
    >
    > 那么解决办法是？<https://scotch.io/tutorials/react-166-reactmemo-for-functional-components-rendering-control>，高阶组件包装无状态组件

- 但是官方提倡我们使用**PureComponent**来减少重新渲染的次数而不是手工编写 shouldComponentUpdate 代码，`shouldComponentUpdate()`。`PureComponent` 会对 props 和 state 进行浅层比较，并减少了跳过必要更新的可能性。

    > 我们不建议在 `shouldComponentUpdate()` 中进行深层比较或使用 `JSON.stringify()`。这样非常影响效率，且会损害性能。

- 具体该怎么选择，全凭开发者自己选择

    > 在未来的版本，shouldComponentUpdate 返回 false，仍然可能导致组件重新的渲染，这是官方自己说的：
    >
    > 目前，如果 `shouldComponentUpdate()` 返回 `false`，则不会调用 [`UNSAFE_componentWillUpdate()`](https://zh-hans.reactjs.org/docs/react-component.html#unsafe_componentwillupdate)，[`render()`](https://zh-hans.reactjs.org/docs/react-component.html#render) 和 [`componentDidUpdate()`](https://zh-hans.reactjs.org/docs/react-component.html#componentdidupdate)。后续版本，React 可能会将 `shouldComponentUpdate` 视为提示而不是严格的指令，并且，当返回 `false` 时，仍可能导致组件重新渲染。

**this.setState**

- 禁止在 shouldComponentUpdate 中**调用 setState**，这会造成循环调用，直至耗光浏览器内存后崩溃。
- 目前的 React 设计，shouldComponentUpdate 被调用时，this.state 并没有被改变，setState 产生的状态改变是通过参数 nextState 来体现的

### Render

- 更新阶段也会触发，装载阶段已经讲过了，不再赘述

**注意**

- 如果 `shouldComponentUpdate()` 返回 false，则不会调用 `render()`。

### getSnapshotBeforeUpdate(prevProps, prevState)

- 这个方法在 render 之后，componentDidUpdate 之前调用
- [`getSnapshotBeforeUpdate()`](https://zh-hans.reactjs.org/docs/react-component.html#getsnapshotbeforeupdate)

**参数**

- prevProps，表示之前的属性
- prevState，表示之前的 state

**返回值**

- 这个函数有一个返回值，会作为第三个参数传给 componentDidUpdate
- 如果你不想要返回值，请返回 null
- 不写的话控制台会有警告：

    ![img](react-basic/1652d5a54fe61cee)

**描述**

- 这个方法一定要和 componentDidUpdate 一起使用，否则控制台也会有警告

    ![img](react-basic/1652d5bf732e3ff3)

**this.setState**

**示例**

- 前面说过这个方法时用来代替 componentWillUpdate/UNSAVE_componentWillUpdate，下面举个例子说明下：

    ```js
    class ScrollingList extends React.Component {  constructor(props) {    super(props);    this.listRef = React.createRef();  }  getSnapshotBeforeUpdate(prevProps, prevState) {    // Are we adding new items to the list?    // Capture the scroll position so we can adjust scroll later.    if (prevProps.list.length < this.props.list.length) {      const list = this.listRef.current;      return list.scrollHeight - list.scrollTop;    }    return null;  }  componentDidUpdate(prevProps, prevState, snapshot) {    // If we have a snapshot value, we've just added new items.    // Adjust scroll so these new items don't push the old ones out of view.    // (snapshot here is the value returned from getSnapshotBeforeUpdate)    if (snapshot !== null) {      const list = this.listRef.current;      list.scrollTop = list.scrollHeight - snapshot;    }  }  render() {    return (      <div ref={this.listRef}>{/* ...contents... */}</div>    );  }}
    ```

### componentDidUpdate(prevProps, prevState, snapshot)

- `componentDidUpdate()` 会在更新后会被立即调用。首次渲染不会执行此方法。
- 该方法在 getSnapshotBeforeUpdate 方法之后被调用

**参数**

- prevProps，表示之前的 props
- prevState，表示之前的 state
- snapshot，第三个参数是 getSnapshotBeforeUpdate 的返回值

**描述**

- 当组件更新后，可以在此处对 DOM 进行操作。如果你对更新前后的 props 进行了比较，也可以选择在此处进行网络请求。（例如，当 props 未发生变化时，则不会执行网络请求）。

    ```js
    componentDidUpdate(prevProps) {  // 典型用法（不要忘记比较 props）：  if (this.props.userID !== prevProps.userID) {    this.fetchData(this.props.userID);  }}
    ```

**注意**

- 如果 [`shouldComponentUpdate()`](https://zh-hans.reactjs.org/docs/react-component.html#shouldcomponentupdate) 返回值为 false，则不会调用 `componentDidUpdate()`。

**this.setState**

- 还可以 setState，但是注意一定要用 if 语句控制，否则会导致无限循环
- 不要将 props “镜像”给 state，请考虑直接使用 props。 欲了解更多有关内容，请参阅 [为什么 props 复制给 state 会产生 bug](https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html)。

## 卸载阶段

### 概述

- 卸载阶段，当我们的组件被卸载或者销毁了
- 刷新浏览器的话会触发这个生命周期嘛？
- 这个阶段的生命周期函数只有一个：
  - componentWillUnmount

### componentWillUnmount

- 现在含有 `will` 的生命周期函数只有这一个了
- 当我们的组件被卸载或者销毁了就会调用，我们可以在这个函数：
  - 清除一些定时器
  - 取消网络请求
  - 清理无效的 DOM 元素等**垃圾清理**工作

**this.setState**

- 注意不要在这个函数里去调用 setState，因为组件不会重新渲染了

## 错误处理

- [Error boundaries](https://zh-hans.reactjs.org/docs/error-boundaries.html) 是一个 Rreact 组件
- 当渲染过程，生命周期，或子组件的构造函数中抛出错误时，会调用如下方法：
  - [`static getDerivedStateFromError()`](https://zh-hans.reactjs.org/docs/react-component.html#static-getderivedstatefromerror)
  - [`componentDidCatch()`](https://zh-hans.reactjs.org/docs/react-component.html#componentdidcatch)
- <https://www.zcfy.cc/article/2-minutes-to-learn-react-16s-componentdidcatch-lifecycle-method>

## 深入笔记

- 对于 React 组件，生命周期是它的核心概念之一。在 1.5 节中，我们已经大概了解了生命周期的概念及用法，本节将深入源码来剖析 React 生命周期的管理艺术。
- React 的主要思想是通过构建可复用组件来构建用户界面。所谓组件，其实就是**有限状态机（FSM）**，通过状态渲染对应的界面，且每个组件都有自己的生命周期，它规定了组件的状态和方法需要在哪个阶段改变和执行。
- 有限状态机，表示有限个状态以及在这些状态之间的转移和动作等行为的模型。一般通过**状态、事件、转换和动作**来描述有限状态机。
- 图 3-8 是描述组合锁状态机的模型图，包括 5 个状态、5 个状态自转换、6 个状态间转换和 1 个复位 RESET 转换到状态 s1。状态机能够记住目前所处的状态，可以根据当前的状态做出相应的决策，并且可以在进入不同的状态时做不同的操作。状态机将复杂的关系简单化，利用这种自然而直观的方式可以让代码更容易理解。
- ![1552093693317](/img/user/programming/font-end/framework/react/react-basic/1552093693317.png)
- React 正是利用这一概念，通过管理状态来实现对组件的管理。
  - 例如，某个组件有**显示和隐藏**两个状态，通常会设计两个方法 show() 和 hide() 来实现切换，而 React 只需要设置状态 setState({ showed: true/false }) 即可实现。同时，React 还引入了组件的生命周期这个概念。通过它，就可以实现组件的**状态机控制**，从而达到“**生命周期→状态→组件**”的和谐画面。
- 虽然组件、状态机、生命周期这三者都不是 React 独创的，但 Web Components 标准与其中的自定义组件的生命周期的概念相似。就目前而言，React 是将这几种概念结合得相对清晰、流畅的 View 实现。
- 其实，mountComponent 本质上是通过**递归渲染内容**的，由于递归的特性，父组件的 componentWillMount 在其子组件的 componentWillMount 之前调用，而父组件的 componentDidMount 在其子组件的 componentDidMount 之后调用。

# 事件处理

## 事件绑定

- React 元素的事件处理和 DOM 元素的很相似，但是有一点语法上的不同:
  - React 事件的命名采用小驼峰式（camelCase），而不是纯小写。
  - 使用 JSX 语法时你需要传入一个**函数**作为事件处理函数，而不是一个字符串
- 例如，传统的 HTML：

    ```html
    <button onclick="activateLasers()">  Activate Lasers</button>
    ```

- 在 React 中略微不同：

    ```jsx
    <button onClick={activateLasers}>  Activate Lasers</button>
    ```

## 阻止默认行为

- 在 React 中另一个不同点是你不能通过返回 `false` 的方式阻止默认行为。你必须显式的使用 `preventDefault` 。例如，传统的 HTML 中阻止链接默认打开一个新页面，你可以这样写：

    ```html
    <a href="#" onclick="console.log('The link was clicked.'); return false">  Click me</a>
    ```

- 在 React 中，可能是这样的：

    ```jsx
    function ActionLink() {  function handleClick(e) {    e.preventDefault();    console.log('The link was clicked.');  }  return (    <a href="#" onClick={handleClick}>      Click me    </a>  );}
    ```

## Event 对象

- 在这里，`e` 是一个合成事件。React 根据 [W3C 规范](https://www.w3.org/TR/DOM-Level-3-Events/) 来定义这些合成事件，所以你不需要担心跨浏览器的兼容性问题。如果想了解更多，请查看 [`SyntheticEvent`](https://zh-hans.reactjs.org/docs/events.html) 参考指南。
- 使用 React 时，你一般不需要使用 `addEventListener` 为已创建的 DOM 元素添加监听器。React 恰恰与之相反，你仅需要在该元素初始渲染的时候添加一个监听器。

## This 的绑定

- 当你使用 [ES6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes) 语法定义一个组件的时候，通常的做法是将事件处理函数声明为 class 中的方法。
- 你必须谨慎对待 JSX 回调函数中的 `this`，在 JavaScript 中，class 的方法默认不会 [绑定](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind) `this`。如果你忘记绑定 `this.handleClick` 并把它传入了 `onClick`，当你调用这个函数的时候 `this` 的值为 `undefined`。

    ```js
    // 为了在回调中使用 `this`，这个绑定是必不可少的this.handleClick = this.handleClick.bind(this);
    ```

- 如果觉得使用 `bind` 很麻烦，这里有两种方式可以解决。如果你正在使用实验性的 [public class fields 语法](https://babeljs.io/docs/plugins/transform-class-properties/)，你可以使用 class fields 正确的绑定回调函数：

    ```jsx
    class LoggingButton extends React.Component {  // 此语法确保 `handleClick` 内的 `this` 已被绑定。  // 注意: 这是 *实验性* 语法。  handleClick = () => {    console.log('this is:', this);  }  render() {    return (      <button onClick={this.handleClick}>        Click me      </button>    );  }}
    ```

- 如果你没有使用 class fields 语法，你可以在回调中使用 [箭头函数](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)：

    ```jsx
    class LoggingButton extends React.Component {  handleClick() {    console.log('this is:', this);  }  render() {    // 此语法确保 `handleClick` 内的 `this` 已被绑定。    return (      <button onClick={(e) => this.handleClick(e)}>        Click me      </button>    );  }}
    ```

- 此语法问题在于每次渲染 `LoggingButton` 时都会创建不同的回调函数。在大多数情况下，这没什么问题，但如果该回调函数作为 prop 传入子组件时，这些组件可能会进行额外的重新渲染。我们通常建议在构造器中绑定或使用 class fields 语法来避免这类性能问题。

## 向事件处理程序传递参数

- 在循环中，通常我们会为事件处理函数传递额外的参数。例如，若 `id` 是你要删除那一行的 ID，以下两种方式都可以向事件处理函数传递参数：

    ```jsx
    <button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button><button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
    ```

- 上述两种方式是等价的，分别通过 [箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) 和 [`Function.prototype.bind`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind) 来实现。
- 在这两种情况下，React 的事件对象 `e` 会被作为第二个参数传递。如果通过箭头函数的方式，事件对象必须显式的进行传递，而通过 `bind` 的方式，事件对象以及更多的参数将会被隐式的进行传递。

# Refs & DOM #

## 概述

- Refs 提供了一种方式，允许我们访问 DOM 节点或在 render 方法中创建的 React 元素。
- 在典型的 React 数据流中，[props](https://zh-hans.reactjs.org/docs/components-and-props.html) 是父组件与子组件交互的唯一方式。要修改一个子组件，你需要使用新的 props 来重新渲染它。但是，在某些情况下，你需要在典型**数据流**之外强制修改子组件。被修改的子组件可能是一个 React 组件的实例，也可能是一个 DOM 元素。对于这两种情况，React 都提供了解决办法。

### 何时使用 Refs

- 下面是几个适合使用 refs 的情况：
  - 管理焦点，文本选择或媒体播放。
  - 触发强制动画。
  - 集成第三方 DOM 库。
- 避免使用 refs 来做任何可以通过声明式实现来完成的事情。
- 举个例子，避免在 `Dialog` 组件里暴露 `open()` 和 `close()` 方法，最好传递 `isOpen` 属性。

### 勿过度使用 Refs

- 你可能首先会想到使用 refs 在你的 app 中“让事情发生”。如果是这种情况，请花一点时间，认真再考虑一下 state 属性应该被安排在哪个组件层中。通常你会想明白，让更高的组件层级拥有这个 state，是更恰当的。查看 [状态提升](https://zh-hans.reactjs.org/docs/lifting-state-up.html) 以获取更多有关示例。

    注意：

    > 下面的例子已经更新为使用在 React 16.3 版本引入的 `React.createRef()` API。如果你正在使用一个较早版本的 React，我们推荐你使用 [回调形式的 refs](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html#callback-refs)。

## 使用

### 创建 Refs

- Refs 是使用 `React.createRef()` 创建的，并通过 `ref` 属性附加到 React 元素。在构造组件时，通常将 Refs 分配给实例属性，以便可以在整个组件中引用它们。

    ```jsx
    class MyComponent extends React.Component {  constructor(props) {    super(props);    this.myRef = React.createRef();  }  render() {    return <div ref={this.myRef} />;  }}
    ```

### 访问 Refs

- 当 ref 被传递给 `render` 中的元素时，对该节点的引用可以在 ref 的 `current` 属性中被访问。

    ```js
    const node = this.myRef.current;
    ```

- ref 的值根据节点的类型而有所不同：
  - 当 `ref` 属性用于 HTML 元素时，构造函数中使用 `React.createRef()` 创建的 `ref` 接收底层 DOM 元素作为其 `current` 属性。
  - 当 `ref` 属性用于自定义 class 组件时，`ref` 对象接收组件的**挂载实例**作为其 `current` 属性。
  - **你不能在函数组件上使用 ref 属性**，因为他们没有实例。

#### 为 DOM 元素添加 Ref

- ```jsx
    class CustomTextInput extends React.Component {  constructor(props) {    super(props);    // 创建一个 ref 来存储 textInput 的 DOM 元素    this.textInput = React.createRef();    this.focusTextInput = this.focusTextInput.bind(this);  }  focusTextInput() {    // 直接使用原生 API 使 text 输入框获得焦点    // 注意：我们通过 "current" 来访问 DOM 节点    this.textInput.current.focus();  }  render() {    // 告诉 React 我们想把 <input> ref 关联到，构造器里创建的 `textInput` 上    return (      <div>        <input          type="text"          ref={this.textInput} />        <input          type="button"          value="Focus the text input"          onClick={this.focusTextInput}        />      </div>    );  }}
    ```
- React 会在组件挂载时给 `current` 属性传入 DOM 元素，并在组件卸载时传入 `null` 值。`ref` 会在 `componentDidMount` 或 `componentDidUpdate` 生命周期钩子触发前更新。

#### 为 Class 组件添加 Ref

- 如果我们想包装上面的 `CustomTextInput`，来模拟它挂载之后立即被点击的操作，我们可以使用 ref 来获取这个自定义的 input 组件并手动调用它的 `focusTextInput` 方法：

    ```jsx
    class AutoFocusTextInput extends React.Component {  constructor(props) {    super(props);    this.textInput = React.createRef();  }  componentDidMount() {    this.textInput.current.focusTextInput();  }  render() {    return (      <CustomTextInput ref={this.textInput} />    );  }}
    ```

#### Refs 与函数组件

- **你不能在函数组件上使用 ref 属性**，因为它们没有实例。
- 如果你需要使用 ref，你应该将组件转化为一个 class，就像当你需要使用生命周期钩子或 state 时一样。
- 不管怎样，你可以**在函数组件内部使用 ref 属性**，只要它指向一个 DOM 元素或 class 组件：

    ```jsx
    function CustomTextInput(props) {  // 这里必须声明 textInput，这样 ref 才可以引用它  let textInput = React.createRef();  function handleClick() {    textInput.current.focus();  }  return (    <div>      <input        type="text"        ref={textInput} />      <input        type="button"        value="Focus the text input"        onClick={handleClick}      />    </div>  );}
    ```

### 回调 Refs

- React 也支持另一种设置 refs 的方式，称为“回调 refs”。它能助你更精细地控制何时 refs 被设置和解除。
- 不同于传递 `createRef()` 创建的 `ref` 属性，你会传递一个函数。这个函数中接受 React 组件实例或 HTML DOM 元素作为参数，以使它们能在其他地方被存储和访问。
- 下面的例子描述了一个通用的范例：使用 `ref` 回调函数，在实例的属性中存储对 DOM 节点的引用。

    ```jsx
    class CustomTextInput extends React.Component {  constructor(props) {    super(props);    this.textInput = null;    this.setTextInputRef = element => {      this.textInput = element;    };    this.focusTextInput = () => {      // 使用原生 DOM API 使 text 输入框获得焦点      if (this.textInput) this.textInput.focus();    };  }  componentDidMount() {    // 组件挂载后，让文本框自动获得焦点    this.focusTextInput();  }  render() {    // 使用 `ref` 的回调函数将 text 输入框 DOM 节点的引用存储到 React    // 实例上（比如 this.textInput）    return (      <div>        <input          type="text"          ref={this.setTextInputRef}        />        <input          type="button"          value="Focus the text input"          onClick={this.focusTextInput}        />      </div>    );  }}
    ```

- React 将在组件挂载时，会调用 `ref` 回调函数并传入 DOM 元素，当卸载时调用它并传入 `null`。在 `componentDidMount` 或 `componentDidUpdate` 触发前，React 会保证 refs 一定是最新的。
- 你可以在组件间传递回调形式的 refs，就像你可以传递通过 `React.createRef()` 创建的对象 refs 一样。

    ```jsx
    function CustomTextInput(props) {  return (    <div>      <input ref={props.inputRef} />    </div>  );}class Parent extends React.Component {  render() {    return (      <CustomTextInput        inputRef={el => this.inputElement = el}      />    );  }}
    ```

- 在上面的例子中，`Parent` 把它的 refs 回调函数当作 `inputRef` props 传递给了 `CustomTextInput`，而且 `CustomTextInput` 把相同的函数作为特殊的 `ref` 属性传递给了 `<input>`。结果是，在 `Parent` 中的 `this.inputElement` 会被设置为与 `CustomTextInput` 中的 `input` 元素相对应的 DOM 节点。
- 如果 `ref` 回调函数是以内联函数的方式定义的，在更新过程中它会被执行两次，第一次传入参数 `null`，然后第二次会传入参数 DOM 元素。这是因为在每次渲染时会创建一个新的函数实例，所以 React 清空旧的 ref 并且设置新的。通过将 ref 的回调函数定义成 class 的绑定函数的方式可以避免上述问题，但是大多数情况下它是无关紧要的。

### 将 DOM Refs 暴露给父组件

- 在极少数情况下，你可能希望在父组件中引用子节点的 DOM 节点。通常不建议这样做，因为它会打破组件的封装，但它偶尔可用于触发焦点或测量子 DOM 节点的大小或位置。
- 虽然你可以 [向子组件添加 ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html#adding-a-ref-to-a-class-component)，但这不是一个理想的解决方案，因为你只能获取组件实例而不是 DOM 节点。并且，它还在函数组件上无效。
- 如果你使用 16.3 或更高版本的 React, 这种情况下我们推荐使用 [ref 转发](https://zh-hans.reactjs.org/docs/forwarding-refs.html)。**Ref 转发使组件可以像暴露自己的 ref 一样暴露子组件的 ref**。关于怎样对父组件暴露子组件的 DOM 节点，在 [ref 转发文档](https://zh-hans.reactjs.org/docs/forwarding-refs.html#forwarding-refs-to-dom-components) 中有一个详细的例子。
- 如果你使用 16.2 或更低版本的 React，或者你需要比 ref 转发更高的灵活性，你可以使用将 ref 作为特殊名字的 prop 直接传递。
- 可能的话，我们不建议暴露 DOM 节点，但有时候它会成为救命稻草。注意这个方案需要你在子组件中增加一些代码。如果你对子组件的实现没有控制权的话，你剩下的选择是使用 [`findDOMNode()`](https://zh-hans.reactjs.org/docs/react-dom.html#finddomnode)，但在 [`严格模式`](https://zh-hans.reactjs.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage) 下已被废弃且不推荐使用。

## Refs 转发

### 概述

Ref 转发是一项将 [ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html) 自动地通过组件传递到其一子组件的技巧。对于大多数应用中的组件来说，这通常不是必需的。但其对某些组件，尤其是可重用的组件库是很有用的。最常见的案例如下所述。

考虑这个渲染原生 DOM 元素 `button` 的 `FancyButton` 组件：

```jsx
function FancyButton(props) {  return (    <button className="FancyButton">      {props.children}    </button>  );}
```

React 组件隐藏其实现细节，包括其渲染结果。其他使用 `FancyButton` 的组件**通常不需要**获取内部的 DOM 元素 `button` 的 [ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html)。这很好，因为这防止组件过度依赖其他组件的 DOM 结构。

虽然这种封装对类似 `FeedStory` 或 `Comment` 这样的应用级组件是理想的，但其对 `FancyButton` 或 `MyTextInput` 这样的高可复用“叶”组件来说可能是不方便的。这些组件倾向于在整个应用中以一种类似常规 DOM `button` 和 `input` 的方式被使用，并且访问其 DOM 节点对管理焦点，选中或动画来说是不可避免的。

**Ref 转发是一个可选特性，其允许某些组件接收 ref，并将其向下传递（换句话说，“转发”它）给子组件。**

### 示例

在下面的示例中，`FancyButton` 使用 `React.forwardRef` 来获取传递给它的 `ref`，然后转发到它渲染的 DOM `button`：

```jsx
// 你可以直接获取 DOM button 的 ref：const ref = React.createRef();<FancyButton ref={ref}>Click me!</FancyButton>;const FancyButton = React.forwardRef((props, ref) => (  <button ref={ref} className="FancyButton">    {props.children}  </button>));
```

这样，**使用** `FancyButton` 的**父组件**可以获取底层 DOM 节点 `button` 的 ref ，并在必要时访问，就像其直接使用 DOM `button` 一样。

**以下是对上述示例发生情况的逐步解释：**

1. 我们通过调用 `React.createRef` 创建了一个 [React ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html) 并将其赋值给 `ref` 变量。
2. 我们通过指定 `ref` 为 JSX 属性，将其向下传递给 `<FancyButton ref={ref}>`。
3. React 传递 `ref` 给 `fowardRef` 内函数 `(props, ref) => ...`，作为其第二个参数。
4. 我们向下转发该 `ref` 参数到 `<button ref={ref}>`，将其指定为 JSX 属性。
5. 当 ref 挂载完成，`ref.current` 将指向 `<button>` DOM 节点。

**注意：**

第二个参数 `ref` 只在使用 `React.forwardRef` 定义组件时存在。常规函数和 class 组件不接收 `ref` 参数，且 props 中也不存在 `ref`。

Ref 转发不仅限于 DOM 组件，你也可以转发 refs 到 class 组件实例中。

### 在高阶组件中转发 Refs

这个技巧对 [高阶组件](https://zh-hans.reactjs.org/docs/higher-order-components.html)（也被称为 HOC）特别有用。让我们从一个输出组件 props 到控制台的 HOC 示例开始：

```jsx
function logProps(WrappedComponent) {  class LogProps extends React.Component {    componentDidUpdate(prevProps) {      console.log('old props:', prevProps);      console.log('new props:', this.props);    }    render() {      return <WrappedComponent {...this.props} />;    }  }  return LogProps;}
```

“logProps” HOC 透传（pass through）所有 `props` 到其包裹的组件，所以渲染结果将是相同的。例如：我们可以使用该 HOC 记录所有传递到 “fancy button” 组件的 props：

```jsx
class FancyButton extends React.Component {  focus() {    // ...  }  // ...}// 我们导出 LogProps，而不是 FancyButton。// 虽然它也会渲染一个 FancyButton。export default logProps(FancyButton);
```

上面的示例有一点需要注意：refs 将不会透传下去。这是因为 `ref` 不是 prop 属性。就像 `key` 一样，其被 React 进行了特殊处理。如果你对 HOC 添加 ref，该 ref 将引用最外层的容器组件，而不是被包裹的组件。

这意味着用于我们 `FancyButton` 组件的 refs 实际上将被挂载到 `LogProps` 组件：

```jsx
import FancyButton from './FancyButton';const ref = React.createRef();// 我们导入的 FancyButton 组件是高阶组件（HOC）LogProps。// 尽管渲染结果将是一样的，// 但我们的 ref 将指向 LogProps 而不是内部的 FancyButton 组件！// 这意味着我们不能调用例如 ref.current.focus() 这样的方法<FancyButton  label="Click Me"  handleClick={handleClick}  ref={ref}/>;
```

幸运的是，我们可以使用 `React.forwardRef` API 明确地将 refs 转发到内部的 `FancyButton` 组件。`React.forwardRef` 接受一个渲染函数，其接收 `props` 和 `ref` 参数并返回一个 React 节点。例如：

```jsx
function logProps(Component) {  class LogProps extends React.Component {    componentDidUpdate(prevProps) {      console.log('old props:', prevProps);      console.log('new props:', this.props);    }    render() {      const {forwardedRef, ...rest} = this.props;      // 将自定义的 prop 属性 “forwardedRef” 定义为 ref      return <Component ref={forwardedRef} {...rest} />;    }  }  // 注意 React.forwardRef 回调的第二个参数 “ref”。  // 我们可以将其作为常规 prop 属性传递给 LogProps，例如 “forwardedRef”  // 然后它就可以被挂载到被 LogPros 包裹的子组件上。  return React.forwardRef((props, ref) => {    return <LogProps {...props} forwardedRef={ref} />;  });}
```

### 在 DevTools 中显示自定义名称

- `React.forwardRef` 接受一个渲染函数。React DevTools 使用该函数来决定为 ref 转发组件显示的内容。
- 例如，以下组件将在 DevTools 中显示为 “*ForwardRef*”：

    ```jsx
    const WrappedComponent = React.forwardRef((props, ref) => {  return <LogProps {...props} forwardedRef={ref} />;});
    ```

- 如果你命名了渲染函数，DevTools 也将包含其名称（例如 “*ForwardRef(myFunction)*”）：

    ```jsx
    const WrappedComponent = React.forwardRef(  function myFunction(props, ref) {    return <LogProps {...props} forwardedRef={ref} />;  });
    ```

- 你甚至可以设置函数的 `displayName` 属性来包含被包裹组件的名称：

    ```jsx
    function logProps(Component) {  class LogProps extends React.Component {    // ...  }  function forwardRef(props, ref) {    return <LogProps {...props} forwardedRef={ref} />;  }  // 在 DevTools 中为该组件提供一个更有用的显示名。  // 例如 “ForwardRef(logProps(MyComponent))”  const name = Component.displayName || Component.name;  forwardRef.displayName = `logProps(${name})`;  return React.forwardRef(forwardRef);}
    ```

### 在父组件获取子组件的 DOM

<https://segmentfault.com/q/1010000007488747>

# 条件渲染

## JSX 条件渲染

- 在 React 中，你可以创建不同的组件来封装各种你需要的行为。然后，依据应用的不同状态，你可以只渲染对应状态下的部分内容。
- React 中的条件渲染和 JavaScript 中的一样，使用 JavaScript 运算符 **if** 或者 **条件运算符** 创建元素来表现当前的状态，然后让 React 根据它们来更新 UI。

### 运算符 if

- 来看一个运算符 if 的例子

    ```jsx
    function Greeting(props) {  const isLoggedIn = props.isLoggedIn;  if (isLoggedIn) {    return <UserGreeting />;  }  return <GuestGreeting />;}ReactDOM.render(  // Try changing to isLoggedIn={true}:  <Greeting isLoggedIn={false} />,  document.getElementById('root'));
    ```

- 这个示例根据 `isLoggedIn` 的值来渲染不同的问候语。

### 元素变量

- 你可以使用变量来储存元素。 它可以帮助你有条件地渲染组件的一部分，而其他的渲染部分并不会因此而改变。

    ```jsx
      render() {    const isLoggedIn = this.state.isLoggedIn;    let button;    if (isLoggedIn) {      button = <LogoutButton onClick={this.handleLogoutClick} />;    } else {      button = <LoginButton onClick={this.handleLoginClick} />;    }    return (      <div>        <Greeting isLoggedIn={isLoggedIn} />        {button}      </div>    );  }
    ```

- 声明一个变量并使用 `if` 语句进行条件渲染是不错的方式，但有时你可能会想使用更为简洁的语法。接下来，我们将介绍几种在 JSX 中内联条件渲染的方法。

### 与运算符 &&

- 通过花括号包裹代码，你可以 [在 JSX 中嵌入任何表达式](https://zh-hans.reactjs.org/docs/introducing-jsx.html#embedding-expressions-in-jsx)。这也包括 JavaScript 中的逻辑与 (&&) 运算符。它可以很方便地进行元素的条件渲染。

    ```jsx
    function Mailbox(props) {  const unreadMessages = props.unreadMessages;  return (    <div>      <h1>Hello!</h1>          {unreadMessages.length > 0 &&        <h2>          You have {unreadMessages.length} unread messages.        </h2>      }    </div>  );}
    ```

- 如果条件是 `true`，`&&` 右侧的元素就会被渲染，如果是 `false`，React 会忽略并跳过它。

### 三目运算符

- 另一种内联条件渲染的方法是使用 JavaScript 中的三目运算符 [`condition ? true : false`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)。

**示例**

- ```jsx
    render() {  const isLoggedIn = this.state.isLoggedIn;  return (    <div>      The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.    </div>  );}
    ```

### 阻止组件渲染

- 在极少数情况下，你可能希望能隐藏组件，即使它已经被其他组件渲染。若要完成此操作，你可以让 `render` 方法直接返回 `null`，而不进行任何渲染。

**示例**

- 下面的示例中，`<WarningBanner />` 会根据 prop 中 `warn` 的值来进行条件渲染。如果 `warn` 的值是 `false`，那么组件则不会渲染:

    ```jsx
    function WarningBanner(props) {  if (!props.warn) {    return null;  }  return (    <div className="warning">      Warning!    </div>  );}
    ```

- 在组件的 `render` 方法中返回 `null` 并不会影响组件的生命周期。`componentDidUpdate` 依然会被调用。

## React 条件渲染

- 条件渲染总结：<https://juejin.im/post/5ab0bff06fb9a028d444696b#heading-0>
- 模态框:<https://zhuanlan.zhihu.com/p/30271961>

# 列表&key

## 渲染多个组件

- 你可以通过使用 `{}` 在 JSX 内构建一个 [元素集合](https://zh-hans.reactjs.org/docs/introducing-jsx.html#embedding-expressions-in-jsx)。

**示例**

- 下面，我们使用 Javascript 中的 [`map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 方法来遍历 `numbers` 数组。将数组中的每个元素变成 `<li>` 标签，最后我们将得到的数组赋值给 `listItems`：

    ```jsx
    const numbers = [1, 2, 3, 4, 5];const listItems = numbers.map((number) =>  <li>{number}</li>);
    ```

## Key 基本

- 当我们运行上面这段代码，将会看到一个警告 `a key should be provided for list items`，意思是当你创建一个元素时，必须包括一个特殊的 `key` 属性。
- key 帮助 React 识别哪些元素改变了，比如被添加或删除。因此你应当给数组中的每一个元素赋予一个**确定的**标识。

    ```jsx
    const numbers = [1, 2, 3, 4, 5];const listItems = numbers.map((number) =>  <li key={number.toString()}>    {number}  </li>);
    ```

- 一个元素的 key 最好是这个元素在列表中拥有的一个独一无二的**字符串**。通常，我们使用来自数据 id 来作为元素的 key：

    ```jsx
    const todoItems = todos.map((todo) =>  <li key={todo.id}>    {todo.text}  </li>);
    ```

- 当元素没有确定 id 的时候，万不得已你可以使用元素索引 index 作为 key：

    ```jsx
    const todoItems = todos.map((todo, index) =>  // Only do this if items have no stable IDs  <li key={index}>    {todo.text}  </li>);
    ```

- 如果列表项目的顺序可能会变化，我们不建议使用索引来用作 key 值，因为这样做会导致性能变差，还可能引起组件状态的问题。可以看看 Robin Pokorny 的 [深度解析使用索引作为 key 的负面影响](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318) 这一篇文章。
- 如果你选择不指定显式的 key 值，那么 React 将默认使用索引用作为列表项目的 key 值。

### Key 只有放在就近的数组上下文中才有意义

- 元素的 key 只有放在就近的数组上下文中才有意义。

**示例**

- 比方说，如果你 [提取](https://zh-hans.reactjs.org/docs/components-and-props.html#extracting-components) 出一个 `ListItem` 组件，你应该把 key 保留在数组中的这个 `<ListItem />` 元素上，而不是放在 `ListItem` 组件中的 `<li>` 元素上。

    ```jsx
    function ListItem(props) {  const value = props.value;  return (    // 错误！你不需要在这里指定 key：    <li key={value.toString()}>      {value}    </li>  );}function NumberList(props) {  const numbers = props.numbers;  const listItems = numbers.map((number) =>    // 正确！key 应该在数组的上下文中被指定    <ListItem key={number.toString()}              value={number} />  );  return (    <ul>      {listItems}    </ul>  );}
    ```

- 一个好的经验法则是：在 `map()` 方法中的元素需要设置 key 属性。

### Key 只是在兄弟节点之间必须唯一

- 数组元素中使用的 key 在其兄弟节点之间应该是独一无二的。然而，它们不需要是全局唯一的。当我们生成两个不同的数组时，我们可以使用相同的 key 值

### Key 不会传递给的组件

- key 会传递信息给 React ，但不会传递给你的组件。如果你的组件中需要使用 `key` 属性的值，请用其他属性名显式传递这个值：

    ```jsx
    const content = posts.map((post) =>  <Post    key={post.id}    id={post.id}    title={post.title} />);
    ```

- 上面例子中，`Post` 组件可以读出 `props.id`，但是不能读出 `props.key`。

# 表单

## 报错

- 受控表单，如果 value 绑定的 state 没有初始值，那么 value 的值即是 undefined，react 会认为这个组件是非受控组件，一旦此时修改了 state 的值，相当于就是把非受控组件转换成了受控组件，react 会报错
- 参考：<https://stackoverflow.com/questions/47012169/a-component-is-changing-an-uncontrolled-input-of-type-text-to-be-controlled-erro?rq=1>

## 默认值

- 在 React 渲染生命周期时，表单元素上的 `value` 将会覆盖 DOM 节点中的值，在非受控组件中，你经常希望 React 能赋予组件一个初始值，但是不去控制后续的更新。 在这种情况下, 你可以指定一个 `defaultValue` 属性，而不是 `value`。

    ```jsx
    render() {  return (    <form onSubmit={this.handleSubmit}>      <label>        Name:        <input          defaultValue="Bob"          type="text"          ref={this.input} />      </label>      <input type="submit" value="Submit" />    </form>  );}
    ```

- 同样，`<input type="checkbox">` 和 `<input type="radio">` 支持 `defaultChecked`，`<select>` 和 `<textarea>` 支持 `defaultValue`。
- 对于受控组件，请在 state 初始化的时候赋予默认值，但是无论是 value 初始化还是 defaultValue 都没有 placeholder 的效果，而且同时设置的时候也会覆盖 placehloder 的效果
- 解决：初始化 state 为空串，显式 null、undefined 也会报错

# 受控组件

- 在 HTML 中，表单元素（如 `<input>`、 `<textarea>` 和 `<select>`）之类的表单元素通常自己维护 state，并根据用户输入进行更新。而在 React 中，可变状态（mutable state）通常保存在组件的 state 属性中，并且只能通过使用 [`setState()`](https://zh-hans.reactjs.org/docs/react-component.html#setstate) 来更新。
- 我们可以把两者结合起来，使 React 的 state 成为“**唯一数据源**”。渲染表单的 React 组件还控制着用户输入过程中表单发生的操作。被 React 以这种方式控制取值的表单输入元素就叫做“受控组件”。

## Input 标签

- 例如，如果我们想让前一个示例在提交时打印出名称，我们可以将表单写为受控组件：

    ```jsx
    <input type="text" value={this.state.value} onChange={this.handleChange} />
    ```

- 由于在表单元素上设置了 `value` 属性，因此显示的值将始终为 `this.state.value`，这使得 React 的 state 成为唯一数据源。由于 `handlechange` 在每次按键时都会执行并更新 React 的 state，因此显示的值将随着用户输入而更新。
- 对于受控组件来说，每个 state 突变都有一个相关的处理函数。这使得修改或验证用户输入变得简单。例如，如果我们要强制要求所有名称都用大写字母书写，我们可以将 `handlechange` 改写为：

    ```js
    handleChange(event) {  this.setState({value: event.target.value.toUpperCase()});}
    ```

## Textarea 标签

- 在 HTML 中, `<textarea>` 元素通过其子元素定义其文本:

    ```jsx
    <textarea>  你好， 这是在 text area 里的文本</textarea>
    ```

- 而在 React 中，`<textarea>` 使用 `value` 属性代替。这样，可以使得使用 `<textarea>` 的表单和使用单行 input 的表单非常类似：

    ```jsx
    <textarea value={this.state.value} onChange={this.handleChange} />
    ```

- 请注意，`this.state.value` 初始化于构造函数中，因此文本区域默认有初值。

## Select 标签

- 在 HTML 中，`<select>` 创建下拉列表标签。例如，如下 HTML 创建了水果相关的下拉列表：

    ```jsx
    <select>  <option value="grapefruit">葡萄柚</option>  <option value="lime">柠檬</option>  <option selected value="coconut">椰子</option>  <option value="mango">芒果</option></select>
    ```

- 请注意，由于 `selected` 属性的缘故，椰子选项默认被选中。React 并不会使用 `selected` 属性，而是在根 `select` 标签上使用 `value` 属性。这在受控组件中更便捷，因为您只需要在根标签中更新它。例如：

    ```jsx
    <select value={this.state.value} onChange={this.handleChange}>  <option value="grapefruit">葡萄柚</option>  <option value="lime">柠檬</option>  <option value="coconut">椰子</option>  <option value="mango">芒果</option></select>
    ```

- **注意：**你可以将数组传递到 `value` 属性中，以支持在 `select` 标签中选择多个选项：

    ```jsx
    <select multiple={true} value={['B', 'C']}>
    ```

- **总的来说：**这使得 `<input type="text">`, `<textarea>` 和 `<select>` 之类的标签都非常相似—它们都接受一个 `value` 属性，你可以使用它来实现受控组件。

## 文件 Input 标签

- 在 HTML 中，`<input type=“file”>` 允许用户从存储设备中选择一个或多个文件，将其上传到服务器，或通过使用 JavaScript 的 [File API](https://developer.mozilla.org/en-US/docs/Web/API/File/Using_files_from_web_applications) 进行控制。

    ```jsx
    <input type="file" />
    ```

- 因为它的 value 只读，所以它是 React 中的一个**非受控**组件。将与其他非受控组件 [在后续文档中](https://zh-hans.reactjs.org/docs/uncontrolled-components.html#the-file-input-tag) 一起讨论。

## 处理多个输入

- 当需要处理多个 `input` 元素时，我们可以给每个元素添加 `name` 属性，并让处理函数根据 `event.target.name` 的值选择要执行的操作。

    ```js
    handleInputChange(event) {  const target = event.target;  const value = target.type === 'checkbox' ? target.checked : target.value;  const name = target.name;  this.setState({    [name]: value  });}
    ```

## 受控输入空值

- 在 [受控组件](https://zh-hans.reactjs.org/docs/forms.html#controlled-components) 上指定 value 的 prop 可以防止用户更改输入。如果指定了 `value`，但输入仍可编辑，则可能是**意外地**将 `value` 设置为 `undefined` 或 `null`。
- 下面的代码演示了这一点。（输入最初被锁定，但在短时间延迟后变为可编辑。）

    ```jsx
    ReactDOM.render(<input value="hi" />, mountNode);setTimeout(function() {  ReactDOM.render(<input value={null} />, mountNode);}, 1000);
    ```

## 不定个数的组件

- 通过 map 生成的子组件有着多个 input，要怎么样才能把所有的 input 都受控，然后把 props 传递给父组件进行 reduce？
- 如果是 class 语法，可以通过属性表达式，控制不同得变量名，然后 setState 也是一样，可以统一操作
- 如果 hooks 语法呢？每个 state 都有一个钩子，要怎么动态得搜集数据呢？只能在父组件写好然后传给子组件展示了

## Formik

- 如果你想寻找包含验证、追踪访问字段以及处理表单提交的完整解决方案，使用 [Formik](https://jaredpalmer.com/formik) 是不错的选择。然而，它也是建立在受控组件和管理 state 的基础之上——所以不要忽视学习它们。

# 非受控组件

- 在大多数情况下，我们推荐使用 [受控组件](https://zh-hans.reactjs.org/docs/forms.html) 来处理表单数据。在一个受控组件中，表单数据是由 React 组件来管理的。另一种替代方案是使用非受控组件，这时表单数据将交由 DOM 节点来处理。
- 要编写一个非受控组件，而不是为每个状态更新都编写数据处理函数，你可以 [使用 ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html) 来从 DOM 节点中获取表单数据。
- 例如，下面的代码使用非受控组件接受一个表单的值：

    ```jsx
    class NameForm extends React.Component {  constructor(props) {    super(props);    this.handleSubmit = this.handleSubmit.bind(this);    this.input = React.createRef();  }  handleSubmit(event) {    alert('A name was submitted: ' + this.input.current.value);    event.preventDefault();  }  render() {    return (      <form onSubmit={this.handleSubmit}>        <label>          Name:          <input type="text" ref={this.input} />        </label>        <input type="submit" value="Submit" />      </form>    );  }}
    ```

- 因为非受控组件将真实数据储存在 DOM 节点中，所以再使用非受控组件时，有时候反而更容易同时集成 React 和非 React 代码。如果你不介意代码美观性，并且希望快速编写代码，使用非受控组件往往可以减少你的代码量。否则，你应该使用受控组件。
- 如果你还是不清楚在某个特殊场景中应该使用哪种组件：<https://goshakkk.name/controlled-vs-uncontrolled-inputs-react/>

## 文件输入

- 在 HTML 中，`<input type="file">` 可以让用户选择一个或多个文件上传到服务器，或者通过使用 [File API](https://developer.mozilla.org/en-US/docs/Web/API/File/Using_files_from_web_applications) 进行操作。

    ```jsx
    <input type="file">
    ```

- 在 React 中，`<input type="file">` 始终是一个非受控组件，因为它的值只能由用户设置，而不能通过代码控制。
- 您应该使用 File API 与文件进行交互。下面的例子显示了如何创建一个 [DOM 节点的 ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html) 从而在提交表单时获取文件的信息。

# 组件间通信

## 状态提升

- 通常，多个组件需要反映相同的变化数据，这时我们建议将共享状态提升到最近的共同父组件中去。让我们看看它是如何运作的。

### 通过 Props 传递

- 共同的数据放在父组件上, 特有的数据放在自己组件内部 (state)
- 通过 props 可以传递一般数据和函数数据, 只能一层一层传递
  - 一般数据 -->**父组件**传递数据给**子组件**-->子组件读取数据
  - 函数数据 -->**子组件**传递数据给**父组件**-->子组件调用函数

## 使用消息订阅 (subscribe)- 发布 (publish) 机制

- 工具库: PubSubJS
- 下载: npm install pubsub-js --save

**使用:**

1. import PubSub from 'pubsub-js' //引入

        2.  PubSub.subscribe('delete', function(data){ }); //订阅   3.  PubSub.publish('delete', data) //发布消息

### 自定义事件 (消息机制)

- 订阅消息：绑定事件监听
  - 事件名 (类型): 任意
  - 回调函数: 通过**形参接收数据**, 在函数体**处理事件**
  - ```react
        //search.jsxsearch=()=>{  //发布消息  PubSub.publish("search",data)}//main.jsxcomponnetDidMount(){  //订阅消息（收到数据之后干啥）  PubSub.subscribe("search",(msg,data)=>{ //用箭头函数，兼容this    console.log(msg,data)  })}
        ```


- 发布消息：触发事件 (编码)
  - 发布了什么事件（事件名），内容是什么（数据）
  - 事件名 (类型): 与绑定的事件监听的事件名一致
  - 数据: 会自动传递给回调函数

## Redux

# 组合 Vs 继承

## 包含关系

### props.children

- 有些组件无法提前知晓它们子组件的具体内容。在 `Sidebar`（侧边栏）和 `Dialog`（对话框）等展现通用容器（box）的组件中特别容易遇到这种情况。
- 我们建议这些组件使用一个特殊的 `children` prop 来将他们的子组件传递到渲染结果中：

    ```jsx
    function FancyBorder(props) {  return (    <div className={'FancyBorder FancyBorder-' + props.color}>      {props.children}    </div>  );}
    ```

- 这使得别的组件可以通过 JSX 嵌套，将任意组件作为子组件传递给它们。

    ```jsx
    function WelcomeDialog() {  return (    <FancyBorder color="blue">      <h1 className="Dialog-title">        Welcome      </h1>      <p className="Dialog-message">        Thank you for visiting our spacecraft!      </p>    </FancyBorder>  );}
    ```

- `<FancyBorder>` JSX 标签中的所有内容都会作为一个 `children` prop 传递给 `FancyBorder` 组件。因为 `FancyBorder` 将 `{props.children}` 渲染在一个 `<div>` 中，被传递的这些子组件最终都会出现在输出结果中。

### 类插槽

- 少数情况下，你可能需要在一个组件中预留出几个“洞”。这种情况下，我们可以不使用 `children`，而是自行约定：将所需内容传入 props，并使用相应的 prop。

    ```jsx
    function SplitPane(props) {  return (    <div className="SplitPane">      <div className="SplitPane-left">        {props.left}      </div>      <div className="SplitPane-right">        {props.right}      </div>    </div>  );}function App() {  return (    <SplitPane      left={        <Contacts />      }      right={        <Chat />      } />  );}
    ```

- `<Contacts />` 和 `<Chat />` 之类的 React 元素本质就是对象（object），所以你可以把它们当作 props，像其他数据一样传递。这种方法可能使你想起别的库中“槽”（slot）的概念，但在 React 中没有“槽”这一概念的限制，你可以将任何东西作为 props 进行传递。

## 特例关系

- 有些时候，我们会把一些组件看作是其他组件的特殊实例，比如 `WelcomeDialog` 可以说是 `Dialog` 的特殊实例。
- 在 React 中，我们也可以通过组合来实现这一点。“特殊”组件可以通过 props 定制并渲染“一般”组件：

    ```jsx
    function Dialog(props) {  return (    <FancyBorder color="blue">      <h1 className="Dialog-title">        {props.title}      </h1>      <p className="Dialog-message">        {props.message}      </p>    </FancyBorder>  );}function WelcomeDialog() {  return (    <Dialog      title="Welcome"      message="Thank you for visiting our spacecraft!" />  );}
    ```

## 那么继承呢？

- 在 Facebook，我们在成百上千个组件中使用 React。我们并没有发现需要使用继承来构建组件层次的情况。
- Props 和组合为你提供了清晰而安全地定制组件外观和行为的灵活方式。注意：组件可以接受任意 props，包括基本数据类型，React 元素以及函数。
- 如果你想要在组件间复用非 UI 的功能，我们建议将其提取为一个单独的 JavaScript 模块，如函数、对象或者类。组件可以直接引入（import）而无需通过 extend 继承它们。

# 编程习惯

- 为了便于阅读，我们会将 JSX 拆分为多行。同时，我们建议将内容包裹在括号中，虽然这样做不是强制要求的，但是这可以避免遇到 [自动插入分号](http://stackoverflow.com/q/2846283) 陷阱。
- 我们建议从组件自身的角度命名 props，而不是依赖于调用组件的上下文命名。

    > `Avatar` 不需知道它在 `Comment` 组件内部是如何渲染的。因此，我们给它的 props 起了一个更通用的名字：`user`，而不是 `author`。

- react 组件本身类似于 js 的函数，props 就是我们传入的参数，组件本身严格遵守纯函数的概念，不改变传入的参数，在相同的参数下，总是输出相同的结果，但是我们又需要组件是动态的，于是就有了 state**
- 当 state 是一个**数组**的时候

    ```js
    setState({ 'arrary': [...this.state.array, newItem]})
    ```

- state 更新过后，必须重新获取 this.state 才可以拿到新值，再函数初始时获得的时不会变的。为了防止无法及时获取到新的 state，应该保留 state 的值，最后再一起 setState
- 通过标签属性从组件外向组件内传递变化的数据
- 注意: 组件内部**不要修改 props 数据**
- : 将对象的所有属性通过 props 传递
- `<Person {...person}/>`
- 虚拟 DOM 标签中，html 的静态属性写在开头，动态属性写在后面
