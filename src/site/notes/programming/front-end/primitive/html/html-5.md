---
{"aliases":[],"tags":[],"review-dates":["2022-02-23","2023-04-26"],"dg-publish":true,"date-created":"2022-09-03-Sat, 10:07:28 pm","date-modified":"2023-08-04-Fri, 5:24:09 pm","permalink":"/programming/front-end/primitive/html/html-5/","dgPassFrontmatter":true}
---


# 新增 API

## classList

方便对 class 做 CRUD

- textNode.classList.add("className")
- textNode.classList.remove("className")
- textNode.classList.toggle("className")

兼容性还行

## HTMLElement.dataset

textNode.setAttibute.qhf

自定义的 attribute 被定义在了 NODE 的 attributes 属性中，该属性是一个数组，里面按顺序保存着 DOM 的全部属性节点，获取自定义属性的属性值的方法：

```js
NODE.getAttribute('key')
NODE.attributes[index].value
```

> dataset 属性，如果 html 标签内使用驼峰，则 js 内无法用驼峰获取，需要在 html 标签内使用下划线或者短横杠

驼峰的转换

读写均可

```html
<div id="user" data-id="1234567890" data-user="johndoe" data-date-of-birth>John Doe
</div>

<script>
  var el = document.querySelector('#user');

  // el.id == 'user'
  // el.dataset.id === '1234567890'
  // el.dataset.user === 'johndoe'
  // el.dataset.dateOfBirth === ''

  el.dataset.dateOfBirth = '1960-10-03'; // set the DOB.

  // 'someDataAttr' in el.dataset === false

  el.dataset.someDataAttr = 'mydata';
  // 'someDataAttr' in el.dataset === true
</script>
```

## Contenteditable

```html
<div id="test" contenteditable="true">
  djhaldhaskj
</div>
```

设置该属性之后，可以直接在页面，编辑 div 的标签体内容

该属性必须是下面的值之一:

- `true` 或空字符串，表示元素是可编辑的；
- `false` 表示元素不是可编辑的。

如果没有设置该属性，其默认值继承自父元素。

该属性是一个 **枚举属性（enumerated one）**，而不是 **布尔属性（Boolean one）**。这意味着必须显式设置其值为 `true`、`false` 或空字符串中的一个，并且不允许简写为 `<label contenteditable>Example Label</label> `（注: 这在大部分浏览器中是有效的）

正确的用法是 `<label contenteditable="true">Example Label</label>`。

## querySelector()

借鉴 jQuery

是一个快照属性，有坑，只能保持 DOM 元素的快照

# 音频&视频

大家都有在网页中浏览视频的经历，但在 Html5 之前，对视频乃至音频都还没有一个标准

因此在网页中看到的视频，都是通过第三插件的方式嵌入的，可能是 QuickTime，也可能是 RealPlayer 或者 Flash。浏览器很好的整合了这些插件，你完全意识不到它们的存在

- 现在浏览器不在整合插件了，经过用户同意之后才能使用插件

在介绍 Html5 中的音视频标签前，我们需要了解一些概念

## 简单测试

  ```html
  <video src="resource/video/test.mp4" controls="controls" width="500" height="500" ></video>
  <audio src="resource/audio/test.mp3" controls></audio>
  ```

视频的默认宽高，由分辨率决定，由内容撑开，controls 属性之后才能显示控件，视频可以正常播放

设定宽高属性，只能控制控件的宽高，无法直接控制视频的大小，视频会按照分辨率等比例压缩

音频标签，指定宽高属性，没有意义，因为没有内容撑开

## 浏览器间的差异

chrome 本地音视频的进度条都不能拖拽，服务器上的可以拖拽，火狐的都可以拖拽

chrome 有下载按钮，火狐没有下载按钮

浏览器间控件的样式不一样

## 基础概念

### 容器

大多数人会认为视频文件就是 .avi .mp4,但事实上 avi 和 mp4 仅仅是容器的格式，它只决定怎么将视频存储起来，而不关系存储的内容。有点类似于.zip

不管是音频文件或视频文件，实际上都只是一个容器文件。这点类似于压缩了一组文件的 ZIP 文件

视频文件（视频容器）包含了音频轨道、视频轨道和其他一些元数据。

视频播放的时候，**音频轨道** 和 **视频轨道** 是绑定在一起的。

