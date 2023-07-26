---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/es/es-basic/"}
---


# 语法基础

## 变量

JS 对象区分 **大小写**

- 十六进制数的 A~F 既可以是大写也可以是小写
- 科学计数法的 e 既可以是大写也可以是小写

**标识符 (identifier)**，就是指变量、函数、属性的名字，或者函数的参数。标识符可以是按照下列格式规则组合起来的一或多个字符：

- 第一个字符必须是一个字母、下划线（_）或一个美元符号（$）
- 其他字符可以是字母、下划线、美元符号或数字

### 全局对象与全局变量

global 最根本的作用是作为全局变量的宿主。按照 ECMAScript 的定义，满足以下条 件的变量是全局变量：

+ 在最外层定义的变量；
+ 全局对象的属性；
+ 隐式定义的变量（未定义直接赋值的变量）。

当你定义一个全局变量时，这个变量同时也会成为全局对象的属性，反之亦然。需要注 意的是，在 Node.js 中你不可能在最外层定义变量，因为所有用户代码都是属于当前模块的， 而模块本身不是最外层上下文。

**注意：** 最好不要使用 var 定义变量以避免引入全局变量，因为全局变量会污染命名空间，提高代码的耦合风险。

全局变量和 window 上定义的属性的区别：

+ 全局变量不能通过 delete 删除，而 window 属性上定义的变量可以通过 delete 删除
+ 尝试访问未声明的变量会报错，..is not defined，但是通过查询 window 查询，可以知道某个可能未声明的变量是否存在，不会报错，只会显示 undefined
+ let 和 const 定义的变量不会成为全局对象的属性，因为他们在块级作用域生效，而 ES6 全部基于模块

## 注释

ECMAScript 使用 C 风格的注释，包括单行注释和块级注释。单行注释以两个斜杠开头，块级注释以一个斜杠和一个星号（/\*）开头，以一个星号和一个斜杠（*/）结尾，如下所示：

```js
// 单行注释 
/* 
* 这是一个多行 
* （块级）注释 
*/ 
```

虽然上面注释中的第二和第三行都以一个星号开头，但这不是必需的。之所以添加那两个星号，纯

粹是为了提高注释的可读性（这种格式在企业级应用中用得比较多）

## 关键字

带 * 号上标的是第 5 版新增的关键字

```
break         do           instanceof        typeof 
case          else         new               var 
catch         finally      return            void 
continue      for          switch            while 
debugger*
function     this              with 
default       if           throw 
delete        in           try 
```

第 5 版把在非严格模式下运行时的 **保留字** 缩减为下列这些：

```
class          enum          extends         super 
const          export        import 
```

在严格模式下，第 5 版还对以下保留字施加了限制：

```
implements     package       public 
interface      private       static 
let            protected     yiel
d 
```

## 原始数据类型 Primitive

String: 任意字符串

Number: 任意的数字

boolean: true/false

undefined: undefined

null: null

symbol

### Undefined 与 Null 的区别

undefined 代表 **定义未赋值**

> 注意，用未定义的变量是会报错的，未定义的属性是 undefined

`nulll` 定义并赋值了, 只是值为 `null`

什么时候给变量赋值为 null 呢?

- 初始赋值, 表明将要赋值为对象
- 结束前, 让对象成为垃圾对象 (被浏览器的垃圾回收器回收)

### 变量类型与数据类型

**数据** 的类型

- 基本类型（六种基本类型）
- 对象类型

**变量** 的类型 (变量内存值的类型)

- 基本类型: 保存就是基本类型的数据
- 引用类型: 保存的是地址值（指向对象类型数据）

`JS` 是弱类型语言，全都是 var 声明变量，变量是没有类型的，判断的其实是变量保存的值的类型

## 引用类型

引用类型（对象类型）

- Object: 任意对象
- Function: 一种特别的对象 (可以执行)
- Array: 一种特别的对象 (数值下标, 内部数据是有序的)

## 原始类型和引用类型的区别

### 数据

存储在内存中代表特定信息的 ' 东东 ', 本质上是 0101...

数据的特点: 可传递, 可运算

一切皆数据（基本数据、对象数据）

内存中所有 **操作的目标**: **数据**

- 算术运算
- 逻辑运算
- 赋值
- 运行函数

### 内存分配 (memory allocation)

**内存条** 通电后产生的可储存数据的空间 (临时的)

内存产生和死亡: 内存条 (电路版)==>通电==>产生内存空间==>存储数据==>处理数据==>断电==>内存空间和数据都消失

一块小内存的 2 个数据

- 内部存储的数据
- 地址值

内存分类

- 栈: 全局变量/局部变量 （函数名本质上是一个变量名）
- 堆: 对象（包括对象的属性、方法）

![image-20220419153813738](/img/user/programming/font-end/primitive/es/es-basic/image-20220419153813738.png)

