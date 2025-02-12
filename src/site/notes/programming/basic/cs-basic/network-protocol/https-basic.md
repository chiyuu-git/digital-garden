---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-07-21-Fri, 11:10:37 am","date-modified":"2023-08-28-Mon, 12:18:40 pm","permalink":"/programming/basic/cs-basic/network-protocol/https-basic/","dgPassFrontmatter":true}
---


<https://zhuanlan.zhihu.com/p/43789231>

<http://www.ruanyifeng.com/blog/2014/09/illustration-ssl.html>

<https://juejin.im/post/5a2ff29c6fb9a045132aac5a>

# 概述

HTTP 有以下安全性问题：

1. 使用明文进行通信，内容可能会被窃听；
2. 不验证通信方的身份，通信方的身份有可能遭遇伪装；
3. 无法证明报文的完整性，报文有可能遭篡改。

HTTP 协议中的内容都是明文传输，HTTPS 的目的是将这些内容加密，确保信息传输安全。最后一个字母 S 指的是 SSL/TLS 协议，它位于 HTTP 协议与 TCP/IP 协议中间。

## 信息安全的三个方面

1. 客户端和服务器直接的通信只有自己能看懂，即使第三方拿到数据也看不懂这些信息的真实含义。
2. 第三方虽然看不懂数据，但可以 XJB 改，因此客户端和服务器必须有能力判断数据是否被修改过。
3. 客户端必须避免中间人攻击，即除了真正的服务器，任何第三方都无法冒充服务器。

## HTTP 用于安全传输

HTTP 最大的变化发生在 1994 年底。HTTP 在基本的 TCP/IP 协议栈上发送信息，网景公司（Netscape Communication）在此基础上创建了一个额外的 **加密传输层**：SSL 。SSL 1.0 没有在公司以外发布过，但 SSL 2.0 及其后继者 SSL 3.0 和 SSL 3.1 允许通过加密来保证服务器和客户端之间交换消息的真实性，来创建电子商务网站。SSL 在标准化道路上最终成为 TLS,随着版本 1.0, 1.1, 1.2 的出现成功地关闭漏洞。TLS 1.3 目前正在形成。

与此同时，人们对一个加密传输层的需求也愈发高涨：因为 Web 最早几乎是一个学术网络，相对信任度很高，但如今不得不对面一个险恶的丛林：广告客户、随机的个人或者犯罪分子争相劫取个人信息，将信息占为己有，甚至改动将要被传输的数据。随着通过 HTTP 构建的应用程序变得越来越强大，可以访问越来越多的私人信息，如地址簿，电子邮件或用户的地理位置，即使在电子商务使用之外，对 TLS 的需求也变得普遍。

**HTTPS 并不是新协议**，而是让 HTTP 先和 SSL（Secure Sockets Layer）通信，再由 SSL 和 TCP 通信。也就是说 HTTPS 使用了隧道进行通信。

通过使用 SSL，HTTPs 具有了加密（防窃听）、认证（防伪装）和完整性保护（防篡改）

HTTP 和 HTTPS 使用的是完全不同的连接方式，用的端口也不一样，前者是 80，后者是 443。

![img](https-basic/b6daabee3a064fdc750cf0ff41c69871.jpg)

# 混合加密机制

## 对称密钥加密

对称密钥加密（Symmetric-Key Encryption），加密和解密使用同一密钥。

> 比如 `Hello` 可以变换成 `Ifmmp`，规则就是每个字母变成它在字母表上的后一个字母，这里的秘钥就是 `1`，另一方拿到 `Ifmmp` 就可以还原成原来的信息 `Hello` 了。

优点：计算量小，运算速度快，适合加密大数据；

缺点：密钥容易被获取；一个用户对应一个密钥，管理密钥比较麻烦

**传输流程**

1. 双方约定密钥
2. 传输密钥加密后的数据
3. 接收方使用密钥解密

**常用的对称加密算法：**

- DES
- 3DES
- AES

## 非对称密钥加密

公开密钥加密（Public-Key Encryption），也称为非对称密钥加密，使用一对密钥用于加密和解密，分别为公开密钥和私有密钥。

公开密钥所有人都可以获得，通信发送方获得接收方的公开密钥之后，就可以使用公开密钥进行加密，接收方收到通信内容后使用私有密钥解密。

> 它的思想很简单，计算两个质数的乘积很容易，但反过来分解成两个质数的乘积就很难，要经过极为复杂的运算。

**过程：**

- 算法生成公钥和私钥
- 服务器把公钥传递给用户
- 用户使用公钥加密数据，传输给服务器
- 服务器使用私钥解密

**常用的非对称加密算法：**

- RSA

优点：更为安全； 缺点：运算速度慢；

## 对称秘钥如何传输

不能明文传递对称秘钥，而且也不能用一个新的对称加密算法来加密原来的对称秘钥，否则新的对称秘钥同样无法传输，这就是鸡生蛋、蛋生鸡的悖论。

