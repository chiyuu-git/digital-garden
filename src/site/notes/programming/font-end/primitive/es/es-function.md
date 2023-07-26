---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/es/es-function/"}
---


# 基本

函数也是一个 **对象**，也具有普通对象的功能

函数中可以封装一些代码，在需要的时候可以去调用函数来执行这些代码

使用 typeof 检查一个函数时会返回 **function**，与普通 Object 不同

## 函数的声明

### Function 关键字

```javascript
function 函数名([形参1,形参2...形参N]){
  语句...
} //不用分号结尾
```

### 函数表达式

```javascript
var 函数名 = function([形参1,形参2...形参N]){
  语句…
}；//实质：把匿名函数复制给一个变量，所以赋值语句分号结尾
```

### Function 构造函数

```js
var fun = new Function(arg1,arg2...,functionBodyStr);
```

<https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function>

## 函数调用

fun() sum() alert() Number() parseInt()

当我们调用函数时，函数中封装的代码会按照编写的顺序执行

## 形参和实参

### 形参：形式参数

定义函数时，可以在 () 中定义一个或多个形参，形参之间使用 `,` 隔开定义形参就相当于在函数内声明了对应的变量但是并不赋值，形参会在调用时才赋值。

函数的形参，也可以理解为在函数作用域内，使用 let 声明一个局部变量，所以也不允许在函数作用域内，重复声明与函数形参同名的变量。

### 实参：实际参数

调用函数时, 可以在 () 传递实参, 传递的实参会赋值给对应的形参

调用函数时 JS 解析器不会检查实参的类型和个数, 可以传递任意数据类型的值

如果实参的数量大于形参，多余实参将不会赋值

如果实参的数量小于形参，则没有对应实参的形参将会赋值 undefined

```js
var execFunc = function(){
  console.log("executed");
};
setTimeout(execFunc,0);
console.log("changed");
execFunc = function(){
  console.log("another executed");
}
```

### Arguments Vs Params

params 是函数定义时的参数名, 也就是形参

argument 是实际传递进去的值, 也就是实参

```js
function test (str) {

}
```

## 函数的返回值

返回值，就是函数执行的结果。使用 return 来设置函数的返回值。

语法：return 值

该值就会成为函数的返回值，可以通过一个变量来接收函数的返回值

return 后边的代码都不会执行，一旦执行到 return 语句时，函数将会立刻退出，不光是退出循环

return 后可以跟任意类型的值，可以是基本数据类型，也可以是一个对象。

当需要传递的参数太多的时候，可以考虑封装到一个对象中

如果 return 后不跟值，或者是不写 return 则函数默认返回 undefined。

```javascript
  function(sayHello);// function 函数对象
  function(sayHello()); // function()调用函数,相当于使用函数的返回值
  
  function fun3(){
    function fun4(){
      alert(“我是fun4”);
      }
    return fun4;
  }
  a = fun3()
  a() //fun3()() → fun4()
  (functoin(a,b){
    alert(“我是一个匿名函数”);
  console.log(a);
  console.log(b);
  })(a,b)； 
  //匿名函数用()包着，表示是一个整体，相当于省略了var的部分，然后在一个()立即调用，就可以创造一个一次性的立即执行的函数而不同特地开辟内存空间去保存它
```

## 函数作为对象的方法

可以将一个函数设置为一个对象的属性，当一个对象的属性是一个函数时我们称这个函数是该对象的方法。

obj.method = function(){}

## Function 类的实例方法

### call() 和 apply()

这两个方法都是 **函数对象的方法** 需要通过函数对象来调用

通过两个方法可以直接调用函数（执行函数），并且可以通过第一个实参来指定函数中 this

**本质:** 让该临时函数成为实参的方法

```javascript
  fun(){alert(this);}//window
  fun(obj){alert(this);}//obj
```

这两个方法，让函数可以在更多的场合应用 this

```javascript
  obj.sayName.call(obj2);//obj2
  //call()方法可以将实参在对象之后一起传递
  fun.call(obj,2,2);
  //不同的是call是直接传递函数的实参而apply需要将实参封装到一个数组中传递
  fun.apply(obj,[2,3]);
```

#### 优先级

```js
  const obj = {
    name:'a'
  }
  
  const obj2 = {
    name:'b'
  }
  
  function sayName(){
    console.log(this.name)
  }
  
  sayName.bind(obj).call(obj2) // a
```

### Arguments

arguments 和 this 类似，都是函数中的隐含的参数

arguments 是一个 **类数组对象**，它用来封装函数执行过程中的实参

不是数组，但是具有下标索引和 length 属性，没有 foreach 方法

  ```javascript
  arguments instanceof Array //false
  Array.isArray(arguments); //false
  ```

arguments.length; //可以用来获取 **实参** 的数量（长度），不受形参的影响

也可以用其他方法，来操作实参，所以即使不定义形参，也可以通过 arguments 来使用实参

### Callee

arguments 中有一个属性 callee 表示当前执行的函数对象

arguments.callee == fun;//true

# 作用域

作用域简单来说就是一个变量的作用范围。

## 全局作用域

接在 script 标签中编写的代码都运行在全局作用域中

生命周期: 全局作用域在打开页面时创建，在页面关闭时销毁。

全局作用域中有一个全局对象 window，window 对象由浏览器提供，可以在页面中直接使用，它代表的是整个的浏览器的窗口。

在全局作用域中创建的变量都会作为 window 对象的属性保存，在全局作用域中创建的函数都会作为 window 对象的方法保存

```javascript
  console.log(window.b); //b未定义，找一个属性未定义会返回undefined
  console.log(b);//一个变量未定义，则会报错
```

在全局作用域中创建的变量和函数可以在页面的任意位置访问。在函数作用域中也可以访问到全局作用域的变量。

我们创建的函数，其实就是 window 的方法，所以函数和方法没有本质的区别，就是名字不同而已

尽量不要在全局中创建变量

## 函数作用域

函数作用域是函数执行时创建的作用域，每次调用函数都会创建一个新的函数作用域。

生命周期：函数作用域在函数执行时创建，在函数执行结束时销毁。

在函数作用域中创建的变量，不能在全局中访问。

当在函数作用域中使用一个变量时，它会先在自身作用域中寻找，如果找到了则直接使用，如果没有找到则到上一级作用域中寻找，如果找到了则使用，找不到则继续向上找，一直会

