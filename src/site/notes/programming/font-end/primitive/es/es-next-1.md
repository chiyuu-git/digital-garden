---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/es/es-next-1/"}
---


# ES6 简介

## 语法提案的批准流程

任何人都可以向标准委员会（又称 TC39 委员会）提案，要求修改语言标准。

一种新的语法从提案到变成正式标准，需要经历五个阶段。每个阶段的变动都需要由 TC39 委员会批准。

- Stage 0 - Strawman（展示阶段）
- Stage 1 - Proposal（征求意见阶段）
- Stage 2 - Draft（草案阶段）
- Stage 3 - Candidate（候选人阶段）
- Stage 4 - Finished（定案阶段）

一个提案只要能进入 Stage 2，就差不多肯定会包括在以后的正式标准里面。ECMAScript 当前的所有提案，可以在 TC39 的官方网站 [GitHub.com/tc39/ecma262](https://github.com/tc39/ecma262) 查看。

## ECMAScript 的历史

ES6 从开始制定到最后发布，整整用了 15 年。

前面提到，ECMAScript 1.0 是 1997 年发布的，接下来的两年，连续发布了 ECMAScript 2.0（1998 年 6 月）和 ECMAScript 3.0（1999 年 12 月）。3.0 版是一个巨大的成功，在业界得到广泛支持，成为通行标准，奠定了 JavaScript 语言的基本语法，以后的版本完全继承。直到今天，初学者一开始学习 JavaScript，其实就是在学 3.0 版的语法。

### 部署进度

各大浏览器的最新版本，对 ES6 的支持可以查看 [kangax.github.io/compat-table/es6/](https://kangax.github.io/compat-table/es6/)。随着时间的推移，支持度已经越来越高了，超过 90% 的 ES6 语法特性都实现了。

Node 是 JavaScript 的服务器运行环境（runtime）。它对 ES6 的支持度更高。除了那些默认打开的功能，还有一些语法功能已经实现了，但是默认没有打开。使用下面的命令，可以查看 Node 已经实现的 ES6 特性。

```js
  // Linux & Mac
  $ node --v8-options | grep harmony
  
  // Windows
  $ node --v8-options | findstr harmony
```

# Let 命令和 Const 命令

## Let 命令

ES6 新增了 `let` 命令，用来声明变量。它的用法类似于 `var`，但是所声明的变量，只在 `let` 命令所在的代码块内有效。

```javascript
  {
    let a = 10;
    var b = 1;
  }

  a // ReferenceError: a is not defined.
  b // 1
```

### 不存在变量提升

`var` 命令会发生“变量提升”现象，即变量可以在声明之前使用，值为 `undefined`。这种现象多多少少是有些奇怪的，按照一般的逻辑，变量应该在声明语句之后才可以使用。

为了纠正这种现象，`let` 命令改变了语法行为，它所声明的变量一定要在声明后使用，否则报错。

  ```js
    // var 的情况
    console.log(foo); // 输出undefined
    var foo = 2;

    // let 的情况
    console.log(bar); // 报错ReferenceError
    let bar = 2;
  ```

### 暂时性死区

只要块级作用域内存在 `let` 命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响。

  ```javascript
  var tmp = 123;

  if (true) {
    tmp = 'abc'; // ReferenceError
    let tmp;
  }
  ```

上面代码中，存在全局变量 `tmp`，但是块级作用域内 `let` 又声明了一个局部变量 `tmp`，导致后者绑定这个块级作用域，所以在 `let` 声明变量前，对 `tmp` 赋值会报错。

ES6 明确规定，如果区块中存在 `let` 和 `const` 命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。

总之，在代码块内，使用 `let` 命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。

```javascript
  if (true) {
    // TDZ开始
    tmp = 'abc'; // ReferenceError
    console.log(tmp); // ReferenceError

    let tmp; // TDZ结束
    console.log(tmp); // undefined

    tmp = 123;
    console.log(tmp); // 123
  }
```

“暂时性死区”也意味着 `typeof` 不再是一个百分之百安全的操作。

```javascript
  typeof x; // ReferenceError
  let x;
```

作为比较，如果一个变量根本没有被声明，使用 `typeof` 反而不会报错。

```javascript
  typeof undeclared_variable // "undefined"
```

所以，在没有 `let` 之前，`typeof` 运算符是百分之百安全的，永远不会报错。现在这一点不成立了。这样的设计是为了让大家养成良好的编程习惯，变量一定要在声明之后使用，否则就报错。

### 有些“死区”比较隐蔽

```javascript
  function bar(x = y, y = 2) {
    return [x, y];
  }
  
  bar(); // 报错
```

上面代码中，调用 `bar` 函数之所以报错（某些实现可能不报错），是因为参数 `x` 默认值等于另一个参数 `y`，而此时 `y` 还没有声明，属于“死区”。如果 `y` 的默认值是 `x`，就不会报错，因为此时 `x` 已经声明了。

```javascript
  function bar(x = 2, y = x) {
    return [x, y];
  }
  bar(); // [2, 2]
```

另外，下面的代码也会报错，与 `var` 的行为不同。

  ```javascript
  // 不报错
  var x = x;

  // 报错
  let x = x;
  // ReferenceError: x is not defined
  ```

上面代码报错，也是因为暂时性死区。使用 `let` 声明变量时，只要变量在还没有声明完成前使用，就会报错。上面这行就属于这个情况，在变量 `x` 的声明语句还没有执行完成前，就去取 `x` 的值，导致报错”x 未定义“。

ES6 规定暂时性死区和 `let`、`const` 语句不出现变量提升，主要是为了减少运行时错误，防止在变量声明前就使用这个变量，从而导致意料之外的行为。这样的错误在 ES5 是很常见的，现在有了这种规定，避免此类错误就很容易了。

总之，暂时性死区的本质就是，只要一进入当前作用域，所要使用的变量就已经存在了，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量。

### 不允许重复声明

`let` 不允许在相同作用域内，重复声明同一个变量。

```javascript
  // 报错
  function func() {
    let a = 10;
    var a = 1;
  }

  // 报错
  function func() {
    let a = 10;
    let a = 1;
  }
```

因此，不能在函数内部重新声明参数。

```javascript
  function func(arg) {
    let arg; // 报错
  }
  
  function func(arg) {
    {
      let arg; // 不报错
    }
  }
```

函数的形参，也可以理解为在函数作用域内，使用 let 声明一个局部变量，所以也不允许在函数作用域内，重复声明与函数形参同名的变量。

## 块级作用域

### 为什么需要块级作用域？

ES5 只有全局作用域和函数作用域，没有块级作用域，这带来很多不合理的场景。

第一种场景，内层变量可能会覆盖外层变量。

```javascript
  var tmp = new Date();

  function f() {
    console.log(tmp);
    if (false) {
      var tmp = 'hello world';
    }
  }

  f(); // undefined
```

上面代码的原意是，`if` 代码块的外部使用外层的 `tmp` 变量，内部使用内层的 `tmp` 变量。但是，函数 `f` 执行后，输出结果为 `undefined`，原因在于变量提升，导致内层的 `tmp` 变量覆盖了外层的 `tmp` 变量。

第二种场景，用来计数的循环变量泄露为全局变量。

```javascript
  var s = 'hello';

  for (var i = 0; i < s.length; i++) {
    console.log(s[i]);
  }

  console.log(i); // 5
```

上面代码中，变量 `i` 只用来控制循环，但是循环结束后，它并没有消失，泄露成了全局变量。

### ES6 的块级作用域

块级作用域的出现，实际上使得获得广泛应用的立即执行函数表达式（IIFE）不再必要了。

  ```js
    // IIFE 写法
    (function () {
      var tmp = ...;
      ...
    }());
  
    // 块级作用域写法 只有 let、const 支持
    {
      let tmp = ...;
      ...
    }
```

### 块级作用域与函数声明

ES6 规定了可以在块级作用域使用函数声明，表现和 let const 一致，不会提升到外部。但是浏览器为了兼容旧的代码，不一定会支持

考虑到环境导致的行为差异太大，应该 **避免** 在块级作用域内声明函数。如果确实需要，也应该写成函数表达式，而不是函数声明语句。

### 块级作用域必须有大括号

```js
// 第一种写法，报错
if (true) let x = 1;

// 第二种写法，不报错
if (true) {
  let x = 1;
}
```

上面代码中，第一种写法没有大括号，所以不存在块级作用域，而 `let` 只能出现在当前作用域的顶层，所以报错。第二种写法有大括号，所以块级作用域成立。

函数声明也是如此，严格模式下，函数只能声明在当前作用域的顶层。

```javascript
// 不报错
'use strict';
if (true) {
  function f() {}
}

// 报错
'use strict';
if (true)
  function f() {}
```

## Const 命令

### 基本用法

`const` 声明一个只读的常量。一旦声明，常量的值就不能改变。这意味着，`const` 一旦声明变量，就必须立即初始化，不能留到以后赋值。

```javascript
  const PI = 3.1415;
  PI // 3.1415

  PI = 3;
  // TypeError: Assignment to constant variable.
```

对于 `const` 来说，只声明不赋值，就会报错。

```javascript
  const foo;
  // SyntaxError: Missing initializer in const declaration
```

`const` 的作用域与 `let` 命令相同：只在声明所在的块级作用域内有效。

```javascript
  if (true) {
    const MAX = 5;
  }

  MAX // Uncaught ReferenceError: MAX is not defined
```

`const` 命令声明的常量也是不提升，同样存在暂时性死区，只能在声明的位置后面使用。

```javascript
  if (true) {
    console.log(MAX); // ReferenceError
    const MAX = 5;
  }
```

`const` 声明的常量，也与 `let` 一样不可重复声明。

```javascript
  var message = "Hello!";
  let age = 25;
  
  // 以下两行都会报错
  const message = "Goodbye!";
  const age = 30;
```

### 本质

`const` 实际上保证的，并不是变量的值不得改动，而是变量指向的那个 **内存地址** 所保存的 **数据不得改动**。

对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。

但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针，`const` 只能保证这个指针是固定的（即总是指向另一个固定的地址），至于它指向的 **数据结构** 是不是可变的，就完全不能控制了。因此，将一个对象声明为常量必须非常小心。

```javascript
  const foo = {};

  // 为 foo 添加一个属性，可以成功
  foo.prop = 123;
  foo.prop // 123

  // 将 foo 指向另一个对象，就会报错
  foo = {}; // TypeError: "foo" is read-only
```

上面代码中，常量 `foo` 储存的是一个地址，这个地址指向一个对象。不可变的只是这个地址，即不能把 `foo` 指向另一个地址，但对象本身是可变的，所以依然可以为其添加新属性。

```javascript
  const a = [];
  a.push('Hello'); // 可执行
  a.length = 0;    // 可执行
  a = ['Dave'];    // 报错
```

上面代码中，常量 `a` 是一个数组，这个数组本身是可写的，但是如果将另一个数组赋值给 `a`，就会报错。

![es-object](programming/font-end/primitive/es/es-object.md#Object%20freeze)

### ES6 声明变量的六种方法

ES5 只有两种声明变量的方法：`var` 命令和 `function` 命令。ES6 除了添加 `let` 和 `const` 命令，后面章节还会提到，另外两种声明变量的方法：`import` 命令和 `class` 命令。所以，ES6 一共有 6 种声明变量的方法。

## 循环中的 Let 和 Const

### For 循环

```js
  var a = [];
  for (let i = 0; i < 10; i++) {
    a[i] = function () {
      console.log(i);
    };
  }
  a[6](); // 6
```

问题在于，上面讲了 let 不提升，不能重复声明，不能绑定全局作用域等等特性，可是为什么在这里就能正确打印出 i 值呢？

如果是不重复声明，在循环第二次的时候，又用 let 声明了 i，应该报错呀，就算因为某种原因，重复声明不报错，一遍一遍迭代，i 的值最终还是应该是 3 呀，还有人说 for 循环的设置循环变量的那部分是一个单独的作用域，就比如：

  ```js
  for (let i = 0; i < 3; i++) {
    let i = 'abc';
    console.log(i);
  }
  // abc
  // abc
  // abc
  ```

这个例子是对的，如果我们把 let 改成 var 呢？

  ```js
  for (var i = 0; i < 3; i++) {
    var i = 'abc'
    console.log(i);
  }
  // abc
  // 只打印了一个，因为内部的声明覆盖了i，NaN < 3 返回 false，循环结束
  ```

为什么结果就不一样了呢，如果有单独的作用域，结果应该是相同的呀……

如果要追究这个问题，就要抛弃掉之前所讲的这些特性！这是因为 let 声明在循环内部的行为是标准中专门定义的，不一定就与 let 的不提升特性有关，其实，在早期的 let 实现中就不包含这一行为。

我们会发现，在 for 循环中使用 let 和 var，底层会使用不同的处理方式。

那么当使用 let 的时候底层到底是怎么做的呢？

简单的来说，就是在 `for (let i = 0; i < 3; i++)` 中，即圆括号之内建立一个隐藏的作用域。

然后 **每次迭代循环时都创建一个新变量，并以之前迭代中同名变量的值将其初始化**。这样对于下面这样一段代码

  ```js
  var funcs = [];
  for (let i = 0; i < 3; i++) {
      funcs[i] = function () {
          console.log(i);
      };
  }
  funcs[0](); // 0
  ```

相当于

```js
  // 伪代码
  (let i = 0) {
    funcs[0] = function() {
      console.log(i)
    };
  }

  (let i = 1) {
    funcs[1] = function() {
      console.log(i)
    };
  }

  (let i = 2) {
    funcs[2] = function() {
      console.log(i)
    };
  };
```

当执行函数的时候，根据词法作用域就可以找到正确的值，其实你也可以理解为 let 声明模仿了闭包的做法来简化循环过程。

**注意**

+ 如果我们把 let 改成 const 呢？

  ```js
  var funcs = [];
  for (const i = 0; i < 10; i++) {
      funcs[i] = function () {
          console.log(i);
      };
  }
  funcs[0](); // Uncaught TypeError: Assignment to constant variable.
  ```

+ 结果会是报错，因为虽然我们每次都创建了一个新的变量，但是，会以之前迭代中同名变量的值将其初始化，所以我们在迭代中尝试修改 const 的值，所以最终会报错。

### For in 循环

那下面的结果是什么呢？

```js
  var funcs = [], object = {a: 1, b: 1, c: 1};
  for (var key in object) {
      funcs.push(function(){
          console.log(key)
      });
  }

  funcs[0]()
```

结果是 'c';

那如果把 var 改成 let 或者 const 呢？

使用 let，结果自然会是 'a'，const 呢？ 报错还是 'a'?

结果是正确打印 'a'，这是因为在 for in 循环中，**每次迭代不会修改已有的绑定，而是会创建一个新的绑定**

for of 循环 类似

  ```js
  var funcs = [], arr = [1,2,3];
  for (var key of arr) {
      funcs.push(function(){
          console.log(key)
      });
  }
  
  funcs[0]() // 3
  
  var funcs = [], arr = [1,2,3];
  for (let key of arr) {
      funcs.push(function(){
          console.log(key)
      });
  }
  
  funcs[0]() // 1
  ```

## Babel

在 Babel 中是如何编译 let 和 const 的呢？我们来看看编译后的代码：

  ```js
  let value = 1;
  ```

编译为:

  ```js
  var value = 1;
  ```

们可以看到 Babel 直接将 let 编译成了 var，如果是这样的话，那么我们来写个例子：

  ```js
  if (false) {
      let value = 1;
  }
  console.log(value); // Uncaught ReferenceError: value is not defined
  ```

如果还是直接编译成 var，打印的结果肯定是 undefined，然而 Babel 很聪明，它编译成了：

  ```js
  if (false) {
      var _value = 1;
  }
  console.log(value);
  ```

我们再写个直观的例子：

  ```js
  let value = 1;
  {
      let value = 2;
  }
  value = 3;
  ```

编译成：

```js
  var value = 1;
  {
      var _value = 2;
  }
  value = 3;
```

本质是一样的，就是改变量名，使内外层的变量名称不一样。

那像 const 的修改值时报错，以及重复声明报错怎么实现的呢？

其实就是在编译的时候直接给你报错……

### 循环中的 Let 声明

```js
  var funcs = [];
  for (let i = 0; i < 10; i++) {
      funcs[i] = function () {
          console.log(i);
      };
  }
  funcs[0](); // 0
```

Babel 巧妙的编译成了：

```js
  var funcs = [];

  var _loop = function _loop(i) {
      funcs[i] = function () {
          console.log(i);
      };
  };

  for (var i = 0; i < 10; i++) {
      _loop(i);
  }
  funcs[0](); // 0
```

利用闭包

## 顶层对象的属性

顶层对象，在浏览器环境指的是 `window` 对象，在 Node 指的是 `global` 对象。ES5 之中，顶层对象的属性与全局变量是等价的。

```javascript
  window.a = 1;
  a // 1

  a = 2;
  window.a // 2
```

顶层对象的属性与全局变量挂钩，被认为是 JavaScript 语言最大的设计败笔之一。这样的设计带来了几个很大的问题

- 首先是没法在编译时就报出变量未声明的错误，只有运行时才能知道（因为全局变量可能是顶层对象的属性创造的，而属性的创造是动态的）；
- 其次，程序员很容易不知不觉地就创建了全局变量（比如打字出错）；
- 最后，顶层对象的属性是到处可以读写的，这非常不利于模块化编程。另一方面，`window` 对象有实体含义，指的是浏览器的窗口对象，顶层对象是一个有实体含义的对象，也是不合适的。

ES6 为了改变这一点，一方面规定，为了保持兼容性，`var` 命令和 `function` 命令声明的全局变量，依旧是顶层对象的属性；另一方面规定，`let` 命令、`const` 命令、`class` 命令声明的全局变量，不属于顶层对象的属性。也就是说，从 ES6 开始，全局变量将逐步与顶层对象的属性脱钩。

```javascript
  var a = 1;
  // 如果在 Node 的 REPL 环境，可以写成 global.a
  // 或者采用通用方法，写成 this.a
  window.a // 1

  let b = 1;
  window.b // undefined
```

上面代码中，全局变量 `a` 由 `var` 命令声明，所以它是顶层对象的属性；全局变量 `b` 由 `let` 命令声明，所以它不是顶层对象的属性，返回 `undefined`。

let 和 const 定义的变量不会成为全局对象的属性，因为他们在块级作用域生效，而 ES6 全部基于模块

看下面一个例子，像这种情况只有 max 是 let 声明的，其他都是省略了 var 声明而成为全局属性

```js
  let max = min = curMax = curMin =  null
  // 这样写即可
  let max,min,curMax,curMin
```

## Global 对象

很难找到一种方法，可以在所有情况下，都取到顶层对象。现在有一个提案，在语言标准的层面，引入 `global` 作为顶层对象。也就是说，在所有环境下，`global` 都是存在的，都可以从它拿到顶层对象。

# 变量的解构赋值

ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构（Destructuring）。

解构赋值会沿着原型链 @@@

## 数组的解构赋值

### 基本用法

```javascript
let [a, b, c] = [1, 2, 3];
```

本质上，这种写法属于“模式匹配”，只要等号两边的模式相同，左边的变量就会被赋予对应的值。下面是一些使用嵌套数组进行解构的例子。

```javascript
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3

let [ , , third] = ["foo", "bar", "baz"];
third // "baz"

let [x, , y] = [1, 2, 3];
x // 1
y // 3

let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []
```

如果解构不成功，变量的值就等于 `undefined`。

```javascript
let [foo] = [];
let [bar, foo] = [1];
```

### 不完全解构

即等号左边的模式，只匹配一部分的等号右边的数组。这种情况下，解构依然可以成功。

```javascript
let [x, y] = [1, 2, 3];
x // 1
y // 2

let [a, [b], d] = [1, [2, 3], 4];
a // 1
b // 2
d // 4
```

### 不可遍历结构

如果等号的右边不是数组（或者严格地说，不是可遍历的结构，参见《Iterator》一章），那么将会报错。

```javascript
// 报错
let [foo] = 1;
let [foo] = false;
let [foo] = NaN;
let [foo] = undefined;
let [foo] = null;
let [foo] = {};
```

上面的语句都会报错，因为等号右边的值，要么转为对象以后不具备 Iterator 接口（前五个表达式），要么本身就不具备 Iterator 接口（最后一个表达式）。

事实上，只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值。原生具有 Iterator 接口。解构赋值会依次从这个接口获取值。

```javascript
function* fibs() {
  let a = 0;
  let b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

let [first, second, third, fourth, fifth, sixth] = fibs();
sixth // 5
```

### 默认值

解构赋值允许指定默认值。

```javascript
let [foo = true] = [];
foo // true

let [x, y = 'b'] = ['a']; // x='a', y='b'
let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
```

注意，ES6 内部使用严格相等运算符（`\===`），判断一个位置是否有值。所以，只有当一个数组成员 **严格等于** `undefined`，默认值才会生效。

```javascript
let [x = 1] = [undefined];
x // 1

let [x = 1] = [null];
x // null
```

上面代码中，如果一个数组成员是 `null`，默认值就不会生效，因为 `null` 不严格等于 `undefined`。

如果默认值是一个表达式，那么这个表达式是惰性求值的，即只有在用到的时候，才会求值。

默认值可以引用解构赋值的其他变量，但该变量必须已经声明。

## 对象的解构赋值

解构不仅可以用于数组，还可以用于对象。

```javascript
let { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"
```

对象的解构与数组有一个重要的不同。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，**变量必须与属性同名**，才能取到正确的值。@@@

如果变量名与属性名不一致，必须写成下面这样。

```javascript
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"

let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world'
```

这实际上说明，对象的解构赋值是下面形式的简写（参见《对象的扩展》一章）。

```javascript
let { foo: foo, bar: bar } = { foo: "aaa", bar: "bbb" };
```

也就是说，对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。**真正被赋值的是后者，而不是前者。** @@@

```javascript
let { foo: baz } = { foo: "aaa", bar: "bbb" };
baz // "aaa"
foo // error: foo is not defined
```

上面代码中，`foo` 是 **匹配的模式**，`baz` 才是变量。真正被赋值的是变量 `baz`，而不是模式 `foo`。 @@@

**注意**

+ 解构赋值，被赋值者在后
+ 对象赋值的简写手法，被赋值在前

### 嵌套结构的解构

与数组一样，解构也可以用于嵌套结构的对象。

```js
    let obj = {
      p: [
        'Hello',
        { y: 'World' }
      ]
    };

    let { p: [x, { y }] } = obj;
    x // "Hello"
    y // "World"
```

注意，这时 `p` 是 **模式**，不是变量，因此不会被赋值。如果 `p` 也要作为变量赋值，可以写成下面这样。

```javascript
  let obj = {
    p: [
      'Hello',
      { y: 'World' }
    ]
  };
  
  let { p, p: [x, { y }] } = obj;
  x // "Hello"
  y // "World"
  p // ["Hello", {y: "World"}]
```

### 类似的

对象的解构也可以指定默认值。

默认值生效的条件是，对象的属性值严格等于 `undefined`。

如果解构失败，变量的值等于 `undefined`。

### 注意

如果要将一个已经声明的变量用于解构赋值，必须非常小心。

```javascript
  // 错误的写法
  let x;
  {x} = {x: 1};
  // SyntaxError: syntax error
```

上面代码的写法会报错，因为 JavaScript 引擎会将 `{x}` 理解成一个代码块，从而发生语法错误。只有不将大括号写在行首，避免 JavaScript 将其解释为代码块，才能解决这个问题。

  ```javascript
  // 正确的写法
  let x;
  ({x} = {x: 1});

  ;([referLine,currentLine] = [currentLine,referLine])
  ```

上面代码将整个解构赋值语句，放在一个圆括号里面，就可以正确执行。关于圆括号与解构赋值的关系，参见下文。

### 运用

对象的解构赋值，可以很方便地将现有对象的方法，赋值到某个变量。

  ```javascript
  let { log, sin, cos } = Math;
  ```

上面代码将 `Math` 对象的对数、正弦、余弦三个方法，赋值到对应的变量上，使用起来就会方便很多。

由于数组本质是特殊的对象，因此可以对数组进行对象属性的解构。

```javascript
  let arr = [1, 2, 3];
  let {0 : first, [arr.length - 1] : last} = arr;
  first // 1
  last // 3
```

上面代码对数组进行对象解构。数组 `arr` 的 `0` 键对应的值是 `1`，`[arr.length - 1]` 就是 `2` 键，对应的值是 `3`。方括号这种写法，属于“属性名表达式”（参见《对象的扩展》一章）。

### 搭配扩展运算符

见扩展运算符一章

## 字符串的解构赋值

字符串也可以解构赋值。这是因为此时，字符串被转换成了一个类似数组的对象。

```javascript
  const [a, b, c, d, e] = 'hello';
  a // "h"
  b // "e"
  c // "l"
  d // "l"
  e // "o"
```

类似数组的对象都有一个 `length` 属性，因此还可以对这个属性解构赋值。

```javascript
  let {length : len} = 'hello';
  len // 5
```

## 数值、布尔值、null、undefined 解构赋值

解构赋值时，如果等号右边是数值和布尔值，则会先 **转为对象**。

```javascript
  let {toString: s} = 123;
  s === Number.prototype.toString // true

  let {toString: s} = true;
  s === Boolean.prototype.toString // true
```

上面代码中，数值和布尔值的包装对象都有 `toString` 属性，因此变量 `s` 都能取到值。

解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于 `undefined` 和 `null` 无法转为对象，所以对它们进行解构赋值，都会报错。

  ```javascript
  let { prop: x } = undefined; // TypeError
  let { prop: y } = null; // TypeError
  ```

## 函数参数的解构赋值

函数的 **参数** 也可以使用解构赋值。

  ```javascript
  function add([x, y]){
    return x + y;
  }

  add([1, 2]); // 3
  ```

上面代码中，函数 `add` 的参数表面上是一个数组，但在传入参数的那一刻，数组参数就被解构成变量 `x` 和 `y`。对于函数内部的代码来说，它们能感受到的参数就是 `x` 和 `y`。

下面是另一个例子。

```javascript
  [[1, 2], [3, 4]].map(([a, b]) => a + b);
  // [ 3, 7 ]
```

函数参数的解构也可以 **使用默认值**。

```javascript
  function move({x = 0, y = 0} = {}) {
    return [x, y];
  }

  move({x: 3, y: 8}); // [3, 8]
  move({x: 3}); // [3, 0]
  move({}); // [0, 0]
  move(); // [0, 0]
```

上面代码中，函数 `move` 的参数是一个对象，通过对这个对象进行解构，得到变量 `x` 和 `y` 的值。如果解构失败，`x` 和 `y` 等于默认值。

**注意**

- 下面的写法会得到不一样的结果。

  ```javascript
  function move({x, y} = { x: 0, y: 0 }) {
    return [x, y];
  }

  move({x: 3, y: 8}); // [3, 8]
  move({x: 3}); // [3, undefined]
  move({}); // [undefined, undefined]
  move(); // [0, 0]
  ```

- 上面代码是为函数 `move` 的参数指定默认值，而不是为变量 `x` 和 `y` 指定默认值，所以会得到与前一种写法不同的结果。
- `undefined` 就会触发函数参数的默认值。

  ```javascript
  [1, undefined, 3].map((x = 'yes') => x);
  // [ 1, 'yes', 3 ]
  ```

## 用途

变量的解构赋值用途很多。

### 交换变量的值

```js
  let x = 1;
  let y = 2;
  
  [x, y] = [y, x];
  
  // 临时变量
  var _ref = [y, x];
  x = _ref[0];
  y = _ref[1];
```

上面代码交换变量 `x` 和 `y` 的值，这样的写法不仅简洁，而且易读，语义非常清晰。

### 从函数返回多个值

函数只能返回一个值，如果要返回多个值，只能将它们放在数组或对象里返回。有了解构赋值，取出这些值就非常方便。

```javascript
  // 返回一个数组
  
  function example() {
    return [1, 2, 3];
  }
  let [a, b, c] = example();
  
  // 返回一个对象
  
  function example() {
    return {
      foo: 1,
      bar: 2
    };
  }
  let { foo, bar } = example();
  
  // 临时变量
  var _example = example(),
      foo = _example.foo,
      bar = _example.bar;
```

### 函数参数的定义

解构赋值可以方便地将一组参数与变量名对应起来。

```javascript
  // 参数是一组有次序的值
  function f([x, y, z]) { ... }
  f([1, 2, 3]);
  
  // 参数是一组无次序的值
  function f({x, y, z}) { ... }
  f({z: 3, y: 2, x: 1});
  
  function f(_ref) {
    var x = _ref.x,
        y = _ref.y,
        z = _ref.z;
  }
  f({ z: 3, y: 2, x: 1 });
```

### 提取 JSON 数据

解构赋值对提取 JSON 对象中的数据，尤其有用。

  ```javascript
  let jsonData = {
    id: 42,
    status: "OK",
    data: [867, 5309]
  };

  let { id, status, data: number } = jsonData;

  console.log(id, status, number);
  // 42, "OK", [867, 5309]
  ```

上面代码可以快速提取 JSON 数据的值。

### 函数参数的默认值

```javascript
  jQuery.ajax = function (url, {
    async = true,
    beforeSend = function () {},
    cache = true,
    complete = function () {},
    crossDomain = false,
    global = true,
    // ... more config
  } = {}) {
    // ... do stuff
  };
```

指定参数的默认值，就避免了在函数体内部再写 `var foo = config.foo || 'default foo';` 这样的语句。

### 遍历 Map 结构

任何部署了 Iterator 接口的对象，都可以用 `for...of` 循环遍历。Map 结构原生支持 Iterator 接口，配合变量的解构赋值，获取键名和键值就非常方便。

```javascript
  const map = new Map();
  map.set('first', 'hello');
  map.set('second', 'world');

  for (let [key, value] of map) {
    console.log(key + " is " + value);
  }
  // first is hello
  // second is world
```

如果只想获取键名，或者只想获取键值，可以写成下面这样。

```javascript
  // 获取键名
  for (let [key] of map) {
    // ...
  }
  
  // 获取键值
  for (let [,value] of map) {
    // ...
  }
```

### 输入模块的指定方法

加载模块时，往往需要指定输入哪些方法。解构赋值使得输入语句非常清晰。

```javascript
  const { SourceMapConsumer, SourceNode } = require("source-map");
```

# 扩展语法

## Not a Operator

https://stackoverflow.com/questions/44934828/is-it-spread-syntax-or-the-spread-operator

## 语法

扩展语法（spread）是三个点（`...`）。它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的 **参数序列**，**隐式遍历**

  ```js
  console.log(1, ...[2, 3, 4], 5)
  // 1 2 3 4 5

  [...document.querySelectorAll('div')]
  // [<div>, <div>, <div>]
  ```

该运算符主要用于函数调用。

  ```js
  function push(array, ...items) {
    array.push(...items);
  }
  
  function add(x, y) {
    return x + y;
  }
  
  const numbers = [4, 38];
  add(...numbers) // 42
  ```

### 注意

扩展运算符后面还可以放置表达式。

```js
  const arr = [
    ...(x > 0 ? ['a'] : []),
    'b',
  ];
```

如果扩展运算符后面是一个空数组，则不产生任何效果。

```js
  [...[], 1]
  // [1]
```

扩展运算符如果放在括号中，JavaScript 引擎就会认为这是函数调用。如果这时不是函数调用，就会报错。

```js
  (...[1, 2])
  // Uncaught SyntaxError: Unexpected number

  console.log((...[1, 2]))
  // Uncaught SyntaxError: Unexpected number

  console.log(...[1, 2])
  // 1 2
```

上面前两种情况都会报错，因为扩展运算符所在的括号不是函数调用，而第三种情况 `console.log(...[1, 2])` 就不会报错，因为这时是函数调用。

## 替代函数的 Apply 方法

由于扩展运算符可以展开数组，所以不再需要 `apply` 方法，将数组转为函数的参数了。

```js
  // ES5 的写法
  function f(x, y, z) {
    // ...
  }
  var args = [0, 1, 2];
  f.apply(null, args);

  // ES6的写法
  function f(x, y, z) {
    // ...
  }
  let args = [0, 1, 2];
  f(...args);
```

下面是扩展运算符取代 `apply` 方法的一个实际的例子，应用 `Math.max` 方法，简化求出一个数组最大元素的写法。

```js
  // ES5 的写法
  Math.max.apply(null, [14, 3, 77])

  // ES6 的写法
  Math.max(...[14, 3, 77])

  // 等同于
  Math.max(14, 3, 77);
```

上面代码中，由于 JavaScript 不提供求 **数组最大元素的函数**，所以只能套用 `Math.max` 函数，将数组转为一个参数序列，然后求最大值。有了扩展运算符以后，就可以直接用 `Math.max` 了。

另一个例子是通过 `push` 函数，将一个数组添加到另一个数组的尾部。

ES5 写法中，`push` 方法的参数不能是数组，所以只好通过 `apply` 方法变通使用 `push` 方法。有了扩展运算符，就可以直接将数组传入 `push` 方法。

下面是另外一个例子。

```js
  // ES5
  new (Date.bind.apply(Date, [null, 2015, 1, 1]))
  // ES6
  new Date(...[2015, 1, 1]);
```

## 扩展运算符的应用

### 复制数组

### 合并数组

扩展运算符提供了数组合并的新写法。

```js
  const arr1 = ['a', 'b'];
  const arr2 = ['c'];
  const arr3 = ['d', 'e'];

  // ES5 的合并数组
  arr1.concat(arr2, arr3);
  // [ 'a', 'b', 'c', 'd', 'e' ]

  // ES6 的合并数组
  [...arr1, ...arr2, ...arr3]
  // [ 'a', 'b', 'c', 'd', 'e' ]
  // babel
  [].concat(arr1, arr2, arr3);
```

不过，这两种方法都是浅拷贝，使用的时候需要注意。

上面代码中，`a3` 和 `a4` 是用两种不同方法合并而成的新数组，但是它们的成员都是对原数组成员的引用，这就是浅拷贝。如果修改了原数组的成员，会同步反映到新数组。

### 与解构赋值结合

扩展运算符可以与解构赋值结合起来，**用于生成数组**。

```js
  // ES5
  a = list[0], rest = list.slice(1)
  // ES6
  [a, ...rest] = list
```

下面是另外一些例子。

```js
  const [first, ...rest] = [1, 2, 3, 4, 5];
  first // 1
  rest  // [2, 3, 4, 5]

  const [first, ...rest] = [];
  first // undefined
  rest  // []

  const [first, ...rest] = ["foo"];
  first  // "foo"
  rest   // []
```

如果将扩展运算符用于数组赋值，只能放在参数的最后一位，否则会报错。

```js
  const [...butLast, last] = [1, 2, 3, 4, 5];
  // 报错
  
  const [first, ...middle, last] = [1, 2, 3, 4, 5];
  // 报错
```

### 字符串

扩展运算符还可以将字符串转为真正的数组。

### 实现了 Iterator 接口的对象、Map 和 Set 结构，Generator 函数

任何定义了遍历器（Iterator）接口的对象（参阅 Iterator 一章），都可以用扩展运算符转为真正的数组。

《数组的扩展》一章中，已经介绍过扩展运算符（`...`）。ES2018 将这个运算符引入了对象。

## 对象的扩展运算符

ES2018 之后也可以用于对象

主要的应用场景是 **解构赋值和浅拷贝**

对象的解构赋值用于从一个对象取值，相当于将目标对象自身的所有可遍历的（enumerable）、但尚未被读取的属性，分配到指定的对象上面。所有的键和它们的值，都会拷贝到新对象上面。

```javascript
let { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };
x // 1
y // 2
z // { a: 3, b: 4 }
```

由于解构赋值要求等号右边是一个对象，所以如果等号右边是 `undefined` 或 `null`，就会报错，因为它们无法转为对象。

```javascript
let { x, y, ...z } = null; // 运行时错误
let { x, y, ...z } = undefined; // 运行时错误
```

解构赋值必须是最后一个参数，否则会报错。

```javascript
let { ...x, y, z } = someObject; // 句法错误
let { x, ...y, ...z } = someObject; // 句法错误
```

注意，解构赋值的拷贝是浅拷贝，即如果一个键的值是复合类型的值（数组、对象、函数）、那么解构赋值拷贝的是这个值的引用，而不是这个值的副本。

```javascript
let obj = { a: { b: 1 } };
let { ...x } = obj;
obj.a.b = 2;
x.a.b // 2
```

上面代码中，`x` 是解构赋值所在的对象，拷贝了对象 `obj` 的 `a` 属性。`a` 属性引用了一个对象，修改这个对象的值，会影响到解构赋值对它的引用。

另外，扩展运算符的解构赋值，不能复制继承自原型对象的属性。

```javascript
let o1 = { a: 1 };
let o2 = { b: 2 };
o2.__proto__ = o1;
let { ...o3 } = o2;
o3 // { b: 2 }
o3.a // undefined
```

上面代码中，对象 `o3` 复制了 `o2`，但是只复制了 `o2` 自身的属性，没有复制它的原型对象 `o1` 的属性。

### 简化默认配置对象

```js
const commonOptions = {
  value: "function myScript(){return 100;}\n",
  mode:  "javascript",
  theme:'material',
  lineNumbers: true,
  lineWrapping:true,
  foldGutter: true,
  gutters:["CodeMirror-linenumbers", "CodeMirror-foldgutter"],
}

const editor = CodeMirror(cm_container.current,commonOptions)
const receiver  = CodeMirror(cm_receiver.current,{
  ...commonOptions,
  readOnly:true
})
```

以前的方法是通过 `Object.assign({},commonOptions,restOptions)`

### 条件扩展

```js
const obj = {
    a: 123,
}

const foo = {
    b: 123,
    ...(obj.a && obj)
}
```

### 总结 @@@

扩展运算符（spread）是三个点（`...`）。它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的 **参数序列**，**隐式遍历**，虽然是参数列表，但包一层 [] 又可以轻松的转换为数组。ES2018 之后也可以用于对象

# 数值的扩展

## Math 对象的扩展

ES6 在 Math 对象上新增了 17 个与数学相关的方法。所有这些方法都是静态方法，只能在 Math 对象上调用。

### 指数运算符

ES2016 新增了一个指数运算符（`**`）。

```javascript
  2 ** 2 // 4
  2 ** 3 // 8
```

这个运算符的一个特点是右结合，而不是常见的左结合。多个指数运算符连用时，是从最右边开始计算的。 #

```javascript
  // 相当于 2 ** (3 ** 2)
  2 ** 3 ** 2
  // 512
```

上面代码中，首先计算的是第二个指数运算符，而不是第一个。

指数运算符可以与等号结合，形成一个新的赋值运算符（`**=`）。

```javascript
  let a = 1.5;
  a **= 2;
  // 等同于 a = a * a;

  let b = 4;
  b **= 3;
  // 等同于 b = b * b * b;
```

注意，V8 引擎的指数运算符与 `Math.pow` 的实现不相同，对于特别大的运算结果，两者会有细微的差异。

  ```javascript
  Math.pow(99, 99)
  // 3.697296376497263e+197

  99 ** 99
  // 3.697296376497268e+197
  ```

上面代码中，两个运算结果的最后一位有效数字是有差异的。

# Symbol 2023-05-06

## 概述

- ES5 的对象属性名都是字符串，这容易造成属性名的冲突。

  > 比如，你使用了一个他人提供的对象，但又想为这个对象添加新的方法（mixin 模式），新方法的名字就有可能与现有方法产生冲突。
  >
- 如果有一种机制，保证每个属性的名字都是独一无二的就好了，这样就从根本上防止属性名的冲突。这就是 ES6 引入 `Symbol` 的原因。
- ES6 引入了一种新的原始数据类型 `Symbol`，表示独一无二的值。它是 JavaScript 语言的第七种数据类型

  > 前六种是：`undefined`、`null`、布尔值（Boolean）、字符串（String）、数值（Number）、对象（Object）。
  >
- Symbol 值通过 `Symbol` 函数生成。这就是说，对象的 **属性名** 现在可以有两种类型，一种是原来就有的字符串，另一种就是新增的 Symbol 类型。凡是属性名属于 Symbol 类型，就都是独一无二的，可以保证不会与其他属性名产生冲突。

  ```javascript
  let s = Symbol();

  typeof s
  // "symbol"
  // 上面代码中，变量s就是一个独一无二的值。typeof运算符的结果，表明变量s是 Symbol 数据类型，而不是字符串之类的其他类型。
  ```

- **注意**，`Symbol` 函数前不能使用 `new` 命令，否则会报错。这是因为生成的 Symbol 是一个 **原始类型的值**，不是对象。也就是说，由于 Symbol 值不是对象，所以不能添加属性。基本上，它是一种 **类似于字符串的数据类型**。 @@@

**参数**

- `Symbol` 函数可以 **接受一个字符串作为参数**，表示对 Symbol 实例的描述，主要是为了在控制台显示，或者转为字符串时，比较容易区分。

  ```javascript
  let s1 = Symbol('foo');
  let s2 = Symbol('bar');

  s1 // Symbol(foo)
  s2 // Symbol(bar)

  s1.toString() // "Symbol(foo)"
  s2.toString() // "Symbol(bar)"
  // 上面代码中，s1和s2是两个 Symbol 值。如果不加参数，它们在控制台的输出都是Symbol()，不利于区分。有了参数以后，就等于为它们加上了描述，输出的时候就能够分清，到底是哪一个值。
  ```

- 如果 Symbol 的参数是一个 **对象**，就会调用该对象的 `toString` 方法，将其转为字符串，然后才生成一个 Symbol 值。

  ```javascript
  const obj = {
    toString() {
      return 'abc';
    }
  };
  const sym = Symbol(obj);
  sym // Symbol(abc)
  ```

- **注意**，`Symbol` 函数的参数只是表示对当前 Symbol 值的描述，因此相同参数的 `Symbol` 函数的返回值是不相等的。

  ```javascript
  // 没有参数的情况
  let s1 = Symbol();
  let s2 = Symbol();
  
  s1 === s2 // false
  
  // 有参数的情况
  let s1 = Symbol('foo');
  let s2 = Symbol('foo');
  
  s1 === s2 // false
  // 上面代码中，s1和s2都是Symbol函数的返回值，而且参数相同，但是它们是不相等的。
  ```

**注意**

- Symbol 值不能与其他类型的值进行运算，会报错。

  ```javascript
  let sym = Symbol('My symbol');

  "your symbol is " + sym
  // TypeError: can't convert symbol to string
  `your symbol is ${sym}`
  // TypeError: can't convert symbol to string
  ```

- 但是，Symbol 值可以 **显式** 转为字符串。

  ```javascript
  let sym = Symbol('My symbol');

  String(sym) // 'Symbol(My symbol)'
  sym.toString() // 'Symbol(My symbol)'
  ```

- 另外，Symbol 值也可以转为 **布尔值**，但是 **不能转为数值**。

  ```javascript
  let sym = Symbol();
  Boolean(sym) // true
  !sym  // false
  
  if (sym) {
    // ...
  }
  
  Number(sym) // TypeError
  sym + 2 // TypeError
  ```

## 作为属性名的 Symbol

- 由于每一个 Symbol 值都是不相等的，这意味着 Symbol 值可以作为标识符，用于对象的属性名，就能保证不会出现同名的属性。这对于一个对象由多个模块构成的情况非常有用，能防止某一个键被不小心改写或覆盖。

  ```javascript
  let mySymbol = Symbol();

  // 第一种写法
  let a = {};
  a[mySymbol] = 'Hello!';

  // 第二种写法
  let a = {
    [mySymbol]: 'Hello!'
  };

  // 第三种写法
  let a = {};
  Object.defineProperty(a, mySymbol, { value: 'Hello!' });

  // 以上写法都得到同样结果
  a[mySymbol] // "Hello!"

  // 上面代码通过方括号结构和Object.defineProperty，将对象的属性名指定为一个 Symbol 值。
  ```

- **注意**，Symbol 值作为对象属性名时，**不能用点运算符**。因为点运算符后面总是字符串，所以不会读取 `mySymbol` 作为标识名所指代的那个值，导致 `a` 的属性名 **实际上是一个字符串**，而不是一个 Symbol 值。

  ```javascript
  const mySymbol = Symbol();
  const a = {};

  a.mySymbol = 'Hello!';
  a[mySymbol] // undefined
  a['mySymbol'] // "Hello!"
  // 变量mySymbol被转换为字符串
  ```

- 同理，在对象的内部，使用 Symbol 值定义属性时，Symbol 值必须放在方括号之中。

  ```javascript
  let s = Symbol();
  
  let obj = {
    [s]: function (arg) { ... }
  };
  
  obj[s](123);
  // 上面代码中，如果s不放在方括号中，该属性的键名就是字符串s，而不是s所代表的那个 Symbol 值。
  ```

**用途**

- Symbol 类型还可以用于 **定义一组常量**，保证这组常量的值都是不相等的。常量使用 Symbol 值最大的好处，就是其他任何值都不可能有相同的值了，因此可以保证 `switch` 语句会按设计的方式工作。

  ```javascript
  const log = {};
  
  log.levels = {
    DEBUG: Symbol('debug'),
    INFO: Symbol('info'),
    WARN: Symbol('warn')
  };
  console.log(log.levels.DEBUG, 'debug message');
  console.log(log.levels.INFO, 'info message');
  
  // 另一个例子
  const COLOR_RED    = Symbol();
  const COLOR_GREEN  = Symbol();
  
  function getComplement(color) {
    switch (color) {
      case COLOR_RED:
        return COLOR_GREEN;
      case COLOR_GREEN:
        return COLOR_RED;
      default:
        throw new Error('Undefined color');
      }
  }
  ```

### 实例：消除魔术字符串

- 魔术字符串指的是，在代码之中多次出现、与代码形成 **强耦合** 的某一个具体的 **字符串或者数值**。风格良好的代码，应该尽量消除魔术字符串，改由含义清晰的变量代替。

  ```javascript
  function getArea(shape, options) {
    let area = 0;

    switch (shape) {
      case 'Triangle': // 魔术字符串
        area = .5 * options.width * options.height;
        break;
      /* ... more code ... */
    }

    return area;
  }

  getArea('Triangle', { width: 100, height: 100 }); // 魔术字符串

  // 上面代码中，字符串Triangle就是一个魔术字符串。它多次出现，与代码形成“强耦合”，不利于将来的修改和维护。
  ```

- 常用的消除魔术字符串的方法，就是把它写成一个变量。

  ```javascript
  const shapeType = {
    triangle: 'Triangle'
  };

  function getArea(shape, options) {
    let area = 0;
    switch (shape) {
      case shapeType.triangle:
        area = .5 * options.width * options.height;
        break;
    }
    return area;
  }

  getArea(shapeType.triangle, { width: 100, height: 100 });

  // 上面代码中，我们把Triangle写成shapeType对象的triangle属性，这样就消除了强耦合。
  ```

- 如果仔细分析，可以发现 `shapeType.triangle` 等于哪个值并不重要，只要确保不会跟其他 `shapeType` 属性的值冲突即可。因此，这里就很适合改用 Symbol 值。

  ```javascript
  const shapeType = {
    triangle: Symbol()
  };
  // 上面代码中，除了将shapeType.triangle的值设为一个 Symbol，其他地方都不用修改。
  ```

### 属性名的遍历

- Symbol 作为属性名，该属性不会出现在 `for...in`、`for...of` 循环中，也不会被 `Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()` 返回。
- 但是，它也不是私有属性，有一个 `Object.getOwnPropertySymbols` 方法，可以获取指定对象的所有 Symbol 属性名。
- `Object.getOwnPropertySymbols` 方法返回一个数组，成员是当前对象的所有用作属性名的 Symbol 值。

  ```javascript
  const obj = {};
  let a = Symbol('a');
  let b = Symbol('b');

  obj[a] = 'Hello';
  obj[b] = 'World';

  const objectSymbols = Object.getOwnPropertySymbols(obj);

  objectSymbols
  // [Symbol(a), Symbol(b)]
  ```

- 下面是另一个例子，`Object.getOwnPropertySymbols` 方法与 `for...in` 循环、`Object.getOwnPropertyNames` 方法进行对比的例子。

  ```javascript
  const obj = {};

  let foo = Symbol("foo");

  Object.defineProperty(obj, foo, {
    value: "foobar",
  });

  for (let i in obj) {
    console.log(i); // 无输出
  }

  Object.getOwnPropertyNames(obj)
  // []

  Object.getOwnPropertySymbols(obj)
  // [Symbol(foo)]

  // 上面代码中，使用Object.getOwnPropertyNames方法得不到Symbol属性名，需要使用Object.getOwnPropertySymbols方法。
  ```

- 另一个新的 API，`Reflect.ownKeys` 方法可以返回所有类型的键名，包括常规键名和 Symbol 键名。

  ```javascript
  let obj = {
    [Symbol('my_key')]: 1,
    enum: 2,
    nonEnum: 3
  };

  Reflect.ownKeys(obj)
  //  ["enum", "nonEnum", Symbol(my_key)]
  ```

- 由于以 Symbol 值作为名称的属性，不会被常规方法遍历得到。我们可以利用这个特性，为对象定义一些 **非私有的**、但又希望 **只用于内部** 的方法。

  ```javascript
  let size = Symbol('size');

  class Collection {
    constructor() {
      this[size] = 0;
    }

    add(item) {
      this[this[size]] = item;
      this[size]++;
    }

    static sizeOf(instance) {
      return instance[size];
    }
  }

  let x = new Collection();
  Collection.sizeOf(x) // 0

  x.add('foo'); x.add('foo'); // 为x添加了一个'0'属性
  Collection.sizeOf(x) // 1

  Object.keys(x) // ['0']
  Object.getOwnPropertyNames(x) // ['0']
  Object.getOwnPropertySymbols(x) // [Symbol(size)]
  ```

- 上面代码中，对象 `x` 的 `size` 属性是一个 Symbol 值，所以 `Object.keys(x)`、`Object.getOwnPropertyNames(x)` 都无法获取它。这就造成了一种非私有的内部方法的效果。

## Symbol.for()，Symbol.keyFor()

- 有时，我们希望重新使用同一个 Symbol 值，`Symbol.for` 方法可以做到这一点。它接受一个字符串作为参数，然后搜索有没有以该参数作为名称的 Symbol 值。如果有，就返回这个 Symbol 值，否则就新建并返回一个以该字符串为名称的 Symbol 值。

  ```javascript
  let s0 = Symbol('foo')
  let s1 = Symbol.for('foo');
  let s2 = Symbol.for('foo');

  s0 === s1 // false
  s1 === s2 // true

  // 上面代码中，s1和s2都是 Symbol 值，但是它们都是同样参数的Symbol.for方法生成的，所以实际上是同一个值。
  ```

- `Symbol.for()` 与 `Symbol()` 这两种写法，都会生成新的 Symbol。它们的区别是，前者会被 **登记** 在全局环境中供搜索，后者不会。`Symbol.for()` 不会每次调用就返回一个新的 Symbol 类型的值，而是会先检查给定的 `key` 是否已经存在，如果不存在才会新建一个值。
- 比如，如果你调用 `Symbol.for("cat")`30 次，每次都会返回同一个 Symbol 值，但是调用 `Symbol("cat")`30 次，会返回 30 个不同的 Symbol 值。

  ```javascript
  Symbol.for("bar") === Symbol.for("bar")
  // true

  Symbol("bar") === Symbol("bar")
  // false
  // 上面代码中，由于Symbol()写法没有登记机制，所以每次调用都会返回一个不同的值。
  ```

- **参数**：如果没有传入字符串，则会自动传入 'undefined'
- ```js
  let s1 = Symbol.for();
  let s2 = Symbol.for();
  
  s1 === s2 // true
  Symbol.keyFor(s1) // 'undefined'
  typeof Symbol.keyFor(s1) // string
  typeof undefined // undefined
  ```
- `Symbol.keyFor` 方法返回一个 **已登记** 的 Symbol 类型值的 `key`。

  ```javascript
  let s1 = Symbol.for("foo");
  Symbol.keyFor(s1) // "foo"

  let s2 = Symbol("foo");
  Symbol.keyFor(s2) // undefined
  typeof Symbol.keyFor(s2) // undefined
  // 上面代码中，变量s2属于未登记的 Symbol 值，所以返回undefined。
  ```

- 需要注意的是，`Symbol.for` 为 Symbol 值登记的名字，是全局环境的，可以在不同的 iframe 或 service worker 中取到同一个值。

  ```javascript
  iframe = document.createElement('iframe');
  iframe.src = String(window.location);
  document.body.appendChild(iframe);
  
  iframe.contentWindow.Symbol.for('foo') === Symbol.for('foo')
  // true，上面代码中，iframe 窗口生成的 Symbol 值，可以在主页面得到。
  ```

## 实例：模块的 Singleton 模式

- Singleton 模式指的是调用一个类，任何时候返回的都是同一个实例。
- 对于 Node 来说，模块文件可以看成是一个类。怎么保证每次执行这个模块文件，返回的都是同一个实例呢？
- 很容易想到，可以把实例放到顶层对象 `global`。

  ```javascript
  // mod.js
  function A() {
    this.foo = 'hello';
  }

  if (!global._foo) {
    global._foo = new A();
  }

  module.exports = global._foo;
  ```

- 然后，加载上面的 `mod.js`。

  ```javascript
  const a = require('./mod.js');
  console.log(a.foo); // hellow
  ```

- 上面代码中，变量 `a` 任何时候加载的都是 `A` 的同一个实例。
- 但是，这里有一个问题，全局变量 `global._foo` 是可写的，任何文件都可以修改。

  ```javascript
  global._foo = { foo: 'world' };

  const a = require('./mod.js');
  console.log(a.foo);
  ```

- 上面的代码，会使得加载 `mod.js` 的脚本都失真。
- 为了防止这种情况出现，我们就可以使用 Symbol。

  ```javascript
  // mod.js
  const FOO_KEY = Symbol.for('foo');

  function A() {
    this.foo = 'hello';
  }

  if (!global[FOO_KEY]) {
    global[FOO_KEY] = new A();
  }

  module.exports = global[FOO_KEY];
  ```

- 上面代码中，可以保证 `global[FOO_KEY]` 不会被无意间覆盖，但还是 **可以被改写**。

  ```javascript
  global[Symbol.for('foo')] = { foo: 'world' };

  const a = require('./mod.js');
  ```

- 如果键名使用 `Symbol` 方法生成，那么外部将无法引用这个值，当然也就无法改写。

  ```javascript
  // mod.js
  const FOO_KEY = Symbol('foo');

  // 后面代码相同 ……
  ```

- 上面代码将导致其他脚本都无法引用 `FOO_KEY`。但这样也有一个问题，就是如果多次执行这个脚本，每次得到的 `FOO_KEY` 都是不一样的。虽然 Node 会将脚本的执行结果缓存，一般情况下，不会多次执行同一个脚本，但是用户可以手动清除缓存，所以也不是绝对可靠。
- 还是使用 `Symbol.for('foo')`

## 内置的 Symbol 值

- 除了定义自己使用的 Symbol 值以外，ES6 还提供了 11 个内置的 Symbol 值
- 这些内置 Symbol 值被 JavaScript **内建算法** 所使用。例如 `Symbol.iterator` 被用来迭代遍历数组项或字符串，甚至定义自己的迭代器函数。
- 这些特殊的 Symbol 值非常重要，因为它们是对象的 **系统属性**，这些属性允许你定义 **定制的行为**。听起来不错吧，用它们来打入 JavaScript 内部！

### Symbol.hasInstance

- 对象的 `Symbol.hasInstance` 属性，指向一个内部方法。当其他对象使用 `instanceof`**运算符**，判断是否为该对象的实例时，会调用这个方法。比如，`foo instanceof Foo` 在语言内部，实际调用的是 `Foo[Symbol.hasInstance](foo)`。

  ```javascript
  class MyClass {
    [Symbol.hasInstance](foo) {
      return foo instanceof Array;
    }
  }

  [1, 2, 3] instanceof new MyClass() // true
  ```

- 上面代码中，`MyClass` 是一个类，`new MyClass()` 会返回一个实例。该实例的 `Symbol.hasInstance` 方法，会在进行 `instanceof` 运算时自动调用，判断左侧的运算子是否为 `Array` 的实例。
- 下面是另一个例子，**定制了通俗的类行为**

  ```javascript
  class Even {
    static [Symbol.hasInstance](obj) {
      return Number(obj) % 2 === 0;
    }
  }
  
  // 等同于
  const Even = {
    [Symbol.hasInstance](obj) {
      return Number(obj) % 2 === 0;
    }
  };
  
  1 instanceof Even // false
  2 instanceof Even // true
  12345 instanceof Even // false
  ```

### Symbol.isConcatSpreadable

- 对象的 `Symbol.isConcatSpreadable` 属性等于一个 **布尔值**，表示该对象用于 `Array.prototype.concat()` 时，是否展开。

  ```javascript
  let arr1 = ['c', 'd'];
  ['a', 'b'].concat(arr1, 'e') // ['a', 'b', 'c', 'd', 'e']
  arr1[Symbol.isConcatSpreadable] // undefined

  let arr2 = ['c', 'd'];
  arr2[Symbol.isConcatSpreadable] = false;
  ['a', 'b'].concat(arr2, 'e') // ['a', 'b', ['c','d'], 'e']
  ```

- 上面代码说明，数组的默认行为是 **展开**，`Symbol.isConcatSpreadable` 默认等于 `undefined`。该属性等于 `true` 时，也有展开的效果。
- **类似数组** 的对象正好相反，默认 **不展开**。它的 `Symbol.isConcatSpreadable` 属性设为 `true`，才可以展开。

  ```javascript
  let obj = {length: 2, 0: 'c', 1: 'd'};
  ['a', 'b'].concat(obj, 'e') // ['a', 'b', obj, 'e']

  obj[Symbol.isConcatSpreadable] = true;
  ['a', 'b'].concat(obj, 'e') // ['a', 'b', 'c', 'd', 'e']
  ```

- `Symbol.isConcatSpreadable` 属性也可以定义在类里面。

  ```javascript
  class A1 extends Array {
    constructor(args) {
      super(args);
      this[Symbol.isConcatSpreadable] = true;
    }
  }
  class A2 extends Array {
    constructor(args) {
      super(args);
    }
    get [Symbol.isConcatSpreadable] () {
      return false;
    }
  }
  let a1 = new A1();
  a1[0] = 3;
  a1[1] = 4;
  let a2 = new A2();
  a2[0] = 5;
  a2[1] = 6;
  [1, 2].concat(a1).concat(a2)
  // [1, 2, 3, 4, [5, 6]]
  ```

- 上面代码中，类 `A1` 是可展开的，类 `A2` 是不可展开的，所以使用 `concat` 时有不一样的结果。
- 注意，`Symbol.isConcatSpreadable` 的位置差异，`A1` 是定义在实例上，`A2` 是定义在类本身，效果相同。

### Symbol.species

### Symbol.match

### Symbol.replace

### Symbol.search

### Symbol.split

### Symbol.iterator

### Symbol.toPrimitive

### Symbol.toStringTag

### Symbol.unscopables

- > https://juejin.im/entry/57bd14961532bc006583d805
  >

# Set 和 Map 数据结构

## Set

### 基本用法

ES6 提供了新的数据结构 Set。它 **类似于数组**，但是成员的值都是 **唯一** 的，没有重复的值。

`Set` 本身是一个 **构造函数**，用来生成 Set 数据结构。

  ```javascript
  const s = new Set();

  [2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));

  for (let i of s) {
    console.log(i);
  }
  // 2 3 5 4
  // 上面代码通过add()方法向 Set 结构加入成员，结果表明 Set 结构不会添加重复的值。
  ```

`Set` 函数可以接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来 **初始化**。 @@@

  ```javascript
  // 例一
  const set = new Set([1, 2, 3, 4, 4]);
  [...set]
  // [1, 2, 3, 4]

  // 例二
  const items = new Set([1, 2, 3, 4, 5, 5, 5, 5]);
  items.size // 5

  // 例三
  const set = new Set(document.querySelectorAll('div'));
  set.size // 56

  // 类似于
  const set = new Set();
  document
   .querySelectorAll('div')
   .forEach(div => set.add(div));
  set.size // 56

  // 上面代码中，例一和例二都是Set函数接受数组作为参数，例三是接受类似数组的对象作为参数。
  ```

- 上面代码也展示了一种 **去除数组重复成员** 的方法。 @@@

  ```javascript
  // 去除数组的重复成员
  [...new Set(array)]
  ```

+ 上面的方法也可以用于，去除字符串里面的 **重复字符**。

  ```javascript
  [...new Set('ababbc')].join('')
  // "abc"
  ```

- 向 Set 加入值的时候，**不会发生类型转换**，所以 `5` 和 `"5"` 是两个不同的值。Set 内部判断两个值是否不同，使用的算法叫做“Same-value-zero equality”，它类似于精确相等运算符（`\===`），主要的区别是 `NaN` 等于自身，而精确相等运算符认为 `NaN` 不等于自身。

  ```javascript
  let set = new Set();
  let a = NaN;
  let b = NaN;
  set.add(a);
  set.add(b);
  set // Set {NaN}
  
  // 上面代码向 Set 实例添加了两个NaN，但是只能加入一个。这表明，在 Set 内部，两个NaN是相等。
  ```

+ 另外，两个对象总是不相等的。

  ```javascript
  let set = new Set();
  
  set.add({});
  set.size // 1
  
  set.add({});
  set.size // 2
  
  // 上面代码表示，由于两个空对象不相等，所以它们被视为两个值。
  ```

### Set 实例的属性和方法

Set 结构的实例有以下属性：

- `Set.prototype.constructor`：构造函数，默认就是 `Set` 函数。
- `Set.prototype.size`：返回 `Set` 实例的成员总数。
- Set 实例的方法分为两大类：**操作方法**（用于操作数据）和 **遍历方法**（用于遍历成员）。

四个 **操作方法**

- `add(value)`：添加某个值，返回 Set 结构本身。
- `delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
  - 如果没有这个值，也就无法删除了
- `has(value)`：返回一个布尔值，表示该值是否为 `Set` 的成员。
- `clear()`：清除所有成员，没有返回值。
- 上面这些属性和方法的实例如下。

  ```javascript
  s.add(1).add(2).add(2);
  // 注意2被加入了两次

  s.size // 2

  s.has(1) // true
  s.has(2) // true
  s.has(3) // false

  s.delete(2);
  s.has(2) // false
  ```

- 下面是一个对比，看看在判断是否包括一个键上面，`Object` 结构和 `Set` 结构的写法不同。

  ```javascript
  // 对象的写法
  const properties = {
    'width': 1,
    'height': 1
  };

  if (properties[someName]) {
    // do something
  }

  // Set的写法
  const properties = new Set();

  properties.add('width');
  properties.add('height');

  if (properties.has(someName)) {
    // do something
  }
  ```

- `Array.from` 方法可以将 Set 结构转为数组。

  ```javascript
  const items = new Set([1, 2, 3, 4, 5]);
  const array = Array.from(items);
  ```

- 这就提供了去除数组重复成员的另一种方法。

  ```javascript
  function dedupe(array) {
    return Array.from(new Set(array));
  }
  
  dedupe([1, 1, 2, 3]) // [1, 2, 3]
  ```

**四个遍历方法**

- `keys()`：返回键名的遍历器
- `values()`：返回键值的遍历器
- `entries()`：返回键值对的遍历器
- `forEach()`：使用回调函数遍历每个成员
- 需要特别指出的是，`Set` 的 **遍历顺序就是插入顺序**。这个特性有时非常有用，比如使用 Set 保存一个回调函数列表，调用时就能保证按照添加顺序调用。
- `keys` 方法、`values` 方法、`entries` 方法返回的都是 **遍历器对象**（详见《Iterator 对象》一章）。由于 Set 结构没有键名，只有键值（或者说 **键名和键值是同一个值**），所以 `keys` 方法和 `values` 方法的行为完全一致。

  ```javascript
  let set = new Set(['red', 'green', 'blue']);
  
  for (let item of set.keys()) {
    console.log(item);
  }
  // red
  // green
  // blue
  
  for (let item of set.values()) {
    console.log(item);
  }
  // red
  // green
  // blue
  
  for (let item of set.entries()) {
    console.log(item);
  }
  // ["red", "red"]
  // ["green", "green"]
  // ["blue", "blue"]
  // 上面代码中，entries方法返回的遍历器，同时包括键名和键值，所以每次输出一个数组，它的两个成员完全相等。
  ```

+ Set 结构的实例默认可遍历，它的 **默认遍历器生成函数** 就是它的 `values` 方法。

  ```javascript
  Set.prototype[Symbol.iterator] === Set.prototype.values
  // true
  ```

+ 这意味着，可以省略 `values` 方法，直接用 `for...of` 循环遍历 Set。

  ```javascript
  let set = new Set(['red', 'green', 'blue']);
  
  for (let x of set) {
    console.log(x);
  }
  // red
  // green
  // blue
  ```

**foreach**

+ Set 结构的实例与数组一样，也拥有 `forEach` 方法，用于对每个成员执行某种操作，没有返回值。

  ```javascript
  let set = new Set([1, 4, 9]);
  set.forEach((value, key) => console.log(key + ' : ' + value))
  // 1 : 1
  // 4 : 4
  // 9 : 9
  ```

+ 上面代码说明，`forEach` 方法的参数就是一个处理函数。该函数的 **参数** 与数组的 `forEach` 一致，依次为键值、键名、集合本身（上例省略了该参数）。这里需要注意，Set 结构的键名就是键值（两者是同一个值），因此第一个参数与第二个参数的值永远都是一样的。
+ 另外，`forEach` 方法还可以有第二个参数，表示绑定处理函数内部的 `this` 对象。

### 遍历的应用

+ 扩展运算符（`...`）内部使用 `for...of` 循环，所以也可以用于 Set 结构。

  ```javascript
  let set = new Set(['red', 'green', 'blue']);
  let arr = [...set];
  // ['red', 'green', 'blue']
  ```

+ 扩展运算符和 Set 结构相结合，就可以去除数组的重复成员。

  ```javascript
  let arr = [3, 5, 2, 2, 5, 5];
  let unique = [...new Set(arr)];
  // [3, 5, 2]
  ```

+ 而且，数组的 `map` 和 `filter` 方法也可以间接用于 Set 了。

  ```javascript
  let set = new Set([1, 2, 3]);
  set = new Set([...set].map(x => x * 2));
  // 返回Set结构：{2, 4, 6}

  let set = new Set([1, 2, 3, 4, 5]);
  set = new Set([...set].filter(x => (x % 2) == 0));
  // 返回Set结构：{2, 4}
  ```

+ 因此使用 Set 可以很容易地实现 **并集（Union）**、**交集（Intersect）**和**差 集（Difference）**。

  ```javascript
  let a = new Set([1, 2, 3]);
  let b = new Set([4, 3, 2]);

  // 并集，两个集合去重
  let union = new Set([...a, ...b]);
  // Set {1, 2, 3, 4}

  // 交集
  let intersect = new Set([...a].filter(x => b.has(x)));
  // set {2, 3}

  // 差集
  let difference = new Set([...a].filter(x => !b.has(x)));
  // Set {1}
  ```

+ 如果想在遍历操作中，同步改变原来的 Set 结构，目前没有直接的方法，但有两种变通方法。一种是利用原 Set 结构映射出一个新的结构，然后赋值给原来的 Set 结构；另一种是利用 `Array.from` 方法。

  ```javascript
  // 方法一
  let set = new Set([1, 2, 3]);
  set = new Set([...set].map(val => val * 2));
  // set的值是2, 4, 6

  // 方法二
  let set = new Set([1, 2, 3]);
  set = new Set(Array.from(set, val => val * 2));
  // set的值是2, 4, 6
  ```

+ 上面代码提供了两种方法，直接在遍历操作中改变原来的 Set 结构。

## WeakSet

### 含义

+ WeakSet 结构与 Set 类似，也是不重复的值的集合。但是，它与 Set 有两个区别。
+ 首先，WeakSet 的成员只能是 **对象**，而不能是其他类型的值。

  ```javascript
  const ws = new WeakSet();
  ws.add(1)
  // TypeError: Invalid value used in weak set
  ws.add(Symbol())
  // TypeError: invalid value used in weak set
  ```

+ 上面代码试图向 WeakSet 添加一个数值和 `Symbol` 值，结果报错，因为 WeakSet 只能放置对象。
+ 其次，WeakSet 中的对象都是 **弱引用**，即垃圾回收机制不考虑 WeakSet 对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 WeakSet 之中。
+ 这是因为垃圾回收机制依赖引用计数，如果一个值的引用次数不为 `0`，垃圾回收机制就不会释放这块内存。结束使用该值之后，有时会忘记取消引用，导致内存无法释放，进而可能会引发内存泄漏。WeakSet 里面的引用，都不计入垃圾回收机制，所以就不存在这个问题。因此，WeakSet 适合临时存放一组对象，以及存放跟对象绑定的信息。只要这些对象在外部消失，它在 WeakSet 里面的引用就会自动消失。
+ 由于上面这个特点，WeakSet 的成员是不适合引用的，因为它会随时消失。另外，由于 WeakSet 内部有多少个成员，取决于垃圾回收机制有没有运行，运行前后很可能成员个数是不一样的，而垃圾回收机制何时运行是不可预测的，因此 ES6 规定 WeakSet 不可遍历。
+ 这些特点同样适用于本章后面要介绍的 WeakMap 结构。

### 语法

+ WeakSet 是一个构造函数，可以使用 `new` 命令，创建 WeakSet 数据结构。

  ```javascript
  const ws = new WeakSet();
  ```

+ 作为构造函数，WeakSet 可以接受一个数组或类似数组的对象作为参数。（实际上，任何具有 Iterable 接口的对象，都可以作为 WeakSet 的参数。）该数组的所有成员，都会自动成为 WeakSet 实例对象的成员。
+ ```javascript
  const a = [[1, 2], [3, 4]];
  const ws = new WeakSet(a);
  // WeakSet {[1, 2], [3, 4]}
  // 上面代码中，a是一个数组，它有两个成员，也都是数组。将a作为 WeakSet 构造函数的参数，a的成员会自动成为 WeakSet 的成员。
  ```
+ 注意，是 `a` 数组的成员成为 WeakSet 的成员，而不是 `a` 数组本身。这意味着，**数组的成员只能是对象**。

  ```javascript
  const b = [3, 4];
  const ws = new WeakSet(b);
  // Uncaught TypeError: Invalid value used in weak set(…)
  ```

### WeakSet 结构的三个方法。

- **WeakSet.prototype.add(value)**：向 WeakSet 实例添加一个新成员。
- **WeakSet.prototype.delete(value)**：清除 WeakSet 实例的指定成员。
- **WeakSet.prototype.has(value)**：返回一个布尔值，表示某个值是否在 WeakSet 实例之中。

  ```javascript
  const ws = new WeakSet();
  const obj = {};
  const foo = {};
  
  ws.add(window);
  ws.add(obj);
  
  ws.has(window); // true
  ws.has(foo);    // false
  
  ws.delete(window);
  ws.has(window);    // false
  ```

+ WeakSet 没有 `size` 属性，没有办法遍历它的成员。

  ```javascript
  ws.size // undefined
  ws.forEach // undefined

  ws.forEach(function(item){ console.log('WeakSet has ' + item)})
  // TypeError: undefined is not a function
  ```

+ 上面代码试图获取 `size` 和 `forEach` 属性，结果都不能成功。
+ WeakSet 不能遍历，是因为成员都是弱引用，随时可能消失，遍历机制无法保证成员的存在，很可能刚刚遍历结束，成员就取不到了。WeakSet 的一个用处，是 **储存 DOM 节点**，而不用担心这些节点从文档移除时，会引发内存泄漏。 @@@
+ 下面是 WeakSet 的另一个例子。

  ```javascript
  const foos = new WeakSet()
  class Foo {
    constructor() {
      foos.add(this)
    }
    method () {
      if (!foos.has(this)) {
        throw new TypeError('Foo.prototype.method 只能在Foo的实例上调用！');
      }
    }
  }
  ```

+ 上面代码保证了 `Foo` 的实例方法，只能在 `Foo` 的实例上调用。这里使用 WeakSet 的好处是，`foos` 对实例的引用，不会被计入内存回收机制，所以删除实例的时候，不用考虑 `foos`，也不会出现内存泄漏。

## Map

### 含义和基本用法

+ JavaScript 的对象（Object），本质上是键值对的集合（Hash 结构），但是传统上只能用字符串当作键。这给它的使用带来了很大的限制。

  ```javascript
  const data = {};
  const element = document.getElementById('myDiv');

  data[element] = 'metadata';
  data['[object HTMLDivElement]'] // "metadata"
  ```

+ 上面代码原意是将一个 DOM 节点作为对象 `data` 的键，但是由于对象只接受字符串作为键名，所以 `element` 被自动转为字符串 `[object HTMLDivElement]`。
+ 为了解决这个问题，ES6 提供了 Map 数据结构。它 **类似于对象**，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。也就是说，Object 结构提供了“字符串—值”的对应，Map 结构提供了“值—值”的对应，是一种更完善的 Hash 结构实现。如果你需要“键值对”的数据结构，Map 比 Object 更合适。

  ```javascript
  const m = new Map();
  const o = {p: 'Hello World'};

  m.set(o, 'content')
  m.get(o) // "content"

  m.has(o) // true
  m.delete(o) // true
  m.has(o) // false
  ```

+ 上面代码使用 Map 结构的 `set` 方法，将对象 `o` 当作 `m` 的一个键，然后又使用 `get` 方法读取这个键，接着使用 `delete` 方法删除了这个键。
+ 上面的例子展示了如何向 Map 添加成员。作为构造函数，Map 也可以接受一个数组作为参数。该 **数组的成员是一个个表示键值对的数组**。

  ```javascript
  const map = new Map([
    ['name', '张三'],
    ['title', 'Author']
  ]);

  map.size // 2
  map.has('name') // true
  map.get('name') // "张三"
  map.has('title') // true
  map.get('title') // "Author"

  // 上面代码在新建 Map 实例时，就指定了两个键name和title。
  ```

+ `Map` 构造函数接受数组作为参数，实际上执行的是下面的算法。

  ```javascript
  const items = [
    ['name', '张三'],
    ['title', 'Author']
  ];

  const map = new Map();

  items.forEach(
    ([key, value]) => map.set(key, value)
  );
  ```

+ 事实上，不仅仅是数组，任何具有 Iterator 接口、且每个成员都是一个 **双元素的数组的数据结构**（详见《Iterator》一章）都可以当作 `Map` 构造函数的参数。这就是说，`Set` 和 `Map` 都可以用来生成新的 Map。

  ```javascript
  const set = new Set([
    ['foo', 1],
    ['bar', 2]
  ]);
  const m1 = new Map(set);
  m1.get('foo') // 1

  const m2 = new Map([['baz', 3]]);
  const m3 = new Map(m2);
  m3.get('baz') // 3

  // 上面代码中，我们分别使用 Set 对象和 Map 对象，当作Map构造函数的参数，结果都生成了新的 Map 对象。
  ```

+ 如果对同一个键多次赋值，后面的值将覆盖前面的值。

  ```javascript
  const map = new Map();

  map
  .set(1, 'aaa')
  .set(1, 'bbb');

  map.get(1) // "bbb"
  ```

+ 如果读取一个未知的键，则返回 `undefined`。

  ```javascript
  new Map().get('asfddfsasadf')
  // undefined
  ```

+ 注意，只有对同一个对象的引用，Map 结构才将其视为同一个键。这一点要非常小心。

  ```javascript
  const map = new Map();

  map.set(['a'], 555);
  map.get(['a']) // undefined

  // 上面代码的set和get方法，表面是针对同一个键，但实际上这是两个值，内存地址是不一样的，因此get方法无法读取该键，返回undefined。
  ```

+ 同理，同样的值的两个实例，在 Map 结构中被视为两个键。

  ```javascript
  const map = new Map();

  const k1 = ['a'];
  const k2 = ['a'];

  map
  .set(k1, 111)
  .set(k2, 222);

  map.get(k1) // 111
  map.get(k2) // 222
  // 上面代码中，变量k1和k2的值是一样的，但是它们在 Map 结构中被视为两个键。
  ```

+ 由上可知，Map 的键实际上是跟内存地址绑定的，只要内存地址不一样，就视为两个键。这就解决了同名属性碰撞（clash）的问题，我们扩展别人的库的时候，如果使用对象作为键名，就不用担心自己的属性与原作者的属性同名。
  + Symbol 也可以解决同样的问题
+ 如果 Map 的键是一个简单类型的值（数字、字符串、布尔值），则只要两个值 **严格相等**，Map 将其视为一个键，比如 `0` 和 `-0` 就是一个键，布尔值 `true` 和字符串 `true` 则是两个不同的键。另外，`undefined` 和 `null` 也是两个不同的键。虽然 `NaN` 不严格相等于自身，但 Map 将其视为同一个键。
  + ===的规则，加上 NaN 的规则，但是没有 +0 -0 的规则，或者是 Object.is() 规则，没有 00
+ ```javascript
  let map = new Map();
  
  map.set(-0, 123);
  map.get(+0) // 123
  
  map.set(true, 1);
  map.set('true', 2);
  map.get(true) // 1
  
  map.set(undefined, 3);
  map.set(null, 4);
  map.get(undefined) // 3
  
  map.set(NaN, 123);
  map.get(NaN) // 123
  ```

### 实例的属性和操作方法

**size 属性**

+ `size` 属性返回 Map 结构的成员总数。

  ```javascript
  const map = new Map();
  map.set('foo', true);
  map.set('bar', false);
  
  map.size // 2
  ```

**set(key, value)**

+ `set` 方法设置键名 `key` 对应的键值为 `value`，然后返回 **整个 Map 结构**。如果 `key` 已经有值，则键值会被更新，否则就新生成该键。

  ```javascript
  const m = new Map();

  m.set('edition', 6)        // 键是字符串
  m.set(262, 'standard')     // 键是数值
  m.set(undefined, 'nah')    // 键是 undefined
  ```

+ `set` 方法返回的是当前的 `Map` 对象，因此可以采用链式写法。

  ```javascript
  let map = new Map()
    .set(1, 'a')
    .set(2, 'b')
    .set(3, 'c');
  ```

**get(key)**

+ `get` 方法读取 `key` 对应的键值，如果找不到 `key`，返回 `undefined`。

  ```javascript
  const m = new Map();
  
  const hello = function() {console.log('hello');};
  m.set(hello, 'Hello ES6!') // 键是函数
  
  m.get(hello)  // Hello ES6!
  ```

**has(key)**

+ `has` 方法返回一个布尔值，表示某个键是否在当前 Map 对象之中。

  ```javascript
  const m = new Map();
  
  m.set('edition', 6);
  m.set(262, 'standard');
  m.set(undefined, 'nah');
  
  m.has('edition')     // true
  m.has('years')       // false
  m.has(262)           // true
  m.has(undefined)     // true
  ```

**delete(key)**

+ `delete` 方法删除某个键，返回 `true`。如果删除失败，返回 `false`。

  ```javascript
  const m = new Map();
  m.set(undefined, 'nah');
  m.has(undefined)     // true
  
  m.delete(undefined)
  m.has(undefined)       // false
  ```

**clear()**

+ `clear` 方法清除所有成员，没有返回值。

  ```javascript
  let map = new Map();
  map.set('foo', true);
  map.set('bar', false);
  
  map.size // 2
  map.clear()
  map.size // 0
  ```

### 遍历方法

Map 结构原生提供三个 **遍历器生成函数** 和一个 **遍历方法**。

- `keys()`：返回键名的遍历器。
- `values()`：返回键值的遍历器。
- `entries()`：返回所有成员的遍历器。
- `forEach()`：遍历 Map 的所有成员。

+ 需要特别注意的是，Map 的 **遍历顺序就是插入顺序**。

  ```javascript
  const map = new Map([
    ['F', 'no'],
    ['T',  'yes'],
  ]);

  for (let key of map.keys()) {
    console.log(key);
  }
  // "F"
  // "T"

  for (let value of map.values()) {
    console.log(value);
  }
  // "no"
  // "yes"

  for (let item of map.entries()) {
    console.log(item[0], item[1]);
  }
  // "F" "no"
  // "T" "yes"

  // 或者
  for (let [key, value] of map.entries()) {
    console.log(key, value);
  }
  // "F" "no"
  // "T" "yes"

  // 等同于使用map.entries()
  for (let [key, value] of map) {
    console.log(key, value);
  }
  // "F" "no"
  // "T" "yes"
  ```

+ 上面代码最后的那个例子，表示 Map 结构的 **默认遍历器接口（`Symbol.iterator` 属性），就是 `entries` 方法**，所以后三者的表现才会如此类似

  ```javascript
  map[Symbol.iterator] === map.entries
  // true
  ```

+ Map 结构转为数组结构，比较快速的方法是使用扩展运算符（`...`）。

  ```javascript
  const map = new Map([
    [1, 'one'],
    [2, 'two'],
    [3, 'three'],
  ]);

  [...map.keys()]
  // [1, 2, 3]

  [...map.values()]
  // ['one', 'two', 'three']

  [...map.entries()]
  // [[1,'one'], [2, 'two'], [3, 'three']]

  [...map]
  // [[1,'one'], [2, 'two'], [3, 'three']]
  ```

+ 结合数组的 `map` 方法、`filter` 方法，可以实现 Map 的遍历和过滤（Map 本身没有 `map` 和 `filter` 方法）。

  ```javascript
  const map0 = new Map()
    .set(1, 'a')
    .set(2, 'b')
    .set(3, 'c');

  const map1 = new Map(
    [...map0].filter(([k, v]) => k < 3)
  );
  // 产生 Map 结构 {1 => 'a', 2 => 'b'}

  const map2 = new Map(
    [...map0].map(([k, v]) => [k * 2, '_' + v])
      );
  // 产生 Map 结构 {2 => '_a', 4 => '_b', 6 => '_c'}
  ```

+ 此外，Map 还有一个 `forEach` 方法，与数组的 `forEach` 方法类似，也可以实现遍历。

  ```javascript
  map.forEach(function(value, key, map) {
    console.log("Key: %s, Value: %s", key, value);
  });
  ```

+ `forEach` 方法还可以接受第二个参数，用来绑定 `this`。

  ```javascript
  const reporter = {
    report: function(key, value) {
      console.log("Key: %s, Value: %s", key, value);
    }
  };

  map.forEach(function(value, key, map) {
    this.report(key, value);
  }, reporter);
  ```

+ 上面代码中，`forEach` 方法的回调函数的 `this`，就指向 `reporter`。

### 与其他数据结构的互相转换

**Map 转为数组**

+ Map 转为数组最方便的方法，就是使用扩展运算符（`...`）。

  ```javascript
  const myMap = new Map()
    .set(true, 7)
    .set({foo: 3}, ['abc']);
  [...myMap]
  // [ [ true, 7 ], [ { foo: 3 }, [ 'abc' ] ] ]
  ```

**数组 转为 Map**

+ 将数组传入 Map 构造函数，就可以转为 Map。

  ```javascript
  new Map([
    [true, 7],
    [{foo: 3}, ['abc']]
  ])
  // Map {
  //   true => 7,
  //   Object {foo: 3} => ['abc']
  // }
  ```

**Map 转为对象**

+ 如果所有 Map 的键都是字符串，它可以无损地转为对象。

  ```javascript
  function strMapToObj(strMap) {
    let obj = Object.create(null);
    for (let [k,v] of strMap) {
      obj[k] = v;
    }
    return obj;
  }

  const myMap = new Map()
    .set('yes', true)
    .set('no', false);
  strMapToObj(myMap)
  // { yes: true, no: false }
  ```

+ 如果有非字符串的键名，那么这个键名会被转成字符串，再作为对象的键名。

**对象转为 Map**

+ ```javascript
  function objToStrMap(obj) {
    let strMap = new Map();
    for (let k of Object.keys(obj)) {
      strMap.set(k, obj[k]);
    }
    return strMap;
  }
  
  objToStrMap({yes: true, no: false})
  // Map {"yes" => true, "no" => false}
  ```

**Map 转为 JSON**

+ Map 转为 JSON 要区分两种情况。一种情况是，Map 的键名都是字符串，这时可以选择转为对象 JSON。

  ```javascript
  function strMapToJson(strMap) {
    return JSON.stringify(strMapToObj(strMap));
  }

  let myMap = new Map().set('yes', true).set('no', false);
  strMapToJson(myMap)
  // '{"yes":true,"no":false}'
  ```

+ 另一种情况是，Map 的键名有非字符串，这时可以选择转为数组 JSON。

  ```javascript
  function mapToArrayJson(map) {
    return JSON.stringify([...map]);
  }
  
  let myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);
  mapToArrayJson(myMap)
  // '[[true,7],[{"foo":3},["abc"]]]'
  ```

**JSON 转为 Map**

+ JSON 转为 Map，正常情况下，所有键名都是字符串。

  ```javascript
  function jsonToStrMap(jsonStr) {
    return objToStrMap(JSON.parse(jsonStr));
  }

  jsonToStrMap('{"yes": true, "no": false}')
  // Map {'yes' => true, 'no' => false}
  ```

+ 但是，有一种特殊情况，整个 JSON 就是一个数组，且每个数组成员本身，又是一个有两个成员的数组。这时，它可以一一对应地转为 Map。这往往是 Map 转为数组 JSON 的逆操作。

  ```javascript
  function jsonToMap(jsonStr) {
    return new Map(JSON.parse(jsonStr));
  }
  
  jsonToMap('[[true,7],[{"foo":3},["abc"]]]')
  // Map {true => 7, Object {foo: 3} => ['abc']}
  ```

## WeakMap

### 含义

+ `WeakMap` 结构与 `Map` 结构类似，也是用于生成键值对的集合。

  ```javascript
  // WeakMap 可以使用 set 方法添加成员
  const wm1 = new WeakMap();
  const key = {foo: 1};
  wm1.set(key, 2);
  wm1.get(key) // 2

  // WeakMap 也可以接受一个数组，
  // 作为构造函数的参数
  const k1 = [1, 2, 3];
  const k2 = [4, 5, 6];
  const wm2 = new WeakMap([[k1, 'foo'], [k2, 'bar']]);
  wm2.get(k2) // "bar"
  ```

+ `WeakMap` 与 `Map` 的区别有两点。
+ 首先，`WeakMap` 只接受对象作为键名（`null` 除外），不接受其他类型的值作为键名。

  ```javascript
  const map = new WeakMap();
  map.set(1, 2)
  // TypeError: 1 is not an object!
  map.set(Symbol(), 2)
  // TypeError: Invalid value used as weak map key
  map.set(null, 2)
  // TypeError: Invalid value used as weak map key
  ```

+ 其次，`WeakMap` 的键名所指向的对象，不计入垃圾回收机制。
+ `WeakMap` 的设计目的在于，有时我们想在某个对象上面存放一些数据，但是这会形成对于这个对象的引用。请看下面的例子。

  ```javascript
  const e1 = document.getElementById('foo');
  const e2 = document.getElementById('bar');
  const arr = [
    [e1, 'foo 元素'],
    [e2, 'bar 元素'],
  ];
  ```

+ 上面代码中，`e1` 和 `e2` 是两个对象，我们通过 `arr` 数组对这两个对象添加一些文字说明。这就形成了 `arr` 对 `e1` 和 `e2` 的引用。
+ 一旦不再需要这两个对象，我们就必须手动删除这个引用，否则垃圾回收机制就不会释放 `e1` 和 `e2` 占用的内存。

  ```javascript
  // 不需要 e1 和 e2 的时候
  // 必须手动删除引用
  arr [0] = null;
  arr [1] = null;
  ```

+ 上面这样的写法显然很不方便。一旦忘了写，就会造成内存泄露。
+ WeakMap 就是为了解决这个问题而诞生的，它的键名所引用的对象都是弱引用，即垃圾回收机制不将该引用考虑在内。因此，只要所引用的对象的其他引用都被清除，垃圾回收机制就会释放该对象所占用的内存。也就是说，一旦不再需要，WeakMap 里面的键名对象和所对应的键值对会自动消失，不用手动删除引用。
+ 基本上，如果你要往对象上添加数据，又不想干扰垃圾回收机制，就可以使用 WeakMap。一个典型应用场景是，在网页的 DOM 元素上添加数据，就可以使用 `WeakMap` 结构。当该 DOM 元素被清除，其所对应的 `WeakMap` 记录就会自动被移除。 @@@

  ```javascript
  const wm = new WeakMap();

  const element = document.getElementById('example');

  wm.set(element, 'some information');
  wm.get(element) // "some information"
  ```

+ 上面代码中，先新建一个 Weakmap 实例。然后，将一个 DOM 节点作为键名存入该实例，并将一些附加信息作为键值，一起存放在 WeakMap 里面。这时，WeakMap 里面对 `element` 的引用就是弱引用，不会被计入垃圾回收机制。
+ 也就是说，上面的 DOM 节点对象的引用计数是 `1`，而不是 `2`。这时，一旦消除对该节点的引用，它占用的内存就会被垃圾回收机制释放。Weakmap 保存的这个键值对，也会自动消失。
+ [https://github.com/mqyqingfeng/Blog/issues/92](https://github.com/mqyqingfeng/Blog/issues/92)
+ 总之，`WeakMap` 的专用场合就是，它的键所对应的对象，可能会在将来消失。`WeakMap` 结构有助于防止内存泄漏。
+ 注意，WeakMap 弱引用的只是 **键名**，而不是键值。键值依然是正常引用。 @@@

  ```javascript
  const wm = new WeakMap();
  let key = {};
  let obj = {foo: 1};

  wm.set(key, obj);
  obj = null;
  wm.get(key)
  // Object {foo: 1}
  ```

+ 上面代码中，键值 `obj` 是正常引用。所以，即使在 WeakMap 外部消除了 `obj` 的引用，WeakMap 内部的引用依然存在。

### WeakMap 的语法

+ WeakMap 与 Map 在 API 上的区别主要是两个，一是没有遍历操作（即没有 `keys()`、`values()` 和 `entries()` 方法），也没有 `size` 属性。因为没有办法列出所有键名，某个键名是否存在完全不可预测，跟垃圾回收机制是否运行相关。这一刻可以取到键名，下一刻垃圾回收机制突然运行了，这个键名就没了，为了防止出现不确定性，就统一规定不能取到键名。二是无法清空，即不支持 `clear` 方法。因此，`WeakMap` 只有四个方法可用：`get()`、`set()`、`has()`、`delete()`。

  ```javascript
  const wm = new WeakMap();
  
  // size、forEach、clear 方法都不存在
  wm.size // undefined
  wm.forEach // undefined
  wm.clear // undefined
  ```

### WeakMap 的示例

### WeakMap 的用途

+ 前文说过，WeakMap 应用的典型场合就是 DOM 节点作为键名。下面是一个例子。

  ```javascript
  let myElement = document.getElementById('logo');
  let myWeakmap = new WeakMap();

  myWeakmap.set(myElement, {timesClicked: 0});

  myElement.addEventListener('click', function() {
    let logoData = myWeakmap.get(myElement);
    // get到的是对象{timesClicked:0}
    logoData.timesClicked++;
  }, false);
  ```

+ 上面代码中，`myElement` 是一个 DOM 节点，每当发生 `click` 事件，就更新一下状态。我们将这个状态作为键值放在 WeakMap 里，对应的键名就是 `myElement`。一旦这个 DOM 节点删除，该状态就会自动消失，不存在内存泄漏风险。
+ WeakMap 的另一个用处是部署私有属性。

  ```javascript
  const _counter = new WeakMap();
  const _action = new WeakMap();

  class Countdown {
    constructor(counter, action) {
      _counter.set(this, counter);
      _action.set(this, action);
    }
    dec() {
      let counter = _counter.get(this);
      if (counter < 1) return;
      counter--;
      _counter.set(this, counter);
      if (counter === 0) {
        _action.get(this)();
      }
    }
  }

  const c = new Countdown(2, () => console.log('DONE'));

  c.dec()
  c.dec()
  // DONE
  ```

+ 上面代码中，`Countdown` 类的两个内部属性 `_counter` 和 `_action`，是实例的弱引用，所以如果删除实例，它们也就随之消失，不会造成内存泄漏。

# FAQ

#faq/js

## 使用闭包实现每隔一秒打印 1,2,3,4

link 一下

```js
// 使用闭包实现
for (var i = 0; i < 5; i++) {
  (function(i) {
    setTimeout(function() {
      console.log(i);
    }, i * 1000);
  })(i);
}

// 使用 let 块级作用域

for (let i = 0; i < 5; i++) {
  setTimeout(function() {
    console.log(i);
  }, i * 1000);
}
```
