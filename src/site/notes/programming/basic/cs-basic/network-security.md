---
{"dg-publish":true,"permalink":"/programming/basic/cs-basic/network-security/"}
---


参考：<https://juejin.im/post/5cd6ad7a51882568d3670a8e#heading-14>

# 跨站脚本 XSS

## 缩写

Cross-site scripting 的英文首字母缩写本应为**CSS**，但因为 [CSS](https://zh.wikipedia.org/wiki/CSS_(消歧义)) 在 [网页设计](https://zh.wikipedia.org/wiki/網頁設計) 领域已经被广泛指 [层叠样式表](https://zh.wikipedia.org/wiki/层叠样式表)（Cascading Style Sheets），所以将 Cross（意为“交叉”）改以交叉形的**X**做为缩写。但早期的文件还是会使用 CSS 表示 Cross-site scripting。

## 概述

**跨站脚本**（英语：Cross-site scripting，通常简称为：XSS）是一种网站应用程序的安全漏洞攻击，是 [代码注入](https://zh.wikipedia.org/wiki/代碼注入) 的一种。它允许恶意用户将代码注入到网页上，其他用户在观看网页时就会受到影响。这类攻击通常包含了 [HTML](https://zh.wikipedia.org/wiki/HTML) 以及用户端 [脚本语言](https://zh.wikipedia.org/wiki/腳本語言)。

XSS 的本质是：利用网页开发时留下的漏洞，或者恶意代码未经过滤，与网站正常的代码混在一起；浏览器无法分辨哪些脚本是可信的，导致恶意脚本 【被执行。由于直接在用户的终端执行，恶意代码能够直接获取用户的信息，利用这些信息冒充用户向网站发起攻击者定义的请求。

这些恶意网页程序通常是 [JavaScript](https://zh.wikipedia.org/wiki/JavaScript)，但实际上也可以包括 [Java](https://zh.wikipedia.org/wiki/Java)，[VBScript](https://zh.wikipedia.org/wiki/VBScript)，[ActiveX](https://zh.wikipedia.org/wiki/ActiveX)，[Flash](https://zh.wikipedia.org/wiki/Flash) 或者甚至是普通的 [HTML](https://zh.wikipedia.org/wiki/HTML)。攻击成功后，攻击者可能得到更高的权限（如执行一些操作）、私密网页内容、[会话](https://zh.wikipedia.org/wiki/会话) 和 [cookie](https://zh.wikipedia.org/wiki/Cookie) 等各种内容。

根据攻击的来源，XSS 攻击可以分为反射型 (非持久型) 、 DOM 型、存储型 (持久性) 三种。下面我们来详细了解一下这三种 XSS 攻击

## 反射型 XSS

当用户点击一个恶意链接，或者提交一个表单，或者进入一个恶意网站时，注入脚本进入被攻击者的网站。Web 服务器将注入脚本，比如一个错误信息，搜索结果等，未进行过滤直接返回到用户的浏览器上。

### 攻击步骤

1. 攻击者构造出特殊的 `URL`，其中包含恶意代码。
2. 用户打开带有恶意代码的 `URL` 时，网站服务端将恶意代码从 `URL` 中取出，**拼接在 HTML 中返回给浏览器**。
3. 用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
4. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

反射型 XSS 漏洞常见于通过 `URL` 传递参数的功能，如网站搜索、跳转等。由于需要用户主动打开恶意的 `URL` 才能生效，攻击者往往会结合多种手段诱导用户点击。

攻击者发送一个常用网站的链接 让别人点. 但是 url 上拼接了一个 type 参数, 这个参数的值会渲染到网页上. 实际上是 script 脚本.点了就读 cookie

POST 的内容也可以触发反射型 XSS，只不过其触发条件比较苛刻（需要构造表单提交页面，并引导用户点击），所以非常少见。

### **示例**

```js
// 反射型 XSS 攻击
$(document).ready(function() {
  $('#login').click(function() {
    let username = $('#username').val();
    let password = $('#password').val();
    /* 省略了用户名/密码的合规性检查 */
    $.post('/api/login', {
      username,
      password
    }).then((res) => {
      if(res.code === 0) {
        //登录成功页
        location.href = `/welcome?type=<script>alert('恶意内容')<\/script>`;
      }else {
        //登录失败(如果是用户登录成功时，还能够获取到用户的cookie)
        location.href = `/error?type=<script>alert('恶意内容')<\/script>`;
      }
    });
  });
});
```

使用错误的用户名/密码进行登录 (例如: 123 / 123)，会跳向：`http://localhost:3000/error?type=<script>alert('恶意内容')</script>`

```js
app.get('/error', function (req, res) {
    res.send(`${req.query.type}`); //拿到 url 上的 type 参数，并返回给前端    
});
```

使用正确的用户名: yvette / yvette 登录，会跳向: `http://localhost:3000/welcome?type=<script>alert('恶意内容')</script>` ; 虽然 url 仍然包含恶意脚本，但是我们已经进行了转义，不会再被攻击

```js
app.get('/welcome', function (req, res) {
    //对查询参数进行编码，避免XSS攻击
    res.send(`${encodeURIComponent(req.query.type)}`);
    //对type查询参数进行编码，即可解决当前的XSS攻击(可重启服务查看)
    // res.send(`${encodeURIComponent(req.query.type)}`);
});
```

注意 `Chrome` 和 `Safari` 能够检测到 `url` 上的 xss 攻击，将网页拦截掉，但是其它浏览器不行，如 `Firefox`

![1562293629079](/img/user/programming/basic/cs-basic/network-security/1562293629079.png)

如果不希望被前端拿到 cookie，后端可以设置 [HttpOnly](programming/basic/cs-basic/network-protocol/http-session.md#HttpOnly) (不过这不是 `XSS攻击` 的解决方案，只能降低受损范围)

### 防范手段

对 url 的查询参数进行转义后再输出到页面。

## DOM 型 XSS

DOM 型 XSS 攻击，实际上就是前端 `JavaScript` 代码不够严谨，把不可信的内容插入到了页面。在使用 `.innerHTML`、`.outerHTML`、`.appendChild`、`document.write()` 等 API 时要特别小心，不要把不可信的数据作为 HTML 插到页面上，尽量使用 `.innerText`、`.textContent`、`.setAttribute()` 等。

### 攻击步骤

1. 攻击者构造出特殊数据，其中包含恶意代码。
2. 用户浏览器执行了恶意代码。
3. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

### 示例

```js
$('#attck').click(function () {
  let commend = $('#comments').val();
  //没有对输入内容进行过滤
  if ($.trim(commend)) {
    $('.list-group').append(`<li class="list-group-item">${commend}</li>`);
    $('#comments').val('');
  }
});
```

输入评论内容: `2222<script>alert(1)</script>`

如果是登录状态，还可以拿 cookie 等信息~ 还可以悄悄引入其它的 js 文件过来

### 防范手段

防范 DOM 型 XSS 攻击的核心就是对输入内容进行转义 (DOM 中的内联事件监听器和链接跳转都能把字符串作为代码运行，需要对其内容进行检查)。

对于 `url` 链接 (例如图片的 `src` 属性)，那么直接使用 `encodeURIComponent` 来转义。

非 `url`，我们可以这样进行编码：

```js
function encodeHtml(str) {
  return str.replace(/"/g, '&quot;')
    .replace(/'/g, '&apos;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;');
}
$('#security').click(function () {
  let commend = $('#comments').val();
  //对输入内容进行转义
  //如果是url地址，可以直接使用 encodeURIComponent 进行转义
  if ($.trim(commend)) {
    $('.list-group').append(`<li class="list-group-item">${encodeHtml(commend)}</li>`);
    $('#comments').val('');
  }
});
```

DOM 型 XSS 攻击中，取出和执行恶意代码由浏览器端完成，属于前端 JavaScript 自身的安全漏洞。

## 存储型 XSS

恶意脚本永久存储在目标服务器上。当浏览器请求数据时，脚本从服务器传回并执行，影响范围比反射型和 DOM 型 XSS 更大。存储型 XSS 攻击的原因仍然是没有做好数据过滤：

- 前端提交数据至服务端时，没有做好过滤；
- 服务端在接受到数据时，在存储之前，没有做过滤；
- 前端从服务端请求到数据，没有过滤输出。

### 攻击步骤

1. 攻击者将恶意代码提交到目标网站的数据库中。
2. 用户打开目标网站时，网站服务端将恶意代码从数据库取出，拼接在 HTML 中返回给浏览器。
3. 用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
4. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

- 这种攻击常见于带有用户保存数据的网站功能，如论坛发帖、商品评论、用户私信等。

### 示例

接受数据时没有过滤

```js
$('#comments').val('');
```

记录数据时没有进行过滤

```js
comments.push({ username, content: req.body.comment });
```

获取数据后没有进行过滤

```js
$.each(res.comments, (index, comment) => {
  console.log(comment)
  lists += `<li class="list-group-item"><span>${comment.username}:</span> ${comment.content}</li>`;
});
```

导致恶意代码持久化

### 防范手段

防范存储型 XSS 攻击，需要我们增加字符串的过滤：前端输入时过滤/服务端增加过滤/前端输出时过滤

简单来说：全都是不可信的

转义储存

```js
function encodeHtml(str) {
  return str.replace(/"/g, '&quot;')
    .replace(/'/g, '&apos;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;');
}
comments2.push({ username, content: encodeHtml(req.body.comment) });
```

转义接收、转义获取

## 攻击目的

攻击者使被攻击者在浏览器中执行脚本后，如果需要收集来自被攻击者的数据（如 cookie 或其他敏感信息），可以自行架设一个网站，让被攻击者通过 JavaScript 等方式把收集好的数据作为参数提交，随后以 [数据库](https://zh.wikipedia.org/wiki/数据库) 等形式记录在攻击者自己的服务器上。

常用的**XSS**攻击手段和目的有：

- 盗用 cookie，获取敏感信息。
- 利用植入 Flash，通过 [crossdomain](https://zh.wikipedia.org/w/index.php?title=Crossdomain&action=edit&redlink=1) 权限设置进一步获取更高权限；或者利用 Java 等得到类似的操作。
- 利用 iframe、frame、XMLHttpRequest 或上述 Flash 等方式，以（被攻击）用户的身份执行一些管理动作，或执行一些一般的如发 [微博](https://zh.wikipedia.org/wiki/微博)、加好友、发私信等操作。
- 利用可被攻击的域受到其他域信任的特点，以受信任来源的身份请求一些平时不允许的操作，如进行不当的投票活动。
- 在访问量极大的一些页面上的 XSS 可以攻击一些小型网站，实现 [DDoS](https://zh.wikipedia.org/wiki/DDoS) 攻击的效果。

## 漏洞的防御和利用

### 输入内容长度控制

对于不受信任的输入，都应该限定一个合理的长度。虽然无法完全防止 XSS 发生，但可以增加 XSS 攻击的难度。

### 过滤特殊字符

**innerHTML**

![dom-interface](programming/font-end/primitive/browser-api/dom-interface.md#innerHTML)

**textContent**

![dom-interface](programming/font-end/primitive/browser-api/dom-interface.md#textContent)

**document.createTextNode**

![dom-interface](programming/font-end/primitive/browser-api/dom-interface.md#document%20createTextNode)

**HTML 过滤**

避免 XSS 的方法之一主要是将用户所提供的内容进行过滤，许多语言都有提供对 [HTML](https://zh.wikipedia.org/wiki/HTML) 的过滤：

- [PHP](https://zh.wikipedia.org/wiki/PHP) 的 `htmlentities()或是htmlspecialchars()`。
- [Python](https://zh.wikipedia.org/wiki/Python) 的 `cgi.escape()`。
- [ASP](https://zh.wikipedia.org/wiki/ASP) 的 `Server.HTMLEncode()`。
- [ASP.NET](https://zh.wikipedia.org/wiki/ASP.NET) 的 `Server.HtmlEncode()` 或功能更强的 [Microsoft Anti-Cross Site Scripting Library](http://msdn.microsoft.com/en-us/library/aa973813.aspx)
- [Java](https://zh.wikipedia.org/wiki/Java) 的 [xssprotect (Open Source Library)](http://code.google.com/p/xssprotect/)。
- [Node.js](https://zh.wikipedia.org/wiki/Node.js) 的 node-validator。

  ```js
  function encodeHtml(str) {
    return str.replace(/"/g, '&quot;')
      .replace(/'/g, '&apos;')
      .replace(/</g, '&lt;')
      .replace(/>/g, '&gt;');
  }
  ```

对于部分输入，可以限定不能包含特殊字符或者仅能输入数字等。

### 使用 HTTP 头 指定类型

很多时候可以使用 [HTTP头](https://zh.wikipedia.org/wiki/HTTP头字段) 指定内容的类型，使得输出的内容避免被作为 HTML 解析。如在 [PHP](https://zh.wikipedia.org/wiki/PHP) 语言中使用以下代码：

```php
<?php
   header('Content-Type: text/javascript; charset=utf-8');
?>
```

即可强行指定输出内容为文本/JavaScript 脚本（顺便指定了内容编码），而非可以引发攻击的 HTML。

### CSP

<https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CSP>

内容安全策略 ([CSP](https://developer.mozilla.org/en-US/docs/Glossary/CSP)) 是一个额外的安全层，用于检测并削弱某些特定类型的攻击，包括跨站脚本 ([XSS](https://developer.mozilla.org/en-US/docs/Glossary/XSS)) 和数据注入攻击等。无论是数据盗取、网站内容污染还是散发恶意软件，这些攻击都是主要的手段。

在服务端使用 HTTP 的响应 `Content-Security-Policy` 头部来指定策略，或者在前端设置 `meta` 标签。

例如下面的配置只允许加载同域下的资源：

```js
Content-Security-Policy: default-src 'self'
```

```html
<meta http-equiv="Content-Security-Policy" content="form-action 'self';">
```

只允许加载 HTTPS 协议图片

```html
Content-Security-Policy: img-src https://*
```

允许加载任何来源框架

```
Content-Security-Policy: child-src 'none'
```

前端和服务端设置 CSP 的效果相同，但是 `meta` 无法使用 `report`

严格的 CSP 在 XSS 的防范中可以起到以下的作用：

- 禁止加载外域代码，防止复杂的攻击逻辑。
- 禁止外域提交，网站被攻击后，用户的数据不会泄露到外域。
- 禁止内联脚本执行（规则较严格，目前发现 GitHub 使用）。
- 禁止未授权的脚本执行（新特性，Google Map 移动版在使用）。
- 合理使用上报可以及时发现 XSS，利于尽快修复问题。

<https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Security-Policy__by_cnvoid>

### 其他安全措施

HTTP-only Cookie: 禁止 JavaScript 读取某些敏感 Cookie，攻击者完成 XSS 注入后也无法窃取此 Cookie。

验证码：防止脚本冒充用户提交危险操作。

## XSS 检测

使用通用 XSS 攻击字串手动检测 XSS 漏洞

```js
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e
```

能够检测到存在于 HTML 属性、HTML 文字内容、HTML 注释、跳转链接、内联 JavaScript 字符串、内联 CSS 样式表等多种上下文中的 XSS 漏洞，也能检测 eval()、setTimeout()、setInterval()、Function()、innerHTML、document.write() 等 DOM 型 XSS 漏洞，并且能绕过一些 XSS 过滤器。

# 跨站请求伪造 CSRF

[Cookie 与安全](http-session.md#Cookie%20与安全)

跨站请求伪造（英语：Cross-site request forgery），也被称为 one-click attack 或者 session riding，通常缩写为 **CSRF** 或者 **XSRF**， 是一种挟制用户在当前已登录的 Web 应用程序上执行非本意的操作的攻击方法。跟 [跨网站脚本](https://zh.wikipedia.org/wiki/跨網站指令碼)（XSS）相比，**XSS** 利用的是用户对指定网站的信任，CSRF 利用的是网站对用户网页浏览器的信任。

## 攻击的细节

跨站请求攻击，简单地说，是攻击者通过一些技术手段欺骗用户的浏览器去访问一个自己曾经认证过的网站并运行一些操作（如发邮件，发消息，甚至财产操作如转账和购买商品）。

由于浏览器曾经认证过，所以被访问的网站会认为是真正的用户操作而去运行。

这利用了 web 中用户身份验证的一个漏洞：**简单的身份验证只能保证请求发自某个用户的浏览器，却不能保证请求本身是用户自愿发出的**。

### 攻击流程

1. 受害者登录 A 站点，并保留了登录凭证（Cookie）
2. 攻击者诱导受害者访问了站点 B
3. 站点 B 向站点 A 发送了一个请求，**浏览器会默认携带站点 A 的 Cookie 信息**
4. 站点 A 接收到请求后，对请求进行验证，并确认是受害者的凭证，误以为是无辜的受害者发送的请求。
5. 站点 A 以受害者的名义执行了站点 B 的请求。
6. 攻击完成，攻击者在受害者不知情的情况下，冒充受害者完成了攻击。

   ![img](/img/user/programming/basic/cs-basic/network-security/16abb8d5ab69386f.jpg)

**例子**

1. 进入 CSRF 目录，运行 server.js，端口号是 3001 (runcode 就行)
2. 在控制台: node server2.js，端口号 3002
3. 浏览器中访问 `http://localhost:3001/`，没有登录的情况下自动跳转登录页
4. 使用 loki/loki 登录，可以看到 loki 的账号有 10W 的余额，loki 已经登录了，cookie 已经有了

> 浏览器保存了 `http://localhost:3001/` 的 cookie

5. 这个时候，有人给你发了个钓鱼网站的链接: `http://localhost:3002/fish.html`，你点过去了，你的钱就被偷偷偷走了~~~

> 在 `http://localhost:3002/fish.html` 页面内，通过表单向 `http://localhost:3001/api/transfer` 发起了转账的请求，浏览器识别到是对 `http://localhost:3001` 的请求，就把刚刚保存的 cookie 一起发送，服务器误以为是用户转账

6. loki 的钱在不知不觉中就被转到了 yvette 的账户

此外，攻击者也不需要控制放置恶意网址的网站。例如他可以将这种地址藏在论坛，博客等任何 [用户生成内容](https://zh.wikipedia.org/wiki/用戶生成內容) 的网站中。这意味着*如果服务端没有合适的防御措施的话，用户即使访问熟悉的可信网站也有受攻击的危险*

透过例子能够看出，攻击者并不能通过 CSRF 攻击来直接获取用户的账户控制权，也不能直接窃取用户的任何信息。他们能做到的，是*欺骗用户浏览器，让其以用户的名义运行操作*

## 攻击特点

攻击通常在第三方网站发起，如图上的站点 B，站点 A 无法防止攻击发生。

攻击利用受害者在被攻击网站的登录凭证，冒充受害者提交操作；**并不会去获取 cookie 信息 (cookie 有同源策略)**

跨站请求可以用各种方式：图片 URL、超链接、CORS、Form 提交等等 (来源不明的链接，不要点击)

## 防御措施

### 验证码

验证码能够防御 CSRF 攻击，但是我们不可能每一次交互都需要验证码，否则用户的体验会非常差，但是我们可以在转账，交易等操作时，增加验证码，确保我们的账户安全。

在转账之前需要核对验证码，而攻击者无法知晓验证码是什么

### 检查 Referer 字段

HTTP 头中有一个 Referer 字段，这个字段用以标明请求来源于哪个地址。在处理敏感数据请求时，通常来说，Referer 字段应和请求的地址位于同一域名下。

以上文银行操作为例，Referer 字段地址通常应该是转账按钮所在的网页地址，应该也位于 `http://localhost:3001/` 之下。

这种办法简单易行，工作量低，仅需要在关键访问处增加一步校验。

但这种办法也有其局限性，因其完全依赖浏览器发送正确的 Referer 字段。虽然 http 协议对此字段的内容有明确的规定，但并无法保证来访的浏览器的具体实现，亦无法保证浏览器没有安全漏洞影响到此字段。并且也存在攻击者攻击某些浏览器，篡改其 Referer 字段的可能。

### 添加校验 token（主流）

[Token](http-session.md#Token)

由于 CSRF 的本质在于攻击者欺骗用户去访问自己设置的地址，所以如果要求在访问敏感数据请求时，要求用户浏览器提供不保存在 [cookie](https://zh.wikipedia.org/wiki/Cookie) 中，并且攻击者无法伪造的数据作为校验，那么攻击者就无法再运行 CSRF 攻击。

这种数据通常是窗体中的一个数据项。服务器将其生成并附加在窗体中，其内容是一个伪随机数。当客户端通过窗体提交请求时，这个伪随机数也一并提交上去以供校验。正常的访问时，客户端浏览器能够正确得到并传回这个伪随机数，而通过 CSRF 传来的欺骗性攻击中，攻击者无从事先得知这个伪随机数的值，服务端就会因为校验 token 的值为空或者错误，拒绝这个可疑请求。

### Samesite Cookie 属性

为了从源头上解决这个问题，Google 起草了一份草案来改进 HTTP 协议，为 Set-Cookie 响应头新增 Samesite 属性，它用来标明这个 Cookie 是个“同站 Cookie”，同站 Cookie 只能作为第一方 Cookie，不能作为第三方 Cookie，Samesite 有两个属性值，分别是 Strict 和 Lax。

部署简单，并能有效防御 CSRF 攻击，但是存在兼容性问题。

**Samesite=Strict**

`Samesite=Strict` 被称为是严格模式,表明这个 Cookie 在任何情况都不可能作为第三方的 Cookie，有能力阻止所有 CSRF 攻击。

此时，我们在 B 站点下发起对 A 站点的任何请求，A 站点的 Cookie 都不会包含在 cookie 请求头中。

**Samesite=Lax**

`Samesite=Lax` 被称为是宽松模式，与 Strict 相比，放宽了限制，允许发送安全 HTTP 方法带上 Cookie，如 `Get` / `OPTIONS` 、`HEAD` 请求. 但是不安全 HTTP 方法，如： `POST`, `PUT`, `DELETE` 请求时，不能作为第三方链接的 Cookie

## 对比

跟跨网站脚本（XSS）相比，XSS 利用的是用户对指定网站的信任，CSRF 利用的是网站对用户网页浏览器的信任。

# 点击劫持

点击劫持是指在一个 Web 页面中隐藏了一个透明的 iframe，用外层假页面诱导用户点击，实际上是在隐藏的 frame 上触发了点击事件进行一些用户不知情的操作。

## 攻击流程

1. 攻击者构建了一个非常有吸引力的网页【不知道哪些内容对你们来说有吸引力，我就不写页面了，偷个懒】
2. 将被攻击的页面放置在当前页面的 `iframe` 中
3. 使用样式将 iframe 叠加到非常有吸引力内容的上方
4. 将 iframe 设置为 100% 透明
5. 你被诱导点击了网页内容，你以为你点击的是 XX，而实际上，你成功被攻击了。

## 防御措施

### Frame Busting

```js
if ( top.location != window.location ){
    top.location = window.location
}
```

需要注意的是: HTML5 中 iframe 的 `sandbox` 属性、IE 中 iframe 的 `security` 属性等，都可以限制 iframe 页面中的 JavaScript 脚本执行，从而可以使得 frame busting 失效。

### X-Frame-Options

X-FRAME-OPTIONS 是微软提出的一个 http 头，专门用来防御利用 iframe 嵌套的点击劫持攻击。并且在 IE8、Firefox3.6、Chrome4 以上的版本均能很好的支持。

可以设置为以下值:

- DENY: 拒绝任何域加载
- SAMEORIGIN: 允许同源域下加载
- ALLOW-FROM: 可以定义允许 frame 加载的页面地址

# 安全扫描工具

<https://juejin.im/post/5cd6ad7a51882568d3670a8e#heading-14>

# 密码安全

密码安全虽然大多是后端的事情，但是作为一名优秀的前端程序员也需要熟悉这方面的知识。

## 加盐

对于密码存储来说，必然是不能明文存储在数据库中的，否则一旦数据库泄露，会对用户造成很大的损失。并且不建议只对密码单纯通过加密算法加密，因为存在彩虹表的关系。

通常需要对密码加盐，然后进行几次不同加密算法的加密。

```js
// 加盐也就是给原密码添加字符串，增加原密码长度
sha256(sha1(md5(salt + password + salt)))
```

但是加盐并不能阻止别人盗取账号，只能确保即使数据库泄露，也不会暴露用户的真实密码。一旦攻击者得到了用户的账号，可以通过暴力破解的方式破解密码。对于这种情况，通常使用验证码增加延时或者限制尝试次数的方式。并且一旦用户输入了错误的密码，也不能直接提示用户输错密码，而应该提示账号或密码错误

# SQL 注入攻击

SQL 注入攻击指的是攻击者在 HTTP 请求中注入恶意的 SQL 代码，服务器使用参数构建数据库 SQL 命令时，恶意 SQL 被一起构

造，破坏原有 SQL 结构，并在数据库中执行，达到编写程序时意料之外结果的攻击行为。

详细资料可以参考： [《Web 安全漏洞之 SQL 注入》](https://juejin.im/post/5bd5b820e51d456f72531fa8) [《如何防范常见的 Web 攻击》](http://blog.720ui.com/2016/security_web/#SQL%E6%B3%A8%E5%85%A5%E6%94%BB%E5%87%BB)

# DDOS 攻击

## 概念

DDOS 攻击，它在短时间内发起大量请求，耗尽服务器的资源，无法响应正常的访问，造成网站实质下线。

DDOS 里面的 DOS 是 denial of service（停止服务）的缩写，表示这种攻击的目的，就是使得服务中断。最前面的那个 D 是 distributed （分布式），表示攻击不是来自一个地方，而是来自四面八方，因此更难防。你关了前门，他从后门进来；你关了后门，他从窗口跳起来。

> 举例来说，我开了一家餐厅，正常情况下，最多可以容纳 30 个人同时进餐。你直接走进餐厅，找一张桌子坐下点餐，马上就可以吃到东西。
>
> 很不幸，我得罪了一个流氓。他派出 300 个人同时涌进餐厅。这些人看上去跟正常的顾客一样，每个都说 " 赶快上餐 "。但是，餐厅的容量只有 30 个人，根本不可能同时满足这么多的点餐需求，加上他们把门口都堵死了，里三层外三层，正常用餐的客人根本进不来，实际上就把餐厅瘫痪了。

简单来说：爆吧

又称：Flooding 攻击

## CC 攻击

DDOS 不是一种攻击，而是一大类攻击的总称。它有几十种类型，新的攻击方法还在不断发明出来。网站运行的各个环节，都可以是攻击目标。只要把一个环节攻破，使得整个流程跑不起来，就达到了瘫痪服务的目的。

其中，比较常见的一种攻击是 cc 攻击。它就是简单粗暴地送来大量正常的请求，超出服务器的最大承受量，导致宕机。我遭遇的就是 cc 攻击，最多的时候全世界大概 20 多个 IP 地址轮流发出请求，每个地址的请求量在每秒 200 次~300 次。我看访问日志的时候，就觉得那些请求像洪水一样涌来，一眨眼就是一大堆，几分钟的时间，日志文件的体积就大了 100MB。说实话，这只能算小攻击，但是我的个人网站没有任何防护，服务器还是跟其他人共享的，这种流量一来立刻就下线了

本文以下的内容都是针对 cc 攻击

### 防御措施

#### 备用网站

防范 DDOS 的第一步，就是你要有一个备份网站，或者最低限度有一个临时主页。生产服务器万一下线了，可以立刻切换到备份网站，不至于毫无办法。

备份网站不一定是全功能的，如果能做到全静态浏览，就能满足需求。最低限度应该可以显示公告，告诉用户，网站出了问题，正在全力抢修。我的个人网站下线的时候，我就做了一个 [临时主页](http://reverent-thompson-95d408.netlify.com/)，很简单的几行 [HTML 代码](https://github.com/ruanyf/website/blob/master/src/index.html)。

这种临时主页建议放到 [Github Pages](https://pages.github.com/) 或者 [Netlify](https://www.netlify.com/)，它们的带宽大，可以应对攻击，而且都支持绑定域名，还能从源码自动构建。

#### HTTP 请求的拦截

如果恶意请求有特征，对付起来很简单：直接拦截它就行了。

HTTP 请求的特征一般有两种：IP 地址和 User Agent 字段。比如，恶意请求都是从某个 IP 段发出的，那么把这个 IP 段封掉就行了。或者，它们的 User Agent 字段有特征（包含某个特定的词语），那就把带有这个词语的请求拦截。

拦截可以在三个层次做。

**专用硬件**

+ Web 服务器的前面可以架设硬件防火墙，专门过滤请求。这种效果最好，但是价格也最贵。

**本机防火墙**

- 操作系统都带有软件防火墙，Linux 服务器一般使用 [iptables](https://wiki.archlinux.org/index.php/Iptables_(简体中文))。比如，拦截 IP 地址 `1.2.3.4` 的请求，可以执行下面的 [命令](https://www.howtogeek.com/177621/the-beginners-guide-to-iptables-the-linux-firewall/)。

**Web 服务器**

- Web 服务器也可以过滤请求。拦截 IP 地址 `1.2.3.4`，nginx 的 [写法](https://help.dreamhost.com/hc/en-us/articles/216456127-Blocking-IPs-with-Nginx) 如下。

  ```js
  location / {
    deny 1.2.3.4;
  }
  ```

- Apache 的 [写法](https://stackoverflow.com/questions/3264233/apache-block-an-ip-address-from-accessing-the-website) 是在 `.htaccess` 文件里面，加上下面一段。

  ```xml
  <RequireAll>
      Require all granted
      Require not ip 1.2.3.4
  </RequireAll>
  ```

- 如果想要更精确的控制（比如自动识别并拦截那些频繁请求的 IP 地址），就要用到 [WAF](https://en.wikipedia.org/wiki/Web_application_firewall)。这里就不详细介绍了，nginx 这方面的设置可以参考 [这里](https://www.nginx.com/blog/mitigating-ddos-attacks-with-nginx-and-nginx-plus/) 和 [这里](https://www.nginx.com/blog/rate-limiting-nginx/)。
- Web 服务器的拦截非常消耗性能，尤其是 Apache。稍微大一点的攻击，这种方法就没用了。

#### 带宽扩容

上一节的 HTTP 拦截有一个前提，就是请求必须有特征。但是，真正的 DDOS 攻击是没有特征的，它的请求看上去跟正常请求一样，而且来自不同的 IP 地址，所以没法拦截。这就是为什么 DDOS 特别难防的原因。

当然，这样的 DDOS 攻击的成本不低，普通的网站不会有这种待遇。不过，真要遇到了该怎么办呢，有没有根本性的防范方法呢？

答案很简单，就是设法把这些请求都消化掉。30 个人的餐厅来了 300 人，那就想办法把餐厅扩大（比如临时再租一个门面，并请一些厨师），让 300 个人都能坐下，那么就不影响正常的用户了。对于网站来说，就是在短时间内急剧扩容，提供几倍或几十倍的带宽，顶住大流量的请求。这就是为什么云服务商可以提供防护产品，因为他们有大量冗余带宽，可以用来消化 DDOS 攻击。

一个朋友传授了一个方法，给我留下深刻印象。某云服务商承诺，每个主机保 5G 流量以下的攻击，他们就一口气买了 5 个。网站架设在其中一个主机上面，但是不暴露给用户，其他主机都是镜像，用来面对用户，DNS 会把访问量均匀分配到这四台镜像服务器。一旦出现攻击，这种架构就可以防住 20G 的流量，如果有更大的攻击，那就买更多的临时主机，不断扩容镜像。

#### CDN

CDN 指的是网站的静态内容分发到多个服务器，用户就近访问，提高速度。因此，CDN 也是带宽扩容的一种方法，可以用来防御 DDOS 攻击。

网站内容存放在源服务器，CDN 上面是内容的缓存。用户只允许访问 CDN，如果内容不在 CDN 上，CDN 再向源服务器发出请求。这样的话，只要 CDN 够大，就可以抵御很大的攻击。不过，这种方法有一个前提，网站的大部分内容必须可以静态缓存。对于动态内容为主的网站（比如论坛），就要想别的办法，尽量减少用户对动态数据的请求。

上一节提到的镜像服务器，本质就是自己搭建一个微型 CDN。各大云服务商提供的 [高防 IP](https://baike.baidu.com/item/高防服务器)，背后也是这样做的：网站域名指向高防 IP，它提供一个缓冲层，清洗流量，并对源服务器的内容进行缓存。

这里有一个关键点，一旦上了 CDN，千万不要泄露源服务器的 IP 地址，否则攻击者可以绕过 CDN 直接攻击源服务器，前面的努力都白费。搜一下 "[绕过 CDN 获取真实 IP 地址](https://www.baidu.com/s?wd=cdn 真实 ip)"，你就会知道国内的黑产行业有多猖獗。

[cloudflare](https://www.cloudflare.com/) 是一个免费 CDN 服务，并提供防火墙，高度推荐。我还要感谢 v2ex.com 的站长 @livid 热情提供帮助，我现在用的就是他们的 [CDN](https://www.zenlayer.cn/) 产品。

### PS

攻击者看来订阅了我的微博。昨天这篇文章发布没多久，我就又遭受了攻击，他绕过 CDN 直接攻击源服务器（我不知道 IP 地址怎么泄漏的），流量还大过上一次。

感谢腾讯云的朋友，提供了一个高防 IP，使得网站可以重新上线。现在，我的防护措施是，源服务器前面有 CDN。如果攻击域名，CDN 可以挡住；如果直接攻击源服务器，我买了弹性 IP ，可以动态挂载主机实例，受到攻击就换一个地址。这只是一个技术博客，内容都是免费的，要防到这种地步，我也是无语了。

## SYN 攻击

[https://baike.baidu.com/item/SYN%E6%94%BB%E5%87%BB/14762413](https://baike.baidu.com/item/SYN 攻击/14762413)

# 重放攻击

[https://baike.baidu.com/item/%E9%87%8D%E6%94%BE%E6%94%BB%E5%87%BB](https://baike.baidu.com/item/重放攻击)

# SSRF 攻击

<https://baijiahao.baidu.com/s?id=1593973193350471506&wfr=spider&for=pc>

# 中间人攻击

## DNS 欺骗

[https://baike.baidu.com/item/DNS%E6%AC%BA%E9%AA%97](https://baike.baidu.com/item/DNS欺骗)

# 社会工程攻击

[https://baike.baidu.com/item/%E7%A4%BE%E4%BC%9A%E5%B7%A5%E7%A8%8B%E6%94%BB%E5%87%BB/732063](https://baike.baidu.com/item/社会工程攻击/732063)

## 网络钓鱼

[https://baike.baidu.com/item/%E7%BD%91%E7%BB%9C%E9%92%93%E9%B1%BC/1401858?fr=aladdin](https://baike.baidu.com/item/网络钓鱼/1401858?fr=aladdin)

# 网络安全相关的 HTTP 字段

与网络安全相关的 HTTP 头字段有很多，它们在保护网站、用户数据和隐私方面起着重要作用。以下是一些关键的 HTTP 头字段以及它们的作用：

1. Content-Security-Policy (CSP): CSP 头用于定义允许加载哪些资源的策略。它可以减少跨站脚本攻击 (XSS) 和数据注入攻击的风险，限制了能够从哪些来源加载资源。
2. X-Content-Type-Options: 这个头字段用于指示浏览器不要猜测响应的 MIME 类型，从而防止 MIME 类型混淆攻击。
3. X-Frame-Options: 通过设置这个头字段，你可以控制是否允许在 `<frame>`, `<iframe>`, `<object>` 或 `<embed>` 元素中加载页面。它可以防止点击劫持攻击。
4. Strict-Transport-Security (HSTS): HSTS 头指示浏览器仅通过 HTTPS 访问站点，防止中间人攻击和 SSLstrip 攻击。
5. Public-Key-Pins (HPKP): HPKP 头可以强制浏览器只信任预定义的公钥证书，以减少中间人攻击风险。
6. X-XSS-Protection: 这个头用于启用或禁用浏览器内置的跨站脚本 (XSS) 过滤器。
7. Referrer-Policy: 这个头字段控制浏览器在请求中包含的 Referrer 信息，可以减少信息泄露风险。
8. Content-Disposition: 这个头字段可以告知浏览器如何处理内容，可以防止一些文件下载的安全问题，如强制下载和执行。
9. Feature-Policy: Feature-Policy 头可以限制或允许浏览器访问某些 API 或功能，减少恶意站点的滥用。

这只是一些与网络安全相关的 HTTP 头字段，还有其他头字段也可以用于加强网站的安全性。合理配置这些头字段可以帮助减少各种网络攻击和安全问题。

# FAQ

#faq/basic

```
发现小概率发生通过域名被劫持，以下对解决此问题无帮助的是（ A ）。
  A. 部署备用服务器
  B. https
  C. httpdns
  D. ip直连
```
