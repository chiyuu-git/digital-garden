---
{"aliases":[],"tags":[],"review-dates":["2019-05-04","2019-05-29","2019-10-04","2023-04-28"],"dg-publish":true,"date-created":"2022-09-03-Sat, 10:07:33 pm","date-modified":"2023-08-04-Fri, 5:26:11 pm","permalink":"/programming/front-end/primitive/css/css-3/","dgPassFrontmatter":true}
---


# 常用 API

## Calc

### 介绍

CSS 函数 `calc()` 可以用在任何一个需要 `<length>`、`<frequency>`、`<angle>`、`<time>`、`<number>`、`<integer>` 的地方。有了 `calc()，` 你就可以通过计算来决定一个 CSS 属性的值了。

你还可以在一个 `calc() ` 内部嵌套另一个 `calc()` ，里面的 ` calc() ` 会被简单地视为加了括号。

```css
/* property: calc(expression) */
width: calc(100% - 80px);
```

### 表达式

该表达式可以使用以下运算符任意组合（使用正常的运算符优先级顺序）：

+ + 加法
+ - 减法
+ \* 乘法，乘数中至少要有一个是 `<number>` 类型的
+ / 除法，被除数（/右面的数）必须是 `<number>` 类型的

表达式中的操作数可以使用任意语法种类的长度。如果你愿意，你可以在一个表达式中混用多种不同的长度单位。在需要时，你还可以使用小括号来调整计算顺序。

```css
main { 
  position: absolute; 
  top: calc(50% - 3em); 
  left: calc(50% - 9em); 
  width: 18em; height: 6em; 
}
```

**注意：**

+ `+ ` 和 `- ` 运算符的两边必须始终要有空白符。
+ 比如 `calc(50% -8px) ` 会被解析成为一个无效的表达式：一个百分比后跟一个负数长度值。
+ 而 `calc(8px + -50%) ` 会被解析成为一个长度后跟一个加号再跟一个负百分比。
 + `*` 和 `/` 运算符不需要空白符，但考虑到统一性，仍然推荐加上空白符。

## @font-face 自定义字体

允许网页开发者为其网页指定在线字体。 通过这种作者自备字体的方式，可以消除对用户电脑字体的依赖

+ font-family：所指定的字体名字将会被用于 font 或 font-family 属性
+ src：字体资源

注意：不能在一个 CSS 选择器中定义 @font-face

```css
@font-face {
 font-family:"damu";
 src: url(damu/GOUDYSTO.TTF);
}
```

Adobe illustrator

矢量图编辑软件

FontLab

- 字体编辑软件
- 可以用于把矢量图与字体关联

字体兼容处理网站

- <https://www.fontsquirrel.com/tools/webfont-generator>
- 字符集生成 css 自定义字体

icomoon 字体图标

- <https://icomoon.io/#home>
- svg 直接生成 css 自定义字体

字体图标基本思路

1. 设计一套矢量图
2. 将不同的矢量图绑定到不同的字符上生成自定义字体
3. 一般通过工具或者站点来处理
4. 在页面中运用

## Clip-path

### inset()

+ 接受的值和 margin border padding 类似，表示裁剪的上右下左的宽度
+ https://developer.mozilla.org/zh-CN/docs/Web/CSS/clip-path

### Content-visibility

Chome 85 推出了一个新的 css 属性：content-visibility，大致的用途就是设置了该属性的模块，只有进入屏幕内才会开始渲染。文中的例子性能从 232ms -> 30ms 觉得和我们最近做的东西比较相关，所以小分享一下： https://web.dev/content-visibility/ https://drafts.csswg.org/css-contain-2/#cont

# 渐变

**渐变不是颜色是图片**

## 线性渐变

```css
background-image: linear-gradient(  [ <angle> | <side-or-corner> ,]? <color-stop> [, <color-stop>]+ );
```

### 指定渐变角度

默认从上到下发生渐变

#### 使用关键字

linear-gradient(to 结束的方向,red,blue);

```css
background-image:linear-gradient(to top left,red,blue);
/*最多指定两个值，八个方向可控*/
```

#### 使用角度

linear-gradient(角度,red,blue);

指定的是渐变的角度，而不是渐变边界的角度

`[-180deg,180deg]`

```css
background-image:linear-gradient(0deg,red,blue);
```

![1548820163032](/img/user/programming/front-end/primitive/css/css-3/1548820163032.png)

从上图可以，如果角度是负值，就会从右下角到左上角发生渐变

### 线形渐变的构成

为了构建出平滑的渐变，`linear-gradient()` 函数构建一系列垂直于渐变线的着色线，每一条着色线的颜色则取决于与之垂直相交的渐变线上的 **色点**

渐变线由包含渐变图形的 **容器的中心点** 和 **一个角度** 来定义的。

当颜色中间点的位置被隐式定义，它被放置在位于它之前的点和位于它之后的点之间的中间位置处。利用 length 或者 percentage 数据类型可以显示定义一个位置。

