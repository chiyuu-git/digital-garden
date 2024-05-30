---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-09-03-Sat, 10:07:48 pm","date-modified":"2023-12-27-Wed, 4:18:46 pm","permalink":"/programming/front-end/primitive/browser-api/dom-api/","dgPassFrontmatter":true}
---


# requestAnimationFrame API

参考:<https://juejin.im/entry/5ae844ec518825673a205855>

https://zh.javascript.info/js-animation#shi-yong-requestanimationframe

## 背景

传统的 `javascript` 动画是通过定时器 `setTimeout` 或者 `setInterval` 实现的。但是定时器动画一直存在两个问题：

+ 第一个就是动画的循时间环间隔不好确定，设置长了动画显得不够平滑流畅，设置短了浏览器的重绘频率会达到瓶颈，推荐的最佳循环间隔是 17ms（大多数电脑的显示器刷新频率是 60Hz，1000ms/60）
+ 第二个问题是定时器第二个时间参数只是指定了多久后将动画任务添加到浏览器的 UI 线程队列中，如果 UI 线程处于忙碌状态，那么动画不会立刻执行。

为了解决这些问题，H5 中加入了 `requestAnimationFrame`;

## 特点

1. **window.requestAnimationFrame()** 告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行
2. `requestAnimationFrame` 调用你传入给该方法的动画函数 (即你的回调函数)，会把每一帧中的所有 DOM 操作集中起来，在一次重绘或回流中就完成，并且重绘或回流的时间间隔紧紧跟随浏览器的刷新频率

    > 回调函数执行次数通常是每秒 60 次，但在大多数遵循 W3C 建议的浏览器中，回调函数执行次数通常与浏览器屏幕刷新次数相匹配。

3. 在隐藏或不可见的元素中，`requestAnimationFrame` 将不会进行重绘或回流，这当然就意味着更少的 CPU、GPU 和内存使用量
4. `requestAnimationFrame` 是由浏览器专门为动画提供的 API，在运行时浏览器会自动优化方法的调用，并且如果页面不是激活状态下的话，动画会自动暂停，有效节省了 CPU 开销

## 语法

**参数**

+ callback：下一次重绘之前更新动画帧所调用的函数 (即上面所说的回调函数)。该回调函数会被传入 [`DOMHighResTimeStamp`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMHighResTimeStamp) 参数，该参数与 [`performance.now()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Performance/now) 的返回值相同，它表示 `requestAnimationFrame()` 开始去执行回调函数的时刻。
+ 在同一个帧中的多个回调函数，它们每一个都会接受到一个相同的时间戳，即使在计算上一个回调函数的工作负载期间已经消耗了一些时间。该时间戳是一个十进制数，单位毫秒，最小精度为 1ms(1000μs)。

**返回值**

+ 一个 `long` 整数，请求 ID ，是 **回调列表** 中唯一的标识。是个非零值，没别的意义。你可以传这个值给 [`window.cancelAnimationFrame()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/cancelAnimationFrame) 以取消回调函数。

## 范例

`requestAnimationFrame` 本来就是为动画而生的，所以在处理 js 动画不在话下，与定时器的用法非常相似，下面是一个例子，点击元素时开始转动，再次点击转动速速增加。

每次点击都会添加一个新的 rqf，当有多个 rqf 的时候，id 不断被各个 rqf 的 change 函数赋值，随便点击一次 stop，随机停止一个 rqf，直至全部停止

```js
let deg = 0
let id
const rotate = document.getElementById("rotate")

rotate.addEventListener('click', function () {
    const self = this
    requestAnimationFrame(function change() {
        self.style.transform = 'rotate(' + (deg++) + 'deg)'
        id = requestAnimationFrame(change)
    })
})
document.getElementById('stop').onclick = function () {
    cancelAnimationFrame(id)
}
```

## 对比

定时器动画主动指定回调函数的执行间隔，每个定时器有唯一标识，只需要取得定时器函数的返回值就可以用于取消