### 变量

可变化的量, 由变量名和变量值组成

每个变量都对应的一块小内存, 变量名用来查找对应的内存, 变量值就是内存中保存的数据

### 内存、数据、变量三者之间的关系

内存用来存储数据的临时空间

变量是内存的标识

数据赋值给变量使得数据有了实际意义，仅仅是内存中存储着的“18”，可能是年龄，年份，时间等等，只有赋值给了变量才变得有意义

### 关于赋值与内存的问题

`var a = xxx`, `a` 内存中到底保存的是什么?

- `xxx` 是 **基本数据,** 保存的就是这个数据
- `xxx` 是 **对象**, 保存的是对象的地址值
- `xxx` 是一个 **变量**, 保存的 `xxx` 的内存内容 (可能是基本数据, 也可能是地址值)

对象的赋值只能有一次，就是创建的时候，**其他变量想要获取同一个对象的地址值**，只能赋值变量。

创建一个内容一模一样的对象，地址值也是不同的

### 关于引用变量赋值的问题

```js
var obj1 = {};
var obj2 = obj1; 
//这一步的操作是：把obj1保存的内容（{}的地址值）赋值给obj2，而不是把obj1的地址值赋值，因为所有的变量都是有自己的地址值的（用于在内存中查找该数据）
```

- 2 个引用变量指向同一个对象, 通过一个变量修改对象内部数据, 另一个变量看到的是修改之后的数据
- 2 个引用变量指向同一个对象, 让其中一个引用变量指向另一个对象, 另一引用变量依然指向前一个对象

### 形参赋值

```js
var a = {age:12}

function fn (obj){
  obj = {age:15}
}
fn(a)
console.log(a.age) //12
```

实参赋值给形参，obj 保存了 a 的内存内容，age12 的对象的内存地址值，然后 obj 获得了 a 的保存内容，然后又重新获取了一个地址值，age15，然后函数运行完毕，fn 的执行上下文出栈，age15 成为垃圾对象

函数操作的永远是形参，只是形参保存了传入的实参的值

```js
var b = 2 
function fn2 (animal){
  animal = 3
}
fn(b)
console.log(b) //2
```

### 关于数据传递的问题

在 `js` 调用函数时传递变量参数时, 是值传递还是引用传递

理解 1: 都是值 (基本/地址值) 传递

理解 2: 可能是值传递, 也可能是引用传递 (地址值)

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

### JS 引擎如何管理内存

内存生命周期

- 分配小内存空间, 得到它的使用权
- 存储数据, 可以反复进行操作
- 释放小内存空间
- 释放内存

局部变量: 函数执行完自动释放

对象: 成为垃圾对象==>垃圾回收器回收 //即使是函数内部新建的对象

所以要尽量少全局变量

# 类型转换

## 转换为 Boolean 类型

Null → false

Undefined → false

Number：

- 0（包括 +0 和 -0），NaN→false
- 其他数字→true
- 负数也是 true

字符串：

- 空字符串→false
- 其余都为 true，即使只含一个空格

```js
console.log(
  Boolean(null), // false
  Boolean(undefined), // false
  Boolean(0), // false
  Boolean(+0), // false
  Boolean(-0), // false
  Boolean(NaN), // false
  Boolean(1), // true
  Boolean(-1), // true
  Boolean(''), // false
  Boolean(' '), // true
  Boolean('123'), // true
)
```

Object ：true

```js
console.log(!! []) // true [].toBoolean
console.log([] == false) // [].valueOf().toString 👉 '' // toPrimitive
```

参考：<https://www.h5jun.com/post/why-false-why-true.html>

这也是为什么我们不能用 `if(!array)` 来判断空数组而要用 `if(array.length === 0)` 来判断空数组的原因。

运用！非

- 对非 Boolean，会转换为布尔值再取反
- 可以利用这个特点转换为布尔值
- 类型转换调用的是 Boolean() 函数

```js
b = !!b

b = !!! typeof undefined
// typeof返回的是字符串，无法通过!判断true or false只能只用===
```

## 转换为 String 类型

### toString()

Null： 报错

Undefined：报错

Boolean：'true' 'false'

Number：报错，原始数组没有 toString() 方法

字符串：字符串本身

Symbol：输出 Symbol 字符串

```js
console.log(
  // null.toString(), // 抛错
  // undefined.toString(), // 抛错
  true.toString(), // true
  false.toString(), // false
  (1).toString()， // 1
  // 1.toString(), // 报错
  'hello world'.toString(), // 'hello world'
  Symbol(100).toString() // Symbol(100)
)
```

> `()` is grouping operator, which returns the value of the expression inside it. Here in your case, it's `1`, a primitive number. So it can be boxed to a `Number` object and call its method `toString`.
>
> However for `1.toString()`, the JS engine cannot determine what does `.` mean - a dot operator (for object methods), or a float number point?
>
> To solve this confusion, without the grouping operator, you have two approaches:
>
> - `1 .toString()` // a whitespace, works similar as grouping operator
> - `1..toString()` // two dots, in which the second is treated as dot operator

