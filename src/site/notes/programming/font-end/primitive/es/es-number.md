---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/es/es-number/"}
---


# Experience

#programming-experience

只要返回值为 number，就要考虑为 0 的情况下是否会有什么问题

# 基础

在 JavaScript 中, **Number** 是一种 定义为 64 位双精度浮点型（double-precision 64-bit floating point format） (IEEE 754) (numbers between -(2^53^ -1) and 2^53^ -1). 的数字数据类型。

**它并没有为整数给出一种特定的类型**。除了能够表示浮点数外，还有一些带符号的值：`+Infinity`，`-Infinity` 和 `NaN` (非数值，N 原地标记 ot-a-Number)。

超出这个范围，JavaScript 中的数字不再安全了，也就是只有 second mathematical interger 可以在 JavaScript 数字类型中正确表现。

## 浮点数

由于保存浮点数值需要的内存空间是保存整数值的两倍，因此 ECMAScript 会不失时机地将浮点数值转换为整数值。

+ 显然，如果小数点后面没有跟任何数字，那么这个数值就可以作为整数值来保存。
+ 同样地，如果浮点数值本身表示的就是一个整数（如 1.0），那么该值也会被转换为整数

对于那些极大或极小的数值，可以用 e 表示法（即科学计数法）表示的浮点数值表示。

用 e 表示法表示的数值等于 e 前面的数值乘以 10 的指数次幂。ECMAScript 中 e 表示法的格式也是如此：

+ 前面是一个数值（可以是整数也可以是浮点数）
+ 中间是一个大写或小写的字母 E
+ 后面是 10 的幂中的指数，该幂值将用来与前面的数相乘。

下面是一个使用 e 表示法表示数值的例子：

```js
 var floatNum = 3.125e7;   // 等于 31250000 
```

也可以使用 **e 表示法** 表示极小的数值，如 0.00000000000000003，这个数值可以使用更简洁的 3e-17 表示。在默认情况下，ECMASctipt 会将那些小数点后面带有 6 个零以上的浮点数值转换为以 e 表示法表示的数值（例如，0.0000003 会被转换成 3e-7）。

浮点数值的最高精度是 **17 位小数**，但在进行算术计算时其精确度远远不如整数。例如，0.1 加 0.2 的结果不是 0.3，而是 0.30000000000000004。这个小小的舍入误差会导致无法测试特定的浮点数值。例如：

```js
if (a + b == 0.3){          // 不要做这样的测试！ 
    alert("You got 0.3."); 
} 
```

> 关于浮点数值计算会产生舍入误差的问题，有一点需要明确：这是使用基于 IEEE754 数值的浮点计算的通病，ECMAScript 并非独此一家；其他使用相同数值格式的语言也存在这个问题。

## 底层

参考: https://juejin.im/post/5b90e00e6fb9a05cf9080dff

遵循 [IEEE 754](https://zh.wikipedia.org/wiki/IEEE_754) 标准 通过 64 位来表示一个数字

通过图片具体看一下数字在内存中的表示

![](/img/user/programming/font-end/primitive/es/es-number/image-20230413150041910.png)

指数，有 11 bits. 在 ECMAScript 规范 里有给出 e 的范围为 [-1074, 971]. 这样，很容易推导出 JavaScript 能表示的最大整数为：

`1 x (2^53 - 1) x 2^971 = 1.7976931348623157e+308`

> **Number.MAX_SAFE_INTEGER == Math.pow(2,53) - 1, 而不是 Math.pow(2,52) - 1, why？尾数部分不是只有 52 位吗?**
>
> 这是因为二进制表示有效数字总是 1.xx…xx 的形式，尾数部分 f 在规约形式下第一位默认为 1（省略不写，xx..xx 为尾数部分 f，最长 52 位）。因此，JavaScript 提供的有效数字最长为 53 个二进制位（64 位浮点的后 52 位 + 被省略的 1 位）

同理可推导出最小的负数的值为：

`1 x 1 x 2^(-1074) = 5e-324`

注意：MIN_VALUE 表示最接近 0 的正数，而不是最小的数。最小的数是 －Number.MAX_VALUE

# Number 构造函数

`Number` 对象主要用于创建一个数值，如果参数无法被转换为数字，则返回 [`NaN`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/NaN)。

```js
console.log(Number('9/\n'))  // NaN
console.log(Number('9\n ')) // 9
```

在非构造器上下文中 (如：没有 [`new`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new) 操作符)，`Number` 能被用来执行类型转换。

# Number 的属性

## Number.MIN_VALUE

`Number.MIN_VALUE` 属性表示在 JavaScript 中所能表示的最小的正值。

`MIN_VALUE` 属性是 JavaScript 里 **最接近 0 的正值**，而不是最小的负值。

`MIN_VALUE` 的值约为 5e-324。小于 `MIN_VALUE` ("underflow values") 的值将会转换为 0。

因为 `MIN_VALUE` 是 `Number` 的一个静态属性，因此应该直接使用： `Number.MIN_VALUE，` 而不是作为一个创建的 `Number` 实例的属性。

## MAX_VALUE

`Number.MAX_VALUE` 属性表示在 JavaScript 里所能表示的最大数值。

`MAX_VALUE` 属性值接近于 `1.79E+308`。大于 `MAX_VALUE` 的值代表 "`Infinity`"。

s 是符号位，表示正负。 m 是尾数，有 52 bits. e 是

+ <http://www.ynpxrz.com/n1445898c2023.aspx>
+ <http://www.zxjsq.net/>
+ <http://www.oolap.com/programming-double>
+ <https://www.zhihu.com/question/24423421?sort=created>

## MIN_SAFE_INTEGER

**Number.MIN_SAFE_INTEGER** 代表在 JavaScript 中最小的 **安全** 的 integer 型数字 $-(2^{53} - 1)$.

`MIN_SAFE_INTEGER` 的值是 `-9007199254740991`

## MAX_SAFE_INTEGER

**Number.MAX_SAFE_INTEGER** 常量表示在 JavaScript 中最大的 **安全** 整数（maxinum safe integer)（$2^{53} - 1$）

MAX_SAFE_INTEGER 是一个值为 9007199254740991 的常量。

```js
Number.MAX_SAFE_INTEGER // 9007199254740991
Math.pow(2, 53) - 1     // 9007199254740991
Number.MAX_VALUE == Number.MAX_SAFE_INTEGER // false
```

Number.MAX_SAFE_INTEGER 9007199254740991，小于该值能精确表示，不会有精度的丢失

