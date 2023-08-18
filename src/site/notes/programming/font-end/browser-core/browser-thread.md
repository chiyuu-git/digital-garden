---
{"dg-publish":true,"permalink":"/programming/font-end/browser-core/browser-thread/"}
---


参考：<https://juejin.im/post/5a6547d0f265da3e283a1df7#heading-6>

# 进程与线程

## 理解

### 进程 (process)

程序的一次执行, 它占有一片独有的内存空间。

**进程是 cpu 资源分配的最小单位。系统会给它分配内存，是能拥有资源和独立运行的最小单位**

### 线程 (thread)

是进程内独立执行的单元，线程是建立在进程的基础上的一次程序运行单位，一个进程中可以有多个线程

线程是 cpu 调度的最小单位

![1547106772414](/img/user/programming/font-end/browser-core/browser-thread/1547106772414.png)

### 进程与线程

- 应用程序必须运行在某个进程的某个线程上
- 一个进程中一般至少有一个运行的线程: **主线程**
- 一个进程中也可以同时运行多个线程, 我们会说程序是多线程运行的
- **一个进程内的数据可以供其中的多个线程直接共享**
- 进程之间是相互独立的，多个进程之间的数据是不能直接共享的

线程池 (thread pool)：保存多个线程对象的容器，实现线程对象的反复调用

多进程：一个应用程序可以同时启动多个进程运行

多线程：单个进程内，同时运行多个线程

看一个形象化的例子

```js
工厂的资源 -> 系统分配的内存（独立的一块内存）

工厂之间的相互独立 -> 进程之间相互独立

多个工人协作完成任务 -> 多个线程在进程中协作完成任务

工厂内有一个或多个工人 -> 一个进程由一个或多个线程组成

工人之间共享空间 -> 同一进程下的各个线程之间共享程序的内存空间（包括代码段、数据集、堆等）
```

> 不同进程之间也可以通信，不过代价较大
>
> 现在，一般通用的叫法：**单线程与多线程**，都是指**在一个进程内**的单和多。（所以核心还是得属于一个进程才行）

## 优缺点

多线程优点：有效提高 CPU 的利用率

缺点：

- 创建多线程开销
- 线程间切换开销（单个 CPU 单位时间只运行一个线程）
- 死锁与状态同步问题

单线程优点：顺序编程简单易懂

缺点：效率低

# JS 是单线程执行的

## 如何证明 Js 执行是单线程的?

setTimeout() 的回调函数是在主线程执行的

定时器回调函数只有在运行栈中的代码全部执行完后才有可能执行

alert() 之后，暂停了主线程，也暂停了计时，不点确定，定时器的回调函数一直不会执行

## 为什么 Js 要用单线程模式, 而不用多线程模式?

JavaScript 的单线程，与它的用途有关。

作为浏览器脚本语言，JavaScript 的主要用途是与用户互动，以及操作 DOM。

这决定了它只能是单线程，否则会带来很复杂的同步问题: 比如，你在网页上有若干个操作,也就是在主线程中有多个任务，一个线程任务是在某个 DOM 节点上添加内容，另一个线程任务是删除这个节点，这时浏览器应该以哪个线程为准？

即使是支持多线程的 JAVA，在开发安卓应用也只使用一个主线程操作 UI，称作 UI 线程

https://www.cnblogs.com/Jacky-MYD/p/7743532.html

# 浏览器是多进程的

## 浏览器是单进程还是多进程?

有的是单进程

- 老版 firefox
- 老版 IE

有的是多进程

- chrome
- 新版 firefox
- 新版 IE

如何查看浏览器是否是多进程运行的呢?

- 任务管理器==>进程
- 简单点理解，每打开一个 Tab 页，就相当于创建了一个独立的浏览器进程。

**注意**：在这里浏览器应该也有自己的优化机制，有时候打开多个 tab 页后，可以在 Chrome 任务管理器中看到，有些进程被合并了 （所以每一个 Tab 标签对应一个进程并不一定是绝对的）

## 浏览器都包含哪些进程

### Browser 进程

浏览器的主进程（负责协调、主控），只有一个

主要的作用是：