对象：

1. 包装类输出原始值的字符串形式
2. 如果是数组，那么将数组展开输出。空数组，返回 `''`
3. 如果是 Date, 返回日期的文字表示法 @@@
4. 如果是函数，本身
5. 如果是对象，返回 `[object Object]`

```js
let fn = function() {console.log('稳住，我们能赢！')}
console.log(
  Boolean(true).toString(), // true
  Number(1).toString(), // 1
  [].toString(), // ''
  [1, 2, 3, undefined, 5, 6].toString(), // 1,2,3,,5,6
  {a:1}.toString(), // [object Object]
  new Date().toString(), // Sun Sep 22 2019 18:59:24 GMT+0800 (GMT+08:00)
  fn.toString(),// function () {console.log('稳住，我们能赢！')}
)
```

### String()

`String()` 的转换规则与 `toString()` 基本一致，最大的一点不同在于 `null` 和 `undefined`，使用 String 进行转换，null 和 undefined 对应的是字符串 `'null'` 和 `'undefined'`

```js
console.log(
  String(null), // null
  String(undefined), // undefined
)
```

方式一：

* 调用被转换数据类型的 toString() 方法
* 该方法不会影响到原变量，它会将转换的结果返回，基本数据类型的 ``toString()`` 全都被重写了
* 注意：null 和 undefined 这两个值没有 toString() 方法，如果使用这种方法会报错

方式二：

* 调用 String() 函数，并将被转换的函数作为参数传递给函数
* 注意：String() 是大写的
* 用 String() 函数做强制类型转换时，对于 Number 和 Boolean 其实是调用的 toString(); 对于 Null 和 Undefined 则是直接转换为字符串

方式三：(最常用)

* 用 + 号连接含有一个以上字符串的多个变量
* var c = 123;
* c = c + “” //加一个空字符串
* console.log(“c = ”+ c); //表述清晰，经常使用

## 转换为 Number 类型

### Number()

Null：0

Undefined：NaN @@@

  > 注意是 NaN，而不是 0
  >
  > undefind == 0 false

Boolean：true 和 false 分别转换为 1 和 0

Number：返回自身

```js
  console.log(
    Number(null), // 0
    Number(undefined), // NaN
    Number(true), // 1
    Number(false), // 0
    Number(1), // 1
    Number(+0), // 0
    Number(-0), // -0 注意与parseInt的区别
    Number('+0'), // 0
    Number('-0'), // -0
    Number('1'), // 1
  )
```

#### 字符串

  * 如果字符串中只包含数字 (或者 `0b`、`0o`、`0x` 前缀的字符串数值转为十进制，允许包含正负号？？？)，则将其转换为十进制
  * 如果字符串中包含有效的浮点格式，将其转换为浮点数值
  * 空字符串转换为 0，**字符串仅含空格转换为 0**
  * 如不是以上格式的字符串，均返回 `NaN`

```js
  console.log(
    Number(+0), // 0
    Number(-0), // -0 注意与parseInt的区别
    Number('+0'), // 0
    Number('-0'), // -0
    Number('1'), // 1
    Number('0b1111'),  // 15
    Number('0o11'),  // 9
    Number('-0x11'),  // NaN 注意与parseInt的区别
    Number('0x-11'),  // NaN
    Number('987.654'), // 987.654
    Number('987.654.321'), // NaN
    Number('Number'), // NaN
  )
```

#### Symbol

抛出错误

#### 对象

  1. 则调用对象的 `valueOf()` 方法，然后依据前面的规则转换返回的值。
  2. 如果转换的结果是 `NaN` ，则调用对象的 `toString()` 方法，再次依照前面的规则转换返回的字符串值。

部分内置对象调用默认的 `valueOf` 的行为:

  | 对象     | 返回值                                                       |
  | -------- | ------------------------------------------------------------ |
  | Number   | 数字值（原始类型）                                           |
  | Boolean  | 布尔值（原始类型）                                           |
  | String   | 字符串值（原始类型）                                         |
  | Object   | 对象本身（对象类型）                                         |
  | Array    | 数组本身（对象类型）                                         |
  | Function | 函数本身（对象类型）                                         |
  | Date     | 从 UTC 1970 年 1 月 1 日午夜开始计算，到所封装的日期所经过的毫秒数 |

