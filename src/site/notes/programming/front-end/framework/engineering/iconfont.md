---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2020-09-06-Sun, 3:54:25 pm","date-modified":"2023-04-29-Sat, 8:10:11 pm","permalink":"/programming/front-end/framework/engineering/iconfont/","dgPassFrontmatter":true}
---

- web 图标：https://www.w3cplus.com/css/web-icons.html

# Iconfont 字体图标

## Iconfont 介绍

+ 意义：使用字体用 HTML 代码以文本的形式直接在网页中画 icon 小图标。
+ 为什么使用 icon 字体图标: 使用图标字体可大大减少图标维护工作量。
+ 灵活性：轻松地改变图标的颜色或其他 CSS 效果。
+ 可扩展性：改变图标的大小，就像改变字体大小一样容易。
+ 矢量性：图标是矢量的，与像素无关。缩放图标不会影响清晰度。
+ 兼容性：字体图标支持所有现代浏览器（包括糟糕的 IE6）。
+ 本地使用：通过添加定制字体到你的本地系统，你可以在各种不同的设计和编辑应用程序中使用它们。

## Iconfont 使用 (本地没有 svg)

1. 目前国内用的最多的是阿里巴巴矢量库（http://www.iconfont.cn/）
2. 注册账户，把你想要的图标添加入库（购物车）
3. 点击购物车，将你想要的图标添加到项目中

### 三种使用方式

+ unicode 引用
+ symbol 引用
+ font-class 引用

## Iconfont 使用 (本地有 svg)

1. 图标字体: 使用 IcoMoon 将 SVG 格式的图标转换生成图标字体及样式
2. 进入 icoMoon 官网: https://icomoon.io/
3. 点击右上角 , 进入处理页面
4. 点击左上角 , 选择 resource\SVG\*.svg, 上传显示到页面
5. 在页面选择所有 svg, 点击右下角 生成图标字体样式
6. 点击左上角 指定 Font-Name 为 sell-icon, 点击右下角 下载到本地
7. 解压 zip 包, 访问 demo.html 测试
8. 我们项目需要的是 fonts 和 style.css

## 在页面中引入在线的 Iconfont 样式

+ `<link rel="stylesheet"href="http://at.alicdn.com/t/font_518606_6676bmcalnrhehfr.css">`

  ```react
  <i className='iconfont icon-play'></i>
  ```
