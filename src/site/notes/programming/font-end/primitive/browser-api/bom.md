---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/browser-api/bom/"}
---


浏览器对象模型

BOM 可以使我们通过 JS 来操作浏览器, 在 BOM 中为我们提供了一组对象，用来完成对浏览器的操作

# BOM 对象

- Window
  - 代表的是整个浏览器的窗口，同时 window 也是网页中的全局对象
  - window.onload = function(){};
  - //可以避免污染全局空间，这也是和 $() 的一个区别，不可以反复使用
- Navigator
  - 代表的当前浏览器的信息，通过该对象可以来识别不同的浏览器
- Location
  - 代表当前浏览器的地址栏信息，通过 Location 可以获取地址栏信息，或者操作浏览器跳转页面
- History
  - 代表浏览器的历史记录，可以通过该对象来操作浏览器的历史记录
  - 由于隐私原因，该对象不能获取到具体的历史记录，只能操作浏览器向前或向后翻页，而且该操作只在当次访问时有效
- Screen
  - 代表用户的屏幕的信息，通过该对象可以获取到用户的显示器的相关的信息
  - 移动端用的多因为屏幕差别大
  - 这些 BOM 对象在浏览器中都是作为 window 对象的属性保存的，可以通过 window 对象来使用，也可以直接使用

# Navigator

- 代表的当前浏览器的信息，通过该对象可以来识别不同的浏览器
- 由于历史原因，Netscape 网景公司，Navigator 对象中的大部分属性都已经不能帮助我们识别浏览器了
- 一般我们只会使用 userAgent 来判断浏览器的信息，userAgent 等价于浏览器
  - userAgent 是一个字符串，这个字符串中包含有用来描述浏览器信息的内容
- 不同的浏览器会有不同的 userAgent

## 火狐的 userAgent

- Mozilla/5.0 (Windows NT 6.1; WOW64; rv:50.0) Gecko/20100101 Firefox/50.0
  - Windows NT 6.1 就是 win7
  - WOW64 64 位操作系统
  - rv:50.0 版本 50
  - Gecko 火狐专门的 css 渲染引擎

## Chrome 的 userAgent

- Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.82 Safari/537.36

## IE8

- Mozilla/4.0 (compatible; **MSIE 8.0**; Windows NT 6.1; WOW64; Trident/7.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E)

## IE9

- Mozilla/5.0 (compatible; **MSIE 9.0**; Windows NT 6.1; WOW64; Trident/7.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E)

## IE10

- Mozilla/5.0 (compatible; **MSIE 10.0**; Windows NT 6.1; WOW64; Trident/7.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E)

## IE11

- Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E; rv:11.0) like Gecko
  - 在 IE11 中已经将微软和 IE 相关的标识都已经去除了，所以我们基本已经不能通过 UserAgent 来识别一个浏览器是否是 IE 了
  - 如果通过 UserAgent 不能判断，还可以通过一些浏览器中特有的对象，来判断浏览器的信息
  - 比如：ActiveXObject，但是由于用的太多，IE11 把 `!!window.ActiveXObject //fasle`
  - `“ActiveXObject“ in window //true`
  - ActiveXObject 兼容低版本 IE 的 ajax 请求

```js
if(/firefox/i.test(ua)){
  alert("你是火狐")
}else if (/chrome/i.test(ua)){
  alert("你是chrome")
}else if (/msie/i.test(ua)){
  alert("你是IE浏览器")
}else if ("ActiveXObject" in window){
  alert("IE11 funny")
}
```

# History

- 对象可以用来操作浏览器向前或向后翻页
- length
  - 属性，可以获取到当成访问的链接数量
- back()
  - 可以用来回退到上一个页面，作用和浏览器的回退按钮一样
- forward()
  - 可以跳转下一个页面，作用和浏览器的前进按钮一样
  - `history.forward();`
- go()
  - 可以用来跳转到指定的页面
  - 它需要一个整数作为参数
  - 1: 表示向前跳转一个页面 相当于 forward()
  - 2: 表示向前跳转两个页面
  - -1: 表示向后跳转一个页面
  - -2: 表示向后跳转两个页面
  - `history.go(-2);`

# Location

- 该对象中封装了浏览器的地址栏的信息
- 如果直接打印 location，则可以获取到地址栏的信息（当前页面的完整路径）
- 如果直接将 location 属性修改为一个完整的路径，或相对路径则我们页面会自动跳转到该路径，并且会生成相应的历史记录
- assign()
  - 用来跳转到其他的页面，作用和直接修改 location 一样
- reload()
  - 用于重新加载当前页面，作用和刷新按钮一样
  - 如果在方法中传递一个 true，作为参数，则会强制清空缓存刷新页面
  - 相当于 ctrl+f5