- 负责浏览器界面显示，与用户交互。如前进，后退等
- 负责各个页面的管理，创建和销毁其他进程
- 将 Renderer 进程得到的内存中的 Bitmap，绘制到用户界面上
- 网络资源的管理，下载等

### 第三方插件进程

每种类型的插件对应一个进程，仅当使用该插件时才创建

### GPU 进程

最多一个，用于 3D 绘制等

### 浏览器渲染进程

等价于 浏览器内核、Renderer 进程，内部是**多线程**的

默认每个 Tab 页面一个进程，互不影响。

主要作用为：

- 页面渲染
- 脚本执行
- 事件处理等

## 浏览器多进程的优势

相比于单进程浏览器，多进程有如下优点：

- 避免单个 page crash 影响整个浏览器
- 避免第三方插件 crash 影响整个浏览器
- 多进程充分利用多核优势
- 方便使用沙盒模型隔离插件等进程，提高浏览器稳定性

当然，内存等资源消耗也会更大

# 浏览器内核（渲染进程）

## 什么是浏览器内核?

支持浏览器运行的最核心的进程，页面的渲染，JS 的执行，事件的循环，都在这个进程内进行。

不同的浏览器可能不太一样

- Chrome, Safari: webkit
- firefox: Gecko
- IE: Trident
- 360,搜狗等国内浏览器: Trident + webkit

## 渲染进程有哪些线程?

### GUI 渲染线程

负责**渲染**浏览器界面，**解析**HTML，CSS，构建 DOM 树和 RenderObject 树，布局和渲染等。

当界面需要**重绘**（Repaint）或由于某种操作引发**回流**(reflow) 时，该线程就会执行

注意，**GUI 渲染线程与 JS 引擎线程是互斥的**，当 JS 引擎执行时 GUI 线程会被挂起（相当于被冻结了），GUI 更新会被保存在一个队列中**等到 JS 引擎空闲时**立即被执行。

GUI 线程渲染结果多数情况下是一个位图 (Bitmap)，然后交给 Browser 进程显示

### JS 引擎线程

也称为 JS 内核，负责处理 Javascript 脚本程序。（例如 V8 引擎）

JS 引擎线程负责解析 Javascript 脚本，运行代码。

JS 引擎一直等待着**任务队列**中任务的到来，然后加以处理，一个 Tab 页（renderer 进程）中无论什么时候都只有一个 JS 线程在运行 JS 程序

同样注意，**GUI 渲染线程与 JS 引擎线程是互斥的**，所以如果 JS 执行的时间过长，这样就会造成页面的渲染不连贯，导致页面渲染加载阻塞。

可以看到 JS 引擎线程的优先级是更高的，一旦遇到需要执行的脚本，GUI 渲染线程就会被挂起

### 事件触发线程

归属于浏览器而不是 JS 引擎，用来控制事件循环, 可以理解, JS 引擎自己都忙不过来，需要浏览器另开线程协助

当 JS 引擎执行代码块如 setTimeout 时（也可来自浏览器内核的其他线程,如鼠标点击、AJAX 异步请求等），会将对应任务添加到**事件线程**中

当对应的事件符合触发条件被触发时，该线程会把事件添加到任务队列的队尾，等待 JS 引擎的处理

注意，由于 JS 的单线程关系，所以这些任务队列中的事件都得排队等待 JS 引擎处理（当 JS 引擎空闲时才会去执行）

### 定时触发器线程

传说中的 `setInterval` 与 `setTimeout` 所在线程

浏览器定时计数器并不是由 JavaScript 引擎计数的,（因为 JavaScript 引擎是单线程的, 如果处于阻塞线程状态就会影响记计时的准确）

因此通过单独线程来计时并触发定时（任务发起后，添加到事件队列中，等待 JS 引擎空闲后执行）

> 注意，W3C 在 HTML 标准中规定，规定要求 setTimeout 中低于 4ms 的时间间隔算为 4ms

### 异步 Http 请求线程

在 XMLHttpRequest 在连接后是通过浏览器新开一个线程请求

