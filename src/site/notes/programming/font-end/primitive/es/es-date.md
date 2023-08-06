---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/es/es-date/"}
---


<https://segmentfault.com/a/1190000007581722>

日期的对象，在 JS 中通过 Date 对象来表示一个时间

Date 对象基于 1970 年 1 月 1 日（世界标准时间）起的毫秒数。

Date 对象的范围是 -100,000,000 天至 100,000,000 天（等效的毫秒值）

```js
new Date(); // Fri Apr 12 2019 19:53:20 GMT+0800 (GMT+08:00) {}
```

> 虽然是一个对象，但是表现形式和普通对象不同：
>
> 1. 使用 console.log 无法像普通对象那样查看属性方法，原型
> 2. 使用 console.dir 则 ok

使用 `Number` 转换 `Date` 对象

```js
var d = new Date("December 17, 1995 03:24:00");
print(Number(d));
```

这将输出 "819199440000"。

# 常用术语

## 时间戳

Unix 时间戳表示当前时间到 1970 年 1 月 1 日 00:00:00 UTC 对应的秒数。注意，JavaScript 内的时间戳指的是当前时间到 1970 年 1 月 1 日 00:00:00 UTC 对应的毫秒数，和 unix 时间戳不是一个概念，后者表示秒数，差了 1000 倍。

## 当地时间

## 世界协调时

协调世界时，又称世界统一时间、世界标准时间、国际协调时间。由于英文（CUT）和法文（TUC）的缩写不同，作为妥协，简称 UTC