**元数据** 包含了视频的封面、标题、子标题、字幕等相关信息。

  <table width="100%">
    <thead>
    	<tr>
      	<td rowspan=2>主流的视频文件格式 (容器格式)</td>
        <td></td>
        <td rowspan=2>主流的音频文件格式</td>
        <td></td>
      </tr>
    </thead>
    <tr>
    	<td>MPEG-4:</td>
      <td>通常以.mp4 为扩展名</td>
      <td>MPEG-3</td>
      <td>通常以.mp3 为扩展名</td>
    </tr>
    <tr>
    	<td>Flash 视频:</td>
      <td>通常以.flv 为扩展名</td>
      <td>Acc 音频</td>
      <td>通常以.acc 为扩展名</td>
    </tr>
      <tr>
    	<td>Ogg::</td>
      <td>通常以.ogv 为扩展名</td>
      <td>Ogg 音频</td>
      <td>通常以.ogg 为扩展名</td>
    </tr>
      <tr>
    	<td>WebM:</td>
      <td>通常以.webm 为扩展名</td>
      <td>音频视频交错</td>
      <td>通常以.avi 为扩展名</td>
    </tr>
  </table>

### 编解码器

音频和视频编码/解码是一组算法，用来对一段特定音频或视频进行解码和编码，以便音频和视频能够播放。原始的媒体文件体积非常巨大，如果不对其进行编码，那么数据量是非常惊人的，在互联网上传播则要耗费无法忍受的时间；如果不对其进行解码，就无法将编码后的数据重组为原始的媒体数据

| 视频编解码器 | 音频编解码器 |
| ------------ | ------------ |
| H.264        | ACC          |
| VP8          | MPEG-3       |
| Ogg Theora   | Ogg Vorbis   |

H.264: 别名 MPEG-4 的第十部分,由 MPEG 研发并于 2003 年标准化它的目的支持一切设备，无论是低带宽低 cpu，还是高带宽高 cpu 或者是两者之间。要做到这一点，H.264 标准被分成不同的几种配置。高配置使用了更多特性，这会导致在解码过程中更加消耗 CPU，但视频文件本身会更小，视频效果也更好

| 苹果 iphone 手机   | H.264 基本配置 (BaseLine)                                    |
| ---------------- | ---------------------------------------------------------- |
| 正常的电视机支持 | H.264 基本配置 (BaseLine) 和 主配置 (Main) 两种                |
| 正常的电脑支持   | H.264 基本配置 (BaseLine) 和 主配置 (Main) 高级配置 (high) 三种 |

当然有一些编解码器受专利的保护，有一些则是免费的，例如 Ogg 的 Vorbis 音频编解码器。

Ogg 的 Theora 视频编解码器也是可以免费使用的。而想使用 H.264 的话就需要支付相关的费用了

现在的视频编解码器会使用各种技巧减少从一帧到另一帧过程中传递的信息数量，它们不会存储每一帧的所有信息，而只是存储两帧之间的差异信息。

编码器也分有损和无损，无损视频文件一般太大，在网页中没有优势，所以我们重点研究有损编解码器。

有损编解码器中，信息在编码过程中丢失是无法避免的，反复的对视频编码会导致其画面不均匀。

### 浏览器对于容器和编解码器支持的情况

浏览器只能选择一款编解码器，选择的不同造成了差异

| Browser             | MP4(H.264 + AAC) | WebM(VP8 + Vorbis ) | Ogg(Theora + Vorbis) |
| ------------------- | ---------------- | ------------------- | -------------------- |
| Internet Explorer 9 | YES              | NO                  | NO                   |
| Firefox 4.0         | NO               | YES                 | YES                  |
| Google Chrome       | YES              | YES                 | YES                  |
| Apple Safari 5      | YES              | NO                  | NO                   |
| Opera 10.6          | NO               | YES                 | YES                  |
| ios                 | YES              |                     | YES                  |
| android             |                  |                     | YES                  |

  > http://www.html5videoplayer.net/html5video/html5-video-browser-compatibility/

目前还没有一种编解码和容器的组合能应用于所有的浏览器中！！！

### 处理视频的一个流程

  1. 制作一个 Ogg 容器中使用 Theora 视频和 Vorbis 音频的版本
  2. 制作另外一个版本，使用 WebM 视频容器（VP8 + Vorbis）
  3. 再制作一个版本，使用 MP4 视频容器，并使用 H.264 基本配置的视频和 ACC 低配的音频
  4. 链接上面 3 个文件到同一个 video 元素，并向后兼容基于 Flash 的视频播放器

### 格式转化

用 FFmpeg 制作 MP4 视频

- ffmpeg -i test.mp4 -c:v libx264 -s 1280x720 -b:v 1500k -profile:v high -level 3.1 -c:a aac -ac 2 -b:a 160k -movflags faststart OUTPUT.mp4

用 FFmpeg 制作 WebM 视频

