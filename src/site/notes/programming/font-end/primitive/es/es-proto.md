---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/es/es-proto/"}
---


# 原型

## 原型与原型链

### 显式原型：prototype

每个**函数**function 都有一个 prototype，即显式原型（属性），它默认指向一个 Object 空对象 (即称为: 原型对象)

  - js 在 **定义函数** 的时候，内部有一条内部语句：`Fn.prototype={}`
  - 因此不同的函数 prototype 是不相等的
  - 默认值是一个空 Object 对象

内建函数指向的 Object 有许多定义好的方法

### 隐式原型：`__proto__`

每个 **实例对象** 都有一个 `__proto__`，可称为隐式原型（属性）

  - **对象的隐式原型的值为其对应构造函数的显式原型的值**
  - **通过构造函数创建对象** 的时候，内部有一条语句：`this.__proto__=Fn.prototype`
  - 默认值为构造函数的 prototype 属性值
  - 程序员能直接操作显式原型, 但不能直接操作隐式原型 (`ES5` 之前)，现在可以使用 `getPrototypeOf()` 获取实例的隐式原型

函数可以看作是一个特殊的对象, 所以所有的函数也同时具有 `__proto__` 属性

  ![1546691834085](/img/user/programming/font-end/primitive/es/es-proto/1546691834085.png)

### 注意

`__proto__`, 绝大部分浏览器都支持这个非标准的方法访问原型, 然而它并不存在于 Person.prototype 中, 实际上, 它是来自于 Object.prototype, 与其说是一个属性, 不如说是一个 getter/setter, 当使用 obj.`__proto__` 时，可以理解成返回了 `Object.getPrototypeOf(obj)`。

该属性没有写入 ES6 的正文，而是写入了附录，原因是 `__proto__` 前后的双下划线，说明它本质上是一个内部属性，而不是一个正式的对外的 API，只是由于浏览器广泛支持，才被加入了 ES6。标准明确规定，只有浏览器必须部署这个属性，其他运行环境不一定需要部署，而且新的代码最好认为这个属性是不存在的。

因此，无论从语义的角度，还是从兼容性的角度，都不要使用这个属性，而是使用下面的 `Object.setPrototypeOf()`（写操作）、`Object.getPrototypeOf()`（读操作）、`Object.create()`（生成操作）代替。

从最基本的原型的概念上来理解, 实例的原型确实应该和构造函数的原型保持一致, 所以静态方法中也直接使用 prototype 这个单词, 没有问题, 因为他们指向的是同一个 prototype

### Constructor

原型对象也有一个属性叫 **constructor**，这个属性包含一个指针，指向 **原构造函数**。

虽然 **原型对象** 也是 **某个构造函数** 的实例，但是因为它是原型对象，所有 constructor 被设计成指向实例 **构造函数**

```javascript
    function Foo(){}
    Foo.prototype instanceof Foo //false
    Foo.prototype.constructor === Foo //true
    //构造函数是Foo，但却不是Foo的实例
```

  - 原型对象，constructor 和 instanceof 不一致
  - 普通对象，constructor 和 instanceof 一致

本质：**实例对象**，本身是没有 constructor 属性的，它的 constructor 需要到它的 **原型链** 去找。所以 Foo.prototype**确实是 Object 的实例**，如果可以屏蔽本身的 constructor 属性，就会沿着原型链向上查找，

```javascript
    function Foo(){}
    Foo.prototype.constructor === Object //false
    delete Foo.prototype.constructor
    Foo.prototype.constructor === Object //true
```

### 原型链

原型链 (图解)

![1546692183103](/img/user/programming/font-end/primitive/es/es-proto/1546692183103.png)

可以直接访问到的对象只有栈内存的三个

变量找作用域链，对象的属性找原型链

访问一个对象的属性时，先在自身属性中查找，找到返回如果没有, 再沿着 `__proto__` 这条链向上查找, 找到返回如果最终没找到, 返回 undefined

别名: **隐式原型链**

作用: 查找对象的属性 (方法)

构造函数/原型/实体对象的关系 2(图解)

![1546692302049](/img/user/programming/font-end/primitive/es/es-proto/1546692302049.png)

### 总结

所有的对象都源于 Object

函数的显示原型指向对象，默认是空 Object**实例** 对象（不包括 Object 构造函数，Object 的 prototype 不是实例对象）

所有的函数源于 Function 构造函数

  - 推论 1：所有函数都是 Function 的实例（包括 Function 本身）
  - 推论 2：所有函数的隐式原型 ``__proto__`` 都是一样的，包括 Function 自己

  ```js
  Function = new Function()
  Function.__proto__=== Function.prototype // true
  ```

凡是实例，都会经过 `Object.prototype`

所有的函数，都是由 Function 构造的（都是 Function 的实例），凡是函数都会经过 `Function.porotype` 和 ` Object.prototype`

Object.create(prototype, [descriptors]) 相当于是在 `o1, o2` 再接一个 `__proto__` 指向 `o1 ,o2` 的对象，他们没有其他 constructor 和 prototype 属性，因为它们都是实例

![1546692426651](/img/user/programming/font-end/primitive/es/es-proto/1546692426651.png)

### Instanceof 原理

`instanceof` 是如何判断的?

表达式: `A instanceof B`

如果 B 函数的显式原型对象在 A 对象的原型链上, 返回 true, 否则返回 false

Function 是通过 new 自己产生的实例

![1546692412784](/img/user/programming/font-end/primitive/es/es-proto/1546692412784.png)

#### Instanceof 的底层实现原理

```js
function instanceof(left, right) {
  // 获得类型的原型
  let prototype = right.prototype
  // 获得对象的原型
  left = left.__proto__
  // 判断对象的类型是否等于类型的原型
  while (true) {
    if (left === null)
      return false
    if (prototype === left)
      return true
    left = left.__proto__
  }
}
```

### 原型链的面试题

![1546692544602](/img/user/programming/font-end/primitive/es/es-proto/1546692544602.png)

![1546692548349](/img/user/programming/font-end/primitive/es/es-proto/1546692548349.png)

#### 题目

```js
  function func(){}
  
  typeof func.prototype // object
  typeof func.__proto__ // function Function.prototype
  typeof Function.__proto // function Function.prototype
```

### 内置对象的原型 @@@

#### 显式原型

如果是用户自定义的函数，它的 `prototype` 属性就是个普通对象，如果是内置类型的构造函数的话，它的 `prototype` 属性会是该类型的第一个实例：

  ```js
  Number.prototype // Object(0)
  String.prototype // Object("")
  Boolean.prototype // Object(false)
  Array.prototype // []
  Function.prototype // function(){}
  Date.prototype // new Date(NaN)
```

后面 ES3 里又依照这个设定添加了：

```js
  Error.prototype // new Error("")
  RegExp.prototype // /(?:)/
```

到了 2012 年，ES6 的编辑 Allen Wirfs-Brock 表示 ES6 里新增的内置类型的原型对象都会是一个普通对象 而不是该类型的实例。理由是从 ES6 开始会新增很多内置类型（`Symbol.prototype`、`Promise.prototype`、`Generator.prototype` ...），如果像之前一样把这些类型的原型对象都规定为该类型的第一个实例，会白白增加规范的复杂度，还没什么实际用途，而且如果把 `Map.prototype` 规定为一个全局的空 map，那么人们会不会去往这个 map 里存东西呢？没理由这么设计。

