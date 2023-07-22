---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/es/es-string/"}
---


字符串

通过临时转换成包装类来调用

# String 构造函数

**String** 全局对象是一个用于字符串或一个字符序列的构造函数。

你也能使用 `String` 函数将其他值生成或转换成字符串：

```js
String(thing)
new String(thing)
```

**参数**

+ 任何可以被转换成字符串的值。

## 转义字符 Escape Sequence

除了普通的可打印字符以外，一些特殊有特殊功能的字符可以通过转义字符的形式放入字符串中：

| escape sequence          | Output              |
| ------------------------ | ------------------- |
| `\0`                     | 空字符              |
| `\'`                     | 单引号              |
| `\"`                     | `双引号`            |
| `\\`                     | 反斜杠              |
| `\n`                     | 换行                |
| `\r`                     | `回车`              |
| `\v`                     | 垂直制表符          |
| `\t`                     | 水平制表符          |
| `\b`                     | 退格                |
| `\f`                     | 换页                |
| `\uXXXX`                 | unicode 码          |
| `\u{X}` ... `\u{XXXXXX}` | unicode codepoint   |
| `\xXX`                   | Latin-1 字符 (x 小写) |

## UTF-8 万国码

字符串中使用转移字符\u 输出，16 进制位

```javascript
console.log(“\u0031”) //1
```

在 HTML 页面中：&#编码；

10 进制位

## 长字符串

有时，你的代码可能含有很长的字符串。你可能想将这样的字符串写成多行，而不是让这一行无限延长或着被编辑器折叠。有两种方法可以做到这一点。

其一，可以使用 + 运算符将多个字符串连接起来，如下所示：

```js
let longString = "This is a very long string which needs " +
                 "to wrap across multiple lines because " +
                 "otherwise my code is unreadable.";
```

其二，可以在每行末尾使用反斜杠字符（“\”），以指示字符串将在下一行继续。确保反斜杠后面没有空格或任何除换行符之外的字符或缩进; 否则反斜杠将不会工作

```js
let longString = "This is a very long string which needs \
to wrap across multiple lines because \
otherwise my code is unreadable.";
```

## 字符串的比较

熟练使用 C 语言的开发者经常使用 `strcmp` 函数来比较字符串，但在 JavaScript 中，你只需要使用 [比较操作符(>/=/<=)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Comparison_Operators)：

```js
var a = "a";
var b = "b";
if (a < b) // true
  print(a + " is less than " + b);
else if (a > b)
  print(a + " is greater than " + b);
else
  print(a + " and " + b + " are equal.");
```

使用从字符串实例继承而来的 [`localeCompare`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare) 方法也能达到同样的效果。

# 字符串属性

## Length 属性

获取字符串的长度

## 索引

索引属性只读，不可用于赋值 @@@

如果传入负值？无效

# 字符串的方法

字符串的所有方法都不会对原字符串造成影响，因为这些都是包装类的方法，而 string 是基础数据类型 @@@

## charAt()

根据索引获取指定的字符

不如 [] 来得方便

```js
var str = "hello"
console.log(str[0]) //h
```

## charCodeAt()

**参数**

+ index：一个大于等于 0，小于字符串长度的整数。如果不是一个数值，则默认为 0。

**返回值**

+ 返回值是一表示给定索引处（String 中 index 索引处）字符的 UTF-16 代码单元值的数字；如果索引超出范围，则返回 [`NaN`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/NaN)。

```js
"ABC".charCodeAt(0) // returns 65:"A"
"ABC".charCodeAt(1) // returns 66:"B"
"ABC".charCodeAt(2) // returns 67:"C"
"ABC".charCodeAt(3) // returns NaN
```

## codePointAt() @@@

```js
'ABC'.codePointAt(1);          // 66
'\uD800\uDC00'.codePointAt(0); // 65536
```

## ~~String.fromChartCode()~~

## String.fromCodePoint() @@@

构造函数的静态方法

ES5 提供 `String.fromCharCode()` 方法，用于从 **Unicode** 码点返回对应字符，但是这个方法不能识别码点大于 `0xFFFF` 的字符。

```javascript
String.fromCharCode(0x20BB7)
// "ஷ"
```

