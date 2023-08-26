---
{"dg-publish":true,"permalink":"/programming/hybrid/hybrid/"}
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

![](/img/user/programming/hybrid/!hybrid/image-20230826132144545.png)

# JSBridge 桥接器

hybrid 核心技术, 实现 Native 端和 web 端双向通信的一种机制, 以 Javascript 引擎或 Webview 容器为媒介, 通过约定协议进行通信

# JSBridge 实现原理

![](/img/user/programming/hybrid/!hybrid/image-20230826132406786.png)

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

![](/img/user/programming/hybrid/!hybrid/image-20230826132654498.png)

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

![](/img/user/programming/hybrid/!hybrid/image-20230826132924150.png)

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

![](/img/user/programming/hybrid/!hybrid/image-20230826133034298.png)

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
