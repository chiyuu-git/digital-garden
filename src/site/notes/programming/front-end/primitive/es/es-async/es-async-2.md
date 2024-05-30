---
{"aliases":[],"tags":[],"review-dates":["2023-05-02"],"dg-publish":true,"date-created":"2020-09-06-Sun, 3:54:12 pm","date-modified":"2023-05-02-Tue, 6:30:33 pm","permalink":"/programming/front-end/primitive/es/es-async/es-async-2/","dgPassFrontmatter":true}
---


# Generator 函数的异步应用

## 传统方法

- ES6 诞生以前，异步编程的方法，大概有下面四种。
  - 回调函数
  - 事件监听
  - 发布/订阅
  - Promise 对象
- Generator 函数将 JavaScript 异步编程带入了一个全新的阶段。

## 基本概念

### 异步

- 所谓 " 异步 "，简单说就是一个任务不是连续完成的，可以理解成该任务被人为分成两段，先执行第一段，然后转而执行其他任务，等做好了准备，再回过头执行第二段。
- 比如，有一个任务是读取文件进行处理，任务的第一段是向操作系统发出请求，要求读取文件。然后，程序执行其他任务，等到操作系统返回文件，再接着执行任务的第二段（处理文件）。这种不连续的执行，就叫做异步。
- 相应地，连续的执行就叫做同步。由于是连续执行，不能插入其他任务，所以操作系统从硬盘读取文件的这段时间，程序只能干等着。

### 回调函数

- JavaScript 语言对异步编程的实现，就是回调函数。所谓回调函数，就是把任务的第二段单独写在一个函数里面，等到重新执行这个任务的时候，就直接调用这个函数。回调函数的英语名字 `callback`，直译过来就是 " 重新调用 "。
- 读取文件进行处理，是这样写的。

  ```javascript
  fs.readFile('/etc/passwd', 'utf-8', function (err, data) {
    if (err) throw err;
    console.log(data);
  });
  ```

- 上面代码中，`readFile` 函数的第三个参数，就是回调函数，也就是任务的第二段。等到操作系统返回了 `/etc/passwd` 这个文件以后，回调函数才会执行。
- 一个有趣的问题是，为什么 Node 约定，回调函数的第一个参数，必须是错误对象 `err`（如果没有错误，该参数就是 `null`）#
- **原因**是执行分成两段，第一段执行完以后，任务所在的上下文环境就已经结束了。在这以后抛出的错误，原来的上下文环境已经无法捕捉，只能当作参数，传入第二段。

### Promise

- 回调函数本身并没有问题，它的问题出现在多个回调函数嵌套。假定读取 `A` 文件之后，再读取 `B` 文件，代码如下。

  ```javascript
  fs.readFile(fileA, 'utf-8', function (err, data) {
    fs.readFile(fileB, 'utf-8', function (err, data) {
      // ...
    });
  });
  ```

- 不难想象，如果依次读取两个以上的文件，就会出现多重嵌套。代码不是纵向发展，而是横向发展，很快就会乱成一团，无法管理。因为多个异步操作形成了强耦合，只要有一个操作需要修改，它的上层回调函数和下层回调函数，可能都要跟着修改。这种情况就称为 " 回调函数地狱 "（callback hell）。
- Promise 对象就是为了解决这个问题而提出的。它不是新的语法功能，而是一种新的写法，允许将回调函数的嵌套，改成链式调用。采用 Promise，连续读取多个文件，写法如下。

  ```javascript
  var readFile = require('fs-readfile-promise');
  
  readFile(fileA)
  .then(function (data) {
    console.log(data.toString());
  })
  .then(function () {
    return readFile(fileB);
  })
  .then(function (data) {
    console.log(data.toString());
  })
  .catch(function (err) {
    console.log(err);
  });
  ```

- 上面代码中，我使用了 `fs-readfile-promise` 模块，它的作用就是返回一个 Promise 版本的 `readFile` 函数。Promise 提供 `then` 方法加载回调函数，`catch` 方法捕捉执行过程中抛出的错误。
- 可以看到，Promise 的写法只是回调函数的改进，使用 `then` 方法以后，异步任务的两段执行看得更清楚了，除此以外，并无新意。
- Promise 的最大问题是代码冗余，原来的任务被 Promise 包装了一下，不管什么操作，一眼看去都是一堆 `then`，原来的语义变得很不清楚。 #
- 那么，有没有更好的写法呢？

## Generator 函数

### 协程

- 传统的编程语言，早有异步编程的解决方案（其实是多任务的解决方案）。其中有一种叫做 " 协程 "（coroutine），意思是多个线程互相协作，完成异步任务。
- 协程有点像函数，又有点像线程。它的运行流程大致如下。
  - 第一步，协程 `A` 开始执行。
  - 第二步，协程 `A` 执行到一半，进入暂停，执行权转移到协程 `B`。
  - 第三步，（一段时间后）协程 `B` 交还执行权。
  - 第四步，协程 `A` 恢复执行。
- 上面流程的协程 `A`，就是异步任务，因为它分成两段（或多段）执行。
- 举例来说，读取文件的协程写法如下。

  ```javascript
  function* asyncJob() {
    // ...其他代码
    var f = yield readFile(fileA);
    // ...其他代码
  }
  ```

- 上面代码的函数 `asyncJob` 是一个协程，它的奥妙就在其中的 `yield` 命令。它表示执行到此处，执行权将交给其他协程。也就是说，`yield` 命令是异步两个阶段的分界线。
- 协程遇到 `yield` 命令就暂停，等到执行权返回，再从暂停的地方继续往后执行。它的最大优点，就是代码的写法非常像同步操作，如果去除 `yield` 命令，简直一模一样。

### 协程的 Generator 函数实现

- Generator 函数是协程在 ES6 的实现，最大特点就是可以交出函数的执行权（即暂停执行）。
- 整个 Generator 函数就是一个封装的异步任务，或者说是异步任务的容器。异步操作需要暂停的地方，都用 `yield` 语句注明。Generator 函数的执行方法如下。

  ```javascript
  function* gen(x) {
    var y = yield x + 2;  // yield表达式的返回值是undefined
    return y;
  }
  
  var g = gen(1);
  g.next() // { value: 3, done: false }
  g.next() // { value: undefined, done: true }
  ```

- 上面代码中，调用 Generator 函数，会返回一个内部指针（即遍历器）`g`。调用指针 `g` 的 `next` 方法，会移动内部指针（即执行异步任务的第一段），指向第一个遇到的 `yield` 语句，上例是执行到 `x + 2` 为止。
- 换言之，`next` 方法的作用是分阶段执行 `Generator` 函数。每次调用 `next` 方法，会返回一个对象，表示当前阶段的信息（`value` 属性和 `done` 属性）。`value` 属性是 `yield` 语句后面表达式的值，表示当前阶段的值；`done` 属性是一个布尔值，表示 Generator 函数是否执行完毕，即是否还有下一个阶段。

### Generator 函数的数据交换和错误处理

- Generator 函数可以暂停执行和恢复执行，这是它能封装异步任务的根本原因。除此之外，它还有两个特性，使它可以作为异步编程的完整解决方案：函数体内外的数据交换和错误处理机制。
- `next` 返回值的 value 属性，是 Generator 函数向外输出数据；`next` 方法还可以接受参数，向 Generator 函数体内输入数据。

  ```javascript
  function* gen(x){
    var y = yield x + 2;
    return y;
  }
  
  var g = gen(1);
  g.next() // { value: 3, done: false }
  g.next(2) // { value: 2, done: true }
  ```

- 上面代码中，第一个 `next` 方法的 `value` 属性，返回表达式 `x + 2` 的值 `3`。第二个 `next` 方法带有参数 `2`，这个参数可以传入 Generator 函数，作为上个阶段异步任务的返回结果，被函数体内的变量 `y` 接收。因此，这一步的 `value` 属性，返回的就是 `2`（变量 `y` 的值）。
- Generator 函数内部还可以部署错误处理代码，捕获函数体外抛出的错误。

  ```javascript
  function* gen(x){
    try {
      var y = yield x + 2;
    } catch (e){
      console.log(e);
    }
    return y;
  }
  
  var g = gen(1);
  g.next();
  g.throw('出错了');
  // 出错了
  ```

- 上面代码的最后一行，Generator 函数体外，使用指针对象的 `throw` 方法抛出的错误，可以被函数体内的 `try...catch` 代码块捕获。这意味着，出错的代码与处理错误的代码，实现了时间和空间上的分离，这对于异步编程无疑是很重要的。 #

### 异步任务的封装

- 下面看看如何使用 Generator 函数，执行一个真实的异步任务。

  ```javascript
  var fetch = require('node-fetch');
  
  function* gen(){
    var url = 'https://api.github.com/users/github';
    var result = yield fetch(url);
    console.log(result.bio);
  }
  ```

