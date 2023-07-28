---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/css/page-layout/"}
---


# 布局理论

## 流体 + 固定

```less
.line-height_liquid{
  box-sizing: border-box;
  width:auto;
  padding: 0 15px;
  .line-height_solid{
    width:980px;
    margin: 0 auto;
    background-color: pink;
  }
}
```

## 基础布局方式

### 普通/文档流 布局

早期 `<table>`， 后来 `<div>` ，再后来 HTML5 语意化标签按照自上而下的方式顺次排布。

### Float 布局

最初设计目的是用于图文环绕排版、不过目前常用于左右布局。

### 绝对布局

保持与目标元素（`position` 不为 `static` 的最近父元素）的绝对距离，使用场景有顶部固定的广告栏，瀑布流等等。

### Grid 网格布局

较为系统的布局方案

### Flex 弹性盒子布局

较为系统的布局方案

# 常见的布局概念以及实现方法

## 静态布局

### 概念

网页布局始终按照固定的布局和尺寸来显示，不使用 Flex 之类的弹性盒子方案。

### 实现方法

- 普通/文档流 布局
- Float 布局
- 绝对布局
- 对于 PC：一般居中布局，所有样式使用绝对宽度/高度 (px)，屏幕宽高有调整时，使用滚动条来查阅被遮掩部分；
- 对于移动设备：另外建立移动网站，单独设计一个布局，使用不同的域名如 wap.或 m., 在 `<viewport meta>` 标签上设置 `width`，页面的各个元素也采用 `px` 作为单位。通过用 JS 动态修改标签的 `initial-scale` 使得页面等比缩放，从而刚好占满整个屏幕

#### 优点

这种布局方式对设计师和 CSS 编写者来说都是最简单的，亦没有兼容性问题。

#### 缺点

- 显而易见，不能根据用户的屏幕尺寸做出不同的展现：
- 窄屏幕滚动条体验很差
- 宽屏有大片空白，不利于空间利用

## 流式布局

### 概念

流式布局目的是在不同大小的设备上 **满屏呈现同样网页**。它是用于解决类似的设备不同分辨率之间的兼容 (一般分辨率差异较少)

### 实现

实现方法：

- 允许网页宽度自动调整：`<meta name="viewport" content="width=device-width, initial-scale=1" />`
- 不使用绝对尺寸（包括容器/字体/图片），使用百分比、em、rem、vw、vh 等
- 可使用 flex 等弹性盒子（不要使用 px 定尺寸）

### 优点

页面左右满屏

### 缺点

- 使用百分比定义，所以在大屏幕的手机/Pad 下（或者横屏下）显示效果会变成有些页面元素被拉的很大，但是内容数量却不变，显得稀疏不紧凑，空间利用率低下。
- 如果文字也按照百分比放大，则整体效果会非常不协调（老人机效果）。

+ 主流的设计原则: 大屏看的应该更多, 而不是更大

## 自适应布局

### 概念

自适应布局是为不同的屏幕分辨率分别定义不同的布局。改变屏幕分辨率可以切换不同的静态布局（页面元素位置可能发生改变），但在每个静态布局中，页面元素不随窗口大小的调整发生变化。

自适应布局看作是静态布局的一个合集。

### 实现方式

静态布局方法

分辨率 detector（media query/server-side detector/UA）

### 优点

自适应布局 **页面里面元素的位置会变化，很好的解决了流式布局中的大屏空间利用率不高弊端**。

### 缺点

单个布局容器无法灵活伸缩，未触发布局切换的情况下，容器仍然容易出现静态布局中提到的问题。

## 响应式布局

### 概念

响应式布局同样分别为不同的屏幕分辨率定义布局。改变屏幕分辨率可以通过 CSS Media query 实时地切换不同的布局（页面元素位置可能发生改变），在每个布局中，页面元素会随窗口大小的调整发生流式布局中的自动尺寸变化。

### 实现方式

- 流式布局
- CSS media query

### 优点

响应式布局 **融合了流式布局和自适应布局的优势**。

### 缺点

CSS 代码繁琐，对于特定的设备有较多冗余，适用于对于各个终端（特别是移动端）性能要求不高的 Blog Dos 站点。

# 关于网页设计

布局的目的，归根结底，都是为了网页设计服务。那么就顺带聊一聊和响应式设计（RWD）和自适应设计（AWD）。

## 自适应设计（AWD）

### 概念

**“All types of web design layouts”**, **“different versions to different devices”**,

自适应设计是通过**服务端检测设备类型、从 site 的不同版本中选择最合适该设备类型的设计布局/尺寸的版本进行展示。**它可以使用到所有（包括响应式布局）布局方案。

核心是 @media ，通过多套 **静态布局** 方案，适配不同设备

### 实现方式

