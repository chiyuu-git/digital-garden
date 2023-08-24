---
{"dg-publish":true,"permalink":"/programming/font-end/browser-core/browser-optimize/browser-optimize/"}
---


# 资源请求优化

## 域名分片

<https://www.iteye.com/blog/aoyouzi-2171463>

根据并行数量，适当的划分域名

每个域名连接的图片不超过 6 张

网页中包含的资源（图片、js、css、iframe 等）引起的 http 请求基本上都来源于同一个域名，有的网站对静态资源文件使用了独立的无 cookie 域名。YSlow 提倡的高性能网站准则中有一条“reduce DNS lookups”，减少域名数量以便减少域名解析所需要的时间，但是，有的情况下，适当的增加网页中使用的域名数却可以提升网页的速度。

首先，我们来看一个例子：

- 在这个例子中，网页包含 12 张图片，每张图片加载需要 4 秒钟。
- 从上图中可以看到，IE6 下，前 10 张图片是并行下载，后 2 张要在前 10 张图片下载完成后才开始下载，Firefox 下情况比较类似，只不过图片是每 6 张一组下载。这是因为浏览器限制了同一个域名下最大并行下载数量（或者说最大连接数），当并行下载数到达上限时，其后的 http 请求就必须排队等待，除了图片外，css、html 等网页资源也存在同样的问题。

同一域名的最大并行下载数量与浏览器类型、浏览器版本、HTTP 版本有关，以下是网上收集的数据，并未核实。

并行下载是加快网页显示非常关键的一项技术，如何多图片的并行下载呢？我们再来看另外一个例子。

上例中已经实现了多图片的并行下载，网页加载时间从 7.9s 减少到 4s，速度提升了一倍。从源代码中可以看到，图片使用了不同的域名，每个域名连接的图片不超过 6 张。本例的关键技术就是使用了多域名来区分资源（包括图片、css 等）。

### IP 和域名

“每台服务器的最大连接数”，这里说的服务器是指的 URL 地址中的域名，而不是服务器的 IP 地址，所以，同一 IP、不同域名会被看成不同的服务器。利用 Apache 的 ServerAlias 可以很方便的在同一域名下增加多个别名域名，相应的，在域名解析时只需要添加一个别名的 CNAME 记录即可。

### 多少个域名合适

在前面的内容中你已经看到了，两个域名比一个域名好，三个域名是否会比两个域名好呢？10 个域名呢？YSlow 的开发者、Yahoo 网页性能专家 Steve Souders 在他的性能研究报告中指出，从一个域名增加到两个域名，网站性能能够得到提升，当域名大于两个时，会对网页加载时间产生负面影响。这个问题的最终答案是域名的数量与网页资源的数量、体积有关，但是，按着经验来说，两个足以。

### 如何划分资源

如何给指定的资源分配多域名中的一个域名呢？非常关键的一条原则是保证一个资源在分配时总是分配到固定的一个域名，这样，如果资源已经在浏览器缓存了的话，下次访问时就可以在缓存中读取，避免从其他域名再次下载资源。其中一种方法是，使用哈希函数转换资源的文件名为整数，再根据约定的算法选择域名。

当然，手动指定是最原始但有效的方法。

## 缓存

[http-cache](../../../basic/cs-basic/network-protocol/http-cache.md)

## 懒执行

懒执行就是将某些逻辑延迟到使用时再计算。该技术可以用于首屏优化，对于某些耗时逻辑并不需要在首屏就使用的，就可以使用懒执行。懒执行需要唤醒，一般可以通过定时器或者事件的调用来唤醒。

## 懒加载

懒加载就是将不关键的资源延后加载。

懒加载的原理就是只加载自定义区域（通常是可视区域，但也可以是即将进入可视区域）内需要加载的东西。对于图片来说，先设置图片标签的 `src` 属性为一张占位图，将真实的图片资源放入一个自定义属性中，当进入自定义区域时，就将自定义属性替换为 `src` 属性，这样图片就会去下载资源，实现了图片懒加载。

懒加载不仅可以用于图片，也可以使用在别的资源上。比如进入可视区域才开始播放视频等等。

用于占位的 src，可以是一张 loading gif

实现如下：

