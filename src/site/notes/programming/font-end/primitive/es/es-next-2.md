---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/es/es-next-2/"}
---


# Proxy #

## 概述

Proxy 用于修改某些操作的**默认行为**，等同于在语言层面做出修改，所以属于一种“元编程”（meta programming），即对编程语言进行编程。

Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“**代理器**”。

```javascript
var obj = new Proxy({}, {
  get: function (target, key, receiver) {
    console.log(`getting ${key}!`);
    return Reflect.get(target, key, receiver);
  },
  set: function (target, key, value, receiver) {
    console.log(`setting ${key}!`);
    return Reflect.set(target, key, value, receiver);
  }
});
```

上面代码对一个空对象架设了一层拦截，重定义了属性的读取（`get`）和设置（`set`）行为。这里暂时先不解释具体的语法，只看运行结果。对设置了拦截行为的对象 `obj`，去读写它的属性，就会得到下面的结果。

```javascript
obj.count = 1
//  setting count!
++obj.count
//  getting count!
//  setting count!
//  2
```

上面代码说明，Proxy 实际上重载（overload）了点运算符，即用自己的定义覆盖了语言的原始定义。

## 构造函数

### 基本使用

ES6 原生提供 Proxy 构造函数，用来生成 Proxy 实例。

```javascript
var proxy = new Proxy(target, handler);
```

Proxy 对象的所有用法，都是上面这种形式，不同的只是 `handler` 参数的写法。

**参数**

- `target` 参数表示所要**拦截的目标对象**
- `handler` 参数也是一个**对象**，用来定制拦截行为，对于每一个被代理的操作，需要提供一个对应的处理函数，该函数将拦截对应的操作。
  - **`get` 方法**，用来拦截对目标对象属性的**访问请求**。`get` 方法的两个参数分别是目标对象和所要访问的属性。
  - 使用对象方法的简写语法

**示例**

- 下面是另一个拦截读取属性行为的例子。

  ```javascript
  var proxy = new Proxy({}, {
    get: function(target, property) {
      return 35;
    }
  });
  
  proxy.time // 35
  proxy.name // 35
  proxy.title // 35
  ```

- 第一个参数是所要代理的目标对象（上例是一个空对象），即如果没有 `Proxy` 的介入，操作原来要访问的就是这个对象
- 第二个参数是一个**配置对象**，配置对象有一个**`get` 方法**，由于拦截函数总是返回 `35`，所以访问任何属性都得到 `35`。
- **注意**，要使得 `Proxy` 起作用，必须**针对 `Proxy` 实例**（上例是 `proxy` 对象）进行操作，而不是针对目标对象（上例是空对象）进行操作。
- 如果 `handler` 没有设置任何拦截，那就等同于**直接通向原对象**。

  ```javascript
  var target = {};
  var handler = {};
  var proxy = new Proxy(target, handler);
  proxy.a = 'b';
  target.a // "b"
  ```

- 上面代码中，`handler` 是一个空对象，没有任何拦截效果，访问 `proxy` 就等同于访问 `target`。

### 拦截操作一览

同一个拦截器函数，可以设置拦截多个操作。

```javascript
var handler = {
  get: function(target, name) {
    if (name === 'prototype') {
      return Object.prototype;
    }
    return 'Hello, ' + name;
  },

  apply: function(target, thisBinding, args) {
    return args[0];
  },

  construct: function(target, args) {
    return {value: args[1]};
  }
};

var fproxy = new Proxy(function(x, y) {
  return x + y;
}, handler);

fproxy(1, 2) // 1
new fproxy(1, 2) // {value: 2}
fproxy.prototype === Object.prototype // true
fproxy.foo === "Hello, foo" // true
```

对于可以设置、但没有设置拦截的操作，则直接落在目标对象上，按照原先的方式产生结果。

下面是 Proxy 支持的拦截操作一览，一共 13 种。

- `get(target, propKey, receiver)`：拦截对象属性的**读取**，比如 `proxy.foo` 和 `proxy['foo']`。
- `set(target, propKey, value, receiver)`：拦截对象属性的**设置**，比如 `proxy.foo = v` 或 `proxy['foo'] = v`，返回一个布尔值。
- has(target, propKey)：拦截 `propKey in proxy` 的操作，返回一个布尔值。
- deleteProperty(target, propKey)：拦截 `delete proxy[propKey]` 的操作，返回一个布尔值。
- ownKeys(target)：拦截 `Object.getOwnPropertyNames(proxy)`、`Object.getOwnPropertySymbols(proxy)`、`Object.keys(proxy)`、`for...in` 循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而 `Object.keys()` 的返回结果仅包括目标对象自身的可遍历属性。
- getOwnPropertyDescriptor(target, propKey)：拦截 `Object.getOwnPropertyDescriptor(proxy, propKey)`，返回属性的描述对象。
- `defineProperty(target, propKey, propDesc)`：拦截 `Object.defineProperty(proxy, propKey, propDesc）`、`Object.defineProperties(proxy, propDescs)`，返回一个布尔值。
- preventExtensions(target)：拦截 `Object.preventExtensions(proxy)`，返回一个布尔值。
- getPrototypeOf(target)：拦截 `Object.getPrototypeOf(proxy)`，返回一个对象。
- isExtensible(target)：拦截 `Object.isExtensible(proxy)`，返回一个布尔值。
- setPrototypeOf(target, proto)：拦截 `Object.setPrototypeOf(proxy, proto)`，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
- `apply(target, object, args)`：拦截 Proxy 实例作为**函数调用**的操作，比如 `proxy(...args)`、`proxy.call(object, ...args)`、`proxy.apply(...)`。
- `construct(target, args)`：拦截 Proxy 实例作为**构造函数调用**的操作，比如 `new proxy(...args)`。

### 用途

一个**技巧**是将 Proxy 对象，设置到 `object.proxy`**属性**，从而可以在 `object` 对象上调用。

```javascript
var object = { proxy: new Proxy(target, handler) };
```

Proxy 实例也可以作为其他对象的**原型对象**。

```javascript
var proxy = new Proxy({}, {
  get: function(target, property) {
    return 35;
  }
});

let obj = Object.create(proxy);
obj.time // 35
```

上面代码中，`proxy` 对象是 `obj` 对象的原型，`obj` 对象本身并没有 `time` 属性，所以根据原型链，会在 `proxy` 对象上读取该属性，导致被拦截。

## Proxy 实例的方法

### get()

`get` 方法用于拦截某个属性的读取操作，可以接受三个参数，依次为**目标对象**、**属性名**和 proxy **实例本身**（严格地说，是操作行为所针对的对象），其中最后一个参数**可选**。

> 前两个必填？并不是，不填也是 ok 的，只是一般都会用到而已

#### 拦截读取操作

`get` 方法的用法，上文已经有一个例子，下面是另一个拦截读取操作的例子。

```javascript
var person = {
  name: "张三"
};

var proxy = new Proxy(person, {
  get: function(target, property) {
    if (property in target) {
      return target[property];
    } else {
      throw new ReferenceError("Property \"" + property + "\" does not exist.");
    }
  }
});

proxy.name // "张三"
proxy.age // 抛出一个错误
```

上面代码表示，如果访问目标对象不存在的属性，会抛出一个错误。如果没有这个拦截函数，访问不存在的属性，只会返回 `undefined`

#### 通过 Handler 给 Proxy 对象添加属性

访问不存在的属性, 也会被拦截

可以通过这个实现给所有的 proxy 对象都添加统一的属性: 如果访问的是 ReactiveFlags 属性, 可以直接返回对应的值.

> 这种做法和继承相比有啥优势和劣势呢?
```ts
  return function get(target, key, receiver) {
    const isExistInReactiveMap = () =>
      key === ReactiveFlags.RAW && receiver === reactiveMap.get(target);

    const isExistInReadonlyMap = () =>
      key === ReactiveFlags.RAW && receiver === readonlyMap.get(target);

    const isExistInShallowReadonlyMap = () =>
      key === ReactiveFlags.RAW && receiver === shallowReadonlyMap.get(target);

    if (key === ReactiveFlags.IS_REACTIVE) {
      return !isReadonly;
    } else if (key === ReactiveFlags.IS_READONLY) {
      return isReadonly;
    } else if (
      isExistInReactiveMap() ||
      isExistInReadonlyMap() ||
      isExistInShallowReadonlyMap()
    ) {
      return target;
    }
}
```

#### Get 方法可以继承

```javascript
let proto = new Proxy({}, {
  get(target, propertyKey, receiver) {
    console.log('GET ' + propertyKey);
    return target[propertyKey];
  }
});

let obj = Object.create(proto);
obj.foo // "GET foo"
```

上面代码中，拦截操作定义在 `Prototype` 对象上面，所以如果读取 `obj` 对象继承的属性时，拦截会生效。

#### Receiver

下面是一个 `get` 方法的第三个参数的例子，它总是指向**原始的读操作**所在的那个对象，一般情况下就是 Proxy 实例。

```javascript
const proxy = new Proxy({}, {
  get: function(target, property, receiver) {
    return receiver;
  }
});
proxy.getReceiver === proxy // true
```

上面代码中，`proxy` 对象的 `getReceiver` 属性是由 `proxy` 对象提供的，所以 `receiver`**指向 `proxy` 对象**。

```javascript
const proxy = new Proxy({}, {
  get: function(target, property, receiver) {
    return receiver;
  }
});

const d = Object.create(proxy);
d.a === d // true
```

上面代码中，`d` 对象本身没有 `a` 属性，所以读取 `d.a` 的时候，会去 `d` 的原型 `proxy` 对象找。这时，`receiver` 就**指向 `d`**，代表原始的读操作所在的那个对象。

#### Cinfigurable

如果一个属性不可配置（configurable）且不可写（writable），则 Proxy 不能修改该属性，否则通过 Proxy 对象访问该属性会**报错**。

```javascript
const target = Object.defineProperties({}, {
  foo: {
    value: 123,
    writable: false,
    configurable: false
  },
});

const handler = {
  get(target, propKey) {
    return 'abc';
  }
};

const proxy = new Proxy(target, handler);

proxy.foo
// TypeError: Invariant check failed
```

### 实现数组读取负数的索引

下面的例子使用 `get` 拦截，实现数组读取**负数的索引**。

