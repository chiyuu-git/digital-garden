---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/es/es-module/"}
---


# 严格模式

## 概述

[ECMAScript 5](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 的**严格模式**是采用具有限制性 JavaScript 变体的一种方式，从而使代码显示地 脱离“马虎模式/稀松模式/懒散模式“（sloppy）模式。

严格模式不仅仅是一个子集：它的产生是为了形成与正常代码不同的语义。

不支持严格模式与支持严格模式的浏览器在执行严格模式代码时会采用不同行为。

所以在没有对运行环境展开**特性测试**来验证对于严格模式相关方面支持的情况下，就算采用了严格模式也不一定会取得预期效果。严格模式代码和非严格模式代码可以共存，因此项目脚本可以渐进式地采用严格模式。

严格模式对正常的 JavaScript 语义做了一些更改：

1. 严格模式通过**抛出错误**来消除了一些原有**静默错误**。
2. 严格模式修复了一些导致 JavaScript 引擎难以执行优化的缺陷：有时候，相同的代码，严格模式可以比非严格模式下**运行得更快**。
3. 严格模式**禁用了**在 ECMAScript 的未来版本中可能会定义的一些语法。

## 开启严格模式

严格模式可以应用到整个脚本或个别函数中。不要在封闭大括弧 `{}` 内这样做，在这样的上下文中这么做是没有效果的。

### 为脚本开启严格模式

为整个脚本文件开启严格模式，需要在所有语句之前放一个特定语句 `"use strict";` （或 `'use strict';`）

```js
// 整个脚本都开启严格模式的语法
"use strict";
var v = "Hi!  I'm a strict mode script!";
```

> 不能盲目的合并冲突代码。试想合并一个严格模式的脚本和一个非严格模式的脚本：合并后的脚本代码看起来是严格模式。反之亦然：非严格合并严格看起来是非严格的。合并均为严格模式的脚本或均为非严格模式的都没问题，只有在合并严格模式与非严格模式有可能有问题。建议按一个个函数去开启严格模式（至少在学习的过渡期要这样做）.

### 为函数开启严格模式

同样的，要给某个函数开启严格模式，得把 `"use strict";` (或 `'use strict'; `) 声明*一字不漏地*放在函数体所有语句之前。

```js
function strict() {
  // 函数级别严格模式语法
  'use strict';
  function nested() { 
    return "And so am I!"; 
  }
  return "Hi!  I'm a strict mode function!  " + nested();
}

function notStrict() { 
  return "I'm not strict."; 
}
```

### Strict Mode for Modules

ES6 的模块自动采用严格模式，不管你有没有在模块头部加上 `"use strict";`

The entire contents of JavaScript modules are automatically in strict mode, with no statement needed to initiate it.

```js
function strict() {
    // because this is a module, I'm strict by default
}
export default strict;
```

## 限制概述

严格模式同时改变了语法及运行时行为。

严格模式主要有以下限制：

- 变量必须声明后再使用
- `eval` 不会在它的外层作用域引入变量
- `eval` 和 `arguments` 不能被重新赋值
- 禁止 `this` 指向全局对象

其中，尤其需要注意 `this` 的限制。ES6 模块之中，顶层的 `this` 指向 `undefined`，即不应该在顶层代码使用 `this`。

变化通常分为这几类：

+ 将问题直接转化为错误（如语法错误或运行时错误）
+ 简化了如何为给定名称的特定变量计算
+ 简化了 `eval` 以及 `arguments`
+ 将写 " 安全“JavaScript 的步骤变得更简单
+ 以及改变了预测未来 ECMAScript 行为的方式。

## 将过失错误转成异常

在严格模式下, 某些先前被接受的过失错误将会被认为是异常. JavaScript 被设计为能使新人开发者更易于上手, 所以有时候会给本来错误操作赋予新的不报错误的语义 (non-error semantics). 有时候这可以解决当前的问题, 但有时候却会给以后留下更大的问题. 严格模式则把这些失误当成错误, 以便可以发现并立即将其改正.

### 无法再意外创建全局变量

严格模式下无法再意外创建全局变量。在普通的 JavaScript 里面给一个错误命名的变量名赋值会使全局对象新增一个属性并继续“工作”。严格模式中意外创建全局变量被抛出错误替代：

```js
"use strict";
                       // 假如有一个全局变量叫做mistypedVariable
mistypedVaraible = 17; // 因为变量名拼写错误
                       // 这一行代码就会抛出 ReferenceError
```

### 静默失败的赋值操抛出异常

silently fail，即：不报错也没有任何效果

例如, `NaN` 是一个不可写的全局变量. 在正常模式下, 给 `NaN` 赋值不会产生任何作用; 开发者也不会受到任何错误反馈. 但在严格模式下, 给 `NaN` 赋值会抛出一个异常.

任何在正常模式下引起静默失败的赋值操作都会抛出异常

+ 给不可写属性赋值
+ 给只读属性（getter-only）赋值赋值
+ 给不可扩展对象（[non-extensible](https://developer.mozilla.org/zh-CN/JavaScript/Reference/Global_Objects/Object/preventExtensions) object）的新属性赋值

```js
"use strict";

// 给不可写属性赋值
var obj1 = {};
Object.defineProperty(obj1, "x", { value: 42, writable: false });
obj1.x = 9; // 抛出TypeError错误

// 给只读属性赋值
var obj2 = { get x() { return 17; } };
obj2.x = 5; // 抛出TypeError错误

// 给不可扩展对象的新属性赋值
var fixed = {};
Object.preventExtensions(fixed);
fixed.newProp = "ohai"; // 抛出TypeError错误
```

### 删除不可删除的属性时会抛出异常

在严格模式下, 试图删除不可删除的属性时会抛出异常

> 之前这种操作不会产生任何效果

```js
"use strict";
delete Object.prototype; // 抛出TypeError错误
```

### 重名属性被认为是语法错误

在 Gecko 版本 34 之前，严格模式要求一个对象内的所有属性名在对象内必须唯一。正常模式下重名属性是允许的，最后一个重名的属性决定其属性值。因为只有最后一个属性起作用，当代码要去改变属性值而不是修改最后一个重名属性的时候，复制这个对象就产生一连串的 bug。在严格模式下，重名属性被认为是语法错误：

```js
"use strict";
var o = { p: 1, p: 2 }; // !!! 语法错误
```

> 仍没有实现

### 严格模式要求函数的参数名唯一

在正常模式下, 最后一个重名参数名会掩盖之前的重名参数. 之前的参数仍然可以通过 `arguments[i] 来访问`, 还不是完全无法访问. 然而, 这种隐藏毫无意义而且可能是意料之外的 (比如它可能本来是打错了), 所以在严格模式下重名参数被认为是语法错误:

```js
function sum(a, a, c) { // !!! 语法错误
  "use strict";
  return a + a + c; // 代码运行到这里会出错
}
```

### 严格模式禁止八进制数字语法

ECMAScript5 并不包含八进制语法, 但所有的浏览器都支持这种以零 (`0`) 开头的八进制语法: `0644 === 420` 还有 `"\045" === "%"`

> 在 ECMAScript 6 中支持为一个数字加 "`0`o" 的前缀来表示八进制数

```js
var a = 0o10; // ES6: 八进制
```

### 禁止设置 [primitive](https://developer.mozilla.org/en-US/docs/Glossary/primitive) 值的属性

ECMAScript 6 中的严格模式禁止设置 [primitive](https://developer.mozilla.org/en-US/docs/Glossary/primitive) 值的属性.不采用严格模式,设置属性将会简单忽略 (no-op),采用严格模式,将抛出 [`TypeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypeError) 错误

```js
(function() {
  "use strict";

  false.true = "";              //TypeError
  (14).sailing = "home";        //TypeError
  "with".you = "far away";      //TypeError
})();
```

## 简化变量的使用

严格模式简化了代码中变量名字映射到变量定义的方式. 很多编译器的优化是依赖存储变量 X 位置的能力：这对全面优化 JavaScript 代码至关重要. JavaScript 有些情况会使得代码中名字到变量定义的基本映射只在运行时才产生. 严格模式移除了大多数这种情况的发生, 所以编译器可以更好的优化严格模式的代码.

**严格模式禁用 with**

**eval**

**严格模式禁止删除声明变量**

`delete name` 在严格模式下会引起语法错误：

```js
"use strict";

var x;
delete x; // !!! 语法错误
```

## 让 Arguments 变的简单

第一, 和 `arguments` 不能通过程序语法被绑定 (be bound) 或赋值. 以下的所有尝试将引起语法错误:

```js
"use strict";
arguments++;
var obj = { set p(arguments) { } };
try { } catch (arguments) { }
function arguments() { }
var f = new Function("arguments", "'use strict'; return 17;");
```

第二，严格模式下，参数的值不会随 arguments 对象的值的改变而变化。在正常模式下，对于第一个参数是 arg 的函数，对 arg 赋值时会同时赋值给 arguments[`0`]，反之亦然（除非没有参数，或者 arguments[`0`] 被删除）。严格模式下，函数的 arguments 对象会保存函数被调用时的原始参数。arguments[i] 的值不会随与之相应的参数的值的改变而变化，同名参数的值也不会随与之相应的 arguments[i] 的值的改变而变化。

```js
function f(a) {
  "use strict";
  a = 42;
  return [a, arguments[0]];
}
var pair = f(17);
console.assert(pair[0] === 42); // true
console.assert(pair[1] === 17); // true
```

第三，不再支持 `arguments.callee`。正常模式下，`arguments.callee` 指向当前正在执行的函数。这个作用很小：直接给执行函数命名就可以了！此外，`arguments.callee` 十分不利于优化，例如内联函数，因为 `arguments.callee` 会依赖对非内联函数的引用。在严格模式下，`arguments.callee` 是一个不可删除属性，而且赋值和读取时都会抛出异常：

```js
"use strict";
var f = function() { return arguments.callee; };
f(); // 抛出类型错误
// Uncaught TypeError: 'caller', 'callee', and 'arguments' properties may not be accessed on strict mode functions or the arguments objects for calls to them
```

## " 安全的 " JavaScript

第一，在严格模式下通过 `this` 传递给一个函数的值不会被强制转换为一个对象。对一个普通的函数来说，`this` 总会是一个对象：

+ 不管调用时 `this` 它本来就是一个对象；
+ 还是用布尔值，字符串或者数字调用函数时函数里面被封装成对象的 `this`；
+ 还是使用 `undefined` 或者 `null` 调用函数式 `this` 代表的全局对象（使用 [`call`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call), [`apply`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) 或者 `bind` 方法来指定一个确定的 `this`）。

这种自动转化为对象的过程不仅是一种性能上的损耗，同时在浏览器中暴露出全局对象也会成为安全隐患，因为全局对象提供了访问那些所谓安全的 JavaScript 环境必须限制的功能的途径。所以对于一个开启严格模式的函数，指定的 `this` 不再被封装为对象，而且如果没有指定 `this` 的话它值是 `undefined`：

```js
"use strict";
function fun() { return this; }
console.assert(fun() === undefined);
console.assert(fun.call(2) === 2);
console.assert(fun.apply(null) === null);
console.assert(fun.call(undefined) === undefined);
console.assert(fun.bind(true)() === true);
```

第二，在严格模式中再也不能通过广泛实现的 ECMAScript 扩展“游走于”JavaScript 的栈中。在普通模式下用这些扩展的话，当一个叫 `fun` 的函数正在被调用的时候，`fun.caller` 是最后一个调用 `fun` 的函数，而且 `fun.arguments` 包含调用 `fun` 时用的形参。这两个扩展接口对于“安全”JavaScript 而言都是有问题的，因为他们允许“安全的”代码访问 " 专有 " 函数和他们的（通常是没有经过保护的）形参。如果 `fun` 在严格模式下，那么 `fun.caller` 和 `fun.arguments` 都是不可删除的属性而且在存值、取值时都会报错：

```js
function restricted() {
  "use strict";
  restricted.caller;    // 抛出类型错误
  restricted.arguments; // 抛出类型错误
}

function privilegedInvoker() {
  return restricted();
}

privilegedInvoker();
```

第三，严格模式下的 `arguments` 不会再提供访问与调用这个函数相关的变量的途径。在一些旧时的 ECMAScript 实现中 `arguments.caller` 曾经是一个对象，里面存储的属性指向那个函数的变量。这是一个 [安全隐患](http://stuff.mit.edu/iap/2008/facebook/)，因为它通过函数抽象打破了本来被隐藏起来的保留值；它同时也是引起大量优化工作的原因。出于这些原因，**现在的浏览器没有实现它**。但是因为它这种历史遗留的功能，`arguments.caller` 在严格模式下同样是一个不可被删除的属性，在赋值或者取值时会报错：

```js
"use strict";
function fun(a, b) {
  "use strict";
  var v = 12;
  return arguments.caller; // 抛出类型错误，现代版本undefined
}
fun(1, 2); // 不会暴露v（或者a，或者b）
```

### Arguments 总结

作为为数组的功能依然存在，可以正常使用，其他全都没有了

## 为未来的 ECMAScript 版本铺平道路

未来版本的 ECMAScript 很有可能会引入新语法，ECMAScript5 中的严格模式就提早设置了一些限制来减轻之后版本改变产生的影响。如果提早使用了严格模式中的保护机制，那么做出改变就会变得更容易。

第一，在严格模式中一部分字符变成了保留的关键字。这些字符包括 `implements`, `interface`, `let`, `package`, `private`, `protected`, `public`, `static` 和 `yield`。在严格模式下，你不能再用这些名字作为变量名或者形参名。

第二，[严格模式禁止了不在脚本或者函数层面上的函数声明](http://whereswalden.com/2011/01/24/new-es5-strict-mode-requirement-function-statements-not-at-top-level-of-a-program-or-function-are-prohibited/)。在浏览器的普通代码中，在“所有地方”的函数声明都是合法的。这并不在 ES5 规范中（甚至是 ES3）！这是一种针对不同浏览器中不同语义的一种延伸。未来的 ECMAScript 版本很有希望制定一个新的，针对不在脚本或者函数层面进行函数声明的语法。[在严格模式下禁止这样的函数声明](http://wiki.ecmascript.org/doku.php?id=conventions:no_non_standard_strict_decls) 对于将来 ECMAScript 版本的推出扫清了障碍：

```js
"use strict";
if (true) {
  function f() { } // !!! 语法错误
  f();
}

for (var i = 0; i < 5; i++) {
  function f2() { } // !!! 语法错误
  f2();
}

function baz() { // 合法
  function eit() { } // 同样合法
}
```

这种禁止放到严格模式中并不是很合适，因为这样的函数声明方式从 ES5 中延伸出来的。但这是 ECMAScript 委员会推荐的做法，浏览器就实现了这一点。

> 实践中这一点仍然没有实现

# 模块化的演变

## 全局 Function 模式

全局函数模式: 将不同的功能封装成不同的全局函数

问题: Global 被污染了, 很容易引起命名冲突

## Namespace 模式

namespace 模式: 简单对象封装

作用: 减少了全局变量

问题: 不安全 (数据不是私有的, 外部可以直接修改)

```js
// module.js
let myModule = {
  data: 'atguigu.com',
  foo() {
    console.log(`foo() ${this.data}`)
  }
}
```

```html
<script src="module.js"></script>
<script>
  myModule.foo()
</script>
```

## IFEE 模式

IIFE 模式: 匿名函数自调用 (闭包)

IIFE : immediately-invoked function expression(立即调用函数表达式)

作用: 数据是私有的, 外部只能通过暴露的方法操作

问题: 如果当前这个模块依赖另一个模块怎么办?

```js
(function (widdow) {
  let data = 'iife'
  
  function get(){
    return data
  }
  
  function set(value){
    data = value
  }
  
  window.myModule = {get, set}
})(window)
```

```html
<script src="module.js"></script>
<script>
  myModule.get()
  myModule.set(1)
  console.log(myModule.data) // undefined , 无法访问闭包内的变量
</script>
```

## IFEE 模式增强

IIFE 模式增强 : 引入依赖

这就是现代模块实现的基石

```js
(function (widdow, $) {
  let data = 'iife'
  
  function get(){
    return data
  }
  
  function set(value){
    if($.check(value)){
      data = value
    }
  }
  
  window.myModule = {get, set}
})(window, depend)
```

## Load Script

一个页面需要引入多个 js 文件

问题:

- 请求过多
- 依赖模糊
- 难以维护

为了解决这个问题，产生了 CommonJS 规范，实现了 JS 的模块化，可惜可惜大多浏览器并不支持，只能用于 nodejs

于是 CommonJS 开始分裂，变异了一个名为 AMD 规范的模块，可以用于浏览器端，而由于 AMD 与 CommonJS 规范相去甚远，于是 AMD 自立门户，并且推出了 requireJS 这个框架，用于实现并推广 AMD 规范。

正因为 AMD 与 CommonJS 相去甚远，且用于不同的环境，为了能够兼容两个平台，UMD 应运而生，不过笔者认为仅仅是一个 polyfill，以兼容两个平台。

此时，CommonJS 的拥护者认为，浏览端也可以实现 CommonJS 的规范，于是稍作改动，形成了 CMD 规范，并且推出了 seajs 这个框架。

正在 AMD 与 CMD 打得火热的时候，ECMAScript6 给 JS 本身定了一个模块加载的功能，ES6 表示“你们也别争了，JS 模块有原生的语法了”。

# CommonJS

ES5 中没有原生支持模块化，我们只能通过 script 标签引入 js 文件来实现模块化

在 node 中为了对模块管理，引入了 CommonJS 规范

每个文件都可以当成一个模块

## 概念

在 commonjs 中一个 js 文件就是一个模块，默认情况下在 js 文件中编写的内容，都是运行在一个独立的函数中，外部的模块无法访问

## 暴露模块@@@

```js
module.exports = value;

exports.xxx = value; // 改变exports对象的属性

// console.log(module.exports == exports); ==> true

// 错误示范 更改了 exports 变量 切断了与堆内存的联系
// 不再指向与module.exports相同的地址 导致暴露失败
exports = {
  
}
```

暴露的本质：exports 对象

因此通过 exports 只能通过.来暴露，不能重新赋值

而 module.exports 两者均可

## 引入模块

类似于前端中引入其他 js 文件，传递文件的路径作为标识，引入的 JS 文件会自动执行，

```js
var module = require(模块名或模块路径);

var fs = require("fs");
var express = require("express");
```

> 对于**核心模块**（npm 中下载的模块），直接使用模块的名字对其进行引入
>
> 对于自定义的**文件模块**，需要通过文件的路径来对模块进行引入

使用 require() 引入模块之后，该函数会返回一个对象，这个对象代表着引入的模块，通过该对象的方法或属性调用该模块的对象

- 可以避免污染全局空间
- 使用模块名字引入模块时，node 会先在当前的目录的 node_modules 中寻找是否含有该模块，有则直接使用，如果没有则会去上一层目录的 node_modules 中寻找，直到找到为止，直到磁盘的根目录，没有则报错

## 底层原理

在 node 中有一个全局对象，global，它的作用于网页中的 window 类似，在全局中的变量会作为 global 的属性保存，函数会作为 global 的方法保存

> 不用 var 声明变量会变成全局变量这一特点在 ndoe 也存在

每个模块运行在独立的函数中，通过 arguments 可以验证

当 node 在执行模块中的代码时，实际上模块中的代码都是包装在一个函数中，并且在函数执行时，同时传递进了 5 个实参

```js
function (exports,require,module,__filename,__dirname){
  // ...
}
```

1. exports：用于暴露函数内部的对象
2. require：一个函数，用于引入外部的模块
3. module：module 代表的是当前模块本身，exports 实际上是 module 的属性
4. __filename：当前模块的完整路径
5. __dirname：当前模块所在的文件夹的完整路径

## 实现

服务器端实现：Node.js

浏览器端实现：Browserfly

## 区别

Node.js 运行时动态加载模块 (同步)

Browserify 是在转译 (编译) 时就会加载打包 (**合并**)require(**引入**) 的模块

# ES6 Module

## 概念

在 ES6 之前，社区制定了一些模块加载方案，最主要的有 CommonJS 和 AMD 两种。前者用于服务器，后者用于浏览器。

ES6 在语言标准的层面上，实现了模块功能，而且实现得相当简单，完全可以取代 CommonJS 和 AMD 规范，成为浏览器和服务器通用的模块解决方案。

ES6 模块化的好处

- 不再需要 `UMD` 模块格式了，将来服务器和浏览器都会支持 ES6 模块格式。目前，通过各种工具库，其实已经做到了这一点。
- 将来浏览器的新 API 就能用模块格式提供，不再必须做成全局变量或者 `navigator` 对象的属性。
- 不再需要对象作为命名空间（比如 `Math` 对象），未来这些功能可以通过模块提供。

模块功能主要由两个命令构成：`export` 和 `import`。`export` 命令用于规定模块的**对外**接口，`import` 命令用于**引入**其他模块提供的功能。

### 模块

模块（Modules）使用不同方式加载的 JS 文件 (与 JS 原先的脚本加载方式相对）。这种不同模式很有必要，因为它与脚本（script）有大大不同的语义：

+ 模块代码自动运行在严格模式下，并且没有任何办法跳出严格模式；
+ 在模块的顶级作用域创建的变量，不会被自动添加到共享的全局作用域，它们只会在模块顶级作用域的内部存在；
+ 子模块可以访问上级模块的作用域变量嘛？？不可以，只能访问自己的，模块间的通信就像是组件通信一样只能借助其他手段。
+ 模块顶级作用域的	this	值为 undefined ；
+ 模块不允许在代码中使用 HTML 风格的注释（这是 JS	来自于早期浏览器的历史遗留特性）；
+ 对于需要让模块外部代码访问的内容，模块必须导出它们；
+ 允许模块从其他模块导入绑定。

## 基本的导出

你可以使用 `export` 关键字将已发布代码部分公开给其他模块。最简单方法就是将 `export` 放置在任意变量、函数或类**声明**之前，从模块中将它们公开出去，就像这样：

```js
//	导出数据
export	var	color	=	"red";
export	let	name	=	"Nicholas";
export	const	magic Number	=	7;
//	导出函数
export	function	sum(num1,	num2)	{
  return	num1	+	num1;
}
//	导出类
export	class	Rectangle	{
  constructor(length,	width)	{
    this.length	=	length;
    this.width	=	width;
  }
}
//	此函数为模块私有
function	subtract(num1,	num2)	{
  return	num1	-	num2;
}
```

请注意，此例并未导出 `subtract()` 函数。此函数在模块外部不可访问，因为任意没有被显式导出的变量、函数或类都会在模块内保持私有。

`export` 的写法，除了像上面这样，还有另外一种：

```javascript
// 定义多个变量
var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;

// 稍后一起导出
export {firstName, lastName, year};
```

> 我觉得应该优先使用第一种写法，因为一个脚本可能非常大，在最尾部 export 的话会造成逻辑的分离

### 接口与变量

需要特别注意的是，`export` 命令规定的是**对外的接口**，必须与模块内部的变量建立一一对应关系。

```javascript
// 报错
export 1;

// 报错
var m = 1;
export m;
```

上面两种写法都会报错，因为没有提供对外的接口。第一种写法直接输出 1，第二种写法通过变量 `m`，还是直接输出 1。

`1` 只是一个值，外部没有手段可以访问该数值，所以它不是接口。

正确的写法是下面这样。

```javascript
// 写法一
export var m = 1;

// 写法二
var m = 1;
export {m};

// 写法三
var n = 1;
export {n as m};
```

上面三种写法都是正确的，规定了对外的接口 `m`。其他脚本可以通过这个接口，取到值 `1`。它们的实质是，在接口名与模块内部变量之间，建立了一一对应的关系。

同样的，`function` 和 `class` 的输出，也必须遵守这样的写法。

```javascript
// 报错
function f() {}
export f;

// 正确
export function f() {};

// 正确
function f() {}
export {f};
```

## 基本的导入

一旦你有了包含导出的模块，就能在其他模块内使用 `import` 关键字来访问已被导出的功能。

`import` 语句有两个部分，一是需要**导入的标识符**，二是需导入的标识符的**来源模块**。

此处是导入语句的基本形式：

```js
import	{	identifier1,	identifier2	}	from	"./example.js";
```

`import` 之后的花括号指明了从给定模块导入**对应的绑定**，大括号里面的变量名，必须与被导入模块对外接口的名称相同。

`from` 关键字则指明了需要导入的模块。模块由一个表示模块路径的字符串（被称为**模块说明符，module specifier**）来指定。

> 在**浏览器环境**中导入模块，使用与 `<script>` 元素相同的路径格式，这表示你必须在其中包含**文件扩展名**。
>
> 而**Node.js**则遵循了它的传统惯例，基于文件系统前缀来分辨本地文件与包（package），例如，example 代表一个包，而		./example.js	则代表一个本地文件。

> 导入绑定的列表看起来与对象解构相似，其实可以理解为对 default 对象 进行了解构

当从模块导入了一个绑定时，这意味着：

- 不能再定义另一个同名变量（包括导入另一个同名绑定）
- 不能修改它的值。

```js
import {a} from './xxx.js'
a = {}; // Syntax Error : 'a' is read-only;
a.foo = 'hello'; // 合法操作
```

但是，如果 `a` 是一个对象，改写 `a` 的**属性**是允许的。不过，这种写法很难查错，建议凡是输入的变量，都当作完全只读，**轻易不要改变它的属性**。

### 完全导入一个模块

还有一种特殊情况，即允许你将整个模块当作单一对象进行导入，该模块的所有导出都会作为对象的属性存在。例如：

```js
//	完全导入
import	*	as	example	from	"./example.js";
console.log(example.sum(1,example.magicNumber));//	8
console.log(example.multiply(1,	2));//	2
```

这种导入格式被称为**命名空间导入（namespace import）**，这是因为该 example 对象并不存在于 example.js 文件中，而是作为一个命名空间对象被创建使用，其中包含了 example.js 的所有导出成员。

**注意：**模块整体加载所在的那个对象（上例是 `example`），应该是可以静态分析的，所以不允许运行时改变。下面的写法都是不允许的。

```js
import	*	as	example	from	"./example.js";
example.sum = function(){} //报错
example.magicNumber = 1 // 报错
```

### 无绑定的导入

因为 `import` 语句会**执行所加载**的模块，因此可以有下面的写法。

```javascript
import 'lodash'; // 仅仅执行`lodash`模块，但是不输入任何值。
```

无绑定的导入最有可能被用于创建 `polyfill` 与 `shim`（为新语法在旧环境中运行提供向下兼容的方式）

## 重命名导出与导入

通常情况下，`export` 输出的变量就是本来的名字，但是可以使用 `as` 关键字重命名。

```javascript
function v1() { ... }
function v2() { ... }

export {
  v1 as streamV1,
  v2 as streamV2,
  v2 as streamLatestVersion
};
```

```js
export default {
  streamV1: v1,
  streamV2: v2,
  streamV3: v3,
}
```

上面代码使用 `as` 关键字，重命名了函数 `v1` 和 `v2` 的对外接口。重命名后，`v2` 可以用不同的名字输出两次。

如果想为输入的变量重新取一个名字，`import` 命令要使用 `as` 关键字，将输入的变量重命名。-

```javascript
import { lastName as surname } from './profile.js';
```

## 模块的默认值

### 默认导出

从前面的例子可以看出，使用 `import` 命令的时候，用户需要知道所要加载的变量名或函数名，否则无法加载。但是，用户肯定希望快速上手，未必愿意阅读文档，去了解模块有哪些属性和方法。

为了给用户提供方便，让他们不用阅读文档就能加载模块，就要用到 `export default` 命令，为模块指定默认输出。

```javascript
export default function () {
  console.log('foo');
}
```

```js
export default class { ... }
```

`export default` 命令用在**非匿名函数**前，也是可以的。

```javascript
// export-default.js
export default function foo() {
  console.log('foo');
}

// 或者写成
function foo() {
  console.log('foo');
}

export default foo;
```

上面代码中，`foo` 函数的函数名 `foo`，**在模块外部是无效的。加载的时候，视同匿名函数加载**。

### 默认导入

其他模块加载该模块时，`import` 命令可以为该匿名函数指定任意名字。

```javascript
// import-default.js
import customName from './export-default';
customName(); // 'foo'
```

上面代码的 `import` 命令，可以用任意名称指向 `export-default.js` 输出的方法，这时就不需要知道原模块输出的函数名。需要注意的是，这时 `import` 命令后面，**不使用大括号**。

### 本质

#### 表现一

**本质上**，`export default` 就是输出一个叫做 `default` 的变量或方法，所以它后面不能跟**变量声明语句**。

```javascript
  // 正确
  export var a = 1;
  
  // 正确
  var a = 1;
  export default a;
  
  // 错误
  export default var a = 1;
  ```

上面代码中，`export default a` 的含义是将变量 `a` 的值赋给变量 `default`。所以，最后一种写法会报错。

#### 表现二

**本质上**，`export default` 就是输出一个叫做 `default` 的变量或方法，所以可以直接将一个值写在 `export default` 之后。

```javascript
  // 正确
  export default 42;
  
  // 报错
  export 42;
```

上面代码中，后一句报错是因为没有指定对外的接口，而前一句指定对外**接口**为 `default`。

#### 表现三

**本质上**，`export default` 就是输出一个叫做 `default` 的变量或方法，然后系统允许你为它取任意名字。所以，下面的写法是有效的。

```javascript
  // modules.js
  function add(x, y) {
    return x * y;
  }
  export {add as default};
  // 等同于
  // export default add;
  
  // app.js
  import { default as foo } from 'modules';
  // 等同于
  // import foo from 'modules';
```

#### 表现四：默认完全导入

```js
  // es6.mjs
  export var a = 1
  export var d = 1
  var e =2 
  export default e
  
  // test.mjs
  import * as test from './es6.mjs'
  
  console.log(test); // [Module] { a: 1, d: 1, default: 2 }
```

default 做为一个属性名

注意：默认导入和完全导入的区别，默认导入也只是完全导入的一个 default 属性而已

### 对比

下面比较一下默认输出和正常输出。

```javascript
// 第一组
export default function crc32() { // 输出
  // ...
}

import crc32 from 'crc32'; // 输入

// 第二组
export function crc32() { // 输出
  // ...
};

import {crc32} from 'crc32'; // 输入
```

**语法：**

- 第一组是使用 `export default` 时，对应的 `import` 语句不需要使用大括号
- 第二组是不使用 `export default` 时，对应的 `import` 语句需要使用大括号。

**数量：**

- 一个模块只能有一个默认输出，因此 `export default` 命令**只能使用一次**。

  > 所以，import 命令后面才不用加大括号，因为只可能唯一对应 `export default` 命令

- `export` 命令可以使用多次

### 示例

有了 `export default` 命令，输入模块时就非常直观了，以输入 lodash 模块为例。

```javascript
import _ from 'lodash';
```

如果想在一条 `import` 语句中，同时输入默认方法和其他接口，可以写成下面这样。

```javascript
import _, { each, forEach } from 'lodash';
```

```javascript
// 对应上面代码的 export 语句如下。
export default function (obj) {
  // ···
}

export function each(obj, iterator, context) {
  // ···
}

export { each as forEach };
```

上面代码的最后一行的意思是，暴露出 `forEach` 接口，默认指向 `each` 接口，即 `forEach` 和 `each` 指向同一个方法。

## 导出与导入的复合写法

如果在一个模块之中，先输入后输出同一个模块，`import` 语句可以与 `export` 语句写在一起。

```javascript
export { foo, bar } from 'my_module';

// 可以简单理解为
import { foo, bar } from 'my_module';
export { foo, bar };
```

上面代码中，`export` 和 `import` 语句可以结合在一起，写成一行。但需要注意的是，写成一行以后，`foo` 和 `bar` 实际上并没有被导入当前模块，只是相当于对外转发了这两个接口，导致**当前模块不能直接使用 `foo` 和 `bar`。**

模块的接口改名和整体输出，也可以采用这种写法。

```javascript
// 接口改名
export { foo as myFoo } from 'my_module';

// 整体输出
export * from 'my_module';
```

默认接口的写法如下。

```javascript
export { default } from 'foo';
```

具名接口改为默认接口的写法如下。

```javascript
export { es6 as default } from './someModule';

// 等同于
import { es6 } from './someModule';
export default es6;
```

同样地，默认接口也可以改名为具名接口。

```javascript
export { default as es6 } from './someModule';
```

### 注意

`export * from` 命令会忽略模块的 `default` 方法。

下面三种 `import` 语句，没有对应的复合写法。

```javascript
  import * as someIdentifier from "someModule";
  import someIdentifier from "someModule";
  import someIdentifier, { namedIdentifier } from "someModule";
```

为了做到形式的对称，现在有 [提案](https://github.com/leebyron/ecmascript-export-default-from)，提出补上这三种复合写法。

```javascript
  export * as someIdentifier from "someModule";
  export someIdentifier from "someModule";
  export someIdentifier, { namedIdentifier } from "someModule";
```

### 模块的继承

假设有一个 `circleplus` 模块，继承了 `circle` 模块。

```javascript
// circleplus.js

export * from 'circle';
export var e = 2.71828182846;
export default function(x) {
  return Math.exp(x);
}
```

上面代码中的 `export *`，表示再输出 `circle` 模块的所有属性和方法。

> 注意，`export *` 命令会忽略 `circle` 模块的 `default` 方法。然后，上面代码又输出了自定义的 `e` 变量和默认方法。
>
> 这个时候不可以再用 as 关键字重命名了，暂时还不支持这种复合写法

这时，也可以将 `circle` 的属性或方法，改名后再输出。

```javascript
// circleplus.js

export { area as circleArea } from 'circle';
```

上面代码表示，只输出 `circle` 模块的 `area` 方法，且将其改名为 `circleArea`。

加载上面模块的写法如下。

```javascript
// main.js

import * as math from 'circleplus';
import exp from 'circleplus';
console.log(exp(math.e));
```

上面代码中的 `import exp` 表示，将 `circleplus` 模块的默认方法加载为 `exp` 方法。

### 跨模块常量

本书介绍 `const` 命令的时候说过，`const` 声明的常量只在当前代码块有效。如果想设置跨模块的常量（即跨多个文件），或者说一个值要被多个模块共享，可以采用下面的写法。

```javascript
// constants.js 模块
export const A = 1;
export const B = 3;
export const C = 4;

// test1.js 模块
import * as constants from './constants';
console.log(constants.A); // 1
console.log(constants.B); // 3

// test2.js 模块
import {A, B} from './constants';
console.log(A); // 1
console.log(B); // 3
```

如果要使用的常量非常多，可以建一个专门的 `constants` 目录，将各种常量写在不同的文件里面，保存在该目录下。

```javascript
// constants/db.js
export const db = {
  url: 'http://my.couchdbserver.local:5984',
  admin_username: 'admin',
  admin_password: 'admin password'
};

// constants/user.js
export const users = ['root', 'admin', 'staff', 'ceo', 'chief', 'moderator'];
```

然后，将这些文件输出的常量，合并在 `index.js` 里面。

```javascript
// constants/index.js
export {db} from './db';
export {users} from './users';
```

使用的时候，直接加载 `index.js` 就可以了。

```javascript
// script.js
import {db, users} from './constants/index';
```

## import()

### 简介

前面介绍过，`import` 命令会被 JavaScript 引擎静态分析，先于模块内的其他语句执行（`import` 命令叫做“连接” binding 其实更合适）。所以，下面的代码会报错。

```javascript
  // 报错
  if (x === 2) {
    import MyModual from './myModual';
  }
```

上面代码中，引擎处理 `import` 语句是在编译时，这时不会去分析或执行 `if` 语句，所以 `import` 语句放在 `if` 代码块之中毫无意义，因此会报句法错误，而不是执行时错误。也就是说，`import` 和 `export` 命令只能在模块的顶层，不能在代码块之中（比如，在 `if` 代码块之中，或在函数之中）。

这样的设计，固然有利于编译器提高效率，但也导致无法在运行时加载模块。在语法上，**条件加载**就不可能实现。如果 `import` 命令要取代 Node 的 `require` 方法，这就形成了一个障碍。因为 `require` 是运行时加载模块，`import` 命令无法取代 `require` 的动态加载功能。

```javascript
  const path = './' + fileName;
  const myModual = require(path);
```

上面的语句就是动态加载，`require` 到底加载哪一个模块，只有运行时才知道。`import` 命令做不到这一点。

因此，有一个 [提案](https://github.com/tc39/proposal-dynamic-import)，建议引入 `import()` 函数，完成动态加载。

```javascript
  import(specifier)
```

上面代码中，`import` 函数的参数 `specifier`，**指定所要加载的模块的位置**。`import` 命令能够接受什么参数，`import()` 函数就能接受什么参数，两者区别主要是后者为动态加载。

`import()` 返回一个 Promise 对象。下面是一个例子。

```javascript
  const main = document.querySelector('main');
  
  import(`./section-modules/${someVariable}.js`)
    .then(module => {
      module.loadPageInto(main);
    })
    .catch(err => {
      main.textContent = err.message;
    });
```

`import()` 函数可以用在任何地方，不仅仅是模块，非模块的脚本也可以使用。它是运行时执行，也就是说，什么时候运行到这一句，就会加载指定的模块。另外，`import()` 函数与所加载的模块没有静态连接关系，这点也是与 `import` 语句不相同。

**总结**：`import()` 类似于 Node 的 `require` 方法，**区别主要是**

  - **前者是异步加载，后者是同步加载。**
  - 前者返回的是一个 Promise 对象，后者是一个普通对象

### 适用场合

#### （1）按需加载

`import()` 可以在需要的时候，再加载某个模块。

```javascript
  button.addEventListener('click', event => {
    import('./dialogBox.js')
    .then(dialogBox => {
      dialogBox.open();
    })
    .catch(error => {
      /* Error handling */
    })
  });
```

上面代码中，`import()` 方法放在 `click` 事件的监听函数之中，只有用户点击了按钮，才会加载这个模块

#### （2）条件加载

`import()` 可以放在 `if` 代码块，根据不同的情况，加载不同的模块。

```javascript
  if (condition) {
    import('moduleA').then(...);
  } else {
    import('moduleB').then(...);
  }
```

上面代码中，如果满足条件，就加载模块 A，否则加载模块 B。

#### （3）动态的模块路径

`import()` 允许模块路径动态生成。

```javascript
  import(f())
  .then(...);
```

上面代码中，根据函数 `f` 的返回结果，加载不同的模块。

### 注意点

`import()` 加载模块成功以后，这个模块会作为一个对象，当作 `then` 方法的参数。因此，可以使用对象解构赋值的语法，获取输出接口。

```javascript
  import('./myModule.js')
  .then(({export1, export2}) => {
    // ...·
  });
```

上面代码中，`export1` 和 `export2` 都是 `myModule.js` 的输出接口，可以解构获得。

如果模块有 `default` 输出接口，可以用参数直接获得。

```javascript
  import('./myModule.js')
  .then(myModule => {
    console.log(myModule.default);
  });
```

上面的代码也可以使用具名输入的形式。

```javascript
  import('./myModule.js')
  .then(({default: theDefault}) => {
    console.log(theDefault);
  });
  ```

如果想同时加载多个模块，可以采用下面的写法。

```javascript
  Promise.all([
    import('./module1.js'),
    import('./module2.js'),
    import('./module3.js'),
  ])
  .then(([module1, module2, module3]) => {
     ···
  });
```

`import()` 也可以用在 async 函数之中。

```javascript
  async function main() {
    const myModule = await import('./myModule.js');
    const {export1, export2} = await import('./myModule.js');
    const [module1, module2, module3] =
      await Promise.all([
        import('./module1.js'),
        import('./module2.js'),
        import('./module3.js'),
      ]);
  }
  main();
```

# ES6 模块与 CommonJS 模块 @@@

## 共同点

### 整体执行

即使是没有暴露的结构也会一起运行

```js
// modules.js
exports.counter = function(){
  return ++obj.count
}

console.log(12)

// test.js
let test = require('./modules')
console.log(1);
```

```js
// es6.mjs
export var b = 1
console.log(12)
// index.mjs
import {b} from './es6modules.mjs'
console.log(b)
```

先打印 12，再打印 1

### 单次执行

然而要记住，无论你对同一个模块使用了多少次 `import` 语句，该模块都只会被执行一次。在导出模块的代码执行之后，已被实例化的模块就被保留在内存中，并随时都能被其他 `import` 所引用。研究以下例子：

```js
import	{	sum	}	from	"./example.js";
import	{	multiply	}	from	"./example.js";
import	{	magic Number	}	from	"./example.js";
```

尽管此处的模块使用了三个 `import` 语句，但 `example.js` 只会被执行一次。

### 共享实例

若同一个应用中的其他模块打算从 `example.js` 导入绑定，则那些模块都会使用这段代码中所用的同一个模块实例。

```js
// mod.js
function C() {
  this.sum = 0;
  this.add = function () {
    this.sum += 1;
  };
  this.show = function () {
    console.log(this.sum);
  };
}

export let c = new C();
```

上面的脚本 `mod.js`，输出的是一个 `C` 的实例。不同的脚本加载这个模块，得到的都是同一个实例。

```javascript
// x.js
import {c} from './mod';
c.add();

// y.js
import {c} from './mod';
c.show();

// main.js
import './x';
import './y';
```

现在执行 `main.js`，输出的是 `1`。

```bash
$ babel-node main.js
1
```

这就证明了 `x.js` 和 `y.js` 加载的都是 `C` 的同一个实例。

## 差异

它们有两个**重大差异**。

- CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。
- CommonJS 模块输出的是一个**值的拷贝**，ES6 模块输出的是**值的引用**。

### 运行时加载

CommonJS 和 AMD 模块，都只能在运行时确定模块的依赖关系，以及输入和输出的变量。

比如，CommonJS 模块就是对象（即 `module.exports`），该对象只有在脚本**运行完**才会生成，输入时必须查找对象属性。

```javascript
// CommonJS模块
let { stat, exists, readFile } = require('fs');

// 等同于
let _fs = require('fs');
let stat = _fs.stat;
let exists = _fs.exists;
let readfile = _fs.readfile;
```

上面代码的实质是整体加载 `fs` 模块（即加载 `fs` 的所有方法），生成一个对象（`_fs`），然后再从这个对象上面读取 3 个方法。这种加载称为**“运行时加载”**。

#### 缺点

因为只有运行时才能得到这个对象，导致完全没办法在编译时做“静态优化”。

### 静态加载

ES6 模块的设计思想是尽量的静态化，使得**编译时**就能确定模块的依赖关系，以及输入和输出的变量。以便让诸如文本编辑器之类的工具能轻易判断模块有哪些信息可用。

ES6 模块通过 `export` 命令显式指定输出的代码，再通过 `import` 命令输入。

```javascript
// ES6模块
import { stat, exists, readFile } from 'fs';
```

上面代码的实质是从 `fs` 模块加载 3 个方法，其他方法不加载。这种加载称为**“编译时加载”或者静态加载**，即 ES6 可以在编译时就完成模块加载，效率要比 CommonJS 模块的加载方式高。

#### 顶级作用域

由于 `import` 是静态执行，你只能在模块的顶级作用域使用 `export` 或者 `import`

也不能使用表达式和变量，这些只有在运行时才能得到结果的语法结构。

```javascript
// 报错
import { 'f' + 'oo' } from 'my_module';

// 报错
let module = 'my_module';
import { foo } from module;

// 报错
if (x === 1) {
  import { foo } from 'module1';
} else {
  import { foo } from 'module2';
}
```

上面三种写法都会报错，因为它们用到了表达式、变量和 `if` 结构。在静态分析阶段，这些语法都是没法得到值的。

#### 提升效果

**注意**，`import` 命令具有提升效果，会提升到整个模块的头部

```javascript
foo();

import { foo } from 'my_module';
```

上面的代码不会报错，因为 `import` 的执行早于 `foo` 的调用。这种行为的本质是，`import` 命令是**编译阶段执行**的，在代码运行之前。

目前阶段，通过 Babel 转码，CommonJS 模块的 `require` 命令和 ES6 模块的 `import` 命令，可以写在同一个模块里面，但是最好不要这样做。因为 `import` 在静态解析阶段执行，所以它是一个模块之中最早执行的。下面的代码可能不会得到预期结果。

```javascript
require('core-js/modules/es6.symbol');
require('core-js/modules/es6.promise');
import React from 'React';
```

由于 ES6 模块是编译时加载，使得静态分析成为可能。有了它，就能进一步拓宽 JavaScript 的语法，比如引入**宏（macro）**和**类型检验（type system）**这些只能靠静态分析实现的功能。

#### 缺点

当然，这也导致了没法引用 ES6 模块本身，因为它不是对象。

### 值的缓存

CommonJS 模块输出的是值的拷贝，也就是说，一旦输出一个值，模块内部的变化就影响不到这个值。请看下面这个模块文件 `lib.js` 的例子。

```javascript
// lib.js
var counter = 3;
function incCounter() {
  counter++;
}
module.exports = {
  counter: counter,
  incCounter: incCounter,
};
```

在 `main.js` 里面加载这个模块。

```javascript
// main.js
var mod = require('./lib');

console.log(mod.counter);  // 3
mod.incCounter();
console.log(mod.counter); // 3
```

上面代码说明，`lib.js` 模块加载以后，它的内部变化就影响不到输出的 `mod.counter` 了。这是因为 `mod.counter` 是一个**原始类型**的值，会被缓存。

除非写成一个函数，才能得到内部变动后的值。

```javascript
// lib.js
var counter = 3;
function incCounter() {
  counter++;
}
module.exports = {
  get counter() {
    return counter
  },
  incCounter: incCounter,
};
```

上面代码中，输出的 `counter` 属性实际上是一个取值器函数。现在再执行 `main.js`，就可以正确读取内部变量 `counter` 的变动了。

```bash
$ node main.js
3
4
```

如果是一个引用类型得值，可以获取到改动后的值，本质上和以前得 JS 一样，原始类型传递值，引用类型传递引用，真正特殊得其实是 ES6 的模块化语法

#### 可以赋值

```js
// modules.js
function C() {}
exports.c = new C();

// index.je
let test = require('./modules')

test.c = []
console.log(test.c); // Array(0) []
```

#### 删除缓存

通过删除缓存可以实现动态取值

https://blog.csdn.net/Calla_Lj/article/details/86156718

### 动态绑定

ES6 模块的运行机制与 CommonJS 不一样。JS 引擎对脚本静态分析的时候，遇到模块加载命令 `import`，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值。换句话说，ES6 的 `import` 有点像 Unix 系统的“符号连接”，原始值变了，`import` 加载的值也会跟着变。因此，ES6 模块是动态引用，并且不会缓存值，模块里面的变量绑定其所在的模块。

还是举上面的例子。

```javascript
// lib.js
export let counter = 3;
export function incCounter() {
  counter++;
}

// main.js
import { counter, incCounter } from './lib';
console.log(counter); // 3
incCounter();
console.log(counter); // 4
```

上面代码说明，ES6 模块输入的变量 `counter` 是活的，完全反应其所在模块 `lib.js` 内部的变化。

再举一个出现在 `export` 一节中的例子。

```javascript
// m1.js
export var foo = 'bar';
setTimeout(() => foo = 'baz', 500);

// m2.js
import {foo} from './m1.js';
console.log(foo);
setTimeout(() => console.log(foo), 500);
```

上面代码中，`m1.js` 的变量 `foo`，在刚加载时等于 `bar`，过了 500 毫秒，又变为等于 `baz`。

我们看看，`m2.js` 能否正确读取这个变化。

```bash
$ babel-node m2.js
bar
baz
```

上面代码表明，ES6 模块不会缓存运行结果，而是**动态**地去被加载的模块取值，并且变量**总是**绑定其所在的模块。

> 既然这个值是动态绑定的，那么不就可以用于组件间的通信了吗？为什么还需要 context 呢？又要包装组件，又要 useContext
>
> 关键在于触发组件的重新渲染！通过 context 读取的值，在变化的时候所有引用了该 context 的组件都会重新渲染，但是如果仅仅是简单的暴露是不会重新渲染的？我感觉到了 mobx 的味道

#### 无法赋值

由于 ES6 输入的模块变量，只是一个“符号连接”，所以这个变量是只读的，对它进行重新赋值会报错。

```javascript
// lib.js
export let obj = {};

// main.js
import { obj } from './lib';

obj.prop = 123; // OK
obj = {}; // TypeError
```

上面代码中，`main.js` 从 `lib.js` 输入变量 `obj`，可以对 `obj` 添加属性，但是重新赋值就会报错。因为变量 `obj` 指向的地址是只读的，不能重新赋值，这就好比 `main.js` 创造了一个名为 `obj` 的 `const` 变量。

# Module 的加载实现

## 浏览器加载

### 传统方法

HTML 网页中，浏览器通过 `<script>` 标签加载 JavaScript 脚本。

```html
<!-- 页面内嵌的脚本 -->
<script type="application/javascript">
  // module code
</script>

<!-- 外部脚本 -->
<script type="application/javascript" src="path/to/myModule.js">
</script>
```

上面代码中，由于浏览器脚本的默认语言是 JavaScript，因此 `type="application/javascript"` 可以省略。

默认情况下，浏览器是**同步**加载 JavaScript 脚本，即渲染引擎遇到 `<script>` 标签就会停下来，等到执行完脚本，再继续向下渲染。如果是外部脚本，还必须加入脚本下载的时间。

### 异步加载脚本 @@@

如果脚本体积很大，下载和执行的时间就会很长，因此造成浏览器堵塞，用户会感觉到浏览器“卡死”了，没有任何响应。这显然是很不好的体验，所以**浏览器允许脚本异步加载**，下面就是两种异步加载的语法。

```html
<script src="path/to/myModule.js" defer></script>
<script src="path/to/myModule.js" async></script>
```

上面代码中，`<script>` 标签打开 `defer` 或 `async` 属性，脚本就会异步加载。渲染引擎遇到这一行命令，就会开始下载外部脚本，但不会等它下载和执行，而是直接执行后面的命令。

`defer` 与 `async` 的区别是：

- `defer` 要等到**整个页面**在内存中正常渲染结束（DOM 结构完全生成，以及其他脚本执行完成），才会执行；
- `async` 一旦下载完，渲染引擎就会中断渲染，执行这个脚本以后，再继续渲染。

一句话，`defer` 是“渲染完再执行”，`async` 是“下载完就执行”。另外，如果有多个 `defer` 脚本，会按照它们在页面出现的顺序加载，而多个 `async` 脚本是不能保证加载顺序的。

如果网页有多个 `<script type="module">`，默认添加 defer 属性，它们会按照在页面出现的顺序依次执行

一旦使用了 `async` 属性，`<script type="module">` 就不会按照在页面出现的顺序执行，而是只要该模块加载完成，就执行该模块。

**从这一点可以看出 async 属性的权重更高，所以 module 默认是 defer 属性，更加可控**

需要注意的是：async 和 defer 对于内联脚本是无效的

### 加载模块

浏览器加载 ES6 模块，也使用 `<script>` 标签，但是要加入 `type="module"` 属性。

```html
<script type="module" src="./foo.js"></script>
```

上面代码在网页中插入一个模块 `foo.js`，由于 `type` 属性设为 `module`，所以浏览器知道这是一个 ES6 模块。

浏览器对于带有 `type="module"` 的 `<script>`，都是**异步加载**，不会造成堵塞浏览器，即等到整个页面渲染完，再执行模块脚本，等同于打开了 `<script>` 标签的 `defer` 属性。

```html
<script type="module" src="./foo.js"></script>
<!-- 等同于 -->
<script type="module" src="./foo.js" defer></script>
```

ES6 模块也允许内嵌在网页中，语法行为与加载外部脚本完全一致。

```html
<script type="module">
  import utils from "./utils.js";

  // other code
</script>
```

对于外部的模块脚本（上例是 `foo.js`），有几点需要注意。

- 代码是在模块作用域之中运行，而不是在全局作用域运行。模块内部的顶层变量，外部不可见。
- 模块脚本自动采用严格模式，不管有没有声明 `use strict`。
- 模块之中，可以使用 `import` 命令加载其他模块（`.js` 后缀不可省略，需要提供绝对 URL 或相对 URL），也可以使用 `export` 命令输出对外接口。
- 模块之中，顶层的 `this` 关键字返回 `undefined`，而不是指向 `window`。也就是说，在模块顶层使用 `this` 关键字，是无意义的。
- 同一个模块如果加载多次，将只执行一次。

下面是一个示例模块。

```javascript
import utils from 'https://example.com/js/utils.js';

const x = 1;

console.log(x === window.x); //false
console.log(this === undefined); // true
```

### 注意

利用顶层的 `this` 等于 `undefined` 这个语法点，可以侦测当前代码是否在 ES6 模块之中。

```javascript
const isNotModuleScript = this !== undefined;
```

在本地调试的时候，不可以加载模块，需要开启本地服务器才能使用 @@@

```
from origin 'null' has been blocked by CORS policy: Cross origin requests are only supported for protocol schemes: http, data, chrome, chrome-extension, https.
```

## Node 加载

### 概述

Node 对 ES6 模块的处理比较麻烦，因为它有自己的 CommonJS 模块格式，与 ES6 模块格式是不兼容的。目前的解决方案是，将两者分开，ES6 模块和 CommonJS **采用各自的加载方案**。

它们采用不同的加载方案。从 Node.js v13.2 版本开始，Node.js 已经默认打开了 ES6 模块支持。

Node 要求 ES6 模块采用 `.mjs` 后缀文件名。也就是说，只要脚本文件里面使用 `import` 或者 `export` 命令，那么就必须采用 `.mjs` 后缀名。`require` 命令不能加载 `.mjs` 文件，会报错，只有 `import` 命令才可以加载 `.mjs` 文件。反过来，`.mjs` 文件里面也不能使用 `require` 命令，必须使用 `import`。

一句话总结：`.mjs` 文件总是以 ES6 模块加载，`.cjs` 文件总是以 CommonJS 模块加载，`.js` 文件的加载取决于 `package.json` 里面 `type` 字段的设置。

```js
{
   "type": "module"
}
```

```js
{
   "type": "commonjs"
}
```

注意，ES6 模块与 CommonJS 模块尽量不要混用。`require` 命令不能加载 `.mjs` 文件，会报错，只有 `import` 命令才可以加载 `.mjs` 文件。反过来，`.mjs` 文件里面也不能使用 `require` 命令，必须使用 `import`。

### Path

如果模块名不含路径，那么 `import` 命令会去 `node_modules` 目录寻找这个模块。

```javascript
import 'baz';
import 'abc/123';
```

如果模块名包含路径，那么 `import` 命令会按照路径去寻找这个名字的脚本文件。

```javascript
import 'file:///etc/config/app.json';
import './foo';
import './foo?search';
import '../bar';
import '/baz';
```

如果脚本文件省略了后缀名，比如 `import './foo'`，Node 会依次尝试四个后缀名：`./foo.mjs`、`./foo.js`、`./foo.json`、`./foo.node`。

如果这些脚本文件都不存在，Node 就会去加载 `./foo/package.json` 的 `main` 字段指定的脚本。

如果 `./foo/package.json` 不存在或者没有 `main` 字段，那么就会依次加载 `./foo/index.mjs`、`./foo/index.js`、`./foo/index.json`、`./foo/index.node`。

如果以上四个文件还是都不存在，就会抛出错误。

### 内部变量

ES6 模块应该是通用的，同一个模块不用修改，就可以用在浏览器环境和服务器环境。为了达到这个目标，Node 规定 ES6 模块之中不能使用 CommonJS 模块的特有的一些内部变量。

首先，就是 `this` 关键字。ES6 模块之中，顶层的 `this` 指向 `undefined`；CommonJS 模块的顶层 `this` 指向当前模块，这是两者的一个**重大差异**。

其次，以下这些顶层变量在 ES6 模块之中都是不存在的。

- `arguments`
- `require`
- `module`
- `exports`
- `__filename`
- `__dirname`

如果你一定要使用这些变量，有一个变通方法，就是写一个 CommonJS 模块输出这些变量，然后再用 ES6 模块加载这个 CommonJS 模块。但是这样一来，该 ES6 模块就不能直接用于浏览器环境了，所以不推荐这样做。

```javascript
// expose.js
module.exports = {__dirname};

// use.mjs
import expose from './expose.js';
const {__dirname} = expose;
```

上面代码中，`expose.js` 是一个 CommonJS 模块，输出变量 `__dirname`，该变量在 ES6 模块之中不存在。ES6 模块加载 `expose.js`，就可以得到 `__dirname`

### package.json 的 Main 字段

`package.json` 文件有两个字段可以指定模块的入口文件：`main` 和 `exports`。比较简单的模块，可以只使用 `main` 字段，指定模块加载的入口文件。

```javascript
// ./node_modules/es-module-package/package.json
{
  "type": "module",
  "main": "./src/index.js"
}
```

上面代码指定项目的入口脚本为 `./src/index.js`，它的格式为 ES6 模块。如果没有 `type` 字段，`index.js` 就会被解释为 CommonJS 模块。

然后，`import` 命令就可以加载这个模块。

```javascript
// ./my-app.mjs

import { something } from 'es-module-package';
// 实际加载的是 ./node_modules/es-module-package/src/index.js
```

上面代码中，运行该脚本以后，Node.js 就会到 `./node_modules` 目录下面，寻找 `es-module-package` 模块，然后根据该模块 `package.json` 的 `main` 字段去执行入口文件。

这时，如果用 CommonJS 模块的 `require()` 命令去加载 `es-module-package` 模块会报错，因为 CommonJS 模块不能处理 `export` 命令。

### package.json 的 Exports 字段

`exports` 字段的优先级高于 `main` 字段。它有多种用法。

#### 子目录别名

`package.json` 文件的 `exports` 字段可以指定脚本或子目录的别名。

```javascript
// ./node_modules/es-module-package/package.json
{
  "exports": {
    "./submodule": "./src/submodule.js"
  }
}
```

上面的代码指定 `src/submodule.js` 别名为 `submodule`，然后就可以从别名加载这个文件。

```javascript
import submodule from 'es-module-package/submodule';
// 加载 ./node_modules/es-module-package/src/submodule.js
```

下面是子目录别名的例子。

```javascript
// ./node_modules/es-module-package/package.json
{
  "exports": {
    "./features/": "./src/features/"
  }
}

import feature from 'es-module-package/features/x.js';
// 加载 ./node_modules/es-module-package/src/features/x.js
```

如果没有指定别名，就不能用“模块 + 脚本名”这种形式加载脚本。

```javascript
// 报错
import submodule from 'es-module-package/private-module.js';

// 不报错
import submodule from './node_modules/es-module-package/private-module.js';
```

#### Main 的别名

`exports` 字段的别名如果是 `.`，就代表模块的主入口，优先级高于 `main` 字段，并且可以直接简写成 `exports` 字段的值。

```javascript
{
  "exports": {
    ".": "./main.js"
  }
}

// 等同于
{
  "exports": "./main.js"
}
```

由于 `exports` 字段只有支持 ES6 的 Node.js 才认识，所以可以用来兼容旧版本的 Node.js。

```javascript
{
  "main": "./main-legacy.cjs",
  "exports": {
    ".": "./main-modern.cjs"
  }
}
```

上面代码中，老版本的 Node.js （不支持 ES6 模块）的入口文件是 `main-legacy.cjs`，新版本的 Node.js 的入口文件是 `main-modern.cjs`。

#### 条件加载

利用 `.` 这个别名，可以为 ES6 模块和 CommonJS 指定不同的入口。目前，这个功能需要在 Node.js 运行的时候，打开 `--experimental-conditional-exports` 标志。

```javascript
{
  "type": "module",
  "exports": {
    ".": {
      "require": "./main.cjs",
      "default": "./main.js"
    }
  }
}
```

上面代码中，别名 `.` 的 `require` 条件指定 `require()` 命令的入口文件（即 CommonJS 的入口），`default` 条件指定其他情况的入口（即 ES6 的入口）。

上面的写法可以简写如下。

```javascript
{
  "exports": {
    "require": "./main.cjs",
    "default": "./main.js"
  }
}
```

注意，如果同时还有其他别名，就不能采用简写，否则或报错。

```javascript
{
  // 报错
  "exports": {
    "./feature": "./lib/feature.js",
    "require": "./main.cjs",
    "default": "./main.js"
  }
}
```

### ES6 模块加载 CommonJS 模块

CommonJS 模块的输出都定义在 `module.exports` 这个属性上面。Node 的 `import` 命令加载 CommonJS 模块，Node 会自动将 `module.exports` 属性，当作模块的默认输出，即等同于 `export default xxx`。

下面是一个 CommonJS 模块。

```javascript
// a.js
module.exports = {
  foo: 'hello',
  bar: 'world'
};

// 等同于
export default {
  foo: 'hello',
  bar: 'world'
};
```

`import` 命令加载上面的模块，`module.exports` 会被视为默认输出，即 `import` 命令实际上输入的是这样一个对象 `{ default: module.exports }`。

所以，一共有三种写法，可以拿到 CommonJS 模块的 `module.exports`。

```javascript
// 写法一
import baz from './a';
// baz = {foo: 'hello', bar: 'world'};

// 写法二
import {default as baz} from './a';
// baz = {foo: 'hello', bar: 'world'};

// 写法三
import * as baz from './a';
// baz = {
//   get default() {return module.exports;},
//   get foo() {return this.default.foo}.bind(baz),
//   get bar() {return this.default.bar}.bind(baz)
// }
```

上面代码的第三种写法，可以通过 `baz.default` 拿到 `module.exports`。`foo` 属性和 `bar` 属性就是可以通过这种方法拿到了 `module.exports`。

下面是一些例子。

```javascript
// b.js
module.exports = null;

// es.js
import foo from './b';
// foo = null;

import * as bar from './b';
// bar = { default:null };
```

上面代码中，`es.js` 采用第二种写法时，要通过 `bar.default` 这样的写法，才能拿到 `module.exports`。

```javascript
// c.js
module.exports = function two() {
  return 2;
};

// es.js
import foo from './c';
foo(); // 2

import * as bar from './c';
bar.default(); // 2
bar(); // throws, bar is not a function
```

上面代码中，`bar` 本身是一个对象，不能当作函数调用，只能通过 `bar.default` 调用。

CommonJS 模块的输出缓存机制，在 ES6 加载方式下依然有效。

```javascript
// foo.js
module.exports = 123;
setTimeout(_ => module.exports = null);
```

上面代码中，对于加载 `foo.js` 的脚本，`module.exports` 将一直是 `123`，而不会变成 `null`。

由于 ES6 模块是编译时确定输出接口，CommonJS 模块是运行时确定输出接口，所以采用 `import` 命令加载 CommonJS 模块时，不允许采用下面的写法。

```javascript
// 不正确
import { readFile } from 'fs';
```

上面的写法不正确，因为 `fs` 是 CommonJS 格式，只有在运行时才能确定 `readFile` 接口，而 `import` 命令要求编译时就确定这个接口。解决方法就是改为整体输入。

```javascript
// 正确的写法一
import * as express from 'express';
const app = express.default();

// 正确的写法二
import express from 'express';
const app = express();
```

### CommonJS 模块加载 ES6 模块

- CommonJS 模块加载 ES6 模块，不能使用 `require` 命令，而要使用 `import()` 函数。ES6 模块的所有输出接口，会成为输入对象的属性。

  ```javascript
  // es.mjs
  let foo = { bar: 'my-default' };
  export default foo;
  
  // cjs.js
  const es_namespace = await import('./es.mjs');
  // es_namespace = {
  //   get default() {
  //     ...
  //   }
  // }
  console.log(es_namespace.default);
  // { bar:'my-default' }
  ```

- 上面代码中，`default` 接口变成了 `es_namespace.default` 属性。
- 下面是另一个例子

  ```javascript
  // es.js
  export let foo = { bar:'my-default' };
  export { foo as bar };
  export function f() {};
  export class c {};
  
  // cjs.js
  const es_namespace = await import('./es');
  // es_namespace = {
  //   get foo() {return foo;}
  //   get bar() {return foo;}
  //   get f() {return f;}
  //   get c() {return c;}
  // }
  ```

## 循环加载

“循环加载”（circular dependency）指的是，`a` 脚本的执行依赖 `b` 脚本，而 `b` 脚本的执行又依赖 `a` 脚本。

```javascript
// a.js
var b = require('b');

// b.js
var a = require('a');
```

通常，“循环加载”表示**存在强耦合**，如果处理不好，还可能导致递归加载，使得程序无法执行，因此应该避免出现。

但是实际上，这是很难避免的，尤其是依赖关系复杂的大项目，很容易出现 `a` 依赖 `b`，`b` 依赖 `c`，`c` 又依赖 `a` 这样的情况。这意味着，模块加载机制必须考虑“循环加载”的情况。

对于 JavaScript 语言来说，目前最常见的两种模块格式 CommonJS 和 ES6，处理“循环加载”的方法是不一样的，返回的结果也不一样。

### CommonJS 模块的加载原理

介绍 ES6 如何处理“循环加载”之前，先介绍目前最流行的 CommonJS 模块格式的加载原理。

CommonJS 的一个模块，就是一个脚本文件。`require` 命令第一次加载该脚本，就会执行整个脚本，然后在内存生成一个对象。

```javascript
  {
    id: '...',
    exports: { ... },
    loaded: true,
    ...
  }
```

上面代码就是 Node 内部加载模块后生成的一个对象。该对象的 `id` 属性是**模块名**，`exports` 属性是模块输出的各个接口，`loaded` 属性是一个布尔值，表示该模块的脚本是否执行完毕。其他还有很多属性，这里都省略了。

以后需要用到这个模块的时候，就会到 `exports` 属性上面取值。即使再次执行 `require` 命令，也不会再次执行该模块，而是到缓存之中取值。也就是说，**CommonJS 模块无论加载多少次，都只会在第一次加载时运行一次，以后再加载，就返回第一次运行的结果，除非手动清除系统缓存**。

### CommonJS 模块的循环加载

CommonJS 模块的重要特性是加载时执行，即脚本代码在 `require` 的时候，就会全部执行。一旦出现某个模块被 " 循环加载 "，就只输出已经执行的部分，还未执行的部分不会输出。

让我们来看，Node [官方文档](https://nodejs.org/api/modules.html#modules_cycles) 里面的例子。脚本文件 `a.js` 代码如下。

```javascript
  exports.done = false;
  var b = require('./b.js');
  console.log('在 a.js 之中，b.done = %j', b.done);
  exports.done = true;
  console.log('a.js 执行完毕');
```

上面代码之中，`a.js` 脚本先输出一个 `done` 变量，然后加载另一个脚本文件 `b.js`。注意，此时 `a.js` 代码就停在这里，等待 `b.js` 执行完毕，再往下执行。

再看 `b.js` 的代码。

```javascript
  exports.done = false;
  var a = require('./a.js');
  console.log('在 b.js 之中，a.done = %j', a.done);
  exports.done = true;
  console.log('b.js 执行完毕');
```

上面代码之中，`b.js` 执行到第二行，就会去加载 `a.js`，这时，就发生了“循环加载”。系统会去 `a.js` 模块对应对象的 `exports` 属性取值，可是因为 `a.js` 还没有执行完，**从 `exports` 属性只能取回已经执行的部分，而不是最后的值。**

`a.js` 已经执行的部分，只有一行。

```javascript
  exports.done = false;
```

因此，对于 `b.js` 来说，它从 `a.js` 只输入一个变量 `done`，值为 `false`。

然后，`b.js` 接着往下执行，等到全部执行完毕，再把执行权交还给 `a.js`。于是，`a.js` 接着往下执行，直到执行完毕。我们写一个脚本 `main.js`，验证这个过程。

```javascript
  var a = require('./a.js');
  var b = require('./b.js');
  console.log('在 main.js 之中, a.done=%j, b.done=%j', a.done, b.done);
```

执行 `main.js`，运行结果如下。

```bash
  $ node main.js
  
  在 b.js 之中，a.done = false
  b.js 执行完毕
  在 a.js 之中，b.done = true
  a.js 执行完毕
  在 main.js 之中, a.done=true, b.done=true
```

上面的代码证明了两件事。一是，在 `b.js` 之中，`a.js` 没有执行完毕，只执行了第一行。二是，`main.js` 执行到第二行时，不会再次执行 `b.js`，而是输出缓存的 `b.js` 的执行结果，即它的第四行。

```javascript
  exports.done = true;
```

总之，CommonJS 输入的是被输出值的拷贝，不是引用。

另外，由于 CommonJS 模块遇到循环加载时，返回的是当前已经执行的部分的值，而不是代码全部执行后的值，两者可能会有差异。所以，输入变量的时候，必须非常小心。

```javascript
  var a = require('a'); // 安全的写法
  var foo = require('a').foo; // 危险的写法
  
  exports.good = function (arg) {
    return a.foo('good', arg); // 使用的是 a.foo 的最新值
  };
  
  exports.bad = function (arg) {
    return foo('bad', arg); // 使用的是一个部分加载时的值
  };
```

上面代码中，如果发生循环加载，`require('a').foo` 的值很可能后面会被改写，改用 `require('a')` 会更保险一点。

### ES6 模块的循环加载

ES6 处理“循环加载”与 CommonJS 有本质的不同。ES6 模块是动态引用，如果使用 `import` 从一个模块加载变量（即 `import foo from 'foo'`），那些变量不会被缓存，而是成为一个指向被加载模块的引用，需要开发者自己保证，真正取值的时候能够取到值。

请看下面这个例子

```javascript
// a.mjs
import {bar} from './b';
console.log('a.mjs');
console.log(bar);
export let foo = 'foo';

// b.mjs
import {foo} from './a';
console.log('b.mjs');
console.log(foo);
export let bar = 'bar';
```

上面代码中，`a.mjs` 加载 `b.mjs`，`b.mjs` 又加载 `a.mjs`，构成循环加载。执行 `a.mjs`，结果如下。

```bash
$ node --experimental-modules a.mjs
b.mjs
ReferenceError: foo is not defined
```

上面代码中，执行 `a.mjs` 以后会报错，`foo` 变量未定义，这是为什么？

让我们一行行来看，ES6 循环加载是怎么处理的。

首先，执行 `a.mjs` 以后，引擎发现它加载了 `b.mjs`，因此会**优先执行 `b.mjs`，**然后再执行 `a.mjs`。接着，执行 `b.mjs` 的时候，已知它从 `a.mjs` 输入了 `foo` 接口，这时不会去执行 `a.mjs`，而是认为这个接口已经存在了，继续往下执行。执行到第三行 `console.log(foo)` 的时候，才发现这个接口根本没定义，因此报错。

解决这个问题的方法，就是让 `b.mjs` 运行的时候，`foo` 已经有定义了。这可以通过将 `foo` 写成函数来解决。

```javascript
// a.mjs
import {bar} from './b';
console.log('a.mjs');
console.log(bar());
function foo() { return 'foo' }
export {foo};

// b.mjs
import {foo} from './a';
console.log('b.mjs');
console.log(foo());
function bar() { return 'bar' }
export {bar};
```

这时再执行 `a.mjs` 就可以得到预期结果。

```bash
$ node --experimental-modules a.mjs
b.mjs
foo
a.mjs
bar
```

这是因为函数具有提升作用，在执行 `import {bar} from './b'` 时，函数 `foo` 就已经有定义了，所以 `b.mjs` 加载的时候不会报错。这也意味着，如果把函数 `foo` 改写成函数表达式，也会报错。

```javascript
// a.mjs
import {bar} from './b';
console.log('a.mjs');
console.log(bar());
const foo = () => 'foo';
export {foo};
```

上面代码的第四行，改成了函数表达式，就不具有提升作用，执行就会报错。

我们再来看 ES6 模块加载器 [SystemJS](https://github.com/ModuleLoader/es6-module-loader/blob/master/docs/circular-references-bindings.md) 给出的一个例子。

```javascript
// even.js
import { odd } from './odd'
export var counter = 0;
export function even(n) {
  counter++;
  return n === 0 || odd(n - 1);
}

// odd.js
import { even } from './even';
export function odd(n) {
  return n !== 0 && even(n - 1);
}
```

上面代码中，`even.js` 里面的函数 `even` 有一个参数 `n`，只要不等于 0，就会减去 1，传入加载的 `odd()`。`odd.js` 也会做类似操作。

运行上面这段代码，结果如下。

```javascript
  $ babel-node
  > import * as m from './even.js';
  > m.even(10);
  true
  > m.counter
  6
  > m.even(20)
  true
  > m.counter
  17
```

上面代码中，参数 `n` 从 10 变为 0 的过程中，`even()` 一共会执行 6 次，所以变量 `counter` 等于 6。第二次调用 `even()` 时，参数 `n` 从 20 变为 0，`even()` 一共会执行 11 次，加上前面的 6 次，所以变量 `counter` 等于 17。

这个例子要是改写成 CommonJS，就根本无法执行，会报错。

  ```javascript
  // even.js
  var odd = require('./odd');
  var counter = 0;
  exports.counter = counter;
  exports.even = function (n) {
    counter++;
    return n == 0 || odd(n - 1);
  }
  
  // odd.js
  var even = require('./even').even;
  module.exports = function (n) {
    return n != 0 && even(n - 1);
  }
  ```

上面代码中，`even.js` 加载 `odd.js`，而 `odd.js` 又去加载 `even.js`，形成“循环加载”。这时，执行引擎就会输出 `even.js` 已经执行的部分（不存在任何结果），所以在 `odd.js` 之中，变量 `even` 等于 `undefined`，等到后面调用 `even(n - 1)` 就会报错。

  ```bash
  $ node
  > var m = require('./even');
  > m.even(10)
  TypeError: even is not a function
  ```
