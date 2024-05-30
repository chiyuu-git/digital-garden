---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-08-25-Fri, 1:35:18 pm","date-modified":"2023-10-02-Mon, 1:48:54 pm","permalink":"/programming/hydrid/!hybrid/","dgPassFrontmatter":true}
---


[hybrid 开发 - 简书](https://www.jianshu.com/p/f7e0ae5d4c3f)

# 概念

hybrid： 就是前端和客户端的混合开发， 让 App 同时具备原生和 Web 的技术优势。需要前端开发人员和客户端开发人员配合完成

# 特点

1、可以快速迭代更新，无需 APP 审核（hybrid 技术可以一天上线多次）

2、体验流畅（和 Native 开发的体验基本类似）

3、减少开发和沟通成本，双端公用一套代码

4、相对于纯原生开发， 缺点： 性能问题， 兼容性问题 【Android 5.0+ 和 IOS 9.0+ 上缺点不再明显】

# 理解 Webview

是 APP 中的一个组件（App 可以有 Webview，也可以没有）， 主要用于加载 H5 页面，是一个小型的浏览器内核。

# Hybrid 主流技术框架

1. Web 渲染： Cordova 【前身是 PhoneGap】
2. 原生渲染： React Native、Weex
3. 混合渲染： 微信小程序

# Hybrid 应用场景

1. 微信公众号， 通过 JSSDK 连接 Native 和 web 端
2. 微信小程序，通过内置框架连接 Native 和 web 端

![](!hybrid/image-20230826132144545.png)

# JSBridge 桥接器

hybrid 核心技术, 实现 Native 端和 web 端双向通信的一种机制, 以 Javascript 引擎或 Webview 容器为媒介, 通过约定协议进行通信

# JSBridge 实现原理

![](!hybrid/image-20230826132406786.png)

1. 类比 Client/Server
2. 将 Native 端原生接口封装成 Javascript 接口
3. 将 Web 端原生接口封装成 Native 端原生接口
4. Web 端和 Native 端之间双向通信

# JSBridge 两种实现方式

指 Web 调用原生端

原生端调用 Web 端只有执行 Js 代码一种方式

## 方式一: 拦截 WebView 请求的 URL Schema

1. URL Schema: 是 URL 的一种请求格式 `<prototal>://<domain>/<path>?<query>`
2. 自定义 JSBridge 通信的 URL Schema `jsbridge://<method>?<params>`, 比如 `jsbridge://showToast?text=hello`

![](!hybrid/image-20230826132654498.png)

如果符合我们的 URL Schema, 就会拦截解析 url 调用原生的方法, 如果是正常的 url，则走正常的服务端请求

优点：兼容性好

缺点： 不直观、url 长度有限制

### 实践

```tsx
// 1. natvie 调用web方法 【如 调用web弹窗】
// 原生端调用web端只有执行js代码一种方式
private void showWebDialog(string text) {
	String jsCode = String.format("window.showWebDialog('%s')",  text)
	webView.evaluateJavascript(jsCode, null) 
    // webView 引擎去执行
	// 然后再去 web端实现showWebDialog方法
} 

// 2. web端调用native端
// web
showBtn.addEventListener(‘click’, e => {
    const inputValue = editText.value
    showNativeDialog(inputValue)
})

function showNativeDialog(text) {
    window.alert('jsbridge://showNativeDialog?text=' + text) // native拦截
}
// native
...
// 拦截里面...
if ( !message.startWith('jsbridge://') ) {
   return ... // 走默认的
} else {
  showNativeDialog(text)
}
private void showNativeDialog(text) {
    ...
}
```

## 方式二: 向 WebView 注入 JS API

App 向 webView 注入一个 js 对象提供给 webView 用， 方法名、参数和 app 方法一致

![](!hybrid/image-20230826132924150.png)

优点： 简单直观

缺点： 有兼容性问题 (Android 4,2+)

```tsx
// 1、native调用web不变 都是通过执行js脚本

// web端掉用native端
// web
showBtn.addEventListener(‘click’, e => {
    const inputValue = editText.value
    showNativeDialog(inputValue)
})
function showNativeDialog(text) {
    // window.alert('jsbridge://showNativeDialog?text=' + text) // native拦截
  window.NativeBridge.showNativeDialog(text)
}
// native
webView.addJavascriptInterface(new NativeBridge(this),  'NativeBridg') // addJavascriptInterface 原生端向wevView注入对象的方法
class   {
    private ctx
    NativeBridge(ctx) {
        this.ctx = ctx
   }
  @addJavascriptInterface
  private void showNativeDialog(text) {
      ...
  }
}
```

## 方式三: 两次单项调用获取返回值

上面的方式一和方式二都有一个问题, 都是单向的, 不能把调用后返回的结果传回

1. 在对端执行操作并返回结果
2. 有输入有输出才是完整的调用

![](!hybrid/image-20230826133034298.png)

eg： 在 web 端获取 app 端某个输入框的值，并用 web 弹框展示。

```tsx
let id = 1
const callbackMap = {}
window.JSSDK = {
    getNativeEditTextValue ( callback) {
        // 
      const callbackId = id ++ 
      callbackMap[callbackId] = callback
      NativeBridge.getNativeEditTextValue(callbackId)
    },
    receiveMessge(callbackId, value) { // 接收
      if (callbackMap[callbackId]) {
        callbackMap[callbackId](value)
      }
   }
}
showBtn2.addEventListener(‘click’, e => {
    JSSDK.getNativeEditTextValue(value => window.alert(`native 输入值：${value}`))
})
// native
class   {
    private ctx
    NativeBridge(ctx) {
        this.ctx = ctx
   }
  @addJavascriptInterface
   private void getNativeEditTextValue(callbackId) {
       ...
      // receiveMessge
   }
}
```

# JSBridge 的开源实现

 1. JsBridge： 拦截 URL Schema 方式
2. DSBridge： 注入 JS API 方式

# Flutter

[如何看待阿里北海Kraken项目即将弃坑？ - 知乎](https://www.zhihu.com/question/534811524/answer/2595510449)

# YOGA

[`Realm` requires CocoaPods version `>= 1.10`, which is not satisfied by your current version, `1.8.3` [closed\]](https://stackoverflow.com/questions/65199227/realm-requires-cocoapods-version-1-10-which-is-not-satisfied-by-your-cur)

https://stackoverflow.com/questions/65199227/realm-requires-cocoapods-version-1-10-which-is-not-satisfied-by-your-cur/65594547

webview：https://zhuanlan.zhihu.com/p/58691238

https://tbfungeek.github.io/2019/11/05/%E5%9C%A8%E9%A1%B9%E7%9B%AE%E4%B8%AD%E4%BD%BF%E7%94%A8Yoga-%E5%B8%83%E5%B1%80%E5%BC%95%E6%93%8E/

https://tech.meituan.com/2019/09/19/litho-practice-in-dynamic-program-mtflexbox.html

https://mp.weixin.qq.com/s?__biz=MjM5NjQ5MTI5OA==&mid=2651750430&idx=2&sn=89c8c1212f4b6a24694028ec3188aa09&from=timeline

https://juejin.cn/post/6844904041290432525#heading-9

https://tech.meituan.com/2019/03/14/litho-use-and-principle-analysis.html

https://jsonchao.github.io/2020/01/13/%E6%B7%B1%E5%85%A5%E6%8E%A2%E7%B4%A2Android%E5%B8%83%E5%B1%80%E4%BC%98%E5%8C%96%EF%BC%88%E4%B8%8B%EF%BC%89/

## Native 渲染相关

满帮 flutter 动态化：<https://www.sohu.com/a/442313727_463970>

<https://tech.meituan.com/2019/12/19/meituan-mrn-practice.html>

<https://developer.aliyun.com/article/763895>

<http://wiki.baidu.com/pages/viewpage.action?pageId=1083882568>

<https://github.com/leecade/react-native-swiper>

<https://ask.dcloud.net.cn/article/36083>

<http://wiki.baidu.com/pages/viewpage.action?pageId=1175434482>

<https://cloud.tencent.com/developer/article/1680356>

<https://www.zhihu.com/question/384934444>

<https://vczero.github.io/react_native/H5-React-Native-Native.html>

<https://juejin.cn/post/6844903557607456776>

<https://zhuanlan.zhihu.com/p/79725603>

- [x] <https://tech.meituan.com/2017/06/09/webviewperf.html>
