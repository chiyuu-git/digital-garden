---
{"dg-publish":true,"permalink":"/programming/faq/js-faq/js-faq/"}
---


# Global FAQ Collection

| File                                                                           | overview               |
| ------------------------------------------------------------------------------ | ---------------------- |
| [[programming/font-end/primitive/es/es-string\|es-string]]                  | [[programming/font-end/primitive/es/es-string#faq\|es-string#faq]]      |
| [[programming/font-end/primitive/es/es-number\|es-number]]                  | [[programming/font-end/primitive/es/es-number#faq\|es-number#faq]]      |
| [[programming/font-end/primitive/es/es-date\|es-date]]                      | [[programming/font-end/primitive/es/es-date#faq\|es-date#faq]]        |
| [[programming/font-end/primitive/es/es-regexp\|es-regexp]]                  | [[programming/font-end/primitive/es/es-regexp#faq\|es-regexp#faq]]      |
| [[programming/font-end/primitive/es/es-object\|es-object]]                  | [[programming/font-end/primitive/es/es-object#faq\|es-object#faq]]      |
| [[programming/font-end/primitive/es/es-next-1\|es-next-1]]                  | [[programming/font-end/primitive/es/es-next-1#faq\|es-next-1#faq]]      |
| [[programming/font-end/primitive/es/es-basic\|es-basic]]                    | [[programming/font-end/primitive/es/es-basic#faq\|es-basic#faq]]       |
| [[programming/font-end/primitive/es/es-async-2\|es-async-2]]                | [[programming/font-end/primitive/es/es-async-2#faq\|es-async-2#faq]]     |
| [[programming/font-end/primitive/es/es-array\|es-array]]                    | [[programming/font-end/primitive/es/es-array#faq\|es-array#faq]]       |
| [[programming/font-end/primitive/browser-api/dom-interface\|dom-interface]] | [[programming/font-end/primitive/browser-api/dom-interface#faq\|dom-interface#faq]]  |
| [[programming/font-end/primitive/browser-api/dom-event\|dom-event]]         | [[programming/font-end/primitive/browser-api/dom-event#faq\|dom-event#faq]]      |
| [[programming/font-end/primitive/browser-api/bom\|bom]]                     | [[programming/font-end/primitive/browser-api/bom#faq\|bom#faq]]            |
| [[programming/font-end/primitive/browser-api/dom-selection\|dom-selection]] | [[programming/font-end/primitive/browser-api/dom-selection#faq\|dom-selection#faq]]  |
| [[programming/font-end/framework/typescript/ts-advanced\|ts-advanced]]      | [[programming/font-end/framework/typescript/ts-advanced#faq\|ts-advanced#faq]]    |
| [[programming/font-end/browser-core/browser-thread\|browser-thread]]        | [[programming/font-end/browser-core/browser-thread#faq\|browser-thread#faq]] |
| [[programming/font-end/primitive/es/es-proto\|es-proto]]                    | [[programming/font-end/primitive/es/es-proto#faq\|es-proto#faq]]       |

{ .block-language-dataview}

[README-zh-cn.md](https://github.com/denysdovhan/wtfjs/blob/master/README-zh-cn.md)

https://github.com/lydiahallie/javascript-questions

[GitHub - goldbergyoni/nodebestpractices: :white\_check\_mark: The Node.js best practices list (July 2023)](https://github.com/goldbergyoni/nodebestpractices)

[GitHub - goldbergyoni/javascript-testing-best-practices: 📗🌐 🚢 Comprehensive and exhaustive JavaScript & Node.js testing best practices (June 2023)](https://github.com/goldbergyoni/javascript-testing-best-practices)

详细资料可以参考： [《前端程序员经常忽视的一个 JavaScript 面试题》](https://github.com/Wscats/Good-text-Share/issues/85) [《一道考察运算符优先级的 JavaScript 面试题》](https://segmentfault.com/q/1010000008430170) [《一道常被人轻视的前端 JS 面试题》](https://www.cnblogs.com/xxcanghai/p/5189353.html)

# Polyfill

## Call()

call() 方法在使用一个指定的 this 值和若干个指定的参数值的前提下调用某个函数或方法。

让某个函数临时成为指定的 this 的方法，并且进行调用

```js
  var foo = {
      value: 1
  };
  
  function bar() {
      console.log(this.value);
  }
  
  bar.call(foo); // 1
```

call 改变了 this 的指向，指向到 foo

同时，bar 函数执行了

该方法的语法和作用与 [`apply()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) 方法类似，只有一个区别，就是 `call()` 方法接受的是**一个参数列表**，而 `apply()` 方法接受的是**一个包含多个参数的数组**

### 模拟实现第一步

1. 将函数设为对象的属性
2. 执行该函数
3. 删除该函数

改造上面的例子

```js
  // 第一步
  foo.fn = bar
  // 第二步
  foo.fn()
  // 第三步
  delete foo.fn
```

完整版 myCall 函数

```js
  Function.prototype.myCall = function(context){
    // context是传统意义上的this，一个obj
    // 这里的this是要执行的函数, 因为 mycall 是定义在原型上的
    // foo.fn = bar
    context.fn = this 
    context.fn()
    delete context.fn
  }
  
  const foo = {
    value:1,
  }
  
  function bar(){
    console.log(this.value)
  }
  
  bar.myCall(foo) // 1
```

### 模拟实现第二步

call 函数还能给定参数执行函数，但是，传入的参数并不确定，并且传入的参数要再传入到调用的函数中

两种错误的做法

  ```js
  // 将数组里的元素作为多个参数放进函数的形参里
  context.fn(args.join(','))
  // 相当于是传入了一个 join 好的字符串
  
  // 相当于是传入了一个类数组对象，只有第一个形参被赋予该值
  context.fn(arguments)
  ```

使用 ES6 的做法，使用扩展运算符即可解决

  ```js
  Function.prototype.myCall = function(context,...rest){
    context.fn = this
    // {value: 1, fn: },1,2,3
    // context.fn(...arguments)
    context.fn(..rest)
    delete context.fn
  }
  
  const foo = {
    value:1,
  }
  
  function bar(a,b){
    console.log(this.value)
    console.log(a,b) // this,1
  }
  
  bar.myCall(foo,1,2,3) // 1
  ```

不过 call 是 ES3 的方法，模拟实现就别用 ES6 了, 可以使用 eval, 不深究

### 模拟实现第三步

两个注意点

- this 参数可以传 null，当为 null 的时候，视为指向 window
- 函数是可以有返回值的！

最终版完整代码

```js
  // 第三版
  Function.prototype.call2 = function (context) {
      // null
      var context = context || window;
      context.fn = this;
  
      var args = [];
      for(var i = 1, len = arguments.length; i < len; i++) {
          args.push('arguments[' + i + ']');
      }
    
      var result = eval('context.fn(' + args +')');
  
      delete context.fn
      return result;
  }
  
  // 测试一下
  var value = 2;
  
  var obj = {
      value: 1
  }
  
  function bar(name, age) {
      console.log(this.value);
      return {
          value: this.value,
          name: name,
          age: age
      }
  }
  
  bar.call2(null); // 2
  
  console.log(bar.call2(obj, 'kevin', 18));
  // 1
  // Object {
  //    value: 1,
  //    name: 'kevin',
  //    age: 18
  // }
```

## Apply()

 ```js
  Function.prototype.apply = function (context, arr) {
      var context = Object(context) || window;
      context.fn = this;
  
      var result;
      if (!arr) {
          result = context.fn();
      }
      else {
          var args = [];
          for (var i = 0, len = arr.length; i < len; i++) {
              args.push('arr[' + i + ']');
          }
          result = eval('context.fn(' + args + ')')
      }
  
      delete context.fn
      return result;
  }
  ```

## Bind()

 bind() 方法会创建一个新函数。当这个新函数被调用时，bind() 的第一个参数将作为它运行时的 this，之后的一序列参数将会在传递的实参前传入作为它的参数。

### 返回函数的实现

 返回一个函数

 可以指定 this，关于 this 的指定，我们可以使用 call() 或者 apply() 方法实现

  ```js
  Function.prototype.bind2 = function (context) {
  	// self = bar
    var self = this;
    return function () {
      // bar.apply(foo)
      return self.apply(context); // 调用函数可能会有返回值，所以需要再return一个
    }
  
  }
  const foo = {
    value:1,
  }
  
  function bar(){
    console.log(this.value)
  }
  
  bar.bind2(foo)
  ```

### 分段传参的实现

 ```js
  var foo = {
      value: 1
  };
  
  function bar(name, age) {
      console.log(this.value);
      console.log(name);
      console.log(age);
  
  }
  
  var bindFoo = bar.bind(foo, 'daisy');
  bindFoo('18');
  // 1
  // daisy
  // 18
  ```

函数需要传 name 和 age 两个参数，竟然还可以在 bind 的时候，只传一个 name，在执行返回的函数的时候，再传另一个参数 age!

我们用 arguments 进行处理：

  ```js
  // 第二版
  Function.prototype.bind2 = function (context) {
    var self = this;
    // 获取bind2函数从第二个参数到最后一个参数
    var args = Array.prototype.slice.call(arguments, 1);
    return function (...bindArgs) {
      return self.apply(context, args.concat(bindArgs));
    }
  }
  ```

其实就是一个简单的函数柯里化

### 构造函数效果的实现

因为 bind 还有一个特点，就是

> 一个绑定函数也能使用 new 操作符创建对象：这种行为就像把原函数当成构造器。提供的 this 值被忽略，同时调用时的参数被提供给模拟函数。

先来看一个例子

```js
  var value = 2;
  
  var foo = {
      value: 1
  };
  
  function bar(name, age) {
      this.habit = 'shopping';
      console.log(this.value);
      console.log(name);
      console.log(age);
  }
  
  bar.prototype.friend = 'kevin';
  
  var bindFoo = bar.bind(foo, 'daisy');
  
  var obj = new bindFoo('18');
  // undefined
  // daisy
  // 18
  console.log(obj.habit); // shopping
  console.log(obj.friend); // kevin
  
```

**注意**: 尽管在全局和 foo 中都声明了 value 值，最后依然返回了 undefind，说明绑定的 this 失效了，如果大家了解 new 的模拟实现，就会知道这个时候的 this 已经指向了 obj。

我们可以通过修改返回的函数的原型来实现，让我们写一下：

```js
  // 第三版
  Function.prototype.bind2 = function (context) {
    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);
    var fBound = function (...bindArgs) {
      // 当作为构造函数时，this 指向实例，此时结果为 true，将绑定函数的 this 指向该实例，可以让实例获得来自绑定函数的值
      // 以上面的是 demo 为例，如果改成 `this instanceof fBound ? null : context`，实例只是一个空对象，将 null 改成 this ，实例会具有 habit 属性
      // 当作为普通函数时，this 指向 window，此时结果为 false，将绑定函数的 this 指向 context
      return self.apply(this instanceof fBound ? this : context, args.concat(bindArgs));
    }
    // 修改返回函数的 prototype 为绑定函数的 prototype，实例就可以继承绑定函数的原型中的值
    fBound.prototype = this.prototype;
    return fBound;
  }
```

### 构造函数效果的优化实现

但是在这个写法中，我们直接将 fBound.prototype = this.prototype，我们直接修改 fBound.prototype 的时候，也会直接修改绑定函数的 prototype。这个时候，我们可以通过一个空函数来进行中转：

```js
  Function.prototype.bind2 = function (context) {
  
      var self = this;
      var args = Array.prototype.slice.call(arguments, 1);
  
      var fNOP = function () {};
  
      var fBound = function () {
          var bindArgs = Array.prototype.slice.call(arguments);
          return self.apply(this instanceof fBount ? this : context, args.concat(bindArgs));
      }
      fNOP.prototype = this.prototype;
      // 即是fBound的实例，也是fNOP的实例
      fBound.prototype = new fNOP();
      return fBound;
  }
```

寄生构造函数 [寄生组合式继承 (最理想)](../../font-end/primitive/es/es-proto.md#寄生组合式继承%20(最理想))

### 三个小问题

在 MDN 中文版讲 bind 的模拟实现时，apply 这里的代码是：

```js
  self.apply(this instanceof self ? this : context || this, args.concat(bindArgs))
```

多了一个关于 context 是否存在的判断，然而这个是错误的！

举个例子：

```js
  var value = 2;
  var foo = {
      value: 1,
      bar: bar.bind(null)
  };
  
  function bar() {
      console.log(this.value);
  }
  
  foo.bar() // 2
```

以上代码正常情况下会打印 2，如果换成了 context || this，这段代码就会打印 1！

所以这里不应该进行 context 的判断，大家查看 MDN 同样内容的英文版，就不存在这个判断！

调用 bind 的不是函数咋办？

```js
  if (typeof this !== "function") {
    throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
  }
```

### 最终代码

```js
  Function.prototype.bind2 = function (context) {
  
      if (typeof this !== "function") {
        throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
      }
  
      var self = this;
      var args = Array.prototype.slice.call(arguments, 1);
  
      var fNOP = function () {};
  
      var fBound = function (...bindArgs) {
          return self.apply(this instanceof fNOP ? this : context, args.concat(bindArgs));
      }
  
      fNOP.prototype = this.prototype;
      fBound.prototype = new fNOP();
      return fBound;
  }
```

## New()

new 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象类型之一

先来看一个例子

```js
  // Otaku 御宅族，简称宅
  function Otaku (name, age) {
      this.name = name;
      this.age = age;
  
      this.habit = 'Games';
  }
  
  // 因为缺乏锻炼的缘故，身体强度让人担忧
  Otaku.prototype.strength = 60;
  
  Otaku.prototype.sayYourName = function () {
      console.log('I am ' + this.name);
  }
  
  var person = new Otaku('Kevin', '18');
  
  console.log(person.name) // Kevin
  console.log(person.habit) // Games
  console.log(person.strength) // 60
  
  person.sayYourName(); // I am Kevin
  ```

从这个例子中，我们可以看到，实例 person 可以：

- 访问到 Otaku 构造函数里的属性
- 访问到 Otaku.prototype 中的属性

因为 new 是关键字，所以无法像 bind 函数一样直接覆盖，所以我们写一个函数，命名为 objectFactory，来模拟 new 的效果。用的时候是这样的：

```js
  function Otaku () {
      ……
  }
  
  // 使用 new
  var person = new Otaku(……);
  // 使用 objectFactory
  var person = objectFactory(Otaku, ……)
```

### 初步实现

因为 new 的结果是一个新对象，所以在模拟实现的时候，我们也要建立一个新对象，假设这个对象叫 obj，因为 obj 会具有 Otaku **构造函数里的属性**，想想经典继承的例子，我们可以使用 Otaku.apply(obj, arguments) 来给 obj 添加新的属性。

在 JavaScript 深入系列第一篇中，我们便讲了原型与原型链，我们知道实例的 `__proto__ ` 属性会指向构造函数的 prototype，也正是因为建立起这样的关系，实例可以访问原型上的属性。

```js
  // 第一版代码
  function objectFactory(Constructor,...args) {
      var obj = new Object(),
      obj.__proto__ = Constructor.prototype;
      Constructor.apply(obj, args)
      return obj;
  };
```

  1. 用 new Object() 的方式新建了一个对象 obj
  2. 取出第一个参数，就是我们要传入的构造函数。此外因为 shift 会修改原数组，所以 arguments 会被去除第一个参数
  3. 将 obj 的原型指向构造函数的原型，这样 obj 就可以访问到构造函数原型中的属性
  4. 使用 apply，改变构造函数 this 的指向到新建的对象，这样 obj 就可以访问到构造函数中的属性，**其实是最传统的工厂模式**
  5. 返回 obj

### 返回值效果实现

接下来我们再来看一种情况，假如构造函数有返回值，举个例子：

```js
  function Otaku (name, age) {
      this.strength = 60;
      this.age = age;
  
      return {
          name: name,
          habit: 'Games'
      }
  }
  
  var person = new Otaku('Kevin', '18');
  
  console.log(person.name) // Kevin
  console.log(person.habit) // Games
  console.log(person.strength) // undefined
  console.log(person.age) // undefined
```

在这个例子中，构造函数返回了一个对象，在实例 person 中只能访问返回的对象中的属性。而且还要注意一点，在这里我们是返回了一个对象，假如我们只是返回一个基本类型的值呢？

```js
  function Otaku (name, age) {
      this.strength = 60;
      this.age = age;
  
      return 'handsome boy';
  }
  
  var person = new Otaku('Kevin', '18');
  
  console.log(person.name) // undefined
  console.log(person.habit) // undefined
  console.log(person.strength) // 60
  console.log(person.age) // 18
```

结果完全颠倒过来，这次尽管有返回值，但是相当于没有返回值进行处理。

所以我们还需要判断返回的值是不是一个对象，如果是一个对象，我们就返回这个对象，如果没有，我们该返回什么就返回什么。

再来看第二版的代码，也是最后一版的代码：

```js
  // 第二版的代码
  function objectFactory(Constructor,...args) {
      // var obj = new Object()
      // obj.__proto__ = Constructor.prototype;
      var obj = Object.create(Constructor.prototype)
      var ret = Constructor.apply(obj, arguments);
  		// obj 已经经过工厂模式的修改
      return typeof ret === 'object' ? ret : obj;
  };
```

### 面试题

new 操作符默认返回 this，而不是 undefined

```js
  function A(){}
  A.prototype.a = 1
  console.log(new A().a)
```

## Promise/A+

https://github.com/CavsZhouyou/Front-End-Interview-Notebook/blob/master/JavaScript/JavaScript.md#136-%E4%BB%80%E4%B9%88%E6%98%AF-promise-%E5%AF%B9%E8%B1%A1%E4%BB%80%E4%B9%88%E6%98%AF-promisesa-%E8%A7%84%E8%8C%83

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

# Dom 相关问题

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

![image-20201010114831134](/img/user/programming/FAQ/js-faq/!js-faq/image-20201010114831134.png)

现在超链接长按没有任何反应是否符合预期？符合预期

![image-20201010112619797](/img/user/programming/FAQ/js-faq/!js-faq/image-20201010112619797.png)

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
