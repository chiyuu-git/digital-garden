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

## 进制转换（进制形式字符串 To 数值）

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

## 进制转换 (数值 to 字符串)

### Number.prototype.toString()

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

## 进制转换（进制形式字符串 To 进制形式字符串）

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

## 进制转换算法

利用栈的逆序性质

第一次模除的结果是进制的最后一位，余下的

倒数第二位，就是除以 2 之后再次模除，乘以二就是左移一位，除以二就是右移一位，之后再次判断余下的是什么

每次都除以 radix，就是模拟 a~n~ * radix^n-1^

最后再倒过来就是结果

### 从十进制到二进制

```js
  function divideBy2(decNumber) {
  
    const remainderStack = new Stack()
    let remainder,
        binaryString = ''
  
    while (decNumber > 0) { 
      // 取余数, 向下取整忽略非整数的部分
      remainder = Math.floor(decNumber % 2)
      // 保存余数
      remainderStack.push(remainder)
      // 除以2向下取整
      decNumber = decNumber>>>1
    }
  
    while (!remainderStack.isEmpty()) { 
      binaryString += remainderStack.pop().toString();
    }
  
    return binaryString;
  }
  
  console.log(divideBy2(12.2)) // .2 会在第一轮循环之后被忽略
```

### 通用转换算法

 ```js
  function divideByRadix(decNumber,radix) {
  
    var remainderStack = new Stack(),
      remainder,
      radixString = '',
      digits = {
        [2]:'01',
        [4]:'0123',
        [8]:'01234567',
        [16]:'0123456789ABCDEF',
      }
  
    while (decNumber > 0) { //{1} 
      remainder = Math.floor(decNumber % radix); //{2} 取余数,加上向下取整忽略非整数的部分
      remainderStack.push(remainder); //{3} 保存余数
      decNumber = Math.floor(decNumber / radix); //{4} 除以base向下取整
    }
  
    while (!remainderStack.isEmpty()) { //{5} 
      radixString += digits[radix][remainderStack.pop()]; // 根据余数取对应的字符
    }
  
    return radixString;
  }
  
  console.log(divideByRadix(15,16)) // F
```

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
