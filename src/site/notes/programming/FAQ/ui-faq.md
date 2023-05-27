---
{"dg-publish":true,"permalink":"/programming/faq/ui-faq/"}
---


# Global FAQ Collection

| File                                                                                     | overview                       |
| ---------------------------------------------------------------------------------------- | ------------------------------ |
| [[programming/font-end/primitive/html/html-form\|html-form]]                          | [[programming/font-end/primitive/html/html-form#faq\|html-form#faq]]              |
| [[programming/font-end/primitive/css/layout\|layout]]                                 | [[programming/font-end/primitive/css/layout#faq\|layout#faq]]                 |
| [[programming/font-end/primitive/css/tailwind/tailwind-basic\|tailwind-basic]]        | [[programming/font-end/primitive/css/tailwind/tailwind-basic#faq\|tailwind-basic#faq]]         |
| [[programming/font-end/primitive/css/flex\|flex]]                                     | [[programming/font-end/primitive/css/flex#faq\|flex#faq]]                   |
| [[programming/font-end/primitive/browser-api/dom-drag-event\|dom-drag-event]]         | [[programming/font-end/primitive/browser-api/dom-drag-event#faq\|dom-drag-event#faq]]         |
| [[programming/FAQ/ui-component/input\|input]]                                         | [[programming/FAQ/ui-component/input#faq\|input#faq]]                  |
| [[programming/font-end/primitive/css/transition & animation\|transition & animation]] | [[programming/font-end/primitive/css/transition & animation#faq\|transition & animation#faq]] |
| [[programming/font-end/mobile/mobile-basic\|mobile-basic]]                            | [[programming/font-end/mobile/mobile-basic#faq\|mobile-basic#faq]]           |

{ .block-language-dataview}

# 基本原则

能用一个 div 不用两个 div，能用 css 不用 js

# CSS 灵感

css-tricks [CSS Tricks (qishaoxuan.github.io)](https://qishaoxuan.github.io/css_tricks/parabola/#css-实现)

各种加载效果：[SpinKit | Simple CSS Spinners (tobiasahlin.com)](https://tobiasahlin.com/spinkit/)

[Animista - CSS Animations on Demand](https://animista.net/play/basic/flip-scale-2)

[Animate.css | A cross-browser library of CSS animations.](https://animate.style/)

https://github.com/EmilyYoung71415/iCSS/tree/master/4-%E5%BD%A2%E7%8A%B6

[you-need-to-known-css 常见CSS效果的实现)](https://lhammer.cn/You-need-to-know-css/#/zh-cn/circular-smooth)

[css-inspration](https://csscoco.com/inspiration/#/./init)

# Box Modal

## 元素 Attribute 的宽高

### Img

![Attribute Width Height](ui-component/image.md#Attribute%20Width%20Height)

### Canvas

![Canvas 宽高](../font-end/primitive/html/html-5.md#Canvas%20宽高)

## 宽高由内容决定

display:inline-block：本身高度就是由内容决定的

父级浮动：宽度由子元素撑开，子元素 display 为 inline-level，font-size 设置为 0，不允许换行

posa

width:fit-content

JS 动态设置：子元素宽度固定，父元素等于子元素个数\*100%，子元素等于 1/子元素个数\*100%

## 边框、背景、阴影

- [半透明边框](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Ftranslucent-borders)
- [多重边框](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fmultiple-borders)
- [边框内圆角](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Finner-rounding)
- [背景定位](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fextended-bg-position)
- [条纹背景](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fstripes-background)
- [1px 线/边](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fone-pixel-line)
- https://zhuanlan.zhihu.com/p/142978650

### 只在一边或两边显示盒子阴影

如果你要一个盒阴影，试试这个技巧，能为任一边添加阴影。为了实现这个，首先定义一个有具体宽高的盒子，然后正确定位 `:after` 伪类。实现底边阴影的代码如下

```css
.box-shadow {
    background-color: #FF8020;
    width: 160px;
    height: 90px;
    margin-top: -45px;
    margin-left: -80px;
    position: absolute;
    top: 50%;
    left: 50%;
}
.box-shadow:after {
    content: "";
    width: 150px;
    height: 1px;
    margin-top: 88px;
    margin-left: -75px;
    display: block;
    position: absolute;
    left: 50%;
    z-index: -1;
    -webkit-box-shadow: 0px 0px 8px 2px #000000;
       -moz-box-shadow: 0px 0px 8px 2px #000000;
            box-shadow: 0px 0px 8px 2px #000000;
}
```

### CSS3：全屏背景

```css
html { 
    background: url('images/bg.jpg') no-repeat center center fixed; 
    -webkit-background-size: cover;
    -moz-background-size: cover;
    -o-background-size: cover;
    background-size: cover;
}
```

### 多边框

#### outline&border

```css
#multiple_border {
  margin-top:20px;
  margin-left:20px;
  width:200px;
  height: 200px;
  border: solid 10px red;
  outline: solid 10px #888;  
}
```

只能实现双重边框

边框样式灵活，可以实现虚线等样式的边框

描边在盒模型之外，会与外部元素发生重叠

能兼容除 `IE6,7` 以外的浏览器。

但是需要注意的是，outline 属性设定的边框不会随着内部元素边界样式的变化而变化。也就是说，如果元素边框带了圆角，那么 outline 绘制出的最外层边框仍然是矩形的。这是 outline 绘制边框的一个缺憾。

#### 多 DIV

```css
#multiple_border {
  margin-top:20px;
  margin-left:20px;
  width:200px;
  height: 200px;
  border: solid 10px red;
  outline: solid 10px #888;  
}
#multiple_border>.inner{
  box-sizing: border-box;
  width:200px;
  height: 200px;
  border: solid 10px pink;
}
```

这也是唯一不存在兼容性问题的方案。

可以实现多重边框，虚线边框等样式

需要额外的 DIV 元素，增加了代码复杂性、

可能无法修改结构或修改 html 结构成本高；多个 div 都设置圆角时，边框之间不能完全贴合。圆角多边框效果图：

#### 伪元素

```css
#multiple_border {
  position:relative;
  margin-top:20px;
  margin-left:20px;
  width:200px;
  height: 200px;
  border: solid 10px red;
  outline: solid 10px #888;  
}
#multiple_border::before{
  content: "";
  position: absolute;
  top: -30px;
  left: -30px;
  right: -30px;
  bottom: -30px;
  border: solid 10px green;
}
.inner{
  box-sizing: border-box;
  width:200px;
  height: 200px;
  border: solid 10px pink;
}
```

实现代码略复杂，属于 hack 的实现方式，不推荐。

`IE6,7,8` 不兼容

用 `:after` 也可以

同时应用 `:before` 和 `:after` 可以实现三重边框

#### 利用 Border-image 属性

利用 `CSS3` 的 `border-image` 属性实现多重边框。实现方法简单，但需要制做一个额外的边框图片，兼容性较差。

取决于图片，可以实现任意多个

#### 利用 Box-shadow 属性

利用了阴影（`box-shadow`）实现边框多少有一些 hack 的味道。

```css
  #multiple_border {
    position:relative;
    margin-top:50px;
    margin-left:50px;
    width:200px;
    height: 200px;
    border: solid 10px red;
    outline: solid 10px #888;  
    box-shadow: 0 0 0 20px #fff, 0 0 0 30px #888;
  }
  #multiple_border::before{
    content: "";
    position: absolute;
    top: -30px;
    left: -30px;
    right: -30px;
    bottom: -30px;
    border: solid 10px green;
  }
  .inner{
    box-sizing: border-box;
    width:200px;
    height: 200px;
    border: solid 10px pink;
  }
```

可以实现任意多个，圆角也可以契合

box-shadow 也不影响布局的，如果这个元素和其它元素的相对位置关系很重要，需要以外边距等方式来为这些多出来的 “边框” 腾出位置，以防被其它元素覆盖。

注意：使用内嵌投影（即 box-shadow 添加参数为 inset，默认不设置时为外阴影）似乎是更好的选择。因为内嵌投影让投影出现在元素内部，设置内边距在元素的内部给多个边框腾位置，处理起来更容易一些。

#### 注意

方案三的目前代码，不论是伪元素还是额外的 DIV

在 outline 外的边框在放大缩小的过程中会出现 1px 的空隙

## Picker-view 镂空 双向渐变

linear-gradient

# 文本样式

## **Chrome 支持小于 12px 的文字 **

```text
p {
  font-size: 10px;
  -webkit-transform: scale(0.8);  // 0.8 是缩放比例
} 
```

## 浮雕文字

`text-shadow:2px 2px 9px black;`

## 文字描边

`-webkit-text-stroke:4px pink;`

## 文字内容溢出省略号

https://juejin.cn/post/6966516290957606942

https://juejin.cn/post/6844903589672910855

https://www.jianshu.com/p/50bbd21c0c0f

https://www.zoo.team/article/text-overflow

> 为什么大多数移动端浏览器都适用？其实 pc 主要的毒瘤就是 ie，移动端没有 ie。火狐也不支持这个

```css
div{
    display: block; 
    /*关键是宽度是否设死，如果宽度由内容撑开则无法显示省略号，如果宽度设死了，inline-block也可以显示省略号*/
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}
```

## 多行文本省略号

文本有多行所以 nowrap 就不可用了，因为使用了 nowrap 就只有一行了

## 多行文本头部省略号

```js
function strLen(str) {  
  var len = 0;  
  for (var i=0; i<str.length; i++) {  
    if (str.charCodeAt(i)>127 || str.charCodeAt(i)==94) {  
       len += 2;  
     } else {  
       len ++;  
     }  
   }  
  return len;  
}
```

```js

function strlen(str){
    var len = 0;
    for (var i=0; i<str.length; i++) { 
        var c = str.charCodeAt(i); 
        if ((c >= 0x0001 && c <= 0x007e) || (0xff60<=c && c<=0xff9f)) { 
           len++; 
        } else { 
           len+=2; 
        } 
    } 
    return len;
}
```

```js

function strLen(str){
    var l = str.length; 
    var blen = 0; 
    for(i=0; i<l; i++) { 
        if ((str.charCodeAt(i) & 0xff00) != 0) { 
            blen ++; 
        } 
        blen ++; 
    }
}
```

```js

function strLen(str) {
  if (str == null) return 0;
  if (typeof str != "string"){
    str += "";
  }
  return str.replace(/[^\x00-\xff]/g,"01").length;
}
```

## 等宽字体

等宽字体：https://www.zhangxinxu.com/wordpress/2016/07/monospaced-font-css3-ch-unit/

文字渲染：https://eyhn.github.io/text-rendering-talk/1

## 排版空白符问题

dbg_naview

```html
<view
      s-if="col.bubble_title"
      class="c-channel-entry-new-tip">
    <view class="c-channel-entry-new-tip-wrap">
        <view class="c-channel-entry-new-tip-border">
            <text class="c-channel-entry-new-tip-text">
                {{col.bubble_title}}
            </text>
        </view>
        <image src="../../assets/img/icon/radius.png" class="c-channel-entry-new-tip-anchor"/>
    </view>
</view>
```

## 文字渐变 和 文字渐变动画

[如何用 CSS 实现文字渐变色动画 - 链滴 (ld246.com)](https://ld246.com/article/1624431799253)

# 定位

## 绝对定位模拟固定定位

+ 固定定位参考的是 **视窗**

- 绝对定位和固定定位，在没有出现滚动条的时候是一模一样的
- 拖动系统滚动条的时候，初始包含块是不会跟着动的（绝对定位的元素一直跟着初始包含块，就会消失在视窗），如果让初始包含块一直跟着视窗，不受滚动条的影响，绝对定位就是固定定位了
- 因为滚动条都是 body 或者 html 的滚动条传递过去的，先禁用了 html 的滚动条，那么 html 和初始包含块就都不会出现滚动条，body 在给自己一个滚动条，那么拖动滚动条只会拖动 body 内部的元素，初始包含块的位置始终跟着视窗没有变化

# 结构布局

## 常见布局

- [全背景等宽内容居中](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Ffluid-fixed)
- [绝对底部](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fsticky-footer)
- [水平垂直居中](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fcentering-known)
- [类订单布局](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fclass-order-layout)
- [Flex布局](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fflexbox-layout)

## 换行

换行，要换行肯定是 **内联元素**

html 层面：br 标签，行距收到 css 属性 line-height 的影响，如果用行高实现垂直居中，两个内敛元素之间的行间距也无法改变，还是使用绝对定位好了

css 层面：溢出换行，或者 display 为 block

- container>inner>inner>span*2
    - 第一个 inner 在 container 实现垂直水平居中
    - 第二个 inner 在第一个 inner 实现
- 或者一个垂直水平居中，另一个 display：block，然后相对定位

去到一行

- 浮动，父级的宽度
- flex 不压缩 要溢出
- inline-block 父级 white-sapce no wrap，font-size：0
- 浮动和 inline-block 的差别
    - 块级元素默认那父元素的宽度，希望他被内容撑开，而有不影响布局，浮动起来或者 inline-block 都可以

## 对齐

### 文本框和按钮的基线对的不是很齐

一个 float left

一个 float right

改变盒模型即可解决

本质还是 baseline 的问题

### 图片下方带有 3px 间隙解决方法

空隙的本质：img 是一种类似 text 的元素，在结束的时候，会在末尾加上一个空白符，回车在 HTML 中会被识别为空白符。所以就会多出 3px

https://segmentfault.com/a/1190000006808606

https://segmentfault.com/a/1190000006808606

https://segmentfault.com/a/1190000004400766

https://satanwoo.github.io/2016/04/26/blank-space-in-html/

空白符具备宽度（和 font-size 有关），不具备高度。也就是说这个空白符出现在了 3px 空隙的下方

产生原因：主要是因为图片的垂直对齐方式 vertical-align 引发的，默认值是 baseline，默认为此值时图片下方就会多出 3px。

**解决：**

- 设置 img 的 display:block;
- 设置 img 父元素的 font-size: 0px;
- 设置 img 浮动,如果你的父元素是有 img 撑起来的,这个时候会导致,父元素没高度, 可以给父元素添加 绝对定位;
- 设置 img{ vertical-align:top;}
- 设置父元素 div 的 line-height: 0； （需设置父元素宽度让图片垂直排列,父元素不定宽 img 水平排列时无效）
- 设置 img 浮动 float: left; 或 float: right (影响布局)
    - **float 要么依赖前一个（或者后一个）float 元素的边界，要么就依赖于父元素的边界。而一个空白符，既不是包含块（父容器）的边界，也不是另一个 float 元素，因此不受影响，也不会对其余 float 元素有影响。**

video 标签与其他元素之间的空隙，可能是由于空行导致的，设置全局的 font-size 为 0，这种状况普遍存在，由于 html 结构的空格，设置为 0，即可

### display:inline-block 元素显示间隙

移除标签之间的空格；

使用 margin-left 或 margin-right 取负值；

对父元素设置 font-size 为 0，然后对元素的 font-size 初始化；

对父元素设置 letter-spacing（字符间距）为负值，然后设置元素的 letter-spacing 为 0；

对父元素设置 word-spacing（单词间距）为负值，然后设置元素的 word-spacing 为 0。

行内元素之间的由于 html 排版出现的空格，相当于是文字大小，文字大小为零，就可以消除空格，或者让他们浮动，变成块元素，也没有空格

## 水平垂直居中

### Block-level Box

**margin: 0 auto;**

- 自身非浮动没有开启定位的元素：margin 0 auto 水平居中
    - margin auto，也只是水平居中，非浮动非定位盒子上下边距为默认渲染为 0
- 适用于：仅需要水平居中且没有开启浮动、定位的情况，**而且需要宽度不是父级的 100%，才可以看到效果**
- 父级：宽度大于子级即可看到效果，文档流中、浮动、定位均可
- 缺点：只能实现水平居中，不能开启浮动、定位

**position:absolute;**

- **主动** 分配富裕空间

    ```html
    <div id="wrap">
     <div id="inner">
       test
     </div>
    </div>
    
    <style>
      #wrap{
        position: relative;
        width: 400px;
        height: 600px;
        background: pink;
        margin: 0 auto;
      }
    
      #inner{
        position: absolute;
        left: 50%;
        top: 50%;
        margin-left: -50px; //回退半个自身的像素，必须要知道inner的高宽
        margin-top: -50px;
        width: 100px;
        height: 100px;
        background: deeppink;
    </style>
    ```

- 适用于：已知自身宽高的 Box
- 缺点：必须要知道自身的宽高，非响应式

**position:absolute;**

- **自动** 分配富裕空间

    ```css
    #inner{
     position: absolute;
     /* 因为四者默认值是auto，所以需要全部设为0 */
     left: 0;
     right: 0;
     top: 0;
     bottom: 0; 
     margin: auto;
     width: 100px;
     height: 100px;
     background: deeppink;
    }
    ```

- 绝对定位盒子的特性
    - 高宽有内容撑开
    - 水平方向上： left + right + width + padding + margin = 包含块 padding 区域内部的尺寸
    - 垂直方向上： top + bottom + height + padding + margin = 包含块 padding 区域内部的尺寸
- 适用于：宽高固定的 Box
- 缺点：当 Box 的宽高不固定的时候，width 和 height、margin 都是 auto，width 和 height 优先级更高，最终 inner 会填满包含块的 padding；无法仅仅实现水平居中或垂直居中
- 用于图片也是 ok 的，但是图片有更加优雅的方案

**transform:translate**

- 主动分配富裕空间

    ```css
    #inner{
     position: absolute;
     left: 50%;
     top: 50%;
     transform: translate3d(-50%,-50%,0); //针对于自身
     background: deeppink;
    }
    ```

- 适用于：宽高未知的 Box
- 缺点：transform 兼容性比较差

### Inline-level Box

**text-align:center;line-height**

- 控制 **内联元素** 水平居中
- 适用元素：**块级元素**，内敛元素如果浮动则失效
- 加一个行高与父容器的 height 一样，实现垂直居中
- 转换子元素为 inline-level，实现水平或者垂直居中都是简单易行的方案

**vertical-align**

- 适用元素：inline-level and table-cell elements.
- 初始值：baseline
- 不继承
- 可以指定数值

**未知宽高**

- 使用 flex

### 背景图片

**background-position**

### 占位元素（替换元素）

- 图片 input 等
- 在容器内加入了一个行内框比图片高的元素，然后让图片以该元素为基准，进行垂直居中
- 最后这种方式，如果是行内块，也可以实现垂直水平居中，但是一般只用于图片 input 等

    ```html
    <style>
    #wrap{
     height: 400px;
     width: 400px;
     border: 1px solid ;
     margin: 0 auto;
     text-align: center;
    }
    #wrap:after{
     content: "";
     display: inline-block;
     height: 100%;
     width: 0;
     background: pink;
     vertical-align: middle;
    }
    #wrap img{
     vertical-align: middle;
    }
    </style>
    
    <div id="wrap">
     <img src="img/img2.jpg" width="150"/>
    </div>
    
    ```

### 图片文字垂直居中

+ 在 css 中给 img 添加上“vertical-align:middle”属性。

### Canvas 元素

- 调整原点
- 调整偏移
- Canvas 文本

### Flex 布局

容器为 flex，改变了项目，不论原先的 display

```css
.wrap{
  width: 500px;
  height: 500px;
  background:orange;
  margin:0 auto;
  display:flex;
}
.inner{
  width: 200px;
  height: 200px;
  background: green;
  margin:auto; /*不论是否有宽高，都会自动居中*/
}
```

当然也可以使用 justify-content align-items

```css
  #container{
    height: 100px;
    background-color: pink;
    display:flex;
    flex-wrap:wrap;
    justify-content:center;
    align-items:center;
  }
  .item{
    background-color: cornflowerblue;
    width: 100px;
    height: 50px;
  }
```

### Grid 布局

类似于 flex

```css
justify-content: center;
align-content:center;
```

控制栅格系统相对于栅格容器垂直水平居中

开启 grid 布局之后，可以利用项目的 justify-self 属性 单个控制对齐

### Iconfont

iconfont 本质上是图片，所以用于对其的是图片的下边缘，想要字体和 iconfont 水平对齐，应该让 iconfont 的 vertical-align 为 middle

### 最强法宝

display 为某个，然后即可使用相应的布局方案

inline-block text-align

## 盒子不规则对齐

有的左对齐，有的右对齐。最简单就是用 flex 来做，因为开启了 flex，float 会失效，所以右对齐的只能在 flex-grow 之后，内部的元素去浮动，这样不好

更好的做法是倒数第二个元素 flex-grow1 占用用于空间，把最后一个元素挤到右边，自然实现了右对齐

用浮动也能实现同样的功能，只是浮动永远是左右的，而 flex 可以做到上下，而且浮动的话没有办法简单的控制居中

但是有一个问题，flex 延长之后，整个元素都可以触发事件，导致要多包一层结构才方便

看来无论如何都要多包一层空 div，统一命名为 `grow_wrap`

或者在中间加一个占位元素，仅仅是用来占位

又或者是浮动之后分别用 flex 来实现居中，但是有的时候一边只有一个元素，这种方案就很亏

### 左中右对齐

在一行内同时实现左对齐、居中对齐、右对齐

最合适的方案还是左右两边宽度相同，然后居中

如果左右宽度不同，居中的对齐会有偏差

### 使用 Flex

justify-content:sapce-between;

然后左中右三个结构

### Flex & Margin

<iframe height="300" style="width: 100%;" scrolling="no" title=" 不规则对齐 -1" src="https://codepen.io/chiyu-git/embed/wvMdRNg?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true"></iframe>

<iframe height="300" style="width: 100%;" scrolling="no" title=" 不规则对齐 -3" src="https://codepen.io/chiyu-git/embed/RwrVEOW?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true"></iframe>

## 文本对齐

### 纯文本对齐

不想创建太多的 DOM 结构，如果操控纯文本实现各种对齐呢？

http://www.daqianduan.com/6806.html

## 两列自适应布局

### 要求

**一列由内容撑开**

**另一列撑满剩余宽度的布局方式**

### 定位

一列由内容撑开，开启定位之后，元素宽高由内容撑开

另一列怎么做到自动充满剩余高度呢？？

- 使用 JS 动态计算

### 浮动 +overflow:hidden

<iframe height="300" style="width: 100%;" scrolling="no" title="CSS 实现 - 两列自适应 - 浮动&amp;BFC" src="https://codepen.io/chiyu-git/embed/MNXoNa?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true"></iframe>

`overflow: hidden;` 主要是为了防止出现文字环绕的效果，开启了 BFC 之后，不会与浮动元素重叠

### Flex

<iframe height="265" style="width: 100%;" scrolling="no" title=" 两列自适应 -flex" src="https://codepen.io/chiyu-git/embed/xvzrEw/?height=265&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true"></iframe>

因为 align-items: normal;，所以实现了等高

### Grid

<iframe height="265" style="width: 100%;" scrolling="no" title=" 两列自适应 -grid" src="https://codepen.io/chiyu-git/embed/BXVYEw/?height=265&theme-id=0&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true"></iframe>

## 三列布局

两边固定，中间自适应

中间列要完整显示

当中列要优先加载

### 定位

实现 **局部三列** 的最简单方案，但是不适合用来做整体的布局，用定位的话因为相对于初始包含块而且脱离了文档流，很不好控制，**不要用定位做布局框架，用浮动来做**

<iframe height="265" style="width: 100%;" scrolling="no" title=" 三列布局 - 绝对定位 " src="https://codepen.io/chiyu-git/embed/zgaWbB/?height=265&theme-id=0&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true"></iframe>

### 浮动

浮动左右脱离文档流，中间自然上升

文字被左浮动的块卡住，自然的就显示在中间列了

缺点：浮动不能过界，所以中间块只能放在最后，中间块不能优先加载

<iframe height="265" style="width: 100%;" scrolling="no" title=" 三列布局 - 浮动 " src="https://codepen.io/chiyu-git/embed/zgajxN/?height=265&theme-id=0&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true"></iframe>

### 圣杯布局

**margin** 只会改变边界，不会改变位置，通过负值改变元素边界，从而影响布局（也会影响到 padding 的开始位置）

**实现步骤：**

1. middle 至于 html 结构第一位，left,middle,right 三个全部浮动
2. margin 为负值&相对定位: 调整旁边两列的位置 (使三列布局到一行上)
    - 这个时候 left 需要 margin-left：- (100% +**自身宽**)，想到 tramsfrom，但是又兼容性问题
    - 使用相对定位：再负 **自身宽**
3. 给父元素添加 padding，控制使中间块内容展现
    - 只有在 width 是 auto 的时候，加 padding 才会从 width 中扣除，如果是浮动了，宽高都是由内容撑开，为了实现内容的充满屏幕，只能设置 width100%，这个时候加 paddig 就是在屏幕外增加内容了， 所以只能给父元素加 padding，把子元素的内容顶到中间显示
4. 给侧边开启相对定位

    + 因为父级增加了 padding，而浮动元素只能在父级的内容区浮动，**所以** 需要控制侧边去填补多出来的空白

5. 为 body 添加 min-width
    - min-width 不可以设置给中间块？设置给中间块，会导致中间块溢出父元素，破坏布局，demo player
    - 给父元素添加 min-width，因为左右有了 padding，实际的宽度只有中间的 middle 部分，min-width 相当于针对 middle
    - 给 body 添加 min-width，设置为 2*left+right，两个 left 一个 right
- chrome 新增了最小窗口限制，但是火狐的最小窗口要小得多

**缺点：**

- 中间 middle 由内容控制高度，两变块无法跟进
- 当父级的最小宽度小于左右的固定宽度时布局会错乱，因为：左边的 -margin 100%，参考于父级宽度，此时的宽度不足，无法上 left 偏移到上一去，导致布局错乱

<iframe height="265" style="width: 100%;" scrolling="no" title=" 三列布局 - 圣杯布局 " src="https://codepen.io/chiyu-git/embed/xvzjgX/?height=265&theme-id=0&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true"></iframe>

### 圣杯 + 伪等高布局

padding 撑开内容

margin 为负值，收缩边框，在设置 overflow hiddren

因为元素的排列跟着边界，边界由 margin 控制，所以 padding 的值再高也不会影响其他元素，左右可以跟着中间的内容高度

本质：父级被 middle 撑开，露出了原本 hidden 的 padding

缺点：还是没有解决圣杯布局最小错乱问题

```css
#wrap .left{
  float: left;
  width: 200px;
  background: pink;
  padding-bottom: 1000px;
  margin-bottom: -1000px;
}
#wrap .right{
  float: left;
  width: 500px;
  background: deeppink;
  padding-bottom: 1000px;
  margin-bottom: -1000px;
}
```

### 双飞翼布局

希望布局的框架一直在文档流里，不要有层级的提升，完全不使用定位

给 middle 在套一个 div，在增加 padding，然后 right 和 left 通过负 margin 调整位置就可以了，这种方案比较万金油的，可以在不改变外部的所有布局，在内部改变自 - 己，但是如果滥用，结构会变得很复杂

为了让中间的内容完整显示，给 body 增加一个 min-width=2*left+right

所有的内容都在文档里，并且层级没有提升

<iframe height="265" style="width: 100%;" scrolling="no" title=" 三列布局 - 双飞翼布局 " src="https://codepen.io/chiyu-git/embed/WVKQaL/?height=265&theme-id=0&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true"></iframe>

### 两组实现的对比:

1. 俩种布局方式都是把主列放在文档流最前面，使主列优先加载。
2. 两种布局方式在实现上也有相同之处，都是让三列浮动，然后通过 **负外边距** 形成三列布局。
3. 两种布局方式的不同之处在于如何处理中间主列的位置：
    - 圣杯布局是利用父容器的左、右内边距 + 两个从列相对定位；
    - 双飞翼布局是把主列嵌套在一个新的父级块中利用主列的左、右外边距进行布局调整
4. 圣杯布局：center 部分的最小宽度不能小于 left 部分的宽度，否则会 left 部分掉到下一行
5. 双飞翼布局：

    + **在圣杯布局基础上进一步优化，padding 加在新增的 DOM 结构上，不会影响到同级的浮动侧边，解决了圣杯布局错乱问题**
    + **实现了内容与布局的分离。而且任何一栏都可以是最高栏，不会出问题**。

### Flex 实现

左右给固定值，中间给 flex-grow:1，自动吸收剩余空间

left 给 order:-1，排列到队首

<iframe height="265" style="width: 100%;" scrolling="no" title=" 三列布局 -flex" src="https://codepen.io/chiyu-git/embed/JgBGXJ/?height=265&theme-id=0&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true"></iframe>

### Grid 实现

与 flex 类似

<iframe height="265" style="width: 100%;" scrolling="no" title=" 三列布局 -grid" src="https://codepen.io/chiyu-git/embed/gVjPGq/?height=265&theme-id=0&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true"></iframe>

## 等高布局

容器的高度由内容撑开

容器的背景实时变化

内容的背景始终与容器等高

### 负 Margin

实现一变种

背景色从 padding 开始裁剪，足够大的 padding，足够大的背景，同等的负 margin，控制布局边界，当容器被撑开时，显露处 padding

```css
#page .container {
  overflow:hidden; /*清除浮动*/
}
.aside {
  width: 220px;
  float: left;
  margin-right: 20px;
  padding: 5px 15px;
  padding-bottom:10000px;
  margin-bottom:-10000px;
  min-height: 200px;
  background:red;
}

.content {
  width: 720px;
  float: left;
  padding: 5px 15px;
  padding-bottom:10000px;
  margin-bottom:-10000px;
  background:blue;
}

<div id="page">
<div class="container">
<div class="aside">Sidebar ...</div>
<div class="content">Main content ...</div>
</div>
</div>
```

### 渐变方式

实现一

等高列布局，有一种简单的方法就是根据列宽和其背景色制作一张背景图，然后在 Y 轴方向重铺。但这种方式在 **改变宽度** 和 **背景颜色** 的时候就非常的麻烦。那么根据背景图的思路，我们可以考虑使用 CSS3 的渐变属性来制作类似于这样的一张背景图。

假设我们的两列布局，左侧是 220px,主内容是 720px，两者间距是 20px(容器宽度是 960px)。我们需要一个类似于下面的背景图：

![](/img/user/programming/FAQ/ui-faq/image-20221021165735017.png)

那么使用 Gradient 实现类似一张这样的背景，我们需要采用多色渐变，如示例中所示，总共使用了三个颜色：

- 左侧色为 `#f36`，从０点起到 220px 止
- 间隔色为 `#fff`，从 221px 起到 240px 止
- 主内容色为 `#f63`，从 241px 起到 100% 止

```css
  #page .container {
    background-image: -webkit-linear-gradient(to right, #f36 0, #f36 220px, #fff 221px, #fff 240px, #f63 241px, #f63 100%);
    background-image: linear-gradient(to right, #f36 0, #f36 220px, #fff 221px, #fff 240px, #f63 241px, #f63 100%);
    margin-bottom: 10px;
    overflow: hidden;
  }
```

### 伪类

`.wrap` 容器中包含三个子元素和其他们对应的伪类。通过伪类设置背景色。使用这种方法有几个关键点：

- 容器 `.wrap` 需要设置 `position:relative`；
- 伪类 `:before` 或 `:after` 需要绝对定位，不同元素的位置调整对应的 `left` 或者 `right` 值；
- 背景颜色设置在伪类生成的内容上，并且通过 `top` 和 `bottom` 拉伸，致使他们等高。
- 给伪类设置 `z-index` 的值为 `-1`，让其在内容底部。

```js
  #page .container {
    position: relative;
    overflow:hidden; /*清除浮动*/
  }

  .aside{
    width: 220px;
    margin-right:20px;
    float:left;
  }
  .content{
    
    width: 720px;
    float:left;
  }

  .aside:before,
  .content:before {
    content: "";
    position: absolute;
    top: 0;
    bottom: 0;
    z-index: -1;
  }

  .aside:before {
    left: 0;
    width: 220px;
    background: #f63;
  }

  .content:before {
    width: 720px;
    background: #c6f;
    right: 0;
  }
```

### Flex

实现二

flex 布局，align-items 的默认值为 normal，因此，一旦项目不设置高度，就会自动铺满容器的高度，实现等高布局

```css
* {
  margin: 0;
  padding: 0;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}

.container {width: 960px;margin: 0 auto;color: #fff;}

#page .container {background-color: #cef;min-height: 300px;margin-bottom: 10px;display:flex;}

.aside {width: 220px;background-color: #36e;padding: 5px 15px;margin-right:20px;}

.content {background-color: #66f;padding: 5px 15px;}
```

### Grid

### 对比

负 margin：浮动，清除浮动，容器由内容撑开即可，需要逐个添加 padding 和负 margin

渐变方式：浮动，清除浮动，容器由内容撑开即可，使用了 css3 API

伪元素：容器相对定位，伪元素绝对定位，需要逐个添加伪元素

flex：flex 的默认表现，使用了 flex API

## 粘连布局

### 要求

关闭按钮，没有内容的时候在 footer，内容过多的话就跟着内容向下移动

### 绝对定位

```html
<div id="wrap" >
  <div class="main">
    main <br />main <br />main <br />main <br />
  </div>
  <div id="footer">footer</div>
</div>
```

```css
html,body {height: 100%;}
#wrap {
  position: relative;
  min-height: 100%;
  background: pink;
  text-align: center;
  overflow: hidden;
}
#wrap .main {padding-bottom: 50px;}
#footer {
  position:absolute;
  bottom:0;
  width: 100%;
  line-height: 50px;
  background: deeppink;
  text-align: center;
}
```

### Calc

上面的代码中，因为要实现最小高度 100% 的效果，给 html、body 都设置为高度 100%，不利于代码扩展。下面使用 100vh 来代替 100%，代码会简洁很多。

```css
.main{
  min-height:calc(100vh - 50px);
  background: pink;
}
#footer{
  width: 100%;
  line-height:50px;
  background-color:deeppink;
  text-align:center;
}
```

内容部分 main 设置最小高度为 `calc(100vh - $footer高度)` 即可

### Flex

上面的代码中，如果 sticky 的底部高度发生了变化，则内容部分的代码也需要进行相应地调整。如果使用 flex，则可以更加灵活。为父级 (.box) 设置 flex、上下排列及最小高度为 100vh，为内容部分 (.content) 设置 flex:1 即可

```css
#wrap{
  display: flex;
  flex-flow:column;
  min-height:100vh;
  background: pink;
}
.main{
  flex:1;
}
#footer{
  line-height:50px;
  background-color:deeppink;
  text-align:center;
}
```

### Grid

类似额

```css
#wrap{
  display: grid;
  grid-template-rows:1fr 50px;
  min-height:100vh;
  background: pink;
}
#footer{
  line-height:50px;
  background-color:deeppink;
  text-align:center;
}
```

### 负 Margin 法

改变了 DOM 的结构，利用了块级盒子的特性

html body `#wrap` 高度继承，footer 被顶出底部

footer 添加 margin-top 负 $ 自己的高度，出现在底部

此时，wrap 由父元素的高度决定，不是由 main 撑开，只要 main 的内容没有撑开 wrap 的高度，footer 永远都会在最底部

当 main 的内容超过 wrap 的高度时，wrap 不会被撑开，所以取消 wrap 的高度继承，把 min-heigh 设置为 100% 即可

处理 main 内容与 footer 的重叠，给 main 加一个 padding-bottom 为 $footer 的高度

```html
<div id="wrap" >
  <div class="main">
    main <br />main <br />main <br />main <br />
    main <br />main <br />main <br />main <br />
    main <br />main <br />main <br />main <br />
  </div>
</div>
<div id="footer">footer</div>
```

```css
html,
body {
  height: 100%;//高度一层层继承下来
}
#wrap {
  min-height: 100%;
  background: pink;
  text-align: center;
  overflow: hidden;
}
#wrap .main {
  padding-bottom: 50px;
}
#footer {
  margin-top: -50px;
  line-height: 50px;
  background: deeppink;
  text-align: center;
}
```

### position:sticky

两种粘连布局：

+ 内容不够长，粘在底部，内容太长跟着被挤下去，自己实现的 sticky
+ 内容不够长，粘在内容下面，内容太长，固定在底部，position:sticky;
+ 第二种实例：<https://segmentfault.com/a/1190000007938006>

## 图片展示布局

不同数量的图片，不同的布局

# 动画

## Css 动画 Vs Js 动画 性能

https://developer.mozilla.org/zh-CN/docs/Web/Performance/CSS_JavaScript_animation_performance

关键是只要动画涉及的属性不引起 reflow（重新布局）（参考 [CSS triggers](https://csstriggers.com/) 获得更多信息），我们可以把采用操作移出主线程。最常见的属性是 CSS transform。如果一个元素被提升为一个 [layer](https://wiki.mozilla.org/Gecko:Overview#Graphics)，transform 属性动画就可以在 GPU 中进行。这意味着更好地性能，特别实在移动设备上。在 [OffMainThreadCompositing](https://wiki.mozilla.org/Platform/GFX/OffMainThreadCompositing) 上寻找更多细节。

浏览器可以优化渲染流程。总之，我们总是可以尽可能通过 CSS 过渡/动画创建动画。如果你的动画真的很复杂，你可能不得不依赖于 JavaScript 动画。

## 动画过渡

- [弹跳效果](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fbounce)
- [弹性过度](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Felastic)
- [闪烁效果](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fblink)
- [打字效果](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Ftyping)
- [抖动效果](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fshake)
- [无缝平滑效果](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fsmooth)
- [延轨迹平滑效果](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fcircular-smooth)

## 背景渐变动画

`CSS` 中最具诱惑的一个功能是能添加动画效果，除了渐变，你可以给背景色、透明度、元素大小添加动画。目前，你不能为渐变添加动画，但下面的代码可能有帮助。它通过改变背景位置，让它看起来有动画效果。

```css
button {
    background-image: linear-gradient(#5187c4, #1c2f45);
    background-size: auto 200%;
    background-position: 0 100%;
    transition: background-position 0.5s;
}    
button:hover {
    background-position: 0 0;
}
```

## 用 CSS 动画实现省略号动画

这个片段将帮助你制造一个 `ellipsis` 的动画，对于简单的加载状态是很有用的，而不用去使用 `gif` 图像。

```css
.loading:after {
    overflow: hidden;
    display: inline-block;
    vertical-align: bottom;
    animation: ellipsis 2s infinite;
    content: "\2026"; /* ascii code for the ellipsis character */
}
@keyframes ellipsis {
    from {
        width: 2px;
    }
    to {
        width: 15px;
    }
}
```

## 曲线运动

### 勾股定理

### 三角函数

正弦 : sin

余弦 : cos

正切 : tan

余切 : cot

正弦定理

- a/sinA = b/sinB =c/sinC = 2r（r 为外接圆半径）

余弦定理

- cosA = b*b + c*c - a*a / 2bc
- cosB = c*c + a*a - b*b / 2ca
- cosC = a*a + b*b - c*c / 2ab

### 什么是弧度

一个角度到底代表多少弧度：这个角度所包含的外接圆的弧长/外接圆的半径

- 360 角度 = 2*PI*r/r 弧度 (360 角度 = 2*PI 弧度)

单位换算

- 1 角度 = PI/180 弧度
- 1 弧度 = 180/PI 角度

### 三角函数图像

曲线运动

完成曲线运动

与 canvas 结合

人眼能接收的最好频率为一秒钟 60 次,这样的体验是比较好的

```js
// x轴对应的弧度
testNode.style.left = startX + (deg*Math.PI/180)*step/10 +'px';
// y轴弧度对相应的值
testNode.style.top = startY + Math.cos( deg*Math.PI/180 )*step*2+"px";
```

### Demo 阳光下的泡沫

# 功能效果

## 拖拽

- <a href='../demo/utils/drag.js'>drag</a>

## 橡皮筋效果

+ <a href='../demo/utils/mobile'>spring</a>

### 快速滑屏

- 获取最后一次 touchmove 的速度，根据速度额外增加滑动的距离
- 模拟器 字会抖动，触发了重排，而移动端不会触发，3D 变化，不走浏览器，直接由显卡完成？也不会有重排，消耗硬件，效率更高
- 图层不能太多，3D 硬件加速不能太多
- 橡皮筋持续的时间与滑动的速度有关
- 普通的滑动，不应该触发快速滑屏，加一个 extramove 进行判断，如果进入了 touchmove 的橡皮筋逻辑则不要触发快速滑屏

## 斜杠型滑屏

- 当 y 轴方向的 touchmove 距离大于 x 轴方向的距离，禁用 x 方向的逻辑

## 任意滑屏

- 判断首次滑屏方向，如果是横向的，可以影响横向的滑屏，竖向的滑屏不收影响，如果首次滑屏时纵向的，则可以影响纵向的滑屏，横向的滑屏不受影响，且：有滑屏区域 A 和 B，在 A 触发的滑屏，长按滑动到 B，B 不会受到影响
- 比较首次滑动时，横向纵向的滑动距离，判断是否是首次，首次 Y 轴防抖，二次以后 Y 轴防抖（看门狗）

# 视觉效果设计

## 视觉效果

- [常见投影](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fsingle-projection)
- [不规则投影](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Firregular-projection)
- [毛玻璃效果](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Ffrosted-glass)
- [斑马条纹](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fzebra-stripes)
- [文字特效](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Ftext-effects)
- [环形文字](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fcircular-text)
- [插入换行](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fline-breaks)
- [图片对比控件](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fimage-slider)

## **全屏滚动的原理是什么 ？用到了 CSS 的哪些属性 ？**

原理

- 有点类似于轮播，整体的元素一直排列下去，假设有 5 个需要展示的全屏页面，那么高度是 500%，只是展示 100%，剩下的可以通过 transform 进行 y 轴定位，也可以通过 margin-top 实现。
- overflow：hidden；transition：all 1000ms ease；

## 视差滚动效果

视差滚动（Parallax Scrolling）通过在网页向下滚动的时候，`控制背景的移动速度比前景的移动速度慢` 来创建出令人惊叹的 3D 效果。

- CSS3 实现。 优点：开发时间短、性能和开发效率比较好，缺点是不能兼容到低版本的浏览器
- jQuery 实现。 通过控制不同层滚动速度，计算每一层的时间，控制滚动效果。优点：能兼容到各个版本的，效果可控性好。缺点：开发起来对制作者要求高。
- 插件实现方式。 例如：parallax-scrolling，兼容性十分好。

## 滚动到底部

+ 设置容器元素的 scrollTop 属性为 scrollHeight - clientHeight
+ 三个属性都是容器的，clientHeight 是容器自身的高度，代表的是滚动按钮在整个滚动条的占比

# 组件

## 按钮常用样式

```css
.btn{
  display: inline-block;
  width:110px;
  padding:10px 0;
  font-size:14px;
  line-height:normal;
  text-align:center;
  cursor: pointer;
  user-select:none;
}
```

## 部件、挂件

### 常见形状

- [圆与椭圆](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fellipse)
- [parallel四边形](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fparallelogram)
- [切角效果](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fbevel-corners)
- [简易饼图](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fpie-chart)
- [提示气泡](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fpoptip)
- [其他多边形](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fpolygon)

### 利用边框画三角形

https://www.cnblogs.com/v-weiwang/p/5057588.html>

![1548677281175](/img/user/programming/FAQ/ui-faq/1548677281175.png)

```css
#box1{
  width: 100px;
  height: 100px;
  border-style: solid;
  border-width: 100px 100px 100px 100px;
  border-color: red forestgreen blue cyan;
}
```

边框在相交的部分的处理，平常只注意到了红色正方形内部的部分

注意到了边框的细节之后，只要把宽高都变成 0

![1548677320784](/img/user/programming/FAQ/ui-faq/1548677320784.png) ![1548677323159](/img/user/programming/FAQ/ui-faq/1548677323159.png)

```css
#box1{
  width: 0; //宽度默认值为auto，所以必须定死为0
  height: 0;
  border-style: solid;
  border-width: 100px 100px 100px 100px;
  border-color: red forestgreen blue cyan;
}
```

去掉某一边的颜色，就可以得到一个三角形

![1548677374984](/img/user/programming/FAQ/ui-faq/1548677374984.png)![1548677377869](/img/user/programming/FAQ/ui-faq/1548677377869.png)

```css
#box1{
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 100px 100px 100px 100px;
  border-color: transparent transparent blue transparent;
}
```

进一步完善，只维持必要的高度

![1548677420813](/img/user/programming/FAQ/ui-faq/1548677420813.png) ![1548677444590](/img/user/programming/FAQ/ui-faq/1548677444590.png)

```css
border-width: 0 100px 100px 100px;

border-width: 0 0 100px 100px;
```

兼容 ie6：border-style: dashed; 虚线的时候 边框的相交处选择不显示

```css
#wrap .sjx {
  width: 0px;
  height: 0px;
  overflow: hidden;
  border-width: 40px;
  border-style: dashed  dashed dashed solid;
  border-color: transparent transparent transparent #ffff00;
}
```

### 浮标小三角

```css
&:before{
  position: absolute;
  left:-15px;
  top:5px;
  content:'';
  border:4px solid #8996a6;
  border-width:5px 8px;
  border-color:transparent #8996a6 transparent transarent
}
```

### 扇形

<iframe height="300" style="width: 100%;" scrolling="no" title=" 扇形 " src="https://codepen.io/chiyu-git/embed/MWewqOe?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true"></iframe>

## Button

一般不使用 button 元素，因为默认样式的表现比较奇怪，需要进行许多初始化操作

非表单按钮使用 a 标签

## 层级联动

通过 options 对象的 children 属性确定层级

```jsx
import React,{useState}from 'react';

const MenuList = (props) => {
  const {selected,setSelected,listIndex} = props
  // 这一次要渲染的MenuList
  const options = selected[listIndex]
  
  const handleSelect = (ev) => {
    const index = ev.currentTarget.dataset.index

    if(options[index].children===undefined){
      // 已经是最后一级了，将value渲染到input中

    }else{
      // 展开下一级，options[index].children 就是下一个要渲染的MenuList
      // 确认listIndex，提取出前面的
      setSelected([...selected.slice(0,listIndex+1),options[index].children])
    }
  }
  // 渲染对应index
  return (
    <ul className='design_cascader_menu_list'>
      {options.map((option,index) => {
        let arrowRight = null
        if(option.children) arrowRight =  <i className='iconfont icon-arrow_right'></i>
        return (
          <li 
            key={option.value} 
            onClick={handleSelect}
            data-index={index}
            >
            <span>{option.value}</span>
            {arrowRight}
          </li>
        )
      })}
    </ul>
  )
}

const Menu = (props) => {
  const {options} = props
  const [selected,setSelected] = useState([options])
  console.log(selected);
  /**
   * [
   *  [{value,children},{value,children}]
   *  [{value,children},{value,children}]
   *  [{value},{value}]
   * ]
   * 
   */
  // 行内盒，通过定位在input底下，多个ul行内排列
  // 全局只有一个Menu
  return (
    <div className='design_cascader_menu_container slide-down-enter'>
      {selected.map((option,index) => {
        // 上一个options[index].children
        return (
          <MenuList
            selected={selected} 
            setSelected={setSelected}
            listIndex={index}
            key={index}
          />
        )
      })}
    </div>
  );
};

export default Menu;
```

## Tooltip

在目标上 hover 的时候，显示 tooltip，事件 onmouseover

使用 getBoundingClientRect 获取目标位置，绝对定位，在目标位置出现

动画

https://www.jianshu.com/p/561e6e930e0d

如果是在 tranform 元素的内部 fixed 定位会受到影响，所以需要通过 protol 插入到 body

## 拖拽类型组件：进度条拖拽、moveable-view

### 基本思路

1. inner.onmousedown 记录 inner 的初始位置
   1. container.onmousemove 计算鼠标移动的距离，然后把距离插值赋值给 inner。
      1. 移动的记录需要做边界判断
         1. 进度条、音量控制等场景都是从左到右，从上到下，且默认情况下 inner 和 container 都是左对齐、下对齐的可以直接使用 offsetWidth 相减，得出最大移动距离
         2. 真正的区间应该通过 getBoundingRect 获取 x y 轴坐标之后相减得出
      2. 只有 inner.onmousedown 触发过了才能触发 container.onmousemove，flag 为 true
   2. document.onmouseup 更改触发标识，flag 为 false，保证无论鼠标在何处都能取消相关逻辑

## 选择器

hover 状态下弹出候选，要保证候选的 ul 结构在 a 的下面，否则无法在 hover 状态下控制 ul

```css
<a className='language_btn'>
<span>JavaScript</span>
&nbsp;&nbsp;<i className='iconfont icon-arrow_down'></i>
<ul className='candidate_list'>
<li>C++</li>
<li>Java</li>
<li>JavaScript</li>
<li>Python</li>
</ul>
</a>

.language_btn{
  display: inline-block;
  position: relative;
  padding: 0 10px;
  line-height:40px;
  background-color: #516169;
  color: white;
  .candidate_list{
    display: none;
    position: absolute;
    left:0px;
    z-index: 4;
    width: 120px;
    padding:0 10px;
    background-color: #516169;
  }
  &:hover{
    background-color: #46535a;
    .candidate_list{
      display: block;
    }
  }
```

或者用 JS 实现，这样的话 ul 的结构可以在任意地方

## useRanger 源码记录

### 疑问

为什么会有 tempvalues 和 values 两个

tempValues 感觉没啥用嗄

### 基本思路

通过百分比移动 handle ，而不是像素的绝对值？

+ handle 在 track 内绝对定位，left 值百分比参考 track 的宽度，计算出 value 的百分比应用在 left 身上即可做出正确的位移

通过 values 数组的长度，控制 handle 的数量

除了拖拽，还支持通过箭头控制、键盘箭头控制

### 明确元素

track：位移轨道

handle：手柄、把柄、把手，仅能在轨道中做位移

> 游戏手柄一般叫 controller、gamepad

value：因为是 ranger，通过 min 和 max 设置了范围之后，value 值

## 导航栏

### 横向导航

- 无拖拽功能，div>a 标签，布局简单
- 有拖拽功能，div>ul>li>a，便于实现逻辑

## 日期选择器

设计理念：http://www.woshipm.com/pd/2370479.html

具体实现：https://zhuanlan.zhihu.com/p/57043693 https://juejin.im/post/5d71be89e51d4561fc620aba

### 基本原理

获取每个月第一天和最后一天，根据星期几推算出上一个月和下一个月有多少天需要预览

最后生成 7*7 的数组，用于展示即可

### 追加需求：

接受一个日期数组，实现红点，点亮

要怎么实现好呢？感觉还是生成的时候通过属性标识比较好，等生成后再通过 DOM 去添加类名，不太合适

### 范围选择

需要两个日期选择器，一个选择开头，一个选择结尾即可

通过标识区分两种选择器，开头选择器后面的日期染色

结尾选择器，前面的日期染色

## 轮播图

### 基础

+ 根据图片的数量动态生成 ul>li 结构

    ```js
    // 循环生成list结构
    const carousel_list = document.createElement("ul")
    carousel_list.className="carousel_list"
    for (let i = 0; i < imgList.length; i++) {
      // 创建li，添加达到list当中
      const list_item = document.createElement("li")
      list_item.className="list_item"
      list_item.innerHTML = '<a href="javascript:;"><img src="./'+imgList[i]+'"/></a>'
      carousel_list.appendChild(list_item)
    }
    carousel_container.appendChild(carousel_list)
    // css样式
    const styleNode = document.createElement("style")
    styleNode.innerHTML = "#wrap .carousel_list{width:"+imgList.length+"00%}" + 
      "#wrap .carousel_list > .list_item{width:"+1/imgList.length*100+"%}"
    document.documentElement.appendChild(styleNode)
    
    // carousel_list开启了绝对定位，父级高度塌陷，用js指定高度
    setTimeout(() => {
      // 获取图片的高度，需要在图片渲染成功之后
      const carousel_img = document.querySelector("#wrap > .carousel_container > .carousel_list img")
      carousel_container.style.height = carousel_img.offsetHeight + "px"
    },100)
    ```

+ 根据是否存在 span 控件，选择生成 html 结构

### 方案一

+ 直接修改图片 src 更换图片
+ 用一个变量保存索引

### 方案二

+ 3D 方案，实现每屏的出入场，即可随机切换

### 方案三

#### 原理

+ 复制两组轮播图片
    + 当滑动到第一组的第一张时，跳转到第二组的第一张
    + 当滑动到第二组的最后一张时，跳转到第第一组的最后一张

#### 实现

**滑屏逻辑**

+ <a href='../demo/utils/drag.js'>drag</a>

**超出控制**

+ 当滑动到第一组的第一张时，跳转到第二组的第一张
+ 当滑动到第二组的最后一张时，跳转到第第一组的最后一张

**index 的抽象**

+ 偏移比 || 屏数

**自动轮播**

+ 自动轮播的超出控制
+ 共享 index
+ 自动轮播的逻辑分散在 touchstart 和 touchend 中，不断调用 touchend 的逻辑，实现换屏，复用 touchstart 的超出控制 能不能再 touchend 实现超出控制呢？不行，滑到最后一张的时候，需要过渡，但是静默跳转必须取消过渡，还是在 touchstart 实现较好
+ 自动轮播只有一个方向，在这种情况下，如果向右轮播，是永远不会去到第一组的第一张图的，所以考虑超出控制的时候不用考虑从第一组超出。除了第一次开始轮播会走一遍第一组，之后只会在第一组最后一张到第二组间轮播
+ 需要在第二组倒数第二张到倒数第一张的过渡之后，再跳转到第一组最后一张，在超出判断加一个大于过渡事件的延时定时器即可，不会影响效果
+ 自动轮播与手动轮播的冲突

    - 移入 or 点击：清除自动轮播的定时器
    - 移出 or end：重新开启，重点击的地方开始自动轮播

### 无缝划屏基本布局

- 图片、a 标签 display：block，因为原本是 inline-block，会把排版换行的空格当成一个空格，就转换成了图片间的空白，取消图片间的空格：设置为 block，无视空格，或者把 font-size 设置为 0
- css 如何根据传入的数组的 length 确定相应 width？
- JS 生成的 DOM 结构同样存在渲染问题，需要延时 100ms 才能获取到刚刚生成的节点的信息，只延时 0ms 的话，是获取不到的
- 滑屏区域占满视口，宽度百分百
- 子元素宽度固定，父元素等于子元素个数\*100%，子元素等于 1/子元素个数\*100%

### 无缝划屏功能逻辑

- 用 index 抽象图片的索引，抽象 ul 的位置，控制 ul 的偏移
- 抽象 ul 的偏移，控制 ul 的位置
    - carousel_index = Math.round(carousel_index)
    - 四舍五入，实现：滑动过半切换屏幕（index 增加），没有滑动过半就恢复原位 (index 不变)

### 2d

- tranlate 的偏移量，offsetLeft 无法同步，不在一个图层
- 要自己计算出 tranlate 的偏移量，代替 offsetLeft API

## 懒加载

## 瀑布流

参考：http://www.woshipm.com/pd/1379.html

### 概念

+ 内容框宽度固定，高度不固定。
+ 内容框从左到右排列，一行排满后，其余内容框就会按顺序排在短的一列后。
+ 由于移动设备屏幕比电脑小，一个屏幕显示的内容不会非常多，因此可能要经常翻页。而在建网站时使用瀑布流布局，用户则只需要进行滚动就能够不断浏览内容。（这一点和懒加载有一点像）

### 方案一：绝对定位

+ 先计算一行能够容纳几列元素（因为我们需要在不同的设备上浏览）
+ 然后通过计算比较找出这一列元素中高度之和最小一列，然后将下一行的第一个元素添加至高度之和最小的这一列的下面，然后继续计算所有列中高度之和最小的那一列，然后继续将新元素添加至高度之和最小的那一列后面，直至所有元素添加完毕。

    ```js
    const COLUMN_WIDTH = 100
    const GAP = 10
    const offsetLeft = COLUMN_WIDTH+GAP


    

    

    

    

    

    

    

    

    

    

    

    

    

    

    

    


    // 随机生成一个不定高的矩形

    let randomH = 0

    const createRect = function(top,left){

      const newRect = document.createElement('div')

      randomH = Math.floor(Math.random()*100+100) // [100,200]

      newRect.style=`

        position:absolute;

        left:${left}px;

        top:${top}px;

        width:${COLUMN_WIDTH}px;

        height:${randomH}px;

        background-color:pink;

      `

      return newRect

    }

    
    // 插入cascade
    const imageNum = 20
    
    waterfall()
    function waterfall(){
      const viewportW = document.documentElement.clientWidth
      const columns = Math.floor(viewportW/(COLUMN_WIDTH+GAP))
      // 初始化列
      const columnHeight = new Array(columns).fill(0)
    
      for (let i = 0; i < 20; i++) {
        let newRect = null
        // 新建一个矩形
        if(i<columns){
          // 第一行
          newRect = createRect(0,i*offsetLeft)
          // 更新列的高度
          columnHeight[i] += randomH+GAP
        }else{
          // 计算最短的列
          const {height,index} = columnHeight.reduce(function(min,val,i,arr){
            if(val<min.height){
              return {height:val,index:i}
            }else{
              return min
            }
          },{height:Number.MAX_SAFE_INTEGER,index:0})
          // 插入到最短的列中
          newRect = createRect(height,index*offsetLeft)
          columnHeight[index] += randomH+GAP
        }
        cascade.appendChild(newRect)
      }
    }
    
    //当页面尺寸发生变化时，触发函数，实现响应式
    window.onresize = function () {
      cascade.innerHTML = ''
      waterfall();
    }
    ```

### 方案二：多列浮动

+ 各列固定宽度，并且左浮动；
+ 一列中的数据块为一组，列中的每个数据块依次排列即可；
+ 更多数据加载时，需要分别插入到不同的列上；
+ 如何给生成的 DOM 结构添加统一的样式？
    + 动态添加 style 标签

    ```js
    const COLUMN_WIDTH = 100
    const GAP = 10
    
    // 随机生成一个不定高的矩形
    const createRect = function(){
      const newRect = document.createElement('div')
      const randomH = Math.floor(Math.random()*100+100) // [100,200]
      newRect.style=`
        width:100%;
        height:${randomH}px;
        background-color:pink;
        margin-top:${GAP}px;
      `
      return newRect
    }
    
    waterfall()
    function waterfall(){
      const viewportW = document.documentElement.clientWidth
      const columns = Math.floor(viewportW/(COLUMN_WIDTH+GAP))
      // 根据列数生成相应的ul
      for (let i = 0; i <columns ; i++) {
        cascade.appendChild(document.createElement('ul'))
      }
      // 如何给生成的DOM结构添加统一的样式
      let style = document.createElement('style')
      // 只给90%的位置用于瀑布流的展示
      style.innerHTML = `
        #cascade>ul{
          float:left;
          box-sizing:border-box;
          width:${90/columns}%;
          border:1px solid;
          margin-left:${GAP}px;
        }
      `
      const ulNodes = document.querySelectorAll('#cascade>ul')
      
      for (let i = 0; i < 20; i++) {
        // 计算出高度最小的一列
        let minHeightUl = 0
        Array.from(ulNodes).reduce(function(min,node,i){
          if(node.offsetHeight < min){
            minHeightUl = i
            return node.offsetHeight
          }else{
            return min
          }
        },Number.MAX_SAFE_INTEGER)
    
        // 插入新的图片
        ulNodes[minHeightUl].appendChild(createRect())
    
        document.head.appendChild(style)
      }
    }
    
    //当页面尺寸发生变化时，触发函数，实现响应式
    window.onresize = function () {
      cascade.innerHTML =''
      waterfall();
    }
    ```

### 方案二变种：flex

- ```js
    const COLUMN_WIDTH = 100
    const GAP = 10
    
    // 随机生成一个不定高的矩形
    const createRect = function(){
      const newRect = document.createElement('div')
      const randomH = Math.floor(Math.random()*100+100) // [100,200)
      newRect.style=`
        width:${COLUMN_WIDTH}px;
        height:${randomH}px;
        background-color:pink;
        margin-bottom:${GAP}px;
        break-inside:avoid;
      `
      return newRect
    }
    
    waterfall()
    function waterfall(){
      const viewportW = document.documentElement.clientWidth
      const columns = Math.floor(viewportW/(COLUMN_WIDTH+GAP))
      // 根据列数生成 itemContainer
      for (let i = 0; i <columns ; i++) {
        const itemContainer = document.createElement('div')
        itemContainer.className = 'itemContainer'
        cascade.appendChild(itemContainer)
      }
    
      let style = document.createElement('style')
      // 只给90%的位置用于瀑布流的展示
      // 默认值为normal,在项目没有高度的时候表现为 stretch
      style.innerHTML = `
        #cascade{
          display:flex;
          flex-direction:row;
          align-items:flex-start;
        }
        #cascade>.itemContainer{
          display:flex;
          flex-direction:column;
          width:${90/columns}%;
          margin-left:${GAP}px;
        }
      `
      const containers = document.querySelectorAll('#cascade>.itemContainer')
      for (let i = 0; i < 20; i++) {
    
        // 插入新的图片
        // 计算出高度最小的一列
        let minHeightContainer = 0
        Array.from(containers).reduce(function(min,node,i){
          if(node.offsetHeight < min){
            minHeightContainer = i
            return node.offsetHeight
          }else{
            return min
          }
        },Number.MAX_SAFE_INTEGER)
    
        // 插入新的图片
    
        containers[minHeightContainer].appendChild(createRect())
        document.head.appendChild(style)
      }
    }
    
    //当页面尺寸发生变化时，触发函数，实现响应式
    window.onresize = function () {
      cascade.innerHTML =''
      waterfall();
    }
    ```

### 方案三：多栏布局

- column-gap 有默认值
- break-inside 是否断点
- margin-top 改为 margin-bottom，兼容第一行

    ```js
    const COLUMN_WIDTH = 100
    const GAP = 10
    
    // 随机生成一个不定高的矩形
    const createRect = function(){
      const newRect = document.createElement('div')
      const randomH = Math.floor(Math.random()*100+100) // [100,200]
      newRect.style=`
        width:${COLUMN_WIDTH}px;
        height:${randomH}px;
        background-color:pink;
        margin-bottom:${GAP}px;
        break-inside:avoid;
      `
      return newRect
    }
    
    waterfall()
    function waterfall(){
      const viewportW = document.documentElement.clientWidth
      const columns = Math.floor(viewportW/(COLUMN_WIDTH+GAP))
    
      // 如何给生成的DOM结构添加统一的样式
      let style = document.createElement('style')
      // 多栏布局
      style.innerHTML = `
        #cascade{
          column-count:${columns};
          column-gap:${GAP}px;
        }
      `
      for (let i = 0; i < 20; i++) {
    
        // 插入新的图片
        cascade.appendChild(createRect())
    
        document.head.appendChild(style)
      }
    }
    
    //当页面尺寸发生变化时，触发函数，实现响应式
    window.onresize = function () {
      cascade.innerHTML =''
      waterfall();
    }
    ```

### 方案三变种：grid

+ https://github.com/chokcoco/iCSS/issues/40

### 对比

**方案一：绝对定位**

+ 优点：
    + 可谓是最优的一种方案，方便添加数据内容，窗口变化，列数/数据块都会自动调整；
+ 缺点：
    + 需要实现知道数据块高度，如果其中包含图片，需要知道图片高度；
    + JS 动态计算数据块位置，当窗口缩放频繁，可能会狂耗性能。

**方案二：多列浮动**

- 优点：
    - 布局简单
    - 不用明确知道数据块高度，当数据块中有图片时，就不需要指定图片高度
- 缺点：
    - 列数固定，扩展不易，当浏览器窗口大小变化时，只能固定的 x 列，如果要添加一列，很难调整数据块的排列；
    - 滚动加载更多数据时，还要指定插入到第几列中，还是不方便。

**方案三：多栏布局**

- 优点：
    - 直接 CSS 定义，最方便了；
    - 扩展方便，直接往容器里添加内容即可。
- 缺点：
    - 只有高级浏览器中才能使用；
    - 还有一个缺点，他的数据块排列是从上到下排列到一定高度后，再把剩余元素依次添加到下一列，这个本质上就不一样了；
    - 鉴于这两个主要缺点，注定了该方法只能局限于高端浏览器，而且，更适合于文字多栏排列。

### 优化

+ 库的实现：http://www.woshipm.com/pd/1379.html

- http://cued.xunlei.com/log031
- https://www.zhangxinxu.com/wordpress/2012/03/%E5%A4%9A%E6%A0%8F%E5%88%97%E8%A1%A8%E5%8E%9F%E7%90%86%E4%B8%8B%E5%AE%9E%E7%8E%B0%E7%9A%84%E7%80%91%E5%B8%83%E6%B5%81%E5%B8%83%E5%B1%80-waterfall-layout/
- **注意：有时候可能是需要块中的图片被加载才能确定块的大小，没错，Masonry 提供了一个方法叫 imagesLoaded，可****参考：http://masonry.desandro.com/demos/images.html**
    **但是一般不推荐这样，因为图片加载可能是个很耗时的过程，特别是瀑布流这样的有大量图片的页面。那么一般是将图片的 height 信息记录在数据库中，显示的时候直接就确定了大小而不需要等待图片加载。**

## 选项卡

+ ```react
    <BrowserRouter>
      <ul className='nav'>
        <li className='tab'><NavLink to='/codeTest' activeClassName='activeLink'>代码考核</NavLink></li>
        <li className='tab'><NavLink to='/uploadFile' activeClassName='activeLink'>文件演示</NavLink></li>
        <li className='tab'><NavLink to='/equipmentInfo' activeClassName='activeLink'>设备信息</NavLink></li>
      </ul>
      <Switch>
        <Route exact path='/' component={CodeTest}/>
        <Route path='/codeTest' component={CodeTest}/>
        <Route path='/uploadFile' component={UploadFile} />
        <Route path='/equipmentInfo' component={EquipmentInfo} />
      </Switch>
    </BrowserRouter>
    ```

## 计算器

+ ```html
    <div class="calculator">
        <header class="cal-header">简易计算器</header>
        <main class="cal-main">
            <div class="origin-value">0</div>
            <div class="cal-keyboard">
                <ul class="cal-items">
                    <li data-action="num">7</li>
                    <li data-action="num">8</li>
                    <li data-action="num">9</li>
                    <li data-action="operator">÷</li>
                    <li data-action="num">4</li>
                    <li data-action="num">5</li>
                    <li data-action="num">6</li>
                    <li data-action="operator">x</li>
                    <li data-action="num">1</li>
                    <li data-action="num">2</li>
                    <li data-action="num">3</li>
                    <li data-action="operator">-</li>
                    <li data-action="num">0</li>
                    <li data-action="operator">清空</li>
                    <li data-action="operator">=</li>
                    <li data-action="operator">+</li>
                </ul>
            </div>
        </main>
    </div>
    ```

    ```css
    body, ul, li,select {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }
    ul,li {list-style: none;}
    .calculator {
        max-width: 300px;
        margin: 20px auto;
        border: 1px solid #eee;
        border-radius: 3px;
    }
    .cal-header {
        font-size: 16px;
        color: #333;
        font-weight: bold;
        height: 48px;
        line-height: 48px;
        border-bottom: 1px solid #eee;
        text-align: center;
    }
    .cal-main {
        font-size: 14px;
    }
    .cal-main .origin-value {
        padding: 15px;
        height: 40px;
        line-height: 40px;
        font-size: 20px;
        text-align: right;
        overflow: hidden;
        text-overflow:ellipsis;
        white-space: nowrap;
    }
    .cal-main .origin-type,
    .cal-main .target-type {
        padding-left: 5px;
        width: 70px;
        font-size: 14px;
        height: 30px;
        border: 1px solid #eee;
        background-color: #fff;
        vertical-align: middle;
        margin-right: 10px;
        border-radius: 3px;
    }
    .cal-keyboard {
        overflow: hidden;
    }
    .cal-items {
        overflow: hidden;
    }
    .cal-items li {
        user-select: none;
        float: left;
        display: inline-block;
        width: 75px;
        height: 75px;
        text-align: center;
        line-height: 75px;
        border-top: 1px solid #eee;
        border-left: 1px solid #eee;
        box-sizing: border-box;
    }
    li:nth-of-type(4n+1) {
        border-left: none;
    }
    li[data-action=operator] {
        background: #f5923e;
        color: #fff;
    }
    .buttons {
        float: left;
        width: 75px;
    }
    .buttons .btn {
        width: 75px;
        background-color: #fff;
        border-top: 1px solid #eee;
        border-left: 1px solid #eee;
        height: 150px;
        line-height: 150px;
        text-align: center;
    }
    .btn-esc {
        color: #ff5a34;
    }
    .btn-backspace {
        position: relative;
    }
    ```

    ```js

    var Calculator = {

      init: function () {

          var that = this;

          if (!that.isInited) {

              that.isInited = true;

              // 保存操作信息

              // total: Number, 总的结果

              // next: String, 下一个和 total 进行运算的数据

              // action: String, 操作符号

              that.data = {total: 0, next: '', action: ''};

              that.bindEvent();

          }

      },

      bindEvent: function () {

          var that = this;

          // 请补充代码：获取 .cal-keyboard 元素

          var keyboardEl = document.querySelector('.cal-keyboard');

          keyboardEl && keyboardEl.addEventListener('click', function (event) {

              // 请补充代码：获取当前点击的dom元素

              var target = event.target;

              // 请补充代码：获取target的 data-action 值

              var action = target.dataset.action;

              // 请补充代码：获取target的内容

              var value = target.innerText;

              console.log(target,action,value)

              if (action === 'num' || action === 'operator') {

                  that.result(value, action === 'num');

              }

          });

      },

      result: function (action, isNum) {

          var that = this;

          var data = that.data;

          if (isNum) {

              data.next = data.next === '0' ? action : (data.next + action);

              !data.action && (data.total = 0);

          } else if (action === '清空') {

              // 请补充代码：设置清空时的对应状态

              data.total = 0;

              data.next = '';

              data.action = '';

          } else if (action === '=') {

              if (data.next || data.action) {

                  data.total = that.calculate(data.total, data.next, data.action);

                  data.next = '';

                  data.action = '';

              }

          } else if (!data.next) {

              data.action = action;

          } else if (data.action) {

              data.total = that.calculate(data.total, data.next, data.action);

              data.next = '';

              data.action = action;

          } else {

              data.total = +data.next || 0;

              data.next = '';

              data.action = action;

          }

      
          // ���补充代码：获取 .origin-value 元素
          var valEl = document.querySelector('.origin-value');
          valEl && (valEl.innerHTML = data.next || data.total || '0');
      },
      calculate: function (n1, n2, operator) {
          n1 = +n1 || 0;
          n2 = +n2 || 0;
          if (operator === '÷') {
              // 请补充代码：获取除法的结果
              if(n2==0) return 0
              return (n1/n2).toFixed(2)*1;
          } else if (operator === 'x') {
              // 请补充代码：获取乘法的结果
              return (n1*n2).toFixed(2)*1;
          } else if (operator === '+') {
              // 请补充代码：获取加法的结果
              return (n1+n2).toFixed(2)*1;
          } else if (operator === '-') {
              // 请补充代码：获取减法的结果
              return (n1-n2).toFixed(2)*1;
          }
      }
    };
    Calculator.init();
    ```

# 用户体验

## 常见措施

- [选择合适的鼠标光标](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fmouse-cursor)
- [扩大可点击区域](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fextend-hit-area)
- [自定义复选框](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fcustom-checkbox)
- [自定义单选框](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fcustom-radio)
- [输入框完美居中](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Finput-align)
- [通过阴影弱化背景](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fshadow-weaken-background)
- [通过模糊弱化背景](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fblurry-weaken-background)
- [自定义文字下划线](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Ftext-underline)
- [自定义scroll滚动条](https://link.juejin.cn/?target=https%3A%2F%2Flhammer.cn%2FYou-need-to-know-css%2F%23%2Fscrollbar)

# 未分类

## 纯 CSS 实现滚动条

https://juejin.im/post/5c35953ce51d45523f04b6d2

## 视口滚动条属于哪

chrome 滚动条是加给 document 的（初始包含块），html height 100% ,大小是视窗大小

继承是一层层继承下来的，不可以隔代继承

body 的滚动条可以通过 html 传递给 document，如果 body 是 overflow:scroll 或者 overflow:auto,且 html 没有自己的滚动条（即不是 overflow:scroll 或者 overflow:auto），那么 body 的滚动条就会传递给 document

如果 html 和 body 都写了 overflow:scroll 或者 overflow:auto，html 的 scroll 永远给到文档，而 body 自己也会有一个滚动条

  ```css
  html, body {
    height: 100%;
    overflow: hidden;
   }
  ```

html 开启 hidden 就可以禁止默认滚动条，因为默认滚动条都是 body 可能是 body 传递过去的，此时中断了传递

会出现滚动条是因为溢出了容器，所以容器肯定是小于超高的元素的，当我们滑动滚动条的时候容器是没有动的，只是滚动条加在了容器身上

一般会给一个全局的 wrap，让滚动条出现在 wrap 身上，无论怎么滑动都不会影响初始包含块，可以用来模拟固定定位（相对于有滚动条的 wrap 绝对定位，就是固定定位的效果了）

移动端的固定定位一般都是用绝对定位来模拟的
