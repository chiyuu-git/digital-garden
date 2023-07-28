---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/css/css-basic/"}
---


# CSS 选择器

## 层叠样式表 (Cascading Style Sheets)

样式表的组成：

- 规则
  - 选择器 + 声明块
  - 声明块
    - CSS 合法的属性名 + 属性值

浏览器渲染样式表选择器的顺序：**从右往左**

- 若从左向右的匹配，发现不符合规则，需要进行回溯，会损失很多性能。
- 若从右向左匹配，先找到所有的最右节点，对于每一个节点，向上寻找其父节点直到找到根元素或满足条件的匹配规则，则结束这个分支的遍历。

css 可以用来为网页创建样式表，通过样式表可以对网页进行装饰。

所谓层叠，可以将整个网页想象成是一层一层的结构，层次高的将会覆盖层次低的。

而 css 就可以分别为网页的各个层次设置样式。

外部样式表，可以通过浏览器的缓存，加快用户的访问速度

CSS 注释：`/**/` 只有一种

## Css 语法

选择器: 通过选择器, 可以选择页面中指定的元素, 并且将声明块中的样式应用到指定的元素

声明块: 声明块紧跟在选择器的后边, 使用一对 {} 括起来，声明块中实际上就是一组一组的名值对结构， 这一组一组的名值对我们称为声明， 在一个声明块中可以写多个声明，多个声明之间使用; 隔开，声明的样式名和样式值之间使用: 来连接

`，` 结合符，

> 选择器声明的样式还是子元素属性继承父元素的样式，测试选择器，要选择不能继承的属性来测试，比如 border

## 基本选择器

### 元素选择器（标签选择器）（Type selector）

作用：通过元素选择器可以选则页面中的所有指定元素

语法：**标签名** {}

### Id 选择器（ID selector）

作用：通过元素的 id 属性值选中唯一的一个元素

语法：#id 属性值 {}

### 类选择器（Class selector）

作用：通过元素的 class 属性值选中一组元素

语法：.class 属性值 {}

以上三个是基础选择器，可以应用于以下所有复杂选择器

### 通配选择器（Universal selector）

作用：他可以用来选中页面中的所有的元素

语法：*{}

## 复杂选择器

### 并集选择器（选择器分组）（Grouping selectors）

作用：通过选择器分组可以同时选中多个选择器对应的元素

语法：选择器 1,选择器 2,选择器 N{}

### 交集选择器（Compound selector）

作用：可以选中 **同时满足** 多个选择器的元素

语法：选择器 1 选择器 2 选择器 N{}

对于 id 选择器来说，不建议使用复合选择器，因为 id 属性可以唯一确定一个元素

选择器分布：全都是类选择器；一个元素选择器其余是类选择器；

**中间没有空格**，多个元素选择器会无法识别 @@@

### 后代元素选择器（Descendant combinator）

作用：选中指定元素的指定后代元素

语法：祖先元素 后代元素 {}

**中间有空格**

### 直接子元素选择器（Child combinator）

作用：选中指定父元素的指定子元素

语法：父元素 > 子元素

IE6 及以下的浏览器不支持子元素选择器

### 兄弟元素选择器（Sibling combinator）

查找 **所有** 前一个元素后边 **一个** 紧挨着的兄弟元素

语法：前一个元素 + 后一个元素 {}

This selects all B elements that directly follow A.

```css
span + p{
  background-color: yellow;
 }
/*选择所有紧跟着span的p，选中的是p元素*/
/*如果span后面第一个是div，后面的p也不会选中*/
```

查找后边所有的兄弟元素

语法：前一个元素~后面所有 {}

```css
span ~ p{
  background-color: yellow;
 }
/*span后面所有的兄弟p元素*/
```

## 属性选择器

属性选择器可以挑选带有特殊属性的标签，[属性名] 选取含有 **指定属性名** 的元素

**[attr]{}**

```css
p[title]{
  background-color: yellow;
 }

A[attribute]
/*Combine the attribute selector with another selector (like the tag name selector) by adding it to the end.*/

a[href] 
/*selects all a elements that have a href="anything" attribute.*/
```

**[attr=" 属性值 "]**

选取 **仅** 含有指定 **属性值** 的元素

```css
[name="atguigu"]{
  background: pink;
}

<div name="atguigu">李立超</div>
<div name="atguigu_llc atguigu">李立超</div>
/*只有1被选中*/
```

**[attr~=val]**

该选择器仅选择 attr **属性的值**（以空格间隔出多个值）中有包含 val 值的所有元素，比如位于被空格分隔的多个类（class）中的一个类。

```css
[name~="atguigu"]{
 background: pink;
}
/* const regex = /\s(atguigu)\s/*/
<div name="atguigu_llc atguigu">李立超</div>
<div name="atguigu_xfz">晓飞张</div>
<div name="atguigu_bhj atguigu">白浩杰</div>
<div name="atguigu_sym">腿长1米8</div>
/*只有1 3 被选中*/
```

**[属性名 *=" 属性值 "]**

选取属性值以包含指定内容的元素

**属性值** 的引号，可加可不加

```css
input[name*='man']{
  /*123 均可选中*/
}
/* const regex = /man/*/
<input name="man-news" />
<input name="milkman" />
<input name="letterman2" />
<input name="newmilk" />
```

**[属性名^=" 属性值 "]**

选取属性值以指定内容开头的元素

托字符

**[属性名 $=" 属性值 "]**

选取属性值以指定内容结尾的元素

## 伪类选择器 @@@

设计伪元素的原因：为了让 css 有能力 **选中 DOM 树以外** 的元素

伪类专门用来表示元素的一种的 **特殊的状态**，比如：

- 访问过的超链接，
- 普通的超链接，
- 获取焦点的文本框
- 当我们需要为处在这些特殊状态的元素设置样式时，就可以使用伪类

### 链接伪类

`:link` 表示普通的链接（没访问过的链接）

`:visited` 表示访问过的链接

浏览器是通过历史记录来判断一个链接是否访问过,由于涉及到用户的隐私问题，所以使用 visited 伪类只能设置 color\background-color\border-color

```html
<a href="#1">我是第一个a标签</a>
<a href="#2">我是第二个a标签</a>
<a href="#3">我是第三个a标签</a>
<a href="#4">我是第四个a标签</a>
<a href="#5">我是第五个a标签</a>
<a href="#6">我是第六个a标签</a>
<!--不同的URI，代表不同的链接-->
<a href="javascript:;">我是第一个a标签</a>
<!--	访问的地址没有变化，伪类不会生效-->
```

`: target` 代表一个特殊的元素，这个元素的 id 是 URI 的片段标识符

### Demo 最简单的选项卡

![1548642494650](/img/user/programming/font-end/primitive/css/css-basic/1548642494650.png) 把 id 转换成#的 URI 形式 (hash)

```html
:target{
  display: block;
}
div{
  display:none;
}

<a href="#div1">div1</a>
<a href="#div2">div2</a>
<a href="#div3">div3</a>
<div id="div1">
 div1
</div>
<div id="div2">
 div2
</div>
<div id="div3">
 div3
</div>
```

### 动态伪类

`:hover` 伪类表示鼠标移入的状态

`:active` 表示的是超链接被点击的状态

> :hover 和:active 也可以为其他元素设置

IE6 中，不支持对超链接以外的元素设置:hover 和:active

::selection 控制选中的文本的样式

```css
div::selection {
  background: red;
  color: pink;
 }
```

### A 标签的伪类

a 的伪类选择器包含以上五个，顺序是 lvha

### 表单伪类

`:enabled`，匹配可编辑的表单

`:disable`，匹配被禁用的表单

`:checked`，匹配被选中的表单

> checkbox

`:focus`，匹配获焦的表单

`::selection` 选中的内容使用样式

- 有两个冒号
- 注意：这个伪类在火狐中需要采用另一种方式编写::-moz-selection

![1548642753633](/img/user/programming/font-end/primitive/css/css-basic/1548642753633.png)

### 伪类子元素选择器（结构性伪类：child 系）

**:first-child**

- 选择第一个子标签
- **注意：**是** 当前元素**是父元素的第一个子元素，不同于 JS。JS 以父元素为主体，选择子元素。CSS 以子元素为主体
- jQuery 的选择器遵循 css 原则，因为填入的是 css 选择器。这样看来特殊的其实是 JSDOM 方法

  ```css
  p:fitst-child{
    background-color: yellow;
   } 
  /*选中的是p标签的父元素下第一个p标签，且每个在父元素下是第一个子元素的p标签都会被选中*/
  /*如果不是p标签，即使是第一个子元素也无法选中*/
  ```

![1548642952892 | 200](/img/user/programming/font-end/primitive/css/css-basic/1548642952892.png) ![1548642956534 | 200](/img/user/programming/font-end/primitive/css/css-basic/1548642956534.png)

**:last-child**

- 选择最后一个子标签

**:nth-child(an+b)** @@@

- 选择指定位置的子元素，第一个元素对应的是 1，n 从 0 开始递增
- 该选择器后边可以指定一个参数，指定要选中第几个子元素

  ```css
  :nth-child(n) /*从1开始，此时等于:first-child*/
  
  #wrap ele:nth-child(1)
  /*找到#warp底下的所有子元素,并且选中第一个子元素,并且这个子元素必须是ele*/
  ```

- even 表示偶数位置的子元素
- odd 表示奇数位置的子元素

  ```css
  p:nth-child(odd){
    background-color: yellow;
  }
  ```

**:nth-last-child(从 1 开始)** @@@

- Selects the children from the bottom of the parent. This is like nth-child, but counting from the back!

**:only-child**

- You can select any element that is the only element inside of another one
- span:only-child
  - selects the span elements that are the only child of it’s parent.
- ul li:only-child
  - selects the only li element that are in a ul.
- ul *:only-child
  - selects the only any element that are in a ul.
- (相当于:first-child:last-child 或者 :nth-child(1):nth-last-child(1))

### 伪类子元素选择器（结构性伪类：type 系）

**:first-of-type**

**:last-of-type**

**:nth-of-type**

- 选择指定类型的子元素
- 和:first-child 这些非常的类似，只不过 child，是在所有的子元素中排列，而 type，是在 **当前类型的子元素** 中排列

  ```css
  #wrap ele:nth-of-type(1)
  /*找到#warp底下的所有子元素,并且选中第一个ele子元素*/
  ```

- 老大 老二 老三（女） 老四
- 大儿子（老大）大女儿（老三）

![1548643869527](/img/user/programming/font-end/primitive/css/css-basic/1548643869527.png) ![1548643873278](/img/user/programming/font-end/primitive/css/css-basic/1548643873278.png)

```css
plate:nth-of-type(2n+3) 
/* 选中了3 5 两个plate,从第3起算起（包含第三个），每两个选择一个)
n (0,1,2,3…) */
```

![1548644000154](/img/user/programming/font-end/primitive/css/css-basic/1548644000154.png)

```css
span:nth-of-type(6n+2)
/* selects every 6th instance of a span, starting from (and including) the second instance. */
```

