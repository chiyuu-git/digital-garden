---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/es/es-object/"}
---


# 基本

对象是 JS 中的引用数据类型

对象是一种复合数据类型，在对象中可以保存多个不同数据类型的属性

使用 typeof 检查一个对象时，会返回 `'object'`，数据类型 Object 是大写的，但是返回的是小写

## 对象的分类

### 内建对象

由 ES 标准中定义的对象，在任何的 ES 实现中都可以直接使用

比如：Math. String() Number() Boolean()

### 宿主对象

由 JS 的运行环境提供的对象，主要是指由浏览器提供的对象

比如： BOM DOM console.log document.write

### 自定义对象

由开发人员自己创建的对象

## 创建对象

### Object 构造函数

```js
  var obj = new Object()  ;
```

运用 new 关键词调用函数 Object() 创建对象

这里 Object() 被称为构造函数 constructor，构造函数是专门用来创建对象的

### 对象字面量

名值对

```js
var obj = {
  属性名:属性值,
  属性名:属性值,
	属性名:属性值,
	属性名(变量名):{名值对} //对象的值可以是对象
	方法名:function(){    }
}； //赋值语句，分号结尾
```

属性名可以加引号，因为属性名的本质都是字符串

但是，使用特殊属性名时一定要加，如 `'class'` 等关键字或者 `123`

注意，使用字面量形式时，属性值也可以不直接声明 @@@

```js
var obj = {a}
// obj.a = undefined ，并不会报错
```

## 对象的基本操作

### 向对象中添加属性

语法：

  ```js
  对象.属性名 = 属性值；
  对象[“属性名”] = 属性值；
  ```

对象的属性名没有任何要求，不需要遵守标识符的规范，但是还是应该尽量去遵守

用字面量创建更加灵活，能以数字为属性名，能传递变量调用相应的属性

  ```js
  obj[“123”] = “一二三”；
  var n = “123”;
  console.log(obj[n]); //注意n没有双引号，因为"123"
  ```

属性值可以任意数据类型

  ```js
  obj.name = "庞劲"; //typeof string
  obj.age = 18; // typeof number
  obj.b = true;// typeof boolean
  obj.obj2
  ```

### 读取对象中的属性

语法：

  ```js
  对象.属性名
  对象["属性名"] //有引号
  ```

如果读取一个对象中没有的属性，它不会报错，而是返回一个 undefined

### 修改对象的属性值

语法：

  ```js
  对象.属性名=新值；
  对象["属性名"] = 新值;
  ```

### 删除对象的属性

语法：

  ```js
  delete 对象.属性名;
  delete 对象["属性名"];
  ```

### In 运算符

语法：" 属性名 " in 对象

如果在对象或其原型链中含有该属性，则返回 true

如果没有则返回 false

不建议使用 in，性能不好，因为原型链往往很长

### Instanceof

instanceof 运算符用于测试 **构造函数** 的 prototype 属性是否出现在对象的原型链中的任何位置

判断对象的是否是某个 **具体类**（构造函数的实例）

**返回值**

- 如果该对象是构造函数的实例，则返回 true，否则返回 false

**示例**

```js
    语法：对象 instanceof 构造函数
    console.log(per instanceof Person); //true
```

**注意**

- **Object** 是所有对象的祖先，所以任何对象和 Object 做 instanceof 运算符都会返回 true
- 基础数据类型 调用 instanceof 不会发生装箱现象，因此基础数据类型不是任何类的实例

```js
const str = new String('123')

console.log('123' instanceof String) // false
console.log('123' instanceof Object) // false
console.log(str instanceof String) // true
console.log(Object.prototype.toString.call('123')) // string
console.log(Object.prototype.toString.call(str)) // string
```