```html
<img src="" class="image-item" lazyload="true"  data-original="images/1.png"/>
<img src="" class="image-item" lazyload="true"  data-original="images/2.png"/>
// ... 多个图片
```

```js
// 获取视口的高度
const viewHeight = document.documentElement.clientHeight
  function lazyload(){
    const imgs=document.querySelectorAll('img[data-original][lazyload]')
    Array.prototype.forEach.call(imgs,function(item,index){
    if(item.dataset.original==="") return
		// 用于获得页面中某个元素的左，上，右和下分别相对浏览器视窗的位置
    let rect = item.getBoundingClientRect()
    // 即将进入视窗时 可以做一定的提前量 小于 viewHeight + 100px
    if(rect.bottom>=0 && rect.top < viewHeight){
        const img=new Image()
        img.src=item.dataset.original
        // 等图片下载完了再显示
        img.onload=function(){
          item.src=img.src
        }
        item.removeAttribute("data-original")//移除属性，下次不再遍历
        item.removeAttribute("lazyload")
      }
    })
  }
  lazyload()//刚开始还没滚动屏幕时，要先触发一次函数，初始化首页的页面图片
  document.addEventListener("scroll",lazyload)
```

使用节流函数进行优化

# 文件优化

## 图片优化

不用图片。很多时候会使用到很多修饰类图片，其实这类修饰图片完全可以用 CSS 去代替。

对于移动端来说，屏幕宽度就那么点，完全没有必要去加载原图浪费带宽。一般图片都用 CDN 加载，可以计算出适配屏幕的宽度，然后去请求相应裁剪好的图片。

小图使用 base64 格式

将多个图标文件整合到一张图片中（雪碧图）

选择正确的图片格式：

- 对于能够显示 WebP 格式的浏览器尽量使用 WebP 格式。因为 WebP 格式具有更好的图像数据压缩算法，能带来更小的图片体积，而且拥有肉眼识别无差异的图像质量，缺点就是兼容性并不好
- 小图使用 PNG，其实对于大部分图标这类图片，完全可以使用 SVG 代替
- 照片使用 JPEG

## CDN

静态资源尽量使用 CDN 加载，由于浏览器对于单个域名有并发请求上限，可以考虑使用多个 CDN 域名。对于 CDN 加载静态资源需要注意 CDN 域名要与主站不同，否则每次请求都会带上主站的 Cookie

# 打包构建优化

## 使用 Webpack 优化项目

对于 Webpack4，打包项目使用 production 模式，这样会自动开启代码压缩

使用 ES6 模块来开启 tree shaking，这个技术可以移除没有使用的代码

优化图片，对于小图可以使用 base64 的方式写入文件中

按照路由拆分代码，实现按需加载

给打包出来的文件名添加哈希，实现浏览器缓存文件

# 图片加载方法

<https://zhuanlan.zhihu.com/p/87805578>

# 浏览器性能优化

早在五年前，Google 就提出了 1s 完成终端页面的首屏渲染的标准。

![google_atf](/img/user/programming/font-end/browser-core/browser-optimize/!browser-optimize/google_atf.png)

常见的优化网络请求的方法有：DNS Lookup，减少重定向，避免 JS、CSS 阻塞，并行请求，代码压缩，缓存，按需加载，前端模块化…

虽然相较于网络方面的优化，前端渲染的优化显得杯水车薪，而且随着浏览器和硬件性能的增长，再加上主流前端框架（react、vue、angular）的已经帮我们解决了大多数的性能问题，但是前端渲染性能优化依然值得学习，除去网络方面的消耗，留给前端渲染的时间已经不多了。

## 浏览器是如何渲染一个页面的

1. 浏览器把获取到的 HTML 代码解析成 1 个 DOM 树，HTML 中的每个 tag 都是 DOM 树中的 1 个节点，根节点是 document 对象。DOM 树里包含了所有 HTML 标签，包括 `display:none` 隐藏的标签，还有用 JS 动态添加的元素等。
2. 浏览器把所有样式解析成样式结构体，在解析的过程中会去掉浏览器不能识别的样式，比如 IE 会去掉 -moz 开头的样式。
3. DOM Tree 和样式结构体组合后构建 render tree, render tree 类似于 DOM tree，但区别很大:
	1. render tree 能识别样式，render tree 中每个 NODE 都有自己的 style
	2. 而且 render tree 不包含隐藏的节点 (比如 `display:none` 的节点，还有 head 节点)，因为这些节点不会用于呈现，而且不会影响呈现的节点，所以就不会包含到 render tree 中。注意 `visibility:hidden` 隐藏的元素还是会包含到 render tree 中的，因为 `visibility:hidden` 会影响布局 (layout)，会占有空间
	3. 根据 CSS2 的标准，render tree 中的每个节点都称为 Box (Box dimensions)，理解页面元素为一个具有填充、边距、边框和位置的盒子。