```javascript
  var e=20;
  function fun(){
    alert(e);
  } //20;
  function fun2(e){
    alert(e);  //形参相当于在函数内部var e；如果不传递参数就直接找到了e
  }//undefined 
```

## 变量的声明提前

在 **全局作用域中**，使用 var 关键字声明的变量会在所有的代码执行之前被声明，**但是不会赋值**。

所以我们可以在变量声明前使用变量。但是 **不使用 var** 关键字声明的变量不会被声明提前。

在 **函数作用域中**，也具有该特性，使用 var 关键字声明的变量会在 **函数** 所有的代码执行前被声明，

如果没有使用 var 关键字声明变量，则变量会变成全局变量

  ```javascript
  a = 123； //不用var关键字，相当于是window.a
  ```

所以在函数中不使用 var 给已存在的变量赋值，会影响到全局变量的值

  ```javascript
  //eg1
  c=10；
  function fun(){
    c=20;  //(window.)c=20;
  }
  fun();
  console.log(c); //c=20
  //eg2
  c=10；
  function fun(){
    var c=20;  //全局中的c不受影响
  }
  fun();
  console.log(c); //c=10
  ```

## 函数的声明提前

在 **全局作用域中**，使用 **函数声明** 创建的函数 ``function fun(){}``,会在所有的代码执行之前被创建，也就是我们 **可以在函数声明前去调用函数。**

但是使用函数表达式 ``var fun = function(){}`` 创建的函数没有该特性

var 关键字，其实是 variety，就是变量的意思，和 function 是类似的

提前使用函数也是没问题的，因为整个函数都被创建好了，不光是声明了函数

  ```javascript
  fun();
  function fun(){ };//没问题
  ```

在 **函数作用域中**，使用函数声明创建的函数，会在所有的函数中的代码执行之前就被创建好了。

# 函数重载

```js
function method(obj,name,fnc){
    var old = obj[name];
    console.log(old instanceof Function);
    obj[name] = function(){
        console.log(arguments.length+" "+fnc.length);
        if(arguments.length === fnc.length){
            return fnc.apply(this,arguments);
        }else if(typeof old === "function"){
            return old.apply(this,arguments);
        }
    }
}
var people = {
    values:["Zhang san","Li si"]
};
method(people,"find",function(){
    console.log("无参数");
    return this.values;
})
method(people,"find",function(firstname){
    console.log("一个参数");
    var ret = [];
    for(var i = 0;i < this.values.length;i++){
        if(this.values[i].indexOf(firstname) === 0){
            ret.push(this.values[i])
        }
    }
    return ret;
})
method(people,"find",function(firstname,lastname){
    console.log("两个参数");
    var ret = [];
    for(var i = 0;i < this.values.length;i++){
        if(this.values[i] == firstname + " " + lastname){
            ret.push(this.values[i])
        }
    }
    return ret;
})
console.log(people.find());
console.log(people.find("Zhang"));
```

## 函数重载

### 根据 Arguments 的长度执行不同的逻辑

```js
function overLoading() {
  // 根据arguments.length，对不同的值进行不同的操作
  switch(arguments.length) {
    case 0:
      /*操作1的代码写在这里*/
      break;
    case 1:
      /*操作2的代码写在这里*/
      break;
    case 2:
      /*操作3的代码写在这里*/

      //后面还有很多的case......
  }
}
```

### 利用闭包

```js
function addMethod(object, name, fn) {
  var old = object[name]; //把前一次添加的方法存在一个临时变量old里面
  object[name] = function() { // 重写了object[name]的方法
    // 如果调用object[name]方法时，传入的参数个数跟预期的一致，则直接调用
    if(fn.length === arguments.length) {
      return fn.apply(this, arguments);
      // 否则，判断old是否是函数，如果是，就调用old
    } else if(typeof old === "function") {
      return old.apply(this, arguments);
    }
  }
}
```

函数不断重载的时候，会不断的嵌套，一条分支是当前的逻辑，`old` 分支包含之前所有的函数的逻辑

# ES6 扩展

## 函数参数的默认值

### 基本用法

```javascript
function log(x, y = 'World') {
  console.log(x, y);
}

log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', '') // Hello

function log(x) {
  var y = arguments.length > 1 && arguments[1] !== undefined ? arguments[1] : 'World';

  console.log(x, y);
}
```

如果传入 `undefined`，将触发该参数等于默认值，`null` 则没有这个效果。

```javascript
function foo(x = 5, y = 6) {
  console.log(x, y);
}

foo(undefined, null)
// 5 null
```

上面代码中，`x` 参数对应 `undefined`，结果触发了默认值，`y` 参数等于 `null`，就没有触发默认值。

### 参数默认值的位置

通常情况下，定义了默认值的参数，应该是 **函数的尾参数**。因为这样比较容易看出来，到底省略了哪些参数。如果非尾部的参数设置默认值，实际上这个参数是没法省略的。这时，无法只省略该参数，而不省略它后面的参数，除非显式输入 `undefined`。

```javascript
// 例一
function f(x = 1, y) {
  return [x, y];
}

f() // [1, undefined]
f(2) // [2, undefined])
f(, 1) // 报错
f(undefined, 1) // [1, 1]

// 例二
function f(x, y = 5, z) {
  return [x, y, z];
}

f() // [undefined, 5, undefined]
f(1) // [1, 5, undefined]
f(1, ,2) // 报错
f(1, undefined, 2) // [1, 5, 2]
```

### 函数的 Length 属性

指定了默认值以后，函数的 `length` 属性，将返回没有指定默认值的参数个数。也就是说，指定了默认值后，`length` 属性将失真。

```javascript
(function (a) {}).length // 1
(function (a = 5) {}).length // 0
(function (a, b, c = 5) {}).length // 2
```

这是因为 `length` 属性的含义是，该函数 **预期传入** 的参数个数。某个参数指定默认值以后，预期传入的参数个数就不包括这个参数了。同理，后文的 rest 参数也不会计入 `length` 属性。

```javascript
(function(...args) {}).length // 0
```

如果设置了默认值的参数不是尾参数，那么 `length` 属性也不再计入后面的参数了。

```javascript
(function (a = 0, b, c) {}).length // 0
(function (a, b = 1, c) {}).length // 1
```

### 作用域

一旦设置了参数的默认值，函数进行声明初始化时，参数会形成一个单独的作用域（context）。等到初始化结束，这个作用域就会消失。这种语法行为，在不设置参数默认值时，是不会出现的。

