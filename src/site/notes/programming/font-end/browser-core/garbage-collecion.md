---
{"dg-publish":true,"permalink":"/programming/font-end/browser-core/garbage-collecion/"}
---


# 概述

当一个对象没有任何变量或属性对它引用，此时我们将永远无法操作这个对象（堆内存），这种对象称为垃圾。

在 JS 中拥有自动的垃圾回收机制，会自动将这些垃圾对象进行销毁，我们**不需要也不能**进行垃圾回收操作。

我们需要做的是：将不再需要使用的对象设置为 null 即可

V8 实现了准确式 GC，GC 算法采用了分代式垃圾回收机制。因此，V8 将内存（堆）分为新生代和老生代两部分。

[简单介绍一下 V8 引擎的垃圾回收机制](https://github.com/CavsZhouyou/Front-End-Interview-Notebook/blob/master/JavaScript/JavaScript.md#80-%E7%AE%80%E5%8D%95%E4%BB%8B%E7%BB%8D%E4%B8%80%E4%B8%8B-v8-%E5%BC%95%E6%93%8E%E7%9A%84%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E6%9C%BA%E5%88%B6)

# 新生代

新生代中的对象一般存活时间较短，使用 Scavenge GC 算法.

在新生代空间中，内存空间分为两部分，分别为 From 空间和 To 空间

1. 在这两个空间中，必定有一个空间是使用的，另一个空间是空闲的
2. 新分配的对象会被放入 From 空间中
3. 当 From 空间被占满时，新生代 GC 就会启动了
	1. 算法会检查 From 空间中存活的对象并复制到 To 空间中
	2. 如果有失活的对象就会销毁
4. 当复制完成后将 From 空间和 To 空间互换，这样 GC 就结束了

# 老生代

老生代中的对象一般存活时间较长且数量也多，使用了两个算法，分别是标记清除算法和标记压缩算法。

在讲算法前，先来说下什么情况下对象会出现在老生代空间中：

- 新生代中的对象是否已经经历过一次 Scavenge 算法，如果经历过的话，会将对象从新生代空间移到老生代空间中。
- To 空间的对象占比大小超过 25 %。在这种情况下，为了不影响到内存分配，会将对象从新生代空间移到老生代空间中。

老生代中的空间很复杂，有如下几个空间

```js
  enum AllocationSpace {
    // TODO(v8:7464): Actually map this space's memory as read-only.
    RO_SPACE,    // 不变的对象空间
    NEW_SPACE,   // 新生代用于 GC 复制算法的空间
    OLD_SPACE,   // 老生代常驻对象空间
    CODE_SPACE,  // 老生代代码对象空间
    MAP_SPACE,   // 老生代 map 对象
    LO_SPACE,    // 老生代大空间对象
    NEW_LO_SPACE,  // 新生代大空间对象
  
    FIRST_SPACE = RO_SPACE,
    LAST_SPACE = NEW_LO_SPACE,
    FIRST_GROWABLE_PAGED_SPACE = OLD_SPACE,
    LAST_GROWABLE_PAGED_SPACE = MAP_SPACE
  };
```

在老生代中，以下情况会先启动标记清除算法：

+ 某一个空间没有分块的时候
+ 空间中被对象超过一定限制
+ 空间不能保证新生代中的对象移动到老生代中

在这个阶段中，会遍历堆中所有的对象，然后标记活的对象，在标记完成后，销毁所有没有被标记的对象。在标记大型对内存时，可能需要几百毫秒才能完成一次标记。这就会导致一些性能上的问题。为了解决这个问题，2011 年，V8 从 stop-the-world 标记切换到增量标志。在增量标记期间，GC 将标记工作分解为更小的模块，可以让 JS 应用逻辑在模块间隙执行一会，从而不至于让应用出现停顿情况。但在 2018 年，GC 技术又有了一个重大突破，这项技术名为并发标记。该技术可以让 GC 扫描和标记对象时，同时允许 JS 运行，你可以点击 [该博客](https://v8project.blogspot.com/2018/06/concurrent-marking.html) 详细阅读。

清除对象后会造成堆内存出现碎片的情况，当碎片超过一定限制后会启动压缩算法。在压缩过程中，将活的对象像一端移动，直到所有对象都移动完成然后清理掉不需要的内存。

# 内存泄漏

- 意外的全局变量
- 被遗忘的计时器或回调函数
- 脱离 DOM 的引用
- 闭包

还有常见的 event listener 忘记取消监听了

详细资料可以参考： [《JavaScript 内存泄漏教程》](http://www.ruanyifeng.com/blog/2017/04/memory-leak.html) [《4 类 JavaScript 内存泄漏及如何避免》](https://jinlong.github.io/2016/05/01/4-Types-of-Memory-Leaks-in-JavaScript-and-How-to-Get-Rid-Of-Them/) [《杜绝 js 中四种内存泄漏类型的发生》](https://juejin.im/entry/5a64366c6fb9a01c9332c706) [《javascript 典型内存泄漏及 chrome 的排查方法》](https://segmentfault.com/a/1190000008901861)
