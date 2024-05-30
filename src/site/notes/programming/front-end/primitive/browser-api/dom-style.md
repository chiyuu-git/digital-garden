---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-02-16-Thu, 11:01:12 am","date-modified":"2023-12-19-Tue, 2:41:32 pm","permalink":"/programming/front-end/primitive/browser-api/dom-style/","dgPassFrontmatter":true}
---


![](/img/user/programming/front-end/primitive/browser-api/dom-style/image-20230216112523204.png)

# 元素的宽高

gcs() cs 返回的只是元素内容区高度

因为没有 css 属性时描述内容区 + 内边距的，即是转换成了 border-box

## Element.clientHeight，Element.clientWidth

`Element.clientHeight` 属性返回一个整数值，表示元素节点的 CSS 高度（单位像素），只对块级元素生效，对于行内元素返回 `0`。如果块级元素没有设置 CSS 高度，则返回实际高度。 @@@

除了元素本身的高度，它还包括 `padding` 部分，但是不包括 `border`、`margin`。如果有水平滚动条，还要减去水平滚动条的高度。

注意，这个值始终是整数，如果是小数会被四舍五入。

## Element.offsetHeight，Element.offsetWidth

`Element.offsetHeight` 属性返回一个整数，表示元素的 CSS 垂直高度（单位像素），包括元素本身的高度、padding 和 border，以及水平滚动条的高度（如果存在滚动条）。

`Element.offsetWidth` 属性表示元素的 CSS 水平宽度（单位像素），其他都与 `Element.offsetHeight` 一致。

这两个属性都是只读属性，只比 `Element.clientHeight` 和 `Element.clientWidth` 多了边框的高度或宽度。如果元素的 CSS 设为不可见（比如 `display: none;`），则返回 `0`。

**注意**：虽然是 offset 系列，但是却不是定位相关的属性。进行偏移的时候移动的 **可见整体**，不包括滚动条

## 视口

`document.documentElement` 的 `clientHeight` 属性，返回当前视口的高度（即浏览器窗口的高度），等同于 `window.innerHeight` 属性减去水平滚动条的高度（如果有的话）。

`document.body` 的高度则是网页的实际高度。一般来说，`document.body.clientHeight` 大于 `document.documentElement.clientHeight`。

  ```js
  // 视口高度
  document.documentElement.clientHeight
  
  // 网页总高度，考虑了垂直滚动，相当于是可滚动范围
  document.body.clientHeight
  ```

  ```js
  // 获取视口的尺寸
  var w = document.documentElement.clientWidth;
  var w2 = document.documentElement.offsetWidth;
  console.log(w,w2); // 任何时候都是一样的，不包括滚动条，视口的宽高，视口的padding和border都是往内缩的，可以理解为视口默认是border-box
  ```

对于其他元素而言，正常遵循 API 的特性

注意：在 ie10 及 ie10 以下，根标签的 clientWidth 和 offsetWidth 和普通的一样