4. 一旦 render tree 构建完毕后，浏览器就可以根据 render tree 来绘制页面了。

> 由于浏览器使用流式布局，对 `Render Tree` 的计算通常只需要遍历一次就可以完成，但 `table` 及其内部元素除外，他们可能需要多次计算，通常要花 3 倍于同等元素的时间，这也是为什么要避免使用 `table` 布局的原因之一。

在此过程中，前端工程师主要的敌人为：

  - 重新计算样式（Recalculate Style）、计算布局（Layout）=> Rendering/Reflow。
  - 绘制 => Painting/Repaint。

1. 尽量减少 HTTP 请求次数
2. 减少 DNS 查找次数
3. 避免跳转
4. 可缓存的 AJAX
	1. 配置 ETag
	2. 尽早刷新输出缓冲
	3. 使用 GET 来完成 AJAX 请求
5. 推迟加载内容
6. 预加载
7. 减少 DOM 元素数量
8. 根据域名划分页面内容
9. 使 iframe 的数量最小
10. 不要出现 404 错误
11. 使用内容分发网络
12. 为文件头指定 Expires 或 Cache-Control 13、Gzip 压缩文件内容
13. 把样式表置于顶部
14. 避免使用 CSS 表达式（Expression）
15. 使用外部 JavaScript 和 CSS
16. 削减 JavaScript 和 CSS
17. 用<link>代替@import
18. 避免使用滤镜
19. 把脚本置于页面底部
20. 剔除重复脚本

## 首屏优化

参考： https://lz5z.com/Web%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96-%E9%A6%96%E5%B1%8F%E5%92%8C%E7%99%BD%E5%B1%8F%E6%97%B6%E9%97%B4/

### 分析

总是先下载 html，然后总是解析 html，在解析 html 的过程中：

+ 脚本和样式表的下载
+ 脚本和样式表的解析

要么白屏，要么 fouc

白屏就是因为要等待 cssom

fouc 就是不等待 cssom，直接渲染默认样式的

脚本的位置不影响渲染，因为解析脚本的时候渲染是被挂起的，解析完之后还是和有脚本之前的状态一样的

### Css 在 Dom 前

chrome 就是白屏，firefox 就是 fouc

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Page Title</title>
  </head>
  <body>
    <script src='./index.js'></script>
    <script>debugger</script>
    <link rel="stylesheet" href="style.css">
    <script>debugger</script>
    <h1>h1 title</h1>
  </body>