![gradient-diagram](/img/user/programming/front-end/primitive/css/css-3/gradient-diagram.png)

https://www.zhangxinxu.com/wordpress/2013/09/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3css3-gradient%E6%96%9C%E5%90%91%E7%BA%BF%E6%80%A7%E6%B8%90%E5%8F%98/#comments

### 指定终止颜色及渐变边界

终止色就是你想让浏览器去平滑的过渡过去，并且你必须指定 **至少两种**，当然也会可以指定更多的颜色去创建更复杂的渐变效果。

```css
background-image:linear-gradient(red,yellow,blue);
/*从红到黄到蓝的渐变*/
```

#### 颜色节点的分布

第一个不写为 0%，最后一个不写为 100%

linear-gradient(red 长度或者百分比,blue 长度或者百分比);

```css
background-image:linear-gradient(90deg,red 10%,orange 15%,yellow 20%,green 30%,blue 50%,indigo 70%,violet 80%);
```

从 10% 的位置开始渐变，由红到橙，从 15% 的位置开始第二次渐变，由橙到黄

![1548822500054](/img/user/programming/front-end/primitive/css/css-3/1548822500054.png)

#### 透明度也可以设置渐变，使用 Rgba 模式

```css
background-image:linear-gradient(90deg,rgba(255,0,0,0) 50%,rgba(255,0,0,0.5),rgba(255,0,0,1) 60%);
```

#### Repeating-linear-gradient

```css
background-image:repeating-linear-gradient(90deg,red 0,blue 25%)
```

![1548822805445](/img/user/programming/front-end/primitive/css/css-3/1548822805445.png)

如果没有指定渐变区域，则重复渐变会失效，变成普通的渐变

repeating-linear-gradient(90deg,red ,blue )

#### Demo 发廊灯

- 重复渐变属性不会重复纯色块，所以需要黑到黑的渐变，白到白的渐变

#### Demo 光斑动画

## 径向渐变

radial-gradient() 函数创建一个 `<image>`，用来展示由原点（渐变中心）辐射开的颜色渐变

### 默认均匀分布

radial-gradient(red,blue);

### 不均匀分布

radial-gradient(red 50%,blue 70%);

### 改变渐变的形状

radial-gradient(circle ,red,blue)

- circle
- ellipse（默认为椭圆）

  ![1548824473537](/img/user/programming/front-end/primitive/css/css-3/1548824473537.png)

### 渐变形状的大小

- radial-gradient(closest-corner circle ,red,blue)
- closest-side，扩散到最近边为止
- farthest-side
- closest-corner
- farthest-corner，最远角（默认值）

### 改变圆心

- radial-gradient(closest-corner circle **at** 10px 10px,red,blue);
- at 不可省略

  ![1548824657974](/img/user/programming/front-end/primitive/css/css-3/1548824657974.png)

## 角度渐变

+ conic-gradient

# 多列布局（分栏布局）

## 栏目宽度

column-width 指定每一栏的宽度（这是多列布局的第一种分法）

## 栏目列数

column-count 指定要多少栏（这是多列布局的第二种分法）

## 栏目距离

column-gap

## 栏目间隔线

column-rule

```css
#wrap{
 border: 1px solid;
 width: 600px;
 margin: 0 auto;
 /*column-width:100px;*/
 column-count:2;
 column-gap:40px;
 column-rule:1px solid red;
}
```

类似于 word 的分栏

## Break-inside

https://developer.mozilla.org/zh-CN/docs/Web/CSS/break-inside

# CSS 规范

由 W3C 所定义

CSS3 是级联样式表 / 层叠样式表 (Cascading Style Sheets) 语言的最新进化，旨在扩展 CSS2.1。

狭义上，我们可以认为 css3 是所有级别为 3 的 css 特性的集合

广义上，我们应该认为 css3 是 css2.0 后出现的所有 css 相关特性的集合

CSS Level 2 经历了 9 年的时间 (从 2002 年 8 月到 2011 年 6 月) 才达到 Recommendation(推荐) 状态。主要的原因是被一些 secondary features(次要特性) 拖了后腿。

为了加快那些已经确认为没有问题的特性的标准化速度， W3C 的 CSS Working Group(CSS 工作组) 作出了一项决定， 将 CSS 划分为许多小组件，称之为 **模块** 。

这些模块彼此独立， 按照各自的进度来进行/列标准化。其中一些已经是 W3C Recommendation 状态， 也有一些还仅仅是 early Working Drafts(早期工作草案)。 当新需求被确认后， 新的模块也同样被添加进来。

再也没有 css 规范，只存在 css 模块及其模块级别