- replace()
  - 可以使用一个新的页面替换当前页面，调用完毕也会跳转页面
  - 不会生成历史记录，不能使用回退按钮回退
- location 对象是很特别的一个对象，因为它既是 window 对象的属性，也是 document 对象的属性；换句话说，window.location 和 document.location 引用的是同一个对象。

# Console

## console.log()

- 另一种格式化的： `console.log(msg, values)`，这很像 C 或 PHP 中的 `sprintf`

  ```js
  console.log('I like %s but I do not like %s.', 'Skittles', 'pus');
  ```

  ```
  I like Skittles but I do not like pus.
  ```

- 常见的占位符 `%o` (这是字母 o，不是 0)，它接受对象，`%s` 接受字符串，`%d` 表示小数或整数。
- 另一个有趣的是 `%c`，这可能与你所想不太相同，它实际上是 CSS 值的占位符。使用%c 占位符时，对应的后面的参数必须是 CSS 语句，用来对输出内容进行 CSS 渲染。常见的输出方式有两种：`文字样式、图片输出`。

  ```js
  console.log('I am a %cbutton', 'color: white; background-color: orange; padding: 2px 5px; border-radius: 2px');
  
  ```

- console.log(obj); 会输出对象的所有属性 //Object { name=“孙悟空”，age=18,b=true }
- 调用了对象的什么方法嗯？是浏览器的 API，浏览器自己决定
- `console.log` prints the element in an HTML-like tree
- `console.dir` prints the element in a JSON-like tree

## console.warn()

- 可能是最明显的直接替换 `log()`，你可以以完全相同的方式使用 `console.warn()`。 唯一真正的区别是输出字的颜色是黄色的。 具体来说，输出处于警告级别而不是信息级别，因此浏览器将稍微区别对待它。 这具有使其在杂乱输出中更明显的效果。
- <https://juejin.im/post/5ca6bf5151882543fc5e3bb0>

## console.time() 和 console.timeEnd()

```js
console.time('timer1');
// some code
console.timeEnd('timer1');
```

## console.trace()

# 定时器

delay 参数字符串形式的也是合理的

![image-20210107145133926](/img/user/programming/font-end/primitive/browser-api/bom/image-20210107145133926.png)

## setInterval()

可以将一个函数，每隔一段时间执行一次

参数：

1. 回调函数，该函数会每隔一段时间被调用一次
2. 每次调用间隔的时间，单位是毫秒

返回值：

- 返回一个 Number 类型的数据
- 这个数字用来作为定时器的唯一标识

## clearInterval()

可以用来关闭一个 setInterval() 循环定时器

方法中需要一个循环定时器的标识作为参数，这样将关闭标识对应的定时器

`clearInterval(timer)`

## setTimeout()

```js
  var timer = setTimeout(function(){console.log(num++);},3000);
```

延时调用一个函数不马上执行，而是隔一段时间以后在执行，而且只会执行一次

延时调用和定时调用的区别，定时调用会执行多次，而延时调用只会执行一次

延时调用和定时调用实际上是可以 **互相代替** 的，在开发中可以根据自己需要去选择

clearTimeout(timer)

**参数**

- function
- delay
- `param1, ..., paramN` ，附加参数，一旦定时器到期，它们会作为参数传递给 `function` @@@

## 定时器的注意点

- 目前，我们每点击一次按钮，就会开启一个定时器，点击多次就会开启多个定时器，这就导致图片的切换速度过快，并且我们只能关闭最后一次开启的定时器
- 在开启定时器之前，需要将当前元素上的其他定时器关闭

## 定时器和消息队列

第二个参数是一个表示等待多长时间的毫秒数，但经过该时间后指定的代码不一定会执行

JavaScript 是一个单线程序的解释器，因此一定时间内只能执行一段代码。为了控制要执行的代码，就有一个 JavaScript 任务队列。这些任务会按照将它们添加到队列的顺序执行

setTimeout() 的第二个参数告诉 JavaScript 再过多长时间把当前任务添加到队列中 (添加的过程是异步的)

如果队列是空的，那么添加的代码会立即执行；如果队列不是空的，那么它就要等前面的代码执行完了以后再执行

### 为什么要用 Timeout 代替 Interval?

考虑极端情况，假如定时器里面的代码需要进行大量的计算，或者是 DOM 操作。

这样一来，花的时间就比较长，有可能前一次代码还没有执行完，后一次代码就被添加到队列了。

假如时间间隔为 100 毫秒，要执行的代码需要 300 毫秒，如下图所示：