将检测到状态变更时，如果设置有回调函数，异步线程就**产生状态变更事件**，将这个回调再放入任务队列中。再由 JavaScript 引擎执行。

https://juejin.im/post/5a6547d0f265da3e283a1df7#heading-6

## Browser 进程 和 Renderer 进程的通信过程

如果自己打开任务管理器，然后打开一个浏览器，就可以看到：**任务管理器中出现了两个进程（一个是主控进程，一个则是打开 Tab 页的渲染进程）**， 然后在这前提下，看下整个的过程：(简化了很多)

- Browser 进程收到用户请求，首先需要获取页面内容（譬如通过网络下载资源），随后将该任务通过 RendererHost 接口传递给 Render 进程
- Renderer 进程的 Renderer 接口收到消息，简单解释后，交给渲染线程，然后开始渲染
  - 渲染线程接收请求，加载网页并渲染网页，这其中可能需要 Browser 进程获取资源和需要 GPU 进程来帮助渲染
  - 当然可能会有 JS 线程操作 DOM（这样可能会造成回流并重绘）
  - 最后 Render 进程将结果传递给 Browser 进程
- Browser 进程接收到结果并将结果绘制出来
- 浏览器内核源码解析 https://www.cnblogs.com/lhb25/p/how-browsers-work.html

## 浏览器内核中线程之间的关系

### GUI 渲染线程与 JS 引擎线程互斥

由于 JavaScript 是可操纵 DOM 的，如果在修改这些元素属性同时渲染界面（即 JS 线程和 UI 线程同时运行），那么渲染线程前后获得的元素数据就可能不一致了。

因此为了防止渲染出现不可预期的结果，浏览器设置 GUI 渲染线程与 JS 引擎为互斥的关系，当 JS 引擎执行时 GUI 线程会被挂起， GUI 更新则会被保存在一个队列中等到 JS 引擎线程空闲时立即被执行。

### JS 阻塞页面加载

从上述的互斥关系，可以推导出，JS 如果执行时间过长就会阻塞页面。

譬如，假设 JS 引擎正在进行巨量的计算，此时就算 GUI 有更新，也会被保存到队列中，等待 JS 引擎空闲后执行。 然后，由于巨量计算，所以 JS 引擎很可能很久很久后才能空闲，自然会感觉到巨卡无比。

所以，要尽量避免 JS 执行时间过长，这样就会造成页面的渲染不连贯，导致页面渲染加载阻塞的感觉

# 浏览器的事件循环（轮询）模型

 event loop 可视化 https://www.jsv9000.app/

## 理解

所有代码分类

- 初始化执行代码 (同步代码): 包含**绑定**dom 事件监听, **设置**定时器, **发送**ajax 请求的代码
- 同步任务都在主线程上执行，形成一个 执行栈
- 回调执行代码 (异步代码): 处理回调逻辑（调用回调函数）

js 引擎执行代码的基本流程：初始化代码===>回调代码

模型的重要组成部分:

- JS 调用栈：`Javascript` 有一个 `main thread` 主线程和 `call-stack` 调用栈 (执行栈)，所有的任务都会被放到调用栈等待主线程执行。JS 调用栈采用的是后进先出的规则，当函数执行的时候，会被添加到栈的顶部，当执行栈执行完成后，就会从栈顶移出，直到栈内被清空。
- 事件 (定时器/DOM 事件/Ajax) 管理模块（**三个触发线程**）
- **事件触发线程**管理着一个**任务队列**，只要异步任务有了运行结果，就在**任务队列**之中放置一个事件。
- 任务队列、消息队列、事件队列、回调队列，都 OK，下文中统称为任务队列

模型的运转流程

1. 执行初始化代码, 将事件回调函数交给事件触发线程管理，产生执行栈
2. **当事件发生时, 事件触发线程将回调函数及其上数据添加到回调列队中**
3. 只有当初始化代码执行完后 (可能要一定时间), 才会遍历读取任务队列中的回调函数执行

模型概念图

![1547107412345](/img/user/programming/font-end/browser-core/browser-thread/1547107412345.png)

![1547107426240](/img/user/programming/font-end/browser-core/browser-thread/1547107426240.png)

