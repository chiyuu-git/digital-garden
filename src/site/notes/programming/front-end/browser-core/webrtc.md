---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-09-03-Sat, 10:08:16 pm","date-modified":"2023-04-29-Sat, 8:14:28 pm","permalink":"/programming/front-end/browser-core/webrtc/","dgPassFrontmatter":true}
---


基本使用

# 概述

WebRTC 是“网络实时通信”（Web Real Time Communication）的缩写。它最初是为了解决浏览器上视频通话而提出的，即两个浏览器之间直接进行视频和音频的通信，不经过服务器。后来发展到除了音频和视频，还可以传输文字和其他数据。

Google 是 WebRTC 的主要支持者和开发者，它最初在 Gmail 上推出了视频聊天，后来在 2011 年推出了 Hangouts，语序在浏览器中打电话。它推动了 WebRTC 标准的确立。

简单来说，WebRTC 是一个可以在 Web 应用程序中实现音频，视频和数据的实时通信的开源项目。在实时通信中，音视频的采集和处理是一个很复杂的过程。比如音视频流的编解码、降噪和回声消除等，但是在 WebRTC 中，这一切都交由浏览器的底层封装来完成。我们可以直接拿到优化后的媒体流，然后将其输出到本地屏幕和扬声器，或者转发给其对等端。

WebRTC 的音视频处理引擎：