```javascript
function createArray(...elements) {
  let handler = {
    get(target, propKey, receiver) {
      let index = Number(propKey);
      if (index < 0) {
        propKey = String(target.length + index);
      }
      return Reflect.get(target, propKey, receiver);
    }
  };

  let target = [];
  target.push(...elements);
  return new Proxy(target, handler);
}

let arr = createArray('a', 'b', 'c');
arr[-1] // c
```

上面代码中，数组的位置参数是 `-1`，就会输出数组的倒数第一个成员。

利用 Proxy，可以将读取属性的操作（`get`），转变为执行某个函数，从而实现属性的**链式操作**。

```javascript
var pipe = (function () {
  return function (value) {
    var funcStack = [];
    var oproxy = new Proxy({} , {
      get : function (pipeObject, fnName) {
        if (fnName === 'get') {
          return funcStack.reduce(function (val, fn) {
            return fn(val);
          },value);
        }
        funcStack.push(window[fnName]);
        return oproxy;
      }
    });

    return oproxy;
  }
}());

var double = n => n * 2;
var pow    = n => n * n;
var reverseInt = n => n.toString().split("").reverse().join("") | 0;

pipe(3).double.pow.reverseInt.get; // 63
```

### 实现一个生成各种 DOM 节点的通用函数 Dom

下面的例子则是利用 `get` 拦截，实现一个**生成各种 DOM 节点的通用函数**`dom`。

```javascript
const dom = new Proxy({}, {
  get(target, property) {
    return function(attrs = {}, ...children) {
      const el = document.createElement(property);
      for (let prop of Object.keys(attrs)) {
        el.setAttribute(prop, attrs[prop]);
      }
      for (let child of children) {
        if (typeof child === 'string') {
          child = document.createTextNode(child);
        }
        el.appendChild(child);
      }
      return el;
    }
  }
});

const el = dom.div({},
  'Hello, my name is ',
  dom.a({href: '//example.com'}, 'Mark'),
  '. I like:',
  dom.ul({},
    dom.li({}, 'The web'),
    dom.li({}, 'Food'),
    dom.li({}, '…actually that\'s it')
  )
);

document.body.appendChild(el);
```

### set()

`set` 方法用来拦截某个属性的**赋值操作**，可以接受四个参数，依次为**目标对象**、**属性名**、**属性值**和 Proxy **实例本身**，其中最后一个参数可选。

假定 `Person` 对象有一个 `age` 属性，该属性应该是一个不大于 200 的整数，那么可以使用 `Proxy` 保证 `age` 的属性值符合要求。

```javascript
let validator = {
  set: function(obj, prop, value) {
    if (prop === 'age') {
      if (!Number.isInteger(value)) {
        throw new TypeError('The age is not an integer');
      }
      if (value > 200) {
        throw new RangeError('The age seems invalid');
      }
    }

    // 对于满足条件的 age 属性以及其他属性，直接保存
    obj[prop] = value;
  }
};

let person = new Proxy({}, validator);

person.age = 100;

person.age // 100
person.age = 'young' // 报错
person.age = 300 // 报错
```

上面代码中，由于设置了存值函数 `set`，任何不符合要求的 `age` 属性赋值，都会抛出一个错误，这是**数据验证**的一种实现方法。利用 `set` 方法，还可以**数据绑定**，即每当对象发生变化时，会自动更新 DOM。

有时，我们会在对象上面设置**内部属性**，属性名的第一个字符使用下划线开头，表示这些属性不应该被外部使用。结合 `get` 和 `set` 方法，就可以做到防止这些内部属性被外部读写。

```javascript
const handler = {
  get (target, key) {
    invariant(key, 'get');
    return target[key];
  },
  set (target, key, value) {
    invariant(key, 'set');
    target[key] = value;
    return true;
  }
};
function invariant (key, action) {
  if (key[0] === '_') {
    throw new Error(`Invalid attempt to ${action} private "${key}" property`);
  }
}
const target = {};
const proxy = new Proxy(target, handler);
proxy._prop
// Error: Invalid attempt to get private "_prop" property
proxy._prop = 'c'
// Error: Invalid attempt to set private "_prop" property
```

下面是 `set` 方法第**四个参数**的例子。

```javascript
const handler = {
  set: function(obj, prop, value, receiver) {
    obj[prop] = receiver;
  }
};
const proxy = new Proxy({}, handler);
proxy.foo = 'bar';
proxy.foo === proxy // true
```

上面代码中，`set` 方法的第四个参数 `receiver`，指的是原始的操作行为所在的那个对象，一般情况下是 `proxy` 实例本身，请看下面的例子。

```javascript
const handler = {
  set: function(obj, prop, value, receiver) {
    obj[prop] = receiver;
  }
};
const proxy = new Proxy({}, handler);
const myObj = {};
Object.setPrototypeOf(myObj, proxy);

myObj.foo = 'bar';
myObj.foo === myObj // true
```

上面代码中，设置 `myObj.foo` 属性的值时，`myObj` 并没有 `foo` 属性，因此引擎会到 `myObj` 的原型链去找 `foo` 属性。`myObj` 的原型对象 `proxy` 是一个 Proxy 实例，设置它的 `foo` 属性会触发 `set` 方法。这时，第四个参数 `receiver` 就指向原始**赋值行为所在的对象 `myObj`**。

注意，如果目标对象自身的某个属性，不可写且不可配置，那么 `set` 方法将不起作用。

```javascript
const obj = {};
Object.defineProperty(obj, 'foo', {
  value: 'bar',
  writable: false,
});

const handler = {
  set: function(obj, prop, value, receiver) {
    obj[prop] = 'baz';
  }
};

const proxy = new Proxy(obj, handler);
proxy.foo = 'baz';
proxy.foo // "bar"
```

上面代码中，`obj.foo` 属性不可写，Proxy 对这个属性的 `set` 代理将不会生效。

注意，**严格模式**下，`set` 代理如果没有返回 `true`，就会报错。

```javascript
'use strict';
const handler = {
  set: function(obj, prop, value, receiver) {
    obj[prop] = receiver;
    // 无论有没有下面这一行，都会报错
    return false;
  }
};
const proxy = new Proxy({}, handler);
proxy.foo = 'bar';
// TypeError: 'set' on proxy: trap returned falsish for property 'foo'
```

上面代码中，严格模式下，`set` 代理返回 `false` 或者 `undefined`，都会报错。

### apply()

`apply` 方法拦截**函数的调用、`call` 和 `apply` 操作**。

`apply` 方法可以接受三个参数，分别是**目标对象**、**目标对象的上下文对象（`this`）**和**目标对象的参数数组**。

```javascript
var handler = {
  apply (target, ctx, args) {
    return Reflect.apply(...arguments);
  }
};
```

下面是一个例子。

```javascript
var target = function () { return 'I am the target'; };
var handler = {
  apply: function () {
    return 'I am the proxy';
  }
};

var p = new Proxy(target, handler);

p()
// "I am the proxy"
// 上面代码中，变量p是 Proxy 的实例，当它作为函数调用时（p()），就会被apply方法拦截，返回一个字符串。
```

下面是另外一个例子。

```javascript
var twice = {
  apply (target, ctx, args) {
    return Reflect.apply(...arguments) * 2;
  }
};
function sum (left, right) {
  return left + right;
};
var proxy = new Proxy(sum, twice);
proxy(1, 2) // 6
proxy.call(null, 5, 6) // 22
proxy.apply(null, [7, 8]) // 30
// 上面代码中，每当执行proxy函数（直接调用或call和apply调用），就会被apply方法拦截。
```

另外，直接调用 `Reflect.apply` 方法，也会被拦截。

```javascript
Reflect.apply(proxy, null, [9, 10]) // 38
```

### 自定义的方法

```javascript
const target = {
  m: function () {
    console.log(this === proxy);
  }
};
const handler = {};

const proxy = new Proxy(target, handler);

target.m() // false
proxy.m()  // true
```

通过 proxy 访问 m 属性，也会被代理

## Proxy.revocable()

`Proxy.revocable` 方法返回一个可取消的 Proxy 实例。

```javascript
let target = {};
let handler = {};

let {proxy, revoke} = Proxy.revocable(target, handler);

proxy.foo = 123;
proxy.foo // 123

revoke();
proxy.foo // TypeError: Revoked
```

`Proxy.revocable` 方法返回一个对象，该对象的 `proxy` 属性是 `Proxy` 实例，`revoke` 属性是一个函数，可以取消 `Proxy` 实例。上面代码中，当执行 `revoke` 函数之后，再访问 `Proxy` 实例，就会抛出一个错误。

`Proxy.revocable` 的一个使用场景是，目标对象不允许直接访问，必须通过代理访问，一旦访问结束，就收回代理权，不允许再次访问。

## This 问题

虽然 Proxy 可以代理针对目标对象的**访问**，但它不是目标对象的透明代理，即不做任何拦截的情况下，也无法保证与目标对象的行为一致。

主要原因就是在 Proxy 代理的情况下，目标对象内部的 `this` 关键字会指向 Proxy 代理。

```javascript
const target = {
  m: function () {
    console.log(this === proxy);
  }
};
const handler = {};

const proxy = new Proxy(target, handler);

target.m() // false
proxy.m()  // true
```

上面代码中，一旦 `proxy` 代理 `target.m`，后者内部的 `this` 就是指向 `proxy`，而不是 `target`。

下面是一个例子，由于 `this` 指向的变化，导致 Proxy 无法代理目标对象。

```javascript
const _name = new WeakMap();

class Person {
  constructor(name) {
    _name.set(this, name);
  }
  get name() {
    return _name.get(this);
  }
}

const jane = new Person('Jane');
jane.name // 'Jane'

const proxy = new Proxy(jane, {});
proxy.name // undefined
```

上面代码中，目标对象 `jane` 的 `name` 属性，实际保存在外部 `WeakMap` 对象 `_name` 上面，通过 `this` 键区分。由于通过 `proxy.name` 访问时，`this` 指向 `proxy`，导致无法取到值，所以返回 `undefined`。

此外，有些原生对象的内部属性，只有通过正确的 `this` 才能拿到，所以 Proxy 也**无法代理**这些原生对象的属性。

```javascript
const target = new Date();
const handler = {};
const proxy = new Proxy(target, handler);

proxy.getDate();
// TypeError: this is not a Date object.
```