## 一个完整异步过程

1. **JS 引擎线程**发一起一个异步请求
2. **相关线程**接收请求并告知主线程已收到通知
3. 主线程可以继续执行后面的代码，同时工作线程执行异步任务，并管理着回调函数
4. 事件发生，工作线程完成工作后，通知主线程
5. 主线程收到通知后，如果已经执行完了初始化的代码，会遍历读取回调队列（消息队列）中的回调函数执行

## 异步函数

通常具有以下的形式：A(args..., callbackFn);

它可以叫做异步过程的发起函数，或者叫做异步任务**注册函数**。

- args 是这个函数需要的参数
- callbackFn（回调函数）也是这个函数的参数

所以，从主线程的角度看，一个异步过程包括下面两个要素：

1. 发起函数（或叫注册函数）A;
2. 回调函数 callbackFn；

它们都是**主线程**上调用的，其中注册函数用来发起异步过程，回调函数用来处理结果。

```js
setTimeout（()=>{})，1000）；
//其中setTimeout就是异步过程的发起函数，function是回调函数。
```

```js
//注：前面说得形式A(args..., callbackFn)只是一种抽象的表示，并不代表回调函数一定要作为发起函数的参数，例如：
　　var xhr = new XMLHttpRequest();
　　xhr.onreadystatechange = xxx;     // 添加回调函数
　　xhr.open('GET'， url);
　　xhr.send();   // 发起函数　　
//发起函数和回调函数是分离的。
```

## 任务队列和事件循环

工作线程将任务放到**任务队列**, 主线程通过**事件循环**过程去取任务。

任务队列：任务队列是一个先进先出的**队列**，它里面存着各种任务。

事件循环：事件循环是指主线程重复从任务队列中取任务，执行的**过程**。

- 实际上，主线程只会做一件事情，就是从任务队列里面取任务、执行任务、再取任务、再执行任务。
- 当任务队列为空时，就会等待直到任务队列变成非空。
- 而且主线程只有在将前面的任务执行完成后，才会去去下一个任务。
- 这种机制就叫做**事件循环机制**，取一个任务并执行的过程叫做一次循环。 @@@

```js
//事件循环代码表示大概是这样的：
while（true）{
  var message = queue.get（）；
  execute（message）；
}
```

任务队列中放的任务具体是什么？

- 任务的具体结构当然跟具体实现有关，但是为了简单起见，我们可以认为：任务就是注册异步任务时添加的**回调函数**。

```js
//再次以异步AJAX为例，假设存在如下的代码：
　　$.ajax（'http://baidu.com'，function（res）{
　　　　console.log（'我是响应'，res）；
　　}）
　　// 其他代码
　　...
　　...
```

主线程在发起 AJAX 请求后，会继续执行其他代码，AJAX 线程负责请求 http://baidu.com，拿到响应后，它会把响应封装成一个 JavaScript 对象，然后构造一条消息：

```js
var message = function（）{
  callbackFn（response）；
}
//其中callbackFn就是就是前面代码中得到成功响应时的回调函数。
```

主线程在执行完当前循环中的所有代码后，就会到任务队列取出这条任务 (也就是 message 函数)，并执行它。

到此为止，就完成了工作线程对主线程的通知，回调函数也就得到了执行。

如果一开始主线程就没有提供回调函数，AJAX 线程在收到 HTTP 响应后，也就没必要通知主线程，从而也没必要往消息队列放消息。

用图表示这个过程就是：

　　![img](/img/user/programming/font-end/browser-core/browser-thread/1220270-20171027174149305-672812579-1547109462353.png)

从上文中我们也可以得到这样一个明显的结论，就是：**异步过程的回调函数，一定不在当前这一轮事件循环中执行。**

## 异步与事件

任务队列中的每个任务实际上都对应着一个事件。

上文中一直没有提到一类很重要的异步过程：DOM 事件。

```js
var button = document.getElement('#btn');
	button.addEventListener('click', function(e) {
	console.log();
});
```

从事件的角度来看，上述代码表示：

- 在按钮上添加了一个鼠标单击事件的事件监听器；当用户点击按钮时，鼠标单击事件触发，**事件监听器函数**被调用。