- 上面代码中，Generator 函数封装了一个异步操作，该操作先读取一个远程接口，然后从 JSON 格式的数据解析信息。就像前面说过的，这段代码非常像同步操作，除了加上了 `yield` 命令。
- 执行这段代码的方法如下。

  ```javascript
  var g = gen();
  var result = g.next();
  
  result.value.then(function(data){
    return data.json();
  }).then(function(data){
    g.next(data);
  });
  ```

- 上面代码中，首先执行 Generator 函数，获取遍历器对象，然后使用 `next` 方法（第二行），执行异步任务的第一阶段。由于 `Fetch` 模块返回的是一个 Promise 对象，因此要用 `then` 方法调用下一个 `next` 方法。
- 可以看到，虽然 Generator 函数将异步操作表示得很简洁，但是流程管理却不方便（即何时执行第一阶段、何时执行第二阶段）。

## Thunk 函数 （意义不明，还不如直接使用递归解释）

- Thunk 函数是自动执行 Generator 函数的一种方法。

### Thunk 函数的含义

- 编译器的“传名调用”实现，往往是将参数放到一个**临时函数**之中，再将这个临时函数传入函数体。这个临时函数就叫做 **Thunk 函数**。

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

- 上面代码中，函数 f 的参数 `x + 5` 被一个函数替换了。凡是用到原参数的地方，对 `Thunk` 函数求值即可。
- 这就是 Thunk 函数的定义，它是“传名调用”的一种实现策略，用来替换某个表达式。

### JavaScript 语言的 Thunk 函数

- JavaScript 语言是**传值调用**，它的 Thunk 函数含义有所不同。在 JavaScript 语言中，Thunk 函数替换的不是表达式，而是多参数函数，将其替换成一个只**接受回调函数作为参数**的单参数函数。

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

- 上面代码中，`fs` 模块的 `readFile` 方法是一个多参数函数，两个参数分别为文件名和回调函数。经过转换器处理，它变成了一个单参数函数，只接受回调函数作为参数。这个单参数版本，就叫做 Thunk 函数。

  > 与 curry 的区别在于最终只接受一个回调函数

- 任何函数，只要参数有回调函数，就能写成 Thunk 函数的形式。下面是一个简单的 Thunk 函数转换器。

  ```javascript
  const Thunk = function(fn) {
    return function (...args) {
      return function (callback) {
        return fn.call(this, ...args, callback);
      }
    };
  };
  ```

- 使用上面的转换器，生成 `fs.readFile` 的 Thunk 函数。

  ```javascript
  var readFileThunk = Thunk(fs.readFile);
  readFileThunk(fileA)(callback);
  ```

- 下面是另一个完整的例子。

  ```javascript
  function f(a, cb) {
    cb(a);
  }
  const ft = Thunk(f);
  
  ft(1)(console.log) // 1
  ```

### Thunkify 模块

- 生产环境的转换器，建议使用 Thunkify 模块。
- 首先是安装。

  ```bash
  $ npm install thunkify
  ```

- 使用方式如下。

  ```javascript
  var thunkify = require('thunkify');
  var fs = require('fs');
  
  var read = thunkify(fs.readFile);
  read('package.json')(function(err, str){
    // ...
  });
  ```

- Thunkify 的源码与上一节那个简单的转换器非常像。

  ```javascript
  function thunkify(fn) {
    return function() {
      var args = new Array(arguments.length);
      var ctx = this;
  
      for (var i = 0; i < args.length; ++i) {
        args[i] = arguments[i];
      }
  
      return function (done) {
        var called;
  
        args.push(function () {
          // 如果执行过了就推出
          if (called) return;
          called = true;
          done.apply(null, arguments);
        });
  
        try {
          fn.apply(ctx, args);
        } catch (err) {
          done(err);
        }
      }
    }
  };
  ```

- 它的源码主要多了一个检查机制，变量 `called` 确保回调函数只运行一次。这样的设计与下文的 Generator 函数相关。请看下面的例子。

  ```javascript
  function f(a, b, callback){
    var sum = a + b;
    callback(sum);
    callback(sum);
  }
  
  var ft = thunkify(f);
  var print = console.log.bind(console);
  ft(1, 2)(print);
  // 3
  ```

- 上面代码中，由于 `thunkify` 只允许回调函数执行一次，所以只输出一行结果。

### Generator 函数的流程管理

- 你可能会问， Thunk 函数有什么用？回答是以前确实没什么用，但是 ES6 有了 Generator 函数，Thunk 函数现在可以用于 Generator 函数的自动流程管理。
- Generator 函数可以自动执行。

  ```javascript
  function* gen() {
    // ...
  }
  
  var g = gen();
  var res = g.next();
  
  while(!res.done){
    console.log(res.value);
    res = g.next();
  }
  ```

- 上面代码中，Generator 函数 `gen` 会自动执行完所有步骤。
- 但是，这不适合异步操作。如果必须保证前一步执行完，才能执行后一步，上面的自动执行就不可行。这时，Thunk 函数就能派上用处。以读取文件为例。下面的 Generator 函数封装了两个异步操作。

  ```javascript
  var fs = require('fs');
  var thunkify = require('thunkify');
  var readFileThunk = thunkify(fs.readFile);
  
  var gen = function* (){
    var r1 = yield readFileThunk('/etc/fstab');  //在thunk函数里，把value传给了next方法，r1赋值，同时交还执行权
    console.log(r1.toString());
    var r2 = yield readFileThunk('/etc/shells');
    console.log(r2.toString());
  };
  ```

- 上面代码中，`yield` 命令用于将程序的执行权移出 Generator 函数，那么就需要一种方法，将执行权再交还给 Generator 函数。
- 这种方法就是 Thunk 函数，因为它可以**在回调函数里，将执行权交还给 Generator 函数**。为了便于理解，我们先看如何手动执行上面这个 Generator 函数。

  ```javascript
  var g = gen();
  
  var r1 = g.next();
  r1.value(function (err, data) {
    if (err) throw err;
    var r2 = g.next(data);
    r2.value(function (err, data) {
      if (err) throw err;
      g.next(data);
    });
  });
  ```

- 上面代码中，变量 `g` 是 Generator 函数的内部指针，表示目前执行到哪一步。`next` 方法负责将指针移动到下一步，并返回该步的信息（`value` 属性和 `done` 属性）。
- 仔细查看上面的代码，可以发现 Generator 函数的执行过程，其实是将同一个回调函数，反复传入 `next` 方法的 `value` 属性。这使得我们可以用递归来自动完成这个过程。 #

### Thunk 函数的自动流程管理

- Thunk 函数真正的威力，在于可以自动执行 Generator 函数。下面就是一个基于 Thunk 函数的 Generator 执行器。

  ```javascript
  function run(fn) {
    var gen = fn();
  
    function next(err, data) {
      var result = gen.next(data);
      if (result.done) return;
      result.value(next);
    }
  
    next();
  }
  
  function* g() {
    // ...
  }
  
  run(g);
  ```

- 上面代码的 `run` 函数，就是一个 Generator 函数的自动执行器。**内部的 `next` 函数就是 Thunk 的回调函数**。`next` 函数先将指针移到 Generator 函数的下一步（`gen.next` 方法），然后判断 Generator 函数是否结束（`result.done` 属性），如果没结束，就将 `next` 函数再传入 Thunk 函数（`result.value` 属性），否则就直接退出。
- 有了这个执行器，执行 Generator 函数方便多了。不管内部有多少个异步操作，直接把 Generator 函数传入 `run` 函数即可。当然，前提是每一个异步操作，都要是 Thunk 函数，也就是说，跟在 `yield` 命令后面的必须是 Thunk 函数。

  ```javascript
  var g = function* (){
    var f1 = yield readFileThunk('fileA');
    var f2 = yield readFileThunk('fileB');
    // ...
    var fn = yield readFileThunk('fileN');
  };
  
  run(g);
  ```

- 上面代码中，函数 `g` 封装了 `n` 个异步的读取文件操作，只要执行 `run` 函数，这些操作就会自动完成。这样一来，异步操作不仅可以写得像同步操作，而且一行代码就可以执行。
- Thunk 函数并不是 Generator 函数自动执行的唯一方案。**因为自动执行的关键是，必须有一种机制，自动控制 Generator 函数的流程，接收和交还程序的执行权。回调函数可以做到这一点，Promise 对象也可以做到这一点**。

## Co 模块

### 基本用法

