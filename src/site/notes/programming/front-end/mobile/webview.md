---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2020-09-03-Thu, 3:23:12 pm","date-modified":"2023-04-29-Sat, 8:13:55 pm","permalink":"/programming/front-end/mobile/webview/","dgPassFrontmatter":true}
---


# 概述

现在很多 App 里都内置了 Web 网页（Hyprid App），比如说很多电商平台，淘宝、京东、聚划算等等，如下图

那么这种该如何实现呢？其实这是 Android 里一个叫 WebView 的组件实现的。今天我将全面介绍**WebView**的常用用法。

WebView 是一个基于 webkit 引擎、展现 web 页面的控件。

> Android 的 Webview 在低版本和高版本采用了不同的 webkit 版本内核，4.4 后直接使用了 Chrome。

## 作用

显示和渲染 Web 页面

直接使用 html 文件（网络上或本地 assets 中）作布局

可和 JavaScript 交互调用

> WebView 控件功能强大，除了具有一般 View 的属性和设置外，还可以对 url 请求、页面加载、渲染、页面交互进行强大的处理

一般来说 Webview 可单独使用，可联合其子类一起使用

Webview 的最常用的子类

- WebSettings 类
- WebViewClient 类
- WebChromeClient 类

# Webview 常用方法