- ffmpeg -i test.mp4 -c:v libvpx -s 1280x720 -b:v 1500k -c:a libvorbis -ac 2 -b:a 160k OUTPUT.webm

FFmpeg 制作 Ogg 视频

- ffmpeg -i test.mp4 -c:v libtheora -s 1280x720 -b:v 1500k -c:a libvorbis -ac 2 -b:a 160k OUTPUT.ogv

FFmpeg 制作 Mp3 音频

- ffmpeg -i test.mp3 -c:a libmp3lame -ac 2 -b:a 160k OUTPUT.mp3

FFmpeg 制作 Ogg 音频

- ffmpeg -i test.mp3 -c:a libvorbis -ac 2 -b:a 160k OUTPUT.ogg

FFmpeg 制作 ACC 音频

- ffmpeg -i test.mp3 -c:a aac -ac 2 -b:a 160k OUTPUT.aac

## H5 的音视频设计

### Html5 标签

  ```html
  <video>:Html5提供的播放视频的标签
  src:资源地址
  controls:该属性定义是显示还是隐藏用户控制界面
  
  <audio>:Html5提供的播放音频的标签
  src:资源地址
  controls:该属性定义是显示还是隐藏用户控制界面
  
  <source>
  视频编解码属性:
  type='video/webm; codecs="vp8, vorbis"'
  type='video/ogg; codecs="theora, vorbis"'
  type='video/mp4; codecs="avc1.42E01E, mp4a.40.2"'
  
  音频编解码属性:
  type='audio/ogg; codecs="vorbis"'
  type='audio/aac; codecs="aac"'
  type='audio/mpeg'
  ```

  ```html
  <video controls width="300" height="300">
  <source src="resource/video/OUTPUT.mp4" type="video/mp4"></source>
  <source src="resource/video/OUTPUT.ogv" type="video/ogg"></source>
  <source src="resource/video/OUTPUT.webm" type="video/webm"></source>
  当前浏览器不支持 video直接播放，点击这里下载视频： <a href="resource/video/OUTPUT.mp4">下载视频</a>
  </video>
  
  <audio controls >
  <source src="resource/audio/OUTPUT.mp3" type='audio/mpeg'></source>
  <source src="resource/audio/OUTPUT.aac" type='audio/aac; codecs="aac"'></source>
  <source src="resource/audio/OUTPUT.ogg" type='audio/ogg; codecs="vorbis"'></source>
  当前浏览器不支持 video直接播放，点击这里下载视频： <a href="resource/audio/OUTPUT.mp3">下载视频</a>
  </audio>
  ```

### Video 标签的属性

width : 视频显示区域的宽度，单位是 CSS 像素

height: 视频展示区域的高度，单位是 CSS 像素

poster : 一个海报帧的 URL，用于在用户播放或者跳帧之前展示

src: 要嵌到页面的视频的 URL

controls: 显示或隐藏用户控制界面

autoplay: 媒体是否自动播放

loop: 媒体是否循环播放

muted: 是否静音

preload: 该属性旨在告诉浏览器作者认为达到最佳的用户体验的方式是什么

- none: 提示作者认为用户不需要查看该视频，服务器也想要最小化访问流量；换句话说就是提示浏览器该视频不需要缓存。
- metadata: 提示尽管作者认为用户不需要查看该视频，不过抓取元数据（比如: 长度）还是很合理的。
- auto: 用户需要这个视频优先加载；换句话说就是提示: 如果需要的话，可以下载整个视频，即使用户并不一定会用它。
- 空字符串: 也就代指 auto 值。

### Audio 标签的属性

  - src
  - controls
  - autoplay
  - loop
  - muted
  - preload

### Error 属性

https://developer.mozilla.org/en-US/docs/Web/API/MediaError

poster error 和 source error 都会触发 error 事件

可以通过该属性区分是 poster 还是 srouce

poster error 时 该属性为 null

### 音视频 Js 相关属性

duration: 媒体总时间 (只读)

  ```js
  var video = document.querySelector("video");
  var audio = document.querySelector("audio");
  
  console.log(video.duration); //Chrome:NaN，火狐做了优化，优先获取了信息
  console.log(audio.duration); //Chrome:NaN
  
  setTimeout(function(){
    console.log(video.duration); //Chrome:可以获取到
    console.log(audio.duration);
  },500)
  
  video.addEventListener("suspend",function(){
    console.log(video.duration); //几百个NaN，最后返回数据，说明调用了几百次
  })
  
  video.addEventListener("loadeddata",function(){
    console.log(video.duration); //直接返回数据，调用了一次
  })
  ```

**currentTime** : 开始播放到现在所用的时间 (可读写)