上面代码中，`getDate` 方法只能在 `Date` 对象实例上面拿到，如果 `this` 不是 `Date` 对象实例就会报错。这时，**`this` 绑定原始对象**，就可以解决这个问题。

```javascript
const target = new Date('2015-01-01');
const handler = {
  get(target, prop) {
    if (prop === 'getDate') {
      return target.getDate.bind(target);
    }
    return Reflect.get(target, prop);
  }
};
const proxy = new Proxy(target, handler);

proxy.getDate() // 1
```

## 实例

<https://zhuanlan.zhihu.com/p/40772736>

## 实例：Web 服务的客户端

Proxy 对象可以拦截目标对象的任意属性，这使得它很合适用来写 Web 服务的客户端。

```javascript
const service = createWebService('http://example.com/data');

service.employees().then(json => {
  const employees = JSON.parse(json);
  // ···
});
```

上面代码新建了一个 Web 服务的接口，这个接口返回各种数据。Proxy 可以拦截这个对象的任意属性，所以不用为每一种数据写一个适配方法，只要写一个 Proxy 拦截就可以了。

```javascript
function createWebService(baseUrl) {
  return new Proxy({}, {
    get(target, propKey, receiver) {
      return () => httpGet(baseUrl+'/' + propKey);
    }
  });
}
```

同理，Proxy 也可以用来实现数据库的 ORM 层。

# Reflect #

## 概述

`Reflect` 对象与 `Proxy` 对象一样，也是 ES6 为了**操作对象**而提供的新 API。`Reflect` 对象的设计目的有这样几个。

1. 将 `Object` 对象的一些明显属于**语言内部**的方法（比如 `Object.defineProperty`），**反射**到 `Reflect` 对象上。现阶段，某些方法同时在 `Object` 和 `Reflect` 对象上部署，未来的新方法将只部署在 `Reflect` 对象上。也就是说，从 `Reflect` 对象上可以拿到**语言内部**的方法。
2. 修改某些 `Object` 方法的返回结果，让其变得**更合理**。比如，`Object.defineProperty(obj, name, desc)` 在无法定义属性时，会抛出一个错误，而 `Reflect.defineProperty(obj, name, desc)` 则会返回 `false`。

   ```javascript
   // 老写法
   try {
     Object.defineProperty(target, property, attributes);
     // success
   } catch (e) {
     // failure
   }
   
   // 新写法
   if (Reflect.defineProperty(target, property, attributes)) {
     // success
   } else {
     // failure
   }
   ```

3. 让 `Object` 操作都变成函数行为。某些 `Object` 操作是**命令式**，比如 `name in obj` 和 `delete obj[name]`，而 `Reflect.has(obj, name)` 和 `Reflect.deleteProperty(obj, name)` 让它们变成了**函数行为**。

   ```javascript
   // 老写法
   'assign' in Object // true
   
   // 新写法
   Reflect.has(Object, 'assign') // true
   ```

4. `Reflect` 对象的方法**与 `Proxy` 对象的方法一一对应**，只要是 `Proxy` 对象的方法，就能在 `Reflect` 对象上找到对应的方法。这就让 `Proxy` 对象可以方便地调用对应的 `Reflect` 方法，完成默认行为，作为修改行为的基础。也就是说，不管 `Proxy` 怎么修改默认行为，你总可以在 `Reflect` 上**获取默认行为**。

   ```javascript
   Proxy(target, {
     set: function(target, name, value, receiver) {
       var success = Reflect.set(target,name, value, receiver);
       if (success) {
         console.log('property ' + name + ' on ' + target + ' set to ' + value);
       }
       return success;
     }
   });
   ```

- 上面代码中，`Proxy` 方法拦截 `target` 对象的属性赋值行为。它采用 `Reflect.set` 方法将值赋值给对象的属性，**确保完成原有的行为，然后再部署额外的功能。**
- 下面是另一个例子。

  ```javascript
  var loggedObj = new Proxy(obj, {
    get(target, name) {
      console.log('get', target, name);
      return Reflect.get(target, name);
    },
    deleteProperty(target, name) {
      console.log('delete' + name);
      return Reflect.deleteProperty(target, name);
    },
    has(target, name) {
      console.log('has' + name);
      return Reflect.has(target, name);
    }
  });
  ```

- 上面代码中，每一个 `Proxy` 对象的拦截操作（`get`、`delete`、`has`），内部都调用对应的 `Reflect` 方法，保证原生行为能够正常执行。添加的工作，就是将每一个操作**输出一行日志**。
- 有了 `Reflect` 对象以后，很多操作会更易读。

  ```javascript
  // 老写法
  Function.prototype.apply.call(Math.floor, undefined, [1.75]) // 1
  // 等价于，阮一峰真是个辣鸡
  console.log(Math.floor.apply(undefined,[1,75]))
  // 新写法
  Reflect.apply(Math.floor, undefined, [1.75]) // 1
  ```

## 静态方法

`Reflect` 对象一共有 **13 个静态方法**。

- Reflect.apply(target, thisArg, args)
- Reflect.construct(target, args)
- Reflect.get(target, **propertyKey**, receiver)
- Reflect.set(target, **propertyKey**, value, receiver)
- Reflect.defineProperty(target, **propertyKey**, desc)
- Reflect.deleteProperty(target, **propertyKey**)
- Reflect.has(target, **propertyKey**)
- Reflect.ownKeys(target)
- Reflect.isExtensible(target)
- Reflect.preventExtensions(target)
- Reflect.getOwnPropertyDescriptor(target, **propertyKey**)
- Reflect.getPrototypeOf(target)
- Reflect.setPrototypeOf(target, prototype)
- 上面这些方法的作用，大部分与 `Object` 对象的同名方法的作用都是相同的，而且它与 `Proxy` 对象的方法是一一对应的。下面是对它们的解释。

### Reflect.get(target, **propertyKey**, receiver)

- `Reflect.get` 方法查找并返回 `target` 对象的**propertyKey**，如果没有该属性，则返回 `undefined`。

  ```javascript
  var myObject = {
    foo: 1,
    bar: 2,
    get baz() {
      return this.foo + this.bar;
    },
  }
  
  Reflect.get(myObject, 'foo') // 1
  Reflect.get(myObject, 'bar') // 2
  Reflect.get(myObject, 'baz') // 3
  ```

- 如果 `propertyKey` 属性部署了读取函数（getter），则**读取函数的 `this` 绑定 `receiver`**，与 proxy 的 set() 有点不同，此处的 receiver 需要主动绑定，而 proxy 的 receiver 取决于调用的方式，**不可以**主动绑定

  ```javascript
  var myObject = {
    foo: 1,
    bar: 2,
    get baz() {
      return this.foo + this.bar;
    },
  };
  
  var myReceiverObject = {
    foo: 4,
    bar: 4,
  };
  
  Reflect.get(myObject, 'baz', myReceiverObject) // 8
  ```

- 如果第一个参数不是对象，`Reflect.get` 方法会报错。

  ```javascript
  Reflect.get(1, 'foo') // 报错
  Reflect.get(false, 'foo') // 报错
  ```

### Reflect.set(target, **propertyKey**, Value, receiver)

- `Reflect.set` 方法设置 `target` 对象的**`propertyKey`**等于 `value`。

  ```javascript
  var myObject = {
    foo: 1,
    set bar(value) {
      return this.foo = value;
    },
  }
  
  myObject.foo // 1
  
  Reflect.set(myObject, 'foo', 2);
  myObject.foo // 2
  
  Reflect.set(myObject, 'bar', 3)
  myObject.foo // 3
  ```

- 如果 `propertyKey` 设置了**赋值函数**，则赋值函数的 `this` 绑定 `receiver`。

  ```javascript
  var myObject = {
    foo: 4,
    set bar(value) {
      return this.foo = value;
    },
  };
  
  var myReceiverObject = {
    foo: 0,
  };
  
  Reflect.set(myObject, 'bar', 1, myReceiverObject);
  myObject.foo // 4
  myReceiverObject.foo // 1
  
  // target的foo并没有改变，只有receiver改变了，setter的返回值没有给到target
  
  ```

- 注意，如果 `Proxy` 对象和 `Reflect` 对象**联合使用**，前者拦截赋值操作，后者完成赋值的默认行为，而且传入了 `receiver`，那么 `Reflect.set` 会触发 `Proxy.defineProperty` 拦截。

  ```javascript
  let p = {
    a: 'a'
  };
  
  let handler = {
    set(target, key, value, receiver) {
      console.log('set');
      Reflect.set(target, key, value, receiver)
    },
    defineProperty(target, key, attribute) {
      console.log('defineProperty');
      Reflect.defineProperty(target, key, attribute);
    }
  };
  
  let obj = new Proxy(p, handler);
  obj.a = 'A';
  // set
  // defineProperty
  ```

- 上面代码中，`Proxy.set` 拦截里面**使用**了 `Reflect.set`，**而且传入**了 `receiver`，导致触发 `Proxy.defineProperty` 拦截。这是因为 `Proxy.set` 的 `receiver` 参数总是指向当前的 `Proxy` 实例（即上例的 `obj`），而 `Reflect.set` 一旦传入 `receiver`，就会将属性**赋值**到 `receiver` 上面（即 `obj`），导致触发 `defineProperty` 拦截。如果 `Reflect.set` 没有传入 `receiver`，那么就不会触发 `defineProperty` 拦截。
- 如果第一个参数不是对象，`Reflect.set` 会报错。

  ```javascript
  Reflect.set(1, 'foo', {}) // 报错
  Reflect.set(false, 'foo', {}) // 报错
  ```

### Reflect.has(obj, propertyKey)

### Reflect.deleteProperty(obj, propertyKey)

### Reflect.construct(target, args)

### Reflect.getPrototypeOf(obj)

### Reflect.setPrototypeOf(obj, newProto)

### Reflect.apply(func, thisArg, args)

### Reflect.defineProperty(target, propertyKey, attributes)

### Reflect.getOwnPropertyDescriptor(target, propertyKey)

### Reflect.isExtensible (target)

### Reflect.preventExtensions(target)

### Reflect.ownKeys (target)

## 实例：使用 Proxy 实现观察者模式

