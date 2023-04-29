---
{"dg-publish":true,"permalink":"/programming/font-end/framework/san/san-basic/"}
---


# 疑问

为了兼容，是不是只能用 ES5 的语法，甚至是 ES3 的语法？可以引入 babel

模板换行要怎么操作？还是只能引入 babel 然后写 ES6

使用自定义组件的时候，标签名必须是小写的，暂时不清楚原因

https://www.jianshu.com/p/efc955b2b38b

props 如何访问，普通属性可以通过 this.data 访问，为什么方法不行呢？

生命周期的区别，哪个生命周期该做什么

多个动态样式该如何赋值？绑定数组，绑定对象，通过计算属性，

```html
class='{{date.type}}{{date.badge}}'
```

# 注意

只有文本区域和属性插值使用双花括号，所以条件、循环、事件处理等在模板内的表达式还是使用双引号

template 元素在渲染时不会包含自身

能快速的设置 data 吗？单独 set 太慢了，但是又不想设置为对象

如果放分 props，或者说组件间如何简单的通信

/我要给组件传一个函数让他执行，要怎么整，还是组件通信的内容

# 基本认识

## 概述

官方文档：https://baidu.github.io/san/

San 读作三

官方文章：

> https://efe.baidu.com/blog/san-a-traditional-mvvm-component-framework/
>
> https://efe.baidu.com/blog/san-perf/

- 还在兼容 Old IE
- 还在考虑不使用 babel transform 的业务开发场景 ？？？
- 还在为体积纠结
- 还想保持后端无关，而不是推 NodeJS

### 基本特点

San，是一个 MVVM 的组件框架。它体积小巧（< 15K），兼容性好（IE6），性能卓越，是一个可靠、可依赖的实现响应式用户界面的解决方案。