**:nth-last-of-type(从 1 开始)**

**:only-of-type**

- 相当于:first-of-type:last-of-type 或者 :nth-of-type(1):nth-last-of-type(1)

### Nth-child 和 Nth-of-type 的一个重要的区别

```html
<div id="wrap">
 <div class="inner">div</div>
 <p class="inner">p</p>
 <span class="inner">span</span>
 <h1 class="inner">h1</h1>
 <h2 class="inner">h2</h2>
</div>

<style type="text/css">
 *{
  margin: 0;
  padding: 0;
 }
 #wrap .inner:nth-child(1){ /*选中第一个*/
  border: 1px solid;
 }
/*
#wrap .inner:nth-of-type(1){  
  border: 1px solid;
}
*/
/*解析成下面的形式，选中全部，nth-of-type以元素为中心*/
#wrap div:nth-of-type(1){
 border: 1px solid;
}
#wrap p:nth-of-type(1){
 border: 1px solid;
}
#wrap span:nth-of-type(1){
 border: 1px solid;
}
#wrap h1:nth-of-type(1){
 border: 1px solid;
}
#wrap h2:nth-of-type(1){
 border: 1px solid;
}
</style>
```

### 其他结构性伪类

**:empty**

- Selects elements that don't have any other elements inside of them.
- div:empty selects all empty div elemen
- 甚至不能有空格，但是可以有属性

**:not(选择器){}**

- 否定伪类可以帮助我们选择不是其他东西的某件东西。
- p:not(.hello)
  - 表示选择所有的 p 元素但是 class 为 hello 的除外。

## 伪元素选择器 @@@

元素即标签，标签即元素，解析的时候不在 DOM 树当中，但确实是一个 html 元素

本身没有标签，但是仍然易于识别的网页部位适用

**:first-letter 首字母**

**:first-line 首行**

**:before 表示元素最前边的部分**

**:after 表示元素的最后边的部分**

- :after 和:before 只有一个，重复的会被覆盖，而且主元素开启相对定位，after 和 before 的伪元素都可以相对于主元素进行定位，是默认的父子关系嘛？对的，默认是子元素的第一个和最后一个 @@@
- 所有的伪元素选择器，都是既可以写两个冒号也可以写一个冒号，建议写两个冒号

## 冒号

- 单冒号 (:) 用于 CSS3 伪类，双冒号 (::) 用于 CSS3 伪元素。 @@@
- 对于 CSS2 之前已有的伪元素，比如:before，单冒号和双冒号的写法::before 作用是一样的。
- 所以，如果你的网站只需要兼容 webkit、firefox、opera 等浏览器，建议对于伪元素采用双冒号的写法，如果不得不兼容 IE 浏览器，还是用 CSS2 的单冒号写法比较安全。

## 选择器的特殊性 @@@

选择器的特殊性，表现为声明的优先级，只不过是声明与选择器是联系起来的

选择器的特殊性：

- 内联样式 ， 特殊性 1000
- id 选择器，特殊性 100（0,1,0,0）
- 类、伪类、属性选择器， 特殊性 10
- 元素、伪元素选择器，特殊性 1
- 通配 * ， 特殊性 0
- 结合符，对特殊性没有贡献

继承的样式，没有特殊性

```ts
enum Specificity {
    Inline = 0x01000000,
    Id = 0x00010000,
    Attribute = 0x00000100,
    Class = 0x00000100,
    PseudoClass = 0x00000100,
    Type = 0x00000001,
    Universal = 0x00000000,
    Invalid = 0x00000000
}
```

> 即是是父级的重要声明或者内敛样式，只要是继承来的，都以继承计算，没有特殊性

![1568468884276](/img/user/programming/font-end/primitive/css/css-basic/1568468884276.png)

特殊性为 0，大于没有特殊性

选择器的特殊性最终都会授予给其对应的声明

**注意：**id 选择器和属性选择器的区别

```css
div[id="test"]（0,0,1,1） 和 #test（0,1,0,0）   
```

### 特殊性的计算

当选择器中包含多种选择器时，需要将多种选择器的特殊性相加然后在比较，

```css
/*交集选择器*/

#p2{
  background-color: yellowgreen;
 }
 
 p#p2{
  background-color: red; //red
 }

#test.test{
  background: pink;
 }
 #test.test.test{
  background: deeppink; //deeppink
 }

<div id="test" class="test"></div>
```

但是注意，选择器特殊性计算不会超过他的最大的数量级，不进位

- 11 个伪类选择器特殊性最大是 99

如果选择器的特殊性一样， 则使用靠后的样式。

```css
/* class="p1 p3" */

.p3{
  color: green; //green
 }
 
.p1{
  color: yellow;
}
```

**并集选择器** 的特殊性是单独计算

- `div , p , #p1 , .hello{}`

选择器的顺序不影响选择器的特殊性

```css
div #box3 .text{ /* 1+100+10 */
  color:yellow;
}
#box1 div .text{ /* 100+1+10 */
  color:red;
}
div #box2 .text { /* 1+100+10 */
  color:green;
}
#box1 #box2 #box3 { /* 100+1000+100 但是却是继承来的 */
    color:pink;
}
  <div id="box1">
    <div id="box2" class='box'>
      <div id="box3"><p class='text'>text</p></div>
    </div>
  </div>
```

前三个选择器的特殊性都是一样的，因此后写的后应用，但是最后一个对于 p 来说是继承而来的，即使特殊性再高，也会被继承弱化

![1570933641779](/img/user/programming/font-end/primitive/css/css-basic/1570933641779.png)

### !important

可以在 **任意样式** 的最后，添加一个!important，则此时 **该样式** 将会获得一个最高的特殊性，将会优先于所有的样式显示甚至超过内联样式，但是在开发中尽量避免使用!important

background-color: greenyellow !important**;**

标志为 !important 的声明并没有特殊的特殊性值，不过要与 **非重要声明** 分开考虑。实际上所有的重要声明会被浏览器分为一组，重要声明的冲突会在其内部解决。

非重要声明也会被分为一组，非重要声明的冲突也会在其内部解决如果一个重要声明与非重要声明冲突，胜出的总是重要声明，也就是说!important，虽然是 css 的功能，但是可以覆盖 html 的内敛样式

## 层叠

样式表可能有 3 种不同来源：编写者，用户和用户代理

