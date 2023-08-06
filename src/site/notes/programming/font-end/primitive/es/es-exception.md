---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/es/es-exception/"}
---


# Error 对象

发生错误时，JavaScript 会生成一个包含有关此 error 详细信息的对象。然后将该对象作为参数传递给 `catch`：

```javascript
try {
  // ...
} catch (err) { // <-- “error 对象”，也可以用其他参数名代替 err
  // ...
}
```

对于所有内建的 error，error 对象具有两个主要属性：

- `name`

    Error 名称。例如，对于一个未定义的变量，名称是 `"ReferenceError"`。

- `message`

    关于 error 的详细文字描述。

还有其他非标准的属性在大多数环境中可用。其中被最广泛使用和支持的是：

- `stack`

    当前的调用栈：用于调试目的的一个字符串，其中包含有关导致 error 的嵌套调用序列的信息。

例如：

```javascript
try {
  lalala; // error, variable is not defined!
} catch (err) {
  alert(err.name); // ReferenceError
  alert(err.message); // lalala is not defined
  alert(err.stack); // ReferenceError: lalala is not defined at (...call stack)

  // 也可以将一个 error 作为整体显示出来
  // error 信息被转换为像 "name: message" 这样的字符串
  alert(err); // ReferenceError: lalala is not defined
}
```

# Throw 操作符

`throw` 操作符会生成一个 error 对象。

语法如下：

```javascript
throw <error object>
```

技术上讲，我们可以将任何东西用作 error 对象。甚至可以是一个原始类型数据，例如数字或字符串，但最好使用对象，最好使用具有 `name` 和 `message` 属性的对象（某种程度上保持与内建 error 的兼容性）。

JavaScript 中有很多内建的标准 error 的构造器：`Error`，`SyntaxError`，`ReferenceError`，`TypeError` 等。我们也可以使用它们来创建 error 对象。

它们的语法是：

```javascript
let error = new Error(message);
// 或
let error = new SyntaxError(message);
let error = new ReferenceError(message);
// ...
```

对于内建的 error（不是对于其他任何对象，仅仅是对于 error），`name` 属性刚好就是构造器的名字。`message` 则来自于参数（argument）。

例如：

```javascript
let error = new Error("Things happen o_O");

alert(error.name); // Error
alert(error.message); // Things happen o_O
```

让我们来看看 `JSON.parse` 会生成什么样的 error：

```javascript
try {
  JSON.parse("{ bad json o_O }");
} catch(err) {
  alert(err.name); // SyntaxError
  alert(err.message); // Unexpected token b in JSON at position 2
}
```

正如我们所看到的， 那是一个 `SyntaxError`。

在我们的示例中，缺少 `name` 属性就是一个 error，因为用户必须有一个 `name`。

所以，让我们抛出这个 error。

```javascript
let json = '{ "age": 30 }'; // 不完整的数据

try {

  let user = JSON.parse(json); // <-- 没有 error

  if (!user.name) {
    throw new SyntaxError("数据不全：没有 name"); // (*)
  }

  alert( user.name );

} catch(err) {
  alert( "JSON Error: " + err.message ); // JSON Error: 数据不全：没有 name
}
```

在 `(*)` 标记的这一行，`throw` 操作符生成了包含着我们所给定的 `message` 的 `SyntaxError`，与 JavaScript 自己生成的方式相同。`try` 的执行立即停止，控制流转向 `catch` 块。

现在，`catch` 成为了所有 error 处理的唯一场所：对于 `JSON.parse` 和其他情况都适用。

# Try-catch

它按照以下步骤执行：

1. 首先，执行 `try {...}` 中的代码。
2. 如果这里没有错误，则忽略 `catch (err)`：执行到 `try` 的末尾并跳过 `catch` 继续执行。
3. 如果这里出现错误，则 `try` 执行停止，控制流转向 `catch (err)` 的开头。变量 `err`（我们可以使用任何名称）将包含一个 error 对象，该对象包含了所发生事件的详细信息。

![image-20220808114358388](/img/user/programming/font-end/primitive/es/es-exception/image-20220808114358388.png)

## 可选的 “catch” 绑定

**最近新增的特性**

这是一个最近添加到 JavaScript 的特性。 旧式浏览器可能需要 polyfills.

如果我们不需要 error 的详细信息，`catch` 也可以忽略它：

