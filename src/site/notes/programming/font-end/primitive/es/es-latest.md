---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/es/es-latest/"}
---


# 27. 编程风格

+ 下面的内容主要参考了 [Airbnb](https://github.com/airbnb/javascript) 公司的 JavaScript 风格规范。

## 块级作用域

### Let 取代 Var

+ ES6 提出了两个新的声明变量的命令：`let` 和 `const`。其中，`let` 完全可以取代 `var`，因为两者语义相同，而且 `let` 没有副作用。

  ```javascript
  'use strict';
  
  if (true) {
    let x = 'hello';
  }
  
  for (let i = 0; i < 10; i++) {
    console.log(i);
  }
  ```

+ 上面代码如果用 `var` 替代 `let`，实际上就声明了两个全局变量，这显然不是本意。变量应该只在其声明的代码块内有效，`var` 命令做不到这一点。
+ `var` 命令存在变量提升效用，`let` 命令没有这个问题。

  ```javascript
  'use strict';
  
  if (true) {
    console.log(x); // ReferenceError
    let x = 'hello';
  }
  ```

+ 上面代码如果使用 `var` 替代 `let`，`console.log` 那一行就不会报错，而是会输出 `undefined`，因为变量声明提升到代码块的头部。这违反了变量先声明后使用的原则。
+ 所以，建议不再使用 `var` 命令，而是使用 `let` 命令取代。

### 全局常量和线程安全

+ 在 `let` 和 `const` 之间，建议优先使用 `const`，尤其是在全局环境，不应该设置变量，只应设置常量。
+ `const` 优于 `let` 有几个**原因**。
  + 一个是 `const` 可以提醒阅读程序的人，这个变量不应该改变；
  + 另一个是 `const` 比较符合函数式编程思想，运算不改变值，只是新建值，而且这样也有利于将来的分布式运算；
  + 最后一个原因是 JavaScript 编译器会对 `const` 进行优化，所以多使用 `const`，有利于提高程序的运行效率，也就是说 `let` 和 `const` 的本质区别，其实是编译器内部的处理不同。
+ ```javascript
  // bad
  var a = 1, b = 2, c = 3;
  
  // good
  const a = 1;
  const b = 2;
  const c = 3;
  
  // best
  const [a, b, c] = [1, 2, 3];
  ```
+ `const` 声明常量还有两个**好处**
  + 一是阅读代码的人立刻会意识到不应该修改这个值
  + 二是防止了无意间修改变量值所导致的错误。
+ 所有的函数都应该设置为常量。
+ 长远来看，JavaScript 可能会有多线程的实现（比如 Intel 公司的 River Trail 那一类的项目），这时 `let` 表示的变量，只应出现在单线程运行的代码中，不能是多线程共享的，这样有利于保证线程安全。

## 字符串

+ **静态字符串**一律使用单引号或反引号，**不使用双引号**。**动态字符串**使用反引号。

  ```javascript
  // bad
  const a = "foobar";
  const b = 'foo' + a + 'bar';
  
  // acceptable
  const c = `foobar`;
  
  // good
  const a = 'foobar';
  const b = `foo${a}bar`;
  ```

## 解构赋值

+ 使用数组成员对变量赋值时，优先使用解构赋值。

  ```javascript
  const arr = [1, 2, 3, 4];
  
  // bad
  const first = arr[0];
  const second = arr[1];
  
  // good
  const [first, second] = arr;
  ```

+ 函数的参数如果是对象的成员，优先使用解构赋值。

  ```javascript
  // bad
  function getFullName(user) {
    const firstName = user.firstName;
    const lastName = user.lastName;
  }
  
  // good
  function getFullName(obj) {
    const { firstName, lastName } = obj;
  }
  
  // best
  function getFullName({ firstName, lastName }) {
  }
  ```

+ 如果函数返回多个值，优先使用对象的解构赋值，而不是数组的解构赋值。这样便于以后添加返回值，以及更改返回值的顺序。

  ```javascript
  // bad
  function processInput(input) {
    return [left, right, top, bottom];
  }
  
  // good
  function processInput(input) {
    return { left, right, top, bottom };
  }
  
  const { left, right } = processInput(input);
  ```

## 对象

+ 单行定义的对象，最后一个成员不以逗号结尾。多行定义的对象，最后一个成员以逗号结尾。

  ```javascript
  // bad
  const a = { k1: v1, k2: v2, };
  const b = {
    k1: v1,
    k2: v2
  };
  
  // good
  const a = { k1: v1, k2: v2 };
  const b = {
    k1: v1,
    k2: v2,
  };
  ```

+ 对象尽量静态化，一旦定义，就不得随意添加新的属性。如果添加属性不可避免，要使用 `Object.assign` 方法。

  ```javascript
  // bad
  const a = {};
  a.x = 3;
  
  // if reshape unavoidable
  const a = {};
  Object.assign(a, { x: 3 });
  
  // good
  const a = { x: null };
  a.x = 3;
  ```

+ 如果对象的属性名是动态的，可以在创造对象的时候，使用属性表达式定义。

  ```javascript
  // bad
  const obj = {
    id: 5,
    name: 'San Francisco',
  };
  obj[getKey('enabled')] = true;
  
  // good
  const obj = {
    id: 5,
    name: 'San Francisco',
    [getKey('enabled')]: true,
  };
  ```

+ 上面代码中，对象 `obj` 的最后一个属性名，需要计算得到。这时最好采用属性表达式，在新建 `obj` 的时候，将该属性与其他属性定义在一起。这样一来，所有属性就在一个地方定义了。
+ 另外，对象的属性和方法，尽量采用简洁表达法，这样易于描述和书写。

  ```javascript
  var ref = 'some value';
  
  // bad
  const atom = {
    ref: ref,
  
    value: 1,
  
    addValue: function (value) {
      return atom.value + value;
    },
  };
  
  // good
  const atom = {
    ref,
  
    value: 1,
  
    addValue(value) {
      return atom.value + value;
    },
  };
  ```

## 数组

+ 使用扩展运算符（...）拷贝数组。

  ```javascript
  // bad
  const len = items.length;
  const itemsCopy = [];
  let i;
  
  for (i = 0; i < len; i++) {
    itemsCopy[i] = items[i];
  }
  
  // good
  const itemsCopy = [...items];
  ```

+ 使用 Array.from 方法，将类似数组的对象转为数组。

  ```javascript
  const foo = document.querySelectorAll('.foo');
  const nodes = Array.from(foo);
  ```

## 函数

+ 立即执行函数可以写成箭头函数的形式。

  ```javascript
  (() => {
    console.log('Welcome to the Internet.');
  })();
  ```

+ 那些需要使用函数表达式的场合，尽量用箭头函数代替。因为这样更简洁，而且绑定了 this。

  ```javascript
  // bad
  [1, 2, 3].map(function (x) {
    return x * x;
  });
  
  // good
  [1, 2, 3].map((x) => {
    return x * x;
  });
  
  // best
  [1, 2, 3].map(x => x * x);
  ```

+ 箭头函数取代 `Function.prototype.bind`，不应再用 self/_this/that 绑定 this。

  ```javascript
  // bad
  const self = this;
  const boundMethod = function(...params) {
    return method.apply(self, params);
  }
  
  // acceptable
  const boundMethod = method.bind(this);
  
  // best
  const boundMethod = (...params) => method.apply(this, params);
  ```

+ 简单的、单行的、不会复用的函数，建议采用箭头函数。**如果函数体较为复杂，行数较多，还是应该采用传统的函数写法。**
+ 所有配置项都应该集中在一个对象，放在最后一个参数，布尔值不可以直接作为参数。

  ```javascript
  // bad
  function divide(a, b, option = false ) {
  }
  
  // good
  function divide(a, b, { option = false } = {}) {
  }
  ```

+ 不要在函数体内使用 arguments 变量，使用 rest 运算符（...）代替。因为 rest 运算符**显式表明**你想要获取参数，而且 arguments 是一个类似数组的对象，而 rest 运算符可以提供一个真正的数组。

  ```javascript
  // bad
  function concatenateAll() {
    const args = Array.prototype.slice.call(arguments);
    return args.join('');
  }
  
  // good
  function concatenateAll(...args) {
    return args.join('');
  }
  ```

+ 使用默认值语法设置函数参数的默认值。

  ```javascript
  // bad
  function handleThings(opts) {
    opts = opts || {};
  }
  
  // good
  function handleThings(opts = {}) {
    // ...
  }
  ```

## Map 结构

+ 注意区分 Object 和 Map，只有模拟现实世界的实体对象时，才使用 Object。如果只是需要 `key: value` 的数据结构，使用 Map 结构。因为 Map 有内建的遍历机制。

  ```javascript
  let map = new Map(arr);
  
  for (let key of map.keys()) {
    console.log(key);
  }
  
  for (let value of map.values()) {
    console.log(value);
  }
  
  for (let item of map.entries()) {
    console.log(item[0], item[1]);
  }
  ```

## Class

+ 总是用 Class，取代需要 prototype 的操作。因为 Class 的写法更简洁，更易于理解。

  ```javascript
  // bad
  function Queue(contents = []) {
    this._queue = [...contents];
  }
  Queue.prototype.pop = function() {
    const value = this._queue[0];
    this._queue.splice(0, 1);
    return value;
  }
  
  // good
  class Queue {
    constructor(contents = []) {
      this._queue = [...contents];
    }
    pop() {
      const value = this._queue[0];
      this._queue.splice(0, 1);
      return value;
    }
  }
  ```

+ 使用 `extends` 实现继承，因为这样更简单，不会有破坏 `instanceof` 运算的危险。

  ```javascript
  // bad
  const inherits = require('inherits');
  function PeekableQueue(contents) {
    Queue.apply(this, contents);
  }
  inherits(PeekableQueue, Queue);
  PeekableQueue.prototype.peek = function() {
    return this._queue[0];
  }
  
  // good
  class PeekableQueue extends Queue {
    peek() {
      return this._queue[0];
    }
  }
  ```

## 模块

+ 首先，Module 语法是 JavaScript 模块的标准写法，坚持使用这种写法。使用 `import` 取代 `require`。

  ```javascript
  // bad
  const moduleA = require('moduleA');
  const func1 = moduleA.func1;
  const func2 = moduleA.func2;
  
  // good
  import { func1, func2 } from 'moduleA';
  ```

+ 使用 `export` 取代 `module.exports`。

  ```javascript
  // commonJS的写法
  var React = require('react');
  
  var Breadcrumbs = React.createClass({
    render() {
      return <nav />;
    }
  });
  
  module.exports = Breadcrumbs;
  
  // ES6的写法
  import React from 'react';
  
  class Breadcrumbs extends React.Component {
    render() {
      return <nav />;
    }
  };
  
  export default Breadcrumbs;
  ```

+ 如果模块只有一个输出值，就使用 `export default`，如果模块有多个输出值，就不使用 `export default`，`export default` 与普通的 `export` 不要同时使用。
+ 不要在模块输入中使用通配符。因为这样可以确保你的模块之中，有一个默认输出（export default）。

  ```javascript
  // bad
  import * as myObject from './importModule';
  
  // good
  import myObject from './importModule';
  ```

+ 如果模块默认输出一个函数，函数名的首字母应该小写。

  ```javascript
  function makeStyleGuide() {
  }
  
  export default makeStyleGuide;
  ```

+ 如果模块默认输出一个对象，对象名的首字母应该大写。

  ```javascript
  const StyleGuide = {
    es6: {
    }
  };
  
  export default StyleGuide;
  ```

## ESLint 的使用

+ ESLint 是一个语法规则和代码风格的检查工具，可以用来保证写出语法正确、风格统一的代码。

  首先，安装 ESLint。

  ```bash
  $ npm i -g eslint
  ```

+ 然后，安装 Airbnb 语法规则，以及 import、a11y、react 插件。
+ ```bash
  $ npm i -g eslint-config-airbnb
  $ npm i -g eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react
  ```
+ 最后，在项目的根目录下新建一个 `.eslintrc` 文件，配置 ESLint。

  ```javascript
  {
    "extends": "eslint-config-airbnb"
  }
  ```

+ 现在就可以检查，当前项目的代码是否符合预设的规则。
+ `index.js` 文件的代码如下。

  ```javascript
  var unusued = 'I have no purpose!';
  
  function greet() {
      var message = 'Hello, World!';
      alert(message);
  }
  
  greet();
  ```

+ 使用 ESLint 检查这个文件，就会报出错误。

  ```bash
  $ eslint index.js
  index.js
    1:1  error  Unexpected var, use let or const instead          no-var
    1:5  error  unusued is defined but never used                 no-unused-vars
    4:5  error  Expected indentation of 2 characters but found 4  indent
    4:5  error  Unexpected var, use let or const instead          no-var
    5:5  error  Expected indentation of 2 characters but found 4  indent
  
  ✖ 5 problems (5 errors, 0 warnings)
  ```

+ 上面代码说明，原文件有五个错误，其中两个是不应该使用 `var` 命令，而要使用 `let` 或 `const`；一个是定义了变量，却没有使用；另外两个是行首缩进为 4 个空格，而不是规定的 2 个空格。

# 28. 读懂 ECMAScript 规格

## 概述

+ 规格文件是计算机语言的官方标准，详细描述语法规则和实现方法。
+ 一般来说，没有必要阅读规格，除非你要写编译器。因为规格写得非常抽象和精炼，又缺乏实例，不容易理解，而且对于解决实际的应用问题，帮助不大。但是，如果你遇到疑难的语法问题，实在找不到答案，这时可以去查看规格文件，了解语言标准是怎么说的。规格是解决问题的“最后一招”。
+ 这对 JavaScript 语言很有必要。因为它的使用场景复杂，语法规则不统一，例外很多，各种运行环境的行为不一致，导致奇怪的语法问题层出不穷，任何语法书都不可能囊括所有情况。查看规格，不失为一种解决语法问题的最可靠、最权威的终极方法。
+ 本章介绍如何读懂 ECMAScript 6 的规格文件。
+ ECMAScript 6 的规格，可以在 ECMA 国际标准组织的官方网站（[www.ecma-international.org/ecma-262/6.0/](http://www.ecma-international.org/ecma-262/6.0/)）免费下载和在线阅读。
+ 这个规格文件相当庞大，一共有 26 章，A4 打印的话，足足有 545 页。它的特点就是规定得非常细致，每一个语法行为、每一个函数的实现都做了详尽的清晰的描述。基本上，编译器作者只要把每一步翻译成代码就可以了。这很大程度上，保证了所有 ES6 实现都有一致的行为。
+ ECMAScript 6 规格的 26 章之中，第 1 章到第 3 章是对文件本身的介绍，与语言关系不大。第 4 章是对这门语言总体设计的描述，有兴趣的读者可以读一下。第 5 章到第 8 章是语言宏观层面的描述。第 5 章是规格的名词解释和写法的介绍，第 6 章介绍数据类型，第 7 章介绍语言内部用到的抽象操作，第 8 章介绍代码如何运行。第 9 章到第 26 章介绍具体的语法。
+ 对于一般用户来说，除了第 4 章，其他章节都涉及某一方面的细节，不用通读，只要在用到的时候，查阅相关章节即可。

## 术语

+ ES6 规格使用了一些专门的术语，了解这些术语，可以帮助你读懂规格。本节介绍其中的几个。

### 抽象操作

+ 所谓“抽象操作”（abstract operations）就是引擎的一些内部方法，外部不能调用。规格定义了一系列的抽象操作，规定了它们的行为，留给各种引擎自己去实现。
+ 举例来说，`Boolean(value)` 的算法，第一步是这样的。

  > 1. Let `b` be `ToBoolean(value)`.

+ 这里的 `ToBoolean` 就是一个抽象操作，是引擎内部求出布尔值的算法。
+ 许多函数的算法都会多次用到同样的步骤，所以 ES6 规格将它们抽出来，定义成“抽象操作”，方便描述。

### Record 和 Field

+ ES6 规格将键值对（key-value map）的**数据结构**称为 Record，其中的每一组键值对称为 field。这就是说，一个 Record 由多个 field 组成，而每个 field 都包含一个键名（key）和一个键值（value）。

### ``[[Notation\|Notation]]``

+ ES6 规格大量使用 `[[Notation]]` 这种**书写法**，比如 `[[Value]]`、`[[Writable]]`、`[[Get]]`、`[[Set]]` 等等。它用来指代 field 的键名。
+ 举例来说，`obj` 是一个 Record，它有一个 `Prototype` 属性。ES6 规格不会写 `obj.Prototype`，而是写 `obj.[[Prototype]]`。一般来说，使用 `[[Notation]]` 这种书写法的属性，都是对象的内部属性。
+ 所有的 JavaScript 函数都有一个内部属性 `[[Call]]`，用来运行该函数。

  ```javascript
  F.[[Call]](V, argumentsList)
  ```

+ 上面代码中，`F` 是一个函数对象，`[[Call]]` 是它的内部方法，`F.[[call]]()` 表示运行该函数，`V` 表示 `[[Call]]` 运行时 `this` 的值，`argumentsList` 则是调用时传入函数的参数。

### Completion Record

+ 每一个语句都会返回一个 Completion Record，表示运行结果。每个 Completion Record 有一个 `[[Type]]` 属性，表示运行结果的类型。

  `[[Type]]` 属性有五种可能的值。

  - normal
  - return
  - throw
  - break
  - continue

+ 如果 `[[Type]]` 的值是 `normal`，就称为 normal completion，表示运行正常。其他的值，都称为 abrupt completion。其中，开发者只需要关注 `[[Type]]` 为 `throw` 的情况，即运行出错；`break`、`continue`、`return` 这三个值都只出现在特定场景，可以不用考虑。

## 抽象操作的标准流程

+ 抽象操作的运行流程，一般是下面这样。

  > 1. Let `resultCompletionRecord` be `AbstractOp()`.
  > 2. If `resultCompletionRecord` is an abrupt completion, return `resultCompletionRecord`.
  > 3. Let `result` be `resultCompletionRecord.[[Value]]`.
  > 4. return `result`.

+ 上面的第一步是调用抽象操作 `AbstractOp()`，得到 `resultCompletionRecord`，这是一个 Completion Record。第二步，如果这个 Record 属于 abrupt completion，就将 `resultCompletionRecord` 返回给用户。如果此处没有返回，就表示运行结果正常，所得的值存放在 `resultCompletionRecord.[[Value]]` 属性。第三步，将这个值记为 `result`。第四步，将 `result` 返回给用户。
+ ES6 规格将这个标准流程，使用简写的方式表达。

  > 1. Let `result` be `AbstractOp()`.
  > 2. `ReturnIfAbrupt(result)`.
  > 3. return `result`.

+ 这个简写方式里面的 `ReturnIfAbrupt(result)`，就代表了上面的第二步和第三步，即如果有报错，就返回错误，否则取出值。

  甚至还有进一步的简写格式。

  > 1. Let `result` be `? AbstractOp()`.
  > 2. return `result`.

+ 上面流程的 `?`，就代表 `AbstractOp()` 可能会报错。一旦报错，就返回错误，否则取出值。
+ 除了 `?`，ES 6 规格还使用另一个简写符号 `!`。

  > 1. Let `result` be `! AbstractOp()`.
  > 2. return `result`.

+ 上面流程的 `!`，代表 `AbstractOp()` 不会报错，返回的一定是 normal completion，总是可以取出值。

## 相等运算符

+ 下面通过一些例子，介绍如何使用这份规格。
+ 相等运算符（`\==`）是一个很让人头痛的运算符，它的语法行为多变，不符合直觉。这个小节就看看规格怎么规定它的行为。
+ 请看下面这个表达式，请问它的值是多少。
+ ```javascript
  0 == null
  ```
+ 如果你不确定答案，或者想知道语言内部怎么处理，就可以去查看规格，[7.2.12 小节](http://www.ecma-international.org/ecma-262/6.0/#sec-abstract-equality-comparison) 是对相等运算符（`\==`）的描述。
+ 规格对每一种语法行为的描述，都分成两部分：先是总体的行为描述，然后是实现的算法细节。相等运算符的总体描述，只有一句话。
+ > “The comparison `x == y`, where `x` and `y` are values, produces `true` or `false`.”
+ 上面这句话的意思是，相等运算符用于比较两个值，返回 `true` 或 `false`。
+ 下面是算法细节。

  > 1. ReturnIfAbrupt(x).
  > 2. ReturnIfAbrupt(y).
  > 3. If `Type(x)` is the same as `Type(y)`, then Return the result of performing Strict Equality Comparison `x === y`.
  > 4. If `x` is `null` and `y` is `undefined`, return `true`.
  > 5. If `x` is `undefined` and `y` is `null`, return `true`.
  > 6. If `Type(x)` is Number and `Type(y)` is String, return the result of the comparison `x == ToNumber(y)`.
  > 7. If `Type(x)` is String and `Type(y)` is Number, return the result of the comparison `ToNumber(x) == y`.
  > 8. If `Type(x)` is Boolean, return the result of the comparison `ToNumber(x) == y`.
  > 9. If `Type(y)` is Boolean, return the result of the comparison `x == ToNumber(y)`.
  > 10. If `Type(x)` is either String, Number, or Symbol and `Type(y)`is Object, then return the result of the comparison `x == ToPrimitive(y)`.
  > 11. If `Type(x)` is Object and `Type(y)` is either String, Number, or Symbol, then return the result of the comparison `ToPrimitive(x) == y`.
  > 12. Return `false`.

+ 上面这段算法，一共有 12 步，翻译如下。

  > 1. 如果 `x` 不是正常值（比如抛出一个错误），中断执行。
  > 2. 如果 `y` 不是正常值，中断执行。
  > 3. 如果 `Type(x)` 与 `Type(y)` 相同，执行严格相等运算 `x === y`。
  > 4. 如果 `x` 是 `null`，`y` 是 `undefined`，返回 `true`。
  > 5. 如果 `x` 是 `undefined`，`y` 是 `null`，返回 `true`。
  > 6. 如果 `Type(x)` 是数值，`Type(y)` 是字符串，返回 `x == ToNumber(y)` 的结果。
  > 7. 如果 `Type(x)` 是字符串，`Type(y)` 是数值，返回 `ToNumber(x) == y` 的结果。
  > 8. 如果 `Type(x)` 是布尔值，返回 `ToNumber(x) == y` 的结果。
  > 9. 如果 `Type(y)` 是布尔值，返回 `x == ToNumber(y)` 的结果。
  > 10. 如果 `Type(x)` 是字符串或数值或 `Symbol` 值，`Type(y)` 是对象，返回 `x == ToPrimitive(y)` 的结果。
  > 11. 如果 `Type(x)` 是对象，`Type(y)` 是字符串或数值或 `Symbol` 值，返回 `ToPrimitive(x) == y` 的结果。
  > 12. 返回 `false`。

+ 由于 `0` 的类型是数值，`null` 的类型是 Null（这是规格 [4.3.13 小节](http://www.ecma-international.org/ecma-262/6.0/#sec-terms-and-definitions-null-type) 的规定，是内部 Type 运算的结果，跟 `typeof` 运算符无关）。因此上面的前 11 步都得不到结果，要到第 12 步才能得到 `false`。

  ```javascript
  0 == null // false
  ```

## 数组的空位

+ 下面再看另一个例子。

  ```javascript
  const a1 = [undefined, undefined, undefined];
  const a2 = [, , ,];
  
  a1.length // 3
  a2.length // 3
  
  a1[0] // undefined
  a2[0] // undefined
  
  a1[0] === a2[0] // true
  ```

+ 上面代码中，数组 `a1` 的成员是三个 `undefined`，数组 `a2` 的成员是三个空位。这两个数组很相似，长度都是 3，每个位置的成员读取出来都是 `undefined`。
+ 但是，它们实际上存在重大差异。

  ```javascript
  0 in a1 // true
  0 in a2 // false
  
  a1.hasOwnProperty(0) // true
  a2.hasOwnProperty(0) // false
  
  Object.keys(a1) // ["0", "1", "2"]
  Object.keys(a2) // []
  
  a1.map(n => 1) // [1, 1, 1]
  a2.map(n => 1) // [, , ,]
  ```

+ 上面代码一共列出了四种运算，数组 `a1` 和 `a2` 的结果都不一样。前三种运算（`in` 运算符、数组的 `hasOwnProperty` 方法、`Object.keys` 方法）都说明，数组 `a2` 取不到属性名。最后一种运算（数组的 `map` 方法）说明，数组 `a2` 没有发生遍历。
+ 为什么 `a1` 与 `a2` 成员的行为不一致？数组的成员是 `undefined` 或空位，到底有什么不同？

  规格的 [12.2.5 小节《数组的初始化》](http://www.ecma-international.org/ecma-262/6.0/#sec-array-initializer) 给出了答案。

  > “Array elements may be elided at the beginning, middle or end of the element list. Whenever a comma in the element list is not preceded by an AssignmentExpression (i.e., a comma at the beginning or after another comma), the missing array element contributes to the length of the Array and increases the index of subsequent elements. Elided array elements are not defined. If an element is elided at the end of an array, that element does not contribute to the length of the Array.”

+ 翻译如下。

  > " 数组成员可以省略。只要逗号前面没有任何表达式，数组的 `length` 属性就会加 1，并且相应增加其后成员的位置索引。被省略的成员不会被定义。如果被省略的成员是数组最后一个成员，则不会导致数组 `length` 属性增加。”

+ 上面的规格说得很清楚，数组的空位会反映在 `length` 属性，也就是说空位有自己的位置，但是这个位置的值是未定义，即这个值是不存在的。如果一定要读取，结果就是 `undefined`（因为 `undefined` 在 JavaScript 语言中表示不存在）。
+ 这就解释了为什么 `in` 运算符、数组的 `hasOwnProperty` 方法、`Object.keys` 方法，都取不到空位的属性名。因为这个属性名根本就不存在，规格里面没说要为空位分配属性名 (位置索引），只说要为下一个元素的位置索引加 1。
+ 至于为什么数组的 `map` 方法会跳过空位，请看下一节。

## 数组的 Map 方法

+ 规格的 [22.1.3.15 小节](http://www.ecma-international.org/ecma-262/6.0/#sec-array.prototype.map) 定义了数组的 `map` 方法。该小节先是总体描述 `map` 方法的行为，里面没有提到数组空位。
+ 后面的算法描述是这样的。

  > 1. Let `O` be `ToObject(this value)`.
  > 2. `ReturnIfAbrupt(O)`.
  > 3. Let `len` be `ToLength(Get(O, "length"))`.
  > 4. `ReturnIfAbrupt(len)`.
  > 5. If `IsCallable(callbackfn)` is `false`, throw a TypeError exception.
  > 6. If `thisArg` was supplied, let `T` be `thisArg`; else let `T` be `undefined`.
  > 7. Let `A` be `ArraySpeciesCreate(O, len)`.
  > 8. `ReturnIfAbrupt(A)`.
  > 9. Let `k` be 0.
  > 10. Repeat, while `k` < `len`
  > 1. Let `Pk` be `ToString(k)`.
  > 2. Let `kPresent` be `HasProperty(O, Pk)`.
  > 3. `ReturnIfAbrupt(kPresent)`.
  > 4. If `kPresent` is `true`, then
  > 1. Let `kValue` be `Get(O, Pk)`.
  > 2. `ReturnIfAbrupt(kValue)`.
  > 3. Let `mappedValue` be `Call(callbackfn, T, «kValue, k, O»)`.
  > 4. `ReturnIfAbrupt(mappedValue)`.
  > 5. Let `status` be `CreateDataPropertyOrThrow (A, Pk, mappedValue)`.
  > 6. `ReturnIfAbrupt(status)`.
  > 5. Increase `k` by 1.
  > 11. Return `A`.

+ 翻译如下。

  > 1. 得到当前数组的 `this` 对象
  > 2. 如果报错就返回
  > 3. 求出当前数组的 `length` 属性
  > 4. 如果报错就返回
  > 5. 如果 map 方法的参数 `callbackfn` 不可执行，就报错
  > 6. 如果 map 方法的参数之中，指定了 `this`，就让 `T` 等于该参数，否则 `T` 为 `undefined`
  > 7. 生成一个新的数组 `A`，跟当前数组的 `length` 属性保持一致
  > 8. 如果报错就返回
  > 9. 设定 `k` 等于 0
  > 10. 只要 `k` 小于当前数组的 `length` 属性，就重复下面步骤
  > 1. 设定 `Pk` 等于 `ToString(k)`，即将 `K` 转为字符串
  > 2. 设定 `kPresent` 等于 `HasProperty(O, Pk)`，即求当前数组有没有指定属性
  > 3. 如果报错就返回
  > 4. 如果 `kPresent` 等于 `true`，则进行下面步骤
  > 1. 设定 `kValue` 等于 `Get(O, Pk)`，取出当前数组的指定属性
  > 2. 如果报错就返回
  > 3. 设定 `mappedValue` 等于 `Call(callbackfn, T, «kValue, k, O»)`，即执行回调函数
  > 4. 如果报错就返回
  > 5. 设定 `status` 等于 `CreateDataPropertyOrThrow (A, Pk, mappedValue)`，即将回调函数的值放入 `A` 数组的指定位置
  > 6. 如果报错就返回
  > 5. `k` 增加 1
  > 11. 返回 `A`

+ 仔细查看上面的算法，可以发现，当处理一个全是空位的数组时，前面步骤都没有问题。进入第 10 步中第 2 步时，`kPresent` 会报错，因为空位对应的属性名，对于数组来说是不存在的，因此就会返回，不会进行后面的步骤。

  ```javascript
  const arr = [, , ,];
  arr.map(n => {
    console.log(n);
    return 1;
  }) // [, , ,]
  ```

+ 上面代码中，`arr` 是一个全是空位的数组，`map` 方法遍历成员时，发现是空位，就直接跳过，不会进入回调函数。因此，回调函数里面的 `console.log` 语句根本不会执行，整个 `map` 方法返回一个全是空位的新数组。
+ V8 引擎对 `map` 方法的 [实现](https://github.com/v8/v8/blob/44c44521ae11859478b42004f57ea93df52526ee/src/js/array.js#L1347) 如下，可以看到跟规格的算法描述完全一致。

  ```javascript
  function ArrayMap(f, receiver) {
    CHECK_OBJECT_COERCIBLE(this, "Array.prototype.map");
  
    // Pull out the length so that modifications to the length in the
    // loop will not affect the looping and side effects are visible.
    var array = TO_OBJECT(this);
    var length = TO_LENGTH_OR_UINT32(array.length);
    return InnerArrayMap(f, receiver, array, length);
  }
  
  function InnerArrayMap(f, receiver, array, length) {
    if (!IS_CALLABLE(f)) throw MakeTypeError(kCalledNonCallable, f);
  
    var accumulator = new InternalArray(length);
    var is_array = IS_ARRAY(array);
    var stepping = DEBUG_IS_STEPPING(f);
    for (var i = 0; i < length; i++) {
      if (HAS_INDEX(array, i, is_array)) {
        var element = array[i];
        // Prepare break slots for debugger step in.
        if (stepping) %DebugPrepareStepInIfStepping(f);
        accumulator[i] = %_Call(f, receiver, element, i, array);
      }
    }
    var result = new GlobalArray();
    %MoveArrayContents(accumulator, result);
    return result;
  }
  ```

# ArrayBuffer

# 最新提案

# Decorator

# 参考