- 不用加载完毕，直接显示 0
- 通过该属性实现跳跃播放
- 以秒为单位，超过 duration 则无效

muted: 是否静音 (可读写,相比于 volume 优先级要高)

- 读写一个布尔值

volume:0.0-1.0 的音量相对值 (可读写)

  ```JS
  //muted和volume之间不会同步
  //muted的优先级比较高
  video.muted=true;
  audio.muted=true;
  video.volume=0; //如果不设置的话，volume返回1，也就是没有静音的值
  audio.volume=0;
  console.log(video.muted)
  console.log(audio.muted)
  console.log(video.volume)
  console.log(audio.volume)
  ```

paused: 媒体是否暂停**(只读)**

- 注意属性时 pause**d**，而方法是 pause()，因为方法一直存在，如果写错了永远只能进入一个判断

ended: 媒体是否播放完毕**(只读)**

error: 媒体发生错误的时候，返回错误代码 **(只读)**

currentSrc: 以字符串的形式返回媒体地址**(只读)**

视频多的部分:

- poster: 视频播放前的预览图片的 **地址 (读写)**
- width、height : 设置视频的尺寸**(读写)**，与 attribute 对应
- videoWidth、 videoHeight: 视频的实际尺寸 (只读)

### 音视频 Js 相关方法

play(): 媒体播放

pause(): 媒体暂停

  ```js
  video.pause();
  audio.pause();
  ```

> 注意属性是 pause**d**，而方法是 pause()，因为方法一直存在，如果写错了永远只能进入一个判断

load(): 重新加载媒体

  ```js
  var video = document.querySelector("video");
  var audio = document.querySelector("audio");
  
  var source = document.querySelectorAll("source");
  /*video.src="xxx";
  audio.src="xxx";*/
  source[0].src="xxx";
  source[1].src="xxx";
  //修改src值，需要load一遍，才能生效
  video.load();
  audio.load();
  ```

### Js 视频相关事件

abort: 在播放被终止时触发,例如, 当播放中的视频重新开始播放时会触发这个事件。

canplay: 在媒体数据已经有足够的数据（至少播放数帧）可供播放时触发。这个事件对应 CAN_PLAY 的 readyState。

canplaythrough: 在媒体的 readyState 变为 CAN_PLAY_THROUGH 时触发，表明媒体可以在保持当前的下载速度的情况下不被中断地播放完毕。注意: 手动设置 currentTime 会使得 firefox 触发一次 canplaythrough 事件，其他浏览器或许不会如此。

durationchange: 元信息已载入或已改变，表明媒体的长度发生了改变。例如，在媒体已被加载足够的长度从而得知总长度时会触发这个事件。

emptied: 媒体被清空（初始化）时触发。

ended: 播放结束时触发。

error: 在发生错误时触发。元素的 error 属性会包含更多信息。参阅 Error handling 获得详细信息。

loadeddata: 媒体的第一帧已经加载完毕。

loadedmetadata: 媒体的元数据已经加载完毕，现在所有的属性包含了它们应有的有效信息。

 loadstart: 在媒体开始加载时触发。

mozaudioavailable: 当音频数据缓存并交给音频层处理时

pause: 播放暂停时触发。

play: 在媒体回放被暂停后再次开始时触发。即，在一次暂停事件后恢复媒体回放。

playing: 在媒体开始播放时触发（不论是初次播放、在暂停后恢复、或是在结束后重新开始）。

progress: 告知媒体相关部分的下载进度时周期性地触发。有关媒体当前已下载总计的信息可以在元素的 buffered 属性中获取到。

ratechange: 在回放速率变化时触发。

seeked: 在跳跃操作完成时触发。

seeking: 在跳跃操作开始时触发。

stalled: 在尝试获取媒体数据，但数据不可用时触发。

suspend: 在媒体资源加载终止时触发，这可能是因为下载已完成或因为其他原因暂停。

timeupdate: 元素的 currentTime 属性表示的时间已经改变。

volumechange: 在音频音量改变时触发（既可以是 volume 属性改变，也可以是 muted 属性改变）.。

waiting: 在一个待执行的操作（如回放）因等待另一个操作（如跳跃或下载）被延迟时触发

### Js 音频相关事件

abort: 在播放被终止时触发,例如, 当播放中的视频重新开始播放时会触发这个事件。

canplay: 在媒体数据已经有足够的数据（至少播放数帧）可供播放时触发。这个事件对应 CAN_PLAY 的 readyState。

canplaythrough: 在媒体的 readyState 变为 CAN_PLAY_THROUGH 时触发，表明媒体可以在保持当前的下载速度的情况下不被中断地播放完毕。注意: 手动设置 currentTime 会使得 firefox 触发一次 canplaythrough 事件，其他浏览器或许不会如此。