- server-side detection
- different versions to different devices
- 对于 PC: 可使用流式布局；
- 对于 Mobile: 可使用流式布局。推荐一个 Rem 解决方案：
    - 设置元素（可以包括字体等）大小为 `rem` （`rem` 是以跟元素 `font-size` 为基准的单位）
    - 按照屏幕宽度的不同，JS 动态设置 `<html>` 的 `font-size` 大小，元素同样会按照屏幕宽度等比例放大缩小

## 响应式设计（RWD）

### 概念

**“Progressive enhancement”**, **“one-fits-all”**

响应式设计基于响应式布局，**使用同一套页面在各种各样不同大小的设备上进行大小合适、布局（甚至功能）合理的展现。**

响应式设计会根据识别屏幕宽度对于展示的具体内容块进行位置调整，甚至展示和隐藏。

### 实现方式

响应式布局

特性检测 （用于网页功能的渐进增强）

## RWD 和 AWD 的异同

### 相同点

- 均针对不同的分辨率/device 采用不同的样式和布局达到页面展示最优
- 布局方式本质没有差别（AWD 也 including responsive layout）

### 不同点

- 前者强调同一套页面多端兼容展示，而后者给出多套页面，对于不同 device 进行了分类处理
- 前者是通过 CSS Media query 进行分辨率检测，可以实时的响应浏览器尺寸变化，改变元素尺寸/布局，而后者一般是 server side detection，一次性渲染既定布局和样式

>  这里的 AWD 是基于 SSR?

# 响应式布局

## 响应式布局基础

响应式设计实现通常会从以下几方面思考：

+ 组合流式布局、弹性盒子（包括图片、表格、视频）和媒体查询等技术；
+ 使用百分比布局创建流式布局的弹性 UI，同时使用媒体查询限制元素的尺寸和内容变更范围；
+ 使用相对单位使得内容自适应调节；
+ 选择断点，针对不同断点实现不同布局和内容展示；

## 响应式设计模式

目前，响应式设计实践大致可总结为五类：mostly fluid、column drop、layout shifter、tiny tweaks 和 off canvas，通常，我们选择其中的某一种或几种组合实现我们的响应式 UI。

### 微调式（Tiny Tweaks）

Tiny Tweaks 布局模式主要表现为单一列展示大部分内容，随着视口宽的的增加，改变 `font-size` 值和 `padding` 间距，以保证内容的持续可读性。

微调式针对单列布局，简单的修改字体大小，padding 和 margin 间距，保证内容可读性。

 ![Tiny taeaks](/img/user/programming/font-end/primitive/css/page-layout/tiny-tweaks-example.png)

```css
.c1 {
  padding: 10px;
  width: 100%;
}

@media (min-width: 600px) {
  .c1 {
	padding: 20px;
	font-size: 1.5rem;
  }
}

@media (min-width: 960px) {
  .c1 {
	padding: 40px;
	font-size: 2rem;
  }
}
```

### 浮动式（Mostly Fluid）

Mostly Fluid 布局是一种 **多列布局**，在大屏幕上设置较大 `margin`，但是在移动端则会浮动后续列，垂直堆叠排列。该模式很常见，因为通常只需要设置一个断点。

浮动式布局，精髓在于 **浮动**，随着屏幕缩小，浮动后续列，通常 float／flex + width 然后使用 media query 设置不同 width 值实现

![Mostly fluid](/img/user/programming/font-end/primitive/css/page-layout/mostly-fluid-example.png)

```html
<!--Pattern HTML-->
<div id="pattern" class="pattern">
  <div class="c">
	<div class="main">
	  <h2>Main Content (1st in source order)</h2>
	  <p>1</p>
	</div>
	<div class="c2">
	  <h3>Column (2nd in source order)</h3>
	  <p>3</p>
	</div>
	<div class="c3">
	  <h3>Column (3nd in source order)</h3>
	  <p>4.</p>
	</div>
  </div>
</div>
<!--End Pattern HTML-->
```

```css
.main {
  background-color: #8e352e;
}

.c2 {
  background-color: #c84c44;
}

.c3{
  background-color: #a53d36;
}

@media screen and (min-width: 37.5em) {
  .c2, .c3 {
	float: left;
	width: 50%;
  }
}
```

当屏幕宽度大于 31.42em，浏览器默认 font-size 为 16px，则为 `37.5 * 16 = 600`px，大于 600px 像素时下面两个 div 则浮动并列显示，否则垂直堆叠展示。

### 断列式（Column Drop）

Column Drop 也是一种多列布局方式，在移动端垂直堆叠排列，随着屏幕增大将各列平铺排列，这种模式就需要我们选择多个断点并选择变化列。

断列士核心是将内容划分为多列，然后随着屏幕变小，依次将左／右列断开堆叠至主列下方。

![Layout Shifter](/img/user/programming/font-end/primitive/css/page-layout/layout-shifter-example.png)