- **编写者**：编写者根据文档语言约定给源文档指定样式表。例如，HTML 中，样式表可以包含在文档中或者从外部链接
- **用户**：用户可能会给某个特定文档指定样式信息。例如，用户可以指定一个含有样式表的文件，或者用户代理可能会提供一个用来生成用户样式表（或者表现得像这样做了一样）的界面
- 用户代理: （与 CSS 规范）一致的用户代理必须应用一份默认样式表（或者表现得像它做了一样）。用户代理的默认样式表应该以满足文档语言一般表现预期的方式来呈现文档语言元素（例如，对于可视化浏览器，HTML 中 EM 元素用斜体来表示）。关于 HTML 元素的推荐默认样式表，[一份简单的HTML样式表](http://www.ayqy.net/doc/css2-1/sample.html)

> 注意，用户可能会修改系统设置（例如，系统配色），这会影响默认样式表。然而，有些用户代理实现让默认样式表中的值不可改变

默认情况下，编写者样式表中的规则比用户样式表中的规则权重高。然而，对于 "!important" 规则，优先级却是相反的。所有用户和编写者规则都比 UA 默认样式表中的规则权重高

css 样式的来源大致有三种

- 创作人员
- 读者
- 用户代理（浏览器）

**权重：**

- 读者的重要声明
  - ie 等部分浏览器，用户可以导入自己的样式表
![1548646522125](/img/user/programming/font-end/primitive/css/css-basic/1548646522125.png)
- 创作人员的重要声明
- 创作人员的正常声明
- 读者的正常声明
- 用户代理的声明
![1548646586257](/img/user/programming/font-end/primitive/css/css-basic/1548646586257.png)

### 层叠

1. 找出所有相关的规则，这些规则都包含一个选择器
2. 计算声明的优先级

先按来源排序，再按选择器的特殊性排序，最终按书写的先后顺序

内联的重要声明，如何使用 css 覆盖？

```html
<p>
  <span id='s1' class='c1' style='color:green !important;'>1123456</span>
</p>
```

# 长度单位

## 像素 Px

像素是我们在网页中使用的最多的一个单位，

一个像素就相当于我们屏幕中的一个小点，我们的屏幕实际上就是由这些像素点构成的但是这些像素点，是不能直接看见。不同显示器一个像素的大小也不相同，显示效果越好越清晰，像素就越小，反之像素越大。

手机的像素点的大小只有电脑的四分之一，所以手机在显示网页的的时候会默认把像素 *4，使得看起来和电脑一致

## 百分比%

也可以将单位设置为一个百分比的形式，这样浏览器将会根据其父元素的样式来计算该值使用百分比的好处是，当 **父元素** 的属性值发生变化时，子元素也会按照比例发生改变

在我们创建一个自适应的页面时，经常使用百分比作为单位

body 也是 **父元素**，body 的宽高默认就是浏览器窗口的大小，所以使用 100% 没有内容撑开也可以占满屏幕

## Em

em 和百分比类似，它是相对于当前元素的 **字体大小** 来计算的

1em = 1font-size

使用 em 时，当字体大小发生改变时，em 也会随之改变

当设置字体相关的样式时，经常会使用 em

## Pt

**pt 在 css 单位中属于真正的绝对单位，1pt = 1/72(inch),inch 及英寸，而 1 英寸等于 2.54 厘米。**

## Rem

## Vw

# 文本格式化

## 颜色

### 颜色单位

在 CSS 可以直接使用颜色的单词来表示不同的颜色

- 红色：red
- 蓝色：blue
- 绿色：green

### RGB 值

也可以使用 RGB 值来表示不同的颜色

所谓的 RGB 值指的是通过 Red Green Blue 三元色，通过这三种颜色的不同的浓度，来表示出不同的颜色

颜色的浓度需要一个 **0-255 之间的值**，255 表示最大，0 表示没有

浓度也可以采用一个 **百分数** 来设置，需要一个 0% - 100% 之间的数字

- 使用百分数最终也会转换为 0-255 之间的数
- 0% 表示 0
- 100% 表示 255

也可以使用 **十六进制的 rgb 值** 来表示颜色，原理和上边 RGB 原理一样，

#### Hex Code

hexadecimal code（十六进制编码）简写为 hex code

只不过使用十六进制数来代替，使用三组两位的十六进制数组来表示一个颜色每组表示一个颜色 ,第一组表示红色的浓度，范围 00-ff 第二组表示绿色的浓度，范围是 00-ff 第三组表示蓝色的浓度，范围 00-ff

语法：#红色绿色蓝色

十六进制：0 1 2 3 4 5 6 7 8 9 a b c d e f

  - 00 表示没有，相当于 rgb 中的 0
  - ff 表示最大，相当于 rgb 中 255
红色：`#ff0000`
像这种两位两位重复的颜色，可以简写
  - 比如：`#ff0000` 可以写成 `#f00`
  - `#abc` `#aabbcc`

### Rgba

css3 新增

含有透明度

background: rgba(0,0,0,.5);

## 字体

### Color

设置字体颜色,使用 color 来设置 **文字** 的颜色

### Font-size

font-size 设置的并不是 **文字本身的大小**，

设置文字的大小,浏览器中一般默认的文字大小都是 16px

在页面中，每个文字都是处在一个看不见的框中的。我们设置的 font-size 实际上是设置格的高度，并不是字体的大小。一般情况下文字都要比这个格要小一些，也有时会比格大，根据字体的不同，显示效果也不能

- font-size: 30px;
- 类似在田字格里填字，不同字体占用的大小不同

在 chrome 中，字体默认值 16px，最小值 12px，0-12 渲染成 12,0 的时候字体消失，小于 0 渲染成默认值 16

### Font-family

通过 font-family 可以指定文字的字体

当采用某种字体时，如果浏览器支持则使用该字体，如果字体不支持，则使用默认字体

该样式可以同时指定多个字体，多个字体之间使用,分开，当采用多个字体时，浏览器会优先使用前边的字体，如果前边没有在尝试下一个

- font-family: arial , 微软雅黑;

浏览器使用的字体默认就是计算机中的字体，如果计算机中有，则使用，如果没有就不用

在开发中，如果字体太奇怪，用的太少了，尽量不要使用，有可能用户的电脑没有，就不能达到想要的效果。

- font-family: 华文彩云 , arial , 微软雅黑;
- font-family: "curlz mt";
![1548647041177](/img/user/programming/font-end/primitive/css/css-basic/1548647041177.png)

### Font-style

可以用来设置文字的斜体

可选值：

- normal，默认值，文字正常显示
- italic 文字会以斜体显示，靠字体设计上的倾斜
- oblique 文字会以倾斜的效果显示
- 大部分浏览器都不会对倾斜和斜体做区分，也就是说我们设置 italic 和 oblique 它们的效果往往是一样的一般我们只会使用 italic

### Font-weight

可以用来设置文本的加粗效果：

可选值：

- normal，默认值，文字正常显示
- bold，文字加粗显示

该样式也可以指定 100-900 之间的 9 个值，

但是由于用户的计算机往往没有这么多级别的字体，所以不能达到我们想要的效果也就是 200 有可能比 100 粗，300 有可能比 200 粗，但是也可能是一样的，往往不会使用

- bolder;lighter 也没啥用
- font-weight: bold;

### Font-variant

可以用来设置小型大写字母

可选值：

- normal，默认值，文字正常显示
- small-caps 文本以小型大写字母显示
- 小型大写字母：
- 将所有的字母都以大写形式显示，但是小写字母的大写，要比大写字母的大小小一些。![1548647308907](/img/user/programming/font-end/primitive/css/css-basic/1548647308907.png)

### Font 样式（简写属性）

```css
.p2{
 /*设置一个文字大小*/
 font-size: 50px;
 /*设置一个字体*/
 font-family: 华文彩云;
 /*设置文字斜体*/
 font-style: italic;
 /*设置文字的加粗*/
 font-weight: bold;
 /*设置一个小型大写字母*/
 font-variant: small-caps;
}
```

**在 CSS** 中还为我们提供了一个样式叫 font，使用该样式可以同时设置字体相关的 **所有样式**

可以将字体的样式的值，统一写在 font 样式中，不同的值之间使用空格隔开

使用 font 设置字体样式时，斜体 加粗 小大字母，没有顺序要求，甚至可写可不写，

如果不写则使用默认值，但是要求文字的大小和字体必须写，而且字体必须是最后一个样式，大小必须是倒数第二个样式

实际上使用简写属性也会有一个比较好的性能

`font: small-caps bold italic 60px"微软雅黑";`

在 font 中也可以指定行高（陷阱）

- 在字体大小后可以添加**/**行高，来指定行高，该值是可选的，如果不指定则** 会使用默认值**
- 类似的所有简写属性都是如此

不建议使用 font 简写属性

### 字体的分类

**在网页中将字体分成 5 大类：**

1. serif（衬线字体）, 宋体
2. sans-serif（非衬线字体）, 黑体
3. monospace （等宽字体）, IH
![1548647116110](/img/user/programming/font-end/primitive/css/css-basic/1548647116110.png)
4. cursive （草书字体）
5. fantasy （虚幻字体）

可以将字体设置为这些大的分类,当设置为大的分类以后，浏览器会自动选择指定的字体并应用样式，不同浏览器字体不同，仅作保险用

**一般会将字体的大分类，指定为 font-family 中的最后一个字体**

## 文本

### Text-align

用于设置文本的对齐方式

定义 **行内内容**（例如文字）如何相对它的块 **父元素** 对齐。并不控制块元素自己的对齐，只控制它的 **行内内容** 的对齐。

适用元素：块级元素

**可选值：**

1. left 默认值，文本靠左对齐
2. right ， 文本靠右对齐
3. center ， 文本居中对齐
4. justify ， 两端对齐

通过调整文本之间的空格的大小，来达到一个两端对齐的目的

分配富裕空间

### Text-indent

用来设置首行缩进

初始值：0

适用元素：块级元素

继承属性

百分比：参考包含块的宽度

> 然而浏览器厂商并没有按照规范来实现，实际上百分比参考的是自身的宽度
>
> https://github.com/w3c/csswg-drafts/issues/2394

当给它指定一个正值时，会向右侧缩进指定的像素

当给它指定一个负值时，会向左侧缩进指定的像素

**描述**

- 通过这种方式可以将一些不想显示的文字隐藏起来，用户看不见，给搜索引擎看，用的不是太多
- 这个值一般都会使用 em 作为单位

  ```css
  .p5{
    text-indent:2em;
   }
  ```

### Text-decoration

可以用来设置文本的修饰。

**可选值：**

1. none：默认值，不添加任何修饰，正常显示
2. underline 为文本添加下划线
3. overline 为文本添加上划线
4. line-through 为文本添加删除线

**描述**

- 文本修饰属性会 **延伸到后代元素**。这意味着如果祖先元素指定了文本修饰属性，后代元素则不能将其删除。
- text-decoration 会根据 **后代元素** 不同的 display 属性，而决定父元素的底线是否延伸
  - 延伸：block inline
  - 不延伸：inline-block inline-table

**示例**

```
  <p>This text has <em>some emphasized words</em> in it.</p>
  // 应用样式
  p { text-decoration: underline }
```

会对整个段落添加下划线，再添加样式 `em { text-decoration: none }` 也不会引起任何改变，整个段落仍然有下划线修饰。

然而，新加样式 `em { text-decoration: overline }` 则会在 `<em>` 标记的文字上添加另一种新样式

设置 `em` 为 `inline-block`，下划线则不会再延伸

  ![1558931708425](/img/user/programming/font-end/primitive/css/css-basic/1558931708425.png)

**描述**

- CSS Text Decoration Level 3 把这种属性变换成如下三种简写方式：[`text-decoration-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/text-decoration-color)， [`text-decoration-line`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/text-decoration-line)，和 [`text-decoration-style`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/text-decoration-style)。像其他任何简写属性一样，这表示如果简写方式没有明确设定，就把属性设置为默认值。

初始值

- [`text-decoration-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/text-decoration-color): `currentcolor`
- [`text-decoration-style`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/text-decoration-style): `solid`
- [`text-decoration-line`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/text-decoration-line): `none`

适用元素：全部

不可继承

**描述**

- 超链接会默认添加下划线，也就是超链接的 text-decoration 的默认值是 underline 如果需要去除超链接的下划线则需要将该样式设置为 none

```css
  a {
    text-decoration: none;
  }
```

### Text-transform

可以用来设置文本的大小写

**可选值：**

1. none 默认值，该怎么显示就怎么显示，不做任何处理
2. capitalize 单词的首字母大写，通过空格来识别单词 (单词边界)
3. uppercase 所有的字母都大写
4. lowercase 所有的字母都小写

### Letter-spacing

可以指定 **字符间距（汉字、字母）**

### Word-spacing

可以设置单词之间的距离实际上就是设置 **词与词之间空格的大小**

## 文本换行 @@@

### White-space

控制是否换行，溢出省略号四大金刚之一

默认情况下换行时：中文一个字为单位，自动换行，英文以单词边界为判断

```html
<p>This text has so
  me emphasized&#13;&#13;\n&#10;&#10;wo
  rds in it.
</p>
```

**属性值**

- **normal**：连续的空白符会被合并，**换行符会被当作空白符** 来处理。填充 line 盒子时，必要的话会换行

![1558933503642](/img/user/programming/font-end/primitive/css/css-basic/1558933503642.png)

- nowrap：和 normal 一样，连续的空白符会被合并，**换行符会被当作空白符** 来处理。但文本无法换行，直到遇到 `<br>` 标签为止。

![1558933540410](/img/user/programming/font-end/primitive/css/css-basic/1558933540410.png)

- pre：连续的空白符会被保留。在遇到换行符或者 `<br/>` 元素时才会换行。其行为方式类似 HTML 中的 `<pre>` 标签。

![1558933614785](/img/user/programming/font-end/primitive/css/css-basic/1558933614785.png)

- pre-wrap：连续的空白符会被保留。在遇到换行符或者 `<br/>` 元素，或者需要为了填充 line 盒子时才会换行

![1558933668049](/img/user/programming/font-end/primitive/css/css-basic/1558933668049.png)

- pre-line：连续的空白符会被合并。在遇到换行符或者 `<br/>` 元素，或者需要为了填充 line 盒子时会换行。

![1558933686973](/img/user/programming/font-end/primitive/css/css-basic/1558933686973.png)

- 下面的表格总结了各种 white-space 值的行为：

  | 换行符 | 空格和制表符 | 换行符 \|\| `&#10;` | 溢出时 |

  | :--------- | :----------- | :------------------- | ------ |

  | `normal` | 合并 | 合并 | 转行 |

  | `nowrap` | 合并 | 合并 | 不转行 |

  | `pre` | 保留 | 保留 | 不转行 |

  | `pre-wrap` | 保留 | 保留 | 转行 |

  | `pre-line` | 合并 | 保留 | 转行 |

  > 这里的换行符是指敲击回车，而不是字符串的换行符 `\n`，想要获得类似的效果我们需要使用字符实体 换行：`&#10;`，任何时候回车 `&#13;` 都会被忽略

**示例**

```css
  div{
   display: block; 
   /*关键是宽度是否设死，如果宽度由内容撑开则无法显示省略号，如果宽度设死了，inline-block也可以显示省略号*/
   white-space: nowrap;
   overflow: hidden;
   text-overflow: ellipsis;
  }
  ```

### Word-break

CSS 属性 `word-break` 指定了怎样在单词内断行。

**属性值**

- normal 使用默认的断行规则，CJK 任意字符均可断航，non-CJK 单词断行
- break-all：对于 non-CJK (CJK 指中文/日文/韩文) 文本，可在任意字符间断行。
- keep-all：CJK 文本不断行。 Non-CJK 文本表现同 `normal`。

### Overflow-wrap

用来说明当一个不能被分开的字符串太长而不能填充其包裹盒时，为防止其溢出，浏览器是否允许这样的单词中断换行。

**属性值**

- normal：表示在正常的单词结束处换行，无法容纳的部分选择溢出
- break-word：表示如果行内没有多余的地方容纳该单词到结尾，则那些正常的不能被分割的单词会被强制分割换行。

**描述**

- 可见，该属性仅在 word-break:normal 时对 non-CJK 文本有意义

  > word-wrap 属性原本属于微软的一个私有属性，在 CSS3 现在的文本规范草案中已经被重名为 overflow-wrap 。 word-wrap 现在被当作 overflow-wrap 的 “别名”。 稳定的谷歌 Chrome 和 Opera 浏览器版本支持这种新语法。

### Text-overflow

**text-overflow** [CSS](https://developer.mozilla.org/en-US/docs/Web/CSS) 属性确定如何向用户发出未显示的溢出内容信号。它可以被剪切，显示一个省略号（'...'，U + 2026 HORIZONTAL ELLIPSIS）或显示一个自定义字符串。

这个属性只对那些在块级元素溢出的内容有效，但是必须要与块级元素内联 (inline) 方向一致（举个反例：内容在盒子的下方溢出。此时就不会生效）。文本可能在以下情况下溢出：当其因为某种原因而无法换行 (例子：设置了 "white-space:nowrap")，或者一个单词因为太长而不能合理地被安置 (fit)。

这个属性并不会强制“溢出”事件的发生，因此为了能让 "text-overflow" 能够生效，程序员们必须要在元素上添加几个额外的属性，比如 " 将 [`overflow`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/overflow) 设置为 hidden"。

**属性值**

- clip：这个关键字的意思是 " 在内容区域的极限处截断文本 "，因此在字符的中间可能会发生截断。为了能在两个字符过渡处截断，你必须使用一个空字符串值 (`''`)(To truncate at the transition between two characters, the empty string value (`''`) must be used.)。**此为默认值。**
- ellipsis：这个关键字的意思是“用一个省略号 (`'…'`, `U+2026 HORIZONTAL ELLIPSIS`) 来表示被截断的文本”。这个省略号被添加在内容区域中，因此会减少显示的文本。如果空间太小到连省略号都容纳不下，那么这个省略号也会被截断。
- `<string>`：用来表示被截断的文本。字符串内容将被添加在内容区域中，所以会减少显示出的文本。如果空间太小到连省略号都容纳不下，那么这个字符串也会被截断。

## Css3 新增文本样式

### Text-shadow

用来为文字添加阴影，而且可以添加多层，阴影值之间用逗号隔开。（多个阴影时，第一个阴影在最上边）

默认值：none；不可继承

**属性值**

- `<color>`
  - 可选。可以在偏移量 **之前或之后** 指定。如果没有指定颜色，则使用 UA（用户代理）选择的颜色。
- `<offset-x> <offset-y>`
  - 必选。这些 **长度值** 指定阴影相对文字的 **偏移量**。
  - `<offset-x>` 指定水平偏移量，若是负值则阴影位于文字左边。
  - `<offset-y>` 指定垂直偏移量，若是负值则阴影位于文字上面。
  - 如果两者均为 0，则阴影位于文字正后方 (如果设置了 `<blur-radius>` 则会产生模糊效果)。
- `<blur-radius>`
  - 可选。这是 `<length>` 值。如果没有指定，则默认为 0。
  - 值越大，**模糊半径** 越大，阴影也就越大越淡，**不能取负值**
- `text-shadow: -5px -10px ,5px 10px pink;`
  - 以逗号分割列表来描述一个或多个阴影效果

**浮雕文字**

- `text-shadow:2px 2px 9px black;`
- 偏移量小一些，模糊程度大一些

**文字模糊**

```css
  h1{ 
    font:100px/200px "微软雅黑"; 
    text-align:center; 
    color:#000; 
    text-shadow:0 0 0 rgba(0,0,0,1); 
    border:1px solid #000; 
    transition:1s;
   }
   h1:hover{
    color:rgba(0,0,0,0);
    text-shadow:0 0 100px rgba(0,0,0,0.5);
   }
  ```

### -webkit-text-stroke

文字描边

只有 webkit 内核才支持：-webkit-text-stroke（准确的来说不能算是 css3 的东西，但需要大家知道）

`-webkit-text-stroke:4px pink;`

### Direction

文字排版,控制文字的方向

**属性值：**

- ltr:left to right
- rtl

**描述：**

- 一定要配合 unicode-bidi:bidi-override; 来使用

  ```css
  p{
   width:300px;
   border:1px solid #000;
   font:14px/30px "宋体";
   direction:rtl;
   unicode-bidi:bidi-override;
  }
  ```

![1548814801127](/img/user/programming/font-end/primitive/css/css-basic/1548814801127.png)

## Line-height 行间距（行高）

主段落内容的 `line-height` 至少设为 `1.5`。 这将有助于低可视条件下的体验，尤其对认知阻碍者，如阅读困难者。如果文字的大小要随页面的缩放而变化，请使用无单位的值以确保行高也会等比例缩放。

### 可选值

1. 直接就收一个大小 px
2. 可以指定一个百分数，则会 **相对于字体** 去计算行高
3. 可以直接传一个数值（行高因子），则行高会设置字体大小相应的倍数
在 cssreset 中，通常会把行高指定为 1，在编码的时候再一个个自己去调整行高
![1548652652521](/img/user/programming/font-end/primitive/css/css-basic/1548652652521.png)

**对于单行文本来说，可以将行高设置为和父元素的高度一致这样可以使得单行文本在父元素中垂直居中**

在 font 中也可以指定行高（陷阱）

在字体大小后可以添加**/**行高，来指定行高，该值是可选的，如果不指定则** 会使用默认值**

```css
  font: 30px/50px "微软雅黑";
  /*line-height: 50px;*/
  