- 观察者模式（Observer mode）指的是函数自动观察数据**对象**，一旦对象有变化，函数就会自动执行。

  ```javascript
  const person = observable({
    name: '张三',
    age: 20
  });
  
  function print() {
    console.log(`${person.name}, ${person.age}`)
  }
  
  observe(print);
  person.name = '李四';
  // 输出
  // 李四, 20
  ```

- 上面代码中，数据对象 `person` 是观察目标，函数 `print` 是观察者。一旦数据对象发生变化，`print` 就会自动执行。
- 下面，使用 Proxy 写一个观察者模式的最简单实现，即**实现 `observable` 和 `observe` 这两个函数**。思路是 `observable` 函数返回一个原始对象的 Proxy 代理，拦截赋值操作，触发充当观察者的各个函数。

  ```javascript
  // 使用set结构，可以保证按添加的顺序执行
  const queuedObservers = new Set();
  
  const observe = fn => queuedObservers.add(fn);
  const observable = obj => new Proxy(obj, {set});
  
  // handler
  function set(target, key, value, receiver) {
    const result = Reflect.set(target, key, value, receiver);
    queuedObservers.forEach(observer => observer());
    return result;
  }
  ```

- 上面代码中，先定义了一个 `Set` 集合，所有观察者函数都放进这个集合。然后，`observable` 函数返回原始对象的代理，拦截赋值操作。拦截函数 `set` 之中，会自动执行所有观察者。

# Iterator

## Iterator（遍历器）的概念

- JavaScript 原有的表示“**集合**”的数据结构，主要是数组（`Array`）和对象（`Object`），ES6 又添加了 `Map` 和 `Set`。这样就有了四种数据集合，用户还可以组合使用它们，定义自己的数据结构，比如数组的成员是 `Map`，`Map` 的成员是对象。这样就需要一种**统一的接口机制**，来处理所有不同的数据结构。
- 遍历器（Iterator）就是这样一种机制。它是一种接口，为各种不同的数据结构提供统一的访问机制。**任何数据结构只要部署 Iterator 接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）。**
- **Iterator 的作用有三个**：
  - 一是为各种数据结构，提供一个统一的、简便的访问接口；
  - 二是使得数据结构的成员能够按某种次序排列；
  - 三是 ES6 创造了一种新的遍历命令 `for...of` 循环，Iterator 接口主要供 `for...of` 消费。#
- **Iterator 的遍历过程是这样的**：

  （1）创建一个**指针对象**，指向当前数据结构的起始位置。也就是说，遍历器对象**本质**上，就是一个指针对象。

  （2）第一次调用指针对象的**`next` 方法**，可以将指针指向数据结构的第一个成员。

  （3）第二次调用指针对象的 `next` 方法，指针就指向数据结构的第二个成员。

  （4）不断调用指针对象的 `next` 方法，直到它指向数据结构的结束位置。

- 每一次调用 `next` 方法，都会返回数据结构的当前成员的信息。具体来说，就是返回一个**包含 `value` 和 `done` 两个属性的对象**。其中，`value` 属性是当前成员的值，`done` 属性是一个布尔值，表示遍历是否结束。
- 下面是一个模拟 `next` 方法返回值的例子。

  ```javascript
  var it = makeIterator(['a', 'b']);
  
  it.next() // { value: "a", done: false }
  it.next() // { value: "b", done: false }
  it.next() // { value: undefined, done: true }
  
  function makeIterator(array) {
    var nextIndex = 0;
    return {
      next: function() {
        return nextIndex < array.length ?
          {value: array[nextIndex++], done: false} :
          {value: undefined, done: true};
      }
    };
  }
  ```

- 上面代码定义了一个 `makeIterator` 函数，它是一个**遍历器生成函数**，作用就是返回一个遍历器对象。对数组 `['a','b']` 执行这个函数，就会返回该数组的遍历器对象（即指针对象）`it`。
- 指针对象的 `next` 方法，用来移动指针。开始时，指针指向数组的开始位置。然后，每次调用 `next` 方法，指针就会指向数组的下一个成员。第一次调用，指向 `a`；第二次调用，指向 `b`。
- `next` 方法返回一个对象，表示当前数据成员的信息。这个对象具有 `value` 和 `done` 两个属性，`value` 属性返回当前位置的成员，`done` 属性是一个布尔值，表示遍历是否结束，即是否还有必要再一次调用 `next` 方法。
- 总之，调用指针对象的 `next` 方法，就可以遍历事先给定的数据结构。
- 对于遍历器对象来说，`done: false` 和 `value: undefined` 属性都是可以省略的，因此上面的 `makeIterator` 函数可以简写成下面的形式。

  ```javascript
  function makeIterator(array) {
    var nextIndex = 0;
    return {
      next: function() {
        return nextIndex < array.length ?
          {value: array[nextIndex++]} :
          {done: true};
      }
    };
  }
  ```

- 由于 Iterator 只是把接口规格加到数据结构之上，所以，**遍历器与它所遍历的那个数据结构，实际上是分开的**，完全可以写出没有对应数据结构的遍历器对象，或者说用遍历器对象模拟出数据结构。下面是一个无限运行的遍历器对象的例子。

  ```javascript
  var it = idMaker();
  
  it.next().value // 0
  it.next().value // 1
  it.next().value // 2
  // ...
  
  function idMaker() {
    var index = 0;
  
    return {
      next: function() {
        return {value: index++, done: false};
      }
    };
  }
  ```

- 上面的例子中，遍历器生成函数 `idMaker`，返回一个遍历器对象（即指针对象）。但是并没有对应的数据结构，或者说，遍历器对象自己描述了一个数据结构出来。
- 如果使用 **TypeScript** 的写法，遍历器接口（Iterable）、指针对象（Iterator）和 `next` 方法返回值的规格可以描述如下。

  ```ts
  interface Iterable {
    [Symbol.iterator]() : Iterator,
  }
  
  interface Iterator {
    next(value?: any) : IterationResult,
  }
  
  interface IterationResult {
    value: any,
    done: boolean,
  }
  ```

## 默认 Iterator 接口

### 概述

- Iterator 接口的目的，就是为所有数据结构，提供了一种统一的**访问机制**，**即 `for...of` 循环**（详见下文）。当使用 `for...of` 循环遍历某种数据结构时，该循环会自动去寻找 Iterator 接口。
- 一种数据结构只要部署了 Iterator 接口，我们就称这种**数据结构是“可遍历的”（iterable）**。
- ES6 规定，默认的 Iterator 接口部署在**数据结构的 `Symbol.iterator` 属性**，或者说，一个数据结构只要具有 `Symbol.iterator` 属性，就可以认为是“可遍历的”（iterable）。
- `Symbol.iterator` 属性本身是一个**函数**，就是当前数据结构默认的**遍历器生成函数**。执行这个函数，就会返回一个遍历器。
- 至于**属性名**`Symbol.iterator`，它是一个表达式，返回 `Symbol` 对象的 `iterator` 属性，这是一个预定义好的、类型为 Symbol 的特殊值，所以要放在方括号内（参见《Symbol》一章）。

  ```javascript
  const obj = {
    [Symbol.iterator] : function () {
      return {
        next: function () {
          return {
            value: 1,
            done: true
          };
        }
      };
    }
  };
  ```

- 上面代码中，对象 `obj` 是可遍历的（iterable），因为具有 `Symbol.iterator` 属性。执行这个属性，会返回一个遍历器对象。**该对象的根本特征就是具有 `next` 方法**。每次调用 `next` 方法，都会返回一个代表当前成员的信息对象，具有 `value` 和 `done` 两个属性。
- ES6 的有些数据结构**原生具备 Iterator 接口**（比如数组），即不用任何处理，就可以被 `for...of` 循环遍历。原因在于，这些数据结构原生部署了 `Symbol.iterator` 属性（详见下文），另外一些数据结构没有（比如对象）。凡是部署了 `Symbol.iterator` 属性的数据结构，就称为部署了遍历器接口。调用这个接口，就会返回一个遍历器对象。

### 原生具备 Iterator 接口的数据结构

- Array
- Map
- Set
- String
- TypedArray
- 函数的 arguments 对象
- NodeList 对象
- **注意**：arguments 对象和 NodeList 对象都是类数组对象，但是也原生具有 Iterator 接口
- **注意**：后文的 Generator 对象
- 下面的例子是数组的 `Symbol.iterator` 属性。

  ```javascript
  let arr = ['a', 'b', 'c'];
  let iter = arr[Symbol.iterator]();
  
  iter.next() // { value: 'a', done: false }
  iter.next() // { value: 'b', done: false }
  iter.next() // { value: 'c', done: false }
  iter.next() // { value: undefined, done: true }
  ```

- 上面代码中，变量 `arr` 是一个数组，原生就具有遍历器接口，部署在 `arr` 的 `Symbol.iterator` 属性上面。所以，调用这个属性，就得到遍历器对象。
- 对于原生部署 Iterator 接口的数据结构，不用自己写遍历器生成函数，`for...of` 循环会自动遍历它们。除此之外，其他数据结构（主要是对象）的 Iterator 接口，都需要自己在 `Symbol.iterator` 属性上面部署，这样才会被 `for...of` 循环遍历。

### 部署对象的 Iterator 接口

- 对象（Object）之所以没有默认部署 Iterator 接口，是因为对象的哪个属性先遍历，哪个属性后遍历是不确定的，需要开发者手动指定。本质上，遍历器是一种**线性处理**，对于任何非线性的数据结构，部署遍历器接口，就等于**部署一种线性转换**。不过，严格地说，对象部署遍历器接口并不是很必要，因为这时对象实际上被当作 Map 结构使用，ES5 没有 Map 结构，而 ES6 原生提供了。
- 一个对象如果要具备可被 `for...of` 循环调用的 Iterator 接口，就必须在 `Symbol.iterator` 的属性上部署遍历器生成方法（原型链上的对象具有该方法也可）。

  ```javascript
  class RangeIterator {
    constructor(start, stop) {
      this.value = start;
      this.stop = stop;
    }
  
    [Symbol.iterator]() { return this; }
  
    next() {
      var value = this.value;
      if (value < this.stop) {
        this.value++;
        return {done: false, value: value};
      }
      return {done: true, value: undefined};
    }
  }
  
  function range(start, stop) {
    return new RangeIterator(start, stop);
  }
  
  for (var value of range(0, 3)) {
    console.log(value); // 0, 1, 2
  }
  ```