```javascript
var x = 1;

function f(x, y = x) {
  console.log(y);
}

f(2) // 2
```

上面代码中，参数 `y` 的默认值等于变量 `x`。调用函数 `f` 时，参数形成一个单独的作用域。在这个作用域里面，默认值变量 `x` 指向第一个参数 `x`，而不是全局变量 `x`，所以输出是 `2`。

### 注意

参数变量是 **默认声明** 的，所以不能用 `let` 或 `const` 再次声明。

```javascript
function foo(x = 5) {
  let x = 1; // error
  const x = 2; // error
}
```

使用参数默认值时，函数不能有 **同名参数。**

```javascript
// 不报错
function foo(x, x, y) {
  // ...
}

// 报错
function foo(x, x, y = 1) {
  // ...
}
// SyntaxError: Duplicate parameter name not allowed in this context
```

另外，一个容易忽略的地方是，参数默认值不是传值的，而是每次都重新计算默认值表达式的值。也就是说，参数默认值是 **惰性求值** 的。

```javascript
let x = 99;
function foo(p = x + 1) {
  console.log(p);
}

foo() // 100

x = 100;
foo() // 101
```

上面代码中，参数 `p` 的默认值是 `x + 1`。这时，每次调用函数 `foo`，都会重新计算 `x + 1`，而不是默认 `p` 等于 100。

### 应用

利用参数默认值，可以指定某一个参数不得省略，如果省略就抛出一个错误。

```javascript
function throwIfMissing() {
  throw new Error('Missing parameter');
}

function foo(mustBeProvided = throwIfMissing()) {
  return mustBeProvided;
}

foo()
// Error: Missing parameter
```

上面代码的 `foo` 函数，如果调用的时候没有参数，就会调用默认值 `throwIfMissing` 函数，从而抛出一个错误。

从上面代码还可以看到，参数 `mustBeProvided` 的默认值等于 `throwIfMissing` 函数的运行结果（注意函数名 `throwIfMissing` 之后有一对圆括号），这表明参数的默认值不是在定义时执行，而是在运行时执行。如果参数已经赋值，默认值中的函数就不会运行。

另外，可以将参数默认值设为 `undefined`，表明这个参数是可以省略的。

```javascript
function foo(optional = undefined) { ··· }
```

### 与解构赋值默认值结合使用

参数默认值可以与解构赋值的默认值，结合起来使用。

```js
function foo({x, y = 5}) {
  console.log(x, y);
}

foo({}) // undefined 5
foo({x: 1}) // 1 5
foo({x: 1, y: 2}) // 1 2
foo() // TypeError: Cannot read property 'x' of undefined
```

上面代码只使用了对象的解构赋值默认值，没有使用函数参数的默认值。只有当函数 `foo` 的参数是一个对象时，变量 `x` 和 `y` 才会通过解构赋值生成。如果函数 `foo` 调用时没提供参数，变量 `x` 和 `y` 就不会生成，从而报错。通过提供函数参数的默认值，就可以避免这种情况。

```javascript
function foo({x, y = 5} = {}) {
  console.log(x, y);
}

foo() // undefined 5

function foo (x,y,{a=1,b=1}={},){
  console.log(x,y,a,b)
}
foo(1,2,{a:2,b:3}) // 1 2 2 3
```

### 默认配置对象

遍历替换

```js
  for(var key in obj){
    defaults[key]=obj[key]
  }
```

使用 object.assign

```js
let defaults = {
  x:true,
  y:true,
  overflow:true,
}
options = Object.assign(defaults,options)
```