![es-proto](programming/font-end/primitive/es/es-proto.md#Instanceof%20原理)

### hasOwnProperty()

这个方法可以用来检查对象自身中是否含有某个属性，

语法：`对象.hasOwnProperty("属性名")`

因为 in 无法区分自身的属性和原型属性

hasOwnProperty() 是在原型对象的原型的方法，**即 Object 对象的方法**

## 变量基本数据类型和引用类型

基本数据类型的数据，变量是直接保存的它的值

变量与变量之间是互相独立的，修改一个变量不会影响其他的变量。

基本数据类型：

  - a = b;
  - 把 a 的值复制了一份给 b
  - 在栈内存中保存了变量（名）和值
引用数据类型，变量保存的对象的引用（堆内存地址、通过指针）
- 如果多个变量指向的是同一个对象，此时修改一个变量的属性**（其实是对象的属性）**，会影响其他的变量。因为他们的指针是相同的，指向同一个对内存地址
- 但是直接修改变量的值**（给变量赋其他值）**，不会影响其他变量的引用
- 只有创建新的对象才会在 **堆内存** 中开辟新的空间，一旦 **new** 了一个新对象，**就没有办法人为的删除他**，只能切断所有指针，让其变成垃圾对象被回收

```js
  obj2 = obj //并没有构建函数，而是把obj的指针复制给obj2
```

比较两个变量时

  - 对于基本数据类型，比较的就是值，
  - 对于引用数据类型比较的是地址，地址相同才相同

```javascript
var c=10,d=10 ; // c==d true
var obj3 = new Object();
var obj4 = new Object(); //两者属性、方法完全一致， obj3 == obj4 false 像是两个双胞胎
```

| 变量              | 值                                            |
| ----------------- | --------------------------------------------- |
| a                 | 123                                           |
| b (b = a)         | 123 （把 a 的值复制下来）                       |
| obj               | 0x123 （堆内存的地址，保存着 obj 的属性和方法） |
| obj2 (obj2 = obj) | 0x123（把 obj 的值复制下来，有了相同的指针）    |

# 扩展

## 属性的简洁表示法

ES6 允许在对象之中，直接写变量。这时，属性名为变量名, 属性值为变量的值。下面是另一个例子。

  ```javascript
  function f(x, y) {
    return {x, y};
  }
  
  // 等同于
  
  function f(x, y) {
    return {x: x, y: y};
  }
  
  f(1, 2) // Object {x: 1, y: 2}
  ```

除了属性简写，方法也可以简写。

  ```javascript
  const o = {
    method() {
      return "Hello!";
    }
  };
  
  // 等同于
  
  const o = {
    method: function() {
      return "Hello!";
    }
  };
  ```

属性的赋值器（setter）和取值器（getter），事实上也是采用这种写法。

  ```javascript
  const cart = {
    _wheels: 4,
  
    get wheels () {
      return this._wheels;
    },
  
    set wheels (value) {
      if (value < this._wheels) {
        throw new Error('数值太小了！');
      }
      this._wheels = value;
    }
  }
  ```

注意，简洁写法的属性名总是字符串，这会导致一些看上去比较奇怪的结果。

  ```javascript
  const obj = {
    class () {}
  };
  
  // 等同于
  
  var obj = {
    'class': function() {}
  };
  ```

上面代码中，`class` 是字符串，所以不会因为它属于关键字，而导致语法解析报错。

如果某个方法的值是一个 Generator 函数，前面需要加上星号。

  ```javascript
  const obj = {
    * m() {
      yield 'hello world';
    }
  };
  ```

## 属性名表达式

JavaScript 定义对象的属性，有两种方法。

  ```javascript
  // 方法一
  obj.foo = true;
  
  // 方法二
  obj['a' + 'bc'] = 123;
  ```

上面代码的方法一是直接用标识符作为属性名，方法二是用表达式作为属性名，这时要将表达式放在方括号之内。

但是，如果使用字面量方式定义对象（使用大括号），在 ES5 中只能使用方法一（标识符）定义属性。

  ```javascript
  var obj = {
    foo: true,
    abc: 123
  };
  ```

ES6 允许字面量定义对象时，用方法二（表达式）作为对象的属性名，即把表达式放在方括号内。

  ```javascript
  let propKey = 'foo';
  
  let obj = {
    [propKey]: true,
    ['a' + 'bc']: 123
  };
  ```

表达式还可以用于定义方法名。

  ```javascript
  let obj = {
    ['h' + 'ello']() {
      return 'hi';
    }
  };
  
  obj.hello() // hi
  ```

注意，属性名表达式与简洁表示法，不能同时使用，会报错。

  ```javascript
  // 报错
  const foo = 'bar';
  const bar = 'abc';
  const baz = { [foo] };
  
  // 正确
  const foo = 'bar';
  const baz = { [foo]: 'abc'};
  ```

注意，属性名表达式如果是一个对象，默认情况下会自动将对象转为字符串 `[object Object]`，这一点要特别小心。

  ```javascript
  const keyA = {a: 1};
  const keyB = {b: 2};
  
  const myObject = {
    [keyA]: 'valueA',
    [keyB]: 'valueB'
  };
  
  myObject // Object {[object Object]: "valueB"}
  ```

上面代码中，`[keyA]` 和 `[keyB]` 得到的都是 `[object Object]`，所以 `[keyB]` 会把 `[keyA]` 覆盖掉，而 `myObject` 最后只有一个 `[object Object]` 属性。

## 方法的 Name 属性

函数的 `name` 属性，返回函数名。对象方法也是函数，因此也有 `name` 属性。

  ```javascript
  const person = {
    sayName() {
      console.log('hello!');
    },
  };
  
  person.sayName.name   // "sayName"
  ```

上面代码中，方法的 `name` 属性返回函数名（即方法名）。

如果对象的方法使用了取值函数（`getter`）和存值函数（`setter`），则 `name` 属性不是在该方法上面，而是该方法的属性的描述对象的 `get` 和 `set` 属性上面，返回值是方法名前加上 `get` 和 `set`。

  ```javascript
  const obj = {
    get foo() {},
    set foo(x) {}
  };
  
  obj.foo.name
  // TypeError: Cannot read property 'name' of undefined
  
  const descriptor = Object.getOwnPropertyDescriptor(obj, 'foo');
  
  descriptor.get.name // "get foo"
  descriptor.set.name // "set foo"
  ```

如果对象的方法是一个 Symbol 值，那么 `name` 属性返回的是这个 Symbol 值的描述。

  ```javascript
  const key1 = Symbol('description');
  const key2 = Symbol();
  let obj = {
    [key1]() {},
    [key2]() {},
  };
  obj[key1].name // "[description]"
  obj[key2].name // ""
  ```

上面代码中，`key1` 对应的 Symbol 值有描述，`key2` 没有。

## Super 关键字

我们知道，`this` 关键字总是指向函数所在的当前对象，ES6 又新增了另一个类似的关键字 `super`，指向当前对象的原型对象。

  ```javascript
  const proto = {
    foo: 'hello'
  };
  
  const obj = {
    foo: 'world',
    find() {
      return super.foo;
    }
  };
  
  Object.setPrototypeOf(obj, proto);
  obj.find() // "hello"
  ```

上面代码中，对象 `obj.find()` 方法之中，通过 `super.foo` 引用了原型对象 `proto` 的 `foo` 属性。

注意，`super` 关键字表示原型对象时，只能用在对象的方法之中，用在其他地方都会报错。

```javascript
  // 报错
  const obj = {
    foo: super.foo
  }
  
  // 报错
  const obj = {
    foo: () => super.foo
  }
  
  // 报错
  const obj = {
    foo: function () {
      return super.foo
    }
  }
```

上面三种 `super` 的用法都会报错，因为对于 JavaScript 引擎来说，这里的 `super` 都没有用在对象的方法之中。第一种写法是 `super` 用在属性里面，第二种和第三种写法是 `super` 用在一个函数里面，然后赋值给 `foo` 属性。目前，只有对象方法的简写法可以让 JavaScript 引擎确认，定义的是对象的方法。 @@@

JavaScript 引擎内部，`super.foo` 等同于 `Object.getPrototypeOf(this).foo`（属性）或 `Object.getPrototypeOf(this).foo.call(this)`（方法）。

```javascript
  const proto = {
    x: 'hello',
    foo() {
      console.log(this.x);
    },
  };
  
  const obj = {
    x: 'world',
    foo() {
      super.foo();
    }
  }
  
  Object.setPrototypeOf(obj, proto);
  
  obj.foo() // "world"
```

上面代码中，`super.foo` 指向原型对象 `proto` 的 `foo` 方法，但是绑定的 `this` 却还是当前对象 `obj`，因此输出的就是 `world`。

## 对象的扩展运算符

![es-next-1](programming/font-end/primitive/es/es-next-1.md#对象的扩展运算符)

# 静态方法

## Object.is()

ES5 比较两个值是否相等，只有两个运算符：相等运算符（`\==`）和严格相等运算符（`\===`）。它们都有缺点，前者会自动转换数据类型，后者的 `NaN` 不等于自身，以及 `+0` 等于 `-0`。JavaScript 缺乏一种运算，在所有环境中，只要两个值是一样的，它们就应该相等。

ES6 提出“Same-value equality”（同值相等）算法，用来解决这个问题。`Object.is` 就是部署这个算法的新方法。它用来比较两个值是否严格相等，与严格比较运算符（`\===`）的行为基本一致。

不同之处只有两个：一是 `+0` 不等于 `-0`，二是 `NaN` 等于自身。

  ```js
  +0 === -0 //true
  NaN === NaN // false
  
  Object.is(+0, -0) // false
  Object.is(+0,0) // true
  Object.is(NaN, NaN) // true
  ```

## Object.assign()

### 基本用法

`Object.assign` 方法用于对象的合并，将源对象 **自身**（source）的 **所有可枚举属性**，复制到目标对象（target）。

```javascript
  const target = { a: 1 };
  
  const source1 = { b: 2 };
  const source2 = { c: 3 };
  
  Object.assign(target, source1, source2);
  target // {a:1, b:2, c:3}
```

`Object.assign` 方法的第一个参数是目标对象，后面的参数都是源对象。

属性名为 Symbol 值的属性，也会被 `Object.assign` 拷贝。

  ```javascript
  Object.assign({ a: 'b' }, { [Symbol('c')]: 'd' })
  // { a: 'b', Symbol(c): 'd' }
  ```

### 参数

#### Target

如果只有一个参数，`Object.assign` 会直接返回该参数。

```javascript
  const obj = {a: 1};
  Object.assign(obj) === obj // true
```

如果该参数不是对象，则会先转成对象，然后返回。

```javascript
  typeof Object.assign(2) // "object"
```

由于 `undefined` 和 `null` 无法转成对象，所以如果它们作为参数，就会报错。

  ```javascript
  Object.assign(undefined) // 报错
  Object.assign(null) // 报错
  ```

#### Sources

如果非对象参数出现在源对象的位置（即非首参数），那么处理规则有所不同。首先，这些参数都会转成对象，如果无法转成对象，就会跳过。这意味着，如果 `undefined` 和 `null` 不在首参数，就不会报错。

  ```javascript
  let obj = {a: 1};
  Object.assign(obj, undefined) === obj // true
  Object.assign(obj, null) === obj // true
  ```

其他类型的值（即数值、字符串和布尔值）不在首参数，也不会报错。但是，除了字符串会以数组形式，拷贝入目标对象，其他值都不会产生效果。

```javascript
  const v1 = 'abc';
  const v2 = true;
  const v3 = 10;
  
  const obj = Object.assign({}, v1, v2, v3);
  console.log(obj); // { "0": "a", "1": "b", "2": "c" }
  //上面代码中，v1、v2、v3分别是字符串、布尔值和数值，结果只有字符串合入目标对象（以字符数组的形式），数值和布尔值都会被忽略。这是因为只有字符串的包装对象，会产生可枚举属性。
```

布尔值、数值、字符串分别转成对应的包装对象，可以看到它们的原始值都在包装对象的内部属性 `[[PrimitiveValue]]` 上面，这个属性是不会被 `Object.assign` 拷贝的。只有字符串的包装对象，会产生可枚举的实义属性，那些属性则会被拷贝。

```javascript
  Object(10)  //  {[[PrimitiveValue]]: 10}
  Object('abc') // {0: "a", 1: "b", 2: "c", length: 3, [[PrimitiveValue]]: "abc"}
```

### 注意

**target 本身也会被修改，同时也会返回修改后的 target 的引用**

如果目标对象与源对象有 **同名属性**，或多个源对象有同名属性，则后面的属性会覆盖前面的属性

#### 浅拷贝

`Object.assign` 方法实行的是浅拷贝，而不是深拷贝。也就是说，如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用。

```javascript
  const obj1 = {a: {b: 1}};
  const obj2 = Object.assign({}, obj1);
  
  obj1.a.b = 2;
  obj2.a.b // 2
```

上面代码中，源对象 `obj1` 的 `a` 属性的值是一个对象，`Object.assign` 拷贝得到的是这个对象的引用。这个对象的任何变化，都会反映到目标对象上面。

#### 数组的处理

`Object.assign` 可以用来处理数组，但是会把数组视为对象。

```javascript
  Object.assign([1, 2, 3], [4, 5])
  // [4, 5, 3]
```

上面代码中，`Object.assign` 把数组视为属性名为 0、1、2 的对象，因此源数组的 0 号属性 `4` 覆盖了目标数组的 0 号属性 `1`。

#### 取值函数的处理

`Object.assign` 只能进行值的复制，如果要复制的值是一个取值函数，那么将求值后再复制。

  ```javascript
  const source = {
    get foo() { return 1 }
  };
  const target = {};
  
  Object.assign(target, source)
  // { foo: 1 }
  ```

上面代码中，`source` 对象的 `foo` 属性是一个 **取值函数**，`Object.assign` 不会复制这个取值函数，只会拿到值以后，将这个值复制过去。

### 常见用途

#### 为对象添加属性

通过 `Object.assign` 方法，将 `x` 属性和 `y` 属性添加到 `Point` 类的对象实例。

```javascript
  class Point {
    constructor(x, y) {
      Object.assign(this, {x, y});
    }
  }
```

#### 为对象添加方法

下面代码使用了对象属性的简洁表示法，直接将两个函数放在大括号中，再使用 `assign` 方法添加到 `SomeClass.prototype` 之中。

```javascript
  Object.assign(SomeClass.prototype, {
    someMethod(arg1, arg2) {
      ···
    },
    anotherMethod() {
      ···
    }
  });
  
  // 等同于下面的写法
  SomeClass.prototype.someMethod = function (arg1, arg2) {
    ···
  };
  SomeClass.prototype.anotherMethod = function () {
    ···
  };
```

#### 克隆对象

```javascript
  function clone(origin) {
    return Object.assign({}, origin);
  }
```

上面代码将原始对象拷贝到一个空对象，就得到了原始对象的克隆。

不过，采用这种方法克隆，只能克隆原始对象自身的值，不能克隆它继承的值。如果想要保持继承链，可以采用下面的代码。

```javascript
  function clone(origin) {
    let originProto = Object.getPrototypeOf(origin);
    return Object.assign(Object.create(originProto), origin);
  }
```

#### 合并多个对象

将多个对象合并到某个对象。

```javascript
  const merge =
    (target, ...sources) => Object.assign(target, ...sources);
```

如果希望合并后返回一个新对象，可以改写上面函数，对一个空对象合并。

```javascript
  const merge =
    (...sources) => Object.assign({}, ...sources);
```

#### 为属性指定默认值

```javascript
  const DEFAULTS = {
    logLevel: 0,
    outputFormat: 'html'
  };
  
  function processContent(options) {
    options = Object.assign({}, DEFAULTS, options);
    console.log(options);
    // ...
  }
```

上面代码中，`DEFAULTS` 对象是默认值，`options` 对象是用户提供的参数。`Object.assign` 方法将 `DEFAULTS` 和 `options` 合并成一个新对象，如果两者有同名属性，则 `option` 的属性值会覆盖 `DEFAULTS` 的属性值。

注意，由于存在浅拷贝的问题，`DEFAULTS` 对象和 `options` 对象的所有属性的值，最好都是简单类型，不要指向另一个对象。否则，`DEFAULTS` 对象的该属性很可能不起作用。

  ```javascript
  const DEFAULTS = {
    url: {
      host: 'example.com',
      port: 7070
    },
  };
  
  processContent({ url: {port: 8000} })
  // {
  //   url: {port: 8000}
  // }
  ```

上面代码的原意是将 `url.port` 改成 8000，`url.host` 不变。实际结果却是 `options.url` 覆盖掉 `DEFAULTS.url`，所以 `url.host` 就不存在了。

## Object.freeze()

如果真的想将对象冻结，应该使用 `Object.freeze` 方法。

```javascript
  const foo = Object.freeze({});

  // 常规模式时，下面一行不起作用；
  // 严格模式时，该行会报错
  foo.prop = 123;
```

上面代码中，常量 `foo` 指向一个冻结的对象，所以添加新属性不起作用，严格模式时还会报错。

除了将对象本身冻结，对象的属性也应该冻结。下面是一个将对象彻底冻结的函数。

  ```javascript
  var constantize = (obj) => {
    Object.freeze(obj);
    Object.keys(obj).forEach( (key, i) => {
      if ( typeof obj[key] === 'object' ) {
        constantize( obj[key] );
      }
    });
  };
  ```

# 属性描述符相关方法

## 基本概念

ECMAScript 对象中目前存在的 **属性描述符** 主要有两种, **数据描述符 (数据属性) 和存取描述符 (访问器属性)**:

+ 数据描述符是一个拥有可写或不可写值的属性
+ 存取描述符是由一对 getter-setter 函数功能来描述的属性

### 数据 (数据描述符) 属性

数据属性有 4 个描述内部属性的特性

#### `[[Configurable]]`

表示能否通过 **delete** 删除此属性，能否修改属性的特性，或能否修改把属性 **修改为访问器属性**

默认值为 true 的 case

  - 直接使用 **字面量定义对象属性**
  - 向对象中添加的属性，如 `obj.b=2`

  > 不使用 var 声明的全局变量，逐步淘汰

默认值为 false 的 case

  - let，const，class 定义的变量的无法删除

  > 使用 var 声明的全局变量，逐步淘汰

delete 只是不可删除，并不会报错，在严格模式下抛出错误

#### `[[Enumerable]]`

表示该属性是否可枚举，即是否通过**for-in 循环或 Object.keys()** 返回属性

如果直接使用 **字面量定义对象**，默认值为 true

#### `[[Writable]]`

能否 **修改** 属性的值

如果直接使用字面量定义对象，默认值为 true

#### `[[Value]]`

该属性对应的值，默认为 undefined

### 访问器 (存取描述符) 属性

访问器属性也有 4 个描述内部属性的特性

#### `[[Configurable]]`

和数据属性的 `[[Configurable]]` 一样，表示能否通过 delete 删除此属性，能否修改属性的特性，或能否修改把属性修改为 **数据属性**，

如果直接使用字面量定义对象，默认值为 true

#### `[[Enumerable]]`

和数据属性的 `[[Configurable]]` 一样，表示该属性是否可枚举，即是否通过 for-in 循环或 Object.keys() 返回属性

如果直接使用字面量定义对象，默认值为 true

#### `[[Get]]`

一个给属性提供 getter 的方法 (访问对象属性时调用的函数,返回值就是当前属性的值)，如果没有 getter 则为 undefined。该方法的 **返回值被用作属性值**。

默认为 undefined

#### `[[Set]]`

一个给属性提供 setter 的方法 (给对象属性设置值时调用的函数)，如果没有 setter 则为 undefined。该方法将默认接受 **唯一参数：属性的新值**。

默认为 undefined，如果不在 setter 内部给 **属性赋值**，属性的值不会改变

#### 对象内的 Get 和 Set 方法

可以在创建对象字面量的时候直接使用 get 和 set 方法：

  1. get 与 set 是 **操作符**
  2. get 是得到 一般是要返回的 set 是设置 不用返回
  3. 如果调用对象内部的属性 **约定的命名方式** 是带上下划线代表私有变量 \_bar

  ```js
  var myObject = {
    foo: 4,
    _bar:1,
    get bar(){
      return this._bar
    },
    set bar(value) {
      this.foo = value
    }
  }
  console.log(myObject.bar=1,myObject.foo) // 1,1
  // 上面的代码中通过get和set操作符定义了bar属性的setter
  // 如果只有set，则bar属性不可读，因为get默认是undefined，相当于创建了一个只写属性，bar没有真正的值
  // 如果只有get，则bar属性不可写，因为set默认是undefined，相当于创建了一个只读属性，bar不可写
  ```

在访问器属性描述符中的 `[[get]]`，`[[set]]` 都是描述符，本质上是调用对象自身的 get 和 set 操作符

#### 字面量和属性描述在 Getter 和 Setter 上的区别

1. 访问对象的属性是否需要加 this
2. getter 和 setter 简写的形式不同
3. 共同点：所有对属性的读写本质上都是通过 get 和 set

   ```js
   var myObject = {
     foo:1,
     _bar:1,
     get bar(){
       return this._bar // 必须要加this，否则无法访问到外部属性
     },
     set bar(value) {
       this.foo = value
       console.log('set:bar')
     } 
   }
   Object.defineProperty(myObject, 'foo', {
     configurable : true,
     enumerable : true,
     get: function() {
         console.log('get:'+this)
         return foo // foo 可以直接访问，加了this反而报错,加了this 变成了访问foo，又会触发getter
     },
     set: function(newValue) {
         foo = newValue
         console.log('set:'+this)
     }
   })
   myObject.bar = 4
   console.log(myObject.bar,myObject.foo) //1,4
   myObject.foo = 2
   console.log(myObject.bar,myObject.foo) //1,2
   ```

## ES5.1 扩展

### Object.defineProperty(obj, Prop, descriptor)

该方法会直接在一个对象上定义 **一个新属性**，或者 **修改一个对象的现有属性**， 并 **返回这个对象**

如果不指定 configurable, writable, enumerable ，则这些属性默认值为 false，如果不指定 value, get, set，则这些属性默认值为 undefined

**参数**

- **obj:** 需要被操作的目标对象
- **prop:** 目标对象需要定义或修改的属性的名称
- **descriptor:** 将被定义或修改的属性的描述符

  ```js
  var obj = new Object();
  
  Object.defineProperty(obj, 'name', {
      configurable: false,
      writable: true,
      enumerable: true,
      value: '张三'
  })
  
  console.log(obj.name)  //张三
  ```

  ```js
  // 在对象中添加一个属性与存取描述符的示例
  var bValue;
  Object.defineProperty(o, "b", {
    // 对象属性的简写形式，本质上是getter函数，而不是一个传统意义get方法
    get(){
      return bValue;
    },
    set : function(newValue){
      bValue = newValue;
    },
    enumerable : true,
    configurable : true
  });
  ```

**注意**

- 属性名如果是字符串，必须显式的加上引号，与变量做区分
- 属性名哪些时候可以省略引号呢？字面量定义，`.` 操作符读写的时候
- 如果 descriptor 中，configurable 和 enumerable 没有写的话，默认为 false

### Object.defineProperties(object,descriptors)

方法直接在一个对象上定义 **一个或多个新的属性或修改现有属性，并返回该对象**

**参数**

- **obj:** 将要被添加属性或修改属性的对象
- **props:** 该对象的一个或多个键值对定义了将要为对象添加或修改的属性的具体配置，如果没有设置，则默认为 false

```js
  var obj = new Object();
  Object.defineProperties(obj, {
      name: {
          value: '张三',
          configurable: false,
          writable: true,
          enumerable: true
      },
      age: {
          value: 18,
          configurable: true
      }
  })
  
  console.log(obj.name, obj.age) // 张三, 18
```

### Object.getOwnPropertyDescriptor(obj, prop)

返回指定对象上一个 **自有属性对应的属性描述符**

**参数**

- **obj:** 需要查找的目标对象
- **prop:** 目标对象内属性名称

```js
  var person = {
      name: '张三',
      age: 18
  }
  
  var desc = Object.getOwnPropertyDescriptor(person, 'name'); 
  console.log(desc)  结果如下
  // {
  //     configurable: true,
  //     enumerable: true,
  //     writable: true,
  //     value: "张三"
  // }
```

## ES6 扩展

### Object.getOwnPropertyDescriptors(obj, prop)

ES2017 引入了 `Object.getOwnPropertyDescriptors()` 方法，返回指定对象所有 **自身属性**（非继承属性）的 **描述对象**。

```javascript
  const obj = {
    foo: 123,
    get bar() { return 'abc' }
  };
  
  Object.getOwnPropertyDescriptors(obj)
  // { foo:
  //    { value: 123,
  //      writable: true,
  //      enumerable: true,
  //      configurable: true },
  //   bar:
  //    { get: [Function: get bar],
  //      set: undefined,
  //      enumerable: true,
  //      configurable: true } }
```

上面代码中，`Object.getOwnPropertyDescriptors()` 方法返回一个对象，**所有原对象的属性名都是该对象的属性名，对应的属性值就是该属性的描述对象。**

**polyfill**

```javascript
  function getOwnPropertyDescriptors(obj) {
    const result = {};
    for (let key of Reflect.ownKeys(obj)) {
      result[key] = Object.getOwnPropertyDescriptor(obj, key);
    }
    return result;
  }
```

#### 拷贝 Get 和 Set

该方法的引入目的，主要是为了解决 `Object.assign()` 无法正确拷贝 `get` 属性和 `set` 属性的问题。

```javascript
  const source = {
    set foo(value) {
      console.log(value);
    }
  };
  
  const target1 = {};
  Object.assign(target1, source);
  
  Object.getOwnPropertyDescriptor(target1, 'foo')
  // { value: undefined,
  //   writable: true,
  //   enumerable: true,
  //   configurable: true }
  ```

上面代码中，`source` 对象的 `foo` 属性的值是一个赋值函数，`Object.assign` 方法将这个属性拷贝给 `target1` 对象，结果该属性的值变成了 `undefined`。这是因为 `Object.assign` 方法总是拷贝一个属性的值，而不会拷贝它背后的赋值方法或取值方法。

这时，`Object.getOwnPropertyDescriptors()` 方法配合 `Object.defineProperties()` 方法，就可以实现正确拷贝。

```javascript
const source = {
    set foo(value) {
        console.log(value);
    }
};

const target2 = {};
Object.defineProperties(target2, Object.getOwnPropertyDescriptors(source));
Object.getOwnPropertyDescriptor(target2, 'foo')
// { get: undefined,
//   set: [Function: set foo],
//   enumerable: true,
//   configurable: true }

// 合并两个函数
const shallowMerge = (target, source) => Object.defineProperties(
    target,
    Object.getOwnPropertyDescriptors(source)
);
```

#### 继承

另外，`Object.getOwnPropertyDescriptors()` 方法可以实现一个对象继承另一个对象。以前，继承另一个对象，常常写成下面这样。

  ```javascript
  const obj = {
    __proto__: prot,
    foo: 123,
  };
  ```

ES6 规定 `__proto__` 只有浏览器要部署，其他环境不用部署。如果去除 `__proto__`，上面代码就要改成下面这样。

  ```javascript
  const obj = Object.create(prot);
  obj.foo = 123;
  
  // 或者
  
  const obj = Object.assign(
    Object.create(prot),
    {
      foo: 123,
    }
  );
  ```

有了 `Object.getOwnPropertyDescriptors()`，我们就有了另一种写法。

  ```javascript
  const obj = Object.create(
    prot,
    Object.getOwnPropertyDescriptors({
      foo: 123,
    })
  );
  ```

#### 混入模式

`Object.getOwnPropertyDescriptors()` 也可以用来实现 Mixin（混入）模式。

```javascript
  let mix = (object) => ({
    with: (...mixins) => mixins.reduce(
      (c, mixin) => Object.create(
        c, Object.getOwnPropertyDescriptors(mixin)
      ), object)
  });
  
  // multiple mixins example
  let a = {a: 'a'};
  let b = {b: 'b'};
  let c = {c: 'c'};
  let d = mix(c).with(a, b);
  
  d.c // "c"
  d.b // "b"
  d.a // "a"
```

上面代码返回一个新的对象 `d`，代表了对象 `a` 和 `b` 被混入了对象 `c` 的操作。

出于完整性的考虑，`Object.getOwnPropertyDescriptors()` 进入标准以后，以后还会新增 `Reflect.getOwnPropertyDescriptors()` 方法。

## 各种场景下描述符属性的的扩展示例讲解

### 在对象中添加数据描述符属性

如果设置 configurable 属性为 false，则不可使用 delete 操作符 (在严格模式下抛出错误), 修改所有内部属性值会抛出错误

  > 在《javaScript 高级教程中》说只可以改变 writable 的值，现在改变 writable 的值也会抛出错误

```js
  var person = {};
  
  Object.defineProperty(person, 'name', {
      configurable: false,
      value: 'John'
  }) ;
  
  delete person.name   // 严格模式下抛出错误
  
  console.log(person.name)  // 'John'  没有删除
  
  Object.defineProperty(person, 'name', {
      configurable: true  //报错
  });
  
  Object.defineProperty(person, 'name', {
      enumerable: 2  //报错
  });
  
  Object.defineProperty(person, 'name', {
      writable: true  //报错
  });
  
  Object.defineProperty(person, 'name', {
      value: 2  //报错
  });
```

**注意:**

以上是最开始定义属性描述符时,**writable 默认为 false**,才会出现上述效果,如果 writable 定义为 true, 则可以修改 `[[writable]]` 和 `[[value]]` 属性值,修改另外两个属性值 **仍然会** 报错

```js
  var obj = {};
  
  Object.defineProperty(obj, 'a', {
      configurable: false,
      writable: true,
      value: 1
  });
  
  Object.defineProperty(obj, 'a', {
      // configurable: true, //报错
      // enumerable: true,  //报错
      writable: false,
      value: 2
  });
  var d = Object.getOwnPropertyDescriptor(obj, 'a')
  console.log(d);
  // {
  //     value: 2, 
  //     writable: false, 
  // }
```

### 在对象中添加存取描述符属性

```js
  var obj = {};
  var aValue; //如果不初始化变量, 不给下面的a属性设置值,直接读取会报错aValue is not defined
  var b;
  Object.defineProperty(obj, 'a', {
      configurable : true,
      enumerable : true,
      get: function() {
          return aValue
      },
      set: function(newValue) {
          aValue = newValue;
          b = newValue + 1
      }
  })
  console.log(b) // undefined
  console.log(obj.a)  // undefined, 当读取属性值时，调用get方法,返回undefined
  obj.a = 2;  // 当设置属性值时,调用set方法,aValue为2
  
  console.log(obj.a) // 2  读取属性值,调用get方法,此时aValue为2
  console.log(b) // 3  再给obj.a赋值时,执行set方法,b的值被修改为2,额外说一句,vue中的计算属性就是利用setter来实现的
```

**注意:**

1. getter 和 setter 可以不同时使用,但在严格模式下只其中一个,会抛出错误
2. 数据描述符与存取描述符不可混用,会抛出错误

```js
  var obj = {};
  Object.defineProperty(obj, 'a', {
      value: 'a1',
      get: function() {
         return 'a2'
      }    
  });
  // TypeError: Invalid property descriptor. Cannot both specify accessors and a value or writable attribute
```

### `[[Writable]]`

当 writable 为 false(并且 configurable 为 true),`[[value]]` 可以通过 defineProperty 修改, 但 **不能直接赋值修改**

```js
  var obj = {};
  
  Object.defineProperty(obj, 'a', {
      configurable: true,
      enumerable: false,
      writable: false,
      value: 1
  });
  
  Object.defineProperty(obj, 'a', {
      configurable: false,
      enumerable: true,
      writable: false ,
      value: 2
  });
  var d = Object.getOwnPropertyDescriptor(obj, 'a')
  
  console.log(d); // 结果如下
  // {
  //     value: 2, 
  //     writable: false, 
  //     enumerable: true, 
  //     configurable: false
  // }
  
  
  // 但是如果直接赋值修改
  var obj = {}
  
  Object.defineProperty(obj, 'a', {
      configurable: true,
      enumerable: false,
      writable: false,
      value: 1
  });
  obj.a=2;
  var d = Object.getOwnPropertyDescriptor(obj, 'a')
  
  console.log(d); // 结果如下
  
  // {
  //     value: 1,  // 没有做出修改，不会报错
  //     writable: false, 
  //     enumerable: true, 
  //     configurable: false
  // }
```

### `[[Enumerable]]`

```js
  var obj = {};
  Object.defineProperties(obj, {
      a: {
          value: 1,
          enumerable: false
      }, 
      b: {
          value: 2,
          enumerable: true
      },
      c: {
          value: 3,
          enumerable: false
      }
  })
  
  obj.d = 4;
  
  //等同于
  
  //Object.defineProperty(obj, 'd', {
  //    configurable: true,
  //    enumerable: true,
  //    writable: true,
  //    value: 4
  //})
  
  for(var key in obj) {
      console.log(key);  
      // 打印一次b, 一次d, a和c属性enumerable为false，不可被枚举
  } 
  
  var arr = Object.keys(obj);
  console.log(arr);  // ['b', 'd']
```

### Get 和 Set 实现简单的数据双向绑定

```html
  <body>
      <p>
          input1=><input type="text" id="input1">
      </p>
      <p>
          input2=><input type="text" id="input2">
      </p>
      <div>
          我每次比input2的值加1=>
          <span id="span"></span>
      </div>
  </body>
```

```js
  var oInput1 = document.getElementById('input1');
  var oInput2 = document.getElementById('input2');
  var oSpan = document.getElementById('span');
  var obj = {};
  Object.defineProperties(obj, {
      val1: {
          configurable: true,
          get: function() {
              oInput1.value = 0;
              oInput2.value = 0;
              oSpan.innerHTML = 0;
              return 0 //设置初始显示值
          },
          set: function(newValue) {
              oInput2.value = newValue;
              // 可以访问到val2的值吗？使用this可以读取到
              // this.val2 = newValue;
              oSpan.innerHTML = Number(newValue) ? Number(newValue)+1 : 0
          }
      },
      val2: {
          configurable: true,
          get: function() {
              oInput1.value = 0;
              oInput2.value = 0;
              oSpan.innerHTML = 0;
              return 0 //设置初始显示值
          },
          set: function(newValue) {
              oInput1.value = newValue;
              oSpan.innerHTML = Number(newValue)+1;
          }
      }
  })
  // 初始化显示，直接修改input1，input1的setter修改了input2和span
  oInput1.value = obj.val1;
  oInput1.addEventListener('keyup', function() {
      obj.val1 = oInput1.value;
  }, false)
  oInput2.addEventListener('keyup', function() {
      obj.val2 = oInput2.value;
  }, false)
```

# 遍历

## 可枚举性

对象的每个属性都有一个描述对象（Descriptor），用来控制该属性的行为。`Object.getOwnPropertyDescriptor` 方法可以获取该属性的描述对象。

```javascript
  let obj = { foo: 123 };
  Object.getOwnPropertyDescriptor(obj, 'foo')
  //  {
  //    value: 123,
  //    writable: true,
  //    enumerable: true,
  //    configurable: true
  //  }
```

描述对象的 `enumerable` 属性，称为“可枚举性”，如果该属性为 `false`，就表示某些操作会忽略当前属性。

目前，有四个操作会忽略 `enumerable` 为 `false` 的属性。

  - `for...in` 循环：只遍历对象 **自身的和继承的** 可枚举的属性。
  - `Object.keys()`：返回对象自身的所有可枚举的属性的键名。
  - `JSON.stringify()`：只串行化对象自身的可枚举的属性。
  - `Object.assign()`： 忽略 `enumerable` 为 `false` 的属性，只拷贝对象自身的可枚举的属性。

这四个操作之中，前三个是 ES5 就有的，最后一个 `Object.assign()` 是 ES6 新增的。其中，只有 `for...in` 会返回继承的属性，其他三个方法都会忽略继承的属性，只处理对象自身的属性。

实际上，引入“可枚举”（`enumerable`）这个概念的最初目的，就是让某些属性可以规避掉 `for...in` 操作，不然所有内部属性和方法都会被遍历到。比如，对象原型的 `toString` 方法，以及数组的 `length` 属性，就通过“可枚举性”，从而避免被 `for...in` 遍历到。

```javascript
  Object.getOwnPropertyDescriptor(Object.prototype, 'toString').enumerable
  // false
  
  Object.getOwnPropertyDescriptor([], 'length').enumerable
  // false
```

上面代码中，`toString` 和 `length` 属性的 `enumerable` 都是 `false`，因此 `for...in` 不会遍历到这两个继承自原型的属性。

另外，ES6 规定，所有 Class 的原型的方法都是不可枚举的。

```javascript
  Object.getOwnPropertyDescriptor(class {foo() {}}.prototype, 'foo').enumerable
  // false
```

总的来说，操作中引入继承的属性会让问题复杂化，大多数时候，我们只关心对象自身的属性。所以，尽量不要用 `for...in` 循环，而用 `Object.keys()` 代替。

## ~~for…in 语句~~

```js
for(var 变量 in 对象){}

for(var n in obj){
	console.log(n); //打印属性名
	console.log(obj.n); //错误，obj里并没有属性n
	console.log(obj[n]);//正确，向obj动态传递了属性名，可以打印属性值
}
```

for…in 语句，对象中有几个属性，循环体就会执行几次

每次执行时， 会把一个属性的名字赋值给声明的变量

## 属性的遍历

ES6 一共有 5 种方法可以遍历对象的 **属性名**

### for...in

`for...in` 循环遍历对象自身的和继承的可枚举属性（不含 Symbol 属性）。

### Object.keys(obj)

`Object.keys` 返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含 Symbol 属性）的键名。

### Object.getOwnPropertyNames(obj)

`Object.getOwnPropertyNames` 返回一个数组，包含对象自身的所有属性（不含 Symbol 属性，但是包括不可枚举属性）的键名。

### Object.getOwnPropertySymbols(obj)

`Object.getOwnPropertySymbols` 返回一个数组，包含对象自身的所有 Symbol 属性的键名。

### Reflect.ownKeys(obj)

`Reflect.ownKeys` 返回一个数组，包含对象自身的所有键名，不管键名是 Symbol 或字符串，也不管是否可枚举。

### 共同点

以上的 5 种方法遍历对象的键名，都遵守同样的属性遍历的次序规则:

- 首先遍历所有数值键，按照数值升序排列。
- 其次遍历所有字符串键，按照加入时间升序排列。
- 最后遍历所有 Symbol 键，按照加入时间升序排列。

```javascript
  Reflect.ownKeys({ [Symbol()]:0, b:0, 10:0, 2:0, a:0 })
  // ['2', '10', 'b', 'a', Symbol()]
```

## ES5.1 重要扩展

### Object.keys()

`Object.keys` 方法，返回一个数组，成员是参数 **对象自身** 的（不含继承的）所有可遍历（enumerable）属性的 **键名**。

```javascript
  var obj = { foo: 'bar', baz: 42 };
  Object.keys(obj)
  // ["foo", "baz"]
```

**参数**

- 如果是一个空对象，则返回一个空数组

**exception**

+ 在 ES5 里，如果此方法的参数不是对象（而是一个原始值），那么它会抛出 TypeError。
+ 在 ES2015 中，非对象的参数将被强制转换为一个对象。

  ```js
  Object.keys("foo");
  // TypeError: "foo" is not an object (ES5 code)
  
  Object.keys("foo");
  // ["0", "1", "2"]                   (ES6 code)
  ```

+ 如果是 `null` 或者 `undefined` 则会报错

  ```js
  Object.keys(null);
  Object.keys(undefined);
  // Cannot convert undefined or null to object
  ```

+ `Object.values` 和 `Object.entries` 也是一样

## ES6 重要扩展

ES2017 引入了跟 `Object.keys` 配套的 `Object.values` 和 `Object.entries`，作为遍历一个对象的补充手段，供 `for...of` 循环使用。

```javascript
  let {keys, values, entries} = Object;
  let obj = { a: 1, b: 2, c: 3 };
  
  for (let key of keys(obj)) {
    console.log(key); // 'a', 'b', 'c'
  }
  
  for (let value of values(obj)) {
    console.log(value); // 1, 2, 3
  }
  
  for (let [key, value] of entries(obj)) {
    console.log([key, value]); // ['a', 1], ['b', 2], ['c', 3]
  }
```

### Object.values()

`Object.values` 方法返回一个数组，成员是参数 **对象自身** 的（不含继承的）所有可遍历（enumerable）属性的 **键值**。

```javascript
  const obj = { foo: 'bar', baz: 42 };
  Object.values(obj)
  // ["bar", 42]
```

返回数组的成员顺序，与本章的《属性的遍历》部分介绍的排列规则一致。

```javascript
  const obj = { 100: 'a', 2: 'b', 7: 'c' };
  Object.values(obj)
  // ["b", "c", "a"]
  // 上面代码中，属性名为数值的属性，是按照数值大小，从小到大遍历的，因此返回的顺序是b、c、a。
  ```

`Object.values` 只返回对象自身的可遍历属性。

```javascript
  const obj = Object.create({}, {p: {value: 42}});
  Object.values(obj) // []
```

上面代码中，`Object.create` 方法的第二个参数添加的对象属性（属性 `p`），如果不显式声明，默认是不可遍历的，因为 `p` 的属性描述对象的 `enumerable` 默认是 `false`，`Object.values` 不会返回这个属性。只要把 `enumerable` 改成 `true`，`Object.values` 就会返回属性 `p` 的值。

```javascript
  const obj = Object.create({}, {p:
    {
      value: 42,
      enumerable: true
    }
  });
  Object.values(obj) // [42]
```

`Object.values` 会过滤属性名为 Symbol 值的属性。

```javascript
  Object.values({ [Symbol()]: 123, foo: 'abc' });
  // ['abc']
```

**参数**

- 如果 `Object.values` 方法的参数是一个字符串，会返回各个字符组成的一个数组。

  ```javascript
  Object.values('foo')
  // ['f', 'o', 'o']
  // 上面代码中，字符串会先转成一个类似数组的对象。字符串的每个字符，就是该对象的一个属性。因此，`Object.values`返回每个属性的键值，就是各个字符组成的一个数组。
  ```

- 如果参数是一个空对象，则返回空数组
- 如果参数不是对象，`Object.values` 会先将其转为对象。由于数值和布尔值的包装对象，都不会为实例添加非继承的属性。所以，`Object.values` 会返回空数组。

  ```javascript
  Object.values(42) // []
  Object.values(true) // []
  ```

### Object.entries()

`Object.entries()` 方法返回一个数组，成员是参数 **对象自身** 的（不含继承的）所有可遍历（enumerable）属性的 **键值对数组**。

```javascript
  const obj = { foo: 'bar', baz: 42 };
  Object.entries(obj)
  // [ ["foo", "bar"], ["baz", 42] ]
```

除了返回值不一样，该方法的行为与 `Object.values` 基本一致。

如果原对象的属性名是一个 Symbol 值，该属性会被忽略。

  ```javascript
  Object.entries({ [Symbol()]: 123, foo: 'abc' });
  // [ [ 'foo', 'abc' ] ]
  ```

上面代码中，原对象有两个属性，`Object.entries` 只输出属性名非 Symbol 值的属性。将来可能会有 `Reflect.ownEntries()` 方法，返回对象自身的所有属性。

`Object.entries` 的基本用途是 **遍历对象的属性**。

  ```javascript
  let obj = { one: 1, two: 2 };
  for (let [k, v] of Object.entries(obj)) {
    console.log(
      `${JSON.stringify(k)}: ${JSON.stringify(v)}`
    );
  }
  // "one": 1
  // "two": 2
  ```

`Object.entries` 方法的另一个用处是，将对象转为真正的 `Map` 结构。

  ```javascript
  const obj = { foo: 'bar', baz: 42 };
  const map = new Map(Object.entries(obj));
  map // Map { foo: "bar", baz: 42 }
  ```

#### Polyfill

```javascript
  // Generator函数的版本
  function* entries(obj) {
    for (let key of Object.keys(obj)) {
      yield [key, obj[key]];
    }
  }
  
  // 非Generator函数的版本
  function entries(obj) {
    let arr = [];
    for (let key of Object.keys(obj)) {
      arr.push([key, obj[key]]);
    }
    return arr;
  }
```

### Object.fromEntries()

`Object.fromEntries()` 方法是 `Object.entries()` 的逆操作，用于将一个键值对数组转为对象。

```javascript
  Object.fromEntries([
    ['foo', 'bar'],
    ['baz', 42]
  ])
  // { foo: "bar", baz: 42 }
```

该方法的主要目的，是将键值对的数据结构还原为对象，因此特别适合将 Map 结构转为对象。

```javascript
  // 例一
  const entries = new Map([
    ['foo', 'bar'],
    ['baz', 42]
  ]);
  
  Object.fromEntries(entries)
  // { foo: "bar", baz: 42 }
  
  // 例二
  const map = new Map().set('foo', true).set('bar', false);
  Object.fromEntries(map)
  // { foo: true, bar: false }
```

该方法的一个用处是配合 `URLSearchParams` 对象，将查询字符串转为对象。

```javascript
  Object.fromEntries(new URLSearchParams('foo=bar&baz=qux'))
  // { foo: "bar", baz: "qux" }
```

# 克隆

## Api 实现的数组浅拷贝

### Slice() concat()

可以利用数组的一些方法比如：slice、concat 返回一个新数组的特性来实现拷贝。

  ```js
  var arr = ['old', 1, true, null, undefined];
  
  var new_arr = arr.concat();
  var new_arr = arr.slice();
  ```

### 扩展运算符

```js
  var arr = ['old', 1, true, null, undefined];
  var new_arr = [...arr]
```

### Array.from()

如果参数是一个真正的数组，`Array.from` 会返回一个一模一样的新数组，浅克隆

  ```js
  var arr = ['old', 1, true, null, undefined];
  var new_arr = Array.from(arr)
  ```

### 对比

扩展运算符背后调用的是遍历器接口（`Symbol.iterator`），如果一个对象没有部署这个接口，就无法转换。

`Array.from` 方法还支持类似数组的对象。所谓类似数组的对象，本质特征只有一点，即必须有 `length` 属性。因此，任何有 `length` 属性的对象，都可以通过 `Array.from` 方法转为数组，而此时扩展运算符就无法转换。

  ```js
  Array.from({ length: 3 });
  // [ undefined, undefined, undefined ]
  ```

## Object.assign() 实现对象浅拷贝

![Object.assign()](es-object.md#Object.assign())

## Json 实现深拷贝

```js
var arr = ['old', 1, true, ['old1', 'old2'], {old: 1}]

var new_arr = JSON.parse( JSON.stringify(arr) );

console.log(new_arr);
```

有一个问题，不能拷贝函数：

```js
var arr = [function(){console.log(a)}, {b: function(){console.log(b)}}]

var new_arr = JSON.parse(JSON.stringify(arr));

console.log(new_arr);
```

## 浅拷贝的实现

```js
const shallowCopy = function(obj) {
  // 只拷贝对象
  if (typeof obj !== 'object') return;
  // 根据obj的类型判断是新建一个数组还是对象
  const newObj = obj instanceof Array ? [] : {};
  // 遍历obj，并且判断是obj的属性才拷贝
  // for (var key in obj) {
  //   if (obj.hasOwnProperty(key)) newObj[key] = obj[key]
  // }
  for (const [key,value] of Object.entries(obj)) {
    newObj[key] = value
  }
  return newObj;
}
```

## 深拷贝的实现

那如何实现一个深拷贝呢？说起来也好简单，我们在拷贝的时候判断一下属性值的类型，如果是对象，我们递归调用深拷贝函数不就好了~

```js
const deepCopy = function(obj) {
  if (typeof obj !== 'object') return
  if (typeof obj === null) return null
  const newObj = obj instanceof Array ? [] : {}
  for (const [key,value] of Object.entries(obj)) {
    newObj[key] = typeof value === 'object' ? deepCopy(value) : value
  }
  return newObj;
}
```

注意 `null`，可以通过第一层判断，但是 Object.entries() 无法接受 `null` 作为参数, 所以需要再增加一层判断

```js
console.log(deepCopy(null))
// TypeError: Cannot convert undefined or null to object at Function.entries (<anonymous>)
```

如果使用 ES5 API 则会赋值为一个空对象

## 拷贝 Getter 和 Setter

![es-object](programming/font-end/primitive/es/es-object.md#拷贝%20Get%20和%20Set)

## structuredClone()

[structuredClone() - Web API 接口参考 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/structuredClone)

结构化克隆算法是 [由HTML5规范定义](http://www.w3.org/html/wg/drafts/html/master/infrastructure.html#safe-passing-of-structured-data) 的用于复制复杂 JavaScript 对象的算法。通过来自 [Workers](https://developer.mozilla.org/en-US/docs/Web/API/Worker) 的 `postMessage() ` 或使用 [IndexedDB](https://developer.mozilla.org/en-US/docs/Glossary/IndexedDB) 存储对象时在内部使用。它通过递归输入对象来构建克隆，同时保持先前访问过的引用的映射，以避免无限遍历循环。

目前支持的类型有：

- 除 symbols 之外的所有原始类型
- Boolean 对象
- String 对象
- Date
- RegExp （lastIndex 字段不会被保留。）
- Blob
- File
- FileList
- ArrayBuffer
- ArrayBufferView
- ImageData
- Array
- Object
- Map
- Set

不支持：

- 原型链
- Error 对象
- Function
- DOM

### 结构化克隆所不能做到的

`Error` 以及 `Function` 对象是不能被结构化克隆算法复制的；如果你尝试这样子去做，这会导致抛出 `DATA_CLONE_ERR` 的异常。

企图去克隆 DOM 节点同样会抛出 `DATA_CLONE_ERROR` 异常。

对象的某些特定参数也不会被保留

- `RegExp ` 对象的 `lastIndex` 字段不会被保留
- 属性描述符，setters 以及 getters（以及其他类似元数据的功能）同样不会被复制。例如，如果一个对象用属性描述符标记为 read-only，它将会被复制为 read-write，因为这是默认的情况下。
- 原形链上的属性也不会被追踪以及复制。
- symbol 上的属性也不会赋值

### 浏览器实现

用浏览器自身的 API 来实现深度拷贝，有 MessageChannel、history api 、Notification api 等

<http://caibaojian.com/deep-copy.html>

## $.extend()

### 基本用法

jQuery 的 extend 是 jQuery 中应用非常多的一个函数，今天我们一边看 jQuery 的 extend 的特性，一边实现一个 extend!

先来看看 extend 的功能，合并两个或者更多的对象的内容到第一个对象中。

```js
jQuery.extend( target [, object1 ] [, objectN ] )
```

第一个参数 target，表示要拓展的目标，我们就称它为目标对象吧。

后面的参数，都传入对象，内容都会复制到目标对象中，我们就称它们为待复制对象吧。

当两个对象出现相同字段的时候，后者会覆盖前者，**不会进行深层次的覆盖**。

```js
var obj1 = {
  a: 1,
  b: { b1: 1, b2: 2 }
};

var obj2 = {
  b: { b1: 3, b3: 4 },
  c: 3
};

var obj3 = {
  d: 4
}

console.log($.extend(obj1, obj2, obj3));

// {
//    a: 1,
//    b: { b1: 3, b3: 4 },
//    c: 3,
//    d: 4
// }
```

### Extend 第一版

```js
function extend(target, ...options) {
  const len = options.length;
  for (let i = 0; i < len; i++) {
    option = options[i]
    if (option != null) {
      for (let [key, value] of Object.entries(option)) {
        if (value !== undefined) {
          target[key] = value;
        }
      }
    }
    return target
  }
}
```

### Extend 深拷贝

那如何进行深层次的复制呢？jQuery v1.1.4 加入了一个新的用法：

```js
jQuery.extend( [deep], target, object1 [, objectN ] )
```

也就是说，函数的第一个参数可以传一个布尔值，如果为 true，我们就会进行深拷贝，false 依然当做浅拷贝，这个时候，target 就往后移动到第二个参数。

```js
var obj1 = {
  a: 1,
  b: { b1: 1, b2: 2 }
};

var obj2 = {
  b: { b1: 3, b3: 4 },
  c: 3
};

var obj3 = {
  d: 4
}

console.log($.extend(true, obj1, obj2, obj3));

// {
//    a: 1,
//    b: { b1: 3, b2: 2, b3: 4 },
//    c: 3,
//    d: 4
// }
```

因为采用了深拷贝，会遍历到更深的层次进行添加和覆盖。

### Extend 第二版

我们来实现深拷贝的功能，值得注意的是：根据 copy 的类型递归 extend。

```js
function deepExtend(target,...options) {
  var len = options.length;
  // 循环遍历要复制的对象们
for (let i = 0; i < len; i++) {
    option = options[i];
    // 要求不能为空 避免 deepExtend(a,,b) 这种情况
    if (option != null) {
      for (let [key,value] of Object.entries(option)) {
        if (value && typeof value == 'object') {
          // 是对象，进行递归调用
          target[key] = deepExtend(target[key], value)
        }
        else if (value !== undefined){
          // 不是对象，直接赋值
          target[key] = value
        }
      }
    }
  }
  return target;
};
```

在实现上，核心的部分还是跟上篇实现的深浅拷贝函数一致，如果要复制的对象的属性值是一个对象，就递归调用 extend。

### 优化

#### Target 是函数

- 在我们的实现中，`typeof target` 必须等于 `object`，我们才会在这个 `target` 基础上进行拓展，然而我们用 `typeof` 判断一个函数时，会返回 `function`，也就是说，我们无法在一个函数上进行拓展！
- 当然啦，毕竟函数也是一种对象嘛。
- 函数的静态属性也是非常重要的

#### 类型不一致

- 其实我们实现的方法有个小 bug ，不信我们写个 demo:

  ```js
  var obj1 = {
    a: 1,
    b: {
      c: 2
    }
  }
  
  var obj2 = {
    b: {
      c: [5],
  
    }
  }
  
  var d = extend(true, obj1, obj2)
  console.log(d);
  ```

- 我们预期会返回这样一个对象：

  ```js
  {
    a: 1,
    b: {
      c: [5]
    }
  }
  ```

- 实际返回了

  ```js
  {
    a: 1,
    b: {
      c: {
        0: 5
      }
    }
  }
  ```

- 第三遍进行最终的赋值，因为 src 是一个基本类型，我们默认使用一个空对象作为目标值，所以最终的结果就变成了对象的属性！
- 为了解决这个问题，我们需要对目标属性值和待复制对象的属性值进行判断。
- 判断目标属性值跟要复制的对象的属性值类型是否一致:
  - 如果待复制对象属性值类型为数组，目标属性值类型不为数组的话，目标属性值就设为 []
  - 如果待复制对象属性值类型为对象，目标属性值类型不为对象的话，目标属性值就设为 {}

  ```js
  for (let [key,value] of Object.entries(option)) {
    // 排除null和undefined
    if (value && typeof value == 'object') {
      if(Array.isArray(value)){
        // 如果值是数组，保证target也是数组
        target[key] = Array.isArray(target[key])?target[key]:[]
      }
      // 进行递归调用
      target[key] = deepExtend(target[key], value)
    }
    else if (value !== undefined){
      // 不是对象，直接赋值
      target[key] = value
    }
  }
  ```

### 循环引用

实际上，我们还可能遇到一个循环引用的问题，举个例子：

```js
var a = {name : null}
var b = {name : null}
a.name = b
b.name = a
console.log(deepExtend(a, b))
```

我们会得到一个可以无限展开的对象，类似于这样：

![](/img/user/programming/font-end/primitive/es/es-object/image-20230428211700313.png)

为了避免这个问题，我们需要判断要复制的对象属性是否等于 target，如果等于，我们就跳过：

```js
// 对象可能存在循环引用，此时应该跳过
if (target === value) {
  continue;
}
```

使用 WeakMap 来记录已经被拷贝过的对象，如果再次遇到同样的对象，直接返回它的克隆即可，解决了递归方法的循环引用问题。

### 最终代码

```js
function deepExtend(target,...options) {
  var len = options.length;
  // 如果target不是对象，我们是无法进行复制的，所以设为{}
  if (typeof target !== 'object') {
    target = {}
  }
  // 循环遍历要复制的对象们
  for (let i = 0; i < len; i++) {
    option = options[i];
    // 要求不能为空 避免 deepExtend(a,,b) 这种情况
    if (option != null) {
      for (let [key,value] of Object.entries(option)) {
        // 排除null和undefined
        if (value && typeof value == 'object') {
          // 对象可能存在循环引用，此时应该跳过
          if(target === value) continue

          // 如果值是数组，保证target也是数组
          if(Array.isArray(value)) target[key] = Array.isArray(target[key])?target[key]:[]

          // 进行递归调用
          target[key] = deepExtend(target[key], value)
        }else if (value !== undefined){
          // 不是对象，直接赋值
          target[key] = value
        }
      }
    }
  }
  return target;
};
```

### 思考题

如果觉得看明白了上面的代码，想想下面两个 demo 的结果：

```js
var a = extend(true, [4, 5, 6, 7, 8, 9], [1, 2, 3]);
console.log(a) // ??? 
```

![](/img/user/programming/font-end/primitive/es/es-object/image-20230428211641146.png)

```js
var obj1 = {
  value: {
    3: 1
  }
}
var obj2 = {
  value: [5, 6, 7],
}

var b = extend(true, obj1, obj2) // ??? {value:[5,6,7]}
var c = extend(true, obj2, obj1) // ??? {value:[5,6,7,1]}
```

# 类型判断

## Typeof

在 ES6 前，JavaScript 共六种数据类型，分别是：

> Undefined、Null、Boolean、Number、String、Object

typeof 对这些数据类型的值进行操作的时候，返回的结果却不是一一对应，分别是：

> undefined、object、boolean、number、string、object

**注意：返回的字符串都是小写的**，`typeof NaN // number`

可以判断: undefined/ 数值 / 字符串 / 布尔值 / **function**

不能区分: null 与 object object 与 array , 不能区分出 object 的类

```js
typeof null //object
typeof array //object
typeof NaN // number
```

在 JavaScript 最初的实现中，JavaScript 中的值是由一个表示类型的标签和实际数据值表示的。对象的类型标签是 0。由于 `null` 代表的是空指针（大多数平台下值为 0x00），因此，null 的类型标签也成为了 0，`typeof null` 就错误的返回了 "`object"`。（[reference](http://www.2ality.com/2013/10/typeof-null.html)）

ECMAScript 提出了一个修复（通过 opt-in），但 [被拒绝](http://wiki.ecmascript.org/doku.php?id=harmony:typeof_null)。这使得 typeof null === 'object' 将成为长期的错误

## Instanceof

![es-object](programming/font-end/primitive/es/es-object.md#Instanceof)

## 全等运算符

可以判断: undefined, null

因为这两个类型都只有一个值

falsy

## Object.prototype.tostring.call(target)

返回固定字符串： `[object Target 类型]`

`Object.prototype.toString.call(target).slice(8,-1)`，就可以返回准确的类型

ES5 开始，可以检查 Null 和 undefined。ES5 规范地址：<https://es5.github.io/#x15.2.4.2>。

当 `Object.prototype.toString` 方法被调用的时候，下面的步骤会被执行：

> 1. 如果 this 值是 undefined，就返回 `[object Undefined]`
> 2. 如果 this 的值是 null，就返回 `[object Null]`
> 3. 让 O 成为 ToObject(this) 的结果
> 4. 让 class 成为 O 的内部属性 ` [[Class]]` 的值
> 5. 最后返回由 "[object " 和 class 和 "]" 三个部分组成的字符串

无法判断自定义类型

Object.prototype.toString 可以识别出至少 12 种数据类型

```js
// 以下是12种：
var number = 1;          // [object Number]
var string = '123';      // [object String]
var boolean = true;      // [object Boolean]
var und = undefined;     // [object Undefined]
var nul = null;          // [object Null]
var obj = {a: 1}         // [object Object]
var array = [1, 2, 3];   // [object Array]
var date = new Date();   // [object Date]
var error = new Error(); // [object Error]
var reg = /a/g;          // [object RegExp]
var func = function a(){}; // [object Function]
// ES6
const sym = Symbol() // [object Symbol]

function checkType() {
  for (var i = 0; i < arguments.length; i++) {
    console.log(Object.prototype.toString.call(arguments[i]))
  }
}

checkType(number, string, boolean, und, nul, obj, array, date, error, reg, func)
```

```js
console.log(Object.prototype.toString.call(Math)); // [object Math]
console.log(Object.prototype.toString.call(JSON)); // [object JSON]
console.log(Object.prototype.toString.call(window)); // [object Window]
```

```js
function a() {
    console.log(Object.prototype.toString.call(arguments)); // [object Arguments]
}
a();
```

**注意：**

- Array.prototype 的类型 **也是数组**，类似的 Number.prototype 是 number 类型的对象
- 实例本身是没有 `constructor` 属性的，`constructor` 属性在原型上，为了保持一致性，所以 `Array.prototype` 也是 `Array`

## 封装 Type 函数

写一个 type 函数能检测各种类型的值

- 基本类型，就使用 typeof
- 引用类型就使用 toString
- 此外鉴于 typeof 的结果是小写，我也希望所有的结果都是小写。
- 考虑到实际情况下并不会检测 Math 和 JSON，所以去掉这两个类型的检测。

```js
// 第一版
var class2type = {};

// 生成class2type映射
"Boolean Number String Function Array Date RegExp Object Error Null Undefined".split(" ").map(function(item, index) {
    class2type["[object " + item + "]"] = item.toLowerCase();
})

function type(obj) {
    return typeof obj === "object" || typeof obj === "function" ?
        class2type[Object.prototype.toString.call(obj)] || "object" :
        typeof obj;
}
```

## Isfunction

有了 type 函数后，我们可以对常用的判断直接封装，比如 isFunction:

```js
function isFunction(obj) {
    return type(obj) === "function";
}
```

## IsArray

```js
var isArray = Array.isArray || function( obj ) {
    return type(obj) === "array";
}
```

原型和构造函数都有被修改的可能性

## Isarraylike

isArrayLike，看名字可能会让我们觉得这是判断类数组对象的，其实不仅仅是这样，jQuery 实现的 isArrayLike，数组和类数组都会返回 true

```js
function isArrayLike(obj) {

  // obj 必须有 length属性
  var length = !!obj && "length" in obj && obj.length;
  var typeRes = type(obj);

  // 排除掉函数和 Window 对象
  if (typeRes === "function" || isWindow(obj)) {
    return false;
  }

  return typeRes === "array" || length === 0 ||
    typeof length === "number" && length > 0 && (length - 1) in obj;
}
```

重点分析 return 这一行，使用了或语句，只要一个为 true，结果就返回 true。

所以如果 isArrayLike 返回 true，至少要满足三个条件之一：

1. 是数组
2. 长度为 0
3. lengths 属性是大于 0 的数字类型，并且 `obj[length - 1]` 必须存在

第一个就不说了，看第二个，为什么长度为 0 就可以直接判断为 true 呢？

那我们写个对象：

```js
var obj = {a: 1, b: 2, length: 0}
```

isArrayLike 函数就会返回 true，那这个合理吗？

回答合不合理之前，我们先看一个例子：

```js
function a(){
    console.log(isArrayLike(arguments))
}
a();
```

如果我们去掉 length === 0 这个判断，就会打印 false，然而我们都知道 arguments 是一个类数组对象，这里是应该返回 true 的。

所以是不是为了放过空的 arguments 时也放过了一些存在争议的对象呢？

第三个条件：length 是数字，并且 length > 0 且最后一个元素存在。

为什么仅仅要求最后一个元素存在呢？

让我们先想下数组是不是可以这样写：

```json
var arr = [,,3]
```

当我们写一个对应的类数组对象就是：

```js
var arrLike = {
    2: 3,
    length: 3
}
```

也就是说当我们在数组中用逗号直接跳过的时候，我们认为该元素是不存在的，类数组对象中也就不用写这个元素，但是最后一个元素是一定要写的，要不然 length 的长度就不会是最后一个元素的 key 值加 1。比如数组可以这样写

```
var arr = [1,,];
console.log(arr.length) // 2
```

但是类数组对象就只能写成：

```js
var arrLike = {
    0: 1,
    length: 1
}
```

所以符合条件的类数组对象是一定存在最后一个元素的！

这就是满足 isArrayLike 的三个条件，其实除了 jQuery 之外，很多库都有对 isArrayLike 的实现，比如 underscore:

```js
var MAX_ARRAY_INDEX = Math.pow(2, 53) - 1;

var isArrayLike = function(collection) {
    var length = getLength(collection);
    return typeof length == 'number' && length >= 0 && length <= MAX_ARRAY_INDEX;
};
```

## Isplainobject

plainObject 来自于 jQuery，可以翻译成纯粹的对象，所谓 " 纯粹的对象 "，就是该对象是通过 "{}" 或 "new Object" 创建的，该对象含有零个或者多个键值对。

之所以要判断是不是 plainObject，是为了跟其他的 JavaScript 对象如 null，数组，宿主对象（documents）等作区分，因为这些用 typeof 都会返回 object。

jQuery 提供了 isPlainObject 方法进行判断，先让我们看看使用的效果：

```js
function Person(name) {
  this.name = name;
}

console.log($.isPlainObject({})) // true

console.log($.isPlainObject(new Object)) // true

console.log($.isPlainObject(Object.create(null))); // true

console.log($.isPlainObject(Object.assign({a: 1}, {b: 2}))); // true

console.log($.isPlainObject(new Person('yayu'))); // false

console.log($.isPlainObject(Object.create({}))); // false
```

由此我们可以看到，除了 {} 和 new Object 创建的之外，jQuery 认为一个 **没有原型的对象** 也是一个纯粹的对象。

isPlainObject 的实现也在变化，我们今天讲的是 3.0 版本下的 isPlainObject，我们直接看源码：

```js
// 上节中写 type 函数时，用来存放 toString 映射结果的对象
var class2type = {};

// 相当于 Object.prototype.toString
var toString = class2type.toString;

// 相当于 Object.prototype.hasOwnProperty
var hasOwn = class2type.hasOwnProperty;

function isPlainObject(obj) {
  var proto, Ctor;

  // 排除掉明显不是obj的以及一些宿主对象如Window
  if (!obj || toString.call(obj) !== "[object Object]") {
    return false;
  }

  proto = Object.getPrototypeOf(obj);

  // 没有原型的对象是纯粹的，Object.create(null) 就在这里返回 true
  if (!proto) {
    return true;
  }

  /**
   * 以下判断通过 new Object 方式创建的对象
   * 判断 proto 是否有 constructor 属性，如果有就让 Ctor 的值为 proto.constructor
   * 如果是 Object 函数创建的对象，Ctor 在这里就等于 Object 构造函数
   */
  Ctor = hasOwn.call(proto, "constructor") && proto.constructor;

  // 在这里判断 Ctor 构造函数是不是 Object 构造函数，用于区分自定义构造函数和 Object 构造函数
  return typeof Ctor === "function" && hasOwn.toString.call(Ctor) === hasOwn.toString.call(Object);
}
```

注意：我们判断 Ctor 构造函数是不是 Object 构造函数，用的是 ` hasOwn.toString.call(Ctor)`，这个方法可不是 `Object.prototype.toString`，不信我们在函数里加上下面这两句话：

```js
console.log(hasOwn.toString.call(Ctor)); // function Object() { [native code] }
console.log(Object.prototype.toString.call(Ctor)); // [object Function]
```

发现返回的值并不一样，这是因为 `hasOwn.toString` 调用的其实是 ` Function.prototype.toString`，毕竟 `hasOwnProperty ` 可是一个函数！

而且 Function 对象覆盖了从 Object 继承来的 Object.prototype.toString 方法。函数的 toString 方法会返回一个表示函数源代码的字符串。具体来说，包括 function 关键字，形参列表，大括号，以及函数体中的内容。

**因此**：这里要判断的其实是两个函数是否一样

## Isemptyobject

用 `for...in` 遍历

```js
for (var i in obj) { // 如果不为空，则会执行到这一步，返回true
    return false
}
return true // 如果为空,返回false
```

其实所谓的 ` isEmptyObject ` 就是判断是否有属性，for 循环一旦执行，就说明有属性，有属性就会返回 false。

但是根据这个源码我们可以看出 isEmptyObject 实际上判断的并不仅仅是空对象。

```js
console.log(isEmptyObject({})); // true
console.log(isEmptyObject([])); // true
console.log(isEmptyObject(null)); // true
console.log(isEmptyObject(undefined)); // true
console.log(isEmptyObject(1)); // true
console.log(isEmptyObject('')); // true
console.log(isEmptyObject(true)); // true
```

但是既然 jQuery 是这样写，可能是因为考虑到实际开发中 isEmptyObject 用来判断 {} 和 {a: 1} 是足够的吧。如果真的是只判断 {}，完全可以结合上篇写的 type 函数筛选掉不适合的情况。

通过 `JSON` 自带的 `stringify()` 方法来判断

```js
if (JSON.stringify(data) === '{}') {
    return true // 如果为空,返回false
}
return false // 如果不为空，返回true
```

依靠 `Object.keys()`

```js
if (Object.keys(object).length === 0) {
    return true // 如果为空,返回false
}
return false // 如果不为空，则会执行到这一步，返回true
```

## isWindow

Window 对象作为客户端 JavaScript 的全局对象，它有一个 window 属性指向自身，这点在 [《JavaScript深入之变量对象》](https://github.com/mqyqingfeng/Blog/issues/5) 中讲到过。我们可以利用这个特性判断是否是 Window 对象。

```js
function isWindow( obj ) {
    return obj != null && obj === obj.window;
}
```

## isElement

isElement 判断是不是 DOM 元素。

```js
isElement = function(obj) {
    return !!(obj && obj.nodeType === 1);
};
```

# 等值判断

## object.is()

ES5 比较两个值是否相等，只有两个运算符：相等运算符（\=\=）和严格相等运算符（\=\=\=）。它们都有缺点，前者会自动转换数据类型，后者的 `NaN` 不等于自身，以及 `+0` 等于 `-0`。JavaScript 缺乏一种运算，在所有环境中，只要两个值是一样的，它们就应该相等。

ES6 提出“Same-value equality”（同值相等）算法，用来解决这个问题。`Object.is` 就是部署这个算法的新方法。它用来比较两个值是否严格相等，与严格比较运算符（===）的行为基本一致。

不同之处只有两个：一是 `+0` 不等于 `-0`，二是 `NaN` 等于自身。

```javascript
+0 === -0 //true
NaN === NaN // false

Object.is(+0, -0) // false
Object.is(NaN, NaN) // true
```

## +0 和 -0

如果 a === b 的结果为 true， 那么 a 和 b 就是相等的吗？一般情况下，当然是这样的，但是有一个特殊的例子，就是 +0 和 -0。

JavaScript “处心积虑”的想抹平两者的差异：

```js
// 表现1
console.log(+0 === -0); // true

// 表现2
(-0).toString() // '0'
(+0).toString() // '0'

// 表现3
-0 < +0 // false
+0 < -0 // false
```

即便如此，两者依然是不同的：

```ts
1 / +0 // Infinity
1 / -0 // -Infinity

1 / +0 === 1 / -0 // false
```

也许你会好奇为什么要有 +0 和 -0 呢？

这是因为 JavaScript 采用了 IEEE_754 浮点数表示法 (几乎所有现代编程语言所采用)，这是一种二进制表示法，按照这个标准，最高位是符号位 (0 代表正，1 代表负)，剩下的用于表示大小。而对于零这个边界值 ，1000(-0) 和 0000(0) 都是表示 0 ，这才有了正负零的区别。

也许你会好奇什么时候会产生 -0 呢？

```ts
Math.round(-0.1) // -0
```

那么我们又该如何在 === 结果为 true 的时候，区别 0 和 -0 得出正确的结果呢？我们可以这样做：

```js
function eq(a, b){
    if (a === b) return a !== 0 || 1 / a === 1 / b;
    return false;
}

console.log(eq(0, 0)) // true
console.log(eq(0, -0)) // false
```

## NaN

在本篇，我们认为 NaN 和 NaN 是相等的，那又该如何判断出 NaN 呢？

```js
console.log(NaN === NaN); // false
```

利用 NaN 不等于自身的特性，我们可以区别出 NaN，那么这个 eq 函数又该怎么写呢？

```js
function eq(a, b) {
    if (a !== a) return b !== b;
}

console.log(eq(NaN, NaN)); // true
```

## DeepEqual

### String 对象

现在我们开始写 deepEq 函数，一个要处理的重大难题就是 'Curly' 和 new String('Curly') 如何判断成相等？

两者的类型都不一样呐！不信我们看 typeof 的操作结果：

```js
console.log(typeof 'Curly'); // string
console.log(typeof new String('Curly')); // object
```

可是我们在 [《JavaScript专题之类型判断上》](https://github.com/mqyqingfeng/Blog/issues/28) 中还学习过更多的方法判断类型，比如 Object.prototype.toString：

```js
var toString = Object.prototype.toString;
toString.call('Curly'); // "[object String]"
toString.call(new String('Curly')); // "[object String]"
```

神奇的是使用 toString 方法两者判断的结果却是一致的，可是就算知道了这一点，还是不知道如何判断字符串和字符串包装对象是相等的

那我们利用隐式类型转换呢？

```ts
console.log('Curly' + '' === new String('Curly') + ''); // true
```

看来我们已经有了思路：如果 a 和 b 的 Object.prototype.toString 的结果一致，并且都是 "[object String]"，那我们就使用 '' + a === '' + b 进行判断。

可是不止有 String 对象呐，Boolean、Number、RegExp、Date 呢？

### 更多对象

跟 String 同样的思路，利用隐式类型转换。

**Boolean**

```js
var a = true;
var b = new Boolean(true);

console.log(+a === +b) // true
```

**Date**

```js
var a = new Date(2009, 9, 25);
var b = new Date(2009, 9, 25);

console.log(+a === +b) // true
```

**RegExp**

```js
var a = /a/i;
var b = new RegExp(/a/i);

console.log('' + a === '' + b) // true
```

**Number**

```js
var a = 1;
var b = new Number(1);

console.log(+a === +b) // true
```

嗯哼？你确定 Number 能这么简单的判断？

```js
var a = Number(NaN);
var b = Number(NaN);

console.log(+a === +b); // false
```

可是 a 和 b 应该被判断成 true 的呐~

那么我们就改成这样：

```js
var a = Number(NaN);
var b = Number(NaN);

function eq() {
    // 判断 Number(NaN) Object(NaN) 等情况
    if (+a !== +a) return +b !== +b;
    // 其他判断 ...
}

console.log(eq(a, b)); // true
```

#### 基本实现

```js
var toString = Object.prototype.toString;

function deepEq(a, b) {
    var className = toString.call(a);
    if (className !== toString.call(b)) return false;

    switch (className) {
        case '[object RegExp]':
        case '[object String]':
            return '' + a === '' + b;
        case '[object Number]':
            if (+a !== +a) return +b !== +b;
            return +a === 0 ? 1 / +a === 1 / b : +a === +b;
      case '[object Date]':
      case '[object Boolean]':
            return +a === +b;
    }

    // 其他判断
}
```

## isEqualObject

```js
/**
 * 用于判断是否为值相同对象（若有value为对象，则对指定key进行比较）
 *
 * @param {Object} x 目标对象
 * @param {Object} y 对比对象
 * @return {boolean} 比较结果，true表示相同，false为不同
 */
export const isEqualObject = (x = {}, y = {}) => {
    let inx = x instanceof Object;
    let iny = y instanceof Object;
    if (!inx || !iny) {
        return x === y;
    }

    if (Object.keys(x).length !== Object.keys(y).length) {
        return false;
    }

    for (let key in x) {
        let a = x[key] instanceof Object;
        let b = y[key] instanceof Object;
        if (a && b) {
            if (!isEqualObject(x[key], y[key])) {
                return false;
            }
        }
        else if (x[key] !== y[key]) {
            return false;
        }

    }
    return true;
};
```

# FAQ

#faq/js

## 创建一个空对象

Object.create(null) 与 {} 的区别：`b={}` 相当于 `b=new Object`，因此，`b` 是 `Object` 构造函数的实例。

而 `Object.create(null)` 会创建一个空对象，它没有原型。

## Freeze

如果真的想将对象冻结，应该使用 `Object.freeze` 方法。

```javascript
const foo = Object.freeze({});

// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;
```

上面代码中，常量 `foo` 指向一个冻结的对象，所以添加新属性不起作用，严格模式时还会报错。

除了将对象本身冻结，对象的属性也应该冻结。下面是一个将对象彻底冻结的函数。

```javascript
var constantize = (obj) => {
  Object.freeze(obj);
  Object.keys(obj).forEach( (key, i) => {
    if ( typeof obj[key] === 'object' ) {
      constantize( obj[key] );
    }
  });
};
```

## 克隆

[克隆](es-object.md#克隆)

## 类型判断

[es-object](programming/font-end/primitive/es/es-object.md#类型判断)

## 等值判断

[es-object](programming/font-end/primitive/es/es-object.md#等值判断)