```

- 陷阱：font 简写样式会设置默认行高样式值，所以 **行高必须在 font 样式之后指定**，或者在 font 内部指定
- 类似的，其他字体样式也要写在后面才能生效
- 类似的所有简写属性都是如此

### 行高的继承

初始值：normal

可以继承

行高因子：行高的值为 **number 形式**，继承的时候会把父级的行高因子直接继承，子元素的行高由自己的 font-size 决定。

像素 百分比，继承的时候子元素会继承 **计算之后** 的值，子元素的行高由父元素的行高决定

```css
body{
 line-height: 1; //初始化为1，内容区和行高相同便于非文本Box的排版
}
p,span{
 line-height: 1.5; //设定为1.5，大量文本，行距，美观
}
```

主段落内容的 `line-height` 至少设为 `1.5`。 这将有助于低可视条件下的体验，尤其对认知阻碍者，如阅读困难者。如果文字的大小要随页面的缩放而变化，请使用无单位的值以确保行高也会等比例缩放。

# 盒模型

## 块级元素的盒模型 @@@

一个盒子我们会分成几个部分:

1. 内容区 (content)
2. 内边距 (padding)
3. 边框 (border)
4. 外边距 (margin)

![1548661126540](/img/user/programming/font-end/primitive/css/css-basic/1548661126540.png)

### 内容区

内容区指的是盒子中放置内容的区域，**也就是元素中的文本内容，子元素都是存在于内容区中的。**@@@

如果没有为元素设置内边距和边框，则内容区大小默认和 **盒子的可见框** 是一致的。

- width 和 height 加在谁身上值由 box-sizing 决定
- box-sizing 决定了盒子默认大小是由哪些内容组成，但是 **子元素永远在内容区排列**@
- 盒子的大小是内容区 + 内边距 + 边框

通过 width 和 height 两个属性可以设置内容区的大小。

width 和 height 属性只适用于块元素。**行内元素是由内容撑开的** @@@

块级元素的高度由内容撑开，但是高度依然可以指定。行内元素的宽高均有内容撑开，但是指定宽高没有效果，可以改为 inline-block 或者用内容撑开

子元素 width100%，不会覆盖父元素的 padding

- 子元素只能在父元素的内容区排列，即使父元素的 box-sizing 不是 content-box
- content-box 因为 width 是设置内容区的宽度
- border-box 也不会覆盖 padding，最终的结果是子元素的 width 变小了（相对于 content-box 时）

### 内边距

顾名思义，内边距指的就是元素内容区与边框以内的空间。

默认情况下 width 和 height 不包含 padding 的大小。

使用 padding 属性来设置元素的内边距，上、右、下、左四个方向的内边距

内边距会影响盒子的 **可见框** 的大小，元素的背景会延伸到内边距

### 边框 @@@

as each of the properties of the shorthand:

- `border-width` : as each of the properties of the shorthand:
  - [`border-top-width`](https://developer.mozilla.org/en-US/docs/Web/CSS/border-top-width): `medium`
  - [`border-right-width`](https://developer.mozilla.org/en-US/docs/Web/CSS/border-right-width): `medium`
  - [`border-bottom-width`](https://developer.mozilla.org/en-US/docs/Web/CSS/border-bottom-width): `medium`
  - [`border-left-width`](https://developer.mozilla.org/en-US/docs/Web/CSS/border-left-width): `medium`
- `border-style` : as each of the properties of the shorthand:
  - [`border-top-style`](https://developer.mozilla.org/en-US/docs/Web/CSS/border-top-style): `none`
  - [`border-right-style`](https://developer.mozilla.org/en-US/docs/Web/CSS/border-right-style): `none`
  - [`border-bottom-style`](https://developer.mozilla.org/en-US/docs/Web/CSS/border-bottom-style): `none`
  - [`border-left-style`](https://developer.mozilla.org/en-US/docs/Web/CSS/border-left-style): `none`
- `border-color` : as each of the properties of the shorthand:
  - [`border-top-color`](https://developer.mozilla.org/en-US/docs/Web/CSS/border-top-color): `currentcolor`
  - [`border-right-color`](https://developer.mozilla.org/en-US/docs/Web/CSS/border-right-color): `currentcolor`
  - [`border-bottom-color`](https://developer.mozilla.org/en-US/docs/Web/CSS/border-bottom-color): `currentcolor`
  - [`border-left-color`](https://developer.mozilla.org/en-US/docs/Web/CSS/border-left-color): `currentcolor`

可以在元素周围创建边框，边框是元素 **可见框** 的最外部。可以使用 border 属性来设置盒子的边框：

要为一个元素设置边框 **必须** 指定：border-style: 边框的样式

其实设置边框需要三个值，还需要加上：

- border-width: 边框的宽度
- border-color: 边框颜色

但是，在大部分的浏览器中，边框的宽度和颜色都是有 **默认值**

而边框的样式默认值都是 none，所以只写一个 solid 就可以有边框效果

可以使用 border-top/left/right/bottom 分别指定上右下左四个方向的边框。

- 除了 border-width，CSS 中还提供了四个 border-xxx-width
- xxx 的值可能是 top right bottom left 专门用来设置指定边的宽度
- 颜色也是一样，style 也一样

边框可以设置多种 style：

- none（没有边框）
- dotted（点线）
- **dashed（虚线）**
- **solid（实线）**
- double（双线）
- groove（槽线）
- ridge（脊线）
- inset（凹边）
- outset（凸边）

不同 style 的边框交合处的渲染不同：solid，两边按 border-width 值分配；dashed，交合处为空白 @@@

### 简写语法

`border:1px red solid;`

上边的样式分别指定了边框的宽度、颜色和样式，没有顺序要求，但是不能指定单边

单独设置一条边的所有样式，也可以应用简写语法

3 个一样一个不同，border 再单独控制不同的

### 盒子可见框的大小

盒子可见框的大小由内容区，内边距和边框共同决定

盒子可见框的宽度 = border-left-width + padding-left + width + padding-right + border-right-width

可见宽的高度 = border-top- width + padding-top + height + padding-bottom + (border-bottom- width)//底边宽度

外边距不会影响可见框的大小，而是会影响到盒子的位置

### 外边距

外边距是元素边框与 **周围元素** 相距的空间。

外边距也可以指定为一个 **负值**，如果外边距设置的是负值，则元素会向反方向移动

使用 margin 属性可以设置外边距。用法和 padding 类似，同样也提供了四个方向的 margin-top/right/bottom/left。

外边距不会影响可见框的大小，而是会影响到盒子的位置

由于页面中的元素都是靠左靠上摆放的，所以：

- 当我们设置上和左外边距时，会导致盒子自身的位置发生改变
- 如果是设置右和下外边距会改变其他盒子的位置
- 设置负值可以导致元素重合

margin 还可以设置为 auto，auto 一般只设置给水平方向的 margin

如果只指定，左外边距或右外边距的 margin 为 auto 则会将外边距设置为 **最大值**

> 如果将 margin-left 和 margin-right 同时设置为 auto，则会将两侧的外边距设置为相同的值，就可以使元素自动在父元素中居中，所以我们经常将左右外边距设置为 auto，以使子元素在父元素中水平居中
>
> 边界是？块级上下文

垂直方向外边距如果设置为 auto，则外边距 **默认就是 0**

## 内联元素的盒模型 @@@

### 内容区

内联元素 width 和 height 属性没有效果，宽高由内容撑开, 对于 `display:inline` 的元素而言根本就没有这两个属性, 通过设置 line-height 达到相同的效果
{ #6tu6yz}


### 内边距

内联元素可以设置水平方向的内边距

内联元素可以设置垂直方向内边距，但是不会影响页面的布局（会覆盖下面的元素）

### 边框

内联元素可以设置边框，但是垂直的边框不会影响到页面的布局

### 外边距

内联元素支持水平方向的外边距

内联元素 **不支持** 垂直外边距，相当于没有这个属性，不像内边距一样影响可见区域，改变了也完全看不到

## 盒模型样式

### 宽高

https://www.zhangxinxu.com/wordpress/2016/05/css3-width-max-contnet-min-content-fit-content/

### Min-height

无法继承啊

### Max-height

`max-height` 这个属性会阻止 [`height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/height) 属性的设置值变得比 `max-height` 更大。

