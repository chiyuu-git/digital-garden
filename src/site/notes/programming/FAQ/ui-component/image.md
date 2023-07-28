---
{"dg-publish":true,"permalink":"/programming/faq/ui-component/image/"}
---


# Img 标签

https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/img

用于插入图片, 自结束标签, 属于行内替换元素, 表现类似于 inline-block

**属性：**

1. src: 设置一个外部图片的路径
   - 相对路径：相对于当前文档的路径
   - ./原目录 可省略
   - ../上一级目录
2. alt: 设置图片的描述（加载失败时显示）
   - 搜索引擎通过 alt 属性来标识不同的图片
   - 如果不写 alt 属性，搜索引擎不会对图片进行收录

**图片格式：**

1. JPEG: 支持颜色多、可压缩，但是不支持透明，常用于保存照片等颜色丰富的照片，jpg 是一种针对相片使用的一种失真压缩方法，是一种破坏性的压缩，在色调及颜色平滑变化做的不错。在 www 上，被用来储存和传输照片的格式。
2. GIF: 颜色少，只支持简单的透明，支持动态图，颜色单一或者是动态图可以用时 GIF，gif 是一种位图文件格式，以 8 位色重现真色彩的图像。可以实现动画效果。
3. PNG: 支持颜色多，且支持透明，png 是便携式网络图片（Portable Network Graphics）是一种无损数据压缩位图文件格式。 优点是：压缩比高，色彩好。 大多数地方都可以用。
4. bmp: 基本不用
5. webp 格式是谷歌在 2010 年推出的图片格式，压缩率只有 jpg 的 2/3，大小比 png 小了 45%。缺点是压缩的时间更久了，兼容性不好，目前谷歌和 opera 支持。

- 使用原则：效果一致使用小的（颜色多 JPEG，颜色单一 GIF），效果不一致使用效果好的

# Image()

`Image()` 函数将会创建一个新的 [`HTMLImageElement`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLImageElement) 实例。

它的功能等价于 [`document.createElement('img')`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/createElement "document.createElement('img')")

```ts
var myImage = new Image(100, 200);
myImage.src = 'picture.jpg';
document.body.appendChild(myImage);
```

Copy to Clipboard

