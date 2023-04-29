---
{"dg-publish":true,"permalink":"/programming/basic/cs-basic/network-communication/"}
---


5/1

6/29

# 数据的载体

Cookie

+ HTTP 的头部扩展

Session

+ 依赖于 cookie

JWT

+ 本质是 token
+ 依赖于 cookie 或者 header

以上都是常见的鉴权方式

WebStorage

+ 与 cookie 地位相同

IndexDB

+ 与 cookie 地位相同

# Cookie

## 概述

HTTP Cookie（也叫 Web Cookie 或浏览器 Cookie）是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。

通常，它用于告知服务端两个请求是否来自同一浏览器，如保持用户的登录状态。Cookie 使基于 [无状态](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview#HTTP_is_stateless_but_not_sessionless) 的 HTTP 协议记录稳定的状态信息成为了可能。

Cookie 主要用于以下三个方面：

- 会话状态管理（如用户登录状态、购物车、游戏分数或其它需要记录的信息）
- 个性化设置（如用户自定义设置、主题等）
- 浏览器行为跟踪（如跟踪分析用户行为等）

Cookie 曾一度用于客户端数据的存储，因当时并没有其它合适的存储办法而作为唯一的存储手段，但现在随着现代浏览器开始支持各种各样的存储方式，Cookie 的储存功能渐渐被代替。由于服务器指定 Cookie 后，浏览器的每次请求都会携带 Cookie 数据，会带来额外的性能开销（尤其是在移动环境下）。新的浏览器 API 已经允许开发者直接将数据存储到本地，如使用 [Web storage API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Storage_API) （本地存储和会话存储）或 [IndexedDB](https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API) 。

### 工作原理 @@@ #

首先，通过 HTTP 头设置和检索 Cookie。如果浏览器发送请求到 [http://example.com](http://example.com/)，那么响应可能会返回一个标题，说 Set-Cookie：foo = bar。

浏览器存储此 cookie，并且在随后的任何 [http://example.com](http://example.com/) 请求，您的浏览器将发送 foo = bar 在 Cookie 请求头头。 (或至少直到 cookie 过期或被删除。)

+ 任何到 [http://example.com](http://example.com/) 的请求，浏览器会发送 foo = bar cookie，**不管是谁发起请求或上下文是什么**。
+ 如果 [http://example2.com](http://example2.com/) 包含标记< img src =“http://example.com/img.jpg”/>;,则浏览器在取回<http://example.com/img.jpg>时将发送 cookie foo = bar，即使 [http://example2.com](http://example2.com/) 负责请求发送。

**注意**：Cookie 是浏览器加密储存在本地的，而不是储存在某个特定的网页

**注意**：如果向某个站点发送请求的时候，存在该站点的 Cookie 就会随着请求一起发送，不论是谁发起请求或者上下文是什么

> 可以在 B 网站通过图片、表单请求、Ajax 请求等等手段请求 A 站点，浏览器会自动携带 A 站点之前设置过的 Cookie
>
> 这也是造成 CSRF 攻击的真正原因

### 第三方 Cookie

每个 Cookie 都会有与之关联的域（Domain），如果 Cookie 的域和页面的域相同，那么我们称这个 Cookie 为*第一方 Cookie*（*first-party cookie*），如果 Cookie 的域和页面的域不同，则称之为*第三方 Cookie*（*third-party cookie*.）。

一个页面包含图片或存放在其他域上的资源（如图片广告）时，第一方的 Cookie 也只会发送给设置它们的服务器。

通过第三方组件发送的第三方 Cookie 主要用于广告和网络追踪。这方面可以看谷歌使用的 Cookie 类型（[types of cookies used by Google](https://www.google.com/policies/technologies/types/)）。大多数浏览器默认都允许第三方 Cookie，但是可以通过附加组件来阻止第三方 Cookie（如 [EFF](https://www.eff.org/) 的 [Privacy Badger](https://addons.mozilla.org/en-US/firefox/addon/privacy-badger-firefox/)）。

如果你没有公开你网站上第三方 Cookie 的使用情况，当它们被发觉时用户对你的信任程度可能受到影响。一个较清晰的声明（比如在隐私策略里面提及）能够减少或消除这些负面影响。在某些国家已经开始对 Cookie 制订了相应的法规，可以查看维基百科上例子 [cookie statement](https://wikimediafoundation.org/wiki/Cookie_statement)。

## Set-Cookie

### Set-Cookie 响应头部

当服务器收到 HTTP 请求时，服务器可以在**响应头**里面添加一个 `Set-Cookie` 选项。浏览器收到响应后通常会保存下 Cookie，之后对该服务器每一次请求中都通过 `Cookie` 请求头部将 Cookie 信息发送给服务器。

另外，Cookie 的过期时间、域、路径、有效期、适用站点都可以根据需要来指定。

服务器使用 `Set-Cookie` 响应头部向用户代理（一般是浏览器）发送 Cookie 信息。一个简单的 Cookie 可能像这样：

```js
Set-Cookie: <cookie名>=<cookie值>
```

服务器通过该头部告知客户端保存 Cookie 信息。

### Set-Cookie 的参数

其他可选的 cookie 参数会影响将 cookie 发送给服务器端的过程，主要有以下几种：

+ domain： cookie 域名

- path：表示 cookie 影响到的路径，匹配该路径才发送这个 cookie。
- expires 和 maxAge：告诉浏览器这个 cookie 什么时候过期，expires 是 UTC 格式时间，maxAge 是 cookie 多久后过期的相对时间。

  > 如果不设置，即产生 Session Cookie

- secure：当 secure 值为 true 时，cookie 在 HTTP 中是无效，在 HTTPS 中才有效。
- httpOnly：浏览器不允许脚本操作 document.cookie 去更改 cookie。一般情况下都应该设置这个为 true，这样可以避免被 xss 攻击拿到 cookie。

### 会话期 Cookie

Session Cookie 是最简单的 Cookie：浏览器关闭之后它会被自动删除，也就是说它仅在会话期内有效。

如果**不指定**过期时间（`Expires`）或者有效期（Max-Age），那么服务器发送的就会是会话期 cookie

> 注意：有些浏览器提供了会话恢复功能，这种情况下即使关闭了浏览器，会话期 Cookie 也会被保留下来，就好像浏览器从来没有关闭一样。

### 持久性 Cookie

和关闭浏览器便失效的会话期 Cookie 不同，持久性 Cookie 可以指定一个特定的过期时间（`Expires`）或有效期（`Max-Age`）。

```js
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;
```

> **提示：**当 Cookie 的过期时间被设定时，设定的日期和时间只与客户端相关，而不是服务端。

### Secure 和 HttpOnly

标记为 `Secure` 的 Cookie 只应通过被**HTTPS 协议**加密过的请求发送给服务端。但即便设置了 `Secure` 标记，敏感信息也不应该通过 Cookie 传输，因为 Cookie 有其固有的不安全性，`Secure ` 标记也无法提供确实的安全保障。

从 Chrome 52 和 Firefox 52 开始，不安全的站点（`http:`）无法使用 Cookie 的 `Secure` 标记。

为避免跨域脚本 ([XSS](https://developer.mozilla.org/en-US/docs/Glossary/XSS)) 攻击，通过 JavaScript 的 [`Document.cookie`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/cookie) API**无法访问**带有 `HttpOnly` 标记的 Cookie，它们只应该发送给服务端。如果包含服务端 Session 信息的 Cookie 不想被客户端 JavaScript 脚本调用，那么就应该为其设置 `HttpOnly` 标记。

### Cookie 的作用域

`Domain` 和 `Path` 标识定义了 Cookie 的 * 作用域：* 即 Cookie 应该发送给哪些 URL。

`Domain` 标识指定了哪些主机可以接受 Cookie。如果不指定，默认为**当前文档的主机**（**不包含子域名**）。如果指定了 `Domain`，则一般包含子域名。

- 例如，如果设置 `Domain=mozilla.org`，则 Cookie 也包含在子域名中（如 `developer.mozilla.org`）。

`Path` 标识指定了主机下的哪些路径可以接受 Cookie（该 URL 路径必须存在于请求 URL 中）。以字符 `%x2F` ("/") 作为路径分隔符，子路径也会被匹配。

例如，设置 `Path=/docs`，则以下地址都会匹配：

- `/docs`
- `/docs/Web/`
- `/docs/Web/HTTP`

### SameSite Cookies

`SameSite` Cookie 允许服务器要求某个 cookie 在跨站请求时不会被发送，从而可以阻止跨站请求伪造攻击（[CSRF](https://developer.mozilla.org/en-US/docs/Glossary/CSRF)）。但目前 `SameSite` Cookie 还处于实验阶段，并不是所有浏览器都支持。

## 处理 Cookie 的相关 API

### Koa2

koa 使用传递简单参数的 [cookies](https://github.com/jed/cookies) 模块。

`ctx.cookies.get(name, [options])`

+ 通过 `options` 获取 cookie `name`:

  - `signed` 所请求的 cookie 应该被签名

`ctx.cookies.set(name, value, [options])`

```js
router.post('/cookies', (ctx) => {
  if(ctx.cookies.get('isVisit')){
    ctx.response.body = '欢迎再次访问'
  }else{
    ctx.cookies.set('isVisit',1,{maxAge: 60 * 1000})
    ctx.response.body = '第一次访问'
  }
})
```

### Document.cookies

**读取 cookie**

- Document.cookie，获取并设置与当前文档相关联的 [cookie](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)。

  ```js
  allCookies = document.cookie;
  ```

- 在上面的代码中，allCookies 被赋值为一个字符串，该字符串包含所有的 Cookie，每条 cookie 以分号分隔 (即, `key=value ` 键值对)。

**写入 cookie**

- ```js
  document.cookie = newCookie;
  ```
- newCookie 是一个**键值对形式的字符串**。需要注意的是，用这个方法一次只能对一个 cookie 进行设置或更新。
- 以下可选的 cookie 属性值可以跟在键值对后，用来具体化对 cookie 的设定/更新，使用分号以作分隔：
  - `;path=path` (例如 '/', '/mydir') 如果没有定义，默认为当前文档位置的路径。
  - `;domain=domain` (例如 'example.com'， 'subdomain.example.com') 如果没有定义，默认为当前文档位置的路径的域名部分。与早期规范相反的是，在域名前面加 . 符将会被忽视，因为浏览器也许会拒绝设置这样的 cookie。如果指定了一个域，那么子域也包含在内。
  - `;max-age=max-age-in-seconds` (例如一年为 60\*60\*24\*365)
  - `;expires=date-in-GMTString-format` 如果没有定义，cookie 会在对话结束时过期
    - 这个值的格式参见 [Date.toUTCString()](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Date/toUTCString)
  - `;secure` (cookie 只通过 https 协议传输)
- cookie 的值字符串可以用 [encodeURIComponent()](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/encodeURIComponent) 来保证它不包含任何逗号、分号或空格 (cookie 值中禁止使用这些值).

  ```js
  document.cookie = "name=oeschger";
  document.cookie = "favorite_food=tripe";
  alert(document.cookie);
  // 显示: name=oeschger;favorite_food=tripe
  ```

+ 注意：客户端并没有提供读写 cookie 单个属性值的操作，需要引入一些简单的库
+ <https://developer.mozilla.org/en-US/docs/Web/API/Document/cookie/Simple_document.cookie_framework>

## Cookie 的大小与数量限制

参考：https://juejin.im/post/5aede266f265da0ba266e0ef#heading-15

|           | IE6.0 | IE7.0/8.0 | Opera | FF    | Safari | Chrome |
| --------- | ----- | --------- | ----- | ----- | ------ | ------ |
| 个数/个   | 20/域 | 50/域     | 30/域 | 50/域 | 无限制 | 53/域  |
| 大小/Byte | 4095  | 4095      | 4096  | 4097  | 4097   | 4097   |

因为浏览器对于 Cookie 在数量上是有限制的，如果超过了自然会有一些剔除策略。在这篇文章中 [Browser cookie restrictions](https://link.juejin.im?target=https%3A%2F%2Fwww.nczonline.net%2Fblog%2F2008%2F05%2F17%2Fbrowser-cookie-restrictions%2F) 提到的剔除策略如下：

> The least recently used (LRU) approach automatically kicks out the oldest cookie when the cookie limit has been reached in order to allow the newest cookie some space. Internet Explorer and Opera use this approach.

最近最少使用（LRU）方法：在达到 cookie 限制时自动地剔除最老的 cookie，以便腾出空间给许最新的 cookie。Internet Explorer 和 Opera 使用这种方法。

> Firefox does something strange: it seems to randomly decide which cookies to keep although the last cookie set is always kept. There doesn’t seem to be any scheme it’s following at all. The takeaway? Don’t go above the cookie limit in Firefox.

Firefox 决定随机删除 Cookie 集中的一个 Cookie，并没有什么章法。所以最好不要超过 Firefox 中的 Cookie 限制。

## Cookie 与安全

### 第三方 Cookie 引起的 CSRF

### 禁止追踪 Do-Not-Track

虽然并没有法律或者技术手段强制要求使用 [`DNT`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/DNT)，但是通过 [`DNT`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/DNT) 可以告诉 Web 程序不要对用户行为进行追踪或者跨站追踪。查看 [`DNT`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/DNT) 以获取更多信息。

### 欧盟 Cookie 指令

关于 Cookie，欧盟已经在 [2009/136/EC指令](http://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32009L0136) 中提了相关要求，该指令已于 2011 年 5 月 25 日生效。虽然指令并不属于法律，但它要求欧盟各成员国通过制定相关的法律来满足该指令所提的要求。当然，各国实际制定法律会有所差别。

该欧盟指令的大意：在征得用户的同意之前，网站不允许通过计算机、手机或其他设备存储、检索任何信息。自从那以后，很多网站都在网站声明中添加了相关说明，告诉用户他们的 Cookie 将用于何处。

可以通过 [维基百科的相关内容](https://en.wikipedia.org/wiki/HTTP_cookie#EU_cookie_directive) 获取最新的各国法律和更精确的信息。

### 僵尸 Cookie 和删不掉的 Cookie

Cookie 的一个极端使用例子是僵尸 Cookie（或称之为“删不掉的 Cookie”），这类 Cookie 较难以删除，甚至删除之后会自动重建。它们一般是使用 [Web storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)、Flash 本地共享对象或者其他技术手段来达到的。相关内容可以看：

https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies#%E4%BC%9A%E8%AF%9D%E6%9C%9FCookie

# Session

## 概述

session 首先是一个抽象的概念，**指代多个有关联的 http 请求所构成的一个会话**。

session 常常用来指代为了实现一个会话，需要在客户端和服务端之间传输的信息。

- 这些信息可以是会话所需的所有内容（包括用户身份、相关数据等）
- 也可以只是一个 id，让服务端可能从后台检索到相关数据，这也是实际系统中最常用的方式。

如上所述，这里的信息可以是整个 session 的具体数据，也可以只是**session 的标识**。

### 使用 Cookie 储存 Session

可以使用 Cookie 储存 session 的具体信息

**优点：**

- 服务器解析简单，直接读取 Cookie 就可以确认用户

**缺点：**

- cookie 虽然很方便，但是使用 cookie 有一个很大的弊端，cookie 中的所有数据在客户端就可以被修改，数据非常容易被伪造，那么一些重要的数据就不能存放在 cookie 中了
- 而且如果 cookie 中数据字段太多会影响传输效率。

### 使用 Cookie 储存 Session 标识

最常用的手段

`session_id` 通常是存放在客户端的 cookie 中

> 在 express-session 中，默认是 `connect.sid` 这个字段
>
> 在 koa-session 中，默认是 `koa:sess` 这个字段

当请求到来时，服务端检查 cookie 中保存的 `session_id` 并通过这个 `session_id` 与服务器端的 `session data` 关联起来，进行数据的保存和修改。

> 实际运作：
>
> 当用户浏览一个网页时，服务端随机产生一个 1024 比特长的字符串，然后存在 cookie 的某个字段中。
>
> 当用户下次访问时，cookie 会带有这个字符串，服务器通过访问 cookie 中的对应字段，然后从服务器的存储中取出上次记录在该用户身上的数据。

完整的 session 可以存放在

- 内存：开发环境存内存就好了。一般的小程序为了省事，如果不涉及状态共享的问题，用内存 session 也没问题。但内存 session 除了省事之外，没有别的好处。
- redis 或 memcached 等缓存中：最常见的方案
- 数据库中：查询效率较低

**优点**

+ 由于字符串是随机产生的，而且位数足够多，所以也不担心有人能够伪造。

### 使用片段标识符储存 Session 标识

实现 session 的一种方式就是在每个请求的参数或者数据中带上相关信息，这种方式的好处是不受 cookie 可用性的限制。

> 我们在登录某些网站的时候会发现 url 里有长长的一串不规则字符，往往就是编码了用户的 session 信息。但是这种方式也会受到请求长度的限制，使用起来也不方便，而且还有安全性上的 [隐患](https://security.stackexchange.com/questions/14093/why-is-passing-the-session-id-as-url-parameter-insecure)。

### 登录认证

## Koa-session

### 基本使用

```js
const session = require('koa-session');
const Koa = require('koa');
const app = new Koa();
app.keys = ['some secret hurr'];

const CONFIG = {
  key: 'koa:sess', /** (string) cookie key (default is koa:sess) */
  
  /** (number || 'session') maxAge in ms (default is 1 days) */
  /** 'session' will result in a cookie that expires when session/browser is closed */
  /** Warning: If a session cookie is stolen, this cookie will never expire */
  maxAge: 86400000,
  overwrite: true, /** (boolean) can overwrite by js or not (default true) */
  httpOnly: true, /** (boolean) httpOnly or not (default true) */
  signed: true, /** (boolean) signed or not (default true) */
  rolling: false, /** (boolean) Force a session identifier cookie to be set on every response. The expiration is reset to the original maxAge, resetting the expiration countdown. (default is false) */
  renew: false, /** (boolean) renew session when session is nearly expired, so we can always keep user logged in. (default is false)*/
};

app.use(session(CONFIG, app));

app.use(ctx => {
  // ignore favicon
  if (ctx.path === '/favicon.ico') return;

  let n = ctx.session.views || 0;
  ctx.session.views = ++n;
  ctx.body = n + ' views';
});

app.listen(3000);
```

我们看到这个在这个会话状态中，session 中保存了页面访问次数，每次请求的时候，会增加计数再把结果返回给用户。

对于 session 的存储方式，koa-session 同时支持 cookie 和外部存储。

### 使用 Cookie 储存 Session

默认配置下，会使用 cookie 来存储 session 信息，也就是实现了一个 "cookie session"。这种方式对服务端是比较轻松的，不需要额外记录任何 session 信息，但是也有不少限制，比如大小的限制以及安全性上的顾虑。用 cookie 保存时，实现上非常简单，就是对 session(包括过期时间) 序列化后做一个简单的 base64 编码。其结果类似 ：

```js
koa:sess=eyJwYXNzcG9ydCI6eyJ1c2VyIjozMDM0MDg1MTQ4OTcwfSwiX2V4cGlyZSI6MTUxNzI3NDE0MTI5MiwiX21heEFnZSI6ODY0MDAwMDB9;
```

**示例**

- ```js
  const session = require("koa-session")
  app.use(session({
      signed:false,
      maxAge: 20 * 1000
  },app))
  ```

### 外部存储

在实际项目中，会话相关信息往往需要再服务端持久化，因此一般都会使用外部存储来记录 session 信息。

外部存储可以是任何的存储系统：

- 可以是内存数据结构，
- 可以是本地的文件
- 也可以是远程的数据库。

**但是这不意味着我们不需要 cookie 了，由于 http 协议的无状态特性，我们依然需要通过 cookie 来获取 session 的标识 (这里叫 externalKey)。**

koa-session 里的 external key 默认是一个时间戳加上一个随机串，因此 cookie 的内容类似：

```js
koa:sess=1517188075739-wnRru1LrIv0UFDODDKo8trbmFubnVmMU;
```

#### 在内存中存储 Sessoin

要实现一个外置的存储，用户需要自定义 get(), set() 和 destroy() 函数，分别用于获取、更新和删除 session。一个最简单的实现，我们就采用一个 object 来存储 session，那么可以这么来配置：

```js
let store = {
  storage: {},
  get (key, maxAge) {
    return this.storage[key]
  },
  set (key, sess, maxAge) {
    this.storage[key] = sess
  },
  destroy (key) {
    delete this.storage[key]
  }
}
app.use(session({store}, app))
```

#### 在数据库中储存 Session

使用 mongoDB 储存 session

> https://juejin.im/post/5a6ad754f265da3e513352e5#heading-5

```js
const session = require("koa-session")
const MongoStore = require("koa-session-mongo2")
app.use(session({
    store: new MongoStore({
        url:  DB_URL  // your mongodb url  required
        db:'user',
        collection: optional, db session collection name,default "__session"
        signed:false // if true, please set app.keys = ['...']
    }),
    signed:false,
    maxAge: 20 * 1000
},app))
```

使用 mysql 储存 session

> https://chenshenhai.github.io/koa2-note/note/session/info.html

```js
const Koa = require('koa')
const session = require('koa-session-minimal')
const MysqlSession = require('koa-mysql-session')

const app = new Koa()

// 配置存储session信息的mysql
let store = new MysqlSession({
  user: 'root',
  password: 'abc123',
  database: 'koa_demo',
  host: '127.0.0.1',
})

// 存放sessionId的cookie配置
let cookie = {
  maxAge: '', // cookie有效时长
  expires: '',  // cookie失效时间
  path: '', // 写cookie所在的路径
  domain: '', // 写cookie所在的域名
  httpOnly: '', // 是否只用于http请求中获取
  overwrite: '',  // 是否允许重写
  secure: '',
  sameSite: '',
  signed: '',

}

// 使用session中间件
app.use(session({
  key: 'SESSION_ID',
  store: store,
  cookie: cookie
}))

app.use( async ( ctx ) => {

  // 设置session
  if ( ctx.url === '/set' ) {
    ctx.session = {
      user_id: Math.random().toString(36).substr(2),
      count: 0
    }
    ctx.body = ctx.session
  } else if ( ctx.url === '/' ) {

    // 读取session信息
    ctx.session.count = ctx.session.count + 1
    ctx.body = ctx.session
  } 

})

app.listen(3000)
console.log('[demo] session is starting at port 3000')
```

### 分布式 Session

首先是为什么会有这样的概念出现？

先考虑这样一个问题，现在我的应用需要部署在 3 台机器上。是不是出现这样一种情况，我第一次登陆，请求去了机器 1，然后再机器 1 上创建了一个 session；但是我第二次访问时，请求被路由到机器 2 了，但是机器 2 上并没有我的 session 信息，所以得重新登录。当然这种可以通过 nginx 的 IP HASH 负载策略来解决。对于同一个 IP 请求都会去同一个机器。

但是业务发展的越来越大，拆分的越来越多，机器数不断增加；很显然那种方案就不行了。那么这个时候就需要考虑是不是应该将 session 信息放在一个独立的机器上，所以分布式 session 要解决的问题其实就是分布式环境下的 session 共享的问题。

上图中的关于 session 独立部署的方式有很多种，可以是一个独立的数据库服务，也可以是一个缓存服务 (redis，目前比较常用的一种方式，即使用 Redis 来作为 session 缓存服务器)。

## Session 与安全

如果 session 采用外部存储的方式，安全性是比较容易保证的，因为 cookie 中保存的只是 session 的 external key，默认实现是一个时间戳加随机字符串，因此不用担心被恶意篡改或者暴露信息。当然如果 cookie 本身被窃取，那么在过期之前还是可以被用来访问 session 信息（当然我们可以在标识中加入更多的信息，比如 ip 地址，设备 id 等信息，从而增加更多校验来减少风险）。

如果 session 完全保存在 cookie 中，就需要额外注意安全性的问题。在 session 的默认实现中，我们注意到对 cookie 的编码只是简单的 base64，因此理论上客户端很容易解析和修改。

因此在 koa-session 的 config 中有一个 httpOnly 的选项，就是不允许浏览器中的 js 代码来获取 cookie，避免遭到一些恶意代码的攻击。

但是假如 cookie 被窃取，攻击者还是可以很容易的修改 cookie，比如把 maxAge 设为无限就可以一直使用 cookie 了，这种情况如何处理呢？其实是 koa 的 cookie 本身带了安全机制，也就是 config 里的 signed 设为 true 的时候，会自动给 cookie 加上一个 sha256 的签名，类似 `koa:sess.sig=pjadZtLAVtiO6-Haw1vnZZWrRm8`，从而防止 cookie 被篡改。

最后，如何处理 session 的信息被泄露的问题呢？其实 koa-session 允许用户在 config 中配置自己的编码和解码函数，因此完全可以使用自定义的加密解密函数对 session 进行编解码，类似

```
  encode: json => CryptoJS.AES.encrypt(json, "Secret Passphrase"),
  decode: encrypted => CryptoJS.AES.decrypt(encrypted, "Secret Passphrase");
```

### signedCookie

上面有提到

> cookie 虽然很方便，但是使用 cookie 有一个很大的弊端，cookie 中的所有数据在客户端就可以被修改，数据非常容易被伪造

其实不是这样的，那只是为了方便理解才那么写。要知道，计算机领域有个名词叫 **签名**，专业点说，叫 **信息摘要算法**。

比如我们现在面临着一个菜鸟开发的网站，他用 cookie 来记录登陆的用户凭证。相应的 cookie 长这样：`dotcom_user=alsotang`，它说明现在的用户是 alsotang 这个用户。如果我在浏览器中装个插件，把它改成 `dotcom_user=ricardo`，服务器一读取，就会误认为我是 ricardo。然后我就可以进行 ricardo 才能进行的操作了。之前 web 开发不成熟的时候，用这招甚至可以黑个网站下来，把 cookie 改成 `dotcom_user=admin` 就行了，唉，那是个玩黑客的黄金年代啊。

OK，现在我有一些数据，不想存在 session 中，想存在 cookie 中，怎么保证不被篡改呢？答案很简单，签个名。

假设我的服务器有个秘密字符串，是 `this_is_my_secret_and_fuck_you_all`，我为用户 cookie 的 `dotcom_user` 字段设置了个值 `alsotang`。cookie 本应是

```
{dotcom_user: 'alsotang'}
```

而如果我们签个名，比如把 `dotcom_user` 的值跟我的 secret_string 做个 sha1

```
sha1('this_is_my_secret_and_fuck_you_all' + 'alsotang') === '4850a42e3bc0d39c978770392cbd8dc2923e3d1d'
```

然后把 cookie 变成这样

```
{
  dotcom_user: 'alsotang',
  'dotcom_user.sig': '4850a42e3bc0d39c978770392cbd8dc2923e3d1d',
}
```

这样一来，用户就没法伪造信息了。一旦它更改了 cookie 中的信息，则服务器会发现 hash 校验的不一致。毕竟他不懂我们的 secret_string 是什么，而暴力破解哈希值的成本太高。

### **对称加密**

上面一直提到 session 可以存在 cookie 中，现在来讲讲具体的思路。这里所涉及的专业名词叫做 对称加密。

假设我们想在用户的 cookie 中存 session data，使用一个名为 `session_data` 的字段。

```
var sessionData = {username: 'alsotang', age: 22, company: 'alibaba', location: 'hangzhou'}
```

可以将 `sessionData` 与我们的 `secret_string` 一起做个**对称加密**，存到 cookie 的 `session_data` 字段中，只要你的 `secret_string` 足够长，那么攻击者也是无法获取实际 session 内容的。对称加密之后的内容对于攻击者来说相当于一段乱码。

而当用户下次访问时，我们就可以用 `secret_string` 来解密 `sessionData`，得到我们需要的 session data。

signedCookies 跟 cookie-session 还是有区别的：

1）是前者信息可见不可篡改，后者不可见也不可篡改

2）是前者一般是长期保存，而后者是 session cookie

cookie-session 的实现跟 signedCookies 差不多。

不过 cookie-session 我个人建议不要使用，有受到回放攻击的危险。

回放攻击指的是，比如一个用户，它现在有 100 积分，积分存在 session 中，session 保存在 cookie 中。他先复制下现在的这段 cookie，然后去发个帖子，扣掉了 20 积分，于是他就只有 80 积分了。而他现在可以将之前复制下的那段 cookie 再粘贴回去浏览器中，于是服务器在一些场景下会认为他又有了 100 积分。

> 只要数据储存在 cookie 中，就有遭到回放攻击的危险

如果避免这种攻击呢？这就需要引入一个第三方的手段来验证 cookie session，而验证所需的信息，一定不能存在 cookie 中。这么一来，避免了这种攻击后，使用 cookie session 的好处就荡然无存了。如果为了避免攻击而引入了缓存使用的话，那不如把 cookie session 也一起放进缓存中。

### Session Cookie

初学者容易犯的一个错误是，忘记了 session_id 在 cookie 中的**存储方式是 session cookie**。即，当用户一关闭浏览器，浏览器 cookie 中的 session_id 字段就会消失。

常见的场景就是在开发用户登陆状态保持时。

假如用户在之前登陆了你的网站，你在他对应的 session 中存了信息，当他关闭浏览器再次访问时，你还是不懂他是谁。所以我们要在 cookie 中，也保存一份关于用户身份的信息。

比如有这样一个用户

```
{username: 'alsotang', age: 22, company: 'alibaba', location: 'hangzhou'}
```

我们可以考虑把这四个字段的信息都存在 session 中，而在 cookie，我们用 signedCookies 来存个 username。

登陆的检验过程伪代码如下：

```js
if (req.session.user) {
  // 获取 user 并进行下一步
  next()
} else if (req.signedCookies['username']) {
  // 如果存在则从数据库中获取这个 username 的信息，并保存到 session 中
  getuser(function (err, user) {
    req.session.user = user;
    next();
  });
} else {
  // 当做为登陆用户处理
  next();
}
```

# Token

## Session Vs Token

 **session 鉴权的流程：**

- 用户登录的时候，服务端生成一个会话和一个 id 标识
- 会话 id 在客户端和服务端之间通过 cookie 进行传输
- 服务端通过会话 id 可以获取到会话相关的信息，然后对客户端的请求进行响应；如果找不到有效的会话，那么认为用户是未登陆状态
- 会话会有过期时间，也可以通过一些操作（比如登出）来主动删除

**token 的典型流程：**

- 用户登录的时候，服务端生成一个 token 返回给客户端
- 客户端后续的请求都带上这个 token
- 服务端解析 token 获取用户信息，并响应用户的请求
- token 会有过期时间，客户端登出的时候也会废弃 token，但是服务端不需要任何操作

**两种方式的区别在于：**

- session 要求服务端存储信息，并且根据 id 能够检索，而 token 不需要。在大规模系统中，对每个请求都检索会话信息可能是一个复杂和耗时的过程。但另外一方面服务端要通过 token 来解析用户身份也需要定义好相应的协议。
- session 一般通过 cookie 来交互，而 token 方式更加灵活，可以是 cookie，也可以是其他 header，也可以放在请求的内容中。不使用 cookie 可以带来跨域上的便利性。
- token 的生成方式更加多样化，可以由第三方服务来提供

+ 很多情况下，session 和 token 两种方式都会一起来使用。

## JWT

- http://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html
- https://juejin.im/post/5cddae69f265da036207d036

## 跨域认证的问题

互联网服务离不开用户认证。一般流程是下面这样：

1. 用户向服务器发送用户名和密码。
2. 服务器验证通过后，在当前对话（session）里面保存相关数据，比如用户角色、登录时间等等。
3. 服务器向用户返回一个 session_id，写入用户的 Cookie。
4. 用户随后的每一次请求，都会通过 Cookie，将 session_id 传回服务器。
5. 服务器收到 session_id，找到前期保存的数据，由此得知用户的身份。

这种模式的问题在于，扩展性（scaling）不好。单机当然没有问题，如果是服务器集群，或者是跨域的服务导向架构，就要求 session 数据共享，每台服务器都能够读取 session。

举例来说，A 网站和 B 网站是同一家公司的关联服务。现在要求，用户只要在其中一个网站登录，再访问另一个网站就会自动登录，请问怎么实现？

一种解决方案是 session 数据持久化，写入数据库或别的持久层。各种服务收到请求后，都向持久层请求数据。这种方案的优点是架构清晰，缺点是工程量比较大。另外，持久层万一挂了，就会单点失败。

另一种方案是服务器索性不保存 session 数据了，所有数据都保存在客户端，每次请求都发回服务器。JWT 就是这种方案的一个代表。

## JWT 的原理

JWT 的原理是，服务器认证以后，生成一个 JSON 对象，发回给用户，就像下面这样。

```json
{
  "姓名": "张三",
  "角色": "管理员",
  "到期时间": "2018年7月1日0点0分"
}
```

以后，用户与服务端通信的时候，都要发回这个 JSON 对象。服务器完全只靠这个对象认定用户身份。为了防止用户篡改数据，服务器在生成这个对象的时候，会加上签名（详见后文）。

服务器就不保存任何 session 数据了，也就是说，服务器变成无状态了，从而比较容易实现扩展。

## JWT 的数据结构

实际的 JWT 大概就像下面这样。

![img](/img/user/programming/basic/cs-basic/network-communication/bg2018072304.jpg)

它是一个很长的字符串，中间用点（`.`）分隔成三个部分。注意，JWT 内部是没有换行的，这里只是为了便于展示，将它写成了几行。

JWT 的三个部分依次如下。

```json
Header（头部）
Payload（负载）
Signature（签名）
```

![img](/img/user/programming/basic/cs-basic/network-communication/bg2018072303.jpg)

### Header

Header 部分是一个 JSON 对象，描述 JWT 的元数据，通常是下面的样子。

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

上面代码中，`alg` 属性表示签名的算法（algorithm），默认是 HMAC SHA256（写成 HS256）；`typ` 属性表示这个令牌（token）的类型（type），JWT 令牌统一写为 `JWT`。

最后，将上面的 JSON 对象使用 Base64URL 算法（详见后文）转成字符串。

### Payload

Payload 部分也是一个 JSON 对象，用来存放实际需要传递的数据。JWT 规定了 7 个官方字段，供选用。

+ iss (issuer)：签发人
+ exp (expiration time)：过期时间
+ sub (subject)：主题
+ aud (audience)：受众
+ nbf (Not Before)：生效时间，如果当前时间在 nbf 里的时间之前，则 Token 不被接受；一般都会留一些余地，比如几分钟；，是否使用是可选的；
+ iat (Issued At)：签发时间
+ jti (JWT ID)：编号

除了官方字段，你还可以在这个部分定义私有字段，下面就是一个例子。

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```

注意，JWT 默认是不加密的，任何人都可以读到，所以不要把秘密信息放在这个部分。

这个 JSON 对象也要使用 Base64URL 算法转成字符串

### Signature

Signature 部分是**对前两部分的签名**，防止数据篡改。

首先，需要指定一个密钥（secret）。这个密钥只有服务器才知道，不能泄露给用户。然后，使用 Header 里面指定的签名算法（默认是 HMAC SHA256），按照下面的公式产生签名。

```json
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

算出签名以后，把 Header、Payload、Signature 三个部分拼成一个字符串，每个部分之间用 " 点 "（`.`）分隔，就可以返回给用户。

### Base64URL

前面提到，Header 和 Payload 串型化的算法是 Base64URL。这个算法跟 Base64 算法基本类似，但有一些小的不同。

JWT 作为一个令牌（token），有些场合可能会放到 URL（比如 api.example.com/?token=xxx）。Base64 有三个字符 `+`、`/` 和 `\=`，在 URL 里面有特殊含义，所以要被替换掉：`\=` 被省略、`+` 替换成 `-`，`/` 替换成 `_` 。这就是 Base64URL 算法。

## JWT 的使用方式

客户端收到服务器返回的 JWT，可以储存在 Cookie 里面，也可以储存在 localStorage。

此后，客户端每次与服务器通信，都要带上这个 JWT。你可以把它放在 Cookie 里面自动发送，但是这样不能跨域，所以更好的做法是放在 HTTP 请求的头信息 `Authorization` 字段里面。

```json
Authorization: Bearer <token>
```

另一种做法是，跨域的时候，JWT 就放在 POST 请求的数据体里面。

## JWT 的几个特点

JWT 默认是不加密，但也是可以加密的。生成原始 Token 以后，可以用密钥再加密一次。

JWT 不加密的情况下，不能将秘密数据写入 JWT。

JWT 不仅可以用于认证，也可以用于交换信息。有效使用 JWT，可以降低服务器查询数据库的次数。

JWT 的最大缺点是，由于服务器不保存 session 状态，因此无法在使用过程中废止某个 token，或者更改 token 的权限。也就是说，一旦 JWT 签发了，在到期之前就会始终有效，除非服务器部署额外的逻辑。

JWT 本身包含了认证信息，一旦泄露，任何人都可以获得该令牌的所有权限。为了减少盗用，JWT 的有效期应该设置得比较短。对于一些比较重要的权限，使用时应该再次对用户进行认证。

为了减少盗用，JWT 不应该使用 HTTP 协议明码传输，要使用 HTTPS 协议传输。

# 浏览器同源策略

## 概述

### 含义

1995 年，同源政策由 Netscape 公司引入浏览器。目前，所有浏览器都实行这个政策。

最初，它的含义是指，A 网页设置的 Cookie，B 网页不能打开，除非这两个网页 " 同源 "。所谓 " 同源 " 指的是 " 三个相同 "。

- 协议相同
- 域名相同，顶级域名相同，但是次级域名不相同，仍然属于跨域

  > 所以 domain 是 cookie 跨域的一种解决办法

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

> **注意：**这种方法只能规避域名造成的同源策略，无法解决协议和端口号带来的跨域
>
> **注意：**这种方法只适用于 Cookie 和 iframe 窗口，LocalStorage 和 IndexDB 无法通过这种方法，规避同源政策，而要使用下文介绍的 PostMessage API。

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

# Ajax 跨域**（源）**通信

## WebSocket #

WebSocket 是一种通信协议，使用 `ws://`（非加密）和 `wss://`（加密）作为协议前缀。该协议不实行同源政策，只要服务器支持，就可以通过它进行跨源通信。

http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html

## JSONP #

跨域的本质：服务器返回了一个 javascript 脚本，这个脚本调用了一个我们事先定义好的方法，而方法中的参数就是我们想要的数据

由于 `<script>` 元素请求的脚本，**直接作为代码运行**。这时，只要浏览器定义了 `foo` 函数，该函数就会立即调用。

ctx 输出的类型应该是 'text/javascript'

ctx 输出的内容为可执行的返回数据 JavaScript 代码字符串

### **前端**

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

### **koa2 后端**

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

![1553413629413](/img/user/programming/basic/cs-basic/network-communication/1553413629413.png)

一旦把 dataType 定义为 jsonp，jQuery.ajax 就会使用动态创建 script 标签的方法，而不会使用 ajax 的传统 1234 个步骤

![1553413645731](/img/user/programming/basic/cs-basic/network-communication/1553413645731.png)

- 只是 jsonpCallback 有点长
- 尾部是时间戳信息
- jsonp 值默认是 callback
- 详见参考手册

![1553413663815](/img/user/programming/basic/cs-basic/network-communication/1553413663815.png)

![1553413692805](/img/user/programming/basic/cs-basic/network-communication/1553413692805.png)

![1553413697283](/img/user/programming/basic/cs-basic/network-communication/1553413697283.png)

## 特定环境

### Webpack 开发环境下跨域

**devServer.proxy**

- 如果你有单独的后端开发服务器 API，并且希望在**同域名**下发送 API 请求 ，那么代理某些 URL 会很有用。
- dev-server 使用了非常强大的 [http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware) 包。更多高级用法，请查阅其 [文档](https://github.com/chimurai/http-proxy-middleware#options)。

**基本使用**

- 在 `localhost:3000` 上有后端服务的话，你可以这样启用代理：
- **webpack.config.js**

  ```javascript
  module.exports = {
    //...
    devServer: {
      proxy: {
        '/api': 'http://localhost:3000'
      }
    }
  };
  ```

- 请求到 `/api/users` 现在会被代理到请求 `http://localhost:3000/api/users`。

**重写路径**

- 如果你不想始终传递 `/api` ，则需要重写路径：
- **webpack.config.js**

  ```javascript
  module.exports = {
    //...
    devServer: {
      proxy: {
        '/api': {
          target: 'http://localhost:3000',
          pathRewrite: {'^/api' : ''}
        }
      }
    }
  };
  ```

- 后端不需要再加上 ‘api’ ，但是前端请求还是要的，用作**转发的**标志

**代理多个路径**

- 如果你想要代理多个路径特定到同一个 target 下，你可以使用由一个或多个「具有 `context` 属性的对象」构成的数组：

  **webpack.config.js**

  ```javascript
  module.exports = {
    //...
    devServer: {
      proxy: [{
        context: ['/auth', '/api'],
        target: 'http://localhost:3000',
      }]
    }
  };
  ```

**配合 HTTPs**

- https://webpack.docschina.org/configuration/dev-server/#devserver-proxy

**代理根路径**

### CRA 开发环境跨域

**统一代理**

- 对于使用 creat-react-app 构建的项目，可以直接在 package.json 下配置，具体如下

  ```html
  "proxy": "http://api.xxxx.com"
  ```

- 所有请求都被会自动代理，不需要标志 片段 #

**创建多个域**

- ```json
  //package.json中加入
    "proxy": {
      "/api/RoomApi": {
        "target": "http://open.douyucdn.cn",
        "changeOrigin":true
      },
      "/api/v1":{
        "target":"http://capi.douyucdn.cn",
        "changeOrigin":true
      }
    }
  ```

**antd-pro 的项目**

- 需要在 package.json 的同等目录下添加.roadhogrc 文件，具体代码:

  ```js
  {
    "entry": "src/index.js",
    "extraBabelPlugins": [
      "transform-runtime",
      "transform-decorators-legacy",
      "transform-class-properties",
      ["import", { "libraryName": "antd", "libraryDirectory": "es", "style": true }]
    ],
    "env": {
      "development": {
        "extraBabelPlugins": [
          "dva-hmr"
        ]
      }
    },
    "externals": {
      "g2": "G2",
      "g-cloud": "Cloud",
      "g2-plugin-slider": "G2.Plugin.slider"
    },
    "ignoreMomentLocale": true,
    "theme": "./src/theme.js",
    "proxy": {
      "/api": {
        "target": "http://api.xxxx.com/",
        "changeOrigin": true
      }
    }
  }
  ```

- https://blog.csdn.net/qq_29854831/article/details/79456106

### Vue 开发环境跨域

config/index.js

允许跨域

```js
proxyTable: {
  '/api': { // 匹配所有以 '/api'开头的请求路径
    target: 'http://localhost:4000', // 代理目标的基础路径
    changeOrigin: true, // 支持跨域
    pathRewrite: {// 重写路径: 去掉路径中开头的'/api'
      '^/api': ''
    }
  }
}
```

# HTTP 访问控制（CORS）

<https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS>

CORS 是跨源资源分享（Cross-Origin Resource Sharing）的缩写。它是 W3C 标准，是跨源 AJAX 请求的根本解决方法。相比 JSONP 只能发 `GET` 请求，CORS 允许任何类型的请求。

跨域资源共享（CORS）是一种机制，它使用额外的 HTTP 头来告诉浏览器 让运行在一个 origin (domain) 上的 Web 应用被准许访问来自不同源服务器上的指定的资源。当一个资源从与该资源本身所在的服务器**不同的域、协议或端口**请求一个资源时，资源会发起一个**跨域 HTTP 请求**。

跨域资源共享（CORS）机制允许 Web 应用服务器进行跨域访问控制，从而使跨域数据传输得以安全进行。现代浏览器支持在 API 容器中（例如 [`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest) 或 [Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) ）使用 CORS，以降低跨域 HTTP 请求所带来的风险。

出于安全原因，浏览器对于从脚本内发起的跨源 HTTP 请求，会拒绝其 HTTP 响应。 XMLHttpRequest 和 Fetch API 遵循同源策略。 这意味着使用这些 API 的 Web 应用程序只能从加载应用程序的同一个域请求 HTTP 资源

> 限制了接受响应，如果是 POST 请求，可以成功修改数据库吗？
>
> 只要是简单请求，就可以修改，因为服务器正常响应了，但是 HTTP 响应被浏览器拦截了
>
> ![1555986035617](F:\OneDrive\JS\assets\1555986035617.png)
>
> 但是如果是预检请求，就会因为 options 请求失败，无法修改数据库，如果 option 被 block 了，服务器会打印 option，如果 option 是正常的，服务器就只打印 patch
>
> ![1555986195403](F:\OneDrive\JS\assets\1555986195403.png)
>
> 浏览器仅发起了 OPTIONS 请求
>
> 还有可能是被服务器的 CORS 配置拦截，这种情况下，服务器无法正常收到 HTTP 请求
>
> Access to XMLHttpRequest at 'http://localhost:8000/user/update/3' from origin 'http://localhost:8080' has been blocked by CORS policy: Method PATCH is not allowed by Access-Control-Allow-Methods in preflight response.
>
> 总结：无论何时，浏览器都可以正常发起请求，如果被 CORS 拦截，则服务器无响应，如果没有设置 CORS，服务器正常响应，但是会被浏览器拦截。
>
> 而预检请求失败，属于正常的响应流程，控制台不会报错

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

![img](https://mdn.mozillademos.org/files/14293/simple_req.png)

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

![1555985693469](/img/user/programming/basic/cs-basic/network-communication/1555985693469.png)

![1555985720377](/img/user/programming/basic/cs-basic/network-communication/1555985720377.png)

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

![img](https://mdn.mozillademos.org/files/14291/cred-req.png)

对于附带身份凭证的请求，服务器不得设置 `Access-Control-Allow-Origin ` 的值为“*”。 #

这是因为请求的首部中携带了 Cookie 信息，如果 ` Access-Control-Allow-Origin` 的值为“*”，请求将会失败。而将 Access-Control-Allow-Origin 的值设置为 http://foo.example，则请求将成功执行。

另外，响应首部中也携带了 Set-Cookie 字段，尝试对 Cookie 进行修改。如果操作失败，将会抛出异常。

## HTTP 响应首部字段

本节列出了规范所定义的响应首部字段。上一小节中，我们已经看到了这些首部字段在实际场景中是如何工作的。

### Access-Control-Allow-Origin

响应首部中可以携带一个 [`Access-Control-Allow-Origin`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)` 字段，其语法如下:`

```js
Access-Control-Allow-Origin: <origin> | *
```

其中，origin 参数的值指定了允许访问该资源的外域 URI。对于不需要携带身份凭证的请求，服务器可以指定该字段的值为通配符，表示允许来自所有域的请求。

例如，下面的字段值将允许来自 http://mozilla.com 的请求：

```html
Access-Control-Allow-Origin: http://mozilla.com
```

如果服务端指定了具体的域名而非“*”，那么响应首部中的 Vary 字段的值必须包含 Origin。这将告诉客户端：服务器对不同的源站返回不同的内容。

### Access-Control-Expose-Headers

### Access-Control-Max-Age

### Access-Control-Allow-Credentials

### Access-Control-Allow-Methods

### Access-Control-Allow-Headers

## HTTP 请求首部字段

- 本节列出了可用于发起跨域请求的首部字段。请注意，**这些首部字段无须手动设置。 当开发者使用 XMLHttpRequest 对象发起跨域请求时，它们已经被设置就绪。**

### Origin

- `Origin` 首部字段表明预检请求或实际请求的源站。

  ```html
  Origin: <origin>
  ```

  origin 参数的值为源站 URI。它不包含任何路径信息，只是服务器名称。

- 注意，不管是否为跨域请求，ORIGIN 字段总是被发送。

### Access-Control-Request-Method

### Access-Control-Request-Headers

# Nginx 反向代理 #

https://segmentfault.com/a/1190000016653873#articleHeader11

https://zhuanlan.zhihu.com/p/63629321

反向代理解决跨域问题：我们前端在使用 vue-cli 这种脚手架工具开发时，经常会遇到跨域的问题，因为项目自身启动本地服务是需要占用一个端口的（而且肯定和服务端不是同源），所以必然会产生跨域的问题。在使用 webpack 做构建工具的项目中，经常会使用 proxyTable 代理实现跨域（具体实现自行百度）。之所以出现跨域是因为浏览器有同源策略的限制，但服务器是没有的同源策略的限制的。当我们本地服务（假设域名：[http://172.16.40.209:8080](https://link.juejin.im?target=http%3A%2F%2F172.16.40.209%3A8080)）要请求目标服务器（假设域名：target.com）的资源的时候，我们不直接请求 target.com，而是请求本地服务自身 [http://172.16.40.209:8080](https://link.juejin.im?target=http%3A%2F%2F172.16.40.209%3A8080)（这时是同源请求，不存在跨域），本地代理服务再将接口转发给 target.com（注意这时候是两个服务器直接的通信了，而不是客户端和服务器的通信，所以更不存在跨域），本地服务获取到目标服务器的响应数据之后通过再代理伪装成本地服务请求的返回值返回给客户端。

本地服务在浏览器向本地服务发起请求 --> 本地代理转发 --> 目标服务器 --> 响应数据后通过代理伪装成本地服务器请求的返回值 --> 浏览器接受到目标服务器的数据

https://segmentfault.com/a/1190000010197683>

# 服务端推送

## 轮询

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

## Websocket

## EventSource

HTML5 中提供的 EventSource 这玩意儿，这是无比简洁的服务器推送信息的接受函数。

```js
new EventSource("test.php").onmessage=function(evt){
    console.log(evt.data);
};
```

简洁程度和 websocket 是一样的啦，只是这里有一个需要注意的地方，test.php 输出的数据流应该是特殊的 MIME 类型，要求是 "text/event-stream"，如果不设置的话，你试试~ （直接抛出异常）

# 其他通信方法

- 图片连接
