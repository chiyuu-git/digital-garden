---
{"tags":[],"aliases":["contenteditable","textarea"],"review-dates":[],"dg-publish":true,"date-created":"2023-03-05-Sun, 1:42:40 pm","date-modified":"2023-07-30-Sun, 3:43:19 pm","permalink":"/programming/FAQ/ui-component/input/","dgPassFrontmatter":true}
---


# FAQ

#faq/ui

## 获取 Input 失焦时点击的元素

[https://developer.mozilla.org/zh-CN/docs/Web/API/event.relatedTarget](https://link.segmentfault.com/?enc=SaT41cinR5BEpUbXGRymtQ%3D%3D.DxlVZH6wVSTXYf%2FsXdql23Nc0QWy7%2BTul%2B93GAiMZHMuJ0AUhMHAdEq%2FKiTkLFkXnigqbjDQ1dINDy7ApsW%2BFMIhVGN2Mqx%2BtxK3B6KNAk8%3D)，

[http://jsbin.com/diqihunica/edit?html,js,console,output](https://link.segmentfault.com/?enc=wgX17wUV8XvikWNGo843mA%3D%3D.oMoYRlTCmvgjFVvHliu2SM3x5%2FnCUf%2B%2F4MjgRAgnpVu5Ao1ZPVh2Jj%2F7qZT2vfM7IVhACW%2B1Wcm%2BSiEs8MkwOw%3D%3D)

通过 event.relatedTarget 可以获取, 但是感觉不够准确, 在 editor 中无法准确获取到

## 点击其他区域, 防止 Input 失焦

在开发过程中，总会碰到以下两个情况：

1. 要求点击某个区域，阻止 input 框 (或者设置了 contenteditable=“true” 的编辑区) 失去焦点。
2. 要求像微信输入框那种点击选择表情、图片等要求保留原来焦点的位置的情况。因为一失去焦点位置，除非是往最前面或最后面插入，否则要想在原来的地方插入内容是很难的。

### Mousedown

通过阻止外部区域的 mousedown 事件的默认行为

<iframe height="300" style="width: 100%;" scrolling="no" title="prevent input blur" src="https://codepen.io/chiyu-git/embed/xxarXYJ?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true"></iframe>

## Enter Search

利用 type search 和 form 标签配合, 实现虚拟键盘的回车搜索
