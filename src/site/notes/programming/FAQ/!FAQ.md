---
{"dg-publish":true,"permalink":"/programming/faq/faq/"}
---


# HTML\CSS 细节

## 冷知识

你是否想知道为什么在 `<script>` 标签中 没有插入 type="text/javascript" ?

在 HTML5 已经不需要该属性。 JavaScript 在所有现代浏览器中是 HTML5 的默认脚本语言！

## 0 相关单位

+ translate3d，三个都要指定，0 或者 0px 都可以，margin 也是 0 和 0px 都可以，但是秒数的指定 0s，指定 0 会报错

## 继承

+ 背景相关的样式都不会被继承
  + 背景颜色默认值：trasparent
  + 所以子元素透明背景会显示出父元素的背景，看起来就像是继承了一样

## 百分比

+ 块的 padding 和 margin 参考的都是父元素的宽度, 不论方向, 可以利用这一点用 padding 作高度实现一个响应式高度的节点 [CSS Padding a Magic Wand. I have asked this question from many… | by Aayush Arora | Coding Blocks | Medium](https://medium.com/coding-blocks/css-padding-a-magic-wand-2b8a66b84ebe)
+ 块的 height 参考的是父元素的 height
+ 字体的大小 百分比参照父元素 em 单位 参照自身
+ top，bottom，height 百分比参照与 **包含块** 的高度 @@@

  > 当一个元素的高度使用百分比值，如果其包含块没有明确的高度定义（也就是说，取决于内容高度），且这个元素不是绝对定位，则该百分比值等同于 auto。`auto` 是初始默认值，所以看起来就像是“失效”了。

+ left，ight，width，margin 全，padding 全百分比参照与 **包含块的宽度**

  > 不论定位与否

+ border-radius：使用百分数定义圆形半径或椭圆的半长轴，半短轴。水平半轴相对于盒模型的宽度；**垂直半轴相对于盒模型的高度**。不能用负值
+ background-position 百分比参照于（图片区域 - 图片的位图像素值）
+ background-size：指定背景图片相对背景区（background positioning area）的百分比。背景区由 background-origin 设置，默认为盒模型的内容区与内边距
+ translate(-50%,-50%) 百分比参照于自身的宽高
+ transform origin 百分比 像素 关键字（本质百分比） 都是参照自身左上角
+ translateZ 它不能是百分比 值; 那样的移动是没有意义的。因为百分比参照自己的厚度，没有厚度，所以百分比没有意义
+ **flex-basis 的百分比参照 box-sizing** @@@
+ 可以看到，如果将 `grid-column-gap` 设为 `80%`，它的意思就是 `gap` 占栅格容器的 `80%`，所有栅格单元只能分剩下的 `20%`。

## Auto

+ width：会将元素撑开至整个父元素 width，保留 margin border padding，自动调整 width 的大小
+ margin 还可以设置为 auto，auto 一般只设置给水平方向的 margin
  + 如果只指定，左外边距或右外边距的 margin 为 auto 则会将外边距设置为 **最大值**
  + 垂直方向外边距如果设置为 auto，则外边距 **默认就是 0**
+ left top bottom right 默认值 auto，开启绝对定位以后，如果不设置偏移量，则元素的位置 **不会发生变化，保持原本的偏移量**
+ background-size
  + 百分比：指定背景图片相对背景区（background positioning area）的百分比。背景区由 background-origin 设置，默认为盒模型的内容区与内边距
  + auto：以背景图片的比例缩放背景图片。

## 显式声明

+ 流体容器，显式声明 width:100%，因为默认值为 auto
+ 绝对定位，显式声明 left:0;top:0; 因为默认值为 auto，会被其他定位了的块级元素挤下去

## 失效

+ 开了绝对定位 margin 怎么渲染的，和 left 之类的冲突吗？margin 什么时候会失效
  + margin 是相对于兄弟，left 是相对于包含块的偏移量
  + margin 什么时候都不会失效，只有 margin 0 auto 会失效，因为开启了浮动或者定位之后，两边没有东西可以 auto
+ 同时开启绝对定位和浮动，只有定位生效
+ 当 width100% 时，已经填满了父元素，margin 0 auto 也不会居中，要居中的是里面的内容
+ 而开启浮动之后，要么向左，要么向右，与居中都没有关系，别再搞混了
+ 注意，设为 Flex 布局以后，子元素的 float、clear 和 vertical-align 属性将失效。
+ 如果任何 flex 元素的侧轴方向 margin 值设置为 auto，则会忽略 align-self。
+ 内联元素：height 属性失效，由 line-height 属性取代，margin 垂直失效，border\padding 垂直半残废，只影响呈现，不会改变元素的位置
+ **position 跟 display、overflow、float 这些特性相互叠加后会怎么样 ？**
  + display 属性规定元素应该生成的框的类型；
  + position 属性规定元素的定位类型；
  + float 属性是一种布局方式，定义元素在哪个方向浮动。
  + 类似于优先级机制：position：absolute / fixed 优先级最高，有他们在时，float 不起作用，display 值需要调整。float 或者 absolute 定位的元素，只能是块元素或表格。
+ transform 属性 , 只对 **block 级** 元素生效！

![css-basic](programming/font-end/primitive/css/css-basic.md#^6tu6yz)

![flex](../font-end/primitive/css/flex.md#^przfr4)

![flex](../font-end/primitive/css/flex.md#^4tk93x)

## 嵌套规则

+ **a 元素可以包含任意元素**，除了他本身 //浏览器会解析成两个超链接
  + 因为 a 元素的行为怪异，一般直接 display 为 block
+ **p 元素不可以包含任何块元素 ，但是 p 元素本身确实是块级元素**
  + 浏览器会解析成<p></p><div></div><p></p>
  + ![1554989417155](/img/user/programming/FAQ/!FAQ/1554989417155.png)
+ ul li 都是块级元素
  + 纯 html 的 li 都是各占一行的
+ li 的子元素不可以是 li，渲染的时候会自动变成同级

## Css 的负值

+ <https://www.cnblogs.com/coco1s/p/11319676.html>

## 简写样式

> 参考：Shorthand_properties <https://developer.mozilla.org/zh-CN/docs/Web/CSS/Shorthand_properties>

### 简写属性的边界情况

没有指定的值会被设置为它的初始值。这听起来似乎本来就很合理的样子，但这确实意味着，它将会覆盖之前设置的值。

关键词 inherit 只可以应用于单独属性（individual properties），如果应用于一个简写属性（shorthand property），则必须整体应用，而能对简写属性值的每一个部分单独应用。由于单独属性的漏掉的值会被它们的初始值（initial value）替代，因此不可能允许单个属性通过省略继承的 。这意味着让一个属性的值使用继承值的唯一方法就是使用值是 inherit 的普通属性（longhand property）。

### Margin Padding

**初始值：**上右下左，顺时针方向，均为 0

**适用元素：**all elements, except `table-row-group`, `table-header-group`, `table-footer-group`, `table-row`, `table-column-group` and `table-column`. It also applies to [`::first-letter`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/::first-letter).

**是否可继承：**no

### Lisy-style

**初始值：**

+ [`list-style-type`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/list-style-type): `disc`
+ [`list-style-position`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/list-style-position): `outside`
+ [`list-style-image`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/list-style-image): `none`

**适用元素**：list items

**是否可继承**：yes

### Font

**初始值:**

+ [`font-style`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/font-style): `normal`
+ [`font-variant`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/font-variant): `normal`
+ [`font-weight`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/font-weight): `normal`
+ [`font-stretch`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/font-stretch): `normal`(CSS3)
+ [`font-size`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/font-size): `medium`
+ [`line-height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/line-height): `normal`
+ [`font-family`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/font-family): depends on user agent

**适用元素：**全部

**是否可继承：**yes

**注意：**

+ 使用 font 设置字体样式时，斜体 加粗 小大字母，没有顺序要求，甚至可写可不写，
+ 如果不写则使用默认值，但是要求 **文字的大小和字体** 必须写，而且字体必须是最后一个样式，大小必须是倒数第二个样式
+ 在 font 中也可以指定行高（陷阱）
  + 在字体大小后可以添加**/**行高，来指定行高，该值是可选的，如果不指定则** 会使用默认值**

### Text-decoration

+ CSS Text Decoration Level 3 把这种属性变换成如下三种简写方式：[`text-decoration-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/text-decoration-color)， [`text-decoration-line`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/text-decoration-line)，和 [`text-decoration-style`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/text-decoration-style)。像其他任何简写属性一样，这表示如果简写方式没有明确设定，就把属性设置为默认值。

**初始值**

+ [`text-decoration-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/text-decoration-color): `currentcolor`
+ [`text-decoration-style`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/text-decoration-style): `solid`
+ [`text-decoration-line`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/text-decoration-line): `none`

**适用元素**：全部

**不可继承**

### Border

**初始值**

+ `border-width`

  : as each of the properties of the shorthand:

  + [`border-top-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-top-width): `medium`
  + [`border-right-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-right-width): `medium`
  + [`border-bottom-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-bottom-width): `medium`
  + [`border-left-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-left-width): `medium`
+ `border-style`

  : as each of the properties of the shorthand:

  + [`border-top-style`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-top-style): `none`
  + [`border-right-style`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-right-style): `none`
  + [`border-bottom-style`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-bottom-style): `none`
  + [`border-left-style`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-left-style): `none`
+ `border-color`

  : as each of the properties of the shorthand:

  + [`border-top-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-top-color): `currentcolor`
  + [`border-right-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-right-color): `currentcolor`
  + [`border-bottom-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-bottom-color): `currentcolor`
  + [`border-left-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-left-color): `currentcolor`

**适用元素：**all

**是否可继承**：no

**注意：**

+ `border:1px red solid;`
+ 上边的样式分别指定了边框的宽度、颜色和样式，没有顺序要求，但是不能指定单边
+ 单独设置一条边的所有样式，也可以应用简写语法
+ 简写属性，再单独控制一个

### Border-radius

**初始值：**

+ [`border-top-left-radius`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-top-left-radius): `0`
+ [`border-top-right-radius`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-top-right-radius): `0`
+ [`border-bottom-right-radius`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-bottom-right-radius): `0`
+ [`border-bottom-left-radius`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-bottom-left-radius): `0`

**适用元素：**all elements; but User Agents are not required to apply to `table` and `inline-table` elements when [`border-collapse`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-collapse) is `collapse`. The behavior on internal table elements is undefined for the moment.. It also applies to [`::first-letter`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/::first-letter).

**是否可继承**：no

+ 虽然是 border- 系列，但是却不属于 border 简写属性

### Background

**初始值：**

+ [`background-clip`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-clip)：border-box
+ [`background-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-color)：transparent
+ [`background-image`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-image)：none
+ [`background-origin`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-origin)：padding-box
+ [`background-position`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-position)：0% 0%
+ [`background-repeat`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-repeat)：repeat
+ [`background-size`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-size)：auto auto 以背景图片的比例缩放背景图片
+ [`background-attachment`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-attachment)：scroll

**适用元素：**all

**是否可继承：**no

**注意：**

+ 没有顺序的要求
+ `background: #bfa url(img/3.png) center center no-repeat fixed;`
+ 一般只用 background 设置图片和 no -repeat
+ 其他的样式再下面继续声明

### Transition

**初始值：**

+ [`transition-delay`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transition-delay): `0s`
+ [`transition-duration`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transition-duration): `0s`
+ [`transition-property`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transition-property): all
+ [`transition-timing-function`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transition-timing-function): `ease`

**适用元素：**all

**是否可继承：**no

**注意：**

+ `transition` 属性可以被指定为一个或多个 CSS 属性的过渡效果，多个属性之间用逗号进行分隔。
+ 在 transition 属性中，各个值的书写顺序是很重要的：
  + 第一个可以解析为时间的值会被赋值给 transition-duration
  + 第二个可以解析为时间的值会被赋值给 transition-delay

### Animation

**初始值：**

+ [`animation-name`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-name): `none`
+ [`animation-duration`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-duration): `0s`
+ [`animation-timing-function`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-timing-function): `ease`
+ [`animation-delay`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-delay): `0s`
+ [`animation-iteration-count`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-iteration-count): `1`
+ [`animation-direction`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-direction): `normal`
+ [`animation-fill-mode`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-fill-mode): `none`
+ [`animation-play-state`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-play-state): `running`

**适用元素：**all

**是否可继承：**no

+ 在每个动画定义中，顺序很重要：
  + 可以被解析为 <time>的第一个值被分配给 animation-duration,
  + 第二个分配给 animation-delay。

### Flex-flow

**初始值：**

+ [`flex-direction`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/flex-direction): `row`
+ [`flex-wrap`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/flex-wrap): `nowrap`

**适用元素：**flex containers

**是否可继承：**no

+ flex-direction 和 flex-wrap 的简写属性，同时定义主轴和侧轴以及方向

### Flex

![1548841188885](/img/user/programming/FAQ/!FAQ/1548841188885.png)

# React

+ 多层嵌套路由，要注意路径不要写少了，不然只会匹配到少的那个
+ `ref` 会在 `componentDidMount` 或 `componentDidUpdate` 生命周期钩子触发前更新。
+ 因为 `this.props` 和 `this.state` 可能会异步更新，所以你不要依赖他们的值来更新下一个状态。props 怎么就异步更新了呢？因为 props 是父组件传递的，父组件更新的过程中会递归子组件的更新，此时 props 可能会发生改变，而子组件已经更新完了
+ classPrefix：class 前缀。对于组件来说，定义一个统一的 class 前缀，对样式与交互分离起了非常重要的作用。
+ 1.直接添加在 React 元素上的事件，这是 React 在基于 Virtual DOM 的基础上实现的符合 w3c 规范的合成事件 (SyntheticEvent)，这种情况下 React 内部会在组件销毁前自行对事件进行解绑；

  2.JS 原生事件事件（利用 ref 获取原生元素），这种情况下需要我们手动对事件进行解绑。

+ 禁止全局的默认事件会禁止 react-router 的跳转行为，使用 history 模拟跳转
+ 组件名必须大写，否则无法渲染
  + The tag <example> is unrecognized in this browser. If you meant to render a React component, **start its name with an uppercase letter.**
+ 值得注意的是每次 `dispath` 一个 `action`，`store` 都会遍历所有已经注册的 `reducer`（reducer 往往由多个子 reducer 组成），也就是说所有 `reducer` 都会被调用 (从项目中的表现和文档来看是这样的)
+ key={\`response-${i}`}
+ state 更新过后，必须重新获取 this.state 才可以拿到新值，再函数初始时获得的时不会变的
+ 导入的样式会覆盖，引入的子组件自身的样式
+ 添加动态类名的方法：
  + ```react
    {
      sudokuArr.map((index) => {
        return <li className={index===5?'sudoku_item':'sudoku_item active_item'}></li>
      })
    }
    ```
  + 使用 es6 写法
  + ```react
    <li className={`sudoku_item ${index===5?'':'active_item'}`}></li>
    ```
+ **虚拟 DOM 必须有结束标签**
+ **虚拟 DOM 元素只能有一个根元素**
+ constructor 写在最上
+ render 写在最下面，因为 render 最后重要
+ `onClick={this.change}`，与 vue 绑定事件类似，不传参数的话不用加括号，本质上是通知 this（组件对象）自己去调用