- 上面代码是一个类部署 Iterator 接口的写法。`Symbol.iterator` 属性对应一个函数，执行后返回当前对象的遍历器对象。
- 下面是通过遍历器实现指针结构的例子。

  ```javascript
  function Obj(value) {
    this.value = value;
    this.next = null;
  }
  
  Obj.prototype[Symbol.iterator] = function() {
    var iterator = { next: next };
  
    var current = this;
  
    function next() {
      if (current) {
        var value = current.value;
        current = current.next;
        return { done: false, value: value };
      } else {
        return { done: true };
      }
    }
    return iterator;
  }
  
  var one = new Obj(1);
  var two = new Obj(2);
  var three = new Obj(3);
  
  one.next = two;
  two.next = three;
  
  for (var i of one){
    console.log(i); // 1, 2, 3
  }
  ```

- 上面代码首先在构造函数的原型链上部署 `Symbol.iterator` 方法，调用该方法会返回遍历器对象 `iterator`，调用该对象的 `next` 方法，在返回一个值的同时，自动将内部指针移到下一个实例。
- 下面是另一个为对象添加 Iterator 接口的例子。

  ```javascript
  let obj = {
    data: [ 'hello', 'world' ],
    [Symbol.iterator]() {
      const self = this;
      let index = 0;
      return {
        next() {
          if (index < self.data.length) {
            return {
              value: self.data[index++],
              done: false
            };
          } else {
            return { value: undefined, done: true };
          }
        }
      };
    }
  };
  ```

### 部署类数组对象的 Iterator 接口

- 对于**类似数组的对象**（存在数值键名和 `length` 属性），部署 Iterator 接口，有一个简便方法，就是 `Symbol.iterator` 方法**直接引用数组**的 Iterator 接口。

  ```javascript
  NodeList.prototype[Symbol.iterator] = Array.prototype[Symbol.iterator];
  // 或者
  NodeList.prototype[Symbol.iterator] = [][Symbol.iterator];
  
  [...document.querySelectorAll('div')] // 可以执行了
  ```

- NodeList 对象是类似数组的对象，本来就具有遍历接口，可以直接遍历。上面代码中，我们将它的遍历接口改成数组的 `Symbol.iterator` 属性，可以看到没有任何影响。
- 下面是另一个类似数组的对象调用数组的 `Symbol.iterator` 方法的例子。

  ```javascript
  let iterable = {
    0: 'a',
    1: 'b',
    2: 'c',
    length: 3,
    [Symbol.iterator]: Array.prototype[Symbol.iterator]
  };
  for (let item of iterable) {
    console.log(item); // 'a', 'b', 'c'
  }
  ```

- 注意，普通对象部署数组的 `Symbol.iterator` 方法，并无效果。

  ```javascript
  let iterable = {
    a: 'a',
    b: 'b',
    c: 'c',
    length: 3,
    [Symbol.iterator]: Array.prototype[Symbol.iterator]
  };
  for (let item of iterable) {
    console.log(item); // undefined, undefined, undefined
  }
  ```

- 如果 `Symbol.iterator` 方法对应的不是遍历器生成函数（即会返回一个遍历器对象），解释引擎将会报错。

  ```javascript
  var obj = {};
  
  obj[Symbol.iterator] = () => 1;
  
  [...obj] // TypeError: [] is not a function
  ```

- 上面代码中，变量 `obj` 的 `Symbol.iterator` 方法对应的不是遍历器生成函数，因此报错。
- 有了遍历器接口，数据结构就可以用 `for...of` 循环遍历（详见下文），也可以使用 `while` 循环遍历。

  ```javascript
  var $iterator = ITERABLE[Symbol.iterator]();
  var $result = $iterator.next();
  while (!$result.done) {
    var x = $result.value;
    // ...
    $result = $iterator.next();
  }
  ```

- 上面代码中，`ITERABLE` 代表某种可遍历的数据结构，`$iterator` 是它的遍历器对象。遍历器对象每次移动指针（`next` 方法），都检查一下返回值的 `done` 属性，如果遍历还没结束，就移动遍历器对象的指针到下一步（`next` 方法），不断循环。

## 调用 Iterator 接口的场合

- 有一些场合会默认调用 Iterator 接口（即 `Symbol.iterator` 方法），除了下文会介绍的 `for...of` 循环，还有几个别的场合。

### **解构赋值**

- 对数组和 Set 结构进行解构赋值时，会默认调用 `Symbol.iterator` 方法。

  ```javascript
  let set = new Set().add('a').add('b').add('c');
  
  let [x,y] = set;
  // x='a'; y='b'
  
  let [first, ...rest] = set;
  // first='a'; rest=['b','c'];
  ```

### **扩展运算符**

- 扩展运算符（...）也会调用默认的 Iterator 接口。

  ```javascript
  // 例一
  var str = 'hello';
  [...str] //  ['h','e','l','l','o']
  
  // 例二
  let arr = ['b', 'c'];
  ['a', ...arr, 'd']
  // ['a', 'b', 'c', 'd']
  ```

- 上面代码的扩展运算符内部就调用 Iterator 接口。
- 实际上，**这提供了一种简便机制，可以将任何部署了 Iterator 接口的数据结构，转为数组**。也就是说，只要某个数据结构部署了 Iterator 接口，就可以对它使用扩展运算符，将其转为数组。

  ```javascript
  let arr = [...iterable];
  ```

### **yield\***

- `yield*` 后面跟的是一个可遍历的结构，它会调用该结构的遍历器接口。

  ```javascript
  let generator = function* () {
    yield 1;
    yield* [2,3,4];
    yield 5;
  };
  
  var iterator = generator();
  
  iterator.next() // { value: 1, done: false }
  iterator.next() // { value: 2, done: false }
  iterator.next() // { value: 3, done: false }
  iterator.next() // { value: 4, done: false }
  iterator.next() // { value: 5, done: false }
  iterator.next() // { value: undefined, done: true }
  ```

### **其他场合**

- 由于数组的遍历会调用遍历器接口，所以任何接受数组作为参数的场合，其实都调用了遍历器接口。下面是一些例子。
- for...of
- Array.from()
- Map(), Set(), WeakMap(), WeakSet()（比如 `new Map([['a',1],['b',2]])`）
- Promise.all()
- Promise.race()

## 字符串的 Iterator 接口

- 字符串是一个类似数组的对象，也原生具有 Iterator 接口。
- ```javascript
  var someString = "hi";
  typeof someString[Symbol.iterator]
  // "function"
  
  var iterator = someString[Symbol.iterator]();
  
  iterator.next()  // { value: "h", done: false }
  iterator.next()  // { value: "i", done: false }
  iterator.next()  // { value: undefined, done: true }
  ```
- 上面代码中，调用 `Symbol.iterator` 方法返回一个遍历器对象，在这个遍历器上可以调用 next 方法，实现对于字符串的遍历。
- 可以覆盖原生的 `Symbol.iterator` 方法，达到修改遍历器行为的目的。

  ```javascript
  var str = new String("hi");
  
  [...str] // ["h", "i"]
  
  str[Symbol.iterator] = function() {
    return {
      next: function() {
        if (this._first) {
          this._first = false;
          return { value: "bye", done: false };
        } else {
          return { done: true };
        }
      },
      _first: true
    };
  };
  
  [...str] // ["bye"]
  str // "hi"
  ```

- 上面代码中，字符串 str 的 `Symbol.iterator` 方法被修改了，所以扩展运算符（`...`）返回的值变成了 `bye`，而字符串本身还是 `hi`。

## 遍历器对象的 return()，throw()

- 遍历器对象除了具有 `next` 方法，还可以具有 `return` 方法和 `throw` 方法。如果你自己写遍历器对象生成函数，那么 `next` 方法是**必须部署**的，`return` 方法和 `throw` 方法是否部署是**可选**的。
- `return` 方法的使用场合是，如果 `for...of` 循环提前退出（通常是因为出错，或者有 `break` 语句），就会调用 `return` 方法。如果一个对象在完成遍历前，需要清理或释放资源，就可以部署 `return` 方法。

  ```javascript
  function readLinesSync(file) {
    return {
      [Symbol.iterator]() {
        return {
          next() {
            return { done: false };
          },
          return() {
            file.close();
            return { done: true };
          }
        };
      },
    };
  }
  ```

- 上面代码中，函数 `readLinesSync` 接受一个文件对象作为参数，返回一个遍历器对象，其中除了 `next` 方法，还部署了 `return` 方法。下面的两种情况，都会触发执行 `return` 方法。

  ```javascript
  // 情况一
  for (let line of readLinesSync(fileName)) {
    console.log(line);
    break;
  }
  
  // 情况二
  for (let line of readLinesSync(fileName)) {
    console.log(line);
    throw new Error();
  }
  ```

- 上面代码中，情况一输出文件的第一行以后，就会执行 `return` 方法，关闭这个文件；情况二会在执行 `return` 方法关闭文件之后，再抛出错误。

  注意，`return` 方法必须返回一个对象，这是 Generator 规格决定的。

- `throw` 方法主要是配合 Generator 函数使用，一般的遍历器对象用不到这个方法。请参阅《Generator 函数》一章。

# for...of

ES6 借鉴 C++、Java、C# 和 Python 语言，引入了 `for...of` 循环，作为遍历**所有数据结构的统一的方法**。

一个数据结构只要部署了 `Symbol.iterator` 属性，就被视为具有 iterator 接口，就可以用 `for...of` 循环遍历它的成员。也就是说，`for...of` 循环内部调用的是数据结构的 `Symbol.iterator` 方法。

`for...of` 循环可以使用的范围包括数组、Set 和 Map 结构、某些类似数组的对象（比如 `arguments` 对象、DOM NodeList 对象）、后文的 Generator 对象，以及字符串。

**注意**：不同的数据结构 for...of 循环可以获取到的值是不同的

- 数组，键值，结合数组的 entries 等方法，可以获取索引
- set，键值，或者说键值、键名相同
- Map，一个数组包含键名和键值

## 数组