durationchange 元信息已载入或已改变，表明媒体的长度发生了改变。例如，在媒体已被加载足够的长度从而得知总长度时会触发这个事件。

emptied: 媒体被清空（初始化）时触发。

ended: 播放结束时触发。

error: 在发生错误时触发。元素的 error 属性会包含更多信息。参阅 Error handling 获得详细信息。

loadeddata: 媒体的第一帧已经加载完毕。

loadedmetadata: 媒体的元数据已经加载完毕，现在所有的属性包含了它们应有的有效信息。

loadstart: 在媒体开始加载时触发。

mozaudioavailable: 当音频数据缓存并交给音频层处理时

pause: 播放暂停时触发。

play: 在媒体回放被暂停后再次开始时触发。即，在一次暂停事件后恢复媒体回放。

playing: 在媒体开始播放时触发（不论是初次播放、在暂停后恢复、或是在结束后重新开始）。

progress : 告知媒体相关部分的下载进度时周期性地触发。有关媒体当前已下载总计的信息可以在元素的 buffered 属性中获取到。

ratechange : 在回放速率变化时触发。

seeked : 在跳跃操作完成时触发。

seeking: 在跳跃操作开始时触发。

stalled: 在尝试获取媒体数据，但数据不可用时触发。

suspend: 在媒体资源加载终止时触发，这可能是因为下载已完成或因为其他原因暂停。

timeupdate: 元素的 currentTime 属性表示的时间已经改变。

volumechange: 在音频音量改变时触发（既可以是 volume 属性改变，也可以是 muted 属性改变）.。

waiting: 在一个待执行的操作（如回放）因等待另一个操作（如跳跃或下载）被延迟时触发

### 音视频兼容性写法

  ```html
  <video controls width="300" height="300">
  	<source src="resource/video/OUTPUT.mp4" type="video/mp4"></source>
  	<source src="resource/video/OUTPUT.ogv" type="video/ogg"></source>
  	<source src="resource/video/OUTPUT.webm" type="video/webm"></source>
  当前浏览器不支持 video直接播放，点击这里下载视频: <a href="resource/video/OUTPUT.mp4">下载视频</a>
  </video>
  
  <audio controls >
  	<source src="resource/audio/OUTPUT.mp3" type='audio/mpeg'></source>
  	<source src="resource/audio/OUTPUT.aac" type='audio/aac; codecs="aac"'></source>
  	<source src="resource/audio/OUTPUT.ogg" type='audio/ogg; codecs="vorbis"'></source>
  当前浏览器不支持 video直接播放，点击这里下载视频: <a href="resource/audio/OUTPUT.mp3">下载视频</a>
  </audio>
  ```

浏览器会查询 souce 的 type 值，如果是支持的格式，则会访问 src 的路径，如果是不支持的就溜了

### Demo 音阶导航

习惯于 js 有关的属性，都设置为 data

这个对应关系有点复杂了，在 html 上添加了 js 需要的属性，其实可以定义在 for 循环内部，给一个属性标识一下，然后 map 成英文字母的 ++

  ```js
  <script type="text/javascript">
    //http://s8.qhimg.com/share/audio/piano1/g4.mp3
    var liNodes = document.querySelectorAll(".nav>li"); 
  	var audio = document.querySelector("audio"); 
  	for(var i=0;i<liNodes.length;i++){
    liNodes[i].addEventListener("mouseenter",function(){
      //var flag = this.getAttribute("data-flag");
      var flag = this.dataset.flag==undefined? this.dataset.flag: this.getAttribute("data-flag");
      if(flag){
        audio.src="http://s8.qhimg.com/share/audio/piano1/"+flag+"4.mp3";
        audio.play();
      }
    })
  }
  ```

### Demo Player

### 视频与 Canvas 结合

  ```js
  var oc = document.querySelector("#oc");
  var video = document.querySelector("video");
  if(oc.getContext){
    var ctx = oc.getContext("2d");
  
    video.addEventListener("loadeddata",function(){
      setInterval(function(){
        ctx.drawImage(video,0,0,oc.width,oc.height)
      })
    })
  }
  ```

### 截图

通过 canvas 获取像素转描截图

### 最新

声音无法自动播放这个在 IOS/Android 上面一直是个惯例，桌面版的 Safari 在 2017 年的 11 版本也宣布禁掉带有声音的多媒体自动播放功能，紧接着在 2018 年 4 月份发布的 Chrome 66 也正式关掉了声音自动播放，也就是说在桌面版浏览器也将失效。