不仅如此，经过讨论，TC39 还想尝试把老的内置类型的原型对象也改为普通对象，不过经过一些调研，发现还真有人在直接使用 `Function.prototype`（作为 noop 用）和 `Array.prorotype`（underscore 用了） [https://bugzilla.mozilla.org/show_bug.cgi?id=797686#c2](http://link.zhihu.com/?target=https%3A//bugzilla.mozilla.org/show_bug.cgi%3Fid%3D797686%23c2)， 所以这两个类型就不改了，ES6 里只改了其它 6 个类型。

不过 ES6 发布没多久，就爆出 break the web 的问题，Mootools 里有用到 `Number.prototype`，导致一些网站挂了 [https://github.com/mootools/mootools-core/issues/2711](http://link.zhihu.com/?target=https%3A//github.com/mootools/mootools-core/issues/2711)，于是在 ES7 里回滚了对 `Number.prototype` 的改动 ，同时保险起见，也把 `String.prototype` 和 `Boolean.prototype` 也都回滚了。

又过了一段时间，发现有些网站会调用 `RegExp.prototype.toString()`，会报错，所以 ES8 又不得不进行了兼容，让 `RegExp.prototype` 保持是一个普通对象的同时，对它进行特殊处理，让 `toString()` 返回`"/(?:)/"

#### 隐式原型

不论是原始数据类型还是引用数据类型，因为他们都是实例，所以不一定有 `prototype` 属性，但是都具有隐式原型

```js
  console.log(typeof '123')
  console.log('123'.prototype)
  console.log((123).prototype)
  console.log((true).prototype)
  console.log(Object.getPrototypeOf('abc'))
```

## ES5.1 相关扩展

### Object.create(proto, \[propertiesObject]) @@@ #

**Object.create()** 方法创建一个新对象，使用现有的对象来提供新创建的对象的 `__proto__`

返回一个新对象，带着 **指定的原型对象和属性**。

最直观的表现：`o.__proto__ = Constructor.prototype`

**参数**

- proto：新创建对象的原型对象
- propertiesObject：可选。如果没有指定为 `undefined`，则是要添加到新创建对象的 **可枚举属性**（即其自身定义的属性，而不是其原型链上的枚举属性）对象的 **属性描述符** 以及相应的 **属性名称**。这些属性对应 `Object.defineProperties()` 的 **第二个参数**
- 如果 `propertiesObject` 参数不是 `null` 或一个对象，则抛出一个 `TypeError` 异常

#### 创建对象

```js
  var o;
  // 创建一个原型为null的空对象
  o = Object.create(null);
  o = {};
  // 以字面量方式创建的空对象就相当于:
  o = Object.create(Object.prototype);
  
  o = Object.create(Object.prototype, {
    // foo会成为所创建对象的数据属性
    foo: { 
      writable:true,
      configurable:true,
      value: "hello" 
    },
    // bar会成为所创建对象的访问器属性
    bar: {
      configurable: false,
      get: function() { return 10 },
      set: function(value) {
        console.log("Setting `o.bar` to", value);
      }
    }
  });
  
  
  function Constructor(){}
  o = new Constructor()
  // 上面的一句就相当于:
  o.__proto__ = Constructor.prototype
  // 当然,如果在Constructor函数中有一些初始化代码,Object.create不能执行那些代码
  
  
  // 创建一个以另一个空对象为原型,且拥有一个属性p的对象
  o = Object.create({}, { p: { value: 42 } })
  
  // 省略了的属性特性默认为false,所以属性p是不可写,不可枚举,不可配置的:
  o.p = 24
  o.p //42
  
  o.q = 12
  for (var prop in o) {
    console.log(prop)
  }
  //"q"
  
  delete o.p
  //false
  
  //创建一个可写的,可枚举的,可配置的属性p
  o2 = Object.create({}, {
    p: {
      value: 42, 
      writable: true,
      enumerable: true,
      configurable: true 
    } 
  });
```

#### 实现类式继承

```js
  // Shape - 父类(superclass)
  function Shape() {
    this.x = 0;
    this.y = 0;
  }
  
  // 父类的方法
  Shape.prototype.move = function(x, y) {
    this.x += x;
    this.y += y;
    console.info('Shape moved.');
  };
  
  // Rectangle - 子类(subclass)
  function Rectangle() {
    Shape.call(this); // call super constructor.
  }
  
  // 子类续承父类
  Rectangle.prototype = Object.create(Shape.prototype,{
    constructor: {
    value: Rectangle,
    enumerable: false,
    writable: true,
    configurable: true
  }
  });
  //Rectangle.prototype.constructor = Rectangle;
  
  var rect = new Rectangle();
  
  console.log('Is rect an instance of Rectangle?',
    rect instanceof Rectangle); // true
  console.log('Is rect an instance of Shape?',
    rect instanceof Shape); // true
  rect.move(1, 1); // Outputs, 'Shape moved.'
  ```

如果你希望能继承到多个对象，则可以使用混入的方式。

```js
  function MyClass() {
       SuperClass.call(this);
       OtherSuperClass.call(this);
  }
  
  // 继承一个类
  MyClass.prototype = Object.create(SuperClass.prototype);
  // 混合其它
  Object.assign(MyClass.prototype, OtherSuperClass.prototype);
  // 重新指定constructor
  MyClass.prototype.constructor = MyClass;
  
  MyClass.prototype.myMethod = function() {
       // do a thing
  };
```

### Polyfill

```js
  function create(proto) {
      function F() {}
      F.prototype = proto
      F.prototype.constructor = F
  
      return new F()
  }
```

### 注意 @@@

Object.create(null) 与 {} 的区别：`b={}` 相当于 `b=new Object`，因此，`b` 是 `Object` 构造函数的实例。

而 `Object.create(null)` 会创建一个空对象，它没有原型。

### Object.setPrototypeOf(obj, prototype)

`Object.setPrototypeOf` 方法的作用与 `__proto__` 相同，用来设置一个对象的 `prototype` 对象，返回 **参数对象本身**。它是 ES6 **正式推荐** 的设置原型对象的方法。 详见 [性能问题](es-proto.md#性能问题)

```javascript
  const o = Object.setPrototypeOf({}, null);
  
  // 相当于
  function setPrototypeOf(obj, proto) {
    obj.__proto__ = proto;
    return obj;
  }
```

**参数**

- 如果第一个参数不是对象，会自动转为对象。但是由于返回的还是第一个参数，所以这个操作不会产生任何效果。

  ```javascript
  Object.setPrototypeOf(1, {}) === 1 // true
  Object.setPrototypeOf('foo', {}) === 'foo' // true
  Object.setPrototypeOf(true, {}) === true // true
  ```

- 由于 `undefined` 和 `null` 无法转为对象，所以如果第一个参数是 `undefined` 或 `null`，就会报错。

  ```javascript
  Object.setPrototypeOf(undefined, {})
  // TypeError: Object.setPrototypeOf called on null or undefined
  
  Object.setPrototypeOf(null, {})
  // TypeError: Object.setPrototypeOf called on null or undefined
  ```

## ES6 相关扩展

### Object.getPrototypeOf(object)

该方法与 `Object.setPrototypeOf` 方法配套，用于读取一个对象的原型对象。

  ```javascript
  function Rectangle() {
    // ...
  }
  
  const rec = new Rectangle();
  
  Object.getPrototypeOf(rec) === Rectangle.prototype
  // true
  
  Object.setPrototypeOf(rec, Object.prototype);
  Object.getPrototypeOf(rec) === Rectangle.prototype
  // false
  ```

**参数**

- 如果参数不是对象，会被自动转为对象。

```javascript
  // 等同于 Object.getPrototypeOf(Number(1))
  Object.getPrototypeOf(1)
  // Number {[[PrimitiveValue]]: 0}
  
  // 等同于 Object.getPrototypeOf(String('foo'))
  Object.getPrototypeOf('foo')
  // String {length: 0, [[PrimitiveValue]]: ""}
  
  // 等同于 Object.getPrototypeOf(Boolean(true))
  Object.getPrototypeOf(true)
  // Boolean {[[PrimitiveValue]]: false}
  
  Object.getPrototypeOf(1) === Number.prototype // true
  Object.getPrototypeOf('foo') === String.prototype // true
  Object.getPrototypeOf(true) === Boolean.prototype // true
```

- 如果参数是 `undefined` 或 `null`，它们无法转为对象，所以会报错。

```javascript
  Object.getPrototypeOf(null)
  // TypeError: Cannot convert undefined or null to object
  
  Object.getPrototypeOf(undefined)
  // TypeError: Cannot convert undefined or null to object
```

## 总结

虽然方法名都是 `prototype`，但是实际上访问的是 `__proto__`

## Object.create() Vs Object.setPrototypeOf()

由于现代 JavaScript 引擎优化属性访问所带来的特性的关系，更改对象的 `[[Prototype]]` 在各个浏览器和 JavaScript 引擎上都是一个**很慢**的操作。

其在更 改继承的性能上的影响是微妙而又广泛的，这不仅仅限于 `obj.__proto__ = ...` 语句上的时间花费，而且可能会延伸到任何代码，那些可以访问任何 `[[Prototype]]` 已被更改的对象的代码。

如果你关心性能，你应该避免设置一个对象的 `[[Prototype]]`。相反，你应该使用 **Object.create()** 来创建带有你想要的 `[[Prototype]]` 的新对象。

```js
  var MyPrototype = {
      method1 : function(){...},
      method2 : function(){...},
      ...
  };
  
  var newObject = Object.setPrototypeOf({
      property : 1,
      property2 : 'text'                 
  }, MyPrototype);
  
  //性能更高的写法
  var MyPrototype = {
      method1 : function(){...},
      method2 : function(){...},
      ...
  };
  
  var newObject = Object.assign(Object.create(MyPrototype), {
      property : 1,
      property2 : 'text'                 
  });
```

# ES5 面向对象高级 @@@ #

## 对象创建模式

### Object 构造函数模式

套路: 先创建空 Object 对象, 再动态添加属性/方法

适用场景: 起始时不确定对象内部数据

问题: 语句太多

### 对象字面量模式

套路: 使用 {} 创建对象, 同时指定属性/方法

适用场景: 起始时对象内部数据是确定的，创建对象数量少

问题: 如果创建多个对象, 有重复代码

### 工厂模式

套路: 通过工厂函数动态创建对象 **并返回**

```js
  function createPerson(name) {
      var o = new Object();
      o.name = name;
      o.getName = function () {
          console.log(this.name);
      };
      return o;
  }
  
  var person1 = createPerson('kevin');
```

适用场景: 需要创建多个对象

问题: 对象没有一个具体的类型, 都是 Object 类型

### 自定义构造函数模式

**构造函数** 是专门用来创建对象的函数

一个构造函数我们也可以称为一个 **类**

> 使用工厂模式全都是 Object 类

通过一个构造函数创建的对象，我们称该对象是这个构造函数的 **实例**

构造函数就是一个 **普通的函数**，只是他的 **调用方式不同**

+ 如果直接调用，它就是一个普通函数，返回函数的返回值
+ 如果使用 new 来调用，则它就是一个构造函数，**返回的是 this 对象**

一般构造函数首字母会大写（习惯）

#### 构造函数的执行流程

1. 创建一个新的对象, new 关键字, 立即在堆内存创建一个对象
2. 将新建的对象设置为函数中 this,可以使用 this 来引用新建的对象
3. 逐行执行函数中的代码
4. 将新建的对象作为返回值返回

套路: 自定义构造函数, 通过 new 创建对象

```js
  function Person(name) {
      this.name = name;
      this.getName = function () {
          console.log(this.name);
      };
  }
  
  var person1 = new Person('kevin'); 
```

适用场景: 需要创建多个类型确定的对象

问题: 每次创建实例时，每个方法都要被创建一次, 浪费内存

#### 优化

```js
  function Person(name) {
      this.name = name;
      this.getName = getName;
  }
  
  function getName() {
      console.log(this.name);
  }
  
  var person1 = new Person('kevin');
```

优点：解决了每个方法都要被重新创建的问题

缺点：这叫啥封装……

### 原型模式

#### 方案一

不必在构造函数中定义对象实例的信息，而是可以将这些信息直接添加到原型对象中

```js
  function Person(name) {
  
  }
  Person.prototype.name = 'keivn';
  Person.prototype.getName = function () {
      console.log(this.name);
  };
  
  var person1 = new Person();
```

优点：方法不会重新创建

缺点：1. 所有的属性和方法都共享 2. 不能初始化参数

#### 方案二

重写原型

  ```js
  function Person(name) {
  
  }
  
  Person.prototype = {
      name: 'kevin',
      getName: function () {
          console.log(this.name);
      }
  };
  
  var person1 = new Person();
  ```

优点：封装性好了一点

缺点：该有的缺点还是有，而且重写了原型，丢失了 constructor 属性

#### 优化

```js
  function Person(name) {
  
  }
  
  Person.prototype = {
      constructor: Person,
      name: 'kevin',
      getName: function () {
          console.log(this.name);
      }
  };
  
  var person1 = new Person();
```

优点：实例可以通过 constructor 属性找到所属构造函数

缺点：原型模式该有的缺点还是有

### 构造函数 + 原型的组合模式（应用最广）

- 套路: 自定义构造函数, 属性在函数中初始化, 方法添加到原型上

  ```js
  function Person(name) {
      this.name = name;
  }
  
  Person.prototype = {
      constructor: Person,
      getName: function () {
          console.log(this.name);
      }
  }
  
  var person1 = new Person();
  ```

- 适用场景: 需要创建多个类型确定的对象
- 缺点：有的人就是希望全部都写在一起，即 **更好的封装性**

### 动态原型模式

通过检查某个应该存在的方法是否有效，来决定是否需要初始化原型。

  ```js
  function Person(name) {
      this.name = name;
      if (typeof this.getName != "function") {
          Person.prototype.getName = function () {
              console.log(this.name);
          }
      }
  }
  
  var person1 = new Person();
```

### 重写原型的风险

如果实例的原型已经指向完毕了，再重写原型就会切断实例与新原型之间的联系。

#### 原型模式

面是使用原型模式时，重写原型带来的错误

  ```js
  function Person(){ 
  } 
   
  var friend = new Person(); 
           
  Person.prototype = { 
      constructor: Person, 
      name : "Nicholas", 
      age : 29, 
      job : "Software Engineer", 
      sayName : function () { 
          alert(this.name); 
      } 
  }; 
   
  friend.sayName();   //error 
  ```

实例的原型在 new 操作的过程被指定，之后重写了 Person 的原型，但是实例所引用（通过 `friend.__proto__`）的仍然是之前的原型，导致无法访问，报错。

  ![1555139426810](/img/user/programming/font-end/primitive/es/es-proto/1555139426810.png)

#### 动态原型模式

动态原型模式也有同样的风险，回顾一下 new 方法的执行过程：

+ 首先新建一个对象
+ 然后将对象的原型指向 Person.prototype
+ 然后 Person.apply(obj)
+ 返回这个对象

  ```js
  function Person(name) {
      this.name = name;
      if (typeof this.getName != "function") {
          Person.prototype = {
              constructor: Person,
              getName: function () {
                  console.log(this.name);
              }
          }
      }
  }
  
  var person1 = new Person('kevin');
  
  // 报错 并没有该方法 
  person1.getName(); 


```

可以看到原型指定完之后，才执行了 Person 函数，此时通过字面量的形式重写了原型，导致实例无法访问
如果仍像在动态原型模式中使用字面量形式，可以用下面这种方式

```js
  function Person(name) {
      this.name = name;
      if (typeof this.getName != "function") {
          Person.prototype = {
              constructor: Person,
              getName: function () {
                  console.log(this.name);
              }
          }
		  // 重新指向了重写的原型，已经创建了getName方法，所以不会再次进入if循环
          return new Person(name);
      }
  }
  
  var person1 = new Person('kevin');
  var person2 = new Person('daisy');
  
  person1.getName(); // kevin
  person2.getName();  // daisy
```

### 寄生构造函数模式

创建一个函数，该函数的作用仅仅是封装创建对象的代码，然后再返回新创建的对象

但从表面上看，这个函数又很像是典型的构造函数。

```js
  function Person(name) {
  
      var o = new Object();
      o.name = name;
      o.getName = function () {
          console.log(this.name);
      };
  
      return o;
  
  }
  
  var person1 = new Person('kevin');
  console.log(person1 instanceof Person) // false
  console.log(person1 instanceof Object)  // true
```

缺点：创建的实例使用 instanceof 都无法指向构造函数

这样方法可以在特殊情况下使用。比如我们想创建一个具有额外方法的特殊数组，但是又不想直接修改 Array 构造函数，我们可以这样写：

  ```js
  function SpecialArray() {
      var values = new Array();
  
  		values.push.apply(values, arguments);
  
      values.toPipedString = function () {
          return this.join("|");
      };
      return values;
  }
  
  var colors = new SpecialArray('red', 'blue', 'green');
  var colors2 = SpecialArray('red2', 'blue2', 'green2');
  
  
  console.log(colors);
  console.log(colors.toPipedString()); // red|blue|green
  
  console.log(colors2);
  console.log(colors2.toPipedString()); // red2|blue2|green2
  ```

其实所谓的寄生构造函数模式就是比工厂模式在创建对象的时候，多使用了一个 new，实际上两者的结果是一样的。

但是作者可能是希望能像使用普通 Array 一样使用 SpecialArray，虽然把 SpecialArray 当成函数也一样能用，但是这并不是作者的本意，也变得不优雅。

在可以使用其他模式的情况下，不要使用这种模式。

### 稳妥构造函数模式

```js
  function person(name){
      var o = new Object();
      o.sayName = function(){
          console.log(name);
      };
      return o;
  }
  
  var person1 = person('kevin');
  
  person1.sayName(); // kevin
  
  person1.name = "daisy";
  
  person1.sayName(); // kevin
  
  console.log(person1.name); // daisy
```

所谓稳妥对象，指的是没有公共属性，而且其方法也不引用 this 的对象。

与寄生构造函数模式有两点不同：

  + 新创建的实例方法不引用 this
  + 不使用 new 操作符调用构造函数

**注意**: 在以这种模式创建的对象中，除了使用 say Name() 方法之外，没有其他办法访问 name 的值

即使有其他代码会给这个对象添加方法或数据成员，但也不可能有别的办法访问传入到构造函数中的原始数据。

缺点：稳妥构造函数模式也跟工厂模式一样，无法识别对象所属类型。

## 继承模式

### 原型链继承

#### 步骤

1. 定义父类型构造函数
2. 给父类型的原型添加方法
3. 定义子类型的构造函数
4. 创建父类型的实例对象赋值给子类型的原型
5. 将子类型原型的构造属性设置为子类型的构造函数
6. 给子类型原型添加方法
7. 创建子类型的对象: 可以调用父类型的方法

#### 关键

- 实现的本质是重写原型对象，代之以一个新类型的实例。
- 子类型的原型为父类型的一个实例对象
- 如果不去更改子类型的原型的 constructor 属性，则只能在父类型的原型中找到，所以最好更改 constructor 属性，指向子类型

![1547106232276](/img/user/programming/font-end/primitive/es/es-proto/1547106232276.png)

```js
  function Parent () {
      this.name = 'kevin';
  }
  
  Parent.prototype.getName = function () {
      console.log(this.name);
  }
  
  function Child () {
  
  }
  
  Child.prototype = new Parent();
  
  var child1 = new Child();
  
  console.log(child1.getName()) // kevin
```

#### 缺点

引用类型的属性被所有实例共享，举个例子：

  ```js
  function Parent () {
      this.names = ['kevin', 'daisy'];
  }
  
  function Child () {
  
  }
  
  Child.prototype = new Parent();
  
  var child1 = new Child();
  
  child1.names.push('yayu');
  
  console.log(child1.names); // ["kevin", "daisy", "yayu"]
  
  var child2 = new Child();
  
  console.log(child2.names); // ["kevin", "daisy", "yayu"]
  ```

在创建 Child 的实例时，不能向 Parent 传参

### 借用构造函数继承 (经典继承)

#### 步骤

1. 定义父类型构造函数
2. 定义子类型构造函数
3. 在子类型构造函数中调用父类型构造

#### 关键

在子类型构造函数中通用 call() 调用父类型构造函数

并没有真正的继承操作，实质是代码的简化

  ```js
  function Parent () {
      this.names = ['kevin', 'daisy'];
  }
  
  function Child () {
      Parent.call(this);
  }
  
  var child1 = new Child();
  
  child1.names.push('yayu');
  
  console.log(child1.names); // ["kevin", "daisy", "yayu"]
  
  var child2 = new Child();
  
  console.log(child2.names); // ["kevin", "daisy"]
  ```

#### 优点

1. 避免了引用类型的属性被所有实例共享
2. 可以在 Child 中向 Parent 传参

#### 缺点

方法都在构造函数中定义，每次创建实例都会创建一遍方法。

### 组合继承 (应用最广)

原型链 + 借用构造函数的组合继承

利用原型链实现对父类型对象的方法继承

利用 super() 借用父类型构建函数初始化相同属性

  ```js
  function Parent (name) {
    // 父类定义自己的属性和方法
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
  }
  // 父类定义原型上的属性和方法
  Parent.prototype.getName = function () {
  
    console.log(this.name)
  }
  
  function Child (name, age) {
    // 子类继承父类的属性和方法
    Parent.call(this, name);
    // 子类定义自己的属性和方法
    this.age = age;
  }
  // 构造函数的原型指向父类的实例，实例可以访问父类型的原型的方法
  // 为什么不直接赋值Parent.prototype而是要赋值一个实例呢？如果我只想修改子类的原型，而不想修改父类的原型 @@@
  Child.prototype = new Parent();
  // 新原型的constructor属性修正为构造函数
  Child.prototype.constructor = Child; 
  
  var child1 = new Child('kevin', '18');
  
  child1.colors.push('black');
  
  console.log(child1.name); // kevin
  console.log(child1.age); // 18
  console.log(child1.colors); // ["red", "blue", "green", "black"]
  
  var child2 = new Child('daisy', '20');
  
  console.log(child2.name); // daisy
  console.log(child2.age); // 20
  console.log(child2.colors); // ["red", "blue", "green"]
  ```

优点：融合原型链继承和构造函数的优点，是 JavaScript 中最常用的继承模式。

### 原型式继承

ES5 Object.create 的模拟实现，将传入的对象作为创建的对象的原型。

```js
  function createObj(o) {
      function F(){} // nothing，因为create本质就是nothing
      F.prototype = o;
      return new F();
  }
```

缺点：包含引用类型的属性值始终都会共享相应的值，这点跟原型链继承一样。

```js
  var person = {
      name: 'kevin',
      friends: ['daisy', 'kelly']
  }
  
  var person1 = createObj(person);
  var person2 = createObj(person);
  
  person1.name = 'person1';
  console.log(person2.name); // kevin
  
  person1.firends.push('taylor');
  console.log(person2.friends); // ["daisy", "kelly", "taylor"]
```

注意：修改 `person1.name` 的值，`person2.name` 的值并未发生改变，并不是因为 `person1` 和 `person2` 有独立的 name 值，而是因为 `person1.name = 'person1'`，给 `person1` 添加了 name 值，并非修改了原型上的 name 值。

### 寄生式继承

其实就是寄生式构造函数

创建一个仅用于封装继承过程的函数，该函数在内部以某种形式来做增强对象，最后返回对象。

```js
  function createObj (o) {
      var clone = Object.create(o);
      clone.sayName = function () {
          console.log('hi');
      }
      return clone;
  }
```

缺点：跟借用构造函数模式一样，每次创建对象都会创建一遍方法。

### 寄生组合式继承 (最理想)

组合继承最大的缺点是会调用两次父构造函数

一次是设置子类型实例的原型的时候：

  ```js
  Child.prototype = new Parent();
  ```

一次在创建子类型实例的时候：

  ```js
  var child1 = new Child('kevin', '18');
  ```

回想下 new 的模拟实现，其实在这句中，我们会执行：

  ```js
  Parent.call(this, name);
  ```

在这里，我们第二次调用了 Parent 构造函数。

所以，在这个例子中，如果我们打印 child1 对象，我们会发现 Child.prototype 和 child1 都有一个属性为 `colors`，属性值为 `['red', 'blue', 'green']`。

那么我们该如何精益求精，避免这一次重复调用呢？

如果我们不使用 Child.prototype = new Parent() ，而是间接的让 Child.prototype 访问到 Parent.prototype 呢？

看看如何实现：

```js
  function Parent (name) {
      this.name = name;
      this.colors = ['red', 'blue', 'green'];
  }
  
  Parent.prototype.getName = function () {
      console.log(this.name)
  }
  
  function Child (name, age) {
      Parent.call(this, name);
      this.age = age;
  }
  
  // 关键的三步
  var F = function () {};
  
  F.prototype = Parent.prototype;
  // 子类的原型的是一个空函数的实例
  Child.prototype = new F();
  Child.prototype.constructor = Child
  
  var child1 = new Child('kevin', '18');
  
  console.log(child1);
  ```

最后我们封装一下这个继承方法：

  ```js
  function object(o) {
      function F() {}
      F.prototype = o;
      return new F();
  }
  
  function prototype(child, parent) {
      var prototype = object(parent.prototype);
      prototype.constructor = child;
      child.prototype = prototype;
  }
  
  // 当我们使用的时候：
  prototype(Child, Parent);
  ```

引用《JavaScript 高级程序设计》中对寄生组合式继承的夸赞就是：

这种方式的高效率体现它只调用了一次 Parent 构造函数，并且因此避免了在 Parent.prototype 上面创建不必要的、多余的属性。与此同时，原型链还能保持不变；因此，还能够正常使用 instanceof 和 isPrototypeOf。**开发人员普遍认为寄生组合式继承是引用类型最理想的继承范式。**

# Class This 总结

如果是在类的内部，都指向实例，不论是调用父类的方法还是子类的方法

如果是在类的静态方法内部，则指向类本身

# Class 的基本语法

Fields 字段

Methods 方法

## 基本语法

### 类的由来

JavaScript 语言中，生成实例对象的传统方法是通过构造函数。

这种写法跟传统的面向对象语言（比如 C++ 和 Java）差异很大，很容易让新学习这门语言的程序员感到困惑。

ES6 提供了更接近传统语言的写法，引入了 Class（类）这个概念，作为对象的模板。通过 `class` 关键字，可以定义类

### 定义类

基本上，ES6 的 `class` 可以看作只是一个 **语法糖**，它的绝大部分功能，ES5 都可以做到，新的 `class` 写法只是让对象原型的写法更加清晰、更像面向对象编程的语法而已。

`prototype` 对象的 `constructor` 属性，直接指向“类”的本身，这与 ES5 的行为是一致的。

  ```js
  class Point {
    // ...
  }
  
  typeof Point // "function"
  Point === Point.prototype.constructor // true
  ```

上面代码表明，**类的数据类型就是函数，类本身是一个构造函数**。

#### Name 属性

由于本质上，ES6 的类只是 ES5 的构造函数的一层包装，所以函数的许多特性都被 `Class` 继承，包括 `name` 属性。

  ```javascript
  class Point {}
  Point.name // "Point"
  ```

`name` 属性总是返回紧跟在 `class` 关键字后面的类名。

### 使用类创建实例

使用的时候，也是直接对类使用 `new` 命令，跟构造函数的用法完全一致。

  ```js
  class Bar {
    doStuff() {
      console.log('stuff');
    }
  }
  
  var b = new Bar();
  b.doStuff() // "stuff"
  ```

类必须使用 `new` 调用，否则会报错。这是它跟普通构造函数的一个主要区别，后者不用 `new` 也可以执行

```javascript
  class Foo {
    constructor() {
      return Object.create(null);
    }
  }
  
  Foo()
  // TypeError: Class constructor Foo cannot be invoked without 'new'
```

### 类的实例

类的实例与 ES5 构造函数形式的实例没有任何区别

#### Constructor 属性

与 ES5 一样，实例的 `constructor` 方法就是原型的 `constructor` 方法。

```javascript
  class B {}
  let b = new B();
  
  b.constructor === B.prototype.constructor // true
```

#### 原型对象

与 ES5 一样，**类的所有实例共享一个原型对象。**

```javascript
  var p1 = new Point(2,3);
  var p2 = new Point(3,2);
  
  p1.__proto__ === p2.__proto__
  //true
```

上面代码中，`p1` 和 `p2` 都是 `Point` 的实例，它们的原型都是 `Point.prototype`，所以 `__proto__` 属性是相等的。

### Constructor 方法

`constructor` 方法是类的默认方法，**通过 `new` 命令生成对象实例时，自动调用该方法。**

一个类必须有 `constructor` 方法，**如果没有显式定义，一个空的 `constructor` 方法会被默认添加。**

  ```javascript
  class Point {
  }
  
  // 等同于
  class Point {
    constructor() {}
  }
  ```

`constructor` 方法 **默认返回实例对象（即 `this`）**，完全 **可以指定返回另外一个对象**。

  ```javascript
  class Foo {
    constructor() {
      return Object.create(null);
    }
  }
  
  new Foo() instanceof Foo
  // false
  ```

**上面代码中，`constructor` 函数返回一个全新的对象，结果导致实例对象不是 `Foo` 类的实例。**

### 定义实例的属性和方法

与 ES5 一样，实例的属性需要显示定义在其本身（即通过 `constructor` 方法定义在 `this` 对象上）

实例的属性除非显式定义在其本身（即定义在 `this` 对象上），否则都是定义在原型上

  ```js
  //定义类
  class Point {
    constructor(x, y) {
      this.x = x;
      this.y = y;
      this.getName = fucntion(){
        return this.x
      }
    }
    toString() {
      return '(' + this.x + ', ' + this.y + ')';
    }
  }
  ```

上面代码中，`x` 和 `y` 都是实例对象 `point` 自身的属性（因为定义在 `this` 变量上），所以 `hasOwnProperty` 方法返回 `true`

### 定义原型的方法

#### 基本语法

构造函数的 `prototype` 属性，在 ES6 的“类”上面继续存在。事实上，类的所有方法都定义在类的 `prototype` 属性上面。

```javascript
  class Point {
    constructor() {
      this.x = x;
      this.y = y;
    }
  
    toString() {
      // ...
    }
  
    toValue() {
      // ...
    }
  }
  
  // 等同于
  
  Point.prototype = {
    constructor() {},
    toString() {},
    toValue() {},
  };
```

与 `x,y` 不同，`toString` 是原型对象的属性（因为定义在 `Point` 类内部），所以 `hasOwnProperty` 方法返回 `false`。这些都与 ES5 的行为保持一致。

注意，定义“类”的方法的时候，前面不需要加上 `function` 这个关键字，直接把函数定义放进去了就可以了。**另外，方法之间不需要逗号分隔，加了会报错。**

> 注意：这并不是函数的简写语法，而是类本身的语法

#### 使用属性表达式

类的属性名，可以采用表达式。

  ```javascript
  let methodName = 'getArea';
  
  class Square {
    constructor(length) {
      // ...
    }
  
    [methodName]() {
      // ...
    }
  }
  ```

上面代码中，`Square` 类的方法名 `getArea`，是从表达式得到的。

### 定义原型的属性

在类的内部，无法直接定义原型的属性，但是可以使用 **setter 或者 getter 函数** 定义属性，这样的属性是在原型对象上的

#### 取值函数（getter）和存值函数（setter）

与 ES5 一样, 在“类”的内部可以使用 `get` 和 `set` 关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为

```javascript
  class MyClass {
    constructor() {
      // ...
    }
    get prop() {
      return 'getter';
    }
    set prop(value) {
      console.log('setter: '+value);
    }
  }
  
  let inst = new MyClass();
  
  inst.prop = 123;
  // setter: 123
  
  inst.prop
  // 'getter'
```

上面代码中，`prop` 属性有对应的存值函数和取值函数，因此赋值和读取行为都被自定义了。

**存值函数和取值函数是设置在属性的 Descriptor 对象上的。**

  ```javascript
  class CustomHTMLElement {
    constructor(element) {
      this.element = element;
    }
  
    get html() {
      return this.element.innerHTML;
    }
  
    set html(value) {
      this.element.innerHTML = value;
    }
  }
  
  var descriptor = Object.getOwnPropertyDescriptor(
    CustomHTMLElement.prototype, "html"
  );
  
  "get" in descriptor  // true
  "set" in descriptor  // true
  ```

上面代码中，存值函数和取值函数是定义在 `html` 属性的描述对象上面，这与 ES5 完全一致。

### 向原型添加属性和方法

#### 通过显示原型

由于类的方法都定义在 `prototype` 对象上面，所以类的 **新方法可以添加** 在 `prototype` 对象上面。`Object.assign` 方法可以很方便地一次向类添加多个方法。

  ```javascript
  class Point {
    constructor(){
      // ...
    }
  }
  
  Object.assign(Point.prototype, {
    toString(){},
    toValue(){}
  });
  ```

#### 通过隐式原型

上文有提到，所有类的实例都指向同一个原型对象，这也意味着，**可以通过实例的 `__proto__` 属性为“类”添加方法。**

> `__proto__` 并不是语言本身的特性，这是各大厂商具体实现时添加的私有属性，虽然目前很多现代浏览器的 JS 引擎中都提供了这个私有属性，但依旧不建议在生产中使用该属性，避免对环境产生依赖。生产环境中，我们可以使用 `Object.getPrototypeOf` 方法来获取实例对象的原型，然后再来为原型添加方法/属性。

```javascript
  var p1 = new Point(2,3);
  var p2 = new Point(3,2);
  
  p1.__proto__.printName = function () { return 'Oops' };
  
  p1.printName() // "Oops"
  p2.printName() // "Oops"
  
  var p3 = new Point(4,2);
  p3.printName() // "Oops
```

上面代码在 `p1` 的原型上添加了一个 `printName` 方法，由于 `p1` 的原型就是 `p2` 的原型，因此 `p2` 也可以调用这个方法。而且，此后新建的实例 `p3` 也可以调用这个方法。这意味着，使用实例的 `__proto__` 属性改写原型，必须相当谨慎，不推荐使用，因为这会改变“类”的原始定义，影响到所有实例。

### 定义实例属性和方法的新语法

实例属性除了定义在 `constructor()` 方法里面的 `this` 上面，也可以定义在类的最顶层

**注意**: 实例属性的新写法看起来和原型的方法层次一样，但是原型的方法都是在类的原型上的，而新写法定义的属性还是在实例自己身上，新写法仅仅是 constructor 的语法糖而已

```js
  class IncreasingCounter {
    constructor() {
      this._count = 0;
    }
    get value() {
      console.log('Getting the current value!');
      return this._count;
    }
    increment() {
      this._count++;
    }
  }
```

上面代码中，实例属性 `this._count` 定义在 `constructor()` 方法里面。另一种写法是，这个属性也可以定义在类的最顶层，其他都不变。

  ```javascript
  class IncreasingCounter {
    _count = 0;
    get value() {
      console.log('Getting the current value!');
      return this._count;
    }
    increment() {
      this._count++;
    }
  }
  ```

上面代码中，实例属性 `_count` 与取值函数 `value()` 和 `increment()` 方法，处于同一个层级。这时，不需要在实例属性前面加上 `this`。

当然，定义方法只需要把函数赋值给属性即可

  ```js
  class A{
    test = function(){}
  }
  
  console.log(new A().hasOwnProperty('test')) // true
  ```

这种新写法的好处是，所有实例对象自身的属性都定义在类的头部，看上去比较整齐，一眼就能看出这个类有哪些实例属性。

  ```javascript
  class foo {
    bar = 'hello';
    baz = 'world';
  
    constructor() {
      // ...
    }
  }
  ```

上面的代码，一眼就能看出，`foo` 类有两个实例属性，一目了然。另外，写起来也比较简洁。

**注意**: 顶层的声明会被 constructor 内部的覆盖 @@@

  ```js
  class A{
    constructor(){
      this.name = 'inner'
    }
    name='outer'
  }
  
  console.log(new A().name) // inner
  ```

### Class 表达式

与函数一样，类也可以使用表达式的形式定义。

```javascript
  const MyClass = class Me {
    getClassName() {
      return Me.name;
    }
  };
```

上面代码使用表达式定义了一个类。需要注意的是，**这个类的名字是 `Me`，但是 `Me` 只在 Class 的内部可用，指代当前类。在 Class 外部，这个类只能用 `MyClass` 引用。**

```javascript
  let inst = new MyClass();
  inst.getClassName() // Me
  Me.name // ReferenceError: Me is not defined
```

上面代码表示，`Me` 只在 Class 内部有定义。

如果类的内部没用到的话，可以省略 `Me` 也就是可以写成下面的形式。

```javascript
  const MyClass = class { /* ... */ };
```

采用 Class 表达式，可以写出 **立即执行的 Class**,其实就是加一个 new 关键字，声明类之后马上赋值

  ```javascript
  let person = new class {
    constructor(name) {
      this.name = name;
    }
  
    sayName() {
      console.log(this.name);
    }
  }('张三');
  
  person.sayName(); // "张三"
  ```

上面代码中，`person` 是一个立即执行的类的实例。

### This 的指向

**类的方法** 内部如果含有 `this`，它 **默认指向类的实例**。

也正是因为 `this` 的指向是类的实例，所以我们在 **类的内部** 可以通过 `this`：

  - 访问实例的属性和方法
  - 沿着原型链，也可以调用刚定义的原型的方法和属性

  ```javascript
  class Logger {
    printName(name = 'there') {
      this.print(`Hello ${name}`);
    }
  
    print(text) {
      console.log(text);
    }
  }
  
  const logger = new Logger();
  const { printName } = logger;
  printName(); // TypeError: Cannot read property 'print' of undefined
  ```

上面代码中，`printName` 方法中的 `this`，默认指向 `Logger` 类的实例。但是，如果将这个方法提取出来单独使用，`this` 会指向该方法运行时所在的环境（由于 class 内部是严格模式，所以 this 实际指向的是 `undefined`），从而导致找不到 `print` 方法而报错**。

一个比较简单的解决方法是，在构造方法中绑定 `this`，**这样就不会找不到 `print` 方法了。

  ```javascript
  class Logger {
    constructor() {
      this.printName = this.printName.bind(this);
    }
  
    // ...
  }
  ```

另一种解决方法是 **使用箭头函数**。

  ```javascript
  class Logger {
    constructor() {
      this.printName = (name = 'there') => {
        this.print(`Hello ${name}`);
      };
    }
  
    // ...
  }
  ```

还有一种解决方法是使用 `Proxy`，获取方法的时候，自动绑定 `this`。

  ```javascript
  function selfish (target) {
    const cache = new WeakMap();
    const handler = {
      get (target, key) {
        const value = Reflect.get(target, key);
        if (typeof value !== 'function') {
          return value;
        }
        if (!cache.has(value)) {
          cache.set(value, value.bind(target));
        }
        return cache.get(value);
      }
    };
    const proxy = new Proxy(target, handler);
    return proxy;
  }
  
  const logger = selfish(new Logger());
  ```

### 静态方法

#### 概念

所有在类中定义的方法，实例都可以通过原型链来访问。

如果在一个方法前，加上 `static` 关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”

> 想想常见的静态方法 Array.isArray() Object.setPrototypeOf()
  >
> 常见的静态属性：Math.PI

**相当于是在构造函数上定义方法**

  + 我们知道类本身是一个构造函数
  + 类内部的方法定义在构造函数的显式原型上
  + 所以通过静态方法，可以在类的内部直接给构造函数添加方法，也就是给类本身添加方法

#### 语法

```js
  class Foo {
    static classMethod() {
      return 'hello';
    }
  }
  
  Foo.classMethod() // 'hello'
  
  var foo = new Foo();
  foo.classMethod()
  // TypeError: foo.classMethod is not a function
```

上面代码中，`Foo` 类的 `classMethod` 方法前有 `static` 关键字，表明该方法是一个静态方法，可以直接在 `Foo` 类上调用（`Foo.classMethod()`），而不是在 `Foo` 类的实例上调用。

当然，类的原型上也不存在这个方法，因为实例调用的本质上是原型上的方法，所以在实例上调用静态方法，会抛出一个错误，表示不存在该方法。

当然，也可以使用 ES5 的写法

  ```js
  class Foo {
  
  }
  Foo.classMethod = fucntion {
      return 'hello';
  }
  ```

#### This 指向

注意，如果 **静态方法包含 `this` 关键字，这个 `this` 指的是类，而不是实例**。

  ```javascript
  class Foo {
    static bar() {
      this.baz();
    }
    static baz() {
      console.log('hello');
    }
    baz() {
      console.log('world');
    }
  }
  
  Foo.bar() // hello
  ```

上面代码中，静态方法 `bar` 调用了 `this.baz`，这里的 `this` 指的是 `Foo` 类，而不是 `Foo` 的实例，等同于调用 `Foo.baz`。**另外，从这个例子还可以看出，静态方法可以与非静态方法重名。**

#### 继承

**但是**: 父类的静态方法，可以被子类继承，不会被 **实例** 继承，

  ```javascript
  class Foo {
    static classMethod() {
      return 'hello';
    }
  }
  
  class Bar extends Foo {
  }
  
  Bar.classMethod() // 'hello'
  ```

上面代码中，父类 `Foo` 有一个静态方法，子类 `Bar` 可以调用这个方法。

**本质**: 把 Foo 作为 Bar 的 `__proto__`，所以 Foo 身上的属性和方法都可以被 Bar 访问 @@@

> 直接赋值了 Foo，因此给 `Bar.__proto__` 添加属性，Foo 也可以直接访问，也只有这样才可以继承静态属性

#### 在内置对象中没有静态继承

请注意，内置类没有静态 `[[Prototype]]` 引用。例如，`Object` 具有 `Object.defineProperty`，`Object.keys` 等方法，但 `Array`，`Date` 不会继承它们。

`Date` 和 `Object` 之间毫无关联，他们独立存在，不过 `Date.prototype` 继承于 `Object.prototype`，仅此而已。

造成这个情况是因为 JavaScript 在设计初期没有考虑使用 class 语法和继承静态方法。

#### Super

**静态方法也是可以从 `super` 对象上调用的**

  ```javascript
  class Foo {
    static classMethod() {
      return 'hello';
    }
  }
  
  class Bar extends Foo {
    static classMethod() {
      return super.classMethod() + ', too';
    }
  }
  
  Bar.classMethod() // "hello, too"
  ```

### 静态属性

静态属性指的是 Class 本身的属性，即 `Class.propName`，而不是定义在实例对象（`this`）上的属性。

  ```javascript
  class Foo {
  }
  
  Foo.prop = 1;
  Foo.prop // 1
  ```

上面的写法为 `Foo` 类定义了一个静态属性 `prop`。

目前，只有这种写法可行，因为 ES6 明确规定，Class 内部只有静态方法，没有静态属性。现在有一个 [提案](https://github.com/tc39/proposal-class-fields) 提供了类的静态属性，写法是在实例属性法的前面，加上 `static` 关键字。

  ```javascript
  class MyClass {
    static myStaticProp = 42;
  
    constructor() {
      console.log(MyClass.myStaticProp); // 42
    }
  }
  ```

这个新写法大大方便了静态属性的表达。

  ```javascript
  // 老写法
  class Foo {
    // ...
  }
  Foo.prop = 1;
  
  // 新写法
  class Foo {
    static prop = 1;
  }
  ```

上面代码中，老写法的静态属性定义在类的外部。整个类生成以后，再生成静态属性。这样让人很容易忽略这个静态属性，也不符合相关代码应该放在一起的代码组织原则。

另外，**新写法是显式声明（declarative），而不是赋值处理，语义更好**。

### 私有方法和私有属性

私有方法和私有属性，是只能在类的内部访问的方法和属性，外部不能访问。这是常见需求，有利于代码的封装，但 ES6 不提供，只能通过变通方法模拟实现。

一种做法是在 **命名上加以区别**。

  ```javascript
  class Widget {
  
    // 公有方法
    foo (baz) {
      this._bar(baz);
    }
  
    // 私有方法
    _bar(baz) {
      return this.snaf = baz;
    }
  
    // ...
  }
  ```

上面代码中，`_bar` 方法前面的下划线，表示这是一个只限于内部使用的私有方法。但是，这种命名是不保险的，在类的外部，还是可以调用到这个方法。

另一种方法就是索性将私有方法移出模块，因为模块内部的所有方法都是对外可见的。

  ```javascript
  class Widget {
    foo (baz) {
      bar.call(this, baz);
    }
  
    // ...
  }
  
  function bar(baz) {
    return this.snaf = baz;
  }
  ```

上面代码中，`foo` 是公开方法，内部调用了 `bar.call(this, baz)`。这 **使得 `bar` 实际上成为了当前模块的私有方法**。

还有一种方法是利用 `Symbol` 值的唯一性，**将私有方法的名字命名为一个 `Symbol` 值。**

  ```javascript
  const bar = Symbol('bar');
  const snaf = Symbol('snaf');
  
  export default class myClass{
  
    // 公有方法
    foo(baz) {
      this[bar](baz);
    }
  
    // 私有方法
    [bar](baz) {
      return this[snaf] = baz;
    }
  
    // ...
  };
  ```

上面代码中，`bar` 和 `snaf` 都是 `Symbol` 值，一般情况下无法获取到它们，因此达到了私有方法和私有属性的效果。但是也不是绝对不行，`Reflect.ownKeys()` 依然可以拿到它们。

  ```javascript
  const inst = new myClass();
  
  Reflect.ownKeys(myClass.prototype)
  // [ 'constructor', 'foo', Symbol(bar) ]
  ```

上面代码中，Symbol 值的属性名依然可以从类的外部拿到。

### 私有属性的提案

目前，有一个 [提案](https://github.com/tc39/proposal-private-methods)，为 `class` 加了私有属性。方法是在属性名之前，**使用 `#` 表示**。

  ```javascript
  class IncreasingCounter {
    #count = 0;
    get value() {
      console.log('Getting the current value!');
      return this.#count;
    }
    increment() {
      this.#count++;
    }
  }
  ```

上面代码中，`#count` 就是私有属性，只能在类的内部使用（`this.#count`）。**如果在类的外部使用，就会报错**。

  ```javascript
  const counter = new IncreasingCounter();
  counter.#count // 报错
  counter.#count = 42 // 报错
  ```

下面是另一个例子。

  ```javascript
  class Point {
    #x;
  
    constructor(x = 0) {
      this.#x = +x;
    }
  
    get x() {
      return this.#x;
    }
  
    set x(value) {
      this.#x = +value;
    }
  }
  ```

上面代码中，`#x` 就是私有属性，在 `Point` 类之外是读取不到这个属性的。由于井号 `#` 是属性名的一部分，使用时必须带有 `#` 一起使用，**所以 `#x` 和 `x` 是两个不同的属性。**

之所以要引入一个新的前缀 `#` 表示私有属性，而没有采用 `private` 关键字，是因为 JavaScript 是一门动态语言，没有类型声明，使用独立的符号似乎是唯一的比较方便可靠的方法，能够准确地区分一种属性是否为私有属性。另外，Ruby 语言使用 `@` 表示私有属性，ES6 没有用这个符号而使用 `#`，是因为 `@` 已经被留给了 Decorator。

这种写法不仅可以写私有属性，还可以用来写 **私有方法**。

  ```javascript
  class Foo {
    #a;
    #b;
    constructor(a, b) {
      this.#a = a;
      this.#b = b;
    }
    #sum() {
      return #a + #b;
    }
    printSum() {
      console.log(this.#sum());
    }
  }
  ```

上面代码中，`#sum()` 就是一个私有方法。

另外，私有属性也可以设置 getter 和 setter 方法。

  ```javascript
  class Counter {
    #xValue = 0;
  
    constructor() {
      super();
      // ...
    }
  
    get #x() { return #xValue; }
    set #x(value) {
      this.#xValue = value;
    }
  }
  ```

上面代码中，`#x` 是一个私有属性，它的读写都通过 `get #x()` 和 `set #x()` 来完成。

私有属性不限于从 `this` 引用，只要是在类的内部，实例也可以引用私有属性。

  ```javascript
  class Foo {
    #privateValue = 42;
    static getPrivateValue(foo) {
      return foo.#privateValue;
    }
  }
  
  Foo.getPrivateValue(new Foo()); // 42
  ```

上面代码允许从实例 `foo` 上面引用私有属性。

私有属性和私有方法前面，也可以加上 `static` 关键字，表示这是一个静态的私有属性或私有方法。

  ```javascript
  class FakeMath {
    static PI = 22 / 7;
    static #totallyRandomNumber = 4;
  
    static #computeRandomNumber() {
      return FakeMath.#totallyRandomNumber;
    }
  
    static random() {
      console.log('I heard you like random numbers…')
      return FakeMath.#computeRandomNumber();
    }
  }
  
  FakeMath.PI // 3.142857142857143
  FakeMath.random()
  // I heard you like random numbers…
  // 4
  FakeMath.#totallyRandomNumber // 报错
  FakeMath.#computeRandomNumber() // 报错
  ```

上面代码中，`#totallyRandomNumber` 是私有属性，`#computeRandomNumber()` 是私有方法，只能在 `FakeMath` 这个类的内部调用，外部调用就会报错。

### new.target 属性

`new` 是从构造函数生成实例对象的命令。ES6 为 `new` 命令引入了一个 `new.target` 属性，该属性一般用在构造函数之中，**返回 `new` 命令作用于的那个构造函数。**

如果构造函数不是通过 `new` 命令或 `Reflect.construct()` 调用的，`new.target` 会返回 `undefined`，因此 **这个属性可以用来确定构造函数是怎么调用的。**

  ```javascript
  function Person(name) {
    if (new.target !== undefined) {
      this.name = name;
    } else {
      throw new Error('必须使用 new 命令生成实例');
    }
  }
  
  // 另一种写法
  function Person(name) {
    if (new.target === Person) {
      this.name = name;
    } else {
      throw new Error('必须使用 new 命令生成实例');
    }
  }
  
  var person = new Person('张三'); // 正确
  var notAPerson = Person.call(person, '张三');  // 报错
  ```

上面代码确保构造函数只能通过 `new` 命令调用。

Class 内部调用 `new.target`，返回当前 Class。

  ```javascript
  class Rectangle {
    constructor(length, width) {
      console.log(new.target === Rectangle);
      this.length = length;
      this.width = width;
    }
  }
  
  var obj = new Rectangle(3, 4); // 输出 true
  ```

**需要注意的是，子类继承父类时，`new.target` 会返回子类**。

  ```javascript
  class Rectangle {
    constructor(length, width) {
      console.log(new.target === Rectangle);
      // ...
    }
  }
  
  class Square extends Rectangle {
    constructor(length) {
      super(length, width);
    }
  }
  
  var obj = new Square(3); // 输出 false
  ```

上面代码中，`new.target` 会返回子类。

利用这个特点，可以写出不能独立使用、必须继承后才能使用的类。

  ```javascript
  class Shape {
    constructor() {
      if (new.target === Shape) {
        throw new Error('本类不能实例化');
      }
    }
  }
  
  class Rectangle extends Shape {
    constructor(length, width) {
      super();
      // ...
    }
  }
  
  var x = new Shape();  // 报错
  var y = new Rectangle(3, 4);  // 正确
  ```

上面代码中，`Shape` 类不能被实例化，只能用于继承。

**注意，在函数外部，使用 `new.target` 会报错。**

### 与 ES5 构造函数形式的区别

#### 严格模式

类和模块的内部，默认就是严格模式，所以不需要使用 `use strict` 指定运行模式。只要你的代码写在类或模块之中，就只有严格模式可用。考虑到未来所有的代码，其实都是运行在模块之中，**所以 ES6 实际上把整个语言升级到了严格模式**。

#### 不存在提升

类不存在变量提升（hoist），这一点与 ES5 完全不同。

  ```javascript
  new Foo(); // ReferenceError
  class Foo {}
  ```

上面代码中，`Foo` 类使用在前，定义在后，这样会报错，因为 ES6 不会把类的声明提升到代码头部。

**这种规定的原因与下文要提到的继承有关，必须保证子类在父类之后定义。**

```javascript
  {
    let Foo = class {};
    class Bar extends Foo {
    }
  }
```

上面的代码不会报错，因为 `Bar` 继承 `Foo` 的时候，`Foo` 已经有定义了。但是，如果存在 `class` 的提升，上面代码就会报错，因为 `class` 会被提升到代码头部，而 `let` 命令是不提升的，所以导致 `Bar` 继承 `Foo` 的时候，`Foo` 还没有定义

#### 不可直接使用

类必须使用 `new` 调用，否则会报错。这是它跟普通构造函数的一个主要区别，后者不用 `new` 也可以执行

  ```javascript
  class Foo {
    constructor() {
      return Object.create(null);
    }
  }
  
  Foo()
  // TypeError: Class constructor Foo cannot be invoked without 'new'
  ```

#### 不可枚举

**类的内部** 所有定义的方法，**都是不可枚举的（non-enumerable）**。

> 类的内部所有定义的方法其实就是定义在原型上的方法

```javascript
  class Point {
    constructor(x, y) {
      // ...
    }
  
    toString() {
      // ...
    }
  }
  
  Object.keys(Point.prototype)
  // []
  Object.getOwnPropertyNames(Point.prototype)
  // ["constructor","toString"]
```

上面代码中，`toString` 方法是 `Point` 类内部定义的方法，它是不可枚举的。**这一点与 ES5 的行为不一致**。

```javascript
  var Point = function (x, y) {
    // ...
  };
  
  Point.prototype.toString = function() {
    // ...
  };
  
  Object.keys(Point.prototype)
  // ["toString"]
  Object.getOwnPropertyNames(Point.prototype)
  // ["constructor","toString"]
```

上面代码采用 ES5 的写法，`toString` 方法就是可枚举的。

#### 私有属性

类无论属性是否私有，都需要通过 this 来引用

而 ES5 通常是通过闭包来解决

#### Get 和 Set

ES5 无法像对象字面量那样给实例定义 getter 和 setter，只能通过 defineProperty 方法

ES6 可以直接在类的内部通过 get 和 set 定义原型的方法，类似于对象字面量的形式

### 注意

类内部方法的定义，并不是函数的简写形式：

  + 函数表达式形式定义的函数给到实例，本质上是实例属性的新语法，然后赋值一个函数
  + **' 简写写法 '** 给到原型对象，正确来说定义原型的方法只能用这种写法 @@@

构造函数是整个 class {} 代码块，而不仅仅是内部的 constructor 方法，constructor() 和自定义的方法一样，是在原型对象上的一个方法而已，作用是：new 时自动调用（而自定义的方法不会），在其内部生命的属性或方法会直接给到实例

在类的内部不可直接使用 this

  ```js
  class Point{
    this.x = 1
  	this.y = 2
  }
  // 语法错误，不支持这样的语法
  ```

定义类的时候就可以修改类自己的原型了，但是这样有一个严重的问题。每次新建一个实例的时候都会重复定义原型，因此还是放在外面定义最好，注意 this 要保证是实例自己

```js
//定义类
class Point {
    constructor(x, y) {
      this.x = x;
      this.y = y;
      this.toString()
    }
    toString() {
        Point.prototype.test = 'test'
    }
  }

console.log(new Point())
```

### 总结

class 其实就是一个 **构造函数**，但同时也是一个 **容器**，在 class 内部可以：

  + 通过 `constructor` 方法定义经典的构造函数，从而给实例添加属性和方法
  + 在顶部定义实例的属性
  + 给原型添加属性和方法
  + 通过 `static` 关键字，定义构造函数自身的属性和方法
  + 私有属性和私有方法

一览

  ```js
  class Foo { 
    constructor() {
      // 实例的属性或方法
        this.x = 1 // this绑定实例
      // 无法使用get和set关键字
    }
    // 在实例身上，位置可以任意，处于顶层即可
    bar = 'hello';
    baz = this.bar; // 'hello',this绑定类
    // 在实例身上
    oppo = function(){}
  
    // 定义原型对象的方法
    toString() {
    // ...
    }
  	// 定义原型对象的属性
  	get prop() {
    return 'getter';
  	}
    set prop(value) {
      console.log('setter: '+value);
    }
    // 定义类本身的方法
    static classMethod() {
      return 'hello';
    }
  	// 定义类本身的属性
    static prop = 1
  }
  // 在类的外部给原型添加属性或者方法
  // Foo.prototype = ...
  // 在类的外部给类（构造函数）添加属性或者方法
  // Foo.propName = ...
  
  const foo = new Foo()
  // foo.hasOwnProperty('oppo') true
  // foo.hasOwnProperty('toString') false 
  // foo.__proto__.hasOwnProperty('toString') true
  // foo.classMethod()  not a fucntion
  ```

# Class 的继承

## 基本语法

Class 可以通过 `extends` 关键字实现继承，这比 ES5 的通过修改原型链实现继承，要清晰和方便很多。

Class 类用于定义构造函数和原型，extends 类似于定义现有的对象为对象的原型

  ```javascript
  class Point {
  }
  
  class ColorPoint extends Point {
  } 
  ```

上面代码定义了一个 `ColorPoint` 类，该类通过 `extends` 关键字，**继承了 `Point` 类的所有属性和方法**。

## 本质

**`extends` 关键字是 ES5 组合继承的语法糖**。为了方便对比，我们先回忆以下经典组合继承

  ```js
  function Parent (name) {
    // 父类定义自己的属性和方法
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
  }
  // 父类定义原型上的属性和方法
  Parent.prototype.getName = function () {
  
    console.log(this.name)
  }
  
  function Child (name, age) {
    // 子类继承父类的属性和方法
    Parent.call(this, name);
    // 子类定义自己的属性和方法
    this.age = age;
  }
  // 构造函数的原型指向父类的实例，实例可以访问父类型的原型的方法
  Child.prototype = new Parent();
  // 新原型的constructor属性修正为构造函数
  Child.prototype.constructor = Child; 
  
  var child1 = new Child('kevin', '18');
  
  child1.colors.push('black');
  
  console.log(child1.name); // kevin
  console.log(child1.age); // 18
  console.log(child1.colors); // ["red", "blue", "green", "black"]
  
  var child2 = new Child('daisy', '20');
  
  console.log(child2.name); // daisy
  console.log(child2.age); // 20
  console.log(child2.colors); // ["red", "blue", "green"]
  ```

下面是 class 继承

  ```js
  class Point {
    // 在顶部定义固有属性
    foo = 'foo'
    // 在构造函数内定义父类的可变属性
    constructor(x,y){
      this.x = x
      this.y = y
    }
   // 定义父类原型上的方法
    sayPoint(){
      return `(${this.x}.${this.y})`
  }
  
  class ColorPoint extends Point {
    constructor(x, y, color) {
      super(x, y); // 调用父类的constructor(x, y) → Parent.call(this, name);
      this.color = color;
    }
    // 在子类的原型上定义方法
    sayColorPoint() {
      return this.color + ' ' + super.sayPoint(); // 调用父类的toString()
    }
  }
  
  let color = new ColorPoint(1,1,'red')
  ```

子类继承父类的属性和方法

```js
  color.foo // foo
```

**构造函数的原型** 指向父类的实例，**实例** 可以访问父类型的原型的方法

```js
  // 子类的原型指向父类的实例
  ColorPoint.prototype instanceof Point // true
  // 在子类的内部定义的方法在父类的实例身上
  ColorPoint.prototype.hasOwnProperty('sayColorPoint') // true
  // 实例的隐式原型是父类的实例
  Object.getPrototypeOf(color) instanceof Point // true
  // 实例可以访问父类的原型的方法
  color.sayPoint() // (1,1)
```

> 如果是寄生组合式继承，子类的构造函数的原型指向的是一个 **空函数** 的实例

子类的实例，即是子类的实例，也是父类的实例，本质都是沿着原型链

```js
  color instanceof ColorPoint // true
  color instanceof Point // true
```

**新原型** 的 constructor 属性修正为构造函数

```js
  ColorPoint.prototype.constructor === ColorPoint // true
```

```js
  // 子类的原型指向父类的实例
  ColorPoint.prototype instanceof Point // true
  color = new ColorPoint()
  console.log(Object.getPrototypeOf(color))
```

可以看到 **Point 类的实例** 如下，有着在子类内部定义的方法和被修正的 constructor 属性

  ![1555229904808](/img/user/programming/font-end/primitive/es/es-proto/1555229904808.png)

注意：虽然是大写开头，但是 Point 是一个实例，而不是一个构造函数，`class语法` 的构造函数是 `class` 关键字开头的，如 `class ColorPoint`

## 区别

### 继承父类的属性和方法

在 ES5 的经典组合继承中，我们通过 call 方法 **借用** 父构造函数来继承父类的属性和方法，

  ```js
  function Child (name, age) {
    // 子类继承父类的属性和方法
    Parent.call(this, name);
    // 子类定义自己的属性和方法
    this.age = age;
  }
  ```

在 class 语法中当然也有同样的步骤，不同之处在，class 语法需要通过 **super** 来 **借用** 父构造函数

  ```js
  class Point {
    // 在顶部定义固有属性
    foo = 'foo'
    // 在构造函数内定义父类的可变属性
    constructor(x,y){
      this.x = 'x:' + x
      this.y = 'y:' + y
    }
  }
  
  class ColorPoint extends Point {
    constructor(x, y, color) {
      super(x, y); // 调用父类的constructor(x, y)
      this.color = color;
    }
  }
  ```

  ```js
  color.x // 'x:1'
  color.y // 'y:1'
  color.foo // 'foo'
  ```

#### 对比

ES5 的继承，实质是先创造子类的实例对象 `this`，然后再将父类的方法添加到 `this` 上面（`Parent.apply(this)`）。

ES6 的继承机制稍有不同，实质是：

  1. 通过父类构造函数创建 `this`（所以必须先调用 `super` 方法）, this 虽然是由父类构造函数创建的，但是 this 仍然指向子类的实例
  2. 然后将 **父类实例对象** 的属性和方法，加到 `this` 上面
  3. 最后再用子类的构造函数修改 `this`。

这个差别使得 ES6 可以继承内置对象。 @@@

#### 注意

如果不调用 `super` 方法，子类就得不到 `this` 对象

```js
  class Point { /* ... */ }
  
  class ColorPoint extends Point {
    constructor() {
    }
  }
  
  let cp = new ColorPoint(); // ReferenceError
```

另一个需要注意的地方是，在子类的构造函数中，**只有调用 `super` 之后，才可以使用 `this` 关键字**，否则会报错。这是因为子类实例的构建，基于父类实例，我们必须先得到 `this`，只有 `super` 方法才能创建 `this`

  ```js
  class Point {
    constructor(x, y) {
      this.x = x;
      this.y = y;
    }
  }
  
  class ColorPoint extends Point {
    constructor(x, y, color) {
      this.color = color; // ReferenceError
      super(x, y);
      this.color = color; // 正确
    }
  }
  ```

如果子类没有定义 `constructor` 方法，这个方法会被默认添加，代码如下。也就是说，不管有没有显式定义，任何一个子类都有 `constructor` 方法，**同时也会调用 super（）**

  ```js
  class ColorPoint extends Point {
  }
  
  // 等同于
  class ColorPoint extends Point {
    constructor(...args) {
      super(...args);
    }
  }
  ```

### 静态方法的继承

在 ES5 经典组合继承中，父构造函数自身的属性和方法，子构造函数肯定是没有的，因为子构造函数的隐式原型始终没有被改变，还是指向 `Funtion.prototype`

而 class 继承中，父类的静态方法，也会被子类继承

  ```js
  class A {
    static hello() {
      console.log('hello world');
    }
  }
  
  class B extends A {
  }
  
  B.hello()  // hello world
  ```

这说明，class 继承比经典组合继承还多了一个步骤

  ```js
  // 令子类的隐式原型指向父类
  Object.setPrototypeOf(B,A)
  // 相当于是让A代替了Function.prototype
  ```

class 继承并没有对 A 做 constructor 属性的修正，因为 A 的隐式原型指向 Function.prototype，而且 A 和 B 都是 Function 的实例，沿着隐式原型链可以返回正确的结果

```js
  Object.getPrototypeOf(B) // class A
  constructor in Object.getPrototypeOf(B) // false → constuctor in A false
  Object.getPrototypeOf(B).constructor // Function
```

#### 用 ES5 经典继承模拟 Class

```js
  function Parent (name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
  }
  // 定义父类的静态方法
  Parent.static = 'static'
  Parent.prototype.getName = function () {
    console.log(this.name)
  }
  
  function Child (name, age) {
    Parent.call(this, name);
    this.age = age;
  }
  Child.prototype = new Parent();
  Child.prototype.constructor = Child; 
  // 令子类的隐式原型指向父类，以访问父类的属性和方法
  Object.setPrototypeOf(Child,Parent)
  
  var child1 = new Child('kevin', '18');
  child1.colors.push('black');
  var child2 = new Child('daisy', '20'); 
```

```js
  console.log(Child.static) // static
```

## Super 关键字

上文我们提到，在类的内部 `this` 指向类的实例，所以我们可以通过 `this` 访问实例的属性、方法和原型的属性和方法，那么类继承之后，有没有办法在类的内容访问父类的各种属性和方法呢？

访问的关键就是 super 关键字，**既可以当作函数使用，也可以当作对象使用**。

在这两种情况下，它的 **用法完全不同**，通过 super 关键字可以在子类中访问父类的：

  + 父类的构造函数：super 作为函数使用，在子类的 `constuctor` 方法内访问父类的构造函数
  + 父类本身：super 作为对象使用，在子类的静态方法内部访问父类的本身（静态方法和静态属性）
  + 父类的原型：super 作为对象使用，在子类的原型的方法内部访问父类的原型（属性和方法）

### 作为函数使用

第一种情况，`super` 作为函数调用时，**代表父类的构造函数**。

ES6 要求，子类的构造函数必须执行一次 `super` 函数，通过 `super` 函数创建 `this`

  ```js
  class A {}
  
  class B extends A {
    constructor(...args) {
      super(..args); // 在这一步调用了父类的构造函数，对this的方法和属性进行绑定，实现继承
    }
  }
  ```

#### This 的指向

`super` 虽然代表了父类 `A` 的构造函数，但是 **返回的是子类 `B` 的实例**，即 `super` 内部的 `this` 指的是 `B` 的实例，因此 `super()` 在这里相当于 `A.prototype.constructor.call(this)`。

```js
  class A {
    constructor() {
      console.log(new.target.name);
    }
  }
  class B extends A {
    constructor(...args) {
      super(...args);
    }
  }
  new A() // A
  new B() // B
```

上面代码中，`new.target` 指向当前正在执行的 **构造函数**。可以看到，在 `super()` 执行时，它指向的是子类 `B` 的构造函数，而不是父类 `A` 的构造函数。也就是说，`super()` 内部的 `this` 指向的是 `B`。

#### 注意

作为函数时, `super()` 只能用在 **子类的 constructor 方法** 之中，用在其他地方就会报错。

```javascript
  class A {}
  
  class B extends A {
    m() {
      super(); // 报错
    }
  }
```

上面代码中，`super()` 用在 `B` 类的 `m` 方法之中，就会造成句法错误。

### 作为对象使用

第二种情况，`super` 作为对象时，**在普通方法中，指向父类的原型对象；在静态方法中，指向父类。**

#### 访问父类的原型

```js
  class A {
    p() {
      return 2;
    }
  }
  
  class B extends A {
    constructor() {
      super();
      console.log(super.p()); // 2
    }
  }
  
  let b = new B();
```

上面代码中，子类 `B` 当中的 `super.p()`，就是将 `super` 当作一个对象使用。这时，`super` 在普通方法之中，指向 `A.prototype`，所以 `super.p()` 就相当于 `A.prototype.p()`。

如果属性定义在父类的原型对象上，`super` 也可以取到

```js
  class A {}
  A.prototype.x = 2;
  
  class B extends A {
    constructor() {
      super();
      console.log(super.x) // 2
    }
  }
  
  let b = new B(); 
```

#### This 的指向

ES6 规定，在子类普通方法中通过 `super` 调用父类的方法时，方法内部的 `this` 指向当前的 **子类实例**。

  ```js
  class A {
    constructor() {
      this.x = 1;
    }
    print() {
      console.log(this.x);
    }
  }
  
  class B extends A {
    constructor() {
      super();
      this.x = 2;
    }
    m() {
      super.print();
    }
  }
  
  let b = new B();
  b.m() // 2
  ```

上面代码中，`super.print()` 虽然调用的是 `A.prototype.print()`，但是 `A.prototype.print()` 内部的 `this` 指向子类 `B` 的实例，导致输出的是 `2`，而不是 `1`。也就是说，实际上执行的是 `super.print.call(this)`。

由于 `this` 指向子类实例，所以如果通过 `super` 对某个属性赋值，这时 `super` 就是 `this`，赋值的属性会变成子类实例的属性。

  ```js
  class A {
    constructor() {
      this.x = 1;
    }
  }
  
  class B extends A {
    constructor() {
      super();
      this.x = 2;
      super.x = 3;
      console.log(super.x); // undefined
      console.log(this.x); // 3
    }
  }
  
  let b = new B();
  ```

#### 注意

这里需要注意，由于 `super` 指向父类的原型对象，所以定义在 **父类实例** 上的方法或属性，是无法通过 `super` 调用的。

```js
  class A {
    constructor() {
      this.p = 2;
    }
  }
  
  class B extends A {
    get m() {
      return super.p;
    }
  }
  
  let b = new B();
  b.m // undefined
```

#### 访问父类本身

如果 `super` 作为对象，用在静态方法之中，这时 `super` 将指向父类，而不是父类的原型对象。

  ```js
  class Parent {
    static myMethod(msg) {
      console.log('static', msg);
    }
  
    myMethod(msg) {
      console.log('prototype', msg);
    }
  }
  
  class Child extends Parent {
    static myMethod(msg) {
      super.myMethod(msg);
    }
  
    myMethod(msg) {
      super.myMethod(msg);
    }
  }
  
  Child.myMethod(1); // static 1
  
  var child = new Child();
  child.myMethod(2); // prototype 2
  ```

上面代码中，`super` 在静态方法之中指向父类，在普通方法之中指向父类的原型对象。因为只有静态方法可以定义在父类身上，其他的属性或者方法都是给到实例或这原型对象的，所以想要获取父类自身的属性也必须在静态方法之中，以做区分

#### This 的指向

在子类的静态方法中通过 `super` 调用父类的方法时，方法内部的 `this` 指向当前的子类，而不是子类的实例。

  ```js
  class A {
    constructor() {
      this.x = 1;
    }
    static print() {
      console.log(this.x);
    }
  }
  
  class B extends A {
    constructor() {
      super();
      this.x = 2;
    }
    static m() {
      super.print();
    }
  }
  
  B.x = 3;
  B.m() // 3
  ```

上面代码中，静态方法 `B.m` 里面，`super.print` 指向父类的静态方法。这个方法里面的 `this` 指向的是 `B`，而不是 `B` 的实例。

### 注意

注意，使用 `super` 的时候，必须 **显式指定** 是作为函数、还是作为对象使用，否则会报错。

  ```js
  class A {}
  
  class B extends A {
    constructor() {
      super();
      console.log(super); // 报错
    }
  }
  ```

```js
  class A {}
  
  class B extends A {
    constructor() {
      super();
      console.log(super.valueOf() instanceof B); // true
    }
  }
  
  let b = new B();
```

上面代码中，`super.valueOf()` 表明 `super` 是一个对象，因此就不会报错。同时，由于 `super` 使得 `this` 指向 `B` 的实例，所以 `super.valueOf()` 返回的是一个 `B` 的实例。

不可以使用 `super.constructor()` 代替 `super()`，原因待查 @@@

> 相当于是 parent.constructor()
>
> super() 则是 Parent.prototype.constructor()

箭头函数无 super，它会从外部函数中获取 super。例如：

  ```js
  class Rabbit extends Animal {
    stop() {
      setTimeout(() => super.stop(), 1000); // call parent stop after 1sec
    }
  }
  ```

箭头函数中的 super 与 stop() 中的相同，所以它按预期工作。如果我们在这里用普通函数，便会报错：

  ```js
  // Unexpected super
  setTimeout(function() { super.stop() }, 1000);
  ```

### 为什么继承类的构造函数要先调用 Super

参考 https://segmentfault.com/a/1190000015565616

#### 现象

在 JavaScript 中，继承了其他类的构造函数比较特殊。在继承类中，相应的构造函数被标记为特殊的内部属性 `[[ConstructorKind]]：“derived”`。

区别在于：

  - 当一个普通的构造函数运行时，它会创建一个空对象作为 this，然后继续运行。
  - 但是当派生的构造函数运行时，与上面说的不同，它指望父构造函数来完成这项工作。

首先要说的是，以我们迄今为止学到的知识来看，实现 super 是不可能的。

那么思考一下，这是什么原理？当一个对象方法运行时，它将当前对象作为 `this`。如果我们调用 `super.method()`，那么如何检索 `method`？很容易想到，我们需要从当前对象的原型中取出 `method`。从技术上讲，我们（或 JavaScript 引擎）可以做到这一点吗？

也许我们可以从 `this` 的 `[[Prototype]]` 中获得方法，就像 `this .__ proto __.method` 一样？不幸的是，这是行不通的。

让我们试一试，简单起见，我们不使用 class 了，直接使用普通对象。

在这里，`rabbit.eat()` 调用父对象的 `animal.eat()` 方法：

```js
  let animal = {
    name: "Animal",
    eat() {
      alert(`${this.name} eats.`);
    }
  };
  
  let rabbit = {
    __proto__: animal,
    name: "Rabbit",
    eat() {
  
      // that's how super.eat() could presumably work
      this.__proto__.eat.call(this); // (*)
  
    }
  };
  
  rabbit.eat(); // Rabbit eats.
```

在 `(*)` 这一行，我们从原型（`animal`）中取出 `eat`，并以当前对象的上下文中调用它。请注意，`.call（this）` 在这里很重要，因为只写 `this .__ proto __.eat()` 的话 `eat` 的调用对象将会是 `animal`，而不是当前对象

以上代码的 `alert` 是正确的。

但是现在让我们再添加一个对象到原型链中，就要出事了：

  ```js
  let animal = {
    name: "Animal",
    eat() {
      alert(`${this.name} eats.`);
    }
  };
  
  let rabbit = {
    __proto__: animal,
    eat() {
      // ...bounce around rabbit-style and call parent (animal) method
      this.__proto__.eat.call(this); // (*)
    }
  };
  
  let longEar = {
    __proto__: rabbit,
    eat() {
      // ...do something with long ears and call parent (rabbit) method
      this.__proto__.eat.call(this); // (**)
    }
  };
  
  
  longEar.eat(); // Error: Maximum call stack size exceeded
  ```

噢，完蛋！调用 `longEar.eat()` 报错了！

这原因一眼可能看不透，但如果我们跟踪 `longEar.eat()` 调用，大概就知道为什么了。在 `(*)` 和 `(**)` 两行中， `this` 的值是当前对象 (`longEar`)。重点来了：所有方法都将 `longEar` 作为 `this`

因此，在两行 `(*)` 和 `(**)` 中，`this.__ proto__` 的值都是 `rabbit`。他们都调用了 `rabbit.eat`，于是就这么无限循环下去。

情况如图：

  ![img](https://segmentfault.com/img/remote/1460000015565620?w=259&h=217)

这个问题不能简单使用 `this` 解决。

#### `[[HomeObject]]`

为了提供解决方案，JavaScript 为函数添加了一个特殊的内部属性：`[[HomeObject]]`。

**当函数被指定为类或对象方法时，其 `[[HomeObject]]` 属性为该对象。**

这实际上违反了 unbind 函数的思想，因为方法记住了它们的对象。并且 `[[HomeObject]]` 不能被改变，所以这是永久 bind（绑定）。所以在 JavaScript 这是一个很大的变化。

但是这种改变是安全的。 `[[HomeObject]]` 仅用于在 `super` 中获取下一层原型。所以它不会破坏兼容性。

让我们来看看它是如何在 `super` 中运作的：

```js
  let animal = {
    name: "Animal",
    eat() {         // [[HomeObject]] == animal
      alert(`${this.name} eats.`);
    }
  };
  
  let rabbit = {
    __proto__: animal,
    name: "Rabbit",
    eat() {         // [[HomeObject]] == rabbit
      super.eat();
    }
  };
  
  let longEar = {
    __proto__: rabbit,
    name: "Long Ear",
    eat() {         // [[HomeObject]] == longEar
      super.eat();
    }
  };
  
  
  longEar.eat();  // Long Ear eats.
  ```

每个方法都会在内部 `[[HomeObject]]` 属性中记住它的对象。然后 `super` 使用它来解析原型。通过 `[[HomeObject` 来追溯原型链, 同时也是 `super` 的调用链, 这样就不用绑定 this, 也就不会有上面死循环的问题了

在类和普通对象中定义的方法中都定义了 `[[HomeObject]]`，但是对于对象，必须使用：`method()` 而不是 `"method: function()"`。

在下面的例子中，使用非方法语法（non-method syntax）进行比较。这么做没有设置 `[[HomeObject]]` 属性，继承也不起作用：

  ```js
  let animal = {
    eat: function() { // should be the short syntax: eat() {...}
      // ...
    }
  };
  
  let rabbit = {
    __proto__: animal,
    eat: function() {
      super.eat();
    }
  };
  
  
  rabbit.eat();  // Error calling super (because there's no [[HomeObject]])
  ```

#### 结论

`super(...)` 其实相当于是 `this = new ParentConstructor(…);` 只是一个语法糖。

`ParentConstructor` 是被继承的类，`this=` 是对 `this` 进行了初始化。这里语法有点不正确，当然语法糖只是一个简单说法，实际上它是从 `new.target.prototype` 继承，而不是 `ParentConstructor.prototype`（`new` 是从 `ParentConstructor.prototype` 继承的）。这样就和 ES5 的继承方式没关系了，而是一个全新的继承方式。

```js
  class Parent {
      constructor() {
          // implicit (from the `super` call)
          //    new.target = Child;
          // implicit (because `Parent` doesn't extend anything):
          //    this = Object.create(new.target.prototype);
          console.log(new.target) // Child!
      }
  }
  class Child extends Parent {
      constructor() {
          // `this` is uninitialised (and would throw if accessed)
          // implicit (from the `new` call):
          //    new.target = Child 
          super(); // this = Reflect.construct(Parent, [], new.target);
          console.log(this);
      }
  }
  new Child
```

### 为什么不能用 super.constructor() 代替 super()

为什么？ 因为不是从 ParentConstructor.prototype 继承, 而是从 new.target.prototype 继承的

### 在普通对象中使用 Super

由于对象总是继承其他对象的，所以 **可以在任意一个对象** 中，使用 `super` 关键字。

  ```js
  var obj = {
    toString() {
      return "MyObject: " + super.toString();
    }
  };
  
  obj.toString(); // MyObject: [object Object]
  ```

更多在对象中的使用参见对象一章，在此处只要讨论类内部中的 `super` 关键字

## Extend 关键字

`extends` 关键字后面可以跟多种类型的值。

  ```javascript
  class B extends A {
  }
  ```

上面代码的 `A`，只要是一个有 `prototype` 属性的函数，就能被 `B` 继承。由于函数都有 `prototype` 属性（除了 `Function.prototype` 函数），因此 `A` 可以是任意函数。

### 子类继承 Object 类

```js
  class A extends Object {
  }
  
  A.__proto__ === Object // true
  A.prototype.__proto__ === Object.prototype // true
```

这种情况下，`A` 其实就是构造函数 `Object` 的复制，`A` 的实例就是 `Object` 的实例。

### 不存在任何继承

```js
  class A {
  }
  
  A.__proto__ === Function.prototype // true
  A.prototype.__proto__ === Object.prototype // true
```

这种情况下，`A` 作为一个基类（即不存在任何继承），就是一个普通函数，所以直接继承 `Function.prototype`。但是，`A` 调用后返回一个空对象（即 `Object` 实例），所以 `A.prototype.__proto__` 指向构造函数（`Object`）的 `prototype` 属性。

## 原生构造函数的继承

原生构造函数是指语言内置的构造函数，通常用来生成数据结构。

**ECMAScript 的原生构造函数**

  + Boolean()
  + Number()
  + String()
  + Array()
  + Date()
  + Function()
  + RegExp()
  + Error()
  + Object()

以前，这些原生构造函数是无法继承的，比如，不能自己定义一个 `Array` 的子类。

  ```js
  function MyArray() {
    Array.apply(this, arguments);
  }
  
  MyArray.prototype = Object.create(Array.prototype, {
    constructor: {
      value: MyArray,
      writable: true,
      configurable: true,
      enumerable: true
    }
  });
  ```

上面代码定义了一个继承 Array 的 `MyArray` 类。但是，这个类的行为与 `Array` 完全不一致。

  ```js
  var colors = new MyArray();
  colors[0] = "red";
  colors.length  // 0
  
  colors.length = 0;
  colors[0]  // "red"
  ```

之所以会发生这种情况，是因为 **子类无法获得原生构造函数的内部属性**，通过 `Array.apply()` 或者分配给原型对象都不行。原生构造函数会忽略 `apply` 方法传入的 `this`，也就是说，**原生构造函数的 `this` 无法绑定，导致拿不到内部属性。** @@@

ES5 是先新建子类的实例对象 `this`，再将父类的属性添加到子类上，由于父类的内部属性无法获取，导致无法继承原生的构造函数。

比如，`Array` 构造函数有一个内部属性 `[[DefineOwnProperty]]`，用来定义新属性时，更新 `length` 属性，这个内部属性无法在子类获取，导致子类的 `length` 属性行为不正常。

> ES5 之前都是通过寄生式继承的方法模拟

下面的例子中，我们想让一个普通对象继承 `Error` 对象。

  ```js
  var e = {};
  
  Object.getOwnPropertyNames(Error.call(e))
  // [ 'stack' ]
  
  Object.getOwnPropertyNames(e)
  // []
  ```

上面代码中，我们想通过 `Error.call(e)` 这种写法，让普通对象 `e` 具有 `Error` 对象的实例属性。但是，`Error.call()` 完全忽略传入的第一个参数，而是返回一个新对象，`e` 本身没有任何变化。这证明了 `Error.call(e)` 这种写法，无法继承原生构造函数。

ES6 允许继承原生构造函数定义子类，因为 ES6 是先新建父类的实例对象 `this`，然后再用子类的构造函数修饰 `this`，使得父类的所有行为都可以继承。下面是一个继承 `Array` 的例子。

```javascript
  class MyArray extends Array {
    constructor(...args) {
      super(...args);
    }
  }
  
  var arr = new MyArray();
  arr[0] = 12;
  arr.length // 1
  
  arr.length = 0;
  arr[0] // undefined
```

上面代码定义了一个 `MyArray` 类，继承了 `Array` 构造函数，因此就可以从 `MyArray` 生成数组的实例。这意味着，**ES6 可以自定义原生数据结构（比如 `Array`、`String` 等）的子类**，这是 ES5 无法做到的。上面这个例子也说明，`extends` 关键字不仅可以用来继承类，还可以用来继承原生的构造函数。因此可以在原生数据结构的基础上，定义自己的数据结构。下面就是定义了一个带版本功能的数组。

```javascript
  class VersionedArray extends Array {
    constructor() {
      super();
      this.history = [[]];
    }
    commit() {
      this.history.push(this.slice());
    }
    revert() {
      this.splice(0, this.length, ...this.history[this.history.length - 1]);
    }
  }
  
  var x = new VersionedArray();
  
  x.push(1);
  x.push(2);
  x // [1, 2]
  x.history // [[]]
  
  x.commit();
  x.history // [[], [1, 2]]
  
  x.push(3);
  x // [1, 2, 3]
  x.history // [[], [1, 2]]
  
  x.revert();
  x // [1, 2]
```

上面代码中，`VersionedArray` 会通过 `commit` 方法，将自己的当前状态生成一个版本快照，存入 `history` 属性。`revert` 方法用来将数组重置为最新一次保存的版本。除此之外，`VersionedArray` 依然是一个普通数组，所有原生的数组方法都可以在它上面调用。

下面是一个自定义 `Error` 子类的例子，可以用来定制报错时的行为。

  ```javascript
  class ExtendableError extends Error {
    constructor(message) {
      super();
      this.message = message;
      this.stack = (new Error()).stack;
      this.name = this.constructor.name;
    }
  }
  
  class MyError extends ExtendableError {
    constructor(m) {
      super(m);
    }
  }
  
  var myerror = new MyError('ll');
  myerror.message // "ll"
  myerror instanceof Error // true
  myerror.name // "MyError"
  myerror.stack
  // Error
  //     at MyError.ExtendableError
  //     ...
  ```

注意，继承 `Object` 的子类，有一个 [行为差异](http://stackoverflow.com/questions/36203614/super-does-not-pass-arguments-when-instantiating-a-class-extended-from-object)。

  ```javascript
  class NewObj extends Object{
    constructor(){
      super(...arguments);
    }
  }
  var o = new NewObj({attr: true});
  o.attr === true  // false
  ```

上面代码中，`NewObj` 继承了 `Object`，但是无法通过 `super` 方法向父类 `Object` 传参。这是因为 ES6 改变了 `Object` 构造函数的行为，一旦发现 `Object` 方法不是通过 `new Object()` 这种形式调用，ES6 规定 `Object` 构造函数会忽略参数。

## Mixin 模式的实现

Mixin 指的是多个对象合成一个新的对象，新对象具有各个组成成员的接口。它的最简单实现如下。

  ```javascript
  const a = {
    a: 'a'
  };
  const b = {
    b: 'b'
  };
  const c = {...a, ...b}; // {a: 'a', b: 'b'}
  ```

上面代码中，`c` 对象是 `a` 对象和 `b` 对象的合成，具有两者的接口。

下面是一个更完备的实现，将多个类的接口“混入”（mix in）另一个类。

  ```javascript
  function mix(...mixins) {
    class Mix {
      constructor() {
        for (let mixin of mixins) {
          copyProperties(this, new mixin()); // 拷贝实例属性
        }
      }
    }
  
    for (let mixin of mixins) {
      copyProperties(Mix, mixin); // 拷贝静态属性
      copyProperties(Mix.prototype, mixin.prototype); // 拷贝原型属性
    }
  
    return Mix;
  }
  
  function copyProperties(target, source) {
    for (let key of Reflect.ownKeys(source)) {
      if ( key !== 'constructor'
        && key !== 'prototype'
        && key !== 'name'
      ) {
        let desc = Object.getOwnPropertyDescriptor(source, key);
        Object.defineProperty(target, key, desc);
      }
    }
  }
  ```

上面代码的 `mix` 函数，可以将多个对象合成为一个类。使用的时候，只要继承这个类即可。

```javascript
  class DistributedEdit extends mix(Loggable, Serializable) {
    // ...
  }
```

# FAQ

#faq/js

## 原型链的面试题

![es-proto](programming/font-end/primitive/es/es-proto.md#原型链的面试题)

## 手动实现一个 Instanceof

![es-proto](programming/font-end/primitive/es/es-proto.md#Instanceof%20的底层实现原理)

## 私有属性

### 模拟内部属性

有时，我们会在对象上面设置 **内部属性**，属性名的第一个字符使用下划线开头，表示这些属性不应该被外部使用。结合 `get` 和 `set` 方法，就可以做到防止这些内部属性被外部读写。

### 私有属性

WeakMap 也可以被用于实现私有变量，不过在 ES6 中实现私有变量的方式有很多种，这只是其中一种：

```
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

上面代码中，`Countdown` 类的两个内部属性 `_counter` 和 `_action`，是实例的弱引用，所以如果删除实例，它们也就随之消失，不会造成内存泄漏。

### Symbol 的遍历

Symbol 作为属性名，该属性不会出现在 `for...in`、`for...of` 循环中，也不会被 `Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()` 返回。

但是，它也不是私有属性，有一个 `Object.getOwnPropertySymbols` 方法，可以获取指定对象的所有 Symbol 属性名。

`Object.getOwnPropertySymbols` 方法返回一个数组，成员是当前对象的所有用作属性名的 Symbol 值。

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

下面是另一个例子，`Object.getOwnPropertySymbols` 方法与 `for...in` 循环、`Object.getOwnPropertyNames` 方法进行对比的例子。

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

另一个新的 API，`Reflect.ownKeys` 方法可以返回所有类型的键名，包括常规键名和 Symbol 键名。

```javascript
let obj = {
  [Symbol('my_key')]: 1,
  enum: 2,
  nonEnum: 3
};

Reflect.ownKeys(obj)
//  ["enum", "nonEnum", Symbol(my_key)]
```

由于以 Symbol 值作为名称的属性，不会被常规方法遍历得到。我们可以利用这个特性，为对象定义一些 **非私有的**、但又希望 **只用于内部** 的方法。

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

上面代码中，对象 `x` 的 `size` 属性是一个 Symbol 值，所以 `Object.keys(x)`、`Object.getOwnPropertyNames(x)` 都无法获取它。这就造成了一种非私有的内部方法的效果。

## 实现继承的 N 种方法

![es-object](programming/font-end/primitive/es/es-object.md#继承)