数组原生具备 `iterator` 接口（即默认部署了 `Symbol.iterator` 属性），**`for...of` 循环本质上就是调用这个接口产生的遍历器**，可以用下面的代码证明。

  ```javascript
  const arr = ['red', 'green', 'blue'];
  
  for(let v of arr) {
    console.log(v); // red green blue
  }
  
  const obj = {};
  obj[Symbol.iterator] = arr[Symbol.iterator].bind(arr);
  
  for(let v of obj) {
    console.log(v); // red green blue
  }
  ```

上面代码中，空对象 `obj` 部署了数组 `arr` 的 `Symbol.iterator` 属性，结果 `obj` 的 `for...of` 循环，产生了与 `arr` 完全一样的结果。

JavaScript 原有的 `for...in` 循环，只能获得对象的键名，不能直接获取键值。ES6 提供 `for...of` 循环，允许遍历获得键值。

  ```javascript
  var arr = ['a', 'b', 'c', 'd'];
  
  for (let a in arr) {
    console.log(a); // 0 1 2 3
  }
  
  for (let a of arr) {
    console.log(a); // a b c d
  }
  ```

上面代码表明，`for...in` 循环读取键名，`for...of` 循环读取键值。如果要通过 `for...of` 循环，获取数组的索引，可以借助数组实例的 `entries` 方法和 `keys` 方法

### 注意

`for...of` 循环调用遍历器接口，数组的遍历器接口只返回**具有数字索引**的属性。这一点跟 `for...in` 循环也不一样。

  ```javascript
  let arr = [3, 5, 7];
  arr.foo = 'hello';
  
  for (let i in arr) {
    console.log(i); // "0", "1", "2", "foo"
  }
  
  for (let i of arr) {
    console.log(i); //  "3", "5", "7"
  }
  ```

上面代码中，`for...of` 循环不会返回数组 `arr` 的 `foo` 属性。

## Set 和 Map 结构

- Set 和 Map 结构也原生具有 Iterator 接口，可以直接使用 `for...of` 循环。

  ```javascript
  var engines = new Set(["Gecko", "Trident", "Webkit", "Webkit"]);
  for (var e of engines) {
    console.log(e);
  }
  // Gecko
  // Trident
  // Webkit
  
  var es6 = new Map();
  es6.set("edition", 6);
  es6.set("committee", "TC39");
  es6.set("standard", "ECMA-262");
  for (var [name, value] of es6) {
    console.log(name + ": " + value);
  }
  // edition: 6
  // committee: TC39
  // standard: ECMA-262
  ```

- 上面代码演示了如何遍历 Set 结构和 Map 结构。Set 结构遍历时，**返回的是一个值**，而 Map 结构遍历时，返回的是一个数组，该数组的两个成员分别为当前 Map 成员的**键名和键值**。

  ```javascript
  let map = new Map().set('a', 1).set('b', 2);
  for (let pair of map) {
    console.log(pair);
  }
  // ['a', 1]
  // ['b', 2]
  
  for (let [key, value] of map) {
    console.log(key + ' : ' + value);
  }
  // a : 1
  // b : 2
  ```

### 遍历顺序

- map 和 set 遍历的顺序是按照各个成员被**添加进数据结构的顺序**。这个特性有时非常有用，比如使用 Set 保存一个回调函数列表，调用时就能保证按照添加顺序调用。

## 类似数组的对象 @@@

- 类似数组的对象包括好几类。下面是 `for...of` 循环用于字符串、DOM NodeList 对象、`arguments` 对象的例子。

  ```javascript
  // 字符串
  let str = "hello";
  
  for (let s of str) {
    console.log(s); // h e l l o
  }
  
  // DOM NodeList对象
  let paras = document.querySelectorAll("p");
  
  for (let p of paras) {
    p.classList.add("test");
  }
  
  // arguments对象
  function printArgs() {
    for (let x of arguments) {
      console.log(x);
    }
  }
  printArgs('a', 'b');
  // 'a'
  // 'b'
  ```

- 对于字符串来说，`for...of` 循环还有一个特点，就是会正确识别 32 位 UTF-16 字符。

  ```javascript
  for (let x of 'a\uD83D\uDC0A') {
    console.log(x);
  }
  // 'a'
  // '\uD83D\uDC0A'
  ```

- 并不是所有类似数组的对象都具有 Iterator 接口，一个简便的解决方法，就是使用 `Array.from` 方法将其转为数组。

  ```javascript
  let arrayLike = { length: 2, 0: 'a', 1: 'b' };
  
  // 报错
  for (let x of arrayLike) {
    console.log(x);
  }
  
  // 正确
  for (let x of Array.from(arrayLike)) {
    console.log(x);
  }
  ```

## 计算生成的数据结构

- 有些数据结构是在现有数据结构的基础上，计算生成的。比如，ES6 的数组、Set、Map **都部署了以下三个方法**，调用后都返回遍历器对象。
  - `entries()` 返回一个遍历器对象，用来遍历**[键名, 键值]**组成的数组。对于数组，键名就是索引值；对于 Set，键名与键值相同。Map 结构的 Iterator 接口，默认就是调用 `entries` 方法。
  - `keys()` 返回一个遍历器对象，用来遍历所有的键名。
  - `values()` 返回一个遍历器对象，用来遍历所有的键值。
- 这三个方法调用后生成的遍历器对象，所遍历的都是计算生成的数据结构。

  ```javascript
  let arr = ['a', 'b', 'c'];
  for (let pair of arr.entries()) {
    console.log(pair);
  }
  // [0, 'a']
  // [1, 'b']
  // [2, 'c']
  ```

## 对象

- 对于普通的对象，`for...of` 结构不能直接使用，会报错，必须部署了 Iterator 接口后才能使用。但是，这样情况下，`for...in` 循环依然可以用来遍历键名。

  ```javascript
  let es6 = {
    edition: 6,
    committee: "TC39",
    standard: "ECMA-262"
  };
  
  for (let e in es6) {
    console.log(e);
  }
  // edition
  // committee
  // standard
  
  for (let e of es6) {
    console.log(e);
  }
  // TypeError: es6[Symbol.iterator] is not a function
  ```

- 上面代码表示，对于普通的对象，`for...in` 循环可以遍历键名，`for...of` 循环会报错。
- 一种解决方法是，使用 `Object.keys` 方法将对象的键名生成一个数组，然后遍历这个数组。

  ```javascript
  for (var key of Object.keys(someObject)) {
    console.log(key + ': ' + someObject[key]);
  }
  ```

- 另一个方法是使用 Generator 函数将对象重新包装一下。

  ```javascript
  function* entries(obj) {
    for (let key of Object.keys(obj)) {
      yield [key, obj[key]];
    }
  }
  
  for (let [key, value] of entries(obj)) {
    console.log(key, '->', value);
  }
  // a -> 1
  // b -> 2
  // c -> 3
  ```

### 遍历顺序 @@@ #

- 首先遍历所有数值键，按照数值升序排列。
- 其次遍历所有字符串键，按照加入时间升序排列。
- 最后遍历所有 Symbol 键，按照加入时间升序排列。

## 注意

- 数组、Set、Map 的 `keys()`，`values()`，`entries()` 部署在原型链上，可以通过实例调用
- 对象的 `keys()`，`values()`，`entries()` 是 Object 的静态方法，通过 Object 构造函数调用，遍历的是**对象自身**的（不含继承的）所有可遍历（enumerable）属性

## 与其他遍历语法的比较 @@@

### 数组遍历方法

- 以数组为例，JavaScript 提供多种遍历语法。最原始的写法就是 `for` 循环。

  ```javascript
  for (var index = 0; index < myArray.length; index++) {
    console.log(myArray[index]);
  }
  ```

- 这种写法比较麻烦，因此数组提供内置的 `forEach` 方法。

  ```javascript
  myArray.forEach(function (value) {
    console.log(value);
  });
  ```

- 这种写法的问题在于，无法中途跳出 `forEach` 循环，**`break` 命令或 `return` 命令都不能奏效**。

### for...in

- `for...in` 循环可以遍历数组的**键名**。

  ```javascript
  for (var index in myArray) {
    console.log(myArray[index]);
  }
  ```

- `for...in` 循环有几个缺点。
  - 数组的键名是数字，但是 `for...in` 循环是以字符串作为键名“0”、“1”、“2”等等。
  - `for...in` 循环不仅遍历数字键名，还会遍历手动添加的其他键，甚至包括原型链上的键。
  - 某些情况下，`for...in` 循环会以任意顺序遍历键名。
  - JavaScript 原有的 `for...in` 循环，只能获得对象的键名，不能直接获取键值。ES6 提供 `for...of` 循环，允许遍历获得键值。
  - **`for...in` 循环读取键名，`for...of` 循环读取键值。如果要通过 `for...of` 循环，获取数组的索引，可以借助数组实例的 `entries` 方法和 `keys` 方法**
- 总之，`for...in` 循环主要是**为遍历对象而设计的**，不适用于遍历数组。 @@@

### 优点

- `for...of` 循环相比上面几种做法，有一些显著的优点。

  ```javascript
  for (let value of myArray) {
    console.log(value);
  }
  ```

- 有着同 `for...in` 一样的简洁语法，但是没有 `for...in` 那些缺点。
- 不同于 `forEach` 方法，它可以与 `break`、`continue` 和 `return` 配合使用。
- 提供了遍历所有数据结构的统一操作接口。

  ```javascript
  for (var n of fibonacci) {
    if (n > 1000)
      break;
    console.log(n);
  }
  ```

- 上面的例子，会输出斐波纳契数列小于等于 1000 的项。如果当前项大于 1000，就会使用 `break` 语句跳出 `for...of` 循环。

# Iterator 接口与 Generator 函数 #

- `Symbol.iterator` 方法的最简单实现，还是使用下一章要介绍的 Generator 函数。

  ```javascript
  let myIterable = {
    [Symbol.iterator]: function* () {
      yield 1;
      yield 2;
      yield 3;
    }
  }
  [...myIterable] // [1, 2, 3]
  
  // 或者采用下面的简洁写法
  
  let obj = {
    * [Symbol.iterator]() {
      yield 'hello';
      yield 'world';
    }
  };
  
  for (let x of obj) {
    console.log(x);
  }
  // "hello"
  // "world"
  ```

- 上面代码中，`Symbol.iterator` 方法几乎不用部署任何代码，只要用 yield 命令给出每一步的返回值即可。