![mobile-basic](../../mobile/mobile-basic.md#如何获取三个视口的值)

## Element.scrollHeight，Element.scrollWidth

`Element.scrollHeight` 属性返回一个整数值（小数会四舍五入），表示当前元素的总高度（单位像素），包括溢出容器、当前不可见的部分。它包括 `padding`，还包括伪元素（`::before` 或 `::after`）的高度。但是不包括 `border`、`margin` 以及水平滚动条的高度，

`Element.scrollWidth` 属性表示当前元素的总宽度（单位像素），其他地方都与 `scrollHeight` 属性类似。这两个属性只读。

整张网页的总高度可以从 `document.documentElement` 或 `document.body` 上读取。

  ```js
  // 返回网页的总高度
  document.documentElement.scrollHeight
  document.body.scrollHeight
  ```

- 注意，如果元素节点的内容出现溢出，即使溢出的内容是隐藏的，`scrollHeight` 属性仍然返回元素的总高度。

## Element.clientLeft，Element.clientTop

`Element.clientLeft` 属性等于节点左边框（left border）的宽度（单位像素），不包括左侧的 `padding` 和 `margin`。如果没有设置左边框，或者是行内元素（`display: inline`），该属性返回 `0`。该属性总是返回整数值，如果是小数，会四舍五入。

`Element.clientTop` 属性等于节点顶部边框的宽度（单位像素），其他特点都与 `clientLeft` 相同。

**提示:** 你可以使用 [style.borderTopWidth](https://www.runoob.com/jsref/prop-style-bordertopwidth.html) 属性来获取元素顶部框的宽度。

**提示:** 要返回元素左侧边框的宽度可以使用 [clientLeft](https://www.runoob.com/jsref/prop-element-clientLeft.html) 属性。

# 元素的位置

## Element.scrollLeft，Element.scrollTop

`Element.scrollLeft` 属性表示当前元素的水平滚动条向右侧滚动的像素数量

`Element.scrollTop` 属性表示当前元素的垂直滚动条向下滚动的像素数量

对于那些没有滚动条的网页元素这两个属性总是等于 0。

如果要查看整张网页的水平的和垂直的滚动距离，要从 `document.documentElement` 元素上读取。

```js
  document.documentElement.scrollLeft
  document.documentElement.scrollTop
```

这两个属性都可读写，设置该属性的值，会导致浏览器将当前元素自动滚动到相应的位置。

**注意**：不用加 px 单位

**判断滚动条是否滚动到底**

- 垂直滚动条，容器的：scrollHeight - scrollTop = clientHeight
- 水平滚动，容器的：scrollWidth - scrollLeft = clientWidth
- 三个属性都是容器的，clientHeight 是容器自身的高度，代表的是滚动按钮在整个滚动条的占比

## Element.offsetParent

`Element.offsetParent` 属性返回最靠近当前元素的、并且 CSS 的 `position` 属性不等于 `static` 的上层元素。

该属性主要用于确定子元素位置偏移的计算基准，`Element.offsetTop` 和 `Element.offsetLeft` 就是 `offsetParent` 元素计算的。

如果该元素是不可见的（`display` 属性为 `none`），或者位置是固定的（`position` 属性为 `fixed`），则 `offsetParent` 属性返回 `null`。

如果某个元素的所有上层节点的 `position` 属性都是 `static`，则 `Element.offsetParent` 属性指向 `<body>` 元素。

保证：html 和 body 之间的 margin 被清除

本身定位为 fiexd，不管你父级有没有定位:

+ 火狐的 offsetParent --> body
+ 非火狐 offsetParent --> null //此时 offsetLeftTop 还是参照于 body

非 fiexd（包括本身不定位）:

+ 父级没有定位 offsetParent --> body
+ 父级有定位（除了 static 都是开启了定位） offsetParent --> 定位父级

body 的 offsetparent：null，就算 html、body 的 margin 没有 reset，也不影响，offsetLeftTop 的值都是 0

区别 parentNode 和 offsetParent

  - parentNode：直接父级
  - offsetParent：**类似于** css 的包含块

## Element.offsetLeft，Element.offsetTop

`Element.offsetLeft` 返回当前元素左上角相对于 `Element.offsetParent` 节点的水平位移，element.offsetTop` 返回垂直位移，单位为像素。

通常，这两个值是指相对于父节点 **内边距边界** 的位移。

**没有开启定位的元素也有这两个属性**

## 获取元素的绝对位置

下面的代码可以算出元素左上角相对于整张网页的坐标。

  ```js
  function getElementPosition(e) {
    var x = 0;
    var y = 0;
    while (e !== null)  {
      x += e.offsetLeft;
      y += e.offsetTop;
      e = e.offsetParent;
    }
    return {x: x, y: y};
  }
  ```

首先 html 和 body 的 margin 要重置，因为循环最终只能去到 body

boder margin 会影响这个函数的取值

  ![1548123244295](/img/user/programming/front-end/primitive/browser-api/dom-interface/1548123244295.png)

offsetLeftTop 会直接无视 border 的值

自己和父元素的 **内边距** 不会对 offsetLeftTop 造成影响，因为左上角始终不变，内容区被挤向右下方而已

  ![1548124728065](/img/user/programming/front-end/primitive/browser-api/dom-interface/1548124728065.png)

**自己的外边距、父元素的外边距** 会影响 offsetLeftTop 的值，因为从内边距边界出发

## Element.getBoundingClientRect()

`Element.getBoundingClientRect` 方法返回一个对象，提供当前元素节点的大小、位置等信息，基本上就是 CSS 盒状模型的所有信息。

`getBoundingClientRect` 方法返回的 `rect` 对象，具有以下属性（全部为只读）。

  - `x`：元素左上角相对于视口的横坐标
  - `y`：元素左上角相对于视口的纵坐标
  - `height`：元素高度
  - `width`：元素宽度
  - `left`：元素左上角相对于视口的横坐标，与 `x` 属性相等
  - `right`：元素右边界相对于视口的横坐标（等于 `x + width`）
  - `top`：元素顶部相对于视口的纵坐标，与 `y` 属性相等
  - `bottom`：元素底部相对于视口的纵坐标（等于 `y + height`）
- 于元素相对于视口（viewport）的位置，会随着页面滚动变化，因此表示位置的四个属性值，都不是固定不变的。如果想得到绝对位置，可以将 `left` 属性加上 `window.scrollX`，`top` 属性加上 `window.scrollY`。

注意，`getBoundingClientRect` 方法的所有属性，都把边框（`border` 属性）算作元素的一部分。也就是说，都是从边框外缘的各个点来计算。因此，`width` 和 `height` 包括了元素本身 + `padding` + `border`。

另外，上面的这些属性，都是继承自原型的属性，`Object.keys` 会返回一个空数组，这一点也需要注意。

  ```
  var rect = document.body.getBoundingClientRect();
  Object.keys(rect) // []
  ```

上面代码中，`rect` 对象没有自身属性，而 `Object.keys` 方法只返回对象自身的属性，所以返回了一个空数组。

## 获取元素的相对位置（到视口）

### 绝对位置减去元素滚动的距离

```js
  function getPointRe(node){
    //while循环叠加offsetParent的offsetTop和offsetLeft
    var x =0;
    var y = 0;
    while(node){
      x+=node.offsetLeft;
      y+=node.offsetTop;
  
      node = node.offsetParent;
    }
  
    var L = document.documentElement.scrollLeft||document.body.scrollLeft;
    var T = document.documentElement.scrollTop||document.body.scrollTop;
  
    return {x:x-L,y:y-T};
  }
```

### getBoundingClientRect

一个元素四个角的相对位置

getBoundingClientRect + 滚动条滚动时元素滚动的距离→绝对位置

```js
  /*
  代表元素border-box的尺寸（可以使用offsetWH代替）
  height
  width
  
  元素左上角的相对位置
  left
  top
  
  元素右下角的相对位置
  right
  bottom
  */
  window.onload=function(){
    var inner1 = document.querySelector("#inner1");
    var point = inner1.getBoundingClientRect();
    console.log(point);
  }
```

getBoundingClientRect() 方法。它返回一个对象，其中包含了 left、right、top、bottom 四个属性，分别对应了该元素的左上角和右下角相对于浏览器窗口（viewport）左上角的距离。

### Demo Mac 停靠栏

```js
  var r = 320;
  var imgNodes = document.querySelectorAll("#wrap > img");
  
  document.onmousemove=function(ev){
    ev =  ev||event;
    for(var i=0;i<imgNodes.length;i++){
      var a = imgNodes[i].getBoundingClientRect().left + imgNodes[i].offsetWidth/2 - ev.clientX;
      var b = imgNodes[i].getBoundingClientRect().top + imgNodes[i].offsetHeight/2 - ev.clientY;
      var c = Math.sqrt(a*a+b*b);
  
  
      if(c>=r){
        c=r;
      }
      imgNodes[i].style.width =128 - c*0.2 +"px";
    }
  }
```

## 元素距离左上角的距离

下面的代码可以算出元素左上角相对于整张网页的坐标。

```js
function getElementPosition(e) {
  var x = 0;
  var y = 0;
  while (e !== null)  {
    x += e.offsetLeft;
    y += e.offsetTop;
    e = e.offsetParent;
  }
  return {x: x, y: y};
}
```

## 判断滚动条是否滚动到底

垂直滚动条：`scrollHeight - scrollTop = clientHeight`

水平滚动：`scrollWidth - scrollLeft = clientWidth`

## 容器内拖拽的最长距离

拖拽的最长距离：容器的 clientWidth - 子元素的 offsetWidth

在容器的内容区移动，子元素的边框不会被覆盖

# 其他样式相关的 API

## Element.matches()

`Element.matches` 方法返回一个布尔值，表示当前元素是否匹配给定的 CSS 选择器。

```js
  if (el.matches('.someClass')) {
    console.log('Match!');
  }
```

# 内联样式

操作样式最简单的方法，就是使用网页元素节点的 `getAttribute` 方法、`setAttribute` 方法和 `removeAttribute` 方法，直接读写或删除网页元素的 `style` 属性来操作元素的 **内联样式**

```js
div.setAttribute(
  'style',
  'background-color:red;' + 'border:1px solid black;'
);
```

上面的代码相当于下面的 HTML 代码。

```js
<div style="background-color:red; border:1px solid black;" />
```

`style` 不仅可以使用字符串读写，它本身还是一个对象，部署了 CSSStyleDeclaration 接口（详见下面的介绍），可以直接读写个别属性。

```js
e.style.fontSize = '18px';
e.style.color = 'black';
```

通过 style 修改的样式都是内联样式，由于内联样式的优先级比较高，所以我们通过 JS 来修改的样式，往往会立即生效，但是如果样式中设置了!important，则内联样式将不会生效。

## 重置样式 样式表 的冲突

```js
// 如果把宽度设置为0，那么内联样式的特殊性只能在样式表通过!important解决
// upNodes[i].style.width="0";
// 把值设为空，在内联样式不会有属性值，样式表的属性就可以正常作用了
upNodes[i].style.width="";
```

## 单位

.style.property 需要加 px，如果是 attribute 浏览器会自动加 px

# 总结

**读**：style 属性

**写单个**：style 属性

**写多个**

+ style.cssText
+ 通过 cssText 属性必须要克隆之前的 text，否则样式可能会丢失

**多个覆盖**

+ 如何实现多个覆盖呢？只能一个一个赋值
+ style.cssText +=
+ 但是要注意，如果同样的属性在 style 中使用了 important，后写的值无法生效

# CSSStyleDeclaration 接口

## 简介

CSSStyleDeclaration 接口用来操作元素的样式。三个地方部署了这个接口。

- 元素节点的 `style` 属性（`Element.style`）
- `CSSStyle` 实例的 `style` 属性
- `window.getComputedStyle()` 的返回值

CSSStyleDeclaration 接口可以直接读写 CSS 的样式属性，不过，连词号需要变成骆驼拼写法。

```js
var divStyle = document.querySelector('div').style;

divStyle.backgroundColor = 'red';
divStyle.border = '1px solid black';
divStyle.width = '100px';
divStyle.height = '100px';
divStyle.fontSize = '10em';

divStyle.backgroundColor // red
divStyle.border // 1px solid black
divStyle.height // 100px
divStyle.width // 100px
```

上面代码中，`style` 属性的值是一个 CSSStyleDeclaration 实例。这个对象所包含的属性与 CSS 规则一一对应。

## 注意

+ CSS 规则名字需要改写，比如 `background-color` 写成 `backgroundColor`。改写的规则是将横杠从 CSS 属性名中去除，然后将横杠后的第一个字母大写。如果 CSS 属性名是 JavaScript 保留字，则规则名之前需要加上字符串 `css`，比如 `float` 写成 `cssFloat`。
+ 该对象的属性值都是字符串，设置时必须包括单位，但是不含规则结尾的分号。比如，`divStyle.width` 不能写为 `100`，而要写为 `100px`。
+ 另外，`Element.style` 返回的只是行内样式，并不是该元素的全部样式。通过样式表设置的样式，或者从父元素继承的样式，无法通过这个属性得到。元素的全部样式要通过 `window.getComputedStyle()` 得到。
+ CSSStyleDeclaration 实例本身是一个对象，但是接受的是一个字符串

- 修改多个内联样式：

  ```js
    newRect.style=`
      position:absolute;
      left:${left}px;
      top:${top}px;
      width:${COLUMN_WIDTH}px;
      height:${randomH}px;
      background-color:pink;
    `
  ```

- 接受的不是一个对象，而是一个字符串，但是修改单个值的时候却表现得像对象一样 @@@
- 这是因为，style 不是一个单纯的 JS 对象，而是被解析成 CSS 样式声明，默认情况下所有的属性值都是空串，对 style 读写只会影响对应的属性值，而不会发生覆盖

  ![1557133380777](/img/user/programming/front-end/primitive/browser-api/dom-interface/1557133380777.png)

  ![1557133396510](/img/user/programming/front-end/primitive/browser-api/dom-interface/1557133396510.png)

- 注意 **不要** 直接给 style 属性设置字符串（如：`elt.style = "color: blue;"`）来设置 style，因为 style 应被当成是只读的（尽管 Firefox(Gecko), Chrome 和 Opera 允许修改它），这是因为通过 style 属性返回的 `CSSStyleDeclaration` 对象是只读的。
- 但是 `style` 属性本身的属性 **能** 够用来设置样式。此外，通过单独的样式属性（如 `elt.style.color = '...'`）比用 `elt.style.cssText = '...' 或者 elt.setAttribute('style', '...')` 形式更加简便，除非你希望完全通过一个单独语句来设置元素的全部样式。
- 考虑到语义，设置 HTMLElement.style.cssText 是更好的选择

## CSSStyleDeclaration 实例属性

### cssText

+ `CSSStyleDeclaration.cssText` 属性用来读写当前规则的所有样式声明文本。

  ```js
  var divStyle = document.querySelector('div').style;
  
  divStyle.cssText = 'background-color: red;'
    + 'border: 1px solid black;'
    + 'height: 100px;'
    + 'width: 100px;';
  ```

+ 注意，`cssText` 的属性值不用改写 CSS 属性名。
+ 删除一个元素的所有行内样式，最简便的方法就是设置 `cssText` 为空字符串。

  ```js
  divStyle.cssText = '';
  ```

+ 不要直接修改 style 属性，而是修改 `style.cssText`
+ **本质:** 修改 cssText 就是在修改元素的 style

### Length

+ `CSSStyleDeclaration.length` 属性返回一个整数值，表示当前规则包含多少条样式声明。

### parentRule

+ `CSSStyleDeclaration.parentRule` 属性返回当前规则所属的那个样式块（CSSRule 实例）。如果不存在所属的样式块，该属性返回 `null`。
+ 该属性只读，且只在使用 CSSRule 接口时有意义。

  ```js
  var declaration = document.styleSheets[0].rules[0].style;
  declaration.parentRule === document.styleSheets[0].rules[0]
  // true
  ```

## CSSStyleDeclaration 实例方法

### getPropertyPriority()

+ `CSSStyleDeclaration.getPropertyPriority` 方法接受 CSS 样式的属性名作为参数，返回一个字符串，表示有没有设置 `important` 优先级。如果有就返回 `important`，否则返回空字符串。

### getPropertyValue()

+ `CSSStyleDeclaration.getPropertyValue` 方法接受 CSS 样式属性名作为参数，返回一个字符串，表示该属性的属性值。
+ style 属性是可读的，直接使用 `.` 运算符读取更加方法

### item()

+ `CSSStyleDeclaration.item` 方法接受一个整数值作为参数，返回该位置的 CSS 属性名。

### removeProperty()

+ `CSSStyleDeclaration.removeProperty` 方法接受一个属性名作为参数，在 CSS 规则里面移除这个属性，返回这个属性原来的值。
+ 删除属性以后，属性会变为默认值，而不是设置为 0 或者空串

### setProperty()

+ `CSSStyleDeclaration.setProperty` 方法用来设置新的 CSS 属性。该方法没有返回值。
+ 该方法可以接受三个参数。

  - 第一个参数：属性名，该参数是必需的。
  - 第二个参数：属性值，该参数可选。如果省略，则参数值默认为空字符串。
  - 第三个参数：优先级，该参数可选。如果设置，唯一的合法值是 `important`，表示 CSS 规则里面的 `!important`。

  ```js
  // HTML 代码为
  // <div id="myDiv" style="color: red; background-color: white;">
  //   111
  // </div>
  var style = document.getElementById('myDiv').style;
  style.setProperty('border', '1px solid blue');
  ```

+ 上面代码执行后，`myDiv` 元素就会出现蓝色的边框。
+ **注意**：`important` 是属于 CSS 的内容，直接通过 style 属性读写的是内联样式，是 HTML 定义的，但是在值得后面跟上 `important` 也是 ok 的，同时还可以通过 `setProperty()` 方法，且他们的的优先级比 CSS 更高。
+ 内联样式 important > CSS 声明 important > 内联样式 > CSS 声明

# CSS CanIUse

CSS 的规格发展太快，新的模块层出不穷。不同浏览器的不同版本，对 CSS 模块的支持情况都不一样。有时候，需要知道当前浏览器是否支持某个模块，这就叫做“CSS 模块的侦测”。

一个比较普遍适用的方法是，判断元素的 `style` 对象的某个属性值是否为字符串。

```js
  typeof element.style.animationName === 'string';
  typeof element.style.transform === 'string';
```

如果该 CSS 属性确实存在，会返回一个字符串。即使该属性实际上并未设置，也会返回一个空字符串。如果该属性不存在，则会返回 `undefined`。

```js
  document.body.style['maxWidth'] // ""
  document.body.style['maximumWidth'] // undefined
```

上面代码说明，这个浏览器支持 `max-width` 属性，但是不支持 `maximum-width` 属性。

注意，不管 CSS 属性名的写法带不带连词线，`style` 属性上都能反映出该属性是否存在。

```js
  document.body.style['backgroundColor'] // ""
  document.body.style['background-color'] // ""
```

另外，使用的时候，需要把不同浏览器的 CSS 前缀也考虑进去。

```js
  var content = document.getElementById('content');
  typeof content.style['webkitAnimation'] === 'string'
```

这种侦测方法可以写成一个函数。

```js
  function isPropertySupported(property) {
    if (property in document.body.style) return true;
    var prefixes = ['Moz', 'Webkit', 'O', 'ms', 'Khtml'];
    var prefProperty = property.charAt(0).toUpperCase() + property.substr(1);
  
    for(var i = 0; i < prefixes.length; i++){
      if((prefixes[i] + prefProperty) in document.body.style) return true;
    }
  
    return false;
  }
  
  isPropertySupported('background-clip')
  // true
  ```

![dom-style](dom-style.md#CSS%20supports)

# CSS 对象

浏览器原生提供 CSS 对象，为 JavaScript 操作 CSS 提供一些工具方法。

这个对象目前有两个静态方法。

## CSS.escape()

`CSS.escape` 方法用于转义 CSS 选择器里面的特殊字符。

```html
  <div id="foo#bar">
```

上面代码中，该元素的 `id` 属性包含一个 `#` 号，该字符在 CSS 选择器里面有特殊含义。不能直接写成 `document.querySelector('#foo#bar')`，只能写成 `document.querySelector('#foo\\#bar')`。这里必须使用双斜杠的原因是，单引号字符串本身会转义一次斜杠。

`CSS.escape` 方法就用来转义那些特殊字符。

```js
  document.querySelector('#' + CSS.escape('foo#bar'))
```

## CSS.supports()

`CSS.supports` 方法返回一个布尔值，表示当前环境是否支持某一句 CSS 规则。

它的参数有两种写法，一种是第一个参数是属性名，第二个参数是属性值；另一种是整个参数就是一行完整的 CSS 语句。

```js
  // 第一种写法
  CSS.supports('transform-origin', '5px') // true
  
  // 第二种写法
  CSS.supports('display: table-cell') // true
```

注意，第二种写法的参数结尾不能带有分号，否则结果不准确。

```js
  CSS.supports('display: table-cell;') // false
```

# window.getComputedStyle(node)

行内样式（inline style）具有最高的优先级，改变行内样式，通常会立即反映出来。但是，网页元素最终的样式是综合各种规则计算出来的。因此，如果想得到元素实际的样式，只读取行内样式是不够的，需要得到浏览器最终计算出来的样式规则。

`window.getComputedStyle` 方法，就用来返回浏览器计算后得到的最终规则。它接受一个节点对象作为参数，返回一个 CSSStyleDeclaration 实例，包含了指定节点的最终样式信息。所谓“最终样式信息”，指的是各种 CSS 规则叠加后的结果。

注意，CSSStyleDeclaration 实例是一个 **活的对象**，任何对于样式的修改，会实时反映到这个实例上面。另外，这个实例是只读的。

`getComputedStyle` 方法还可以接受第二个参数，表示当前元素的伪元素（比如 `:before`、`:after`、`:first-line`、`:first-letter` 等）。

```js
  var result = window.getComputedStyle(div, ':before');
```

有几点需要注意:

  - CSSStyleDeclaration 实例返回的 CSS 值都是绝对单位。比如，长度都是像素单位（返回值包括 `px` 后缀），颜色是 `rgb(#, #, #)` 或 `rgba(#, #, #, #)` 格式。
  - CSS 规则的简写形式无效。比如，想读取 `margin` 属性的值，不能直接读，只能读 `marginLeft`、`marginTop` 等属性；再比如，`font` 属性也是不能直接读的，只能读 `font-size` 等单个属性。
  - 如果读取 CSS 原始的属性名，要用方括号运算符，比如 `styleObj['z-index']`；如果读取骆驼拼写法的 CSS 属性名，可以直接读取 `styleObj.zIndex`。
  - 该方法返回的 CSSStyleDeclaration 实例的 `cssText` 属性无效，返回 `undefined`。

## IE8

使用 ``currentStyle`` 属性

语法：元素.currentStyle.样式名

```js
box.currentStyle["width"] //[]字面量形式兼容性好
width:auto;
```

通过这个属性读取到的样式是只读的不能修改

只要判断有没有这个属性，即可实现兼容

window.getComputedStyle 有的话返回，然后转换为 true，没有的话 undefined，转换为 flase 。因为 IE 高版本两者都有，建议用 gcs

# StyleSheet 接口

## 概述

`StyleSheet` 接口代表网页的一张样式表，包括 `<link>` 元素加载的样式表和 `<style>` 元素内嵌的样式表。

`document` 对象的 `styleSheets` 属性，可以返回当前页面的所有 `StyleSheet` 实例（即所有样式表）。它是一个类似数组的对象。

如果是 `<style>` 元素嵌入的样式表，还有另一种获取 `StyleSheet` 实例的方法，就是这个节点元素的 `sheet` 属性。

  ```js
  // HTML 代码为 <style id="myStyle"></style>
  var myStyleSheet = document.getElementById('myStyle').sheet;
  myStyleSheet instanceof StyleSheet // true
  ```

严格地说，`StyleSheet` 接口不仅包括网页样式表，还包括 XML 文档的样式表。所以，它有一个子类 `CSSStyleSheet` 表示网页的 CSS 样式表。我们在网页里面拿到的样式表实例，实际上是 `CSSStyleSheet` 的实例。这个子接口继承了 `StyleSheet` 的所有属性和方法，并且定义了几个自己的属性，下面把这两个接口放在一起介绍。

## 实例属性

StyleSheet.disabled

`Stylesheet.href`: 返回样式表的网址。对于内嵌样式表，该属性返回 `null`。该属性只读。

StyleSheet.media

+ `StyleSheet.media` 属性返回一个类似数组的对象（`MediaList` 实例），成员是表示适用媒介的字符串。表示当前样式表是用于屏幕（screen），还是用于打印（print）或手持设备（handheld），或各种媒介都适用（all）。该属性只读，默认值是 `screen`。
+ `MediaList` 实例的 `appendMedium` 方法，用于增加媒介；`deleteMedium` 方法用于删除媒介。

```js
document.styleSheets[0].media.appendMedium('handheld');
document.styleSheets[0].media.deleteMedium('print');
```

StyleSheet.title

`StyleSheet.type`: 属性返回样式表的 `type` 属性，通常是 `text/css`。

StyleSheet.parentStyleSheet: CSS 的 `@import` 命令允许在样式表中加载其他样式表。`StyleSheet.parentStyleSheet` 属性返回包含了当前样式表的那张样式表。如果当前样式表是顶层样式表，则该属性返回 `null`。

StyleSheet.ownerNode: `StyleSheet.ownerNode` 属性返回 `StyleSheet` 对象所在的 DOM 节点，通常是 `<link>` 或 `<style>`。对于那些由其他样式表引用的样式表，该属性为 `null`。

`CSSStyleSheet.cssRules`: 属性指向一个类似数组的对象（`CSSRuleList` 实例），里面每一个成员就是当前样式表的一条 CSS 规则。使用该规则的 `cssText` 属性，可以得到 CSS 规则对应的字符串。

```js
var sheet = document.querySelector('#styleElement').sheet;

sheet.cssRules[0].cssText
// "body { background-color: red; margin: 20px; }"

sheet.cssRules[1].cssText
// "p { line-height: 1.4em; color: blue; }"
```

  每条 CSS 规则还有一个 `style` 属性，指向一个对象，用来读写具体的 CSS 命令。

```js
  cssStyleSheet.cssRules[0].style.color = 'red';
  cssStyleSheet.cssRules[1].style.color = 'purple';
```

CSSStyleSheet.ownerRule

## 实例方法 #

### CSSStyleSheet.insertRule()

`CSSStyleSheet.insertRule` 方法用于在当前样式表的插入一个新的 CSS 规则。

```js
var sheet = document.querySelector('#styleElement').sheet;
sheet.insertRule('#block { color: white }', 0);
sheet.insertRule('p { color: red }', 1);
```

该方法可以接受两个参数，第一个参数是表示 CSS 规则的字符串，这里只能有一条规则，否则会报错。第二个参数是该规则在样式表的插入位置（从 0 开始），该参数可选，默认为 0（即默认插在样式表的头部）。注意，如果插入位置大于现有规则的数目，会报错。

该方法的返回值是新插入规则的位置序号。

注意，浏览器对脚本在样式表里面插入规则有很多 [限制](https://drafts.csswg.org/cssom/#insert-a-css-rule)。所以，这个方法最好放在 `try...catch` 里使用。

### CSSStyleSheet.deleteRule()

`CSSStyleSheet.deleteRule` 方法用来在样式表里面移除一条规则，它的参数是该条规则在 `cssRules` 对象中的位置。该方法没有返回值。

```js
document.styleSheets[0].deleteRule(1);
```

# 实例：添加样式表

网页添加样式表有两种方式。一种是添加一张内置样式表，即在文档中添加一个 `<style>` 节点。

另一种是添加外部样式表，即在文档中添加一个 `<link>` 节点，然后将 `href` 属性指向外部样式表的 URL。

# CSSRule 接口

[#](https://wangdoc.com/javascript/dom/css.html#cssrule-接口)

# CSSRuleList 接口

[#](https://wangdoc.com/javascript/dom/css.html#cssrulelist-接口)

# window.matchMedia()

[#](https://wangdoc.com/javascript/dom/css.html#windowmatchmedia)

## 基本用法

`window.matchMedia` 方法用来将 CSS 的 [`MediaQuery`](https://developer.mozilla.org/en-US/docs/DOM/Using_media_queries_from_code) 条件语句，转换成一个 MediaQueryList 实例。

```js
var mdl = window.matchMedia('(min-width: 400px)');
mdl instanceof MediaQueryList // true
```

注意，如果参数不是有效的 `MediaQuery` 条件语句，`window.matchMedia` 不会报错，依然返回一个 MediaQueryList 实例。

```js
window.matchMedia('bad string') instanceof MediaQueryList // true
```

## MediaQueryList 接口的实例属性

### MediaQueryList.media

`MediaQueryList.media` 属性返回一个字符串，表示对应的 MediaQuery 条件语句。

### MediaQueryList.matches

`MediaQueryList.matches` 属性返回一个布尔值，表示当前页面是否符合指定的 MediaQuery 条件语句。

```js
if (window.matchMedia('(min-width: 400px)').matches) {
/* 当前视口不小于 400 像素 */
} else {
/* 当前视口小于 400 像素 */
}
```

 下面的例子根据 `mediaQuery` 是否匹配当前环境，加载相应的 CSS 样式表。

```js
var result = window.matchMedia("(max-width: 700px)");

if (result.matches){
var linkElm = document.createElement('link');
linkElm.setAttribute('rel', 'stylesheet');
linkElm.setAttribute('type', 'text/css');
linkElm.setAttribute('href', 'small.css');

document.head.appendChild(linkElm);
}
``` 

### MediaQueryList.onchange

如果 MediaQuery 条件语句的适配环境发生变化，会触发 `change` 事件。`MediaQueryList.onchange` 属性用来指定 `change` 事件的监听函数。该函数的参数是 `change` 事件对象（MediaQueryListEvent 实例），该对象与 MediaQueryList 实例类似，也有 `media` 和 `matches` 属性。

```js
var mql = window.matchMedia('(max-width: 600px)');

mql.onchange = function(e) {
if (e.matches) {
  /* 视口不超过 600 像素 */
} else {
  /* 视口超过 600 像素 */
}
}
```

上面代码中，`change` 事件发生后，存在两种可能。一种是显示宽度从 700 像素以上变为以下，另一种是从 700 像素以下变为以上，所以在监听函数内部要判断一下当前是哪一种情况。

## MediaQueryList 接口的实例方法

MediaQueryList 实例有两个方法 `MediaQueryList.addListener()` 和 `MediaQueryList.removeListener()`，用来为 `change` 事件添加或撤销监听函数。

```js
  var mql = window.matchMedia('(max-width: 600px)');
  
  // 指定监听函数
  mql.addListener(mqCallback);
  
  // 撤销监听函数
  mql.removeListener(mqCallback);
  
  function mqCallback(e) {
    if (e.matches) {
      /* 视口不超过 600 像素 */
    } else {
      /* 视口超过 600 像素 */
    }
  }
```

# FAQ

#faq/js

## 获取元素到视口相对位置

[获取元素的相对位置（到视口）](dom-style.md#获取元素的相对位置（到视口）)

## 判断滚动条是否滚动到底

[判断滚动条是否滚动到底](dom-style.md#判断滚动条是否滚动到底)
