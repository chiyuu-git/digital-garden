---
{"dg-publish":true,"permalink":"/programming/basic/cs-basic/network-protocol/http-cross-origin/"}
---


# 浏览器同源策略

## 概述

### 含义

1995 年，同源政策由 Netscape 公司引入浏览器。目前，所有浏览器都实行这个政策。

最初，它的含义是指，A 网页设置的 Cookie，B 网页不能打开，除非这两个网页 " 同源 "。所谓 " 同源 " 指的是 " 三个相同 "。

- 协议相同
- 域名相同，顶级域名相同，但是次级域名不相同，仍然属于跨域, 所以 domain 是 cookie 跨域的一种解决办法
- 端口相同

### 目的

同源政策的目的，是为了保证用户信息的安全，防止恶意的网站窃取数据。

网络上的许多页面都会加载来自不同域的 CSS 样式表，图像和脚本等资源。

设想这样一种情况：A 网站是一家银行，用户登录以后，又去浏览其他网站。如果其他网站可以读取 A 网站的 Cookie，会发生什么？

很显然，如果 Cookie 包含隐私（比如存款总额），这些信息就会泄漏。更可怕的是，Cookie 往往用来保存用户的登录状态，如果用户没有退出登录，其他网站就可以冒充用户，为所欲为。因为浏览器同时还规定，提交表单不受同源政策的限制。

由此可见，" 同源政策 " 是必需的，否则 Cookie 可以共享，互联网就毫无安全可言

### 限制范围

随着互联网的发展，" 同源政策 " 越来越严格。目前，如果非同源，共有三种行为受到限制。

- Cookie、LocalStorage 和 IndexDB 无法读取。
- DOM 无法获得。
- AJAX 请求不能发送。

# 窗口之间的同源策略规避

## Cookie

Cookie 是服务器写入浏览器的一小段信息，只有同源的网页才能共享。

对于 Cookie 来说，Cookie 的同源只关注域名，是忽略协议和端口的。所以一般情况下，https://localhost:80/和 http://localhost:8080/的 Cookie 是共享的。

Cookie 是不可跨域的；在没有经过任何处理的情况下，二级域名不同也是不行的。(wenku.baidu.com 和 baike.baidu.com)。

### Domain

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

## window.postMessage() #

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

### **示例**

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

# Ajax 跨域（源）通信

## JSONP #

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

## Webpack 开发环境下跨域