# Decorator

参考：

基本介绍：https://aotu.io/notes/2016/10/24/decorator/index.html

https://juejin.im/post/59f1c484f265da431c6f8940

在 ES6 中增加了对类对象的相关定义和操作（比如 `class` 和 `extends` ），这就使得我们在多个不同类之间共享或者扩展一些方法或者行为的时候，变得并不是那么优雅。这个时候，我们就需要一种更优雅的方法来帮助我们完成这些事情。

## Python 中的装饰器

装饰器是在 python 2.4 里增加的功能，它的主要作用是给一个已有的方法或类扩展一些新的行为，而不是去直接修改它本身。

```python
def decorator(f):
    print "my decorator"
    return f

@decorator
def myfunc():
    print "my function"

myfunc()

# my decorator
# my function
```

这里的 `@decorator` 就是我们说的装饰器。在上面的代码中，我们利用装饰器给我们的目标方法执行前打印出了一行文本，并且并没有对原方法做任何的修改。代码基本等同于

```python
def decorator(f):
    def wrapper():
        print "my decorator"
        return f()
    return wrapper

def myfunc():
    print "my function"

myfunc = decorator(myfuc)
```

体感上是类似于高阶函数，不对接受的函数做修改，而是执行一些操作之后再执行原本的函数

当我们对某个函数应用了装饰以后，其实就改变了被装饰方法的入口引用，使其重新指向了装饰器返回的方法的入口点，从而来实现我们对原函数的扩展、修改等操作。

## Javascript 中的装饰器（第一版）

是的，在 ES6 之前，装饰器对于 JS 来说确实显得不太重要，你只是需要加几层 wrapper 包裹就好了（虽然也会显得不那么优雅）。但是在 ES6 提出之后，当我们需要在多个不同的类之间共享或者扩展一些方法或行为的时候，代码会变得错综复杂，极其不优雅，这也就是装饰器被提出的一个很重要的原因。

话说从装饰器被提出已经有一年多的时间了，同时期的很多其他新的特性已经随着 ES6 的推进而被大家广泛使用，而这货现在却还停留在 stage 2 的阶段，也很少被人提及和应用。那么，装饰器到底是在 Javascript 中是怎样表现的呢？我们下面来一起看一下吧！

> 据说最近已经被打回重修了，现阶段不推荐使用 decorator

### Javascript 中的装饰器

先来看一下装饰器在代码中是长成什么样子吧

```js
@decorator
class Cat {}

class Dog {
    @decorator
    run() {}
}
```

嗯，代码中的 `@decorator` 就是 JS 中的装饰器，看起来基本和 python 中的样子一样，以 `@` 作为标识符，可以作用于类，也可以作用于类的属性。那么接下来，我们就来看看它具体的表现及运行原理吧。

### ES6 中的类

首先我们先来看一下关于 ES6 中的类吧

```js
class Cat {
    say() {
        console.log("meow ~");
    }
}
```

上面这段代码是 ES6 中定义一个类的写法，其实只是一个语法糖，而实际上当我们给一个类添加一个属性的时候，会调用到 `Object.defineProperty` 这个方法，它会接受三个参数：`target` 、`name` 和 `descriptor` ，所以上面的代码实际上在执行时是这样的：

```js
function Cat() {}
Object.defineProperty(Cat.prototype, "say", {
    value: function() { console.log("meow ~"); },
    enumerable: false,
    configurable: true,
    writable: true
});
```

好了，有了上面这段代码以后，我们再来看看装饰器在 JS 中到底是怎么样工作的吧！

### 类装饰器

当一个装饰器作用于类的时候，大概是这个样子的：

```js
function isAnimal(target) {
    target.isAnimal = true;
   return target;
}

@isAnimal
class Cat {
    ...
}

console.log(Cat.isAnimal);    // true
```

是不是很像之前我们在 python 中看到的装饰器？

所以这段代码实际上基本等同于：

```js
Cat = isAnimal(function Cat() { ... });
```

相当于是给 Cat 添加了一个静态属性

### 类方法装饰器

比如有的时候，我们希望把我们的部分属性置成只读，以避免别人对其进行修改，如果使用装饰器的话，我们可以这样来做：

```js
function readonly(target, name, descriptor) {
    discriptor.writable = false;
    return discriptor;
}

class Cat {
    @readonly
    say() {
        console.log("meow ~");
    }
}

var kitty = new Cat();

kitty.say = function() {
    console.log("woof !");
}

kitty.say()    // meow ~
```

我们通过上面的代码把 `say` 方法设置成了只读，所以在我们后面再次对它赋值的时候就不会生效，调用的还是之前的方法。

在上面的代码中我们可以看到，我们在定义装饰器的时候，**参数是有三个**，`target`、`name`、`descriptor` 。

没错，就是我们上文提到过的关于类的定义那一块儿的 `Object.defineProperty` 的参数，所以其实装饰器在作用于属性的时候，实际上是通过 `Object.defineProperty` 来进行扩展和封装的。

所以在上面的这段代码中，装饰器实际的作用形式是这样的：

```js
let descriptor = {
    value: function() {
        console.log("meow ~");
    },
    enumerable: false,
    configurable: true,
    writable: true
};

descriptor = readonly(Cat.prototype, "say", descriptor) || descriptor;

Object.defineProperty(Cat.prototype, "say", descriptor);
```

嗯嗯，是不是这样看就清楚很多了呢？这里也是 JS 里装饰器作用于类和作用于类的属性的不同的地方。

我们可以看出，当装饰器作用于类本身的时候，我们操作的对象也是这个类本身，而当装饰器作用于类的某个具体的属性的时候，我们操作的对象既不是类本身，也不是类的属性，而是它的描述符（descriptor），而描述符里记录着我们对这个属性的全部信息，所以，我们可以对它自由的进行扩展和封装，最后达到的目的呢，就和之前说过的装饰器的作用是一样的。

当然，如果你喜欢的话，也可以直接在 `target` 上进行扩展和封装，比如

```js
function fast(target, name, descriptor) {
    target.speed = 20;

    let run = descriptor.value;
    descriptor.value = function() {
        run();
        console.log(`speed ${this.speed}`);
    }

    return descriptor;
}

class Rabbit {
    @fast
    run() {
        console.log("running~");
    }
}

var bunny = new Rabbit();

bunny.run();
// running~
// speed 20

console.log(bunny.speed);   // 20
```

关键点：作用于类的原型，透过 defineproperty

### 类属性装饰器

类似于方法装饰器，但它的入参少了属性描述符。原因则是目前没有方法在定义原型对象成员同时去描述一个实例的属性（创建描述符）。

```ts
function addProps(): PropertyDecorator {
  return (target, propertyKey) => {
    console.log(target);
    console.log(propertyKey);
  };
}

class A {
  @addProps()
  originProps: any;
}
```

### 访问操作符装饰器

与属性方法类似，就不详述了。

```js
class Person {
  @nonenumerable
  get kidCount() { return this.children.length; }
}

function nonenumerable(target, name, descriptor) {
  descriptor.enumerable = false;
  return descriptor;
}
```

### 参数装饰器

参数装饰器的入参首要两位与属性装饰器相同，第三个参数则是参数在当前函数参数中的**索引**。

```ts
function paramDeco(params?: any): ParameterDecorator {
  return (target, propertyKey, index) => {
    console.log(target);
    console.log(propertyKey);
    console.log(index);
    target.constructor.prototype.fromParamDeco = '呀呼！';
  };
}

class B {
  someMethod(@paramDeco() param1: any, @paramDeco() param2: any) {
    console.log(`${param1}  ${param2}`);
  }
}

new B().someMethod('啊哈', '林不渡！');
// @ts-ignore
console.log(B.prototype.fromParamDeco);
```

## TS 中的装饰器

### 装饰器与注解

装饰器与注解实际上也有一定区别，由于并没有学过 Java，这里就不与 Java 中的注解进行比较了。而只是说我所认为的二者差异：

- **注解** 应该如同字面意义一样， 只是为某个被注解的对象提供元数据（`metadata`）的注入，本质上不能起到任何修改行为的操作，需要 `scanner` 去进行扫描获得元数据并基于其去执行操作，注解的元数据才有实际意义。
- **装饰器** 没法添加元数据，只能基于已经由注解注入的元数据来执行操作，来对类、方法、属性、参数进行某种特定的操作。

但实际上，TS 中的装饰器通常是同时包含了这两种效能的，它可能消费元数据的同时也提供了元数据供别的装饰器消费。

### 装饰器顺序

> 装饰器求值顺序来自于 TypeScript 官方文档一节中的装饰器说明。

类中不同声明上的装饰器将按以下规定的顺序应用：

1. 参数装饰器，然后依次是方法装饰器 *，*访问符装饰器*，或 * 属性装饰器应用到每个实例成员。
2. 参数装饰器，然后依次是方法装饰器，访问符装饰器，或属性装饰器应用到每个静态成员。
3. 参数装饰器应用到构造函数。
4. 类装饰器应用到类。

注意这个顺序，后面我们能够实现元数据读写，也正是因为这个顺序。

当存在多个装饰器来装饰同一个声明时，则会有以下的顺序：

首先，由上至下依次对装饰器表达式求值，得到返回的真实函数（如果有的话）

而后，求值的结果会由下至上依次调用

（有点类似洋葱模型）

```ts
function foo() {
    console.log("foo in");
    return function (target, propertyKey: string, descriptor: PropertyDescriptor) {
        console.log("foo out");
    }
}

function bar() {
    console.log("bar in");
    return function (target, propertyKey: string, descriptor: PropertyDescriptor) {
        console.log("bar out");
    }
}

class A {
    @foo()
    @bar()
    method() {}
}

// foo in
// bar in
// bar out
// foo out

```

## Reflect MetaData

### 基本元数据读写

Reflect Metadata 是属于 ES7 的一个提案，其主要作用是在声明时去读写元数据。TS 早在 1.5+ 版本就已经支持反射元数据的使用，目前想要使用，我们还需要安装 reflect-metadata 与在 tsconfig.json 中启用 emitDecoratorMetadata 选项。

你可以将元数据理解为用于描述数据的数据，如某个对象的键、键值、类型等等就可称之为该对象的元数据。我们先不用太在意元数据定义的位置，先做一个简单的阐述：

