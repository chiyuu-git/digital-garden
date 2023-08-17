---
{"dg-publish":true,"permalink":"/programming/font-end/framework/react/jsx/"}
---


# JSX 标签

## JSX 标签名

JSX 标签的第一部分指定了 React 元素的类型。

大写字母开头的 JSX 标签意味着它们是 React 组件。这些标签会被编译为对命名变量的直接引用，所以，当你使用 JSX `<Foo />` 表达式时，`Foo` 必须包含在作用域内。

当元素类型以小写字母开头时，它表示一个内置的组件，如 `<div>` 或 `<span>`，将导致字符串 `'div'` 或 `'span'` 传递给 `React.createElement`。

以大写字母开头的类型，如 `<Foo />` 编译为 `React.createElement(Foo)`，并且它正对应于你在 JavaScript 文件中定义或导入的组件。

> 我们建议使用大写字母开头命名自定义组件。如果你确实需要一个以小写字母开头的组件，则在 JSX 中使用它之前，必须将它赋值给一个大写字母开头的变量。

## 动态标签名

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
import React from "react";
import { PhotoStory, VideoStory } from "./stories";

const components = {
  photo: PhotoStory,
  video: VideoStory,
};

function Story(props) {
  // 正确！JSX 类型可以是大写字母开头的变量。
  const SpecificStory = components[props.storyType];
  return <SpecificStory story={props.story} />;
}
```

## JSX 标签名 点语法

在 JSX 中，你也可以使用点语法来引用一个 React 组件。当你在一个模块中导出许多 React 组件时，这会非常方便。例如，如果 `MyComponents.DatePicker` 是一个组件，你可以在 JSX 中直接使用：

```jsx
import React from "react";

const MyComponents = {
  DatePicker: function DatePicker(props) {
    return <div>Imagine a {props.color} datepicker here.</div>;
  },
};

function BlueDatePicker() {
  return <MyComponents.DatePicker color="blue" />;
}
```

## JSX 标签 属性绑定

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
<div style={{ color: "red", fontWeight: "bold" }} />
```

如果你没有给属性传值，它默认为 `true`。因此下面两个 JSX 是等价的：

```jsx
<MyTextBox autocomplete /><MyTextBox autocomplete={true} />
```

## 无法使用的 Js 语法

`if` 语句以及 `for` 循环不是 JavaScript 表达式，所以不能在 JSX 中直接使用。但是，你可以用在 JSX 以外的代码中。比如：

```jsx
function NumberDescriber(props) {
  let description;
  if (props.number % 2 == 0) {
    description = <strong>even</strong>;
  } else {
    description = <i>odd</i>;
  }
  return (
    <div>
      {props.number} is an {description} number
    </div>
  );
}
```

**直观来看，JSX 指的是标签的部分，包括标签名、标签体、标签的属性**

# JSX 标签体 @@@

在既包含开始标签又包含结束标签的 JSX 表达式中，这两个标签之间的内容被传递为专门的属性：`props.children`。有几种不同的方法来传递子代：

## 字符串字面量

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

## 布尔值、Null 和 Undefined 被忽略

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
  {props.messages.length > 0 && <MessageList messages={props.messages} />}
</div>
```

相反，如果你想让类似 `false`、`true`、`null` 或 `undefined` 出现在输出中，你必须先把它 [转换成字符串](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String#String_conversion) :

```react
<div>
  My JavaScript variable is {String(myVariable)}.
</div>
```

## JSX 标签嵌套

```react
<MyContainer>
  <MyFirstComponent />
  <MySecondComponent />
</MyContainer>
```

## JavaScript 表达式

```react
<MyContainer>
 {todos.map((message) => <Item key={message} message={message} />)}
</MyContainer>
```

map 函数的返回值是一个数组，数组的每个元素都是 JSX 标签，react 会自动把数组的每个元素都渲染出来

```jsx
<div> {[1, 2, 3, 4, 5]}</div>
```

渲染成：“12345”

## props.children 函数

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

# JSX 本质

Babel 会把 JSX 转译成一个名为 `React.createElement()` 函数调用。

以下两种示例代码完全等效：

```jsx
const element = <h1 className="greeting">Hello, world!</h1>;
```

```js
const element = React.createElement(
  "h1",
  { className: "greeting" },
  "Hello, world!"
);
```

`React.createElement()` 会预先执行一些检查，以帮助你编写无错代码，但实际上它创建了一个这样的对象：

```js
// 注意：这是简化过的结构
const element = {
  type: "h1",
  props: {
    className: "greeting",
    children: "Hello, world!",
  },
};
```

这些对象被称为 “React 元素”。它们描述了你希望在屏幕上看到的内容。React 通过读取这些对象，然后使用它们来构建 DOM 以及保持随时更新。

如果没有子节点，你还可以使用自闭合的标签形式，如：

```jsx
<div className="sidebar" />
```

会编译为:

```jsx
React.createElement("div", { className: "sidebar" }, null);
```

## JSX 标签是一个表达式

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

## React 必须在作用域内

由于 JSX 会编译为 `React.createElement` 调用形式，所以 `React` 库也必须包含在 JSX 代码作用域内。

在 17 版本这个限制被取消了

如果你不使用 JavaScript 打包工具而是直接通过 `<script>` 标签加载 React，则必须将 `React` 挂载到全局变量中。

# 注意

- JSX 必须要有闭合标签，否则无法识别，不像 html 浏览器会自动加上
- **只能有一个根元素**，因为最终会调用 React.createElement 方法
- 因为 JSX 语法上更接近 JavaScript 而不是 HTML，所以 React DOM 使用 `camelCase`（小驼峰命名）来定义属性的名称，而不使用 HTML 属性名称的命名约定。

  > 例如，JSX 里的 `class` 变成了 `className`，而 `tabindex` 则变为 `tabIndex`。
  >
  > 绑定事件也是驼峰的

- 生成的列表需要添加 key 以作标志，方便 vDOM 的快速比对
- 在一个组件的子元素位置使用注释要用 {} 包起来
- 省略 Boolean 属性值会导致 JSX 认为 bool 值设为了 true。要传 false 时，必须使用属性表达式。这常用于表单元素中，比如 disabled、required、checked 和 readOnly 等。

```jsx
  const data = { name: "foo", value: "bar" };
  const component = <Component {...data} />;
```

```jsx
  <div dangerouslySetInnerHTML={{ __html: "cc &copy; 2015" }} />
```

- 创建的虚拟 DOM 对象，属性和方法比实际 DOM 少得多
![](/img/user/programming/font-end/framework/react/jsx/image-20230816195730156.png)

## JSX 防止注入攻击 @link XSS 安全

你可以安全地在 JSX 当中插入用户输入内容：

```jsx
const title = response.potentiallyMaliciousInput; // 直接使用是安全的：const element = <h1>{title}</h1>;
```

React DOM 在渲染所有输入内容之前，默认会进行 [转义](https://stackoverflow.com/questions/7381974/which-characters-need-to-be-escaped-on-html)。它可以确保在你的应用中，永远不会注入那些并非自己明确编写的内容。所有的内容在渲染之前都被转换成了字符串。这样可以有效地防止 [XSS（cross-site-scripting, 跨站脚本）](https://en.wikipedia.org/wiki/Cross-site_scripting) 攻击。