![project-init](programming/project/project-init.md#Dev-server%20配置跨域请求)

# HTTP 访问控制（CORS）

<https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS>

CORS 是跨源资源分享（Cross-Origin Resource Sharing）的缩写。它是 W3C 标准，是跨源 AJAX 请求的根本解决方法。相比 JSONP 只能发 `GET` 请求，CORS 允许任何类型的请求。

跨域资源共享（CORS）是一种机制，它使用额外的 HTTP 头来告诉浏览器 让运行在一个 origin (domain) 上的 Web 应用被准许访问来自不同源服务器上的指定的资源。当一个资源从与该资源本身所在的服务器**不同的域、协议或端口**请求一个资源时，资源会发起一个**跨域 HTTP 请求**。

跨域资源共享（CORS）机制允许 Web 应用服务器进行跨域访问控制，从而使跨域数据传输得以安全进行。现代浏览器支持在 API 容器中（例如 [`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest) 或 [Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) ）使用 CORS，以降低跨域 HTTP 请求所带来的风险。

出于安全原因，浏览器对于从脚本内发起的跨源 HTTP 请求，会拒绝其 HTTP 响应。 XMLHttpRequest 和 Fetch API 遵循同源策略。 这意味着使用这些 API 的 Web 应用程序只能从加载应用程序的同一个域请求 HTTP 资源

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

使用下列方法之一：

- [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET)
- [`HEAD`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/HEAD)
- [`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST)

Fetch 规范定义了 [对 CORS 安全的首部字段集合](https://fetch.spec.whatwg.org/#cors-safelisted-request-header)，不得人为设置该集合之外的其他首部字段。该集合为：

- [`Accept`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Accept)
- [`Accept-Language`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Accept-Language)
- [`Content-Language`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Language)
- [`Content-Type`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type) （需要注意额外的限制）
- `DPR`
- `Downlink`
- `Save-Data`
- `Viewport-Width`
- `Width`

[`Content-Type`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type) 的值仅限于下列三者之一：@@@

- `text/plain`
- `multipart/form-data`
- `application/x-www-form-urlencoded`

> 当请求事 post 请求时，contentype 设置为 application/json 则不是简单请求

请求中的任意 [`XMLHttpRequestUpload`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequestUpload) 对象均没有注册任何事件监听器；[`XMLHttpRequestUpload`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequestUpload) 对象可以使用 [`XMLHttpRequest.upload`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/upload) 属性访问。

请求中没有使用 [`ReadableStream`](https://developer.mozilla.org/zh-CN/docs/Web/API/ReadableStream) 对象。

> **注意:** 这些跨域请求与浏览器发出的其他跨域请求并无二致。如果服务器未返回正确的响应首部，则请求方不会收到任何数据。因此，那些不允许跨域请求的网站无需为这一新的 HTTP 访问控制特性担心。

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

客户端和服务器之间使用 CORS 首部字段来处理跨域权限：

分别检视请求报文和响应报文：

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

第 13~22 行是来自于 http://bar.other 的服务端响应。响应中携带了响应首部字段 [`Access-Control-Allow-Origin`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)（第 16 行）。使用 [`Origin`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Origin) 和 [`Access-Control-Allow-Origin`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) 就能完成最简单的访问控制。本例中，服务端返回的 `Access-Control-Allow-Origin: *` 表明，该资源可以被**任意**外域访问。如果服务端仅允许来自 http://foo.example 的访问，该首部字段的内容如下：

```
Access-Control-Allow-Origin: http://foo.example
```

现在，除了 http://foo.example，其它外域均不能访问该资源（该策略由请求首部中的 ORIGIN 字段定义，见第 10 行）。`Access-Control-Allow-Origin` 应当为 * 或者包含由 Origin 首部字段所指明的域名。

### 在 Koa2 中配置 CORS 跨域

引入 koa2-cors ，安装依赖后，在 app.js 中加入下面代码：

```js
const cors = require('koa2-cors');
// 通过简化的属性直接定义相应的字段
app.use(cors({
  origin: '*',
  exposeHeaders: ['WWW-Authenticate', 'Server-Authorization'],
  maxAge: 5,
  credentials: true,
  allowMethods: ['GET', 'POST', 'DELETE'],
  allowHeaders: ['Content-Type', 'Authorization', 'Accept'],
}));
```

### 预检请求

简单请求和非简单请求并不是规范上定义的，而是 MDN 定义的术语

表单设计时是跨域的，表单只有三中 conten-tyoe，所以就保留了那三中，作为安全的

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

第 7 行将 `XMLHttpRequest ` 的 `withCredentials` 标志设置为 true，从而向服务器发送 Cookies。因为这是一个简单 GET 请求，所以浏览器不会对其发起“预检请求”。但是，如果服务器端的响应中未携带 `Access-Control-Allow-Credentials: true `，浏览器将不会把响应内容返回给请求的发送者。

对于附带身份凭证的请求，服务器不得设置 `Access-Control-Allow-Origin ` 的值为 `*`

这是因为请求的首部中携带了 Cookie 信息，如果 ` Access-Control-Allow-Origin` 的值为 `**`，请求将会失败。而将 Access-Control-Allow-Origin 的值设置为 http://foo.example，则请求将成功执行。

另外，响应首部中也携带了 Set-Cookie 字段，尝试对 Cookie 进行修改。如果操作失败，将会抛出异常。

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

## HTTP 请求首部字段

本节列出了可用于发起跨域请求的首部字段。请注意，**这些首部字段无须手动设置。 当开发者使用 XMLHttpRequest 对象发起跨域请求时，它们已经被设置就绪。**

### Origin

`Origin` 首部字段表明预检请求或实际请求的源站。

  ```html
  Origin: <origin>
  ```

  origin 参数的值为源站 URI。它不包含任何路径信息，只是服务器名称。

注意，不管是否为跨域请求，ORIGIN 字段总是被发送。

### Access-Control-Request-Method

### Access-Control-Request-Headers

# Nginx 反向代理

[nginx](../../../back-end/nginx.md)

# 服务端推送

## 短轮询

轮询是一种“拉”取信息的工作模式。设置一个定时器，定时询问服务器是否有信息，每次建立连接传输数据之后，链接会关闭。

```js
var polling = function(url, type, data){
    var xhr = new XMLHttpRequest(), 
        type = type || "GET",
        data = data || null;

    xhr.onreadystatechange = function(){
        if(xhr.readyState == 4) {
            receive(xhr.responseText);
            xhr.onreadystatechange = null;
        }
    };

    xhr.open(type, url, true);
    //IE的ActiveXObject("Microsoft.XMLHTTP")支持GET方法发送数据，
    //其它浏览器不支持，已测试验证
    xhr.send(type == "GET" ? null : data);
};

var timer = setInterval(function(){
    polling();
}, 1000);
```

在轮询的过程中，如果因为网络原因，导致上一个 xhr 对象还没传输完毕，定时器已经开始了下一个询问，上一次的传输是否还会在队列中，这个问题我没去研究。如果感兴趣可以自己写一个 ajax 的请求管理队列。

## 长轮询

长轮询其实也没啥特殊的地方，就是在 xhr 对象关闭连接的时候马上又给他接上~ 看码：

```js
var longPoll = function(type, url){
    var xhr = new XMLHttpRequest();

    xhr.onreadystatechange = function(){
        // 状态为 4，数据传输完毕，重新连接
        if(xhr.readyState == 4) {
            receive(xhr.responseText);
            xhr.onreadystatechange = null;

            longPoll(type, url);
        }
    };

    xhr.open(type, url, true);
    xhr.send();
}
```

只要服务器断开连接，客户端马上连接，不让他有一刻的休息时间，这就是长轮询。

## WebSocket

[websocket](websocket.md)

## EventSource

HTML5 中提供的 EventSource 这玩意儿，这是无比简洁的服务器推送信息的接受函数。

```js
new EventSource("test.php").onmessage=function(evt){
    console.log(evt.data);
};
```

简洁程度和 websocket 是一样的啦，只是这里有一个需要注意的地方，test.php 输出的数据流应该是特殊的 MIME 类型，要求是 "text/event-stream"，如果不设置的话，你试试~ （直接抛出异常）

## 其他通信方法

- 图片连接

## 即时通讯的实现，短轮询、长轮询、SSE 和 WebSocket 间的区别

短轮询和长轮询的目的都是用于实现客户端和服务器端的一个即时通讯。

短轮询的基本思路就是浏览器每隔一段时间向浏览器发送 http 请求，服务器端在收到请求后，不论是否有数据更新，都直接进行响应。这种方式实现的即时通信，本质上还是浏览器发送请求，服务器接受请求的一个过程，通过让客户端不断的进行请求，使得客户端能够模拟实时地收到服务器端的数据的变化。这种方式的优点是比较简单，易于理解。缺点是这种方式由于需要不断的建立 http 连接，严重浪费了服务器端和客户端的资源。当用户增加时，服务器端的压力就会变大，这是很不合理的。

长轮询的基本思路是，首先由客户端向服务器发起请求，当服务器收到客户端发来的请求后，服务器端不会直接进行响应，而是先将这个请求挂起，然后判断服务器端数据是否有更新。如果有更新，则进行响应，如果一直没有数据，则到达一定的时间限制才返回。

客户端 JavaScript 响应处理函数会在处理完服务器返回的信息后，再次发出请求，重新建立连接。长轮询和短轮询比起来，它的优点是明显减少了很多不必要的 http 请求次数，相比之下节约了资源。长轮询的缺点在于，连接挂起也会导致资源的浪费。

SSE 的基本思想是，服务器使用流信息向服务器推送信息。严格地说，http 协议无法做到服务器主动推送信息。但是，有一种变通方法，就是服务器向客户端声明，接下来要发送的是流信息。也就是说，发送的不是一次性的数据包，而是一个数据流，会连续不断地发送过来。这时，客户端不会关闭连接，会一直等着服务器发过来的新的数据流，视频播放就是这样的例子。SSE 就是利用这种机制，使用流信息向浏览器推送信息。它基于 http 协议，目前除了 IE/Edge，其他浏览器都支持。它相对于前面两种方式来说，不需要建立过多的 http 请求，相比之下节约了资源。

上面三种方式本质上都是基于 http 协议的，我们还可以使用 WebSocket 协议来实现。WebSocket 是 Html5 定义的一个新协议，与传统的 http 协议不同，该协议允许由服务器主动的向客户端推送信息。使用 WebSocket 协议的缺点是在服务器端的配置比较复杂。WebSocket 是一个全双工的协议，也就是通信双方是平等的，可以相互发送消息，而 SSE 的方式是单向通信的，只能由服务器端向客户端推送信息，如果客户端需要发送信息就是属于下一个 http 请求了。