从异步过程的角度看：

- addEventListener 函数就是异步过程的**发起函数**，事件监听器函数就是异步过程的回调函数。事件触发时，表示异步任务完成，会将事件监听器函数封装成一条消息放到消息队列中，等待主线程执行。

**事件的概念实际上并不是必须的，事件机制实际上就是异步过程的通知机制。**

我觉得它的存在是为了编程接口对开发者更友好。

另一方面，所有的异步过程也都可以用事件来描述。

例如：setTimeout 可以看成对应一个时间到了！的事件。

前文的 setTimeout(fn, 1000); 可以看成：`timer.addEventListener('timeout', 1000, fn);`

# 定时触发器线程 #

上述事件循环机制的核心是：JS 引擎线程和事件触发线程

为什么要单独的定时器线程？因为 JavaScript 引擎是单线程的, 如果处于阻塞线程状态就会影响记计时的准确，因此很有必要单独开一个线程用来计时。

什么时候会用到定时器线程？**当使用 setTimeout 或 setInterval 时**，它需要定时器线程计时，计时完成后就会将特定的事件推入**事件队列**中。

```js
setTimeout(function(){
    console.log('hello!');
}, 1000);
```

这段代码的作用是当 `1000` 毫秒计时完毕后（由定时器线程计时），将**回调函数推入事件队列**中，等待主线程执行

```js
setTimeout(function(){
    console.log('hello!');
}, 0);

console.log('begin');
```

这段代码的效果是最快的时间内将回调函数推入事件队列中，等待主线程执行

注意：执行结果是：先 `begin` 后 `hello!`

虽然代码的本意是 0 毫秒后就推入事件队列，但是 W3C 在 HTML 标准中规定，规定要求 setTimeout 中低于 4ms 的时间间隔算为 4ms。 (不过也有一说是不同浏览器有不同的最小时间设定)

就算不等待 4ms，就算假设 0 毫秒就推入事件队列，也会先执行 `begin`（因为只有可执行栈内空了后才会主动读取事件队列）