</html>
```

### Css 在 Dom 后

实测两者都是 fouc，但是网上部分文章说 chrome 是白屏

```html
<script src='./index.js'></script>
<script>debugger</script>
<h1>h1 title</h1>
<script>debugger</script>
<link rel="stylesheet" href="style.css">
```

### 白屏的产生

白屏：对于 -webkit 内核的浏览器（IE 也会产生，Safari），在进行网页渲染时，会同时加载 html 和 css 分别构建 DOM 树和 CSSOM，等两者都构建完成后，再绘制渲染树，然后将页面显示出来。如果在 html 中将 css 文件放置在文档最后，那么将会导致 CSSOM 晚于 DOM 树的建立，浏览器需要等待 CSSOM 建立，然后才进行网页内容的绘制，这个等待的过程，没有内容显示，就导致了白屏的产生，因此在开发中，需要将 CSS 放在 head 标签内，让其与 html 内容同时被加载。

白屏的产生有三种情况：

- 将 css 文件放在 html 文档的最后
- 使用@import 引入 css（因为通过@import 引入的 css 文件会被最后加载，因此也会导致白屏）
- 将 js 文件放在头部，而未使用 defer 或 async 延迟或异步加载 js 文件，导致 js 阻塞 html 和 css 的加载

### FOUC 的产生

FOUC - Flash Of Unstyled Content 文档样式闪烁

FOUC 的产生：主要是由于浏览器先显示已加载的 html 内容，等到 css 加载完成后重新对内容添加样式导致的，主要代表有 Firefox

在弄清楚 FOUC 和白屏之前，先要弄清楚几种浏览器解析的方式

Chrome 和 Safari: 当发现 `<link rel ="stylesheet">` 后立即停止渲染，在所有 css 加载完成之前页面上不会有任何内容

Firefox:`<head>` 标签中的 `<link rel ="stylesheet">` 与 Chrome 和 Safari 中完全一致，这些 link 标签全部加载完之前，页面上不显示任何内容，而 `<body>` 中的内容则不阻塞任何内容显示，也就是说，放 `<body>` 内，先渲染没有样式的，再渲染有样式的。

IE/Edge：未加载完成的 `<link real="stylesheet">` 只阻塞其后面 HTML 内容显示，而对其前面的内容不影响。

所以，对于以 webkit 为内核的 google,Safari 浏览器来说 CSS 放在页面哪个位置都一样

火狐 放 head 先解析 CSS 放 body 里面，边加载边渲染，这样就会出现闪屏（无样式内容闪烁，Fouc），这样，对于火狐浏览器来说，建议吧 CSS 文件放到 `<head>` 标签里

所以，综上所述，CSS 文件要放到 `<head>` 标签内。

要将各浏览器对 CSS 的加载比喻成参加晚会，那

google 浏览器 先解析 css 解析完之后再开始给 HTML 渲染可以比喻成 先化妆再出门（急性子的姑娘总是把事情做好了才踏实）

优点：节约渲染时间

火狐：边解析变渲染则可以比喻成先出门然后在车上化妆（慢性子的姑娘，不急哈）

```html
<style type="text/css" media="all">@import "../fouc.css";</style>
```

而引用 CSS 文件的@import 就是造成这个问题的罪魁祸首。IE 会先加载整个 HTML 文档的 DOM，然后再去导入外部的 CSS 文件，因此，在页面 DOM 加载完成到 CSS 导入完成中间会有一段时间页面上的内容是没有样式的，这段时间的长短跟网速，电脑速度都有关系。

解决方法：只要在 `<head>` 之间加入一个 `<link>` 或者 `<script>` 元素就可以了。

### 解决方案

浏览器可以延迟呈现，直到所有的样式表都下载完之后，这就导致了白屏。反之，浏览器可以逐步呈现，但要承担闪烁的风险。这里没有完美的选择。

由于外部的 JS 和 CSS 文件是**并行下载**的。随着 JS 技术的发展，JS 也开始承担起页面的渲染工作了。如果 JS 加载需要很长时间，会影响用户体验。所以需要将 JS 区分为承担页面渲染工作的 JS 和承担事件处理的 JS。渲染页面的 JS 放在前面，事务处理的 JS 放在后面。

前端一般主要关心首屏的渲染速度，根据这一点，有如下最佳实践

> 这也是为什么要提倡“图片懒加载”的原因。

与首屏呈现有关的样式表放在 `<head></head>` 标签之间

与首屏呈现有关的 JS 脚本放在 `<head></head>` 标签之间

> 白屏和无样式内容的闪烁 ---- 都不再是风险

与首屏呈现无关的样式表，可以在首屏加载完成之后再动态加载

与首屏呈现无关的样式表的 JS 脚本放在 ` <body></body> ` 之间的，配合 window.onload 保证 DOM 节点的访问

### 把 Script 标签放在页面的最底部的 Body 封闭之前和封闭之后有什么区别？浏览器会如何解析它们？

详细资料可以参考： [《为什么把 script 标签放在 body 结束标签之后 html 结束标签之前？》](https://www.zhihu.com/question/20027966) [《从 Chrome 源码看浏览器如何加载资源》](https://zhuanlan.zhihu.com/p/30558018)

### 异步加载脚本

![es-module](programming/font-end/primitive/es/es-module.md#异步加载脚本)

### 首屏和白屏

白屏时间是指浏览器从响应用户输入网址地址，到浏览器开始显示内容的时间

白屏时间 = 地址栏输入网址后回车 - 浏览器出现第一个元素

影响白屏时间的因素：网络，服务端性能，前端页面结构设计。

首屏时间是指浏览器从响应用户输入网络地址，到首屏内容渲染完成的时间

首屏时间 = 地址栏输入网址后回车 - 浏览器第一屏渲染完成

影响首屏时间的因素：白屏时间，资源下载执行时间。

### 监控首屏和白屏

将 chrome 网速调为 Fast 3G，然后打开 Performance 工具，点击 “Start profiling and reload page” 按钮，查看 Screenshots 如下图

### 白屏时间

通常认为浏览器开始渲染 `<body>` 或者解析完 `<head>` 的时间是白屏结束的时间点。

  ```html
  <!DOCTYPE html>
  <html>
    <head>
      <meta charset="utf-8">
      <title>白屏</title>
      <script>
        // 不兼容 performance.timing 的浏览器
        window.pageStartTime = Date.now()
      </script>
      <!-- 页面 CSS 资源 -->
      <link rel="stylesheet" href="xx.css">
      <link rel="stylesheet" href="zz.css">
      <script>
        // 白屏结束时间
        window.firstPaint = Date.now()
        // 白屏时间
        console.log(firstPaint - performance.timing.navigationStart)
      </script>
    </head>
    <body>
      <h1>Hello World</h1>
    </body>
  </html>
  ```

白屏时间 = `firstPaint - performance.timing.navigationStart || pageStartTime`

### 首屏时间

关于首屏时间是否包含图片加载网上有不同的说法，个人认为，只要首屏中的图片加载完成，即是首屏完成，不在首屏中的图片可以不考虑。

#### 首屏模块标签标记法

由于浏览器解析 HTML 是按照顺序解析的，当解析到某个元素的时候，你觉得首屏完成了，就在此元素后面加入 `script` 计算首屏完成时间。

  ```html
  <!DOCTYPE html>
  <html>
  <head>
      <meta charset="utf-8">
      <title>首屏</title>
      <script>
          // 不兼容 performance.timing 的浏览器
          window.pageStartTime = Date.now()
      </script>
  </head>
  <body>
      <!-- 首屏可见内容 -->
      <div class=""></div>
      <!-- 首屏可见内容 -->
      <div class=""></div>
      <script type="text/javascript">
              // 首屏屏结束时间
              window.firstPaint = Date.now()
              // 首屏时间
              console.log(firstPaint - performance.timing.navigationStart)
      </script>
      <!-- 首屏不可见内容 -->
      <div class=""></div>
      <!-- 首屏不可见内容 -->
      <div class=""></div>
  </body>
  </html>
  ```

#### 统计首屏内加载最慢的图片/iframe

通常首屏内容中加载最慢的就是图片或者 iframe 资源，因此可以理解为当图片或者 iframe 都加载出来了，首屏肯定已经完成了。

由于浏览器对每个页面的 TCP 连接数有限制，使得并不是所有图片都能立刻开始下载和显示。我们只需要监听首屏内所有的图片的 onload 事件，获取图片 onload 时间最大值，并用这个最大值减去 navigationStart 即可获得近似的首屏时间。

```html
  <!DOCTYPE html>
  <html>
    <head>
      <meta charset="utf-8">
      <title>首屏</title>
      <script>
        // 不兼容 performance.timing 的浏览器
        window.pageStartTime = Date.now()
      </script>
    </head>
    <body>
      <img src="https://lz5z.com/assets/img/google_atf.png" alt="img" onload="load()">
      <img src="https://lz5z.com/assets/img/css3_gpu_speedup.png" alt="img" onload="load()">
      <script>
        function load () {
          window.firstScreen = Date.now()
        }
        window.onload = function () {
          // 首屏时间
          console.log(window.firstScreen - performance.timing.navigationStart)
        }
      </script>
    </body>
  </html>