[`max-height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/max-height) 重载（覆盖掉） [`height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/height), 但是 [`min-height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/min-height) 又会重载（覆盖掉） [`max-height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/max-height).

**初始值**: none

**适用元素**: all

**是否继承**: no

**百分比**: 根据包含该元素的父元素的高度计算得来的,如果该元素的父元素没有明确的指定高度,则该百分比相当于 `none`.

**值**

- none：不限制高度
- `<length>`

### Cursor

cursor 属性规定要显示的鼠标的光标类型。

常用取值：pointer（手），crosshair（十字线），default（箭头），auto（浏览器设置的光标）

### Outline

CSS2.1

input 默认的 focus 样式就是使用的 outline

[CSS](https://developer.mozilla.org/en-US/docs/CSS) 的 `outline` 属性是用来设置一个或多个单独的轮廓属性的 [简写属性](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Shorthand_properties) ， 例如 [`outline-style`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/outline-style), [`outline-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/outline-width) 和 [`outline-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/outline-color)。 多数情况下，简写属性更加可取和便捷。

**轮廓与边框** 在以下几个方面存在不同：

- 轮廓不占据空间，它们被描绘于 **边框之外**
- 轮廓可以是非矩形的。在 Gecko/Firefox 中，轮廓是矩形的，但是 Opera 则会围绕元素结构绘制 非矩 形的 形状 ，如 下图

![1557234953791](/img/user/programming/font-end/primitive/css/css-basic/1557234953791.png)

**初始值**

- [`outline-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/outline-color): `invert`, for browsers supporting it, `currentColor` for the other
- [`outline-style`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/outline-style): `none`
- [`outline-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/outline-width): `medium`

**适用元素**: all

**不可继承**

**描述**

- 任意顺序的 1~3 个属性值

**示例**

```css
  /* 宽度 | 样式 | 颜色 */
  outline: 1px solid white;
```

### Outline-offset

border 和 outline 之间的距离

## CSS3 新增

### Box-shadow

以逗号分割列表来描述一个或多个阴影效果，可以用到几乎任何元素上。

如果元素同时设置了 border-radius ，阴影也会有圆角效果。

多个阴影时和多个 text shadows 规则相同 (第一个阴影在最上面)。

**适用元素：**all

**初始值: none**

**不可继承**

**属性值**

- inset
  - 默认阴影在边框外，使用 inset 后，阴影在边框内。
- `<offset-x> <offset-y>`
  - 这是头两个 `<length>` 值，用来设置阴影偏移量。
  - `<offset-x>` 设置水平偏移量，如果是负值则阴影位于元素左边。
  - `<offset-y>` 设置垂直偏移量，如果是负值则阴影位于元素上面。
  - 如果两者都是 0，那么阴影位于元素后面。
- `<blur-radius>`
  - 这是第三个 `<length>` 值。值越大，模糊面积越大，阴影就越大越淡。 **不能为负值**
  - 默认为 0，此时阴影边缘锐利。
- `<spread-radius>`
  - 这是第四个 `<length>` 值。取正值时，阴影扩大；取负值时，阴影.收缩。默认为 0，此时阴影与元素同样大。
- `<color>`
  - 阴影颜色，如果没有指定，则由浏览器决定
- 建议：inset 最前，color 最后，把数值包裹在中间，编码格式比较好

  ```css
  box-shadow:10px 10px 30px 40px blue ;
  ```

### drop-shadow()

### -webkit-box-reflect

倒影效果，设置元素的倒影（准确的来说不能算是 css3 的东西，但需要大家知道）

默认值:none 不可继承

**属性值：有顺序规定**

1. 倒影的方向
   - above, below, right, left
2. 倒影的距离
   - 长度单位
3. 渐变

`-webkit-box-reflect:right 10px linear-gradient(-90deg,rgba(0,0,0,1) 0,rgba(0,0,0,0) 80%);`

### Resize

CSS 属性允许你控制一个元素的可调整大小性。（一定要配合 **overflow:auto** 使用）

默认值：none 不可继承

**属性值**

- none
  - 元素不能被用户缩放。
- both
  - 允许用户在水平和垂直方向上调整元素的大小。
- horizontal
  - 允许用户在水平方向上调整元素的大小。
- vertical
  - 允许用户在垂直方向上调整元素的大小。

### Box-sizing

box-sizing 属性用于更改用于计算元素宽度和高度的默认的 CSS 盒子模型。可以使用此属性来模拟不正确支持 CSS 盒子模型规范的浏览器的行/列为。

默认值：content-box 不可继承

**属性值**

- content-box
  - 默认值，标准盒子模型。 width 与 height 只包括内容的宽和高， 不包括边框（border），内边距（padding），外边距（margin）。
  - 注意: 内边距, 边框 & 外边距 都在这个盒子的外部。 比如. 如果 .box {width: 350px}; 而且 {border: 10px solid black;} 那么在浏览器中的渲染的实际宽度将是 370px;
  - 尺寸计算公式：
    - width = 内容的宽度，
    - height = 内容的高度。
    - 宽度和高度都不包含内容的边框（border）和内边距（padding）

  > 区分好盒子的宽高，日常生活中用来衡量事物大小的标准，而在 css 中可见区域是包括内边距和边框的，与日常生活的差异，留意到这一点，非常重要@，所以 border-box 更加符合日常生活的思维

- border-box
  - width 和 height 属性包括 **内容，内边距和边框**，但不包括外边距。这是当文档处于 Quirks 模式 时 Internet Explorer 使用的盒模型。
  - 这里的维度计算为：
    - width = border + padding + 内容的 width
    - width 1170px ; padding 0 15px ， 这 30px 其实是从 width 里面扣的，这个时候已经无法直接设定内容的 width（普通盒模型的 width）
    - height = border + padding + 内容的 height
- 根据 UI 的切图习惯，选择合适的 box-sizing，才能符合 UI 的设计

### Border-radius

传统的圆角生成方案，必须使用多张图片作为背景图案

CSS3 圆角的出现，使得我们再也不必浪费时间去制作这些图片了，而且还有其他多个优点：

- 减少维护的工作量。图片文件的生成、更新、编写网页代码，这些工作都不再需要了。
- 提高网页性能。由于不必再发出多余的 HTTP 请求，网页的载入速度将变快。
- 增加视觉可靠性。某些情况下（网络拥堵、服务器出错、网速过慢等等），背景图片会下载失败，导致视觉效果不佳。CSS3 就不会发生这种情况

border-radius：用来设置边框圆角。当使用一个半径时确定一个圆形；当使用两个半径时确定一个椭圆，这个 (椭) 圆与边框的交集形成圆角效果。

**适用元素：all**

> all elements; but User Agents are not required to apply to `table` and `inline-table` elements when [`border-collapse`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-collapse) is `collapse`. The behavior on internal table elements is undefined for the moment.. It also applies to [`::first-letter`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/::first-letter).

**初始值：0**

**不可继承**

**属性值**

- 固定的 px 值定义圆形半径或椭圆的半长轴，半短轴。不能用负值
- 使用百分数定义圆形半径或椭圆的半长轴，半短轴。水平半轴相对于 **盒模型的宽度**；**垂直半轴相对于盒模型的高度**。不能用负值
- 正方形：当所有的角都是 50% 的时候，大于 50% 和 50% 的效果相同，一个圆。当某一个角为 0 的时候，可以大于 50%
- 矩形：大边配大角，小边配小角，统一设置 50%，即是一个椭圆，各取边的 50%

**这是一个简写属性，用来设置**

- border-top-left-radius,
- border-top-right-radius,
- border-bottom-right-radius ，
- border-bottom-left-radius

**定义半径 (单值)，属性值可取 1~4 个值**

1. border-radius: radius
2. border-radius: top-left-and-bottom-right top-right-and-bottom-left
   - `border-radius:40px 100px;`
   - 设置左上右下为 40px，右上左下为 100px
3. border-radius: top-left top-right-and-bottom-left bottom-right
4. border-radius: top-left top-right bottom-right bottom-left

- 角为圆

**定义半轴 (双值)**

1. border-radius: (first radius values) / radius
2. border-radius: (first radius values) / top-left-and-bottom-right top-right-and-bottom-left
   - border-radius:40px/60px 40px;
   - 40px 60px 定义第一个椭圆，用于正斜杠的两个角
   - 40px 40px 定义第二椭圆，用于反斜杠的两个角
3. border-radius: (first radius values) / top-left top-right-and-bottom-left bottom-right
4. border-radius: (first radius values) / top-left top-right bottom-right bottom-left

- 角为椭圆

**注意，百分比值**

- 在旧版本的 Chrome 和 Safari 中不支持。(fixed in Sepember 2010)
- 在 11.50 版本以前的 Opera 中实现有问题。
- Gecko 2.0 (Firefox 4) 版本前实现不标准：水平半轴和垂直半轴都相对于盒子模型的宽度。
- 在旧版本的 iOS (iOS 5 之前) 和 Android 中 (WebKit 532 之前) 不支持。
- 移动端的浏览器是内置在操作系统内部的，更新很缓慢

**demo 风车**

- border-radius:0 60%;

![1548816112576](/img/user/programming/font-end/primitive/css/css-basic/1548816112576.png)

**demo 气泡对话框**

- 主体是椭圆
- 气泡用伪元素来写即可
- right 0 bottom 0 坐标轴的颠倒，重合在主元素的右下角

**应用：**

- 裁剪内切圆 jpg，内容是圆形的，jpg 格式的背景不透明，不用返工 UI，直接设置背景为圆角

### 边框图片

在 border 里放图片，一般需要专门设计

**border-image-source 属性**

- 定义使用一张图片来代替边框样式；如果只为 none，则仍然使用 border-style 定义的样式。
- 默认值：none 不可继承

**border-image-slice 属性**

- 通过规范将 border-image-source 的图片明确的分割为 9 个区域：四个角，四边以及中心区域，并可指定偏移量
- 默认值：100% 不可继承
- 值得百分比参照于 image 本身！！

 **border-image-repeat**

- 定义图片如何填充边框。或为单个值，设置所有的边框；或为两个值，分别设置水平与垂直的边框。
- 默认值：stretch 不可继承
  - stretch （拉伸）
  - repeat，round（平铺）

**border-image-width**

- 定义图像边框宽度。
- 默认值：1 不可继承

**border-image-outset**

- 定义边框图像可超出边框盒的大小
- 默认值：0 不可继承
- 正值： 可超出边框盒的大小

## Display 和 Visibility

### Display

我们不能为行内元素设置 width、height、margin-top 和 margin-bottom。

我们可以通过修改 display 来修改元素的性质。

**可选值：**

- block：设置元素为块元素（独占一行，这一行不能再有其他元素）
- inline：设置元素为行内元素
- inline-block：设置元素为行内块元素（不会独占一行，又有可以设置宽高，常见的 img 就是行内块）
- none：隐藏元素（元素将在页面中完全消失（渲染树中不存在），但是 DOM 结构依然存在@@@）

不常用：

- list-item：元素像块元素一样显示，并添加样式列表标记。
- table：元素会作为块级表格来显示。
- table-caption：元素会作为一个表格标题显示。
- inherit：从父元素继承 display 属性。
- <https://segmentfault.com/a/1190000012833458>

### Visibility

- 可以用来设置元素的隐藏和显示的状态
- 是一个可动画属性

**可选值：**

- visible 默认值，元素默认会在页面显示
- hidden 元素会隐藏不显示

  > 使用 visibility:hidden; 隐藏的元素虽然不会在页面中显示，但是它的 **位置会依然保持**（与 v-if v-show 都不同，相当于是 **设置透明度**）

- Collapse
  - 当在表格元素中使用时，此值可删除一行或一列，但是它不会影响表格的布局，被行或列占据的空间会留给其他内容使用。
  - 如果此值被用在其他的元素上，会呈现为 hidden。
  - 当一个元素的 visibility 属性被设置成 collapse 值后，对于一般的元素，它的表现跟 hidden 是一样的。
  - chrome 中，使用 collapse 值和使用 hidden 没有区别。
  - firefox，opera 和 IE，使用 collapse 值和使用 display：none 没有什么区别。

### display:none 和 visibility:hidden 的区别 @@@

1. 渲染树
   - 使用 display:none 后，元素从文档流中消失（渲染树中不存在），因此会引起回流和重绘
   - 使用 visibility:hidden 后，元素仍然在文档流中（渲染树中仍然存在），因此会引起重绘。
2. 继承性
   - display：none 是非继承属性，子孙节点消失是由于元素从渲染树中消失造成，通过修改子孙节点的属性无法显示；
   - visibility：hidden 是继承属性，子孙节点消失是由于继承了 hidden，通过设置 visibility：visible，可以让子孙节点显示。
3. JS 读取属性值
   - 如果在样式文件或页面文件代码中直接用 display:none 对元素进行了隐藏，载入页面后，在没有通过 js 设置样式使元素显示的前提下，使用 js 代码会无法正确获得该元素的 **一些属性**，比如 offSetTop,offSetLeft 等，返回的值会为 0，通过 js 设置 style.display 来使元素显示后才能正确获得这些值
4. SEO
   - 使用 display:none 隐藏的元素不会被百度等搜索网站检索，会影响到网站的 SEO，某些情况下可以使用 left:-100000px 来达到同样效果。
5. 读屏软件
   - display:none 不可读
6. 优先级
   - display:none;visibility:visible 时，元素不可见，渲染树中不存在

### Opacity

CSS3 新增

在结合其他特性的情况下性能比 visibility 更好，涉及到 [层叠上下文](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Understanding_z_index/The_stacking_context)

不继承，但是可以影响自己的后代元素

**透明度也可以设置渐变，使用 rgba 模式**

**属性值：**

- 0 表示完全透明
- 1 表示完全不透明
- 0.5 表示半透明
- opacity 属性在 IE8 及以下的浏览器中不支持，IE8 及以下的浏览器需要使用如下属性代替 alpha(opacity=透明度) 透明度，需要一个 0-100 之间的值
  - 0 表示完全透明
  - 100 表示完全不透明
  - 50 半透明
  - 这种方式支持 IE6，但是这种效果在 IE Tester 中无法测试

#### Rgba 和 Opacity 的区别

rgba 和 opacity 都能实现透明效果，但最大的不同在于 opacity 作用于元素本身以及元素内的所有内容，而 rgba 只作用于元素本身，子元素不会有透明效果。

rgba 是 CSS3 的属性

opacity 也是 CSS3 的属性

### 过滤器（模糊）

css3 新增

模糊整个元素，与 text-shadow 对比

filter: blur(10px);

## Overflow

**overflow 只能作用于块级元素**，即使通过定位改变了行内元素的表现，该属性应该给容器，而不是溢出的元素自己

子元素默认是存在于 **父元素的内容区** 中，理论上讲子元素的最大可以等于父元素内容区大小

如果子元素的大小超过了父元素的内容区，则超过的大小会在父元素以外的位置显示，超出父元素的内容，我们称为溢出的内容

父元素默认是将溢出内容，在父元素外边显示，通过 overflow 可以设置父元素如何处理溢出内容。

**可选值：**

- visible，默认值，不会对溢出内容做处理，元素会在父元素以外的位置显示
- hidden, 溢出的内容，会被修剪，不会显示
- scroll, 会为父元素添加滚动条，通过拖动滚动条来查看完整内容，**该属性不论内容是否溢出，都会添加水平和垂直双方向的滚动条**
- auto，会根据需求自动 **添加滚动条**，需要水平就添加水平，需要垂直就添加垂直 (优先加垂直，显示文本)，都不需要就都不加

换行与横向滚动条：无论什么时候只要内容超过了视觉视口，就会出现换行或者横向滚动条，如果是中文、有单词边界的英文、浮动元素，则会换行，如果没有换行的选择，就只能溢出，然后出现横向滚动条

## 文档流

文档流处在网页的最底层，它表示的是一个页面中的位置，我们所创建的元素默认都处在文档流中

元素在文档流中的有各自的特点

### 块元素

块元素在文档流中会独占一行，块元素会自上向下排列。

块元素在文档流中默认宽度是 auto，一般渲染为父元素的 100%

块元素在文档流中的 **高度默认被内容撑开**

### 内联元素

内联元素在文档流中只占自身的大小，会默认从左向右排列，

如果一行中不足以容纳所有的内联元素，则换到下一行，继续自左向右。

在文档流中，内联元素的宽度和高度默认都被内容撑开

### 脱离文档流

1. 浮动
2. 绝对（固定）定位

在 **文档流** 中，子元素的宽度默认占父元素的全部

块元素脱离文档流以后，高度和宽度都被内容撑开

内联元素脱离文档流以后会变成浮动的块元素

## 浮动

块元素在文档流中默认垂直排列，所以这个三个 div 自上至下依次排开，如果希望块元素在页面中水平排列，可以使块元素脱离文档流

使用 float 来使元素浮动，从而脱离文档流

开启浮动，元素类似 block-inline，表现为 block，但是可以并排，不支持 vertical-align，对齐，而 line-block 时可以对齐的

**可选值：**

- none，默认值，元素默认在文档流中排列
- left，元素会立即脱离文档流，向页面的左侧浮动
- right，元素会立即脱离文档流，向页面的右侧浮动

**描述**

- 当为一个元素设置浮动以后（float 属性是一个非 none 的值），元素会 **立即** 脱离文档流，元素脱离文档流以后，它下边的元素会 **立即** 向上移动

  > 脱离文档流后，元素的宽高由内容撑开

- 元素浮动以后，会尽量向页面的左上或者是右上漂浮，直到遇到 **父元素的边框** 或者 **其他的浮动元素**
- 如果浮动元素上边是一个 **没有浮动的块元素**，则浮动元素不会超过该元素。
- 浮动的元素不会超过他上边 (html 代码的位置) 的 **兄弟元素**，**最多一边齐**
- 可以理解成浮动流，右上方还有空位，但是是塞不下 2 了，所以才会导致换行，3 没有办法流到 2 的前面，所以无法填最右上的空位

 ![1548669602738](/img/user/programming/font-end/primitive/css/css-basic/1548669602738.png)

### 浮动与文字

浮动的元素不会盖住 **文字**，文字会自动环绕在浮动元素的周围，所以我们可以通过浮动来设置文字环绕图片的效果

**本质**: 就是为了文字环绕图片而设置的浮动属性

**表现**: 浮动只提升了半层，除了文字，把另外半层提了上去

### 浮动提升半层级

一个元素，分为盒子层和元素内容层，浮动会使得元素提升到 **文档流的** 元素内容层，表现为提升半层

```html
<style type="text/css">
  *{
    margin: 0;
    padding: 0;
  }
  #d1{
    width: 100px;
    height: 100px;
    float: left;
    background-color: pink;
  }
  #d2{
    width: 100px;
    height: 100px;
    /*position: relative; 开启了定位后，提升一层，会盖着div1，以及div3*/
    background-color: deeppink;
  }
  #d3{
    width: 100px;
    height: 100px;
    background-color: yellow;
    color: red;
  }
</style>

<div id="d1">div1</div>
<div id="d2">div2</div>
<div id="d3">div10</div>
```

![1548736910181](/img/user/programming/font-end/primitive/css/css-basic/1548736910181.png)

div1 开启了浮动，整体提升到了文档流的元素内容层，所以把文字“div2”挤出了 div2 的盒子范围

同时 div1 原本的位置产生了空缺（div1 的盒子层，div1 的元素内容层），所以 div3 补充了 div1 的空缺，和被挤出 div2 的文字重叠在了一起，且由于在 html 结构上 div3 在后，所以遮盖了 div2 的文字

给 div2 开启定位

![1548737232029](/img/user/programming/font-end/primitive/css/css-basic/1548737232029.png)

整体上升一个层级，盒子盖住了 div1，文字盖住了 div3 的元素内容

### 清除浮动 @@@

由于受到 box1 浮动的影响，box2 整体向上移动了 100px

我们有时希望清除掉其他元素浮动对当前元素产生的影响，这时可以使用 clear 来完成功能（只能 **作用于兄弟元素**）

clear 可以用来清除其他浮动元素对 **当前元素** 的影响（高度坍塌，随着文档流的移动等）

清除浮动：清除影响最大的那个兄弟元素浮动造成的影响，清除浮动通过增加外边距来达到相同的效果

**可选值：**

- none，默认值，不清除浮动
- left，清除左侧浮动元素对当前元素影响 **最大的那个** 元素的浮动
- right，清除右侧浮动元素对当前元素影响 **最大的那个** 元素的浮动
- both，清除对他影响 **最大的那个** 元素的浮动

  ![1548676801210](/img/user/programming/font-end/primitive/css/css-basic/1548676801210.png)

- left 和 right 都只考虑一边的对自己影响最大的那个，所以蓝色不会跟着黄色，而是像是被绿色顶着一样，因为：绿色没有浮动的时候，蓝色会被绿色顶着，现在清除了绿色的浮动最蓝色的影响，回到了绿色没有浮动时的状态

**br 标签清除浮动**

- 运用了 br 的 html 属性，而不是 css 样式
- 不兼容 ie6

  ```html
  <div id="wrap">
   <div id="inner">
    
   </div>
   <br clear="all" />
  </div>
  ```

**空标签清浮动**

- ie6 最小高度为 19px 相关问题

  ```html
  <div id="wrap">
   <div id="inner">
    
   </div>
   <div style="clear: both;"></div>
  </div>
  ```

**更多**

- 参考高度坍塌

## 定位 @@@

定位指的就是将指定的元素摆放到页面的任意位置，通过定位可以任意的摆放元素通过 position 属性来设置元素的定位。

**可选值：**

- static：默认值，元素没有开启定位
- relative：开启元素的相对定位
- absolute：开启元素的绝对定位
- fixed：开启元素的固定定位（也是绝对定位的一种）

**定位属性：**

- left 比 right，权重高。有 left 又有 right 的时候，执行 left 的值。
- top 比 bottom，权重高。有 top 又有 bottom 的时候，执行 top 的值。

### 相对定位

当元素的 position 属性设置为 relative 时，则开启了元素的相对定位

**相对定位的特点**

1. 当开启了元素的相对定位以后，而不设置偏移量时，元素不会发生任何变化
2. 相对定位是相对于元素在 **文档流** 中 **原来的位置** 进行定位（左上角原点）

> 这意味着要考虑的 margin-box，border-box 影响大小，margin 影响位置
   >
> border-box 总不能丢下 margin 逃走吧（

3. 相对定位的元素 **不会脱离文档流**
4. 相对定位会使元素 **提升一个层级（定位元素盖着文档流元素）**
5. 相对定位不会改变元素的性质，块还是块，内联还是内联

当开启了元素的定位（position 属性值是一个非 static 的值）时，可以通过 left right top bottom 四个属性来设置元素的偏移量

- left：元素相对于其定位位置的左侧偏移量
- right：元素相对于其定位位置的右侧偏移量
- top：元素相对于其定位位置的上边的偏移量
- bottom：元素相对于其定位位置下边的偏移量

通常偏移量只需要使用 **两个** 就可以对一个元素进行定位，一般选择水平方向的一个偏移量和垂直方向的偏移量来为一个元素进行定位

### 绝对定位

当 position 属性值设置为 absolute 时，则开启了元素的绝对定位

**绝对定位的特点**

1. 开启绝对定位，会使元素 **脱离文档流**
2. 开启绝对定位以后，如果不设置偏移量，则元素的位置 **不会发生变化，保持原本的偏移量** @@@
3. 绝对定位会改变元素的性质：
	- 内联元素变成块元素
	- 块元素的宽度和高度默认都被内容撑开
	- 宽度和高度的百分比参照的都是包含块
	- 也就是：内联元素和块元素的表现一致，没有内联和块的区分了
1. 绝对定位会使元素 **提升一个层级**
	- 绝对定位是相对于离他最近的 **开启了定位** 的 **祖先元素** 进行定位的（开了相对定位或者绝对定位都是可以的，**一般情况，开启了子元素的绝对定位都会同时开启父元素的相对定位**）
	- 如果所有的祖先元素都没有开启相对定位，则会相对初始包含块（一个大小为视口大小的块）进行定位

### 固定定位

当元素的 position 属性设置 fixed 时，则开启了元素的固定定位

固定定位也是一种绝对定位，它的大部分特点都和绝对定位一样

**注意**: 当元素祖先的 `transform` 属性非 `none` 时，容器由视口改为该祖先。

https://www.imooc.com/article/67784

**不同的是：**

- 固定定位永远都会相对于浏览器窗口进行定位
- 固定定位会固定在浏览器窗口某个位置，不会随滚动条滚动
- IE6 不支持固定定位
- 通过绝对定位模拟固定定位

### Sticky @@@

粘性定位可以被认为是相对定位和固定定位的混合。元素在跨越特定阈值前为相对定位，之后为固定定位。例如：

<https://www.cnblogs.com/coco1s/p/6402723.html>

<https://www.cnblogs.com/xiaohuochai/p/8391288.html>

<https://www.zhangxinxu.com/wordpress/2018/12/css-position-sticky/>

<https://segmentfault.com/a/1190000007938006>

#### 概述

这是一个结合了 `position:relative` 和 `position:fixed` 两种定位功能于一体的特殊定位，适用于一些特殊场景。

元素先按照普通文档流定位，然后相对于该元素在流中的 flow root（BFC）和 containing block（最近的块级祖先元素）定位。

而后，元素定位表现为在跨越特定阈值前为相对定位，之后为固定定位。

这个特定阈值指的是 top, right, bottom 或 left 之一，换言之，指定 top, right, bottom 或 left 四个阈值其中之一，才可使粘性定位生效。否则其行为与相对定位相同。

#### 示例

嗯，上面的文字描述估计还是很难理解，看看下面这张 GIF 图，想想要实现的话，使用 JS + CSS 的方式该如何做

按照常规做法，大概是监听页面 scroll 事件，判断每一区块距离视口顶部距离，超过了则设定该区块 `position:fixed`，反之去掉。

而使用 `position:sticky` ，则可以非常方便的实现：

简单描述下生效过程，因为设定的阈值是 `top:0` ，这个值表示当元素距离 **页面视口**（Viewport，也就是 fixed 定位的参照）顶部距离大于 0px 时，元素以 relative 定位表现，而当元素距离页面视口小于 0px 时，元素表现为 fixed 定位，也就会固定在顶部。

不理解可以再看看下面这两张示意图（top:20px 的情况，取自开源项目 [fixed-sticky](https://github.com/filamentgroup/fixed-sticky)）：

距离页面顶部大于 20px，表现为 `position:relative`：

![sticky-top-off](/img/user/programming/font-end/primitive/css/css-basic/7dabb642-f3a7-11e6-8eba-e48cc56642dc.gif)

距离页面顶部小于 20px，表现为 `position:fixed`：

![sticky-top-on](/img/user/programming/font-end/primitive/css/css-basic/c0cfee66-f3a7-11e6-995b-0b497360ca2b.gif)

#### 生效规则

1. 须指定 top, right, bottom 或 left 四个阈值其中之一，才可使粘性定位生效。否则其行为与相对定位相同。
   - 并且 `top` 和 `bottom` 同时设置时，`top` 生效的优先级高，`left` 和 `right` 同时设置时，`left` 的优先级高。
2. 设定为 `position:sticky` 元素的任意父节点的 overflow 属性必须是 visible，否则 `position:sticky` 不会生效。这里需要解释一下：
   - 如果 `position:sticky` 元素的任意父节点定位设置为 `overflow:hidden`，则父容器无法进行滚动，所以 `position:sticky` 元素也不会有滚动然后固定的情况。
   - 如果 `position:sticky` 元素的任意父节点定位设置为 `position:relative | absolute | fixed`，则元素相对父元素进行定位，而不会相对 viewprot 定位。
3. 达到设定的阀值。这个还算好理解，也就是设定了 `position:sticky` 的元素表现为 `relative` 还是 `fixed` 是根据元素是否达到设定了的阈值决定的。

### 面试题

```css
{
  /* 全局值 */
  position: inherit;
  position: initial;
  position: unset;
}
```

## 层级 @@@

如果定位元素的层级是一样，则 **下边** 的元素会盖住 **上边的**

通过 z-index 属性可以用来设置元素的层级，可以为 z-index 指定一个 **正整数** 作为值，该值将会作为当前元素的层级层级越高，越优先显示

对于没有开启定位的元素不能使用 z-index @@@

父元素的层级再高，也不会盖住子元素

浮动提升半级

相对定位使得元素提升一个层级

绝对定位也是一个层级

z-index 为 1，层级比定位元素高，z-index 为 -1，比浮动的 开启定位的都低

堆叠上下文 详见 CSS2.1

# 背景

## Background-color

background-color 会设置元素的背景色

**默认值： transparent** **不可继承**

## Background-image

使用 background-image 来设置背景图片

语法：background-image:url(相对路径);

- 如果背景图片大于元素，默认会显示图片的左上角
- 如果背景图片和元素一样大，则会将背景图片全部显示
- 如果背景图片小于元素大小，则会默认将背景图片平铺以充满元素

可以同时为一个元素指定背景颜色和背景图片，这样背景颜色将会作为背景图片的底色

一般情况下设置背景图片时都会同时指定一个背景颜色

如果指定多张背景图片，先指定的图片会在后指定的图片的基础上进行绘制

![1548680092167](/img/user/programming/font-end/primitive/css/css-basic/1548680092167.png)

## Background-repeat

用于设置背景图片的重复方式

**可选值：**

- repeat，默认值，背景图片会双方向重复（平铺）
- no-repeat ，背景图片不会重复，有多大就显示多大
- repeat-x， 背景图片沿水平方向重复
- repeat-y，背景图片沿垂直方向重复

## Background-position

背景图片默认是贴着元素的 `padding-box` 左上角显示通过 background-position 可以调整背景图片在元素中的位置

**可选值：**

- 该属性可以使用 top right left bottom center 中的 **两个值** 来指定一个背景图片的位置
  - top left 左上 ，bottom right 右下
  - 如果只给出一个值，则第二个值默认是 center
- 直接指定两个偏移量（像素或者百分比），
  - 第一个值是水平偏移量
    - 如果指定的是一个正值，则图片会向右移动指定的像素
    - 如果指定的是一个负值，则图片会向左移动指定的像素
  - 第二个是垂直偏移量
    - 如果指定的是一个正值，则图片会向下移动指定的像素
    - 如果指定的是一个负值，则图片会向上移动指定的像素
- 百分比：参照尺寸为背景图片定位区域的大小减去背景图片的大小
  - 背景区域小于背景图片，就是负值，会向左上方偏移
- background-position 原理
  - > https://www.cnblogs.com/starof/p/4610984.html
  - 其实所有的居中 API 本质都是如此，首先：左外边距为父元素的宽度的一半，然后在减去自身的宽度的一半，然后提取公因而已
  - text-align:center; 父元素宽度一半减去行内元素宽度一半
  - 有点类似于富裕空间的分配，父元素的减去子元素的，剩余就是富裕空间，然后分配在两边，本质也是如此吧

## Background-attachment

background-attachment 用来设置背景图片是否随页面一起滚动

**可选值：**

- scroll，默认值，背景图片随着窗口滚动
- fixed，背景图片会固定在浏览器窗口的某一位置，不随页面滚动
- 不随窗口滚动的图片，我们一般都是设置给 body，而不设置给其他元素，其他元素随着页面滚动消失，fixed 的背景图片也无法显示
- 建议不要使用，性能差，用 img 和相关样式来代替即可

## Demo 伪类切换背景图片

做完功能以后，发现在第一次切换图片时，会发现图片有一个非常快的闪烁，这个闪烁会造成一次不佳的用户体验。

**产生问题的原因：**

- 背景图片是以外部资源的形式加载进网页的，浏览器每加载一个外部资源就需要单独的发送一次请求，但是我们外部资源并 - 不是同时加载，浏览器会在资源被使用才去加载资源
- 我们这个练习，一上来浏览器只会加载 link.png 由于 hover 和 active 的状态没有马上触发，所以 hover.png 和 active.png 并不是立即加载的
- 当 hover 被触发时，浏览器才去加载 hover.png
- 当 active 被触发时，浏览器才去加载 active.png
- 由于加载图片需要一定的时间，所以在加载和显示过程会有一段时间，背景图片无法显示，导致出现闪烁的情况
- 为了解决该问题，可以将三个图片整合为一张图片，这样可以同时将三张图片一起加载，就不会出现闪烁的问题了，然后在通过 background-position 来切换要显示的图片的位置，这种技术叫做图片整合技术（CSS-Sprite**雪碧图、精灵图**）

**优点：**

- 将多个图片整合为一张图片里，浏览器只需要发送一次请求，可以同时加载多个图片，提高访问效率，提高了用户体验。
- 将多个图片整合为一张图片，减小了图片的总大小，提高请求的速度，增加了用户体验

## 简写属性 Background

通过该属性可以同时设置所有背景相关的样式

没有顺序的要求，谁在前睡在后都行也没有数量的要求，不写的样式就使用默认值

`background: #bfa url(img/3.png) center center no-repeat fixed;`

一般建议 url 和 repeat 用简写，其他全部写在下面，不要分散设定背景的相关样式

**默认值：**

- background-image: none
- background-position: 0% 0%
- background-size: auto auto
- background-repeat: repeat
- background-origin: padding-box
- background-clip: border-box
- background-attachment: scroll
- background-color: transparent
- 顺序无关，不可继承

## 注意： @@@

背景颜色、图片，除了 margin 都会绘制，但是背景图片从 padding 开始绘制，从 borderbox 开始裁剪

只是会被边框的样式遮挡，设置了透明度就可以看见了，

![1548681192317](/img/user/programming/font-end/primitive/css/css-basic/1548681192317.png)

```css
#inner{
  width:200px;
  height: 200px;
  padding:50px;
  border:50px solid rgba(0,0,0,.2);
  margin:10px;
  background: deeppink url("./robot.png") repeat;
}
```

## CSS3 新增

### Background-origin

**默认值：**padding-box

- 设置背景 **图片** 的渲染的起始位置，`background-position` 的起始点；

  > 背景颜色默认是铺满 border-box，而此处定义的仅仅是渲染的起始位置，如果背景是 repeat 的，图片也会铺满 border-box

- border-box
- padding-box
- content-box

**示例**

```css
  .box1{
    margin:0 auto;
    width: 200px;
    height: 200px;
    padding:20px;
    border:20px solid rgba(0,0,0,0.5);
    background-color:deeppink;
    background-origin:content-box;
    background-repeat: no-repeat;
    background-image:url('./img/img2-middle.jpg');
    background-size: 50%;
  }
```

  ![1558999541823](/img/user/programming/font-end/primitive/css/css-basic/1558999541823.png)

- background-repeat:repeat;

  ![1558999583253](/img/user/programming/font-end/primitive/css/css-basic/1558999583253.png)

### -webkit-background-clip

**默认值：**border-box

- 设置背景裁剪位置（包括 **背景颜色和背景图片**）

  ```css
  div{
    margin:0 auto;
    width: 200px;
    height: 200px;
    padding:100px;
    border:20px solid rgba(0,0,0,0.5);
    background-color:deeppink;
    background-clip:border-box;
  }
  ```

- border-box

  ![1554986337264](/img/user/programming/font-end/primitive/css/css-basic/1554986337264.png)

- padding-box

  ![1554986325585](/img/user/programming/font-end/primitive/css/css-basic/1554986325585.png)

- content-box

  ![1554986306818](/img/user/programming/font-end/primitive/css/css-basic/1554986306818.png)

- `-webkit-background-clip:text;`

  ![1548681359672](/img/user/programming/font-end/primitive/css/css-basic/1548681359672.png)

### Background-size

设置背景图片大小

**初始值：auto auto**

**不可继承**

**属性值**

- auto：以背景图片的比例缩放背景图片
- `<length>`：`<length>` 值，指定背景图片大小，不能为负值。
- 百分比：指定背景图片相对背景区（background positioning area）的百分比。背景区由 background-origin 设置，默认为盒模型的内容区与内边距
- contain：
  - 缩放背景图片以完全装入背景区，可能背景区部分空白。
  - `contain` 尽可能的缩放背景并保持图像的宽高比例（图像不会被压缩）。
  - 该背景图会填充所在的容器。当背景图和容器的大小的不同时，容器的空白区域（上/下或者左/右）会显示由 background-color 设置的背景颜色。
- cover：
  - 缩放背景图片以完全覆盖背景区，可能背景图片部分看不见。
  - 和 `contain` 值相反，`cover` 值尽可能的缩放背景图像并保持图像的宽高比例（图像不会被压扁）。该背景图以它的全部宽或者高覆盖所在容器。当容器和背景图大小不同时，背景图的 左/右 或者 上/下 部分会被裁剪。

**注意：**

- 单值时，这个值指定图片的宽度，图片的高度隐式的为 auto
- 两个值: 第一个值指定图片的宽度，第二个值指定图片的高度
- 100% 100% 和背景区一致

**示例**

```css
  .box1{
    margin:0 auto;
    width: 200px;
    height: 200px;
    padding:20px;
    border:20px dashed rgba(0,0,0,0.5);
    background-color:deeppink;
    background-origin:padding-box;
    background-image:url('./img/img2-middle.jpg');
    background-repeat:no-repeat;
    background-size: 100% 100%;
  }
```

  ![1559000169915](/img/user/programming/font-end/primitive/css/css-basic/1559000169915.png)

- backroung-origin:content-box;

  ![1559000201253](/img/user/programming/font-end/primitive/css/css-basic/1559000201253.png)

- backroung-origin:content-box;background-size: contain;

  ![1559000621960](/img/user/programming/font-end/primitive/css/css-basic/1559000621960.png)

- backroung-origin:content-box;background-size: cover;

  ![1559000601274](/img/user/programming/font-end/primitive/css/css-basic/1559000601274.png)

# 其他样式

## @import

@import [CSS](https://developer.mozilla.org/en-US/docs/Web/CSS)[@规则](https://developer.mozilla.org/en-US/docs/Web/CSS/At-rule)，用于从其他样式表导入样式规则。

这些规则必须 **先于** 所有其他类型的规则，[`@charset`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@charset) 规则除外; 因为它不是一个 [嵌套语句](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Syntax#nested_statements)

@import 不能在 [条件组的规则](https://developer.mozilla.org/zh-CN/docs/Web/CSS/At-rule#Conditional_Group_Rules) 中使用。

因此，用户代理可以避免为不支持的媒体类型检索资源，作者可以指定依赖媒体的@import 规则。这些条件导入在 URI 之后指定逗号分隔的 [媒体查询](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries)。在没有任何媒体查询的情况下，导入是无条件的。指定所有的媒体具有相同的效果。

## @import 和 Link 引入样式的区别

### 从属关系区别

`@import` 是 CSS 提供的语法规则，只有导入样式表的作用；`link` 是 HTML 提供的 **标签**，不仅可以加载 CSS 文件，还可以定义 RSS、rel 连接属性等。

### 加载顺序区别

加载页面时，`link` 标签引入的 CSS 被同步加载；`@import` 引入的 CSS 将在页面加载完毕后被加载。

### 兼容性区别

`@import` 是 CSS2.1 才有的语法，故只可在 IE5+ 才能识别；`link` 标签作为 HTML 元素，不存在兼容性问题。

### DOM 可控性区别

可以通过 JS 操作 DOM ，插入 `link` 标签来改变样式；由于 DOM 方法是基于文档的，无法使用 `@import` 的方式插入样式。

### 权重区别

写在后边都样式会覆盖前面的样式

link 先于@import 加载，是不是也先于@import 渲染呢？

@import 引入的样式后被加载，却会在加载完毕后置于样式表引入的位置，最终渲染时自然会被下面的同名样式层叠。

### 媒体查询

link 定义的方式：

```html
<link rel="stylesheet" type="text/css" href="print.css" media="print"/>
```

@import：

```css
@import url("print.css") print;
@import "common.css" screen, projection;
@import url('landscape.css') screen and (orientation:landscape);
@import url('mobile.css') (max-width: 680px);
```

这里要注意的是，**不论是 link 还是 import 方式，会下载所有 css 文件，然后根据媒体去应用 css 样式**，而不是根据媒体去选择性下载 css 文件。

### @import 影响页面性能

影响浏览器的并行下载

多个@import 导致下载顺序紊乱

不要使用

参考： https://www.qianduan.net/high-performance-web-site-do-not-use-import/ca