raf 则是根据屏幕的刷新频率调整，因此不断的通过递归在调用回调函数，每一次 raf(callback) 都产生了新的标识，因此需要不断赋值给外部变量，用来取消 raf

## 大数据渲染

在大数据渲染过程中，比如表格的渲染，如果不进行一些性能策略处理，就会出现 UI 冻结现象，用户体验极差。有个场景，将后台返回的十万条记录插入到表格中，如果一次性在循环中生成 DOM 元素，会导致页面卡顿 5s 左右。这时候我们就可以用 `requestAnimationFrame` 进行分步渲染，确定最好的时间间隔，使得页面加载过程中很流畅。

```js
const total = 100000
const size = 100
const count = total / size
const done = 0
const ul = document.getElementById('list')

function addItems() {
    let li = null
    const fg = document.createDocumentFragment()

    for (let i = 0; i < size; i++) {
        li = document.createElement('li')
        li.innerText = 'item ' + (done * size + i)
        fg.appendChild(li)
    }

    ul.appendChild(fg)
    done++

    if (done < count) {
        requestAnimationFrame(addItems)
    }
};

requestAnimationFrame(addItems)
```

## 兼容性

firefox、chrome、ie10 以上支持很好，但不兼容 IE9 及以下浏览器，但是我们可以用定时器来做一下兼容，以下是兼容代码：

```js
(function () {
    var lastTime = 0;
    var vendors = ['webkit', 'moz'];
    for (var x = 0; x < vendors.length && !window.requestAnimationFrame; ++x) {
        window.requestAnimationFrame = window[vendors[x] + 'RequestAnimationFrame'];
        window.cancelAnimationFrame =
            window[vendors[x] + 'CancelAnimationFrame'] || window[vendors[x] + 'CancelRequestAnimationFrame'];
    }

    if (!window.requestAnimationFrame)
        window.requestAnimationFrame = function (callback) {
            /*调整时间，让一次动画等待和执行时间在最佳循环时间间隔内完成*/
            var currTime = new Date().getTime();
            var timeToCall = Math.max(0, 17 - (currTime - lastTime));
            var id = window.setTimeout(function () {
                    callback(currTime + timeToCall);
                },
                timeToCall);
            lastTime = currTime + timeToCall;
            return id;
        };

    if (!window.cancelAnimationFrame)
        window.cancelAnimationFrame = function (id) {
            clearTimeout(id);
        };
}());
```

## 性能对比

以上面大数据渲染为例，我们向一个页面中插入 1 万条数据。

下面是用 `setTimeout` 后浏览器帧率：

![img](dom-api/1631b4d8f001e74f)

下面是用 `requestAnimationFrame` 后浏览器帧率：

![img](dom-api/1631b4d8eebeba55)

# Mutation Observer API

## 概述

Mutation Observer API 用来监视 DOM 变动。DOM 的任何变动，比如节点的增减、属性的变动、文本内容的变动，这个 API 都可以得到通知。

概念上，它很接近事件，可以理解为 DOM 发生变动就会触发 Mutation Observer 事件。但是，它与事件有一个本质不同：事件是同步触发，也就是说，DOM 的变动立刻会触发相应的事件；Mutation Observer 则是 **异步触发**，DOM 的变动并不会马上触发，而是要等到当前所有 DOM 操作都结束才触发。

这样设计是为了应付 DOM 变动频繁的特点。举例来说，如果文档中连续插入 1000 个 `<p>` 元素，就会连续触发 1000 个插入事件，执行每个事件的回调函数，这很可能造成浏览器的卡顿；而 Mutation Observer 完全不同，只在 1000 个段落都插入结束后才会触发，而且只触发一次。

Mutation Observer 有以下特点:

- 它等待所有脚本任务完成后，才会运行（即异步触发方式），在事件循环中属于微任务
- 它把 DOM 变动记录封装成一个数组进行处理，而不是一条条个别处理 DOM 变动。
- 它既可以观察 DOM 的所有类型变动，也可以指定只观察某一类变动。