```

## Performance API

 [Performance API](https://developer.mozilla.org/zh-CN/docs/Web/API/Performance)

Performance 接口可以获取到当前页面与性能相关的信息。

未读：<https://zhuanlan.zhihu.com/p/82981365>[10分钟彻底搞懂前端页面性能监控](https://zhuanlan.zhihu.com/p/82981365)

<https://juejin.im/post/5b879e16f265da436d7e543c>

<https://developers.google.com/web/fundamentals/performance/rendering/stick-to-compositor-only-properties-and-manage-layer-count>

### Performance.timing 对象

在 chrome 中查看 performance.timing 对象：

与浏览器对应的状态如下图：

左边红线代表的是网络传输层面的过程，右边红线代表了服务器传输回字节后浏览器的各种事件状态，这个阶段包含了浏览器对文档的解析，DOM 树构建，布局，绘制等等。

上图是 Level 1 的规范，2012 年底进入候选建议阶段，至今仍在日常使用中；但是在 W3C 的议程上，它已经功成身退，让位给了精度更高，功能更强大，层次更分明的 Level 2（处理模型如下图）。比如独立划分出来的 Resource Timing，使得我们可以获取具体资源的详细耗时信息。

![img](https://pic2.zhimg.com/v2-dc4740614499ad2493efce8d5e827eb1_b.jpg)

### Timming 对象的属性

- navigationStart: 表示从上一个文档卸载结束时的 unix 时间戳，如果没有上一个文档，这个值将和 fetchStart 相等。
- unloadEventStart: 表示前一个网页（与当前页面同域）unload 的时间戳，如果无前一个网页 unload 或者前一个网页与当前页面不同域，则值为 0。
- unloadEventEnd: 返回前一个页面 unload 时间绑定的回掉函数执行完毕的时间戳。
- redirectStart: 第一个 HTTP 重定向发生时的时间。有跳转且是同域名内的重定向才算，否则值为 0。
- redirectEnd: 最后一个 HTTP 重定向完成时的时间。有跳转且是同域名内部的重定向才算，否则值为 0。
- fetchStart: 浏览器准备好使用 HTTP 请求抓取文档的时间，这发生在检查本地缓存之前。
- domainLookupStart/domainLookupEnd: DNS 域名查询开始/结束的时间，如果使用了本地缓存（即无 DNS 查询）或持久连接，则与 fetchStart 值相等
- connectStart: HTTP（TCP）开始/重新 建立连接的时间，如果是持久连接，则与 fetchStart 值相等。
- connectEnd: HTTP（TCP） 完成建立连接的时间（完成握手），如果是持久连接，则与 fetchStart 值相等。
- secureConnectionStart: HTTPS 连接开始的时间，如果不是安全连接，则值为 0。
- requestStart: HTTP 请求读取真实文档开始的时间（完成建立连接），包括从本地读取缓存。
- responseStart: HTTP 开始接收响应的时间（获取到第一个字节），包括从本地读取缓存。
- responseEnd: HTTP 响应全部接收完成的时间（获取到最后一个字节），包括从本地读取缓存。
- domLoading: 开始解析渲染 DOM 树的时间，此时 Document.readyState 变为 loading，并将抛出 readystatechange 相关事件。
- domInteractive: 完成解析 DOM 树的时间，Document.readyState 变为 interactive，并将抛出 readystatechange 相关事件，注意只是 DOM 树解析完成，这时候并没有开始加载网页内的资源。
- domContentLoadedEventStart: DOM 解析完成后，网页内资源加载开始的时间，在 DOMContentLoaded 事件抛出前发生。
- domContentLoadedEventEnd: DOM 解析完成后，网页内资源加载完成的时间（如 JS 脚本加载执行完毕）。
- domComplete: DOM 树解析完成，且资源也准备就绪的时间，Document.readyState 变为 complete，并将抛出 readystatechange 相关事件。
- loadEventStart: load 事件发送给文档，也即 load 回调函数开始执行的时间。
- loadEventEnd: load 事件的回调函数执行完毕的时间。

### Performance.navigation 对象

- redirectCount: 0 // 页面经过了多少次重定向
- type
  - 0 表示正常进入页面；
  - 1 表示通过 `window.location.reload()` 刷新页面；
  - 2 表示通过浏览器前进后退进入页面；
  - 255 表示其它方式

### Performance.memory

- jsHeapSizeLimit: 内存大小限制
- totalJSHeapSize: 可使用的内存
- usedJSHeapSize: JS 对象占用的内存

## DOMContentLoaded Vs Load

- DOMContentLoaded 是指页面元素加载完毕，但是一些资源比如图片还无法看到，但是这个时候页面是可以正常交互的，比如滚动，输入字符等。 jQuery 中经常使用的 `$(document).ready()` 其实监听的就是 DOMContentLoaded 事件。
- load 是指页面上所有的资源（图片，音频，视频等）加载完成。jQuery 中 `$(document).load()` 监听的是 load 事件。

# DNS 预解析

- DNS 解析也是需要时间的，可以通过预解析的方式来预先获得域名所对应的 IP。

  ```html
  <link rel="dns-prefetch" href="//yuchengkai.cn" />
  ```

# 预获取

prefetch

# 预加载 @@@

参考：

+ https://juejin.im/post/5a7fb09bf265da4e8e785c38

- 在开发中，可能会遇到这样的情况。有些资源不需要马上用到，但是希望尽早获取，这时候就可以使用预加载。
- 预加载其实是声明式的 `fetch` ，强制浏览器请求资源，并且不会阻塞 `onload` 事件，可以使用以下代码开启预加载

```html
  <link rel="preload" href="http://example.com" />
