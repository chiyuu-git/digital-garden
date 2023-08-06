---
{"dg-publish":true,"permalink":"/programming/font-end/framework/engineering/eslint/eslint-basic/"}
---


# Global Experience Collection

希望可以通过 dataview 查询所有的经验 section 自动汇总到这里

+ 首先想到的方案就是给 section 打上标签，然后 query 标签即可，再动态拼接 section 即可
+ 但是 ob 的 tag 是 page 维度的, 需要再研究其他方法

| File                                                              | overview                   |
| ----------------------------------------------------------------- | -------------------------- |
| [[programming/font-end/primitive/es/es-array\|es-array]]       | [[programming/font-end/primitive/es/es-array#experience\|es-array#experience]]    |
| [[programming/font-end/primitive/es/es-regexp\|es-regexp]]     | [[programming/font-end/primitive/es/es-regexp#experience\|es-regexp#experience]]   |
| [[programming/font-end/primitive/es/es-number\|es-number]]     | [[programming/font-end/primitive/es/es-number#experience\|es-number#experience]]   |
| [[programming/font-end/primitive/es/es-function\|es-function]] | [[programming/font-end/primitive/es/es-function#experience\|es-function#experience]] |

{ .block-language-dataview}

# 编程经验

为什么编程实践要放在 esilnt 下？因为这些实践不应该死记硬背，应该直接通过 linter 固化

抽取重复的是为了后期的维护，应该先使得功能实现了，再抽取相同的部分封装，直接就写封装然后分别运用，境界很高的

> 就像做数学，一开始每次都把公式抄一遍，要做的很熟练，才可以跳过公式

但是上线的代码，还是要做好设计和抽离，不然维护起来成本太大了

了解一个概念的方法：是什么，有哪些作用，实际中怎么使用，为什么会这么设计

## 哲学

### 加不加分号

在下面 2 种情况下不加分号会有问题

1. 小括号开头的前一条语句

    ```js
    var a = 3
    ;(function () {
    
    })()
    //解释器理解(错误理解)：
    var a = 3(function () {
    })();
    //把3看成了一个函数，去调用后面的function
    //TypeError:3 is not a function
    ```

2. 中方括号开头的前一条语句

    ```js
    var b = 4
    ;[1, 3].forEach(function () {
    })
    //解释器理解(错误理解)：
    var b = 4[3].forEach(function () {
    })
    //把4[3]看成了一个变量，去调用后面的forEach方法
    //TypeError:4[3] is undefined
    ```

解决办法: 在行首加分号

强有力的例子: vue.js 库

知乎热议: https://www.zhihu.com/question/20298345

### 空格 Vs Tab

使用空格还是 tab 的这个问题，如同程序员之间的『语言之争』，『vim/emacs 编辑器之争』一样是个永远的圣战，这个争论不会有结果，你怎么选择都有自己的道理，只是看你选择认同谁而已。

就我而言，我提倡尽可能用空格（除了少数必须用 tab 的情形以外）。理由如下：

1. 空格在各种情况下代码都是你想要的样子。而 tab 仅仅当你与代码作者的 tab 尺寸设定为相同时，代码才好看。 修改 tab 尺寸并不能解决这个问题，因为你很难做到每打开一个文件就修改一次 tab 尺寸，而每个人通常有不同的习惯（POSIX/Unix 标准的 tab 应当为 8 字符宽度，Linus 大神也规定 Linux 内核中所有代码的 tab 尺寸为 8）。如果存在行尾注释，则 tab 尺寸更加是必须设定为与作者相同，这就意味着你看不同的代码需要经常修改 tab 尺寸。我看过许多代码，其使用的 tab 尺寸有从 2,3,4,5,6,8,16 甚至 32 的，如果你使用的 tab 尺寸与作者不同，外观将很不理想。
2. 靠谱的编辑器都能解决前进后退增加减少缩进的问题，即便是四个空格，一个退格键也能全退了，所以在使用的方便性方面根本不存在问题。——如果抱怨删除调整还不能有效解决的，你需要研究一下你的编辑器了。实际上增加减少缩进在主流编辑器中都直接有快捷键，无论是 tab 还是空格还是退格都很少直接被用于缩进。
3. tab 是制表符而不是缩进符，正如在 html 页面中大量使用 `<table>` 进行布局是个不好的编程习惯一样，在编程中大量使用制表符布局通常也不是个好习惯。
4. 如果代码需要压缩发布，使用空格的代码通常具有更好的压缩率。各位不信的可以使用批处理工具把代码用全空格或者全 tab 走一遍。——这里面的原理是信息量，使用 tab 缩进的代码中，仍然不可避免的含有空格（运算符之间的间隔，注释等等），但使用空格的代码中根本不含有 tab，这使得 tab 缩进代码虽然不压缩的时候更小，但熵更高，因而压缩率较差，压缩之后反而更大。——当然，压缩发布代码仅仅对开源软件有意义，商业软件可以无视。

## 回调函数的简写形式

```js
PubSub.subscribe("deleComment",(msg,index)=>{
  this.deleteComment(index)
})

deleteComment(index){}

//可以简写，因为第二个参数接受一个回调函数，这个回调函数有两个参数，第一个是msg，第二个是index，
//如果deleteComment也设计成这样（msg，index），其实就是和上面那种麻烦的形式是一样的
PubSub.subscribe("deleComment",deleteComment)

deleteComment(msg,index){}
```

## 如何避免循环依赖

一个模块依赖另一个模块，这两个模块互相依赖

## If Else 简化

把不同的 if else clause 封装成不同的状态，通过 [状态机](../../../../basic/cs-basic/design-pattern.md#状态机) 来解决

### 复杂判断

[JavaScript 复杂判断的更优雅写法 - 知乎](https://zhuanlan.zhihu.com/p/52110677)

## 代码短路逻辑

一种情况是直接 throw error，会停止 js 线程的执行，后续的逻辑没有必要每次都判断是否存在了。但是问题在于代码复用之后经常是无法短路的

第二种情况就是短路返回默认值，让默认值和代码逻辑的返回值保持类型一致即可。但是后续的代码都需要检查默认值，比如空数组、空对象

如果一个返回值不可能为 null 或者 undefined，那么抛出一个 error 会是一个不错的选择。此时 ts 不会认为可能存在边界值如 null undefined，因为此时会短路 throw。这样后续的逻辑就不需要针对 null 做判断了

## 凡是监听，一定要取消监听

逻辑层因为是所有通用的，如果不取消监听的话，很容易内存泄漏

视图层是每个 page 单独，整个 context 都一起销毁了，所以没那么容易出事。

## 命名基准

只有数组使用复数，或者是 xxList，其他所有变量都是单数

如果是对象就用 xxMap

对大小写敏感的使用驼峰，不敏感的使用下划线，什么时候用短横线？无关的词组合形成一个全新的词的时候

- userName，表示 user 的 name
- react-redux ，表示一个组合成新的名词，双击不可以全选
- react_redux，标识一个单词，双击可以全选

expandHeightWithContent 其实就是为了解决高度塌陷的问题，欣姐说不要用这个函数解决的了什么问题来命名，而是应该描述这个函数做了什么，其实很有道理

## 性能技巧

访问变量远远快于访问属性

因为性能问题，减少 gc，这次从 foreach 改为 for 循环

```ts
Object.keys(methodList).forEach 

for keys of Object.keys(methodList)

arr.forEach((val,i) => {})
for i len 
```

相较于 foreach，少了一个匿名函数，foreach 在某些函数可以并行执行，而 for 系列无法做这种优化

### 数组

虽说 js 的数据不需要指定长度，但是数组固定长度性能还是有优化的，如果可以指定还是指定一下吧

### 避免使用 Delete

[为什么说在 JS 中要避免使用 delete - 掘金](https://juejin.cn/post/6844903918804172814)

[[JS]为什么不建议用delete删除对象属性 - 掘金](https://juejin.cn/post/7048070062980202503)

可以赋值为 undefined, 或者重新创建赋值一个有值字段对象

### 循环和递归

时间效率上，看具体语言实现了，有尾递归优化的几乎一样。没有 [尾递归优化](https://www.zhihu.com/search?q=%E5%B0%BE%E9%80%92%E5%BD%92%E4%BC%98%E5%8C%96&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%22128969831%22%7D) 就要看调用和返回的时间成本大小，但也慢不多，主要是有额外的空间成本。

## 约定优于配置

约定优于配置（convention over configuration），也称作按约定编程，是一种软件设计范式，旨在减少软件开发人员需做决定的数量，获得简单的好处，而又不失灵活性。

> 约定优于配置也被称为习惯优于配置、约定大于配置

本质来说，系统、类库或框架应该假定合理的默认值，而非要求提供不必要的配置。比如说模型中有一个名为 User 的类，那么数据库中对应的表就会默认命名为 user。只有在偏离这一个约定的时候，例如想要将该表命名为 system_user，才需要写有关这个名字的配置。

简单来说，如果你所用工具的约定和你的期待相符，就可以省去配置；不符合的话，你就要通过相关的配置来达到你所期待的结果。

约定优于配置意味着通用化，标准化，意味着开发者都需要遵循同一套约定。这样，当一个开发者要看另一个开发者写的程序的时候，就会很容易上手，因为他了解同一套约定，减少了重新学习的成本。



# HTML

## 命名

**[强制] `class` 必须单词全字母小写，单词间以 `-` 分隔。**

**[强制] `class` 必须代表相应模块或部件的内容或功能，不得以样式信息进行命名。**

```html
<!-- good -->
<div class="sidebar"></div>

<!-- bad -->
<div class="left"></div>
```

**[强制] 元素 `id` 必须保证页面唯一。**

同一个页面中，不同的元素包含相同的 `id`，不符合 `id` 的属性含义。并且使用 `document.getElementById` 时可能导致难以追查的问题。

**[建议] `id` 建议单词全字母小写，单词间以 `-` 分隔。同项目必须保持风格一致。**

**[建议] `id`、`class` 命名，在避免冲突并描述清楚的前提下尽可能短。**

```html
<!-- good -->
<div id="nav"></div>
<!-- bad -->
<div id="navigation"></div>

<!-- good -->
<p class="comment"></p>
<!-- bad -->
<p class="com"></p>

<!-- good -->
<span class="author"></span>
<!-- bad -->
<span class="red"></span>
```

**[强制] 禁止为了 `hook 脚本`，创建无样式信息的 `class`。**

不允许 `class` 只用于让 JavaScript 选择某些元素，`class` 应该具有明确的语义和样式。否则容易导致 CSS class 泛滥。

使用 `id`、属性选择作为 hook 是更好的方式。

**[强制] 同一页面，应避免使用相同的 `name` 与 `id`。**

IE 浏览器会混淆元素的 `id` 和 `name` 属性， `document.getElementById` 可能获得不期望的元素。所以在对元素的 `id` 与 `name` 属性的命名需要非常小心。

一个比较好的实践是，为 `id` 和 `name` 使用不同的命名法。

```html
<input name="foo">
<div id="foo"></div>
<script>
// IE6 将显示 INPUT
alert(document.getElementById('foo').tagName);
</script>
```

**[强制] 标签名必须使用小写字母。**

```js
<!-- good -->
<p>Hello StyleGuide!</p>

<!-- bad -->
<P>Hello StyleGuide!</P>
```

**[强制] 属性名必须使用小写字母。**

```html
<!-- good -->
<table cellspacing="0">...</table>

<!-- bad -->
<table cellSpacing="0">...</table>
```

**[强制] 属性值必须用双引号包围。**

```html
<!-- good -->
<script src="esl.js"></script>

<!-- bad -->
<script src='esl.js'></script>
<script src=esl.js></script>
```

## 标签

**[强制] 标签使用必须符合标签嵌套规则。**

比如 `div` 不得置于 `p` 中，`tbody` 必须置于 `table` 中。

详细的标签嵌套规则参见 [HTML DTD](http://www.cs.tut.fi/~jkorpela/html5.dtd) 中的 `Elements` 定义部分。

> 有几个特殊的块级元素只能包含内嵌元素，不能再包含块级元素 h1、h2、h3、h4、h5、h6、p、dt
>
> 块级元素不能放在标签 p 里面
>
> li 标签可以包含 div 标签，因为 li 和 div 标签都是装载内容的容器
>
> <https://www.zhihu.com/question/34952563/answer/60672228>

**[建议] 布尔类型的属性，建议不添加属性值。**

```html
<input type="text" disabled>
<input type="checkbox" value="1" checked>
```

**[建议] 自定义属性建议以 `xxx-` 为前缀，推荐使用 `data-`。**

使用前缀有助于区分自定义属性和标准定义的属性。

```html
<ol data-ui-type="Select"></ol>
```

## 通用

### DOCTYPE

**[强制] 使用 `HTML5` 的 `doctype` 来启用标准模式，建议使用大写的 `DOCTYPE`。**

```
<!DOCTYPE html>
```

**[建议] 启用 IE Edge 模式。**

```
<meta http-equiv="X-UA-Compatible" content="IE=Edge">
```

[建议] 在 `html` 标签上设置正确的 `lang` 属性。

有助于提高页面的可访问性，如：让语音合成工具确定其所应该采用的发音，令翻译工具确定其翻译语言等。

```
<html lang="zh-CN">
```

### 编码

**[强制] 页面必须使用精简形式，明确指定字符编码。指定字符编码的 `meta` 必须是 `head` 的第一个直接子元素。**

见 [HTML5 Charset能用吗](http://www.qianduan.net/html5-charset-can-it.html) 一文。

```html
<html>
    <head>
        <meta charset="UTF-8">
        ......
    </head>
    <body>
        ......
    </body>
</html>
```

### CSS 和 JavaScript 引入

与优化一致

## Head

**[强制] 保证 `favicon` 可访问。**

在未指定 favicon 时，大多数浏览器会请求 Web Server 根目录下的 `favicon.ico` 。为了保证 favicon 可访问，避免 404，必须遵循以下两种方法之一：

1. 在 Web Server 根目录放置 `favicon.ico` 文件。
2. 使用 `link` 指定 favicon。

```html
<link rel="shortcut icon" href="path/to/favicon.ico">
```

**[建议] 有下载需求的图片采用 `img` 标签实现，无下载需求的图片采用 CSS 背景图实现。**

解释：

1. 产品 logo、用户头像、用户产生的图片等有潜在下载需求的图片，以 `img` 形式实现，能方便用户下载。
2. 无下载需求的图片，比如：icon、背景、代码使用的图片等，尽可能采用 CSS 背景图实现。

## 表单

**[建议] 尽量不要使用按钮类元素的 `name` 属性。**

由于浏览器兼容性问题，使用按钮的 `name` 属性会带来许多难以发现的问题。具体情况可参考 [此文](http://w3help.org/zh-cn/causes/CM2001)。

**[建议] 当使用 JavaScript 进行表单提交时，如果条件允许，应使原生提交功能正常工作。**

当浏览器 JS 运行错误或关闭 JS 时，提交功能将无法工作。如果正确指定了 `form` 元素的 `action` 属性和表单控件的 `name` 属性时，提交仍可继续进行。

```html
<form action="/login" method="post">
    <p><input name="username" type="text" placeholder="用户名"></p>
    <p><input name="password" type="password" placeholder="密码"></p>
</form>
```

**[建议] 在针对移动设备开发的页面时，根据内容类型指定输入框的 `type` 属性。**

根据内容类型指定输入框类型，能获得能友好的输入体验。

```php+HTML
<input type="date">
```

## 多媒体

## 模板中的 HTML

## DOM 数

根据 Google 的说法，最佳 DOM 树是：

- 少于 1500 个节点
- 深度少于 32 级
- 父节点拥有少于 60 个子节点

# CSS

## 命名

css 类名的第一个字符最好不要使用数字。它无法在 Mozilla Firefox 中起作用

less 变量不可以以数字开头，不支持特殊字符

## 空格

自动化处理

## 选择器

**[强制] 当一个 rule 包含多个 selector 时，每个选择器声明必须独占一行。**

```css
/* good */
.post,
.page,
.comment {
    line-height: 1.5;
}

/* bad */
.post, .page, .comment {
    line-height: 1.5;
}
```

**[强制] `>`、`+`、`~` 选择器的两边各保留一个空格。**

**[强制] 如无必要，不得为 `id`、`class` 选择器添加类型选择器进行限定。**

在性能和维护性上，都有一定的影响。

```js
/* good */
#error,
.danger-message {
    font-color: #c00;
}

/* bad */
dialog#error,
p.danger-message {
    font-color: #c00;
}
```

> <https://segmentfault.com/q/1010000003749652>

## 属性

**[建议] 在可以使用缩写的情况下，尽量使用属性缩写。**

```js
/* good */
.post {
    font: 12px/1.5 arial, sans-serif;
}

/* bad */
.post {
    font-family: arial, sans-serif;
    font-size: 12px;
    line-height: 1.5;
}
```

**[建议] 使用 `border` / `margin` / `padding` 等缩写时，应注意隐含值对实际数值的影响，确实需要设置多个方向的值时才使用缩写。**

`border` / `margin` / `padding` 等缩写会同时设置多个属性的值，容易覆盖不需要覆盖的设定。如某些方向需要继承其他声明的值，则应该分开设置。

## 属性顺序

[建议] 同一 rule set 下的属性在书写时，应按功能进行分组，并以 **Formatting Model（布局方式、位置） > Box Model（尺寸） > Typographic（文本相关） > Visual（视觉效果）** 的顺序书写，以提高代码的可读性。

解释：

- Formatting Model 相关属性包括：`position` / `top` / `right` / `bottom` / `left` / `float` / `display` / `overflow` 等
- Box Model 相关属性包括：`border` / `margin` / `padding` / `width` / `height` 等
- Typographic 相关属性包括：`font` / `line-height` / `text-align` / `word-wrap` 等
- Visual 相关属性包括：`background` / `color` / `transition` / `list-style` 等

另外，如果包含 `content` 属性，应放在最前面。

```css
.sidebar {
    /* formatting model: positioning schemes / offsets / z-indexes / display / ...  */
    position: absolute;
    top: 50px;
    left: 0;
    overflow-x: hidden;

    /* box model: sizes / margins / paddings / borders / ...  */
    width: 200px;
    padding: 5px;
    border: 1px solid #ddd;

    /* typographic: font / aligns / text styles / ... */
    font-size: 14px;
    line-height: 20px;

    /* visual: colors / shadows / gradients / ... */
    background: #f5f5f5;
    color: #333;
    -webkit-transition: color 1s;
       -moz-transition: color 1s;
            transition: color 1s;
}
```

## 清除浮动

## Z-index

**[建议] 将 `z-index` 进行分层，对文档流外绝对定位元素的视觉层级关系进行管理。**

同层的多个元素，如多个由用户输入触发的 Dialog，在该层级内使用相同的 `z-index` 或递增 `z-index`。

建议每层包含 100 个 `z-index` 来容纳足够的元素，如果每层元素较多，可以调整这个数值。

**[建议] 在可控环境下，期望显示在最上层的元素，`z-index` 指定为 `999999`。**

可控环境分成两种，一种是自身产品线环境；还有一种是可能会被其他产品线引用，但是不会被外部第三方的产品引用。

不建议取值为 `2147483647`。以便于自身产品线被其他产品线引用时，当遇到层级覆盖冲突的情况，留出向上调整的空间。

**[建议] 在第三方环境下，期望显示在最上层的元素，通过标签内联和 `!important`，将 `z-index` 指定为 `2147483647`。**

第三方环境对于开发者来说完全不可控。在第三方环境下的元素，为了保证元素不被其页面其他样式定义覆盖，需要采用此做法。

## 值与单位

### 数值

**[强制] 当数值为 0 - 1 之间的小数时，省略整数部分的 `0`。省略可省的单位（长度单位如 `px`、`em`，不包括时间、角度等如 `s`、`deg`）。**

```css
/* good */
panel {
    opacity: .8;
}

/* bad */
panel {
    opacity: 0.8;
}
```

### Url

**[强制] `url()` 函数中的路径不加引号。**

```css
body {
    background: url(bg.png);
}
```

**[建议] `url()` 函数中的绝对路径可省去协议名。**

```css
body {
    background: url(//baidu.com/img/bg.png) no-repeat 0 0;
}
```

### 长度

**[强制] 长度为 `0` 时须省略单位。 (也只有长度单位可省)** @@@

```css
/* good */
body {
    padding: 0 5px;
}

/* bad */
body {
    padding: 0px 5px;
}
```

### 颜色

**[强制] RGB 颜色值必须使用十六进制记号形式 `#rrggbb`。不允许使用 `rgb()`。**

带有 alpha 的颜色信息可以使用 `rgba()`。使用 `rgba()` 时每个逗号后必须保留一个空格。

**[强制] 颜色值可以缩写时，必须使用缩写形式。**

**[强制] 颜色值不允许使用命名色值。**

### 2D 位置

**[强制] 必须同时给出水平和垂直方向的位置。**

2D 位置初始值为 `0% 0%`，但在只有一个方向的值时，另一个方向的值会被解析为 center。为避免理解上的困扰，应同时给出两个方向的值。[background-position属性值的定义](http://www.w3.org/TR/CSS21/colors.html#propdef-background-position)

```css
/* good */
body {
    background-position: center top; /* 50% 0% */
}

/* bad */
body {
    background-position: top; /* 50% 0% */
}
```

## 文本编排

### 字体族

# Typescript

## 接口名是否应该以 `I` 开头

接口一般首字母大写。[有的编程语言中会建议接口的名称加上 `I` 前缀](https://msdn.microsoft.com/en-us/library/8bc1fexb(v=vs.71).aspx)。

统一使用 Pascal

## 实践总结

原始数据类型，定义成具体的字面量或许会更合适

## 值与类型同名

很常见的写法，值小写驼峰，类型大写驼峰

## 隐藏的 Any

未赋值的变量会被推断成 any，要注意避免

## 未使用的变量

为什么有些定义了的变量（比如使用 `enum` 定义的变量）未使用，ESLint 却没有报错？

因为无法支持这种变量定义的检查。建议在 `tsconfig.json` 中添加以下配置，使 `tsc` 编译过程能够检查出定义了未使用的变量：

```json
{
    "compilerOptions": {
        "noUnusedLocals": true,
        "noUnusedParameters": true
    }
}
```

启用了 noUnusedParameters 之后，只使用了第二个参数，但是又必须传入第一个参数，这就会报错了

第一个参数以下划线开头即可，参考 <https://github.com/Microsoft/TypeScript/issues/9458>

## --strictNullChecks

顺便说一句，如果将 `--strictNullChecks` 标志添加到 `tsconfig文件`，TypeScript 将应用非空性规则。只有被声明 null 后才能被赋值：

```ts
// 关闭 --strictNullChecks
let s: string = "foo";
s = null; // 正常

// 开启 --strictNullChecks
s = null; // Error: Type 'null' is not assignable to type 'string'. 
```

## 空对象

使用 `Record<string, unknown>` 代替空对象

还是有一点区别的：联合类型的时候空对象会直接消失，但是 `Record<string, unknown>` 不会

## 只有 null，没有 Undefined

# 后端

mvc 模式的命名和意义

1. 是否使用复数取决于上下文即可，不要纠结了
2. 我突然决定一律使用单数，哪个脑残再说复数的？
3. <https://qastack.cn/programming/338156/table-naming-dilemma-singular-vs-plural-names>
4. 复数仅用于变量名，表示是一个数组，其他场合一律不适用复数，因为复数表示数组比 xxxArray 简单多了

数据库的命名

字段的命名风格，驼峰？

字段是否需要带上表明？ 比如 user 下的 id 字段，是直接 userid ？ 还是直接 id？

表名、字段名必须使用小写字母或数字，禁止出现数字开头

表名用单数

字段名单数还是复数？字段名不再重复表名，比如 project 表里的 name 即可。主键需要重复表名，因为主键会出现在其他表中

大小写不敏感

长查询语句，关键字单独一行，查询列表一个一行，通过快捷键对齐

<https://www.cnblogs.com/daihang2366/p/13560596.html>

<https://cloud.tencent.com/developer/article/1054482>

# Restful Api

## URL 命名

### 形式

URL 命名通常有三种：

- 驼峰命名法 (serverAddress)，由于 URL 是大小写敏感的，如果用驼峰命名在输入的时候就要求区分大小写，一个是增加输入难度，另外也容易输错，报 404。
- 蛇形命名法 (server_address)，在输入的时候需要切换 shfit，同时下划线容易被文本编辑器的下划线掩盖，支付宝用的是蛇形命名法
- 脊柱命名法 (server-address)，stackoverflow.com 和 github.com 用的是脊柱命名法（例如<https://help.github.com/articles/why-are-my-commits-linked-to-the-wrong-user/#commits-are-not-linked-to-any-user 和 (https://stackoverflow.com/questions/5262224/how-are-reddit-and-hacker-news-ranking-algorithms-used>）

在 RESTful 接口中，对于 URI 一个普遍接受的规则是：全部小写，用中划线连接

之所以不混用大小写字母，是因为早期的 URI 一般都是表示服务器上的文件路径，而不同服务器对大小写的敏感性是不同的，为了兼容不同服务器所以才规定不能混用大小写字母，然后用中划线或下划线连接多个单词

我们如今开发的 api，URI 一般不再表示服务器上的文件了，而是一种程序使用的路由，而各类开发程序对字符串的判断也都是区分大小写的，所以就算 URI 混用了大小写一般也不会有问题。但是为了与那些仍旧表示服务器文件路径的 URI 保持一致，更多的人仍旧愿意沿用全部使用小写字母的规则。至于连接多个单词使用中划线还是下划线，我只能说，中划线使用得更加广泛

### 语义

Server 提供的 RESTful API 中，URL 中只使用名词来指定资源，原则上不使用动词。为什么呢？因为 REST 对于信息的核心抽象是资源，而表示资源的词语天然的就是名词，所以这不是什么技术问题，而是常识

单数复数 大部分人认为应该统一使用复数

### 复杂条件查询

- 对于 collection 的所有查询 `Action Name`，都需要以 list 开头。
- 查询的条件中，如果条件为一到两个，使用 `By` 和 `And`。eg.: listByUserIdAndName
- 如果查询条件大于 3 个，则使用 `ByOptions`，查询条件作为请求体传入。eg: `listByOptions`

# SQL

<https://choerodon.io/zh/docs/contributor-guide/development/conventions/sql/>

# Eslint

文档：<http://eslint.cn/docs/user-guide/configuring>

## 基本规则

### 使用

ESLint 可以安装在当前项目中或全局环境下，因为代码检查是项目的重要组成部分，所以我们一般会将它安装在当前项目中。可以运行下面的脚本来安装：

```bash
npm install --save-dev eslint
```

ESLint 需要一个配置文件来决定对哪些规则进行检查，配置文件的名称一般是 `.eslintrc.js` 或 `.eslintrc.json`。

`--quiet` 这个选项允许你禁止报告警告。如果开启这个选项，ESLint 只会报告 error 级别错误。

### 规则的错误等级有三种

```js
rules: {
    '@typescript-eslint/consistent-type-definitions': [
        "error",
        "interface"
      ],
}
```

规则的取值一般是一个数组（上例中的 `@typescript-eslint/consistent-type-definitions`），其中第一项是 `off`、`warn` 或 `error` 中的一个，表示关闭、警告和报错。后面的项都是该规则的其他配置。

如果没有其他配置的话，则可以将规则的取值简写为数组中的第一项（上例中的 `no-var`）。

关闭、警告和报错的含义如下：

- 关闭：禁用此规则
- 警告：代码检查时输出错误信息，但是不会影响到 exit code
- 报错：发现错误时，不仅会输出错误信息，而且 exit code 将被设为 1（一般 exit code 不为 0 则表示执行出现错误）
- 0：关闭规则。
- 1：打开规则，并且作为一个警告（信息打印黄色字体）
- 2：打开规则，并且作为一个错误（信息打印红色字体）

### Eslint 注释

文件级注释

```js
/* eslint-disable fecs-camelcase */
```

一个 disable，一个 enable 两个一起可以当作块级注释

行级注释

```js
// eslint-disable-next-line no-unused-vars
```

### 获取单个文件的全部 Eslint 配置

````
npx eslint --print-config xxx.ts > all-eslint-confing.json
````

使用 npx 智能查找 eslint 命令

设置成 json 文件可以快速的查找出重复的 rule？不行，eslint 会输出最终使用的规则，只能看下默认的配置有没有这项规则，没有的话再加上，或者重写这样

## 开启 ESLint 检查

检查分为两种：一种是编辑器智能提示，一种是主动运行 ESLint 命令进行检查

### 检查整个项目的 Ts 文件

我们的项目源文件一般是放在 `src` 目录下，所以需要将 `package.json` 中的 `eslint` 脚本改为对一个目录进行检查。由于 `eslint` 默认不会检查 `.ts` 后缀的文件，所以需要加上参数 `--ext .ts`：

```json
{
    "scripts": {
        "eslint": "eslint src --ext .ts"
    }
}
```

此时执行 `npm run eslint` 即会检查 `src` 目录下的所有 `.ts` 后缀的文件。

# Markdownlint

## Custom Rule

按理说，这些都会提供一个 cli 脚手架的，克隆下来之后直接 run 就可以开始 debugger 基础示例了

我可以先随便新建一个项目，然后依赖 一个第三方的自定义 rule，一个 markdownlint-cli ，这样就可以在这个项目内通过命令行调试代码

必备的环境基础：

1. 测试用的 markdown 文件
2. 一个 markdownlint 的环境，让我可以通过命令行执行 lint 检查

其实只要这两点就够了，那是不是 vscode 的环境也行呢？ 我直接复制一个 js 文件作为 custom rule 每次保存就可以跑起来了。不太行，没有起环境打 log，还是新建一个项目好了

参考 rule：

MD022

MD032/blanks-around-lists: Lists should be surrounded by blank lines

# 依赖分析

依赖分析工具 madge：https://juejin.cn/post/6844904047024177159

https://www.jianshu.com/p/38694618b2e6

```
madge -i app.svg app.js
```

看 app.js 文件的依赖关系

依赖分析工具 dependency-cruiser https://juejin.cn/post/7096650413209813029

```
npx depcruise --output-type dot --max-depth 1 ./logic | dot -T svg > app.svg
```

```
npx depcruise --output-type dot --max-depth 1 logger | dot -T svg > app.sv
```

看 src 目录下的依赖关系

需要设置 ts config、webpack config 这样才能识别路径别名

[code-analysis](../../../../basic/common/code-analysis.md)

# 如何阅读源码

[如何阅读源码 —— 以 Vetur 为例 - 知乎](https://zhuanlan.zhihu.com/p/395405684)