部分内置对象调用默认的 `toString` 的行为:

  | 对象     | 返回值                                                       |
  | -------- | ------------------------------------------------------------ |
  | Number   | 返回数值的字符串表示。还可返回以指定 **进制** 表示的字符串     |
  | Boolean  | 是 true，则返回”true”。否则返回”false”。                      |
  | String   | 返回 String 对象的值。                                       |
  | Object   | 返回”[object ObjectName]”，其中 ObjectName 是对象类型的名称。 |
  | Array    | 将 Array 的每个元素转换为字符串，两个元素之间用英文逗号作为分隔符进行拼接 |
  | Function | 返回如下格式的字符串，“function functionName() { [native code] }” |
  | Date     | 返回如下格式的字符串，"Sun Sep 22 2019 18:59:24 GMT+0800 (GMT+08:00)" |

```js
  console.log(
    // Number(Symbol('123')), TypeError: Cannot convert a Symbol value to a number
    Number([1,2,3]), // NaN
    Number(Boolean(true)), // 1
    Number(new Date()),  // 时间戳
    Number(function(){}), // NaN
    Number({}), // NaN
    Number({a:1}), // NaN
    Number({a:'0o11'}), // NaN
    Number(String('0o11')), // 9
  )
```

#### 三个 0

  ```js
  console.log(
    Number(''),  // Boolean('') false
    Number('  '), // Boolean('   ') true
    Number([]) // valueOf()→NaN toString()→''→0
  )
  ```

### parseInt()

Null、 Undefined、Boolean 或者是一个对象类型：返回 NaN

如果第一个参数是数组： 1. 去数组的第一个元素，按照上面的规则进行解析

  ```js
  console.log(
    parseInt(null), // NaN
    parseInt(undefined), // NaN
    parseInt(true), // NaN
    parseInt(false), // NaN
    parseInt([1,2,3]), // 1
    parseInt(Boolean(true)), // NaN
    parseInt(new Date()),  // NaN
    parseInt(function(){}), // NaN
    parseInt({}), // NaN
    parseInt({a:1}), // NaN
    parseInt({a:'0o11'}), // NaN
  )
  ```

Number 类型:

  1. 数字如果是 0 开头，则将其当作八进制来解析 (如果是一个八进制数)；
  2. 如果以 0x 开头，则将其当作十六进制来解析

字符串类型:

  1. 忽略字符串前面的空格，直至找到第一个非空字符，如果是空字符串，返回 NaN
  2. 如果第一个字符不是数字符号或者正负号，返回 NaN
  3. 如果第一个字符是数字/正负号，则继续解析直至字符串解析完毕或者遇到一个非数字符号为止

Symbol 类型： 抛出错误

  ```js
  console.log(
    parseInt(+0), // 0
    parseInt(-0), // 0 与Number不同
    parseInt('+0'), // 0
    parseInt('-0'), // -0
    parseInt(1), // 1
    parseInt('1'), // 1
    parseInt('0b1111'),  // 0
    parseInt('0o11'),  // 0
    parseInt('-0x11'),  // -17 与Number不同
    parseInt('0x-11'),  // NaN
    parseInt('987.654'), // 987
    parseInt('987.654.321'), // 987
    parseInt('parseInt'), // NaN
    // parseInt(Symbol('123')), TypeError: Cannot convert a Symbol value to a parseInt
  )
  ```

### parseFloat()

和 parseInt 类似

### 算术运算符

用 + - * / 等运算符的特性，把非 Number 转换成 Number。调用的是 Number()

当值为 String 的时候只能用 - * /

```javascript
  d = “123”
  d = d – 0
  d = d * 1
  d = d / 1
  typeof d // Number
```

当值为非 Number 非 String 可以用 + - * /

```javascript
  d = true
  d = true + 0
  typeof d // Number
```

## 隐式转换

### 条件判断

&& 、|| 、 ! 、 if/while 的条件判断

需要将数据转换成 Boolean 类型，转换规则同 Boolean 强制类型转换

### 算术运算符

`+` 号操作符，不仅可以用作数字相加，还可以用作字符串拼接。

仅当 `+` 号两边都是数字时，进行的是加法运算。如果两边都是字符串，直接拼接，无需进行隐式类型转换。

除了上面的情况外，如果操作数是对象、数值或者布尔值，则调用 toString() 方法取得字符串值 (toString 转换规则)。对于 undefined 和 null，分别调用 String() 显式转换为字符串，然后再进行拼接。

```js
console.log({}+10); //[object Object]10
console.log([1, 2, 3, undefined, 5, 6] + 10);//1,2,3,,5,610
```

`-`、`*`、`/` 操作符针对的是运算，如果操作值之一不是数值，则被隐式调用 Number() 函数进行转换。如果其中有一个输转换为了 NaN, 则运算结果为 NaN.

### 关系操作符

==、>、< 、<=、>=

遵循以下规则：

1. 如果两个操作值都是数值，则进行数值比较
2. 如果两个操作值都是字符串，则比较字符串对应的 **字符编码值**
3. 如果有一方是 Symbol 类型，抛出错误
4. 除了上述情况之外，都进行 Number() 进行类型转换，然后再进行比较。