```

- 预加载可以一定程度上降低首屏的加载时间，因为可以将一些不影响首屏但重要的文件延后加载，唯一缺点就是兼容性不好。

## 使用 Link 标签创建

```html
  <!-- 使用 link 标签静态标记需要预加载的资源 -->
  <link rel="preload" href="/path/to/style.css" as="style">
  
  <!-- 或使用脚本动态创建一个 link 标签后插入到 head 头部 -->
  <script>
  const link = document.createElement('link');
  link.rel = 'preload';
  link.as = 'style';
  link.href = '/path/to/style.css';
  document.head.appendChild(link);
  </script>
```

**示例**

如我们常用到的 antd 会依赖一个 CDN 上的 font.js 字体文件，我们可以设置为提前加载，以及有一些模块虽然是按需异步加载，但在某些场景下知道其必定会加载的，则可以设置 preload 进行预加载，如：

```html
  <link rel="preload" as="font"   href="https://at.alicdn.com/t/font_zck90zmlh7hf47vi.woff">
  <link rel="preload" as="script" href="https://a.xxx.com/xxx/PcCommon.js">
  <link rel="preload" as="script" href="https://a.xxx.com/xxx/TabsPc.js">
```

## 使用 HTTP 响应头的 Link 字段创建

```
  Link: <https://example.com/other/styles.css>; rel=preload; as=style
