---
{"aliases":[],"tags":[],"review-dates":["2020-05-03","2020-05-26","2023-04-25"],"dg-publish":true,"date-created":"2022-09-03-Sat, 10:07:24 pm","date-modified":"2023-08-04-Fri, 5:24:30 pm","permalink":"/programming/front-end/primitive/html/html-basic/","dgPassFrontmatter":true}
---


# 基本

## 术语

超文本：指的是超链接，意思是可以通过超链接从一个网页到另一个网页

元素：就是标签

## 字符实体

表示特殊字符的符号，**HTML 中的预留字符必须被替换为字符实体。**

浏览器在解析到实体时，会自动将其转换为对应的字符

### 实体的语法

&${entity_name};

&#${entity_number};

常用的实体:

- < 小于号 less then → `&lt;` || `&#60`;
- \> 大于号 greater then → `&gt;`
- 版权符号 → ` &copy;`
- 换行：`&#10; `
- 回车：`&#13; `

实体参考手册：http://www.w3school.com.cn/tags/html_ref_entities.html

### 换行与回车

今天，我总算搞清楚 " 回车 "（carriage return）和 " 换行 "（line feed）这两个概念的来历和区别了。

在计算机还没有出现之前，有一种叫做电传打字机（Teletype Model 33）的玩意，每秒钟可以打 10 个字符。但是它有一个问题，就是打完一行换行的时候，要用去 0.2 秒，正好可以打两个字符。要是在这 0.2 秒里面，又有新的字符传过来，那么这个字符将丢失。

于是，研制人员想了个办法解决这个问题，就是在每行后面加两个表示结束的字符。一个叫做 " 回车 "，告诉打字机把打印头定位在左边界；另一个叫做 " 换行 "，告诉打字机把纸向下移一行。

这就是 " 换行 " 和 " 回车 " 的来历，从它们的英语名字上也可以看出一二。

后来，计算机发明了，这两个概念也就被般到了计算机上。那时，存储器很贵，一些科学家认为在每行结尾加两个字符太浪费了，加一个就可以。于是，就出现了分歧。

Unix 系统里，每行结尾只有 " 换行 "，即 `\n`

Windows 系统里面，每行结尾是 "<回车><换行>"，即 `\r\n`

Mac 系统里，每行结尾是 "<回车>"。

一个直接后果是, Unix/Mac 系统下的文件在 Windows 里打开的话, 所有文字会变成一行;

而 Windows 里的文件在 Unix/Mac 下打开的话，在每行的结尾可能会多出一个^M 符号。

### 常用空格 @@@

http://www.cnblogs.com/keyi/p/7286177.html

在 HTML 中，默认情况下连续的空白符会被合并，**换行符会被当作空白符** 来处理。

在 HTML 中，如果你用空格键产生此空格，空格是不会累加的（只算 1 个）。要使用 html 实体表示才可累加。

`&nbsp;`

- 不换行空格，全称 No-Break Space。
- 它是按下 space 键产生的空格。
- 该空格占据宽度受字体影响明显而强烈

`&ensp;`

- 半角空格, 全称是 En Space, en 是字体排印学的计量单位，为 `em` 宽度的一半。
- 名义上是小写字母 n 的宽度，此空格传承空格家族一贯的特性：透明的
- 根据定义，它等同于字体度的一半（如 16px 字体中就是 8px）
- 此空格有个相当稳健的特性，就是其占据的宽度正好是 1/2 个中文宽度，而且基本上不受字体影响

`&emsp;`

- 全角空格，全称是 Em Space，em 是字体排印学的计量单位，相当于当前指定的点数。例如，1 em 在 16px 的字体中就是 16px。
- 此空格也传承空格家族一贯的特性：透明的
- 此空格也有个相当稳健的特性，就是其占据的宽度正好是 1 个中文宽度，而且基本上不受字体影响。

`&thinsp;`

- 窄空格，全称是 Thin Space。
- 我们不妨称之为“瘦弱空格”，就是该空格长得比较瘦弱，身体单薄，占据的宽度比较小。它是 em 之六分之一宽。

`&zwnj; `

- 它叫零宽不连字，全称是 Zero Width Non Joiner，简称“ZWNJ”，是一个不打印字符，放在电子文本的两个字符之间，抑制本来会发生的连字，而是以这两个字符原本的字形来绘制。Unicode 中的零宽不连字字符映射为“”（zero width non-joiner，U+200C），HTML 字符值引用为： `&#8204;`