Number.MAX_VALUE 1.7976931348623157e+308，大于该值得到的是 Infinity，介于 Infinity 和安全值之间的无法精确表示。

## Number.NaN

`Number.NaN` 的值同全局对象 NaN 属性的值相同。

```js
Number.isNaN(NaN) // true
Number.isNaN(Number.NaN) // true
```

NaN：举个例子，可以利用这个特殊行为来检测函数的参数是可运算的（可以像 number 一样进行加减乘除等运算）。如果不可运算，则可赋予这个参数一个默认的值或其他合适的内容。这样，就可以得到一个隐式转换参数值的函数，而这得益于 Javascript 的全功能性。

## POSITIVE_INFINITY

`Number.POSITIVE_INFINITY ` 属性表示正无穷大。

`Number.POSITIVE_INFINITY` 的值同全局对象 [`Infinity`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Infinity) 属性的值相同。

与数学上的正无穷略有不同，具体查阅 MDN

## NEGATIVE_INFINITY

`Number.NEGATIVE_INFINITY ` 属性表示负无穷大。

`Number.NEGATIVE_INFINITY` 的值同全局对象 -Infinity 属性的值相同。

## Number.EPSILON

# Number 的方法

## ~~window.isNaN()~~

`isNaN()` 函数用来确定一个值是否为 [`NaN`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/NaN) 。

**描述**

+ 与 JavaScript 中其他的值不同，[`NaN`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/NaN) 不能通过相等操作符（== 和 \==\=）来判断 ，因为 `NaN == NaN` 和 `NaN === NaN` 都会返回 `false`。 因此，`isNaN` 就很有必要了。
+ 有很多怪异行为，建议不要使用
+ 下一个版本的 ECMAScript (ES2015) 包含 [`Number.isNaN()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/isNaN) 函数。通过 `Number.isNaN(x)` 来检测 `变量x` 是否是一个 `NaN` 将会是一种可靠的做法。
+ 然而，在缺少 `Number.isNaN` 函数的情况下, 通过表达式 `(x != x)` 来检测 `变量x` 是否是 `NaN` 会更加可靠。利用了 `NaN` 自身永不相等于自身这一特征

## Number.isNaN()

ES6 新增

**Number.isNaN()** 方法确定传递的值是否为 [`NaN`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/NaN) 和其类型是 [`Number`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number)。它是原始的全局 [`isNaN()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/isNaN) 的更强大的版本。

**描述**

+ 和全局函数 [`isNaN()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/isNaN) 相比，该方法不会强制将参数转换成数字，只有在参数是真正的数字类型，且值为 `NaN` 的时候才会返回 `true`。

```JS
  isNaN(NaN) // true
  isNaN("NaN") // true
  Number.isNaN(NaN) // true
  Number.isNaN("NaN") // false
  Number.isNaN(1) // false
```

## window.isFinite()

## Number.isFinite()

ES6 新增

**Number.isFinite()** 方法用来检测传入的参数是否是一个有穷数（finite number）。

**描述**

+ 和全局的 [`isFinite()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/isFinite) 函数相比，这个方法不会强制将一个非数值的参数转换成数值，这就意味着，只有数值类型的值，且是有穷的（finite），才返回 `true`。

  ```javascript
  isFinite(25) // true
  isFinite("25") // true
  Number.isFinite(25) // true
  Number.isFinite("25") // false
  ```

## Number.isInteger()

**Number.isInteger()** 方法用来判断给定的参数是否为整数。

**描述**

+ 如果被检测的值是整数，则返回 `true`，否则返回 `false`。注意 [`NaN`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/NaN) 和正负 [`Infinity`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Infinity) 不是整数。

## Number.isSafeInteger()

ES6 新增

**Number.isSafeInteger()** 方法用来判断传入的参数值是否是一个“安全整数”（safe integer）。

比如，2^53^- 1 是一个安全整数，它能被精确表示，在任何 IEEE-754 舍入模式（rounding mode）下，没有其他整数舍入结果为该整数。作为对比，2^53^ 就不是一个安全整数，它能够使用 IEEE-754 表示，但是 2^53^ + 1 不能使用 IEEE-754 直接表示，在就近舍入（round-to-nearest）和向零舍入中，会被舍入为 2^53^。

安全整数范围为 -(2^53^ - 1) 到 ` `2^53^ - 1 之间的整数，包含 -(2^53^ - 1) 和 2^53^ - 1。

## Number.parseInt()

ES6 新增

和全局函数 window.parseInt() 一致

## Number.parseFloat()

ES6 新增

和全局函数 window.parseFloat() 一致

这种调整时为了使得全局变量的更加模块化

## window.parseInt()

**parseInt()** 函数解析一个 **字符串参数**，并返回一个指定基数的整数 (数学系统的基础)。

**参数**

- **string**：要被解析的值。如果参数不是一个字符串，则将其转换为字符串 (使用 `ToString ` 抽象操作)。字符串开头的空白符将会被忽略。
- **基数 radix** ：一个介于 2 和 36 之间的整数 (数学系统的基础)，表示上述字符串的 **基数**。比如参数 "10" 表示使用我们通常使用的十进制数值系统。**始终指定此参数** 可以消除阅读该代码时的困惑并且保证转换结果可预测。当未指定基数时，不同的实现会产生不同的结果。

**返回值**

- 返回解析后的 **十进制整数值**。
- 如果被解析参数的第一个字符无法被转化成数值类型，则返回 [`NaN`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/NaN)。

**安全整数**

+ 只能保证安全整数的解析

  ```js
  parseInt(1000000000000000000000,10) // 1
  ```

**可以解析的合法字符**

- 十六进制表示法，分别用 `0x` 表示，无法识别二进制和八进制的字符串

  ```js
  parseInt('0xF') //15
  parseInt('F') // NaN
  parseInt('0o11') //0
  parseInt('0o11',8) // 0
  parseInt('011',8) // 9 不推荐使用
  parseInt('0b11') //0
  parseInt('0b11',2) // 0
  ```

+ `0` 前缀的八进制表示法也可以识别，但是已经不推荐使用了
+ 相信以后 parseInt 可以识别新的前缀

**示例**

- `radix` 参数为 n 将会把第一个参数看作是一个数的 n 进制表示，而 **返回的值则是十进制** 的。例如：

  ```js
  parseInt('123', 5) // 将'123'看作5进制数，返回十进制数38 => 1*5^2 + 2*5^1 + 3*5^0 = 38
  ```

