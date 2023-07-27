---
{"dg-publish":true,"permalink":"/programming/font-end/browser-core/browser-storage/"}
---


![http-cache](programming/basic/cs-basic/network-protocol/http-cache.md#什么是缓存)

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

![1554011850615](/img/user/programming/font-end/browser-core/browser-storage/1554011850615.png)

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