`&zwj;`

- 它叫零宽连字，全称是 Zero Width Joiner，简称“ZWJ”，是一个不打印字符，放在某些需要复杂排版语言（如阿拉伯语、印地语）的两个字符之间，使得这两个本不会发生连字的字符产生了连字效果。零宽连字符的 Unicode 码位是 U+200D (HTML: &#8205; &zwj;）。
- 此外，浏览器还会把以下字符当作空白进行解析：空格（`&#x0020;`）、制表位（`&#x0009;`）、换行（`&#x000A;`）和回车（`&#x000D;`）还有（`&#12288;`）等等。

https://www.zhangxinxu.com/wordpress/2015/01/tips-blank-character-chinese-align/

## DOCTYPE 和浏览器渲染模式

DOCTYPE，或者称为 Document Type Declaration（文档类型声明，缩写 DTD）

通常情况下，DOCTYPE 位于一个 HTML 文档的最前面的位置，位于根元素 HTML 的起始标签之前。

因为浏览器必须在解析 HTML 文档正文之前就确定当前文档的类型，以决定其需要采用的渲染模式，不同的渲染模式会影响到浏览器对于 CSS 代码甚至 JavaScript 脚本的解析

到目前为止，各浏览器主要包含了三种模式。在 HTML5 草案中，更加明确的规定了模式的定义:

| 传统名称                    | HTML5 草案名称      | document.compatMode 返回值 |
| --------------------------- | ------------------- | -------------------------- |
| standards mode(strict mode) | no-quirks mode      | CSS1Compat                 |
| almost standards mode       | limited-quirks mode | CSS1Compat                 |
| quirks mode                 | quirks mode         | BackCompat                 |

### document.compatMode

document.compatMode 属性最初由微软在 IE 中创造出来，这是一个只读的属性，返回一个字符串，只可能存在两种返回值:

- BackCompat: 标准兼容模式未开启（怪异模式）
- CSS1Compat: 标准兼容模式已开启（标准模式）

### 浏览器渲染

在现代主流浏览器中，其实怪异模式的渲染和标准与几乎标准间没有太大的差别（ie9+ 谷歌 火狐 ...）

- ie5.5 之前都是 ie 自己的渲染模式，怪异模式
- ie6 才开始慢慢支持标准，标准模式，在 ie6 中怪异和标准模式的区别最大
- ie7 8 9 都是基于标准模式升级的，他们理论上存在怪异模式

HTML5 提供的 `<DOCTYPE html>` 是标准模式，向后兼容的,等同于开启了标准模式，

- 那么浏览器就得老老实实的按照 W3C 的 标准解析渲染页面
- 一个不含任何 DOCTYPE 的网页将会以 怪异 (quirks) 模式渲染。
- 在 `<DOCTYPE html>` 之前的标签和注释也可能会导致怪异模式

### 怪异盒模型

怪异模式下的盒模型就是后来的 border-box

### DTD

**`<!DOCTYPE html>`**

当 doctype 信息如上时，表明该页面是遵守了 HTML5 规范的，浏览器会选择 Standards Mode，这种 doctype 是最推荐的一种，我们平时设计页面都应该加上这一个 doctype。

HTML5 不基于 SGML,所以不需要引用 DTD,浏览器内部本身有对标签进行解析渲染验证的模块

**`<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">`**

当 doctype 如上时，浏览器同样会选择 Standards Mode，虽然和第一种 doctype 有一些区别，但是几乎可以认为是一样的。

**`<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">`**

当 doctype 如上时，浏览器会选择 Almost Standards Mode，渲染时和标准会有一些区别

当 doctype 缺失的时候，浏览器会选择 Quirks Mode，这是非常不推荐的方式，

当 doctype 上面有注释，标签或者空行时，某些浏览器都会认为该页面不具有 doctype。

我们应该尽量避免 Quirks Mode，这对一个 web 应用是非常不利的地方。

每种 doctype 在浏览器中对应的渲染模式

> https://en.wikipedia.org/wiki/Quirks_mode

# HTML 标签

html 标签我们更关心的是语义，而不是表现形式

## 常用标签

### H1 标签

h1 标签并不是没有用，因为搜索引擎检索的时候，检索完 title 会立即查看 h1 的内容，会影响搜索引擎的排名。

h1 标签非常重要，不可以忽略语义方面的意义

h1 只能写一个，再语义方面的考量

### P 标签

段落标签

默认独占一行

语义，标签内的是一个段落

块级元素，但是却不可以嵌套其他块级元素，浏览器会将 p 解析成两个

![1568469277490](/img/user/programming/front-end/primitive/html/html-basic/1568469277490.png)

嵌套失败

### Br 标签

换行标签，执行换行，是一个自结束标签

在 html 中，多个空格会被当成一个空格解析，换行也会当成一个空格解析

### Hr 标签

水平线，自闭合标签

通过 border 控制相关属性

想要修改 hr 的颜色，应该修改 border 属性，而不是 color 或者 background

### Img 标签

[image](../../../FAQ/ui-component/image.md)

### A 标签

使用超链接可以从一个页面跳转到另一个页面

**`anchor`（锚点）元素**

使用 a 标签来创建超链接

**属性：**

1. href: 指向目标地址，路径或网址
2. target: 规定在何处打开目标文档
   - \_self: 自己，在当前窗口打开（默认值）
   - \_blank: 在新标签页打开
   - 设置为一个内联框架的 name 属性，链接将会在指定的内联框架中打开

**利用超链接跳转：**

`<a id="bottom" href="#"></a>`  //回到顶部

a 标签的 href=”#”，会阻止 js 绑定的点击事件，可以改成”javascript:;

html 中有一个属性，每个标签都可以设置，该属性可以作为标签的唯一标识：id 属性，id 属性不能以数字开头

`<a href="#bottom"><a/>`

发送电子邮件的超链接，点击链接之后自动打开计算机的邮件客户端，如果有默认邮件客户端则打开，否则根据浏览器而定，现在很少用了

`<ahref=”mailto:aaa@aaa.com”><a/>`

### Div 标签

用于布局，无任何语义和样式

### 列表标签

#### 无序列表

使用 ul 标签来创建一个无序列表

使用 li 在 ul 中创建一个一个的列表项

一个 li 就是一个列表项

通过 list-style-type 属性可以修改无序列表的项目符号，可选值：

1. disc，默认值，实心的圆点
2. square，实心的方块
3. circle，空心的圆

list-style 简写属性，可以设置 `list-style-type list-style-position list-style-image`

```css
ul {
  list-style: none; //去除项目符号
}
```

注意：默认的项目符号我们一般都不使用！！

如果需要设置项目符号，则可以采用 `list-style-image` 来设置@

**ul li 都是块级元素**

#### 有序列表

有序列表和无序列表类似，只不过它使用 ol 来代替 ul

`list-style-type` 属性，可以指定序号的类型，可选值：

- 1，默认值，使用阿拉伯数字
- a/A 采用小写或大写字母作为序号
- i/I 采用小写或大写的罗马数字作为序号

ol 也是块元素

#### 定义列表

定义列表用来对一些词汇或内容进行定义

使用 dl 来创建一个定义列表

dl 中有两个子标签

- dt ： 被定义的内容
- dd ： 对定义内容的描述

dl 和 ul 和 ol 之间都可以互相嵌套

```html
<dl>
  <dt>武松</dt>
  <dd>景阳冈打虎英雄，战斗力99</dd>
  <dd>后打死西门庆，投奔梁山</dd>
  <dt>武大</dt>
  <dd>著名餐饮企业家，战斗力0</dd>
</dl>
```

![1548597120934](/img/user/programming/front-end/primitive/html/html-basic/1548597120934.png)

### Span 标签

用于 **选中文字**，为文字设置样式

### 内联框架：iframe 标签

使用内联框架可以引入一个外部的页面

使用 iframe 标签来创建一个内联框架

开发中不推荐使用内联框架，因为内联框架的内容不会被浏览器检索

如果是内网网页开发，可以使用内联框架

**属性：**

1. src: 外部页面的路径
2. width;height;
3. name: 为内联框架指定一个 name 属性，可以在超链接通过 name 属性指定该框架，让页面在框架内打开

**缺点**：

- 会阻塞主页面的 onload 事件
- 搜索引擎的检索程序无法解读这种页面，不利于 SEO

## 块级元素和内联元素

### 块级元素

div 就是一个块级元素，所谓的块级元素就是会独占一行的的元素,无论他的内容有多少他都会独占一整行。

- p h1 h2 h3 table...
- **ul li 都是块级元素**

div 这个标签没有任何语义，就是一个纯粹的块元素，并且不会为它里边的元素设置任何的默认样式，

div 元素主要用来对页面进行布局的

### 内联元素（行内元素）

span 是一个内联元素（行内元素）

所谓的行内元素，指的是只占自身大小的元素，不会占用一行

常见的内联元素: a img iframe span

span 没有任何的语义，span 标签专门用来选中文字，然后为文字来设置样式

### 嵌套规则 @@@

块元素主要用来做页面中的布局，内联元素主要用来选中 **文本** 设置样式，

一般情况下只使用块元素去包含内联元素，而不会使用内联元素去包含一个块元素

**a 元素可以包含任意元素**，除了他本身, 浏览器会解析成两个超链接, 因为 a 元素的行为怪异，一般直接 display 为 block

**p 元素不可以包含任何块元**, 浏览器会解析成

内联元素的 img 与 input 比较特殊，他们有内联元素没有的宽高，w3c 定义为 replace 元素，将元素设置为 display:inline-block，模拟的就是 replace 元素。

### 注意

那哪些块元素里面不能放哪些块元素呢？我们可以先把所有的块元素再次划分成几个级别的，我们已经知道 `<html>` 是在最外层，`<html>`

下一级里面只会有 `<head>`、`<body>`、`<frameset>`、`<noframes>`。而我们已经知道了可视的元素只会出现在 `<body>` 里，所以我们把 `<body>` 划在第一个级里面

接着，把不可以自由嵌套的元素划在第三个级，其他的就归进第二个级。

所谓的不可自由嵌套的元素就是里面只能放内联元素的，它们包括有：标题标记的 `<h1>`、`<h2>`、`<h3>`、`<h4>`、`<h5>`、`<h6>`、`<caption>`；段落标记的 `<p>`；分隔线 `<hr>` 和一个特别的元素 `<dt>`(它只存在于列表元素 `<dl>` 的子一级)。

为什么说第二级的元素可以自由嵌套呢？我们可以把它们看成是一些容器（或者说是盒子）， 这些容器的大小可以自由变化，例如我们可以把 ``<ul>`嵌在`<div>`里面，也可以把`<div>`嵌在`<li>` 里面。

在 HTML 里有几个元素是比较特别的：`<ul>`、`<ol>`、`<dl>`、`<table>`，它们的子一层必须是指定元素，`<ul>`、`<ol>` 的子一级必须是 `<li>`；`<dl>` 的子一级必须是 `<dt>` 或者 `<dd>`；`<table>` 的子一层必须是 `<caption>` 或 `<thead>`、`<tfoot>`、`<tbody>` 等，而再子一层必须是 `<tr>` （`<tr>` 只存在于 `<thead>`、`<tfoot>`、`<tbody>` 中），之后才是可放内容的 `<td>` 或者 `<th>`。

`<img>` 和 `<input>` 有着其它内联元素没有的宽和高。它们在 "inline" 的情况下又有 "black" 的特性，W3C 称之为 replace 元素。

## xHTML 语法规范

为了从 HTML 过渡到 XML

XML 语法更加严格

HTML 中不区分大小写，但是我们一般 **使用小写**

HTML 中注释不能嵌套

HTML 标签必须结构完整，自结束或者成对出现

HTML 标签可以嵌套，但是不可以交叉嵌套

HTML 标签中属性必须有值，而且值必须加引号（所以字符串值一定出现两对引号）

## 语义化标签 (h5 保留)

为了加强 html 的语义，h5 标注仍然保留了一些文本标签，这些标签我们不关心表现，仅关注语义，reset 全都会清除

浏览器为了在页面中没有样式时，也可以有一个比较好的显示效果，所以为很多的元素都设置了一些默认的 margin 和 padding，而它的这些默认样式，正常情况下我们是不需要使用的。所以我们往往在编写样式之前需要将浏览器中的默认的 margin 和 padding 统统的去掉

### Em 和 Strong

这两个标签都表示一个强调的内容

- em 主要表示语气上的强调,em 在浏览器中默认使用 **斜体** 显示
- strong 表示强调的内容，比 em 更强烈，默认使用 **粗体** 显示

### I 和 B

i 标签中的内容会以斜体显示

b 标签中的内容会以加粗显示

这两个标签没有语义，h5 规范中规定，对于不需要着重的内容而是单纯的加粗或者是斜体，就可以使用 b 和 i 标签

**i 标签常用于包裹 icon 字体图标，约定俗成的语义**

### Small 标签

small 标签中的内容会比他的 **父元素** 中的文字要小一些

在 h5 中使用 small 标签来表示一些细则一类的内容，比如：合同中小字，网站的版权声明都可以放到 small

big 被淘汰了

### Cite 标签

网页中所有的加书名号的内容都可以使用 cite 标签，表示参考的内容。比如：书名 歌名 话剧名 电影名

### Q 标签

q 标签表示一个 **短** 的引用（行内引用）

q 标签引用的内容，浏览器会默认加上引号

### Blockquote 标签

blockquote 标签表示一个长引用（块级引用）

行级标签内不可以嵌套

### Sup 和 Sub 标签

sup 上标

sub 下标

### Ins 和 Del 标签

ins 表示插入的内容，通常会加上下划线

del 表示删除的内容，通常会加上删除线

### Cite

引述来源作品的标题

### Pre 和 Code 标签

需要页面中直接编写一些代码

pre 是一个预格式标签，会将代码中的格式保存，不会忽略多个空格

code 专门用来表示代码

我们一般结合使用 pre 和 code 来表示一段代码

![1548593674100](/img/user/programming/front-end/primitive/html/html-basic/1548593674100.png)

## 语义化标签 (H5 新增)

### 产生

在 HTML 5 出来之前，我们用 div 来表示页面头部，章节，页脚等。但是这些 div 都没有实际意义。

各大浏览器厂商分析了上百万的页面，从中发现了 DIV 名称的通用 id 名称大量重复。例如，很多开发人员喜欢使用

div id="footer" 来标记页脚内容，所以 Html5 元素引入了语义化标签（一组新的片段类元素）

- https://dev.opera.com/blog/presentation-html5-and-accessibility-sitting-in-a-tree-4/idlist-url.htm
- https://dev.opera.com/blog/presentation-html5-and-accessibility-sitting-in-a-tree-4/classlist-url.htm

```html
<hgroup></hgroup>
<header></header>
<nav></nav>
<section></section>
<footer></footer>
<article></article>
<aside></aside>
```

### 语义化的好处

1. HTML5 可以让很多更语义化结构化的代码标签代替大量的无意义的 div 标签
2. 这种语义化的特性提升了网页的质量和语义
3. 对搜索引擎更加的友好
4. 他们这些标签功能就是代替 `<div>` 功能中的一部分，他们没有任何的默认样式，除了会让文本另起一行外；
   - https://gsnedders.html5.org/outliner/

### Hgroup 元素

hgroup 元素代表 网页 或 section 的标题，当元素有多个层级时，该元素可以将 h1 到 h6 元素放在其内，譬如文章的主标题和副标题的组合

```html
<hgroup>
  <h1>网站标题</h1>
  <h2>网站副标题</h2>
</hgroup>
```

**注意:**

1. 如果只需要一个 h1-h6 标签就不用 hgroup
2. 如果有连续多个 h1-h6 标签就用 hgroup
3. 如果有连续多个标题和其他文章数据，h1-h6 标签就用 hgroup 包住，和其他文章元数据一起放入 header 标签

### Header 元素

header 元素代表 网页 或 section 的页眉。

通常包含 h1-h6 元素或 hgroup

```html
<header>
  <hgroup>
    <h1>网站标题</h1>
    <h2>网站副标题</h2>
  </hgroup>
</header>
```

**注意:**

1. 可以是“网页”或任意“section”的头部部分
2. 没有个数限制。
3. 如果 hgroup 或 h1-h6 自己就能工作的很好，那就不要用 header。

### Nav 元素

nav 元素代表页面的导航链接区域。用于定义页面的主要导航部分。

```html
<nav>
  <ul>
    <li>HTML 5</li>
    <li>CSS3</li>
    <li>JavaScript</li>
  </ul>
</nav>
```

nav 使用注意: 用在整个页面主要导航部分上，不合适就不要用 nav 元素；

### Section 元素

section 元素代表文档中的 节 或 段，段可以是指一篇文章里按照主题的分段；节可以是指一个页面里的分组。

```html
<section>
  <h1>section是啥？</h1>
  <article>
    <h2>关于section</h1>
    <p>section的介绍</p>
    <section>
      <h3>关于其他</h3>
      <p>关于其他section的介绍</p>
    </section>
  </article>
</section>
```

**注意:**

1. section 不是一般意义上的容器元素，如果想作为样式展示和脚本的便利，可以用 div。
2. article、nav、aside 可以理解为特殊的 section，
3. 所以如果可以用 article、nav、aside 就不要用 section，没实际意义的就用 div

### Article 元素

article 元素最容易跟 section 和 div 容易混淆，其实 article 代表一个在文档，页面或者网站中自成一体的内容

```html
<article>
  <h1>一篇文章</h1>
  <p>文章内容..</p>
  <footer>
    <p><small>版权:html5jscss网所属，作者:damu</small></p>
  </footer>
</article>
```

**注意:**

1. 独立文章: 用 article
2. 单独的模块: 用 section
3. 没有语义的: 用 div

### Aside 元素

aside 元素被包含在 article 元素中作为主要内容的附属信息部分，其中的内容可以是与当前文章有关的相关资料、标签、名次解释等

在 article 元素之外使用作为页面或站点全局的附属信息部分。最典型的是侧边栏，其中的内容可以是日志串连，其他组的导航，甚至广告，这些内容相关的页面。

```html
<article>
  <p>内容</p>
  <aside>
    <h1>作者简介</h1>
    <p>小北，前端一枚</p>
  </aside>
</article>
```

**总结:**

1. aside 在 article 内表示主要内容的附属信息，
2. 在 article 之外则可做侧边栏
3. 如果是广告，其他日志链接或者其他分类导航也可以用

### Footer 元素

footer 元素代表 网页 或 section 的页脚，通常含有该节的一些基本信息，譬如: 作者，相关文档链接，版权资料。

```html
<footer>COPYRIGHT@damu</footer>
```

**注意:**

1. 可以是 网页 或任意 section 的底部部分；
2. 没有个数限制，除了包裹的内容不一样，其他跟 header 类似。

## 其他新增标签

### 状态标签:meter

用来显示已知范围的标量值或者分数值。

**属性**

1. value: 当前的数值。
2. min: 值域的最小边界值。如果设置了，它必须比最大值要小。如果没设置，默认为 0
3. max: 值域的上限边界值。如果设置了，它必须比最小值要大。如果没设置，默认为 1
4. low: 定义了低值区间的上限值,如果设置了，它必须比最小值属性大，并且不能超过 high 值和最大值。未设置或者比最小值还要小时，其值即为最小值。
5. high: 定义了高值区间的下限值。如果设置了，它必须小于最大值，同时必须大于 low 值和最小值。如果没有设置，或者比最大值还大，其值即为最大值。
6. optimum: 这个属性用来指示最优/最佳取值。

   ```html
   <meter value="60" min="20" max="100"></meter>
   <meter value="80" min="20" max="100" low="40" high="60"></meter>
   <meter value="30" min="20" max="100" low="40" high="60" optimum="80"></meter>
   ```

![1548060303729](/img/user/programming/front-end/primitive/html/html-basic/1548060303729.png)

- 最优值不是中间档的时候，会出现红色，绿色代表最优

### 状态标签:progress

用来显示一项任务的完成进度

**属性**

1. max: 该属性描述了这个 progress 元素所表示的任务一共需要完成多少工作.
2. value: 该属性用来指定该进度条已完成的工作量.如果没有 value 属性,则该进度条的进度为 " 不确定 ",也就是说,进度条不会显示任何进度,你无法估计当前的工作会在何时完成

   ```html
   <progress value="50" max="100"></progress> <progress max="100"></progress>
   ```

### 注释标签:ruby

rt: ruby 的子标签，展示文字注音或字符注释。

### 标记标签:mark

mark: 黄色高亮文字

## 标签属性 @@@

title 属性，这个属性可以给任何标签指定，当鼠标移入到元素上时，元素中的 title 属性的值将会作为提示文字显示

> alt 和 title 同时设置的时候，alt 作为图片的替代文字出现，title 是图片的解释文字

在表单中则是 name 属性

class 属性

id 属性，不能以数字开头 @@@

什么时候是 checked = checked，所有的 attribute 都是 x = x，所有的 attribute 都是写了就有嘛？

> 是的

### Style

```html
<div style="width:100px;height:100px;background-color:pink"></div>
```

内部的键值对都不需要再加双引号，不要使用 HTML 的标签样式，而是使用 style 属性

#### Src 和 Hef 的区别

href 是指向网络资源所在位置，建立和当前元素（锚点）或当前文档（链接）之间的连接，用于 **超链接**

src 是执行外部资源的位置，指向的内容会嵌入到文档中当前标签所在位置，在请求 src 资源时会将其指向的资源下载并应用到文档中。当浏览器解析到该元素时，会暂停其他资源的下载和处理，直到将该资源加载、编译、执行完毕

使用 href 的有：a 标签

## Meta 标签 @@@

### 概述

meta 是 html 文档在 head 标签里定义的一个对文档进行描述的功能性标签

meta 标签有下面的作用：

1. 搜索引擎优化（SEO）
2. 定义页面使用语言
3. 自动刷新并指向新的页面
4. 实现网页转换时的动态效果
5. 控制页面缓冲
6. 网页定级评价
7. 控制网页显示的窗口

**注意：**全局属性 `name` 在 <meta> 元素中具有特殊的语义；另外， 在同一个 <meta> 标签中，`name`, `http-equiv` 或者 `charset` 三者中任何一个属性存在时，`itemprop` 属性不能被使用。

### Charset 属性

这个属性声明了文档的字符编码。如果使用了这个属性，其值必须是与 ASCII 大小写无关（ASCII case-insensitive）的 "`utf-8`"。

### Name 属性

name 属性主要用于描述网页，与之对应的属性值为 content，content 中的内容主要是便于搜索引擎机器人查找信息和分类信息用的。

meta 标签的 name 属性语法格式是：

```html
<meta name="参数" content="具体的参数值" />
```

#### Keywords(关键字)

- keywords 用来告诉搜索引擎你网页的关键字是什么。

#### description(网站内容描述)

#### obots(机器人向导)

- robots 用来告诉搜索机器人哪些页面需要索引，哪些页面不需要索引。
- content 的参数有 all,none,index,noindex,follow,nofollow。默认是 all。

  ```html
  <meta name="robots" content="none" />
  ```

#### author(作者)

#### renderer(渲染)

- 告诉浏览器你的渲染模式

  ```html
  <meta name="renderer" content="webkit" />
  ```

#### viewport(视图模式）

- 详见 H5 移动端

  ```html
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  ```

### Http-equiv 属性

http-equiv 顾名思义，相当于 http 的文件头作用，它可以向浏览器传回一些有用的信息，以帮助正确和精确地显示网页内容，与之对应的属性值为 content，content 中的内容其实就是各个参数的变量值。

meta 标签的 http-equiv 属性语法格式是：

```html
<meta http-equiv="参数" content="参数变量值" />
```

最好还是不要指定 HTML 标签，通过可能会出现混乱（到底以那端为主，实际 response header 的优先级更高）。此外，在 HTML5 中，这些<meta HTTP 等>标签是无效的。只有 HTML5 规范中列出的 HTTP 等效值才被允许。

#### Window-target(显示窗口的设定)

- 强制页面在当前窗口以独立页面显示。

  ```
  <meta http-equiv="Window-target" content="_blank">
  ```

#### 请求的重定向

- `<meta http-equiv=”refresh” content=”5;url=http://www.baidu.com”>`
- http-equiv，http 协议的内容，值是固定的
- 5 表示多少秒之后跳转

# HTML5

HTML5 是定义 HTML 标准的最新的版本。 该术语表示两个不同的概念:

- 它是一个新版本的 HTML 语言，具有新的元素，属性和行为，
- 它有更大的技术集，允许更多样化和强大的网站和应用程序。
- 这个集合有时称为 HTML5 和朋友，通常缩写为 HTML5。
- HTML5 约等于 HTML + CSS + JS

## Html5 优势

跨平台: 唯一一个通吃 PC MAC Iphone Android 等主流平台的跨平台语言

快速迭代

降低成本

导流入口多

分发效率高

## 根元素

### H4 中的根标签

` <html xmlns="http://www.w3.org/1999/xhtml">`

首先这个标记没有任何问题，你喜欢的话,那就背下来继续用。它是有效的。但这个标记中的很多字节在 Html5 中我们都可以省略了

xmlns: 这是 XHTML1.0 的东西，它的意思是在这个页面上的元素都位于 http://www.w3.org/1999/xhtml 这个命名空间内

但是 HTML5 中的每个元素都具有这个命名空间，不需要在页面上再显示指出

### H5 中的根标签

` <html></html>`

## Head 标签

### MIME 类型:

每当浏览器请求一个页面时，web 服务器会在发送实际页面内容之前，先发送一些头信息。

浏览器需要这些信息来决定如何解析随后的页面内容。最重要的是 Content-Type

比如:

- `Content-Type:text/html`，text/html: 即这个页面的 " 内容类型 ",或者称为 **MIME 类型**。这个头信息将唯一确定某个资源的本质是什么也决定了它应该如何被呈现。
- 图片也有自己的 MIME 类型
  - **jpg:image/jpeg**
  - **png:image/png**
- js 也有自己的 MIME 类型，css 也有自己的 MIME 类型，
- 任何资源都有自己的 MIME 类型，整个 web 都依靠 MIME 类型来运作

### HTTP 协议整合 MIME 类型

浏览器通常使用 MIME 类型（而不是文件扩展名）来确定如何处理文档；因此服务器设置正确以将正确的 MIME 类型附加到响应对象的头部是非常重要的。

**语法：**type/subtype

MIME 的组成结构非常简单；由类型与子类型两个字符串中间用 `'/'` 分隔而组成。不允许空格存在。_type_ 表示可以被分多个子类的独立类别。subtype 表示细分后的每个类型。

MIME 类型对大小写不敏感，但是传统写法都是小写。

| 类型          | 描述                                                                    | 典型示例                                                                                                                                        |
| ------------- | ----------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `text`        | 表明文件是普通文本，理论上是人类可读                                    | `text/plain`, `text/html`, `text/css, text/javascript`                                                                                          |
| `image`       | 表明是某种图像。不包括视频，但是动态图（比如动态 gif）也使用 image 类型 | `image/gif`, `image/png`, `image/jpeg`, `image/bmp`, `image/webp`, `image/x-icon`, `image/vnd.microsoft.icon`                                   |
| `audio`       | 表明是某种音频文件                                                      | `audio/midi`, `audio/mpeg, audio/webm, audio/ogg, audio/wav`                                                                                    |
| `video`       | 表明是某种视频文件                                                      | `video/webm`, `video/ogg`                                                                                                                       |
| `application` | 表明是某种二进制数据                                                    | `application/octet-stream`, `application/pkcs12`, `application/vnd.mspowerpoint`, `application/xhtml+xml`, `application/xml`, `application/pdf` |

对于 text 文件类型若没有特定的 subtype，就使用 `text/plain`。类似的，二进制文件没有特定或已知的 subtype，即使用 `application/octet-stream`。

![1547468425704](/img/user/programming/front-end/primitive/html/html-basic/1547468425704.png)

![1547468438453](/img/user/programming/front-end/primitive/html/html-basic/1547468438453.png)

![1547468454926](/img/user/programming/front-end/primitive/html/html-basic/1547468454926.png)

# 表格 @@@

## Tabel 标签

在 HTML 中，使用 table 标签来创建一个表格

## Caption 标签

caption 元素定义表格标题。

caption 标签必须紧随 table 标签之后。您只能对每个表格定义一个标题。通常这个标题会被居中于表格之上。

## Tr 标签

在 table 标签中使用 tr 来表示表格中的一行，有几行就有几个 tr

> row

## Td 标签

在 tr 中需要使用 td 来创建一个单元格，有几个单元格就有几个 td

> dan yuan ge

## Th 标签

可以使用 th 标签来表示表头中的内容，它的用法和 td 一样，不同的是它会有一些默认效果

> head

## Rowspan 属性

rowspan 属性用来设置 **纵向** 的合并单元格

## Colspan 属性

colspan 属性 **横向** 的合并单元格

```html
<tr>
  <td>企画</td>
  <td colspan="4">サンライズ</td>
</tr>
```

## Border-spacing 属性

table 和 td 边框之间默认有一个距离通过 border-spacing 属性可以设置这个距离

![1548681866982](/img/user/programming/front-end/primitive/html/html-basic/1548681866982.png)

## Border-collapse 属性

border-collapse 可以用来设置表格的边框合并

border-collapse:collapse

如果设置了 **边框合并**，则 border-spacing 自动失效

```css
table {
  border-collapse: collapse;
}
```

## 表格语义

有一些情况下表格是非常的长的， 这时就需要将表格分为三个部分，表头，表格的主体，表格底部

**在 HTML 中为我们提供了三个标签：**

- thead 表头，thead 中的内容，永远会显示在表格的头部
- tbody 表格主体，tbody 中的内容，永远都会显示表格的中间
- tfoot 表格底部，tfoot 中的内容，永远都会显示表格的底部
- 这三个标签的作用，就来区分表格的不同的部分，他们都是 table 的子标签，都需要直接写到 table 中，tr 需要写在这些标签当中
- 如果表格中没有写 tbody，浏览器会自动在表格中添加 tbody，并且将所有的 tr 都放到 tbody 中，所以 **注意**tr 并不是 table 的子元素，而是 tbody 的子元素
- 通过 table > tr 无法选中行 需要通过 tbody > tr

# 内容模型

新的嵌套规则

参考: http://chenhaizhou.github.io/2016/01/19/html-element-class.html