```javascript
try {
  // ...
} catch { // <-- 没有 (err)
  // ...
}
```

## 全局的 Try-catch

全局的 try-catch 意义不大，因为一旦出现了 error，被 catch 捕获之后， try 内部剩下的逻辑也不会执行了。起不到捕获后继续执行剩余逻辑的效果。

要做到类似的效果，只能是细粒度的 try catch

## Try-catch 仅对运行时的 Error 有效

要使得 `try-catch` 能工作，代码必须是可执行的。换句话说，它必须是有效的 JavaScript 代码。

如果代码包含语法错误，那么 `try-catch` 将无法正常工作，例如含有不匹配的花括号

![](/img/user/programming/font-end/primitive/es/es-exception/image-20230525102711466.png)

JavaScript 引擎首先会读取代码，然后运行它。在读取阶段发生的错误被称为“解析时间（parse-time）”错误，并且无法恢复（从该代码内部）。这是因为引擎无法理解该代码。

所以，`try...catch` 只能处理有效代码中出现的错误。这类错误被称为“运行时的错误（runtime errors）”，有时被称为“异常（exceptions）”。

## Try-catch 同步执行

如果在“计划的（scheduled）”代码中发生异常，例如在 `setTimeout` 中，则 `try...catch` 不会捕获到异常：

```javascript
try {
  setTimeout(function() {
    noSuchVariable; // 脚本将在这里停止运行
  }, 1000);
} catch (err) {
  alert( "不工作" );
}
```

因为 `try...catch` 包裹了计划要执行的函数，该函数本身要稍后才执行，这时引擎已经离开了 `try...catch` 结构。

为了捕获到计划的（scheduled）函数中的异常，那么 `try...catch` 必须在这个函数内：

```javascript
setTimeout(function() {
  try {
    noSuchVariable; // try...catch 处理 error 了！
  } catch {
    alert( "error 被在这里捕获了！" );
  }
}, 1000);
```

## try…catch…finally

等一下，以上并不是所有内容。

`try...catch` 结构可能还有一个代码子句（clause）：`finally`。

如果它存在，它在所有情况下都会被执行：

- `try` 之后，如果没有 error，
- `catch` 之后，如果有 error。

该扩展语法如下所示：

```javascript
try {
   ... 尝试执行的代码 ...
} catch (err) {
   ... 处理 error ...
} finally {
   ... 总是会执行的代码 ...
}
```

试试运行这段代码：

```javascript
try {
  alert( 'try' );
  if (confirm('Make an error?')) BAD_CODE();
} catch (err) {
  alert( 'catch' );
} finally {
  alert( 'finally' );
}
```

这段代码有两种执行方式：

1. 如果你对于 “Make an error?” 的回答是 “Yes”，那么执行 `try -> catch -> finally`。
2. 如果你的回答是 “No”，那么执行 `try -> finally`。

`finally` 子句（clause）通常用在：当我们开始做某事的时候，希望无论出现什么情况都要完成完成某个任务。

例如，我们想要测量一个斐波那契数字函数 `fib(n)` 执行所需要花费的时间。通常，我们可以在运行它之前开始测量，并在运行完成时结束测量。但是，如果在该函数调用期间出现 error 该怎么办？特别是，下面这段 `fib(n)` 的实现代码在遇到负数或非整数数字时会返回一个 error。

无论如何，`finally` 子句都是一个结束测量的好地方。

在这儿，`finally` 能够保证在两种情况下都能正确地测量时间 —— 成功执行 `fib` 以及 `fib` 中出现 error 时：

```javascript
let num = +prompt("输入一个正整数？", 35)

let diff, result;

function fib(n) {
  if (n < 0 || Math.trunc(n) != n) {
    throw new Error("不能是负数，并且必须是整数。");
  }
  return n <= 1 ? n : fib(n - 1) + fib(n - 2);
}

let start = Date.now();

try {
  result = fib(num);
} catch (err) {
  result = 0;
} finally {
  diff = Date.now() - start;
}

alert(result || "出现了 error");

alert( `执行花费了 ${diff}ms` );
```

你可以通过运行上面这段代码并在 `prompt` 弹窗中输入 `35` 来进行检查 —— 代码运行正常，先执行 `try` 然后是 `finally`。如果你输入的是 `-1` —— 将立即出现 error，执行将只花费 `0ms`。以上两种情况下的时间测量都正确地完成了。