```

## 如何判断浏览器是否支持 Preload

目前我们支持的浏览器主要为高版本 Chrome，所以可放心使用 preload 技术。

在不支持 preload 的浏览器环境中，会忽略对应的 link 标签，而若需要做特征检测的话，则：

```js
  const isPreloadSupported = () => {
    const link = document.createElement('link');
    const relList = link.relList;
  
    if (!relList || !relList.supports) {
      return false;
    }
  
    return relList.supports('preload');
  };
```

## 如何区分 Preload 和 Prefetch

preload 是告诉浏览器页面**必定**需要的资源，浏览器**一定会**加载这些资源；

> preload 是确认会加载指定资源，如在我们的场景中，x-report.js 初始化后一定会加载 PcCommon.js 和 TabsPc.js, 则可以预先 preload 这些资源

prefetch 是告诉浏览器页面**可能**需要的资源，浏览器**不一定会**加载这些资源。

> prefetch 是预测会加载指定资源，如在我们的场景中，我们在页面加载后会初始化首屏组件，当用户滚动页面时，会拉取第二屏的组件，若能预测用户行为，则可以 prefetch 下一屏的组件。

## Preload 将提升资源加载的优先级

使用 preload 前，在遇到资源依赖时进行加载：

使用 preload 后，不管资源是否使用都将提前加载：

可以看到，preload 的资源加载顺序将被提前：

## 避免滥用 Preload

使用 preload 后，Chrome 会有一个警告：

如上文所言，若不确定资源是必定会加载的，则不要错误使用 preload，以免本末倒置，给页面带来更沉重的负担。

当然，可以在 PC 中使用 preload 来刷新资源的缓存，但在移动端则需要特别慎重，因为可能会浪费用户的带宽。

## 避免混用 Preload 和 Prefetch

preload 和 prefetch 混用的话，并不会复用资源，而是会重复加载。

```html
  <link rel="preload"   href="https://at.alicdn.com/t/font_zck90zmlh7hf47vi.woff" as="font">
  <link rel="prefetch"  href="https://at.alicdn.com/t/font_zck90zmlh7hf47vi.woff" as="font">