为类或类属性添加了元数据后，构造函数的原型（或是构造函数，根据静态成员还是实例成员决定）会具有 [[Metadata\|Metadata]] 属性，该属性内部包含一个 Map 结构，键为属性键，值为元数据键值对。

reflect-metadata 提供了对 Reflect 对象的扩展，在引入后，我们可以直接从 Reflect 对象上获取扩展方法。

```ts
import 'reflect-metadata';

@Reflect.metadata('className', 'D')
class D {
  @Reflect.metadata('methodName', 'hello')
  public hello(): string {
    return 'hello world';
  }
}

const d = new D();
console.log(Reflect.getMetadata('className', D));
console.log(Reflect.getMetadata('methodName', d));
```

可以看到，我们给类 D 与 D 内部的方法 hello 都注入了元数据，并通过 getMetadata(metadataKey, target) 这个方式取出了存放的元数据。

我们注意到，想要取出注入到类 D 中的元数据，target 需要为类 D。而想要取出注入到类的方法上的元数据，target 需要为实例 d。原因其实我们实际上在上面的装饰器执行顺序提到了，这是由于注入在方法、属性、参数上的元数据实际上是被添加在了实例对应的位置上，因此需要实例化才能取出。

### TS 内置元数据

Reflect 允许程序去检视自身，基于这个效果，我们可以在装饰器运行时去检查其类型相关信息，如目标类型、目标参数的类型以及方法返回值的类型，这需要借助 TS 内置的元数据 metadataKey 来实现，以一个检查入参的例子为例：

```ts
import 'reflect-metadata';

class Point {
  x: number;
  y: number;
}

class Line {
  private _p0: Point;
  private _p1: Point;

  @validate
  set p0(value: Point) {
    this._p0 = value;
  }
  get p0() {
    return this._p0;
  }

  @validate
  set p1(value: Point) {
    this._p1 = value;
  }
  get p1() {
    return this._p1;
  }
}

function validate<T>(
  target: any,
  propertyKey: string,
  descriptor: TypedPropertyDescriptor<T>
) {
  let set = descriptor.set!;
  descriptor.set = function (value: T) {
    let type = Reflect.getMetadata('design:type', target, propertyKey);
    if (!(value instanceof type)) {
      throw new TypeError('Invalid type.');
    }
    set(value);
  };
}

```

这个例子来自于 TypeScript 官方文档，但实际上不能正常执行。因为在经过装饰器处理后，set 方法的 this 将会丢失。但我猜想官方的用意只是展示 design:type 的用法。

在这个例子中，我们基于 Reflect.getMetadata('design:type', target, propertyKey); 获取到了装饰器对应声明的属性类型，并确保在 setter 被调用时检查值类型。

这里的 design:type 即是 TS 的内置元数据，你可以理解为 TS 在编译前还手动执行了@Reflect.metadata("design:type", Point)。TS 还内置了**design:paramtypes（获取目标参数类型）与 design:returntype（获取方法返回值类型）**这两种元数据字段来提供帮助。但有一点需要注意，即使对于基本类型，这些元数据也返回对应的包装类型，如 number -> [Function: Number]

## 实例：解析路由装饰器

```ts
@controller('/user')
export class UserController {
  @get('/all')
  async getAllUser(): Promise<void> {
    // ...
  }

  @post('/update')
  async updateUser(): Promise<void> {
    // ...
  }
}
```

首先思考 `controller` 和 `get`/`post` 装饰器，我们需要使用这几个装饰器注入哪些信息：

- **路径**
- **方法（方法装饰器）**

### @controller

首先是对于整个类，我们需要将 `path: "/user"` 这个数据注入：

```ts
// 工具常量枚举
export enum METADATA_MAP {
  METHOD = 'method',
  PATH = 'path',
  GET = 'get',
  POST = 'post',
  MIDDLEWARE = 'middleware',
}

const { METHOD, PATH, GET, POST } = METADATA_MAP;

export const controller = (path: string): ClassDecorator => {
  return (target) => {
    Reflect.defineMetadata(PATH, path, target);
  };
};

```

### @get

而后是方法装饰器，我们选择一个高阶函数（柯里化）去吐出各个方法的装饰器，而不是为每种方法定义一个。

```ts
// 方法装饰器 保存方法与路径
export const methodDecoCreator = (method: string) => {
  return (path: string): MethodDecorator => {
    return (_target, _key, descriptor) => {
      Reflect.defineMetadata(METHOD, method, descriptor.value!);
      Reflect.defineMetadata(PATH, path, descriptor.value!);
    };
  };
};

// 首先确定方法，而后在使用时才去确定路径
const get = methodDecoCreator(GET);
const post = methodDecoCreator(POST);

```

### 生成路由表

接下来我们要做的事情就很简单了：

- 拿到注入在类上元数据的根路径
- 拿到每个方法上元数据的方法、路径
- 拼接，生成路由表

```ts
const routeGenerator = (ins: Object) => {
  const prototype = Object.getPrototypeOf(ins);

  const rootPath = Reflect.getMetadata(PATH, prototype['constructor']);

  const methods = Object.getOwnPropertyNames(prototype).filter(
    (item) => item !== 'constructor'
  );

  const routeGroup = methods.map((methodName) => {
    const methodBody = prototype[methodName];

    const path = Reflect.getMetadata(PATH, methodBody);
    const method = Reflect.getMetadata(METHOD, methodBody);
    return {
      path: `${rootPath}${path}`,
      method,
      methodName,
      methodBody,
    };
  });
  console.log(routeGroup);
  return routeGroup;
};
```

## DI 工具库

- [TypeDI](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Ftypestack%2Ftypedi)，TypeStack 出品
- [TSYringe](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fmicrosoft%2Ftsyringe)，微软出品
- [Injection](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fmidwayjs%2Finjection)，MidwayJS 团队出品，是 MidwayJS 底层 IoC 的能力支持
- inversify

实际上，一个依赖注入工具库必定会提供的就是 **从容器中获取实例** 与 **注入对象到容器中**的两个方法，如上面的 `provide` 与 `inject`，TypeDI 的 `Service` 与 `Inject`。

## 装饰器（第三版）

前两版是类似于 python decorator 的语义，第三版是静态语义，类似于弱化的宏

# Latest 语法

## 可选链操作符

### 概述

**可选链**操作符 ( **`?.`** ) 允许读取位于连接对象链深处的属性的值，而不必明确验证链中的每个引用是否有效。`?.` 操作符的功能类似于 `.` 链式操作符，不同之处在于，在引用为空 ([nullish](https://developer.mozilla.org/zh-CN/docs/Glossary/Nullish) ) ([`null`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/null) 或者 [`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined)) 的情况下不会引起错误，该表达式短路返回值是 `undefined`。与函数调用一起使用时，如果给定的函数不存在，则返回 `undefined`。

**语法**

```js
obj?.prop
obj?.[expr]
arr?.[index]
func?.(args)
```

**实例**

```js
let nestedProp = obj.first?.second;
```

通过使用 `?.` 操作符取代 `.` 操作符，JavaScript 会在尝试访问 `obj.first.second` 之前，先隐式地检查并确定 `obj.first` 既不是 `null` 也不是 `undefined`。如果 `obj.first` 是 `null` 或者 `undefined`，表达式将会短路计算直接返回 `undefined`。

这等价于以下表达式，但实际上没有创建临时变量：

```js
let temp = obj.first;
let nestedProp = ((temp === null || temp === undefined) ? undefined : temp.second);
```

needBatcherByDataChanges

```js
targetNode = dataChanges[i]?.option?.target?.node;

var targetNode = (_c = (_b = (_a = dataChanges[i]) === null || _a === void 0 ? void 0 : _a.option) === null || _b === void 0 ? void 0 : _b.target) === null || _c === void 0 ? void 0 : _c.node;
```

### 可选链与函数调用

当尝试调用一个可能不存在的方法时也可以使用可选链。这将是很有帮助的，比如，当使用一个 API 的方法可能不可用时，要么因为实现的版本问题要么因为当前用户的设备不支持该功能。

函数调用时如果被调用的方法不存在，使用可选链可以使表达式自动返回 `undefined` 而不是抛出一个异常。

```js
let result = someInterface.customMethod?.();
```

**注意:** 如果存在一个属性名且不是函数, 使用 `?.` 仍然会产生一个 [`TypeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypeError) 异常 (`x.y is not a function`)

**注意:** 如果 `someInterface` 自身是 `null` 或者 `undefined` ，异常 [`TypeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypeError) 仍会被抛出 `someInterface is null` 如果你希望允许 `someInterface` 也为 `null` 或者 `undefined` ，那么你需要像这样写 `someInterface?.customMethod?.()`

### 处理可选的回调函数或者事件处理器

如果使用 [解构赋值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) 来解构的一个对象的回调函数或 fetch 方法，你可能得到不能当做函数直接调用的不存在的值，除非你已经校验了他们的存在性。使用 `?.` 的你可以忽略这些额外的校验：

```js
//  ES2019的写法
function doSomething(onContent, onError) {
  try {
    // ... do something with the data
  }
  catch (err) {
    if (onError) { // 校验onError是否真的存在
      onError(err.message);
    }
  }
}
```

```js
// 使用可选链进行函数调用
function doSomething(onContent, onError) {
  try {
   // ... do something with the data
  }
  catch (err) {
    onError?.(err.message); // 如果onError是undefined也不会有异常
  }
}
```

### 可选链不能用于赋值

```js
let object = {};
object?.property = 1; // Uncaught SyntaxError: Invalid left-hand side in assignment
```

## 空值合并运算符

**空值合并操作符**（**`??`**）是一个逻辑操作符，当左侧的操作数为 [`null`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/null) 或者 [`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined) 时，返回其右侧操作数，否则返回左侧操作数。

与 [逻辑或操作符（`||`）](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators#logical_or_2) 不同，逻辑或操作符会在左侧操作数为 [假值](https://developer.mozilla.org/zh-CN/docs/Glossary/Falsy) 时返回右侧操作数。也就是说，如果使用 `||` 来为某些变量设置默认值，可能会遇到意料之外的行为。比如为假值（例如，`''` 或 `0`）时。见下面的例子。

https://cloud.tencent.com/developer/article/1600583