上面的代码相当于在 [`<body>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/body) 中定义了下面的 HTML:

```html
<img width="100" height="200" src="picture.jpg">
```

# Width & Height

## Attribute Width Height

attribute width height 单位是 CSS 像素。可以只指定 `width` 和 `height` 中的一个值，浏览器会根据原始图像进行缩放。

attribute style 的优先级很低, 会被其他样式覆盖

img 的宽高属性没有什么特别的, 就只是 html 的遗留产物而已

如果是在本地的话, 则需要: formDataAsUrl

![](/img/user/programming/FAQ/ui-component/image/image-20221116160729705.png)

## 通过 JS 获取图片原始尺寸

当我们通过 style 样式给 img 设置一个固定的大小时，图片就会按照这个大小来显示。同时设置后，使用 img 的 width 和 height 属性得到的也是我们设置的宽高，而不是图片原始尺寸。

### naturalWidth & naturalHeight

https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLImageElement/naturalHeight

推荐通过 onload 事件回调中获取.

### document.createElement(img)

动态创建一个 imgElement，通过给 src 赋值，最终来获取 img 的宽和高

需要注意的是在给 img 的 src 赋值时，这是一个异步过程，会存在获取 img 的宽度时值为 0(图片还未加载完成)，可以在给 img 赋值之前加上 _onload_ 事件

```js
var img = document.createElement("img")
img.onload = function () {
    var width = img.width
}
img.src = "1.jpg"
```

# 图片上传功能

## 如何实现点击按钮直接让用户上传图片?

只要触发了 input file 的 click 事件即可唤起文件管理器

## 上传到的文件, 要如何插入回编辑器中?

关键是 src 属性, 如果是上传到后端, 那么直接插入后端的地址即可

如果要做本地插入可以使用 formDataAsUrl 之类的能力生成一个本地的 url

### 服务端如何实现?

- 服务端上传接口实现可以参考 [jQuery-File-Upload](https://github.com/blueimp/jQuery-File-Upload/wiki#server-side)。
- 如果要做本地 mock 可以参考这个 [express 的例子](https://github.com/react-component/upload/blob/master/server.js)。

# 图片预览功能

一般是通过 base64 来做

# [JS基础篇--搞清Image加载事件(onload)、加载状态(complete)后，实现图片的本地预览，并自适应于父元素内](https://segmentfault.com/a/1190000010465626)

https://segmentfault.com/a/1190000010465626

# 图片点击态

https://stackoverflow.com/questions/27327765/how-to-get-a-img-tag-the-focus

https://stackoverflow.com/questions/3656467/is-it-possible-to-focus-on-a-div-using-javascript-focus-function

focus any element, contenteditable can be focus

# 图片下方 3px 空隙

来自于 html 排版的幽灵节点, 空白符号, 最常见的解决方法是把 image parent 的 font-size 设置为 0

但是在富文本中, 给所有 img parent 加一个 font-size 0 是否有必要呢? 因为只能加载 style 中, 感觉不太合适

因為圖片預設是 inline 元素，而 inline 元素的 vertical-align 設定預設是和父元素的 baseline 對齊的，而 baseline 本身又和父元素底邊有一定的間距。這個間距是來自於英文文字中像是 j, y 這些超過基準線的字符。

```css
img { 
  vertical-align: top/bottom/text-top/text-bottom;
}
```

```css
img { 
  display: block;
}
```

解法二的用意是將 img 改為區塊元素，讓圖片不會因為行內（inline）元素的特性而受到影響。

.box 是指 img 的父元素，解法三的方式是讓文字大小為 0px，進而讓因為文字而有的間距不見。

[浮动处理，图片下间隙处理_诸神的坟墓的博客-CSDN博客](https://blog.csdn.net/qq_40944918/article/details/95661035)

# 图片压缩

[前端实现图片压缩及遇到的问题 - 知乎](https://zhuanlan.zhihu.com/p/143873360)

## 图片格式分类

无压缩: 无压缩的图片格式不对图片数据进行压缩处理，能准确地呈现原图片。BMP 格式就是其中之一。

无损压缩: 压缩算法对图片的所有的数据进行编码压缩，能在保证图片的质量的同时降低图片的尺寸。png 是其中的代表。

有损压缩: 压缩算法不会对图片所有的数据进行编码压缩，而是在压缩的时候，去除了人眼无法识别的图片细节。因此有损压缩可以在同等图片质量的情况下大幅降低图片的尺寸。其中的代表是 jpg。

结合以上的介绍，我们了解了各种图片格式的优缺点及适用场景，我们再来通过一个图表来做一个抽象总结：

| 格式 | 优点                                       | 缺点                               | 适用场景                   |
| ---- | ------------------------------------------ | ---------------------------------- | -------------------------- |
| gif  | 文件小，支持动画、透明，无兼容性问题       | 只支持 256 种颜色                    | 色彩简单的 logo、icon、动图 |
| jpg  | 色彩丰富，文件小                           | 有损压缩，反复保存图片质量下降明显 | 色彩丰富的图片/渐变图像    |
| png  | 无损压缩，支持透明，简单图片尺寸小         | 不支持动画，色彩丰富的图片尺寸大   | logo/icon/透明图           |
| webp | 文件小，支持有损和无损压缩，支持动画、透明 | 浏览器兼容性不好                   | 支持 webp 格式的 app 和 webview |

## Gif

采用 LZW 压缩算法进行编码，是一种无损的基于索引色的图片格式。由于采用了无损压缩，相比古老的 bmp 格式，尺寸较小，而且支持透明和动画。缺点是由于 gif 只存储 8 位索引（也就是最多能表达 2^8=256 种颜色），色彩复杂、细节丰富的图片不适合保存为 gif 格式。色彩简单的 logo、icon、线框图适合采用 gif 格式。

### Jpg

jpg 是一种有损的基于直接色的图片格式。由于采用直接色，jpg 可使用的颜色有 1600w 之多（2^24），而人眼识别的颜色数量大约只有 1w 多种，因此 jpg 非常适合色彩丰富图片、渐变色。jpg 有损压缩移除肉眼无法识别的图片细节后，可以将图片的尺寸大幅度地减小。

但是 jpg 不适合 icon、logo，因为相比 gif/png-8，它在文件大小上丝毫没有优势。

### Png

[PNG图片压缩原理解析 - 掘金](https://juejin.cn/post/6844903798658318343)

PNG 图片主要有三个类型，分别为 PNG 8/ PNG 24 / PNG 32。

- `PNG 8`：PNG 8 中的 8，其实指的是 8bits，相当于用 2^8（2 的 8 次方）大小来存储一张图片的颜色种类，2^8 等于 256，也就是说 PNG 8 能存储 256 种颜色，一张图片如果颜色种类很少，将它设置成 PNG 8 得图片类型是非常适合的。
- `PNG 24`：PNG 24 中的 24，相当于 3 乘以 8 等于 24，就是用三个 8bits 分别去表示 R（红）、G（绿）、B（蓝）。R(0~255),G(0~255),B(0~255)，可以表达 256 乘以 256 乘以 256=16777216 种颜色的图片，这样 PNG 24 就能比 PNG 8 表示色彩更丰富的图片。但是所占用的空间相对就更大了。
- `PNG 32`：PNG 32 中的 32，相当于 PNG 24 加上 8bits 的透明颜色通道，就相当于 R（红）、G（绿）、B（蓝）、A（透明）。R(0~255),G(0~255),B(0~255),A(0~255)。比 PNG 24 多了一个 A（透明），也就是说 PNG 32 能表示跟 PNG 24 一样多的色彩，并且还支持 256 种透明的颜色，能表示更加丰富的图片颜色类型。

怎么说呢，总的来说，PNG 8/ PNG 24 / PNG 32 就相当于我们屌丝心中，把女神分为三类：

- `一类女神 = PNG 8`：屌丝舔狗们见到第一类女神，顿时会觉得心情愉悦、笑逐颜开，屌丝发黑的印堂逐渐舒展，确认过眼神，是心动的感觉。
- `二类女神 = PNG 24`：第二类女神开始厉害了，会给屌丝们一种菊花一紧、振聋发聩的心弦震撼，接触多了第二类女神能让屌丝每天精神抖擞，延年益寿。
- `三类女神 = PNG 32`：在第三类女神面前，所有的语言都显得苍白无力。那是一种看了让屌丝上下通透、手眼通天的至尊级存在。超凡脱俗、天神下凡都不足以描摹她美色的二分之一。我曾经只有在梦里才见到过。

### Webp

WebP 图片是一种新的图像格式，由 Google 开发。与 png、jpg 相比，相同的视觉体验下，WebP 图像的尺寸缩小了大约 30％。另外，WebP 图像格式还支持有损压缩、无损压缩、透明和动画。理论上完全可以替代 png、jpg、gif 等图片格式，当然目前 webp 的还没有得到全面的支持。

## Dom Method

[前端图片最优化压缩方案 - 掘金](https://juejin.cn/post/6940430496128040967)

## Compressorjs

[GitHub - fengyuanchen/compressorjs: JavaScript image compressor.](https://github.com/fengyuanchen/compressorjs)

## PICA

指定 size 进行压缩.

[GitHub - nodeca/image-blob-reduce: Resize image blobs with high quality. Pica's wrapper to work with file inputs.](https://github.com/nodeca/image-blob-reduce)

[GitHub - nodeca/pica: Resize image in browser with high quality and high speed](https://github.com/nodeca/pica)

## 压缩 GIF

[前端玩转GIF图片压缩 - 掘金](https://juejin.cn/post/6935627673989283848)

## Ndoe 方案

[图片不压缩，前端要背锅 🍳 - 掘金](https://juejin.cn/post/7153086294409609229)

# 图片的大小、分辨率、质量

大小的概念, 太模糊了, 既可以指体积大小, 也可以指分辨率大小, 甚至可以指实际的尺寸大小, 所以尽量不要使用大小来描述图片的信息

## 体积

图片体积（size）指的是图片文件占用的存储空间的大小。

## 分辨率 / 尺寸

图片分辨率 这个说法其实很有问题。分辨率（resolution）说的是另外一件完全不相干的事情。由于人眼球的感光细胞的特性，分辨率有好多种。比如，其中一种是量度人眼能够分辨（resolve）两条平行线的最小距离。二维的情况下可以理解为两个像素点能靠得多近。但后来『分辨率』这个词被用在不同场合描述不同的量度，于是有了今天的混乱结果。

我们通常说的图片分辨率（image resolution）其实是指『像素数』（pixel count）, 通常表达为横向多少个像素 x 纵向多少个像素这样. 像 480x800 这样的表述其实本来应该叫做尺寸（dimensions）的, 但是因为数字图片并没有物理的长宽的概念, 叫做尺寸反而可能会引起误解. 数字图片的『宽』(width) 和『高』(height) 并非物理意义的长度单位, 而是在两个维度上图片包含的像素个数. 比如 480x800 这样的图片是由横向 480 个像素、纵向 800 个像素（合计 384 000 个像素点）构成的.

## 质量

图片质量是一个主观的量度, 一般可以理解为数字图片与真实图片的相似程度. 越相似、质量越高

## 压缩

数字图片存储的时候如果不压缩，体积会比较大。比如，一张 1920x1080 的 24 位彩色 BMP 图片的体积约为 6MB。压缩可以减小体积。压缩分有损（lossy）（常见如 JPEG 格式）和无损（lossless）（常见如 PNG 格式）两种。

无损压缩不会丢失图片的任何信息, 而仅仅是通过减少重复达到缩小体积的目的. 比如一张图全部是同一个颜色, 那么所有像素点不用单独描述, 只需描述一个点加上尺寸就好了. 很多图片（比如人物、风景）可以经过有损压缩进一步缩小尺寸. 有损图片压缩技术利用人眼的特性, 使得可以将部分图片细节丢掉而人眼无法区分（或者说区别不明显）. 一般而言，对于同一张原始图片，有损压缩得越厉害，得到的压缩后的图片偏离原始图片就越大，质量越低.

## 体积与分辨率与质量的关系

图片体积和尺寸、质量的关系比较复杂，因为涉及到图片内容的特性（复杂图片更难压缩、简单图像更容易压缩）、压缩方法等多种因素的影响。通常可以这么理解：尺寸越大、质量越高，则体积越大。

# 图片宽高比 1:1

by chatgpt

```css
.square-container {
  width: 100%; /* 设置容器的宽度 */
  position: relative;
  overflow: hidden;
  padding-bottom: 100%; /* 通过设置 padding-bottom 为百分比值，约束容器的高度为宽度的相同百分比 */
}

.square-container img {
  width: 100%; /* 图像宽度设置为 100%，占满容器宽度 */
  height: auto; /* 图像高度自适应，保持原始比例 */
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  margin: auto; /* 使用 margin: auto 居中图像 */
}
```

这段代码中，`.square-container` 的宽度被设置为 100% 以占满其父容器的宽度。通过设置 `padding-bottom` 为 100%，容器的高度将与宽度相等，因为 padding 值是基于容器宽度计算的。

图像通过 `position: absolute` 和对应的 `top`, `left`, `bottom`, `right` 属性被居中显示在容器中。`margin: auto` 用于在垂直和水平方向上将图像居中。

这样，无论图像的实际宽度是多少，容器的高度都会按照宽高比 1:1 进行约束。

# 图片的懒加载和预加载

预加载：提前加载图片，当用户需要查看时可直接从本地缓存中渲染。

懒加载：懒加载的主要目的是作为服务器前端的优化，减少请求数或延迟请求数。

两种技术的本质：两者的行为是相反的，一个是提前加载，一个是迟缓甚至不加载。 懒加载对服务器前端有一定的缓解压力作用，预加载则会增加服务器前端压力。

懒加载也叫延迟加载，指的是在长网页中延迟加载图片的时机，当用户需要访问时，再去加载，这样可以提高网站的首屏加载速度，提升用户的体验，并且可以减少服务器的压力。它适用于图片很多，页面很长的电商网站的场景。懒加载的实现原理是，将页面上的图片的 src 属性设置为空字符串，将图片的真实路径保存在一个自定义属性中，当页面滚动的时候，进行判断，如果图片进入页面可视区域内，则从自定义属性中取出真实路径赋值给图片的 src 属性，以此来实现图片的延迟加载。

预加载指的是将所需的资源提前请求加载到本地，这样后面在需要用到时就直接从缓存取资源。通过预加载能够减少用户的等待时间，提高用户的体验。我了解的预加载的最常用的方式是使用 js 中的 image 对象，通过为 image 对象来设置 scr 属性，来实现图片的预加载。

详细资料可以参考： [《懒加载和预加载》](https://juejin.im/post/5b0c3b53f265da09253cbed0) [《网页图片加载优化方案》](https://juejin.im/entry/5a73f38cf265da4e99575be3) [《基于用户行为的图片等资源预加载》](https://www.zhangxinxu.com/wordpress/2016/06/image-preload-based-on-user-behavior/)
