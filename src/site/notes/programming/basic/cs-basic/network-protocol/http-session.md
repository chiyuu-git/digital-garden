---
{"dg-publish":true,"permalink":"/programming/basic/cs-basic/network-protocol/http-session/"}
---


# Cookie

## 概述

HTTP Cookie（也叫 Web Cookie 或浏览器 Cookie）是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。

通常，它用于告知服务端两个请求是否来自同一浏览器，如保持用户的登录状态。Cookie 使基于 [无状态](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview#HTTP_is_stateless_but_not_sessionless) 的 HTTP 协议记录稳定的状态信息成为了可能。

Cookie 主要用于以下三个方面：

- 会话状态管理（如用户登录状态、购物车、游戏分数或其它需要记录的信息）
- 个性化设置（如用户自定义设置、主题等）
- 浏览器行为跟踪（如跟踪分析用户行为等）

Cookie 曾一度用于客户端数据的存储，因当时并没有其它合适的存储办法而作为唯一的存储手段，但现在随着现代浏览器开始支持各种各样的存储方式，Cookie 的储存功能渐渐被代替。由于服务器指定 Cookie 后，浏览器的每次请求都会携带 Cookie 数据，会带来额外的性能开销（尤其是在移动环境下）。新的浏览器 API 已经允许开发者直接将数据存储到本地，如使用 [Web storage API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Storage_API) （本地存储和会话存储）或 [IndexedDB](https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API) 。

### 工作原理 @@@

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

## 服务器处理 Cookie

就是 set-cookie 字段

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

- domain： cookie 域名
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

> **提示**：当 Cookie 的过期时间被设定时，设定的日期和时间只与客户端相关，而不是服务端。

### Secure

标记为 `Secure` 的 Cookie 只应通过被**HTTPS 协议**加密过的请求发送给服务端。但即便设置了 `Secure` 标记，敏感信息也不应该通过 Cookie 传输，因为 Cookie 有其固有的不安全性，`Secure ` 标记也无法提供确实的安全保障。

从 Chrome 52 和 Firefox 52 开始，不安全的站点（`http:`）无法使用 Cookie 的 `Secure` 标记。

### HttpOnly

为避免跨域脚本 ([XSS](https://developer.mozilla.org/en-US/docs/Glossary/XSS)) 攻击，通过 JavaScript 的 [`Document.cookie`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/cookie) API**无法访问**带有 `HttpOnly` 标记的 Cookie，它们只应该发送给服务端。如果包含服务端 Session 信息的 Cookie 不想被客户端 JavaScript 脚本调用，那么就应该为其设置 `HttpOnly` 标记。

### Cookie 的作用域

`Domain` 和 `Path` 标识定义了 Cookie 的 * 作用域：浏览器会根据 `Domain` 和 `Path` 来判断是否携带 Cookie 给当前的服务器

`Domain` 标识指定了哪些域名可以接受 Cookie。如果不指定，默认为**当前服务器的域名**（**不包含子域名**）。如果指定了 `Domain`，则一般包含子域名。

- 例如，如果设置 `Domain=mozilla.org`，则 Cookie 也包含在子域名中（如 `developer.mozilla.org`）。

`Path` 标识指定了服务器下的哪些路径可以接受 Cookie（该 URL 路径必须存在于请求 URL 中）。以字符 `%x2F` ("/") 作为路径分隔符，子路径也会被匹配。

例如，设置 `Path=/docs`，则以下地址都会匹配：

- `/docs`
- `/docs/Web/`
- `/docs/Web/HTTP`

### SameSite Cookies

`SameSite` Cookie 允许服务器要求某个 cookie 在跨站请求时不会被发送，从而可以阻止跨站请求伪造攻击（[CSRF](https://developer.mozilla.org/en-US/docs/Glossary/CSRF)）。但目前 `SameSite` Cookie 还处于实验阶段，并不是所有浏览器都支持。

## 浏览器处理 Cookie

### Document.cookies

#### 读取 Cookie

Document.cookie，获取并设置与当前文档相关联的 [cookie](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)。

```js
  allCookies = document.cookie;
```

在上面的代码中，allCookies 被赋值为一个字符串，该字符串包含所有的 Cookie，每条 cookie 以分号分隔 (即, `key=value ` 键值对)。

#### 写入 Cookie

```js
  document.cookie = newCookie;
```

newCookie 是一个**键值对形式的字符串**。需要注意的是，用这个方法一次只能对一个 cookie 进行设置或更新。

以下可选的 cookie 属性值可以跟在键值对后，用来具体化对 cookie 的设定/更新，使用分号以作分隔：

- `;path=path` (例如 '/', '/mydir') 如果没有定义，默认为当前文档位置的路径。
- `;domain=domain` (例如 'example.com'， 'subdomain.example.com') 如果没有定义，默认为当前文档位置的路径的域名部分。与早期规范相反的是，在域名前面加 . 符将会被忽视，因为浏览器也许会拒绝设置这样的 cookie。如果指定了一个域，那么子域也包含在内。
- `;max-age=max-age-in-seconds` (例如一年为 60\*60\*24\*365)
- `;expires=date-in-GMTString-format` 如果没有定义，cookie 会在对话结束时过期, 这个值的格式参见 [Date.toUTCString()](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Date/toUTCString)
- `;secure` (cookie 只通过 https 协议传输)

cookie 的值字符串可以用 [encodeURIComponent()](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/encodeURIComponent) 来保证它不包含任何逗号、分号或空格 (cookie 值中禁止使用这些值).

  ```js
  document.cookie = "name=oeschger";
  document.cookie = "favorite_food=tripe";
  alert(document.cookie);
  // 显示: name=oeschger;favorite_food=tripe
  ```

客户端并没有提供读写 cookie 单个属性值的操作，需要引入一些简单的库

https://developer.mozilla.org/en-US/docs/Web/API/Document/cookie/Simple_document.cookie_framework

## 注意

实际上，如果设置了两个相同名称的 Cookie，浏览器会携带这两个 Cookie。它们不会被覆盖。每个 Cookie 在请求头中都会被单独发送，浏览器不会合并相同名称的 Cookie。这意味着服务器在处理请求时可能会看到多个相同名称的 Cookie，每个都对应不同的值。

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

如果客户在浏览器禁用了 Cookie，该怎么办呢?

方案一：拼接 SessionId 参数。在 GET 或 POST 请求中拼接 SessionID，GET 请求通常通过 URL 后面拼接参数来实现，POST 请求可以放在 Body 中。无论哪种形式都需要与服务器获取保持一致。

这种方案比较常见，比如老外的网站，经常会提示是否开启 Cookie。如果未点同意或授权，会发现浏览器的 URL 路径中往往有 "?sessionId=123abc" 这样的参数。

方案二：基于 Token(令牌)。在 APP 应用中经常会用到 Token 来与服务器进行交互。Token 本质上就是一个唯一的字符串，登录成功后由服务器返回，标识客户的临时授权，客户端对其进行存储，在后续请求时，通常会将其放在 HTTP 的 Header 中传递给服务器，用于服务器验证请求用户的身份。

实现 session 的一种方式就是在每个请求的参数或者数据中带上相关信息，这种方式的好处是不受 cookie 可用性的限制。

> 我们在登录某些网站的时候会发现 url 里有长长的一串不规则字符，往往就是编码了用户的 session 信息。但是这种方式也会受到请求长度的限制，使用起来也不方便，而且还有安全性上的 [隐患](https://security.stackexchange.com/questions/14093/why-is-passing-the-session-id-as-url-parameter-insecure)。

[Token](http-session.md#Token)

### 分布式系统 Session

在分布式系统中，往往会有多台服务器来处理同一业务。如果用户在 A 服务器登录，Session 位于 A 服务器，那么当下次请求被分配到 B 服务器，将会出现登录失效的问题。

针对类似的场景，有三种解决方案：

方案一：请求精确定位。也就是通过负载均衡器让来自同一 IP 的用户请求始终分配到同一服务上。比如，Nginx 的 ip_hash 策略，就可以做到。

方案二：Session 复制共享。该方案的目标就是确保所有的服务器的 Session 是一致的。像 Tomcat 等多数主流 web 服务器都采用了 Session 复制实现 Session 的共享.

方案三：基于共享缓存。该方案是通过将 Session 放在一个公共地方，各个服务器使用时去取即可。比如，存放在 Redis、Memcached 等缓存中间件中。

在 Spring Boot 项目中，如果集成了 Redis，Session 共享可以非常方便的实现。

### 登录认证

### Koa-session

[Koa-session](../../../back-end/koa.md#Koa-session)

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

todo: [请立刻停止使用 JWT 进行会话管理 - 知乎](https://zhuanlan.zhihu.com/p/372104412)

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

![img](http-session/bg2018072304.jpg)

它是一个很长的字符串，中间用点（`.`）分隔成三个部分。注意，JWT 内部是没有换行的，这里只是为了便于展示，将它写成了几行。

JWT 的三个部分依次如下。

```json
Header（头部）
Payload（负载）
Signature（签名）
```

![img](http-session/bg2018072303.jpg)

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

# Storage

storage. 同样可以实现状态, 只不过状态会被清除, 如果是纯前端实现或者要求不高都可以考虑使用 storage 实现状态. 最典型的就是网页引导, 用户第一次进入可以引导, 之后存储到 storage 里面. 如果清除了缓存就会再次出现
