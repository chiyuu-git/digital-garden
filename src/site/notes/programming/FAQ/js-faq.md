---
{"dg-publish":true,"permalink":"/programming/faq/js-faq/"}
---


2023-02-06

# Global FAQ Collection

| File                                                                           | overview              |
| ------------------------------------------------------------------------------ | --------------------- |
| [[programming/font-end/primitive/browser-api/dom-selection\|dom-selection]] | [[programming/font-end/primitive/browser-api/dom-selection#faq\|dom-selection#faq]] |
| [[programming/font-end/primitive/es/es-date\|es-date]]                      | [[programming/font-end/primitive/es/es-date#faq\|es-date#faq]]       |
| [[programming/font-end/primitive/es/es-proto\|es-proto]]                    | [[programming/font-end/primitive/es/es-proto#faq\|es-proto#faq]]      |
| [[programming/font-end/primitive/browser-api/dom-interface\|dom-interface]] | [[programming/font-end/primitive/browser-api/dom-interface#faq\|dom-interface#faq]] |
| [[programming/font-end/primitive/es/es-array\|es-array]]                    | [[programming/font-end/primitive/es/es-array#faq\|es-array#faq]]      |
| [[programming/font-end/primitive/es/es-async-2\|es-async-2]]                | [[programming/font-end/primitive/es/es-async-2#faq\|es-async-2#faq]]    |
| [[programming/font-end/primitive/es/es-number\|es-number]]                  | [[programming/font-end/primitive/es/es-number#faq\|es-number#faq]]     |
| [[programming/font-end/primitive/es/es-regexp\|es-regexp]]                  | [[programming/font-end/primitive/es/es-regexp#faq\|es-regexp#faq]]     |
| [[programming/font-end/primitive/browser-api/bom\|bom]]                     | [[programming/font-end/primitive/browser-api/bom#faq\|bom#faq]]           |
| [[programming/font-end/primitive/es/es-basic\|es-basic]]                    | [[programming/font-end/primitive/es/es-basic#faq\|es-basic#faq]]      |
| [[programming/font-end/primitive/es/es-object\|es-object]]                  | [[programming/font-end/primitive/es/es-object#faq\|es-object#faq]]     |
| [[programming/font-end/primitive/es/es-string\|es-string]]                  | [[programming/font-end/primitive/es/es-string#faq\|es-string#faq]]     |

{ .block-language-dataview}

# 工具函数

## 字符串和数组的转换

String.prototype.split("")

Array.prototype.join("")

```js
[...'hello']
// [ "h", "e", "l", "l", "o" ]

function _toConsumableArray(arr) { 
  if (Array.isArray(arr)) { 
    for (var i = 0, arr2 = Array(arr.length); i < arr.length; i++) { 
      arr2[i] = arr[i]; 
    } 
    return arr2; 
  } else { 
    return Array.from(arr); 
  } 
}

[].concat(_toConsumableArray('hello'));
```

```js
const string = '1312312'

console.log(Array.from(string));
```

Array.prototype.toString

Array.from()，字符串也部署了 iterator 接口

自然，扩展运算符也是可以的，默认调用 iterator 接口 `[...str]`

## 互换 交换 Swap 函数

临时变量法

```js
// 仅适用于数值
a = a + b;
b = a - b;
a = a - b;

a = a - b;
b = a + b;
a = b - a;

// 全部均可
// 最快速度，位操作
a ^= b;
b ^= a;
a ^= b;

a = (b^=a^=b)^a;

a = {a:b,b:a};
b = a.b;
a = a.a;

a = [a,b];
b = a[0];
a = a[1];

// 最巧妙
a = [b,b=a]0];
// ES6
[a,b] = [b,a];
```

[344. 反转字符串 - 力扣（LeetCode）](https://leetcode.cn/problems/reverse-string/submissions/)

双指针比负 index 效率更高, 因为不需要运算

```js
/**
 * @param {character[]} s
 * @return {void} Do not return anything, modify s in-place instead.
 */
var reverseString = function(s) {
    // 直接循环交换就可以了
    // for (let i = 0; i < (s.length + 1) / 2 - 1; i++) {
    //     // 交换的焦虑不高哇
    //     // [s[s.length -1 -i], s[i]] = [s[i], s[s.length - 1 - i]]
    //     // 交换的效率是一样的
    //     // const temp = s[i];
    //     // s[i] = s[s.length - 1 - i]
    //     // s[s.length - 1 - i] = temp;
    // }
    for (let left = 0, right = s.length - 1; left < right; left++, right--) {
        [s[right], s[left]] = [s[left], s[right]]
    }
    return s
};

```

## String Template One Line

```js
let message = `
	Hi,
	Daisy!
	I am
	Kevin.
`;
```

出于可读性或者其他原因，我希望书写的时候是换行的，但是最终输出的字符是在一行，这就需要借助模板标签来实现了，我们尝试写一个这样的函数：

```js
// oneLine 第一版
function oneLine(template, ...expressions) {
    let result = template.reduce((prev, next, i) => {
        let expression = expressions[i - 1];
        return prev + expression + next;
    });
    result = result.replace(/(\s+)/g, " ");
    result = result.trim();
    return result;
}
```

实现原理很简单，拼合回去然后将多个空白符如换行符、空格等替换成一个空格。

使用如下：

```js
let message = oneLine `
    Hi,
    Daisy!
    I am
    Kevin.
`;
console.log(message); // Hi, Daisy! I am Kevin.
```

不过你再用下去就会发现一个问题，如果字符间就包括多个空格呢？举个例子：

```js
let message = oneLine`
  Preserve eg sentences.  Double
  spaces within input lines.
`;
```

我们可以再优化一下，我们想要的效果是将每行前面的多个空格替换成一个空格，其实应该匹配的是换行符以及换行符后面的多个空格，然后将其替换成一个空格，我们可以将正则改成：

```js
result = result.replace(/(\n\s*)/g, " ");
```

就可以正确的匹配代码。最终的代码如下：

```js
// oneLine 第二版
function oneLine(template, ...expressions) {
    let result = template.reduce((prev, next, i) => {
        let expression = expressions[i - 1];
        return prev + expression + next;
    });

    result = result.replace(/(\n\s*)/g, " ");
    result = result.trim();

    return result;
}
```

## 通用遍历方法 Each 的实现

### Each 介绍

jQuery 的 each 方法，作为一个通用遍历方法，可用于遍历对象和数组。

语法为：

```
jQuery.each(object, [callback])
```

回调函数拥有两个参数：第一个为对象的成员或数组的索引，第二个为对应变量或内容。

```js
// 遍历数组
$.each( [0,1,2], function(i, n){
    console.log( "Item #" + i + ": " + n );
});

// Item #0: 0
// Item #1: 1
// Item #2: 2
// 遍历对象
$.each({ name: "John", lang: "JS" }, function(i, n) {
    console.log("Name: " + i + ", Value: " + n);
});
// Name: name, Value: John
// Name: lang, Value: JS
```

### 退出循环

尽管 ES5 提供了 forEach 方法，但是 forEach 没有办法中止或者跳出 forEach 循环，除了抛出一个异常。但是对于 jQuery 的 each 函数，如果需要退出 each 循环可使回调函数返回 false，其它返回值将被忽略。

```js
$.each( [0, 1, 2, 3, 4, 5], function(i, n){
    if (i > 2) return false;
    console.log( "Item #" + i + ": " + n );
});

// Item #0: 0
// Item #1: 1
// Item #2: 2
```

### 分析

首先，我们肯定要根据参数的类型进行判断，如果是数组，就调用 for 循环，如果是对象，就使用 for in 循环，有一个例外是类数组对象，对于类数组对象，我们依然可以使用 for 循环。

中止循环的功能。

```js
if (callback(i, obj[i]) === false) {
    break;
}
```

指定 this，我们可以使用 call 或者 apply，其实也很简单：

```js
if (callback.call(obj[i], i, obj[i]) === false) {
    break;
}
```

### 实现

```js
function each(obj, callback) {
  var length, i = 0;

  if (isArrayLike(obj)) {
    length = obj.length;
    for (; i < length; i++) {
      if (callback.call(obj[i], i, obj[i]) === false) {
        break;
      }
    }
  } else {
    for (i in obj) {
      if (callback.call(obj[i], i, obj[i]) === false) {
        break;
      }
    }
  }

  return obj;
}
```

each 函数和 eachWithCall 函数唯一的区别就是 eachWithCall 调用了 call，从结果我们可以推测出，call 会导致性能损失，但也正是 call 的存在，我们才能将 this 指向循环中当前的元素。

现在完全可以用 `for...of` 代替

# 防抖

防抖的原理就是：你尽管触发事件，但是我一定在事件触发 n 秒后才执行，如果你在一个事件触发的 n 秒内又触发了这个事件，那我就以新的事件的时间为准，n 秒后才执行，总之，就是要等你触发完事件 n 秒内不再触发事件，我才执行，真是任性呐!

## 初步实现

```js
function debounce(func,wait){
  let timeout
  return function(){
    clearTimeout(timeout)
    timeout = setTimeout(func,wait)
  }
}
```

## 完善

this 的指向问题，因为涉及到了定时器

```js
  let count = 1;
  const container = document.getElementById('container');

  function getUserAction() {
      container.innerHTML = count++;
      console.log(this)
  };

  function debounce(func,wait){
    let timeout
    return function(){
      clearTimeout(timeout)
      timeout = setTimeout(() => {
        func.call(this)
      },wait)
    }
  }

  container.onmousemove = debounce(getUserAction,1000);
```

不使用箭头函数的话，需要主动绑定 this

```js
  function debounce(func,wait){
    let timeout
    return function(){
      const ctx = this // 事件回调函数指向正确的this
      clearTimeout(timeout)
      timeout = setTimeout(function() {
        func.call(ctx) // 调用call和apply方法的同时会执行函数，所以必须包裹在一个匿名函数内
      },wait)
    }
  }
```

事件 event 对象的缺失

```js
  function debounce(func,wait){
    let timeout
    return function(ev){
      clearTimeout(timeout)
      timeout = setTimeout(() => {
        func.call(this,ev)
      },wait)
    }
  }
```

关键是意识到真正作为回调函数的是 debounce 函数的返回值，所以这个函数有着正确的 this 指向和传入的参数

## 需求

### 立即触发

- 希望第一次可以立即触发函数，如果频繁触发，则需要在停止触发 n 秒后才可以继续触发

  ```js
  function debounce(func, wait, immediate) {
  
    var timeout;
    var first  = true
    var callNow = false
    return function () {
      var context = this;
      var args = arguments;
  
      if (timeout) clearTimeout(timeout);
      if (immediate) {
        timeout = setTimeout(function(){
          callNow = true;
        }, wait)
        // 保证第一次执行，和之后可以重复执行
        if (first || callNow){
          func.apply(context, args)
          first = false
          callNow = false
        }
      }
      else {
        timeout = setTimeout(function(){
          func.apply(context, args)
        }, wait);
      }
    }
  }
  ```

- 利用定时器的返回值的特性，减少变量

  ```js
  function debounce(func, wait, immediate) {
  
    var timeout;
  
    return function () {
      var context = this;
      var args = arguments;
  
      if (timeout) clearTimeout(timeout);
      if (immediate) {
        // timeout的值不为0，说明开启了定时器，不可执行
        var callNow = !timeout;
        // 只要在wait时间内不再触发事件，timeout会从一个数字（定时器编号）变成null
        timeout = setTimeout(function(){
          timeout = null;
        }, wait)
        if (callNow) func.apply(context, args)
      }
      else {
        timeout = setTimeout(function(){
          func.apply(context, args)
        }, wait);
      }
    }
  }
  ```

### 取消防抖

- 希望能取消 debounce 函数，比如说我 debounce 的时间间隔是 10 秒钟，immediate 为 true，这样的话，我只有等 10 秒后才能重新触发事件，现在我希望有一个按钮，点击后，取消防抖，这样我再去触发，就可以又立刻执行啦

  ```js
  function debounce(func, wait, immediate) {
  
    var timeout, result;
  
    var debounced = function () {
      var context = this;
      var args = arguments;
  
      if (timeout) clearTimeout(timeout);
      if (immediate) {
        // 如果已经执行过，不再执行
        var callNow = !timeout;
        timeout = setTimeout(function(){
          timeout = null;
        }, wait)
        if (callNow) result = func.apply(context, args)
      }
      else {
        timeout = setTimeout(function(){
          func.apply(context, args)
        }, wait);
      }
      return result;
    };
  
    debounced.cancel = function() {
      clearTimeout(timeout);
      timeout = null;
    };
  
    return debounced;
  }
  ```

- 使用

  ```js
  var count = 1;
  var container = document.getElementById('container');
  
  function getUserAction(e) {
    container.innerHTML = count++;
  };
  
  var setUseAction = debounce(getUserAction, 10000, true);
  
  container.onmousemove = setUseAction;
  
  document.getElementById("button").addEventListener('click', function(){
    setUseAction.cancel();
  })
  ```

## 返回值

虽然说一般事件绑定函数是没有返回值的，因为传入的是一个回调函数，想要获得回调函数的处理结果需要再传入一个回调函数接受这个结果，但是这样没办法再 immediate 为 false 的时候获得返回值，因为定时器的 this 指向 window，且不接受额外的回调函数，最好的方法还是使用 promise

## 对比

前面的防抖表现为：停止一段时间后，由定时器触发

需求内的防抖变成了：立即执行，停止一段时间后，才可以再去触发

后一种情况才是实际中更加多见的吧

## 防抖应用场景

1. 搜索框输入查询，如果用户一直在输入中，没有必要不停地调用去请求服务端接口，等用户停止输入的时候，再调用，设置一个合适的时间间隔，有效减轻服务端压力。
2. 表单验证
3. 按钮提交事件。
4. 浏览器窗口缩放，resize 事件等。

# 节流

节流的原理很简单：如果你持续触发事件，每隔一段时间，只执行一次事件。

根据首次是否执行以及结束后是否执行，效果有所不同，实现的方式也有所不同。我们用 leading 代表首次是否执行，trailing 代表结束后是否再执行一次。

关于节流的实现，有两种主流的实现方式，一种是使用时间戳，一种是设置定时器。

## 使用时间戳

使用时间戳，当触发事件的时候，我们取出当前的时间戳，然后减去之前的时间戳 (最一开始值设为 0 )，如果大于设置的时间周期，就执行函数，然后更新时间戳为当前的时间戳，如果小于，就不执行。

```js
let count = 1;
const container = document.getElementById('container');

function getUserAction(ev) {
  container.innerHTML = count++;
  // console.log(this)
  console.log(ev)
};

function throttle (func,wait){
  let ctx
  let prev = 0

  return function(...args){
    // 返回的是时间戳对象，先转换为数字
    let now = +new Date()
    ctx = this
    if(now - prev > wait){
      func.apply(ctx,args)
      prev = now
    }
  }

}

container.onmousemove = throttle(getUserAction,1000);
```

当鼠标移入的时候，事件立刻执行，每过 1s 会执行一次，如果在 4.2s 停止触发，以后不会再执行事件。

## 使用定时器

当触发事件的时候，我们设置一个定时器，**再** 触发事件的时候，如果定时器存在，就不执行，直到定时器执行，然后执行函数，清空定时器，这样就可以设置下个定时器。

```js
function throttle(func, wait) {
  var timeout;

  return function() {
    context = this;
    args = arguments;
    // timeout 为 null 的时候会启动一个定时器
    // 除非定时器执行，否则无法开启下一个定时器
    if (!timeout) {
      timeout = setTimeout(function(){
        timeout = null;
        func.apply(context, args)
      }, wait)
    }
  }
}
```

当鼠标移入的时候，事件不会立刻执行，晃了 3s 后终于执行了一次，此后每 3s 执行一次，当数字显示为 3 的时候，立刻移出鼠标，相当于大约 9.2s 的时候停止触发，但是依然会在第 12s 的时候执行一次事件。

所以比较两个方法：

1. 第一种事件会立刻执行，第二种事件会在 n 秒后第一次执行
2. 第一种事件停止触发后没有办法再执行事件，第二种事件停止触发后依然会再执行一次事件

## 各取所长

可能会有这样的需求：就是鼠标移入能立刻执行，停止触发的时候还能再执行一次

```js
function throttle(func, wait) {
  var timeout, context, args, result;
  var previous = 0;

  var throttled = function () {
    var now = +new Date();
    //下次触发 func 剩余的时间
    var remaining = wait - (now - previous);
    context = this;
    args = arguments;
    // 如果没有剩余的时间了或者你改了系统时间
    if (remaining <= 0 || remaining > wait) {
      if (timeout) {
        // 防止重复触发，剩余时间小于0，说明定时器马上就要执行了
        // 由于事件循环机制可能造成的误差
        clearTimeout(timeout);
        timeout = null;
        console.log('in timeout')
      }
      // 头执行
      previous = now;
      func.apply(context, args);
    } else if (!timeout) {
      // 中间节流和尾执行 调用 later 传入剩余时间
      // 还有剩余时间，且上一次设置的定时器已经执行完毕
      // 频繁触发时 remaining 都是差不多的值 990+
      timeout = setTimeout(later, remaining);
      console.log(remaining)
    }
  };

  var later = function () {
    // 本来 时间戳 部分的逻辑 也可以做到中间节流的，但是每次都被later抢先了
    previous = +new Date();
    timeout = null;
    func.apply(context, args)
  };

  return throttled;
}
```

但是我有时也希望无头有尾，或者有头无尾，这个咋办？

那我们设置个 options 作为第三个参数，然后根据传的值判断到底哪种效果，我们约定:

leading：false 表示禁用第一次执行

trailing: false 表示禁用停止触发的回调

```js
function throttle(func, wait, options) {
  var timeout, context, args, result;
  var previous = 0;
  if (!options) options = {};

  var later = function() {
    // 不需要leading，previous为0，需要leading，previous为 时间戳
    previous = options.leading === false ? 0 : new Date().getTime();
    timeout = null;
    func.apply(context, args);
    // 防止内存泄漏
    context = args = null;
  };

  var throttled = function() {
    var now = new Date().getTime();
    // previous为0 不需要leading
    if (!previous && options.leading === false) previous = now;
    var remaining = wait - (now - previous);
    context = this;
    args = arguments;
    if (remaining <= 0 || remaining > wait) {
      if (timeout) {
        clearTimeout(timeout);
        timeout = null;
      }
      previous = now;
      func.apply(context, args);
      if (!timeout) context = args = null;
    } else if (!timeout && options.trailing !== false) {
      timeout = setTimeout(later, remaining);
    }
  };
  return throttled;
}
```

https://github.com/mqyqingfeng/Blog/issues/26

## 应用场景

1. 按钮点击事件
2. 拖拽事件
3. onScoll
4. 计算鼠标移动的距离 (mousemove)

# 函数库骨架

## 自己实现

如果是我们自己去组织这些函数，我们该怎么做呢？我想我会这样做：

```js
(function(){
  var root = this;

  var _ = {};

  root._ = _;

  // 在这里添加自己的方法
  _.reverse = function(string){
    return string.split('').reverse().join('');
  }

})()

_.reverse('hello');
=> 'olleh'
```

我们将所有的方法添加到一个名为 `_` 的对象上，然后将该对象挂载到全局对象上。

之所以不直接 `window._ = _` 是因为我们写的是一个工具函数库，不仅要求可以运行在浏览器端，还可以运行在诸如 Node 等环境中。

## Root

然而 underscore 可不会写得如此简单，我们从 `var root = this` 开始说起。

之所以写这一句，是因为我们要通过 this 获得全局对象，然后将 `_` 对象，挂载上去。

然而在严格模式下，this 返回 undefined，而不是指向 Window，幸运的是 underscore 并没有采用严格模式，可是即便如此，也不能避免，因为在 ES6 中模块脚本自动采用严格模式，不管有没有声明 `use strict`。

如果 this 返回 undefined，代码就会报错，所以我们的思路是对环境进行检测，然后挂载到正确的对象上。我们修改一下代码：

```js
var root = (typeof window == 'object' && window.window == window && window) ||
           (typeof global == 'object' && global.global == global && global);
```

在这段代码中，我们判断了浏览器和 Node 环境，可是只有这两个环境吗？那我们来看看 Web Worker。

## Web Worker

Web Worker 属于 HTML5 中的内容，引用《JavaScript 权威指南》中的话就是：

> 在 Web Worker 标准中，定义了解决客户端 JavaScript 无法多线程的问题。其中定义的 “worker” 是指执行代码的并行过程。不过，Web Worker 处在一个自包含的执行环境中，无法访问 Window 对象和 Document 对象，和主线程之间的通信业只能通过异步消息传递机制来实现。

在 Web Worker 中，是无法访问 Window 对象的，所以 `typeof window` 和 `typeof global` 的结果都是 `undefined`，所以最终 root 的值为 false，将一个基本类型的值像对象一样添加属性和方法，自然是会报错的。

虽然在 Web Worker 中不能访问到 Window 对象，但是我们却能通过 ` self ` 访问到 Worker 环境中的全局对象。我们只是要找全局变量挂载而已，所以完全可以挂到 self 中嘛。

而且在浏览器中，除了 window 属性，我们也可以通过 self 属性直接访问到 Winow 对象。

```js
console.log(window.window === window); // true
console.log(window.self === window); // true
```

考虑到使用 self 还可以额外支持 Web Worker，我们直接将代码改成 self：

```js
var root = (typeof self == 'object' && self.self == self && self) ||
           (typeof global == 'object' && global.global == global && global);
```

## Node Vm

到了这里，依然没完，让你想不到的是，在 node 的 vm 模块中，也就是沙盒模块，runInContext 方法中，是不存在 window，也不存在 global 变量的，[查看代码](https://github.com/mqyqingfeng/Blog/blob/master/demos/node-vm/index.js)。

但是我们却可以通过 this 访问到全局对象，所以就有人发起了一个 PR，代码改成了：

```js
var root = (typeof self == 'object' && self.self == self && self) ||
           (typeof global == 'object' && global.global == global && global) ||
           this;
```

## 微信小程序

到了这里，还是没完，轮到微信小程序登场了。

因为在微信小程序中，window 和 global 都是 undefined，加上又强制使用严格模式，this 为 undefined，挂载就会发生错误，所以就有人又发了一个 PR，代码变成了：

```js
var root = (typeof self == 'object' && self.self == self && self) ||
           (typeof global == 'object' && global.global == global && global) ||
           this ||
           {};
```

这就是现在 v1.8.3 的样子。

虽然作者可以直接讲解最终的代码，但是作者更希望带着大家看看这看似普通的代码是如何一步步演变成这样的，也希望告诉大家，代码的健壮性，并非一蹴而就，而是汇集了很多人的经验，考虑到了很多我们意想不到的地方，这也是开源项目的好处吧。

## 函数对象

现在我们讲第二句 `var _ = {};`

如果仅仅设置 _ 为一个空对象，我们调用方法的时候，只能使用 `_.reverse('hello')` 的方式，实际上，underscore 也支持类似面向对象的方式调用，即：

```js
_('hello').reverse(); // 'olleh'
```

再举个例子比较下两种调用方式：

```js
// 函数式风格
_.each([1, 2, 3], function(item){
    console.log(item)
});

// 面向对象风格
_([1, 2, 3]).each(function(item){
    console.log(item)
});
```

可是该如何实现呢？

既然以 `_([1, 2, 3])` 的形式可以执行，就表明 `_` 不是一个字面量对象，而是一个函数！

幸运的是，在 JavaScript 中，函数也是一种对象，我们完全可以将自定义的函数定义在 `_` 函数上！

**目前的写法**

```js
var root = (typeof self == 'object' && self.self == self && self) ||
           (typeof global == 'object' && global.global == global && global) ||
           this ||
           {};

var _ = function() {}

root._ = _;
```

如何做到 `_([1, 2, 3]).each(...)` 呢？即 _ 函数返回一个对象，这个对象，如何调用挂在 _ 函数上的方法呢？

我们看看 underscore 是如何实现的：

```js
var _ = function(obj) {
    if (!(this instanceof _)) return new _(obj);
    this._wrapped = obj;
};

_([1, 2, 3]);
```

我们分析下 `_([1, 2, 3])` 的执行过程：

1. 执行 `this instanceof _`，this 指向 window ，`window instanceof _` 为 false，`!` 操作符取反，所以执行 `new _(obj)`。
2. `new _(obj)` 中，this 指向实例对象，`this instanceof _` 为 true，取反后，代码接着执行
3. 执行 `this._wrapped = obj`， 函数执行结束
4. 总结，`_([1, 2, 3])` 返回一个对象，为 `{_wrapped: [1, 2, 3]}`，该对象的原型指向 _.prototype

示意图如下：

![_()ç¤ºæå¾](js-fap/new-obj.png)

然后问题来了，我们是将方法挂载到 _ 函数对象上，并没有挂到函数的原型上呐，所以返回了的实例，其实是无法调用 _ 函数对象上的方法的！

所以我们还需要一个方法将 _ 上的方法复制到 `_.prototype` 上，这个方法就是 `_.mixin`。

## _.Functions

为了将 _ 上的方法复制到原型上，首先我们要获得 _ 上的方法，所以我们先写个 `_.functions` 方法。

```js
_.functions = function(obj) {
    var names = [];
    for (var key in obj) {
        if (_.isFunction(obj[key])) names.push(key);
    }
    return names.sort();
};
```

isFunction 函数可以参考 [《JavaScript专题之类型判断(下)》](https://github.com/mqyqingfeng/Blog/issues/28)

## Mixin

[es-proto mixin 方案](programming/font-end/primitive/es/es-proto.md#Mixin%20模式的实现)

现在我们可以写 mixin 方法了。

```js
var ArrayProto = Array.prototype;
var push = ArrayProto.push;

_.mixin = function(obj) {
  	const funcs = _.functions(obj)
    for(left [key,func] of Object.ectries(funcs)){
      _.prototype[key] = function(...rest) {
            var args = [this._wrapped];
            return func.apply(_, [...args,...rest]);
        };
    }
    return _;
};

_.mixin(_);
```

值得注意的是：因为 `_[name] = obj[name]` 的缘故，我们可以给 underscore 拓展自定义的方法:

```js
_.mixin({
  addOne: function(num) {
    return num + 1;
  }
});

_(2).addOne(); // 3
```

至此，我们算是实现了同时支持面向对象风格和函数风格。

![es-object](programming/font-end/primitive/es/es-object.md#混入模式)

## 导出

终于到了讲最后一步 `root._ = _`，我们直接看源码：

```js
if (typeof exports != 'undefined' && !exports.nodeType) {
  if (typeof module != 'undefined' && !module.nodeType && module.exports) {
    exports = module.exports = _;
  }
  exports._ = _;
} else {
  root._ = _;
}
```

为了支持模块化，我们需要将 _ 在合适的环境中作为模块导出，但是 nodejs 模块的 API 曾经发生过改变，比如在早期版本中：

```js
// add.js
exports.addOne = function(num) {
  return num + 1
}

// index.js
var add = require('./add');
add.addOne(2);
```

在新版本中：

```js
// add.js
module.exports = function(1){
    return num + 1
}

// index.js
var addOne = require('./add.js')
addOne(2)
```

所以我们根据 exports 和 module 是否存在来选择不同的导出方式，那为什么在新版本中，我们还要使用 `exports = module.exports = _` 呢？

这是因为在 nodejs 中，exports 是 module.exports 的一个引用，当你使用了 module.exports = function(){}，实际上覆盖了 module.exports，但是 exports 并未发生改变，为了避免后面再修改 exports 而导致不能正确输出，就写成这样，将两者保持统一。

最后为什么要进行一个 exports.nodeType 判断呢？这是因为如果你在 HTML 页面中加入一个 id 为 exports 的元素，比如

```html
<div id="exports"></div>
```

就会生成一个 window.exports 全局变量，你可以直接在浏览器命令行中打印该变量。

此时在浏览器中，`typeof exports != 'undefined'` 的判断就会生效，然后 `exports._ = _`，然而在浏览器中，我们需要将 _ 挂载到全局变量上呐，所以在这里，我们还需要进行一个是否是 DOM 节点的判断。

# NextTick

因为 js 是单线程的，在 setData 之后，到底什么时候派发 nextTickReach 是可以有选择的

1. data 真正更新之后马上 reach，此时框架控制的节点树都还没更新呢，别说是真实的 dom 树了
2. 等框架控制的节点树更新完之后派发，对于 san 就是 changes 传导到了每个节点的 _update 方法之后，对于 vue 和 react 就是 vDom 更新完毕之后

讨论的 issue：https://github.com/baidu/san/issues/141

vue 最新的源码：https://github.com/vuejs/vue/blob/dev/dist/vue.js

把 na 组件的插入操作放在 nextTick 中，可以延后执行 na 组件的插入操作，可以提升组件性能

> san 的视图更新是异步的。组件数据变更后，将在下一个时钟周期更新视图。如果你修改了某些数据，想要在 DOM 更新后做某些事情，则需要使用 `nextTick` 方法。
>
> san.dev.js 搜索 nextTick 即可

只能是 attached 之后了，感觉是类似 FMP 的一个逻辑，可以 san 组件实例已经走到 attached 了，再插入 NA 组件本身

用 cts 实例测试发现好几个 cover-view 都 attached 了 之后，此时很多 h5 的内容都已经上屏了，才进去第一个 nextTick

> 假如我有 1w 个 na 组件，也会在全部 attached 之后才到 nextTick 的轮次吗？感觉有可能哦
>
> 从上面的表现来看，我们这里的 nextTick 不就是宏任务的吗？不一定哦，因为 debugger 是可以挂起 js 线程的，alert 会占用 js 线程，并没有让给 gui，反而是 debugger 是让给了 gui 的
>
> 可以测试一下 nextTick 的微任务和宏任务了，无论是微任务还是宏任务，反正端能力都会异步的，除非是宏任务，可以做到保证在 h5 的组件渲染完成
>
> 我们现在 setImmediate 和 messageChannel 都是有的，所以是宏任务

san 的数据更新操作是异步的，会被打包在一个 nextTick 中。因此我们再使用 nextTick 可以获取到数据更新的 nextTick 之后的 data，微任务队列

还有一个点就是 dom 的更新和 UI 渲染是两个概念，因为 js 线程和渲染线程是互斥的，所以 UI 渲染之前，dom 就已经是更新完的了。

> 这就涉及到我们 dom api 获取的究竟是？肯定是从 dom 树入手的呀，render 树究竟是怎么样的，确实有部分 dom api 是从 render 树 入手的，但是有部分的 dom api 会强制渲染，这个时候会发生什么？

如果是 promise 这种微任务实现，当我们在 nextTick 中再次 setData，那么会在 UI 渲染前成功 setData

如果选择宏任务实现会在 UI 渲染之后，导致重新渲染，因此微任务应该是更好的选择，但是为什么 san 中优先选择了宏任务呢？vue 目前也是 promise 优先

> 我们 slave 触发的是哪个 nextTick 呢？我们组件代码运行在 slave 上，是 webview 环境，有 js 解释器 bom api dom api

我自己也是可以测试的，无论是 san 还是 vue，我需要有一个方法知道 dom 到底更新了没有，还需要明确一点 dom 更新和 UI 渲染的关系

网上的文章并没有错，nextTick 的初衷是为了获取更新后的 dom？ 那问题就只剩下上一个了，dom 的更新和 ui 的渲染，肯定是分开的吧，不可能说 UI 渲染完了 dom 才更新了，肯定是更新后的 dom 被用于 UI 渲染了

我只要整一个函数更新 dom，然后看看实际的 dom 结构更新和渲染的先后关系

> 采用 alert 语句进行提示，alert 语句会 block 住 js 线程，将执行权让给 gui 渲染线程，执行 alert 之后浏览器会把这个语句之前的所有对 dom 的操作都进行体现。
>
> 感觉打断点的话也会被 js 线程也会被吊起，ui 渲染会直接进行，因此没办法捕捉到 dom 更新了，但是 UI 没渲染的那个瞬间
>
> 体验 dom 更新了，但是 UI 没有渲染的过程：https://blog.csdn.net/qdmoment/article/details/83657410

# Dom 相关问题 2023-02-06 Here

因为 DOM 和 BOM 主要是一些独立的 API，彼此之间联系并不紧密，因此 DOM 和 BOM 的 FAQ 不单独放在子文件

## A 标签模拟 Disabled 属性

因为 a 标签没有 disabled 属性，可以用以下实现

```css
/** demo.html */
<a class="a">提交</button>
/** demo1.js
 *  css 方法
 */
$('.a').css('pointer-events','none');

/** demo1.js
 *  属性 方法
 */
$('.a').attr('href','javascript:;');
```

这个时候则不能进行任何点击,但是样式的话,需要自己自行添加置灰.

## 判断滚动条是否滚动到底

垂直滚动条：`scrollHeight - scrollTop = clientHeight`

水平滚动：`scrollWidth - scrollLeft = clientWidth`

## 容器内拖拽的最长距离

拖拽的最长距离：容器的 clientWidth- 子元素的 offsetWidth

在容器的内部移动，子元素的边框不会被覆盖

## 元素距离左上角的距离

下面的代码可以算出元素左上角相对于整张网页的坐标。

```js
function getElementPosition(e) {
  var x = 0;
  var y = 0;
  while (e !== null)  {
    x += e.offsetLeft;
    y += e.offsetTop;
    e = e.offsetParent;
  }
  return {x: x, y: y};
}
```

## 确认节点类型

使用 DOM 类型常量

```js
var node = document.documentElement.firstChild;
if (node.nodeType === Node.ELEMENT_NODE) {
  console.log('该节点是元素节点');
}
```

文档节点（document）：9，对应常量 `Node.DOCUMENT_NODE`

元素节点（element）：1，对应常量 `Node.ELEMENT_NODE`

属性节点（attr）：2，对应常量 `Node.ATTRIBUTE_NODE`

文本节点（text）：3，对应常量 `Node.TEXT_NODE`

文档片断节点（DocumentFragment）：11，对应常量 `Node.DOCUMENT_FRAGMENT_NODE`

文档类型节点（DocumentType）：10，对应常量 `Node.DOCUMENT_TYPE_NODE`

注释节点（Comment）：8，对应常量 `Node.COMMENT_NODE`

## 判断一个节点是否是空白

判断一个节点有没有子节点，有许多种方法，下面是其中的三种。

- `node.hasChildNodes()`
- `node.firstChild !== null`
- `node.childNodes && node.childNodes.length > 0`

## 获取单个字符的宽度

display:inline，或者 float、绝对定位等，总之让字符去撑开容器的宽度，再获取容器宽度除以字符数即可得到单个字符的宽度

## 操作 Style

### 重置样式 样式表 的冲突

```js
// 如果把宽度设置为0，那么内联样式的特殊性只能在样式表通过!important解决
// upNodes[i].style.width="0";
// 把值设为空，在内联样式不会有属性值，样式表的属性就可以正常作用了
upNodes[i].style.width="";
```

### 单位

.style.property 需要加 px，如果是 attribute 浏览器会自动加 px

## 隐藏页面中的某个元素

### 隐藏类型

屏幕并不是唯一的输出机制，比如说屏幕上看不见的元素（隐藏的元素），其中一些依然能够被读屏软件阅读出来（因为读屏软件依赖于可访问性树来阐述）。为了消除它们之间的歧义，我们将其归为三大类：

- 完全隐藏：元素从渲染树中消失，不占据空间。
- 视觉上的隐藏：屏幕中不可见，占据空间。
- 语义上的隐藏：读屏软件不可读，但正常占据空。

### 完全隐藏

`display` 属性 (不占据空间)

```css
display: none;
```

hidden 属性 (不占据空间)

```html
<div hidden></div>
```

### 视觉上的隐藏

利用 `position` 和 盒模型 将元素移出可视区范围

+ 设置 `posoition` 为 `absolute` 或 `fixed`，通过设置 `top`、`left` 等值，将其移出可视区域。(可视区域不占位)
+ 设置 `position` 为 `relative`，通过设置 `top`、`left` 等值，将其移出可视区域。（可视区域占位）
+ 设置 margin 值，将其移出可视区域范围（可视区域占位）。
+ 如果希望其在可视区域不占位，需同时设置 `height: 0`;

利用 transfrom

+ 缩放 (占据空间)

    ```css
    transform: scale(0);
    ```

+ 移动 `translateX`, `translateY`(占据空间)
+ 旋转 `rotate` (占据空间)

    ```css
    transform: rotateY(90deg);
    ```

设置其大小为 0

+ 宽高为 0，字体大小为 0：
+ 宽高为 0，超出隐藏:

    ```css
    height: 0;
    width: 0;
    overflow: hidden;
    ```

设置透明度 opacity 为 0 (占据空间)

visibility 属性 (占据空间)

层级覆盖，`z-index` 属性 (占据空间)

+ ```css
    position: relative;
    z-index: -999;
    ```
+ 再设置一个层级较高的元素覆盖在此元素上。

clip-path 裁剪 (占据空间)

+ ```css
    clip-path: polygon(0 0, 0 0, 0 0, 0 0);
    ```

### 语义上的隐藏

aria-hidden 属性 (占据空间)

读屏软件不可读，占据空间，可见。

```
<div aria-hidden="true">
</div>
```

## 事件

### Addeventlistener

DOM event 原理：https://zhuanlan.zhihu.com/p/50577896>

### Addeventlistener 多次绑定，只执行一次

方案一：每次执行完，再 handler 解绑自己

方案二：使用 once 属性

### addEventListener This

事件监听, this 一定是 dom 本身, 如果是在 class 里面想要指定 this, 有如下实践:

```ts
        this.el.addEventListener('mouseup', this.handleMouseUp = () => {
            if (!this.dragging) {
                return;
            }

            this.dragging = false;
            this.activeSelection = null as any;
        });

		this.el.removeEventListener('mouseup', this.handleMouseUp);
```

本质上就是那个经典的 react 问题

## 颜色相关操作

### 使得 Rgb 色值变明亮或者变灰暗

https://www.php.cn/css-tutorial-480162.html

# Bom 相关问题

## 限制浏览器默认行为

### 禁止全局默认事件

```js
window.onload=function(){
  document.addEventListener("touchstart",function(ev){
    ev=ev||event;
    ev.preventDefault();
  },{passive:false})
}
```

### 禁用系统滚动条

```css
html, body {
  height: 100%;
  overflow: hidden;
 }
```

chrome 滚动条是加给文档的（初始包含块），html height 100% ，大小是视窗大小，继承是一层层继承下来的，不可以隔代继承

body 的滚动条可以通过 html 传递给 document，如果 body 是 `overflow:scroll` 或者 `overflow:auto`，且 html 没有自己的滚动条（即不是 `overflow:scroll` 或者 `overflow:auto`），那么 body 的滚动条就会传递给 document

如果 html 和 body 都写了 `overflow:scroll` 或者 `overflow:auto`，html 的 scroll 永远给到文档，而 body 自己也会有一个滚动条

html 开启 hidden 就可以禁止默认滚动条，因为默认滚动条都是 body，可能是 body 传递过去的，此时中断了传递

会出现滚动条是因为溢出了容器，所以容器肯定是小于超高的元素的，当我们滑动滚动条的时候容器是没有动的，只是滚动条加在了容器身上

一般会给一个全局的 wrap，让滚动条出现在 wrap 身上，无论怎么滑动都不会影响初始包含块，可以用来模拟固定定位（相对于有滚动条的 wrap 绝对定位，就是固定定位的效果了）

移动端的固定定位一般都是用绝对定位来模拟的

### 禁用 a 标签

hangUpBtn.style.pointerEvents = 'none'

## 移动端长按菜单

### 基础

长按不同的内容弹出的菜单是不一样的

+ 长按超链接，弹出刻板印象中的菜单，h5 正常，所有浏览器表现都一致
+ 长按文字，弹出复制黏贴的 tips 型菜单，手百的 h5 页面的表现反而是与普通浏览器不符合的
+ 长按图片，弹出查看图片，下载图片一类的。此处的图片必须是 img 标签，如果是 background-image 的话长按也不会弹出菜单

### **超链接**

搜狐网的那些就是超链接吗？不是超链接，那是什么？点击一个页面，跳转到另一个页面，全都是路由？

测试时发现使用 a 标签也会被转换为 swan-view

![image-20201010114831134](/img/user/programming/FAQ/js-faq/image-20201010114831134.png)

现在超链接长按没有任何反应是否符合预期？符合预期

![image-20201010112619797](/img/user/programming/FAQ/js-faq/image-20201010112619797.png)

### 图片

对于 image 组件，因为是通过 background-image 方式实现的，因此不弹出菜单是正常的，所以需要 src 逻辑判断使用端的菜单

对于原生的 img 表现，因为 base.styl 通过属性进行了限制，所以 ios 端是没有菜单的，安卓 webview 本身没有实现长按图片弹出菜单的功能，因此也不会有菜单。表现为全局禁用了默认彩蛋

> https://xiaozhuanlan.com/topic/9874152630

我们能触发普通的 h5 事件吗？是不是所有的事件都是端传给我们的？

+ 根据图片的这种表现，是不是说明我们不能触发普通的 h5 事件？
+ 或者说明的是这个元素，没有这个事件？合理的来看的话，有的元素确实是右键冒泡到 document 才弹出菜单的
+ 现在看来是 css 属性阻止了这个事件的触发，事件的默认行为是打开菜单

**文字**

+ 属性上，user-select 做了禁止，所以小程序现在长按普通文本是不会选中的，可以选中的都是经过配置的 text 组件和 rich-text 组件

### Get-image-src

image 和 rich-text 下的 img 标签弹出的菜单是 端上给的菜单，因为前端的菜单现在是无法弹出的

### Text 组件和 Rich-text 组件

叶青做的仅仅是吧 selectable 设置为 true 而已，我需要去研究一下 selectable 的作用

### User-select

[CSS](https://developer.mozilla.org/zh-CN/docs/Web/CSS) 属性 `user-select` 控制用户能否选中文本。除了文本框内，它对被载入为 [chrome](https://developer.mozilla.org/zh-CN/docs/Glossary/Chrome) 的内容没有影响。

| [初始值](https://developer.mozilla.org/zh-CN/docs/Web/CSS/initial_value) | `auto`       |
| :----------------------------------------------------------- | ------------ |
| 适用元素                                                     | all elements |
| [是否是继承属性](https://developer.mozilla.org/zh-CN/docs/Web/CSS/inheritance) | 否           |
| [计算值](https://developer.mozilla.org/zh-CN/docs/Web/CSS/computed_value) | as specified |
| Animation type                                               | discrete     |

选中的操作：

1. 双击选中文字
2. 鼠标框选
3. 移动端长按选中，会同时弹出菜单，用于复制或者搜索，符合预期

    + 即使设置不能选中是否仍然会弹出菜单？也不会弹出菜单，手百的实现与 chrome 不一致，即使设置了 none，也仍然可以弹出菜单，然后进行选择
    + 当父元素为 none 时，如果子元素为 auto，在电脑中，也采用了 none，不可选中，但是手机上还是可以长按选中并弹出菜单
    + 如果此时禁用了菜单，那么就只可以选中，不会弹出菜单
    + 在 ios 中即使禁用了菜单，也还是会弹出菜单，或者说那个拷贝和搜索根本不是前端菜单？
    + 在 ios 中，只要 body 设置了 use-slect：none，全局都是 none
    + 安卓也是一样耶，设置了 body 就 ok 了，但是如果子元素设置了 auto，反而又不行了，此时加上禁用菜单，所用弹出的那个确实是菜单
    + 总结：body 加了，其他元素别加就是 ok 的，ios 加了也不影响，chrome 加了就变成可以选的了

#### 属性值

**none：**元素及其子元素的文本不可选中。

**auto：**

- 在 `::before` 和 `::after` 伪元素上，采用的属性值是 `none`
- 如果元素是可编辑元素，则采用的属性值是 `contain`
- 否则，如果此元素的父元素的 `user-select` 采用的属性值为 `all`，则该元素采用的属性值也为 `all`
- 否则，如果此元素的父元素的 `user-select` 采用的属性值为 `none`，则该元素采用的属性值也为 `none`
- 否则，采用的属性值为 `text`
- 哪里可以看到这个的具体逻辑呢？现在感觉是如果父元素是 auto，自己也是 auto，ios 为向上追溯，chrome 就是 text 了

> 注意：非继承属性与初始值 auto（其使用值取决于父元素）的不寻常组合使创建有效的选择性继承成为可能。这最初是由微软在 IE 中提出的，目的是引入一种类似于继承的行为，

**text：**用户可以选择文本。

**all：**在** 一个 HTML 编辑器**中，当双击子元素或者上下文时，那么包含该子元素的最顶层元素也会被选中。

**contain：**允许在元素内选择；但是，选区将被限制在该元素的边界之内。

### -Webkit-touch-callout

好像不能限制文本，只能限制图片长按菜单，限制了之后会选中图片附近的内容

### Contextmenue

貌似只要禁用了 document 的就可以做到全局禁用

### 全局禁用

只需要 body 添加即可，就可以全部都禁用了包括菜单

```css
body {

-webkit-touch-callout:none;/*系统默认菜单被禁用*/

-webkit-user-select:none;/*webkit浏览器，添加了这个safari才能全局禁用*/

-khtml-user-select:none;/*早起浏览器*/

-moz-user-select:none;/*火狐浏览器*/

-ms-user-select:none;/*IE浏览器*/

user-select:none;/*用户是否能够选中文本*/

}
```

但是此时安卓图片还是可以长按弹出菜单，因此需要给 document 添加禁用菜单

但是安卓 webview 默认长按图片是不会有菜单的

以上就是全局禁用框选以及长按菜单，兼容 pc，ios，安卓

特别注意，这时子元素如果再添加 user-select:auto，又会变成可以选中，因为 user-select 不是真正的继承属性，此时应该是计算成 text 了

```css
#son{
 -webkit-user-select:auto;/*webkit浏览器*/
 user-select:auto;
}
```

如果想单独禁用菜单，可以给 document 添加，pc 端和安卓可以做到全局禁用，但是 ios 还是要逐个添加。

# 业务场景

## 前端路由

[前端路由的前生今世及实现原理](https://segmentfault.com/a/1190000011967786)

### 选项卡\原生路由

```html
<style>
    :target{
      display: block;
    }
    div{
      display:none;
    }
</style>

<a href="#div1">div1</a>
<a href="#div2">div2</a>
<a href="#div3">div3</a>
<div id="div1">
 div1
</div>
<div id="div2">
 div2
</div>
<div id="div3">
 div3
</div>
```

## 日志

### 输入操作日志

用 Proxy 拦截操作，打印日志，用 reflect 还原默认的行为

### 获取 Log 函数被调用时所在的函数名

```ts
function where() {
  return where.caller.name
}
```

已废弃

```ts
function where = () => {
    let reg = /\s+at\s(\S+)\s(/g
    let str = new Error().stack.toString()
    let res = reg.exec(str) && reg.exec(str)
    return res && res[1]
}
```

默认情况下，Error 对象不仅仅能给你返回调用当前函数的外部函数名称，它甚至可以给你返回最多 10 层的函数调用栈。

```ts
const err - new Error('my error')
Error.stackTraceLimit = Infinity;
throw err;
```

想看当前行数和列数以及文件名 都是用的这个方法… 尤其 nodejs

## 截图

https://juejin.im/entry/58b91491570c35006c4f7fdf

DOM 转换成 canvas

DOM 准换成 svg

## 请求

### 继发请求

for 循环即可

实际开发中，经常遇到一组异步操作，需要按照顺序完成。比如，依次远程读取一组 URL，然后按照读取的顺序输出结果。

**Promise 写法**

```javascript
function logInOrder(urls) {
  // 远程读取所有URL
  const textPromises = urls.map(url => {
    return fetch(url).then(response => response.text());
  });

  // 按次序输出
  textPromises.reduce((chain, textPromise) => {
    return chain.then(() => textPromise)
      .then(text => console.log(text))
  }, Promise.resolve())
}
```

上面代码使用 `fetch` 方法，同时远程读取一组 URL。每个 `fetch` 操作都返回一个 Promise 对象，放入 `textPromises` 数组。然后，`reduce` 方法依次处理每个 Promise 对象，然后使用 `then`，将所有 Promise 对象连起来，因此就可以依次输出结果。

**async 函数实现**

这种写法不太直观，可读性比较差。下面是 async 函数实现。

```javascript
async function logInOrder(urls) {
  for (const url of urls) {
    const response = await fetch(url);
    console.log(await response.text());
  }
}
```

上面代码确实大大简化，问题是所有远程操作都是继发。只有前一个 URL 返回结果，才会去读取下一个 URL

### 并发请求

继发请求效率很差，非常浪费时间。我们需要的是 **并发** 发出远程请求。

```javascript
async function logInOrder(urls) {
  // 并发读取远程URL
  const textPromises = urls.map(async url => {
    const response = await fetch(url);
    return response.text();
  });

  // 按次序输出
  for (const textPromise of textPromises) {
    console.log(await textPromise);
  }
}
```

上面代码中，虽然 `map` 方法的参数是 `async` 函数，但它是并发执行的，因为只有 `async` 函数内部是继发执行，外部不受影响。后面的 `for..of` 循环内部使用了 `await`，因此实现了按顺序输出。

### Promise.all

稍微有点反直觉，promise.all 方法，其实保存的是所有 promise 的返回值，在 all 方法之前，其实所有的 promise 就已经在等待执行了，因为微任务的缘故，所以代码看起来是先发送请求，其实是先放在 all 里面了

### 压缩查询字符串

这里以查询字符串（querystring）压缩技术为例，注意下面 `replace` 方法中，回调函数根本没有返回任何东西。

```js
function compress(source) {
	var keys = {};
	source.replace(/([^=&]+)=([^&]*)/g, function(full, key, value) {
		keys[key] = (keys[key] ? keys[key] + ',' : '') + value;
	});
	var result = [];
	for (var key in keys) {
		result.push(key + '=' + keys[key]);
	}
	return result.join('&');
}

console.log( compress("a=1&b=2&a=3&b=4") );
// => "a=1,3&b=2,4"
```

## 功能模块

### 用户登陆验证

https://www.nowcoder.com/test/question/done?tid=22865052&qid=13860#summary

## 框架

### 一个项目多个框架

通过 webpack 配直多个 html 模板，引用不同的入口 js，不同的 js 可以使用不同的框架进行编写

### 多个客户端重用组件

像毕设的时候的 interview 和 candidate，有很多组件是应该重用的，但是却没能做好

### 数据持久化

场景：用户在某个组件发送 ajax 请求获取到了需要的数据，然后该数据被传输到下一个组件当中，在刷新该组件，数据就会丢失，如果保持呢？

redux 数据持久化：redux-persist

一般持久化：storage，因为组件的渲染是小组件先完成大组件在完成，所以在大组件持久化的话小组件就先报错了。如果不是在 useEffect 之后执行一次，就会不断重复渲染，所以最好放在公共 Header 处理持久化，但是渲染比 setState 还是快多了。只能是判断 context 有没有，没有就返回 null，等 context 被改变了会自动重新渲染，内容就出来了

ws 持久化：每次重新创建在连接

react-router：query 形式和 state 形式都可以实现持久化

## 虚拟列表

https://cloud.tencent.com/developer/article/1533206

# Event

## 监听与解除绑定

class 内部的事件监听与解除绑定, 因为箭头函数 this, 以及 eventListener 的 this 问题, 需要形成一个最佳实践.

```ts
export const EditorContent = san.defineComponent<EditorContentProps>({

    template: `
        <div class="editor-san" on-click="handleClick">
            <slot></slot>
        </div>`,

    attached() {
        this.handleEditor(this.data.get('editor'));

        this.watch('editor', this.handleEditor);
    },

    handleEditor(editor: Editor) {
        if (editor && editor.options.element) {
            this.handleValidate(editor);
        }
    },

    /**
     * 处理 santd form validate, 外部直接传入 decorator 即可
     * 仅在失焦时做校验, 避免频繁获取 html
     */
    handleValidate(editor: Editor) {

        this.validateListener = () => {
            this.dispatch('UI:form-item-interact', {
                fieldValue: editor.getHTML(),
                type: 'change'
            });
            console.log('editor.getHTML(): ', editor.getHTML());
        };

        editor.on('blur', this.validateListener);
    },

    detached() {
        editor.off('blur', this.validateListener);
    },
});

```

动态的改 this 的做法挺好的

## 什么情况下需要解绑?

取决于 dom 节点是否还被引用

If a DOM element which is removed is reference-free (no references pointing to it) then **yes** - the element itself is picked up by the garbage collector as well as any event handlers/listeners associated with it.

```javascript
var a = document.createElement('div');
var b = document.createElement('p');
// Add event listeners to b etc...
a.appendChild(b);
a.removeChild(b);
b = null; 
// A reference to 'b' no longer exists 
// Therefore the element and any event listeners attached to it are removed.
```

However; if there are references that still point to said element, the element and its event listeners are retained in memory.

```javascript
var a = document.createElement('div');
var b = document.createElement('p'); 
// Add event listeners to b etc...
a.appendChild(b);
a.removeChild(b); 
// A reference to 'b' still exists 
// Therefore the element and any associated event listeners are still retained.
```