上面代码中，`String.fromCharCode()` 不能识别大于 `0xFFFF` 的码点，所以 `0x20BB7` 就发生了溢出，最高位 `2` 被舍弃了，最后返回码点 `U+0BB7` 对应的字符，而不是码点 `U+20BB7` 对应的字符。

ES6 提供了 `String.fromCodePoint()` 方法，可以识别大于 `0xFFFF` 的字符，弥补了 `String.fromCharCode()` 方法的不足。在作用上，正好与下面的 `codePointAt()` 方法相反。

```javascript
String.fromCodePoint(0x20BB7)
// "𠮷"
String.fromCodePoint(0x78, 0x1f680, 0x79) === 'x\uD83D\uDE80y'
// true
```

上面代码中，如果 `String.fromCodePoint` 方法有多个参数，则它们会被合并成一个字符串返回。

注意，`fromCodePoint` 方法定义在 `String` 对象上，而 `codePointAt` 方法定义在字符串的实例对象上。

## toLowerCase()

将字符串转换为小写并返回

## toUpperCase()

将字符串转换为大写并返回

## slice() @@@

可以从一个**字符串中**截取指定的内容，并将截取到内容返回，不会影响原变量
数组中也有类似的同名方法

**参数**

+ beginIndex：
  + 从该索引（以 0 为基数）处开始提取原字符串中的字符。
  + 如果值为负数，会被当做 `strLength + beginIndex` 看待，这里的 `strLength` 是字符串的长度（例如， 如果 `beginIndex` 是 -3 则看作是：`strLength - 3`）
+ endIndex：
  + 可选。在该索引（以 0 为基数）处结束提取字符串，但不包括该索引处的字符串
  + 如果省略该参数，`slice()` 会一直提取到字符串末尾。 @@@
  + 如果该参数为负数，则被看作是 strLength + endIndex，这里的 strLength 就是字符串的长度 (例如，如果 endIndex 是 -3，则是, strLength - 3)。

> splice 是数组的方法，字符串要想实现删除指定索引要怎么做呢？
>
> 可以转换成数组操作，或者重新拼接

**exception**

+ 如果结束位置小于开始位置 ``str.slice(2,0)``，则返回结果空串

**描述**

+ 不会提取索引为 endIndex 处的字符串

  ```js
  '10101'.slice(0,2) // '10'
  ```

+ 理解负数索引

  ```js
  // 倒过来提取字符串的一半
  const str = '10101'
  const half = str.length>>>1
  const partOne = str.slice(0,half) // '10'，(0,2) 不包含str[2]
  const partTwo = str.slice(-half) // '01'，(5-2,len) 从str[3]到结尾，这里的endIndex必须省略
  const partTwo = str.slice(0,-3) // '10'
  ```

+ 为什么 `endIndex` 必须省略呢？因为如果结束位置小于开始位置会返回空串
+ 截图倒数第一个字符串

  ```js
  console.log('10101'.slice(-1,5)) // '1' (len-1,len)
  ```

**substr()**

+ 和 slice() 基本一致，不同的是它第二个参数不是索引，而是截取的数量
+ 非标准少用

**substring()**

+ 和 slice() 基本一致，不同的是它不能接受负值作为参数，如果设置一个负值，则会自动修正为 0，
+ substring() 中如果第二个参数小于第一个，**自动调整位置**，会交换 //1,0 == 0,1

## concat()

`concat` 方法将一个或多个字符串与原字符串连接合并，形成一个新的字符串并返回。 `concat` 方法并不影响原字符串。

拼串操作 使用 [赋值操作符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Assignment_Operators)（+, +=）代替此方法，因为性能

## trim()

**去除字符串两端的空格**

## repeat()

ES6

`repeat` 方法返回一个新字符串，表示将原字符串重复 `n` 次。

**参数**

+ **count**：介于 0 和正无穷大之间的整数 : `[0, +∞)` 。表示在新构造的字符串中重复了多少遍**原字符串**。
  + 参数如果是**小数**，会被取整 （floor 方法）
  + 但是，如果参数是 0 到 -1 之间的小数，则等同于 0，这是因为会先进行取整运算。0 到 -1 之间的小数，取整以后等于 `-0`，`repeat` 视同为 0。
  + 参数 `NaN` 等同于 0。
  + 如果 `repeat` 的参数是字符串，则会先转换成数字。