```

使用 preload 和 prefetch 的逻辑可能不是写到一起，但一旦发生对用一资源 preload 或 prefetch 的话，会带来双倍的网络请求，这点通过 Chrome 控制台的网络面板就能甄别：

## 避免错用 Preload 加载跨域资源

若 css 中有应用于已渲染到 DOM 树的元素的选择器，且设置了 @font-face 规则时，会触发字体文件的加载。 而字体文件加载中时，DOM 中的这些元素，是处于不可见的状态。对已知必加载的 font 文件进行预加载，除了有性能提升外，更有体验优化的效果。

在我们的场景中，已知 antd.css 会依赖 font 文件，所以我们可以对这个字体文件进行 preload:

```html
  <link rel="preload" as="font" href="https://at.alicdn.com/t/font_zck90zmlh7hf47vi.woff">
```

然而我发现这个文件加载了两次：

原因是对跨域的文件进行 preload 的时候，我们必须加上 crossorigin 属性：

```html
  <link rel="preload" as="font" crossorigin href="https://at.alicdn.com/t/font_zck90zmlh7hf47vi.woff">
```

再看一下网络请求，就变成一条了。

W3 规范是这么解释的：

> Preload links for CORS enabled resources, such as fonts or images with a crossorigin attribute, must also include a crossorigin attribute, in order for the resource to be properly used.

那为何会有两条请求，且优先级不一致，又没有命中缓存呢？这就得引出下一个话题来解释了。

## 不同资源加载的优先级规则

> 这张表详见：Chrome Resource Priorities and Scheduling

这张图表示的是，在 Chrome 46 以后的版本中，不同的资源在浏览器渲染的不同阶段进行加载的优先级。 在这里，我们只需要关注 DevTools Priority 体现的优先级，一共分成五个级别：

  + Highest 最高
  + Hight 高
  + Medium 中等
  + Low 低
  + Lowest 最低

### 最高

html 主要资源，其优先级是最高的

css 样式资源，其优先级也是最高的

CSS(match) 指的是对已有的 DOM 具备规则的有效的样式文件。

### Script 脚本资源，优先级不一

前三个 js 文件是写死在 html 中的静态资源依赖，后三个 js 文件是根据首屏按需异步加载的组件资源依赖，这正验证了这个规则。

### Font 字体资源，优先级不一

css 样式文件中有一个 @font-face 依赖一个 font 文件，样式文件中依赖的字体文件加载的优先级是 Highest； 在使用 preload 预加载这个 font 文件时，若不指定 crossorigin 属性 (即使同源)，则会采用匿名模式的 CORS 去加载，优先级是 High，看下图对比：

  + 第一条 High 优先级也就是 preload 的请求：
  + 第二条 Highest 也就是样式引入的请求：

可以看到，在 preload 的请求中，缺少了一个 origin 的请求头字段，表示这个请求是匿名的请求。 让这两个请求能共用缓存的话，目前的解法是给 preload 加上 crossorigin 属性，这样请求头会带上 origin, 且与样式引入的请求同源，从而做到命中缓存：

```html
  <link rel="preload" as="font" crossorigin href="https://at.alicdn.com/t/font_zck90zmlh7hf47vi.woff">
```

这么请求就只剩一个：

在网络瀑布流图中，也显示成功预加载且后续命中缓存不再二次加载：

## 总结

preload 是个好东西，能告诉浏览器提前加载当前页面必须的资源，将加载与解析执行分离开，做得好可以对首次渲染带来不小的提升，但要避免滥用，区分其与 prefetch 的关系，且需要知道 preload 不同资源时的网络优先级差异。

preload 加载页面必需的资源如 CDN 上的字体文件，与 prefetch 预测加载下一屏数据，兴许是个不错的组合。

# 预渲染

可以通过预渲染将下载的文件预先在后台渲染，可以使用以下代码开启预渲染

```html
  <link rel="prerender" href="http://example.com" />
```

预渲染虽然可以提高页面的加载速度，但是要确保该页面在之后会被用户打开，否则就白白浪费资源去渲染