- [co 模块](https://github.com/tj/co) 是著名程序员 TJ Holowaychuk 于 2013 年 6 月发布的一个小工具，用于 Generator 函数的自动执行。
- 下面是一个 Generator 函数，用于依次读取两个文件。

  ```javascript
  var gen = function* () {
    var f1 = yield readFile('/etc/fstab');
    var f2 = yield readFile('/etc/shells');
    console.log(f1.toString());
    console.log(f2.toString());
  };
  ```

- co 模块可以让你不用编写 Generator 函数的执行器。

  ```javascript
  var co = require('co');
  co(gen);
  ```

- 上面代码中，Generator 函数只要传入 `co` 函数，就会自动执行。
- `co` 函数返回一个 `Promise` 对象，因此可以用 `then` 方法添加回调函数。

  ```javascript
  co(gen).then(function (){
    console.log('Generator 函数执行完成');
  });
  ```

- 上面代码中，等到 Generator 函数执行结束，就会输出一行提示。

### Co 模块的原理

- 为什么 co 可以自动执行 Generator 函数？
- 前面说过，Generator 就是一个异步操作的容器。它的自动执行需要一种机制，当异步操作有了结果，能够自动交回执行权。
- 两种方法可以做到这一点。 #

  （1）回调函数。将异步操作包装成 Thunk 函数，在回调函数里面交回执行权。

  （2）Promise 对象。将异步操作包装成 Promise 对象，用 `then` 方法交回执行权。

- co 模块其实就是将两种自动执行器（Thunk 函数和 Promise 对象），包装成一个模块。使用 co 的前提条件是，Generator 函数的 `yield` 命令后面，只能是 Thunk 函数或 Promise 对象。如果数组或对象的成员，全部都是 Promise 对象，也可以使用 co，详见后文的例子。
- 上一节已经介绍了基于 Thunk 函数的自动执行器。下面来看，基于 Promise 对象的自动执行器。这是理解 co 模块必须的。

### 基于 Promise 对象的自动执行

- 还是沿用上面的例子。首先，把 `fs` 模块的 `readFile` 方法包装成一个 Promise 对象。

  ```javascript
  var fs = require('fs');
  
  var readFile = function (fileName){
    return new Promise(function (resolve, reject){
      fs.readFile(fileName, function(error, data){
        if (error) return reject(error);
        resolve(data);
      });
    });
  };
  
  var gen = function* (){
    var f1 = yield readFile('/etc/fstab');
    var f2 = yield readFile('/etc/shells');
    console.log(f1.toString());
    console.log(f2.toString());
  };
  ```

- 然后，手动执行上面的 Generator 函数。

  ```javascript
  var g = gen();
  // value值才是promise
  g.next().value.then(function(data){
    g.next(data).value.then(function(data){
      g.next(data);
    });
  });
  ```

- 手动执行其实就是用 `then` 方法，层层添加回调函数。理解了这一点，就可以写出一个自动执行器。
 ```javascript
  function run(gen){
    var g = gen();
  
    function next(data){
      var result = g.next(data);
      if (result.done) return result.value;
      result.value.then(function(data){
        next(data);
      });
    }
  
    next();
  }
  
  run(gen);
  ```
- 上面代码中，只要 Generator 函数还没执行到最后一步，`next` 函数就调用自身，以此实现自动执行。

### Co 模块的源码

- co 就是上面那个自动执行器的扩展，它的源码只有几十行，非常简单。
- 首先，co 函数接受 Generator 函数作为参数，返回一个 Promise 对象。

  ```javascript
  function co(gen) {
    var ctx = this;
  
    return new Promise(function(resolve, reject) {
    });
  }
  ```

- 在返回的 Promise 对象里面，co 先检查参数 `gen` 是否为 Generator 函数。如果是，就执行该函数，得到一个内部指针对象；如果不是就返回，并将 Promise 对象的状态改为 `resolved`。

  ```javascript
  function co(gen) {
    var ctx = this;
  
    return new Promise(function(resolve, reject) {
      if (typeof gen === 'function') gen = gen.call(ctx);
      if (!gen || typeof gen.next !== 'function') return resolve(gen);
    });
  }
  ```

- 接着，co 将 Generator 函数的内部指针对象的 `next` 方法，包装成 `onFulfilled` 函数。这主要是为了能够**捕捉抛出的错误**。

  ```javascript
  function co(gen) {
    var ctx = this;
  
    return new Promise(function(resolve, reject) {
      if (typeof gen === 'function') gen = gen.call(ctx);
      if (!gen || typeof gen.next !== 'function') return resolve(gen);
  
      onFulfilled();
      function onFulfilled(res) {
        var ret;
        try {
          ret = gen.next(res);
        } catch (e) {
          return reject(e);
        }
        next(ret);
      }
    });
  }
  ```

- 最后，就是关键的 `next` 函数，它会反复调用自身。

  ```javascript
  function next(ret) {
    if (ret.done) return resolve(ret.value);
    var value = toPromise.call(ctx, ret.value);
    if (value && isPromise(value)) return value.then(onFulfilled, onRejected);
    return onRejected(
      new TypeError(
        'You may only yield a function, promise, generator, array, or object, '
        + 'but the following object was passed: "'
        + String(ret.value)
        + '"'
      )
    );
  }
  ```

- 上面代码中，`next` 函数的内部代码，一共只有四行命令。
- 第一行，检查当前是否为 Generator 函数的最后一步，如果是就返回。
- 第二行，确保每一步的返回值，是 Promise 对象。
- 第三行，使用 `then` 方法，为返回值加上回调函数，然后通过 `onFulfilled` 函数再次调用 `next` 函数。
- 第四行，在参数不符合要求的情况下（参数非 Thunk 函数和 Promise 对象），将 Promise 对象的状态改为 `rejected`，从而终止执行。

### 处理并发的异步操作

- co 支持并发的异步操作，即允许某些操作同时进行，等到它们全部完成，才进行下一步。
- 这时，要把**并发的操作都放在数组或对象里面**，跟在 `yield` 语句后面。

  ```javascript
  // 数组的写法
  co(function* () {
    var res = yield [
      Promise.resolve(1),
      Promise.resolve(2)
    ];
    console.log(res);
  }).catch(onerror);
  
  // 对象的写法
  co(function* () {
    var res = yield {
      1: Promise.resolve(1),
      2: Promise.resolve(2),
    };
    console.log(res);
  }).catch(onerror);
  ```

- 下面是另一个例子。

  ```javascript
  co(function* () {
    var values = [n1, n2, n3];
    yield values.map(somethingAsync);
  });
  
  function* somethingAsync(x) {
    // do something async
    return y
  }
  ```

- 上面的代码允许并发三个 `somethingAsync` 异步操作，等到它们全部完成，才会进行下一步。

### 实例：处理 Stream

- Node 提供 Stream 模式读写数据，特点是一次只处理数据的一部分，数据分成一块块依次处理，就好像“数据流”一样。这对于处理大规模数据非常有利。Stream 模式使用 EventEmitter API，会释放三个事件。
  - `data` 事件：下一块数据块已经准备好了。
  - `end` 事件：整个“数据流”处理完了。
  - `error` 事件：发生错误。
- 使用 `Promise.race()` 函数，可以判断这三个事件之中哪一个最先发生，只有当 `data` 事件最先发生时，才进入下一个数据块的处理。从而，我们可以通过一个 `while` 循环，完成所有数据的读取。

  ```javascript
  const co = require('co');
  const fs = require('fs');
  
  const stream = fs.createReadStream('./les_miserables.txt');
  let valjeanCount = 0;
  
  co(function*() {
    while(true) {
      const res = yield Promise.race([
        new Promise(resolve => stream.once('data', resolve)),
        new Promise(resolve => stream.once('end', resolve)),
        new Promise((resolve, reject) => stream.once('error', reject))
      ]);
      if (!res) {
        break;
      }
      stream.removeAllListeners('data');
      stream.removeAllListeners('end');
      stream.removeAllListeners('error');
      valjeanCount += (res.toString().match(/valjean/ig) || []).length;
    }
    console.log('count:', valjeanCount); // count: 1120
  });
  ```

- 上面代码采用 Stream 模式读取《悲惨世界》的文本文件，对于每个数据块都使用 `stream.once` 方法，在 `data`、`end`、`error` 三个事件上添加一次性回调函数。变量 `res` 只有在 `data` 事件发生时才有值，然后累加每个数据块之中 `valjean` 这个词出现的次数。

# Async 函数

## 含义

ES2017 标准引入了 async 函数，使得异步操作变得更加方便。

async 函数是什么？一句话，它就是 **Generator 函数的语法糖**。

前文有一个 Generator 函数，依次读取两个文件。

  ```javascript
  const fs = require('fs');
  
  const readFile = function (fileName) {
    return new Promise(function (resolve, reject) {
      fs.readFile(fileName, function(error, data) {
        if (error) return reject(error);
        resolve(data);
      });
    });
  };
  
  const gen = function* () {
    const f1 = yield readFile('/etc/fstab');
    const f2 = yield readFile('/etc/shells');
    console.log(f1.toString());
    console.log(f2.toString());
  };
  ```

上面代码的函数 `gen` 可以写成 `async` 函数，就是下面这样。

  ```javascript
  const asyncReadFile = async function () {
    const f1 = await readFile('/etc/fstab');
    const f2 = await readFile('/etc/shells');
    console.log(f1.toString());
    console.log(f2.toString());
  };
  ```

一比较就会发现，`async` 函数就是将 Generator 函数的星号（`*`）替换成 `async`，将 `yield` 替换成 `await`，仅此而已。

`async` 函数对 Generator 函数的改进，体现在以下四点。

### （1）内置执行器

Generator 函数的执行必须靠执行器，所以才有了 `co` 模块，而 `async` 函数自带执行器。也就是说，`async` 函数的执行，与普通函数一模一样，只要一行。

  ```javascript
  asyncReadFile();
  ```

上面的代码调用了 `asyncReadFile` 函数，然后它就会自动执行，输出最后结果。这完全不像 Generator 函数，需要调用 `next` 方法，或者用 `co` 模块，才能真正执行，得到最后结果。

### （2）更好的语义

`async` 和 `await`，比起星号和 `yield`，语义更清楚了。`async` 表示函数里有异步操作，`await` 表示紧跟在后面的表达式需要等待结果。

### （3）更广的适用性

`co` 模块约定，`yield` 命令后面只能是 Thunk 函数或 Promise 对象，而 `async` 函数的 `await` 命令后面，可以是 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时会自动转成立即 resolved 的 Promise 对象）。

### （4）返回值是 Promise

`async` 函数的返回值是 Promise 对象，这比 Generator 函数的返回值是 Iterator 对象方便多了。你可以用 `then` 方法指定下一步的操作。

进一步说，`async` 函数完全可以看作多个异步操作，包装成的一个 Promise 对象，而 `await` 命令就是内部 `then` 命令的语法糖。

## 基本用法

`async` 函数返回一个 Promise 对象，可以使用 `then` 方法添加回调函数。当函数执行的时候，一旦遇到 `await` 就会先返回，等到异步操作完成，再接着执行函数体内后面的语句。

下面是一个例子，指定多少毫秒后输出一个值。

  ```javascript
  function timeout(ms) {
    return new Promise((resolve) => {
      setTimeout(resolve, ms);
    });
  }
  
  async function asyncPrint(value, ms) {
    await timeout(ms);
    console.log(value);
  }
  
  asyncPrint('hello world', 50);
  ```

上面代码指定 50 毫秒以后，输出 `hello world`。

由于 `async` 函数返回的是 Promise 对象，可以作为 `await` 命令的参数。所以，上面的例子也可以写成下面的形式。

```javascript
  async function timeout(ms) {
    await new Promise((resolve) => {
      setTimeout(resolve, ms);
    });
  }
  
  async function asyncPrint(value, ms) {
    await timeout(ms);
    console.log(value);
  }
  
  asyncPrint('hello world', 50);
```

async 函数有多种使用形式。

```javascript
  // 函数声明
  async function foo() {}
  
  // 函数表达式
  const foo = async function () {};
  
  // 对象的方法
  let obj = { async foo() {} };
  obj.foo().then(...)
  
  // Class 的方法
  class Storage {
    constructor() {
      this.cachePromise = caches.open('avatars');
    }
  
    async getAvatar(name) {
      const cache = await this.cachePromise;
      return cache.match(`/avatars/${name}.jpg`);
    }
  }
  
  const storage = new Storage();
  storage.getAvatar('jake').then(…);
  
  // 箭头函数
  const foo = async () => {};
```

## 语法

`async` 函数的语法规则总体上比较简单，难点是错误处理机制。

### 返回 Promise 对象

`async` 函数返回一个 Promise 对象。

`async` 函数内部 `return` 语句返回的值，会成为 `then` 方法回调函数的参数。

  ```javascript
  async function f() {
    return 'hello world';
  }
  
  f().then(v => console.log(v))
  // "hello world"
  ```

上面代码中，函数 `f` 内部 `return` 命令返回的值，会被 `then` 方法回调函数接收到。

`async` 函数内部抛出错误，会导致返回的 Promise 对象变为 `reject` 状态。抛出的错误对象会被 `catch` 方法回调函数接收到。

  ```javascript
  async function f() {
    throw new Error('出错了');
  }
  
  f().then(
    v => console.log(v),
    e => console.log(e)
  )
  // Error: 出错了
  ```

### Promise 对象的状态变化

`async` 函数返回的 Promise 对象，必须等到内部所有 `await` 命令后面的 Promise 对象执行完，才会发生状态改变，除非遇到 `return` 语句或者抛出错误。也就是说，只有 `async` 函数内部的异步操作执行完，才会执行 `then` 方法指定的回调函数

下面是一个例子。

  ```javascript
  async function getTitle(url) {
    let response = await fetch(url);
    let html = await response.text();
    return html.match(/<title>([\s\S]+)<\/title>/i)[1];
  }
  getTitle('https://tc39.github.io/ecma262/').then(console.log)
  // "ECMAScript 2017 Language Specification"
  ```

上面代码中，函数 `getTitle` 内部有三个操作：抓取网页、取出文本、匹配页面标题。只有这三个操作全部完成，才会执行 `then` 方法里面的 `console.log`。

### Await 命令

正常情况下，`await` 命令后面是一个 Promise 对象，返回该对象的结果。如果不是 Promise 对象，就直接返回对应的值。

  ```javascript
  async function f() {
    // 等同于
    // return 123;
    return await 123;
  }
  
  f().then(v => console.log(v))
  // 123
  ```

上面代码中，`await` 命令的参数是数值 `123`，这时等同于 `return 123`。

**另一种情况是**，`await` 命令后面是一个 `thenable` 对象（即定义 `then` 方法的对象），那么 `await` 会将其等同于 Promise 对象。

  ```javascript
  class Sleep {
    constructor(timeout) {
      this.timeout = timeout;
    }
    then(resolve, reject) {
      const startTime = Date.now();
      setTimeout(
        () => resolve(Date.now() - startTime),
        this.timeout
      );
    }
  }
  
  (async () => {
    const actualTime = await new Sleep(1000);
    console.log(actualTime);
  })();
  ```

上面代码中，`await` 命令后面是一个 `Sleep` 对象的实例。这个实例不是 Promise 对象，但是因为定义了 `then` 方法，`await` 会将其视为 `Promise` 处理。

await 命令返回一个 promise 的意义是什么呢？##

### 错误处理

`await` 命令后面的 Promise 对象如果变为 `reject` 状态，则 `reject` 的参数会被 `catch` 方法的回调函数接收到。

```javascript
  async function f() {
    await Promise.reject('出错了');
  }
  
  f()
  .then(v => console.log(v))
  .catch(e => console.log(e))
  // 出错了
```

注意，上面代码中，`await` 语句前面**没有 `return`，但是**`reject` 方法的参数依然传入了 `catch` 方法的回调函数。这里如果在 `await` 前面加上 `return`，效果是一样的。

任何一个 `await` 语句后面的 Promise 对象变为 `reject` 状态，那么整个 `async` 函数都会**中断执行**。

```javascript
  async function f() {
    await Promise.reject('出错了');
    await Promise.resolve('hello world'); // 不会执行
  }
```

上面代码中，第二个 `await` 语句是不会执行的，因为第一个 `await` 语句状态变成了 `reject`。

有时，我们希望即使前一个异步操作失败，也不要中断后面的异步操作。这时可以将第一个 `await` 放在 `try...catch` 结构里面，这样不管这个异步操作是否成功，第二个 `await` 都会执行。

```javascript
  async function f() {
    try {
      await Promise.reject('出错了');
    } catch(e) {
    }
    return await Promise.resolve('hello world');
  }
  
  f()
  .then(v => console.log(v))
  // hello world
```

另一种方法是 `await` 后面的 Promise 对象再跟一个 `catch` 方法，处理前面可能出现的错误。

```javascript
  async function f() {
    await Promise.reject('出错了')
      .catch(e => console.log(e));
    return await Promise.resolve('hello world');
  }
  
  f()
  .then(v => console.log(v))
  // 出错了
  // hello world
```

如果有多个 `await` 命令，可以统一放在 `try...catch` 结构中。

```javascript
  async function main() {
    try {
      const val1 = await firstStep();
      const val2 = await secondStep(val1);
      const val3 = await thirdStep(val1, val2);
  
      console.log('Final: ', val3);
    }
    catch (err) {
      console.error(err);
    }
  }
```

下面的例子使用 `try...catch` 结构，实现多次重复尝试。

```javascript
  const superagent = require('superagent');
  const NUM_RETRIES = 3;
  
  async function test() {
    let i;
    for (i = 0; i < NUM_RETRIES; ++i) {
      try {
        await superagent.get('http://google.com/this-throws-an-error');
        break;
      } catch(err) {}
    }
    console.log(i); // 3
  }
  
  test();
```

上面代码中，如果 `await` 操作成功，就会使用 `break` 语句退出循环；如果失败，会被 `catch` 语句捕捉，然后进入下一轮循环。

### 使用范例

#### 错误捕获

第一点，前面已经说过，`await` 命令后面的 `Promise` 对象，运行结果可能是 `rejected`，所以最好把 `await` 命令放在 `try...catch` 代码块中。

  ```javascript
  async function myFunction() {
    try {
      await somethingThatReturnsAPromise();
    } catch (err) {
      console.log(err);
    }
  }
  
  // 另一种写法
  
  async function myFunction() {
    await somethingThatReturnsAPromise()
    .catch(function (err) {
      console.log(err);
    });
  }
  ```

#### 并行触发

第二点，多个 `await` 命令后面的异步操作，如果不存在继发关系，最好让它们同时触发。

```javascript
  let foo = await getFoo();
  let bar = await getBar();
```

上面代码中，`getFoo` 和 `getBar` 是两个独立的异步操作（即互不依赖），被写成继发关系。这样比较耗时，因为只有 `getFoo` 完成以后，才会执行 `getBar`，完全可以让它们同时触发。

```javascript
  // 写法一
  let [foo, bar] = await Promise.all([getFoo(), getBar()]);
  
  // 写法二
  // 先调用，取得结果后返回
  let fooPromise = getFoo();
  let barPromise = getBar();
  let foo = await fooPromise;
  let bar = await barPromise;
```

上面两种写法，`getFoo` 和 `getBar` 都是同时触发，这样就会缩短程序的执行时间。

#### Await 命令只能用在 Async 函数之中

第三点，`await` 命令只能用在 `async` 函数之中，如果用在普通函数，就会报错。

```javascript
  async function dbFuc(db) {
    let docs = [{}, {}, {}];
  
    // 报错
    docs.forEach(function (doc) {
      await db.post(doc);
    });
  }
```

上面代码会报错，因为 `await` 用在普通函数之中了。但是，如果将 `forEach` 方法的参数改成 `async` 函数，也有问题。

```javascript
  function dbFuc(db) { //这里不需要 async
    let docs = [{}, {}, {}];
  
    // 可能得到错误结果
    docs.forEach(async function (doc) {
      await db.post(doc);
    });
  }
```

上面代码可能不会正常工作，原因是这时三个 `db.post` 操作将是**并发执行**，也就是同时执行，而不是继发执行。正确的写法是采用 `for` 循环。

```javascript
  async function dbFuc(db) {
    let docs = [{}, {}, {}];
  
    for (let doc of docs) {
      await db.post(doc);
    }
  }
```

如果确实希望多个请求并发执行，可以使用 `Promise.all` 方法。当三个请求都会 `resolved` 时，下面两种写法效果相同。

  ```javascript
  async function dbFuc(db) {
    let docs = [{}, {}, {}];
    let promises = docs.map((doc) => db.post(doc));
  
    let results = await Promise.all(promises);
    console.log(results);
  }
  
  // 或者使用下面的写法
  
  async function dbFuc(db) {
    let docs = [{}, {}, {}];
    let promises = docs.map((doc) => db.post(doc));
  
    let results = [];
    for (let promise of promises) {
      results.push(await promise);
    }
    console.log(results);
  }
   
```

#### 顶层 Await

目前，[`esm`](https://www.npmjs.com/package/esm) 模块加载器支持顶层 `await`，即 `await` 命令可以不放在 async 函数里面，直接使用。

```javascript
  // async 函数的写法
  const start = async () => {
    const res = await fetch('google.com');
    return res.text();
  };
  
  start().then(console.log);
  
  // 顶层 await 的写法
  const res = await fetch('google.com');
  console.log(await res.text());
```

上面代码中，第二种写法的脚本必须使用 `esm` 加载器，才会生效。

#### 保留运行堆栈

第四点，async 函数可以**保留运行堆栈**。

```javascript
  const a = () => {
    b().then(() => c());
  };
```

上面代码中，函数 `a` 内部运行了一个异步任务 `b()`。当 `b()` 运行的时候，函数 `a()` 不会中断，而是继续执行。等到 `b()` 运行结束，可能 `a()` 早就运行结束了，`b()` 所在的上下文环境已经消失了。如果 `b()` 或 `c()` 报错，错误堆栈将不包括 `a()`。

现在将这个例子改成 `async` 函数。

  ```javascript
  const a = async () => {
    await b();
    c();
  };
  ```

上面代码中，`b()` 运行的时候，`a()` 是**暂停执行**，上下文环境都保存着。一旦 `b()` 或 `c()` 报错，错误堆栈将包括 `a()`。

## Async 函数的实现原理

async 函数的实现原理，就是将 **Generator 函数和自动执行器**，包装在一个函数里。

```javascript
  async function fn(args) {
    // ...
  }
  
  // 等同于
  
  function fn(args) {
    return spawn(function* () {
      // ...
    });
  }
```

所有的 `async` 函数都可以写成上面的第二种形式，其中的 `spawn` 函数就是自动执行器

下面给出 `spawn` 函数的实现，基本就是前文自动执行器的翻版。

```javascript
  function spawn(genF) {
    // 接受一个Generator函数，返回一个Promise对象
    return new Promise(function(resolve, reject) {
      // 获取指针对象
      const gen = genF();
      // step函数，用于不断调用指针对象的next方法
      // 它接受一个函数作为参数，函数返回指针对象next方法的执行结果
      // 即：{value，done}
      // 第一次调用next方法，启动Generator函数的指针
      step(function() { return gen.next(undefined); });
      function step(nextF) {
        let next;
        try {
          // 函数的返回结果：{value,done}
          next = nextF();
        } catch(e) {
          return reject(e);
        }
        if(next.done) {
          // 如果done 为true，指针已经到了底部
          return resolve(next.value);
        }
        // 指针未到底部，继续执行next方法
        // 使用Promise.resolve，传入next.value
        // 获得一个promise 对象Promise.resolve(next.value)，状态为resolved
        // then 方法自动执行，把获取到的value传递给next方法，作为上一次yield的返回值
        Promise.resolve(next.value).then(function(v) {
          step(function() { return gen.next(v); });
        }, function(e) {
          step(function() { return gen.throw(e); });
        });
      }
    });
  }
```

## 与其他异步处理方法的比较

我们通过一个例子，来看 async 函数与 Promise、Generator 函数的比较。

假定某个 DOM 元素上面，部署了一系列的动画，前一个动画结束，才能开始后一个。如果当中有一个动画出错，就不再往下执行，返回上一个成功执行的动画的返回值。

### Promise 写法

```js
  function chainAnimationsPromise(elem, animations) {
  
    // 变量ret用来保存上一个动画的返回值
    let ret = null;
  
    // 新建一个空的Promise
    let p = Promise.resolve();
  
    // 使用then方法，添加所有动画
    for(let anim of animations) {
      p = p.then(function(val) {
        ret = val;
        return anim(elem);
      });
    }
  
    // 返回一个部署了错误捕捉机制的Promise
    return p.catch(function(e) {
      /* 忽略错误，继续执行 */
    }).then(function() {
      return ret;
    });
  
  }
```

虽然 Promise 的写法比回调函数的写法大大改进，但是一眼看上去，代码完全都是 Promise 的 API（`then`、`catch` 等等），操作本身的语义反而不容易看出来。

### Generator 函数的写法

```javascript
  function chainAnimationsGenerator(elem, animations) {
  
    return spawn(function*() {
      let ret = null;
      try {
        for(let anim of animations) {
          ret = yield anim(elem);
        }
      } catch(e) {
        /* 忽略错误，继续执行 */
      }
      return ret;
    });
  
  }
```

上面代码使用 Generator 函数遍历了每个动画，语义比 Promise 写法更清晰，用户定义的操作全部都出现在 `spawn` 函数的内部。这个写法的问题在于，必须有一个任务运行器，自动执行 Generator 函数，上面代码的 `spawn` 函数就是自动执行器，它返回一个 Promise 对象，而且必须保证 `yield` 语句后面的表达式，必须返回一个 Promise。

这种写法其实就是 async 函数实现原理的 Generator 函数形式的体现

### Async 函数的写法

```javascript
  async function chainAnimationsAsync(elem, animations) {
    let ret = null;
    try {
      for(let anim of animations) {
        ret = await anim(elem);
      }
    } catch(e) {
      /* 忽略错误，继续执行 */
    }
    return ret;
  }
```

可以看到 Async 函数的实现最简洁，最符合语义，几乎没有语义不相关的代码。它将 **Generator 写法中的自动执行器，改在语言层面提供**，不暴露给用户，因此代码量最少。如果使用 Generator 写法，自动执行器需要用户自己提供。

## 串行与并行异步操作 @@@

实际开发中，经常遇到一组异步操作，需要按照顺序完成。比如，依次远程读取一组 URL，然后按照读取的顺序输出结果。

### Promise 写法

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

### Async 函数实现

这种写法不太直观，可读性比较差。下面是 async 函数实现。

  ```javascript
  async function logInOrder(urls) {
    for (const url of urls) {
      const response = await fetch(url);
      console.log(await response.text());
    }
  }
  ```

上面代码确实大大简化，问题是所有远程操作都是继发。只有前一个 URL 返回结果，才会去读取下一个 URL，这样做效率很差，非常浪费时间。我们需要的是**并发**发出远程请求。

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

## 异步遍历器 #

《遍历器》一章说过，Iterator 接口是一种数据遍历的协议，只要调用遍历器对象的 `next` 方法，就会得到一个对象，表示当前遍历指针所在的那个位置的信息。`next` 方法返回的对象的结构是 `{value, done}`，其中 `value` 表示当前的数据的值，`done` 是一个布尔值，表示遍历是否结束。

这里隐含着一个规定，`next` 方法必须是同步的，只要调用就必须立刻返回值。也就是说，一旦执行 `next` 方法，就必须同步地得到 `value` 和 `done` 这两个属性。如果遍历指针正好指向同步操作，当然没有问题，但对于异步操作，就不太合适了。目前的解决方法是，Generator 函数里面的异步操作，返回一个 Thunk 函数或者 Promise 对象，即 `value` 属性是一个 Thunk 函数或者 Promise 对象，等待以后返回真正的值，而 `done` 属性则还是同步产生的。

ES2018 [引入](https://github.com/tc39/proposal-async-iteration) 了“异步遍历器”（Async Iterator），为异步操作提供原生的遍历器接口，即 `value` 和 `done` 这两个属性都是异步产生。

### 异步遍历的接口

异步遍历器的最大的语法特点，就是调用遍历器的 `next` 方法，**返回的是一个 Promise 对象**。

  ```javascript
  asyncIterator
    .next()
    .then(
      ({ value, done }) => /* ... */
    );
  ```

上面代码中，`asyncIterator` 是一个异步遍历器，调用 `next` 方法以后，返回一个 Promise 对象。因此，可以使用 `then` 方法指定，这个 Promise 对象的状态变为 `resolve` 以后的回调函数。回调函数的参数，则是一个具有 `value` 和 `done` 两个属性的对象，这个跟同步遍历器是一样的。

我们知道，一个对象的同步遍历器的接口，部署在 `Symbol.iterator` 属性上面。同样地，对象的异步遍历器接口，部署在 `Symbol.asyncIterator` 属性上面。不管是什么样的对象，只要它的 `Symbol.asyncIterator` 属性有值，就表示应该对它进行异步遍历。

下面是一个异步遍历器的例子。

```javascript
  const asyncIterable = createAsyncIterable(['a', 'b']);
  const asyncIterator = asyncIterable[Symbol.asyncIterator]();
  
  asyncIterator
  .next()
  .then(iterResult1 => {
    console.log(iterResult1); // { value: 'a', done: false }
    return asyncIterator.next();
  })
  .then(iterResult2 => {
    console.log(iterResult2); // { value: 'b', done: false }
    return asyncIterator.next();
  })
  .then(iterResult3 => {
    console.log(iterResult3); // { value: undefined, done: true }
  });
```

上面代码中，异步遍历器其实返回了两次值。第一次调用的时候，返回一个 Promise 对象；等到 Promise 对象 `resolve` 了，再返回一个表示当前数据成员信息的对象。这就是说，异步遍历器与同步遍历器最终行为是一致的，只是会先返回 Promise 对象，作为中介。

由于异步遍历器的 `next` 方法，返回的是一个 Promise 对象。因此，可以把它放在 `await` 命令后面。

```javascript
  async function f() {
    const asyncIterable = createAsyncIterable(['a', 'b']);
    const asyncIterator = asyncIterable[Symbol.asyncIterator]();
    console.log(await asyncIterator.next());
    // { value: 'a', done: false }
    console.log(await asyncIterator.next());
    // { value: 'b', done: false }
    console.log(await asyncIterator.next());
    // { value: undefined, done: true }
  }
```

上面代码中，`next` 方法用 `await` 处理以后，就不必使用 `then` 方法了。整个流程已经很接近同步处理了。

注意，异步遍历器的 `next` 方法是可以连续调用的，不必等到上一步产生的 Promise 对象 `resolve` 以后再调用。这种情况下，`next` 方法会累积起来，自动按照每一步的顺序运行下去。下面是一个例子，把所有的 `next` 方法放在 `Promise.all` 方法里面。

```javascript
  const asyncIterable = createAsyncIterable(['a', 'b']);
  const asyncIterator = asyncIterable[Symbol.asyncIterator]();
  const [{value: v1}, {value: v2}] = await Promise.all([
    asyncIterator.next(), asyncIterator.next()
  ]);
  
  console.log(v1, v2); // a b
```

另一种用法是一次性调用所有的 `next` 方法，然后 `await` 最后一步操作。

```javascript
  async function runner() {
    const writer = openFile('someFile.txt');
    writer.next('hello');
    writer.next('world');
    await writer.return();
  }
  
  runner();
```

## For await...of

前面介绍过，`for...of` 循环用于遍历同步的 Iterator 接口。新引入的 `for await...of` 循环，则是用于遍历异步的 Iterator 接口。

```javascript
  async function f() {
    for await (const x of createAsyncIterable(['a', 'b'])) {
      console.log(x);
    }
  }
  // a
  // b
```

上面代码中，`createAsyncIterable()` 返回一个拥有异步遍历器接口的对象，`for...of` 循环自动调用这个对象的异步遍历器的 `next` 方法，会得到一个 Promise 对象。`await` 用来处理这个 Promise 对象，一旦 `resolve`，就把得到的值（`x`）传入 `for...of` 的循环体。

`for await...of` 循环的一个用途，是部署了 asyncIterable 操作的异步接口，可以直接放入这个循环。

```javascript
  let body = '';
  
  async function f() {
    for await(const data of req) body += data;
    const parsed = JSON.parse(body);
    console.log('got', parsed);
  }
```

上面代码中，`req` 是一个 asyncIterable 对象，用来异步读取数据。可以看到，使用 `for await...of` 循环以后，代码会非常简洁。

如果 `next` 方法返回的 Promise 对象被 `reject`，`for await...of` 就会报错，要用 `try...catch` 捕捉。

```javascript
  async function () {
    try {
      for await (const x of createRejectingIterable()) {
        console.log(x);
      }
    } catch (e) {
      console.error(e);
    }
  }
```

注意，`for await...of` 循环也可以用于同步遍历器。

```javascript
  (async function () {
    for await (const x of ['a', 'b']) {
      console.log(x);
    }
  })();
  // a
  // b
```

Node v10 支持异步遍历器，Stream 就部署了这个接口。下面是读取文件的传统写法与异步遍历器写法的差异。

```javascript
  // 传统写法
  function main(inputFilePath) {
    const readStream = fs.createReadStream(
      inputFilePath,
      { encoding: 'utf8', highWaterMark: 1024 }
    );
    readStream.on('data', (chunk) => {
      console.log('>>> '+chunk);
    });
    readStream.on('end', () => {
      console.log('### DONE ###');
    });
  }
  
  // 异步遍历器写法
  async function main(inputFilePath) {
    const readStream = fs.createReadStream(
      inputFilePath,
      { encoding: 'utf8', highWaterMark: 1024 }
    );
  
    for await (const chunk of readStream) {
      console.log('>>> '+chunk);
    }
    console.log('### DONE ###');
  }
```

### 异步 Generator 函数

就像 Generator 函数返回一个同步遍历器对象一样，异步 Generator 函数的作用，是返回一个异步遍历器对象。

在语法上，异步 Generator 函数就是 `async` 函数与 Generator 函数的结合。

```javascript
  async function* gen() {
    yield 'hello';
  }
  const genObj = gen();
  genObj.next().then(x => console.log(x));
  // { value: 'hello', done: false }
```

上面代码中，`gen` 是一个异步 Generator 函数，执行后返回一个异步 Iterator 对象。对该对象调用 `next` 方法，返回一个 Promise 对象。

异步遍历器的设计目的之一，就是 Generator 函数处理同步操作和异步操作时，能够使用同一套接口。

  ```javascript
  // 同步 Generator 函数
  function* map(iterable, func) {
    const iter = iterable[Symbol.iterator]();
    while (true) {
      const {value, done} = iter.next();
      if (done) break;
      yield func(value);
    }
  }
  
  // 异步 Generator 函数
  async function* map(iterable, func) {
    const iter = iterable[Symbol.asyncIterator]();
    while (true) {
      const {value, done} = await iter.next();
      if (done) break;
      yield func(value);
    }
  }
  ```

上面代码中，`map` 是一个 Generator 函数，第一个参数是可遍历对象 `iterable`，第二个参数是一个回调函数 `func`。`map` 的作用是将 `iterable` 每一步返回的值，使用 `func` 进行处理。上面有两个版本的 `map`，前一个处理同步遍历器，后一个处理异步遍历器，可以看到两个版本的写法基本上是一致的。

下面是另一个异步 Generator 函数的例子。

  ```javascript
  async function* readLines(path) {
    let file = await fileOpen(path);
  
    try {
      while (!file.EOF) {
        yield await file.readLine();
      }
    } finally {
      await file.close();
    }
  }
  ```

上面代码中，异步操作前面使用 `await` 关键字标明，即 `await` 后面的操作，应该返回 Promise 对象。凡是使用 `yield` 关键字的地方，就是 `next` 方法停下来的地方，它后面的表达式的值（即 `await file.readLine()` 的值），会作为 `next()` 返回对象的 `value` 属性，这一点是与同步 Generator 函数一致的。

异步 Generator 函数内部，能够同时使用 `await` 和 `yield` 命令。可以这样理解，`await` 命令用于将外部操作产生的值输入函数内部，`yield` 命令用于将函数内部的值输出。

上面代码定义的异步 Generator 函数的用法如下。

```javascript
  (async function () {
    for await (const line of readLines(filePath)) {
      console.log(line);
    }
  })()
```

异步 Generator 函数可以与 `for await...of` 循环结合起来使用。

```javascript
  async function* prefixLines(asyncIterable) {
    for await (const line of asyncIterable) {
      yield '> ' + line;
    }
  }
```

异步 Generator 函数的返回值是一个异步 Iterator，即每次调用它的 `next` 方法，会返回一个 Promise 对象，也就是说，跟在 `yield` 命令后面的，应该是一个 Promise 对象。如果像上面那个例子那样，`yield` 命令后面是一个字符串，会被自动包装成一个 Promise 对象。

```javascript
  function fetchRandom() {
    const url = 'https://www.random.org/decimal-fractions/'
      + '?num=1&dec=10&col=1&format=plain&rnd=new';
    return fetch(url);
  }
  
  async function* asyncGenerator() {
    console.log('Start');
    const result = await fetchRandom(); // (A)
    yield 'Result: ' + await result.text(); // (B)
    console.log('Done');
  }
  
  const ag = asyncGenerator();
  ag.next().then(({value, done}) => {
    console.log(value);
  })
```

上面代码中，`ag` 是 `asyncGenerator` 函数返回的异步遍历器对象。调用 `ag.next()` 以后，上面代码的执行顺序如下。

  1. `ag.next()` 立刻返回一个 Promise 对象。
  2. `asyncGenerator` 函数开始执行，打印出 `Start`。
  3. `await` 命令返回一个 Promise 对象，`asyncGenerator` 函数停在这里。
  4. A 处变成 fulfilled 状态，产生的值放入 `result` 变量，`asyncGenerator` 函数继续往下执行。
  5. 函数在 B 处的 `yield` 暂停执行，一旦 `yield` 命令取到值，`ag.next()` 返回的那个 Promise 对象变成 fulfilled 状态。
  6. `ag.next()` 后面的 `then` 方法指定的回调函数开始执行。该回调函数的参数是一个对象 `{value, done}`，其中 `value` 的值是 `yield` 命令后面的那个表达式的值，`done` 的值是 `false`。

A 和 B 两行的作用类似于下面的代码。

```javascript
  return new Promise((resolve, reject) => {
    fetchRandom()
    .then(result => result.text())
    .then(result => {
       resolve({
         value: 'Result: ' + result,
         done: false,
       });
    });
  });
```

如果异步 Generator 函数抛出错误，会导致 Promise 对象的状态变为 `reject`，然后抛出的错误被 `catch` 方法捕获。

```javascript
  async function* asyncGenerator() {
    throw new Error('Problem!');
  }
  
  asyncGenerator()
  .next()
  .catch(err => console.log(err)); // Error: Problem!
```

注意，普通的 async 函数返回的是一个 Promise 对象，而异步 Generator 函数返回的是一个异步 Iterator 对象。可以这样理解，async 函数和异步 Generator 函数，是封装异步操作的两种方法，都用来达到同一种目的。区别在于，前者自带执行器，后者通过 `for await...of` 执行，或者自己编写执行器。下面就是一个异步 Generator 函数的执行器。

```javascript
  async function takeAsync(asyncIterable, count = Infinity) {
    const result = [];
    const iterator = asyncIterable[Symbol.asyncIterator]();
    while (result.length < count) {
      const {value, done} = await iterator.next();
      if (done) break;
      result.push(value);
    }
    return result;
  }
```

上面代码中，异步 Generator 函数产生的异步遍历器，会通过 `while` 循环自动执行，每当 `await iterator.next()` 完成，就会进入下一轮循环。一旦 `done` 属性变为 `true`，就会跳出循环，异步遍历器执行结束。

下面是这个自动执行器的一个使用实例。

```javascript
  async function f() {
    async function* gen() {
      yield 'a';
      yield 'b';
      yield 'c';
    }
  
    return await takeAsync(gen());
  }
  
  f().then(function (result) {
    console.log(result); // ['a', 'b', 'c']
  })
```

异步 Generator 函数出现以后，JavaScript 就有了四种函数形式：普通函数、async 函数、Generator 函数和异步 Generator 函数。请注意区分每种函数的不同之处。基本上，如果是一系列按照顺序执行的异步操作（比如读取文件，然后写入新内容，再存入硬盘），可以使用 async 函数；如果是一系列产生相同数据结构的异步操作（比如一行一行读取文件），可以使用异步 Generator 函数。

异步 Generator 函数也可以通过 `next` 方法的参数，接收外部传入的数据。

```javascript
  const writer = openFile('someFile.txt');
  writer.next('hello'); // 立即执行
  writer.next('world'); // 立即执行
  await writer.return(); // 等待写入结束
```

上面代码中，`openFile` 是一个异步 Generator 函数。`next` 方法的参数，向该函数内部的操作传入数据。每次 `next` 方法都是同步执行的，最后的 `await` 命令用于等待整个写入操作结束。

最后，同步的数据结构，也可以使用异步 Generator 函数。

```javascript
  async function* createAsyncIterable(syncIterable) {
    for (const elem of syncIterable) {
      yield elem;
    }
  }
```

上面代码中，由于没有异步操作，所以也就没有使用 `await` 关键字。

### yield* 语句

`yield*` 语句也可以跟一个异步遍历器。

```javascript
  async function* gen1() {
    yield 'a';
    yield 'b';
    return 2;
  }
  
  async function* gen2() {
    // result 最终会等于 2
    const result = yield* gen1();
  }
```

上面代码中，`gen2` 函数里面的 `result` 变量，最后的值是 `2`。

与同步 Generator 函数一样，`for await...of` 循环会展开 `yield*`。

```javascript
  (async function () {
    for await (const x of gen2()) {
      console.log(x);
    }
  })();
  // a
  // b
```

# FAQ

#faq/js

## Co 模块

### 基本用法

- [co 模块](https://github.com/tj/co) 是著名程序员 TJ Holowaychuk 于 2013 年 6 月发布的一个小工具，用于 Generator 函数的自动执行。
- 下面是一个 Generator 函数，用于依次读取两个文件。

  ```javascript
  var gen = function* () {
    var f1 = yield readFile('/etc/fstab');
    var f2 = yield readFile('/etc/shells');
    console.log(f1.toString());
    console.log(f2.toString());
  };
  ```

- co 模块可以让你不用编写 Generator 函数的执行器。

  ```javascript
  var co = require('co');
  co(gen);
  ```

- 上面代码中，Generator 函数只要传入 `co` 函数，就会自动执行。
- `co` 函数返回一个 `Promise` 对象，因此可以用 `then` 方法添加回调函数。

  ```javascript
  co(gen).then(function (){
    console.log('Generator 函数执行完成');
  });
  ```

- 上面代码中，等到 Generator 函数执行结束，就会输出一行提示。

### Co 模块的原理

- 为什么 co 可以自动执行 Generator 函数？
- 前面说过，Generator 就是一个异步操作的容器。它的自动执行需要一种机制，当异步操作有了结果，能够自动交回执行权。
- 两种方法可以做到这一点。 #

  （1）回调函数。将异步操作包装成 Thunk 函数，在回调函数里面交回执行权。

  （2）Promise 对象。将异步操作包装成 Promise 对象，用 `then` 方法交回执行权。

- co 模块其实就是将两种自动执行器（Thunk 函数和 Promise 对象），包装成一个模块。使用 co 的前提条件是，Generator 函数的 `yield` 命令后面，只能是 Thunk 函数或 Promise 对象。如果数组或对象的成员，全部都是 Promise 对象，也可以使用 co，详见后文的例子。
- 上一节已经介绍了基于 Thunk 函数的自动执行器。下面来看，基于 Promise 对象的自动执行器。这是理解 co 模块必须的。

### 基于 Promise 对象的自动执行

- 还是沿用上面的例子。首先，把 `fs` 模块的 `readFile` 方法包装成一个 Promise 对象。

  ```javascript
  var fs = require('fs');
  
  var readFile = function (fileName){
    return new Promise(function (resolve, reject){
      fs.readFile(fileName, function(error, data){
        if (error) return reject(error);
        resolve(data);
      });
    });
  };
  
  var gen = function* (){
    var f1 = yield readFile('/etc/fstab');
    var f2 = yield readFile('/etc/shells');
    console.log(f1.toString());
    console.log(f2.toString());
  };
  ```

- 然后，手动执行上面的 Generator 函数。

  ```javascript
  var g = gen();
  // value值才是promise
  g.next().value.then(function(data){
    g.next(data).value.then(function(data){
      g.next(data);
    });
  });
  ```

- 手动执行其实就是用 `then` 方法，层层添加回调函数。理解了这一点，就可以写出一个自动执行器。
- ```javascript
  function run(gen){
    var g = gen();
  
    function next(data){
      var result = g.next(data);
      if (result.done) return result.value;
      result.value.then(function(data){
        next(data);
      });
    }
  
    next();
  }
  
  run(gen);
  ```
- 上面代码中，只要 Generator 函数还没执行到最后一步，`next` 函数就调用自身，以此实现自动执行。

### Co 模块的源码

- co 就是上面那个自动执行器的扩展，它的源码只有几十行，非常简单。
- 首先，co 函数接受 Generator 函数作为参数，返回一个 Promise 对象。

  ```javascript
  function co(gen) {
    var ctx = this;
  
    return new Promise(function(resolve, reject) {
    });
  }
  ```

- 在返回的 Promise 对象里面，co 先检查参数 `gen` 是否为 Generator 函数。如果是，就执行该函数，得到一个内部指针对象；如果不是就返回，并将 Promise 对象的状态改为 `resolved`。

  ```javascript
  function co(gen) {
    var ctx = this;
  
    return new Promise(function(resolve, reject) {
      if (typeof gen === 'function') gen = gen.call(ctx);
      if (!gen || typeof gen.next !== 'function') return resolve(gen);
    });
  }
  ```

- 接着，co 将 Generator 函数的内部指针对象的 `next` 方法，包装成 `onFulfilled` 函数。这主要是为了能够 **捕捉抛出的错误**。

  ```javascript
  function co(gen) {
    var ctx = this;
  
    return new Promise(function(resolve, reject) {
      if (typeof gen === 'function') gen = gen.call(ctx);
      if (!gen || typeof gen.next !== 'function') return resolve(gen);
  
      onFulfilled();
      function onFulfilled(res) {
        var ret;
        try {
          ret = gen.next(res);
        } catch (e) {
          return reject(e);
        }
        next(ret);
      }
    });
  }
  ```

- 最后，就是关键的 `next` 函数，它会反复调用自身。

  ```javascript
  function next(ret) {
    if (ret.done) return resolve(ret.value);
    var value = toPromise.call(ctx, ret.value);
    if (value && isPromise(value)) return value.then(onFulfilled, onRejected);
    return onRejected(
      new TypeError(
        'You may only yield a function, promise, generator, array, or object, '
        + 'but the following object was passed: "'
        + String(ret.value)
        + '"'
      )
    );
  }
  ```

- 上面代码中，`next` 函数的内部代码，一共只有四行命令。
- 第一行，检查当前是否为 Generator 函数的最后一步，如果是就返回。
- 第二行，确保每一步的返回值，是 Promise 对象。
- 第三行，使用 `then` 方法，为返回值加上回调函数，然后通过 `onFulfilled` 函数再次调用 `next` 函数。
- 第四行，在参数不符合要求的情况下（参数非 Thunk 函数和 Promise 对象），将 Promise 对象的状态改为 `rejected`，从而终止执行。

### 处理并发的异步操作

- co 支持并发的异步操作，即允许某些操作同时进行，等到它们全部完成，才进行下一步。
- 这时，要把 **并发的操作都放在数组或对象里面**，跟在 `yield` 语句后面。

  ```javascript
  // 数组的写法
  co(function* () {
    var res = yield [
      Promise.resolve(1),
      Promise.resolve(2)
    ];
    console.log(res);
  }).catch(onerror);
  
  // 对象的写法
  co(function* () {
    var res = yield {
      1: Promise.resolve(1),
      2: Promise.resolve(2),
    };
    console.log(res);
  }).catch(onerror);
  ```

- 下面是另一个例子。

  ```javascript
  co(function* () {
    var values = [n1, n2, n3];
    yield values.map(somethingAsync);
  });
  
  function* somethingAsync(x) {
    // do something async
    return y
  }
  ```

- 上面的代码允许并发三个 `somethingAsync` 异步操作，等到它们全部完成，才会进行下一步。

### 实例：处理 Stream

- Node 提供 Stream 模式读写数据，特点是一次只处理数据的一部分，数据分成一块块依次处理，就好像“数据流”一样。这对于处理大规模数据非常有利。Stream 模式使用 EventEmitter API，会释放三个事件。
  - `data` 事件：下一块数据块已经准备好了。
  - `end` 事件：整个“数据流”处理完了。
  - `error` 事件：发生错误。
- 使用 `Promise.race()` 函数，可以判断这三个事件之中哪一个最先发生，只有当 `data` 事件最先发生时，才进入下一个数据块的处理。从而，我们可以通过一个 `while` 循环，完成所有数据的读取。

  ```javascript
  const co = require('co');
  const fs = require('fs');
  
  const stream = fs.createReadStream('./les_miserables.txt');
  let valjeanCount = 0;
  
  co(function*() {
    while(true) {
      const res = yield Promise.race([
        new Promise(resolve => stream.once('data', resolve)),
        new Promise(resolve => stream.once('end', resolve)),
        new Promise((resolve, reject) => stream.once('error', reject))
      ]);
      if (!res) {
        break;
      }
      stream.removeAllListeners('data');
      stream.removeAllListeners('end');
      stream.removeAllListeners('error');
      valjeanCount += (res.toString().match(/valjean/ig) || []).length;
    }
    console.log('count:', valjeanCount); // count: 1120
  });
  ```

- 上面代码采用 Stream 模式读取《悲惨世界》的文本文件，对于每个数据块都使用 `stream.once` 方法，在 `data`、`end`、`error` 三个事件上添加一次性回调函数。变量 `res` 只有在 `data` 事件发生时才有值，然后累加每个数据块之中 `valjean` 这个词出现的次数。

## Async 函数的实现原理

async 函数的实现原理，就是将 **Generator 函数和自动执行器**，包装在一个函数里。

```javascript
  async function fn(args) {
    // ...
  }
  
  // 等同于
  
  function fn(args) {
    return spawn(function* () {
      // ...
    });
  }
```

所有的 `async` 函数都可以写成上面的第二种形式，其中的**`spawn` 函数就是自动执行器**。

下面给出 `spawn` 函数的实现，基本就是前文自动执行器的翻版。

```javascript
  function spawn(genF) {
    // 接受一个Generator函数，返回一个Promise对象
    return new Promise(function(resolve, reject) {
      // 获取指针对象
      const gen = genF();
      // step函数，用于不断调用指针对象的next方法
      // 它接受一个函数作为参数，函数返回指针对象next方法的执行结果
      // 即：{value，done}
      // 第一次调用next方法，启动Generator函数的指针
      step(function() { return gen.next(undefined); });
      function step(nextF) {
        let next;
        try {
          // 函数的返回结果：{value,done}
          next = nextF();
        } catch(e) {
          return reject(e);
        }
        if(next.done) {
          // 如果done 为true，指针已经到了底部
          return resolve(next.value);
        }
        // 指针未到底部，继续执行next方法
        // 使用Promise.resolve，传入next.value
        // 获得一个promise 对象Promise.resolve(next.value)，状态为resolved
        // then 方法自动执行，把获取到的value传递给next方法，作为上一次yield的返回值
        Promise.resolve(next.value).then(function(v) {
          step(function() { return gen.next(v); });
        }, function(e) {
          step(function() { return gen.throw(e); });
        });
      }
    });
  }
```