## MutationObserver 构造函数

- 使用时，首先使用 `MutationObserver` 构造函数，新建一个观察器实例，同时指定这个实例的回调函数。

    ```js
    var observer = new MutationObserver(callback);
    ```

- 上面代码中的回调函数，会在每次 DOM 变动后调用

**回调函数的参数**

- 该回调函数接受两个参数：
    - 第一个是变动数组
    - 第二个是观察器实例
- 下面是一个例子

    ```js
    var observer = new MutationObserver(function (mutationRecords , observer) {
      mutationRecords.forEach(function(mutationRecord) {
        console.log(mutation);
      });
    });
    ```

### mutationRecords

- 一个个 `MutationRecord` 实例所组成的数组。

## MutationObserver 的实例方法

### observe()

- `observe` 方法用来启动监听

**参数**

1. DomNode，所要观察的 DOM 节点
2. options，一个配置对象，指定所要观察的特定变动。

**options**

- 观察器所能观察的 DOM 变动类型（即上面代码的 `options` 对象），有以下几种。

    1. **childList**：子节点的变动（指新增，删除或者更改）
    2. **attributes**：属性的变动
    3. **characterData**：节点内容或节点文本的变动

> characterData 必须监视文本节点才有用

- 想要观察哪一种变动类型，就在 `option` 对象中指定它的值为 `true`。需要注意的是，必须同时指定 `childList`、`attributes` 和 `characterData` 中的一种或多种，若未均指定将报错。
- 除了变动类型，`options` 对象还可以设定以下属性：
    - `subtree`：布尔值，表示是否将该观察器应用于该节点的所有后代节点。
    - `attributeOldValue`：布尔值，表示观察 `attributes` 变动时，是否需要记录变动前的属性值。
    - `characterDataOldValue`：布尔值，表示观察 `characterData` 变动时，是否需要记录变动前的值。
    - `attributeFilter`：数组，表示需要观察的特定属性（比如 `['class','src']`）。

**示例**

```js
    var article = document.querySelector('article');
    
    var  options = {
      'childList': true,
      'attributes':true
    } ;
    
    observer.observe(article, options);
```

+ 上面代码中，`observe` 方法接受两个参数，第一个是所要观察的 DOM 元素是 `article`，第二个是所要观察的变动类型（子节点变动和属性变动）。

```js
    // 开始监听文档根节点（即<html>标签）的变动
    mutationObserver.observe(document.documentElement, {
      attributes: true,
      characterData: true,
      childList: true,
      subtree: true,
      attributeOldValue: true,
      characterDataOldValue: true
    });
```

- 下面的例子是观察新增的子节点。

    ```js
    var insertedNodes = [];
    var observer = new MutationObserver(function(mutations) {
      mutations.forEach(function(mutation) {
        for (var i = 0; i < mutation.addedNodes.length; i++) {
          insertedNodes.push(mutation.addedNodes[i]);
        }
      });
      console.log(insertedNodes);
    });
    observer.observe(document, { childList: true, subtree: true });
    ```

**注意**

- 对一个节点添加观察器，就像使用 `addEventListener` 方法一样，多次添加同一个观察器是无效的，回调函数依然只会触发一次。但是，如果指定不同的 `options` 对象，就会被当作两个不同的观察器。

### disconnect()，takeRecords（）

`disconnect` 方法用来停止观察。调用该方法后，DOM 再发生变动，也不会触发观察器。

```js
    observer.disconnect();
```

[`MutationObserver`](https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver) 的 `takeRecords()` 方法返回已检测到但尚未由观察者的回调函数处理的所有匹配 DOM 更改的列表，使变更队列保持为空。

```
    observer.takeRecords();
```

此方法最常见的使用场景是在断开观察者之前立即获取所有未处理的更改记录，以便在停止观察者时可以处理任何未处理的更改。