(https://www.w3.org/Style/CSS/current-work)

## 模块级别的判断

1. 如果模块在 CSS 2.1 就有相关的内容，那么这些模块就从 level 3 开始。
2. 如果是完全新的属性（比如 Flexbox），就直接从 level 1 开始。
2. 一个模块的级别和它所在 CSS 的版本无关，即不管它是 CSS 2.1 的内容还是完全新的属性。
2. 可能会看到类似 css4-backgrounds 这样的写法，其实代表的是 CSS Background & Borders Level 4，即 4 表示的是模块的等级。

## CSS Working Group(CSS 工作组)

W3C（World Wide Web Consortium，万维网联盟）实际上并不制定标准。

对于 W3C 旗下的各个工作组（Working Groups, WG）来说，W3C 更像是一个论坛，聚集各种兴趣团体并让他们为某个标准而努力。

当然，W3C 并不只是作为整个论坛的观察者：它制定整个论坛的基本规则并观察标准制定的整个流程。

## 一份 Css 标准要经历的一些流程

标准不是凭空捏造的，制定标准也不是闭门造车。CSS WG 所有的提交都是透明的，所有的交流都是对公众开放的（https://lists.w3.org/Archives/Public/www-style/）

标准确定一般有 6 个阶段，其中两个是过渡阶段：（https://www.w3.org/2005/10/Process-20051014/tr#maturity-levels）

1. 编辑草案 Editor's Draft (ED)
   - 这个是规范的开始阶段，一个 CSS 属性或者选择器被提出来，并在 CSSWG 内部研究。如果小组成员同意这个属性可以正式推出，它就能进入下一阶段 。对于处于这一阶段的规范，不附加任何必要条件，也不保证会被 WG 批准。此外，这也是修改版的第一个阶段：所有的修改内容首先要经过 ED，然后才能被发布。
1. 工作草案 Working Draft (WD)

- 编辑草案后是工作草案，标准的设计阶段。小组反复处理来自 CSSWG 内部和来自小组外部的反馈，这个阶段有两个结果：一是可能会因为技术困难或者可能会引起其他问题而使新属性被完全拒绝；二是规范会通过这个阶段，并会作为第一次公开工作草案（ First Public Working Draft (FPWD)）发布，后面还会有数个工作草案，会处理来自 CSSWG 内部和小组外部更广泛社会的反馈。

3. 最后通告工作草案– Last Call Working Draft (LCWD) 过渡阶段
   - 这是第一个过渡阶段，当规范开始考虑从工作草案进入到下一个阶段时，将会对新属性的一些小改动的反馈设置一个截止日期，LCWD 即是日期截至后最后的一次公开草案处理。

4. 候选推荐标准 Candidate Recommendation (CR)
   - 规范会在这个阶段通过完整的测试，测试人员来自 CSSWG 以及被选为实现这个规范的浏览器生产商 (Chrome, Safari, Firefox, Opera, 等等)。为了继续进入下一阶段，CSSWG 会推出两个正确的实现规范。

5. 建议推荐标准– Proposed Recommendations (PR) 过渡阶段
   - 当到达这个阶段，W3C 全球资源小组：W3C 咨询委员会（W3C Advisory Committee），决定这个规范是否会继续进入下一个阶段。这个阶段一般很少有异议出现，所以也是一个过渡阶段而已。

6. 推荐标准 Recommendation (REC)
   - 如果规范到达这个阶段，说明规范已经考虑完备并可以让浏览器商实现，W3C 和 CSSWG 对这个规范的讨论处理不再活跃，只做一些必要的维护。

## 标准（规范）什么时候是稳定的

规范的稳定性基本和它所在的流程阶段没有关系。当规范特性已经开始传播开来，并因为向后兼容性不能改变时，它才是稳定的，这个阶段可能会在 ED 规范阶段或者 CR 阶段，这才是稳定性评判的正确方法，而不是 W3C 的标准发布流程。

## 浏览器前缀

有些 CSS 模块已经十分稳定并满足了 CSSWG 规定的三个推荐级别之一：Candidate Recommendation(候选推荐)， Proposed Recommendation(建议推荐) 或 Recommendation(推荐)。

这表明这些模块已经十分稳定，使用时也不必添加前缀， 但是一些特性还是有可能在 Candidate Recommendation 阶段被放弃。

## 总结

级联样式表 (CSS) 再也没有传统意义上的版本了; 相反，它有级别。每个级别的 CSS 都建立在以前的、细化的定义和添加特性上。每个高级级别的特性集都是任何较低级别的超集，因此，符合更高级别 CSS 的用户代理也符合所有较低的级别。

# 什么是 HTML5

现在就连 html css 这些规范也是模块化的开发了，那些规范制定好了，有用的就更新，然后浏览器厂商直接就可以开始准备实现，不再像以前那种准备好一套全部规范再推出，所以以后也没有 css4 并不是第 4 版本，html5 也不是第 5 版本，也更不会有 html6，

到了 h5 和 c3，版本的更新就停止了，模块化的开发，叫 html 和 css 更加准确，12345 只是这个模块的级别