这里我们引入 **非对称加密的方式**，非对称加密的特性决定了服务器用私钥加密的内容并不是 **真正的加密**，因为公钥所有人都有，所以服务器的密文能被所有人解析。但私钥只掌握在服务器手上，这就带来了两个巨大的优势:

1. 服务器下发的内容不可能被伪造，因为别人都没有私钥，所以无法加密。强行加密的后果是客户端用公钥无法解开。
2. 任何人用公钥加密的内容都是绝对安全的，因为私钥只有服务器有，也就是只有真正的服务器可以看到被加密的原文。

所以传输对称秘钥的问题就迎刃而解了: **对称秘钥不是由服务器下发，而是由客户端生成并且主动告诉服务器**。@@@

所以当引入非对称加密后，HTTPS 的握手流程依然是两步，不过细节略有变化:

1. 客户端: 你好，我需要发起一个 HTTPS 请求，这是我的 **(用公钥加密后的)** 对称秘钥。
2. 服务器: 好的，我知道你的秘钥了，后续就用它传输。

## 公钥怎么传输

每一个使用 HTTPS 的服务器都必须去专门的 **证书机构** 注册一个证书，证书中存储了用 **权威机构私钥加密的公钥**。这样客户端用权威机构的公钥解密就可以了。

现在 HTTPS 协议的握手阶段变成了四步:

1. 客户端: 你好，我要发起一个 HTTPS 请求，请给我公钥
2. 服务器: 好的，这是我的证书，里面有加密后的公钥
3. 客户端: 解密成功以后告诉服务器: 这是我的 (用公钥加密后的) 对称秘钥。
4. 服务器: 好的，我知道你的秘钥了，后续就用它传输。

## 权威机构的公钥又怎么传输

这个公钥不用传输，会直接 **内置** 在各大操作系统 (或者浏览器) 的出厂设置里。之所以不把每个服务器的公钥内置在电脑里，一方面是因为服务器太多，存不过来。另一方面操作系统也不信任你，凭什么你说你这个就是百度/淘宝的证书呢？

所以各个公司要先去权威机构认证，申请证书，然后操作系统只会存储权威机构的公钥。因为权威机构数量有限，所以操作系统厂商相对来说容易管理。如果这个权威机构不够权威，XJB 发证书，就会取消他的资格，比如可怜的沃通

## 怎么知道证书有没有被篡改

服务器第一次会返回证书，也就是加密以后的公钥，那我怎么知道这个证书是可靠的？

**将信息 hash 值随着信息一起传递**

为了确保原始证书没有被篡改，我们可以在传递证书的同时传递证书的哈希值。由于第三者无法解析数据，只能 XJB 改，那么修改后的数据在解密后，就不可能通过哈希。

## 防止第三方冒充服务器

也许可以

1. 首先真正的服务器下发的内容，无法被别人篡改。
2. 他们有权威机构的公钥，所以可以解密
3. 但是因为没有私钥，所以解密以后的信息无法加密。
4. 没有加密或者错误加密的信息被客户端用公钥解密以后，必然无法通过哈希校验。

但是，如果你一开始请求的就不是真的服务器，而是一个攻击者，此时的他完全有机会进行中间人攻击。我们知道第一次握手的时候服务器会下发用于证明自己身份的证书，这个证书会用预设在设备上的公钥来解密。所以要么是经过认证的证书用权威机构的私钥加密，再用权威机构解密，要么是用非权威机构的私钥加密，然后找不到公钥解密。

所以如果不小心安装过非权威机构的根证书，比如黑客提供的恶意证书，这时候设备上就多了一个预设的公钥，那么用恶意私钥加密的证书就能被正常解析出来。所以千万不要随便装根证书，这等于是为那些恶意证书留了一扇门。

<https://juejin.im/post/5a2ff29c6fb9a045132aac5a>

## 完整流程

HTTPS 采用混合的加密机制，使用公开密钥加密用于传输，对称密钥来保证安全性，之后使用对称密钥加密进行通信来保证效率。

![img](https-basic/bg2014092003-1588646813692.png)

### TLS 握手过程