- 一些数中可能包含 e 字符（例如 6.022e23），使用 parseInt 去截取包含 e 字符数值部分会造成难以预料的结果。例如：

  ```js
  parseInt("6.022e23", 10);        // 返回 6
  parseInt(6.022e2, 10);          // 返回 602
  ```

- 如果 `parseInt` 遇到了不属于 `radix` 参数所指定的基数中的字符那么该字符和其后的字符都将被忽略。接着返回已经解析的整数部分。`parseInt` 将截取整数部分。开头和结尾的空白符允许存在，会被忽略。

  ```js
  parseInt("FXX123", 16); // 仅仅解析了F
  ```

- 以下例子均返回 `15`:

  ```js
  parseInt("0xF", 16);
  parseInt("F", 16);
  parseInt("17", 8);
  parseInt(021, 8);
  parseInt("015", 10);   // parseInt(015, 10); 返回 15
  parseInt(15.99, 10);
  parseInt("15,123", 10);
  parseInt("FXX123", 16);
  parseInt("1111", 2);
  parseInt("15 * 3", 10);
  parseInt("15e2", 10);
  parseInt("15px", 10);
  parseInt("12", 13);
  ```

- 以下例子均返回 `NaN`:

  ```js
  parseInt("-F", 16);
  parseInt("-0F", 16);
  parseInt("-0XF", 16);
  parseInt(-15.1, 10);
  parseInt(" -17", 8);
  parseInt("-1111", 2);
  parseInt("-15e1", 10);
  parseInt("-12", 13);
  ```

## window.parseFloat()

**parseFloat()** 函数解析一个字符串参数并返回一个浮点数。

**参数**

- 需要被解析成为浮点数的值。

**返回值**

- 给定值被解析成浮点数。
- 如果参数字符串的第一个字符不能被解析成为数字,`则parseFloat` 返回 `NaN`.

**描述**

- `parseFloat` 将它的字符串参数解析成为浮点数并返回.如果在解析过程中遇到了
  - 正负号 (+ 或 -)
  - 数字 (0-9)
  - 小数点,
  - 科学记数法中的指数 (e 或 E)
- 以外的字符,则它会忽略该字符以及之后的所有字符,返回当前已经解析到的浮点数
- 同时参数字符串首位的空白符会被忽略

**示例**

- 下面的例子都返回 **3.14**

  ```js
  parseFloat("3.14");
  parseFloat("314e-2");
  parseFloat("0.0314E+2");
  parseFloat("3.14more non-digit characters");
  ```

- 下面的例子将返回 `NaN`

  ```js
  parseFloat("FF2");
  ```

# Number 实例的方法 @@@

## ~~toFixed()~~

`toFixed()` 方法使用定点表示法来格式化一个数。

**注意**: toFixed 四舍五入采用银行家算法, 与传统的四舍五入有所不同, 不建议使用这个垃圾函数. toPercision 也有同样的问题

**参数**

- **digits**：小数点后数字的个数；介于 0 到 20 （包括）之间，实现环境可能支持更大范围。如果忽略该参数，则默认为 0。

**返回值**

- 所给数值的定点数表示法的 **字符串形式**。
- 如果数值大于 1e+21，该方法会简单调用 [`Number.prototype.toString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/toString) 并返回一个指数记数法格式的字符串。

**抛出异常**

- RangeError：如果 *digits* 参数太小或太大。0 到 20（包括）之间的值不会引起 [`RangeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RangeError)。实现环境（implementations）也可以支持更大或更小的值。
- TypeError：如果该方法在一个非 [`Number`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number) 类型的对象上调用。

**描述**

- 一个数值的字符串表现形式，**不使用指数记数法**，而是在小数点后有 digits（注：digits 具体值取决于传入参数）位数字。
- 该数值在必要时进行四舍五入，另外在必要时会用 0 来填充小数部分，以便小数部分有指定的位数。

**示例**

```js
  function financial(x) {
    return Number.parseFloat(x).toFixed(2);
  }
  
  console.log(financial(123.456));
  // expected output: "123.46"
  
  console.log(financial(0.004));
  // expected output: "0.00"
  
  console.log(financial('1.23e+6'));
  // expected output: "123000.00"
  
  console.log(financial('1.23e6'));
  // expected output: "123000.00"
```

```js
  var numObj = 12345.6789;
  
  numObj.toFixed();         // 返回 "12346"：进行四舍五入，不包括小数部分
  numObj.toFixed(1);        // 返回 "12345.7"：进行四舍五入
  
  numObj.toFixed(6);        // 返回 "12345.678900"：用0填充
  
  (1.23e+20).toFixed(2);    // 返回 "123000000000000000000.00"
  
  (1.23e-10).toFixed(2);    // 返回 "0.00"
  
  2.34.toFixed(1);          // 返回 "2.3"
  
  -2.34.toFixed(1);         // 返回 -2.3 （由于操作符优先级，负数不会返回字符串）
  
  (-2.34).toFixed(1);       // 返回 "-2.3" （若用括号提高优先级，则返回字符串）
```

## ~~toPrecision()~~

`toPrecision()` 方法以指定的精度返回该数值对象的字符串表示。

**参数**

+ 可选。一个用来指定有效数个数的整数。

**返回值**

+ 以定点表示法或指数表示法表示的一个数值对象的字符串表示，四舍五入到 `precision` 参数指定的显示数字位数。
+ 如果忽略 `precision` 参数，则该方法表现类似于 [`Number.prototype.toString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/toString)。如果该参数是一个非整数值，将会向下舍入到最接近的整数。

**异常**

+ RangeError：如果 *precison* 参数不在 1 和 100 （包括）之间，将会抛出一个 `RangeError` 。执行环境也可以支持更大或更小的范围。ECMA-262 只需要最多 21 位显示数字。

**示例**

```js
  var numObj = 5.123456;
  console.log("numObj.toPrecision()  is " + numObj.toPrecision());  //输出 5.123456
  console.log("numObj.toPrecision(5) is " + numObj.toPrecision(5)); //输出 5.1235
  console.log("numObj.toPrecision(2) is " + numObj.toPrecision(2)); //输出 5.1
  console.log("numObj.toPrecision(1) is " + numObj.toPrecision(1)); //输出 5
  
  // 注意：在某些情况下会以指数表示法返回,为了不对数量级造成影响
  console.log((1234.5).toPrecision(2)); // "1.2e+3"
```

## toExponential()

**toExponential()** 方法以指数表示法返回该数值 **指数字符串** 表示形式。

**参数**

- **fractionDigits**：可选。一个整数，用来指定小数点后有几位数字。默认情况下用尽可能多的位数来显示数字。

**返回值**

- 一个用幂的形式 (科学记数法) 来表示 [`Number`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number) 对象的 **字符串**。小数点后以 fractionDigits 提供的值来四舍五入
- 如果 `fractionDigits` 参数被忽略了，小数点后的将尽可能用最多的位数来表示该数值。

**注意**

- 对数值字面量使用 `toExponential()` 方法，且该数值没有小数点和指数时，应该在该数值与该方法之间隔开一个空格，以避免点号被解释为一个小数点。也可以使用两个点号调用该方法。
- 如果一个数值的小数位数多余 `fractionDigits` 参数所提供的，则该数值将会在 `fractionDigits` 指定的小数位数处四舍五入。可以查看 [`toFixed()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/toFixed) 方法描述中关于四舍五入的讨论，同样应用于 `toExponential() ` 方法。

