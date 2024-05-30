---
{"aliases":[],"tags":["review"],"review-dates":["2023-08-24","2023-08-27"],"dg-publish":true,"date-created":"2023-07-21-Fri, 2:53:54 pm","date-modified":"2023-12-26-Tue, 3:57:57 pm","sr-due":"2023-09-18","sr-interval":14,"sr-ease":250,"permalink":"/programming/basic/cs-basic/network-protocol/http-cross-origin/","dgPassFrontmatter":true}
---


# 浏览器同源策略

## 概述

### 含义

1995 年，同源政策由 Netscape 公司引入浏览器。目前，所有浏览器都实行这个政策。

最初，它的含义是指，A 网页设置的 Cookie，B 网页不能打开，除非这两个网页 " 同源 "。所谓 " 同源 " 指的是 " 三个相同

- 协议相同
- 域名相同，顶级域名相同，但是次级域名不相同，仍然属于跨域, 所以 domain 是 cookie 跨域的一种解决办法
- 端口相同

### 目的

同源政策的目的，是为了保证用户信息的安全，防止恶意的网站窃取数据。

>网络上的许多页面都会加载来自不同域的 CSS 样式表，图像和脚本等资源。 设想这样一种情况：A 网站是一家银行，用户登录以后，又去浏览其他网站。如果其他网站可以读取 A 网站的 Cookie，会发生什么？ 很显然，如果 Cookie 包含隐私（比如存款总额），这些信息就会泄漏。更可怕的是，Cookie 往往用来保存用户的登录状态，如果用户没有退出登录，其他网站就可以冒充用户，为所欲为。因为浏览器同时还规定，提交表单不受同源政策的限制。 由此可见，" 同源政策 " 是必需的，否则 Cookie 可以共享，互联网就毫无安全可言

### 限制范围

随着互联网的发展，" 同源政策 " 越来越严格。目前，如果非同源，共有三种行为受到限制。

- Cookie、LocalStorage 和 IndexDB 无法读取
- DOM 无法获得
- AJAX 请求不能发送

# 窗口之间的同源策略规避

## Cookie

Cookie 是服务器写入浏览器的一小段信息，只有同源的网页才能共享。

对于 Cookie 来说，Cookie 的同源只关注域名，是忽略协议和端口的。所以一般情况下，https://localhost:80/和 http://localhost:8080/的 Cookie 是共享的。

Cookie 是不可跨域的；在没有经过任何处理的情况下，二级域名不同也是不行的。(wenku.baidu.com 和 baike.baidu.com)。

### Domain