![](/img/user/programming/font-end/primitive/browser-api/bom/image-20230726193640030.png)

一开始执行 `setInterval`, 100 毫秒后将要执行的代码添加到队列。

100 毫秒时，执行代码进入队列，队列空闲，定时器内的代码执行。

200 毫秒时，第一次的定时器代码还在执行当中。第二次的定时器代码被推入事件队列，等待队列空闲，然后执行。

300 毫秒时，第一次的定时器代码还在执行中，第二次的定时器代码在事件队列末端等待执行。因为该定时器已经有第二次的代码在队列中等待了，所以这一次的代码不会被推入队列，被忽略了。

400 毫秒时，第一次的定时器代码执行完毕，队列空闲，下一个等待的代码执行，第二次的定时器代码开始执行。

捋一捋，这里的第一次的代码和第二次代码的间隔并没有预期的 100 毫米，而是第一次的执行完，第二次的立马执行了。因为第一的代码还没执行完，第二次的代码就已经在队列中等待了。

关于被忽略的第三次定时器代码，因为 300 毫秒时，这个定时器已经有第二次的代码在等待了，而只有当没有该定时器的代码在队列中时，该定时器新的代码才能去排队，所以第三次不会被添加到队列中。

由上可见，在这种极端情况下，`setIntreval` 实现不了需求

### 用 setTimeout 实现 setInterval

而 setTimeout 可以实现 setInterval 的功能，并且不会出现上面的情况。

```js
const repeat = (func, ms) => {
  setTimeout(() => {
    func()
    repeat(func, ms)
  }, ms)
}
```

上面的 repeat 接受两个参数，func 是要以间隔时间执行的函数，ms 代表间隔的毫秒数。

repeat 内部用 setTimeout 在指定的毫秒数 ms 之后，将匿名函数推入事件队列，匿名函数中包含要执行的 func , 以及 repeat(func, ms)。匿名函数执行时，先执行 func, 然后递归调用 repeat 来模拟 setInterval,递归调用的 repeat 中，又将执行 setTimeout, 在 ms 毫秒后，将下一次的定时器代码推入队列末端。

这里可以注意到，将下一次定时器代码推入队列时，上一次的代码无论如何都已经执行完了，所以不会重蹈 setInterval 的覆辙。

但是这种实现方式，不能清除定时器，还需要改造一下。

setTimeout 方式的改良

```js
function Timer() {
  this.timeID = null
  this.func = null
}

Timer.prototype.repeat = function(func, ms) {
  if (this.func === null) {
    this.func = func
  }

  // 确保一个 Timer 实例只能重复一个 func
  if (this.func !== func) {
    return
  }

  this.timeID = setTimeout(() => {
    func()
    this.repeat(func, ms)
  }, ms)
}

Timer.prototype.clear = function() {
  clearTimeout(this.timeID)
}

const a = () => console.log('a')

const b = () => console.log('b')

const timer = new Timer()

timer.repeat(a, 1000)
timer.repeat(b, 1000) // 不会定时执行 b
```

上面的代码定义了构造函数 Timer, 其产生的实例有两个属性，timeID 用来存储 setTimeout 返回的值，也就是定时器的 ID; func 则用来存储需要被定时执行的函数。

Timer 的 prototype 上定义了 repeat 方法，和之前的 repeat 函数大致一样。只是在开头，将传入的 func 存到了 this.func. repeat 递归调用时，每执行一次 setTimeout , this.timeID 就会取得最新的定时器的 ID.

Timer 的 prototype 上还定义了 clear 方法，用来清除定时器,。

注意到 repeat 和 clear 两个方法，定义时用的是函数声明，而不是匿名函数。之所以这样做，是为了保证方法内 this 指向 Timer 实例。如果用的是箭头函数，这两个方法里面的 this 将指向全局对象 Window, 更多和 this 有关的，可参考这篇博客。

接下来定义了两个简单的函数 a 和 b.

再 new 一个 Timer 赋值给变量 timer, timer 执行 repeat 方法定时运行 a 和 b .

可以发现，每隔大约一秒，就会执行一次 a, 控制台就会打印一次 a. 而 b 则不会定时重复执行。因为一个 Timer 实例只能重复执行一个函数。假如不作限制，a 和 b 重复，那么在 clear 的时候，只能清除掉最后添加进来的重复代码的定时器 ID.

假如要重复执行 b, 只能再实例化一个 Timer.

### 总结

因为 setInterval 在一些情况下，会导致前后两次定时器代码的执行间隔产生不可预料的变化，甚至会跳过某次定时器代码的执行，所以可用 setTimeout 实现 setInterval 的功能。

## 定时器和浏览器进程