San 通过声明式的类 HTML 视图模板，在支持所有原生 HTML 的语法特性外，还支持了数据到视图的绑定指令、业务开发中最常使用的分支、循环指令等，在保持良好的易用性基础上，由框架完成基于字符串的模板解析，并构建出视图层的 [节点关系树](https://github.com/baidu/san/blob/master/doc/anode.md)，通过高性能的视图引擎快速生成 UI 视图。San 中定义的数据会被封装，使得当数据发生有效变更时通知 San 组件，San 组件依赖模板编译阶段生成的 [节点关系树](https://github.com/baidu/san/blob/master/doc/anode.md)，确定需要变更的最小视图，进而完成视图的异步更新，保证了视图更新的高效性。

- HTML 模板
- 支持指令
- 没有 vdom

组件是 San 的基本单位，是独立的数据、逻辑、视图的封装单元。从页面角度看，组件是 HTML 元素的扩展；从功能模式角度看，组件是一个 ViewModel。San 组件提供了完整的生命周期，与 WebComponent 的生命周期相符合，组件间是可嵌套的树形关系，完整的支持了组件层级、组件间的通信，方便组件间的数据流转。San 的组件机制，可以有效支撑业务开发上的组件化需求。

- 组件化，ViewModel
- 组件生命周期与 WebComponent 生命周期相符

San 支持 [组件反解](https://baidu.github.io/san/tutorial/reverse/)，以此提供 [服务端渲染](https://baidu.github.io/san/tutorial/ssr/) 能力，可以解决纯前端渲染导致的响应用户交互时延长、SEO 问题。除此之外，San 还提供了一些周边开源产品，与 San 配合使用，可以帮助开发者快速搭建可维护的大型 SPA 应用。

## Hello

```js
var MyApp = san.defineComponent({
  template: "<p>Hello {{name}}!</p>",

  initData: function () {
    return {
      name: "San",
    };
  },
});

var myApp = new MyApp();
myApp.attach(document.body);
```

可以看到，通常情况使用 San 会经过这么几步：

1. 我们先定义了一个 San 的组件，在定义时指定了组件的 **内容模板** 与 **初始数据** 。
2. 初始化组件对象
3. 让组件在相应的地方渲染

额外提示：在 JavaScript 中书写 HTML 片段对维护来说是不友好的，我们可以通过 WebPack、AMD plugin、异步请求等方式管理。这里为了例子的简单就写在一起了。

# 模板语法

San 使用基于 HTML 的模板，允许你以声明的方式将渲染的 DOM 和 San 实例的数据绑定。

## 插值/数据绑定

### 模板插值

和许多模板引擎一样，插值的语法形式是表达式位于双大括号中，表达式后可以接任意多个过滤器。

```html
{{ expr [[| filter-call1] | filter-call2...] }}
```

在**文本内容区域**我们可以使用插值替换。

```html
<p>Hello {{name}}!</p>
```

在 HTML 标签的 **属性 (attribute)** 内，我们也可以使用插值替换。

```html
<span title="This is {{name}}">{{name}}</span>
```

**注意**：插值将默认进行 HTML 转义。如果不需要 HTML 转义请参考本篇后续的 [输出 HTML](https://baidu.github.io/san/tutorial/template/#输出HTML) 章节。

### 属性绑定

顾名思义，属性绑定的意思是，将数据绑定到子组件的 属性 (property) 上。属性绑定的形式和插值绑定相同，\通过 HTML 标签的 属性 (attribute) 声明，通常情况只声明一个 。

下面的例子中，当 jokeName 数据变化时，会自动将新的值设置到 label 组件的 text 属性中。

```html
<ui-label text="{{jokeName}}"></ui-label>
```

### 属性整体绑定

**版本**：>= 3.5.8

通过 **s-bind** ，可以为组件绑定整个数据。当**s-bind** 和单个的属性绑定并存时，单个的属性绑定将覆盖整体绑定中相应的数据项。

```html
<ui-label s-bind="{{ {text: email, title: name} }}"></ui-label>

<!-- text 单个属性声明将覆盖 s-bind 中的 text 项 -->
<ui-label s-bind="{{ {text: email, title: name} }}" text="{{name}}"></ui-label>
```

**提示**：对象字面量常用于属性整体绑定。

### 双向绑定

通过 HTML 标签的 属性 (attribute) 声明 **{= expression =}**，可以进行双向绑定。

双向绑定通常出现在 **用户输入** 的场景，将用户输入结果自动更新到组件数据。所以我们通常在 **输入表单元素** 或 **具有输入反馈的自定义组件** 上应用双向绑定。

**注意**：双向绑定仅支持普通变量和属性访问表达式，否则可能导致不可预测的问题。

**示例**

在这个例子中，通过 **{= expression =}** 声明双向绑定，把输入框的 value 与数据项 name 绑定起来。

```js
var MyApp = san.defineComponent({
  template:
    "" +
    "<div>" +
    '<input value="{= name =}" placeholder="please input">' +
    "Hello {{name}}!" +
    "</div>",
});

var myApp = new MyApp();
myApp.attach(document.body);
```

下面的例子将 input、select、自定义组件的 value 属性与声明的数据项（name、online、color）建立了双向绑定关系。当用户输入时，相应数据项的值会发生变化。

```html
<input type="text" value="{= name =}" />

<select value="{= online =}">
  <option value="errorrik">errorrik</option>
  <option value="otakustay">otakustay</option>
  <option value="firede">firede</option>
</select>

<ui-colorpicker value="{= color =}"></ui-colorpicker>
```

### 控制属性

只有文本区域和属性插值使用双花括号，所以条件、循环、事件处理等在模板内的表达式还是使用双引号

**注:** 控制属性不需要被双大括号包裹

```html
<!-- condition-demo.swan -->
<view s-if="flag">如果为flag为true，你看得到我。</view>
```

```js
// condition-demo.js
Page({
  data: {
    flag: true,
  },
});
```

### 表达式

San 提供了丰富的表达式类型支持，让使用者在编写视图模板时更方便。

- 数据访问 (普通变量、属性访问)
- 一元否定
- 一元取负 `>= 3.6.6`
- 二元运算
- 二元关系
- 三元条件
- 括号
- 字符串
- 数值
- 布尔
- 数组字面量 `>= 3.5.9`
- 对象字面量 `>= 3.5.9`
- 方法调用 `>= 3.6.11`

下面通过插值替换的例子列举支持的表达式类型。

```html
<!-- 普通变量 -->
<p>{{name}}</p>

<!-- 属性访问 -->
<p>{{person.name}}</p>
<p>{{persons[1]}}</p>

<!-- 一元否定 -->
<p>{{!isOK}}</p>
<p>{{!!isOK}}</p>

<!-- 一元取负 -->
<p>{{-num1 + num2}}</p>

<!-- 二元运算 -->
<p>{{num1 + num2}}</p>
<p>{{num1 - num2}}</p>
<p>{{num1 * num2}}</p>
<p>{{num1 / num2}}</p>
<p>{{num1 + num2 * num3}}</p>

<!-- 二元关系 -->
<p>{{num1 > num2}}</p>
<p>{{num1 !== num2}}</p>

<!-- 三元条件 -->
<p>{{num1 > num2 ? num1 : num2}}</p>

<!-- 括号 -->
<p>{{a * (b + c)}}</p>

<!-- 数值 -->
<p>{{num1 + 200}}</p>

<!-- 字符串 + 三元条件 -->
<p>{{item ? ',' + item : ''}}</p>

<!-- 数组字面量 -->
<x-list data="{{ persons || [] }}" />

<!-- 对象字面量 -->
<x-article data="{{ {title: articleTitle, author: user} }}" />

<!-- 方法调用 -->
<p>{{max(num1, num2)}}</p>
```

**注意：**对象字面量支持了在模板里重组对象以及使用扩展运算符 ... 来展开对象。 @@@

## 方法调用

\>= 3.6.11

在模板中可以直接调用组件中声明的方法。

```js
var MyApp = san.defineComponent({
  template: "<u>{{sum(num1, num2)}}</u>",

  sum: function (a, b) {
    return a + b;
  },

  initData: function () {
    return {
      num1: 1,
      num2: 2,
    };
  },
});
```

**优点**：相比 [过滤器](https://baidu.github.io/san/tutorial/template/#过滤器)，方法调用有时会显得更直观一些。比如 sum 的场景，通过 [过滤器](https://baidu.github.io/san/tutorial/template/#过滤器) 实现就会显得有些别扭。

**缺点**：[过滤器](https://baidu.github.io/san/tutorial/template/#过滤器) 让数据针对视图的处理和变换方法都放在 filters 中，组件上的方法可以专注于视图无关的逻辑行为。如果使用方法调用则破坏了这一点。

```js
// 通过 filter 实现 sum 显得有些别扭
var MyApp = san.defineComponent({
  template: "<u>{{num1 | sum(num2)}}</u>",

  filters: {
    sum: function (a, b) {
      return a + b;
    },
  },

  initData: function () {
    return {
      num1: 1,
      num2: 2,
    };
  },
});
```

### 动态调用

方法调用可以动态选择方法，不过第一级必须是静态的。

```js
var MyApp = san.defineComponent({
  template: '<u>{{ops[opType ? "sum" : "average"](num1, num2)}}</u>',

  ops: {
    sum: function (a, b) {
      return a + b;
    },

    average: function (a, b) {
      return (a + b) / 2;
    },
  },

  initData: function () {
    return {
      opType: 1,
      num1: 1,
      num2: 2,
    };
  },
});
```

### this@@@

调用的方法中，可以通过 this 继续调用组件上的其他方法。

```js
var MyApp = san.defineComponent({
  template: "<u>{{enhance(num)}}</u>",

  enhance: function (a) {
    return this.square(a);
  },

  square: function (a) {
    return a * a;
  },

  initData: function () {
    return {
      num: 2,
    };
  },
});
```

**警告**：使用方法调用，方法在运行时通过 this.data 可以触及组件的所有数据。但是，千万不要这么干，否则就会造成对数据的隐式依赖，导致数据变化时，视图不会随着更新。下面是错误和正确的示例。

```js
var Bad = san.defineComponent({
  template: "<u>{{enhance(num)}}</u>",

  enhance: function (num) {
    return num * this.data.get("times");
  },

  initData: function () {
    return {
      num: 2,
      times: 3,
    };
  },
});

var Good = san.defineComponent({
  template: "<u>{{enhance(num, times)}}</u>",

  enhance: function (num, times) {
    return num * times;
  },

  initData: function () {
    return {
      num: 2,
      times: 3,
    };
  },
});
```

**提示**：如果使用方法调用，调用的方法最好是无副作用的 pure function。

## 样式

样式处理是编写视图模板时常见的场景，涉及到的 attribute 有 class 和 style，它们的处理方式和其他元素 attribute 有一些区别。本文专门描述样式处理上常见的场景。

**注意**：San 并没有为 class 和 style 处理提供特殊的绑定语法，他们的处理与其它 attribute 方式一样。

### Class

我们可能会设计一些用于表示状态的 class，这些 class 是否应该被添加到元素上，取决于某些数据的值。一个简单的场景是下拉列表的收起和展开状态切换。

```html
<!-- template -->
<div>
  <button on-click="toggle"></button>
  <ul class="list{{isHidden ? ' list-hidden' : ''}}">
    ...
  </ul>
</div>
<!-- Component -->
<script>
  san.defineComponent({
    toggle: function () {
      var isHidden = this.data.get("isHidden");
      this.data.set("isHidden", !isHidden);
    },
  });
</script>
```

上面的例子中，isHidden 数据为真时，ul 具有 list-hidden 的 class，为假时不具有。

San 在设计时，希望视图模板开发者像写正常的 attribute 一样编写 class 与 style，所以没有提供特殊的绑定语法。通过三元运算符的支持可以处理这样的场景。

下面例子是一个根据状态不同，切换不同 class 的场景。

```html
<ul class="list {{isHidden ? 'list-hidden' : 'list-visible'}}">
  ...
</ul>
```

{{false}} 这种值是如何表现的？

{{__plain && 'plain'}} 会导致无法渲染

但是具体的解析规则应该是 san 的

### Style

对 style 的处理通常没有 class 那么复杂。我们很少会把样式信息写在数据中，但有时我们期望用户能定制一些界面样式，这个时候样式可能来源于数据。

```html
<ul>
  <li
    s-for="item, index in datasource"
    style="background: {{item.color}}"
    class="{{item.id == value ? 'selected' : ''}}"
    on-click="itemClick(index)"
  >
    {{ item.title }}
  </li>
</ul>
```

此时需要警惕的是，数据可能并不存在，导致你设置的 style 并不是一个合法的样式。如果你不能保证数据一定有值，需要把样式名包含在插值中。

```html
<ul>
  <li
    s-for="item, index in datasource"
    style="{{item.color ? 'background:' + item.color : ''}}"
    class="{{item.id == value ? 'selected' : ''}}"
    on-click="itemClick(index)"
  >
    {{ item.title }}
  </li>
</ul>
```

## 原始 HTML

有时候我们希望输出不被转义的真实 HTML，在 San 里有两种方式可以做到。

1. 指令 `s-html`
2. 过滤器 raw。过滤器 raw 是一个虚拟过滤器

```
<p s-html="rawHTML"></p>
<p>{{rawHTML | raw}}</p>
```

## HTML 实体

在我们编写 HTML 时，如果内容中包含 HTML 预留字符或特殊字符，我们需要写字符实体。San 的模板 HTML 是自解析的，由于体积的关系，只支持有限的具名字符实体：

- \&lt;
- \&gt;
- \&nbsp;
- \&quot;
- \&emsp;
- \&ensp;
- \&thinsp;
- \&copy;
- \&reg;
- \&zwnj;
- \&zwj;
- \&amp;

除此之外，我们可以使用 `&#[entity_number];` 或 `&#x[entity_number];` 形式的编号字符实体。

```html
<p>LiLei &#38; HanMeiMei are a couple.</p>
<p>1 + 1 &#60; 3</p>
```

# 模板指令

## 条件

### S-if

**与 Swan 相同**

### s-elif/s-else-if

\> 3.2.3 s-elif

\> 3.5.6 **s-else-if**

**s-else-if** 指令是 **s-elif** 指令的别名，效果相同。

### 虚拟元素

在 san 中，**template 元素在渲染时不会包含自身**，只会渲染其内容。对 template 元素应用 if 指令能够让多个元素同时根据条件渲染视图，可以省掉一层不必要的父元素。

```html
<div>
  <template s-if="num > 10000">
    <h2>biiig</h2>
    <p>{{num}}</p>
  </template>
  <template s-elif="num > 1000">
    <h3>biig</h3>
    <p>{{num}}</p>
  </template>
  <template s-elif="num > 100">
    <h4>big</h4>
    <p>{{num}}</p>
  </template>
  <template s-else>
    <h5>small</h5>
    <p>{{num}}</p>
  </template>
</div>
```

## 循环

**与 Swan 相同**

# 数据的视图变换

我们经常遇到 **视图对数据的展示不是原始值** 的场景，比如日期的格式、上次修改时间根据当前时间显示分钟或天等等。在这种场景下，我们可以使用如下方法进行数据的处理和变换：

- 过滤器
- 方法调用

## 过滤器

过滤器在组件声明时注册，在定义视图模板时，插值是常用的展现数据的方式。在编写插值时，我们常使用 **过滤器** 将数据转换成适合视图展现的形式。

**注意**：过滤器仅在插值替换时支持。

插值替换支持多过滤器处理。过滤器之间以类似管道的方式，**前一个过滤器的输出做为后一个过滤器的输入，向后传递**。

```html
<!-- 普通变量 -->
<p>{{myVariable | html | url}}</p>
```

filter 支持参数传递，参数可以是任何支持的 [表达式](https://baidu.github.io/san/tutorial/template/#表达式)。

```html
<!-- // 假设存在过滤器: comma -->
<p>{{myVariable | comma(3)}}</p>
<p>{{myVariable | comma(commaLength)}}</p>
<p>{{myVariable | comma(commaLength + 1)}}</p>
```

```js
{
  {
    createTime | dateFormat("yyyy-MM-dd");
  }
}
```

### 内置过滤器

San 针对常用场景，内置了几个过滤器：

- `html` - HTML 转义。当不指定过滤器时，默认使用此过滤器
- `url` - URL 转义
- `raw` - 不进行转义。当不想使用 HTML 转义时，使用此过滤器

### 定制过滤器

通过定义组件的 **filters** 成员，可以指定组件的视图模板可以使用哪些过滤器。

```js
san.defineComponent({
  template: '<a>{{createTime | dateFormat("yyyy-MM-dd")}}</a>',

  filters: {
    dateFormat: function (value, format) {
      return moment(value).format(format);
    },
  },
});
```

过滤器函数的第一个参数是表达式对应的数据值，过滤器调用时传入的参数从第二个参数开始。

### 注意

**注意**：考虑到组件的独立性，San 没有提供全局过滤器注册的方法，组件要使用的过滤器必须在自身的 **filters** 中定义。

**警告**：filter 方法在运行时通过 this.data 可以触及组件的数据。但是，这么干会造成对数据的隐式依赖，导致数据变化时，视图不会随着更新。所以，filter 方法应该是无副作用的 pure function。

```js
var Bad = san.defineComponent({
  template: "<u>{{num | enhance}}</u>",

  filters: {
    enhance: function (n) {
      return n * this.data.get("times");
    },
  },

  initData: function () {
    return {
      num: 2,
      times: 3,
    };
  },
});

var Good = san.defineComponent({
  template: "<u>{{num | enhance(times)}}</u>",

  filters: {
    enhance: function (n, times) {
      return n * times;
    },
  },

  initData: function () {
    return {
      num: 2,
      times: 3,
    };
  },
});
```

## 方法调用

# 数据操作

San 在组件的 data 上提供了一些数据操作的方法。通过 get 方法可以获取数据；通过 set、splice 等方法修改数据，相应的视图会被自动刷新。

**说明**：为什么是通过 San 提供的方法操作数据，而不是直接操作数据？因为 defineProperty 并未被国内常用的浏览器广泛支持，并且我们也并不喜欢这种**侵入式**的风格，所以我们选择了折中的方式。因此，只有通过 San 提供的方法修改数据，视图才会自动刷新。

## 基本操作

### 初始化

**initData**

通过定义 initData 方法，可以在定义组件时指定组件初始化时的数据。initData 方法返回组件实例的初始化数据。

```js
san.defineComponent({
  initData: function () {
    return {
      width: 200,
      top: 100,
      left: -1000,
    };
  },
});
```

**构造函数入参**

```js
var MyComponent = san.defineComponent({
  components: {
    "x-men": Men,
  },
  template: `<div>
            <x-men data="{{men}}" s-ref="men">
              <h3>{{n}}</h3>
              <p><b>{{sex}}</b><u>{{email}}</u></p>
            </x-men>
        </div>`,
});
var myComponent = new MyComponent({
  data: {
    men: [
      { name: "errorrik", sex: 1, email: "errorrik@gmail.com" },
      { name: "leeight", sex: 0, email: "leeight@gmail.com" },
      { name: "otakustay", email: "otakustay@gmail.com", sex: 1 },
    ],
  },
});
```

### Get

```
{*} get({string|Object?}expr)
```

get 方法能够让我们从 data 中获取数据。

```js
san.defineComponent({
  initData: function () {
    return {
      width: 200,
      top: 100,
      left: -1000,
    };
  },

  attached: function () {
    this.data.get("width"); // 200
  },
});
```

**property accessor**

**data.get** 方法接受一个表示 property accessor 的字符串，所以上面的例子也可以写成这样：

```js
san.defineComponent({
  attached: function () {
    var param = this.data.get("params[1]");
    this.data.set("list", getList(param));
  },
});
```

**ESNext 解构**

当 expr 参数为空时，将返回整个数据项的对象。提供无参的 **get** 方法，主要是为了 ESNext 的解构，能够在一个表达式中获取多个数据项。

```js
san.defineComponent({
  initData: function () {
    return {
      width: 200,
      top: 100,
      left: -1000,
    };
  },

  attached: function () {
    this.data.get().width; // 200

    // top: 100
    // left: -1000
    let { top, left } = this.data.get();
  },
});
```

**注意**：

- **get** 方法获取的数据不能直接修改，否则可能导致不一致的问题。数据修改请使用本文下面的 **set** 、 **splice** 等方法
- 为什么要 get，直接访问不就可以了吗？ this.data.row.date

  ![image-20200503151224115](/img/user/programming/font-end/framework/san/san-basic/image-20200503151224115.png)

### Set

```
set({string|Object}expr, {*}value, {Object?}option)
```

set 方法是最常用的数据修改方法，作用相当于 JavaScript 中的赋值 (=)。

```js
san.defineComponent({
  attached: function () {
    requestUser().then(this.userReceived.bind(this));
  },

  userReceived: function (data) {
    this.data.set("user", data);
  },

  changeEmail: function (email) {
    this.data.set("user.email", email);
  },
});
```

> 可以直接 set 没有初始化的属性吗？

### Merge

\>= 3.4.0

```
merge({string|Object}expr, {*}value, {Object?}option)
```

merge 方法用于将目标数据对象（target）和传入数据对象（source）的键进行合并，作用类似于 JavaScript 中的 `Object.assign(target, source)`。

```js
san.defineComponent({
  attached: function () {
    requestUser().then(this.updateUserInfo.bind(this));
  },

  updateUserInfo: function (data) {
    this.data.merge("user", data);
  },
});
```

### Apply

\>= 3.4.0

```
apply({string|Object}expr, {function({*}):{*}}value, {Object?}option)\
```

apply 方法接受一个函数作为参数，传入当前的值到函数，然后用新返回的值更新它。其行为类似 `Array.prototype.map` 方法。

```js
san.defineComponent({
  attached: function () {
    this.data.set("number", {
      value: 1,
    });
    this.updateNumber();
  },

  updateNumber: function (data) {
    this.data.apply("number", function (number) {
      return {
        value: number.value * 2,
      };
    });
  },
});
```

## 计算数据

有时候，一个数据项的值可能由其他数据项计算得来，这时我们可以通过 **computed** 定义计算数据。 **computed** 是一个对象，key 为计算数据项的名称，value 是返回数据项值的函数。

```js
san.defineComponent({
  template: "<a>{{name}}</a>",

  // name 数据项由 firstName 和 lastName 计算得来
  computed: {
    name: function () {
      return this.data.get("firstName") + " " + this.data.get("lastName");
    },
  },
});
```

上面的例子中，name 数据项是计算数据，依赖 firstName 和 lastName 数据项，其值由 firstName 和 lastName 计算得来。

**注意**：计算数据的函数中只能使用 _this.data.get_ 方法获取数据项的值，不能通过 this.method 调用组件方法，也不能通过 this.data.set 设置组件数据。

```js
san.defineComponent({
  template: "<a>{{info}}</a>",

  // name 数据项由 firstName 和 lastName 计算得来
  computed: {
    name: function () {
      return this.data.get("firstName") + " " + this.data.get("lastName");
    },

    info: function () {
      return this.data.get("name") + " - " + this.data.get("email");
    },
  },
});
```

计算数据项可以依赖另外一个计算数据项，上面的例子中，info 项依赖的 name 项就是一个计算数据项。但是使用时一定要注意，不要形成计算数据项之间的循环依赖。

### Computed 对象的实现

考虑：

1. 对象遍历
2. 数据依赖

先遍历 computed 对象， 逐个遍历计算，如果发现有 undefined 的 data，就去看下是否存在对应的 computed 方法

又或者是对 computed 对象进行依赖分析，看一下 computed 依赖了哪些 data，然后再处理 data 的顺序

https://zhuanlan.zhihu.com/p/371620220

## 数组方法

我们提供了一些数组操作的方法，这些方法与 JavaScript 的数组操作方法基本同名，以减少使用者的学习与记忆成本。除了 **删除** 操作。

**提示**：修改数组项还是直接使用 set 方法。我们可以认为，基本上所有写 JavaScript 时使用 = 赋值的场景，都用 set 方法。

```js
san.defineComponent({
  flag: function () {
    this.data.set("users[0].flag", true);
  },
});
```

### Push

```
{number} push({string|Object}expr, {*}value, {Object?}option)
```

在数组末尾插入一条数据。

```js
san.defineComponent({
  addUser: function (name) {
    this.data.push("users", { name: name });
  },
});
```

### Pop

```
{*} pop({string|Object}expr, {Object?}option)
```

在数组末尾弹出一条数据。

```js
san.defineComponent({
  rmLast: function () {
    this.data.pop("users");
  },
});
```

### Unshift

```
{number} unshift({string|Object}expr, {*}value, {Object?}option)
```

在数组开始插入一条数据。

```js
san.defineComponent({
  addUser: function (name) {
    this.data.unshift("users", { name: name });
  },
});
```

### Shift

```
{*} shift({string|Object}expr, {Object?}option)
```

在数组开始弹出一条数据。

```js
san.defineComponent({
  rmFirst: function () {
    this.data.shift("users");
  },
});
```

### Remove

```
remove({string|Object}expr, {*}item, {Object?}option)
```

移除一条数据。只有当**数组项与传入项完全相等**(===) 时，数组项才会被移除。

```js
san.defineComponent({
  rm: function (user) {
    this.data.remove("users", user);
  },
});
```

> 原生数组没有 remove 方法

### removeAt

```
removeAt({string|Object}expr, {number}index, {Object?}option)
```

通过**数据项的索引**移除一条数据。

```js
san.defineComponent({
  rmAt: function (index) {
    this.data.removeAt("users", index);
  },
});
```

### Splice

```
{Array} splice({string|Object}expr, {Array}spliceArgs, {Object?}option)
```

向数组中添加或删除项目。

```
san.defineComponent({
    rm: function (index, deleteCount) {
        this.data.splice('users', [index, deleteCount]);
    }
});
```

## Option @@@

每个数据操作方法，最后都可以包含一个类型为 Object 的数据操作选项参数对象，该对象中的参数可控制视图更新行为。

silent：静默更新数据，不触发视图变更。

force：设置相同的数据时，强制触发视图变更。该参数仅对 set 方法有效

版本：>= 3.5.5

## 数据校验 DataTypes

我们可以给组件的 data 指定校验规则。如果传入的数据不符合规则，那么 san 会抛出异常。当组件给其他人使用时，这很有用。

指定校验规则，需要使用 `DataTypes` 进行声明：

```js
import san, { DataTypes } from "san";

let MyComponent = san.defineComponent({
  dataTypes: {
    name: DataTypes.string,
  },
});
```

`DataTypes` 提供了一系列校验器，可以用来保证组件得到的数据是合法的。在上边的示例中，我们使用了 `DataTypes.string`。当一个 `name` 得到了一个不合法的数据值时，san 会抛出异常。

**考虑到性能原因，`dataTypes` 只会在 `development` 模式下进行数据校验。**

请参考 [这里](https://github.com/baidu/san/tree/master/dist) 来确认在不同的 san 发布版本中数据校验功能的支持情况。

### 示例

和 React 基本一致@@@

下边是 `DataTypes` 提供的各种校验的一个示例代码：

```js
import san, { DataTypes } from "san";

san.defineComponent({
  // 你可以声明数据为 JS 原生类型。
  // 默认的以下这些数据是可选的。
  optionalArray: DataTypes.array,
  optionalBool: DataTypes.bool,
  optionalFunc: DataTypes.func,
  optionalNumber: DataTypes.number,
  optionalObject: DataTypes.object,
  optionalString: DataTypes.string,
  optionalSymbol: DataTypes.symbol,

  // 你也可以声明数据为指定类的实例。
  // 这里会使用 instanceof 进行判断。
  optionalMessage: DataTypes.instanceOf(Message),

  // 如果你可以确定你的数据是有限的几个值之一，那么你可以将它声明为枚举类型。
  optionalEnum: DataTypes.oneOf(["News", "Photos"]),

  // 可以是指定的几个类型之一
  optionalUnion: DataTypes.oneOfType([
    DataTypes.string,
    DataTypes.number,
    DataTypes.instanceOf(Message),
  ]),

  // 数组中每个元素都必须是指定的类型
  optionalArrayOf: DataTypes.arrayOf(DataTypes.number),

  // 对象的所有属性值都必须是指定的类型
  optionalObjectOf: DataTypes.objectOf(DataTypes.number),

  // 具有特定形状的对象
  optionalObjectWithShape: DataTypes.shape({
    color: DataTypes.string,
    fontSize: DataTypes.number,
  }),

  // 以上所有校验器都拥有 `isRequired` 方法，来确保此数据必须被提供
  requiredFunc: DataTypes.func.isRequired,
  requiredObject: DataTypes.shape({
    color: DataTypes.string,
  }).isRequired,

  // 一个必须的但可以是任何类型的数据
  requiredAny: DataTypes.any.isRequired,

  // 你也可指定一个自定义的校验器。
  // 如果校验失败，它应该丢出一个异常。
  customProp: function (props, propName, componentName) {
    if (!/matchme/.test(props[propName])) {
      throw new Error(
        "Invalid prop `" +
          propName +
          "` supplied to" +
          " `" +
          componentName +
          "`. Validation failed."
      );
    }
  },

  // 你也可以给 `arrayOf` 和 `objectOf` 提供一个自定义校验器。
  // 如果校验失败，那么应该当抛出一个异常。
  // 对于数组或者对象中的每个元素都会调用校验器进行校验。
  // 第一个参数是这个数组或者对象，第二个参数是元素的 key。
  customArrayProp: DataTypes.arrayOf(function (
    dataValue,
    key,
    componentName,
    dataFullName
  ) {
    if (!/matchme/.test(dataValue[key])) {
      throw new Error(
        "Invalid prop `" +
          dataFullName +
          "` supplied to" +
          " `" +
          componentName +
          "`. Validation failed."
      );
    }
  }),
});
```

# 生命周期

San 的组件是 HTML 元素扩展的风格，所以其生命周期与 WebComponents 相符合。

- `compiled` - 组件视图模板编译完成
- `inited` - 组件实例初始化完成
- `created` - 组件元素创建完成
- `attached` - 组件已被附加到页面中
- `detached` - 组件从页面中移除
- `disposed` - 组件卸载完成

组件的生命周期有这样的一些特点：

- 生命周期代表组件的状态，生命周期本质就是状态管理。
- 在生命周期的不同阶段，组件对应的钩子函数会被触发运行。
- 并存。比如 attached 和 created 等状态是同时并存的。
- 互斥。attached 和 detached 是互斥的，disposed 会互斥掉其它所有的状态。
- 有的时间点并不代表组件状态，只代表某个行为。当行为完成时，钩子函数也会触发。如 **updated** 代表每次数据变化导致的视图变更完成。

下图详细描述了组件的生存过程：

computed 和 initData 的先后顺序？

![](https://raw.githubusercontent.com/chiyu-git/chiyuu-pic/master/20200401163254.png)

## initData

感觉也是一个生命周期，但是 initData 的时候，组件的方法还没有声明@@@

为了整合，我想把 initData 的逻辑，也放到一个公共函数里，在哪个声明周期函数才被声明呢？ inited 周期

## Attached

这里有个很常用的实践方法：在生命周期 **attached** 中重新灌入数据，使视图刷新。在这里，我们可以发起获取数据的请求，在请求返回后更新数据。

```js
var ListComponent = san.defineComponent({
  template: '<ul><li s-for="item in list">{{item}}</li></ul>',

  initData: function () {
    return {
      list: [],
    };
  },

  attached: function () {
    requestList().then(this.updateList.bind(this));
  },

  updateList: function (list) {
    this.data.set("list", list);
  },
});
```

# 事件处理

事件是开发中最常用的行为管理方式。通过 **on-** 前缀，可以将事件的处理绑定到组件的方法上。

**提示**：在 San 中，无论是 DOM 事件还是组件的自定义事件，都通过 **on-** 前缀绑定，没有语法区分。

## DOM 事件

**on- + 事件名** 将 DOM 元素的事件绑定到组件方法上。当 DOM 事件触发时，组件方法将被调用，this 指向组件实例。下面的例子中，当按钮被点击时，组件的 submit 方法被调用。

```js
san.defineComponent({
  template: '...<button type="button" on-click="submit">submit</button>',

  submit: function () {
    var title = this.data.get("title");
    if (!title) {
      return;
    }

    sendData({ title: title });
  },
});
```

绑定事件时，可以指定参数，引用当前渲染环境中的数据。参数可以是任何类型的 [表达式](https://baidu.github.io/san/tutorial/template/#表达式)。

```html
<!-- Template -->
<ul>
  <li s-for="item, index in todos">
    <h3>{{ item.title }}</h3>
    <p>{{ item.desc }}</p>
    <i class="fa fa-trash-o" on-click="rmTodo(item)"></i>
  </li>
</ul>
```

```js
// Component
san.defineComponent({
  rmTodo: function (todo) {
    service.rmTodo(todo.id);
    this.data.remove("todos", todo);
  },
});
```

### **$event**

指定参数时，**$event** 是 San 保留的一个特殊变量，指定 $event 将引用到 DOM Event 对象。从而你可以拿到事件触发的 DOM 对象、鼠标事件的鼠标位置等事件信息。

**$event 参数的位置可以是任意的**

```js
san.defineComponent({
  template:
    '<button type="button" on-click="clicker($event)">click here</button>',

  clicker: function (e) {
    alert(e.target.tagName); // BUTTON
  },
});
```

## 自定义事件

在组件上通过 **on-** 前缀，可以绑定组件的自定义事件。

下面的例子中，MyComponent 为 Label 组件绑定了 done 事件的处理方法。

```js
var MyComponent = san.defineComponent({
  components: {
    "ui-label": Label,
  },

  template:
    '<div><ui-label bind-text="name" on-done="labelDone($event)"></ui-label></div>',

  labelDone: function (doneMsg) {
    alert(doneMsg);
  },
});
```

San 的组件体系提供了事件功能，Label 直接通过调用 fire 方法就能方便地派发一个事件。

```js
var Label = san.defineComponent({
  template: '<template class="ui-label" title="{{text}}">{{text}}</template>',

  attached: function () {
    this.fire("done", this.data.get("text") + " done");
  },
});
```

## 修饰符

### Capture

`版本`：>= 3.3.0

在元素的事件声明中使用 capture 修饰符，事件将被绑定到捕获阶段。

```js
var MyComponent = san.defineComponent({
  template:
    "" +
    '<div on-click="capture:mainClick">' +
    '<button on-click="capture:btnClick">click</button>' +
    "</div>",

  mainClick: function (title) {
    alert("Main");
  },

  btnClick: function (title) {
    alert("Button");
  },
});
```

**注意**：只有在支持 **addEventListener** 的浏览器环境支持此功能，老旧 IE 上使用 capture 修饰符将没有效果。

### Native

`版本`：>= 3.3.0

在组件的事件声明中使用 native 修饰符，事件将被绑定到**组件根元素**的 DOM 事件。

```js
var Button = san.defineComponent({
  template: '<a class="my-button"><slot/></a>',
});

var MyComponent = san.defineComponent({
  components: {
    "ui-button": Button,
  },

  template:
    '<div><ui-button on-click="native:clicker(title)">{{title}}</ui-button></div>',

  clicker: function (title) {
    alert(title);
  },
});
```

上例种 click 事件是绑定在 Button 组件的 a 元素上

有时候组件封装了一些基础结构和样式，同时希望点击、触摸等 DOM 事件由外部使用方处理。如果组件需要 fire 每个根元素 DOM 事件是很麻烦并且难以维护的。native 修饰符解决了这个问题。

# Ref&Dom

ref({string}name)

获取定义了 **s-ref** 的子组件或 HTMLElement。详细请参考 [组件层级](https://baidu.github.io/san/tutorial/component/#组件层级) 文档。

**注意**：组件根元素即使定义了 **s-ref**，也无法通过 **ref** 方法获得。获取组件根元素请使用 **this.el**。

```js
var AddForm = san.defineComponent({
  // template

  components: {
    "ui-timepicker": require("../ui/TimePicker"),
    "ui-calendar": require("../ui/Calendar"),
  },

  submit: function () {
    this.ref("endDate");
    this.ref("endHour");

    this.ref("rootNode"); // undefined
    this.el; //根元素 <div class="form"> ... </div>
  },
});

/* template:
<div class="form" s-ref="rootNode">
    <div>预期完成时间：
        <ui-calendar bindx-value="endTimeDate" s-ref="endDate"></ui-calendar>
        <ui-timepicker bindx-value="endTimeHour" s-ref="endHour"></ui-timepicker>
    </div>

    <div class="form-op">
        <button type="button" on-click="submit">ok</button>
    </div>
</div>
*/
```

## 注意

声明子组件时，如果通过 **s-ref** 指定了名称，则可以在 owner 组件实例的 **ref** 方法调用到。

**注意**：有了声明式的初始化、数据绑定与事件绑定，我们很少需要在 owner 中拿到子组件的实例。虽然 San 提供了这个途径，但当你用到它的时候，请先思考是不是非要这么干。

# 条件渲染

if 指令

&&

三目运算符

```html
<td
  s-for="date in week"
  class="{{date.type}} {{date}}"
  on-click="pickDate(date)"
>
  {{date.day}}
</td>
```

可以用双插值嘛？

# 表单

表单是常见的用户输入承载元素，本篇介绍一些常用表单元素的用法。在 MVVM 中，我们一般在用户输入的表单元素或组件上应用 **双向绑定**。

## 输入框

输入框的绑定方法比较简单，直接对输入框的 value 属性应用双向绑定就行了。

```html
<input type="text" value="{= name =}" />
```

## Checkbox

checkbox 常见的使用场景是分组，在组件模板中，我们把需要分组的 checkbox 将 checked 属性双向绑定到同名的组件数据中。

**提示**：除非你需要进行传统的表单提交，否则无需指定 checkbox 的 name 属性。San 仅以 checked 作为分组的依据。

```html
<!-- Template -->
<div>
  <label
    ><input
      type="checkbox"
      value="errorrik"
      checked="{= online =}"
    />errorrik</label
  >
  <label
    ><input
      type="checkbox"
      value="otakustay"
      checked="{= online =}"
    />otakustay</label
  >
  <label
    ><input
      type="checkbox"
      value="firede"
      checked="{= online =}"
    />firede</label
  >
</div>
```

我们期望 checkbox 绑定到的数据项是一个 **Array** 。当 checkbox 被选中时，其 value 会被添加到绑定的数据项中；当 checkbox 被取消选中时，其 value 会从绑定数据项中移除。

```js
// Component
san.defineComponent({
  // ...

  initData: function () {
    return {
      online: [],
    };
  },

  attached: function () {
    this.data.set("online", ["errorrik", "otakustay"]);
  },
});
```

## Radio

与 checkbox 类似，我们在组件模板中，把需要分组的 radio 将 checked 属性绑定到同名的组件数据中。

**提示**：你需要手工指定分组 radio 的 name 属性，使浏览器能处理 radio 选择的互斥。可以把它设置成与绑定数据的名称相同。

```html
<!-- Template -->
<div>
  <label
    ><input
      type="radio"
      value="errorrik"
      checked="{= online =}"
      name="online"
    />errorrik</label
  >
  <label
    ><input
      type="radio"
      value="otakustay"
      checked="{= online =}"
      name="online"
    />otakustay</label
  >
  <label
    ><input
      type="radio"
      value="firede"
      checked="{= online =}"
      name="online"
    />firede</label
  >
</div>
```

我们期望 radio 绑定到的数据项是一个 **string** 。当 radio 被选中时，绑定的数据项值被设置成选中的 radio 的 value 属性值。

```js
// Component
san.defineComponent({
  // ...

  initData: function () {
    return {
      online: "errorrik",
    };
  },
});
```

## Select

select 的使用方式和输入框类似，直接对 value 属性应用双向绑定。

```html
<!-- Template -->
<select value="{= online =}">
  <option value="errorrik">errorrik</option>
  <option value="otakustay">otakustay</option>
  <option value="firede">firede</option>
</select>
```

**提示**：在浏览器中，select 的 value 属性并不控制其选中项，select 的选中项是由 option 的 selected 属性控制的。考虑到开发的方便，开发者不需要编写 option 的 selected 属性，San 会在下一个视图更新时间片中刷新 select 的选中状态。

```js
// Component
san.defineComponent({
  // ...

  initData: function () {
    return {
      online: "errorrik",
    };
  },
});
```

# 插槽

与 Swan 共通 @@@

## 基本使用

在组件的视图模板中可以通过 slot 声明一个插槽的位置，其位置的内容可以由外层组件或者页面定义。

[在开发者工具中预览效果](swanide://fragment/2f9e341159539941e2c5ca6efe8022901578384723623)

```html
<!-- 组件内部模板 -->
<view class="wrapper">
  <view>组件内部节点</view>
  <slot></slot>
</view>
```

```html
<view>
  <custom-component>
    <view>这里是插入到组件slot中的内容</view>
  </custom-component>
</view>
```

HTML 标准关于 slot 的描述可以参考 [这里](https://developers.google.com/web/fundamentals/getting-started/primers/shadowdom#slots)

### 命名

通过 name 属性可以给 slot 命名。一个视图模板的声明可以包含**一个默认 slot 和多个命名 slot**。外层组件或页面的元素通过 slot="name" 的属性声明，可以指定自身的插入点。**没有声明 name 属性的则插入默认的 slot 中**

```html
<!-- 组件内部模板 -->
<view>
  <slot name="slot1"></slot>
  <slot name="slot2"></slot>
</view>
```

```html
<view>
  <custom-component>
    <view slot="slot1">我会被插入到组件上方</view>
    <view slot="slot2">我会被插入到组件下方</view>
  </custom-component>
</view>
```

**注意**：外层组件的替换元素，只有在**直接子元素**上才能声明 `slot="name"` 指定自身的插入点。下面例子中的 a 元素无法被插入 title slot。

```html
<!-- 组件内部模板 -->
<view class="container">
  <slot name="slot1"></slot>
  <slot></slot>
</view>
```

```html
<view>
  <custom-component>
    <p slot="slot1">one</p>
    <p>two<a slot="slot1">slot fail</a></p>
  </custom-component>
</view>

<!-- 
custom-component 渲染结果，a 元素无法被插入 slot1
是P插入到了默认的slot，a仍是P的直接子元素
<div>
  <p>one</p>
  <p>two<a>slot fail</a></p>
</div>
-->
```

### 数据环境

插入 slot 部分的内容，其数据环境为变量声明时的环境。

[在开发者工具中预览效果](swanide://fragment/cbbd50cbb74f64cfad4d5ada7b7fba041578384852492)

```html
<!-- custom-component自定义组件 -->
<view class="component-range">
  <slot name="inner"></slot>
</view>
```

```js
Component({
  data: {
    name: "swan-inner",
  },
});
```

```html
<!-- 使用组件的页面或者组件 -->
<view>
  <custom-component>
    <view>{{name}}</view>
  </custom-component>
</view>
```

```js
Page({
  data: {
    name: "swan-outer",
  },
});
```

渲染结果：

```HTML
<view>
    <view class="component-range">
        <view>swan-outer</view>
    </view>
</view>
```

### 插槽应用指令

`版本`：>= 3.3.0

在 slot 声明时应用 if 或 for 指令，可以让插槽根据组件数据动态化。

```xml
<view>
    <slot s-if="!visible" name="subcomponent"></slot>
</view>
```

## Scoped 插槽

如果 slot 声明中包含 **s-bind** 或 1 个以上 **var-** 数据前缀声明，该 slot 为 scoped slot。scoped slot 具有独立的 **数据环境**。

scoped slot 通常用于组件的视图部分期望由 **外部传入视图结构**，渲染过程**使用组件内部数据**。

**注意**：scoped slot 中不支持双向绑定。

### Var

`版本`：>= 3.3.0

**var-** 的 scoped 数据声明的形式为 **var-name=”expression”**。

[在开发者工具中预览效果](swanide://fragment/5e80a0cb75229f0fd1704255f47960ab1578384891595)

```html
<!-- custom-component自定义组件 -->
<view class="component-range">
  <slot var-name="name"></slot>
</view>
```

```js
Component({
  data: {
    name: "swan-inner",
  },
});
```

```html
<!-- 使用组件的页面或者组件 -->
<view>
  <custom-component>
    <view>{{name}}</view>
  </custom-component>
</view>
```

```js
Page({
  data: {
    name: "swan-outer",
  },
});
```

渲染结果：

```html
<view>
  <view class="component-range">
    <view>swan-inner</view>
  </view>
</view>
```

### S-bind

`版本`：>= 3.6.0

**s-bind** 的 scoped 数据声明的形式为 **s-bind=”expression”**。

当 **s-bind** 和 **var-** 并存时，**var-** 将覆盖整体绑定中相应的数据项。

```js
var Men = san.defineComponent({
  // slot内的p有什么用？@@@
  template: `<div>
        <slot 
          s-for="item in data" 
          s-bind="{
            n: item.name, 
            email: item.email, 
            sex: item.sex ? \'male\' : \'female\'
          }">
            <p>{{n}},{{sex}},{{email}}</p>
        </slot>
      </div>`,
});

var MyComponent = san.defineComponent({
  components: {
    "x-men": Men,
  },

  template: `<div>
        <x-men data="{{men}}" s-ref="men">
          <h3>{{n}}</h3>
          <p><b>{{sex}}</b><u>{{email}}</u></p>
        </x-men>
     </div>`,
  attached: function () {
    var slots = this.ref("men").slot();

    // 3
    slots.length;

    // truthy
    slots[0].isInserted;

    // truthy
    contentSlot.isScoped;
  },
});

var myComponent = new MyComponent({
  data: {
    men: [
      { name: "errorrik", sex: 1, email: "errorrik@gmail.com" },
      { name: "leeight", sex: 0, email: "leeight@gmail.com" },
      { name: "otakustay", email: "otakustay@gmail.com", sex: 1 },
    ],
  },
});

/* MyComponent 渲染结果
<div>
    <h3>errorrik</h3>
    <p><b>male</b><u>errorrik@gmail.com</u></p>
    <h3>leeight</h3>
    <p><b>female</b><u>leeight@gmail.com</u></p>
    <h3>otakustay</h3>
    <p><b>male</b><u>otakustay@gmail.com</u></p>
</div>
*/
```

### 访问环境数据

`版本`：>= 3.3.1

scoped slot 中，除了可以访问 **var-** 声明的数据外，还可以访问当前环境的数据。

- 使用 slot 默认内容时，可以访问组件内部环境数据
- 外层组件定义的 slot 内容，可以访问外层组件环境的数据

```js
var Man = san.defineComponent({
  template:
    "" +
    "<p>" +
    '<slot var-n="who.name" var-email="who.email">' +
    "{{n}},{{email}},{{country}}" +
    "</slot>" +
    "</p>",
});

var MyComponent = san.defineComponent({
  components: {
    "x-man": Man,
  },

  template:
    "" +
    '<div><x-man who="{{man}}" country="{{country}}">' +
    "<b>{{n}} - {{province}}</b>" +
    "<u>{{email}}</u>" +
    "</x-man></div>",
});

var myComponent = new MyComponent({
  data: {
    man: {
      name: "errorrik",
      email: "errorrik@gmail.com",
    },
    country: "China",
    province: "HN",
  },
});

/* MyComponent 渲染结果
<div>
    <p>
        <b>errorrik - HN</b>
        <u>errorrik@gmail.com</u>
    </p>
</div>
*/
```

## 动态命名

`版本`：>= 3.3.1

slot 声明中，组件可以使用当前的数据环境进行命名，从而提供动态的插槽。插槽的动态命名常用于 **组件结构根据数据生成** 的场景下，比如表格组件。

```js
var Table = san.defineComponent({
  template:
    "" +
    "<table>" +
    '<thead><tr><th s-for="col in columns">{{col.label}}</th></tr></thead>' +
    "<tbody>" +
    '<tr s-for="row in datasource">' +
    '<td s-for="col in columns">' +
    '<slot name="col-{{col.name}}" var-row="row" var-col="col">{{row[col.name]}}</slot>' +
    "</td>" +
    "    </tr>" +
    "</tbody>" +
    "</table>",
});

var MyComponent = san.defineComponent({
  components: {
    "x-table": Table,
  },

  template:
    "" +
    "<div>" +
    '<x-table columns="{{columns}}" datasource="{{list}}">' +
    '<b slot="col-name">{{row.name}}</b>' +
    "</x-table>" +
    "</div>",
});

var myComponent = new MyComponent({
  data: {
    columns: [
      { name: "name", label: "名" },
      { name: "email", label: "邮" },
    ],
    list: [
      { name: "errorrik", email: "errorrik@gmail.com" },
      { name: "leeight", email: "leeight@gmail.com" },
    ],
  },
});

/* MyComponent 渲染结果
<div>
    <table>
        <thead>
            <tr>
                <th>名</th>
                <th>邮</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td><b>errorrik</b></td>
                <td>errorrik@gmail.com</td>
            </tr>
            <tr>
                <td><b>leeight</b></td>
                <td>leeight@gmail.com</td>
            </tr>
        </tbody>
    </table>
</div>
*/
```

`注意`：表格的视图更新在 IE 下可能存在兼容性问题。

## Slot 的直接子元素

slot 的直接子元素意义是什么？

# 过渡

在视图中，过渡效果是常见的场景。平滑的过渡动画能够给用户更好的感官体验。san 提供了基础的过渡机制，你可以基于此开发丰富的过渡效果。

`版本`：>= 3.3.0

## S-transition

在元素上通过 **s-transition** 指令，可以声明过渡动画控制器。

```html
<button s-transition="opacityTransition">click</button>
```

这个对象是**元素 owner** 的成员。

```js
san.defineComponent({
  template:
    '<div><button s-transition="opacityTransition">click</button></div>',

  opacityTransition: {
    // 过渡动画控制器的结构在下文中描述
    // ...
  },
});
```

我们通常把 **s-transition** 和条件或循环指令一起使用。

```html
<button s-transition="opacityTransition" s-if="allowEdit">Edit</button>
<b s-transition="opacityTransition" s-else>Edit not allow</b>
```

**s-transition** 声明的过渡动画控制器可以是 owner 组件的深层成员。

```js
san.defineComponent({
  template: '<div><button s-transition="trans.opacity">click</button></div>',

  trans: {
    opacity: {
      // 过渡动画控制器的结构在下文中描述
      // ...
    },
  },
});
```

**注意**：**s-transition** 只能应用在具体的元素中。template 这种没有具体元素的标签上应用 **s-transition** 将没有效果。

## 动画控制器

过渡动画控制器是一个包含 **enter** 和 **leave** 方法的对象。

注意：enter 和 leave 方法是由 san 触发的，当一个元素消失或者渲染时会触发相应的方法

> react-transition 是通过改变 state 组件重新渲染触发的

**enter** 和 **leave** 方法的签名为 **function({HTMLElement}el, {Function}done)**。san 会把要过渡的元素传给过渡动画控制器，控制器在完成动画后调用 **done** 回调函数。

```js
san.defineComponent({
  template: `
    <div>
      <button on-click="toggle">toggle</button>
      <button s-if="isShow" s-transition="opacityTrans">Hello San!</button>
      <button s-else s-transition="opacityTrans">Hello ER!</button>
    </div>
  `,

  toggle: function () {
    this.data.set("isShow", !this.data.get("isShow"));
  },

  opacityTrans: {
    enter: function (el, done) {
      var steps = 20;
      var currentStep = 0;

      function goStep() {
        if (currentStep >= steps) {
          el.style.opacity = 1;
          done();
          return;
        }

        el.style.opacity = (1 / steps) * currentStep++;
        requestAnimationFrame(goStep);
      }

      goStep();
    },

    leave: function (el, done) {
      var steps = 20;
      var currentStep = 0;

      function goStep() {
        if (currentStep >= steps) {
          el.style.opacity = 0;
          done();
          return;
        }

        el.style.opacity = 1 - (1 / steps) * currentStep++;
        requestAnimationFrame(goStep);
      }

      goStep();
    },
  },
});
```

**注意**：

san 把动画控制器留给应用方实现，框架本身不内置动画控制效果。应用方可以：

- 使用 css 动画，在 transitionend 或 animationend 事件监听中回调 done
- 使用 requestAnimationFrame 控制动画，完成后回调 done
- 在老旧浏览器使用 setTimeout / setInterval 控制动画，完成后回调 done
- 发挥想象力

## 动画控制器 Creator

**s-transition** 指令声明对应的对象如果是一个 function，san 将把它当成 **过渡动画控制器 Creator**。

每次触发过渡动画前，san 会调用**过渡动画控制器 Creator**，用其返回的对象作为过渡动画控制器。

```js
san.defineComponent({
  template: `
    <div>
      <button on-click="toggle">toggle</button>
      <button s-if="isShow" s-transition="opacityTrans">Hello San!</button>
      <button s-else s-transition="opacityTrans">Hello ER!</button>
    </div>
	`,

  toggle: function () {
    this.data.set("isShow", !this.data.get("isShow"));
  },

  opacityTrans: function () {
    // 区别就在这里，仅仅是返回了上例中声明的对象
    return {
      enter: function (el, done) {
        var steps = 20;
        var currentStep = 0;

        function goStep() {
          if (currentStep >= steps) {
            el.style.opacity = 1;
            done();
            return;
          }

          el.style.opacity = (1 / steps) * currentStep++;
          requestAnimationFrame(goStep);
        }

        goStep();
      },

      leave: function (el, done) {
        var steps = 20;
        var currentStep = 0;

        function goStep() {
          if (currentStep >= steps) {
            el.style.opacity = 0;
            done();
            return;
          }

          el.style.opacity = 1 - (1 / steps) * currentStep++;
          requestAnimationFrame(goStep);
        }

        goStep();
      },
    };
  },
});
```

和 [事件声明](https://baidu.github.io/san/tutorial/event/) 类似，**过渡动画控制器 Creator**调用支持传入参数。

```js
san.defineComponent({
  template: `
    <div>
      <button on-click="toggle">toggle</button>
      <button on-click="toggleTrans">toggle transition</button>
      <button s-if="isShow" s-transition="opacityTrans(noTransition)">Hello San!</button>
      <button s-else s-transition="opacityTrans(noTransition)">Hello ER!</button>
    </div>
`,

  toggle: function () {
    this.data.set("isShow", !this.data.get("isShow"));
  },

  toggleTrans: function () {
    this.data.set("noTransition", !this.data.get("noTransition"));
  },

  initData: function () {
    return {
      noTransition: false,
    };
  },
  // 区别在于接受了一个控制参数 noTransition
  opacityTrans: function (disabled) {
    return {
      enter: function (el, done) {
        if (disabled) {
          done();
          return;
        }

        var steps = 20;
        var currentStep = 0;

        function goStep() {
          if (currentStep >= steps) {
            el.style.opacity = 1;
            done();
            return;
          }

          el.style.opacity = (1 / steps) * currentStep++;
          requestAnimationFrame(goStep);
        }

        goStep();
      },

      leave: function (el, done) {
        if (disabled) {
          done();
          return;
        }

        var steps = 20;
        var currentStep = 0;

        function goStep() {
          if (currentStep >= steps) {
            el.style.opacity = 0;
            done();
            return;
          }

          el.style.opacity = 1 - (1 / steps) * currentStep++;
          requestAnimationFrame(goStep);
        }

        goStep();
      },
    };
  },
});
```

# 组件通信

## Props

组件标签上的属性，子组件可以像全局的变量一样在模板内使用

state 也是，组件内声明了之后，可以向全局变量一样在模板内使用，不用加 this

其实就是传入的 props，成了 state，san 没有 props 和 state 的区别

如何在方法内访问 props 呢？挺离谱的

```js
console.log(this.data.get("title"));
```

方法必须是上层组件的 data 才可以，如果是上层组件的方法，是不行的，为什么呢？区别在哪里

data 的是 data，方式是在原型上的，为什么无法传递给子组件呢？@@@

binds 上都是有值的，但是 data 上却没有值，binds 我懂，只是模板分析的结果。

也就是说如果是父组件的方法，在访问的时候，没有找到

```js
parent{
  template:`<calender getSpot="{{getMonthData}}">`
  getMonthData()
}

child{
  this.data.get('getSpot') // null
}
```

```js
parent{
  template:`<calender getSpot="{{getMonthData}}">`
  initData:{
    return{getMonthData()}
    }
  }

  child{
    this.data.get('getSpot') // 有值
  }
```

传给组件的 props，如果只写一个 props，是否就默认是 true？写空串就和不写是一样的，默认为 true

# 组件层级

## 基础

### 示例

我们知道组件体系下，组件必须是可嵌套的树形关系。下面从一段代码，做一些说明。在下面的代码中，AddForm 内部使用了两个自定义组件：ui-calendar 和 ui-timepicker。

```html
<!-- Template -->
<div class="form">
  <input
    type="text"
    class="form-title"
    placeholder="标题"
    value="{= title =}"
  />
  <textarea class="form-desc" placeholder="备注" value="{= desc =}"></textarea>

  <div>
    预期完成时间：
    <ui-calendar value="{= endTimeDate =}" s-ref="endDate"></ui-calendar>
    <ui-timepicker value="{= endTimeHour =}" s-ref="endHour"></ui-timepicker>
  </div>

  <div class="form-op">
    <button type="button" on-click="submit">ok</button>
  </div>
</div>
```

```js
var AddForm = san.defineComponent({
  // template

  components: {
    "ui-timepicker": require("../ui/TimePicker"),
    "ui-calendar": require("../ui/Calendar"),
  },

  submit: function () {
    this.ref("endDate");
    this.ref("endHour");
  },
});
```

### Components

组件中通常通过声明自定义元素，使用其它组件。

组件视图可以使用哪些子组件类型，必须通过定义组件的 **components** 成员指定。key 是自定义元素的标签名，value 是组件的类。

**注意**：考虑到组件的独立性，San 没有提供全局组件注册的方法，组件必须在自身的 **components** 中声明自己内部会用到哪些组件。

有些组件可能在内容中会使用自己，比如树的节点。我们可以将 **components** 中这一项的值设置为字符串 **self**。

> key 必须是小写

```js
var Node = san.defineComponent({
    template:'<div></div>' // 可以通过属性名引用子组件

    components: {
        'ui-node': 'self'
    }
});
```

### 注意

使用自定义组件的时候，标签名必须是小写的，暂时不清楚原因

## Owner 与 Parent

**owner** 与 **parent** 的概念已经被 react 明确过了，但这里还是要专门明确下。

**owner** 指的是目标在声明时位于哪个组件的组件视图中，其生存时间、交互的通信等行为都由 **owner** 管理。**owner** 必须是一个组件。ui-calendar 的 **owner** 是 AddForm 组件。

**parent** 指的是目标在视图中对应的直接父级元素。ui-calendar 的 **parent** 是其上层的 div。**parent** 对组件管理并没有直接的意义。

## Ref

声明子组件时，如果通过 **s-ref** 指定了名称，则可以在 owner 组件实例的 **ref** 方法调用到。

**注意**：有了声明式的初始化、数据绑定与事件绑定，我们很少需要在 owner 中拿到子组件的实例。虽然 San 提供了这个途径，但当你用到它的时候，请先思考是不是非要这么干。

## 消息

通过 **dispatch** 方法，组件可以向组件树的上层派发消息。

```js
var SelectItem = san.defineComponent({
  template: '<li on-click="select"><slot></slot></li>',

  select: function () {
    var value = this.data.get("value");

    // 向组件树的上层派发消息
    this.dispatch("UI:select-item-selected", value);
  },
});
```

消息将沿着组件树向上传递，直到遇到第一个处理该消息的组件，则停止。通过 **messages** 可以声明组件要处理的消息。**messages** 是一个对象，key 是消息名称，value 是消息处理的函数，接收一个包含 target(派发消息的组件) 和 value(消息的值) 的参数对象。

> message 是一个对象，指的是在上层组件中定义好的 meesage 对象

```js
var Select = san.defineComponent({
  template: "<ul><slot></slot></ul>",

  // 声明组件要处理的消息
  messages: {
    "UI:select-item-selected": function (arg) {
      var value = arg.value;
      this.data.set("value", value);

      // arg.target 可以拿到派发消息的组件
    },
  },
});
```

消息主要用于组件与非 **owner** 的上层组件进行通信。比如，slot 内组件 SelectItem 的 **owner** 是更上层的组件，但它需要和 Select 进行通信。

```js
san.defineComponent({
  components: {
    "ui-select": Select,
    "ui-selectitem": SelectItem,
  },

  template:
    "" +
    "<div>" +
    '  <ui-select value="{=value=}">' +
    '    <ui-selectitem value="1">one</ui-selectitem>' +
    '    <ui-selectitem value="2">two</ui-selectitem>' +
    '    <ui-selectitem value="3">three</ui-selectitem>' +
    "  </ui-select>" +
    "</div>",
});
```

## 动态子组件

在一些场景下，我们希望组件不在自身视图渲染时创建子组件，而是通过 JavaScript 灵活控制在未来的某些时间点创建子组件。比如：

- 浮动层子组件的 **parent** 不在其根元素 **el** 内，声明式用着不方便
- 列表只有在用户点击时才需要创建并展示

动态子组件对开发者要求更高，我们在这里给出一些需要注意的地方，下面节选的代码也做了一些简单的说明：

- 动态创建的子组件无需在 **components** 中声明类型
- 保证动态子组件不要被重复创建。常见的做法是在实例的属性上持有对创建组件的引用，并以此作判断

```js
san.defineComponent({
  mainClick: function () {
    if (!this.layer) {
      this.layer = new Layer();
      this.layer.attach(document.body);
    }

    this.layer.show();
  },

  disposed: function () {
    if (this.layer) {
      this.layer.dispose();
    }

    this.layer = null;
  },
});
```

### **owner 参数**

\>= 3.7.0

指定 owner 可以自动维护 owner 与动态子组件之间的关系：

- owner 可以收到动态子组件 dispatch 的消息
- owner dispose 时，动态子组件将自动 dispose

**注意**：指定 owner 后，不允许将组件 push 到 owner 的 children 中，否则组件 dispose 过程中，会对动态子组件进行多次 dispose 操作。

### **source 参数**

\>= 3.7.0

source 可以声明动态子组件与 owner 之间的绑定关系：

- 数据绑定，含双向绑定
- 事件

下面的示例展示了动态子组件与 owner 通信的不同方式

**注意**：如果你的组件包含指定 source 声明的动态子组件，并且预期会被循环多次创建，可以将 source 模板手动预编译，避免框架对 source 字符串进行多次重复编译，提升性能。

### 示例 1：动态子组件的数据与事件绑定

```js
// 动态子组件的数据与事件绑定，指定owner和source
// 3.7.0+
var Person = san.defineComponent({
  template:
    "<div>" +
    '  <input type="text" value="{=name=}">' +
    '  <input type="text" value="{=email=}">' +
    '  <button on-click="done">Done</button>' +
    "</div>",

  done: function () {
    this.fire("done", {
      name: this.data.get("name"),
      email: this.data.get("email"),
    });
  },
});

var MyApp = san.defineComponent({
  template:
    "<div>" +
    "  name: {{author.name}}; email{{author.email}}" +
    '  <button on-click="edit">edit</button>' +
    "</div>",

  edit: function () {
    if (!this.editor) {
      this.editor = new Person({
        owner: this,
        source:
          '<x-person name="{{author.name}}" email="{{author.email}}" on-done="editDone($event)"/>',
      });
      this.editor.attach(document.body);
    }
  },

  editDone: function (e) {
    this.data.set("author", e);
  },
});

var myApp = new MyApp({
  data: {
    author: {
      name: "erik",
      email: "errorrik@gmail.com",
    },
  },
});
myApp.attach(document.body);
```

### 示例 2：双向绑定

```js
// 动态子组件双向绑定，指定owner和source
// 3.7.0+
var Person = san.defineComponent({
  template:
    "<div>" +
    '  <input type="text" value="{=name=}">' +
    '  <input type="text" value="{=email=}">' +
    "</div>",
});

var MyApp = san.defineComponent({
  template:
    "<div>" +
    "  name: {{author.name}}; email{{author.email}}" +
    '  <button on-click="edit">edit</button>' +
    "</div>",

  edit: function () {
    if (!this.editor) {
      this.editor = new Person({
        owner: this,
        source: '<x-person name="{=author.name=}" email="{=author.email=}"/>',
      });
      this.editor.attach(document.body);
    }
  },
});

var myApp = new MyApp({
  data: {
    author: {
      name: "erik",
      email: "errorrik@gmail.com",
    },
  },
});
myApp.attach(document.body);
```

### 示例 3：通过 Dispatch 通信

```js
// 动态子组件指定owner，可以dispatch
// 3.7.0+
var Person = san.defineComponent({
  template:
    "<div>" +
    '  <input type="text" value="{=name=}">' +
    '  <input type="text" value="{=email=}">' +
    '  <button on-click="done">Done</button>' +
    "</div>",

  done: function () {
    this.dispatch("person-done", {
      name: this.data.get("name"),
      email: this.data.get("email"),
    });
  },
});

var MyApp = san.defineComponent({
  template:
    "<div>" +
    "  name: {{author.name}}; email{{author.email}}" +
    '  <button on-click="edit">edit</button>' +
    "</div>",

  edit: function () {
    if (!this.editor) {
      this.editor = new Person({
        owner: this,
        source: '<x-person name="{{author.name}}" email="{{author.email}}"/>',
      });
      this.editor.attach(document.body);
    }
  },

  messages: {
    "person-done": function (e) {
      this.data.set("author", e.value);
    },
  },
});

var myApp = new MyApp({
  data: {
    author: {
      name: "erik",
      email: "errorrik@gmail.com",
    },
  },
});
myApp.attach(document.body);
```

### 示例 4：手工预编译

```js
// 手工预编译 source
// 3.7.0+
var PersonDetail = san.defineComponent({
  template:
    "<div>" +
    "  {{name}}, {{email}}" +
    '  <button on-click="close">close</button>' +
    "</div>",

  close: function () {
    this.el.style.display = "none";
  },
  open: function () {
    this.el.style.display = "block";
  },
});

var Person = san.defineComponent({
  template:
    "<div>" +
    "  name: {{info.name}}" +
    '  <button on-click="showDetail">detail</button>' +
    "</div>",

  // 手工预编译 source
  detailSource: san.parseTemplate(
    '<x-person name="{{info.name}}" email="{{info.email}}"/>'
  ).children[0],

  showDetail: function () {
    if (!this.detail) {
      this.detail = new PersonDetail({
        owner: this,
        source: this.detailSource,
      });
      this.detail.attach(document.body);
    }

    this.detail.open();
  },
});

var MyApp = san.defineComponent({
  template: '<div><x-p s-for="p in members" info="{{p}}" /></div>',

  components: {
    "x-p": Person,
  },
});

var myApp = new MyApp({
  data: {
    members: [
      { name: "errorrik", email: "errorrik@what.com" },
      { name: "otakustay", email: "otakustay@what.com" },
    ],
  },
});
```

### 示例 5：指定 Slot

在 3.7.1 以上的版本，动态子组件的 source 参数允许声明子元素，指定插入 slot 部分的内容。

```js
// 指定插入 slot 部分的内容
// 3.7.1+
var Dialog = san.defineComponent({
  template: '<span><slot name="title"/><slot/></span>',
});

var MyApp = san.defineComponent({
  template: '<div><button on-click="alterStrong">alter strong</button></div>',

  attached: function () {
    if (!this.dialog) {
      this.dialog = new Dialog({
        owner: this,
        source:
          "<x-dialog>" +
          '<h2 slot="title">{{title}}</h2>' +
          '<b s-if="strongContent">{{content}}</b><u s-else>{{content}}</u>' +
          "</x-dialog>",
      });
      this.dialog.attach(this.el);
    }
  },

  alterStrong: function () {
    this.data.set("strongContent", !this.data.get("strongContent"));
  },
});

var myApp = new MyApp({
  data: {
    title: "MyDialog",
    content: "Hello San",
    strongContent: true,
  },
});
```

# 组件

组件是 San 的基本单位，是独立的数据、逻辑、视图的封装单元。从页面的角度看，组件是 HTML 元素的扩展。从功能模式的角度看，组件是一个 ViewModel。

## 组件定义

定义组件最基本的方法是，从 **san.Component** 继承。San 提供了 **san.inherits** 方法，用于继承。

```js
function MyApp(options) {
  san.Component.call(this, options);
}
san.inherits(MyApp, san.Component);

MyApp.prototype.template = '<ul><li s-for="item in list">{{item}}</li></ul>';

MyApp.prototype.attached = function () {
  this.data.set("list", ["san", "er", "esui", "etpl", "esl"]);
};
```

然后，通过 new 的方式就可以使用这个组件了。当然，通常你可能希望让组件出现在页面上，所以需要调用 attach 方法，将组件添加到页面的相应位置。

```js
var myApp = new MyApp();
myApp.attach(document.body);
```

**注意**：由于 ESNext 没有能够编写 prototype 属性的语法，所以 San 对组件定义时的属性支持 static property。通过 ESNext 的 extends 继承时，template / filters / components 属性请使用 static property 的方式定义。

```js
import { Component } from "san";

class HelloComponent extends Component {
  constructor(options) {
    super(options);
    // .....
  }

  static template = "<p>Hello {{name}}!</p>";

  initData() {
    return { name: "San" };
  }
}

new HelloComponent().attach(document.body);
```

对于不使用 ESNext 时，写一个 function 然后调用 **san.inherits** 再写各种 prototype 实在是有点麻烦，San 提供了快捷方法 **san.defineComponent** 用于方便地定义组件。

```js
var MyApp = san.defineComponent({
  template: '<ul><li s-for="item in list">{{item}}</li></ul>',

  attached: function () {
    this.data.set("list", ["san", "er", "esui", "etpl", "esl"]);
  },
});
```

## 初始化参数

### Data

组件初始化数据。通常在 [组件反解](https://baidu.github.io/san/tutorial/reverse/) 的场景下使用。

类型： Object

```js
var MyComponent = san.defineComponent({});

var myComponent = new MyComponent({
    el: document.getElementById('my-label'),
    data: {
        email: 'errorrik@gmail.com',
        name: 'errorrik'
    }
});

/* html:
<label id="my-label">
    <span title="errorrik@gmail.com" prop-title="{{email}}">errorrik</span>
</label>
```

### El

组件根元素。传入此参数意味着不使用组件的 **template** 作为视图模板，组件视图由 San 自动反解。详情可参考 [组件反解](https://baidu.github.io/san/tutorial/reverse/) 文档。

类型： HTMLElement

```js
var MyComponent = san.defineComponent({});

var myComponent = new MyComponent({
  el: document.getElementById("my-label"),
  data: {
    email: "errorrik@gmail.com",
    name: "errorrik",
  },
});

/* html:
<label id="my-label">
    <span title="errorrik@gmail.com" prop-title="{{email}}">errorrik</span>
</label>
*/
```

**owner&source**

**transition**

## 组件成员

### Template

组件的视图模板。详细描述请参考 [视图模板](https://baidu.github.io/san/tutorial/component/#视图模板) 文档。

类型： string

```js
san.defineComponent({
  template: '<span title="{{text}}">{{text}}</span>',
});
```

### Filters

声明组件视图模板中可以使用哪些过滤器。详细描述请参考 [过滤器](https://baidu.github.io/san/tutorial/component/#过滤器) 文档。

类型： Object

```js
san.defineComponent({
  template: '<a>{{createTime | dateFormat("yyyy-MM-dd")}}</a>',

  filters: {
    dateFormat: function (value, format) {
      return moment(value).format(format);
    },
  },
});
```

### Components

声明组件中可以使用哪些类型的子组件。

类型： Object

```js
var AddForm = san.defineComponent({
  components: {
    "ui-timepicker": TimePicker,
    "ui-calendar": Calendar,
  },
});
```

### Computed

声明组件中的计算数据。详细描述请参考 [计算数据](https://baidu.github.io/san/tutorial/component/#计算数据) 文档。

类型： Object

```js
san.defineComponent({
  template: "<a>{{name}}</a>",

  // name 数据项由 firstName 和 lastName 计算得来
  computed: {
    name: function () {
      return this.data.get("firstName") + " " + this.data.get("lastName");
    },
  },
});
```

### Messages @@@

声明处理子组件派发消息的方法。详细描述请参考 [消息](https://baidu.github.io/san/tutorial/component/#消息) 文档。

类型： Object

```js
var Select = san.defineComponent({
  template: "<ul><slot></slot></ul>",

  messages: {
    "UI:select-item-selected": function (arg) {
      // arg.target 可以拿到派发消息的组件
      var value = arg.value;
      this.data.set("value", value);
    },
  },
});
```

### initData

返回组件实例的初始数据。详细描述请参考 [初始数据](https://baidu.github.io/san/tutorial/component/#初始数据) 文档。

类型： function():Object

```js
var MyApp = san.defineComponent({
  template: '<ul><li s-for="item in list">{{item}}</li></ul>',

  initData: function () {
    return {
      list: ["san", "er", "esui", "etpl", "esl"],
    };
  },
});
```

### trimWhitespace

定义组件模板解析时对空白字符的 trim 模式。

- 默认为 **none**，不做任何事情
- **blank** 时将清除空白文本节点
- **all** 时将清除所有文本节点的前后空白字符

版本：>= 3.2.5

类型： string

```js
var MyApp = san.defineComponent({
  trimWhitespace: "blank",

  // ,
  // ......
});
```

源码

```js
switch (options.trimWhitespace) {
  case "blank":
    if (/^\s+$/.test(text)) {
      text = null;
    }
    break;

  case "all":
    text = text.replace(/(^\s+|\s+$)/g, "");
    break;
}
```

### Delimiters

定义组件模板解析时插值的分隔符。值为 2 个项的数组，分别为起始分隔符和结束分隔符。默认为:

```
['{{', '}}']
```

版本：>= 3.5.0

类型： Array

```js
var MyComponent = san.defineComponent({
  delimiters: ["{%", "%}"],
  template: '<a><span title="Good {%name%}">Hello {%name%}</span></a>',
});
```

### updateMode

可控制视图刷新的逻辑，当前仅支持 `optimized`。当取值为 `optimized` 时，对 s-for 指令的视图更新会根据不同浏览器环境进行优化，更新过程对 DOM 的操作和数据变化可能无法对应。

版本：>= 3.7.4

类型： string

```js
var MyComponent = san.defineComponent({
  updateMode: "optimized",
  template: '<ul><li s-for="item in list">{{item}}</li></ul>',
});
```

## 生命周期

## 组件方法

### Fire

fire({string}eventName, {\*}eventArgument)

派发一个自定义事件。San 为组件提供了自定义事件功能，组件开发者可以通过该方法派发事件。事件可以在视图模板中通过 **on-** 的方式绑定监听，也可以通过组件实例的 **on** 方法监听。可参考 [Event](https://baidu.github.io/san/tutorial/event/#自定义事件) 文档。

### On

on({string}eventName, {Function}eventListener)

添加自定义事件监听器。 **on** 一般仅用在使用 JavaScript 动态创建的组件中，通过视图模板创建的子组件应通过 **on-** 的方式绑定监听。可参考 [动态子组件](https://baidu.github.io/san/tutorial/component/#动态子组件) 文档

### Un

un({string}eventName, {Function=}eventListener)

移除事件监听器。 当 eventListener 参数为空时，移除所有 eventName 事件的监听器。

### Dispatch

dispatch({string}name, {\*}value)

派发一个消息。消息将沿着组件树向上传递，直到遇到第一个处理该消息的组件。上层组件通过 **messages** 声明组件要处理的消息。消息主要用于组件与非 **owner** 的上层组件进行通信。可参考 [消息](https://baidu.github.io/san/tutorial/component/#消息) 文档。

### Watch

描述： watch({string}dataName, {function({\*}value)}listener)

监听组件的数据变化。通常我们使用绑定，在子组件数据变化时自动更新父组件的对应数据。 **watch** 一般仅用在使用 JavaScript 动态创建的组件中。可参考 [动态子组件](https://baidu.github.io/san/tutorial/component/#动态子组件) 文档

```js
san.defineComponent({
  // ...

  initLayer: function () {
    if (!this.monthView) {
      var monthView = new MonthView();
      this.monthView = monthView;

      this.monthView.watch(
        "value",
        function (value) {
          this.data.set("value", value);
        }.bind(this)
      );

      this.watch("value", function (value) {
        monthView.data.set("value", value);
      });

      this.monthView.attach(document.body);
    }
  },
});
```

### Ref

### Slot

### nextTick

San 的视图更新是异步的。组件数据变更后，将在下一个时钟周期更新视图。如果你修改了某些数据，想要在 DOM 更新后做某些事情，则需要使用 `nextTick` 方法。

> 因为没有 updated 的生命周期，因此有了这方法

```js
const Component = san.defineComponent({
  template: `
        <div>
            <div s-ref="name">{{name}}</div>
            <button on-click="clicker">change name</button>
        </div>
    `,

  initData() {
    return { name: "erik" };
  },

  clicker() {
    this.data.set("name", "leeight");
    console.log(this.ref("name").innerHTML); // erik
    this.nextTick(() => {
      console.log(this.ref("name").innerHTML); // leeight
    });
  },
});
```

## 组件层级

## 视图

### 视图模板

定义组件时，通过 template 可以定义组件的视图模板。

```js
san.defineComponent({
  template:
    "<div>" +
    '<label><input type="checkbox" value="errorrik" checked="{= online =}">errorrik</label>' +
    '<label><input type="checkbox" value="otakustay" checked="{= online =}">otakustay</label>' +
    '<label><input type="checkbox" value="firede" checked="{= online =}">firede</label>' +
    "</div>",

  initData: function () {
    return {
      online: ["errorrik", "otakustay"],
    };
  },
});
```

通常，将 HTML 片段写在 JavaScript 中是不友好的，我们可以把模板写在单独的文件里，通过工具或装载器去管理。

在 webpack + ESNext 环境下引用模板：

```
待补充
```

在 AMD 环境下通过 text plugin 引用模板：

```js
san.defineComponent({
  template: require("text!./template.html"),

  initData: function () {
    return {
      online: ["errorrik", "otakustay"],
    };
  },
});
```

**注意**：San 要求组件对应 **一个** HTML 元素，所以视图模板定义时，只能包含一个 HTML 元素，其它所有内容需要放在这个元素下。

组件对应的 HTML 元素可能是由其 **owner** 组件通过视图模板指定的，视图模板不好直接定死对应 HTML 元素的标签。此时可以将视图模板对应的 HTML 元素指定为 **template**

```html
<template class="ui-timepicker">{{ value | valueText }}</template>
```

### 插槽

### El

组件实例的属性 **el** 表示组件对应的 HTML 元素，组件初始化时可以通过 option 传入。

基本上在编写组件时不需要关心它，但是在初始化组件时如果传入 **el**，意味着让组件以此元素作为组件根元素。元素将：

- 不会使用预设的 template 渲染视图
- 不会创建根元素
- 直接到达 compiled、created、attached 生命周期

有时我们为了首屏时间，期望初始的视图是直接的 HTML，不希望初始视图是由组件渲染的。但是我们希望组件为我们管理数据、逻辑与视图，后续的用户交互行为与视图变换通过组件管理，此时就可以通过 **el** 传入一个现有元素。

组件将以 **el** 传入的元素作为组件根元素并反解析出视图结构。这个过程我们称作 **组件反解**。详细请参考 [组件反解](https://baidu.github.io/san/tutorial/reverse/) 文档。

## 异步组件

版本：>= 3.7.0

[createComponentLoader](https://baidu.github.io/san/doc/main-members/#createComponentLoader) 方法返回一个组件加载器。components 中的声明为组件加载器时，将进行异步渲染：在 attach 过程中将不渲染该组件，组件将在加载完成后进行渲染。异步渲染有如下特性：

- 对同一个 [createComponentLoader](https://baidu.github.io/san/doc/main-members/#createComponentLoader) 方法返回的组件加载器，只会进行一次加载。换句话说，load 方法只会调用一次
- 组件的渲染一定是异步的。即使组件加载器当前已经完成加载，也会在主体渲染完成后的下一个 macro task，进行异步组件渲染

[createComponentLoader](https://baidu.github.io/san/doc/main-members/#createComponentLoader) 方法可以接受一个返回 Promise 的函数，加载完成后，使用组件类 resolve。

```js
var InputComponent = san.defineComponent({
  template: '<input type="text" value="{{value}}"/>',
});

// 模拟加载，1秒后加载完成
var inputLoader = san.createComponentLoader(function () {
  return new Promise(function (resolve) {
    setTimeout(function () {
      resolve(InputComponent);
    }, 1000);
  });
});

var MyApp = san.defineComponent({
  components: {
    "x-input": inputLoader,
  },

  template: '<div><x-input value="{{name}}"/></div>',
});

var myApp = new MyApp({
  data: {
    name: "San",
  },
});
myApp.attach(document.body);
```

通常情况，需要异步加载的组件都会有一定的复杂度。如果期望在加载过程中显示简单的替代视图，可以传入一个 Object 给 [createComponentLoader](https://baidu.github.io/san/doc/main-members/#createComponentLoader) 方法，通过 load 属性传入加载方法，并指定一个 placeholder 组件。

```js
var InputComponent = san.defineComponent({
  template: '<input type="text" value="{{value}}"/>',
});

var LabelComponent = san.defineComponent({
  template: "<u>{{value}}</u>",
});

// 模拟加载，1秒后加载完成
var inputLoader = san.createComponentLoader({
  load: function () {
    return new Promise(function (resolve) {
      setTimeout(function () {
        resolve(InputComponent);
      }, 1000);
    });
  },

  placeholder: LabelComponent,
});

var MyApp = san.defineComponent({
  components: {
    "x-input": inputLoader,
  },

  template: '<div><x-input value="{{name}}"/></div>',
});

var myApp = new MyApp({
  data: {
    name: "San",
  },
});
myApp.attach(document.body);
```

你可以指定一个 fallback 组件，用于加载失败时使用的视图组件。但是，标识失败需要你 reject 返回的 Promise。

```js
var LabelComponent = san.defineComponent({
  template: "<u>{{value}}</u>",
});

// 模拟加载，1秒后加载完成
var inputLoader = san.createComponentLoader({
  load: function () {
    return new Promise(function (resolve, reject) {
      setTimeout(function () {
        reject();
      }, 1000);
    });
  },

  fallback: LabelComponent,
});

var MyApp = san.defineComponent({
  components: {
    "x-input": inputLoader,
  },

  template: '<div><x-input value="{{name}}"/></div>',
});

var myApp = new MyApp({
  data: {
    name: "San",
  },
});
myApp.attach(document.body);
```

加载失败时使用的视图组件，除了通过 fallback 指定，也可以通过 reject 指定。

```js
var LabelComponent = san.defineComponent({
  template: "<u>{{value}}</u>",
});

// 模拟加载，1秒后加载完成
var inputLoader = san.createComponentLoader(function () {
  return new Promise(function (resolve, reject) {
    setTimeout(function () {
      reject(LabelComponent);
    }, 1000);
  });
});

var MyApp = san.defineComponent({
  components: {
    "x-input": inputLoader,
  },

  template: '<div><x-input value="{{name}}"/></div>',
});

var myApp = new MyApp({
  data: {
    name: "San",
  },
});
myApp.attach(document.body);
```

# 其他

## 组件反解与服务端渲染

## 主模块 API

## 周边

## san-loader/san-webpack-loader

使用了该 loader 之后，可以像 Vue 一样编写组件

```html
<template>
  <div class="hello">hello {{msg}}</div>
</template>
<script>
  export default {
    data: {
      msg: "world",
    },
  };
</script>
<style>
  .hello {
    color: blue;
  }
</style>
```

### 注意

san-loader 和 san-webpack-loader

两者都无法直接使用，提示无法找到 app.san ， fixed：loader 依赖于 html-loader，然而库里的依赖版本太老了，需要重新安装 html-loader

webpack.config.js

### Css 预编译

和 vue 类似

```html
<style lang="less" rel="stylesheet/less">
  .wrap {
    & > .title {
      color: red;
    }
  }
</style>
```

## San-router

### 基本使用

main.js

```js
import { router, Link } from "san-router";

router.add({
  rule: "/book/:id",
  Component: BookDetail,
});
router.start();
```

## 应用状态管理

这年头，一个方案里没有应用状态管理，别人看你都像残废。所以我们提供了 [san-store](https://github.com/ecomfe/san-store)。它还是有自己的特点的：

- 名字有特点。在大家都叫 nnnx 的时候，我们希望传达 store 做为全局唯一的应用状态源的观念，就叫 store 了
- 抽象有不同。我们还是希望尽量好用好理解。redux 的模式我们嫌烦琐，为了假装有节操又不能抄 vuex，所以我们提供了更简单的抽象，只有 Action。
- 应用状态数据的操作，我们通过 [san-update](https://github.com/ecomfe/san-update) 完成

## 组件库

组件库是减少实际业务开发工作量、解放生产力的根本。

- [Material Design](https://material.io/guidelines/) 是认知度比较高的一套视觉体系，我们基于它开发了一个 [MUI 组件库](https://ecomfe.github.io/san-mui/)。
- [WeUI](https://weui.io/) 是微信输出的、Mobile 友好的一套视觉体系，但是目前没有 San 的实现，欢迎感兴趣的同学来一套，质量好的话我们会在官网推荐喔。
- [AntDesign](https://ant.design/) 是蚂蚁输出的、适合各种管理系统的一套视觉体系，但是目前没有 San 的实现，也欢迎感兴趣的同学来一套，质量好的话我们会在官网推荐喔。
- 如果你的应用拥有自己的视觉体系，自己开发组件库是免不了的

曾经有人和我说，你们应该推自己的组件库啊，其实大家做应用的时候并不 care 是什么，只要好看好用就行。可是我厂是没有自己的视觉交互体系的，我能怎么办，我也很绝望啊。