中国大陆、中国香港、中国澳门、中国台湾、蒙古国、新加坡、马来西亚、菲律宾、西澳大利亚州的时间与 UTC 的 [时差](https://baike.baidu.com/item/时差/1305648) 均为 +8，也就是 UTC+8。

## TimeZone&UTC Offsets: 时区与偏移

人们经常会把时区与 UTC 偏移量搞混，UTC 偏移量代表了某个具体的时间值与 UTC 时间之间的差异，通常用 HH:mm 形式表述。而 TimeZone 则表示某个地理区域，某个 TimeZone 中往往会包含多个偏移量，而多个时区可能在一年的某些时间有相同的偏移量。譬如 America/Chicago, America/Denver, 以及 America/Belize 在一年中不同的时间都会包含 -06:00 这个偏移。

## 日期字符串

[IETF-compliant RFC 2822 timestamps](http://tools.ietf.org/html/rfc2822#page-14)

[version of ISO8601](http://www.ecma-international.org/ecma-262/5.1/#sec-15.9.1.15)）

RFC2822 标准日期字符串：

```js
YYYY/MM/DD HH:MM:SS ± timezon(时区用4位数字表示)
// eg 1992/02/12 12:23:22+0800
```

ISO 8601 标准日期字符串：日期和时间中间的 `T` 不可以被省略，一省略就出错。

```js
 YYYY-MM-DDThh:mm:ss ± timezone(时区用HH:MM表示)

 1997-07-16T08:20:30Z
 // “Z”表示UTC标准时区，即"00:00",所以这里表示零时区的`1997年7月16日08时20分30秒`
 //转换成位于东八区的北京时间则为`1997年7月17日16时20分30秒`

 1997-07-16T19:20:30+01:00
 // 表示东一区的1997年7月16日19时20秒30分，转换成UTC标准时间的话是1997-07-16T18:20:30Z
```

# Date 构造函数

## 不传参数

如果没有输入任何参数，则 Date 的构造器会依据 **系统设置** 的当前时间来创建一个 Date 对象。

```js
new Date(); // Fri Apr 12 2019 19:53:20 GMT+0800 (GMT+08:00) {}
```

## 单个参数

**timestamp**：代表自 1970 年 1 月 1 日 00:00:00 (世界标准时间) 起经过的 **毫秒数**。

```js
new Date(timestamp)
```

### dateString

**dateString**：表示日期的 **字符串值**。该字符串应该能被 [`Date.parse()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/parse) 方法识别（符合 [IETF-compliant RFC 2822 timestamps](http://tools.ietf.org/html/rfc2822#page-14) 或 [version of ISO8601](http://www.ecma-international.org/ecma-262/5.1/#sec-15.9.1.15)）。

```js
new Date(dateString)
```

> **注意:** 由于浏览器之间的差异与不一致性，强烈不推荐使用 `Date` 构造函数来解析日期字符串 (或使用与其等价的 `Date.parse`)。对 RFC 2822 格式的日期仅有约定俗称的支持。 对 ISO 8601 格式的支持中，仅有日期的串 (例如 "1970-01-01") 会被处理为 UTC 而不是本地时间，与其他格式的串的处理不同。

## 多个参数

1. **year**：代表年份的整数值。为了避免 2000 年问题最好指定 4 位数的年份; 使用 `1998`, 而不要用 `98`.
2. **month**：代表月份的整数值从**(0-11)**

> 月份没有 0，却依然是从 0 开始，11 代表 Dec
>
> **自动调整**

3. **date**: 代表一个月中的第几天的整数值 (1-31)

> **自动调整**，如果输入 0，月份会变成上一个月的最后一天，如果输入 -1，则是上个月的最后一天再往前一天，即是倒数第二天
>
> 在 edge 试了一下是从 0 开始的

4. **hour**: 代表一天中的小时数的整数值 (0-23)

> **自动调整**，如果输入 24，会变成明天的零点

5. **minute**：分钟数（0-59）

> 以下的参数也都会自动调整

6. **second**：秒数（0-59）
7. **millisecond**：表示时间的毫秒部分的整数值

如果提供了至少两个参数，其余的参数均会 **默认设置为 1**（如果没有提供 day 参数）或者 0。

> 当 Date 作为构造函数调用并传入多个参数时，所定义参数代表的是 **当地时间**。如果需要世界协调时, 再想怎么处理

## **注意**

需要注意的是只能通过调用 Date 构造函数来实例化日期对象：以常规函数调用它（即不加 [new](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new) 操作符）将会返回一个字符串，而不是一个日期对象。另外，不像其他 JavaScript 类型，**Date 对象没有字面量格式**

接受一个溢出的 **日期字符串**，只能向后溢出一天

```js
console.log(new Date('2019-9-0')) //  Invalid Date {}
console.log(new Date('2019-9-31')) // Tue Oct 01 2019 00:00:00 GMT+0800 (GMT+08:00) {}
console.log(new Date('2019-9-32')) // Invalid Date {}
```

接受多个参数，注意 0base 的月份，1base 的日期

```js
console.log(new Date(2019,9,-62)) // 7-30
console.log(new Date(2019,9,-5)) // 9-25
console.log(new Date(2019,9,0)) // 9-30
console.log(new Date(2019,9,31)) // 10-31
console.log(new Date(2019,9,62)) // 11-01
```

**Date.length**

`Date.length` 的值是 7。这是该构造函数可接受的参数个数。

# Date 静态方法

## Date.now()

**Date.now()** 方法返回自 1970 年 1 月 1 日 00:00:00 UTC 到当前时间的毫秒数。

## Date.parse()

`Date.parse()` 方法解析一个表示某个日期的字符串，并返回从 1970-1-1 00:00:00 UTC 到该日期对象（该日期对象的 UTC 时间）的毫秒数

如果该字符串无法识别，或者一些情况下，包含了不合法的日期数值（如：2015-02-31），则返回值为 NaN。

不推荐在 ES5 之前使用 Date.parse 方法，因为字符串的解析完全取决于实现。直到至今，不同宿主在如何解析日期字符串上仍存在许多差异，因此最好还是手动解析日期字符串（在需要适应不同格式时库能起到很大帮助）。

**显式调用**

```js
Date.parse(dateString)
```

**隐式调用**

```js
new Date(dateString)
```

**参数**

**dateString**：一个符合 [RFC2822](http://tools.ietf.org/html/rfc2822#page-14) 或 ISO 8601 日期格式的字符串（其他格式也许也支持，但结果可能与预期不符）。

**返回值**

一个表示从 1970-1-1 00:00:00 UTC 到给定日期字符串所表示时间的毫秒数的数值。如果参数不能解析为一个有效的日期，则返回 [`NaN`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/NaN)。

**描述**

由于在解析日期字符串时存在偏差会导致结果不一致，因此推荐始终手动解析日期字符串，特别是不同的 ECMAScript 实现会把诸如“2015-10-12 12:00:00”的字符串解析为 NaN，UTC 或者本地时间。

## Date.UTC()

`Date.UTC()` 方法接受的参数同日期构造函数接受最多参数时一样，返回从 1970-1-1 00:00:00 UTC 到指定日期的的毫秒数。

**返回值**

`Date.UTC` 方法返回一个时间数值，而不是一个日期对象。

**描述**

同样的，如果有一个指定的参数超出其合理范围，则 UTC 方法会通过更新其他参数直到该参数在合理范围内。例如，为月份指定 15，则年份将会加 1，然后月份将会使用 3。

# Date 实例的 Getter

## getFullYear()

从 Date 对象以四位数字返回年份。

## getMonth()

返回当前日期对象的月份（0-11）

## getDate() @@@

当前日期对象是几日（1-31）

## getDay()

返回当前日期对象时周几（0-6）

0 周日，那又相当于是 1base 了，总共只有两个是 1base 的 @@@

## getHours()

返回 Date 对象的小时 (0 ~ 23)。

## getMinutes()

返回 Date 对象的分钟 (0 ~ 59)。

## getSeconds()

返回 Date 对象的秒数 (0 ~ 59)。

## getMilliseconds()

返回 Date 对象的毫秒 (0 ~ 999)。

## getTime()

返回当前日期对象的时间戳

时间戳，指的是从 1970 年月 1 日 0 时 0 分 0 秒，到现在时间的毫秒数

计算机底层保存时间都是以时间戳的形式保存的。

有时区问题，这是格林威治标准时间的 1 月 1 日，算上与中国的 8 小时时差，会返回负值

```js
var date = new Date(“1/1/1970 0:0:0”);
console.log(date);//-28800000
```

## getTimezoneOffset()

## getUTCFullYears/Date...()

# Date 实例的 Setter

## 注意

没有 setDay

## 通性

会影响原来的 date 对象，date 对象会自动调整

返回值是 number 类型的时间戳

## setDate() @@@

根据 **本地时间** 来指定一个日期对象的天数。

接受一个整数，表示该月的第几天。

```js
var theBigDay = new Date(1962, 7, 7); // 1962-07-07
theBigDay.setDate(24);  // 1962-07-24
theBigDay.setDate(32);  // 1962-08-01
```

如果大于当月的天数，会自动进位到下一个月

如果输入一个负数，会自动减少相应的天数，如果需要则退回上一个月，返回值是毫秒数

## setHours()

设置 date 的时间

```js
const deadline = new Date('2019-10-12') // 0800
// deadline.setUTCHours(6)
deadline.setHours(6)
console.log(deadline) // 0600
```

## setUTCHours()

以 UTC 时间为基准，加上参数，如

```js
const deadline = new Date('2019-10-12') // 0800
deadline.setUTCHours(6)
// deadline.setHours(6)
console.log(deadline) // 1400
```

## setFullYeas()

`setFullYear()` 方法根据本地时间为一个日期对象设置年份。

```js
dateObj.setFullYear(yearValue[, monthValue[, dayValue]])
```

**注意**

如果没有指定 `monthValue` 和 `dayValue` 参数，将会使用 `getMonth` 和 `getDate` 方法的返回值。

```js
console.log(new Date())
console.log(new Date(2019)) // timestamp
const date = new Date()
date.setFullYear(2019)
console.log(date)
/*
Sat Oct 05 2019 19:28:49 GMT+0800 (GMT+08:00) {}
Thu Jan 01 1970 08:00:02 GMT+0800 (GMT+08:00) {}
Sat Oct 05 2019 19:28:49 GMT+0800 (GMT+08:00) {}
*/
```

## *setYears

因为 2000 年问题，已经被舍弃

# Date 字符化

Date 对象提供了一系列的 `to*` 方法来支持从 Date 对象转化为字符串

```js
var d = new Date(2013, 0, 1);

d.toString()
// "Tue Jan 01 2013 00:00:00 GMT+0800 (CST)"

d.toUTCString()
// "Mon, 31 Dec 2012 16:00:00 GMT"

d.toISOString()
// "2012-12-31T16:00:00.000Z"，后退了一天，时区发生了变化

d.toJSON()
// "2012-12-31T16:00:00.000Z"，后退了一天，时区发生了变化

d.toDateString() // "Tue Jan 01 2013"

d.toTimeString() // "00:00:00 GMT+0800 (CST)"


```

## Local 系列

```js
d.toLocaleDateString()
// 中文版浏览器为"2013年1月1日"
// 英文版浏览器为"1/1/2013"
d.toLocaleTimeString()
// 中文版浏览器为"上午12:00:00"
// 英文版浏览器为"12:00:00 AM"
```

## toLocaleDateString()

https://juejin.im/entry/572b04b72e958a0069542a76

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat

## Date 类型转换常见日期格式

快速将 Date 类型转换成 YYYY-MM-DD 的格式

大错误，时区发生了变化

```js
from.toJSON().split('T')[0]
```

## YYYY-M-D

先转换成 Date，再转换成 YYYY-MM-DD

## Date String

+ <https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date>
+ <https://juejin.im/entry/572b04b72e958a0069542a76>
+ <https://juejin.im/entry/57c917996be3ff006a7b0af8>

# FAQ

#faq/js

## 注意

用户的输入是 1base，方法的参数是 0base @@@

变量名称统一用 XXXDate 代表 Date 实例

用 day 表示日期中的某一天

统一用 XXXWeekDay 代表星期几

## 比较日期大小

可以直接通过 比较运算符 比较大小

date.getTIme()

获取时间戳之后比较

## 字符串 Format 日期比较

两者格式不同 YYYY-M-D YYYY-MM-DD

new Date() 的结果也不同，YYYY-MM-DD 会被当成 UTC，多了 8 个小时，无法比较

## 正确的格式化 YYYY-MM-DD

 ```ts
 new Date(prevSat).toJSON().split('T')[0]
 ```

## 前后交互的时候如何处理 Month

因为 month 是 0 base 的，但是日常理解是 1 base 的 ，很麻烦

决定保持前后端统一，为了更好理解，date 的 0 base 全部转换成 1 base 的处理

## 获取上一个星期六

先获取今天是星期几，然后通过 getDate 去处理

## Date 对象增加一天

先 getDate，再 setDate+1

## Date 加减法

减法返回的是时间戳的差值，返回值是 number 类型的

加法的结果是拼串

## 判断闰年

```js
function isLeap(n){
  if（(n%4 === 0 && n%100 != 0) || n %400 === 0）return true
}
```

当我们遇到这个问题时，大家都知道该怎么计算，那就是所谓的：四年一闰，百年不闰，四百年再闰。

关于公历闰年是这样规定的：

- 地球绕太阳公转一周叫做一回归年，一回归年长 365 日 5 时 48 分 46 秒。
- 因此，公历规定有平年和闰年，平年一年有 365 日，比回归年短 0.2422 日，四年共短 0.9688 日，故每四年增加一日，这一年有 366 日，就是闰年。
- 但四年增加一日比四个回归年又多 0.0312 日,400 年后将多 3.12 日,故在 400 年中少设 3 个闰年,也就是在 400 年中只设 97 个闰年，这样公历年的平均长度与回归年就相近似了。
- 由此规定：年份是整百数的必须是 400 的倍数才是闰年，例如 1900 年、2100 年就不是闰年。

## 获得某个月的天数/某个月的最后一天

### 映射法

```js
const EVERY_MONTH_DAYS = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];

function getDays(year, month) {
  if (month === 1 && isLeap(year)) return 29;
  return EVERY_MONTH_DAYS[month];
}
```

手动做了每个月天数的映射，如果是 2 月份并闰年，那天数 +1

### 利用自动调整

利用 Date 的自动调整，对于非 2 月，判断 30+1，之后还是否是同一个月

```js
function getDays(year, month) {
  // 输入是1base，参数是0base
  const date = new Date(year,month-1)
  if(month==2){
    //2月
    date.setDate(29)
    return date.getMonth()==month-1?29:28
  }else{
    //非2月
    date.setDate(31)
    return date.getMonth()==month-1?31:30
  }
}
console.log(getDays(2016,2))
```

### 利用自动调整@@@推荐

利用 Date 的自动调整，当接受 0 的时候，自动回退到上个月的最后一天

```js
function getDays(year, month) {
  // 输入是1base，参数是0base
  return new Date(year,month,0).getDate()
}
console.log(getDays(2016,2))
```

### 获取某个月的第一天

```js
new Date(year,month-1,1)
```

### 时间戳转换为日期

使用构造函数

```js
new Date(timestamp)
```

date 的 setter 系列的返回值是 number 类型，常常用此方法转换

通过数学操作，转换为小时，分钟，毫秒