**返回值**

+ 包含指定字符串的指定数量副本的新字符串。

**Exceptions**

+ [`RangeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Errors/Negative_repetition_count): 重复次数不能为负数。
+ [`RangeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Errors/Resulting_string_too_large): 重复次数必须小于 infinity，且长度不会大于最长的字符串。

**示例**

```js
  'x'.repeat(3) // "xxx"
  'hello'.repeat(2) // "hellohello"
  'na'.repeat(0) // ""
  ```

+ 参数如果是**小数**，会被取整。

  ```js
  'na'.repeat(2.9) // "nana"
  ```

+ 但是，如果参数是 0 到 -1 之间的小数，则等同于 0，这是因为会先进行取整运算。0 到 -1 之间的小数，取整以后等于 `-0`，`repeat` 视同为 0。

  ```js
  'na'.repeat(-0.9) // ""
  ```

+ 如果 `repeat` 的参数是负数或者 `Infinity`，会报错。

  ```js
  'na'.repeat(Infinity)
  // RangeError
  'na'.repeat(-1)
  // RangeError
  ```

+ 参数 `NaN` 等同于 0。

  ```js
  'na'.repeat(NaN) // ""
  ```

+ 如果 `repeat` 的参数是字符串，则会先转换成数字。

  ```js
  'na'.repeat('na') // ""
  'na'.repeat('3') // "nanana"
  ```

## padStart()，padEnd()

ES8 新增

**padStart()** 方法用另一个字符串填充当前字符串 (重复，如果需要的话)，以便产生的字符串达到给定的长度。填充从当前字符串的开始**(左侧)**应用的。

**padEnd()** 方法会用一个字符串填充当前字符串（如果需要的话则重复填充），从当前字符串的末尾**（右侧）**开始填充。

**参数**

+ **targetLength**：当前字符串需要填充到的目标长度。
+ **padString**：填充字符串。如果字符串太长，使填充后的字符串长度超过了目标长度，则只保留最左侧的部分，其他部分会被截断。此参数的缺省值为 " "（U+0020）。

**返回值**

+ 在原字符串末尾填充指定的填充字符串直到目标长度所形成的**新字符串**。
+ 如果这个数值**小于**当前字符串的长度，则返回当前字符串本身。

# 字符串的搜索

## **indexOf()**

## **lastIndexOf()**

从一个字符串中**检索指定内容**

**参数**

+ **searchValue**：一个字符串表示被查找的值。
+ **fromIndex**：可选，默认值是 0，来表示**开始查找的位置**
  + 如果 `fromIndex < 0` 则查找整个字符串（如同传进了 0）。
  + 如果 `fromIndex >= str.length`，则该方法返回 -1。
  + 当**被查找**的字符串是一个空字符串
    + `fromIndex <= 0` 时返回 `0`，
    + `0 < fromIndex <= str.length` 时返回 `fromIndex`
    + `fromIndex > str.length` 时返回 `str.length`。

**返回值**

+ 如果找到该内容，则会返回其**第一次**出现的索引
+ 如果没有找到则返回 -1。

**描述**

+ indexOf() 是从前向后找
+ lastIndexOf() 是从后向前找

**注意**

+ 该方法区分大小写 @@@

## includes()

ES6

**includes()** 方法用于判断一个字符串是否包含在另一个字符串中，根据情况返回 true 或 false。

**参数**

+ **searchString**：要在此字符串中搜索的字符串。
+ **position**：可选，默认值是 0，来表示**开始查找的位置**
  + 如果 `fromIndex < 0` 则查找整个字符串（如同传进了 0）。
  + 如果 `fromIndex >= str.length`，则该方法返回 -1。
  + 当**被查找**的字符串是一个空字符串时，无论 position 为和值都返回 true

  > position 和 fromIndex 是两种形参名，因为他们的表现不一致

**返回值**

+ 如果当前字符串包含被搜寻的字符串，就返回 true；否则返回 false。

**注意**

+ `includes()` 方法是**区分大小写**的。例如，下面的表达式会返回 `false` ：

  ```js
  'Blue Whale'.includes('blue'); // returns false
  ```

## startsWith(), endsWith()

ES6