```js
    // 保存所有没有被观察器处理的变动
    var changes = mutationObserver.takeRecords();
    
    // 停止观察
    mutationObserver.disconnect();
```

## MutationRecord 对象

- DOM 每次发生变化，就会生成一条变动记录（MutationRecord 实例）。该实例包含了与变动相关的所有信息。Mutation Observer 处理的就是一个个 `MutationRecord` 实例所组成的数组。
- `MutationRecord` 对象包含了 DOM 的相关信息，有如下属性：
    - `type`：观察的变动类型（`attributes`、`characterData` 或者 `childList`）。
    - `target`：发生变动的 DOM 节点。
    - `addedNodes`：新增的 DOM 节点。
    - `removedNodes`：删除的 DOM 节点。
    - `previousSibling`：前一个同级节点，如果没有则返回 `null`。
    - `nextSibling`：下一个同级节点，如果没有则返回 `null`。
    - `attributeName`：发生变动的属性。如果设置了 `attributeFilter`，则只返回预先指定的属性。
    - `oldValue`：变动前的值。这个属性只对 `attribute` 和 `characterData` 变动有效，如果发生 `childList` 变动，则返回 `null`。

## 应用示例

### 子元素的变动

- ```js
    var callback = function (records){
      records.map(function(record){
        console.log('Mutation type: ' + record.type);
        console.log('Mutation target: ' + record.target);
      });
    };
    
    var mo = new MutationObserver(callback);
    
    var option = {
      'childList': true,
      'subtree': true
    };
    
    mo.observe(document.body, option);
    ```
- 上面代码的观察器，观察 `<body>` 的所有下级节点（`childList` 表示观察子节点，`subtree` 表示观察后代节点）的变动。回调函数会在控制台显示所有变动的类型和目标节点。

### 属性的变动

- 下面的例子说明如何追踪属性的变动。

    ```js
    var callback = function (records) {
      records.map(function (record) {
        console.log('Previous attribute value: ' + record.oldValue);
      });
    };
    
    var mo = new MutationObserver(callback);
    
    var element = document.getElementById('#my_element');
    
    var options = {
      'attributes': true,
      'attributeOldValue': true
    }
    
    mo.observe(element, options);
    ```

- 上面代码先设定追踪属性变动（`'attributes': true`），然后设定记录变动前的值。实际发生变动时，会将变动前的值显示在控制台。

### 文本节点的变动

+ ```html
    <p id="test">123</p>
    ```

    ```js
    var callback = function (records){
      console.log(6)
      records.map(function(record){
        console.log('Mutation type: ' + record.type);
      });
    };
    
    const mo = new MutationObserver(callback)
    const option = {
      characterData:true
    }
    
    mo.observe(test.childNode[0],option)
    
    // 改变文本节点的值
    test.childNodes[0].data = 456
    ```

+ 也可以使用 nodeValue 属性

### 取代 DOMContentLoaded 事件 @@@

- 网页加载的时候，DOM 节点的生成会产生变动记录，因此只要观察 DOM 的变动，就能在第一时间触发相关事件，也就没有必要使用 `DOMContentLoaded` 事件。

    ```js
    var observer = new MutationObserver(callback);
    observer.observe(document.documentElement, {
      childList: true,
      subtree: true
    });
    ```