**注：** NaN 是非常特殊的值，它不和任何类型的值相等，包括它自己，同时它与任何类型的值比较大小时都返回 false。 @@@

[https://yuchengkai.cn/docs/frontend/#%E5%9B%9B%E5%88%99%E8%BF%90%E7%AE%97%E7%AC%A6](https://yuchengkai.cn/docs/frontend/#四则运算符)

<https://juejin.im/post/5cbd1e33e51d45789161d053#heading-6>

## 包装类

在 JS 中为我们提供了三个包装类：String() Boolean() Number()

这三个实际上都是构造函数

通过这三个包装类可以创建基本数据类型的 **对象** @@@

```js
var num = new Number(2);
var str = new String("hello");
var bool = new Boolean(true);
//作为构造函数使用，运用到转换类型时是普通的函数
```

但是在实际应用中千万不要这么干。

```js
var num = new Number(3);
var num2 = new Number(3);
num == num2; //flase ，对象比较内存地址
var num3 = 3;
num == num3l // true ,自动转换了类型，Object也可以转换
```

当我们去操作一个基本数据类型的属性和方法时，解析器会临时将其转换为对应的包装类，然后再去操作属性和方法，操作完成以后再将这个临时对象进行销毁。

```js
var s=123
s=s.toString()
//不会报错，因为toString在Object的原型上，转换为Number()包装类之后，可以获取到
```

### 装箱

```js
var s1 = "some text";
var s2 = s1.substring(2);
```

如上所视，变量 s1 是一个基本类型值，它不是对象，所以它不应该有方法。但是 js 内部为我们完成了一系列处理（即我们称之为装箱），使得它能够调用方法,实现的机制如下：

（1）创建 String 类型的一个实例；

（2）在实例上调用指定的方法；

（3）销毁这个实例；

这个过程也可以用代码来展现：

```js
var s1  = new String("some text");
var s2 = s1.substring(2);
s1 = null;
```

我在很多地方都看到过说装箱操作会泄露内存，可是这里明明是销毁了实例的，哎，我还是相信《javascript 高级程序设计》吧，认为它不会泄露，如果你有见解，请留言！

### 拆箱

将引用类型对象转换为对应的值类型对象，它是通过引用类型的 valueOf() 或者 toString() 方法来实现的。如果是自定义的对象，你也可以自定义它的 valueOf()/tostring() 方法，实现对这个对象的拆箱。

```js
var objNum = new Number(123);  
var objStr =new String("123");  

console.log( typeof objNum ); //object
console.log( typeof objStr ); //object

console.log( typeof objNum.valueOf() ); //number
console.log( typeof objStr.valueOf() ); //string
console.log( typeof objNum.toString() ); // string 
console.log( typeof objStr.toString() ); // string
```

### 操作符不会装箱

```js
const str = new String('123')

console.log('123' instanceof String) // false
console.log('123' instanceof Object) // false
console.log(str instanceof String) // true
console.log(Object.prototype.toString.call('123')) // string
console.log(Object.prototype.toString.call(str)) // string
```

typeof 定了返回值，而 instanceof 没有

# 运算符

运算符也叫操作符：

通过运算对一个或者多个值进行运算，**并获取运算结果**

比如：``typeof`` 就是运算符（而不是函数），可以获得一个值的类型。它会将该值的类型以 **字符串** 的形式返回。

```javascript
typeof null //Object
typeof a  //number 
typeof typeof a  //string
delete
```

## 算数运算符

对于非 Number 的值进行运算时，都会转换为 Number 然后运算

比如：``true → 1 false → 0 NaN → 0 Undefined → NaN``

任何值和 ``NaN`` 计算，都得 ``NaN``

**只有 + 运算符 会有拼串操作**， 其他的会把字符串也转换 Number

### + 加号

对两个值进行加法运算，并将结果返回

如果对两个字符串进行加法运算，则会做拼串操作

![1546491428535](/img/user/programming/font-end/primitive/es/es-basic/1546491428535.png)

分号不可以换行，利用加号的特性对长字符串进行排版

任何的值和字符串做运算，都会先转换为字符串再做拼串操作**//底层使用 String()**

```javascript
c = 1 + 2 + “3” // c = 33
c = “1”+ 2 + 3 // c = 123
```

### - 减号

对两个值进行减法运算，并将结果返回

### /除号

对两个值进行除法运算，并将结果返回

### * 乘号

对两个值进行乘法运算，并将结果返回

### % 取模 (取余数)

对两个值进行模除运算，并将结果返回

### ** 指数

### 语法糖 += -=

## 一元运算符

只需要一个操作数的运算符。比如：``typeof``

### + 正号 – 负号

正号不会对数字产生任何影响

负号对数字进行符号取反

```javascript
a = true
a = + a //1  typeof a → Number
a = - a //-1  typeof a → Number
```

对于非 Number 值会先转换为 Number 再运算

### 自增 ++

i ++ 先返回 i 再自增，等于原值（自增前的值）

```javascript
var i =20
i = i ++ 
// i 的原值不变  
//i变成了21 又被赋予了 i ++ 的值 即 20
```

++ i 先自增再返回 i ，等于新值（原值 +1）

无论是 ++ i 还是 i ++ ，i 的值都会马上 +1，区别只是两个运算符返回的值不同

> ++ 是一个操作符，操作符就会有返回值

```javascript
var c = 10
c++
console.log(c++) //11
var d = 20
console.log(++d) //21
console.log(++d)//22
var e = 20
result = e++ + ++e + e //20 + 22 + 22
```

利用自增实现循环，i++，i = i%5 //实现 0-4 i 不断循环

### 关键字

```js
// delete 运算
console.log(true === delete a.fake);
// void 运算
console.log(undefined === void a);
// typeof 运算
console.log("number" === typeof a);
```

## 逻辑运算符 Logical Operators

### ! 非

对布尔值进行取反，并返回结果

对非 Boolean，会转换为布尔值再取反

可以利用这个特点转换为布尔值

b = !!b; 两次取反转换为布尔值

调用的是 Boolean() 函数

### && 与

对两侧进行与运算，并返回结果

- 男女真心相爱 true && true
- 不是真心相爱 false && true

JS 的与是短路的与，如果第一值为 false 则不会看第二值，表现为：

- 如果第一个值为 false，则返回第一个值
- 如果第一个值为 true，则返回第二个值

```javas
 true && alert(“”) //alert()会执行
1 && 2 // 2 
```

### || 或

对两侧进行或运算，并返回结果

或是亲情

- false || true 父母不爱我，但是我爱着父母，还会住在

或也是短路的或，如果第一个值是 true 则不会检查第二个值，表现为：

- 第一个值为 true，返回第一个值
- 第一个值为 false，返回第二个值

```javascript
false || alert()  //会执行
1 || 2 // 1
```

### && || 面对非 Boolean 的情况

先转换为 Boolean，再运算，并且返回 **原值**，而不是 true false

与运算，都是 true，返回第二个值

或运算，都是 false，返回第二个值

## 赋值运算符

=赋值号

+=

```javascript
a += 5 // a = a+5

var str = "hello"; 
str += "world"

let str = ''
str+=1+3 // '4' 相当于有一个小括号 #
```

加号和等中间不能有空格

-=

*=

/=

%=

```js
a = 10; a <<= 10;
console.log(10240 === a);
a = 10; a >>= 2;
console.log(2 === a);
a = 10; a >>>= 2;
console.log(2 === a);
a = 10; a &= 3;
console.log(2 === a);
a = 10; a ^= 3;
console.log(9 === a);
a = 10; a |= 3;
console.log(11 === a);
```

## 比较运算符 Comparison Operators

又称为关系运算符，relational operators

关系运算符用来比较两个值之间的大小关系的

- \>
- \>=
- <
- <=

关系运算符的规则和数学中一致，用来比较两个值之间的关系，

- 如果关系成立则返回 true，关系不成立则返回 false。
- 如果比较的两个值是 **非数值**，会将其转换为 Number 然后再比较。
  - 一个非 Number 非 String 和 一个 String
  - 所以 任何值 和 NaN 比较都是 false

如果比较的两个值 **都是字符串**，此时会比较字符串的 Unicode 编码，而不会转换为 Number。

```javascript
console.log(“11” < “5”); //true 先拿1和5比
console.log(“11” < +“5”)； //这样就可以了
console.log(“abc” < “b”) //a和b比，比完就ok
console.log("abc" > "ab"); //true
```

如果两位一样，就比较下一位

应用：人名按字母排序 比较中文没有意义

### ==

如果相等会返回 true

会自动转换类型，转换的情况不一定，一般情况下会转换为 Number

```javascript
console.log(1 == 1); //true
console.log(“1” == 1); //true
console.log(“1” ==  true); //true
console.log(null==  0); //false
```

undefined 衍生自 null，所以==判断会返回 true

NaN 不和任何值相等，包括他本身，

- 可以通过 isNaN() 函数判断是否是 NaN

当比较运算涉及类型转换时 (i.e., non–strict comparison), JavaScript 会按以下规则对字符串，数字，布尔或对象类型的操作数进行操作:

- 当比较数字和字符串时，字符串会转换成数字值。 JavaScript 尝试将数字字面量转换为数字类型的值。 首先, 一个数学上的值会从数字字面量中衍生出来，然后得到被四舍五入后的数字类型的值。
- 如果其中一个操作数为布尔类型，那么布尔操作数如果为 true，那么会转换为 1，如果为 false，会转换为整数 0，即 0。
- 如果一个对象与数字或字符串相比较，JavaScript 会尝试返回对象的默认值。操作符会尝试通过方法 valueOf 和 toString 将对象转换为其原始值（一个字符串或数字类型的值）。如果尝试转换失败，会产生一个运行时错误。
- 注意：当且仅当与原始值比较时，对象会被转换为原始值。当两个操作数均为对象时，它们作为对象进行比较，仅当它们引用相同对象时返回 true。

### !=

如果不相等返回 true

会自动转换类型，转换的情况不一定，一般情况下会转换为 Number

### ===

全等运算符，用于判断是否全等

不同的是：不会做自动的类型转换，即类型不同直接返回 false

```javascript
console.log(“1” === 1); //false
console.log( null === undefined); //false 因为null undefined 是两个类型
```

### !==

不全等，用于判断两个值是否不全等

不会做自动的类型转换，即类型不同直接返回 true

## 条件运算符 Ternary Operator

又称三目运算符

条件运算符在执行时，首先对条件表达式进行求值，如果该值为 true，则执行语句 1，并返回执行结果，如果为 false 则执行语句 2 ，并返回执行结果

```javascript
条件表达式?语句1:语句2  //语句结束的分号可加可不加
true?语句1:语句2； //语句2
var max = a>b?a:b;
max = max>c?max:c;
var max = a>b?(a>c?a:c):(b>c?:b:c); //这种写法不推荐使用，可读性不好，性能没有区别，记得加括号
```

如果条件表达式是非布尔值，会先转换为布尔值

条件运算符?和：是 **一对运算符**,不能分开单独使用

条件运算符的结合方向是自右至左，保证最外层只有一个条件运算符

```javascript
a>b?a:c>d?c:d 
//应理解为 
a>b?a:(c>d?c:d)
```

因为条件运算符的优先级较低，所以一定要加上括号防止出现 bug

## 运算符的优先级

`,` 运算符

使用 `,` 可以分割多个语句，一般在声明多个变量的时候使用

多用括号，可以减少背诵

**单目加减** 优先级 高于乘除，**因为是代表正负**

<https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Operator_Precedence#Table>

逻辑与大于逻辑或

```js
function foo(n){
  return n && foo(n-1) + n || 1
}
console.log(foo(10)) // 56=55+1
```

优先级高的表现是什么呢？这是运算符，优先级高就套上括号就可以理解了

## 左结合与右结合

对于优先级别相同的运算符，大多数情况，计算顺序总是从左到右，这叫做运算符的“左结合”（left-to-right associativity），即从左边开始计算。

```
x + y + z
```

上面代码先计算最左边的 `x` 与 `y` 的和，然后再计算与 `z` 的和。

但是少数运算符的计算顺序是从右到左，即从右边开始计算，这叫做运算符的“右结合”（right-to-left associativity）。其中，最主要的是赋值运算符（`\=`）和三元条件运算符（`?:`）。

```js
w = x = y = z;
q = a ? b : c ? d : e ? f : g;
```

上面代码的运算结果，相当于下面的样子。

```js
w = (x = (y = z));
q = a ? b : (c ? d : (e ? f : g));
```

上面的两行代码，各有三个等号运算符和三个三元运算符，都是先计算最右边的那个运算符。

指数运算符（`**`）也是右结合的。

```js
// 相当于 2 ** (3 ** 2)
2 ** 3 ** 2
// 512
```

# 流程控制语句

## If Statements

```javascript
// if ...

if (表达式) 语句;

if (表达式)
    语句;

if (表达式) {
    代码块;
}


// if ... else

if (表达式) 语句;
else 语句;

if (表达式)
    语句;
else
    语句;

if (表达式) {
    代码块;
} else {
    代码块;
}

// if ... else if ... else ...

if (表达式) {
    代码块;
} else if (表达式) {
    代码块;
} else if (表达式) {
    代码块;
} else {
    代码块;
}
```

定义域要覆盖满，大于 100，小于 0 都是不合法的

else{} 嵌套 if else，也有 else if {} 语句。差别在于代码段的分配

多个数从大到小输出

每个数比较，else 到最后自己 alert 分支排序

条件判断，一定要注意不要写成 = 赋值号

## 条件分支语句

```javascript
switch语句
	switch(条件表达式){
	case 表达式：语句；
		break； //case后的都会执行，所以需要break
	default：语句；
		break；
}
```

```javascript
/*
对于成绩大于60分的，输出“合格”。低于60分的，输出“不合格”
6x/10 = 6.x
7x/10 = 7.x
*/
var score = 75;
//方法一：
switch(parseInt(score/10)){
  case 10:
  case 9:
  case 8:
  case 7:
  case 6:
    console.log("合格")
    break
  default:
    console.log("不合格")
    break
}
//方法二：
var score = 75;
switch(true){
  case score >= 60:
    console.log("合格")
    break
  default:
    console.log("不合格")
    break
}
```

default 分支可以省略不写。

case 关键词后面只能使用：变量，数字，字符串。

因为 switch 是判断条件表达式和 case 表达式的 **值是否相等**，进而选择

使用严格运算符 `\===`

第二种方法更常用，多个表达式，只有一个是正确的，直接设置条件表达式为 true，，但是其他语言不会这样写的样子

## 循环语句

循环的三要素：初始化变量、循环判断语句、变量更新语句 -

### While 语句

```javascript
while(条件表达式){
	语句//循环体
}  
```

while 语句在执行时，会先对条件表达式进行求值判断，

如果判断结果为 false，则终止循环

如果判断结果为 true，则执行循环体循环体执行完毕，继续对条件表达式进行求值判断，依此类推

```javascript
while (true){
	i++;
	if(i=10){
	break;
	}
} //直接进入循环体，就相当于do while  
```

### Do While 语句

```javascript
do{语句...}
while(条件表达式);
```

会先执行 do 后的循环体，然后在对条件表达式进行判断，如果判断判断结果为 false，则终止循环。

如果判断结果为 true，则继续执行循环体，依此类推

和 while 的区别：

- while：先判断后执行
- do...while: 先执行后判断
- do...while 可以确保循环体至少执行一次。

### For 语句

for 语句专门提供了位置用于放置三要素

```javascript
for(1.初始化表达式；2.条件表达式；4.更新表达式；){3.语句}
```

初始化表达式，可以进行定义，赋值瘦身

执行流程：

- 首先执行①初始化表达式，初始化一个变量，
- 然后对②条件表达式进行求值判断，如果为 false 则终止循环
- 如果判断结果为 true，则执行③循环体
- 循环体执行完毕，执行④更新表达式，对变量进行更新。
- 更新表达式执行完毕重复②

for 循环三个语句都可以省略

- 如果条件表达式不写，则判断为 true

+ 嵌套的 for 循环，要注意初始化变量
+ `i--` 也是存在的 不要太惯性思维， 5-i

### Break 和 Continue 关键字

break

- 可以立即跳出最内层循环或 switch 语句
  - 可以创建一个 label，来表示当前循环，即可做到跳出相应循环
  - label（可以是任意）：循环语句
  - break label；
  - 不可以单独用于 if

continue

- 可以跳过余下内容，马上执行下一次循环
- 同样作用于内层循环

```js
var i, j;
loop2:
for (i = 0; i < 3; i++) {      //The first for statement is labeled "loop1"
   loop2:
   for (j = 0; j < 3; j++) {   //The second for statement is labeled "loop2"
      if (i == 1 && j == 1) {
         continue loop2;
      }
      console.log("i = " + i + ", j = " + j);
   }
}

// Output is:
//   "i = 0, j = 0"
//   "i = 0, j = 1"
//   "i = 0, j = 2"
//   "i = 1, j = 0"
//   "i = 2, j = 0"
//   "i = 2, j = 1"
//   "i = 2, j = 2"
// Notice how it skips both "i = 1, j = 1" and "i = 1, j = 2"

var i, j;
loop1:
for (i = 0; i < 3; i++) {      //The first for statement is labeled "loop1"
   loop2:
   for (j = 0; j < 3; j++) {   //The second for statement is labeled "loop2"
      if (i == 1 && j == 1) {
         break loop1;
      }
      console.log("i = " + i + ", j = " + j);
   }
}
// Output is:
//   "i = 0, j = 0"
//   "i = 0, j = 1"
//   "i = 0, j = 2"
//   "i = 1, j = 0"
// Notice the difference with the previous continue example
```

# FAQ

#faq/js

## 类型转换精简版

条件判断类型的，隐式转换为 Boolean，结果只有 true 和 false 两种

算术运算和关系操作符，隐式转换为 Number：

+ 如果是布尔类型，转换为 0 和 1
+ 如果是有数字意义的，数字，数字字符串，各种进制开头的字符串，转换为相应的数字
+ 没有数字意义的，结果只可能是 0 和 NaN
  + 转换为 0 的，null ， 空串
  + 其他都是 NaN
+ 如果是对象，先调用 valueOf，然后转换，如果结果是 NaN，在调用 toString，再次转换

## 0 空串  Undefined 的经典场景

```ts
	const selectedItem = event?.target as HTMLImageElement;
	const itemIndex = Number(selectedItem.dataset.index);

	if (Number.isInteger(itemIndex)) {}
```

先判断有没有, 再转换为 number

## 如何获取安全的 Undefined 值

因为 undefined 是一个标识符，所以可以被当作变量来使用和赋值，但是这样会影响 undefined 的正常判断。

表达式 void ___ 没有返回值，因此返回结果是 undefined。void 并不改变表达式的结果，只是让表达式不返回值。

按惯例我们用 void 0 来获得 undefined。