**异常**

- RangeError：如果 *fractionDigits* 太小或太大将会抛出该错误。介于 0 和 20（包括 20）之间的值不会引起 `RangeError` 。 执行环境也可以支持更大或更小范围。
- TypeError：如果该方法在一个非数值类型对象上调用。

**示例**

```js
  var numObj = 77.1234;
  
  alert("numObj.toExponential() is " + numObj.toExponential()); //输出 7.71234e+1
  alert("numObj.toExponential(4) is " + numObj.toExponential(4)); //输出 7.7123e+1
  alert("numObj.toExponential(2) is " + numObj.toExponential(2)); //输出 7.71e+1
  alert("77.1234.toExponential() is " + 77.1234.toExponential()); //输出 7.71234e+1
  alert("77 .toExponential() is " + 77 .toExponential()); //输出 7.7e+1
```

## 三者的差别

toFixed()，有效的小数

toPrecision()，指定多少位有效数字，相当于是数学题的经典坑人伎俩

toExponential()，转换为指数形式，顺便可以指定小数位数

## toString()

**toString()** 方法返回指定 [`Number`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number) 对象的字符串表示形式。

**参数**

- radix：指定要用于数字到字符串的转换的基数 (从 2 到 36)。如果未指定 radix 参数，则默认值为 10。

**描述**

