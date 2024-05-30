---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-08-27-Sun, 7:50:17 pm","date-modified":"2023-08-27-Sun, 8:05:56 pm","permalink":"/programming/basic/cs-basic/network/tcp/","dgPassFrontmatter":true}
---


# TCP 的运输连接管理

运输连接有三个阶段，即：**连接建立**、**数据传送**和**连接释放**。运输连接的管理就是使运输连接的建立和释放都能正常地进行。

连接建立过程中要解决以下三个问题：

- 要使每一方能够确知对方的存在。
- 要允许双方协商一些参数（如最大报文段长度，最大窗口大小，服务质量等）。
- 能够对运输实体资源（如缓存大小，连接表中的项目等）进行分配。

## 三次握手建立 TCP 连接

下面是 TCP 建立连接的三次握手的过程:

1. 首先客户端向服务器发送一个 SYN 连接请求报文段和一个随机序号
2. 服务端接 收到请求后向服务器端发送一个 SYN ACK 报文段，确认连接请求，并且也向客户端发送一个随机序号
3. 客户端接收服务器的 确认应答后，进入连接建立的状态，同时向服务器也发送一个 ACK 确认报文段，服务器端接收到确认后，也进入连接建立 状态，此时双方的连接就建立起来了

![](/img/user/programming/basic/cs-basic/network/tcp/image-20230827195757382.png)

![](/img/user/programming/basic/cs-basic/network/tcp/image-20230827195803311.png)

![1550539643131](/img/user/programming/basic/cs-basic/network/!network/1550539643131.png)

![](/img/user/programming/basic/cs-basic/network/tcp/image-20230827195845483.png)

![](/img/user/programming/basic/cs-basic/network/tcp/image-20230827195851170.png)

### 为什需要三次握手

《计算机网络》第四版中讲“三次握手”的目的是“为了防止已失效的连接请求报文段突然又传送到了服务端，因而产生错误”

书中的例子是这样的，“已失效的连接请求报文段”的产生在这样一种情况下：client 发出的第一个连接请求报文段并没有丢失，而是在某个网络结点长时间的滞留了，以致延误到连接释放以后的某个时间才到达 server。本来这是一个早已失效的报文段。但 server 收到此失效的连接请求报文段后，就误认为是 client 再次发出的一个新的连接请求。于是就向 client 发出确认报文段，同意建立连接

假设不采用“三次握手”，那么只要 server 发出确认，新的连接就建立了。由于现在 client 并没有发出建立连接的请求，因此不会理睬 server 的确认，也不会向 server 发送数据。但 server 却以为新的运输连接已经建立，并一直等待 client 发来数据。这样，server 的很多资源就白白浪费掉了。采用“三次握手”的办法可以防止上述现象发生。例如刚才那种情况，client 不会向 server 的确认发出确认。server 由于收不到确认，就知道 client 并没有要求建立连接。”。主要目的防止 server 端一直等待，浪费资源

## 四次挥手释放 TCP 连接

![](/img/user/programming/basic/cs-basic/network/tcp/image-20230827195857064.png)

![](/img/user/programming/basic/cs-basic/network/tcp/image-20230827195900891.png)

![](/img/user/programming/basic/cs-basic/network/tcp/image-20230827195904246.png)

![](/img/user/programming/basic/cs-basic/network/tcp/image-20230827195908421.png)

![](/img/user/programming/basic/cs-basic/network/tcp/image-20230827195912687.png)

用 TCP 传送 312 字节的数据，设窗口为 100 字节，而 TCP 报文段每次也传送 100 字节的数据，再设发送端和接收端的起始序号分别为 100 和 200，试画出 TCP 连接建立、数据传送以及连接释放工作示意图。

![](/img/user/programming/basic/cs-basic/network/tcp/image-20230827195917420.png)

### 为什么建立连接是三次握手，而关闭连接却是四次挥手呢？

这是因为服务端在 LISTEN 状态下，收到建立连接请求的 SYN 报文后，把 ACK 和 SYN 放在一个报文里发送给客户端。而关闭连接时，当收到对方的 FIN 报文时，仅仅表示对方不再发送数据了但是还能接收数据，己方也未必全部数据都发送给对方了，所以己方可以立即 close，也可以发送一些数据给对方后，再发送 FIN 报文给对方来表示同意现在关闭连接，因此，己方 ACK 和 FIN 一般都会分开发送。