```html
<!--Pattern HTML-->
<div id="pattern" class="pattern">
  <div class="c">
	<div class="main">
	  <h2>Main Content (1st in source order)</h2>
	  <p>1</p>
	</div>
	<div class="sb">
	  <h3>Column (2nd in source order)</h3>
	  <p>2</p>
	</div>
	<div class="sb-2">
	  <h3>Column (3nd in source order)</h3>
	  <p>3</p>

	</div>
  </div>
</div>
<!--End Pattern HTML-->
```

```css
    .main {
      background-color: #8e352e;
    }
    
    .sb {
      background-color: #c84c44;
    }
    
    .sb-2 {
      background-color: #a53d36;
    }
    @media screen and (min-width: 42em) {
      .main {
        width: 75%;
        float: left;
        padding: 0 1em 0 0;
      }
      .sb {
        float: left;
        width: 25%;
    
      }
      .sb-2 {
        clear: both;
      }
    }
    @media screen and (min-width: 62em) {
      .main {
        width: 50%;
        margin-left: 25%;
        padding: 0 1em;
      }
      .sb {
        margin-left: -75%;
      }
      .sb-2 {
        float: right;
        width: 25%;
        clear: none;
      }
    }
```

### 移位式（Layout Shifter）

Layout Shifter 响应式设计是指针对不同屏幕进行不同布局和内容展示，通常需要设置多个断点，然后针对不同大小屏幕，进行不同设计，和前面几种模式在处理小屏幕时自动将后面列往下堆叠不同，在每类断点之间都需要涉及布局和内容两者的修改；这意味着我们需要做更多的编码工作，当然我们的可控性也更强。

移位式核心在于确定不同屏幕需要何种布局及内容展示方式，然后在各断点使用 media query 进行控制。

![Layout Shifter](/img/user/programming/font-end/primitive/css/page-layout/layout-shifter-example-1557385124316.png)

### 分屏式（Off Canvas）

在这之前的四种设计思路都是在大屏铺开展示，然后随着屏幕缩小，将其余列垂直堆叠展示，用户需要上下滚动才能查看页面所有内容，而 Off Canvas 模式则换了一个思路, 分屏：

+ 在小屏幕设备，将不常用或非主要的内容（如导航和菜单之类）放在屏幕外左右两侧，点击可以切换显示／隐藏；
+ 在大屏幕可选择性的铺开展示；

分屏式精华是划分主要内容（如文章列表）和非主要内容（如导航栏），然后优先展示主要内容，非主要内容可以在左右两侧隐藏，支持用户主动点击／滑动切换显示／隐藏。

![Off Canvas](/img/user/programming/font-end/primitive/css/page-layout/off-canvas-example.png)

通常的做法是，在小屏幕，设置不常用内容 `display: none;` 或 `transform: translate(-200px, 0);`，然后点击打开按钮时，添加恢复样式 `display: block;` 或 `transform: translate(0, 0);`，即可展示；在大屏幕则可选择性设计展示方式，通常是直接平铺。

# Css3 媒体查询

响应式方案的核心

## Css2 的媒体查询

```html
<link rel="stylesheet" type="text/css" href="index.css" media="screen"/>
```

media 属性定义了只有在屏幕的时候才会显示该样式表，打印的时候无法显示

相似的：media：print，则是在打印的时候才显示（比如水印之类的）

这就是媒体查询的雏形

## 媒体类型

- all 所有媒体（默认值）
- screen 彩色屏幕
- print 打印预览
- projection 手持设备
- tv 电视
- braille 盲文触觉设备
- embossed 盲文打印机
- speech “听觉”类似的媒体设备
- tty 不适用像素的设备

```css
@media print {
 /*规则，只有是打印的时候才会使用这些规则（选择器 + 声明块）*/
//媒体查询选择器不会对声明的优先级做任何贡献
 #wrap{
  border: 10px solid;
 }
}
```

本质上类似于一个 style 的 if 判断，为了让媒体选择器内部的规则生效，一般会写在样式表最下面

## 媒体属性

width （可加 max min 前缀）

- 视口的尺寸
- @media screen and (min-width:800px) {}

height （可加 max min 前缀）

- 有滚动条，一般只考虑宽度

device-width （可加 max min 前缀）

- 设备的分辨率
- 设备独立像素 320\*568

device-pixel-ratio（可加 max min 前缀，需要加 webkit 前缀）

- 设备的像素比
- pc 端为 1
- 移动端：机器的参数

![1548841419565](layout/1548841419565.png)

- 使用的时候一般要加 -webkit- 前缀
- 高清屏的像素比不会是 1

## Orientation

portrait 竖屏 （英文的意思是：肖像画，一般是竖着的）

landscape 横屏 （英文的意思是：风景画，一般是横着的）