![img](https://pic4.zhimg.com/80/v2-6ded77857319ce0bacc0b246872adc8b_720w.jpg)

WebRTC 主要让浏览器具备三个作用。

+ 获取音频和视频
+ 进行音频和视频通信
+ 进行任意数据的通信

WebRTC 共分成三个 API，分别对应上面三个作用。

+ MediaStream （又称 getUserMedia）
+ RTCPeerConnection
+ RTCDataChannel

不过，虽然浏览器给我们解决了大部分音视频处理问题，但是从浏览器请求音频和视频时，我们还是需要特别注意流的大小和质量。因为即便硬件能够捕获高清质量流，CPU 和带宽也不一定可以跟上，这也是我们在建立多个对等连接时，不得不考虑的问题。

# 获取音视频流

## MediaDevices.getUserMedia(constraints)

`MediaDevices.getUserMedia()` 会提示用户给予使用媒体输入的许可，媒体输入会产生一个 [`MediaStream`](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream)，里面包含了请求的媒体类型的轨道。

此流可以包含一个视频轨道（来自硬件或者虚拟视频源，比如相机、视频采集设备和屏幕共享服务等等）、一个音频轨道（同样来自硬件或虚拟音频源，比如麦克风、A/D 转换器等等），也可能是其它轨道类型。

**参数**

+ **constraints**：作为一个 `MediaStreamConstraints` 对象，指定了请求的媒体类型和相对应的参数。

  ```js
  { audio: true, video: true }
  
  {
    audio: true,
    video: { width: 1280, height: 720 }
  }
  ```

+ 如果浏览器无法找到指定的媒体类型或者无法满足相对应的参数要求，那么返回的 Promise 对象就会处于 rejected［失败］状态，`NotFoundError` 作为 rejected［失败］回调的参数。
+ 强制要求获取特定的尺寸时，可以使用关键字 `min`, `max`, 或者 `exact`(就是 min == max). 以下参数表示要求获取最低为 1280x720 的分辨率。

  ```js
  {
    audio: true,
    video: {
      width: { min: 1280 },
      height: { min: 720 }
    }
  }
  ```

+ 如果摄像头不支持请求的或者更高的分辨率，返回的 Promise 会处于 rejected 状态，`NotFoundError作为`rejected 回调的参数，而且用户将不会得到要求授权的提示。
+ 当请求包含一个 ideal（应用最理想的）值时，这个值有着更高的权重，意味着浏览器会先尝试找到最接近指定的理想值的设定或者摄像头（如果设备拥有不止一个摄像头）。

  ```js
  {
    audio: true,
    video: {
      width: { min: 1024, ideal: 1280, max: 1920 },
      height: { min: 776, ideal: 720, max: 1080 }
    }
  }
  ```

+ 并不是所有的 constraints 都是数字。例如, 在移动设备上面，如下的例子表示优先使用前置摄像头（如果有的话）：

  ```js
  { audio: true, video: { facingMode: "user" } }
  ```

+ 强制使用后置摄像头，请用：

  ```js
  { audio: true, video: { facingMode: { exact: "environment" } } }
  ```

**返回值**

+ 它返回一个 [`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 对象，成功后会 `resolve` 回调一个 [`MediaStream`](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream) 对象。
+ 若用户拒绝了使用权限，或者需要的媒体源不可用，`promise` 会 `reject` 回调一个 `PermissionDeniedError` 或者 `NotFoundError` 。

  > 返回的 promise 对象可能既不会 resolve 也不会 reject，因为用户不是必须选择允许或拒绝。

**异常**

+ https://developer.mozilla.org/zh-CN/docs/Web/API/MediaDevices/getUserMedia

**描述**

+ ```js
  navigator.mediaDevices.getUserMedia(constraints)
  .then(function(stream) {
    /* 使用这个stream stream */
  })
  .catch(function(err) {
    /* 处理error */
  });
  ```

**帧率**

+ 在某些情况下，比如 WebRTC 上使用受限带宽传输时，低帧率可能更适宜。

  ```js
  var constraints = { video: { frameRate: { ideal: 10, max: 15 } } };
  ```

## 权限

在一个可安装的 app（如 [Firefox OS app](https://developer.mozilla.org/en-US/Apps/Build/Building_apps_for_Firefox_OS/Firefox_OS_app_beginners_tutorial)）中使用 `getUserMedia()` ，你需要在声明文件中指定以下的权限：

```js
"permissions": {
  "audio-capture": {
    "description": "Required to capture audio using getUserMedia()"
  },
  "video-capture": {
    "description": "Required to capture video using getUserMedia()"
  }
}
```

## 连接数据流和 DOM

在旧版本的媒体资源规范中，添加流作为 vedio 元素需要创建一个关于 [`MediaStream`](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream) 的对象 URL。现已没必要这样做了，浏览器已经移除了该操作的支持。

```js
video.src = stream;
video.srcObject = mediaStream; // mediaStream 专用属性
```

Chrome 和 Opera 还允许 getUserMedia 获取的音频数据，直接作为 audio 或者 video 元素的值，也就是说如果还获取了音频，上面代码播放出来的视频是有声音的。

## 示例：获取摄像头

下面通过 getUserMedia 方法，将摄像头拍摄的图像展示在网页上。

首先，需要先在网页上放置一个 video 元素。图像就展示在这个元素中。

```html
<video id="webcam"></video>
```

然后，用代码获取这个元素。

```js
function onSuccess(stream) {
    var video = document.getElementById('webcam');
}
```

接着，将这个元素的 src 属性绑定数据流，摄影头拍摄的图像就可以显示了。

```js
// 想要获取一个最接近 1280x720 的相机分辨率
var constraints = { audio: true, video: { width: 1280, height: 720 } } 

navigator.mediaDevices.getUserMedia(constraints)
.then(function(mediaStream) {
  video.srcObject = mediaStream
  video.onloadedmetadata = function(e) {
    video.play()
  }
})
.catch(function(err) { console.log(err.name + ": " + err.message); }) // 总是在最后检查错误
```

## 截屏

获取摄像头的主要用途之一，是让用户使用摄影头为自己拍照。Canvas API 有一个 ctx.drawImage(video, 0, 0) 方法，可以将视频的一个帧转为 canvas 元素。这使得截屏变得非常容易。

```html
<video autoplay></video>
<img src="">
<canvas style="display:none;"></canvas>

<script>
  var video = document.querySelector('video');
  var canvas = document.querySelector('canvas');
  var ctx = canvas.getContext('2d');
  var localMediaStream = null;

  function snapshot() {
    if (localMediaStream) {
      ctx.drawImage(video, 0, 0);
      // “image/webp”对Chrome有效，
      // 其他浏览器自动降为image/png
      document.querySelector('img').src = canvas.toDataURL('image/webp');
    }
  }

  video.addEventListener('click', snapshot, false);

  navigator.getUserMedia({video: true}, function(stream) {
    video.src = window.URL.createObjectURL(stream);
    localMediaStream = stream;
  }, errorCallback);
</script>
```

## MediaStreamTrack.getSources()

如果本机有多个摄像头/麦克风，这时就需要使用 MediaStreamTrack.getSources 方法指定，到底使用哪一个摄像头/麦克风。

```js
MediaStreamTrack.getSources(function(sourceInfos) {
  var audioSource = null;
  var videoSource = null;

  for (var i = 0; i != sourceInfos.length; ++i) {
    var sourceInfo = sourceInfos[i];
    if (sourceInfo.kind === 'audio') {
      console.log(sourceInfo.id, sourceInfo.label || 'microphone');

      audioSource = sourceInfo.id;
    } else if (sourceInfo.kind === 'video') {
      console.log(sourceInfo.id, sourceInfo.label || 'camera');

      videoSource = sourceInfo.id;
    } else {
      console.log('Some other kind of source: ', sourceInfo);
    }
  }

  sourceSelected(audioSource, videoSource);
});

function sourceSelected(audioSource, videoSource) {
  var constraints = {
    audio: {
      optional: [{sourceId: audioSource}]
    },
    video: {
      optional: [{sourceId: videoSource}]
    }
  };

  navigator.getUserMedia(constraints, onSuccess, onError);
}
```

上面代码表示，MediaStreamTrack.getSources 方法的回调函数，可以得到一个本机的摄像头和麦克风的列表，然后指定使用最后一个摄像头和麦克风。

## 结束使用

https://s0developer0mozilla0org.icopy.site/zh-CN/docs/Web/API/MediaDevices/getUserMedia

https://s0developer0mozilla0org.icopy.site/zh-CN/docs/Web/API/MediaStream

https://s0developer0mozilla0org.icopy.site/zh-CN/docs/Web/API/MediaStream/getTracks

https://s0developer0mozilla0org.icopy.site/zh-CN/docs/Web/API/MediaStreamTrack

```
mediaStream.getTracks()[0].stop();
```

# 录音

## 获取音频

使用 WebRTC 的 getUserMedia，获取调起麦克风并获取数据

```js
function record () {
    window.navigator.mediaDevices.getUserMedia({
        audio: true
    }).then(mediaStream => {
        console.log(mediaStream);
        beginRecord(mediaStream);
    }).catch(err => {
        // 如果用户电脑没有麦克风设备或者用户拒绝了，或者连接出问题了等
        // 这里都会抛异常，并且通过err.name可以知道是哪种类型的错误 
        console.error(err);
    })  ;
}
```

指定音频的格式

```js
window.navigator.mediaDevices.getUserMedia({
    audio: {
        sampleRate: 44100, // 采样率
        channelCount: 2,   // 声道
        volume: 1.0        // 音量
    }
}).then(mediaStream => {
    console.log(mediaStream);
});
```

## 保存音频

出于实际考虑，一般不会一边录音一边播放音频，往往我们需要先把录音保存起来，之后再播放。

它是音频流的抽象，把这个流用来初始化一个 `MediaStreamAudioSourceNode` 对象，然后把这个节点 `connect` 连接到一个 `JavascriptProcessorNode`，在它的 `onaudioprocess` 里面获取到音频数据，然后保存起来，就得到录音的数据。

https://juejin.im/post/5b8bf7e3e51d4538c210c6b0#heading-2

API 怪异且不稳定，直接用第三方库进行音频处理

## 第三方库

地址：https://github.com/2fps/recorder

# 信令与视频通话

## 流程

[WebRTC](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API) 是一个完全对等技术，用于实时交换音频、视频和数据，同时提供一个中心警告。如其他地方所讨论的，必须进行一种发现和媒体格式协商，以使不同网络上的两个设备相互定位。这个过程被称为**信令**，并涉及两个设备连接到第三个共同商定的服务器。通过这个第三方服务器，这两台设备可以相互定位，并交换协商消息。

在信令阶段需要完成的任务：

+ 为每个连接端创建一个 RTCPeerConnection 实例，并添加本地媒体流。
+ 双向通信服务辅助信息交换，如 Socket 等：
  + 获取并交换本地和远程描述：SDP 格式的本地媒体元数据。
  + 获取并交换网络信息：潜在的连接端点称为 ICE 候选者
+ STUN、TURN 辅助实现 NAT 穿越，建立点对点连接

## WebRTC 架构概述

用来创建 WebRTC 连接的 API 底层使用了一系列的网络协议和连接标准。这篇文章涵盖了这些标准。

### SDP

会话描述协议 [Session Description Protocol (SDP)](http://en.wikipedia.org/wiki/Session_Description_Protocol) 是一个描述多媒体连接内容的协议，例如分辨率，格式，编码，加密算法等。所以在数据传输时两端都能够理解彼此的数据。本质上，这些描述内容的元数据并不是媒体流本身。

![img](/img/user/programming/front-end/browser-core/webrtc/27612-20190831135602926-602146104.png)

**媒体信息**

通常某个浏览器所在的电脑，都会连接具体的多媒体设备（比如：麦克风、摄像头)。如果 A 电脑上的摄像头只支持 VP8，H264 格式，而另一台电脑上的摄像头只支持 H264、MPEG-4 格式，它俩要能正常播放彼此的视频，肯定会选择双方都能识别的 H264 格式。

![img](/img/user/programming/front-end/browser-core/webrtc/27612-20190831135531303-281615681.png)

**网络信息**

+ 网络情况也是类似的，二个浏览器所在的电脑可能在不同的网络环境中，假如 A 机器具备公网 +192 内网网段，而 B 机器只有 192+198 内网网段，二台电脑要能相互连接，很容易想到，使用双方都能连通的公共 192 内网网段通信最为方便。

### ICE

交互式连接设施 [Interactive Connectivity Establishment (ICE)](http://en.wikipedia.org/wiki/Interactive_Connectivity_Establishment) 是一个允许你的浏览器和对端浏览器建立连接的协议框架。在实际的网络当中，有很多原因能导致简单的从 A 端到 B 端直连不能如愿完成：

+ 这需要绕过阻止建立连接的防火墙
+ 给你的设备分配一个唯一可见的地址（通常情况下我们的大部分设备没有一个固定的公网地址）
+ 如果路由器不允许主机直连，还得通过一台服务器转发数据。

ICE 通过使用以下几种技术完成上述工作。

![img](/img/user/programming/front-end/browser-core/webrtc/27612-20190831135612273-714303056.png)

这是 mozilla 开发者官网上的一张图, 大致描述了 webrtc 的处理过程：

- A 通过 STUN 服务器，收集自己的网络信息
- A 创建 Offer SDP，通过 Signal Channel(信令服务器) 给到 B
- B 做出回应生成 Answer SDP，通过 Signal Channel 给到 A
- B 通过 STUN 收集自己的网络信息，通过 Signal Channel 给到 A

注：如果 A,B 之间无法直接穿透（即：无法建立点对点的 P2P 直连），将通过 TURN 服务器中转。

### NAT

网络地址转换协议 [Network Address Translation (NAT)](http://en.wikipedia.org/wiki/NAT) 用来给你的（私网）设备映射一个公网的 IP 地址的协议。一般情况下，路由器的 WAN 口有一个公网 IP，所有连接这个路由器 LAN 口的设备会分配一个私有网段的 IP 地址（例如 192.168.1.3）。私网设备的 IP 被映射成路由器的公网 IP 和唯一的端口，通过这种方式不需要为每一个私网设备分配不同的公网 IP，但是依然能被外网设备发现。

一些路由器严格地限定了部分私网设备的对外连接。这种情况下，即使 STUN 服务器识别了该私网设备的公网 IP 和端口的映射，依然无法和这个私网设备建立连接。这种情况下就需要转向 TURN 协议。

### STUN

NAT 的会话穿越功能 [Session Traversal Utilities for NAT (STUN)](http://en.wikipedia.org/wiki/STUN) (缩略语的最后一个字母是 NAT 的首字母) 是一个允许位于 NAT 后的客户端找出自己的公网地址，判断出路由器阻止直连的限制方法的协议。

客户端通过给公网的 STUN 服务器发送请求获得自己的公网地址信息，以及是否能够被（穿过路由器）访问。

![An interaction between two users of a WebRTC application involving a STUN server.](/img/user/programming/front-end/browser-core/webrtc/webrtc-stun.png)

### TURN

一些路由器使用一种“对称型 NAT”的 NAT 模型。这意味着路由器只接受和对端先前建立的连接（就是下一次请求建立新的连接映射）。

NAT 的中继穿越方式 [Traversal Using Relays around NAT (TURN)](http://en.wikipedia.org/wiki/TURN) 通过 TURN 服务器中继所有数据的方式来绕过“对称型 NAT”。你需要在 TURN 服务器上创建一个连接，然后告诉所有对端设备发包到服务器上，TURN 服务器再把包转发给你。很显然这种方式是开销很大的，所以只有在没得选择的情况下采用。

## 基础示例：本地 1v1 对等连接

我们先来实现一个本地的对等连接，借此熟悉一下流程和部分 API。

```html
<video id='A'  width="300" height="150"></video>
<video id="B" width="300" height="150"></video>
<button id='receive'>接受视频</button>
```

A 作为输出端，需要获取到本地流并添加到自己的 RTCPeerConnection；B 作为呼叫端，并没有输出的需求，因此只需要接收流。

### 步骤

1. 初始化 RTC 端口

   ```js
   // 初始化RTC端口
   let PeerConnection = 
     window.RTCPeerConnection ||
     window.mozRTCPeerConnection ||
     window.webkitRTCPeerConnection
   
   let peerA=peerB=null
   peerA = new PeerConnection()
   peerB = new PeerConnection()
   ```

2. 端口 A 获取视频流，`addStream` 方法将 `getUserMedia` 方法中获取的流 (`stream`) 添加到 `RTCPeerConnection` 对象中，以进行传输

   ```js
   const constraints = { audio: true, video: true }
   navigator.mediaDevices.getUserMedia(constraints)
   .then(function(mediaStream) {
     A.srcObject = mediaStream
     A.onloadedmetadata = function(e) {
       A.play()
     }
     // 获取到媒体流后，添加到端口A
     peerA.addStream(mediaStream)
   })
   .catch(function(err) { console.log(err.name + ": " + err.message); })
   ```

3. 点击按钮的时候，端口 A 创建 `offer sdp`，并发送给 B，同时设置**本地 sdp 描述符**。

   + `createOffer()` 是 `RTCPeerConnection` 对象自带的方法，用来创建 `offer`
   + 创建成功后调用 `setLocalDescription` 方法将 `localDescription` 设置为 `offer`
   + `localDescription` 即为我们需要发送给应答方的 `sdp`

   ```js
   receive.onclick = function(){
     // 发起RTC连接请求
     peerA.createOffer().then((offer) => {
       peerA.setLocalDescription(offer).then(
         answer(offer) // sendOffer
       )
     })
   }
   ```

4. 端口 B 收到 A 发送的 sdp，做出应答，创建 `answer sdp`

   + `offer sdp` 是端口 B 的 `remoteDescription` 属性
   + 同时 `answer sdp` 是端口 A 的 `remoteDescription` 属性
   + `offer` 和 `answer` 都有 sdp 属性，包含着用于交换的 sdp 描述符

   ```js
   async function answer(offer){
     // 接收端设置远程 offer sdp 描述
     peerB.setRemoteDescription(offer)
     // 接收端创建 answer
     let answer = await peerB.createAnswer()
     // 接收端设置本地 answer sdp 描述 
     peerB.setLocalDescription(answer)
     // 发起端 要接受 answer sdp 描述
     peerA.setRemoteDescription(answer)
   }
   ```

5. 此时虽然 `WebRTC` 连接已经完成，但是通信双方还不能直接通信，因为发送的 `ICE` 还没有处理，通信双方还没有确定最优的连接方式。此时 `onicecandidate` 事件会不断的触发，用来寻找合适的 `ICE`，每次端口号都会变化和协议等 都可能变化

   ```js
   // 监听 A 的ICE候选信息 如果收集到，就添加给 B 连接状态
   peerA.onicecandidate = (event) => {
     const can = event.candidate
     if (can) peerB.addIceCandidate(can)
   }
   // 监听 B 的ICE候选信息 如果收集到，就添加给 A 连接状态
   peerB.onicecandidate = (event) => {
     const can = event.candidate
     if (can) peerA.addIceCandidate(can)
   }
   ```

   ![image-20200305182607034](/img/user/programming/front-end/browser-core/webrtc/image-20200305182607034.png)

6. 当协商稳定之后，端口 B 就会接收到视频流。`onaddstream` 事件用来监听通道中新加入的流，通过 `event.stream` 获取

   ```js
   // 监听是否有媒体流接入，如果有就赋值给 rtcB 的 src
   peerB.onaddstream = (e) => {
     B.srcObject = event.stream
     B.onloadedmetadata = function(e) {
       B.play()
     }
   }
   ```

+ `sendOffer` 和 `sendCandidate` 方法是自定义方法，用来将数据发送给服务器

## 网络 1v1 对等连接

本地连接与网络连接的区别：

+ 本地连接端口都在同一个作用域，可以直接传递 `offer sdp` 和 `answer sdp`，以及 `iceCandidate`
+ 因此网络连接只需要把这些信息通过服务器进行传递即可

网络服务器我们使用 ws

### 候选目标

要通过服务器进行连接，我们先要有一个已经连接到服务器上的目标，因此需要先实现一个连接用户列表

每当有一个用户连接到 ws 服务器时，就向全部用户广播，并添加到 textarea 中显示

## 信令服务器

两个设备之间建立 WebRTC 连接需要一个**信令服务器**来实现双方通过网络进行连接。信令服务器的作用是作为一个中间人帮助双方在尽可能少的暴露隐私的情况下建立连接。

WebRTC 并没有提供信令传递机制，你可以使用任何你喜欢的方式如 [WebSocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket_API) 或者 [`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest) 等等，来交换彼此的令牌信息。

重要的是信令服务器并不需要理解和解释信令数据内容。虽然它基于 [SDP](https://developer.mozilla.org/en-US/docs/Glossary/SDP) 但这并不重要：通过信令服务器的消息的内容实际上是一个黑盒。重要的是，当 [ICE](https://developer.mozilla.org/en-US/docs/Glossary/ICE) 子系统指示你将信令数据发送给另一个对等方时，你就这样做，而另一个对等方知道如何接收此信息并将其传递给自己的 ICE 子系统。你所要做的就是来回传递信息。内容对信令服务器一点都不重要。

### 聊天服务器

我们的 [聊天服务器和客户端](https://github.com/mdn/samples-server/tree/master/s/websocket-chat) 使用 [WebSocket API](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket_API) [JSON](https://developer.mozilla.org/en-US/docs/Glossary/JSON) 格式的字符串来传递数据。服务器支持多种消息格式来处理不同的任务，比如注册新用户、设置用户名、发送公共信息等等。

为了让服务器支持信令和 ICE 协商，我们需要升级代码，我们需要直接发送聊天系统到指定的用户而不是发送给所有人，并且保证服务器在不需要理解数据内容的情况下传递未被认可的任何消息类型。这让我们可以使用一台服务器来传递信令和消息而不是多台。

让我们看一下我们还需要做些什么让它支持 WebRTC 信令. 代码在 [chatserver.js](https://github.com/mdn/samples-server/tree/master/s/webrtc-from-chat/chatserver.js).中实现。

首先来看 `sendToOneUser()` 函数，如名所示它发送 JSON 字符串到指定的用户。

```js
function sendToOneUser(target, msgString) {
  var isUnique = true;
  var i;

  for (i=0; i<connectionArray.length; i++) {
    if (connectionArray[i].username === target) {
      connectionArray[i].sendUTF(msgString);
      break;
    }
  }
}
```

这个函数遍历所有在线用户直到找到给定的用户名然后发送数据 `msgString` 一个 JSON 字符串对象，我们可以让它接收我们的原始消息对象，但是在当前这种情况下它的效率更高因为我们的消息已经字符串化了，我们达到了不需要进一步处理就可以发送消息的目的。

我们原来的 DEMO 不能发送消息到指定的用户，我们可以通过修改 WebSocket 消息处理句柄来实现这个功能，这需要在 `connection.on()` 尾部修改。

```js
if (sendToClients) {
  var msgString = JSON.stringify(msg);
  var i;

  // If the message specifies a target username, only send the
  // message to them. Otherwise, send it to every user.
  if (msg.target && msg.target !== undefined && msg.target.length !== 0) {
    sendToOneUser(msg.target, msgString);
  } else {
    for (i=0; i<connectionArray.length; i++) {
      connectionArray[i].sendUTF(msgString);
    }
  }
}
```

代码会检查我们的数据是否提供了 `target` 属性. 这个属性包含了我们想要发送给的人的用户名。如果提供了 `target` 属性, 通过调用 `sendToOneUser()` 消息将只发送给指定的人. 否则的话将遍历在线列表发送给每一个人。

由于现行的代码可以发送任意类型的消息，所以我们不需要做任何的修改。现在我们的客户端可以发送任意消息给指定的用户。

我们需要做的在服务器这边，现在我们来考虑信令协议的设计与实现。

### 设计信令协议

现在我们要构建一套信息交换规则，我们需要一套协议来定义消息格式。实现这个有好多种办法，demo 里只是其中一种，并不是唯一。

例子中的服务器使用字符串化的 JSON 对象来和客户端通信，意味着我们的信令消息也将使用 JSON 格式，其内容指定消息类型和如何处理这些消息。

#### **交换会话描述信息**

开始处理信号的时候，用户的初始化操作会创建一个**请求（offer）** ，根据 [SDP](https://developer.mozilla.org/en-US/docs/Glossary/SDP) 协议其中会包含一个 session 描述符，并且需要把这个发送到我们称之为**接收者（callee）**那里， 接受者需要返回一个包含描述符的**应答（answer）**信息。我们的服务器使用 WebSocket 来传递 `"video-offer"` `"video-answer"` 两种类型的消息数据。这些消息包含以下属性：

+ type，消息类型; `"video-offer"` 或 `"video-answer"`
+ name，发送者用户名
+ target，接受者的用户名（如果呼叫者正在发送消息，则指定被呼叫者，反之亦然）
+ sdp，描述连接本地端 SDP（Session Description Protocol）协议字符串（从接收者的角度来看，它描述远程端）

到此为止双方都知道使用什么样的代码和参数进行通信了。尽管如此他们仍然不知道自己该如何传递媒体数据。 [Interactive Connectivity Establishment (ICE)](https://developer.mozilla.org/en-US/docs/Glossary/ICE) 协议该上场了。

#### 交换 ICE 候选

两个节点需要交换 ICE 候选来协商他们自己具体如何连接。每一个 ICE 候选描述一个发送者使用的通信方法，每个节点按照他们被发现的顺序发送候选并且保持发送直到退出，即使媒体数据流已经开始传递也要如此。

使用 `pc.setLocalDescription(offer)` 添加本地描述符后一个 `icecandidate` 事件将被发送到 [`RTCPeerConnection`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection)

一旦两端同意了一个互相兼容的候选，该候选的 SDP 就被用来创建并打开一个连接，通过该连接媒体流就开始运转。如果之后他们同意了一个更好（通常更高效）的候选，流亦会按需变更格式。

> 虽然当前并未被支持，一个候选在媒体流已经开始运转之后理论上如果需要的话也可以降级至一个低带宽的连接。

每个 ICE 候选通过信令服务器发送一个 `"new-ice-candidate"` 类型的 JSON 信息来送给远程的另一端。每个候选信息包括以下字段：

+ type：消息类型： `"new-ice-candidate"`
+ target：待建立联系人的用户名；服务器将仅会管理与该用户的信息。
+ candidate：SDP 候选字符串，描述了计划的连接方法。通常不需要查看此字符串的内容。你需要做的所有代码都是使用信令服务器将其路由到远程对等机。

每个 ICE 消息都建议提供一个通信协议（TCP 或 UDP）、IP 地址、端口号、连接类型（例如，指定的 IP 是对等机本身还是中继服务器），以及将两台计算机连接在一起所需的其他信息。这包括 NAT 或其他网络问题。

>**注意:** 最需要注意的是: 你的代码在 ICE 协商期间唯一需要负责的是从 ICE 层接受外向候选并通过与另一端的信号连接发送他们，当你的 [`onicecandidate`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection/onicecandidate) 控制器已经执行后, 同时从信令服务器接收 ICE 候选消息 (当接收到 `"new-ice-candidate"` 消息时) 然后通过调用 [`RTCPeerConnection.addIceCandidate()`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection/addIceCandidate) 发送他们到你的 ICE 层。 嗯，就是这样。
>
>SDP 的内容基本上在所有情况下都是与你不相关的。在你真正知道自己在做什么之前，不要试图让事情变得更复杂。否则情况会非常混乱。

你的信令服务器现在需要做的就是发送它请求的消息。你的工作流还可能需要登录/身份验证功能，但这些细节都是大同小异的。

#### 信令事务流程

信令过程涉及到使用中间层信令服务器在两个对等机之间交换消息。当然，具体的处理过程会有所不同，但一般来说，处理信令消息的关键点有以下几个：

+ 在 Web 浏览器中运行的每个用户的客户端
+ 每个用户的 Web 浏览器
+ 信令服务器
+ 承载聊天服务的 Web 服务器

假设 Naomi 和 Priya 正在使用聊天软件进行讨论，Naomi 决定在两人之间打开一个视频通话。以下是预期的事件顺序：

![Diagram of the signaling process](webrtc/WebRTC%20-%20Signaling%20Diagram.svg)

#### ICE 候选交换过程

当每端的 ICE 层开始发送候选时，它会在链中的各个点之间进行交换，如下所示：

![Diagram of ICE candidate exchange process](webrtc/WebRTC%20-%20ICE%20Candidate%20Exchange.svg)

每一端从本地的 ICE 层接收候选时，都会将其发送给另一方；不存在轮流或成批的候选。一旦两端就一个候选达成一致，双方就都可以用此候选来交换媒体数据，媒体数据就开始流动。即使在媒体数据已经开始流动之后，每一端都会继续向候选发送消息，直到他们没有选择的余地。这样做是为了找到比最初选择的更好的选择。

如果条件发生变化，例如网络连接恶化，一个或两个对等方可能建议切换到较低带宽的媒体分辨率，或其他编解码器。这将触发新的候选交换，之后可能会发生另一种媒体格式和/或编解码器更改。

作为可选项， 查看 [RFC 5245: Interactive Connectivity Establishment](https://tools.ietf.org/html/rfc5245), [section 2.6 ("Concluding ICE")](https://tools.ietf.org/html/rfc5245#section-2.6) 如果你想更深入地了解这一过程，就要在 ICE 层内部完成。你应该注意到，候选交换后，一旦 ICE 层满足要求，媒体数据就开始流动。所有这些都是在幕后处理端。我们的任务就是简单地通过信令服务器来回发送候选。

## 客户端应用

任何信号处理的核心是其消息处理。使用 WebSockets 来发送信号并不是必须的，但这是一种常见的解决方案。当然，您应该选择一种机制来交换适合你的应用程序的信号信息。

让我们更新聊天客户端以支持视频呼叫。

### 更新 HTML

我们客户端的 HTML 需要一个视频显示位置。也就是视频框和挂断电话的按钮：

```html
<div class="flexChild" id="camera-container">
  <div class="camera-box">
    <video id="received_video" autoplay></video>
    <video id="local_video" autoplay muted></video>
    <button id="hangup-button" onclick="hangUpCall();" disabled>
      Hang Up
    </button>
  </div>
</div>
```

注意这两个 video 元素，一个用于观看自己，一个用于连接，还有 button 元素.

`id` 为 "`received_video`" 的 元素将显示从连接的用户接收的视频。我们指定了 `autoplay` 属性，确保一旦视频到达，它立即播放。这消除了在代码中显式处理回放的任何需要。

`local_video` 元素显示用户相机的预览；指定 `muted` 属性，因为我们不需要在此预览面板中听到本地音频。

最后，定义 "`hangup-button`" 来挂断一个呼叫，并将其配置为禁用启动（将此设置为未连接任何调用时的默认设置），并在单击时调用函数 `hangUpCall()` 。这个函数的作用是关闭调用，并向另一个对等端发送一个信号服务器通知，请求它也关闭。

### JavaScript 代码

我们将把这段代码划分为多个功能区，以便更容易地描述它是如何工作的。该代码的主体位于 `connect()` 函数中：它在 6503 端口上打开一个 [`WebSocket`](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket) 服务器，并建立一个处理程序来接收 JSON 对象格式的消息。此代码通常像以前那样处理文本聊天消息。

#### **向信令服务器发送信息**

在整个代码中，我们调用 `sendToServer()` 以便向信令服务器发送消息。此函数使用 [WebSocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) 连接执行其工作：

```js
function sendToServer(msg) {
  var msgJSON = JSON.stringify(msg);

  connection.send(msgJSON);
}
```

#### 开始通话的交互

处理 `"userlist"` 消息的代码会调用 `handleUserlistMsg()`。在这里，我们在聊天面板左侧显示的用户列表中为每个连接的用户设置处理程序。此方法接收一个消息对象，其 `users` 属性是一个字符串数组，指定每个连接用户的用户名。

```js
function handleUserlistMsg(msg) {
  var i;
  var listElem = document.querySelector(".userlistbox");

  while (listElem.firstChild) {
    listElem.removeChild(listElem.firstChild);
  }

  msg.users.forEach(function(username) {
    var item = document.createElement("li");
    item.appendChild(document.createTextNode(username));
    item.addEventListener("click", invite, false);

    listElem.appendChild(item);
  });
}
```

在获得对 ul 的引用（其中包含变量 `listElem` 中的用户名列表）后，我们通过删除其每个子元素清空列表。

> **注意：**显然，通过添加和删除单个用户而不是每次更改时都重新构建整个列表来更新列表会更有效，但对于本例而言，这已经足够好了。

然后我们使用 [`forEach()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) 迭代用户名数组。对于每个名称，我们创建一个新的 li 元素，然后使用 [`createTextNode()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/createTextNode) 创建一个包含用户名的新文本节点。该文本节点被添加为 li 元素的子节点。接下来，我们为列表项上的 `click` 事件设置一个处理程序，单击用户名将调用 `invite()` 方法，我们将在下一节中查看该方法。

#### 开始一个通话

当用户单击要调用的用户名时，将调用 `invite()` 函数作为该事件的事件处理程序 `click` 事件：

```js
var mediaConstraints = {
  audio: true, // We want an audio track
  video: true // ...and we want a video track
};

function invite(evt) {
  if (myPeerConnection) {
    alert("You can't start a call because you already have one open!");
  } else {
    var clickedUsername = evt.target.textContent;

    if (clickedUsername === myUsername) {
      alert("I'm afraid I can't let you talk to yourself. That would be weird.");
      return;
    }

    targetUsername = clickedUsername;

    createPeerConnection();

    navigator.mediaDevices.getUserMedia(mediaConstraints)
    .then(function(localStream) {
      document.getElementById("local_video").srcObject = localStream;
      myPeerConnection.addStream(localStream);
    })
    .catch(handleGetUserMediaError);
  }
}
```

这从一个基本的健全性检查开始：用户是否连在一起？如果没有 [`RTCPeerConnection`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection) ，他们显然无法进行呼叫。然后，从事件目标的 [`textContent`](https://developer.mozilla.org/zh-CN/docs/Web/API/Node/textContent) 属性中获取单击的用户的名称，并检查以确保尝试启动调用的不是同一个用户。

然后我们将要调用的用户的名称复制到变量 `targetUsername` 中，并调用 `createPeerConnection()`，该函数将创建并执行 [`RTCPeerConnection`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection) 的基本配置。

创建 `RTCPeerConnection` 后，我们通过调用 [`MediaDevices.getUserMedia()`](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaDevices/getUserMedia)，请求访问用户的相机和麦克风，该命令通过 `Navigator.mediaDevices.getUserMedia` 属性向我们公开。当成功完成返回的 promise 时，将执行我们的 `then` 处理程序。它接收一个 [`MediaStream`](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream) 对象作为输入，该对象表示来自用户麦克风的音频和来自网络摄像机的视频流。

> **注意：我们可以通过调用** [`navigator.mediaDevices.enumerateDevices()`](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaDevices/enumerateDevices) **获取设备列表，根据所需条件筛选结果列表，然后使用所选设备**`deviceId` **传入**`getUserMedia() `**的** `mediaConstraints` **对象的**`deviceId` **字段中的值。事实上，除非必须要不然很少这样用，因为大部分工作都是由** `getUserMedia()`**为你完成的。**

我们通过设置元素的 [`srcObject`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLMediaElement/srcObject) 属性，将传入流附加到本地预览 video 元素。由于元素被配置为自动播放传入的视频，因此流开始在本地预览框中播放。

然后遍历流中的磁道，调用 `addStream()` 将每个磁道添加到 `RTCPeerConnection`。尽管连接尚未完全建立，但必须尽快开始向其发送媒体数据，因为媒体数据将帮助 ICE 层决定采取的最佳连接方式，这有助于协商过程。

一旦媒体数据连接到 `RTCPeerConnection`，就会在连接处触发事件 `negotiationneeded` 事件，以便启动 ICE 协商。

如果在尝试获取本地媒体流时发生错误，catch 子句将调用 `handleGetUserMediaError()`，根据需要向用户显示适当的错误。

#### 处理 getUserMedia() 错误

如果 `getUserMedia()` 返回的 promise 失败，将执行 `handleGetUserMediaError()` 函数。

```js
function handleGetUserMediaError(e) {
  switch(e.name) {
    case "NotFoundError":
      alert("Unable to open your call because no camera and/or microphone" +
            "were found.");
      break;
    case "SecurityError":
    case "PermissionDeniedError":
      // Do nothing; this is the same as the user canceling the call.
      break;
    default:
      alert("Error opening your camera and/or microphone: " + e.message);
      break;
  }

  closeVideoCall();
}
```

除了一条错误信息外，所有情况下都会显示一条错误信息。在本例中，我们忽略 `"SecurityError"` 和 `"PermissionDeniedError"` 结果，处理拒绝授予使用媒体硬件的权限与用户取消呼叫的方法是相同的。

不管尝试获取流失败的原因是什么，我们调用 `closeVideoCall()` 函数关闭 [`RTCPeerConnection`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection)，并释放尝试调用过程中已分配的任何资源。此代码旨在安全地处理部分启动的调用。

#### 创建端到端连接

调用方和被调用方都使用 `createPeerConnection()` 函数来构造它们的 [`RTCPeerConnection`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection) 对象及其各自的 WebRTC 连接端。当调用者试图启动调用时，由 `invite()` 调用；当被调用者从调用者接收到要约消息时，由 `handleVideoOfferMsg()` 调用。

```js
function createPeerConnection() {
  myPeerConnection = new RTCPeerConnection({
    iceServers: [     // Information about ICE servers - Use your own!
      {
        urls: "stun:stun.stunprotocol.org"
      }
    ]
  });

  myPeerConnection.onicecandidate = handleICECandidateEvent;
  myPeerConnection.ontrack = handleTrackEvent;
  myPeerConnection.onnegotiationneeded = handleNegotiationNeededEvent;
  myPeerConnection.onremovetrack = handleRemoveTrackEvent;
  myPeerConnection.oniceconnectionstatechange = handleICEConnectionStateChangeEvent;
  myPeerConnection.onicegatheringstatechange = handleICEGatheringStateChangeEvent;
  myPeerConnection.onsignalingstatechange = handleSignalingStateChangeEvent;
}
```

当使用 [`RTCPeerConnection()`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection/RTCPeerConnection) 构造函数时，我们将指定一个 [`RTCConfiguration`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCConfiguration)- 兼容对象，为连接提供配置参数。在这个例子中，我们只使用其中的一个： `iceServers`。这是描述 [ICE](https://developer.mozilla.org/en-US/docs/Glossary/ICE) 层的 STUN 和/或 TURN 服务器的对象数组，在尝试在呼叫者和被呼叫者之间建立路由时使用。这些服务器用于确定在对等端之间通信时要使用的最佳路由和协议，即使它们位于防火墙后面或使用 [NAT](https://developer.mozilla.org/en-US/docs/Glossary/NAT)。

> **注意：你应该始终使用你拥有的或你有特定授权使用的**STUN/TURN**服务器。这个例子是使用一个已知的公共服务器，但是滥用这些是不好的。**

`iceServers` 中的每个对象至少包含一个 `urls` 字段，该字段提供可以访问指定服务器的 URLs。它还可以提供 `username` 和 `credential` 值，以便在需要时进行身份验证。

在创建了 [`RTCPeerConnection`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection) 之后，我们为对我们重要的事件设置了处理程序。

前三个事件处理程序是必需的；你必须处理它们才能使用 WebRTC 执行任何涉及流媒体的操作。其余的并不是严格要求的，但可能有用，我们将对此进行探讨。在这个例子中，还有一些其他的事件我们没有使用。下面是我们将要实现的每个事件处理程序的摘要：

+ **[`RTCPeerConnection.onicecandidate`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection/onicecandidate)**：当需要你通过信令服务器将一个 ICE 候选发送给另一个对等端时，本地 ICE 层将会调用你的 `icecandidate` 事件处理程序。有关更多信息，请参阅 [Sending ICE candidates](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Signaling_and_video_calling#Sending_ICE_candidates) 以查看此示例的代码。
+ [`RTCPeerConnection.ontrack`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection/ontrack)：当向连接中添加磁道时，`track` 事件的此处理程序由本地 WebRTC 层调用。例如，可以将传入媒体连接到元素以显示它。详见 [Receiving new streams](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Signaling_and_video_calling#Receiving_new_streams) 。
+ https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Signaling_and_video_calling

#### 开始协商

一旦调用者创建了其 [`RTCPeerConnection`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection) ，创建了媒体流，并将其磁道添加到连接中，如 **开始一个通话**所示，浏览器将向 [`RTCPeerConnection`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection) 传递一个 `negotiationneeded` 事件，以指示它已准备好开始与其他对等方协商。以下是我们处理 `negotiationneeded` 事件的代码：

```js
function handleNegotiationNeededEvent() {
  myPeerConnection.createOffer().then(function(offer) {
    return myPeerConnection.setLocalDescription(offer);
  })
  .then(function() {
    sendToServer({
      name: myUsername,
      target: targetUsername,
      type: "video-offer",
      sdp: myPeerConnection.localDescription
    });
  })
  .catch(reportError);
}
```

要开始协商过程，我们需要创建一个 SDP 请求并将其发送给我们想要连接的对等端。此请求包括支持的连接配置列表，包括有关我们在本地添加到连接的媒体流（即，我们希望发送到呼叫另一端的视频）的信息，以及 ICE 层已经收集到的任何 ICE 候选。我们通过调用 `myPeerConnection.createOffer()` 创建此请求。

当 `createOffer()` 成功（执行 promise）时，我们将创建的请求信息传递到 `myPeerConnection.setLocalDescription()` ，它为调用方的连接端配置连接和媒体配置状态。

> 注意：从技术上讲， `createOffer()` 返回的字符串是 [RFC 3264](https://tools.ietf.org/html/rfc3264) 请求。

我们知道描述是有效的，并且在满足 `setLocalDescription()` 返回的 promise 时已经设置好了。也就是说我们创建了一个包含本地描述（现在与请求相同）的新 `"video-offer"` 消息，然后通过我们的信令服务器将请求发送给被叫方。请求有以下要素：

+ type：消息类型: `"video-offer"`.
+ name：调用方的用户名。
+ target：被调用方的用户名
+ sdp：SDP 字符串描述了请求

如果在初始 `createOffer()` 或后面的任何实现处理程序中发生错误，则通过调用 `reportError()` 函数报告错误。

在 `setLocalDescription()` 的实现处理程序运行后，ICE 代理开始向其发现的每个潜在 [`RTCPeerConnection`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection) 配置发送 `icecandidate` 事件。我们的 `icecandidate` 事件处理程序负责将候选对象传输到另一个对等方。

#### 会话协商

既然我们已经开始与另一个对等方进行协商并传输了一个请求，那么让我们来看一下在连接的被呼叫方会发生什么。被调用方接收该请求并调用 `handleVideoOfferMsg()` 函数来处理它。让我们看看被呼叫方如何处理 `"video-offer"` 消息。

**处理请求**

+ 当请求到达时，调用被呼叫方的 `handleVideoOfferMsg()` 函数时会收到 `"video-offer"` 消息。这个函数需要做两件事。首先，它需要创建自己的 [`RTCPeerConnection`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection) 并添加包含麦克风和网络摄像头的音频和视频的磁道。其次，它需要对收到的请求进行处理，构建并返回应答。

  ```js
  function handleVideoOfferMsg(msg) {
    var localStream = null;
  
    targetUsername = msg.name;
    createPeerConnection();
  
    var desc = new RTCSessionDescription(msg.sdp);
  
    myPeerConnection.setRemoteDescription(desc).then(function () {
      return navigator.mediaDevices.getUserMedia(mediaConstraints);
    })
    .then(function(stream) {
      localStream = stream;
      document.getElementById("local_video").srcObject = localStream;
  
      localStream.getTracks().forEach(track => myPeerConnection.addTrack(track, localStream));
    })
    .then(function() {
      return myPeerConnection.createAnswer();
    })
    .then(function(answer) {
      return myPeerConnection.setLocalDescription(answer);
    })
    .then(function() {
      var msg = {
        name: myUsername,
        target: targetUsername,
        type: "video-answer",
        sdp: myPeerConnection.localDescription
      };
  
      sendToServer(msg);
    })
    .catch(handleGetUserMediaError);
  }
  ```

+ 此代码与我们在 [Starting a call](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Signaling_and_video_calling#Starting_a_call) 中在 `invite()` 函数中所做的非常相似。它首先使用 `createPeerConnection()` 函数创建和配置 [`RTCPeerConnection`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection) 。然后，它从收到的 `"video-offer"` 消息中获取 SDP 请求，并使用它创建一个表示调用方会话描述的新 [`RTCSessionDescription`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCSessionDescription) 对象。
+ 然后将该会话描述传递到 [`myPeerConnection.setRemoteDescription()`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection/setRemoteDescription)。这将把接收到的请求建立为连接的远程（调用方）端的描述。如果成功，promise 成功处理程序（在 then() 子句中）将使用 [`getUserMedia()`](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaDevices/getUserMedia)，将磁道添加到连接，以此类推，如前面在 `invite()` 中看到的那样。
+ 一旦使用 `myPeerConnection.createAnswer()` 创建了应答，通过调用 `myPeerConnection.setLocalDescription()` 连接本地端的描述被设置为应答的 SDP，则通过信令服务器将应答发送给调用者，让他们知道应答是什么。
+ 捕捉到的任何错误都会被传递给 `handleGetUserMediaError()`，详见 [Handling getUserMedia() errors](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Signaling_and_video_calling#Handling_getUserMedia()_errors) 。

  > 注意：与调用者的情况一样，一旦 `setLocalDescription()` 实现处理程序运行完毕，浏览器将开始触发被调用者必须处理的 `icecandidate` 事件，每个需要传输到远程对等方的候选事件对应一个事件。

**发送 ICE 候选**

+ ICE 协商过程涉及到每一个对等端不断地向另一个对等端发送候选，直到它用尽了支持 `RTCPeerConnection` 的媒体传输需求的潜在方法。因为 ICE 不知道你的信令服务器，所以你的处理程序代码需要处理 `icecandidate` 事件中每个候选的传输。
+ 你的 [`onicecandidate`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection/onicecandidate) 处理程序接收一个事件，该事件的候选属性是描述该候选的 SDP（或为 `null` ，表示 ICE 层已耗尽建议的潜在配置）。候选的内容是你需要使用信令服务器传输的内容。下面是我们的示例实现：

  ```js
  function handleICECandidateEvent(event) {
    if (event.candidate) {
      sendToServer({
        type: "new-ice-candidate",
        target: targetUsername,
        candidate: event.candidate
      });
    }
  }
  ```

+ 这将构建一个包含候选对象的对象，然后使用前面在 [Sending messages to the signaling server](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Signaling_and_video_calling#Sending_messages_to_the_signaling_server) 中描述的 `sendToServer()` 函数将其发送给另一个对等方。消息属性为：
  + type：消息类型: `"new-ice-candidate"`.
  + target：ICE 候选需要传递到的用户名。这允许信令服务器路由消息。
  + candidate：代表 ICE 层想要传输给另一个对等体的候选体的 SDP。
+ 此消息的格式（与处理信号时所做的所有操作一样）完全取决于你的需要；你可以根据需要提供其他信息。

  > **注意**：重要的是要记住， `icecandidate` 事件**不会**在 ICE 候选从呼叫的另一端到达时发送。相反，它们是由你自己的呼叫端发送的，这样你就可以承担通过你选择的任何通道传输数据的任务。当你刚接触 WebRTC 时，这会让人困惑。

**接收 ICE 候选**

+ 信令服务器使用它选择的任何方法将每个 ICE 候选传递给目标对等机；在我们的示例中，我们用的是 JSON 对象， `type` 属性包含字符串 `"new-ice-candidate"`。我们的 r `handleNewICECandidateMsg()` 函数由主 [WebSocket](https://developer.mozilla.org/zh-CN/docs/MDN/Doc_status/API/WebSockets) 传入消息代码调用，以处理这些消息：

  ```js
  function handleNewICECandidateMsg(msg) {
    var candidate = new RTCIceCandidate(msg.candidate);
  
    myPeerConnection.addIceCandidate(candidate)
      .catch(reportError);
  }
  ```

+ 此函数通过将接收到的 SDP 传递给它的构造函数来构造一个 `RTCIceCandidate` 对象，然后通过 [`myPeerConnection.addIceCandidate()`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection/addIceCandidate) 将候选传递给 ICE 层。这把新建的 ICE 候选交给了当地的 ICE 层，最终，我们在处理整个候选的过程中的角色就完整的了。
+ 每一个对等端向另一个对等端发送一个候选的可能传输配置，它认为这对于正在交换的媒体可能是可行的。在某种程度上，这两端认为，一个给定的候选是一个很好的选择，于是他们打开连接，开始分享媒体数据。然而，重点要注意的是，一旦媒体数据开始流动，ICE 上协商就不会停止。相反，在对话开始后，候选对象可能仍然在不断地进行交换，可能是在试图找到更好的连接方法的同时，也可能只是因为在对等方成功建立连接时，他们已经在传输中了。
+ 此外，如果发生什么事情导致流场景发生变化，协商将再次开始，将事件 `negotiationneeded` 事件发送到 [`RTCPeerConnection`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection)，整个过程将如前所述重新开始。这可能发生在各种情况下，包括：
  + 网络状态的变化，如带宽变化、从 WiFi 过渡到蜂窝连接等。
  + 在手机的前后摄像头之间切换。
  + 流的配置更改，如分辨率或帧速率。

**接收新的流数据**

+ 当新的磁道添加到 `RTCPeerConnection` 时——通过调用其 `addTrack()` 方法，或者由于重新协商流的格式——对于添加到连接的每个磁道， 一个 `track` 事件设置为 `RTCPeerConnection` 。使用新添加的媒体需要实现 `track` 事件的处理程序。常见的需要是将传入的媒体附加到适当的 HTML 元素。在我们的示例中，我们将磁道的流添加到显示传入视频的 video 元素：
+ 传入流附加到 `"received_video"` 元素，并且启用 "Hang Up" button 元素，以便用户挂断呼叫。
+ 完成此代码后，其他对等方发送的视频将显示在本地浏览器窗口中！

**处理流的移除**

+ 当远程对等方通过调用 `RTCPeerConnection.removeTrack()`.从连接中删除磁道时，你的代码将接收事件 `removetrack` 事件。 `"removetrack"` 的处理程序是：

  ```js
  function handleRemoveTrackEvent(event) {
    var stream = document.getElementById("received_video").srcObject;
    var trackList = stream.getTracks();
   
    if (trackList.length == 0) {
      closeVideoCall();
    }
  }
  ```

+ 此代码从 `"received_video"` 元素的 `srcobject` 属性获取传入视频 `getTracks()` 方法获取流的磁道数组。
+ 如果数组的长度为零，意味着流中没有剩余的磁道，则通过调用 `closeVideoCall()` 结束调用。这样就可以将我们的应用程序恢复到可以启动或接收另一个呼叫的状态。请参阅 [Ending the call](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Signaling_and_video_calling#Ending_the_call) 了解 `closeVideoCall()` 的工作原理。

#### 结束通话

通话可能结束的原因有很多。一个通话可能已经结束，当一方或双方都挂断了电话。可能发生了网络故障，或者某个用户退出了浏览器，或者发生了系统崩溃。无论如何，一切美好的事物都必须结束。

**挂机**

+ 当用户单击 "Hang Up" 按钮结束调用时，将调用 `hangUpCall()` 函数：

  ```js
  function hangUpCall() {
    closeVideoCall();
    sendToServer({
      name: myUsername,
      target: targetUsername,
      type: "hang-up"
    });
  }
  ```

+ `hangUpCall()` 执行 `closeVideoCall()` 来关闭并重置连接并释放资源。然后它会生成一个 `"hang-up"` 消息，并将其发送到呼叫的另一端，告诉另一个对等端整齐地关闭自己。

**结束通话**

+ `closeVideoCall()` 函数，如下所示，负责停止流、清理和处理 [`RTCPeerConnection`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection) 对象：

  ```js
  function closeVideoCall() {
    var remoteVideo = document.getElementById("received_video");
    var localVideo = document.getElementById("local_video");
  
    if (myPeerConnection) {
      myPeerConnection.ontrack = null;
      myPeerConnection.onremovetrack = null;
      myPeerConnection.onremovestream = null;
      myPeerConnection.onicecandidate = null;
      myPeerConnection.oniceconnectionstatechange = null;
      myPeerConnection.onsignalingstatechange = null;
      myPeerConnection.onicegatheringstatechange = null;
      myPeerConnection.onnegotiationneeded = null;
  
      if (remoteVideo.srcObject) {
        remoteVideo.srcObject.getTracks().forEach(track => track.stop());
      }
  
      if (localVideo.srcObject) {
        localVideo.srcObject.getTracks().forEach(track => track.stop());
      }
  
      myPeerConnection.close();
      myPeerConnection = null;
    }
    
    remoteVideo.removeAttribute("src");
    remoteVideo.removeAttribute("srcObject");
    localVideo.removeAttribute("src");
    remoteVideo.removeAttribute("srcObject");
  
    document.getElementById("hangup-button").disabled = true;
    targetUsername = null;
  }
  ```

+ 在引用了两个 video 元素之后，我们检查 WebRTC 连接是否仍然存在；如果存在，则继续断开并关闭调用：

  1. 有事件处理程序都将被删除。这可以防止在连接关闭过程中触发杂散事件处理程序，从而可能导致错误。
  2. 对于远程视频流和本地视频流，我们对每个磁道进行迭代，调用 `MediaStreamTrack.stop()` 方法关闭每个磁道。
  3. 通过调用 `myPeerConnection.close()`.关闭 [`RTCPeerConnection`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection) 。
  4. 设置 `myPeerConnection` 为 `null`，确保我们的代码知道没有正在进行的调用；当用户单击用户列表中的名称时，这很有用。

+ 然后，对于传入和传出的 video 元素，我们使用它们的 [`removeAttribute()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/removeAttribute) 方法删除它们的 `srcobject` 和 `src` 属性。这就完成了流与视频元素的分离。
+ 最后，我们在 "Hang Up" 按钮上将 `disabled` 属性设置为 `true`，使其在没有调用的情况下不可点击；然后我们将 `targetUsername` 设置为 `null` ，因为我们不再与任何人交谈。这允许用户呼叫另一个用户，或接收来电。

#### 处理状态变更

还有许多其他事件可以设置监听器，用于通知代码各种状态更改。我们使用三种方法： `iceconnectionstatechange`，`icegatheringstatechange`，和 `signalingstatechange`。

**ICE 连接状态**

+ 事件 `iceconnectionstatechange` 当连接状态更改时（例如，当从另一端终止调用时），由 ICE 层将事件发送到 [`RTCPeerConnection`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection) 。

  ```js
  function handleICEConnectionStateChangeEvent(event) {
    switch(myPeerConnection.iceConnectionState) {
      case "closed":
      case "failed":
      case "disconnected":
        closeVideoCall();
        break;
    }
  }
  ```

+ 这里，当 ICE 连接状态更改为 `"closed"`，`"failed"`，或者 `"disconnected"` 时，我们将应用 `closeVideoCall()` 函数。这将处理关闭我们的连接端，以便我们准备好重新开始或接受呼叫。

**ICE 信令状态**

+ 同样，我们监听 `signalingstatechange` 事件。如果信号状态变为 `closed`，我们同样关闭呼叫。

  ```js
    myPeerConnection.onsignalingstatechange = function(event) {
      switch(myPeerConnection.signalingState) {
        case "closed":
          closeVideoCall();
          break;
      }
    };
  ```

  > **注意:** `closed` 的信令状态已被弃用，取而代之的是 `closed`[`iceConnectionState`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCPeerConnection/iceConnectionState)。我们在这里监听它以增加一点向后兼容性。

**ICE 收集状态**

+ `icegatheringstatechange` 事件用于让你知道何时 ICE 候选收集进程状态发生更改。我们的示例并没有将其用于任何用途，但是为了调试的目的观察这些事件以及检测候选集合何时完成都是有用的。

  ```js
  function handleICEGatheringStateChangeEvent(event) {
    // Our sample just logs information to console here,
    // but you can do whatever you need.
  }
  ```

# Web RTC API

## MediaStream 对象

getUserMedia 的 promise 返回值

id [String]: 对当前的 MS 进行唯一标识。所以每次刷新浏览器或是重新获取 MS，id 都会变动。

active [boolean]: 表示当前 MS 是否是活跃状态（就是是否可以播放）。

onactive: 当 active 为 true 时，触发该事件。

getAudioTracks()、getVideoTracks() 来查看获取到的流的某些信息

+ kind: 是当前获取的媒体流类型（Audio/Video）。

* label: 是媒体设备，我这里用的是虚拟摄像头。
* muted: 表示媒体轨道是否静音。

## RTCPeerConnection 构造函数

### 概述

RTCPeerConnection 作为创建点对点连接的 API，是我们实现音视频实时通信的关键。

全部 API 都支持 promise 语法

### 创建实例

```js
let PeerConnection = 
    window.RTCPeerConnection ||
    window.mozRTCPeerConnection ||
    window.webkitRTCPeerConnection
let peer = new PeerConnection(iceServers)
```

我们看见 RTCPeerConnection 也同样接收一个参数 — iceServers，先来看看它长什么样：

```js
{
  iceServers: [
    { url: "stun:stun.l.google.com:19302"}, // 谷歌的公共服务
    {
      url: "turn:***",
      username: ***, // 用户名
      credential: *** // 密码
    }
  ]
}
```

参数配置了两个 url，分别是 STUN 和 TURN，这便是 WebRTC 实现点对点通信的关键，也是一般 P2P 连接都需要解决的问题：NAT 穿越。

### createOffer()

生成一个 offer，它是一个带有特定的配置信息寻找远端匹配机器（peer）的请求。

**参数**

+ 这个方法的前两个参数分别是方法调用成功以及失败的回调函数，可选的第三个参数是用户对视频流以及音频流的定制选项（一个对象）。

### addStream(mediaSream)

### removeStream(mediaStream)

将一个作为本地音频或视频源的媒体流 [`MediaStream`](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream) 移除。如果本地端与远端协调已经发生了，那么需要一个新的媒体流，这样远端才可以停止使用它。

### close()

关闭一个 RTCPeerConnection 实例所调用的方法。

### iceConnectionState

我们可以通过 `oniceconnectionstatechange` 方法来监测 ICE 连接的状态

```js
peer.oniceconnectionstatechange = (evt) => {
    console.log('ICE connection state change: ' + evt.target.iceConnectionState);
}
```

它一共有七种状态：

+ new：ICE 代理正在收集候选人或等待提供远程候选人。
+ checking：ICE 代理已经在至少一个组件上接收了远程候选者，并且正在检查候选但尚未找到连接。除了检查，它可能还在收集。
+ connected：ICE 代理已找到所有组件的可用连接，但仍在检查其他候选对以查看是否存在更好的连接。它可能还在收集。
+ completed：ICE 代理已完成收集和检查，并找到所有组件的连接。
+ failed：ICE 代理已完成检查所有候选对，但未能找到至少一个组件的连接。可能已找到某些组件的连接。
+ disconnected：ICE 连接断开
+ closed：ICE 代理已关闭，不再响应 STUN 请求。

我们需要注意的是 completed 和 disconnected，一个是完成连接时触发，一个在断开连接时触发。

# 更新中

https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Connectivity

# 文章

+ https://juejin.im/post/5b8fa33ae51d450e950fa746
+ https://juejin.im/post/5b3038ed6fb9a00e9d1b60cf#heading-0
+ https://juejin.im/post/5e0ae59d5188253a5b3ccf74