换句话说，函数 `fib` 以 `return` 还是 `throw` 完成都无关紧要。在这两种情况下都会执行 `finally` 子句。

**变量和 `try...catch...finally` 中的局部变量**

请注意，上面代码中的 `result` 和 `diff` 变量都是在 `try...catch` **之前** 声明的。

否则，如果我们使用 `let` 在 `try` 块中声明变量，那么该变量将只在 `try` 块中可见。

**`finally` 和 `return`**

`finally` 子句适用于 `try...catch` 的 **任何** 出口。这包括显式的 `return`。

在下面这个例子中，在 `try` 中有一个 `return`。在这种情况下，`finally` 会在控制转向外部代码前被执行。

```javascript
function func() {

  try {
    return 1;

  } catch (err) {
    /* ... */
  } finally {
    alert( 'finally' );
  }
}

alert( func() ); // 先执行 finally 中的 alert，然后执行这个 alert
```

**`try...finally`**

没有 `catch` 子句的 `try...finally` 结构也很有用。当我们不想在这儿处理 error（让它们 fall through），但是需要确保我们启动的处理需要被完成。

```javascript
function func() {
  // 开始执行需要被完成的操作（比如测量）
  try {
    // ...
  } finally {
    // 完成前面我们需要完成的那件事，即使 try 中的执行失败了
  }
}
```

上面的代码中，由于没有 `catch`，所以 `try` 中的 error 总是会使代码执行跳转至函数 `func()` 外。但是，在跳出之前需要执行 `finally` 中的代码。

### [使用 finally 还是直接放在代码后面？](https://zh.javascript.info/try-catch#shi-yong-finally-huan-shi-zhi-jie-fang-zai-dai-ma-hou-mian)

重要程度: 5

比较下面两个代码片段。

1. 第一个代码片段，使用 `finally` 在 `try..catch` 之后执行代码：

    ```javascript
    try {
      // 工作
    } catch (err) {
      // 处理 error
    } finally {
      // 清理工作空间
    }
    ```

2. 第二个代码片段，将清空工作空间的代码放在了 `try...catch` 之后：

    ```javascript
    try {
      // 工作
    } catch (err) {
      // 处理 error
    }
    
    // 清理工作空间
    ```

我们肯定需要在工作后进行清理，无论工作过程中是否有 error 都不影响。

在这儿使用 `finally` 更有优势，还是说两个代码片段效果一样？如果在这有这样的优势，如果需要，请举例说明。

解决方案

当我们看函数中的代码时，差异就变得很明显了。

如果在这有“跳出” `try..catch` 的行为，那么这两种方式的表现就不同了。

例如，当 `try...catch` 中有 `return` 时。`finally` 子句会在 `try...catch` 的 **任意** 出口处起作用，即使是通过 `return` 语句退出的也是如此：在 `try...catch` 刚刚执行完成后，但在调用代码获得控制权之前。

```javascript
function f() {
  try {
    alert('start');
    return "result";
  } catch (err) {
    /// ...
  } finally {
    alert('cleanup!');
  }
}

f(); // cleanup!
```

……或者当有 `throw` 时，如下所示：

```javascript
function f() {
  try {
    alert('start');
    throw new Error("一个 error");
  } catch (err) {
    // ...
    if("无法处理此 error") {
      throw err;
    }

  } finally {
    alert('cleanup!')
  }
}

f(); // cleanup!
```

正是这里的 `finally` 保证了 cleanup。如果我们只是将代码放在函数 `f` 的末尾，则在这些情况下它不会运行。

# 全局 Catch