考虑网页失焦和浏览器被切后台

如果做过秒杀倒计时的项目大概就不会起这个标题了吧。切后台或者多一级激活 webview 的时候 settimeout 会暂停执行，而 setinterval 不会。

## 倒计时的纠偏实现

在前端实现中我们一般通过 setTimeout 和 setInterval 方法来实现一个倒计时效果。但是使用这些方法会存在时间偏差的问题，这是由于 js 的程序执行机制造成的，setTimeout 和 setInterval 的作用是隔一段时间将回调事件加入到事件队列中，因此事件并不是立即执行的，它会等到当前执行栈为空的时候再取出事件执行，因此事件等待执行的时间就是造成误差的原因。

一般解决倒计时中的误差的有这样两种办法：

（1）第一种是通过前端定时向服务器发送请求获取最新的时间差，以此来校准倒计时时间。

（2）第二种方法是前端根据偏差时间来自动调整间隔时间的方式来实现的。这一种方式首先是以 setTimeout 递归的方式来实现倒计时，然后通过一个变量来记录已经倒计时的秒数。每一次函数调用的时候，首先将变量加一，然后根据这个变量和每次的间隔时间，我们就可以计算出此时无偏差时应该显示的时间。然后将当前的真实时间与这个时间相减，这样我们就可以得到时间的偏差大小，因此我们在设置下一个定时器的间隔大小的时候，我们就从间隔时间中减去这个偏差大小，以此来实现由于程序执行所造成的时间误差的纠正。

