---
{"dg-publish":true,"permalink":"/programming/font-end/field/js-functional/"}
---



函数式指南：[https://llh911001.gitbooks.io/mostly-adequate-guide-chinese/content/ch2.html#%E4%B8%BA%E4%BD%95%E9%92%9F%E7%88%B1%E4%B8%80%E7%AD%89%E5%85%AC%E6%B0%91](https://llh911001.gitbooks.io/mostly-adequate-guide-chinese/content/ch2.html#为何钟爱一等公民)

# 基本概念

## 命令式

命令式编程的主要思想是关注计算机执行的步骤，即一步一步告诉计算机先做什么再做什么。

## 声明式

声明式编程是以数据结构的形式来表达程序执行的逻辑。它的主要思想是告诉计算机应该做什么，但不指定具体要怎么做。

SQL 语句就是最明显的一种声明式编程的例子。

## 纯函数

一类特别的函数: 只要是同样的输入，必定得到同样的输出

必须遵守以下一些约束：

1. 不得改写参数
2. 不能调用系统 I/O 的 API
3. 能调用 Date.now() 或者 Math.random() 等不纯的方法
4. reducer 函数必须是一个纯函数

## 副作用

这个副作用的概念也是。它的要求很高，概括的讲，只要是跟函数外部环境发生的交互就都是副作用。从“副作用”这个词语来看，它更多的情况在于“改变系统状态”。

## 高阶函数

理解: 一类特别的函数

情况 1: 参数是函数

情况 2: 返回是函数

## 常见的高阶函数

定时器设置函数

数组的 map()/filter()/reduce()/find()/bind()

react-redux 中的 connect 函数

作用：能实现更加动态, 更加可扩展的功能

# 函数柯里化

**参考：**<https://github.com/mqyqingfeng/Blog/issues/42>

函数柯里化是把接受多个参数的函数转变成接受一个单一参数 (最初函数的第一个参数),并且返回接受余下的参数而且返回结果的新函数的技术

柯里化其实本身是固定一个可以预期的参数，并返回一个特定的函数，处理批特定的需求。这增加了函数的适用性，但同时也降低了函数的通用范围。

**本质**：用闭包把参数保存起来，当参数的数量足够执行函数了，就开始执行函数

## 基础定义

```js
function currying(fn, ...args) {
    return function (...rest) {
        return fn.apply(null, args.concat(rest))
    }
}
const curryFunc = currying(fn, args)
```

### 应用示例

```js
function square(i){
	return i * i;
}
function dubble(i){
	return i *= 2;
}
function map(handeler,list){
	return list.map(handeler)
}
// 数组的每一项平方
map(square, [1, 2, 3, 4, 5]);
map(square, [6, 7, 8, 9, 10]);
map(square, [10, 20, 30, 40, 50]);

// 数组的每一项加倍
map(dubble, [1, 2, 3, 4, 5]);
map(dubble, [6, 7, 8, 9, 10]);
map(dubble, [10, 20, 30, 40, 50]);
```

例子中，创建了一个 map 通用函数，用于适应不同的应用场景。显然，通用性不用怀疑。同时，例子中重复传入了相同的处理函数：square 和 dubble。

应用中这种可能会更多。当然，通用性的增强必然带来适用性的减弱。但是，我们依然可以在中间找到一种平衡。

我们利用柯里化改造一下：

```js
function square(i) {
    return i * i;
}

function dubble(i) {
    return i *= 2;
}

function map(handeler, list) {
    return list.map(handeler);
}

var mapSQ = currying(map, square);
mapSQ([1, 2, 3, 4, 5]);
mapSQ([6, 7, 8, 9, 10]);
mapSQ([10, 20, 30, 40, 50]);

var mapDB = currying(map, dubble);
mapDB([1, 2, 3, 4, 5]);
mapDB([6, 7, 8, 9, 10]);
mapDB([10, 20, 30, 40, 50]);
```

我们缩小了函数的适用范围，但同时提高函数的适性。

由此，可知柯里化不仅仅是提高了代码的合理性，更重的它突出一种思想 ---**降低适用范围，提高适用性**

## 通用定义

正确的做法如下

```js
const curry = function(fn,args=[]) {
  return function(...rest) {
    // 复制闭包里的参数，同时合并新传入的参数
    const _args = args.concat(rest)
    if (fn.length === _args.length) {
      // 累计的参数达到了形参的数量，执行函数
      return fn.apply(this, _args)
    }else{
      // 累计的参数没有达到了形参的数量，继续累计参数
      return curry.call(this, fn, _args);
    }
  }
}
```

```js
// 使用三目运算符，同时维护this
function curry(fn, args = []) {
  return fn.length === args.length
    ? fn.apply(this, args)
    : function (...rest) {
    	// 当参数满了也会再次调用，然后执行fn
      return curry.call(this, fn, args.concat(rest))
    }
}

// 配合箭头函数，配合递归，但是无法维护this
const curry = (fn, _args = []) => fn.length === _args.length 
  ? fn(..._args) 
  : (...rest) => curry(fn, _args.concat(rest));
```

如果不想定义参数，想要在传入空参数的时候直接调用

```js
const curry = function(fn,args=[]) {
  return function(...rest) {
    // 复制闭包里的参数，同时合并新传入的参数
    const _args = args.concat(rest)
    if (fn.length === _args.length || rest.length ===0) {
      // 累计的参数达到了形参的数量，执行函数
      return fn.apply(this, _args)
    }else{
      // 累计的参数没有达到了形参的数量，继续累计参数
      return curry.call(this, fn, _args);
    }
  }
}
```

### 示例

```js
var curry = function(fn) {
  // 把所有传入的参数都保存在闭包内
  var _args = []
  return function cb(...rest) {
    if (fn.length === arr.length) {
      // 累计的参数达到了形参的数量，执行函数
      return fn.apply(this, _args)
    }
    // 累计的参数没有达到了形参的数量，继续累计参数
    _args.concat(rest)
    return cb;
  }
}

function add(){
  let sum = 0
  for (let i = 0; i < arguments.length; i++) {
    sum += arguments[i]
  }
  return sum
}
const curryAdd = curry(add)
console.log(curryAdd(1)(2)(3)(4)())
```

## 延迟执行

柯里化的另一个应用场景是延迟执行。不断的柯里化，通过闭包累积传入的参数，最后执行

更加通用的写法

```js
var curry = function(fn) {
    var _args = []
    return function cb() {
        if (arguments.length == 0) {
            return fn.apply(this, _args)
        }
        Array.prototype.push.apply(_args, arguments);
        return cb;
    }
}
```

### 示例

```js
function add(){
  let sum = 0
  for (let i = 0; i < arguments.length; i++) {
    sum += arguments[i]
  }
  return sum
}
const curryAdd = curry(add)
console.log(curryAdd(1)(2)(3)(4)())
```

## 固定易变因素

柯里化特性决定了它这应用场景。提前把易变因素，传参固定下来，生成一个更明确的应用函数。最典型的代表应用，是 bind 函数用以固定 this 这个易变对象。

```js
Function.prototype.bind = function(context,...rest) {
  var _this = this,
      _args = rest;
  return function(...rest) {
    return _this.apply(context, _args.concat(rest))
  }
}
```

## 更加强大的功能

curry 函数写到这里其实已经很完善了，但是注意这个函数的传参顺序必须是从左到右，根据形参的顺序依次传入，如果我不想根据这个顺序传呢？

```js
var curry = function(fn) {
  // 把所有传入的参数都保存在闭包内
  var _args = []
  return function cb(...rest) {
    if (fn.length === arr.length) {
      // 累计的参数达到了形参的数量，执行函数
      return fn.apply(this, _args)
    }
    // 累计的参数没有达到了形参的数量，继续累计参数
    _args.concat(rest)
    return cb;
  }
}
```

我们可以创建一个占位符，比如这样：

```js
var fn = curry(function(a, b, c) {
    console.log([a, b, c]);
});

fn("a", _, "c")("b") // ["a", "b", "c"]
```

# 偏函数

## 定义

维基百科中对偏函数 (Partial application) 的定义为：

> In computer science, partial application (or partial function application) refers to the process of fixing a number of arguments to a function, producing another function of smaller arity.

在计算机科学中，局部应用是指固定一个函数的一些参数，然后产生另一个更小元的函数。

什么是元？元是指函数参数的个数，比如一个带有两个参数的函数被称为二元函数。

```js
function add(a, b) {
  return a + b;
}

// 执行 add 函数，一次传入两个参数即可
add(1, 2) // 3

// 假设有一个 partial 函数可以做到局部应用
var addOne = partial(add, 1);

addOne(2) // 3
```

## 柯里化与局部应用

实际上你会发现这个例子和柯里化太像了，所以两者到底是有什么区别呢？

柯里化是将一个多参数函数转换成多个单参数函数，也就是将一个 n 元函数转换成 n 个一元函数。

局部应用则是固定一个函数的一个或者多个参数，也就是将一个 n 元函数转换成一个 n - x 元函数。

如果说两者有什么关系的话，引用 [functional-programming-jargon](https://github.com/hemanth/functional-programming-jargon#partial-application) 中的描述就是：

> Curried functions are automatically partially applied.

## Partial 函数实现

我们可以直接使用 bind 呐，举个例子：

```js
function add(a, b) {
    return a + b;
}

var addOne = add.bind(null, 1);

addOne(2) // 3
```

然而使用 bind 我们还是改变了 this 指向，我们要写一个不改变 this 指向的方法。

### 第一版

```js
// 第一版
// 似曾相识的代码
function partial(fn) {
    var args = [].slice.call(arguments, 1);
    return function() {
        var newArgs = args.concat([].slice.call(arguments));
        return fn.apply(this, newArgs);
    };
};
// 因为涉及到调用时的this，所以不可以使用箭头函数
function partial(fn,...rest) {
  return function(...inRest) {
      return fn.apply(this, rest.concat(inRest));
  };
};


```

我们来写个 demo 验证下 this 的指向：

```js
function add(a, b) {
    return a + b + this.value;
}

// var addOne = add.bind(null, 1);
var addOne = partial(add, 1);

var value = 1;
var obj = {
    value: 2,
    addOne: addOne
}
obj.addOne(2); // ???
// 使用 bind 时，结果为 4
// 使用 partial 时，结果为 5
```

## 第二版

然而正如 curry 函数可以使用占位符一样，我们希望 partial 函数也可以实现这个功能，我们再来写第二版：

```js
var _ = {};

function partial(fn,...rest) {
    return function() {
        var position = 0, len = args.length;
        for(var i = 0; i < len; i++) {
            rest[i] = rest[i] === _ ? arguments[position++] : args[i]
        }
        while(position < arguments.length) args.push(arguments[position++]);
        return fn.apply(this, args);
    };
};
```

# 惰性函数

## 需求

我们现在需要写一个 foo 函数，这个函数返回首次调用时的 Date 对象，注意是首次。

## 普通方法

```js
var t;
function foo() {
    if (t) return t;
    t = new Date()
    return t;
}
```

问题有两个，一是污染了全局变量，二是每次调用 foo 的时候都需要进行一次判断。

### 使用闭包改进

```js
var foo = (function() {
    var t;
    return function() {
        if (t) return t;
        t = new Date();
        return t;
    }
})();
```

然而还是没有解决调用时都必须进行一次判断的问题。

## 函数对象

```js
function foo() {
    if (foo.t) return foo.t;
    foo.t = new Date();
    return foo.t;
}
```

函数也是一种对象，利用这个特性，我们也可以解决这个问题。

## 惰性函数

不错，惰性函数就是解决每次都要进行判断的这个问题，解决原理很简单，重写函数。

```js
var foo = function() {
    var t = new Date()
    foo = function() {
        return t;
    };
    return foo();
}
```

## 解决了什么问题？

对于同样的输入，每次调用函数都在判断

## 实际应用

### 兼容性优化

DOM 事件添加中，为了兼容现代浏览器和 IE 浏览器，我们需要对浏览器环境进行一次判断：

```js
// 简化写法
function addEvent (type, el, fn) {
    if (window.addEventListener) {
        el.addEventListener(type, fn, false);
    }
    else if(window.attachEvent){
        el.attachEvent('on' + type, fn);
    }
}
```

问题在于我们每当使用一次 addEvent 时都会进行一次判断。

利用惰性函数，我们可以这样做：

```js
function addEvent (type, el, fn) {
    if (window.addEventListener) {
        addEvent = function (type, el, fn) {
            el.addEventListener(type, fn, false);
        }
    }
    else if(window.attachEvent){
        addEvent = function (type, el, fn) {
            el.attachEvent('on' + type, fn);
        }
    }
}
```

当然我们也可以使用闭包的形式：

```js
var addEvent = (function(){
    if (window.addEventListener) {
        return function (type, el, fn) {
            el.addEventListener(type, fn, false);
        }
    }
    else if(window.attachEvent){
        return function (type, el, fn) {
            el.attachEvent('on' + type, fn);
        }
    }
})();
```

## 面试题

```js
function increment(){
  let count = 1
  increment = function(){
    return ++count
  }
  return count
}

console.log(increment())
console.log(increment())
console.log(increment())
console.log(increment())
```

写出一个每次调用都会增加的函数，存在一个很神奇的现象，原先的外层函数依然存在，闭包也正常运行，但是已经没有办法访问到原本的 increment 了，那么要怎么防止内存泄漏呢？

改进

```js
let increment = (function(){
  let count = 0
  return function(){
    return ++count
  }
})()

console.log(increment())
console.log(increment())
console.log(increment())
console.log(increment())
```

还是存在内存泄漏的问题？只要把 increment 赋值为 null，闭包的引用就不会再保存了

# 函数组合

## 需求

我们需要写一个函数，输入 'kevin'，返回 'HELLO, KEVIN'。

## 尝试

```js
var toUpperCase = function(x) { return x.toUpperCase(); };
var hello = function(x) { return 'HELLO, ' + x; };

var greet = function(x){
    return hello(toUpperCase(x));
};

greet('kevin');
```

还好我们只有两个步骤，首先小写转大写，然后拼接字符串。如果有更多的操作，greet 函数里就需要更多的嵌套，类似于 `fn3(fn2(fn1(fn0(x))))`。

## 优化

试想我们写个 compose 函数：

```js
var compose = function(f,g) {
    return function(x) {
        return f(g(x));
    };
};
```

greet 函数就可以被优化为：

```js
var greet = compose(hello, toUpperCase);
greet('kevin');
```

利用 compose 将两个函数组合成一个函数，让代码从 **右向左** 运行，而不是由内而外运行，可读性大大提升。这便是函数组合。

> 主要符合数学 **从右到左** 的操作概念

但是现在的 compose 函数也只是能支持两个参数，如果有更多的步骤呢？我们岂不是要这样做：

```js
compose(d, compose(c, compose(b, a)))
```

为什么我们不写一个帅气的 compose 函数支持传入多个函数呢？这样就变成了：

```js
compose(d, c, b, a)
```

## Compose 实现

我们直接抄袭 underscore 的 compose 函数的实现：

```js
function compose() {
    var args = arguments;
    var start = args.length - 1;
    return function() {
        var i = start;
        var result = args[start].apply(this, arguments);
        while (i--) result = args[i].call(this, result);
        return result;
    };
};

function compose(...funcs) {
  if (funcs.length === 0) return arg => arg
  if (funcs.length === 1) return funcs[0]
  // a是累加器，最终返回的函数
  return funcs.reduce((a, b) => (...args) => a(b(...args)))
}
// args 是 c(...args)，函数从右向左执行
// compose(a,b,c,d,e) 最终返回的 (...args) => a(b(c(d(e(...args)))))
```

**注意**: 其中, 第一个函数是可以支持传入多个参数的, 但是后续的函数都只能接受前一个函数的返回值作为唯一参数.

现在的 compose 函数已经可以支持多个函数了，然而有了这个又有什么用呢？

在此之前，我们先了解一个概念叫做 pointfree。

## Pointfree

pointfree 指的是函数无须提及将要操作的数据是什么样的。依然是以最初的需求为例：

```js
// 需求：输入 'kevin'，返回 'HELLO, KEVIN'。

// 非 pointfree，因为提到了数据：name
var greet = function(name) {
    return ('hello ' + name).toUpperCase();
}

// pointfree
// 先定义基本运算，这些可以封装起来复用
var toUpperCase = function(x) { return x.toUpperCase(); };
var hello = function(x) { return 'HELLO, ' + x; };

var greet = compose(hello, toUpperCase);
greet('kevin');
```

我们再举个稍微复杂一点的例子，为了方便书写，我们需要借助在 [《JavaScript专题之函数柯里化》](https://github.com/mqyqingfeng/Blog/issues/42) 中写到的 curry 函数：

```js
// 需求：输入 'kevin daisy kelly'，返回 'K.D.K'

// 非 pointfree，因为提到了数据：name
var initials = function (name) {
    return name.split(' ').map(compose(toUpperCase, head)).join('. ');
};

// pointfree
// 先定义基本运算
var split = curry(function(separator, str) { return str.split(separator) })
var head = function(str) { return str.slice(0, 1) }
var toUpperCase = function(str) { return str.toUpperCase() }
var join = curry(function(separator, arr) { return arr.join(separator) })
var map = curry(function(fn, arr) { return arr.map(fn) })

var initials = compose(join('.'), map(compose(toUpperCase, head)), split(' '));

initials("kevin daisy kelly");
```

从这个例子中我们可以看到，利用柯里化（curry）和函数组合 (compose) 非常有助于实现 pointfree。

也许你会想，这种写法好麻烦呐，我们还需要定义那么多的基础函数……可是如果有工具库已经帮你写好了呢？比如 [ramda.js](http://ramda.cn/docs/)：

```js
// 使用 ramda.js
var initials = R.compose(R.join('.'), R.map(R.compose(R.toUpper, R.head)), R.split(' '));
```

**本质**：Pointfree 的本质就是使用一些通用的函数，组合出各种复杂运算。上层运算不要直接操作数据，而是通过底层函数去处理。即不使用所要处理的值，只合成运算过程。

那么使用 pointfree 模式究竟有什么好处呢？pointfree 模式能够帮助我们减少不必要的命名，让代码保持简洁和通用，更符合语义，更容易复用，测试也变得轻而易举。

## 实例

这个例子来自于 [Favoring Curry](http://fr.umio.us/favoring-curry/)：

假设我们从服务器获取这样的数据：

```js
var data = {
    result: "SUCCESS",
    tasks: [
        {id: 104, complete: false,            priority: "high",
                  dueDate: "2013-11-29",      username: "Scott",
                  title: "Do something",      created: "9/22/2013"},
        {id: 105, complete: false,            priority: "medium",
                  dueDate: "2013-11-22",      username: "Lena",
                  title: "Do something else", created: "9/22/2013"},
        {id: 107, complete: true,             priority: "high",
                  dueDate: "2013-11-22",      username: "Mike",
                  title: "Fix the foo",       created: "9/22/2013"},
        {id: 108, complete: false,            priority: "low",
                  dueDate: "2013-11-15",      username: "Punam",
                  title: "Adjust the bar",    created: "9/25/2013"},
        {id: 110, complete: false,            priority: "medium",
                  dueDate: "2013-11-15",      username: "Scott",
                  title: "Rename everything", created: "10/2/2013"},
        {id: 112, complete: true,             priority: "high",
                  dueDate: "2013-11-27",      username: "Lena",
                  title: "Alter all quuxes",  created: "10/5/2013"}
    ]
};
```

我们需要写一个名为 getIncompleteTaskSummaries 的函数，接收一个 username 作为参数，从服务器获取数据，然后筛选出这个用户的未完成的任务的 ids、priorities、titles、和 dueDate 数据，并且按照日期升序排序。

以 Scott 为例，最终筛选出的数据为：

```js
[
    {id: 110, title: "Rename everything", 
        dueDate: "2013-11-15", priority: "medium"},
    {id: 104, title: "Do something", 
        dueDate: "2013-11-29", priority: "high"}
]
```

普通的方式为：

```js
// 第一版 过程式编程
var fetchData = function() {
  // 模拟
  return Promise.resolve(data)
};

var getIncompleteTaskSummaries = function(membername) {
  return fetchData()
    .then(function(data) {
    return data.tasks;
  })
    .then(function(tasks) {
    return tasks.filter(function(task) {
      return task.username == membername
    })
  })
    .then(function(tasks) {
    return tasks.filter(function(task) {
      return !task.complete
    })
  })
    .then(function(tasks) {
    return tasks.map(function(task) {
      return {
        id: task.id,
        dueDate: task.dueDate,
        title: task.title,
        priority: task.priority
      }
    })
  })
    .then(function(tasks) {
    return tasks.sort(function(first, second) {
      var a = first.dueDate,
          b = second.dueDate;
      return a < b ? -1 : a > b ? 1 : 0;
    });
  })
    .then(function(task) {
    console.log(task)
  })
};

getIncompleteTaskSummaries('Scott')
```

如果使用 pointfree 模式：

```js
// 第二版 pointfree 改写
var fetchData = function() {
  return Promise.resolve(data)
};

// 编写基本函数
var prop = curry(function(name, obj) {
  return obj[name];
});

var propEq = curry(function(name, val, obj) {
  return obj[name] === val;
});

var filter = curry(function(fn, arr) {
  return arr.filter(fn)
});

var map = curry(function(fn, arr) {
  return arr.map(fn)
});

var pick = curry(function(args, obj){
  var result = {};
  for (var i = 0; i < args.length; i++) {
    result[args[i]] = obj[args[i]]
  }
  return result;
});

var sortBy = curry(function(fn, arr) {
  return arr.sort(function(a, b){
    var a = fn(a),
        b = fn(b);
    return a < b ? -1 : a > b ? 1 : 0;
  })
});

var getIncompleteTaskSummaries = function(membername) {
  return fetchData()
    .then(prop('tasks'))
    .then(filter(propEq('username', membername)))
    .then(filter(propEq('complete', false)))
    .then(map(pick(['id', 'dueDate', 'title', 'priority'])))
    .then(sortBy(prop('dueDate')))
    .then(console.log)
};

getIncompleteTaskSummaries('Scott')
```

如果直接使用 ramda.js，你可以省去编写基本函数:

```js
// 第三版 使用 ramda.js
var fetchData = function() {
  return Promise.resolve(data)
};

var getIncompleteTaskSummaries = function(membername) {
  return fetchData()
    .then(R.prop('tasks'))
    .then(R.filter(R.propEq('username', membername)))
    .then(R.filter(R.propEq('complete', false)))
    .then(R.map(R.pick(['id', 'dueDate', 'title', 'priority'])))
    .then(R.sortBy(R.prop('dueDate')))
    .then(console.log)
};

getIncompleteTaskSummaries('Scott')
```

当然了，利用 compose，你也可以这样写：

```js
// 第四版 使用 compose
var fetchData = function() {
  return Promise.resolve(data)
};

var getIncompleteTaskSummaries = function(membername) {
  return fetchData()
    .then(R.compose(
    console.log,
    R.sortBy(R.prop('dueDate')),
    R.map(R.pick(['id', 'dueDate', 'title', 'priority'])
         ),
    R.filter(R.propEq('complete', false)),
    R.filter(R.propEq('username', membername)),
    R.prop('tasks'),
  ))
};

getIncompleteTaskSummaries('Scott')
```

compose 是从右到左依此执行，当然你也可以写一个从左到右的版本，但是从右向左执行更加能够反映数学上的含义。

ramda.js 提供了一个 R.pipe 函数，可以做的从左到右，以上可以改写为：

```js
// 第五版 使用 R.pipe
var getIncompleteTaskSummaries = function(membername) {
  return fetchData()
    .then(R.pipe(
    R.prop('tasks'),
    R.filter(R.propEq('username', membername)),
    R.filter(R.propEq('complete', false)),
    R.map(R.pick(['id', 'dueDate', 'title', 'priority'])
          R.sortBy(R.prop('dueDate')),
          console.log,
         ))
};
```

## Typed-compose

### 从简单到复杂

如果我们要给 compose 函数加上 ts 类型, 根据从简单到复杂的思想, 可以写出下面这种实现:

```ts
type UnknownFunction = (...args: unknown[]) => unknown;
/**
 * 函数式编程，函数组合工具，按顺序传递返回值调用函数管道
 *
 * @returns
 */
export function compose<P>(): (arg: P) => P;
export function compose<A extends unknown[], B>(ab: (this: void, ...a: A) => B): (...args: A) => B;
export function compose<A extends unknown[], B, C>(
    bc: (this: void, b: B) => C,
    ab: (this: void, ...a: A) => B,
): (...args: A) => C;
export function compose<A extends unknown[], B, C, D>(
    cd: (this: void, c: C) => D,
    bc: (this: void, b: B) => C,
    ab: (this: void, ...a: A) => B,
): (...args: A) => D;
export function compose<A extends unknown[], B, C, D, E>(
    de: (this: void, d: D) => E,
    cd: (this: void, c: C) => D,
    bc: (this: void, b: B) => C,
    ab: (this: void, ...a: A) => B,
): (...args: A) => E;
export function compose<A extends unknown[], B, C, D, E, F>(
    ef: (this: void, e: E) => F,
    de: (this: void, d: D) => E,
    cd: (this: void, c: C) => D,
    bc: (this: void, b: B) => C,
    ab: (this: void, ...a: A) => B,
): (...args: A) => F;
export function compose(...funcs: UnknownFunction[]): UnknownFunction {
    if (funcs.length === 0) return (arg) => arg;
    if (funcs.length === 1) return funcs[0];
    // a是累加器，最终返回的函数
    return funcs.reduce((a, b) => (...args) => a(b(...args)));
}
```

利用重载给不同数量的 funcs 都写上对应的类型, 但是这样不够通用

### 利用可变元组

[ts-basic](../framework/typescript/ts-basic.md#可变元祖类型)

观察第一种实现方法, 发现我们需要做到以下几点:

1. 第一个函数可以接受任意数量的参数
2. 其余函数只能接受一个参数, 并且是上一个函数的返回值
3. compose 方法最终返回一个函数
	1. 该函数的参数是第一个函数的参数
	2. 该函数的返回值是最后一个函数的返回值

第二点其余函数接受的参数类型是上一个函数的返回值, 这个看起来有点难以实现, 我们先支持第一点和第三点

```ts

type SingleParamFunc = (...args: any) => any;

type PickFirstFn<T extends SingleParamFunc[]> = T extends [...rest: infer U, first: infer F extends SingleParamFunc] ? F : never;

type FirstFnParameterType<T extends SingleParamFunc[]> = Parameters<PickFirstFn<T>>
// 获取第一个函数的参数
type Compose<Funcs extends SingleParamFunc[]> = (...funcs: Funcs) =>
    ((...args: FirstFnParameterType<Funcs>) => ReturnType<Funcs[0]>);

const compose = <T extends SingleParamFunc[]>(...funcs: T) => (p: FirstFnParameterType<T>): ReturnType<T[0]> =>  funcs.reduceRight((acc: any, cur: any) => cur(acc), p);
```

待补充: 函数的参数和上一个函数的返回值类型需要是相同的

# 管道函数

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce

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

## 按序执行 Promise

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce

# 函数记忆

## 定义

函数记忆是指将上次的计算结果缓存起来，当下次调用时，如果遇到相同的参数，就直接返回缓存中的数据。

举个例子：

```js
function add(a, b) {
    return a + b;
}

// 假设 memoize 可以实现函数记忆
var memoizedAdd = memoize(add);

memoizedAdd(1, 2) // 3
memoizedAdd(1, 2) // 相同的参数，第二次调用时，从缓存中取出数据，而非重新计算一次
```

## 原理

实现这样一个 memoize 函数很简单，原理上只用把参数和对应的结果数据存到一个对象中，调用时，判断参数对应的数据是否存在，存在就返回对应的结果数据。

## 第一版

```js
// 第一版 (来自《JavaScript权威指南》)
function memoize(f) {
  var cache = {};
  return function(...args){
    var key = args.length + args.join(",");
    if (key in cache) {
      return cache[key]
    }
    else {
      return cache[key] = f.apply(this, args)
    }
  }
}
```

我们来测试一下：

```js
var add = function(a, b, c) {
  return a + b + c
}

var memoizedAdd = memoize(add)

console.time('use memoize')
for(var i = 0; i < 100000; i++) {
    memoizedAdd(1, 2, 3)
}
console.timeEnd('use memoize')

console.time('not use memoize')
for(var i = 0; i < 100000; i++) {
    add(1, 2, 3)
}
console.timeEnd('not use memoize')
```

在 Chrome 中，使用 memoize 大约耗时 60ms，如果我们不使用函数记忆，大约耗时 1.3 ms 左右。

## 注意

什么，我们使用了看似高大上的函数记忆，结果却更加耗时，这个例子近乎有 60 倍呢！

所以，函数记忆也并不是万能的，你看这个简单的场景，其实并不适合用函数记忆。

需要注意的是，函数记忆只是一种编程技巧，本质上是牺牲算法的空间复杂度以换取更优的时间复杂度，在客户端 JavaScript 中代码的执行时间复杂度往往成为瓶颈，因此在大多数场景下，这种牺牲空间换取时间的做法以提升程序执行效率的做法是非常可取的。

## 第二版

因为第一版使用了 join 方法，我们很容易想到当参数是对象的时候，就会自动调用 toString 方法转换成 `[Object object]`，再拼接字符串作为 key 值。我们写个 demo 验证一下这个问题：

```js
var propValue = function(obj){
    return obj.value
}

var memoizedAdd = memoize(propValue)

console.log(memoizedAdd({value: 1})) // 1
console.log(memoizedAdd({value: 2})) // 1
```

两者都返回了 1，显然是有问题的，所以我们看看 underscore 的 memoize 函数是如何实现的：

```js
// 第二版 (来自 underscore 的实现)
var memoize = function(func, hasher) {
  var memoize = function(key) {
    var cache = memoize.cache;
    var address = '' + (hasher ? hasher.apply(this, arguments) : key);
    if (!cache[address]) {
      cache[address] = func.apply(this, arguments);
    }
    return cache[address];
  };
  memoize.cache = {};
  return memoize;
};
```

从这个实现可以看出，underscore 默认使用 function 的第一个参数作为 key，所以如果直接使用

```js
var add = function(a, b, c) {
  return a + b + c
}

var memoizedAdd = memoize(add)

memoizedAdd(1, 2, 3) // 6
memoizedAdd(1, 2, 4) // 6
```

肯定是有问题的，如果要支持多参数，我们就需要传入 hasher 函数，自定义存储的 key 值。所以我们考虑使用 JSON.stringify：

```js
var memoizedAdd = memoize(add, function(){
    var args = Array.prototype.slice.call(arguments)
    return JSON.stringify(args)
})

console.log(memoizedAdd(1, 2, 3)) // 6
console.log(memoizedAdd(1, 2, 4)) // 7
```

如果使用 JSON.stringify，参数是对象的问题也可以得到解决，因为存储的是对象序列化后的字符串。

<https://github.com/mqyqingfeng/Blog/issues/46>

# 其他函数概念

## Thunk 函数

Thunk 函数是自动执行 Generator 函数的一种方法。

### 参数的求值策略

Thunk 函数早在上个世纪 60 年代就诞生了。

那时，编程语言刚刚起步，计算机学家还在研究，编译器怎么写比较好。一个争论的焦点是 " 求值策略 "，即函数的参数到底应该何时求值。

```javascript
var x = 1;

function f(m) {
  return m * 2;
}

f(x + 5)
```

上面代码先定义函数 `f`，然后向它传入表达式 `x + 5`。请问，这个表达式应该何时求值？

一种意见是 " 传值调用 "（call by value），即在进入函数体之前，就计算 `x + 5` 的值（等于 6），再将这个值传入函数 `f`。**C 语言** 就采用这种策略。

```javascript
f(x + 5)
// 传值调用时，等同于
f(6)
```

另一种意见是“传名调用”（call by name），即直接将表达式 `x + 5` 传入函数体，只在用到它的时候求值。Haskell 语言采用这种策略。

```javascript
f(x + 5)
// 传名调用时，等同于
(x + 5) * 2
```

传值调用和传名调用，哪一种比较好？

回答是各有利弊。传值调用比较简单，但是对参数求值的时候，实际上还没用到这个参数，有可能造成性能损失。

```javascript
function f(a, b){
  return b;
}

f(3 * x * x - 2 * x - 1, x);
```

上面代码中，函数 `f` 的第一个参数是一个复杂的表达式，但是函数体内根本没用到。对这个参数求值，实际上是不必要的。因此，有一些计算机学家倾向于 " 传名调用 "，即只在执行时求值。

### Thunk 函数的含义

编译器的“传名调用”实现，往往是将参数放到一个 **临时函数** 之中，再将这个临时函数传入函数体。这个临时函数就叫做 **Thunk 函数**。

```javascript
function f(m) {
  return m * 2;
}

f(x + 5);

// 等同于

var thunk = function () {
  return x + 5;
};

function f(thunk) {
  return thunk() * 2;
}
```

上面代码中，函数 f 的参数 `x + 5` 被一个函数替换了。凡是用到原参数的地方，对 `Thunk` 函数求值即可。

这就是 Thunk 函数的定义，它是“传名调用”的一种实现策略，用来替换某个表达式。

### JavaScript 语言的 Thunk 函数

JavaScript 语言是 **传值调用**，它的 Thunk 函数含义有所不同。在 JavaScript 语言中，Thunk 函数替换的不是表达式，而是多参数函数，将其替换成一个只 **接受回调函数作为参数** 的单参数函数。

```javascript
// 正常版本的readFile（多参数版本）
fs.readFile(fileName, callback);

// Thunk版本的readFile（单参数版本）
var Thunk = function (fileName) {
  return function (callback) {
    return fs.readFile(fileName, callback);
  };
};

var readFileThunk = Thunk(fileName);
readFileThunk(callback);
```

上面代码中，`fs` 模块的 `readFile` 方法是一个多参数函数，两个参数分别为文件名和回调函数。经过转换器处理，它变成了一个单参数函数，只接受回调函数作为参数。这个单参数版本，就叫做 Thunk 函数。

> 与 curry 的区别在于最终只接受一个回调函数

任何函数，只要参数有回调函数，就能写成 Thunk 函数的形式。下面是一个简单的 Thunk 函数转换器。

```javascript
// ES5版本
var Thunk = function(fn){
  return function (){
    var args = Array.prototype.slice.call(arguments);// 转换为真实的数组，因为要使用apply方法
    return function (callback){
      args.push(callback);
      return fn.apply(this, args);
    }
  };
};

// ES6版本
const Thunk = function(fn) {
  return function (...args) {
    return function (callback) {
      return fn.call(this, ...args, callback);
    }
  };
};
```

使用上面的转换器，生成 `fs.readFile` 的 Thunk 函数。

```javascript
var readFileThunk = Thunk(fs.readFile);
readFileThunk(fileA)(callback);
```

下面是另一个完整的例子。

```javascript
function f(a, cb) {
  cb(a);
}
const ft = Thunk(f);

ft(1)(console.log) // 1
```