[chrome 禁用 domain](https://mp.weixin.qq.com/s?__biz=Mzk0MDMwMzQyOA==&mid=2247492113&idx=1&sn=cacc274e8c8756582e77582388be6665&chksm=c2e1173af5969e2c3513d2e1649f0f09ac79fd011155ae0f92883e4bbb9f9bb3cdb533fee100&token=2132458547&lang=zh_CN#rd)

两个网页一级**域名**相同，只是二级**域名**不同，浏览器允许通过设置 `document.domain` 共享 Cookie。

另外，服务器也可以在设置 Cookie 的时候，指定 Cookie 的所属域名为一级域名，比如 `.example.com`。

```js
Set-Cookie: key=value; domain=.example.com; path=/
```

这样的话，二级域名和三级域名不用做任何设置，都可以读取这个 Cookie。

> **注意**：这种方法只能规避域名造成的同源策略，无法解决协议和端口号带来的跨域
>
> **注意**：这种方法只适用于 Cookie 和 iframe 窗口，LocalStorage 和 IndexDB 无法通过这种方法，规避同源政策，而要使用下文介绍的 PostMessage API。

`document.domain` 基本上是一个只读属性，只有一种情况除外。次级域名的网页，可以把 `document.domain` 设为对应的上级域名。比如，当前域名是 `a.sub.example.com`，则 `document.domain` 属性可以设置为 `sub.example.com`，也可以设为 `example.com`。修改后，`document.domain` 相同的两个网页，可以读取对方的资源，比如设置的 Cookie。

另外，设置 `document.domain` 会导致端口被改成 `null`。因此，如果通过设置 `document.domain` 来进行通信，双方网页都必须设置这个值，才能保证端口相同。

**示例**

- 举例来说，A 网页是 `http://w1.example.com/a.html`，B 网页是 `http://w2.example.com/b.html`，那么只要设置相同的 `document.domain`，两个网页就可以共享 Cookie。

  ```js
  document.domain = 'example.com';
  ```

- 现在，A 网页通过脚本设置一个 Cookie

  ```js
  document.cookie = "test1=hello";
  ```

- B 网页就可以读到这个 Cookie。

  ```js
  var allCookie = document.cookie;
  ```

## Iframe

如果两个网页不同源，就无法拿到对方的 DOM。典型的例子是 `iframe` 打开的窗口，它们与父窗口无法通信。

如果两个窗口一级域名相同，只是二级域名不同，那么设置上一节介绍的 `document.domain` 属性，就可以规避同源政策，拿到 DOM。

对于完全不同源的网站，目前有三种方法，可以解决**iframe 窗口**的通信问题

```js
片段识别符（fragment identifier）
window.name
postMessage API
```

其中前两种方法只适用于 iframe，最后一种方法也适用于 window.open() 打开的窗口

### 片段识别符

片段标识符（fragment identifier）指的是，URL 的 `#` 号后面的部分，比如 `http://example.com/x.html#fragment` 的 `#fragment`。如果只是改变片段标识符，页面不会重新刷新。

父窗口可以把信息，写入子窗口的片段标识符。

```js
var src = originURL + '#' + data;
document.getElementById('myIFrame').src = src;
```

子窗口通过监听 `hashchange` 事件得到通知。

```js
window.onhashchange = checkMessage;

function checkMessage() {
  var message = window.location.hash;
  // ...
}
```

同样的，子窗口也可以改变父窗口的片段标识符。

### window.name

浏览器窗口有 `window.name` 属性。这个属性的最大特点是，无论是否同源，只要在同一个窗口里，前一个网页设置了这个属性，后一个网页可以读取它。

## window.postMessage()

上面两种方法都属于破解，HTML5 为了解决这个问题，引入了一个全新的 API：跨文档通信 API（Cross-document messaging）。

这个 API 为 `window` 对象新增了一个 `window.postMessage` 方法，允许跨窗口通信，不论这两个窗口是否同源。

这个 API 可以解决以下情况的跨域通信：

+ iframe 窗口之间
+ `window.open` 方法打开的窗口

有了 `postMessage API`，不同的窗口之间就可以传递：

+ 普通的数据
+ DOM 节点
+ Cookie
+ localStorage

**参数**

+ 第一个参数是具体的信息内容
+ 第二个参数是接收消息的窗口的源（origin）
  + 即 " 协议 + 域名 + 端口 "。也可以设为 `*`，表示不限制域名，向所有窗口发送。

### 示例

举例来说，父窗口 `http://aaa.com` 向子窗口 `http://bbb.com` 发消息，调用 `postMessage` 方法就可以了。

```js
var popup = window.open('http://bbb.com', 'title');
popup.postMessage('Hello World!', 'http://bbb.com');
```

子窗口向父窗口发送消息的写法类似。

```js
window.opener.postMessage('Nice to see you', 'http://aaa.com');
```

父窗口和子窗口都可以通过 `message` 事件，监听对方的消息。

```js
window.addEventListener('message', function(e) {
  console.log(e.data);
},false);
```

`message` 事件的事件对象 `event`，提供以下三个属性。

```js
event.source：发送消息的窗口
event.origin: 消息发向的网址
event.data: 消息内容
```

下面的例子是，子窗口通过 `event.source` 属性引用父窗口，然后发送消息。

```js
window.addEventListener('message', receiveMessage);
function receiveMessage(event) {
  event.source.postMessage('Nice to see you!', '*');
}
```

`event.origin` 属性可以过滤不是发给本窗口的消息。

```js
window.addEventListener('message', receiveMessage);
function receiveMessage(event) {
  if (event.origin !== 'http://aaa.com') return;
  if (event.data === 'Hello World') {
      event.source.postMessage('Hello', event.origin);
  } else {
    console.log(event.data);
  }
}
```

### WebStorage

+ 通过 `window.postMessage`，读写其他窗口的 LocalStorage 也成为了可能。
+ http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html

# HTTP 访问控制（CORS）

解决 AJAX 跨域请求

https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS

出于安全原因，浏览器对于从脚本内发起的跨源 HTTP 请求，会拒绝其 HTTP 响应。 XMLHttpRequest 和 Fetch API 遵循同源策略。 这意味着使用这些 API 的 Web 应用程序只能从加载应用程序的同一个域请求 HTTP 资源

CORS 是跨源资源分享（Cross-Origin Resource Sharing）的缩写。它是 W3C 标准，是跨源 AJAX 请求的根本解决方法。相比 JSONP 只能发 `GET` 请求，CORS 允许任何类型的请求。

跨域资源共享（CORS）是一种机制，它使用额外的 HTTP 头来告诉浏览器 让运行在一个 origin (domain) 上的 Web 应用被准许访问来自不同源服务器上的指定的资源。当一个资源从与该资源本身所在的服务器**不同的域、协议或端口**请求一个资源时，资源会发起一个**跨域 HTTP 请求**。

跨域资源共享（CORS）机制允许 Web 应用服务器进行跨域访问控制，从而使跨域数据传输得以安全进行。现代浏览器支持在 API 容器中（例如 [`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest) 或 [Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) ）使用 CORS，以降低跨域 HTTP 请求所带来的风险。

## 什么情况下需要 CORS ？

跨域资源共享标准（ [cross-origin sharing standard](http://www.w3.org/TR/cors/) ）允许在下列场景中使用跨域 HTTP 请求：

- 前文提到的由 [`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest) 或 [Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 发起的跨域 HTTP 请求。
- Web 字体 (CSS 中通过 ` @font-face ` 使用跨域字体资源), [因此，网站就可以发布 TrueType 字体资源，并只允许已授权网站进行跨站调用](http://www.webfonts.info/wiki/index.php?title=%40font-face_support_in_Firefox)。
- [WebGL 贴图](https://developer.mozilla.org/zh-CN/docs/Web/API/WebGL_API/Tutorial/Using_textures_in_WebGL)
- 使用 `drawImage` 将 Images/video 画面绘制到 canvas
- 样式表（使用 [CSSOM](https://developer.mozilla.org/en-US/docs/Web/CSS/CSSOM_View)）

本文概述了跨域资源共享机制及其所涉及的 HTTP 头。

## 功能概述

跨域资源共享标准新增了一组 **HTTP 首部字段**，允许服务器声明哪些源站通过浏览器有权限访问哪些资源。

CORS 请求失败会产生错误，但是为了安全，在 JavaScript 代码层面是无法获知到底具体是哪里出了问题。你只能查看浏览器的控制台以得知具体是哪里出现了错误。

接下来的内容将讨论相关场景，并剖析该机制所涉及的 HTTP 首部字段。

## 若干访问控制场景

这里，我们使用三个场景来解释跨域资源共享机制的工作原理。这些例子都使用 [`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest) 对象。

规范要求，对那些可能对服务器数据产生副作用的 HTTP 请求方法（特别是 [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET) 以外的 HTTP 请求，或者搭配某些 MIME 类型的 [`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST) 请求），浏览器必须首先使用 [`OPTIONS`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/OPTIONS) 方法发起一个预检请求（preflight request），从而获知服务端是否允许该跨域请求。服务器确认允许之后，才发起实际的 HTTP 请求。在预检请求的返回中，服务器端也可以通知客户端，是否需要携带身份凭证（包括 [Cookies](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies) 和 HTTP 认证相关数据）。

### 简单请求

某些请求不会触发 [CORS 预检请求](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS#Preflighted_requests)。本文称这样的请求为“简单请求”，请注意，该术语并不属于 [Fetch](https://fetch.spec.whatwg.org/) （其中定义了 CORS）规范。若请求满足所有下述条件，则该请求可视为“简单请求”：

1. 使用下列方法之一：
	- [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET)
	- [`HEAD`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/HEAD)
	- [`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST)
2. Fetch 规范定义了 [对 CORS 安全的首部字段集合](https://fetch.spec.whatwg.org/#cors-safelisted-request-header)，不得人为设置该集合之外的其他首部字段。该集合为：
	- [`Accept`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Accept)
	- [`Accept-Language`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Accept-Language)
	- [`Content-Language`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Language)
	- [`Content-Type`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type) （需要注意额外的限制）
	- `DPR`
	- `Downlink`
	- `Save-Data`
	- `Viewport-Width`
	- `Width`
3. [`Content-Type`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type) 的值仅限于下列三者之一：@@@
	- `text/plain`
	- `multipart/form-data`
	- `application/x-www-form-urlencoded`
	- 比如: 当请求事 post 请求时，contentype 设置为 application/json 则不是简单请求
4. 请求中的任意 [`XMLHttpRequestUpload`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequestUpload) 对象均没有注册任何事件监听器；[`XMLHttpRequestUpload`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequestUpload) 对象可以使用 [`XMLHttpRequest.upload`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/upload) 属性访问。
5. 请求中没有使用 [`ReadableStream`](https://developer.mozilla.org/zh-CN/docs/Web/API/ReadableStream) 对象。

> **注意:** 这些跨域请求与浏览器发出的其他跨域请求并无二致。如果服务器未返回正确的响应首部，则请求方不会收到任何数据。因此，那些不允许跨域请求的网站无需为这一新的 HTTP 访问控制特性担心。

#### 示例

比如说，假如站点 http://foo.example 的网页应用想要访问 http://bar.other 的资源。http://foo.example 的网页中可能包含类似于下面的 JavaScript 代码：

```js
var invocation = new XMLHttpRequest();
var url = 'http://bar.other/resources/public-data/';
   
function callOtherDomain() {
  if(invocation) {    
    invocation.open('GET', url, true);
    invocation.onreadystatechange = handler;
    invocation.send(); 
  }
}
```

客户端和服务器之间使用 CORS 首部字段来处理跨域权限, 分别检视请求报文和响应报文：

```
GET /resources/public-data/ HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.5; en-US; rv:1.9.1b3pre) Gecko/20081130 Minefield/3.1b3pre
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.7
Connection: keep-alive
Referer: http://foo.example/examples/access-control/simpleXSInvocation.html
Origin: http://foo.example


HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 00:23:53 GMT
Server: Apache/2.0.61 
Access-Control-Allow-Origin: *
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Transfer-Encoding: chunked
Content-Type: application/xml

[XML Data]
```

第 1~10 行是请求首部。第 10 行 的请求首部字段 [`Origin`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Origin) 表明该请求来源于 `http://foo.exmaple`。

第 13~22 行是来自于 http://bar.other 的服务端响应。响应中携带了响应首部字段 [`Access-Control-Allow-Origin`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)（第 16 行）。

使用 [`Origin`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Origin) 和 [`Access-Control-Allow-Origin`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) 就能完成最简单的访问控制。本例中，服务端返回的 `Access-Control-Allow-Origin: *` 表明，该资源可以被**任意**外域访问。如果服务端仅允许来自 http://foo.example 的访问，该首部字段的内容如下：

```
Access-Control-Allow-Origin: http://foo.example
```

现在，除了 http://foo.example, 其它外域均不能访问该资源（该策略由请求首部中的 ORIGIN 字段定义，见第 10 行）。`Access-Control-Allow-Origin` 应当为 * 或者包含由 Origin 首部字段所指明的域名。

### 预检请求

简单请求和非简单请求并不是规范上定义的，而是 MDN 定义的术语

表单设计时是跨域的，表单只有三种 conten-tyoe，所以就保留了那三种，作为安全的

还有可能是被服务器的 CORS 配置拦截，这种情况下，服务器无法正常收到 HTTP 请求

Access to XMLHttpRequest at 'http://localhost:8000/user/update/3' from origin 'http://localhost:8080' has been blocked by CORS policy: Method PATCH is not allowed by Access-Control-Allow-Methods in preflight response.

![1555985693469](http-cross-origin/1555985693469.png)

![1555985720377](http-cross-origin/1555985720377.png)

### 附带身份凭证的请求

[Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 与 CORS 的一个有趣的特性是，可以基于 [HTTP cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies) 和 HTTP 认证信息发送身份凭证。一般而言，对于跨域 [`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest) 或 [Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 请求，浏览器**不会**发送身份凭证信息。如果要发送凭证信息，需要设置 `XMLHttpRequest ` 的某个特殊标志位。

```js
var invocation = new XMLHttpRequest();
var url = 'http://bar.other/resources/credentialed-content/';
    
function callOtherDomain(){
  if(invocation) {
    invocation.open('GET', url, true);
    invocation.withCredentials = true;
    invocation.onreadystatechange = handler;
    invocation.send(); 
  }
}
```

第 7 行将 `XMLHttpRequest ` 的 `withCredentials` 标志设置为 true，从而向服务器发送 Cookies。因为这是一个简单 GET 请求，所以浏览器不会对其发起“预检请求”。

对于附带身份凭证的请求，服务器不得设置 `Access-Control-Allow-Origin` 的值为 `*`, 类似的 headers、Methods 字段均不能为通配符

#### 第三方 Cookie

但是，如果**服务器端的响应**中未携带 `Access-Control-Allow-Credentials: true `，浏览器将不会把响应内容返回给请求的发送者。

另外，响应首部中也携带了 Set-Cookie 字段，尝试对 Cookie 进行修改。如果操作失败，将会抛出异常

注意在 CORS 响应中设置的 cookie 适用一般性第三方 cookie 策略。在上面的例子中，页面是在 `foo.example` 加载，但是第 19 行的 cookie 是被 `bar.other` 发送的，如果用户设置其浏览器拒绝所有第三方 cookie，那么将不会被保存。

请求中的 cookie（第 10 行）也可能在正常的第三方 cookie 策略下被阻止。因此，强制执行的 cookie 策略可能会使本节描述的内容无效（阻止你发出任何携带凭据的请求）。

Cookie 策略受 [SameSite](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Set-Cookie#samesitesamesite-value) 属性控制。

## HTTP 响应首部字段

本节列出了规范所定义的响应首部字段。上一小节中，我们已经看到了这些首部字段在实际场景中是如何工作的。

### Access-Control-Allow-Origin

响应首部中可以携带一个 [`Access-Control-Allow-Origin`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) 字段，其语法如下:

```
Access-Control-Allow-Origin: <origin> | *
```

其中，origin 参数的值指定了允许访问该资源的外域 URI。对于不需要携带身份凭证的请求，服务器可以指定该字段的值为通配符，表示允许来自所有域的请求。

例如，下面的字段值将允许来自 http://mozilla.com 的请求：

```html
Access-Control-Allow-Origin: http://mozilla.com
```

如果服务端指定了具体的域名而非 `*`，那么响应首部中的 Vary 字段的值必须包含 Origin。这将告诉客户端：服务器对不同的源站返回不同的内容。

```
Access-Control-Allow-Origin: https://mozilla.org
Vary: Origin
```

### Access-Control-Expose-Headers

### Access-Control-Max-Age

### Access-Control-Allow-Credentials

### Access-Control-Allow-Methods

### Access-Control-Allow-Headers

### Cross-Origin-Resource-Policy

### Cross-Origin-Embedder-Policy

Because your site has the Cross-Origin Embedder Policy (COEP) enabled, each embedded iframe must also specify this policy. This behavior protects private data from being exposed to untrusted third party sites.

To solve this, add one of following to the embedded frame’s HTML response header:

- `Cross-Origin-Embedder-Policy: require-corp`
- `Cross-Origin-Embedder-Policy: credentialless` (Chrome > 96)

引用 iframe 时需要配置. iframe 源网站需要配置, 引用 iframe 的也需要配置

You can only access certain features like [`SharedArrayBuffer`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) objects or [`Performance.now()`](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now) with unthrottled timers, if your document has a COEP header with a value of `require-corp` or `credentialless` set.

![http-cross-origin](programming/basic/cs-basic/network-protocol/http-cross-origin.md#shareArrayBuffer)

## HTTP 请求首部字段

本节列出了可用于发起跨域请求的首部字段。请注意，**这些首部字段无须手动设置。 当开发者使用 XMLHttpRequest 对象发起跨域请求时，它们已经被设置就绪。**

### Origin

`Origin` 首部字段表明预检请求或实际请求的源站

  ```html
  Origin: <origin>
  ```

  origin 参数的值为源站 URI。它不包含任何路径信息，只是服务器名称。

注意，不管是否为跨域请求，ORIGIN 字段总是被发送。

### Access-Control-Request-Method

### Access-Control-Request-Headers

## 限制了接受响应，如果是 POST 请求，可以成功修改数据库吗？

只要是简单请求，就可以修改，因为服务器正常响应了，但是 HTTP 响应被浏览器拦截了

但是如果是预检请求，就会因为 options 请求失败，无法修改数据库，如果 option 被 block 了，服务器会打印 option，如果 option 是正常的，服务器就只打印 patch

浏览器仅发起了 OPTIONS 请求

还有可能是被服务器的 CORS 配置拦截，这种情况下，服务器无法正常收到 HTTP 请求

```
Access to XMLHttpRequest at 'http://localhost:8000/user/update/3' from origin 'http://localhost:8080' has been blocked by CORS policy: Method PATCH is not allowed by Access-Control-Allow-Methods in preflight response.
```

总结：无论何时，浏览器都可以正常发起请求，如果被 CORS 拦截，则服务器无响应，如果没有设置 CORS，服务器正常响应，但是会被浏览器拦截。

而预检请求失败，属于正常的响应流程，控制台不会报错

## *JSONP

跨域的本质：服务器返回了一个 javascript 脚本，这个脚本调用了一个我们事先定义好的方法，而方法中的参数就是我们想要的数据

由于 `<script>` 元素请求的脚本，**直接作为代码运行**。这时，只要浏览器定义了 `foo` 函数，该函数就会立即调用。

ctx 输出的类型应该是 'text/javascript'

ctx 输出的内容为可执行的返回数据 JavaScript 代码字符串

### 前端

```js
// 动态生成一个script标签
let script = document.createElement('script')
// 只能是GET方法
script.src = 'http://localhost:8000/jsonp?city=shenzhen&callback=foo'
let head = document.querySelector('head')
// 添加到头部相当于发送请求
head.appendChild(script)

// 定义一个全局方法
window.foo = function(data){
  console.log(data);
}
```

### Koa2 后端

```js
router.get('/jsonp', (ctx) => {
  // 获取query
  let city = ctx.query.city
  // 获取要执行的函数名
  let callbackName = ctx.query.callback || 'callback'
  let data = ''
  switch(city){
    case 'shenzhen':
    data = '28°'
    break
  }
  // 用text/javascript，让请求支持跨域获取
  ctx.type = 'text/javascript'
  // 拼接可执行的字符串，类似eval()，调用全局函数，传递data作为参数
  let jsonpStr = `;${callbackName}(${JSON.stringify(data)})`
  // 返回javascript脚本
  ctx.body = jsonpStr
})
```

### 封装 JSONP

```js
function myJSONPAcross(obj){
  //定义默认对象
  var defaults = {
   type : "get",
   url:"#",
   data:{},
   success:function(result){console.log(result)},
   jsonp:"callback", //动态回调函数
   jsonpCallback:"cb"
  }
  //替换默认对象
  for(var key in obj){
    defaults[key]=obj[key]
  }
  //拼接query
  var query = ""
  for(var attr in defaults.data){
    query += attr + "=" + defaults.data[attr] + "&"
  }
  //删除params最后的&
  if(query){
    query = query.slice(0,query.length-1)
    //params准备完毕，拼接url
    defaults.url += "?" + query
  }
  //拼接回调函数
  defaults.url += "&" + defaults.jsonp + "="+defaults.jsonpCallback
  //生成script标签（发送请求）
  var script = document.createElement("script")
  script.src = defaults.url

  //给window添加方法（获取返回值）
  window[defaults.jsonpCallback]=function(result){
    defaults.success(result)
  }
  //生成script标签，插入文档头部
  var head = document.querySelector("head")
  head.appendChild(script)
}
```

使用

```js
myJSONPAcross({
  url:'http://localhost:8000/jsonp',
  data:{city:'shenzhen'},
})
```

### jQuery 中的跨域请求

![1553413629413](http-cross-origin/1553413629413.png)

一旦把 dataType 定义为 jsonp，jQuery.ajax 就会使用动态创建 script 标签的方法，而不会使用 ajax 的传统 1234 个步骤

![1553413645731](http-cross-origin/1553413645731.png)

- 只是 jsonpCallback 有点长
- 尾部是时间戳信息
- jsonp 值默认是 callback
- 详见参考手册

![1553413663815](http-cross-origin/1553413663815.png)

![1553413692805](http-cross-origin/1553413692805.png)

![1553413697283](http-cross-origin/1553413697283.png)

## *Webpack 开发环境下跨域

[project-init](../../../programming-project/project-init.md#Dev-server%20配置跨域请求)

# COOP 和 COEP

[新的跨域策略：使用COOP、COEP为浏览器创建更安全的环境 - 掘金](https://juejin.cn/post/6855129007906963464?searchId=2023111619452928716436F6F0BA6BAAEE)

[由一个报错引发的浏览器跨域隔离探索 - 掘金](https://juejin.cn/post/7159391029135671333?searchId=2023111619452928716436F6F0BA6BAAEE)

[HTML页面安全策略汇总(1)：同源策略、CORS、COOP、COEP - 掘金](https://juejin.cn/post/7172493262115045383?searchId=2023111619452928716436F6F0BA6BAAEE)

[跨源相关机制综述（三）：crossorigin属性 - 知乎](https://zhuanlan.zhihu.com/p/345564689)a

[匿名 iframe：COEP 的福音！-腾讯云开发者社区-腾讯云](https://cloud.tencent.com/developer/article/2207623?areaSource=102001.6&traceId=3br1YC1WyO5SLc1z8tkR4)

[更简单的开启 COOP](https://mp.weixin.qq.com/s?__biz=Mzk0MDMwMzQyOA==&mid=2247498661&idx=1&sn=28eb77193affb91021bcdea5c1ef3840&chksm=c2e10e8ef59687981e12b2cbeb42e087aca2a5091a85f223c965f637a16d9a80cc4db3cc99bc&=1638573446&=zh_CN#rd)

# Nginx 反向代理

[nginx](../../../back-end/nginx.md)

# FAQ

## Nginx 实际解决的问题

生产环境 前后端不在一个域名, nginx 的配置, 其实可以吹一下, 我本地的项目就是通过 nginx 配置去转发的.

DNS 污染, 改 hosts, 或者反向代理

## 实际遇到的跨域问题

### Canvas 跨域

最关键的一点是什么呢? `Access-Control-Allow-Origin` 设置的只是域名.

而往往开发环境中端口号都是 3000 这种, 图片服务器则默认是 80 或者 443. 因为为了在本地开发环境使用 drawImage() 还得把本地开发环境的端口号设置为 80 或者 443. 然后这是行不通的, 因为这两个端口号往往已经被占用了. 因此需要通过 DNS 映射, 把本地域名的 3000 端口映射成本地开发机域名的 80 端口

[解决canvas图片getImageData,toDataURL跨域问题 « 张鑫旭-鑫空间-鑫生活](https://www.zhangxinxu.com/wordpress/2018/02/crossorigin-canvas-getimagedata-cors/)

### shareArrayBuffer

平面编辑器中使用到了 shareArrayBuffer. 对 COEP 有要求, 于是让工作台的页面也配置上了 COEP
