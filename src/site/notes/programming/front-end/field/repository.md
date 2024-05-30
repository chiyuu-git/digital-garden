---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2021-10-31-Sun, 1:25:43 pm","date-modified":"2023-04-29-Sat, 8:15:06 pm","permalink":"/programming/front-end/field/repository/","dgPassFrontmatter":true}
---


# html2image

参考：[高质量前端快照方案：来自页面的「自拍」](https://segmentfault.com/a/1190000021275782)

## 基础

### 背景

将网页保存为图片（以下简称为快照），是用户记录和分享页面信息的有效手段，在各种兴趣测试和营销推广等形式的活动页面中尤为常见。

快照环节通常处于页面交互流程的末端，汇总了用户最终的参与结果，直接影响到用户对于活动的完整体验。因此，生成高质量的页面快照，对于活动的传播和品牌的转化具有十分重要的意义。

本文基于云音乐往期优质活动的相关实践（例如「[关于你的画](https://link.segmentfault.com/?enc=%2FsBFedV7KWCQacK92%2BhmDA%3D%3D.9ILoFFKfqixqHL7ozVT37ZbeizD%2FUBcvV6vzOi%2BwR2qlsAvjgL1Myvj9Weyhpt7U1YMsYXblP3hd%2BCKNnRWADA%3D%3D)」、「[权力的游戏](https://link.segmentfault.com/?enc=EnF%2BadT4vk6NShPgBvnx6A%3D%3D.m1aix%2BDM4S8xOExxuyfgFAfRbhEtEnxQuBI7F9e%2BEj6NQauo5jjBSCsP8ycLUaNS)」和「[你的使用说明书](https://link.segmentfault.com/?enc=Vfcl%2F%2FIYFkDoSjgbbkqgjQ%3D%3D.3w%2Bp55PIyU2%2F80n6GUemLRMQ541L6J04UvZ9Bz9H8vSK7fMgu2Nan8%2BAIfDWMOzR)」等)，从快照的**内容完整性**、**清晰度**和**转换效率**等多个方面，讨论将网页转换为高质量图片的实践探索。

### 适用场景

- 适用于将页面转为图片，特别是对实时性要求较高的场景。
- 希望在快照中展示跨域图片资源的场景。
- 针对生成图片内容不完整、模糊或者转换过程缓慢等问题，寻求有效解决方案的场景。

### 原理简析

依据图片是否由设备**本地生成**，快照可分为前端处理和后端处理两种方式。

由于后端生成的方案依赖于网络通信，不可避免地存在通信开销和等待时延，同时对于模板和数据结构变更也有一定的维护成本。

因此，出于**实时性**和**灵活性**等综合考虑，我们优先选用前端处理的方式。

前端侧对于快照的处理过程，实质上是将 DOM 节点包含的视图信息转换为图片信息的过程。这个过程可以借助 canvas 的原生 API 实现，这也是方案可行性的基础。

![theory](https://segmentfault.com/img/remote/1460000021275785)

具体来说，转换过程是将目标 DOM 节点绘制到 canvas 画布，然后 canvas 画布以图片形式导出。可简单标记为绘制阶段和导出阶段两个步骤：

- **绘制阶段**：选择希望绘制的 DOM 节点，根据 `nodeType` 调用 canvas 对象的对应 API，将目标 DOM 节点绘制到 canvas 画布（例如对于 `<img>` 的绘制使用 [drawImage](https://link.segmentfault.com/?enc=7qztPEeiaJKYi24NV1PWUQ%3D%3D.%2B7FoqlVrBe39FS61y9IHCjBLjIW8WqI9qPPOD58OcavXqvgpIskO2yZhCWJV11%2B5kGSzo9QyNPjvsatS%2BbPsMcR%2BG1wwy5S18R2OgiSpVjlG69R59iV0MiZfLa9sCYHP) 方法)。
- **导出阶段**：通过 canvas 的 [toDataURL](https://link.segmentfault.com/?enc=XgFlaOGocdhlaJWufHVmPw%3D%3D.HMhn4lonICne2MiiAB2Ip%2BFZaL3YdNFJVjZU2ScBuTo7WjTT0aFzEHcWddIRV7veS3Jb3sZWecnk%2B21BDFpz4QwR6KLBdkfoaItkuY4tKiY%3D) 或 [getImageData](https://link.segmentfault.com/?enc=mI0pEyyu7eJgyy4dGQpGrA%3D%3D.KIR1E246JrM4ILAJsuKIONiF2h5Hna%2Bdkp%2Bjv5YB79DH43mnarYlHFO4LpYjohIEYaU8JWU411yn2v9YZctrWA%3D%3D) 等对外接口，最终实现画布内容的导出。

### 原生示例

具体地，对于单个 `<img>` 元素可按如下方式生成自身的快照：

```html
<img id="target" src="./music-icon.png" />
```

```js
// 获取目标元素
const target = document.getElementById('target');

// 新建canvas画布
const canvas = document.createElement('canvas');
canvas.width = 100;
canvas.height = 100;
const ctx = canvas.getContext("2d");

// 导出阶段：从canvas导出新的图片
const exportNewImage = (canvas) => {
    const exportImage = document.createElement('img');
    exportImage.src = canvas.toDataURL();
    document.body.appendChild(exportImage);
}

// 绘制阶段：待图片内容加载完毕后绘制画布
target.onload = () => {
    // 将图片内容绘入画布
    ctx.drawImage(target, 0, 0, 100, 100);

    // 将画布内容导出为新的图片
    exportNewImage(canvas);
}
```

其中，`drawImage` 是 canvas 上下文对象的实例方法，提供 [多种方式](https://link.segmentfault.com/?enc=282bzPZRN%2FgsYHegzcqUuQ%3D%3D.L8BZiwAeQAOwIds1mLSlxCv2X4bDE5tj2HPKe2VRFGR2pOUxbG6MtpVJoG7eIK5gKIGTioGSmuQD17ooMRyV3%2FFK7RXmWwO1nHpKIdlnhWsF%2B%2BRDcFTr%2BCkpDB5WmEl4) 将 [CanvasImageSource](https://link.segmentfault.com/?enc=YKq6PXewF3vjLKIhD24tzw%3D%3D.AvODb61e7HL6%2BSY9Wl%2FutQ%2FzB1TtNTgrlj%2FvOt8AbuJq5QZ9VxefXBq8CYUS7dklihSgG6yU9ZFkF7q4RrWYcw7IDkg0QU87fCf%2BHJllwxM%3D) 源绘制到 canvas 画布上。`exportNewImage` 用于将 canvas 中的视图信息导出为包含图片展示的 [data URI](https://link.segmentfault.com/?enc=LhtYcQjhHu3GA54tb7UW8A%3D%3D.OfgxpPoB%2BzHNypgMxvrwtzLcL%2Bftm0NffPAIcnBPHFazYKHcBzmfP44a3%2BjqmLzgHlzfptoDSQ0zC7PsTHo1qntcBuJrP7%2BQPbfNpS%2Brx7M%3D)。

## 基础方案

在上一部分中，我们可以看到基于 canvas 提供的相关基础 API，为前端侧的页面快照处理提供了可能。

然而，具体的业务应用往往更加复杂，上面的「低配版」实例显然未能覆盖多数的实际场景，例如：

- canvas 的 `drawImage` 方法只接受 [CanvasImageSource](https://link.segmentfault.com/?enc=DuW9R%2BwNa6YEf5XtJAz3pA%3D%3D.dpidWwzfbzsIiTH15C8IPZU%2BY7YIgMIQUPJ9RXYovhyfqXCkP0Rzuv0Mk%2F4GzDBJZVvmb8Z%2BtvStIl37Nvgo6JizMtCW9crIPn9w2OKS%2FyQ%3D)，而 `CanvasImageSource` 并不包括 [文本节点](https://link.segmentfault.com/?enc=hTqKvWCQvJgKaosN3B5EEQ%3D%3D.lT39Gz7VKZyk6Jf%2FnVYeeRoYQsIc595vY%2Bo4l7f4R2r%2Bq1GKIqxi5UKeLmpnhk48vaVSgyaHVQknx3yqhvxYzA%3D%3D)、普通的 `div` 等，将非 `<img>` 的元素绘制到 canvas 需要特定处理。
- 当有多个 DOM 元素需要绘制时，层级优先级处理较为复杂。
- 需要关注 `float`、`z-index`、`position` 等布局定位的处理。
- 样式合成绘制计算较为繁琐。

因此，基于对综合业务场景的考虑，我们采用社区中认可度较高的方案：`html2canvas` 和 `canvas2image` 作为实现快照功能的基础库。

### html2canvas

提供将 DOM 绘制到 canvas 的能力

这款来自社区的神器，为开发者简化了将逐个 DOM 绘制到 canvas 的过程。简单来说，其**基本原理**为：

- 递归遍历目标节点及其子节点，收集节点的样式信息；
- 计算节点本身的层级关系，根据一定优先级策略将节点逐一绘制到 canvas 画布中；
- 重复这一过程，最终实现目标节点内容的全部绘制。

在使用方面，`html2canvas` 对外暴露了一个可执行函数“

1. 它的第一个参数用于接收待绘制的目标节点 (必选)
2. 第二个参数是可选的 [配置项](https://link.segmentfault.com/?enc=ofLkpCnY%2B5keApZLThrJQg%3D%3D.0DWrse2kZb%2F37onPE1ZJzDt5aqUNNoPp%2B9dqx2jtaXWayRfNGcxanzrG8b3Z8vBq)，用于设置涉及 canvas 导出的各个参数：

```js
// element 为目标绘制节点，options为可选参数
html2canvas(element[,options]);  
```

简易调用示例如下：

```ts
import html2canvas from 'html2canvas';

const options = {};

// 输入body节点，返回包含body视图内容的canvas对象
html2canvas(document.body, options).then(function(canvas) {
    document.body.appendChild(canvas);
});
```

### canvas2image

提供由 canvas 导出图片信息的多种方法

相比于 `html2canvas` 承担的复杂绘制流程，[canvas2image](https://link.segmentfault.com/?enc=BWDbAT%2B6h9xqnI4nczRu6w%3D%3D.4KdeKvHd7XJ4PWdExSsNX2RiSjALgGfd3GUupwQqJwrK2ck%2B%2FJBFqpUUQNIuXbOX) 所要做的事情简单的多。

`canvas2image` 仅用于将输入的 canvas 对象按特定格式转换和存储操作，其中这两类操作均支持 PNG，JPEG，GIF，BMP 四种图片类型：

```ts
// 格式转换
Canvas2Image.convertToPNG(canvasObj, width, height);
Canvas2Image.convertToJPEG(canvasObj, width, height);
Canvas2Image.convertToGIF(canvasObj, width, height);
Canvas2Image.convertToBMP(canvasObj, width, height);

// 另存为指定格式图片
Canvas2Image.saveAsPNG(canvasObj, width, height);
Canvas2Image.saveAsJPEG(canvasObj, width, height);
Canvas2Image.saveAsGIF(canvasObj, width, height);
Canvas2Image.saveAsBMP(canvasObj, width, height);
```

实质上，`canvas2image` 只是提供了针对 canvas 基础 API 的二次封装（例如 [getImageData](https://link.segmentfault.com/?enc=6q89cY2NYTTzXu053L5WdA%3D%3D.wvK1PAVsZ6hFy%2B4A%2BifM93PsNuaKNg9cU7khdNUvCBQs5mKEwBvkzkiXz2DzRhvRN6l7fP02i1iVBiGq0wv%2BqQ%3D%3D)、[toDataURL](https://link.segmentfault.com/?enc=guufnOFH6UoshrZh9DxByA%3D%3D.mS5328YAUszSujnda%2B%2FgO3DeTc3UBqXsYt%2Bwt9H5qRKquAqScOUPc3RtRGUPzF3VI0dgM5SCE0ZTLVvHNZ7uvlhj65hrq6HtQJj%2BSt7H8bM%3D)），而本身并不依赖 `html2canvas`。

### html2image

接下来，我们基于以上两个工具库，实现一个基础版的快照生成方案。同样是分为两个阶段：

- 第一步，通过 `html2canvas` 实现 DOM 节点绘制到 canvas 对象中；
- 第二步，将上一步返回的 canvas 对象传入 `canvas2image`，进而按需导出快照图片信息。

具体地，我们封装一个 `html2Image` 的函数，用于输入目标节点以及 [配置项参数](https://link.segmentfault.com/?enc=oLJTgpNI5hKgpdc9L%2BkmTg%3D%3D.ZtdhvZiey8ylzB930d27IpytN1LSjCfN7oTBdSxTb8QU5wylg8DNeCLkW6GokiLZ)，输出快照图片信息。

```ts
/**
 * @file 封装 html2image 方法
 */
import html2canvas, { Options } from 'html2canvas';
import Canvas2Image from './canvas-to-image';

/**
 * 基础版快照方案
 * @param {HTMLElement} container
 * @param {object} options html2canvas相关配置
 */
export function html2Image(container: HTMLElement, fileName: string, options?: Options) {
    return html2canvas(container, options).then((canvas) => {
        const imageEl = Canvas2Image.saveAsPNG(canvas, canvas.width, canvas.height, fileName);
        return imageEl;
    });
}

```

```ts
function saveFile(strData, fileName) {
    // document.location.href = strData;

    const downloadElement = document.createElement('a');
    downloadElement.href = strData;
    // 下载后文件名
    downloadElement.download = `${fileName}.png`;
    document.body.append(downloadElement);
    // 触发点击下载
    downloadElement.click();
    // 下载完成移除元素
    downloadElement.remove();
}
```

## 内容完整性

详见参考

## 清晰度优化

最终生成快照的清晰度，源头上取决于第一步中 DOM 转换成的 canvas 的清晰度。

以下介绍 5 种行之有效的清晰度优化方法。

### 使用 Px 单位

为了给到 `html2canvas` 明确的整数计算值，避免因小数舍入而导致的拉伸模糊，建议将布局中使用中使用 `%`、`vw`、`vh` 或 `rem` 等单位的元素样式，统一改为使用 `px`。

**good:**

```xml
<div style="width: 100px;"></div>
```

**bad:**

```xml
<div style="width: 30%;"></div>
```

### 优先使用 Img 标签展示图片

很多情况下，导出图片模糊是由原视图中的图片是以 css 中 background 的方式显示的。

因为 background-size 并不会反馈一个具体的宽高数值，而是通过枚举值如 contain、cover 等代表图片缩放的类型；相对于 `<img>` 标签， background 方式最终生成的图片会较为模糊。

将 background 改为 `<img>` 方式呈现，对于图片清晰度会有一定的改观。对于必须要使用 background 的场景，参见 5.25 节的解决方案。

**good:**

```xml
<img class="u-image" src="./music.png" alt="icon">
```

**bad:**

```xml
<div class="u-image" style="background: url(./music.png);"></div>
```

### 配置高倍的 Canvas 画布

对于高分辨率的屏幕，canvas 可通过将 css 像素与高分屏的物理像素对齐，实现一定程度的清晰度提升（[这里](https://link.segmentfault.com/?enc=xVH0X%2B7vgJCHCAcwT7l%2BzA%3D%3D.bvLs6pocePEuxeHrdV00xpVbSrFs48Dq5Xy8f0thbHteooL8%2BjS%2BAWXZXjRO7gBU) 对两类像素有详细描述和讨论）。

在具体操作中，创建由 [devicePixelRatio](https://link.segmentfault.com/?enc=pZ%2FRUUqa0roJaMzBx0WKZg%3D%3D.5cHLKB9Yi0Hzco7swx%2BTt4RlQdmJ4YdWRjViCZKkOGwM%2FJ1QyQgh%2FRS00G8EIWsrQlnEHSrnWw9ERa%2FwAIAT0FRzsSd50ggg%2FH36vH689hA%3D) 放大的图像，然后使用 css 将其缩小相同的倍数，有效地提高绘制到 canvas 中的图像清晰度表现。

在使用 `html2canvas` 时，我们可以配置一个放缩后的 canvas 画布用于导入节点的绘制。

```ts
// convertToImage.js
import html2canvas from 'html2canvas';

// 创建用于绘制的基础canvas画布
function createBaseCanvas(scale) {
    const canvas = document.createElement("canvas");
    canvas.width = width * scale;
    canvas.height = height * scale;
    canvas.getContext("2d").scale(scale, scale);

    return canvas;
}

// 生成快照
function convertToImage(container, options = {}) {
    // 设置放大倍数
    const scale = window.devicePixelRatio;

    // 创建用于绘制的基础canvas画布
    const canvas = createBaseCanvas(scale);

    // 传入节点原始宽高
    const width = container.offsetWidth;
    const height = container.offsetHeight;   

    // html2canvas配置项
    const ops = {
        scale,
        width,
        height,
        canvas,
        useCORS: true,
        allowTaint: false,
        ...options
    };

    return html2canvas(container, ops).then(canvas => {
        const imageEl = Canvas2Image.convertToPNG(canvas, canvas.width, canvas.height);
        return imageEl;
    });
}
```

### 关闭抗锯齿

[imageSmoothingEnabled](https://link.segmentfault.com/?enc=Ku36rrzUHsFJXVb%2FTxhJug%3D%3D.248kUBMEt90C8kzzoiIkwgdfGkDrbG9a2GvSPf0oRFeZZLAX7Kx%2FRlTBIliUTEv5DCZaDG9S%2Bsdidd55nD%2B9ByF0PsSNLo2j4g5kIeBZdV84T0uhuRWHUzpkXZw8SbyT) 是 `Canvas 2D API` 用来设置图片是否平滑的属性，`true` 表示图片平滑（默认值），`false` 表示关闭 canvas 抗锯齿。

默认情况下，canvas 的抗锯齿是开启的，可以通过关闭抗锯齿来实现一定程度上的图像锐化，提高线条边缘的清晰度。

据此，我们将以上 `createBaseCanvas` 方法升级为：

```ts
// 创建用于绘制的基础canvas画布
function createBaseCanvas(scale) {
    const canvas = document.createElement("canvas");
    canvas.width = width * scale;
    canvas.height = height * scale;

    const context = canvas.getContext("2d");

    // 关闭抗锯齿
    context.mozImageSmoothingEnabled = false;
    context.webkitImageSmoothingEnabled = false;
    context.msImageSmoothingEnabled = false;
    context.imageSmoothingEnabled = false;

    context.scale(scale, scale);

    return canvas;
}
```

### 锐化特定元素

受到 canvas 画布放缩的启发，我们对特定的 DOM 元素也可以采用类似的优化操作，即设置待优化元素宽高设置为 2 倍或 `devicePixelRatio` 倍，然后通过 css 缩放的方式控制其展示大小不变。

例如，对于必须用背景图 `background` 的元素，采用以下方式可明显提高快照的清晰度：

```ts
.box {
    background: url(/path/to/image) no-repeat;
    width: 100px;
    height: 100px;
    transform: scale(0.5);
    transform-origin: 0 0;
}
```

其中，`width` 和 `height` 为实际显示宽高的 2 倍值，通过 `transform: scale(0.5)` 实现了元素大小的缩放，`transform-origin` 根据实际情况设置。
