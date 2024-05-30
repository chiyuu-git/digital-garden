---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-12-07-Thu, 10:12:46 am","date-modified":"2023-12-15-Fri, 4:29:11 pm","permalink":"/programming/front-end/primitive/wasm/!wasm/","dgPassFrontmatter":true}
---


Figma 是使用 Wasm 的典型案例，Figma 文件是在 C++/Wasm 中处理的，这确实能他们带来巨大的速度提升

Zaplib 创始人 JP Posma，他是一位具有 18 年编程经验的计算机科学家，认为使用手动内存管理（大量 ArrayBuffers）、WebWorkers 等在浏览器里开发密集内容的应用程序非常痛苦

最后，Zaplib 团队指出，在某些情况下，Rust 确实比 JS 更快，但这类情况比预想的要少，而且性能一般也就翻一倍，大多数情况下达不到 10 倍。

“只有真正依赖 Rust 的零成本抽象特性时，才能实现 10 倍的巨大收益——这要归功于内存布局和对垃圾回收（GC）的规避，因此处理 100 万个 Rust 微结构的速度确实比处理 100 万个 JS 对象更快。但这种情况其实相当罕见，在增量调整中就更别指望了。即使 10 倍性能改进基本不成立，工程师们自然不会愿意接受这样一套需要重新学习、重新维护的工具链和技术堆栈。

我们自己肯定不愿意，自然也不能强迫其他人。总之，**要想实现性能改进，一般都有比转向 Rust/Wasm 更简单的方法。”**

另外，他们还特地强调，虽然 Figma 在用 Wasm，但仔细观察就会发现，他们使用 Wasm 其实更多是个“历史遗留问题”——他们的目标是在 C++ 中构建以保护原生应用，而不是追求更高性能。Figma 文件是在 C++/Wasm 中处理的，这确实能带来巨大的速度提升，但真正让 Figma 性能脱颖而出的其实是他们的 WebGL 渲染器。

[图形编辑器开发：是否要像 Figma 一样上 wasm - 知乎](https://zhuanlan.zhihu.com/p/645670604)

# SharedArrayBuffer 和 ffmpeg.wasm

[前端的 FFmpeg? 可能还没准备好 - 掘金](https://juejin.cn/post/7150962372130111518?searchId=2023111619452928716436F6F0BA6BAAEE)

[http-cross-origin](programming/basic/cs-basic/network-protocol/http-cross-origin.md#COOP%20和%20COEP)