[全局 catch](https://zh.javascript.info/try-catch#quan-ju-catch)

**环境特定**

这个部分的内容并不是 JavaScript 核心的一部分。

设想一下，在 `try...catch` 结构外有一个致命的 error，然后脚本死亡了。这个 error 就像编程错误或其他可怕的事儿那样。

有什么办法可以用来应对这种情况吗？我们可能想要记录这个 error，并向用户显示某些内容（通常用户看不到错误信息）等。

规范中没有相关内容，但是代码的执行环境一般会提供这种机制，因为它确实很有用。例如，Node.JS 有 [`process.on("uncaughtException")`](https://nodejs.org/api/process.html#process_event_uncaughtexception)。在浏览器中，我们可以将一个函数赋值给特殊的 [window.onerror](https://developer.mozilla.org/zh/docs/Web/API/GlobalEventHandlers/onerror) 属性，该函数将在发生未捕获的 error 时执行。

语法如下：

```javascript
window.onerror = function(message, url, line, col, error) {
  // ...
};
```

- `message`

    error 信息。

- `url`

    发生 error 的脚本的 URL。

- `line`，`col`

    发生 error 处的代码的行号和列号。

- `error`

    error 对象。

例如：

```markup
<script>
  window.onerror = function(message, url, line, col, error) {
    alert(`${message}\n At ${line}:${col} of ${url}`);
  };

  function readData() {
    badFunc(); // 啊，出问题了！
  }

  readData();
</script>
```

全局错误处理程序 `window.onerror` 的作用通常不是恢复脚本的执行 —— 如果发生编程错误，恢复脚本的执行几乎是不可能的，它的作用是将错误信息发送给开发者。

也有针对这种情况提供 error 日志的 Web 服务，例如 [https://errorception.com](https://errorception.com/) 或 [http://www.muscula.com](http://www.muscula.com/)。

它们会像这样运行：

1. 我们注册该服务，并拿到一段 JavaScript 代码（或脚本的 URL），然后插入到页面中。
2. 该 JavaScript 脚本设置了自定义的 `window.onerror` 函数。
3. 当发生 error 时，它会发送一个此 error 相关的网络请求到服务提供方。
4. 我们可以登录到服务方的 Web 界面来查看这些 error。

需要注意：

`onerror` 最好写在所有 `JS` 脚本的前面，否则有可能捕获不到错误；

`onerror` 无法捕获语法错误；

2.再试试语法错误呢？

```
window.onerror = function(message, source, lineno, colno, error) {
console.log('捕获到异常：',{message, source, lineno, colno, error});
}
let name = 'Jartto
```

控制台打印出了这样的异常：

```
Uncaught SyntaxError: Invalid or unexpected token
```

## window.addEventListener

当一项资源（如图片或脚本）加载失败，加载资源的元素会触发一个 `Event` 接口的 `error` 事件，并执行该元素上的 `onerror()` 处理函数。这些 `error` 事件不会向上冒泡到 `window` ，不过（至少在 `Firefox` 中）能被单一的 `window.addEventListener` 捕获。

```
<scritp>
window.addEventListener('error', (error) => {
    console.log('捕获到异常：', error);
}, true)
</script>
<img src="./jartto.png">
```

控制台输出：

![error2](/img/user/programming/font-end/primitive/es/es-exception/error2.png)

由于网络请求异常不会事件冒泡，因此必须在捕获阶段将其捕捉到才行，但是这种方式虽然可以捕捉到网络请求的异常，但是无法判断 `HTTP` 的状态是 `404` 还是其他比如 `500` 等等，所以还需要配合服务端日志才进行排查分析才可以。

需要注意：

- 不同浏览器下返回的 `error` 对象可能不同，需要注意兼容处理。
- 需要注意避免 `addEventListener` 重复监听。

# Promise Catch

在 `promise` 中使用 `catch` 可以非常方便的捕获到异步 `error` ，这个很简单。

没有写 `catch` 的 `Promise` 中抛出的错误无法被 `onerror` 或 `try-catch` 捕获到，所以我们务必要在 `Promise` 中不要忘记写 `catch` 处理抛出的异常。

解决方案： 为了防止有漏掉的 `Promise` 异常，建议在全局增加一个对 `unhandledrejection` 的监听，用来全局监听 `Uncaught Promise Error`。使用方式：

```
window.addEventListener("unhandledrejection", function(e){
  console.log(e);
});
```

我们继续来尝试一下：

```
window.addEventListener("unhandledrejection", function(e){
  e.preventDefault()
  console.log('捕获到异常：', e);
  return true;
});
Promise.reject('promise error');
```

可以看到如下输出：

![promise](/img/user/programming/font-end/primitive/es/es-exception/promise.png)

那如果对 `Promise` 不进行 `catch` 呢？

```
window.addEventListener("unhandledrejection", function(e){
  e.preventDefault()
  console.log('捕获到异常：', e);
  return true;
});
new Promise((resolve, reject) => {
  reject('jartto: promise error');
});
```

嗯，事实证明，也是会被正常捕获到的。

所以，正如我们上面所说，为了防止有漏掉的 `Promise` 异常，建议在全局增加一个对 `unhandledrejection` 的监听，用来全局监听 `Uncaught Promise Error`。

补充一点：如果去掉控制台的异常显示，需要加上：

```
event.preventDefault();
```

# Script Error

一般情况，如果出现 `Script error` 这样的错误，基本上可以确定是出现了跨域问题。这时候，是不会有其他太多辅助信息的，但是解决思路无非如下：

跨源资源共享机制 ( `CORS` )：我们为 `script` 标签添加 `crossOrigin` 属性。

```
<script src="http://jartto.wang/main.js" crossorigin></script>
```

或者动态去添加 `js` 脚本：

```
const script = document.createElement('script');
script.crossOrigin = 'anonymous';
script.src = url;
document.body.appendChild(script);
```

特别注意，服务器端需要设置：Access-Control-Allow-Origin

此外，我们也可以试试这个 -[解决 Script Error 的另类思路](https://juejin.im/post/5c00a405f265da610e7fd024)：

```
const originAddEventListener = EventTarget.prototype.addEventListener;
EventTarget.prototype.addEventListener = function (type, listener, options) {
  const wrappedListener = function (...args) {
    try {
      return listener.apply(this, args);
    }
    catch (err) {
      throw err;
    }
  }
  return originAddEventListener.call(this, type, wrappedListener, options);
}
```

简单解释一下：

- 改写了 `EventTarget` 的 `addEventListener` 方法；
- 对传入的 `listener` 进行包装，返回包装过的 `listener`，对其执行进行 `try-catch`；
- 浏览器不会对 `try-catch` 起来的异常进行跨域拦截，所以 `catch` 到的时候，是有堆栈信息的；
- 重新 `throw` 出来异常的时候，执行的是同域代码，所以 `window.onerror` 捕获的时候不会丢失堆栈信息；

利用包装 `addEventListener`，我们还可以达到「扩展堆栈」的效果：

```
(() => {
   const originAddEventListener = EventTarget.prototype.addEventListener;
   EventTarget.prototype.addEventListener = function (type, listener, options) {
+    // 捕获添加事件时的堆栈
+    const addStack = new Error(`Event (${type})`).stack;
     const wrappedListener = function (...args) {
       try {
         return listener.apply(this, args);
       }
       catch (err) {
+        // 异常发生时，扩展堆栈
+        err.stack += '\n' + addStack;
         throw err;
       }
     }
     return originAddEventListener.call(this, type, wrappedListener, options);
   }
 })();
```

# 崩溃和卡顿

卡顿也就是网页暂时响应比较慢， `JS` 可能无法及时执行。但崩溃就不一样了，网页都崩溃了，`JS` 都不运行了，还有什么办法可以监控网页的崩溃，并将网页崩溃上报呢？

崩溃和卡顿也是不可忽视的，也许会导致你的用户流失。

1.利用 `window` 对象的 `load` 和 `beforeunload` 事件实现了网页崩溃的监控。

不错的文章，推荐阅读：[Logging Information on Browser Crashes](http://jasonjl.me/blog/2015/06/21/taking-action-on-browser-crashes/)。

```
window.addEventListener('load', function () {
    sessionStorage.setItem('good_exit', 'pending');
    setInterval(function () {
        sessionStorage.setItem('time_before_crash', new Date().toString());
    }, 1000);
  });

  window.addEventListener('beforeunload', function () {
    sessionStorage.setItem('good_exit', 'true');
  });

  if(sessionStorage.getItem('good_exit') &&
    sessionStorage.getItem('good_exit') !== 'true') {
    /*
        insert crash logging code here
    */
    alert('Hey, welcome back from your crash, looks like you crashed on: ' + sessionStorage.getItem('time_before_crash'));
  }
```

2.基于以下原因，我们可以使用 `Service Worker` 来实现 [网页崩溃的监控](https://juejin.im/entry/5be158116fb9a049c6434f4a?utm_source=gold_browser_extension)：

- `Service Worker` 有自己独立的工作线程，与网页区分开，网页崩溃了，`Service Worker` 一般情况下不会崩溃；
- `Service Worker` 生命周期一般要比网页还要长，可以用来监控网页的状态；
- 网页可以通过 `navigator.serviceWorker.controller.postMessage API` 向掌管自己的 `SW` 发送消息。
