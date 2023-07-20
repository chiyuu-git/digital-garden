---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/css/css-2-1/","tags":["gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry"]}
---


CSS Module 英文规范：<https://drafts.csswg.org/css-cascade/#value-stages>

CSS2.1 英文规范：<https://www.w3.org/TR/CSS2/cascade.html#q6.0>

CSS2.1 中文规范： http://www.ayqy.net/doc/css2-1/visuren.html#inline-boxes

https://github.com/w3c/csswg-drafts

latest： https://www.w3.org/Style/CSS/#specs

# 属性赋值，层叠（Cascading）和继承

## 指定值，计算值和实际值

一旦用户代理已经解析了文档并构造好了[文档树](http://www.ayqy.net/doc/css2-1/conform.html#doctree)，它就必须给树中的每个元素上适用于目标[媒体类型](http://www.ayqy.net/doc/css2-1/media.html)的每个属性赋值

属性的最终值是4步计算的结果：先通过指定来确定值（“指定值（specified value）”），接着处理得到一个用于继承的值（“计算值（computed value）”），然后如果有必要的话转化为一个绝对值（“应用值（used value）”），最后根据本地环境限制进行转换（“实际值（actual value）”）

### 指定值

用户代理必须先根据下列机制（按优先顺序）给每个属性赋值一个指定值：

1. 如果[层叠](http://www.ayqy.net/doc/css2-1/cascade.html#cascade)产生了一个值，就使用它 (样式表如果**指定**了)
2. 否则，如果属性是[继承的](http://www.ayqy.net/doc/css2-1/cascade.html#inheritance)并且该元素不是文档树的根元素，使用其父元素的计算值
3. 否则，使用属性的初始值，每个属性的初始值都在属性定义中指出了

### 计算值

指定值通过层叠被处理为计算值，例如，'em'和'ex'等相对单位被计算为像素或者绝对长度。计算一个值并不需要用户代理渲染文档

一个属性的计算值由属性定义中Computed Value行决定。当指定值为'inherit'时，计算值的定义见[继承](http://www.ayqy.net/doc/css2-1/cascade.html#inheritance)小节

即使属性不适用（于当前元素，定义在['Applies To'](http://www.ayqy.net/doc/css2-1/about.html#applies-to)行），其计算值也存在。然而，有些属性可能根据属性是否适用于该元素来定义元素属性的计算值

### 应用值

处理计算值时，尽可能不要格式化文档。然而，有些值只能在文档布局完成时确定。例如，如果一个元素的宽度是其包含块的特定百分比，在包含块的宽度确定之前无法确定这个宽度。

应用值是把计算值**剩余的**依赖（值）都处理成绝对值后的（计算）结果

### 实际值

原则上，应用值应该用于渲染，但用户代理可能无法在给定的环境中利用该值。例如，用户代理或许只能用整数像素宽度渲染边框，因此不得不对宽度的计算值做近似处理，实际值是经过近似处理后的应用值

### getComputedStyle()

有时候不同浏览器中使用 `window.getComputedStyle` 返回的值可能不一样，就是因为有的返回了计算值，有的返回应用值。

在 Firefox 中，把 `window.getComputedStyle` 返回的值叫做 resolved value，这个值有时是计算值有时是应用值，因属性而异。Chrome 也类似，但有时就会遇到跟 Firefox 返回值不一样的情况。

> 比如给一个元素 `line-height: normal`，然后用 `window.getComputedStyle` 获取它的行高，Chrome 返回计算值 `normal`，Firefox 返回的是具体的应用值比如 `16px`。
>
> （根据规范，`line-height: normal` 的计算值还是 `normal`，应用值在 `1.0-1.2` 之间。）

## 继承与初始

### 继承属性与非继承属性 @@@

**inherit关键字**

- 每个属性都可以指定`inherit`层叠值
- 如果属性值是**继承的**，那么继承的就是父级的**计算值**，来自父级的计算值同时作为子级**指定值和计算值**

  > 直接父级元素没有设置的话会一级级往上继承，如果都没有就继承根元素的初始值

- 如果属性值是**非继承**，**强行**指定`inherit`关键字就会继承**指定值**，来自父级的指定值作为子级的**指定值**
- 如果`inherit`值设置在根元素上，该属性会被赋值为其初始值
- 区别继承与非继承，可继承不可继承，所有的css属性都是**可继承的**，只是部分默认是非继承的

**继承属性示例**

- 给出如下样式表和文档片段：

  ```css
  body { font-size: 10pt }
  h1 { font-size: 130% }
  ```

  ```html
  <BODY>
    <H1>A <EM>large</EM> heading</H1>
  </BODY>
  ```

- font-size是继承属性，子级继承父级的计算值
- H1元素的'font-size'属性将获得**计算值**'13pt'（130%乘以父元素的值10pt）
- 因为['font-size'](http://www.ayqy.net/doc/css2-1/fonts.html#propdef-font-size)的计算值是继承的，EM元素也将获得计算值'13pt'

  > 如果用户代理没有可用的13pt字体，H1和EM的['font-size'](http://www.ayqy.net/doc/css2-1/fonts.html#propdef-font-size)的实际值可能是 例如，'12pt'

  > 注意，继承遵循文档树并且不会被[匿名框](http://www.ayqy.net/doc/css2-1/visuren.html#box-gen)截断

**非继承示例**

```html
  <body>
    <div id="root">
      <div id="box1"></div>
    </div>
  </body>
```

```css
    html,body{
      width:800px;
      height:800px;
    }
    #root{
      width:50%;
      height:50%;
      background-color: greenyellow;
    }
    #box1 {
      width:inherit;
      height:inherit;
      background-color: pink;
    }
```

- width,height是非继承属性，子级继承父级的指定值
- box1的width,height将获得**指定值**`50%`，**计算值**为200px（50%乘以父元素的宽度）

  > 作为对比，如果继承的是父级的计算值，那么box1的width,height应该是400px

### 继承属性 #

- azimuth, border-collapse, bor de r- sp ac in g, caption-side, color, cursor, direction, elevation, empty-cells, orphans, pitch-range, pitch, quotes, richness, speak-header, speaknumeral, speak-punctuation, speak, speechrate, stress, voice-family, volume, whitespace, widows,
- **文本相关属性**：
  - font-family, font-size, font-style,font-variant, font-weight, font,
  - text-align，text-indent，text-transform，letter-spacing，word-spacing
  - line-height
- **列表相关属性**：list-style-image, list-style-position,list-style-type, list-style
- **常用属性**：color，cursor，visibility
- 继承计算值

### 非继承属性

- margin、border、padding、background
- width、height、min-height、max-height、min-width、max-width
- display、overflow、z-index、float、clear
- position、left、right、top、bottom
- vertical-align
- table-layout
- page-break-after、page-bread-before和unicode-bidi
- 继承指定值

### 特例

`line-height`是继承属性，因此给line-height设置百分比，子元素继承的是**计算值**。但是当指定值是`<number>`时，子元素会继承**指定值**

`text-indent` 是继承属性，`text-indent` 可以接受一个百分比值，用来乘以包含块宽度。子元素从父元素继承时，得到的也是百分比值，即**指定值**。

### 初始值（Initial）

对于继承属性, 初始值 只能 被用于没有指定值的根元素上

> 因为不是根元素都会继承其他的父元素指定值

> 对于继承属性，初始值仅对没有指定值的根元素有意义，其他所有元素继承属性都是继承自根元素的

对于非继承属性 ,初始值可以被用于 任意 没有指定值的元素上

在CSS3中允许作者使用 initial 关键词明确的设定初始值

# CSS 视觉格式化模型（visual Formatting model）

视觉格式化模型： https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Visual_formatting_model

规范9.0 https://segmentfault.com/a/1190000003096320

规范10.0 https://segmentfault.com/a/1190000003820437#articleHeader24

块级格式化上下文： https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context

vertcal-align：https://www.cnblogs.com/starof/p/4512284.html

vertcal-align：https://www.cnblogs.com/hykun/p/3937852.html

vertcal-align：https://www.jianshu.com/p/dda695749019

line-height：https://blog.csdn.net/u013185654/article/details/78756347

line-height：https://www.cnblogs.com/dolphinX/p/3236686.html

## 基本概念

### 概述

视觉格式化模型：用户代理`User Agent`如何在视觉媒体` Visual Media `下处理文档树` Document Tree `。

视觉格式化模型会根据[CSS盒子模型](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Box_Model/Introduction_to_the_CSS_box_model)将文档中的元素转换为一个个盒子，每个盒子的布局由以下因素决定：

- 盒子的尺寸：精确指定、由约束条件指定或没有指定
- 盒子的类型：行内盒子（inline）、行内级盒子（inline-level）、原子行内级盒子（atomic inline-level）、块盒子（block）
- [定位方案（positioning scheme）](https://developer.mozilla.org/zh-CN/docs/CSS/Box_positioning_scheme)：普通流定位、浮动定位或绝对定位
- 文档树中的其它元素：即当前盒子的子元素或兄弟元素
- [视口](https://developer.mozilla.org/en-US/docs/Glossary/viewport)尺寸与位置
- 所包含的图片的尺寸
- 其他的某些外部因素

### 视口 The Viewport

在连续媒体` Continuous Media `上工作的用户代理一般会向用户提供一个视口（屏幕上的一个窗口或其它可视区域）来帮助用户访问文档。用户代理可以在调整视口大小的同时改变文档的布局（见初始包含块` Initial Containing Block `）。

如果视口小于渲染文档的画布区域，用户代理应提供一个滚动机制。每个画布最多有一个视口，但用户代理可以把文档渲染到多个画布上（即为相同文档提供不同视图）。

### 包含块 Containing Blocks

CSS2.1中，许多盒的定位和大小都根据一个名为包含块` Containing Block `的矩形盒的边缘来计算。

一般地：

- 生成的盒会充当其**后代盒的包含块**；
- 我们称盒为其后代“创建”了包含块。
- 说“盒的包含块”即是说“盒所处的包含块”，而不是盒所产生的包含块。

每个盒会被赋予一个相对于其包含块的位置，但它不会被局限在其包含块内；它有可能溢出。

包含块的尺寸如何计算的细节将在下章讲述。

“根元素”的包含块（初始包含块）由用户代理定义（浏览器）。在HTML中，根元素就是html元素，（部分浏览器根元素是body）。在大多数浏览器中，初始包含块是一个视窗大小的矩形（不等同于视窗，只是大小相等）

- 对于非根元素，如果position值是relative或者static，**包含块**则是最近的**块级框**。或者：表单元格或行内块祖先框的**内容边界**
- 对于非根元素，如果position值是absolute，包含块为最近的position值不是static的祖先元素（可以是任何类型）。具体过程如下：
  - 如果这个祖先元素是块元素，**包含块**则设置为该元素的内边距边界；换句话说，就是由**边框界定的区域**
  - 如果没有祖先元素，或者所有的祖先元素都没有开启定位，元素的包含块定义为初始包含块

  > 对于定位的元素：只是包含块的边界与父元素的边框相同，但是因为left、top的默认值是auto，所以不影响padding对子元素的作用。
  >
  > 当left和top为auto时，设置了padding之后子元素并不会黏在父元素的边框上
  >
  > 此时，设置left和top为0，子元素粘在父元素的边框上

### 可替换元素和不可替换元素

从元素本身的特点来讲，可以分为可替换元素(replaceable element)和不可替换元素(none-replaceable element)。

#### 可替换元素

在 CSS中，**可替换元素**（**replaced element**）的展现效果不是由 CSS 来控制的。这些元素是一种外部对象，它们外观的渲染，是独立于 CSS 的。

简单来说，它们的内容不受当前文档的样式的影响。CSS 可以影响可替换元素的位置，但不会影响到可替换元素自身的内容。某些可替换元素，例如`<iframe>` 元素，可能具有自己的样式表，但它们不会继承父文档的样式。

可替换元素就是浏览器根据元素的标签和属性，来决定元素的具体显示内容。

CSS 能对可替换元素产生的唯一影响在于，部分属性支持控制元素内容在其框中的位置或定位方式

推论：`replaced element`之所以冠以`replaced`的形容词，是因为这类element的内容会被指向的外部资源给replace掉，通过`src`属性

典型的可替换元素有：

- `<iframe>`
- `<img>`
- `<video>`

有些元素仅在特定情况下被作为可替换元素处理，例如：

- `<option>`
- `<audio>`
- `<canvas>`

HTML 规范也说了`<input>`元素可替换，因为 `"image"` 类型的`<input>`元素就像`<img>`一样被替换。但是其他形式的**控制元素**，包括其他类型的`<input>`元素，被明确地列为非可替换元素（non-replaced elements）。该规范用术语小挂件（Widgets）来描述它们**默认的限定平台的渲染行为**。

#### Css 与 可替换元素

用 CSS [content](https://developer.mozilla.org/zh-CN/docs/Web/CSS/content) 属性插入的对象是匿名的可替换元素。它们并不存在于 HTML 标记中，因此是“匿名的”。

需要注意的是，一部分（并非全部）可替换元素，其本身具有的尺寸和基线（baseline）会被一些 CSS 属性用到，加入计算之中，例如 [vertical-align](https://developer.mozilla.org/zh-CN/docs/Web/CSS/vertical-align)，会只有可替换元素才能具有这种自带值。

#### 不可替换元素

`html` 的大多数元素是不可替换元素，即其内容直接表现给用户端（例如浏览器）。

如果元素的内容包含在文档之中，则为非替换元素。除了几个可替换元素，其他所有的都是不可替换元素。

### 可替换元素的特性

具有自己的替换宽高，以canvas为例，默认的画布宽高通过html的width和height指定

https://whatwg-cn.github.io/html/#the-canvas-element

webview的animation-video组件：

1. 使用canvas元素支持afx透明视频，且context为webgl，此时canvas是替换元素
2. object-fit的默认值为fill，同时开发者无法直接控制canvas的属性，可以认为object-fit的值保持为fill

综上需要实现：

1. resource-width/height，用于指定canvas画布的宽高，默认值均为400
2. 当canvas的styleWidth和styleHeight均有值时，canvas画布需要拉伸以适应style指定的宽高（object-fit:fill）
3. 当styleWidth的值为`<length>`、`<percentage>` ，且styleHeigth为auto时，需要根据canvas画布的宽高比，计算出相应的styleHeight，维持画布的宽高比（styleWidth为auto时类似）
4. 当styleWidth和styleHeight均为auto时，canvas画布的宽高即为canvas元素的宽高

### 替换元素的特有样式 Object-fit

https://www.runoob.com/cssref/pr-object-fit.html

## 盒的生成 Controlling Box Generation

本节描述CSS2.1中可生成的盒类型。盒的类型会影响其在视觉格式化模型中的表现。

盒子的生成是 CSS 视觉格式化模型的一部分，用于**从文档元素生成盒子**。盒子有不同的类型，不同类型的盒子的格式化方法也有所不同。盒子的类型取决于 CSS [`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 属性。

### 块级

#### 基本概念

**块** block：一个抽象的概念，一个块在文档流上占据一个独立的区域，块与块之间在垂直方向上按照顺序依次堆叠。

**元素** elements：是html的概念(与标签一一对应)

**盒子** box：一个抽象的概念，由CSS引擎根据文档中的内容所创建，主要用于文档元素的定位、布局和格式化等用途。盒子与元素并不是一一对应的，有时多个元素会合并生成一个盒子，有时一个元素会生成多个盒子（如匿名盒子）。

> 这些盒子与盒模型有对应关系吗？content-box? margin-box?

**框**：盒的边界，有些翻译用于指代盒子，如行内框，实指行内盒 inline box

**块级元素** block-level element：

- 元素的 `display` 为 `block`、`list-item`、`table`时，该元素将成为块级元素。
- 元素是否是块级元素仅是元素本身的属性，并不直接用于格式化上下文的创建或布局。
- 块级元素是文档中会被视觉格式化为块状（例：段落）的元素，默认按照垂直方向依次排列。

**块级盒子** block-level box：

- 由块级元素生成。
- **参与**块格式化上下文` Block Formatting Context `
- 每个块级元素生成一个主要的块级盒` Principal Block-level Box `来包含其后代盒和生成的内容，同时参与定位体系` Positioning Scheme `。
- 某些块级元素还会在主要盒之外产生额外的盒：` list-item `元素。这些额外的盒会相对于主要盒来摆放，生成额外的盒子用于放置项目符号，而那些会生成列表项的元素可能会生成更多的盒子。不过，多数元素只生成一个主块级盒子。

**块盒子** block box：

- 如果一个块级盒子同时也是一个块容器盒子（见下），则称其为块盒子。
- 除具名块盒子之外，还有一类块盒子是匿名的，称为匿名块盒子（Anonymous block box），匿名盒子无法被CSS选择符选中，当然，**匿名块盒子也是块容器盒子和块级盒子**

  > 注意：盒子分为“块盒子”和“块级盒子”两种，但元素只有“块级元素”，而没有“块元素”。下面的“行内级元素”也是一样。

**块容器盒子**：

- block container box或block containing box
- 除了表格盒` Table Boxes `，和可替换元素（` Replaced Elements `），一个块级盒同时也是一个块容器盒`Block Container Box`
- 块容器盒子侧重于当前盒子作为“容器”的这一角色，它不参与当前块的布局和定位，它所描述的仅仅是当前盒子与其后代之间的关系。换句话说，块容器盒子主要用于**确定其子元素的定位、布局**等。
- 一个块容器盒子**只会**有两种情况：
	1. 只包含块级盒子
	2. 只包含行内级盒子，**创建**一个行内格式化上下文`Inline Formatting Context`

> 这看起来也许有些奇怪，这与上文提到的匿名块盒子有关, 它保证BFC内只有块级盒子, 下文将会有详细的讲解
>
> 为什么没有提到 只包含块级盒子就创建一个块级格式化上下文呢？因为开启BFC需要那几个条件，否则只需要自身参与到块级格式化上上下文即可

- **并非所有的块容器盒都是块级盒：**
	- 不可替换的行内块` Bon-replaced Inline Blocks `和不可替换的表格单元格` Non-replaced Table Cells `也是块容器但不是块级盒。
	- 能够注意到块级盒子与块容器盒子是不同的这一点很重要。前者描述了**元素与其父元素和兄弟元素之间的行为**，而后者描述了**元素跟其后代之间的行为**。
- 这三个术语，“块级盒”、“块容器盒”、“块盒”在意义明确时可简称为“块”。

  ![1553073314335.png](/img/user/programming/font-end/primitive/css/css-2.1/5cee87e4d7ac396572.png)

#### 匿名块盒

在某些情况下进行视觉格式化时，需要添加一些增补性的盒子，这些盒子不能用CSS选择符选中，因此称为匿名盒子（*anonymous boxes）*。

CSS选择器不能作用于匿名盒子(*anonymous boxes*)，所以它不能被样式表赋予样式。也就是说，此时所有可继承的 CSS 属性值都为 `inherit` ，而所有不可继承的 CSS 属性值都为 `initial`。

**情况一**

- 块容器盒子可能只包含行内级盒子，也可能只包含块级盒子，但通常的文档都会**同时包含两者**，在这种情况下，就会在相邻的**行内级盒子外**创建匿名块盒子。

```html
  <div>Some inline text <p>followed by a paragraph</p> followed by more inline text.</div>
```

- 此时会产生两个匿名块盒子：一个是 `<p>` 元素前面的那些文本（`Some inline text`），另一个是 `<p>` 元素后面的文本（`followed by more inline text.`）。此时会生成下面的块结构：

![1553066995197.png](/img/user/programming/font-end/primitive/css/css-2.1/5cee882c8416580129.png)

- 显示为：

```html
  Some inline text
  followed by a paragraph
  followed by more inline text.
```

**从情况一可知**：

- 当一个块容器盒子，既包含行内级元素（生成行内级盒子），又包含块级元素（生成块级盒子）时，会创建匿名块盒子包裹行内级盒子，使得块容器盒子下只有块级盒子

**情况二**

- 另一种会创建匿名块盒子的情况是一个**行内盒子**中包含一或多个块盒子。此时，包含 块盒子 的**行内盒子**会拆分为**两个行内盒子**，分别位于块盒子的前面和后面。块盒子前面的所有行内盒子会被一个匿名块盒子包裹，块盒子后面的行内盒子也是一样。因此，块盒子将成为这两个匿名块盒子的兄弟盒子。
- 如果有多个**块盒子**，而它们中间又没有行内元素，则会在这些盒子的前面和后面创建两个匿名块盒子。
- 请看下面的例子：

  ```html
  <!--假设 <p> 的 display 为 inline，<span> 的 display 为 block：-->
  <p>Some <em>inline</em> text <span>followed by a paragraph</span> followed by more inline text.</p>
  ```

- 此时会产生两个匿名块盒子：一个是 `<span>` 元素前面的文本（`Some *inline* text`），另一个是其之后的文本（`followed by more inline text.`）。此时会生成下面的块结构：

  ![1553068343611.png](/img/user/programming/font-end/primitive/css/css-2.1/5cee886f4562890560.png)

**从情况二可知：**

- 即使在html结构上使用行内级元素包裹块级元素，在渲染的时候也依然是块级盒子包裹行内级盒子，这是可以使用行内及元素包裹块级元素的真正原因
{ #vkanjg}


**注意：**

- 对匿名盒子来说，程序员无法像`<p>`元素那样控制它们的样式，因此它们会从包含它的非匿名盒那里继承那些可继承的属性，如 [`color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/color)。其他不可继承的属性则会设置为 `initial`

  > 比如，因为没有为它们指定 [`background-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-color)，因此其具有默认的透明背景，而 `<p>` 元素的盒子则能够用CSS指定背景颜色。类似地，两个匿名盒子的文本颜色总是一样的。

- 当一个元素导致了匿名块盒的生成，则该元素上设置的属性一样能应用于该元素生成的盒和该元素的内容。例如，在上面例子中，如果在` p `元素上设置了边框，则这个边框将画在` C1 `（在行的结尾开）和` C2 `（在行的结尾闭）周围。

```css
  p{
    display:inline;
    border:solid red; /*不可继承*/
    background:yellow; /*不可继承*/
    color:red; /*可继承*/
  }
  span{
    display:block;
  }
```

  ![1553081068612.png](/img/user/programming/font-end/primitive/css/css-2.1/5cee88b0278fd12635.png)

- 计算百分比值时，应忽略匿名块盒，而以最近的**非匿名祖先盒**来替代。例如，情况一的` div `里，如果一个匿名块盒的子盒在需要知道其包含块的高度来获得一个百分比高度。那么它将使用` div `形成的包含块的高度，而不是匿名块盒的高度。

### 行内级

#### 基本概念

**行内级元素** Inline-level Element：

- 是在源文档中那些不为其内容形成新的块、其内容分布在多行中的元素（如，段落内着重文本，行内图片等等)。
- 与块级元素一样，元素是否是行内级元素仅是元素**本身的属性**，并不直接用于格式化上下文的创建或布局。
- 以下的` display `属性值产生一个行内级元素：` inline `，` inline-table `，以及` inline-block `。

**行内级盒** Inline-level Boxes ：

- 行内级元素生成行内级盒` Inline-level Boxes `，行内级盒子包括行内盒子和原子行内级盒子两种，区别在于该盒子是否参与行内格式化上下文的创建。

**行内盒** Inline Boxes：

- 行内盒是一个行内级盒，且其内容参与了该行内盒的行内格式化上下文。
- 一个` display `值是` inline `的不可替换元素会生成一个行内盒。
- 与块盒类似，行内盒也分为具名行内盒和匿名行内盒（anonymous inline box）两种。

**原子行内级盒** Atomic Inline-level Boxes ：@

- 那些不是行内盒的行内级盒（例如可替换的行内级元素` Replaced Inline-level Elements `、行内块元素`inline-block`、行内表格元素` inline-table `）被称为原子行内级盒` Atomic Inline-level Boxes `，因为它们以单一不透明盒的形式来参与它们的行内格式化上下文。
- 原子行内级盒子一开始叫做原子行内盒子（atomic inline box），后被修正。
- 原子行内级盒子的内容不会拆分成多行显示。

![1553082284515.png](/img/user/programming/font-end/primitive/css/css-2.1/5cee88d9bd88327277.png)

#### 匿名行内盒

Anonymous Inline Boxes

任何被直接包含在一个块容器元素（不是包含在行内元素）的文本必须作为匿名行内元素来对待。

如下：

```html
<p>Some <em>emphasized</em> text</p>
```

`p`产生一个块盒，其中包含了一些行内盒。` emphasized `的盒是一个由行内元素` em `生成的行内盒，但其他盒（`some`和` text `的）是由块级元素` p `生成的行内盒。后面这种盒被称作匿名行内盒，因为它们没有相关的行内级元素。

这些匿名行内盒的可继承属性将从它们的父级块盒中继承。非继承性属性取其初始值。在上面例子中，匿名行内盒的`color`从` p `那里继承，但` background `为` transparent `。

空白内容，根据` white-space `属性，如果可被折叠则不会产生任何匿名行内盒。

本规范中，如果可根据上下文来清晰界定一个匿名盒的类型，则匿名行内盒和匿名块盒都可被简称为匿名盒。

在格式化表格时，还会有更多类型的匿名盒出现。

#### Run-in Boxed 插入盒

为使章节号同之前的草案一致，特保留此节。` display: run-in `现已定义至CSS3（参见CSS基本盒模型）。

### Display 属性

**block** 元素产生一个块盒。

**inline-block** 元素产生一个行内级块容器。行内块的内部会被当作块盒来格式化，而此元素本身会被当作**原子行内级盒**来格式化。

**inline** 元素产生一个或多个的**行内框**。@@@

- img 的 display 是 inline
- a 的 display 也是 inline

**none** 元素不出现在格式化结构中（也就是说，在视觉媒体中元素既不产生盒也不影响布局）。其后代元素也不产生任何盒：该元素及其内容会被从格式化结构中完全移除。对后代元素设定` display `属性不能覆盖这个表现。

> 请注意` none `值不产生可见盒；它根本就不生成盒。CSS中有使元素在格式化结构中产生盒并影响格式化，但盒本身不可见的机制。请访问visibility的章节了解详情。

除定位元素和浮动元素以及根元素外（见下文）计算值与指定值相同。根元素的计算值按下文所述改变。

注意，尽管` display `初始值是` inline `，但用户代理的默认样式表规则可能覆盖该值。请见附录中的HTML4参考样式表。

## 定位体系 Positioning Schemes

在CSS2.1中，盒子根据以下三种体系来布局：

- 常规流` Normal Flow `。CSS2.1中，常规流包括块级盒的块格式化，行内盒的行内格式化，以及块级盒和行内级盒的相对定位。
- 浮动` Floats `。在浮动模型中，盒首先根据常规流布局，然后从常规流中脱离并尽可能地向左或向右位移。内容可以布局在浮动周围。
- 绝对定位` Absolutr Positioning `。在绝对定位模型中，盒完全从常规流中脱离（对后面的同胞元素无影响）并根据包含块来分配位置。

浮动元素、绝对定位元素、根元素都被称为脱离文档流` Out of Flow `；其他元素被称为文档流内` In-flow `。

元素` A `的排版流由` A `在文档流内且最近的脱离文档流的祖先是A的元素构成。??

## 常规流 Normal Flow

常规流中的盒子都属于某个格式化上下文，要么块格式化上下文，要么行内格式化上下文，总之不能二者得兼。块级盒参与块格式化上下文，行内级盒参与行内格式化上下文。

### 块格式化上下文

浮动、绝对定位元素、非块盒的块容器（如：行内块` inline-block `、表格单元格` table-cell `以及表格标题` table-caption `）以及` overflow `属性不为` visible `的块盒（除了该值被传播到视口的情况）将为其内容**创建一个新的块级格式化上下文。**

- 浮动、绝对定位元素脱离常规流及其格式化上下文，需要新建一个块级格式化上下文
- 如果只是块级元素的嵌套，不会创建新的块级格式化上上下文，因为他们都仍然处于文档流的块格式化上下文中

在块格式化上下文中，盒从包含块顶部一个接一个地垂直摆放。两个同胞盒间的垂直距离取决于` margin `属性。**同一个块格式化上下文中的相邻块级盒的垂直外边距将折叠。**

在块格式化上下文中，每个盒的左外边缘紧贴包含块的左边缘（从右到左的格式里，则为盒右外边缘紧贴包含块右边缘），甚至有浮动也是如此（尽管盒里的行盒可能由于浮动而收缩），除非盒创建了一个新的块格式化上下文（在这种情况下盒子本身可能由于浮动而变窄）。

### 行内格式化上下文

在行内格式化上下文中，盒从包含块的顶部一个接一个地水平摆放。盒水平方向的外边距、边框和内边距在布局时都会考虑在内。

盒的垂直对齐方式则不一：可能按底部或者顶部对齐，又或者按它们内容文本的基线对齐。

#### 行盒

**包含了一行里所有盒的矩形区域被称为行盒` Line Box `。**

行盒的宽度取决于包含块以及浮动。行盒的高度取决于在**行盒高度计算章节**所给出的规则。

行盒的高总是足以容纳其包含的所有盒

- 然而，它可能高于其所包含的最高盒（比如，包含的盒以基线对齐）。当一个盒（`B`）的高度小于包含它的行盒的高度时，` B `的垂直对齐方式由` vertical-align `属性决定。
- 当在水平方向上几个行内级盒不能完全被单个行盒包含时，它们会被分配到两个或者多个垂直摆放的行盒中。因此，一个段落就是多个行盒的垂直堆叠。行盒的堆叠没有垂直间距（除非有特别声明）并且从不重叠。

一般来说，在同一行内格式化上下文中的行盒是等宽的（包含块的宽度）行盒的左边缘紧贴其包含块的左边缘，其右边缘紧贴包含块的右边缘。然而，如果行盒内的元素开启了浮动，浮动盒可能被置于包含块和行盒边缘之间。由于浮动会造成可用的水平空间减少，行盒的宽度仍可能变动。同一行内格式化上下文中的行盒在高度上通常是变动的（比如，一行可能包含图片但其他行仅包含文本）。

当一行中的行内盒的总宽度小于包含它们的包含块的时候，它们在行里的水平分布取决于` text-align `属性。如果取`justify`值，用户代理可能拉伸行内盒（` inline-table `和` inline-block `盒除外）中的空格和字间距。

当行内盒的宽度超过行盒宽度时，行内盒将被分为多个盒，被分解出的盒则又分布在多个行盒中。如果一个行内盒不可切割（比如，行内盒包含的是单个字符或者语言指定的断字规则不允许断字，又或者行内盒的` white-space `属性值为`nowrap`或` pre `），那么该行内盒将溢出行盒。

当行内盒被分割，外边距、边框和内边距在任何断点处都不会产生视觉影响。

行内盒也可能由于双向文本处理而在一个行盒内被切割成多个盒。

为了包含行内格式化上下文中的行内级内容，行盒按需创建。有的行盒不包含文本、保留空白、外边距或内边距或边框不为零的行内元素、其他文档流内 `In-flow` 内容（如图片、行内块或行内表格），并且不以保留的换行符结尾，如果是为决定它们所包含的元素的定位，则必须视其为零高度的行盒，除此之外的其他目的下应视其为不存在。

**示例**

- 下面是一个行内盒构造的例子。下属的段落（由HTML块级元素` p `创建）包含了有` em `和` strong `交叉的匿名文本。

  ```html
  <p>Several <em>emphasized words</em> appear
  <strong>in this</strong> sentence, dear.</p>
  ```

- `p`元素生成了一个块盒来包含五个行内盒，其中三个行内盒是匿名的：
  - 匿名："Several"
  - em："emphasized words"
  - 匿名："appear"
  - strong："in this"
  - 匿名："sentence, dear."
- 为了格式化该段落，客户端将五个行内盒放进**行盒**。在这个例子中，由` p `元素生成的盒创建了行盒的包含块。如果该包含块足够宽，所有的行内盒将放置在单个行盒：

  ![1553083952313](/img/user/programming/font-end/primitive/css/css-2.1/1553083952313.png)

- 如果宽度不够，行内盒就会被分割并分布在多个行盒。段落可能就变成了：

  ![1553083974850](/img/user/programming/font-end/primitive/css/css-2.1/1553083974850.png)

- 在最后这个情况里，` em `盒被分割成了两个` em `盒（现称之为` split1 `和` split2 `）。外边距、边框、内边距或者文本修饰在` split1 `之前或者` split2 `之后都没有视觉效果。
- 看下面这个例子：

```html
  <style type="text/css">
    em {
      padding: 2px; 
      margin: 1em;
      border-width: medium;
      border-style: dashed;
      line-height: 2.4em;
    }
  </style>
  
  <p>Several <em>emphasized words</em> appear here.</p>
  ```

- 根据` p `的宽度，这些盒可能分布如下：

  ![1553084063643](/img/user/programming/font-end/primitive/css/css-2.1/1553084063643.png)

- 外边距插在了` emphasized `之前和` words `之后
- 内边距被插在了` emphasized `之前、上、下，` words `值后、上、下。虚线边框渲染在了每个单词的三边。

### 相对定位 Relative Positioning

一旦一个盒遵循常规流或者浮动而布局好位置后，它有可能根据这个位置来相对位移。这被称作相对定位。通过这种方式移动盒（` B1 `）对随后的盒（` B2 `）没有影响：` B2 `被赋予了一个如同` B1 `没有位移的位置，并且` B2 `在` B1 `移动后不会重定位。这意味着相对定位可能造成盒重叠。然而，如果相对定位造成一个` overflow:auto `或` overflow:scroll `的盒溢出，客户端必须通过创建滚动条来让用户可以访问到该内容（在其偏移位置），这可能影响布局。

一个相对定位盒保持其常规流中的大小，包括断行和原本为其保留的空间。包含块一节解释了相对定位盒创建新的包含块的情况。

对于相对定位元素而言，` left `和` right `在不改变盒大小的同时使其水平位移。` left `使盒向右移动，` right `时期向左。` left `或` right `没有造成盒的分割或拉伸，因此应用的值始终满足：` left `等于 `-right `。

- 如果` left `和` right `值均为` auto `（其默认值），应用的值为` 0 `（即是说，盒保持在其原位）。
- 如果` left `是` auto `，其应用值为` right `的负值（即盒向左移动` right `值）。
- 如果` right `是` auto `，其应用值为` left `的负值。
- 如果` left `和` right `均不为` auto `，定位则被**过度约束**，其中一值必须被忽略。如果包含块的` direction `属性值为`ltr`，则` left `值胜出而` right `值改为 -` left `。如果包含块的` direction `属性值为` rtl `，` right `值胜出而`left`值被忽略。

请看下面的例子，这三条规则是等效的

```css
div.a8 {
   position: relative;
   direction: ltr;
   left: -1em;
   right: auto
}
div.a8 {
   position: relative;
   direction: ltr;
   left: auto;
   right: 1em
}
div.a8 {
   position: relative;
   direction: ltr;
   left: -1em;
   right: 5em
}
```

`top`和` bottom `属性在不改变相对定位元素的大小的同时使其上下位移。` top `使其下移，` bottom `则使其上移。`top`或` bottom `没有造成盒的分割或拉伸，因此应用的值始终满足：` top `等于 `-bottom `。如果二者均为` auto `，其值则均为`0`。如果其中一个值为` auto `，则该属性取另一属性的负值。如果二者均不为` auto `，` bottom `将被忽略（也就是说，`bottom`应用值为` top `的负值）。

注：在脚本环境中动态移动相对定位盒可以产生动画效果（见` visibility `属性）。尽管相对定位可被用于上标和下标效果，但行高在自动调整时不会将其定位纳入计算。参见行高计算一节的描述了解更多信息。 @@@

相对定位的例子将在对比常规流、浮动和相对定位一节中提供。

## 浮动

## 绝对定位

## Display Position Float之间的关系

# 尺寸体系

https://www.zhangxinxu.com/wordpress/2016/05/css3-width-max-contnet-min-content-fit-content/

https://blog.csdn.net/tian361zyc/article/details/76444567

http://www.divcss5.com/rumen/r422.shtml

# Width与margin

为什么没有border和padding呢？

计算值与应用值，padding的初始值是0，应用值与计算值永远都是一致的，border也类似。

但是width初始值是auto

margin初始值是0，可以选择auto

元素的['width'](http://www.ayqy.net/doc/css2-1/visudet.html#propdef-width)，['margin-left'](http://www.ayqy.net/doc/css2-1/box.html#propdef-margin-left)，['margin-right'](http://www.ayqy.net/doc/css2-1/box.html#propdef-margin-right)，['left'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-left)和['right'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-right)属性**用于布局的值**，取决于生成盒的类型以及相互影响（用于布局的值有时被称为[应用值](http://www.ayqy.net/doc/css2-1/cascade.html#usedValue)）。原则上，应用值与计算值一样，都是把'auto'替换成一些合适的值，百分比相对其包含块来计算，但也存在例外。以下情况需要加以区分：

# Height与margin

# 行高详述

https://segmentfault.com/a/1190000003820437#articleHeader24

## 行高概念

行高类似于我们上学单线本，单线本是一行一行，线与线之间的距离就是行高，

![](/img/user/programming/font-end/primitive/css/css-2.1/31243722432787177.jpg)

**基线**：字母X下边缘的位置，微软雅黑完美压线，各个字体略有不同

**中线**：基线+1/2字母x的高度的位置

> 可见，中线的位置与font-size有关

**行高**：上下文本行的基线间的距离，由line-height属性值确定

> 为什么需要两行？其实一行也是有行高的，由两行这个概念上的定义可以确定一行的行高

**内容区**：底线和顶线包裹的区域，是一种围绕文字的盒子，大小与`font-size`和`font-family`相关

> 在页面中选中文字的时候的蓝色背景就是content-area，实际上就是em-box
>
> 行内元素display：inline可以通过background-color属性显示出来

**行距**：一行底线到下一行顶线的垂直距离，行距默认情况下会均分到内容区的两侧

> 在simsun字体下，内容区高度等于文字大小值：行间距=行高 - font-size，为了降低难度，以下的demo都是simsun字体，新宋
>
> 其他字体，行间距 = 行高 - 内容区大小

**行内盒(inline box)**：即是行内框，行中的每个元素都会生成一个内容区，内容区的大小由**字体和字号**确定，将行距/2分别应用到内容区的顶部和底部，其结果就是该元素的行内盒

> 将line-height的计算值减去font-size的**计算值**，这个值就是行距，这个值可能是个负值，

**行盒(line box)**：即是行框，行盒是指一个虚拟的矩形盒，**默认情况**下行盒高度等于本行内所有元素的行内盒最大的值

> 一行上垂直对齐时以行高值最大的行内盒为基准，其他行内盒采用自己的对齐方式向基准对齐，最终计算行盒的高度 ，还会收到vertical-align属性的影响
>
> 每一行只能有一个行盒，行内嵌套宽度由内容撑开，仍然只一个行内盒而已

**注意：**

- 浏览器渲染的时候，会比实际的font-size要大，30px的字体大小，内容区本应是30px高度，但是被渲染成了40px![](/img/user/programming/font-end/primitive/css/css-2.1/1548652489942.png)

  > 字体在基线之上和之下的高度和深度被假定为包含在字体内的特性。（更多细节，参见CSS3。）
  >
  > 因为默认值normal比较小，由于字体的原因，同一个元素内容区的高度很可能比行内盒的高度高，
  >
  > 对于高清屏，测量值会受到WIN10 默认放大的影响，可以自行换算，实际上是准确的

- 在实际开发中，处理大量文本的时候，建议把行高设置成比字体大小大，**默认行高是font-size 的1.5倍左右**，各浏览器差异大

## Line-height

适用元素：all

是否继承：可继承

### 属性值

**normal**：让用户代理设使用值为一个基于元素字体的“合理”值。该值与\< number >意义相同。我们推荐 `normal` 的使用值在1.0到1.2之间。计算值为 `normal`。

> normal具体的表现是数值，具体的数值由用户代理决定，在1-1.2之间
>
> chrome：微软雅黑，1.32，/ sinsum，1.14
>
> firefox: 微软雅黑，1.32 / sinsum，1.144
>
> 计算方法：font-size:100px; container.clientHeight/100

**\<length>**：指定长度用于行盒高度计算。负值非法。

**\<number>**：本属性的应用值为此数值乘以本元素的字号。负值非法。

**\<percentage>**：本属性的计算值为此百分比乘以**自身**的字号计算值。负值非法。

**inherit**：对于某些可替换元素，可能line-height的默认值不是normal，需要使用inherit重置

**示例**

- 下面例子中的三条规则的行高结果相同：

  ```css
  div { line-height: 1.2; font-size: 10pt }    /* number */
  div { line-height: 1.2em; font-size: 10pt }    /* length */
  div { line-height: 120%; font-size: 10pt }    /* percentage */
  ```

- 继承结果不同，**< number >**的话指定值，然后子元素根据自身的font-size计算line-height @@@
- \< length>和\< percentage >继承父元素line-height的计算值（根据父元素的font-size计算）
- 考虑下面这个例子：

<iframe height="300" style="width: 100%;" scrolling="no" title="CSS2.1-line-height-继承" src="https://codepen.io/chiyu-git/embed/wvwRBWR?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true"></iframe>

### 可替换行内元素

在此文中，请简单理解为图片

`line-height`属性值没有意义，对图片没有任何影响

### 不可替换行内元素

在此文中，请简单理解为文本

`line-height` 指定用于计算行盒高度的高度

即使行高设定的比内容区高度还要小（内容区大小取决于字体大小），行内盒的高度仍然是由line-height决定

> **内联元素的实际高度是由line-height决定的**，这和我们学习的块元素的盒子模型完全不同
>
> 对于行内盒而言可以做到撑开父级的只有一个属性：line-height，其他的盒模型部分都是不相关的，包括垂直方向上的margin、border、padding、content，全都没有办法撑开父元素的高度
>
> 也就是说：内联元素在垂直方向上的位置只会受到：font-size，line-height，vertical-align的影响

行内盒的位置会受到字体的影响因为，行距 = line-height - font-size

**文字会默认在行高中垂直居中显示**，这是因为行距默认等分在内容区的两边

注意：内容区的大小会影响横向的排列，比如

![](/img/user/programming/font-end/primitive/css/css-2.1/1557719631113.png)

> text line-height为0，无法改变行盒的高度，但是font-size变化会影响横向的排列
>
> height是由内容撑开的，font-size变化，平分线也跟着变化
>
> 使用simsun字体，并且line-height为1，可以把内容区看作行内盒的边框，也就是上图中粉色的背景区域

### 块级容器元素

一般情况下，`line-height` 指定该容器内**行盒**的**最小高度**

> 与heigt属性值做区分，height值一旦指定，容器的高度就不会在发生变化，因此子元素可能**溢出**

**验证：**`line-height` 指定该容器内**行盒**的**最小高度**，而不是该容器的最小高度，区别于`min-height`

```html
<div id='container'>
</div>
```

```css
  #container{
    position: relative;
    line-height: 100px;
    font-size:36px;
    font-family: 'simsun';
    background-color: rgb(200, 200, 200);
  }
```

此时容器是没有高度，但是如果指定的是min-height，则容器会保持这个高度

那么指定行盒的最小高度要怎么理解呢？为了解决这个疑惑，我们需要引入一个重要的概念：**支柱**

### 支柱 Strut

对于块容器元素而言，line-height属性值的另一个重要的意义在于**被支柱继承**

line-height是继承属性，因此line-height任何情况下都有默认值。line-height的默认值是normal，那么line-height的计算值就是该元素的**字号**\*normal

font-size也是可继承的，因此font-size任何情况下都有默认值

> 该值同样是由用户代理决定的，以chrome为例，默认的font-size为16px

因此：对内容由行内级元素组成的块容器元素而言

- 就如同每个行盒以一个具有该块容器元素**字体**和**字号**以及`line-height`属性的**零宽度**行内盒开始一样。我们称此虚构盒为“支柱 `Strut` ”。（该命名灵感源于Tex。）
- 根据上述说明，默认情况下支柱的高度是：16px*normal

```html
<div id='container'>
  <span>xxx</span>
</div>
```

```css
#container{
  /* 块容器的行高：16*normal */
  /* 同时也是支柱的行高 */
  background-color: rgb(200, 200, 200);
}
span{
  /*span的行高小于支柱：12*normal */
  font-size:12px;
  background-color: white;
}
```

![](/img/user/programming/font-end/primitive/css/css-2.1/1557626200364.png)

可以看到，容器的高度比span的高度还要高，这是因为容器此时被**支柱**给撑开了

因此，对于**非空**的块容器元素而言，可以认为`line-height`指定了容器的最小高度

如果容器是空的，即使指定了line-height容器也是没有高度的，说明容器的高度不是line-height指定的，而是被支柱继承了之后撑开的

更准确的理解：容器的line-height属性，设置了支柱的高度，当支柱存在时，可以确定行盒以及容器的最小高度

### 支柱出现的条件

如果容器标签体为空则不会出现支柱

如果有文字或者图片

- 不论文字的行内盒font-size为何值，都存在支柱
- 图片高度无论是什么值，存在支柱

**支柱的本质：不是html排版的空白符，而是浏览器默认添加的隐匿文本节点，空白符**

### 行盒的高度

块容器可以设置高度height，line-height指定其实是支柱的高度

对于块容器而言，容器的高度是由行盒撑开的，能够撑开行盒：

- 支柱，继承了容器的line-height值和font-size值
- 不可替换行内元素，是其`line-height`值，而不是内容区

  > 还要考虑vertical-align属性

- 可替换行内元素，是其**外边距盒**的高度

  > 还要考虑vertical-align属性

- 对于元素本身而言，设定line-height的值少于font-size 的值，元素高度为fontsize的值？

  > 这句话正确吗？错误，如果是块容器，没有支柱则没有高度。如果是行内元素，则是line-height值，而不会是收到font-size的影响

**验证：**撑开父元素的是不可替换行内元素的`line-height`，而不是内容区

```css
#container{
  line-height:0px;
  font-size:0px;
  font-family: 'simsun';
  background-color: rgb(200, 200, 200);
}
span{
  line-height:36px;
  background-color: white;
}
```

![](/img/user/programming/font-end/primitive/css/css-2.1/1557627831859.png)

可以看到父元素还是被撑开了，此时支柱的行高和字号都是0，也就是说父容器的高度被span的line-height属性决定的，这一点适用于所有不可替换行内元素，也包括支柱

### 确定基线和中线

为了方便理解，以下所有demo中我们根据以下的标准进行探讨：

- 以一个**显式的支柱**为基准
- 以最简单的结构，不要考虑行内元素的嵌套，不要考虑字体影响，不要考虑表格元素

  > 一个行盒可以由块级盒产生，也可以由行内级盒产生，它描述了一行行内元素的渲染方式
  >
  > 统一采用simsun字体
  >
  > 表格元素表现差异很大，另外讨论

```html
<div id='container'>
  <span id='strut'>llx</span>
  <span id='text'>textYyx</span>
  <img src="./img/img2-middle.jpg" alt="123"/>
</div>
```

```css
  #container{
    position: relative;
    line-height: 100px;
    font-size:36px;
    font-family: 'simsun';
    background-color: rgb(200, 200, 200);
  }
  #text{
    line-height: normal;
    font-size:36px;
    position: relative;
    background-color: pink;
    vertical-align: baseline;
  }
  #strut{
    position: relative;
    font-size:inherit;
    line-height: inherit;
    background-color: pink;
    border:1px greenyellow solid;
  }
  img{
    position: relative;
    line-height:normal;
    font-size:36px;
    width: 15px;
    height: 15px;
    vertical-align: baseline;
  }
```

![](/img/user/programming/font-end/primitive/css/css-2.1/20190531220523.png)

> 为了更加直观的理解，css的属性都是显式写出，支柱相关属性都是继承于块容器
>
> 开启相对定位，用伪元素标识出元素的平分线，容器的平分线为红色，文本的平分线为绿色，图片的平分线为黑色

**首先：**我们可以认为**父元素的基线就是支柱的基线**，因为支柱的所有性质都继承于父元素，假设在支柱内有一个字母x，那么这个字母x的下边缘就是基线

> 基线永远在小写字母x的下边缘

确定基线的偏移，不论**支柱**是否是行内盒的最高盒，基线总是向上偏移半行距。基线的偏移**直接**受到父元素`line-height`属性值的影响：

- 当**支柱**是行内盒中最高的时候，基线向上偏移半行距

![](/img/user/programming/font-end/primitive/css/css-2.1/20190531220223.png)

- 当支柱的高度小于行内最高盒的时候，支柱撑起父容器的**角色**被最高盒代替，最高盒成为**新的支柱**，**但是**基线的位置仍然是在**初始支柱**的小写字母x的下边缘

![](/img/user/programming/font-end/primitive/css/css-2.1/20190531220041.png)

基线的偏移**间接**受到父元素`font-size`属性值的影响，**本质上是影响了行距**，因为 行距 = line-height - font-size

基线的位置再加上1/2 x的高度就是中线的位置

中线也是**相对绝对性**，永远在基线上方1/2字母x的高度处，因此也会受到`line-height`和`font-size`的影响

## Line-height规范解读

https://drafts.csswg.org/css2/#propdef-line-height

https://drafts.csswg.org/css-inline/#line-height-property

| Name:                                                        | line-height                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Value:](https://www.w3.org/TR/css-values/#value-defs)       | normal  \| `<number>`\| `<length>` \| `<percentage>`\| inherit     |
| [Initial:](https://www.w3.org/TR/css-cascade/#initial-values) | normal                                                       |
| [Applies to:](https://www.w3.org/TR/css-cascade/#applies-to) | *                                                            |
| [Inherited:](https://www.w3.org/TR/css-cascade/#inherited-property) | yes                                                          |
| [Percentages:](https://www.w3.org/TR/css-values/#percentages) | refer to the font size of the element itself                 |
| [Computed value:](https://www.w3.org/TR/css-cascade/#computed) | for  `<length>`  and `<percentage>`  the absolute value; otherwise as specified |
| [Canonical order:](https://www.w3.org/TR/cssom/#serializing-css-values) | per grammar                                                  |
| Media:                                                       | visual                                                       |

On a [block container element](https://drafts.csswg.org/css2/#block-boxes) whose content is composed of [inline-level](https://drafts.csswg.org/css2/#inline-level) elements, [line-height](https://drafts.csswg.org/css2/#propdef-line-height) specifies the *minimal* height of line boxes within the element. The minimum height consists of a minimum height above the baseline and a minimum depth below it, exactly as if each line box starts with a zero-width inline box with the element’s font and line height properties. We call that imaginary box a "strut." (The name is inspired by TeX.).

The height and depth of the font above and below the baseline are assumed to be metrics that are contained in the font. (For more details, see CSS level 3.)

On a non-replaced [inline](https://drafts.csswg.org/css2/#inline-boxes) element, [line-height](https://drafts.csswg.org/css2/#propdef-line-height) specifies the height that is used in the calculation of the line box height.

Values for this property have the following meanings:

### 属性值

- **normal**: Tells user agents to set the used value to a "reasonable" value based on the font of the element. The value has the same meaning as `<number>`. We recommend a used value for [normal](https://drafts.csswg.org/css2/#valdef-line-height-normal) between 1.0 to 1.2. The [computed value](https://drafts.csswg.org/css2/#computed-value) is normal.
- **\<length>**: The specified length is used in the calculation of the line box height. Negative values are illegal.
- **\<number>**: The used value of the property is this number multiplied by the element’s font size. Negative values are illegal. The [computed value](https://drafts.csswg.org/css2/#computed-value) is the same as the specified value.
- **\<percentage>**: The [computed value](https://drafts.csswg.org/css2/#computed-value) of the property is this percentage multiplied by the element’s computed font size. Negative values are illegal.

**Note.** When there is only one value of [line-height](https://drafts.csswg.org/css2/#propdef-line-height) (other than [normal](https://drafts.csswg.org/css2/#valdef-line-height-normal)) for all inline boxes in a block container box and they all use the same first available font (and there are no replaced elements, inline-block elements, etc.), the above will ensure that baselines of successive lines are exactly line-height apart. This is important when columns of text in different fonts have to be aligned, for example in a table.

## Vertical-align

此属性影响**行内级元素**生成盒在行盒内的垂直定位。

### **属性**

- **baseline**：把盒的基线同父盒的**基线**对齐

  > 父盒x的下边缘

- **middle**：把盒的**垂直中点**同父盒的**中线**对齐

  > 中线：基线加上父盒一半的 x-height

- **text-top**：使元素的顶部与父元素的**顶线**对齐。
- **text-bottom**：使元素的底部与父元素的**底线**对齐。
- **top**：使元素及其后代元素的顶部与整行的顶部对齐

  > 注意：不是父盒的顶线

- **bottom**：使元素及其后代元素的底部与整行的底部对齐

  > 注意：不是父盒的底线

- 上述值仅相对于父行内元素或父块容器元素的支柱 `Strut` 有意义。
- 在上面的定义中，对行内不可替换元素而言，用于对齐的行内级盒是高度为 `line-height` 的盒（**不**包括行内盒的内容区）

  > 只有基线对齐是依赖于行内盒的基线，其他全都是根据line-height盒
  >
  > 假设行内盒的line-height为0，那么对于该行内盒而言用于对齐的就是**平分线**的位置，与字体大小完全无关，但此时baseline还是根据字体的基线

- 对于其他所有元素，用于对齐的行内级盒是**外边距盒**

### 起作用的前提

行内级元素

### 结语

现在我们准确的捕捉到了基线的所在，这样理解vertical-align属性就很简单了，只要找准支柱，主要属性都试一遍，也就理解了90%了。剩下的主要是一些边际情况和实际运用，我会在下一篇文章讲解

## 行盒高度的计算

行盒高度的计算，最后这个高度会撑开父容器

**行盒的高度决定如下：**

1. 计算行盒内每个**行内级盒**的高度

   - 对于可替换元素、**行内块**元素以及行内表格元素，高度是其**外边距盒**的高度；
   - 对于**行内盒**：就是其**line-height属性值**

     > 设置line-height，定死了行内盒的高度，不论文字溢出与否容器的实际高度不变
     >
     > 区别于块容器，设置的最小高度

   - 对于**支柱**：根据父元素的line-height和font-size确定支柱的line-height，根据支柱的line-height和font-size确定基线，根据基线确定中线、顶线、底线

2. 行内级盒根据其 `vertical-align` 属性垂直对齐。

   > 如果它们对齐 `top` 或 `bottom`，它们必须以能最小化行盒高度的方式对齐。如果这些盒足够高，则有多种解决方案并且CSS2.1没有规定此行盒的基线的位置（即，支柱 `Strut` 位置）

   ```html
     <div id="root">
       <h1>line-height&vertical-aling</h1>
       <div id='container'>
         <span>xxx</span>
         <img src="./img/img2.jpg" alt="123">
       </div>
     </div>
   ```

   ```css
     #container{
       line-height:16px;
       font-size:36px;
       font-family: 'simsun';
       background-color: rgb(200, 200, 200);
     }
     span{
       line-height:0px;
       background-color: pink;
     }
     img{
       width: 10px;
       height: 10px;
       vertical-align: baseline;
     }
   ```

   - 假如没有图片，无论父元素的font-size 怎么变化，行高都不收影响，如果有图片，会被图片的对齐方式影响行高

     ![1557637961740](/img/user/programming/font-end/primitive/css/css-2.1/1557637961740.png)

3. **行盒**高度是最上盒顶部到最下盒底部的距离。（包括支柱）

   ![1557722129003](/img/user/programming/font-end/primitive/css/css-2.1/1557722129003.png)

   text的行高为0，但是行内盒依然存在，line-height-font-size = 行距，所以总是收缩在平分线处，当平分线上升到了容器的最顶部，此时高度为0的行内盒就是最上盒，所以即使高度为0，也可以撑开容器

- 空行内元素生成空行内盒，但这些盒仍然有外边距、内边距、边框和一个行高，因此跟有内容的元素一样会影响计算。

  ![1557723773875](/img/user/programming/font-end/primitive/css/css-2.1/1557723773875.png)

- 无论是br主动换行，还是自动换行，不影响行高
- 计算值会影响基线的偏移，为了最小化行盒的高度，所以基线会被影响，常见应用 content dipslay inline-block height 100%

## Inline-block

类比于块容器，块容器此时生成一个行内级格式化上下文

height为auto时

- 如果它只含有行内级子级，生成一个行内级格式化上下文，高度就是最高的行框的顶端与最低的行框的底端之间的距离
- 如果它只含有块级子级，高度就是最高的块级子级盒的上外边距边界到最低的块级子级盒的下外边距边界之间的距离

![1557722701927](/img/user/programming/font-end/primitive/css/css-2.1/1557722701927.png)

![1557717139077](/img/user/programming/font-end/primitive/css/css-2.1/1557717139077.png)

如果inline-block里面什么都没有，基线就是margin，如果行内级元素，那就是里面的行盒的基线决定，如果有多个行盒，就由最后一个行盒的基线决定整个inline-block的基线

为什么背景会分布在x两边？是因为文字有默认居中的特性，为了基线对齐，看起来就像是整个盒子往下移动了

inline-block内部有支柱吗？有，特性和块容器的完全一致

背景永远都是黏在行内盒的顶端，因为行内级盒子默认在父容器内自从左上开始排列，所以增加height，是向下延伸

增加line-height，背景向上移动，行距默认分布在内容区的两边

![1559096116352](/img/user/programming/font-end/primitive/css/css-2.1/1559096116352.png)

.square的line-height，是136px，square是高50px，所以刚好在内容区的正上方

## 实践

### 全局行高使用经验

```css
body{font-size:14px;line-height:?}
```

博客：1.5

匹配20像素，行高的默认值是20px line-height:1.4286

### 单行文本垂直居中

直接使用line-height

```css
#content{
  font-size:36px;
  line-height:500px;
}
span{
  line-height:0px;
  background-color: pink;
}
```

![1557640161117](/img/user/programming/font-end/primitive/css/css-2.1/1557640161117.png)

其他什么都不需要，因为line-height值被平均分配在内容区的两边，支柱和容器一样高，基线对齐，字体大小一致

如果文本的字体大小和支柱的字体大小不一致，把支柱的字体设为0，此时支柱四线合一，控制文本vertical-align ：middle即可，因为font-size为零，此时text-top和text-bottom失效，表现与middle一致，只有baseline仍然存在

如果没有line-height，无法使用text-top和text-bottom

使用middle，绿线与红线的距离等于绿线到x中间的距离

### 图片的3px空隙

![1557713705205](/img/user/programming/font-end/primitive/css/css-2.1/1557713705205.png)

### 单个图片的垂直居中

只设置line-height的话并没有真正的居中，向下偏移了

支柱的font-size为0，然后图片middle

![1557570724729](/img/user/programming/font-end/primitive/css/css-2.1/1557570724729.png)

![1557570768007](/img/user/programming/font-end/primitive/css/css-2.1/1557570768007.png)

### 多行文本垂直水平居中

![1557581435771](/img/user/programming/font-end/primitive/css/css-2.1/1557581435771.png)

### 大小不固定的图片和多行文字的垂直居中

支柱的font-size为0，中线基线合一，控制图片middle，控制文本middle即可

如果font-size不为0：

- 只有文本可以对齐，line-height平分在内容区两边，默认baseline，自然居中
- 但是，因为中线始终在平分线的下方，此时图片无法真正的垂直居中

插入一个大的inline-block，inline-block：middle，对准了支柱的中线，此时容器由插入的元素撑开，支柱的中线就调整到了平分线的位置，然后全部middle即可垂直居中对齐

关键就是：**使得支柱的中线与容器的平分线重合**，这样无论是图片还是文本都可以通过vertical-align居中

无论容器的line-height属性究竟是多少，这两个方法都是万能的

## Code

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name='viewport' content='width=device-width,initial-scale=1.0,user-scalable=no'>
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <title>Page Title</title>
</head>
<style>
  * {
    margin: 0;
    padding: 0;
  }
  h1{
    line-height: 72px;
    background-color: white;
  }
  #root{
    height:1000px;
  }
  #container{
    position: relative;
    line-height: 100px;
    font-size:36px;
    font-family: 'simsun';
    background-color: rgb(200, 200, 200);
  }
  #text{
    line-height: normal;
    font-size:36px;
    position: relative;
    background-color: pink;
    vertical-align: baseline;
  }
  #strut{
    position: relative;
    font-size:inherit;
    line-height: inherit;
    background-color: pink;
    border:1px greenyellow solid;
  }
  img{
    position: relative;
    line-height:normal;
    font-size:36px;
    width: 15px;
    height: 20px;
    vertical-align: baseline;
  }
  .square{
    line-height: normal;
    font-size:36px;
    position: relative;
    width: 50px;
    height: 50px;
    display: inline-block;
    background-color: deeppink;
    vertical-align: baseline;
  }
  #inner_text{
    background-color: pink;
  }
  #inner_strut{
    line-height:0;
    font-size:0px;
  }
  #empty{
    line-height: normal;
    font-size:36px;
    position: relative;
    background-color: pink;
    vertical-align: baseline;
  }
  #container:before{
    position: absolute;
    content:" ";
    top:50%;
    width: 100%;
    border-top: 1px red solid;
  }
/*   #container:after{
    content: "";
    display: inline-block;
    height: 150px;
    width: 0;
    background: pink;
    vertical-align: middle;
  }    */
  .square::before,#text::before,#strut::before{
    position: absolute;
    content:" ";
    top:50%;
    width: 100%;
    border-top: 1px green solid;
  }
</style>

<body>
  <div id="root">
    <h2>line-height&vertical-align</h2>
    <div id='container'>
      <span id='strut'>llx</span>
      <span id='text'>textYyx</span>
      <img src="./img/img2-middle.jpg" alt="123"/>
      <span id='empty'></span>
      <span class="square">
        <span id='inner_text'>x</span>
      </span>
      <span class="square">
        <span id="inner_strut">llx</span>
      </span>
      <span class="square">
        <img src="./img//img2-middle.jpg" alt="456">
      </span>
    </div>
  </div>
<script>
</script>
</body>
</html>
```

# 垂直外边距的重叠

8.3.1 合并外边距

在网页中相邻的**垂直方向**的外边距会发生**外边距的重叠**，水平方向的相邻外边距不会重叠，而是求和

> 垂直水平margin都是会发生重叠的，但是一般只考虑垂直的，因为块级元素不可能水平，如果浮动了也同时开启了BFC，不会发生margin重叠

所谓的垂直外边距重叠指**兄弟元素之间**的**相邻**外边距会取**最大值**而**不是取和**

如果**父子元素**的**垂直外边距相邻**了，则**子元素的外边距会设置给父元素，0+margin-top**

**避免：**

1. 在子元素上**插入（非空或者有高度的空元素）一个元素（且该元素不可以浮动）**
2. 插入一个空的table也OK，用css display:table
3. 为子元素设置一个上**边框**或者为父元素设置一个上**边框**；
4. 设子置上padding，再刨去1px，保持平衡
5. 为父元素设置垂直上内边距，内容区被撑大，再刨去内边距大小的高度

```stylus
.time_picker
  width 200px
  float left
  margin-left 10px
  margin-top 10px
  margin-bottom 10px
.title_container
  float left
  margin-left 10px
  margin-top 10px
  margin-bottom 10px
  .event_title
    width 270px
    height 36px
    border 1px #dbdbdb solid
    border-radius 5px
    padding-left 10px
.msg_container
  margin-left 10px
  margin-top 10px //会传递给上方开启了浮动的兄弟，如果自己也开浮动就没问题了

```

# 高度塌陷 @@@

在文档流中，父元素的高度默认是被子元素撑开的，也就是子元素多高，父元素就多高。

子元素**脱离文档流**，此时将会导致子元素无法撑起父元素的高度，导致父元素的高度塌陷。

由于父元素的高度塌陷了，则父元素下的所有元素都会向上移动，这样将会导致页面布局混乱

## 重点：绝对定位导致的高度塌陷

自己也开始绝对定位，通过4个0，自动拉伸到宽高，适用于祖父存在的情况

或者开启相对定位，定义宽高100%

如果祖父即是文档本身，则无法修复坍塌

## 方案一：设置高度

在开发中一定要避免出现高度塌陷的问题,我们可以将父元素的高度写死，以避免塌陷的问题出现

缺点：一旦**高度写死**，父元素的高度将不能自动适应子元素的高度，所以这种方案是不推荐使用的。

## 方案二：开启Block Formatting Context @@@

根据W3C的标准，在页面中元素都一个隐含的属性叫做Block Formatting Context，简称BFC，该属性可以设置打开或者关闭，默认是关闭的。

元素的类型和display的属性决定了Box的类型。不同类型的Box会参与不同的Formatting Context（一个决定如果渲染文档的容器），所以不同的Box会以不同的方式渲染

block-level box:

- display属性为block，list-item，table的元素，会生成block-level box，并参与block formatting context（BFC）
- display属性为inline，inline-block，inline-table的元素，会生成inline-level box，并参与inline formatting context（IFC）

**Formatting context**

- Formatting context 是 w3c css2.1规范中的一个概念
- 它是页面中的一块渲染区域，并且有一套渲染规则，它决定了子元素将如何定位，以及和其他元素的关系和相互作用。

**BFC**

- 直译为“块级格式化上下文”。它是一个独立的渲染区域，只有block-level box参与，它规定了内部的block-level box如何布局，并且与这个区域外不毫不相干
- 当开启元素的BFC以后，元素将会具有如下的特性：
  1. 内部的Box会在垂直方向，一个接一个地放置（块级元素独占一行）
  2. BFC区域不会与float box重叠
  3. 内部的Box垂直方向的距离由margin决定。属于**同一个BFC的两个相邻**的block-level Box的margin会发生重叠
     - 插入一个空的div是不行的
     - 水平的margin不会发生重叠
  4. 计算BFC的高度时，浮动元素也参与计算。
  5. BFC就是页面上一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之亦然
- 产生BFC的元素，在它产生的BFC内无法管理自己，只能管理内部的block-level box

**如何开启元素的BFC**

1. 设置元素**浮动**
   - 使父元素也浮动，虽然可以撑开父元素，但是会导致父元素的宽度丢失。而且使用这种方式也会导致下边的元素上移，不能解决问题
2. 设置元素绝对**定位**或固定**定位**
   - 与1类似
3. 设置元素为inline-block，table-cell，table-caption，flex，inline-flex
   - 可以解决问题，但是会导致宽度丢失，不推荐使用这种方式，行内元素宽高由内容撑开
   - inline-block虽然可以开启BFC，但是无法管理display属性为inline-block的元素
4. 将元素的overflow设置为一个**非**visible的值
   - 推荐方式：将overflow设置为hidden是副作用最小的开启BFC的方式。
   - 有相对定位的元素，如果移到了父元素之外，则不可以用这种方法，因为会被hidden
   - 没啥特殊要求的话直接用这个是最好的 @@@
5. 根元素

- 但是在IE6及以下的浏览器中并不支持BFC，所以使用这种方式不能兼容IE6。
- 在IE6中虽然没有BFC，但是具有另一个隐含的属性叫做hasLayout，该属性的作用和BFC类似，所在IE6浏览器可以通过开hasLayout来解决该问题
- 开启方式很多，我们直接使用一种副作用最小的：直接将元素的zoom设置为1即可
  - zoom表示放大的意思，后边跟着一个数值，写几就将元素放大几倍
  - zoom:1表示不放大元素，但是通过该样式可以开启hasLayout
  - zoom这个样式，只在IE中支持，其他浏览器都不支持
  - 在IE6中，如果为元素指定了一个宽度，则会默认开启hasLayout

## 方案三：空div+clear

clear，只能作用于兄弟元素，所以不能直接解决高度坍塌的问题

可以直接在高度塌陷的父元素的最后，添加一个空白的div，由于这个div并没有浮动，所以他是可以撑开父元素的高度的

然后在对其进行清除浮动，这样可以通过这个空白的div来撑开父元素的高度，基本没有副作用，兼容各种浏览器

使用这种方式虽然可以解决问题，但是会在页面中添加多余的结构。

## 方案三完善：伪类

通过after伪类，选中box1的后边，可以通过after伪类向元素的最后添加一个空白的块元素，然后对其清除浮动，

这样做和添加一个div的原理一样，可以达到一个相同的效果

必须转换为块元素，**因为清除浮动实际上是通过增加外边距来达到相似的效果，**而内联元素不支持垂直方向的外边距

在IE6中不支持after伪类, 所以在IE6中还需要使用hasLayout来处理

```css
.clearfix:after{
 /*添加一个内容*/
 content: "";
 /*转换为一个块元素*/
 display: block;
 /*清除两侧的浮动*/
 clear: both;
}
.clearfix {
  zoom:1;
}
```

## 方案四：display:table;兼容垂直外边距

子元素和父元素相邻的垂直外边距会发生重叠，子元素的外边距会传递给父元素

使用空的table标签可以隔离父子元素的外边距，阻止外边距的重叠

```css
.clearfix:before,
.clearfix:after{
 content: "";
 display: table;
 clear: both;
}
.clearfix{
 zoom: 1; //兼容IE6
}
```

经过修改后的clearfix是一个多功能的，既可以解决高度塌陷，又可以确保父元素和子元素的垂直外边距不会重叠

问题来了：方案四before和after都用了，而且都是table，为什么block不行？还有兄弟元素间的重叠怎么解决呢？

**在移动端，因为没有ie浏览器，所以一般都是直接用overflow:hidden解决**

## 总结 @@@

clearfix，使用空table而不使用空div的原因是：必须是块级元素，因为清除浮动是外边距，而内联元素不支持垂直外边距，使用table的原因是：插入非空元素或者元素具有高度，才可以防止垂直外边距重叠，而插入空的table就可以

# 层叠上下文

[层叠上下文](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Positioning/Understanding_z_index/The_stacking_context) @@@

## 失效的 position:fixed

https://www.cnblogs.com/coco1s/p/7358830.html

当元素祖先的 transform 属性非 none 时,容器由视口改为该祖先

指定了 `position:fixed` 的元素，如果其祖先元素存在非 none 的 transform 值 ，那么该元素将相对于设定了 `transform` 的祖先元素进行定位。

那么，为什么会发生这种情况呢？说好的相对视口（Viewport）定位呢？

这个问题，就牵涉到了 Stacking Context ，也就是堆叠上下文的概念了。解释上面的问题分为两步：

1. 任何非 none 的 transform 值都会导致一个堆叠上下文（Stacking Context）和包含块（Containing Block）的创建。
2. 由于堆叠上下文的创建，该元素会影响其子元素的固定定位。设置了 `position:fixed` 的子元素将不会基于 viewport 定位，而是基于这个父元素。

下述 3 种方式目前都会使得 `position:fixed` 定位的基准元素改变（本文重点）：

1. `transform` 属性值不为 none 的元素
2. `perspective` 值不为 none 的元素
3. 在 `will-change` 中指定了任意 CSS 属性

上面也谈到了，上述结论是在最新的 Chrome 浏览器下（Blink内核），经过测试发现，火狐，edge同样

在 MAC 下的 Safari 浏览器（WebKit内核，Version 9.1.2 (11601.7.7)）和 IE Trident/ 内核及 Edge 浏览器下，上述三种方式都不会改变 `position: fixed` 的表现！

> 试了一下最新的 safari，貌似也会被改变了

所以，当遇到 `position: fixed` 定位基准元素改变的时候，需要具体问题具体分析，多尝试一下，根据需要兼容适配的浏览器作出调整，不能一概而论。

## 概念

**堆叠上下文**（Stacking Context）：堆叠上下文是 HTML 元素的三维概念，这些 HTML 元素在一条假想的相对于面向（电脑屏幕的）视窗或者网页的用户的 z 轴上延伸，HTML 元素依据其自身属性按照优先级顺序占用层叠上下文的空间。

生成了 Stacking Context 的元素会影响该元素的层叠关系与定位关系

### 形成条件

文档中的层叠上下文由满足以下任意一个条件的元素形成：

- 根元素 (HTML),
- z-index 值不为 "auto"的 绝对/相对定位，
- 一个 z-index 值不为 "auto"的 flex 项目 (flex item)，即：父元素 display: flex|inline-flex，
- [`opacity`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/opacity) 属性值小于 1 的元素（参考 [the specification for opacity](http://www.w3.org/TR/css3-color/#transparency)），
- [`transform`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transform) 属性值不为 "none"的元素，
- [`perspective`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/perspective)值不为“none”的元素，
- `position: fixed`
- [`mix-blend-mode`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/mix-blend-mode) 属性值不为 "normal"的元素，
- [`filter`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/filter)值不为“none”的元素，
- [`isolation`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/isolation) 属性被设置为 "isolate"的元素，
- 在 [`will-change`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/will-change) 中指定了任意 CSS 属性，即便你没有直接指定这些属性的值（参考 [这篇文章](http://dev.opera.com/articles/css-will-change-property/)）
- [`-webkit-overflow-scrolling`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/-webkit-overflow-scrolling) 属性被设置 "touch"的元素

### 特点

在层叠上下文中，其子元素同样也按照上面解释的规则进行层叠。 特别值得一提的是，其子元素的 z-index 值只在父级层叠上下文中有意义。子级层叠上下文被自动视为父级层叠上下文的一个独立单元。

给一个 HTML 元素定位和 z-index 赋值创建一个层叠上下文，（opacity 值不为 1 的也是相同）。

层叠上下文可以包含在其他层叠上下文中，并且一起创建一个有层级的层叠上下文。

每个层叠上下文完全独立于它的兄弟元素：当处理层叠时只考虑子元素。

每个层叠上下文是自包含的：当元素的内容发生层叠后，整个该元素将会 在父层叠上下文中 按顺序进行层叠。

### 示例

![](/img/user/programming/font-end/primitive/css/css-2.1/2022-06-01-15-05-12.png)

在这个例子中，每个被定位的元素都创建了独自的层叠上下文，因为他们被指定了定位属性和 z-index 值。我们把层叠上下文的层级列在下面：

```text
Root
  DIV #1
  DIV #2
  DIV #3
    DIV #4
    DIV #5
    DIV #6
```

分辨出层叠的元素在 Z 轴上的渲染顺序的一个简单方法是将它们想象成一系列的版本号，子元素是其父元素版本号之下的次要版本。通过这个方法我们可以轻松得看出为什么一个 z-index 为 1 的元素（`DIV #5`）层叠于一个 z-index 为 2 的元素（`DIV #2`）之上，而一个 z-index 为 6 的元素（`DIV #4`）层叠于 z-index 为 5 的元素（`DIV #1`）之下。在我们的例子中（依照最终渲染次序排列）：

- Root
  - DIV #2 - z-index 为 2
  - DIV #3 - z-index 为 4
    - DIV #5 - z-index 为 1，在一个 z-index 为 4 的元素内层叠，所以渲染次序为 4.1
    - DIV #6 - z-index 为 3，在一个 z-index 为 4 的元素内层叠，所以渲染次序为 4.3
    - DIV #4 - z-index 为 6，在一个 z-index 为 4 的元素内层叠，所以渲染次序为 4.6
  - DIV #1 - z-index 为 5

### Z-index:0 和 z-index:auto

z-index为0是会创建新的层叠上下文。

z-index: 0与z-index不设置，也就是z-index: auto在同一层级内没有高低之分，文档流中后出现的会覆盖先出现的。

前提是得有层级，如果是默认的文档流里面的，那还是会被 绝对定位的节点覆盖，必须得是 relative 或者是 absolute 定位的才行

# CSS Hack

## 检测低版本ie函数

js中的作用域都是函数作用域

```js
function *isIE*(version){
  var b = *document*.createElement("b");
  b.innerHTML="<!--[if IE "+version+"]><i></i><![endif]-->";
  return   b.getElementsByTagName("i").length == 1 ;
 }
```