`endsWith()` 方法用来判断当前字符串是否是以另外一个给定的子字符串“结尾”的，根据判断结果返回 `true` 或 `false`。

**参数**

+ **searchString**：要搜索的子字符串。
+ **position**：可选。作为 `str` 的长度，默认值为 `str.length`。

**返回值**

+ 如果传入的子字符串在搜索字符串的末尾则返回 `true`；否则将返回 `false`

**注意**

+ 与 includes() 类似，大小写敏感的

# FAQ

#faq/js

## 多行字符串拼接

模板字符串, 中间的空格无法消除

![es-string](es-string.md#长字符串)

数组 join

## 在字符串索引处插入

转化为数组，使用 splice(index,0,str)

使用 slice()，截断再拼接

## 删除 String 里某个字符的方法

使用 replace

```js
var str = "abcdaabbssaaa";
var reg = new RegExp("a","g");
var a = str.replace(reg,"");
console.log(a);
```

使用 split

```js
var str = "abcdaabbssaaa";
var a = str.split("a").join("");
console.log(a);
```

## 删除指定索引的字符

转换为数组

```js
var result = S.split('');
result.splice(bindex, eindex - bindex);
S = result.join('');
```

重新拼接

## 生成一段填充字符串

先调用数组的 fill 方法，再转换成字符串

使用 Array.from()

```js
Array.from({ length: 2 }, () => 'jack')
// ['jack', 'jack']
```

使用 ES8 的 padStart/End 方法

**推荐方案**: 使用 repeat 方法

```js
'x'.repeat(3) // "xxx"
'hello'.repeat(2) // "hellohello"
'na'.repeat(0) // ""
```

## 字典排序

根据字母的 unicode 编码调整字符串的字母位置

https://leetcode.cn/problems/orderly-queue/submissions/

字符串是可以直接比较大小和 sort 的，默认就是按照 unicode 编码来比较

k>2 其实就是冒泡排序

## 字符串首字母大写：capitalize

情况有：

1. 一个句子中所有的单词的首字母大写
2. 一个句子中只有第一个单词的首字母需要大写
3. 只有一个单词，然后这个单词首字母大写即可，情况三同时是 1 和 2 的子集
4. 相似但完全不同的 case：页面中的样式，实现上述的效果，直接使用 text-transform: capitalize; 即可

exception：

1. 参数有可能是 undefine 或者 null
2. 参数是否有 charAt replace 等方法，还是直接判断是否是字符串最直接，如果不是字符串返回原值

通过函数重载处理

```ts
function capitalize(val: string): string;
function capitalize<T>(val: T): T;
function capitalize<T>(val: string | T): string | T {
    if(typeof val === 'string') {
        _capitalize(val);
    }
    else {
        return val;
    }
}
```

### 情况二

方法也不少试一下哪个效率好一点

方法一

```js
function _capitalize(str) {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

function _capitalize(str) {
    return str[0].toUpperCase() + str.slice(1);
}
```

方法二：非空首字母

```js
function firstUpperCase(val) {
    return val.replace(/^\S/, function (s) {
        return s.toUpperCase();
    });
}
```

```js
// 10,889,187 operations/sec
function capitalizeFirstLetter(string) {
    return string[0].toUpperCase() + string.slice(1);
}

// 10,875,535 operations/sec
function capitalizeFirstLetter(string) {
    return string.charAt(0).toUpperCase() + string.slice(1);
}

// 4,632,536 operations/sec
function capitalizeFirstLetter(string) {
    return string.replace(/^./, string[0].toUpperCase());
}

// 1,977,828 operations/sec
String.prototype.capitalizeFirstLetter = function() {
    return this.charAt(0).toUpperCase() + this.slice(1);
}
```

## 字符串大小写判断

![520. 检测大写字母](../../../basic/leetcode/520.%20检测大写字母.md)

## 字符串压缩

存粹算法上的压缩，原理不太清楚，但是压缩效率是有限的，把字符串压缩成二进制 buffer 存储

服务器的短链接，本质是键值对，用一个短链接对应一个存储在数据库的长连接，并没有真正的节省空间

[535. TinyURL 的加密与解密 - 力扣（LeetCode）](https://leetcode.cn/problems/encode-and-decode-tinyurl/)