根据宽高比来区分横竖屏

![1548841452863](layout/1548841452863.png)

手机的横屏和竖屏

平板的横屏和竖屏

```css
@media screen and (orientation:landscape) {}
```

## 操作符（关键字）

### Only

防止老旧的浏览器 不支持带媒体属性的查询而应用到给定的样式.

```css
@media only screen and (min-width:800px ){
规则；
规则
}
@media screen and (min-width:800px ){
规则；
规则
}
```

在老款的浏览器下

  - @media only ---> 因为没有 only 这种设备 规则被忽略
  - @media screen ---> 因为有 screen 这种设备而且老浏览器会忽略带媒体属性的查询

**建议在每次抒写 media query 的时候带上 only**

### And

连接媒体属性 、连接媒体类型

对于所有的连接选项都要匹配成功才能应用规则

### （or）: 和 and 相似

关键字里并没有 or，逗号的作用和 or 一样

对于所有的连接选项只要匹配成功一个就能应用规则

  ```css
@media only screen and (orientation:landscape) ,(all and) (-webkit-device-pixel-ratio:2) {
 #wrap{
  border: 10px solid;
 }
}
  ```

应用规则的条件一：横屏的彩色屏幕

条件二：任何像素比是 2 的设备

all and 相当于是被省略了，每个条件都是完整的媒体查询

### Not: 取反

这个时候不需要加 only 了

## 媒体查询的声明优先级

并不会改变改变声明的优先级, 所以在样式表中的顺序也很关键

## 在 Link 标签中使用

视口宽度大于 800px 的纵向显示屏，加载特定 css 文件：

```html
<link rel="stylesheet" media="screen and (orientation: portrait) and (min-width:
800px)" href="800wide-portrait-screen.css" />
```

打印设备特定 css 文件：

```html
<link rel="stylesheet" type="text/css" media="print" href="print.css" />
```

## window.matchMedia()

window.matchMedia() 方法用来检查 CSS 的 mediaQuery 语句

[注意]IE9- 浏览器不支持，可以使用第三方函数库 [matchMedia.js](https://github.com/paulirish/matchMedia.js/)

**参数**

+ mediaQuery 语句的字符串作为参数

**返回值**

+ 返回一个 MediaQueryList 对象。该对象有 media 和 matches 两个属性
    + media：返回所查询的 mediaQuery 语句字符串
    + matches：返回一个布尔值，表示当前环境是否匹配查询语句

  ```js
  

  

  

  

  

  

var result = window.matchMedia('(min-width: 600px)');

console.log(result.media); //'(min-width: 600px)'

console.log(result.matches); // true

  ```


+ 注意：如果 window.matchMedia 无法解析 mediaQuery 参数，matches 属性返回的总是 false，而不是报错

**事件**

+ window.matchMedia 方法返回的 MediaQueryList 对象有两个方法，用来监听事件：addListener 方法和 removeListener 方法


  ```js
// 指定回调函数
mql.addListener(mqCallback);
// 撤销回调函数
mql.removeListener(mqCallback);
  ```

+ 注意，只有 mediaQuery 查询结果发生变化时，才调用指定的回调函数
+ 所以，如果想要 mediaQuery 查询未变化时，就显示相应效果，需要提前调用一次函数
+ 下面这个例子是当页面宽度小于 1000px 时，页面背景颜色为品红色；否则为淡蓝色

  ```js
  

  

  

  

  

  

var mql = window.matchMedia("(min-width: 1000px)");

mqCallback(mql);

mql.addListener(mqCallback);

function mqCallback(mql) {

  if (mql.matches) {

    document.body.background = 'pink';

  }else{

      document.body.background = 'lightblue';

  }

}

  ```


## 实际应用

### 区分苹果和安卓

没有办法通过 css3 的媒体查询来区分苹果和安卓，因为他们的分辨率是类似的

通过 safari 和 chrome 来区分即可，本质是浏览器的区分

```js
if (navigator.userAgent.indexOf('Safari') != -1 && navigator.userAgent.indexOf('Chrome') == -1) { 
  $('html').addClass('safari-only'); 
}
```

# Faq

#faq/ui

## 响应式布局大屏幕三等分、中屏幕二等分、小屏幕一等分

### Grid + Media Query

<iframe height="300" style="width: 100%;" scrolling="no" title="Untitled" src="https://codepen.io/chiyu-git/embed/gOzVwjv?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true"></iframe>

### Tailwind

```html
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4"></div>
```

### Only-grid

```css
.container {
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
}
```

`Grid` 布局可以自动判断容器大小，无论大小屏幕自动撑满并均分

1. `repeat`: 用以 N 整分
2. `auto-fill`：表示自动填充
3. `minmx`: 即书面意思，最小宽度为 `300px`

[使用终极解决方案的在线页面](https://devtool.tech/fe-logo)