+ [`Number`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number) 对象覆盖了 [`Object`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object) 对象上的 `toString()` 方法，它不是继承的 [`Object.prototype.toString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/toString)。对于 [`Number`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number) 对象，`toString()` 方法以指定的基数返回该对象的字符串表示。
+ 如果转换的基数大于 10，则会使用字母来表示大于 9 的数字，比如基数为 16 的情况，则使用 a 到 f 的字母来表示 10 到 15。
+ 如果基数没有指定，则使用 10。
+ 如果对象是负数，则会保留负号。即使 radix 是 2 时也是如此：返回的字符串包含一个负号（-）前缀和正数的二进制表示，**不是** 数值的二进制补码。
+ 进行数字到字符串的转换时，建议 **用小括号将要转换的目标括起来**，防止调用符号被解析为小数点

  ```js
  var count = 10;
  
  console.log(count.toString());    // 输出 '10'
  console.log((17).toString());     // 输出 '17'
  console.log((17.2).toString());   // 输出 '17.2'
  
  var x = 6;
  
  console.log(x.toString(2));       // 输出 '110'
  console.log((254).toString(16));  // 输出 'fe'
  
  console.log((-10).toString(2));   // 输出 '-1010'
  console.log((-0xff).toString(2)); // 输出 '-11111111'
  ```

## toLocaleString()

# NaN 的产生条件

0 除以 0 会返回 `NaN `—— 但是其他数除以 0 则不会返回 `NaN`。

0 乘以 `POSITIVE_INFINITY` 为 NaN。

NaN 乘以 `POSITIVE_INFINITY` 为 NaN。

含有 NaN 的算数表达式

# 进制

Number('0b111') // 7

Number('0o10') // 8

Number('0xF') //15

## 进制表示法

十六进制前缀 `0x`

ES6 提供了二进制和八进制数值的新的写法，分别用前缀 `0b`（或 `0B`）和 `0o`（或 `0O`）表示。

```js
0b111110111 === 503 // true
0o767 === 503 // true
```

从 ES5 开始，在严格模式之中，八进制就不再允许使用前缀 `0` 表示，ES6 进一步明确，要使用前缀 `0o` 表示。

```js
// 非严格模式
(function(){
  console.log(0o11 === 011);
})() // true

// 严格模式
(function(){
  'use strict';
  console.log(0o11 === 011);
})() // Uncaught SyntaxError: Octal literals are not allowed in strict mode.
```

## 合法数值

ES 的合法数值进制是十进制、二进制、八进制、十六进制，除了十进制都需要添加前缀

在控制台打印、进行算数运算的时候的时候会自动转换成十进制

```js
console.log(0x11) // 17
console.log(0o11) // 9
console.log(0b11) // 3
console.log(0b11+5) // 3
```

十进制数值，还可以使用 e 表示法

```js
console.log(.1e1) // 1
```

**注意**

- 区分合法数值、合法数值的字符串形式、可读的数值字符串

  ```js
  0b1001
  '0b1001'
  '1001' //既可以看作是一千零一，也可以看作是二进制
  ```

**注意**

```js
  0x12e3 === 4835
  0x12e+3 === 305
  0x12e-3 === 299
  0x12 === 18
```

- 其实 0x12e3 是因为 e 也是十六进制里的字符，十六进制的字符是 0-9 加上 a-f，大小写无所谓，所以 0x12e3 是一个正常的 十六进制 数字格式。
- 0x12e+3 为什么也不是呢， 其实它只是两个数相加而已，0x12e 的十进制是 302，加上 3 就得到 305 了，所以他是一个表达式，不是一个单纯的数字。
- 而 011e2 这样的格式也是不对的，甚至直接报语法错误。
- 所以 **指数格式只能在 十进制 里使用**。
- .1e1 其实是指数表示法，$0.1 * 10$

## 进制转换（字符串 To 数值）

只要是 **合法数值的字符串形式**，都可以通过函数转换为相应的数值

有 2 个函数可以把非数值转换为数值间的 **进制转换**：Number()、parseInt()

> parseFloat() 方法无法识别二进制、八进制、十六进制合法表示的字符串

### 二进制和八进制

如果要将 `0b` 和 `0o` 前缀的字符串数值转为十进制，要使用 `Number` 方法

  ```js
  Number('0b111')  // 7
  Number('0o10')  // 8
  
  parseInt('0o11') // 0
  parseInt('0b11') // 0
  parseInt('0o11',8) // 0
  parseInt('0b11',2) // 0
  Number('0o11') // 9
  Number('0b11') // 3
  ```

### 十六进制

如果要将 `0x` 前缀的字符串数值转为十进制，可以使用 Number()、parseInt()

  ```js
  parseInt('0xF') // 15
  parseFloat('0xF') // 0
  Number('0xF') //15
  ```

### 进制转换 (数值 to 字符串)

#### Number.prototype.toString()

**toString()** 方法返回指定 [`Number`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number) 对象的字符串表示形式。

**参数**

- radix: 指定要用于数字到字符串的转换的基数 (从 2 到 36)。如果未指定 radix 参数，则默认值为 10。

**描述**

- 如果转换的基数大于 10，则会使用字母来表示大于 9 的数字，比如基数为 16 的情况，则使用 a 到 f 的字母来表示 10 到 15。
- 如果基数没有指定，则使用 10。
- 如果对象是负数，则会保留负号。即使 radix 是 2 时也是如此：返回的字符串包含一个负号（-）前缀和正数的二进制表示，**不是** 数值的二进制补码。
- 进行数字到字符串的转换时，建议 **用小括号将要转换的目标括起来**，防止出错。

```js
  var count = 10;
  
  console.log(count.toString());    // 输出 '10'
  console.log((17).toString());     // 输出 '17'
  console.log((17.2).toString());   // 输出 '17.2'
  
  var x = 6;
  
  console.log(x.toString(2));       // 输出 '110'
  console.log((254).toString(16));  // 输出 'fe'
  
  console.log((-10).toString(2));   // 输出 '-1010'
  console.log((-0xff).toString(2)); // 输出 '-11111111'
```

### 进制转换（数值 To 进制形式字符串）

ES 无法直接进行数值到数值的进制转换，原因如下：

- Number() 方法，只有一个参数，无法接受 radix
- parseInt() 方法，指定的是字符串的进制形式，最终的返回值一定是十进制
- Number.prototype.toString()，只能转换成字符串

要完成从数值到 **' 数值 '** 的转换，只能先变成十进制，再通过 toString 方法

下面是一个十六进制数转二进制数的例子

```js
var hexadecimal = '0xF'
// 先变成十进制字符串
let binary = parseInt(hexadecimal).toString(2)
console.log(binary) // '1111'
```

# Math 对象

Math 属于一个工具类，它不需要我们创建对象，它里边封装了属性运算相关的常量和方法

虽然是大写的，但是不是一个构造函数 @@@

我们可以直接使用它来进行数学运算相关的操作

## 属性

### Math.LN10

10 的自然对数, 约等于 2.303.

### Math.PI

常量，圆周率

其他属性都是常量

## 方法

### 隐式转换

调用 Number()

如果有任一参数不能被转换为数值，则结果为 NaN，Math 全部方法均是如此

### Math.abs()

绝对值运算

### Math.ceil()

天花板

向上取整

无论正负非整数一定变大

### Math.floor()

向下取整

无论正负非整数一定变小

```js
Math.floor(-2.5) // -3
```

### Math.round()

四舍五入取整

### Math.random()

生成一个 0-1 之间的随机数

生成一个 x-y 之间的随机数

```js
Math.round(Math.random()*(y-x)+x);
```

### Math.pow(x,y)

求 x 的 y 次幂

### Math.log()

返回一个数的自然对数（log~e~， 即 ln）。

### Math.sqrt()

对一个数进行开方

### Math.max()

求多个数中最大值

**参数**

- **value1, value2, ...** 一组数值

**返回值**

- 返回给定的一组数字中的最大值。
- 如果给定的参数中至少有一个参数无法被转换成数字，则会返回 [`NaN`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/NaN)。
- 如果没有参数，则结果为 - [`Infinity`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Infinity)。

  ```js
  Math.max(10, 20);   //  20
  Math.max(-10, -20); // -10
  Math.max(-10, 20);  //  20
  ```

### Math.min()

求多个数中的最小值

### Math.sin/cos/tan()

需要注意的是三角函数（`sin()`, `cos()`, `tan()`,`asin()`, `acos()`, `atan()`, `atan2()` 是以弧度返回值的。可以通过除法（`Math.PI / 180`）把弧度转换为角度，也可以通过其他方法来转换。

### ES6 新增

### Math.cbrt()

`Math.cbrt` 方法用于计算一个数的立方根。

### Math.sign()

`Math.sign` 方法用来判断一个数到底是正数、负数、还是零。对于非数值，会先将其转换为数值。

### Math.trunc()

`Math.trunc` 方法用于去除一个数的小数部分，返回整数部分。

```js
Math.trunc(4.1) // 4
Math.trunc(4.9) // 4
Math.trunc(-4.1) // -4
Math.trunc(-4.9) // -4
Math.trunc(-0.1234) // -0
```

对于非数值，`Math.trunc` 内部使用 `Number` 方法将其先转为数值。

```js
Math.trunc('123.456') // 123
Math.trunc(true) //1
Math.trunc(false) // 0
Math.trunc(null) // 0
```

对于空值和无法截取整数的值，返回 `NaN`。

```js
Math.trunc(NaN);      // NaN
Math.trunc('foo');    // NaN
Math.trunc();         // NaN
Math.trunc(undefined) // NaN
```

对于没有部署这个方法的环境，可以用下面的代码模拟。

```js
Math.trunc = Math.trunc || function(x) {
  return x < 0 ? Math.ceil(x) : Math.floor(x);
};
```

#### 与已有 API 的区别

简单的说，`parseInt()` 主要用于将字符串转换成整数，所以哪怕目标本身就是一个数，也极有可能是先转换成字符串再来处理的，这也能解释科学计数法的结果

`Math.trunc` 是直接对数值进行处理，理论上来说会快一些也更准确一些。可惜有些浏览器不支持。所以现在用 `Math.floor`、`Math.ceil` 的比较多，但是要注意处理负数。

```js
Math.floor(-5.1) // -6
Math.ceil(-5.1) // -5
```

# 位运算符 (Bitwise operators)

## 基础

### 概述

尽管一个数字常常仅代表它本身的值，但 JavaScript 提供了一些 [位运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators)。

> 这些位运算符和一个单一数字通过位操作可以用来表现一些布尔值。
>
> 然而自从 JavaScript 提供其他的方式来表示一组布尔值（如一个布尔值数组或一个布尔值分配给命名属性的对象）后，这种方式通常被认为是不好的。位操作也容易使代码难以阅读，理解和维护， 在一些非常受限的情况下，可能需要用到这些技术，比如试图应付本地存储的存储限制。 位操作只应该是用来优化尺寸的最后选择。

**按位操作符（Bitwise operators）** 将其操作数（operands）当作 32 位的比特序列（由 0 和 1 组成），而不是十进制、十六进制或八进制 [数值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number)。例如，十进制数 9，用二进制表示则为 1001。

按位操作符操作数字的二进制形式，但是 **返回值依然是标准的 JavaScript 数值**。

| 运算符                                                       | 用法      | 描述                                                         |
| ------------------------------------------------------------ | --------- | ------------------------------------------------------------ |
| [按位与（ AND）](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_AND) | `a & b`   | 对于每一个比特位，只有两个操作数相应的比特位都是 1 时，结果才为 1，否则为 0。 |
| [按位或（OR）](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_OR) | `a \| b`  | 对于每一个比特位，当两个操作数相应的比特位至少有一个 1 时，结果为 1，否则为 0。 |
| [按位异或（XOR）](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_XOR) | `a ^ b`   | 对于每一个比特位，当两个操作数相应的比特位有且只有一个 1 时，结果为 1，否则为 0。 |
| [按位非（NOT）](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_NOT) | `~ a`     | 反转操作数的比特位，即 0 变成 1，1 变成 0。                       |
| [左移（L](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Left_shift)[eft shift）](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Left_shift) | `a << b`  | 将 `a` 的二进制形式向左移 `b` (< 32) 比特位，右边用 0 填充。   |
| [有符号右移](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Right_shift) | `a >> b`  | 将 a 的二进制表示向右移 ` b `(< 32) 位，丢弃被移出的位。      |
| [无符号右移](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Unsigned_right_shift) | `a >>> b` | 将 a 的二进制表示向右移 ` b `(< 32) 位，丢弃被移出的位，并使用 0 在左侧填充。 |

这些位运算符直接处理每一个比特位（bit），所以是非常底层的运算，好处是速度极快，缺点是很不直观，许多场合不能使用它们，否则会使代码难以理解和查错。

有一点需要特别注意，位运算符只对 **整数起作用**，如果一个运算子不是整数，会自动转为整数后再执行。另外，虽然在 JavaScript 内部，数值都是以 64 位浮点数的形式储存，但是做位运算的时候，是以 32 位带符号的整数进行运算的，并且返回值也是一个 32 位带符号的整数。

截断：<https://jerryzou.com/posts/do-you-really-want-use-bit-operators-in-JavaScript/>

<https://blog.csdn.net/LingXi__Y/article/details/82022828>

### 隐式转换

```
i = i | 0;
```

上面这行代码的意思，就是将 `i`（不管是整数或小数）转为 32 位整数。

利用这个特性，可以写出一个函数，将任意数值转为 32 位整数。

```
function toInt32(x) {
  return x | 0;
}
```

上面这个函数将任意值与 `0` 进行一次或运算，这个位运算会自动将一个值转为 32 位整数。下面是这个函数的用法。

```js
toInt32(1.001) // 1
toInt32(1.999) // 1
toInt32(1) // 1
toInt32(-1) // -1
toInt32(Math.pow(2, 32) + 1) // 1
toInt32(Math.pow(2, 32) - 1) // -1
```

上面代码中，`toInt32` 可以将小数转为整数。对于一般的整数，返回值不会有任何变化。对于大于或等于 2 的 32 次方的整数，大于 32 位的数位都会被舍去。

使用二进制否运算取整，是所有取整方法中最快的一种。

对字符串进行二进制否运算，JavaScript 引擎会先调用 `Number` 函数，将字符串转为数值。

```js
// 相当于~Number('011')
~'011'  // -12

// 相当于~Number('42 cats')
~'42 cats' // -1

// 相当于~Number('0xcafebabe')
~'0xcafebabe' // 889275713

// 相当于~Number('deadbeef')
~'deadbeef' // -1
```

`Number` 函数将字符串转为数值的规则，参见《数据的类型转换》一章。

对于其他类型的值，二进制否运算也是先用 `Number` 转为数值，然后再进行处理。

```js
// 相当于 ~Number([])
~[] // -1

// 相当于 ~Number(NaN)
~NaN // -1

// 相当于 ~Number(null)
~null // -1
```

### 有符号 32 位整数

所有的按位操作符的操作数都会被转成 **补码**（two's complement）形式的有符号 32 位整数。

正数的补码就是原码，负数的补码为符号位不变，其余位取反后加一，如 2 的补码为 `0000 0010`，-2 的补码为 `1111 1110`，补码保证了当一个数是正数时，其最左的比特位是 0，当一个数是负数时，其最左的比特位是 1。因此，最左边的比特位被称为符号位，关于补码的作用可以看看阮一峰老师 [这篇文章](http://www.ruanyifeng.com/blog/2009/08/twos_complement.html)，个人觉得通俗易懂。

反转比特位即该数值进行’非‘位运算，也即该数值的反码。

`0` 是所有比特数字 0 组成的整数。

```js
0 (base 10) = 0000 0000 0000 0000 0000 0000 0000 0000 (base 2)
```

`-1` 是所有比特数字 1 组成的整数。

```js
-1 (base 10) = 1111 1111 1111 1111 1111 1111 1111 1111 (base 2)
```

`-2147483648`（十六进制形式：`-0x80000000`）是除了最左边为 1 外，其他比特位都为 0 的整数

```js
-2147483648 (base 10) = 1000 0000 0000 0000 0000 0000 0000 0000 (base 2)
```

`2147483647`（十六进制形式：`0x7fffffff`）是除了最左边为 0 外，其他比特位都为 1 的整数

```js
2147483647 (base 10) = 01111111111111111111111111111111 (base 2)
```

数字 `-2147483648` 和 `2147483647` 是 32 位有符号数字所能表示的最小和最大整数

> 符号位不光是代表符号，也可以代表数？
>
> 2147483647 + 1 = - 2147483648

### 或运算

二进制或运算符（`|`）逐位比较两个运算子，两个二进制位之中只要有一个为 `1`，就返回 `1`，否则返回 `0`。

```js
0 | 3 // 3
```

上面代码中，`0` 和 `3` 的二进制形式分别是 `00` 和 `11`，所以进行二进制或运算会得到 `11`（即 `3`）。

### 与运算

二进制与运算符（`&`）的规则是逐位比较两个运算子，两个二进制位之中只要有一个位为 `0`，就返回 `0`，否则返回 `1`。

```js
0 & 3 // 0
```

上面代码中，0（二进制 `00`）和 3（二进制 `11`）进行二进制与运算会得到 `00`（即 `0`）。

### 否运算、非运算

二进制否运算符（`~`）将每个二进制位都变为相反值（`0` 变为 `1`，`1` 变为 `0`）。它的返回结果有时比较难理解，因为涉及到计算机内部的数值表示机制。

```js
~ 3 // -4
```

`3` 的 32 位整数形式是 `0000 0000 0000 0000 0000 0000 0000 0011`，二进制否运算以后得到 `1111 1111 1111 1111 1111 1111 1111 1100`。由于第一位（符号位）是 1，所以这个数是一个负数。JavaScript 内部采用补码形式表示负数，即需要将这个数减去 1，再取一次反，然后加上负号，才能得到这个负数对应的 10 进制值。这个数减去 1 等于 `11111111111111111111111111111011`，再取一次反得到 `00000000000000000000000000000100`，再加上负号就是 `-4`。

考虑到这样的过程比较麻烦，可以简单记忆成，**一个数与自身的取反值相加，等于 -1。**

```js
~ -3 // 2
```

上面表达式可以这样算，`-3` 的取反值等于 `-1` 减去 `-3`，结果为 `2`。

```js
~~3 // 3
```

所有的位运算都只对整数有效。二进制否运算遇到小数时，也会将小数部分舍去，只保留整数部分。所以，对一个小数连续进行两次二进制否运算，能达到取整效果。

```js
~~2.9 // 2
~~47.11 // 47
~~1.9999 // 1
~~3 // 3
```

使用二进制否运算取整，是所有取整方法中最快的一种。

### 异或运算

异或运算（`^`）在两个二进制位不同时返回 `1`，相同时返回 `0`。

```
0 ^ 3 // 3
```

上面表达式中，`0`（二进制 `00`）与 `3`（二进制 `11`）进行异或运算，它们每一个二进制位都不同，所以得到 `11`（即 `3`）。

“异或运算”有一个特殊运用，连续对两个数 `a` 和 `b` 进行三次异或运算，`a^=b; b^=a; a^=b;`，可以 [互换](http://en.wikipedia.org/wiki/XOR_swap_algorithm) 它们的值。这意味着，使用“异或运算”可以在不引入临时变量的前提下，互换两个变量的值。

```js
var a = 10;
var b = 99;

a ^= b, b ^= a, a ^= b;

a // 99
b // 10
```

这是互换两个变量的值的最快方法。

### 左移运算符

左移运算符（`<<`）表示将一个数的二进制值向左移动指定的位数，尾部补 `0`，即乘以 `2` 的指定次方。向左移动的时候，**最高位的符号位是一起移动的**。

```js
// 4 的二进制形式为100，
// 左移一位为1000（即十进制的8）
// 相当于乘以2的1次方
4 << 1
// 8

-4 << 1
// -8
```

上面代码中，`-4` 左移一位得到 `-8`，是因为 `-4` 的二进制补码形式是 `11111111111111111111111111111100`，左移一位后得到 `11111111111111111111111111111000`，该数转为十进制（减去 1 后取反，再加上负号）即为 `-8`。

左移运算符用于二进制数值非常方便。

```js
var color = {r: 186, g: 218, b: 85};

// RGB to HEX
// (1 << 24)的作用为保证结果是6位数
var rgb2hex = function(r, g, b) {
  return '#' + ((1 << 24) + (r << 16) + (g << 8) + b)
    .toString(16) // 先转成十六进制，然后返回字符串
    .substr(1);   // 去除字符串的最高位，返回后面六个字符串
}

rgb2hex(color.r, color.g, color.b)
// "#bada55"
```

上面代码使用左移运算符，将颜色的 RGB 值转为 HEX 值。

### 右移运算符

右移运算符（`>>`）表示将一个数的二进制值向右移动指定的位数。如果是正数，头部全部补 `0`；如果是负数，头部全部补 `1`。右移运算符基本上相当于除以 `2` 的指定次方（最高位即符号位参与移动）。

```js
4 >> 1
// 2
/*
// 因为4的二进制形式为 00000000000000000000000000000100，
// 右移一位得到 00000000000000000000000000000010，
// 即为十进制的2
*/

-4 >> 1
// -2
/*
// 因为-4的二进制形式为 11111111111111111111111111111100，
// 右移一位，头部补1，得到 11111111111111111111111111111110,
// 即为十进制的-2
*/
```

右移运算可以模拟 2 的整除运算。

```js
5 >> 1
// 2
// 相当于 5 / 2 = 2

21 >> 2
// 5
// 相当于 21 / 4 = 5

21 >> 3
// 2
// 相当于 21 / 8 = 2

21 >> 4
// 1
// 相当于 21 / 16 = 1
```

### 无符号右移

头部补零的右移运算符（`>>>`）与右移运算符（`>>`）只有一个差别，就是一个数的二进制形式向右移动时，头部一律补零，而不考虑符号位。所以，该运算总是得到正值。对于正数，该运算的结果与右移运算符（`>>`）完全一致，区别主要在于负数。

```js
4 >>> 1
// 2

-4 >>> 1
// 2147483646
/*
// 因为-4的二进制形式为11111111111111111111111111111100，
// 带符号位的右移一位，得到01111111111111111111111111111110，
// 即为十进制的2147483646。
*/
```

这个运算实际上将一个值转为 32 位无符号整数。

查看一个负整数在计算机内部的储存形式，最快的方法就是使用这个运算符。

```
-1 >>> 0 // 4294967295
```

上面代码表示，`-1` 作为 32 位整数时，内部的储存形式使用无符号整数格式解读，值为 4294967295（即 `(2^32)-1`，等于 `11111111111111111111111111111111`）。

### 运用

使用否运算取整

使用右移除以 2 后取整

### 开关作用

位运算符可以用作设置对象属性的开关。

假定某个对象有四个开关，每个开关都是一个变量。那么，可以设置一个四位的二进制数，它的每个位对应一个开关。

```js
var FLAG_A = 1; // 0001
var FLAG_B = 2; // 0010
var FLAG_C = 4; // 0100
var FLAG_D = 8; // 1000
```

上面代码设置 A、B、C、D 四个开关，每个开关分别占有一个二进制位。

然后，就可以用二进制与运算检验，当前设置是否打开了指定开关。

```js
var flags = 5; // 二进制的0101

if (flags & FLAG_C) {
  // ...
}
// 0101 & 0100 => 0100 => true
```

上面代码检验是否打开了开关 `C`。如果打开，会返回 `true`，否则返回 `false`。

现在假设需要打开 `A`、`B`、`D` 三个开关，我们可以构造一个掩码变量。

```js
var mask = FLAG_A | FLAG_B | FLAG_D;
// 0001 | 0010 | 1000 => 1011
```

上面代码对 `A`、`B`、`D` 三个变量进行二进制或运算，得到掩码值为二进制的 `1011`。

有了掩码，二进制或运算可以确保打开指定的开关。

```js
flags = flags | mask;
```

二进制与运算可以将当前设置中凡是与开关设置不一样的项，全部关闭。

```js
flags = flags & mask;
```

异或运算可以切换（toggle）当前设置，即第一次执行可以得到当前设置的相反值，再执行一次又得到原来的值。

```js
flags = flags ^ mask;
```

二进制否运算可以翻转当前设置，即原设置为 `0`，运算后变为 `1`；原设置为 `1`，运算后变为 `0`。

```js
flags = ~flags;
```

## 位操作题集

### >>>0

https://blog.csdn.net/wutingyehe/article/details/92166140

https://zhuanlan.zhihu.com/p/97121092

### [136. 只出现一次的数字](https://leetcode-cn.com/problems/single-number/)

#### 题目

+ 给定一个 **非空** 整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

  ```
  输入: [2,2,1]
  输出: 1
  ```

#### 分析

+ 如果我们对 0 和二进制位做 XOR 运算，得到的仍然是这个二进制位

  `a ⊕ 0 = a`

+ 如果我们对相同的二进制位做 XOR 运算，返回的结果是 0

  `a ⊕ a = 0`

+ XOR 满足交换律和结合律

  `a ⊕ b ⊕ a = ( a ⊕ a ) ⊕ b = 0 ⊕ b = b`

+ 所以我们只需要将所有的数进行 XOR 操作，得到那个唯一的数字

  ```js
  var singleNumber = function(nums) {
    let res = 0
    for (let i = 0; i < nums.length; i++) {
      res ^=nums[i]
    }
    return res
  }
  let nums = [4,1,2,1,2]
  console.log(singleNumber(nums))
  ```

### Swan Animation-video 替换元素模拟

当 styleWidth 的值为 `<length>`、`<percentage>` ，且 styleHeigth 为 auto 时

需要根据 canvas 画布的宽高比，计算出相应的 styleHeight，维持画布的宽高比（styleWidth 为 auto 时类似）

这就有四种情况

1. 无宽度，无高度，00
2. 有宽度，无高度，10
3. 无宽度，有高度，01
4. 有宽度，有高度，11

可以把 4 个 if else 转换为 switch

# Unicode 编码

## charCodeAt()

- 根据索引获取指定的 **字符编码**
- A 65 Z 90
- a 97 z 122

## fromCharCode()

- 根据字符编码获取字符

## 字符的 Unicode 表示法

## codePointAt()

## String.fromCharCode()

# FAQ

#faq/js

## Number 类型转换

![es-basic](es-basic.md#转换为%20Number%20类型)

## 浮点数计算

```js
function numberEqual(a,b){

  return Math.abs(a-b)<Number.EPSILON
}
let a = 0.1+0.2, b=0.3
console.log(numberEqual(a,b));

function numbersequal(a,b){ return Math.abs(a-b)<Number.EPSILON;} 
var c=2.1+2.2, d=2.3;
console.log(numbersequal(c,d)); //true

```

## 判断一个数是否是 Number

typeof number

## 判断一个数是否是整数

**ES6 提供了 Number.isInteger**

```js
Number.isInteger(3) // true
Number.isInteger(3.1) // false
Number.isInteger('') // false
Number.isInteger('3') // false
Number.isInteger(true) // false
Number.isInteger([]) // false
```

**方法一：模除判断**

对于空字符串、字符串类型数字、布尔 true、空数组都会发生隐式转换，需要先判断类型

```js
function isInteger(obj) {
 return typeof obj === 'number' && obj%1 === 0
}
```

**方法二：使用 Math.round、Math.ceil、Math.floor 判断**

整数取整后还是等于自己。利用这个特性来判断是否是整数，Math.floor 示例，如下

```js
function isInteger(obj) {
 // 如果发生了隐式转换也不OK
 return Math.floor(obj) === obj
}

isInteger(3) // true
isInteger(3.3) // false
isInteger('') // false
isInteger('3') // false
isInteger(true) // false
isInteger([]) // false
```

**方式三、通过 parseInt 判断**

```js
function isInteger(obj) {
 return parseInt(obj, 10) === obj
}

isInteger(3) // true
isInteger(3.3) // false
isInteger('') // false
isInteger('3') // false
isInteger(true) // false
isInteger([]) // false、

//很不错，但也有一个缺点 #，太大了被截取了
isInteger(1000000000000000000000) // false
```

**通过位运算判断**

```js
function isInteger(obj) {
 return (obj | 0) === obj
}

isInteger(3) // true
isInteger(3.3) // false
isInteger('') // false
isInteger('3') // false
isInteger(true) // false
isInteger([]) // false

//这个函数很不错，效率还很高。但有个缺陷，上文提到过，位运算只能处理32位以内的数字，对于超过32位的无能为力
isInteger(Math.pow(2, 32)) // 32位以上的数字返回false了
```

## 保留小数

~~toFixed() 方法会存在后导零，而且是字符串~~

~~通过 Number()，可以消除后导零，转换为浮点数~~

```js
console.log(
  (1234.5).toFixed(2), // '1234.50'
  (1234.5).toFixed(2)*1, // 1234.5
)
```

~~保留有效数字：toPrecision()~~

例如：对于 X 进行保留两位小数的处理，则可以使用 Math.round(X * 100) / 100.进行处理。

[js toFixed 四舍五入问题 - 掘金](https://juejin.cn/post/7050760083239600141o)

## 拆分整数和小数

处理成整数，然后相减得到小数

转换成字符串，使用 split

## 转换成千分位数字

### 字符串匹配

![es-regexp](programming/font-end/primitive/es/es-regexp.md#数字的千位分隔符表示法)

### 方法二

```js
function format1(number) {
  return Intl.NumberFormat().format(number)
}
```

### 方法三

```js
function format2(number) {
  return number.toLocaleString('en')
}
```

## 转换成百分数

先乘 100，在保留小数，再拼串