[定时器和消息队列](programming/font-end/primitive/browser-api/bom.md#定时器和消息队列)

# Macrotask 与 Microtask

在 ES6 中，Promise 里有了一个新的概念：`microtask`

进一步，JS 中分为两种任务类型：**macrotask 和 microtask**，在 ECMAScript 中，microtask 称为 `jobs`，macrotask 可称为 `task`

## Macrotask

又称之为宏任务，可以理解是每次**执行栈执行的代码**就是一个宏任务（包括每次从事件队列中获取一个事件回调并放到执行栈中执行）

每一个 task 会从头到尾将这个任务执行完毕，不会执行其它

浏览器为了能够使得 JS 内部 task 与 DOM 任务能够有序的执行，会在一个 task 执行结束后，在下一个 task 执行开始前，对页面进行重新渲染 （`task->渲染->task->...`）

## Microtask

又称为微任务，可以理解是在当前 task 执行结束后立即执行的任务

- 也就是说，在当前 task 任务后，下一个 task 之前，在渲染之前
- 所以它的响应速度相比 setTimeout（setTimeout 是 task）会更快，因为无需等渲染
- 也就是说，在某一个 macrotask 执行完后，就会将在它执行期间产生的所有 microtask 都执行完毕（在渲染前）

## 常见的宏任务和微任务

macrotask：

- 主代码块，setTimeout，setInterval、（可以看到，事件队列中的每一个事件都是一个 macrotask）
- `setImmediate`（浏览器暂时不支持，只有 IE10 支持，具体可见 [`MDN`](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FWindow%2FsetImmediate)）、`I/O`、`UI Rendering`。
- `MessageChannel`

> 宏任务的优先级？

microtask：

+ Promise
+ process.nextTick
+ Object.observe(废弃)
+ MutationObserver`等

> **补充：在 node 环境下，process.nextTick 的优先级高于 Promise**，也就是可以简单理解为：在宏任务结束后会先执行微任务队列中的 nextTickQueue部分，然后才会执行微任务中的 Promise部分。

再根据线程来理解下：

- macrotask中的事件都是放在**一个事件队列**中的，而这个队列由**事件触发线程**维护
- microtask中的所有微任务都是添加到**微任务队列**（Job Queues）中，等待当前 macrotask执行完毕后执行，而这个队列由**JS 引擎线程维护** （这点由自己理解 +推测得出，因为它是在主线程下无缝执行的）

所以，总结下运行机制：

- 执行一个宏任务（栈中没有就从事件队列中获取）
- 执行过程中如果遇到微任务，就将它添加到微任务的 Job Queues 中
- 宏任务执行完毕后，执行这个 task宏任务期间发生的所有 job微任务
- 当前宏任务执行完毕，开始检查渲染，然后 GUI线程接管渲染
- 渲染完毕后，JS线程继续接管，开始下一个宏任务（从事件队列中获取）

promise.all 和 单独的 promise一样，只不过多个 primise的逻辑在 all方法内实现，最终都是**一个**micro-task挂载到队列上。 @@@

> 小知识：[babel中对于微任务的polyfill](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2Fzloirock%2Fcore-js%2Fblob%2Fmaster%2Fpackages%2Fcore-js%2Finternals%2Ftask.js)，如果是拥有 `setImmediate` 函数平台，则使用之，若没有则自定义则利用各种比如 nodejs中的 `process.nextTick`，浏览器中支持 `postMessage` 的，或者是通过 create一个 script来实现微任务 (microtask)。最终的最终，是使用 setTimeout，不过这个就和微任务无关了，promise变成了宏任务的一员。

[nextTick](../framework/framwork-common/!framework-common.md#NextTick)

# FAQ

#faq/js

参考：谷歌 73以上：<https://v8.js.cn/blog/fast-async/>

## 基本概念

执行一个宏任务（栈中没有就从事件队列中获取）

执行过程中如果遇到微任务，就将它添加到微任务的 Job Queues 中

> 微任务也是一个队列，先加入的先执行

宏任务执行完毕后，执行这个 task宏任务期间发生的所有 job微任务

> resolve()是微任务还是算同步代码？算作微任务
>
> 微任务产生的微任务如何处理？在同一个微任务队列中处理，直到微任务为空，才执行下一个宏任务
>
> 如果产生无限的微任务，就会发生死循环

```js
setTimeout(()=>{
  console.log(1) 
},0)
/* Promise.resolve().then(()=>{
  console.log(2) 
}) */

let a = new Promise((resolve,reject) => {
  resolve()
})
let target = 50
for (let i = 2; i < target; i++) {
  a = a.then(() => {
    console.log(i)
  })
}
console.log(3)  // 直到50全部输出完才打印1
```

当前宏任务执行完毕，开始检查渲染，然后 GUI线程接管渲染

渲染完毕后，JS线程继续接管，开始下一个宏任务（从事件队列中获取）

promise 的 excutor 是同步函数，resolve 不会阻塞同步代码的执行

注意的是，立即 `resolve` 的 Promise 对象，是在本轮“事件循环”（event loop）的结束时，而不是在下一轮“事件循环”的开始时。

```javascript
setTimeout(function () {
  console.log('three');
}, 0);

Promise.resolve().then(function () {
  console.log('two');
});

console.log('one');

// one
// two
// three
```

上面代码中，`setTimeout(fn, 0)` 在下一轮“事件循环”开始时执行，`Promise.resolve()` 在本轮“事件循环”结束时执行，`console.log('one')` 则是立即执行，因此最先输出。

### 等级一

```js
setTimeout(()=>{
   console.log(1) 
},0)
Promise.resolve().then(()=>{
   console.log(2) 
})
console.log(3) 
```

```js
3 2 1
```

这个是属于 Eventloop的问题。main script运行结束后，会有微任务队列和宏任务队列。微任务先执行，之后是宏任务。

### 等级二

```js
setTimeout(()=>{
   console.log(1) 
},0)
let a=new Promise((resolve)=>{
    console.log(2)
    resolve()
}).then(()=>{
   console.log(3) 
}).then(()=>{
   console.log(4) 
})
console.log(4) 
```

```js
2 4 3 4 1
```

这个要从 Promise的实现来说：

- Promise的 executor是一个同步函数，即非异步，立即执行的一个函数，因此他应该是和当前的任务一起执行的。
- 而 Promise的链式调用 then，每次都会在内部**生成一个新的 Promise**，然后执行 then，在执行的过程中不**断向微任务 (microtask) 推入新的函数**，因此直至微任务 (microtask)的队列清空后才会执行下一波的 macrotask。

### 等级三

```js
new Promise((resolve,reject)=>{
  console.log("promise1")
  resolve()
}).then(()=>{
  console.log("then11")
  new Promise((resolve,reject)=>{
    console.log("promise2")
    resolve()
  }).then(()=>{
    console.log("then21")
  }).then(()=>{
    console.log("then23")
  })
}).then(()=>{
  console.log("then12")
})
```

```js
'promise1'
'then11'
'promise2'
'then21'
'then12'
'then23'
```

第一轮

- current task: promise1是当之无愧的立即执行的一个函数，参考上一章节的 executor，立即执行输出 `[promise1]`
- micro task queue: [promise1的第一个 then]

第二轮

- current task: then1执行中，立即输出了 `then11` 以及新 promise2的 `promise2`
- micro task queue: [新 promise2的 then函数,以及 promise1的第二个 then函数]

第三轮

- current task: 新 promise2的 then函数输出 `then21` 和 promise1的第二个 then函数输出 `then12`。
- micro task queue: [新 promise2的第二 then函数]

第四轮

- current task: 新 promise2的第二 then函数输出 `then23`
- micro task queue: []

关键是微任务被加入队列的顺序

### 等级三变种

Promise中 then返回一个 Promise呢？？

```js
new Promise((resolve,reject)=>{
    console.log("promise1")
    resolve()
}).then(()=>{
    console.log("then11")
    return new Promise((resolve,reject)=>{
        console.log("promise2")
        resolve()
    }).then(()=>{
        console.log("then21")
    }).then(()=>{
        console.log("then23")
    })
}).then(()=>{
    console.log("then12")
})
```

```js
'promise1'
'then11'
'promise2'
'then21'
'then23'
'then12'
```

这里 Promise的第二个 then相当于是挂在新 Promise的最后一个 then的返回值上。

### 等级三变种 2

```js
new Promise((resolve,reject)=>{
    console.log("promise1")
    resolve()
}).then(()=>{
    console.log("then11")
    new Promise((resolve,reject)=>{
        console.log("promise2")
        resolve()
    }).then(()=>{
        console.log("then21")
    }).then(()=>{
        console.log("then23")
    })
}).then(()=>{
    console.log("then12")
})
new Promise((resolve,reject)=>{
    console.log("promise3")
    resolve()
}).then(()=>{
    console.log("then31")
})
```

```js
'promise1'
'promise3'
'then11'
'promise2'
'then31'
'then21'
'then12'
'then23'
```

第一轮

- current task: promise1，promise3
- micro task queue: [`promise2的第一个then`，`promise3的第一个then`]

第二轮

- current task: then11，promise2，then31
- micro task queue: [`promise2的第一个then`，`promise1的第二个then`]

第三轮

- current task: then21，then12
- micro task queue: [`promise2的第二个then`]

第四轮

- current task: then23
- micro task queue: []

最终输出:[`promise1`,`promise3`,`then11`,`promise2`,`then31`,`then21`,`then12`,`then23`]

### 等级四

在 async/await之下，对 Eventloop的影响。

```js
async function async1() {
    console.log("async1 start");
    await  async2();
    console.log("async1 end");
}

async  function async2() {
    console.log( 'async2');
}

console.log("script start");

setTimeout(function () {
    console.log("settimeout");
},0);

async1();

new Promise(function (resolve) {
    console.log("promise1");
    resolve();
}).then(function () {
    console.log("promise2");
});
console.log('script end'); 
```

```js
'script start'
'async1 start'
'async2'
'promise1'
'script end'
'async1 end'
'promise2'
'settimeout'
```

async/await仅仅影响的是函数内的执行，而不会影响到函数体外的执行顺序。也就是说 async1()并不会阻塞后续程序的执行，`await async2()` 相当于一个 Promise，`console.log("async1 end");` 相当于前方 Promise的 then之后执行的函数。

按照上章节的解法，最终输出结果：[`script start`,`async1 start`,`async2`,`promise1`,`script end`,`async1 end`,`promise2`,`settimeout`]

此处唯一有争议的就是 async的 then和 promise的 then的优先级的问题，请看下方详解。*

### async/await与 Promise的优先级详解

- https://juejin.im/post/5c9a43175188252d876e5903#heading-5
- 最新版的 async异步成本与 promise完全一致，谁先谁后取决于谁先注册
- 旧版的同一论注册的 async 后于 promise
- https://juejin.im/post/5b73d7a6518825610072b42b#heading-0
- https://github.com/rhinel/blog-word/issues/4
- https://juejin.im/post/5c3cc981f265da616a47e028
- async` 函数返回一个 Promise 对象。
- `async` 函数内部 `return` 语句返回的值，会成为 `then` 方法回调函数的参数。

  ```javascript
  async function f() {
    return 'hello world';
  }
  
  f().then(v => console.log(v))
  // "hello world"
  ```

- 上面代码中，函数 `f` 内部 `return` 命令返回的值，会被 `then` 方法回调函数接收到。
- 注意：虽然显示声明了返回值 是一个字符串，但是还是可以调用 then 方法，说明是返回值被包装成了 promise 对象，并且会自动把原值作为参数传递给 then 方法

  ```js
  function getFoo(){
    return new Promise(resolve =>{
      setTimeout(() => {
        console.log('2s:getFoo')
        resolve('2s:getFoo')
      },2000)
    })
  }
  function getBar(){
    return new Promise(resolve =>{
      setTimeout(() => {
        console.log('3s:getBar')
        resolve('3s:getBar') //作为then方法的回调函数的参数
      },3000)
    })
  }
  
  (async function(){
    // 写法一
    let [foo, bar] = await Promise.all([getFoo(), getBar()]);
    console.log([foo,bar]) 
    //?为什么foo和bar会获取到resolve的参数？可以肯定的是，resolve的参数肯定传递给了then方法的两个回调，问题是他内部做了什么，有一个自动执行器，不断的把data进行传递，最后返回
  })()
  ```

- 其次，如果不设置回调函数，`Promise`**内部抛出的错误**，不会反应到外部。
- Promise 对象的错误具有**“冒泡”性质**，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个 `catch` 语句捕获。

  ```javascript
  getJSON('/post/1.json').then(function(post) {
    return getJSON(post.commentURL);
  }).then(function(comments) {
    // some code
  }).catch(function(error) {
    // 处理前面三个Promise产生的错误
  });
  ```

- 上面代码中，一共有三个 Promise 对象：一个由 `getJSON` 产生，两个由 `then` 产生。它们之中任何一个抛出的错误，都会被最后一个 `catch` 捕获。

## 全家福

```js
  const ch = new MessageChannel()
  const port1 = ch.port1
  const port2 = ch.port2
  
  port1.onmessage = function() {
      console.log(8)
  }
  
  
  var callback = function (){
    console.log(6)
  };
  
  const mo = new MutationObserver(callback)
  const option = {
    characterData:true
  }
  mo.observe(test.childNodes[0],option)
  
  setTimeout(() => {
    console.log(1)
  }, 0)
  
  new Promise((resolve) => {
    console.log(2)
    for(let i = 0; i < 100000; i++) {
        (i === 99999) && resolve()
    }
    console.log(3)
  }).then(() => {
    console.log(4)
  })
  
  console.log(5)
  
  port2.postMessage(8)
  
  test.childNodes[0].data = 456
  
  console.log(7)
  
  // 发送消息
  
  
  
  // 2
  // 3
  // 5
  // 7
  // 4
  // 6
  // 8
  // 1
  ```

# 为什么 JS 要是异步的，能解决什么问题

因为 JS 是单线程的, 说说 UI 单线程

所以要异步处理一些耗时的操作
