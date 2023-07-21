---
{"dg-publish":true,"permalink":"/programming/basic/cs-basic/network-protocol/http-storage/"}
---


<https://juejin.im/post/5a6c87c46fb9a01ca560b4d7#heading-33>

# Web Storage

**Web Storage API** 提供机制， 使浏览器能以一种比使用 Cookie 更直观的方式**存储**键/值对。

由 HTML5 定义

## Web Storage 概念和用法

Web Storage 包含如下两种机制：

+ `sessionStorage` 为每一个给定的源（given origin）维持一个独立的存储区域，该存储区域在页面会话期间可用（即只要浏览器处于打开状态，包括页面重新加载和恢复）。
+ `localStorage` 同样的功能，但是在浏览器关闭，然后重新打开后数据仍然存在。

这两种机制是通过 [`Window.sessionStorage`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/sessionStorage) 和 [`Window.localStorage`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/localStorage) 属性使用（更确切的说，在支持的浏览器中 `Window` 对象实现了 `WindowLocalStorage` 和 `WindowSessionStorage` 对象并挂在其 `localStorage` 和 `sessionStorage` 属性下）—— 调用其中任一对象会创建 [`Storage`](https://developer.mozilla.org/zh-CN/docs/Web/API/Storage) 对象，通过 [`Storage`](https://developer.mozilla.org/zh-CN/docs/Web/API/Storage) 对象，可以设置、获取和移除数据项。对于每个源（origin）`sessionStorage` 和 `localStorage` 使用不同的 Storage 对象——独立运行和控制。

https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Storage_API

例如，在文档中调用 `localStorage` 将会返回一个 [`Storage`](https://developer.mozilla.org/zh-CN/docs/Web/API/Storage) 对象；调用 `sessionStorage` 返回一个不同的 [`Storage`](https://developer.mozilla.org/zh-CN/docs/Web/API/Storage) 对象。可以使用相同的方式操作这些对象，但是操作是独立的。

### 作用域 @@@

localStorage: 在同一个浏览器内，**同源文档**之间共享 localStorage 数据，可以互相读取、覆盖。

sessionStorage: 与 localStorage 一样需要同一浏览器同源文档这一条件。不仅如此，sessionStorage 的作用域还被限定在了窗口中，也就是说，只有同一浏览器、同一窗口的同源文档才能共享数据。

为了更好的理解 `sessionStorage`,我们来看个例子：

+ 例如你在浏览器中打开了两个相同地址的页面 A、B,虽然这两个页面的源完全相同，但是他们还是不能共享数据，因为他们是不同窗口中的。但是如果是一个窗口中，有两个同源的 `iframe` 元素的话，这两个 `iframe` 的 sessionStorage 是可以互通的。

## Storage

不论是 [`Window.sessionStorage`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/sessionStorage) 和 [`Window.localStorage`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/localStorage) 都拥有一下的属性和方法

另外，`Storage` 中的键值对总是以字符串的形式存储。 (需要注意, 和 js 对象相比, 键值对总是以字符串的形式存储意味着数值类型会自动转化为字符串类型).

### Storage.length

返回一个整数，表示存储在 `Storage` 对象中的数据项数量。

### Storage.key()

该方法接受一个数值 n 作为参数，并返回存储中的第 n 个键名。

### Storage.getItem()

该方法接受一个键名作为参数，返回键名对应的值。

### Storage.setItem()

该方法接受一个键名和值作为参数，将会把键值对添加到存储中，如果键名存在，则更新其对应的值。

### Storage.removeItem()

该方法接受一个键名作为参数，并把该键名从存储中删除。

### Storage.clear()

调用该方法会清空存储中的所有键名。

### 注意

只能以字符串形式储存，所以读写都需要 JSON 的两个方法配合 @@@

而且，读取空的键值对，返回的 null，也是字符串形式

### Demo

这里我们通过调用 `localStorage` 来访问一个 `Storage` 对象。

首先，使用 `!localStorage.getItem('bgcolor')` 测试本地存储中是否包含该数据项。

+ 如果包含，则运行 `setStyles()` 函数，该函数使用 `localStorage.getItem()` 来获取数据项，并使用这些值更新页面样式。
+ 如果不包含，我们运行另一个函数 `populateStorage()`，该函数使用 `localStorage.setItem()` 设置数据项，然后运行 `setStyles()`。

```js
if(!localStorage.getItem('bgcolor')) {
  populateStorage();
} else {
  setStyles();
}

function populateStorage() {
  localStorage.setItem('bgcolor', document.getElementById('bgcolor').value);
  localStorage.setItem('font', document.getElementById('font').value);
  localStorage.setItem('image', document.getElementById('image').value);

  setStyles();
}

function setStyles() {
  var currentColor = localStorage.getItem('bgcolor');
  var currentFont = localStorage.getItem('font');
  var currentImage = localStorage.getItem('image');

  document.getElementById('bgcolor').value = currentColor;
  document.getElementById('font').value = currentFont;
  document.getElementById('image').value = currentImage;

  htmlElem.style.backgroundColor = '#' + currentColor;
  pElem.style.fontFamily = currentFont;
  imgElem.setAttribute('src', currentImage);
```

# IndexedDB

**IndexedDB** 是一种低级 API，用于客户端存储**大量结构化数据**(包括, 文件/ blobs)。该 API 使用索引来实现对该数据的高性能搜索。

虽然 [Web Storage](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Storage_API) 对于存储较少量的数据很有用，但对于存储更大量的结构化数据来说，这种方法不太有用。**IndexedDB**提供了一个解决方案。

> **注意**：IndexedDB API 是强大的，但对于简单的情况可能看起来太复杂。如果你更喜欢一个简单的 API，请尝试类库，如 [localForage](https://localforage.github.io/localForage/), [dexie.js](http://www.dexie.org/), 和 [ZangoDB](https://github.com/erikolson186/zangodb)，使 IndexedDB 更方便用户。

## 概念和用法

IndexedDB 是一个事务型数据库系统，类似于基于 SQL 的 RDBMS。 然而不同的是它使用固定列表，IndexedDB 是一个基于 JavaScript 的面向对象的数据库。 IndexedDB 允许您存储和检索用键索引的对象; 可以存储 [structured clone algorithm](https://developer.mozilla.org/en-US/docs/Web/Guide/API/DOM/The_structured_clone_algorithm) 支持的任何对象。 您只需要指定数据库模式，打开与数据库的连接，然后检索和更新一系列事务中的数据。

IndexedDB 分别为同步和异步访问提供了单独的 API 。同步 API 本来是要用于仅供 [Web Workers](https://developer.mozilla.org/en-US/docs/DOM/Worker) 内部使用，但是还没有被任何浏览器所实现。异步 API 在 Web Workers 内部和外部都可以使用。

简单理解，是浏览器内置的一个非关系型数据库

# 储存查看器

存储查看器使你能够查看网页使用的多种存储类型。如今，它能够查看如下存储类型：

- **Cache 缓存** — 使用缓存 API 创建的任何 DOM 缓存
- **Cookies** — 所有页面创建的 cookies 或页面中任何的 iframes。还列出了作为网络呼叫响应的一部分创建的 Cookie，但仅适用于工具打开时发生的响应
- **IndexedDB** — 所有页面创建的 IndexedDB 或或页面中任何的 IndexedDB。其对象存储以及存储在这些对象库中的项目。
- **本地存储**— 所有页面创建*的本地存储*或页面中任何的 iframes。
- **Session 存储**—所有页面创建的 Session 或页面中任何的 iframes。

目前，Storage Inspector 仅为您提供存储的只读视图。 但我们正努力让您在将来的版本中编辑存储内容。

![1554011850615](http-storage/1554011850615.png)

https://developer.mozilla.org/zh-CN/docs/Tools/%E5%AD%98%E5%82%A8%E6%9F%A5%E7%9C%8B%E5%99%A8

https://www.nowcoder.com/test/question/done?tid=22865052&qid=14747#summary

有时需要将网页中的一些数据保存在浏览器端，这样做的好处是，当下次访问页面时，不需要再次向服务器请求数据，直接就可以从本地读取数据。目前常用的有以下几种方法： @@@

**cookie**

cookie 会随着每次 HTTP 请求头信息一起发送，无形中增加了网络流量，另外，cookie 能存储的数据容量有限，根据浏览器类型不同而不同，IE6 大约只能存储 2K。

**Flash ShareObject**

这种方式能能解决上面提到的 cookie 存储的两个弊端，而且能够跨浏览器，应该说是目前最好的本地存储方案。不过，需要在页面中插入一个 Flash，当浏览器没有安装 Flash 控件时就不能用了。所幸的是，没有安装 Flash 的用户极少。

缺点：需要安装 Flash 插件。

**Google Gear**

Google 开发出的一种本地存储技术。

缺点：需要安装 Gear 组件。

**userData**

IE 浏览器可以使用 userData 来存储数据，容量可达到 640K，这种方案是很可靠的，不需要安装额外的插件。缺点：它仅在 IE 下有效。

**sessionStorage**

使用于 Firefox2+ 的火狐浏览器，用这种方式存储的数据仅窗口级别有效，同一个窗口（或者 Tab）页面刷新或者跳转，都能获取到本地存储的数据，当新开窗口或者页面时，原来的数据就失效了。

缺点：IE 不支持、不能实现数据的持久保存。

**globalStorage**

使用于 Firefox2+ 的火狐浏览器，类似于 IE 的 userData。

```
1 //赋值 2 globalStorage[location.hostname]['name'] = 'tugai'; 3 //读取 4 globalStorage[location.hostname]['name']; 5 //删除 6 globalStorage[location.hostname].removeItem('name');
```

缺点：IE 不支持。

**localStorage**

localStorage 是 Web Storage 互联网存储规范中的一部分，现在在 Firefox 3.5、Safari 4 和 IE8 中得到支持。

缺点：低版本浏览器不支持。

结论：

Flash shareobject 是不错的选择，如果你不想在页面上嵌入 Flash，可以结合使用 userData(IE6+) 和 globalStorage(Firefox2+) 和 localStorage(chrome3+) 实现跨浏览器。

# HTTP 缓存 #

## 加速资源

更多地利用缓存资源，可以提高网站的性能和响应速度。为了优化缓存，过期时间设置得尽量长是一种很好的策略。对于定期或者频繁更新的资源，这么做是比较稳妥的，但是对于那些长期不更新的资源会有点问题。这些固定的资源在一定时间内受益于这种长期保持的缓存策略，但一旦要更新就会很困难。特指网页上引入的一些 js/css 文件，当它们变动时需要尽快更新线上资源。

web 开发者发明了一种被 Steve Souders 称之为 `revving` 的技术 [[../../../font-end/primitive/es/es-number/1]](https://www.stevesouders.com/blog/2008/08/23/revving-filenames-dont-use-querystring/) 。不频繁更新的文件会使用特定的命名方式：在 URL 后面（通常是文件名后面）会加上版本号。加上版本号后的资源就被视作一个完全新的独立的资源，同时拥有一年甚至更长的缓存过期时长。但是这么做也存在一个弊端，所有引用这个资源的地方都需要更新链接。web 开发者们通常会采用自动化构建工具在实际工作中完成这些琐碎的工作。当低频更新的资源（js/css）变动了，只用在高频变动的资源文件（html）里做入口的改动。

这种方法还有一个好处：同时更新两个缓存资源不会造成部分缓存先更新而引起新旧文件内容不一致。对于互相有依赖关系的 css 和 js 文件，避免这种不一致性是非常重要的。

![img](http-storage/HTTPRevved.png)

加在加速文件后面的版本号不一定是一个正式的版本号字符串，如 1.1.3 这样或者其他固定自增的版本数。它可以是任何防止缓存碰撞的标记例如 hash 或者时间戳。

## 带 Vary 头的响应

[`Vary`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Vary) HTTP 响应头决定了对于后续的请求头，如何判断是请求一个新的资源还是使用缓存的文件。

当缓存服务器收到一个请求，只有当前的请求和原始（缓存）的请求头跟缓存的响应头里的 Vary 都匹配，才能使用缓存的响应。

使用 vary 头有利于内容服务的动态多样性。例如，使用 Vary: User-Agent 头，缓存服务器需要通过 UA 判断是否使用缓存的页面。如果需要区分移动端和桌面端的展示内容，利用这种方式就能避免在不同的终端展示错误的布局。另外，它可以帮助 Google 或者其他搜索引擎更好地发现页面的移动版本，并且告诉搜索引擎没有引入 [Cloaking](https://en.wikipedia.org/wiki/Cloaking)。

![The Vary header leads cache to use more HTTP headers as key for the cache.](http-storage/HTTPVary.png)

```js
Vary: User-Agent
```

因为移动版和桌面的客户端的请求头中的 User-Agent 不同， 缓存服务器不会错误地把移动端的内容输出到桌面端到用户。

# HTTP 缓存

## 概念

缓存是一种保存资源副本并在下次请求时直接使用该副本的技术

很多开发者习惯把 cookie、webStorage 以及 IndexedDB 存储的数据也称之为缓存，理由是都是保存在客户端的数据，没有什么区别。其实这是不严谨的，cookie 的存在更多的是为了让服务端区别用户，webStorage 和 IndexedDB 则更多用在保存具体的数据和在客户端存储大量结构化数据 (文件/blobs) 上面。

实际上所谓的缓存只有一种——它是**请求资源的副本**

HTTP 协议里定义了很多关于缓存的请求和响应字段，我们通过这些字段控制缓存的建立和删除

### 缓存的好处

缓解服务器压力 (不用每次去请求资源)；

提升性能 (打开本地资源速度当然比请求回来再打开要快得多)；

减少带宽消耗 (我相信你可以理解)；

## 缓存的种类

缓存在**宏观上**可以分成两类：**私有缓存**和**共享缓存**

+ 共享缓存就是那些能被**各级代理**缓存的缓存
+ 私有缓存就是**用户专享**的，各级代理不能缓存的缓存

**微观上**可以分下面三类：浏览器缓存、代理服务器缓存、网关缓存

### 浏览器缓存

我相信只要你经常使用某个浏览器 (Chrome,Firefox,IE 等)，肯定知道这些浏览器在设置里面都是有个清除缓存功能，这个功能存在的作用就是删除存储在你本地磁盘上资源副本，也就是清除缓存。

![1568532699818](http-storage/1568532699818.png)

缓存存在的意义就是当用户点击 back 按钮或是再次去访问某个页面的时候能够更快的响应。尤其是在多页应用的网站中，如果你在多个页面使用了一张相同的图片，那么缓存这张图片就变得特别的有用。

memory cache 关闭页面时清除

disk cache 一般不会清除，除非：

+ 主动清理
+ 缓存过期

### 代理服务器缓存

代理服务器缓存原理和浏览器端类似，但规模要大得多，因为是为成千上万的用户提供缓存机制，大公司和大型的 ISP 提供商通常会将它们设立在防火墙上或是作为一个独立的设备来运营。(**下文如果没有特殊说明,所有提到的缓存服务器都是指代理服务器。**)

由于缓存服务器不是客户端或是源服务器的一部分，它们存在于网络中，请求路由必须经过它们才会生效，所以实际上你可以去手动设置浏览器的代理，或是通过一个中间服务器来进行转发，这样用户自然就察觉不到代理服务器的存在了。

代理服务器缓存就是一个共享缓存，不只为一个用户服务，经常为大量用户使用，因此在减少响应时间和带宽使用方面很有效：因为同一个缓存可能会被重用多次。

### 网关缓存

也被称为**代理缓存或反向代理缓存**，网关也是一个中间服务器，网关缓存一般是网站管理员自己部署，从让网站拥有更好的性能。

CDNS(网络内容分发商) 分布网关缓存到整个（或部分）互联网上，并出售缓存服务给需要的网站，比如国内的七牛云、又拍云都有这种服务。

### 数据库缓存

数据库缓存是指当我们的应用极其复杂，表自然也很繁杂，我们必须进行频繁的进行数据库查询，这样可能导致数据库不堪重负，一个好的办法就是将查询后的数据放到内存中，下一次查询直接从内存中取就好了。关于数据库缓存本篇不会展开。

## 浏览器的缓存策略

### **缓存的目标**

缓存的关键主要包括 request method 和目标 URI（一般只有 GET 请求才会被缓存）。

普遍的缓存案例:

+ 一个检索请求的成功响应: 对于 **GET 请求**，响应状态码为：200，则表示为成功。一个包含例如 HTML 文档，图片，或者文件的响应；
+ 不变的重定向: 响应状态码：**301**；
+ 可用缓存响应：响应状态码：**304**，这个存在疑问，Chrome 会缓存 304 中的缓存设置，Firefox 则不会
+ 错误响应: 响应状态码：**404** 的一个页面；
+ 不完全的响应: 响应状态码 **206**，只返回局部的信息；
+ 除了 GET 请求外，如果匹配到作为一个已被定义的**cache 键名**的响应；

**浏览器对于缓存的处理是根据第一次请求资源时返回的响应头来确定的。**

那么浏览器怎么确定一个资源该不该缓存，如何去缓存呢❓响应头！响应头！响应头！重要的事情说三遍。@@@

> 那么是看具体哪个字段呢？Cache-Control

我们看：

```
Age:23146
Cache-Control:max-age=2592000
Date:Tue, 28 Nov 2017 12:26:41 GMT
ETag:W/"5a1cf09a-63c6"
Expires:Thu, 28 Dec 2017 05:27:45 GMT
Last-Modified:Tue, 28 Nov 2017 05:14:02 GMT
Vary:Accept-Encoding
```

### 强缓存阶段

**以上请求头来自百度首页某个**CSS 文件的响应头。去除了一些和缓存无关的字段，只保留了以上部分。我们来分析下：

+ `Expires` 是**HTTP/1.0**中的定义缓存的字段，它规定了缓存过期的一个**绝对时间**。
+ `Cache-Control:max-age=2592000` 是**HTTP/1.1**定义的关于缓存的字段，它规定了缓存过期的一个**相对时间**。

这就是**强缓存阶段**，当浏览器再次试图访问这个 CSS 文件，发现有这个文件的缓存，那么就根据上一次的响应判断是否过期，**如果没过期**，使用缓存，加载文件，OVER！

> 浏览器缓存该资源和发现该资源的缓存，都是**自发**的，只有判断是否可用的过程，受到字段的限制 @@@
>
> 在有效时间内，不会再请求服务器（请求都不会发起），直接从浏览器本地缓存中获取资源。

Firefox 浏览器表现为：一个灰色的 200 状态码。

Chrome 浏览器状态码表现为：200 (from disk cache) 或是 200 OK (from memory cache)

> **多说一点：**关于缓存是从磁盘中获取还是从内存中获取，查找了很多资料，得出了一个较为可信的结论：Chrome 会根据本地内存的使用率来决定缓存存放在哪，如果内存使用率很高，放在磁盘里面，内存的使用率很高会暂时放在内存里面。这就可以比较合理的解释了为什么同一个资源有时是 from memory cache 有时是 from disk cache 的问题了。
>
> 在浏览器接收到服务器响应后，会检测响应头部（Header），如果有 Etag 字段，那么浏览器就会将本次缓存写入硬盘中。

### 协商缓存阶段

那么当这个 CSS 文件**过期了**或者**没有设置**强缓存的字段?`ETag` 和 `Last-Modified` 就该闪亮登场了

先说 `Last-Modified`，这个字段是文件最后一次修改的时间；

`ETag` 是对文件的一个标记，具体生成方式 HTTP 并没有给出一个明确的方式，所以理论上只要不会重复生成方式无所谓，比如对资源内容使用抗碰撞散列函数，使用最近修改的时间戳的哈希值，甚至只是一个版本号。

利用这两个字段浏览器可以进入**协商缓存阶段**： @@@

1. 当浏览器再次试图访问这个 CSS 文件，无论是否变化，是否过期都会发起请求，而且会在本次请求的**请求头**里携带 `If-Moified-Since` 和 `If-None-Match` 这两个字段
2. 服务器通过这两个字段来判断资源是否有修改，如果有修改则返回状态码**200 和新的内容**，如果没有修改返回状态码**304**
3. 浏览器收到**200 状态码**，该咋处理就咋处理 (相当于首次访问这个文件了)，发现返回**304**，于是知道了本地缓存**虽然过期但仍然可以用**，于是加载本地缓存。然后根据新的返回的响应头来**重新设置缓存**

> 第三步有所差异，不同浏览器的处理是不同的，chrome 会为 304 设置缓存，firefox 则不会

具体两个字段携带的内容如下 (分别和上面的 `Last-Modified`、`ETag` 携带的值对应)：@@@

```
If-Moified-Since: Tue, 28 Nov 2017 05:14:02 GMT
If-None-Match: W/"5a1cf09a-63c6"
```

ETag 解决了 Last-Modified 使用时可能出现的资源的时间戳变了但内容没变及如果再一秒钟以内资源变化但 Last-Modified 没变的问题，ETag 更加稳妥。 @@@

### 启发式缓存阶段

我们把上面的响应头改下：

```
Age:23146
Cache-Control: public
Date:Tue, 28 Nov 2017 12:26:41 GMT
Last-Modified:Tue, 28 Nov 2017 05:14:02 GMT
Vary:Accept-Encoding
```

发现没？浏览器用来确定缓存过期时间的字段一个都没有！那该怎么办？有人可能会说下次请求直接进入协商缓存阶段，携带 `If-Moified-Since` 呗，不是的，浏览器还有个**启发式缓存阶段**

**根据响应头中 2 个时间字段 Date 和 Last-Modified 之间的时间差值，取其值的 10% 作为缓存时间周期**

> 因为是否缓存的判断标准是 Cache-Control 字段，而不是是否存在缓存时间相关字段 @@@

这就是启发式缓存阶段。这个阶段很容让人忽视，但实际上每时每刻都在发挥着作用。所以在今后的开发过程中如果遇到那种**默认缓存**的坑，浏览器只是在遵循启发式缓存协议而已。

### 优先级

1. 强缓存和对比缓存同时存在，如果强缓存还在生效期则强制缓存覆盖对比缓存，对比缓存不生效；如果强缓存不在有效期，对比缓存生效。即：强缓存优先级 > 对比缓存优先级
2. 强缓存 expires 和 cache-control 同时存在时，则 cache-control 会覆盖 expires，expires 无论有没有过期，都无效。 即：cache-control 优先级 > expires 优先级。
3. 对比缓存 Etag 和 Last-Modified 同时存在时，则 Etag 会覆盖 Last-Modified，Last-Modified 不会生效。即：ETag 优先级 > Last-Modified 优先级。

### Last-Modified 与 ETag @@@

Last-Modified 的最后修改时间，只能精确到秒，如果在短时间内被多次修改，就会出现失误

当某些文件是定期生成的，但是他的内容并没有改变，Last-Modified 同样会出现失误

服务器没有准确获取文件修改时间，或者是代理服务器时间不一致等情况

Etag 是服务器自动生成或者由开发者生成的对应资源在服务器端的唯一标识符，能够更加准确的控制缓存。Last-Modified 与 ETag 是可以一起使用的，服务器会优先验证 ETag，一致的情况下，才会继续比对 Last-Modified，最后才决定是否返回 304。

**yahoo 的 Yslow 法则中则提示谨慎设置 Etag**

(一句话：集群情况下，同一个文件，不同的服务器生成的 ETag 不一样，ETag 大概率匹配不上，如果依靠这个来决定缓存，大概率无法 304，而是 200，然后返回全部数据；这时，建议是用 last-modified 来验证缓存)

### 流程

强缓存的状态码是 200 @@@

![image-20200505144921077](http-storage/image-20200505144921077.png)

### 哪些不能被缓存

Cache-Control:no-store;

Cache-Control:max-age=0;

post 请求无法被缓存 @@@

## 缓存相关的首部字段

HTTP 报文是什么呢？就是 HTTP 报文，这是一个概念，主要由以下两部分构成：

1. **首部 (header)**：包含了很多字段，比如：cookie、缓存、报文大小、报文格式等等）；
2. **主体 (body)**：HTTP 请求真正要传输的部分，比如：一个 HTML 文档，一个 js 文件；

以上我们知道浏览器对于缓存的处理过程，也简单的提到了几个相关的字段。接下来我们具体看下这几个字段：

### 通用首部字段

| 字段名称      | 说明                                                |
| :------------ | :-------------------------------------------------- |
| Cache-Control | 控制缓存具体的行为                                  |
| Pragma        | HTTP1.0 时的遗留字段，当值为 "no-cache" 时强制验证缓存 |
| Date          | 创建报文的日期时间 (启发式缓存阶段会用到这个字段)    |

### 响应首部字段

| 字段名称 | 说明                                                      |
| -------- | :-------------------------------------------------------- |
| ETag     | 服务器生成资源的唯一标识                                  |
| Vary     | 代理服务器缓存的管理信息                                  |
| Age      | 资源在缓存代理中存贮的时长 (取决于 max-age 和 s-maxage 的大小) |

### 请求首部字段

| 字段名称            | 说明                                                         |
| :------------------ | :----------------------------------------------------------- |
| If-Match            | 条件请求，携带上一次响应中资源的 ETag，服务器根据这个字段判断文件是否有新的修改 |
| If-None-Match       | 和 If-Match 作用相反，服务器根据这个字段判断文件是否有新的修改 |
| If-Modified-Since   | 比较资源前后两次访问最后的修改时间是否一致，携带上一次响应中资源的 Last-Modified |
| If-Unmodified-Since | 比较资源前后两次访问最后的修改时间是否一致                   |

### 实体首部字段

| 字段名称      | 说明                             |
| ------------- | -------------------------------- |
| Expires       | 告知客户端资源缓存失效的绝对时间 |
| Last-Modified | 资源最后一次修改的时间           |

## 浏览器缓存控制

[HTTP/1.1](https://link.juejin.im?target=https%3A%2F%2Fwww.w3.org%2FProtocols%2Frfc2616%2Frfc2616-sec14.html) 一共规范了 47 种首部字段，而和缓存相关的就有以上 12 个之多。接下来的两个小节会一个一个介绍给大家。

### Cache-Control

通用首部字段

通过 cache-control 的指令可以控制告诉客户端或是服务器如何处理缓存。这也是 11 个字段中指令最多的一个

#### **请求指令**：

| 指令            | 参数               | 说明                                      |
| --------------- | ------------------ | ----------------------------------------- |
| no-cache        | 无                 | 强制源服务器再次验证                      |
| no-store        | 无                 | 不缓存请求或是响应的任何内容              |
| max-age=[秒]    | 缓存时长，单位是秒 | 缓存的时长，也是**响应的**最大的 Age 值 @@@ |
| min-fresh=[秒]  | 必需               | 期望在指定时间内响应仍然有效              |
| no-transform    | 无                 | 代理不可更改媒体类型                      |
| only-if-cached  | 无                 | 从缓存获取                                |
| cache-extension | -                  | 新的指令标记 (token)                       |

#### **响应指令**：

| 指令             | 参数               | 说明                                           |
| ---------------- | ------------------ | ---------------------------------------------- |
| public           | 无                 | 任意一方都能缓存该资源 (客户端、代理服务器等)   |
| private          | 可省略             | 只能特定用户缓存该资源                         |
| no-cache         | 可省略             | 缓存前必须先确认其有效性                       |
| no-store         | 无                 | 不缓存请求或响应的任何内容                     |
| no-transform     | 无                 | 代理不可更改媒体类型                           |
| must-revalidate  | 无                 | 可缓存但必须再向源服务器进确认                 |
| proxy-revalidate | 无                 | 要求中间缓存服务器对缓存的响应有效性再进行确认 |
| max-age=[秒]     | 缓存时长，单位是秒 | 缓存的时长，也是响应的最大的 Age 值              |
| s-maxage=[秒]    | 必需               | 公共缓存服务器响应的最大 Age 值                  |
| cache-extension  | -                  | 新指令标记 (token                               |

请注意 no-cache 指令很多人误以为是不缓存，这是不准确的，no-cache 的意思是可以缓存，但每次用应该去想服务器验证缓存是否可用。no-store 才是不缓存内容。另外部分指令也可以组合使用，比如： @@@

```
Cache-Control: max-age=100, must-revalidate, public
```

上面指令的意思是缓存的有效时间为 100 秒，之后访问需要向源服务器发送请求验证，此缓存可被代理服务器和客户端缓存。

请求 10M 的文件，使用 no-cache，就不需要每次都传递这么大的文件

### Pragma

通用首部字段

这是 HTTP/1.0 里面的一个字段，但**优先级很高**，测试发现，Chrome 和 Firefox 中 Pragma 的优先级高于 Cache-Control 和 Expires，为了向下兼容，这个字段依然发挥着它的作用。一般可能我们会这么用：

```
<meta http-equiv="Pragma" content="no-cache">
```

Pragma 属于通用首部字段，在客户端上使用时，常规要求我们往 html 上加上上面这段 meta 元标签（而且可能还得 [做些hack放到body后面去](https://link.juejin.im?target=https%3A%2F%2Fsupport.microsoft.com%2Fzh-cn%2Fkb%2F222064)。

事实上这种禁用缓存的形式用处很有限：

1. **仅有 IE 才能识别这段 meta 标签含义**，其它主流浏览器仅能识别 `Cache-Control: no-store` 的 meta 标签 (见 [出处](https://link.juejin.im?target=http%3A%2F%2Fsecurityevaluators.com%2Fknowledge%2Fcase_studies%2Fcaching%2F))
2. 在 IE 中识别到该 meta 标签含义，并不一定会在请求字段加上 Pragma，但的确会让当前页面每次都发新请求 (仅限页面，页面上的资源则不受影响)。——[浅谈浏览器http的缓存机制](https://link.juejin.im?target=http%3A%2F%2Fwww.cnblogs.com%2Fvajoy%2Fp%2F5341664.html)

读者可以自行拷贝后面模拟服务端决策的代码进行测试。

**服务端响应添加 'Pragma': 'no-cache'，浏览器表现行为和强制刷新类似。**

### Expires

实体首部字段

这又是一个 HTTP/1.0 的字段，上面也说过了定义的是缓存到期的**绝对时间。**

同样，我们也可以在 html 文件里直接使用:

```
<meta http-equiv="expires" content="Thu, 30 Nov 2017 11:17:26 GMT">
```

如果设置的是已经过去的时间会怎样呢？YES！！！则刷新页面会重新发送请求。

**Pragma 禁用缓存，如果又给 Expires 定义一个还未到期的时间，那么 Pragma 字段的优先级会更高。**

Expires 有一个很大的弊端，就是它返回的是服务器的时间，但判断的时候用的却是客户端的时间，这就导致 Expires 很被动，因为用户有可能改变客户端的时间，导致缓存时间判断出错，这也是引入 `Cache-Control:max-age` 指令的原因之一。 @@@

### Last-Midified

实体首部字段

接下来这几个字段都是**校验字段**，或者说是在**协商缓存阶段**发挥作用的字段。第一个就是 Last-modified，这个字段不光协商缓存起作用，在启发式缓存阶段同样起到至关重要的作用。

在浏览器第一次请求某一个 URL 时，服务器端的返回状态码会是 200，**响应**的实体内容是客户端请求的资源，同时有一个 `Last-Modified` 的属性标记此文件在**服务器端**最后被修改的时间。like this:

```
Last-Modified : Fri , 12 May 2006 18:53:33 GMT
```

### If-Modified-Since

请求字段

当浏览器第二次请求这个 URL 的时候，根据 HTTP 协议规定，浏览器会把第一次 `Last-Modified` 的值存储在 `If-Modified-Since` 里面发送给服务端来验证资源有没有修改。like this:

```
If-Modified-Since : Fri , 12 May 2006 18:53:33 GMT
```

服务端通过 `If-Modified-Since` 字段来判断在这两次访问期间资源有没有被修改过，从而决定是否返回完整的资源。如果有修改正常返回资源，状态码 200，如果没有修改只返回响应头，状态码 304，告知浏览器资源的本地缓存还可用。

**用途：**验证本地缓存是否可用

### If-Unmodified-Since

请求字段

这个字段字面意思和 `If-Modified-Since` 相反，但处理方式并不是相反的。如果文件在两次访问期间没有被修改则返回 200 和资源，如果文件修改了则返回状态码 412(预处理错误)。

**用途：**

- 与含有 `If-Range` 消息头的范围请求搭配使用，实现断点续传的功能，即如果资源没修改继续下载，如果资源修改了，续传的意义就没有了。
- POST、PUT 请求中，优化并发控制，即当多用户编辑用一份文档的时候，如果服务器的资源已经被修改，那么在对其作出编辑会被拒绝提交。 @@@

### ETag

响应字段

`Last-Modified` 有几个缺点：没法准确的判断资源是否真的修改了，比如某个文件在 1 秒内频繁更改了多次，根据 Last-Modified 的时间 (单位是秒) 是判断不出来的，再比如，某个资源只是修改了，但实际内容并没有发生变化，Last-Modified 也无法判断出来，因此在 HTTP/1.1 中还推出了 `ETag` 这个字段

服务器可以通过某种自定的算法对资源生成一个唯一的标识 (比如 md5 标识)，然后在浏览器第一次请求某一个 URL 时把这个标识放到响应头传到客户端。服务器端的返回状态会是 200。

```js
ETag: abc-123456
```

ETag 的值有可能包含一个 W/ 前缀，来提示应该采用弱比较算法（这个是画蛇添足，因为 If-None-Match 用且仅用这一算法）。

### If-None-Match

请求字段

If-None-Match 和 If-Modified-Since 同时存在的时候 If-None-Match 优先级更高。

当浏览器第二次请求这个 URL 的时候，根据 HTTP 协议规定，浏览器回把第一次 ETag 的值存储在 If-None-Match 里面发送给服务端来验证资源有没有修改。like this:

```js
If-None-Match: abc-123456
```

Get 请求中，当且仅当服务器上没有任何资源的 ETag 属性值与这个首部中列出的相匹配的时候，服务器端会才返回所请求的资源，响应码为**200**。如果没有资源的 ETag 值相匹配，那么返回**304**状态码。

**POST、PUT 等请求改变文件的请求，如果没有资源的 ETag 值相匹配，那么返回 412 状态码。**

### If-Match

请求字段

在请求方法为 GET) 和 HEAD 的情况下，服务器仅在请求的资源满足此首部列出的 `ETag` 之一时才会返回资源。而对于 PUT 或其他非安全方法来说，只有在满足条件的情况下才可以将资源上传。

**用途：**

- For GET 和 HEAD 方法，搭配 Range 首部使用，可以用来保证新请求的范围与之前请求的范围是对同一份资源的请求。如果 ETag 无法匹配，那么需要返回 **416**（范围请求无法满足) 响应。
- 对于其他方法来说，尤其是 PUT, `If-Match` 首部可以用来避免更新丢失问题。它可以用来检测用户想要上传的不会覆盖获取原始资源之后做出的更新。如果请求的条件不满足，那么需要返回**412**(预处理错误) 响应。

+ 当然和 `Last-Modified` 相比，ETag 也有自己的缺点，比如由于需要对资源进行生成标识，性能方面就势必有所牺牲。

### **关于强校验和弱校验：**

| ETag 1 | ETag 2 | Strong Comparison | Weak Comparison |
| ------ | ------ | ----------------- | --------------- |
| W/"1"  | W/"1"  | no match          | match           |
| W/"1"  | W/"2"  | no match          | no match        |
| W/"1"  | "1"    | no match          | match           |
| "1"    | "1"    | match             | match           |

## 服务端缓存控制

当 `Expires` 和 `Cache-Control:max-age=xxx` 同时存在的时候取决于缓存服务器应用的 HTTP 版本。应用 HTTP/1.1 版本的服务器会优先处理 max-age，忽略 Expires，而应用 HTTP/1.0 版本的缓存服务器则会优先处理 Expires 而忽略 max-age。接下来看下和缓存服务器相关的两个字段。

### Vary #

响应字段

Vary 用来做什么的呢？试想这么一个场景：在某个网页中网站提供给移动端的内容是不同的，怎么让缓存服务器区分移动端和 PC 端呢？不知道你是否注意，浏览器在每次请求都会携带 UA 字段来表明来源，所以我们可以利用 `User-Agent` 字段来区分不同的客户端，用法如下：

```
Vary: User-Agent
```

再比如，源服务器启用了 gzip 压缩，但用户使用了比较旧的浏览器，不支持压缩，缓存服务器如何返回？就可以这么设定：

```
Vary: Accept-Encoding
```

当然，也可以这么用：

```
Vary: User-Agent, Accept-Encoding
```

这意味着缓存服务器以 `User-Agent` 和 `Accept-Encoding` 两个请求首部字段来区分缓存版本。根据请求头里的这两个字段来决定返回给客户端什么内容。

### Age

响应字段

这个字段说的是资源在缓存服务器存在的时长，前面也说了 `Cache-Control: max-age=[秒]` 就是 Age 的最大值。@@@

这个字段存在的意义是什么呢？用来区分请求的资源来自源服务器还是缓存服务器的缓存的。

但得结合另一个字段来进行判断，就是 Date，Date 是报文创建的时间。

### Date

通用字段

如果按 F5 频繁刷新发现响应里的 Date 没有改变，就说明**命中了缓存服务器的缓存**

以下面的一个响应为例子：

```
Accept-Ranges: bytes
Age: 1016859
Cache-Control: max-age=2592000
Content-Length: 14119
Content-Type: image/png
Date: Fri, 01 Dec 2017 12:27:25 GMT
ETag: "5912bfd0-3727"
Expires: Tue, 19 Dec 2017 17:59:46 GMT
Last-Modified: Wed, 10 May 2017 07:22:56 GMT
Ohc-Response-Time: 1 0 0 0 0 0
Server: bfe/1.0.8.13-sslpool-patch
```

如上图来自百度首页某个图片的响应字段。我们可以看到 Age=1016859，说明这个资源已经在缓存服务器存在了 1016859 秒。如果文件被修改或替换，Age 会重新由 0 开始累计。 #

> Age 消息头的值通常接近于 0。表示此消息对象刚刚从原始服务器获取不久；其他的值则是表示代理服务器当前的系统时间与此应答消息中的通用消息头 Date 的值之差。

上面这个结论归结为一个等式就是：

```
静态资源Age + 静态资源Date = 原服务端Date
```

## 用户操作行为对缓存的影响

搜索了很久有没有关于这方面的权威总结，最后竟然在百度百科找到了也是很惊讶，我自己加了一条用户强制刷新操作浏览器的反应。

强制刷新，window 下是 `Ctrl+F5`,mac 下就是 `command+shift+R` 操作了。:relieved:

| 操作           |                             说明                             |
| :------------- | :----------------------------------------------------------: |
| 打开新窗口     | 如果指定 cache-control 的值为 private、no-cache、must-revalidate,那么打开新窗口访问时都会重新访问服务器。而如果指定了 max-age 值,那么在此值内的时间里就不会重新访问服务器,例如：Cache-control: max-age=5 表示当访问此网页后的 5 秒内不会去再次访问服务器. |
| 在地址栏回车   | 如果值为 private 或 must-revalidate,则只有第一次访问时会访问服务器,以后就不再访问。如果值为 no-cache,那么每次都会访问。如果值为 max-age,则在过期之前不会重复访问。 |
| 按后退按扭     | 如果值为 private、must-revalidate、max-age,则不会重访问,而如果为 no-cache,则每次都重复访问. |
| 按刷新按扭     | 无论为何值,都会重复访问.（可能返回状态码：200、304，这个不同浏览器处理是不一样的，FireFox 正常，Chrome 则会启用缓存 (200 from cache)） |
| 按强制刷新按钮 |             当做首次进入重新请求 (返回状态码 200)              |

如果想在浏览器点击“刷新”按钮的时候不让浏览器去发新的验证请求呢？办法找到一个，知乎上面一个回答，在页面加载完毕后通过脚本动态地添加资源：

```
$(window).load(function() {
  	var bg='http://img.infinitynewtab.com/wallpaper/100.jpg';
  	setTimeout(function() {
    	$('#bgOut').css('background-image', 'url('+bg+')');
  	},0);
});
```

## HTML5 的缓存

这部分准备的说应该叫离线存储。现在比较普遍用的是 `Appcache`，但 `Appcache` 已经从 web 标准移除了，在可预见的未来里，`ServiceWorker` 可能会是一个比较适合的解决方案。

### Appcache

这是 HTML5 的一个新特性，通过离线存储达到用户在没有网络连接的情况下也能访问页面的功能。离线状态下即使用户点击刷新都能正常加载文档。

使用方法如下，在 HTML 文件中引入 `appcache` 文件:

```
<!DOCTYPE html>
<html manifest="manifest.appcache">
<head>
  <meta charset="UTF-8">
  <title>***</title>
</head>
<body>
  <div id="root"></div>
</body>
</html>
```

web 应用中的 `manifest` 特性可以指定为缓存清单文件的相对路径或一个绝对 URL(绝对 URL 必须与应用同源)。缓存清单文件可以使用任意扩展名，但传输它的 MIME 类型必须为 `text/cache-manifest。`

> 注意：在 Apache 服务器上，若要设置适用于清单 (.appcache) 文件的 MIME 类型，可以向根目录或应用的同级目录下的一个 .htaccess 文件中增加 `AddType text/cache-manifest .appcache` 。

```
CACHE MANIFEST
# 注释：需要缓存的文件，无论在线与否，均从缓存里读取
# v1 2017-11-30
# This is another comment
/static/logo.png

# 注释：不缓存的文件，始终从网络获取
NETWORK:
example.js

# 注释：获取不到资源时的备选路径，如index.html访问失败，则返回404页面
FALLBACK:
index.html 404.html
```

上面就是一个完整的缓存清单文件的示例。

**注意：**主页一定会被缓存起来的，因为 AppCache 主要是用来做离线应用的，如果主页不缓存就无法离线查看了，因此把 index.html 添加到 NETWORK 中是不起效果的。

实际上这个特性已经 web 标准中删除，但现在为止还有很多浏览器支持它，所以这里提一下。

你可以用最新的 Firefox(版本 57.0.1) 测试下，控制台会有这么一行字👉：

> 程序缓存 API（AppCache）已不赞成使用，几天后将被移除。需离线支持请尝试使用 Service Worker。

最新 Chrome(版本 62.0.3202.94) 倒是没有这个警告。🐻

`AppCache` 之所以不受待见我想了下面几个原因：

1. 一旦使用了 manifest 后，没办法清空这些缓存，只能更新缓存，或者得用户自己去清空浏览器的缓存；
2. 假如更新的资源中有一个资源更新失败了，那么所有的资源就会全部更新失败，将用回上一版本的缓存；
3. 主页会被强制缓存 (使用了 manifest 的页面)，并且无法清除；
4. appache 文件可能会无法被及时更新，因为各大浏览器对于 appcache 文件的处理方式不同；
5. 以上几个弊端一旦出问题，会让用户抓狂更会让开发者抓狂！

### Service Worker

Service worker 还是一个实验性的功能，线上环境不推荐使用。这里大概介绍一下。

Service worker 本质上充当 Web 应用程序与浏览器之间的代理服务器。

**首先讲个小故事：**

我们都知道浏览器的 js 引擎处理 js 是单线程的，它就好像一个大 Boss 高高在上，同一个时间它只做一个事情 (就是那么傲娇)，基于这个弊端，**W3C**(HR) 给大 Boss 招聘了一个秘书 (`web worker`)，大 Boss 可以把琐碎的事情交给秘书 `web worker` 去做，做完了发个微信 (`postMessage`) 通知大 Boss，大 Boss 通过 `onmessage` 来获取秘书 `web worker` 做的事情的结果。傍晚时分，下班时间到！大 Boss 回家哄儿子了，秘书也出去约会去了，没人加班了！这怎么行！**W3C**(HR) 又提出了招个**程序🐵**的想法的想法，OK，`Service Worker` 应聘成功！于是，**程序🙈**就坚持在工作岗位上了，从此开启没完没了的加班之路。

**总的来说这只猿的工作是这样的：**

- 后台数据同步
- 响应来自其它源的资源请求
- 集中接收计算成本高的数据更新，比如地理位置和陀螺仪信息，这样多个页面就可以利用同一组数据
- 在客户端进行 CoffeeScript，LESS，CJS/AMD 等模块编译和依赖管理（用于开发目的）
- 后台服务钩子
- 自定义模板用于特定 URL 模式
- 性能增强，比如预取用户可能需要的资源

  ——[Service Worker API](https://link.juejin.im?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FService_Worker_API)

+ 注意：Service workers 之所以优于以前同类尝试（如上面提到的 AppCache)），是因为它们无法支持当操作出错时终止操作。Service workers 可以更细致地控制每一件事情。如何控制的呢？
+ Service workers 利用了 ES6 中比较重要的特性 Promise，并且在拦截请求的时候使用的是新的 fetch API，之所以使用 fetch 就是因为 fetch 返回的是 Promise 对象。可以说 Service workers 重要组成部分就是三块：事件、Promise 和 Fetch 请求。OK，talk is cheap，show you the code。🤓
+ 首先我们看下 app.js 文件：告诉浏览器注册某个 JavaScript 文件为 service worker，检查 service worker API 是否可用，如果可用就注册 service worker：

  ```js
  //使用 ServiceWorkerContainer.register()方法首次注册service worker。
  if (navigator.serviceWorker) {
    	navigator.serviceWorker.register('./sw.js', {scope: './'})
        	.then(function (registration) {
            	console.log(registration);
        	})
        	.catch(function (e) {
            	console.error(e);
        	});
  } else {
    	console.log('该浏览器不支持Service Worker');
  }
  ```

+ 再来看看具体作为 service worker 的文件 sw.js，例子如下：

  ```js
  const CACHE_VERSION = 'v1'; // 缓存文件的版本
  const CACHE_FILES = [ // 需要缓存的文件
  	'./test.js',
  	'./app.js',
  	'https://code.jquery.com/jquery-3.0.0.min.js'
  ];
  
  self.addEventListener('install', function (event) { // 监听worker的install事件
      event.waitUntil( // 延迟install事件直到缓存初始化完成
          caches.open(CACHE_VERSION)
  		.then(function (cache) {
  			console.log('缓存打开');
  			return cache.addAll(CACHE_FILES);
  		})
      );
  });
  
  self.addEventListener('activate', function(event) {// 监听worker的activate事件
      event.waitUntil(// 延迟activate事件直到
          caches.keys().then(function(keys) {
              return Promise.all(keys.map(function(key, i){
                  if(key !== CACHE_VERSION){
                      return caches.delete(keys[i]); // 清除旧版本缓存
                  }
              }))
          })
      )
  });
  
  self.addEventListener('fetch', function(event) { // 截取页面的资源请求
      event.respondWith(
          caches.match(event.request).then(function(res) { // 判断缓存是否命中
              if (res) { // 返回缓存中的资源
                  return res;
              }
              _request(event); // 执行请求备份操作
          })
      )
  });
  
  function _request(event) {
      var url = event.request.clone();
      return fetch(url).then(function(res) {// 使用fetch请求线上资源
          // 错误判断
          if (!res || res.status !== 200 || res.type !== 'basic') {
              return res;
          }
  
          var response = res.clone(); // 创建了一个响应对象的克隆,储藏在一个单独的变量中
  
          caches.open(CACHE_VERSION).then(function(cache) {// 缓存从线上获取的资源
              cache.put(event.request, response);
          });
          return res;
      })
  }
  ```

+ 清除一个 Service Worker 也很简单：

  ```js
  if ('serviceWorker' in navigator) {
    navigator.serviceWorker.register('/sw.js', {scope: './'}).then(function(registration) {
      // registration worked
      console.log('Registration succeeded.');
      registration.unregister().then(function(boolean) {
        // if boolean = true, unregister is successful
      });
    }).catch(function(error) {
      // registration failed
      console.log('Registration failed with ' + error);
    });
  };
  复制代码
  ```

相对 AppCache 来说，Service Worker 的 API 增多了不少，用法也更复杂了些，但看得出 Service Worker 才是未来，对于 web app 来说，更是如虎添翼。现在支持 Service Worker 的浏览器除了 Chrome 和 Firefox，最近新添一个生力军——Safari 也支持 Service Worker 了。期待它在未来大放异彩吧。🤗

## 模拟实现服务端决策

如下，使用 node 原生代码简单的模拟下服务器发送响应的过程，包括对于协商缓存的处理过程：

```js
var http = require('http');
var fs = require('fs');
var url = require('url');

process.env.TZ = 'Europe/London';

let tag = '123456';

http.createServer( function (request, response) {  

   var pathname = url.parse(request.url).pathname;

   	console.log("Request for " + pathname + " received.");
   	const fileMap = {
	   'js': 'application/javascript; charset=utf-8',
	   'html': 'text/html',
	   'png': 'image/png',
	   'jpg': 'image/jpeg',
	   'gif': 'image/gif',
	   'ico': 'image/*',
       'appcache': 'text/cache-manifest'
   	}
   	fs.readFile(pathname.substr(1), function (err, data) {
		if (request.headers['if-none-match'] === tag) {
			response.writeHead(304, {
				'Content-Type': fileMap[pathname.substr(1).split('.')[1]],
				'Expires': new Date(Date.now() + 30000),
				'Cache-Control': 'max-age=10, public',
				'ETag': tag,
				'Last-Modified': new Date(Date.now() - 30000),
				'Vary': 'User-Agent'
			});
	   } else {             
			response.writeHead(200, {
				'Content-Type': fileMap[pathname.substr(1).split('.')[1]],
				'Cache-Control': 'max-age=10, public',
				'Expires': new Date(Date.now() + 30000),
				'ETag': tag,
				'Last-Modified': new Date(Date.now() - 30000),
				'Vary': 'User-Agent'
			});
			response.write(fs.readFileSync(pathname.substr(1)));        
      	}
      	response.end();
   	});   
}).listen(8081);
```

如上代码。如果你没使用过 node，拷贝下代码存为 file.js，安装 node，命令行输入 `node file.js`，可以在同目录下建立 index.html 文件，在 html 文件中引用一些图片，CSS 等文件，浏览器输入 `localhost:8081/index.html` 进行模拟。🤓

## 关于缓存的一些问答

**1. 问题：请求被缓存，导致新代码未生效**

> **解决方案:**

- 服务端响应添加 `Cache-Control:no-cache,must-revalidate` 指令；
- 修改请求头 `If-modified-since:0` 或 `If-none-match`；
- 修改请求 URL，请求 URL 后加随机数，随机数可以是时间戳，哈希值，比如：http://damonare.cn?a=1234

**2. 问题：服务端缓存导致本地代码未更新**

> **解决方案：**

- 合理设置 Cache-Control:s-maxage 指令；
- 设置 Cache-Control:private 指令，防止代理服务器缓存资源；
- CDN 缓存可以使用管理员设置的缓存刷新接口进行刷新；

**3. 问题： Cache-Control: max-age=0 和 no-cache 有什么不同**

> **回答：**

`max-age=0` 和 `no-cache` 应该是从语气上不同。`max-age=0` 是告诉客户端资源的缓存到期**应该**向服务器验证缓存的有效性。而 `no-cache` 则告诉客户端使用缓存前**必须**向服务器验证缓存的有效性。

## 实践

### 其一

+ 针对我们当前的项目，由于 css 和 js 在打包时加了 md5 值，建议直接使用强缓存，并且 expires 和 cache-control 同时使用，建议设置时长为 7 天较为妥当。图片文件由于没有加 md5 值，建议采用对比缓存，html 文件也建议采用对比缓存。
+ ps: 当我们不设置 cache-control，只设置对比缓存，在不同浏览器下会有不同的表现。chrome 会直接从本地缓存获取，其他会请求服务器返回 304.这时候有两种方式让他们的响应一致。1、设置 cache-control: public, max-age=0; 记住，这里的 public 是关键。因为默认值是 private，表示其他代理都不要缓存，只有服务器缓存，而 max-age 又为 0，所以每次都会发起 200 的请求。设置 public 的意思就是允许其他各级代理缓存资源，因此如果资源没改变会返回 304。 2、直接设置 max-age=1000。即是一秒之后内容过期，目的是触发浏览器缓存。也能达到想要 304 的效果。

<https://juejin.im/post/5c136bd16fb9a049d37efc47>

### 更新了文件，前端看不到效果，有哪些可能？怎么解决

1、ctrl+F5

2、浏览器隐私模式开发（**Ctrl+Shift+N**）

3、chrome 开发者工具里将 Disable cache 选项打勾，阻止缓存

4、在开发阶段，给资源加上一个**动态的参数**，由于每次资源的修改都要更新引用的位置，同时修改参数的值，所以操作起来不是很方便，除非是在动态页面比如 jsp 里开发就可以用服务器变量来解决，或者用前端构建工具来处理这个参数修改的问题。

5、如果资源引用的页面被嵌入到了一个 iframe 里面，可以在 iframe 的区域右键重新加载该页面 (给大家演示下)

6、如果缓存问题出现在 ajax 请求中，最有效的解决办法就是 ajax 的请求地址追加随机数

7、动态设置 iframe 的 src 时，有可能因为缓存问题导致看不到最新效果，在 src 后面添加随机数即可

8、通过前端开发工具 grunt gulp 等的插件来启动一个静态服务器，则在这个服务器下所有资源返回的 response header 中，Cache-Control 始终被设置为不缓存

# CDN 缓存

+ CDN(Content Delivery Network) 内容分发网络。
+ 客户端直接从源站点获取数据，当服务器访问量大时会影响访问速度，进而影响用户体验，且无法保证客户端与原站点间的距离足够段，适合传输数据。CDN 解决的正是如何将数据快速可靠的从原站点传递到客户端，通过 CDN 对数据的分发，用户可以从一个距离较近的服务器获取数据，而不是源站点，从而达到快速访问，且减少原站点负载压力的目的。
+ **没有 CDN：**

  ![img](http-storage/8917733-02c4b11af1076cb8.webp)

1、用户在浏览器访问栏中输入要访问的域名；

2、浏览器向 DNS 服务器请求对该域名的解析；

3、DNS 服务器返回该域名的 IP 地址给浏览器

4、浏览器使用该 IP 地址向服务器请求内容。

5、服务器将用户请求的内容返回给浏览器。

+ **使用了 CDN：**

  ![img](http-storage/8917733-48ece468354b471d.webp)

1、用户在浏览器中输入要访问的域名。

2、浏览器向 DNS 服务器请求对域名进行解析。由于 CDN 对域名解析进行了调整，DNS 服务器会最终将域名的解析权交给 CNAME 指向的 CDN 专用 DNS 服务器。

3、CDN 的 DNS 服务器将 CDN 的负载均衡设备 IP 地址返回给用户。

4、用户向 CDN 的负载均衡设备发起内容 URL 访问请求。

5、CDN 负载均衡设备会为用户选择一台合适的缓存服务器提供服务。

选择的依据包括：根据用户 IP 地址，判断哪一台服务器距离用户最近；根据用户所请求的 URL 中携带的内容名称，判断哪一台服务器上有用户所需内容；查询各个服务器的负载情况，判断哪一台服务器的负载较小。

基于以上这些依据的综合分析之后，负载均衡设置会把缓存服务器的 IP 地址返回给用户。

6、用户向缓存服务器发出请求。

7、缓存服务器响应用户请求，将用户所需内容传送到用户。

**没有 CDN：浏览器缓存**

**使用了 CDN：浏览器缓存 +CDN 缓存**

在用户第一次访问网站后，网站的一些静态资源如图片等就会被下载到本地，作为缓存，当用户第二次访问该网站的时候，浏览器就会从缓存中加载资源，不用向服务器请求资源，从而提高了网站的访问速度，而若使用了 CDN，当浏览器本地缓存的资源过期之后，浏览器不是直接向源站点请求资源，而是向 CDN 边缘节点请求资源，CDN 边缘节点中也存在缓存，若 CDN 中的缓存也过期，那就由 CDN 边缘节点向源站点发出回源请求来获取最新资源。

**浏览器缓存以及 CDN 缓存都有一套判断文件是否需要更新的机制：**

浏览器参考上面

CDN 节点缓存机制在不同服务商中是不同的，但一般都遵循 HTTP 协议，通过 http 响应头中的 Cache-Control:max-age 的字段来设置 CDN 节点文件缓存时间。当客户端向 CDN 节点请求数据时，CDN 会判断缓存数据是否过期，若没有过期，则直接将缓存数据返回给客户端，否则就向源站点发出请求，从源站点拉取最新数据，更新本地缓存，并将最新数据返回给客户端。CDN 服务商一般会提供基于文件后缀、目录多个维度来指定 CDN 缓存时间，为用户提供更精细化的缓存管理。CDN 缓存时间会对“回源率”产生直接的影响，若 CDN 缓存时间短，则数据经常失效，导致频繁回源，增加了源站的负载，同时也增大了访问延时；若缓存时间长，数据更新时间慢，因此需要针对不同的业务需求来选择特定的数据缓存管理。