- 上面代码中，监听 `document.documentElement`（即网页的 `<html>`HTML 节点）的子节点的变动，`subtree` 属性指定监听还包括后代节点。因此，任意一个网页元素一旦生成，就能立刻被监听到。
- 下面的代码，使用 `MutationObserver` 对象封装一个监听 DOM 生成的函数。

    ```js
    (function(win){
      'use strict';
    
      var listeners = [];
      var doc = win.document;
      var MutationObserver = win.MutationObserver || win.WebKitMutationObserver;
      var observer;
    
      function ready(selector, fn){
        // 储存选择器和回调函数
        listeners.push({
          selector: selector,
          fn: fn
        });
        if(!observer){
          // 监听document变化
          observer = new MutationObserver(check);
          observer.observe(doc.documentElement, {
            childList: true,
            subtree: true
          });
        }
        // 检查该节点是否已经在DOM中
        check();
      }
    
      function check(){
      // 检查是否匹配已储存的节点
        for(var i = 0; i < listeners.length; i++){
          var listener = listeners[i];
          // 检查指定节点是否有匹配
          var elements = doc.querySelectorAll(listener.selector);
          for(var j = 0; j < elements.length; j++){
            var element = elements[j];
            // 确保回调函数只会对该元素调用一次
            if(!element.ready){
              element.ready = true;
              // 对该节点调用回调函数
              listener.fn.call(element, element);
            }
          }
        }
      }
    
      // 对外暴露ready
      win.ready = ready;
    
    })(this);
    
    // 使用方法
    ready('.foo', function(element){
      // ...
    });
    ```

# Web Animations API (WAAPI)

https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API/Web_Animations_API_Concepts