TLS 握手过程;;[link](https://digital-garden.chiyuu.top/programming/basic/cs-basic/network-protocol/https-basic/#tls) <!--SR:!2023-09-10,8,250-->

并不是在 tcp 的三次握手的基础上增加一次握手, 而是完全不同的四次握手

1. 第一步，客户端向服务器发起请求，请求中包含使用的协议版本号、生成的一个随机数、以及客户端支持的加密方法。
2. 第二步，服务器端接收到请求后，确认双方使用的加密方法、并给出服务器的证书、以及一个服务器生成的随机数。
3. 第三步，客户端确认服务器证书有效后，生成一个新的随机数，并使用数字证书中的公钥，加密这个随机数，然后发给服 务器。并且还会提供一个前面所有内容的 hash 的值，用来供服务器检验。
4. 第四步，服务器使用自己的私钥，来解密客户端发送过来的随机数。并提供前面所有内容的 hash 值来供客户端检验。
5. 第五步，客户端和服务器端根据约定的加密方法使用前面的三个随机数，生成对话秘钥，以后的对话过程都使用这个秘钥 来加密信息。

#### TLS/SSL 中什么一定要用三个随机数，来生成 " 会话密钥 "

客户端和服务器都需要生成随机数，以此来保证每次生成的秘钥都不相同。使用三个随机数，是因为 SSL 的协议默认不信任每个主机都能产生完全随机的数，如果只使用一个伪随机的数来生成秘钥，就很容易被破解。通过使用三个随机数的方式，增加了自由度，一个伪随机可能被破解，但是三个伪随机就很接近于随机了，因此可以使用这种方法来保持生成秘钥的随机性和安全性。

## SSL 连接断开后如何恢复

一共有两种方法来恢复断开的 SSL 连接，一种是使用 session ID，一种是 session ticket。

使用 session ID 的方式，每一次的会话都有一个编号，当对话中断后，下一次重新连接时，只要客户端给出这个编号，服务器 如果有这个编号的记录，那么双方就可以继续使用以前的秘钥，而不用重新生成一把。目前所有的浏览器都支持这一种方法。但是这种方法有一个缺点是，session ID 只能够存在一台服务器上，如果我们的请求通过负载平衡被转移到了其他的服务器上，那 么就无法恢复对话。

另一种方式是 session ticket 的方式，session ticket 是服务器在上一次对话中发送给客户的，这个 ticket 是加密的 ，只有服务器能够解密，里面包含了本次会话的信息，比如对话秘钥和加密方法等。这样不管我们的请求是否转移到其他的服务器 上，当服务器将 ticket 解密以后，就能够获取上次对话的信息，就不用重新生成对话秘钥了。

# HTTPS 性能问题

HTTPS 降低用户访问速度，时间主要花费在两组 SSL 之间的耗时和证书的读取验证上，对称算法的加解密时间几乎可以忽略不计。

但是只要经过合理优化和部署，HTTPS 对速度的影响完全可以接受。在很多场景下，HTTPS 速度完全不逊于 HTTP，如果使用 SPDY，HTTPS 的速度甚至还要比 HTTP 快。

而且如果不是首次握手，后续的请求并不需要完整的握手过程。客户端可以把上次的加密情况直接发送给服务器从而快速恢复，具体细节可以参考 [图解 SSL/TLS 协议](https://link.juejin.im/?target=http%3A%2F%2Fwww.ruanyifeng.com%2Fblog%2F2014%2F09%2Fillustration-ssl.html)。

相对于 HTTPS 降低访问速度，其实更需要关心的是服务器端的 CPU 压力，HTTPS 中大量的密钥算法计算，会消耗大量的 CPU 资源，只有足够的优化，HTTPS 的机器成本才不会明显增加。

对缓存的影响？

![1568513010765](https-basic/1568513010765.png)

## 使用 SPDY

2012 年 google 如一声惊雷提出了 SPDY 的方案，大家才开始从正面看待和解决老版本 HTTP 协议本身的问题，SPDY 可以说是综合了 HTTPS 和 HTTP 两者有点于一体的传输协议，主要解决：

1. 降低延迟，针对 HTTP 高延迟的问题，SPDY 优雅的采取了 **多路复用（multiplexing）**。多路复用通过多个请求 stream 共享一个 tcp 连接的方式，解决了 HOL blocking 的问题，降低了延迟同时提高了带宽的利用率。
2. 请求优先级（request prioritization）。多路复用带来一个新的问题是，在连接共享的基础之上有可能会导致关键请求被阻塞。SPDY 允许给每个 request 设置优先级，这样重要的请求就会优先得到响应。比如浏览器加载首页，首页的 html 内容应该优先展示，之后才是各种静态资源文件，脚本文件等加载，这样可以保证用户能第一时间看到网页内容。
3. header 压缩。前面提到 HTTP1.x 的 header 很多时候都是重复多余的。选择合适的压缩算法可以减小包的大小和数量。
4. 基于 HTTPS 的加密协议传输，大大提高了传输数据的可靠性。
5. 服务端推送（server push），采用了 SPDY 的网页，例如我的网页有一个 sytle.css 的请求，在客户端收到 sytle.css 数据的同时，服务端会将 sytle.js 的文件推送给客户端，当客户端再次尝试获取 sytle.js 时就可以直接从缓存中获取到，不用再发请求了。SPDY 构成图：

SPDY 位于 HTTP 之下，TCP 和 SSL 之上，这样可以轻松兼容老版本的 HTTP 协议 (将 HTTP1.x 的内容封装成一种新的 frame 格式)，同时可以使用已有的 SSL 功能。