![es-function](es-function.md#与解构赋值默认值结合使用)

## Rest 参数

ES6 引入 rest 参数（形式为 `...变量名`），用于获取函数的多余参数，这样就不需要使用 `arguments` 对象了。rest 参数搭配的变量是 **一个数组**，该变量将多余的参数放入数组中。

```javascript
  function add(...values) {
    let sum = 0;
  
    for (var val of values) {
      sum += val;
    }
  
    return sum;
  }
  
  add(2, 5, 3) // 10
```

上面代码的 `add` 函数是一个求和函数，利用 rest 参数，可以向该函数传入任意数目的参数。

### 代替 Arguments

下面是一个 rest 参数代替 `arguments` 变量的例子。

```javascript
  // arguments变量的写法
  function sortNumbers() {
    return Array.prototype.slice.call(arguments).sort();
    // return [...arguments].sort()
  }
  
  // rest参数的写法
  const sortNumbers = (...numbers) => numbers.sort();
  
  var sortNumbers = function sortNumbers() {
    for (var _len = arguments.length, numbers = Array(_len), _key = 0; _key < _len; _key++) {
      numbers[_key] = arguments[_key];
    }
  
    return numbers.sort();
  };
  ```

`arguments` 对象不是数组，而是一个类似数组的对象。所以为了使用数组的方法，必须使用 `Array.prototype.slice.call` 先将其转为数组。rest 参数就不存在这个问题，它就是一个真正的数组，数组特有的方法都可以使用。

更推荐使用 `rest` 参数，由于严格模式的限制，

### 注意

注意，rest 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错。

  ```javascript
  // 报错
  function f(a, ...b, c) {
    // ...
  }
  ```

函数的 `length` 属性，不包括 rest 参数。

```javascript
  (function(a) {}).length  // 1
  (function(...a) {}).length  // 0
  (function(a, ...b) {}).length  // 1
```

使用了 rest 参数相当于给一个同名变量一个空数组默认值

```js
  function foo(...arr){return Object.prototype.toString.call(arr)}
  console.log(foo()) // array
```

### 运用示例

下面是一个利用 rest 参数改写数组 `push` 方法的例子，使得 `push` 方法支持多个参数

  ```javascript
    function push(array, ...items) {
      items.forEach(function(item) {
        array.push(item);
        console.log(item);
      });
    }
  
    var a = [];
    push(a, 1, 2, 3)
  ```

### 严格模式

从 ES5 开始，函数内部可以设定为严格模式。

```javascript
  function doSomething(a, b) {
    'use strict';
    // code
  }
```

ES2016 做了一点修改，规定只要函数参数使用了默认值、解构赋值、或者扩展运算符，那么函数内部就不能显式设定为严格模式，否则会报错。

  ```javascript
  // 报错
  function doSomething(a, b = a) {
    'use strict';
    // code
  }
  
  // 报错
  const doSomething = function ({a, b}) {
    'use strict';
    // code
  };
  
  // 报错
  const doSomething = (...a) => {
    'use strict';
    // code
  };
  
  const obj = {
    // 报错
    doSomething({a, b}) {
      'use strict';
      // code
    }
  };
  ```

这样规定的原因是，函数内部的严格模式，同时适用于函数体和函数参数。但是，函数执行的时候，先执行函数参数，然后再执行函数体。这样就有一个不合理的地方，只有从函数体之中，才能知道参数是否应该以严格模式执行，但是参数却应该先于函数体执行。

```javascript
  // 报错
  function doSomething(value = 070) {
    'use strict';
    return value;
  }
```

两种方法可以规避这种限制。第一种是设定全局性的严格模式，这是合法的。

  ```javascript
  'use strict';
  
  function doSomething(a, b = a) {
    // code
  }
  ```

第二种是把函数包在一个无参数的立即执行函数里面。

  ```javascript
  const doSomething = (function () {
    'use strict';
    return function(value = 42) {
      return value;
    };
  }());
  ```

## Name 属性

### Function 关键字

函数的 `name` 属性，返回该函数的函数名。

```javascript
  function foo() {}
  foo.name // "foo"
```

这个属性早就被浏览器广泛支持，但是直到 ES6，才将其写入了标准。

### 匿名函数赋值

需要注意的是，ES6 对这个属性的行为做出了一些修改。如果将一个 **匿名函数赋值** 给一个变量，ES5 的 `name` 属性，会返回空字符串，而 ES6 的 `name` 属性会返回实际的函数名。

```javascript
  var f = function () {};
  
  // ES5
  f.name // ""
  
  // ES6
  f.name // "f"
```

### 具名函数赋值

- 如果将一个 **具名函数赋值** 给一个变量，则 ES5 和 ES6 的 `name` 属性都返回这个具名函数原本的名字。

```javascript
  const bar = function baz() {};
  
  // ES5
  bar.name // "baz"
  
  // ES6
  bar.name // "baz"
```

### Function()

`Function` 构造函数返回的函数实例，`name` 属性的值为 `anonymous`。

  ```javascript
  (new Function).name // "anonymous"
  ```

`bind` 返回的函数，`name` 属性值会加上 `bound` 前缀。

```javascript
  function foo() {};
  foo.bind({}).name // "bound foo"
  
  (function(){}).bind({}).name // "bound "
```

### bind()

`bind` 方法创造的函数，`name` 属性返回 `bound` 加上原函数的名字；

```js
    var doSomething = function() {
      // ...
    };
    doSomething.bind().name // "bound doSomething"
```

## 箭头函数

### 基本用法

ES6 允许使用“箭头”（`\=>`）定义函数。

```javascript
  var f = v => v;
  
  // 等同于
  var f = function (v) {
    return v;
  };
```

#### 只有一个参数

箭头函数只有一个参数，可以省略圆括号，直接填入变量

```js
  var f = v => v;
  // 等同于
  var f = function (v) {
    return v;
  };
```

这种写法的好处是，在处理函数柯里化的时候可以做到非常的简洁

#### 不需要参数或需要多个参数

箭头函数 **不需要参数或需要多个参数**，就使用一个 **圆括号** 代表参数部分。

```javascript
  var f = () => 5;
  // 等同于
  var f = function () { return 5 };
  
  var sum = (num1, num2) => num1 + num2;
  // 等同于
  var sum = function(num1, num2) {
    return num1 + num2;
  };
```

#### 代码块部分只有一行语句

如果箭头函数只有一行语句，可以采用下面的写法，就不用写大括号了

```js
  var f = v => v;
  
  // 等同于
  var f = function (v) {
    return v;
  };
```

在只有一行语句的时候，箭头有 `return` 的作用

如果箭头函数只有一行语句，且不需要返回值，可以使用 `void` 关键字

```javascript
  let fn = () => void doesNotReturn();
```

**直接返回一个对象**

由于大括号被解释为代码块，所以如果箭头函数直接返回一个对象，必须在对象外面加上括号，否则会报错。

  ```javascript
  // 报错
  let getTempItem = id => { id: id, name: "Temp" };
  
  // 不报错
  let getTempItem = id => ({ id: id, name: "Temp" });
  ```

#### 代码块部分多于一条语句

如果箭头函数的 **代码块部分多于一条语句**，就要使用大括号将它们括起来，并且使用 `return` 语句返回。

  ```javascript
  var sum = (num1, num2) => { return num1 + num2; }
  ```

### 运用示例

箭头函数的一个用处是简化回调函数。

  ```javascript
  // 正常函数写法
  [1,2,3].map(function (x) {
    return x * x;
  });
  
  // 箭头函数写法
  [1,2,3].map(x => x * x);
  ```

下面是 rest 参数与箭头函数结合的例子。

```javascript
  const numbers = (...nums) => nums;
  
  numbers(1, 2, 3, 4, 5)
  // [1,2,3,4,5]
  
  const headAndTail = (head, ...tail) => [head, tail];
  
  headAndTail(1, 2, 3, 4, 5)
  // [1,[2,3,4,5]]
```

### 使用注意点 @@@ #

#### This 相关

函数体内的 `this` 对象，就是定义时所在的对象，而不是使用时所在的对象。

上面四点中，第一点尤其值得注意。`this` 对象的指向是可变的，但是在箭头函数中，它是固定的。

```javascript
  function foo() {
    setTimeout(() => {
      console.log('id:', this.id);
    }, 100);
  }
  
  var id = 21;
  
  foo.call({ id: 42 });
  // id: 42
```

上面代码中，`setTimeout` 的参数是一个箭头函数，这个箭头函数的定义生效是在 `foo` 函数生成时，而它的真正执行要等到 100 毫秒后。如果是普通函数，执行时 `this` 应该指向全局对象 `window`，这时应该输出 `21`。但是，箭头函数导致 `this` 总是指向函数定义生效时所在的对象（本例是 `{id: 42}`），所以输出的是 `42`。

`this` 指向的固定化，并不是因为箭头函数内部有绑定 `this` 的机制，实际原因是箭头函数根本没有自己的 `this`，导致内部的 `this` 就是外层代码块的 `this`。正是因为它没有 `this`，所以也就不能用作构造函数。

  > 在 call 和 apply 的实现中，我们通过让函数临时成为对象的方法来绑定 this，这种行为称为绑定，在箭头函数中显然不是这样做的，而是直接引用了外层的 this @@@

所以，箭头函数转成 ES5 的代码如下。

  ```javascript
  // ES6
  function foo() {
    setTimeout(() => {
      console.log('id:', this.id);
    }, 100);
  }
  
  // ES5
  function foo() {
    var _this = this;
  
    setTimeout(function () {
      console.log('id:', _this.id);
    }, 100);
  }
  ```

上面代码中，转换后的 ES5 版本清楚地说明了，箭头函数里面根本没有自己的 `this`，而是引用外层的 `this`。

  ```javascript
  function foo() {
    return () => {
      return () => {
        return () => {
          console.log('id:', this.id);
        };
      };
    };
  }
  
  var f = foo.call({id: 1});
  
  var t1 = f.call({id: 2})()(); // id: 1
  var t2 = f().call({id: 3})(); // id: 1
  var t3 = f()().call({id: 4}); // id: 1
  ```

上面代码之中，只有一个 `this`，就是函数 `foo` 的 `this`，所以 `t1`、`t2`、`t3` 都输出同样的结果。因为所有的内层函数都是箭头函数，都没有自己的 `this`，它们的 `this` 其实都是最外层 `foo` 函数的 `this`。

另外，由于箭头函数没有自己的 `this`，所以当然也就不能用 `call()`、`apply()`、`bind()` 这些方法去改变 `this` 的指向。

  ```javascript
  (function() {
    return (() => this.x).bind({ x: 'inner' })()
  }).call({ x: 'outer' })
  // 'outer'
  ```

上面代码中，箭头函数没有自己的 `this`，所以 `bind` 方法无效，内部的 `this` 指向外部的 `this`。

长期以来，JavaScript 语言的 `this` 对象一直是一个令人头痛的问题，在对象方法中使用 `this`，必须非常小心。箭头函数”绑定”`this`，很大程度上解决了这个困扰。

#### 其他

不可以当作构造函数，也就是说，不可以使用 `new` 命令，否则会抛出一个错误。

不可以使用 `arguments` 对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。

除了 `this`，以下 **三个变量** 在箭头函数之中也是 **不存在的**，指向外层函数的对应变量：`arguments`、`super`、`new.target`。

  ```javascript
  function foo() {
    setTimeout(() => {
      console.log('args:', arguments);
    }, 100);
  }
  
  foo(2, 4, 6, 8)
  // args: [2, 4, 6, 8]
  ```

上面代码中，箭头函数内部的变量 `arguments`，其实是函数 `foo` 的 `arguments` 变量。

不可以使用 `yield` 命令，因此箭头函数不能用作 Generator 函数。

没有 super。连原型都没有，自然也不能通过 super 来访问原型的属性，所以箭头函数也是没有 super 的，不过跟 this、arguments、new.target 一样，这些值由外围最近一层非箭头函数决定。

### 不适用场合

由于箭头函数使得 `this` 从“动态”变成“静态”，下面两个场合不应该使用箭头函数。

第一个场合是定义函数的方法，且该方法内部包括 `this`。

  ```javascript
  const cat = {
    lives: 9,
    jumps: () => {
      this.lives--;
    }
  }
  ```

第二个场合是需要动态 `this` 的时候，也不应使用箭头函数。

  ```javascript
  var button = document.getElementById('press');
  button.addEventListener('click', () => {
    this.classList.toggle('on');
  });
  ```

上面代码运行时，点击按钮会报错，因为 `button` 的监听函数是一个箭头函数，导致里面的 `this` 就是全局对象。如果改成普通函数，`this` 就会动态指向被点击的按钮对象。

另外，如果函数体很复杂，有许多行，或者函数内部有大量的读写操作，不单纯是为了计算值，这时也不应该使用箭头函数，而是要使用普通函数，这样可以提高代码可读性。

### 箭头函数的嵌套

箭头函数内部，还可以再使用箭头函数。下面是一个 ES5 语法的多重嵌套函数。

```javascript
  function insert(value) {
    return {into: function (array) {
      return {after: function (afterValue) {
        array.splice(array.indexOf(afterValue) + 1, 0, value);
        return array;
      }};
    }};
  }
  
  insert(2).into([1, 3]).after(1); //[1, 2, 3]
```

上面这个函数，可以使用箭头函数改写。

```javascript
  let insert = value => ({into: (array) => ({after: (afterValue) => {
    array.splice(array.indexOf(afterValue) + 1, 0, value);
    return array;
  }})});
  
  insert(2).into([1, 3]).after(1); //[1, 2, 3]
```

### 嵌套箭头函数的运用

下面是一个 **部署管道机制**（pipeline）的例子，即前一个函数的输出是后一个函数的输入。

  ```javascript
  const pipeline = (...funcs) =>
    val => funcs.reduce((a, b) => b(a), val);
  
  const plus1 = a => a + 1;
  const mult2 = a => a * 2;
  const addThenMult = pipeline(plus1, mult2);
  
  addThenMult(5)
  // 12
  ```

如果觉得上面的写法可读性比较差，也可以采用下面的写法。

  ```javascript
  const plus1 = a => a + 1;
  const mult2 = a => a * 2;
  
  mult2(plus1(5))
  // 12
  ```

箭头函数还有一个功能，就是可以很方便地改写 λ 演算。

  ```javascript
  // λ演算的写法
  fix = λf.(λx.f(λv.x(x)(v)))(λx.f(λv.x(x)(v)))
  
  // ES6的写法
  var fix = f => (x => f(v => x(x)(v)))
                 (x => f(v => x(x)(v)));
  ```

上面两种写法，几乎是一一对应的。由于 λ 演算对于计算机科学非常重要，这使得我们可以用 ES6 作为替代工具，探索计算机科学。

## 函数绑定”（function bind）运算符

箭头函数可以绑定 `this` 对象，大大减少了显式绑定 `this` 对象的写法（`call`、`apply`、`bind`）。但是，箭头函数并不适用于所有场合，所以现在有一个 [提案](https://github.com/zenparsing/es-function-bind)，提出了“函数绑定”（function bind）运算符，用来取代 `call`、`apply`、`bind` 调用，即是双冒号运算符

函数绑定运算符是并排的两个冒号（`::`），双冒号左边是一个对象，右边是一个函数。该运算符会自动将左边的对象，作为上下文环境（即 `this` 对象），绑定到右边的函数上面。

  ```javascript
  foo::bar;
  // 等同于
  bar.bind(foo);
  
  foo::bar(...arguments);
  // 等同于
  bar.apply(foo, arguments);
  
  const hasOwnProperty = Object.prototype.hasOwnProperty;
  function hasOwn(obj, key) {
    return obj::hasOwnProperty(key);
  }
  ```

如果双冒号左边为空，右边是一个对象的方法，则等于将该方法绑定在该对象上面。

  ```javascript
  var method = obj::obj.foo;
  // 等同于
  var method = ::obj.foo;
  
  let log = ::console.log;
  // 等同于
  var log = console.log.bind(console);
  ```

如果双冒号运算符的运算结果，还是一个对象，就可以采用链式写法。

  ```javascript
  import { map, takeWhile, forEach } from "iterlib";
  
  getPlayers()
  ::map(x => x.character())
  ::takeWhile(x => x.strength > 100)
  ::forEach(x => console.log(x));
  ```

## 尾调用优化

### 什么是尾调用？

尾调用（Tail Call）是函数式编程的一个重要概念，本身非常简单，一句话就能说清楚，就是指某个函数的最后一步是调用另一个函数。

```javascript
function f(x){
  return g(x);
}
```

上面代码中，函数 `f` 的最后一步是调用函数 `g`，这就叫尾调用。

以下三种情况，都不属于尾调用。

```javascript
// 情况一
function f(x){
  let y = g(x);
  return y;
}

// 情况二
function f(x){
  return g(x) + 1;
}

// 情况三
function f(x){
  g(x);
}
```

上面代码中，情况一是调用函数 `g` 之后，还有赋值操作，所以不属于尾调用，即使语义完全一样。情况二也属于调用后还有操作，即使写在一行内。情况三等同于下面的代码。

```javascript
function f(x){
  g(x);
  return undefined;
}
```

```javascript
function f(x) {
  if (x > 0) {
    return m(x)
  }
  return n(x);
}
```

上面代码中，函数 `m` 和 `n` 都属于尾调用，因为它们都是函数 `f` 的最后一步操作。

### 尾调用优化

尾调用之所以与其他调用不同，就在于它的特殊的调用位置。

我们知道，函数调用会在内存形成一个“调用记录”，又称“调用帧”（call frame），保存调用位置和内部变量等信息。如果在函数 `A` 的内部调用函数 `B`，那么在 `A` 的调用帧上方，还会形成一个 `B` 的调用帧。等到 `B` 运行结束，将结果返回到 `A`，`B` 的调用帧才会消失。如果函数 `B` 内部还调用函数 `C`，那就还有一个 `C` 的调用帧，以此类推。所有的调用帧，就形成一个“调用栈”（call stack）。

尾调用由于是函数的最后一步操作，所以不需要保留外层函数的调用帧，因为调用位置、内部变量等信息都不会再用到了，只要直接用内层函数的调用帧，取代外层函数的调用帧就可以了。

```javascript
function f() {
  let m = 1;
  let n = 2;
  return g(m + n);
}
f()

// 等同于
function f() {
  return g(3);
}
f();

// 等同于
g(3);
```

- 上面代码中，如果函数 `g` 不是尾调用，函数 `f` 就需要保存内部变量 `m` 和 `n` 的值、`g` 的调用位置等信息。但由于调用 `g` 之后，函数 `f` 就结束了，所以执行到最后一步，完全可以删除 `f(x)` 的调用帧，只保留 `g(3)` 的调用帧。
- 这就叫做“尾调用优化”（Tail call optimization），即只保留内层函数的调用帧。如果所有函数都是尾调用，那么完全可以做到每次执行时，调用帧只有一项，这将大大节省内存。这就是“尾调用优化”的意义。
- 注意，只有不再用到外层函数的内部变量，内层函数的调用帧才会取代外层函数的调用帧，否则就无法进行“尾调用优化”。

  ```javascript
  function addOne(a){
    var one = 1;
    function inner(b){
      return b + one;
    }
    return inner(a);
  }
  ```

- 上面的函数不会进行尾调用优化，因为内层函数 `inner` 用到了外层函数 `addOne` 的内部变量 `one`。

### 尾递归

函数调用自身，称为递归。如果尾调用自身，就称为尾递归。

递归非常耗费内存，因为需要同时保存成千上百个调用帧，很容易发生“栈溢出”错误（stack overflow）。但对于尾递归来说，由于只存在一个调用帧，所以永远不会发生“栈溢出”错误。

```javascript
function factorial(n) {
  if (n === 1) return 1;
  return n * factorial(n - 1);
}

factorial(5) // 120
```

上面代码是一个阶乘函数，计算 `n` 的阶乘，最多需要保存 `n` 个调用记录，复杂度 O(n) 。

如果改写成尾递归，只保留一个调用记录，复杂度 O(1) 。

```javascript
function factorial(n, total) {
  if (n === 1) return total;
  return factorial(n - 1, n * total);
}

factorial(5, 1) // 120
```

- 还有一个比较著名的例子，就是计算 Fibonacci 数列，也能充分说明尾递归优化的重要性。
- 非尾递归的 Fibonacci 数列实现如下。

  ```javascript
  function Fibonacci (n) {
    if ( n <= 1 ) {return 1};
  
    return Fibonacci(n - 1) + Fibonacci(n - 2);
  }
  
  Fibonacci(10) // 89
  Fibonacci(100) // 堆栈溢出
  Fibonacci(500) // 堆栈溢出
  ```

- 尾递归优化过的 Fibonacci 数列实现如下。

  ```javascript
  function Fibonacci2 (n , ac1 = 1 , ac2 = 1) {
    if( n <= 1 ) {return ac2};
  
    return Fibonacci2 (n - 1, ac2, ac1 + ac2);
  }
  
  Fibonacci2(100) // 573147844013817200000
  Fibonacci2(1000) // 7.0330367711422765e+208
  Fibonacci2(10000) // Infinity
  ```

- 由此可见，“尾调用优化”对递归操作意义重大，所以一些函数式编程语言将其写入了语言规格。ES6 亦是如此，第一次明确规定，所有 ECMAScript 的实现，都必须部署“尾调用优化”。这就是说，ES6 中只要使用尾递归，就不会发生栈溢出，相对节省内存。

### 递归函数的改写

- 尾递归的实现，往往需要改写递归函数，确保最后一步只调用自身。做到这一点的方法，就是把所有用到的内部变量改写成函数的参数。比如上面的例子，阶乘函数 factorial 需要用到一个中间变量 `total`，那就把这个中间变量改写成函数的参数。这样做的缺点就是不太直观，第一眼很难看出来，为什么计算 `5` 的阶乘，需要传入两个参数 `5` 和 `1`？
- 两个方法可以解决这个问题。方法一是在尾递归函数之外，再提供一个正常形式的函数。

  ```js
  function tailFactorial(n, total) {
    if (n === 1) return total;
    return tailFactorial(n - 1, n * total);
  }
  
  function factorial(n) {
    return tailFactorial(n, 1);
  }
  
  factorial(5) // 120
  ```

- 上面代码通过一个正常形式的阶乘函数 `factorial`，调用尾递归函数 `tailFactorial`，看起来就正常多了。
- 函数式编程有一个概念，叫做柯里化（currying），意思是将多参数的函数转换成单参数的形式。这里也可以使用柯里化。

  ```js
  function currying(fn, n) {
    return function (m) {
      return fn.call(this, m, n);
    };
  }
  
  function tailFactorial(n, total) {
    if (n === 1) return total;
    return tailFactorial(n - 1, n * total);
  }
  
  const factorial = currying(tailFactorial, 1);
  
  factorial(5) // 120
  ```

- 上面代码通过柯里化，将尾递归函数 `tailFactorial` 变为只接受一个参数的 `factorial`。
- 第二种方法就简单多了，就是采用 ES6 的函数默认值。

  ```js
  function factorial(n, total = 1) {
    if (n === 1) return total;
    return factorial(n - 1, n * total);
  }
  
  factorial(5) // 120
  ```

- 上面代码中，参数 `total` 有默认值 `1`，所以调用时不用提供这个值。
- 总结一下，递归本质上是一种循环操作。纯粹的函数式编程语言没有循环操作命令，所有的循环都用递归实现，这就是为什么尾递归对这些语言极其重要。对于其他支持“尾调用优化”的语言（比如 Lua，ES6），只需要知道循环可以用递归代替，而一旦使用递归，就最好使用尾递归

### 严格模式

- ES6 的尾调用优化只在严格模式下开启，正常模式是无效的。
- 这是因为在正常模式下，函数内部有两个变量，可以跟踪函数的调用栈。
  - `func.arguments`：返回调用时函数的参数。
  - `func.caller`：返回调用当前函数的那个函数。
- 尾调用优化发生时，函数的调用栈会改写，因此上面两个变量就会失真。严格模式禁用这两个变量，所以尾调用模式仅在严格模式下生效。

  ```js
  function restricted() {
    'use strict'
    restricted.caller;    // 报错
    restricted.arguments; // 报错
  }
  restricted();
  ```

### 理论意义

- Proper tail calls have been implemented but not yet shipped given that a change to the feature is currently under discussion at TC39.意思就是人家已经做好了，但是就是还不能不给你用：）嗨呀，好气喔。
- 当然，人家肯定是有他的正当理由的：
  - 在引擎层面消除尾递归是一个隐式的行为，程序员写代码时可能意识不到自己写了死循环的尾递归，而出现死循环后又不会报出 `stack overflow` 的错误，难以辨别。
  - 堆栈信息会在优化的过程中丢失，开发者调试非常困难。

### 手动优化

- 把递归改写成迭代

## 函数参数的尾逗号

- ES2017 [允许](https://github.com/jeffmo/es-trailing-function-commas) 函数的最后一个参数有尾逗号（trailing comma）。
- 此前，函数定义和调用时，都不允许最后一个参数后面出现逗号。

  ```javascript
  function clownsEverywhere(
    param1,
    param2
  ) { /* ... */ }
  
  clownsEverywhere(
    'foo',
    'bar'
  );
  ```

- 上面代码中，如果在 `param2` 或 `bar` 后面加一个逗号，就会报错。
- 如果像上面这样，将参数写成多行（即每个参数占据一行），以后修改代码的时候，想为函数 `clownsEverywhere` 添加第三个参数，或者调整参数的次序，就势必要在原来最后一个参数后面添加一个逗号。这对于版本管理系统来说，就会显示添加逗号的那一行也发生了变动。这看上去有点冗余，因此新的语法允许定义和调用时，尾部直接有一个逗号。

  ```javascript
  function clownsEverywhere(
    param1,
    param2,
  ) { /* ... */ }
  
  clownsEverywhere(
    'foo',
    'bar',
  );
  ```

- 这样的规定也使得，函数参数与数组和对象的尾逗号规则，保持一致了。

# FAQ

## 空函数

no operation funtion

```js
const noop = function(){}
```

运用

```js
export function(arg1,callback=noop){
  // 可以减少对callback的判断，不用判断是否为空
  if(typeof callback==='function'){
    callback()
  }
}
```

# Experience

#programming-experience

## 使用 Function 构造函数代替 eval()

### Function()

**`Function` 构造函数** 创建一个新的 `Function` **对象**。直接调用此构造函数可用动态创建函数，但会遇到和 [`eval`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/eval) 类似的的安全问题和 (相对较小的) 性能问题。然而，与 `eval` 不同的是，`Function` 创建的函数只能在全局作用域中运行。

**语法**

```
new Function ([arg1[, arg2[, ...argN]],] functionBody)
```

**参数**

arg1, arg2, ... arg*N*：**参数名称是一个有效的 JavaScript 标识符的字符串**，或者一个用逗号分隔的有效字符串的列表; 例如“`×`”，“`theValue`”，或“`a,b`”。

functionBody：**一个含有包括函数定义的 JavaScript 语句的字符串**。

**描述**

使用 `Function` 构造器生成的 `Function` 对象是在函数创建时解析的。这比你使用 [函数声明](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/function) 或者 [函数表达式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/function) 并在你的代码中调用更为低效，因为使用后者创建的函数是跟其他代码一起解析的。

所有被传递到构造函数中的参数，都将被视为将被创建的函数的参数，并且是相同的标示符名称和传递顺序。

以调用函数的方式调用 `Function` 的构造函数（而不是使用 `new` 关键字) 跟以构造函数来调用是一样的。 @@@

> 不理解这句话

**示例**

```js
// 可以直接在 JavaScript 控制台中运行
// 创建了一个能返回两个参数和的函数
const adder = new Function("a", "b", "return a + b");
// 调用函数
adder(2, 6);
// > 8
```

**作用域**

由 `Function` 构造器创建的函数不会创建当前环境的闭包，它们总是被创建于全局环境，因此在运行时它们只能访问全局变量和自己的局部变量，不能访问它们被 `Function` 构造器创建时所在的作用域的变量。这一点与使用 [`eval`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/eval) 执行创建函数的代码不同。

```js
var x = 10;

function createFunction1() {
    var x = 20;
    return new Function('return x;'); // 这里的 x 指向最上面全局作用域内的 x
}

function createFunction2() {
    var x = 20;
    function f() {
        return x; // 这里的 x 指向上方本地作用域内的 x
    }
    return f;
}

var f1 = createFunction1();
console.log(f1());          // 10
var f2 = createFunction2();
console.log(f2());          // 20
```

虽然这段代码可以在浏览器中正常运行，但在 Node.js 中 `f1()` 会产生一个“找不到变量 `x` ”的 `ReferenceError`。这是因为在 Node 中顶级作用域不是全局作用域，而 `x` 其实是在当前模块的作用域之中。

### eval()

`eval()` 函数会将传入的字符串当做 JavaScript 代码进行执行。

```js
console.log(eval('2 + 2'));
// expected output: 4

console.log(eval(new String('2 + 2')));
// expected output: 2 + 2

console.log(eval('2 + 2') === eval('4'));
// expected output: true

console.log(eval('2 + 2') === eval(new String('2 + 2')));
// expected output: false
```

如果你间接的使用 `eval()`，比如通过一个引用来调用它，而不是直接的调用 `eval`。 从 [ECMAScript 5](http://www.ecma-international.org/ecma-262/5.1/#sec-10.4.2) 起，它工作在全局作用域下，而不是局部作用域中。这就意味着，例如，下面的代码的作用声明创建一个全局函数，并且 `eval` 中的这些代码在执行期间不能在被调用的作用域中访问局部变量。

```js
function test() {
  var x = 2, y = 4;
  console.log(eval('x + y'));  // 直接调用，使用本地作用域，结果是 6
  var geval = eval; // 等价于在全局作用域调用
  console.log(geval('x + y')); // 间接调用，使用全局作用域，throws ReferenceError 因为`x`未定义
  (0, eval)('x + y'); // 另一个间接调用的例子
​}
```

### 永远不要使用 `eval`

`eval()` 是一个危险的函数， 它使用与调用者相同的权限执行代码。如果你用 `eval()` 运行的字符串代码被恶意方（不怀好意的人）修改，您最终可能会在您的网页/扩展程序的权限下，在用户计算机上运行恶意代码。更重要的是，第三方代码可以看到某一个 `eval()` 被调用时的作用域，这也有可能导致一些不同方式的攻击。相似的 [`Function`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function) 就不容易被攻击。

`eval()` 通常比其他替代方法更慢，因为它必须调用 JS 解释器，而许多其他结构则可被现代 JS 引擎进行优化。

此外，现代 JavaScript 解释器将 javascript 转换为机器代码。 这意味着任何变量命名的概念都会被删除。 因此，任意一个 eval 的使用都会强制浏览器进行冗长的变量名称查找，以确定变量在机器代码中的位置并设置其值。 另外，新内容将会通过 `eval()` 引进给变量， 比如更改该变量的类型，因此会强制浏览器重新执行所有已经生成的机器代码以进行补偿。 但是，（谢天谢地）存在一个非常好的 eval 替代方法：只需使用 [window.Function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function)。

**示例 1**

使用 eval 的糟糕代码：

```js
function looseJsonParse(obj){
    return eval("(" + obj + ")");
}
console.log(looseJsonParse(
   "{a:(4-1), b:function(){}, c:new Date()}"
))
```

使用 Function()：

```js
function looseJsonParse(obj){
    return Function('"use strict";return (' + obj + ')')();
}
console.log(looseJsonParse(
   "{a:(4-1), b:function(){}, c:new Date()}"
))
```

注意 `c: new Date()` 在执行体中，eval 的这个代码的速度要慢得多。 使用 Function() 的函数中，默认是在全局作用域，因此浏览器可以放心的假设 `Date` 是来自 `window.Date` 的而不是一个名为 `Date` 的局部变量。 然而，在使用 `eval()` 的代码中，浏览器不能假设这一点。

**示例 2**

```js
function Date(n){
    return ["Monday","Tuesday","Wednesday","Thursday","Friday","Saturday","Sunday"][n%7 || 0];
}
function looseJsonParse(obj){
    return eval("(" + obj + ")");
}
console.log(looseJsonParse(
   "{a:(4-1), b:function(){}, c:new Date()}"
))
```

在 `eval()` 版本的代码中，浏览器被迫进行高代价的查找调用以检查是否存在名为 Date() 的任何局部变量。

### 使用 Function 访问局部变量

```js
function Date(n){
    return ["Monday","Tuesday","Wednesday","Thursday","Friday","Saturday","Sunday"][n%7 || 0];
}
function runCodeWithDateFunction(obj){
    return Function('"use strict";return (' + obj + ')')()(
        Date
    );
}
console.log(runCodeWithDateFunction(
   "function(Date){ return Date(5) }"
))
```

由于三重嵌套函数，上面的代码似乎效率低下，但让我们分析一下上述有效方法的好处：

1. 它使得传递给 `runCodeWithDateFunction` 的字符串中的代码更少。
2. 函数调用开销很小，使得代码尺寸小得多，值得获益
3. `Function()` 更容易让你的代码利用特性修饰 `"use strict"`;
4. 代码不使用 eval（），使其比其他方式快几个数量级。

**另一种写法**BEST

```js
function runCode(){
  let result = null
  try{
    result = Function(`
      return function(print){
        ${editor.getValue()}
      }
    `)()(print)
  }
  catch(error){
    setCodeResult(error+'')
  }
}
function print(result){
  setCodeResult(result)
}
```

**API 原生写法**

```js
function test(n){
  console.log(n) 
}
function runCode(obj){
  return Function('test',`
    return ${obj}
  `)(test)
}
console.log(runCode(
 "test(5)"
))
```

### 使用 Function 运行编辑器的字符串代码

```js
function runCode(){
  let result = null
  try{
    result = Function(`
      return function(print){
        ${editor.getValue()}
      }
    `)()(print)
  }
  catch(error){
    setCodeResult(error+'')
  }
}
function print(result){
  setCodeResult(result)
}
/*
function sun(a,b){
  return a + b
}

print(sun(1,2))

*/

```

### 对比

作用域

性能

eval 会直接执行代码，而 Function 仅仅是返回一个函数