JavaScript 规范确实借鉴了很多社区内的优秀类库，通过原生实现的方式提供更好的性能。WAAPI 提供了与 jQuery 类似的语法，同时也做了很多补充，使得其更加的强大。同时 W3C 官方也为开发者提供了 [web-animations/web-animations-js](https://link.zhihu.com/?target=https%3A//github.com/web-animations/web-animations-js/tree/master) polyfill。

## 特点

### 双模型

它通过组合两个模型来实现：时序模型 和 动画模型。

### WAAPI 优雅简洁

web animation 的 api 设计优雅而又全面。文中比对了常见的 WAAPI 与 CSS Animation 对照关系，我们可以看到 WAAPI 更加简洁，而且语法上也更加容易为开发者接受。确实，在写一些复杂的动画逻辑时，需要灵活控制性强的接口。我们可以看到，在处理串连多个动画、截取完整动画的一部分时更加方便。如果非要说有什么劣势，个人在开发中感觉 keyframe 的很多只都只能使用字符串，不过这也是将 css 写在 js 中最常见的一种方式了。

### 低耦合

CSS 动画中，如果需要控制动画或者过渡的开始或结束只能通过相应的 dom 事件来监听，并且在回调函数中操作，这也是受 CSS 本身语言特性约束所致。也就是说很多情况下，想要完成一个动画需要结合 CSS 和 JS 来共同完成。使用 WAAPI 则有 promise 和 event 两种方式与监听 dom 事件相对应。从代码可维护性和完整性上看 WAAPI 有自身语言上的优势。

### 兼容性和流畅度

兼容性上 WAAPI 常用方法已经兼容了大部分现代的浏览器。如果想现在就玩玩 WAAPI，可以使用官方提供的 polyfill。而 CSS 动画我们也用了很久，基本作为一种在现代浏览器中提升体验的方式，对于老旧的浏览器只能用一些优雅的降级方案。至于流畅度的问题，文中也提到性能与 CSS 动画一般，而且提供了性能优化的方案。

## 指定动画

WAAPI 提供了很简洁明了的，我们可以直接在 dom 元素上直接调用 animate 函数：

```js
var element = document.querySelector('.animate-me');
var animation = element.animate(keyframes, 1000);
var animation = element.animate(keyframes, options); 
```

### Keyframes

第一个参数是一个对象数组，每个对象表示动画中的一帧：每个对象代表原始 CSS 中的一个键。 然而，与 CSS 不同，Web 动画 API 不需要明确地告知每个键出现的动画的百分比。 它将根据您给出的按键数量自动将动画划分为相等的部分。 这意味着具有三个键的关键帧对象将通过动画的每个循环的方式播放中间键，除非另有说明。

```js
var aliceTumbling = [
  { transform: 'rotate(0) translate3D(-50%, -50%, 0)', color: '#000' },
  { color: '#431236', offset: 0.3},
  { transform: 'rotate(360deg) translate3D(-50%, -50%, 0)', color: '#000' }
];
```

```css
@keyframes aliceTumbling {
  0% {
    color: #000;
    transform: rotate(0) translate3D(-50%, -50%, 0);
  }
  30% {
    color: #431236;
  }
  100% {
    color: #000;
    transform: rotate(360deg) translate3D(-50%, -50%, 0);
  }
}
```

当我们想要明确地设置一个键与其他键的偏移量时，我们可以直接在对象中指定一个偏移量，并与逗号分隔。 在上面的例子中，为了确保爱丽丝的颜色变化为 30％而不是 50％，我们给它的偏移量为 0.3。

**异常**

必须至少指定两个关键帧（表示动画序列的开始和结束状态）.如果您的关键帧列表只有一个条目, [`Element.animate()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/animate) 将抛出不支持的异常报错

### Duration / Options

第二个参数是 duration，表示动画的时间。同时也支持在第二个参数中传入配置项来指定缓动方式、循环次数等。

```js
var options = {
  iterations: Infinity, // 动画的重复次数，默认是 1
  iterationStart: 0, // 用于指定动画开始的节点，默认是 0
  delay: 0, // 动画延迟开始的毫秒数，默认 0
  endDelay: 0, // 动画结束后延迟的毫秒数，默认 0
  direction: 'alternate', // 动画的方向 默认是按照一个方向的动画，alternate 则表示交替
  duration: 700, // 动画持续时间，默认 0
  fill: 'forwards', // 是否在动画结束时回到元素开始动画前的状态
  easing: 'ease-out', // 缓动方式，默认 "linear"
};
```

有了这些配置项，基本可以满足开发者的动画需求。同时，文中也提到了在 WAAPI 中很多专业术语与 CSS 变量有所不同，不过这些变化也更显简洁。

CSS 动画中使用的属性值与 Web 动画中使用的属性值存在一些小的差异。比如,Web 动画中不能使用字符串“infinite”，而是使用 Javascript 的关键字 Infinity。 以及我们用 `easing` 来代替 `timing-function`。我们不必在这列出 `easing` 的值，因为不像在 CSS 动画里，默认的 "[animation-timing-function](https://developer.mozilla.org/en-US/docs/Web/CSS/animation-timing-function)" 是 `ease`。页面动画 API 的默认 easing 是 `linear`— 而这就是我们想要的。

## 控制方法

[`Element.animate()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/animate) 方法会在调用后立即执行

```js
var nommingCake = document.getElementById('eat-me_sprite').animate(
[
  { transform: 'translateY(0)' },
  { transform: 'translateY(-80%)' }
], {
  fill: 'forwards',
  easing: 'steps(4, end)',
  duration: aliceChange.effect.timing.duration / 2
});
```

### pause()

```js
nommingCake.pause();
```

### play()

```js
nommingCake.play();
```

### finish()

使动画结束

### cancel()

### reverse()

设置动画播放速度

### playbackRate

控制动画的速度

到负值，所以它向后运行.

## 动画事件

在 dom 元素上调用 animate 函数之后返回 animation 对象，或者通过 ele.getAnimation 方法获取 dom 上的 animation 对象。借此开发者可以通过 promise 和 event 两种方式对动画进行操作：

**event 方式**

```js
myAnimation.onfinish = function() {
  element.remove();
}
```

**promise 方式**

```js
myAnimation.finished.then(() =>
  element.remove())
```

## 关键帧格式

[`Element.animate()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/animate), [`KeyframeEffect.KeyframeEffect()`](https://developer.mozilla.org/zh-CN/docs/Web/API/KeyframeEffect/KeyframeEffect), 和 [`KeyframeEffect.setKeyframes()` (en-US)](https://developer.mozilla.org/en-US/docs/Web/API/KeyframeEffect/setKeyframes) 都接受格式为一组关键帧的对象， 这种格式有以下几种选项。

### 数组格式

一个由多个关键帧的属性和值组成的对象所构成的 `数组`。这是 [`getKeyframes()` (en-US)](https://developer.mozilla.org/en-US/docs/Web/API/KeyframeEffect/getKeyframes) 方法返回的规范格式。

```js
element.animate([ { opacity: 1 },
                  { opacity: 0.1, offset: 0.7 },
                  { opacity: 0 } ],
                2000);
```

对每个关键帧的偏移可以通过提供一个 `offset` 来指定。

`offset` 的值必须是在**[0.0, 1.0]**这个区间内，且须升序排列。

并非所有的关键帧都需要设置 `offset`。 没有指定 `offset` 的关键帧将与相邻的关键帧均匀间隔。

可以通过提供 `easing` 过渡来给指定关键帧之间应用过渡效果，如下所示：

```js
element.animate([ { opacity: 1, easing: 'ease-out' },
                  { opacity: 0.1, easing: 'ease-in' },
                  { opacity: 0 } ],
                2000);

```

在这个例子中，指定的 easing 仅适用于指定它的关键帧到下一帧之间。 但是在 `options` 中指定的 `easing` 值都将应用在一个动画的整个持续时间里。

### 对象格式

一个包含 key-value 键值的 `对象` 需要包含动画的属性和要循环变化的值 `数组`

```js
element.animate({
  opacity: [ 0, 1 ],          // [ from, to ]
  color:   [ "#fff", "#000" ] // [ from, to ]
}, 2000);

```

使用这种格式，每个数组的元素数量不必相等。所提供的值将独立分开。

```js
element.animate({
  opacity: [ 0, 1 ], // offset: 0, 1
  backgroundColor: [ "red", "yellow", "green" ], // offset: 0, 0.5, 1
}, 2000);

```

特殊键 `offset`，`easing` 和 `composite`（如下）可以与属性值一起指定。

```js
element.animate({
  opacity: [ 0, 0.9, 1 ],
  offset: [ 0, 0.8 ], // [ 0, 0.8, 1 ] 的简写
  easing: [ 'ease-in', 'ease-out' ],
}, 2000);

```

从属性值列表生成一组合适的关键帧后，每个提供的偏移量将应用于相应的关键帧。如果值不足或者列表包含空值 `null`，则以没有指定处理（即和上面第 1 种数组格式的一样均匀间隔）.

如果 `easing` 或 `composite` 值太少，将根据需要，重复相应的列表。

### 特色属性

两个特色的 css 属性：

- [`float`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/float), 必须写成 `cssFloat` ，因为 "float" 是 JavaScript 的保留关键字
- [`offset`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/offset), 必须写成 `cssOffset` ，因为 "offset" 表示如下的关键帧 offset

**offset**：关键帧的 offset 偏移量指定为介于 `0.0` 和 `1.0` 之间的数字或为 `null`。 这相当于使用 `@keyframes` 在 CSS 样式表中以百分比指定开始和结束状态。 如果此值为 `null` 或缺失，则关键帧将在相邻关键帧之间均匀分布。

**easing**：从这个关键帧直到这一级中的下一个关键帧所使用的 [timing function](https://developer.mozilla.org/en-US/docs/Web/CSS/easing-function)

**composite**：[`KeyframeEffect.composite` (en-US)](https://developer.mozilla.org/en-US/docs/Web/API/KeyframeEffect/composite) 操作用于将此关键帧中指定的值与基础值组合在一起。 如果正在使用在效果上指定的复合操作，则不会出现这种情况。

## 核心概念

Web animations consist of Timeline Objects, Animation Objects, and Animation Effect Objects working together. By assembling these disparate objects, we can create animations of our own.

### [Timing Mode](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API/Web_Animations_API_Concepts#timing)

The Timing Model is the backbone of working with the WAAPI. Each document has a master timeline, [`Document.timeline`](https://developer.mozilla.org/en-US/docs/Web/API/Document/timeline), which stretches from the moment the page is loaded to infinity — or until the window is closed. Spread along that timeline according to their durations are our animations. Each animation is anchored to a point in the timeline by its [`startTime`](https://developer.mozilla.org/en-US/docs/Web/API/Animation/startTime), representing the moment along the document’s timeline when the animation starts playing.

All the animation's playback relies on this timeline: seeking the animation moves the animation’s position along the timeline; slowing down or speeding up the playback rate condenses or expands its spread across the timeline; repeating the animation lines up additional iterations of it along the timeline. In the future, we might have timelines based on gestures or scroll position or even parent and child timelines. The Web Animations API opens up so many possibilities!

### [Animation Mode](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API/Web_Animations_API_Concepts#animation)

The animation model can be thought of as an array of snapshots of what the animation could look like at any given time, lined up along the duration of the animation.

### [Timeline](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API/Web_Animations_API_Concepts#timeline)

Timeline objects provide the useful property [`currentTime`](https://developer.mozilla.org/en-US/docs/Web/API/AnimationTimeline/currentTime), which lets us see how long the page has been opened for: it's the "current time" of the document's timeline, which started when the page was opened. As of this writing, there’s only one kind of timeline object: the one based on the active document’s [`timeline`](https://developer.mozilla.org/en-US/docs/Web/API/Document/timeline).

In the future we may see timeline objects that correspond to the length of the page, perhaps a `ScrollTimeline`, or other things entirely.

### [Animation](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API/Web_Animations_API_Concepts#animation_2)

[Animation objects](https://developer.mozilla.org/en-US/docs/Web/API/Animation) can be imagined as DVD players: they’re used for controlling media playback, but without media to play, they don’t do anything. Animation objects accept media in the form of Animation Effects, specifically Keyframe Effects (we’ll get to those in a moment). Like a DVD player, we can use the Animation Object’s methods to [play](https://developer.mozilla.org/en-US/docs/Web/API/Animation/play), [pause](https://developer.mozilla.org/en-US/docs/Web/API/Animation/pause), [seek](https://developer.mozilla.org/en-US/docs/Web/API/Animation/currentTime), and [control the animation’s playback direction](https://developer.mozilla.org/en-US/docs/Web/API/Animation/reverse) and [speed](https://developer.mozilla.org/en-US/docs/Web/API/Animation/playbackRate).

### [Animation Effect](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API/Web_Animations_API_Concepts#animation_effect)

If Animation objects are DVD players, we can think of Animation Effects, or Keyframe Effects, as DVDs. Keyframe Effects are a bundle of information including at the bare minimum a set of keys and the duration they need to be animated over. The Animation Object takes this information and, using the Timeline Object, assembles a playable animation we can view and reference.

We currently have only one animation effect type available: [`KeyframeEffect`](https://developer.mozilla.org/en-US/docs/Web/API/KeyframeEffect). Potentially we could have all kinds of Animation Effects in the future—e.g. effects for grouping and sequencing, not unlike features we had in Flash. In fact, Group Effects and Sequence Effects have already been outlined in the currently-in-progress level 2 spec of the Web Animations API.

## Todo

https://zhuanlan.zhihu.com/p/81954538

https://hackernoon.com/creating-highly-performant-animations-using-web-animations-api-and-react-hooks-k92d3utf

http://timothyschmidt.us/posts/waapi-keyframeeffect-and-react/

https://morioh.com/p/da66e50163ce

https://css-tricks.com/how-i-used-the-waapi-to-build-an-animation-library/

https://github.com/wellyshen/use-web-animations

https://www.w3cplus.com/react/create-highly-performant-animations-using-web-animations-api-and-reack-hooks.html

https://www.zhangxinxu.com/wordpress/2018/03/web-animations-api-dynamic-feature-animation/

# FAQ

#faq/js

## Dom 体操

[HTML DOM — Phuoc Nguyen](https://phuoc.ng/collection/html-dom/)