详细资料可以参考： [《JavaScript 前端倒计时纠偏实现》](https://juejin.im/post/5badf8305188255c8e728adc)

# Window 一些常用方法

alert()

confirm()

prompt() 返回的是用户输入的内容的 字符串形式

## eval() 函数

![es-function](programming/font-end/primitive/es/es-function.md#eval)

## open()

```js
let windowObjectReference = window.open(strUrl, strWindowName, [strWindowFeatures]);
```

`WindowObjectReference`: 打开的新窗口对象的引用。如果调用失败，返回值会是 `null 。如果` 父子窗口满足“[同源策略](https://developer.mozilla.org/cn/JavaScript的同源策略)”，你可以通过这个引用访问新窗口的属性或方法。

`strUrl`: 新窗口需要载入的 url 地址。strUrl 可以是 web 上的 html 页面也可以是图片文件或者其他任何浏览器支持的文件格式。

`strWindowName`: 新窗口的名称。该字符串可以用来作为 超链接 或表单元素的目标属性值。字符串中不能含有空白字符。注意：strWindowName 并不是新窗口的标题。

`strWindowFeatures`: 可选参数。是一个字符串值，这个值列出了将要打开的窗口的一些特性 (窗口功能和工具栏) 。 字符串中不能包含任何空白字符，特性之间用逗号分隔开。参考下文的 [位置和尺寸特征](<https://developer.mozilla.org/zh-CN/docs/Web/API/Window/open#Position> and size features)。

## escape,encodeURI,encodeURIComponent 有什么区别

[《escape,encodeURI,encodeURIComponent 有什么区别?》](https://www.zhihu.com/question/21861899)

# BOM 规范

缺少事实上的规范导致 BOM 有很多问题，因为浏览器提供商会按照各自的想法随意去扩展它。W3C 为了把浏览器中，JavaScript 最基本的部分标准化，已经将 BOM 的主要方面纳入了 HTML5 的规范中。

## Window 对象

- BOM 的核心对象是 window，它表示浏览器的一个实例。在浏览器中，window 对象有双重角色，
- 它既是通过 JavaScript 访问浏览器窗口的一个接口，又是 ECMAScript 规定的 **Global 对象**。
- 这意味着在网页中定义的任何一个对象、变量和函数，都以 window 作为其 Global 对象，因此有权访问 isNaN()、isFinite()、parseInt()、parseFloat() 等方法。

## 全局变量与 Windows 对象属性的差别

- 抛开全局变量会成为 window 对象的属性不谈，定义全局变量与在 window 对象上直接定义属性还是有一点差别：
- 全局变量不能通过 delete 运算符删除，而直接在 window 对象上的定义的属性可以

## window.open()/close 方法

- open 方法的四个参数：可以接收 4 个参数：要加载的 URL、窗口目标、一个 特性 字符 串、 一个表示新页面是否取代浏览器历史记录中当前加载页面的布尔值，后两个参数有很大的兼容性问题
- close() 方法仅适用于通过 window.open() 打开的弹出窗口。对于浏览器的主窗口，如果没有得到用户的允许是不能关闭它的。

# JSON

JSON：JavaScript Object Notation JS 对象表示法。JS 中的对象只有 JS 自己认识，其他的语言都不认识

JSON 就是一个特殊格式的字符串，这个字符串可以被任意的语言所识别，并且可以转换为任意语言中的对象，JSON 在开发中主要用来数据的交互

JSON 和 JS 对象的格式一样，只不过 **JSON 字符串** 中的 **属性名必须加双引号，本质是一个字符串，所以最外层整个再套一个双引号**

分类：

1. 对象 {}
2. 数组 []

JSON 中允许的值：

1. 字符串
2. 数值
3. 布尔值
4. null
5. 对象
6. 数组

在 JS 中，为我们提供了一个工具类，就叫 JSON，这个对象可以帮助我们将一个 JSON 转换为 JS 对象，也可以将一个 JS 对象转换为 JSON

## JSON.parse()

- 可以将以 JSON 字符串转换为 js 对象
- 它需要一个 JSON 字符串作为参数，会将该字符串转换为 JS 对象并返回

## JSON.stringify()

- 可以将一个 JS 对象转换为 JSON 字符串
- 需要一个 js 对象作为参数，会返回一个 JSON 字符串
- JSON 这个对象在 IE7 及以下的浏览器中不支持，所以在这些浏览器中调用时会报错

# Web 通信

## 概述

- web 通信（洋名：web messaging）是一种文档中独立的浏览上下文间的 DOM 不会被恶意的跨域脚本暴露数据分享方式。
  - web 通信是一种数据分享方式（有屁话之嫌）；
  - 通信的主体是“浏览上下文”（这是纳尼？）；
  - “浏览上下文”呢是“一个将 [Document](http://www.w3.org/html/ig/zh/wiki/HTML5/infrastructure#document) 对象呈现给用户的环境”，你可以近似理解为平常我们看到的某个页面所处的环境；
  - web 通信不会有 DOM 被恶意暴露的危险；
  - 目前应用比较多的就是 iframe 和 worker 之间的通信（这是我自个儿额外加的 ![挤眼](http://img.t.sinajs.cn/t35/style/images/common/face/ext/normal/c3/zy_thumb.gif)）
- 通常，对于两个不同页面的脚本，只有当执行它们的页面位于具有相同的协议（通常为 https），端口号（443 为 https 的默认值），以及主机 (两个页面的模数 [`Document.domain`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/domain) 设置为相同的值) 时，这两个脚本才能相互通信。
- 为了规避此限制，当我们谈论 web 通信的时候，实际上谈论的是两个略有不同的系统：**跨文档通信 (cross-document messaging)**和**通道通信 (channel messaging)**。
  - 跨文档通信就是我们国内更为熟知的 HTML5 window.postMessage() 应用的那种通信；
  - 通道通信也被称为”MessageChannel”. 伴随着 server-sent 事件以及 [web sockets](<http://dev.opera.com/articles/tags/web> sockets), 跨文档通信和通道通信成为 HTML5 通信接口“套件”中有用的一部分。
- 浏览器支持完美

## 通信事件

- 用来代表下列情况的消息
  - [Server-sent events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events) (参见 [`EventSource.onmessage`](https://developer.mozilla.org/zh-CN/docs/Web/API/EventSource/onmessage)).
  - [Web sockets](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) (参见 [WebSocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket) 接口的 `onmessage` 属性).
  - Cross-document messaging (参见 [`Window.postMessage()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/postMessage) 和 [`Window.onmessage`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/onmessage)).
  - [Channel messaging](https://developer.mozilla.org/en-US/docs/Web/API/Channel_Messaging_API) (参见 [`MessagePort.postMessage()`](https://developer.mozilla.org/zh-CN/docs/Web/API/MessagePort/postMessage) 和 [`MessagePort.onmessage`](https://developer.mozilla.org/zh-CN/docs/Web/API/MessagePort/onmessage)).
  - Cross-worker/document messaging (参见上面两个入口, 还有 [`Worker.postMessage()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Worker/postMessage), [`Worker.onmessage`](https://developer.mozilla.org/zh-CN/docs/Web/API/Worker/onmessage), [`ServiceWorkerGlobalScope.onmessage`](https://developer.mozilla.org/zh-CN/docs/Web/API/ServiceWorkerGlobalScope/onmessage), 等等.)
  - [Broadcast channels](https://developer.mozilla.org/en-US/docs/Web/API/Broadcast_Channel_API) (参见 [`Broadcastchannel.postMessage()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Broadcastchannel/postMessage)) 和 [`BroadcastChannel.onmessage`](https://developer.mozilla.org/zh-CN/docs/Web/API/BroadcastChannel/onmessage)).
  - WebRTC data channels (参见 [`RTCDataChannel.onmessage`](https://developer.mozilla.org/zh-CN/docs/Web/API/RTCDataChannel/onmessage)).

该事件包含 5 个只读属性：

  |    data     | 包含任意字符串数据，由原始脚本发送                           |
  | :---------: | ------------------------------------------------------------ |
  |   origin    | 一个字符串，包含原始文档的方案、域名以及端口 (如：<http://domain.example:80>) |
  | lastEventId | 一个字符串，包含了当前的消息事件的唯一标识符。               |
  |   source    | 原始文件的窗口的引用。更确切地说，它是一个 [WindowProxy对象](http://www.whatwg.org/specs/web-apps/current-work/multipage/browsers.html#windowproxy)。 |
  |    ports    | [`MessagePort`](https://developer.mozilla.org/zh-CN/docs/Web/API/MessagePort) 对象数组，表示消息正通过特定通道（数据通道）发送的相关端口（适用于通道消息传输或者向一个共享线程（shared work ）发送消息时）。 |

在跨文档通信和通道通信中，`lastEventId` 的值一般是个空字符串；`lastEventId` 应用在服务器端发送事件上。发送信息中如果没有 ports, 则 `ports` 属性值就是个长度为 0 的数组。

`MessageEvent` 继承 DOM[事件接口](http://dvcs.w3.org/hg/domcore/raw-file/tip/Overview.html#interface-event)，且属性共享。然而，通信事件并没有冒泡，不能取消，也没有默认行为。

## 获取窗口引用

### Iframe 窗口

**contentWindow**

- **contentWindow** 属性返回 `<iframe>` 元素的 Window 对象。你可以使用这个 Window 对象来访问 iframe 的文档及其内部 DOM。
- contentWindow 为只读，但是可以像操作全局 Window 对象一样操作其属性。

**Window.frames**

- 返回当前窗口，一个类数组对象，列出了当前窗口的所有直接子窗口。
- 在 window.frames 类数组中的每一项都代表了窗口对应给定对象的 `<frame>` 或 `<iframe>` 的内容，而不是 `(i)frame` DOM 元素
- 即 `window.frames[ 0 ]` 与 `document.getElementsByTagName( "iframe" )[ 0 ].contentWindow` 是相同的）

### Open 窗口

- `subWindow = window.open(url,name)`
- `sourceWindow = window.opener`
- ```js
  window.addEventListener('load', function(e){
      e.currentTarget.opener.postMessage('ready', 'https://www.zhangxinxu.com');
  });
  ```

### messageEvent.sorce

- 获取消息发送方的引用，用来回信

### Worker

## window.postMessage

- 从广义上讲，一个窗口可以获得对另一个窗口的引用（比如 `targetWindow = window.opener`），然后在窗口上调用 `targetWindow.postMessage()` 方法分发一个 [`MessageEvent`](https://developer.mozilla.org/zh-CN/docs/Web/API/MessageEvent) 消息。接收消息的窗口可以根据需要自由 [处理此事件](https://developer.mozilla.org/zh-CN/docs/Web/Guide/Events)。传递给 window.postMessage() 的参数（比如 message ）将 [通过消息事件对象暴露给接收消息的窗口](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/postMessage#The_dispatched_event)。

### 语法

- ```
  otherWindow.postMessage(message, targetOrigin, [transfer]);
  ```

**otherWindow**

- 其他窗口的一个引用，比如 iframe 的 contentWindow 属性、执行 [window.open](https://developer.mozilla.org/en-US/docs/DOM/window.open) 返回的窗口对象、或者是命名过或数值索引的 [window.frames](https://developer.mozilla.org/en-US/docs/DOM/window.frames)。

**message**

- 将要发送到其他 window 的数据。它将会被 [结构化克隆算法](https://developer.mozilla.org/en-US/docs/DOM/The_structured_clone_algorithm) 序列化。这意味着你可以不受什么限制的将数据对象安全的传送给目标窗口而无需自己序列化。[[1](https://developer.mozilla.org/en-US/docs/)]

**targetOrigin**

- 通过窗口的 origin 属性来指定哪些窗口能接收到消息事件，其值可以是字符串 "*"（表示无限制）或者一个 URI。
- 在发送消息的时候，如果目标窗口的协议、主机地址或端口这三者的任意一项不匹配 targetOrigin 提供的值，那么消息就不会被发送；只有三者完全匹配，消息才会被发送。这个机制用来控制消息可以发送到哪些窗口；
- 例如，当用 postMessage 传送密码时，这个参数就显得尤为重要，必须保证它的值与这条包含密码的信息的预期接受者的 origin 属性完全一致，来防止密码被恶意的第三方截获。**如果你明确的知道消息应该发送到哪个窗口，那么请始终提供一个有确切值的 targetOrigin，而不是\*。不提供确切的目标将导致数据泄露到任何对数据感兴趣的恶意站点。**

  > 您还可以通过使用 `"/"` 来限制信息只能同源发送
  >
  > 指定来源的时候，后面不要带上斜杠

  ```js
  window.postMessage('发送信息。','http://example.zhangxinxu.com');
  // 下面是错误示范
  window.postMessage('发送信息。','http://example.zhangxinxu.com/');
  ```

**transfer**

- 可选
- 是一串和 message 同时传递的 [`Transferable`](https://developer.mozilla.org/zh-CN/docs/Web/API/Transferable) 对象. 这些对象的所有权将被转移给消息的接收方，而发送一方将不再保有所有权。

### 示例

- iframe 间通信，关键是要获取到窗口的引用
- 发送方关键代码

  ```js
  window.parent.frames[1].postMessage(message, '*');
  ```

- 接收方关键代码

  ```js
  window.addEventListener("message", messageHandle)
  
  function messageHandle(e) {
      eleBox.innerHTML = '接受到的信息是：' + e.data;
  }
  ```

### 安全问题

- **如果您不希望从其他网站接收 message，请不要为 message 事件添加任何事件侦听器。** 这是一个完全万无一失的方式来避免安全问题。
- 如果您确实希望从其他网站接收 message，请 **始终使用 origin 和 source 属性验证发件人的身份**。 任何窗口（包括例如<http://evil.example.com>）都可以向任何其他窗口发送消息，并且您不能保证未知发件人不会发送恶意消息。 但是，验证身份后，您仍然应该 **始终验证接收到的消息的语法**。 否则，您信任只发送受信任邮件的网站中的安全漏洞可能会在您的网站中打开跨网站脚本漏洞。
- **当您使用 postMessage 将数据发送到其他窗口时，始终指定精确的目标 origin，而不是\*。** 恶意网站可以在您不知情的情况下更改窗口的位置，因此它可以拦截使用 postMessage 发送的数据。
- 用于接收消息的任何事件监听器 **必须** 首先使用 origin 和 source 属性来检查消息的发送者的身份。 **这不能低估：无法检查 origin 和 source 属性会导致跨站点脚本攻击。**

## MessageChannel API

### 背景

- 消息通道提供了一个直接，双向浏览上下文之间的通信手段。跟跨文档通信一样，DOM 不直接暴露。取而代之，管道每端为端口，数据从一个端口发送，另一个变成输入（反之亦然）。
- 消息通道是有用的，特别是跨多个起源的沟通。请考虑以下情形：人人网上 (<http://renren.com) 嵌入了一个第三方的游戏页面（通过 iframe 的形式，如“人人餐厅”），同时，这个第三方的游戏页面 (http://game.com) 又需要从另外一个通讯录网站 (http://address.com>) 获取用户的通讯信息。咋办？
- 也就是说通讯录站点要发送信息给游戏站点，根据跨文档通信，我们让父页面作为代理（也就是这里的人人网页面）（类似第一个 demo）。然而，这种做法意味着通讯录站点需要有和人人网页面一样的信任级别。人人网这个社交站点需要信任每一个请求，或者为我们过滤（应该指：一个一个指定）。
- 但是，使用渠道通信，通讯录站点 (<http://address.com) 和游戏站点 (http://game.com>) 可以直接沟通。
- 是一种点对点的通信方式
- Channel Messaging API 的 [Channel Messaging](https://developer.mozilla.org/en-US/docs/Web/API/Channel_Messaging_API) 接口允许我们创建一个新的消息通道，并通过它的两个 [`MessagePort`](https://developer.mozilla.org/zh-CN/docs/Web/API/MessagePort) 属性发送数据。
- 此特性在 [Web Worker](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API) 中可用。

### MessageChannel()

- The `MessageChannel()` constructor of the [`MessageChannel`](https://developer.mozilla.org/zh-CN/docs/Web/API/MessageChannel) interface returns a new [`MessageChannel`](https://developer.mozilla.org/zh-CN/docs/Web/API/MessageChannel) object with two new [`MessagePort`](https://developer.mozilla.org/zh-CN/docs/Web/API/MessagePort) objects.
- ```
  var channel = new MessageChannel();
  ```
- 当我们创建了一个 `MessageChannel` 对象，我们实际上创造了两个相互关联的端口。
- 我们把其中一个端口转发到其他浏览上下文，两个浏览上下文就可以通过这两个端口进行通信

### MessagePort 对象

- 每一个端口就是一个 `MessagePort` 对象，包含 3 个可用方法：

  | postMessage() | 通过通道发送消息           |
  | :-----------: | -------------------------- |
  |    start()    | 开始在端口上分派接受的信息 |
  |    close()    | 关闭端口                   |

- `MessagePort` 对象还有 `onmessage` 事件属性，可被用来定义事件句柄

### 转发端口

- 通过 `window.postMessage` 转发，第三个参数是一个 `Transferable` 对象
- `Transferable` 接口代表一个能在不同可执行上下文中相互传递的对象，列如主线程和 [`Worker`](https://developer.mozilla.org/zh-CN/docs/Web/API/Worker) 间。
- 这是一个抽象接口且没有任何对象属于此类型。它也没有定义任何方法和属性；它只是一个标签，用来指示对象在特定场合可用，如通过 [`Worker.postMessage()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Worker/postMessage) 方法传递到 [`Worker`](https://developer.mozilla.org/zh-CN/docs/Web/API/Worker) 。
- [`ArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/API/ArrayBuffer) 、[`MessagePort`](https://developer.mozilla.org/zh-CN/docs/Web/API/MessagePort) 和 [`ImageBitmap`](https://developer.mozilla.org/zh-CN/docs/Web/API/ImageBitmap) 实现于此接口。

### 示例

- ```js
  const ch = new MessageChannel()
  const port1 = ch.port1
  const port2 = ch.port2
  
  port1.onmessage = function(d) {
      console.log(`port1接收的消息是：${d.data}`)
  }
  
  port2.onmessage = function(d) {
      console.log(`port2接收的消息是：${d.data}`)
  }
  
  // 发送消息
  port1.postMessage('port1发送的消息')
  port2.postMessage('port2发送的消息')
  ```
- 最常见的就是 iframes 之间通信了，比如我有一个页面，里面嵌入了 iframeA 和 iframeB，两个 iframe 之间通信我们可以通过 top window 来传送 port，然后两个子页面就可以直接对话了。

  ```js
  // frameA
  const mc = new MessageChannel()
  window.parent.postMessage(1, 'http://localhost:8080', [mc.port1])
  
  // top window
  window.addEventListener('message', (eve) => {
      window.frames[1].postMessage(1, 'http://localhost:8081', [eve.ports[0]])
  })
  
  // frameB
  var port
  
  window.addEventListener('message', (eve) => {
      port = eve.ports[0]
  })
  
  btn.onclick = () => {
      port.postMessage({message: 'hi b, i am a'})
  }
  ```

### 缓冲

- 是先执行了 postMessage，在去添加 onmessage 监听，为什么还能接受到消息呢？我们再激进一点

  ```js
  const mc = new MessageChannel()
  const { port1, port2 } = mc
  
  port1.postMessage(123)
  setTimeout(() => {
      port2.onmessage = (eve) => {
          console.log('received message: ', eve.data);
      }
  }, 1000);
  
  // 123
  ```

- 数据依然可以被显示。我没有去查证，推测这里应该有一个缓冲区的概念，当数据被 post 以后，先存在缓冲区，当 onmessage 监听器一旦绑定就消费这些数据。

## BroadcastChannel API

- 从名字可以看出来，这是一个广播形式的通道，所以就不是点对点了，而是类似于发布订阅的一种形式，只要有人订阅了我的消息，就可以接收到消息
- ```js
  const bc = new BroadcastChannel('channel1')
  const bc2 = new BroadcastChannel('channel1')
  
  bc2.onmessage = (eve) => {
      console.log('bc:', eve.data);
  }
  
  setTimeout(() => {
      bc.postMessage({message: 'hello'})
  }, 1000);
  
  // {message: 'hello'}
  ```
- 因为是广播，自然也不存在缓冲区的概念了，你错过了就没了，所以上面那个例子，先 postMessage 再绑定 onmessage 就接收不到消息了。
- BroadcaChannel 用在多个子页面监听父页面的情况下还是很好用的。
- 2019/7/19

  ![1563517626731](/img/user/programming/font-end/primitive/browser-api/bom/1563517626731.png)

# 其他

## **Geolocation**

- **Geolocation.getCurrentPosition()** 方法用来获取设备当前位置。
- **Geolocation.watchPosition()** 用于注册监听器，在设备的地理位置发生改变的时候自动被调用。也可以选择特定的错误处理函数。

# FAQ

#faq/js

## 使用 Settimeout 模拟 Setinterval

```js
(function foo() {
    ...
    setTimeout(foo, delay);
})();
```

同时也是一个 **立即执行** 的 setInterval
