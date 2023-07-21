---
{"dg-publish":true,"permalink":"/programming/basic/cs-basic/network-protocol/websocket/"}
---


# 实现双工的办法

- 使用 JavaScript 来和服务端建立 WebSocket 连接
- 使用 JavaScript 创建定时器，定时发送请求轮询
- 使用 JavaScript 和服务端配合来实现长轮询

# Websocket

**参考** <http://www.ruanyifeng.com/blog/2017/05/websocket.html>

## 概述

官网：<https://github.com/websockets/ws>

WebSocket 协议在 2008 年诞生，2011 年成为国际标准。所有浏览器都已经支持了。

它的最大特点就是，服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话，属于 [服务器推送技术](https://en.wikipedia.org/wiki/Push_technology) 的一种。

![img](/img/user/programming/basic/cs-basic/network-protocol/websocket/bg2017051502-1588680632298.png)

![在这里插入图片描述](/img/user/programming/basic/cs-basic/network-protocol/websocket/3657603663-5c6ba60ac980c_articlex.png)

websocket 协议允许不受信用的客户端代码在可控的网络环境中控制远程主机。该协议包含一个握手和一个基本消息分帧、分层通过 TCP。简单点说，通过握手应答之后，建立安全的信息管道，这种方式明显优于 [前文](https://link.juejin.im/?target=http%3A%2F%2Fwww.cnblogs.com%2Fhustskyking%2Fp%2Fweb-communication.html) 所说的基于 XMLHttpRequest 的 iframe 数据流和长轮询。该协议包括两个方面，握手链接（handshake）和数据传输（data transfer）。

### 特点

建立在 TCP 协议之上，服务器端的实现比较容易。

与 HTTP 协议有着良好的兼容性。默认端口也是 80 和 443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。

数据格式比较轻量，性能开销小，通信高效。

可以发送文本，也可以发送二进制数据。

没有同源限制，客户端可以与任意服务器通信。

协议标识符是 `ws`（如果加密，则为 `wss`），服务器网址就是 URL。

```
ws://example.com:80/some/path
```

![img](/img/user/programming/basic/cs-basic/network-protocol/websocket/bg2017051503.jpg)

## 报文

### 握手连接

握手连接中，client 先主动伸手：

```
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Origin: http://example.com
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
```

![1570780988923](/img/user/programming/basic/cs-basic/network-protocol/websocket/1570780988923.png)

客户端发了一串 Base64 加密的密钥过去，也就是上面你看到的 Sec-WebSocket-Key。 Server 看到 Client 打招呼之后，悄悄地告诉 Client 他已经知道了，顺便也打个招呼。

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
Sec-WebSocket-Protocol: chat
```

![1570781014161](/img/user/programming/basic/cs-basic/network-protocol/websocket/1570781014161.png)

Server 返回了 Sec-WebSocket-Accept 这个应答，这个应答内容是通过一定的方式生成的。生成算法是：

```
mask  = "258EAFA5-E914-47DA-95CA-C5AB0DC85B11";  // 这是算法中要用到的固定字符串
accept = base64( sha1( key + mask ) );
```

key 和 mask 串接之后经过 SHA-1 处理，处理后的数据再经过一次 Base64 加密。分解动作：

```
1. t = "GhlIHNhbXBsZSBub25jZQ==" + "258EAFA5-E914-47DA-95CA-C5AB0DC85B11"
   -> "GhlIHNhbXBsZSBub25jZQ==258EAFA5-E914-47DA-95CA-C5AB0DC85B11"
2. s = sha1(t) 
   -> 0xb3 0x7a 0x4f 0x2c 0xc0 0x62 0x4f 0x16 0x90 0xf6 
      0x46 0x06 0xcf 0x38 0x59 0x45 0xb2 0xbe 0xc4 0xea
3. base64(s) 
   -> "s3pPLMBiTxaQ9kYGzzhZRbK+xOo="
```

上面 Server 端返回的 HTTP 状态码是 101，如果不是 101 ，那就说明握手一开始就失败了

#### 实例

但是 WebSocket 协议 并不是 HTTP 协议，刚开始验证的时候借用了 HTTP 的头，连接成功之后的通信就不是 HTTP 了，不信你用 fiddler2 抓包试试，肯定是拿不到的，后面的通信部分是基于 TCP 的连接。

### 响应报文

<https://juejin.im/entry/5a012eab518825297a0e27f0>

## 框架对比

ws 正如它宣称的：速度最快的 websocket 库之一。速度要比 socket.io 快一倍。socket.io 连接数不足时客户端不会报错，健壮性比 ws 好。ws 客户端在封装时需要考虑连接数不足时的错误处理。

## 基础示例

### 服务器端

安装 ws

```js
npm i --save ws
```

使用 ws

```js
const Koa = require('koa')

const WebSocket = require('ws')
const ws = new WebSocket.Server({port: 8888})

ws.on('connection', ws => {
    console.log('server connection');

    ws.on('message', msg => {
      console.log('server receive msg：', msg);
    });

    ws.send('Information from the server')
})

app.listen(3000)
```

# 聊天服务器

## 区分用户

<https://waylau.com/node.js-websocket-chat/>

用户第一次连接的时候贮存进 map 中，通过 ip 和 port 进行区分，也可以通过 session_id 进行区分

## 服务器端

聊天服务器的业务需求比较简单，是一个群聊聊天室。换言之，所有人发送的消息大家都可以见到。

当有新用户连接到服务器时，会以该用户的“IP+ 端口”作为用户的名称。

代码如下

```js
const WebSocket = require('ws')
const server = new WebSocket.Server({port: 8888})

server.on('open', function open() {
  console.log('ws open');
});

server.on('close', function close() {
  console.log('ws closed');
});


server.on('connection', (ws,req) => {
    const ip = req.connection.remoteAddress
    const port = req.connection.remotePort
    const clientName = ip + port
    console.log('%s is connected', clientName)
    // 发送消息给客户端，告知链接成功
    ws.send("Welcome " + clientName)
  
    // 客户端发送消息，触发了服务端的对应client的message事件
    ws.on('message', msg => {
      console.log('received: %s from %s', msg, clientName)
      // 广播消息给所有客户端
      server.clients.forEach(function each(client) {
        if (client.readyState === WebSocket.OPEN) {
          client.send( clientName + " -> " + msg)
        }
      })
    })
})
```

## 浏览器端

DOM 结构

```html
<form onsubmit="return false;">
  <h3>WebSocket 聊天室：</h3>
  <textarea id="responseText" style="width: 500px; height: 300px;"></textarea>
  <br>
  <input type="text" name="message" style="width: 300px" value="Welcome">
  <input type="button" value="发送消息" onclick="ws.send(this.form.message.value)">
  <input type="button" onclick="javascript:responseText.value=''" 
         value="清空聊天记录">
</form>
```

js 代码

```js
const ws = new WebSocket('ws://localhost:8888')

ws.onopen = () => {
  console.log('WebSocket onopen')
}

ws.onmessage = e => {
  console.log('WebSocket onmessage');
  console.log('WebSocket message received：', e)
  console.log('WebSocket data received：', e.data)
}

ws.onclose = e => {
  console.log("WebSocket onclose");
}
```

修改 onmessage 让接受到的信息显示在 textarea

```js
ta.value += '\n' + event.data
```

# 浏览器端 API

## WebSocket 构造函数

WebSocket 对象作为一个构造函数，用于新建 WebSocket 实例。

```js
var ws = new WebSocket('ws://localhost:8080');
```

执行上面语句之后，客户端就会与服务器进行连接。

实例对象的所有属性和方法清单，参见 [这里](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)。

## webSocket.readyState

`readyState` 属性返回实例对象的当前状态，共有四种。

- CONNECTING：值为 0，表示正在连接。
- OPEN：值为 1，表示连接成功，可以通信了。
- CLOSING：值为 2，表示连接正在关闭。
- CLOSED：值为 3，表示连接已经关闭，或者打开连接失败。

下面是一个示例。

```js
switch (ws.readyState) {
  case WebSocket.CONNECTING:
    // do something
    break;
  case WebSocket.OPEN:
    // do something
    break;
  case WebSocket.CLOSING:
    // do something
    break;
  case WebSocket.CLOSED:
    // do something
    break;
  default:
    // this never happens
    break;
}
```

## webSocket.onopen

实例对象的 `onopen` 属性，用于指定连接成功后的回调函数。

```js
ws.onopen = function () {
  ws.send('Hello Server!');
}
```

如果要指定多个回调函数，可以使用 `addEventListener` 方法。

```js
ws.addEventListener('open', function (event) {
  ws.send('Hello Server!');
});
```

## webSocket.onclose

实例对象的 `onclose` 属性，用于指定连接关闭后的回调函数。

```js
ws.onclose = function(event) {
  var code = event.code;
  var reason = event.reason;
  var wasClean = event.wasClean;
  // handle close event
};

ws.addEventListener("close", function(event) {
  var code = event.code;
  var reason = event.reason;
  var wasClean = event.wasClean;
  // handle close event
});
```

## webSocket.onmessage

实例对象的 `onmessage` 属性，用于指定收到服务器数据后的回调函数。

```js
ws.onmessage = function(event) {
  var data = event.data;
  // 处理数据
};

ws.addEventListener("message", function(event) {
  var data = event.data;
  // 处理数据
});
```

注意，服务器数据可能是文本，也可能是二进制数据（`blob` 对象或 `Arraybuffer` 对象）。

```js
ws.onmessage = function(event){
  if(typeof event.data === String) {
    console.log("Received data string");
  }

  if(event.data instanceof ArrayBuffer){
    var buffer = event.data;
    console.log("Received arraybuffer");
  }
}
```

除了动态判断收到的数据类型，也可以使用 `binaryType` 属性，显式指定收到的二进制数据类型。

```js
// 收到的是 blob 数据
ws.binaryType = "blob";
ws.onmessage = function(e) {
  console.log(e.data.size);
};

// 收到的是 ArrayBuffer 数据
ws.binaryType = "arraybuffer";
ws.onmessage = function(e) {
  console.log(e.data.byteLength);
};
```

## webSocket.send()

实例对象的 `send()` 方法用于向服务器发送数据。

**参数**

+ [`USVString`](https://developer.mozilla.org/zh-CN/docs/Web/API/USVString)：文本字符串。字符串将以 UTF-8 格式添加到缓冲区，并且 `bufferedAmount` 将加上该字符串以 UTF-8 格式编码时的字节数的值。

  ```js
  ws.send('your message');
  ```

+ [`Blob`](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob)：`Blob` 类型将队列 blob 中的原始数据以二进制中传输。 `bufferedAmount` 将加上原始数据的字节数的值。

  ```js
  var file = document
    .querySelector('input[type="file"]')
    .files[0];
  ws.send(file);
  ```

+ [`ArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/API/ArrayBuffer)：您可以使用一有类型的数组对象发送底层二进制数据；其二进制数据内存将被缓存于缓冲区，`bufferedAmount` 将加上所需字节数的值。

  ```js
  // Sending canvas ImageData as ArrayBuffer
  var img = canvas_context.getImageData(0, 0, 400, 320);
  var binary = new Uint8Array(img.data.length);
  for (var i = 0; i < img.data.length; i++) {
    binary[i] = img.data[i];
  }
  ws.send(binary.buffer);
  ```

+ [`ArrayBufferView`](https://developer.mozilla.org/zh-CN/docs/Web/API/ArrayBufferView)：您可以以二进制帧的形式发送任何 [JavaScript 类数组对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Typed_arrays) ；其二进制数据内容将被队列于缓冲区中。值 `bufferedAmount` 将加上必要字节数的值。

**实例**

+ 广播给所有连接的用户，包括自己

  ```js
  const WebSocket = require('ws');
  
  const server = new WebSocket.Server({ port: 8080 });
  
  server.on('connection', function connection(ws) {
    ws.on('message', function incoming(data) {
      server.clients.forEach(function each(client) {
        if (client.readyState === WebSocket.OPEN) {
          client.send(data);
        }
      });
    });
  });
  ```

+ 广播给所有连接的用户，不包括自己

  ```js
  const WebSocket = require('ws');
  
  const server = new WebSocket.Server({ port: 8080 });
  
  server.on('connection', function connection(ws) {
    ws.on('message', function incoming(data) {
      server.clients.forEach(function each(client) {
        if (client!=ws && client.readyState === WebSocket.OPEN) {
          client.send(data);
        }
      });
    });
  });
  ```

## webSocket.bufferedAmount

实例对象的 `bufferedAmount` 属性，表示还有多少字节的二进制数据没有发送出去。它可以用来判断发送是否结束。

```js
var data = new ArrayBuffer(10000000);
socket.send(data);

if (socket.bufferedAmount === 0) {
  // 发送完毕
} else {
  // 发送还没结束
}
```

## webSocket.onerror

实例对象的 `onerror` 属性，用于指定报错时的回调函数。

```js
socket.onerror = function(event) {
  // handle error event
};

socket.addEventListener("error", function(event) {
  // handle error event
});
```

# WS 完善

## WS 集成

但是有些时候我们不想让服务器开启两个端口，因为这样需要修改防火墙规则，也可能会造成一些安全性问题，所以我希望在 koa 监听的端口上挂载 ws 服务，那么我们只需要改一下上面的代码：

```js
const Koa = require('koa');
const WebSocket = require('ws');

// 监听8000端口
const app = new Koa();
let server = app.listen(8000);

// 同一个端口监听不同的服务
const wss = new WebSocket.Server({
    server
});
```

这样我们的 ws 服务就注册完成了，当数据通过 http 协议发送到服务器的时候，服务器会自动进行判断，并进行对应的相应。

### Koa

bin/www

```js
var app = require('../app');
...
var server = http.createServer(app.callback());
server.listen(port);
server.on('error', onError);
server.on('listening', onListening);

/* NOTE: ws */
const WebSocket = require('ws')
// 挂载在koa的服务器上
const webSocketServer = new WebSocket.Server({server})
const webSocketAPI = require('../ws')
webSocketAPI(webSocketServer)
```

# Ws API

## websocket.send(data\[, options][, callback])

`data` {Any} The data to send.

options，{Object}

- `compress` {Boolean} Specifies whether `data` should be compressed or not. Defaults to `true` when permessage-deflate is enabled.
- `binary` {Boolean} Specifies whether `data` should be sent as a binary or not. Default is autodetected.
- `mask` {Boolean} Specifies whether `data` should be masked or not. Defaults to `true` when `websocket` is not a server client.
- `fin` {Boolean} Specifies whether `data` is the last fragment of a message or not. Defaults to `true`.
- `callback` {Function} An optional callback which is invoked when `data` is written out.

+ Send `data` through the connection.
+ 每个用户连接之后都会创建一个 websocket 实例，这个实例就代表着用户，即是 client

# 实例属性

## readyState

| Constant   | Value | Description                                      |
| ---------- | ----- | ------------------------------------------------ |
| CONNECTING | 0     | The connection is not yet open.                  |
| OPEN       | 1     | The connection is open and ready to communicate. |
| CLOSING    | 2     | The connection is in the process of closing.     |
| CLOSED     | 3     | The connection is closed.                        |

```js
if (client.readyState === 1) {
  client.send(text)
}

if (client.readyState === WebSocket.OPEN) {
  client.send(text)
}
```

# 实例事件方法

## 概述

有三种写法，除了浏览器端的 DOM2 和 DOM3 写法，还可以用 on，但是用 on 是没有 event 事件对象的，只有 ws 配置的几个参数

如果是浏览器端的写法则是变成 event 对象的属性

```js
event.code
event.reson
event.target
```

## Close 事件

当客户端断开连接之后，要更新所有连接了的用户的列表

**Event: 'close'**

- `code` {Number}
- `reason` {String}

Emitted when the connection is closed. `code` is a numeric value indicating the status code explaining why the connection has been closed. `reason` is a human-readable string explaining why the connection has been closed.

+ event.code

  event.reson

**websocket.onclose**

- {Function}

+ An event listener to be called when connection is closed. The listener receives a `CloseEvent` named "close".
