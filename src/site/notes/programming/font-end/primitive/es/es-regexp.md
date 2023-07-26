---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/es/es-regexp/"}
---


用的太少了, 工作了两年几乎没用过, 而且很难理解, 考前突袭不要花太多时间在这上面了

# 正则表达式

正则表达式是独立于 JS 的一种通用模式，许多的字符都和 JS 相同，不可以混记

正则表达式是匹配模式，要么匹配字符，要么匹配位置。请记住这句话。

## 复数含义的字符

### ^

+ 脱字符，表示开头的位置
+ 匹配组中表示**除了**

### ?

+ 量词，代表 0 次或者 1 次
+ 贪婪匹配标识

## 修饰符

ES5 中修饰符，共 3 个：

+ `g` 全局匹配，即找到所有匹配的，单词是 global, 子字符串也会检查是否符合
+ `i` 忽略字母大小写, 单词 ingoreCase
+ `m` 多行匹配，只影响 `^` 和 `$`，二者变成行的概念，即行开头和行结尾。单词是 multiline

当然正则对象也有相应的只读属性：

```js
var regex = /\w/img;
console.log( regex.global );
console.log( regex.ignoreCase );
console.log( regex.multiline );
// => true
// => true
// => true
```

# 字符匹配

如果正则只有精确匹配是没多大意义的，比如 `/hello/`，也只能匹配字符串中的 "hello" 这个子串。

## 横向模糊匹配

横向模糊指的是，一个正则可匹配的字符串的**长度**不是固定的，可以是多种情况的。

其实现的方式是**使用量词**。譬如 `{m,n}`，表示连续出现最少 m 次，最多 n 次。

## 纵向模糊匹配

纵向模糊指的是，一个正则匹配的字符串，具体到某一位字符时，它可以不是某个确定的字符，可以有多种可能。

其实现的方式是使用**字符组**。譬如 `[abc]`，表示该字符是可以字符“a”、“b”、“c”中的任何一个。

## 字符组

需要强调的是，虽叫字符组（字符类），但只是其中一个字符。例如 `[abc]`，表示匹配一个字符，它可以是“a”、“b”、“c”之一。

### 范围表示法

如果字符组里的字符特别多的话，怎么办？可以使用范围表示法。

比如 `[123456abcdefGHIJKLM]`，可以写成 `[1-6a-fG-M]`。用连字符 `-` 来省略和简写。

> 因为连字符有特殊用途，那么要匹配“a”、“-”、“z”这三者中任意一个字符，该怎么做呢？
>
> 不能写成 `[a-z]`，因为其表示小写字符中的任何一个字符。
>
> 可以写成如下的方式：`[-az]` 或 `[az-]` 或 `[a\-z]`。即要么放在开头，要么放在结尾，要么转义。总之不会让引擎认为是范围表示法就行了。

### 排除字符组

纵向模糊匹配，还有一种情形就是，某位字符可以是任何东西，但就不能是 "a"、"b"、"c"。

此时就是排除字符组（**反义字符组**）的概念。例如 `[^abc]`，表示是一个除 "a"、"b"、"c" 之外的任意一个字符。字符组的第一位放 `^`（脱字符），表示求反的概念。

当然，也有相应的范围表示法

> 脱字符还可以用于匹配位置，请看清楚功能

### 常见的简写形式

有了字符组的概念后，一些常见的符号我们也就理解了。因为它们都是系统自带的简写形式。

**\d**：`[0-9]`。表示是一位数字。

> 记忆方式：其英文是 digit（数字）。

**\D**：`[^0-9]`。表示除数字外的任意字符。

**\w**：`[0-9a-zA-Z_]`。表示数字、大小写字母和下划线。

> 记忆方式：w 是 word 的简写，也称单词字符
>
> 下划线被认为是单词的一部分

**\W**：`[^0-9a-zA-Z_]`。非单词字符。

**\s**：`[ \t\v\n\r\f]`。表示空白符，包括空格、水平制表符、垂直制表符、换行符、回车符、换页符。

> 记忆方式：s 是 space character 的首字母。

**\S**：`[^ \t\v\n\r\f]`。 非空白符。

> 实际使用时，常常用空格作为单词的分界

**.** 就是 `[^\n\r\u2028\u2029]`。通配符，表示几乎任意字符。换行符、回车符、行分隔符和段分隔符除外。

> 注意：. 是 JS 访问属性的形式，想要准确匹配需要转义
>
> 记忆方式：想想省略号...中的每个点，都可以理解成占位符，表示任何类似的东西。

```js
var str = '..[]..'
console.log(str.match(/..../)) // '..[]'
console.log(str.match(/\.\.\.\./)) // null
```

**所有字符：**`[\s\S]`，比起 `.` 更加准确，可以获取全部的字符

```js
// 用于消除模板内的注释
// san/parse-template
source = source.replace(/<!--([\s\S]*?)-->/mg, '').replace(/(^\s+|\s+$)/g, '');
```

## 量词

量词也称重复。掌握 `{m,n}` 的准确含义后，只需要记住一些简写形式。

### 简写形式

`{m,}` 表示至少出现 m 次。

`{m}` 等价于 `{m,m}`，表示出现 m 次。

`?` 等价于 `{0,1}`，表示出现或者不出现。

> 记忆方式：问号的意思表示，有吗？

`+` 等价于 `{1,}`，表示出现至少一次。

> 记忆方式：加号是追加的意思，得先有一个，然后才考虑追加。

`*` 等价于 `{0,}`，表示出现任意次，有可能不出现。

> 记忆方式：看看天上的星星，可能一颗没有，可能零散有几颗，可能数也数不过来。

## 贪婪匹配和惰性匹配

先看看下面的例子

```js
var regex = /\d{2,5}/g;
var string = "123 1234 12345 123456";
console.log( string.match(regex) ); 
// => ["123", "1234", "12345", "12345"]
```

其中正则 `/\d{2,5}/`，表示数字连续出现 2 到 5 次。会匹配 2 位、3 位、4 位、5 位连续数字。

但是其是贪婪的，它会尽可能多的匹配。你能给我 6 个，我就要 5 个。你能给我 3 个，我就 3 个都要。反正只要在能力范围内，越多越好。

我们知道有时贪婪不是一件好事（请看文章最后一个例子）。而惰性匹配，就是尽可能少的匹配：

```js
var regex = /\d{2,5}?/g;
var string = "123 1234 12345 123456";
console.log( string.match(regex) ); 
// => ["12", "12", "34", "12", "34", "12", "34", "56"]
```

其中 `/\d{2,5}?/` 表示，虽然 2 到 5 次都行，当 2 个就够的时候，就不在往下尝试了

> 如果后面的无法匹配了，此时惰性匹配会再尝试多匹配一点，看最后能否匹配成功

通过在**量词**后面加个**问号**就能实现惰性匹配，因此所有惰性匹配情形如下：

+ `{m,n}?`
+ `{m,}?`
+ `??`
+ `+?`
+ `*?`

对惰性匹配的记忆方式是：量词后面加个问号，问一问你知足了吗，你很贪婪吗？

## 多选分支

一个模式可以实现横向和纵向模糊匹配。而多选分支可以支持多个子模式任选其一。

具体形式如下：`(p1|p2|p3)`，其中 `p1`、`p2` 和 `p3` 是子模式，用 `|`（**管道符**）分隔，表示其中任何之一。

例如要匹配 "good" 和 "nice" 可以使用 `/good|nice/`。测试如下：

```js
var regex = /good|nice/g;
var string = "good idea, nice try.";
console.log( string.match(regex) ); 
// => ["good", "nice"]
```

但有个事实我们应该注意，比如我用 `/good|goodbye/`，去匹配 "goodbye" 字符串时，结果是 "good"：

```js
var regex = /good|goodbye/g;
var string = "goodbye";
console.log( string.match(regex) ); 
// => ["good"]
```

而把正则改成 `/goodbye|good/`，结果是：

```js
var regex = /goodbye|good/g;
var string = "goodbye";
console.log( string.match(regex) ); 
// => ["goodbye"]
```

也就是说，分支结构也是**惰性**的，即当前面的匹配上了，后面的就不再尝试了。

## 匹配字符的案例

匹配字符，无非就是字符组、量词和分支结构的组合使用罢了。

下面找几个例子演练一下（其中，每个正则并不是只有唯一写法）

### 匹配 16 进制颜色值

```js
#ffbbad

#Fc01DF

#FFF

#ffE
```

**分析**

+ 表示一个**16 进制字符**，可以用字符组 `[0-9a-fA-F]`。
+ 其中字符可以出现 3 或 6 次，需要是用量词和分支结构。
+ 使用分支结构时，需要注意顺序。

正则如下：

```js
var regex = /#([0-9a-fA-F]{6}|[0-9a-fA-F]{3})/g;
var string = "#ffbbad #Fc01DF #FFF #ffE";
console.log( string.match(regex) ); 
// => ["#ffbbad", "#Fc01DF", "#FFF", "#ffE"]
```

### 匹配时间

```js
23:59

02:07
```

**分析**

+ 共 4 位数字，第一位数字可以为 `[0-2]`。
+ 当第 1 位为 2 时，第 2 位可以为 `[0-3]`，其他情况时，第 2 位为 `[0-9]`。
+ 第 3 位数字为 `[0-5]`，第 4 位为 `[0-9]`
+ 正则如下：

    ```js
    var regex = /^([01][0-9]|[2][0-3]):[0-5][0-9]$/;
    console.log( regex.test("23:59") ); 
    console.log( regex.test("02:07") ); 
    // => true
    // => true
    ```

+ 如果也要求匹配 7:9，也就是说时分前面的 0 可以省略。此时正则变成：

    ```js
    var regex = /^(0?[0-9]|1[0-9]|[2][0-3]):(0?[0-9]|[1-5][0-9])$/;
    console.log( regex.test("23:59") ); 
    console.log( regex.test("02:07") ); 
    console.log( regex.test("7:9") ); 
    // => true
    // => true
    // => true
    ```

### 匹配日期

YYYY-MM-DD

**分析**

+ 年，四位数字即可，可用 `[0-9]{4}`。
+ 月，共 12 个月，分两种情况 01、02、……、09 和 10、11、12，可用 `(0[1-9]|1[0-2])`。
+ 日，最大 31 天，可用 `(0[1-9]|[12][0-9]|3[01])`。
+ 正则如下：

    ```js
    var regex = /^[0-9]{4}-(0[1-9]|1[0-2])-(0[1-9]|[12][0-9]|3[01])$/;
    console.log( regex.test("2017-06-10") ); 
    // => true
    ```

### Window 操作系统文件路径

```js
F:\study\javascript\regex\regular expression.pdf

F:\study\javascript\regex\

F:\study\javascript

F:\
```

**分析：**

+ 整体模式是: `盘符:\文件夹\文件夹\文件夹\`
+ 其中匹配 `F:\`，需要使用 `[a-zA-Z]:\\`，其中盘符不区分大小写，注意 `\` 字符需要转义。
+ 文件名或者文件夹名，不能包含一些特殊字符，此时我们需要排除字符组 `[^\\:*<>|"?\r\n/]` 来表示合法字符。
+ 另外不能为空名，至少有一个字符，也就是要使用量词 `+`。因此匹配 `文件夹\`，可用 `[^\\:*<>|"?\r\n/]+\\`。
+ 另外 `文件夹\`，可以出现任意次。也就是 `([^\\:*<>|"?\r\n/]+\\)*`。其中括号提供子表达式。
+ 路径的最后一部分可以是 `文件夹`，没有 `\`，因此需要添加 `([^\\:*<>|"?\r\n/]+)?$`。
+ 最后拼接成了一个看起来比较复杂的正则：

    ```js
    var regex = /^[a-zA-Z]:\\([^\\:*<>|"?\r\n/]+\\)*([^\\:*<>|"?\r\n/]+)?$/;
    console.log( regex.test("F:\\study\\javascript\\regex\\regular expression.pdf") ); 
    console.log( regex.test("F:\\study\\javascript\\regex\\") ); 
    console.log( regex.test("F:\\study\\javascript") ); 
    console.log( regex.test("F:\\") ); 
    // => true
    // => true
    // => true
    // => true
    ```

### 匹配 Id

```js
<div id="container" class="main"></div>
```

提取出 id="container"。

可能最开始想到的正则是：

```js
var regex = /id=".*"/
var string = '<div id="container" class="main"></div>';
console.log(string.match(regex)[0]); 
// => id="container" class="main"
```

因为 `.` 是通配符，本身就匹配双引号的，而量词 `*` 又是贪婪的，当遇到 container 后面双引号时，不会停下来，会继续匹配，直到遇到最后一个双引号为止。

解决之道，可以使用惰性匹配：

```js
var regex = /id=".*?"/
var string = '<div id="container" class="main"></div>';
console.log(string.match(regex)[0]); 
// => id="container"
```

当然，这样也会有个问题。效率比较低，因为其匹配原理会涉及到“回溯”这个概念（这里也只是顺便提一下，第四章会详细说明）。可以优化如下：

```js
var regex = /id="[^"]*"/
var string = '<div id="container" class="main"></div>';
console.log(string.match(regex)[0]); 
// => id="container"
```

### queryString 转化为参数对象

```js
const url='http:www.baidu.com/index?name=username&age=27&pwd=zbc|123@&likes=lol&likes=beautifull girl&$id=main#flag=66';
```

`?` 之后开始属于查询字符串的部分

每对名值对被 `&` 分割，名和值之间以等号连接

还要排除片段标识符 `#`

```js
const reg=/[?&]([^=&#]+)=([^&#]*)/g
```

```js
const url='http:www.baidu.com/index?name=username&age=27&pwd=zbc|123@&likes=lol&likes=beautifull girl&$id=main#flag=66';
function parseUrl(url){
  const reg=/[?&]([^=&#]+)=([^&#]*)/g
  // Array(6) ["?name=username", "&age=27", "&pwd=zbc|123@", "&likes=lol", "&likes=beautifull girl", "&$id=main"]
  const querys = url.match(reg)
  const queryObj = {}
  for (const val of querys) {
    const query = val.split('='),
          key = query[0].slice(1),
          value = query[1]
    if(queryObj[key]){
      // 属性已经存在，说明有多个值，令其为数组
      queryObj[key] = [].concat(queryObj[key],value)
    }else{
      // 新的属性，直接赋值
      queryObj[key] = value
    }
  }
  return queryObj
}
console.log(parseUrl(url))
```

### 读取 queryString 中某个属性的值

```js
const url='http:www.baidu.com/index?name=username&age=27&pwd=zbc|123@&likes=lol&likes=beautifull girl&$id=main#flag=66';

function getQueryByName(url,name){
  // 对name进行转义
  name = name.replace(/(?=\W)/g,'\\')
  const reg= new RegExp('[?&]'+name+'=([^&#]*)')
  const query=url.match(reg)
  return query?query[1]:null
}
const value=getQueryByName(url,'$id')
console.log(value)
```

# 位置匹配

正则表达式是匹配模式，要么匹配字符，要么匹配位置。请记住这句话。

然而大部分人学习正则时，对于匹配位置的重视程度没有那么高。

## 位置的定义

位置是相邻字符之间的位置。比如，下图中箭头所指的地方：

![1554862898754](/img/user/programming/font-end/primitive/es/es-regexp/1554862898754.png)

## 锚字符

在 ES5 中，共有 6 个锚字符：

```js
^ $ \b \B (?=p) (?!p)
```

### **^和 $**

`^`（脱字符）匹配开头，在多行匹配中匹配行开头，每一行只能有一个开头

`$`（美元符号）匹配结尾，在多行匹配中匹配行结尾，每一行只能一个结尾

比如我们把字符串的开头和结尾用 "#" 替换（位置可以替换成字符的！）：

```js
var result = "hello".replace(/^|$/g, '#');
console.log(result); 
// => "#hello#"
```

多行匹配模式时，二者是行的概念，这个需要我们的注意：

```js
var result = "I\nlove\njavascript".replace(/^|$/gm, '#');
console.log(result);
/*
#I#
#love#
#javascript#
*/
```

### **\b 和\B**

`\b` 是单词边界，具体就是 `\w` 和 `\W` 之间的位置，也包括 `\w` 和 `^` 之间的位置，以及 `\w` 和 `$` 之间的位置。

比如一个文件名是 `[JS] Lesson_01.mp4` 中的 `\b`，如下：

```js
var result = "[JS] Lesson_01.mp4".replace(/\b/g, '#');
console.log(result); 
// => "[#JS#] #Lesson_01#.#mp4#"
```

为什么是这样呢？这需要仔细看看。

首先，我们知道，`\w` 是字符组 `[0-9a-zA-Z_]` 的简写形式，即 `\w` 是字母数字或者下划线的中任何一个字符。而 `\W` 是排除字符组 `[^0-9a-zA-Z_]` 的简写形式，即 `\W` 是 `\w` 以外的任何一个字符。

此时我们可以看看 `[#JS#] #Lesson_01#.#mp4#` 中的每一个 `#`，是怎么来的。

+ 第一个 "#"，两边是 `[` 与 `J`，是 `\W` 和 `\w` 之间的位置。
+ 第二个 "#"，两边是 "S" 与 "]"，也就是 `\w` 和 `\W` 之间的位置。
+ 第三个 "#"，两边是空格与 "L"，也就是 `\W` 和 `\w` 之间的位置。
+ 第四个 "#"，两边是 "1" 与 "."，也就是 `\w` 和 `\W` 之间的位置。
+ 第五个 "#"，两边是 "." 与 "m"，也就是 `\W` 和 `\w` 之间的位置。
+ 第六个 "#"，其对应的位置是结尾，但其前面的字符 "4" 是 `\w`，即 `\w` 和 `$` 之间的位置。

知道了 `\b` 的概念后，那么 `\B` 也就相对好理解了。

`\B` 就是 `\b` 的反面的意思，非单词边界。例如在字符串中所有位置中，扣掉 `\b`，剩下的都是 `\B` 的。

具体说来就是 `\w` 与 `\w`、`\W` 与 `\W`、`^` 与 `\W`，`\W` 与 `$` 之间的位置。

比如上面的例子，把所有 `\B` 替换成 "#"：

```js
var result = "[JS] Lesson_01.mp4".replace(/\B/g, '#');
console.log(result); 
// => "#[J#S]# L#e#s#s#o#n#_#0#1.m#p#4"
```

### 先行断言 (?=p) 和 (?!p)

`(?=p)`，其中 `p` 是一个子模式，即 `p` 前面的位置。

> pattern 的缩写
>
> 两边是有括号，这才是完整的断言
>
> 这里的 = 号没有任何意思，与负向先行断言的 ! 是对应的，如果理解成 等于 p 就错了

比如 `(?=l)`，表示 'l' 字符前面的位置，例如：

```js
var result = "hello".replace(/(?=l)/g, '#');
console.log(result); 
// => "he#l#lo"
```

而 `(?!p)` 就是 `(?=p)` 的反面意思，比如：

```js
var result = "hello".replace(/(?!l)/g, '#');

console.log(result); 
// => "#h#ell#o#"
```

二者的学名分别是 positive lookahead 和 negative lookahead。中文翻译分别是正向先行断言和负向先行断言。

> (?:p) 非捕获分组

### 后行断言 (?<=p) 和 (?<!p)

ES2018 中，还支持 positive lookbehind 和 negative lookbehind。

具体是 `(?<=p)` 和 `(?<!p)`。

后行断言，就是后面的位置

```js
var result = "hello".replace(/(?<=l)/g, '#');
console.log(result); 
// => hel#l#o

var result = "hello".replace(/(?<!l)/g, '#');
console.log(result); 
// => #h#e#llo#
```

也有书上把这四个东西，翻译成**环视**，即看看右边或看看左边。

但一般书上，没有很好强调这四者是个位置。比如 `(?=p)`，一般都理解成：要求接下来的字符与 `p` 匹配，但不能包括 `p` 的那些字符。

而在本人看来 `(?=p)` 就与 `^` 一样好理解，就是 `p` 前面的那个位置。

## 位置的特性

对于位置的理解，我们可以理解成空字符 ""。

比如 "hello" 字符串等价于如下的形式：

```js
"hello" == "" + "h" + "" + "e" + "" + "l" + "" + "l" + "o" + "";
```

也等价于

```js
"hello" == "" + "" + "hello"
```

因此，把 `/^hello$/` 写成 `/^^hello$$$/`，是没有任何问题的：

```js
var result = /^^hello$$$/.test("hello");
console.log(result); 
// => true
```

甚至可以写成更复杂的:

```js
var result = /(?=he)^^he(?=\w)(?=\w)llo$\b\b$/.test("hello");
console.log(result); 
// => true
```

也就是说字符之间的位置，可以写成多个。

把位置理解空字符，是对位置非常有效的理解方式。

### **注意**

把位置理解成空字符，但是能获取到位置只有匹配位置的方法

`\s` 是获取空格的，首先就和空字符不是同一个概念，而且 `\s` 是匹配字符的，不是匹配到位置 @@@

## 匹配位置的案例

2023-04-29 here

### 不匹配任何字符的正则

让你写个正则不匹配任何东西

easy，`/.^/`

因为此正则要求只有一个字符，但该字符后面是开头。

### 数字的千位分隔符表示法

比如把 "12345678"，变成 "12,345,678"。

可见是需要把相应的位置替换成 ","。

#### 最后一个逗号

使用 `(?=\d{3}$)` 就可以做到：

```js
var result = "12345678".replace(/(?=\d{3}$)/g, ',')
console.log(result); 
// => "12345,678"
```

注意先行断言处的括号，如果不加的话，会报错

#### 弄出所有的逗号

因为逗号出现的位置，要求后面 3 个数字一组，也就是 `\d{3}` 至少出现一次。

此时可以使用量词 `+`：

```js
var result = "12345678".replace(/(?=(\d{3})+$)/g, ',')
console.log(result); 
// => "12,345,678"
```

#### 测试

```js
    var result = "123456789".replace(/(?=(\d{3})+$)/g, ',')
    console.log(result); 
    // => ",123,456,789"
```

因为上面的正则，仅仅表示把从结尾向前数，一但是 3 的倍数，就把其前面的位置替换成逗号。因此才会出现这个问题。

我们知道匹配开头可以使用 `^`，但要求这个位置不是开头怎么办？

```js
var string1 = "12345678",
string2 = "123456789";
reg = /(?!^)(?=(\d{3})+$)/g;

result = string2.replace(reg, ',')
console.log(result); 
// => "123,456,789"
```

#### 支持其他形式

如果要把 "12345678 123456789" 替换成 "12,345,678 123,456,789"。

此时我们需要修改正则，把里面的开头 `^` 和结尾 `$`，替换成 `\b`：

```js
var string = "12345678 123456789",
reg = /(?!\b)(?=(\d{3})+\b)/g;

var result = string.replace(reg, ',')
console.log(result); 
// => "12,345,678 123,456,789"
```

其中 `(?!\b)` 怎么理解呢？

要求当前是一个位置，但不是 `\b` 前面的位置，其实 `(?!\b)` 说的就是 `\B`。

因此最终正则变成了：`/\B(?=(\d{3})+\b)/g`。

#### 支持小数

```js
    const result2 = '123456.123456'
    const reg = /((?!^)(?<!\.))(?=(\d{3})+(?!\d))/g
    console.log(result2.replace(/(\d)(?=(\d{3})+(?!\d))/g,'$1,'))
```

第一种单纯匹配位置

第二种匹配的是千分位千的数字，利用正则变量进行替换

### 验证密码问题

密码长度 6-12 位，由数字、小写字符和大写字母组成，但必须至少包括**2 种**字符。

此题，如果写成多个正则来判断，比较容易。但要写成一个正则就比较困难。

那么，我们就来挑战一下。看看我们对位置的理解是否深刻。

#### 简化

不考虑“但必须至少包括 2 种字符”这一条件。我们可以容易写出：

```js
    var reg = /^\w{6,12}$/
```

#### 判断是否包含有某一种字符

假设，要求的**必须包含数字**，怎么办？此时我们可以使用 `(?=.*[0-9])` 来做。

`.*[0-9]`：表示匹配任意数量的字符，直到匹配到一个字符是数字。

`(?=.*[0-9])` 代表：任意个任意字符后得跟着一个数字，在这样一段字符串的前面, 这意味着，在这个**位置**后面的字符串必须包含一个数子

`(?=.*[0-9])^`：意味着这个位置必须在开头，因为空字符是可以有多个的, 这意味着：从头开始匹配, 直到有一个字符后面跟着一个数字, 这匹配到的子串自然是在开头的位置的.

> 脱字符和先行断言的关系到底是什么? 还是没搞懂, 感觉脱字符是用在后面的字符匹配上的

因此正则变成：

```js
var reg = /(?=.*[0-9])^[0-9A-Za-z]{6,12}$/;
```

#### 同时包含具体两种字符

比如同时包含数字和小写字母，可以用 `(?=.*[0-9])(?=.*[a-z])` 来做。

```js
var reg = /(?=.*[0-9])(?=.*[a-z])^[0-9A-Za-z]{6,12}$/;
```

**解答**

+ 我们可以把原题变成下列几种情况之一：
    + 同时包含数字和小写字母
    + 同时包含数字和大写字母
    + 同时包含小写字母和大写字母
    + 同时包含数字、小写字母和大写字母
+ 以上的 4 种情况是或的关系（实际上，可以不用第 4 条）。
+ 最终答案是：

    ```js
    var reg = /((?=.*[0-9])(?=.*[a-z])|(?=.*[0-9])(?=.*[A-Z])|(?=.*[a-z])(?=.*[A-Z]))^[0-9A-Za-z]{6,12}$/;
    console.log( reg.test("1234567") ); // false 全是数字
    console.log( reg.test("abcdef") ); // false 全是小写字母
    console.log( reg.test("ABCDEFGH") ); // false 全是大写字母
    console.log( reg.test("ab23C") ); // false 不足6位
    console.log( reg.test("ABCDEF234") ); // true 大写字母和数字
    console.log( reg.test("abcdEF234") ); // true 三者都有
    ```

**另外一种解法**

+ “至少包含两种字符”的意思就是说，不能全部都是数字，也不能全部都是小写字母，也不能全部都是大写字母。
+ 那么要求“不能全部都是数字”，怎么做呢？`(?!p)` 出马！
+ 对应的正则是：

    ```js
    var reg = /(?!^[0-9]{6,12}$)^[0-9A-Za-z]{6,12}$/;
    ```

+ 最终答案是：

    ```js
    var reg = /(?!^[0-9]{6,12}$)(?!^[a-z]{6,12}$)(?!^[A-Z]{6,12}$)^[0-9A-Za-z]{6,12}$/;
    console.log( reg.test("1234567") ); // false 全是数字
    console.log( reg.test("abcdef") ); // false 全是小写字母
    console.log( reg.test("ABCDEFGH") ); // false 全是大写字母
    console.log( reg.test("ab23C") ); // false 不足6位
    console.log( reg.test("ABCDEF234") ); // true 大写字母和数字
    console.log( reg.test("abcdEF234") ); // true 三者都有
    ```

# 括号的作用

不管哪门语言中都有括号。正则表达式也是一门语言，而括号的存在使这门语言更为强大。

对括号的使用是否得心应手，是衡量对正则的掌握水平的一个侧面标准。

括号的作用，其实三言两语就能说明白，**括号提供了分组**，便于我们**引用**它。

引用某个分组，会有两种情形：

+ 在 JavaScript 里引用它
+ 在正则表达式里引用它

## 分组和分支结构

这二者是括号最直觉的作用，也是最原始的功能。

### 分组

我们知道 `/a+/` 匹配连续出现的“a”，而要匹配连续出现的“ab”时，需要使用 `/(ab)+/`。

其中括号是提供分组功能，使量词 `+` 作用于“ab”这个整体，测试如下：

```js
var regex = /(ab)+/g;
var string = "ababa abbb ababab";
console.log( string.match(regex) ); 
// => ["abab", "ab", "ababab"]
```

### 分支结构

而在多选分支结构 `(p1|p2)` 中，此处括号的作用也是不言而喻的，提供了子表达式的所有可能。

```js
var regex = /^I love (JavaScript|Regular Expression)$/;
console.log( regex.test("I love JavaScript") );
console.log( regex.test("I love Regular Expression") );
// => true
// => true
```

如果去掉正则中的括号，即 `/^I love JavaScript|Regular Expression$/`，匹配字符串是 "I love JavaScript" 和 "Regular Expression"，当然这不是我们想要的。

## 引用分组

这是括号一个重要的作用，有了它，我们就可以进行数据提取，以及更强大的替换操作。

而要使用它带来的好处，必须配合使用实现环境的 API。

以日期为例。假设格式是 yyyy-mm-dd 的，我们可以先写一个简单的正则：

```js
var regex = /\d{4}-\d{2}-\d{2}/;
```

然后再修改成括号版的：

```js
var regex = /(\d{4})-(\d{2})-(\d{2})/;
```

### 提取数据

比如提取出年、月、日，可以这么做：

```js
var regex = /(\d{4})-(\d{2})-(\d{2})/;
var string = "2017-06-12";
console.log( string.match(regex) ); 
// => ["2017-06-12", "2017", "06", "12", index: 0, input: "2017-06-12"]
```

`match` 返回的一个数组：

+ 第一个元素是整体匹配结果
+ 然后是各个**分组**（括号里）匹配的内容
+ 然后是匹配下标
+ 最后是输入的文本。

**注意：**如果正则是否有修饰符 `g`，`match` 返回的数组格式是不一样的，不会有后面三个附加属性

另外也可以使用正则对象的 `exec` 方法：

```js
var regex = /(\d{4})-(\d{2})-(\d{2})/;
var string = "2017-06-12";
console.log( regex.exec(string) ); 
// => ["2017-06-12", "2017", "06", "12", index: 0, input: "2017-06-12"]
```

同时，也可以使用构造函数的全局属性 `$1` 至 `$9` 来获取：

```js
var regex = /(\d{4})-(\d{2})-(\d{2})/;
var string = "2017-06-12";

regex.test(string); // 正则操作即可，例如
//regex.exec(string);
//string.match(regex);

console.log(RegExp.$1); // "2017"
console.log(RegExp.$2); // "06"
console.log(RegExp.$3); // "12"
```

### 替换

比如，想把 yyyy-mm-dd 格式，替换成 mm/dd/yyyy 怎么做？

```js
var regex = /(\d{4})-(\d{2})-(\d{2})/;
var string = "2017-06-12";
var result = string.replace(regex, "$2/$3/$1");
console.log(result); 
// => "06/12/2017"
```

其中 `replace` 中的，第二个参数里用 `$1`、`$2`、`$3` 指代相应的分组。等价于如下的形式：

```js
var regex = /(\d{4})-(\d{2})-(\d{2})/;
var string = "2017-06-12";
var result = string.replace(regex, function() {
 return RegExp.$2 + "/" + RegExp.$3 + "/" + RegExp.$1;
});
console.log(result); 
// => "06/12/2017"
```

也等价于：

```js
var regex = /(\d{4})-(\d{2})-(\d{2})/;
var string = "2017-06-12";
var result = string.replace(regex, function(match, year, month, day) {
 return month + "/" + day + "/" + year;
});
console.log(result); 
// => "06/12/2017"
```

## 反向引用

除了使用相应 API 来引用分组，也可以在正则本身里引用分组。但只能**引用之前出现的分组**，即反向引用。

还是以日期为例。

比如要写一个正则支持匹配如下三种格式：

```js
2016-06-12

2016/06/12

2016.06.12
```

最先可能想到的正则是:

```js
var regex = /\d{4}(-|\/|\.)\d{2}(-|\/|\.)\d{2}/;
var string1 = "2017-06-12";
var string2 = "2017/06/12";
var string3 = "2017.06.12";
var string4 = "2016-06/12";
console.log( regex.test(string1) ); // true
console.log( regex.test(string2) ); // true
console.log( regex.test(string3) ); // true
console.log( regex.test(string4) ); // true
```

其中 `/` 和 `.` 需要转义。虽然匹配了要求的情况，但也匹配 "2016-06/12" 这样的数据。

假设我们想要求分割符前后一致怎么办？此时需要使用反向引用：

```js
var regex = /\d{4}(-|\/|\.)\d{2}\1\d{2}/;
var string1 = "2017-06-12";
var string2 = "2017/06/12";
var string3 = "2017.06.12";
var string4 = "2016-06/12";
console.log( regex.test(string1) ); // true
console.log( regex.test(string2) ); // true
console.log( regex.test(string3) ); // true
console.log( regex.test(string4) ); // false
```

**注意**里面的 `\1`，表示的引用之前的那个分组 `(-|\/|\.)`。不管它匹配到什么（比如 -），`\1` 都匹配那个同样的具体某个字符。

我们知道了 `\1` 的含义后，那么 `\2` 和 `\3` 的概念也就理解了，即分别指代第二个和第三个分组。

看到这里，此时，恐怕你会有三个问题。

### 括号嵌套怎么办？

**以左括号（开括号）为准**。比如：

```js
var regex = /^((\d)(\d(\d)))\1\2\3\4$/;
var string = "1231231233";
console.log( regex.test(string) ); // true
console.log( RegExp.$1 ); // 123
console.log( RegExp.$2 ); // 1
console.log( RegExp.$3 ); // 23
console.log( RegExp.$4 ); // 3
```

我们可以看看这个正则匹配模式：

+ 第一个字符是数字，比如说 1，
+ 第二个字符是数字，比如说 2，
+ 第三个字符是数字，比如说 3，
+ 接下来的是 `\1`，是第一个分组内容，那么看第一个开括号对应的分组是什么，是 123，
+ 接下来的是 `\2`，找到第 2 个开括号，对应的分组，匹配的内容是 1，
+ 接下来的是 `\3`，找到第 3 个开括号，对应的分组，匹配的内容是 23，
+ 最后的是 `\4`，找到第 3 个开括号，对应的分组，匹配的内容是 3。

### \10 表示什么呢？

另外一个疑问可能是，即 `\10` 是表示第 10 个分组，还是 `\1` 和 `0` 呢？

答案是前者，虽然一个正则里出现 `\10` 比较罕见。测试如下：

```js
var regex = /(1)(2)(3)(4)(5)(6)(7)(8)(9)(#) \10+/;
var string = "123456789# ######"
console.log( regex.test(string) );
// => true
```

### 引用不存在的分组会怎样？

因为反向引用，是引用前面的分组，但我们在正则里引用了不存在的分组时，此时正则不会报错，只是匹配反向引用的字符本身。例如 `\2`，就匹配 "\2"。**注意 "\2" 表示对 "2" 进行了转意。**

```js
var regex = /\1\2\3\4\5\6\7\8\9/;
console.log( regex.test("\1\2\3\4\5\6\7\8\9") ); 
console.log( "\1\2\3\4\5\6\7\8\9".split("") );
```

chrome 浏览器打印的结果：

![img](./es-regexp/f75ad2642625466dd5adcad3e2a4c51a)

### 非捕获分组

之前文中出现的分组，都会捕获它们匹配到的数据，以便后续引用，因此也称他们是捕获型分组。

如果只想要括号最原始的功能，但不会引用它，即，既不在 API 里引用，也不在正则里反向引用。此时可以使用非捕获分组 `(?:p)`，例如本文第一个例子可以修改为：

```js
var regex = /(?:ab)+/g;
var string = "ababa abbb ababab";
console.log( string.match(regex) ); 
// => ["abab", "ab", "ababab"]
```

## 案例

### 字符串 Trim 方法模拟

`trim` 方法是去掉字符串的开头和结尾的空白符。有两种思路去做。

第一种，匹配到开头和结尾的空白符，然后替换成空字符。如：

```js
function trim(str) {
 return str.replace(/^\s+|\s+$/g, '');
}
console.log( trim("  foobar   ") ); 
// => "foobar"
```

第二种，匹配整个字符串，然后用引用来提取出相应的数据：

```js
function trim(str) {
 return str.replace(/^\s*(.*?)\s*$/g, "$1");
}
console.log( trim("  foobar   ") ); 
// => "foobar"
```

这里使用了惰性匹配 `*?`，不然也会匹配最后一个空格之前的所有空格的，匹配到\s 可以匹配为止

当然，前者效率高。

### 将每个单词的首字母转换为大写

找到每个空格后的第一个字母，但是\s 无法匹配开头配置，使用非捕获组保证函数参数正确

```js
function titleize(str) {
 return str.toLowerCase().replace(/(?:^|\s)\w/g, function(c) {
  return c.toUpperCase();
 });
}
console.log( titleize('my name is epeli') ); 
// => "My Name Is Epeli"
// 等价于
function titleize(str) {
 return str.toLowerCase().replace(/^\w|\s\w/g, function(c) {
  return c.toUpperCase();
 });
}

```

因为\b 匹配的是头尾两个位置，加个\w

```js
function titleize(str) {
 return str.toLowerCase().replace(/\b\w/g, function(c) {
  return c.toUpperCase();
 });
}
console.log( titleize('my name is epeli') ); 
// => "My Name Is Epeli"
```

思路是找到每个单词的首字母

顺便看下 JS 的原生方法

```js
var str = 'it is my cat';
var arr = str.split(' ');
arr = arr.map(function(i){
  return i.slice(0, 1).toUpperCase() + i.slice(1);  
})//注意此处要有return 并且map方法将返回一个新数组
```

### 驼峰化

```js
function camelize(str) {
 return str.replace(/[-_\s]+(.)?/g, function(match, c) {
  return c ? c.toUpperCase() : ''
 });
}
console.log( camelize('-moz-transform') )
// => "MozTransform"
```

其中分组 `(.)` 表示首字母。单词的界定是，前面的字符可以是多个连字符、下划线以及空白符。正则后面的 `?` 的目的，是为了应对 `str` 尾部的字符可能不是单词字符，比如 `str` 是 '-moz-transform '。

#### 中划线化

```js
function dasherize(str) {
 return str.replace(/([A-Z])/g, '-$1').replace(/[-_\s]+/g, '-').toLowerCase();
}
console.log( dasherize('MozTransform') ); 
// => "-moz-transform"
```

驼峰化的逆过程。

### Html 转义和反转义

```js
// 将HTML特殊字符转换成等值的实体
function escapeHTML(str) {
 var escapeChars = {
   '¢' : 'cent',
   '£' : 'pound',
   '¥' : 'yen',
   '€': 'euro',
   '©' :'copy',
   '®' : 'reg',
   '<' : 'lt',
   '>' : 'gt',
   '"' : 'quot',
   '&' : 'amp',
   '\'' : '#39'
 };
 return str.replace(new RegExp('[' + Object.keys(escapeChars).join('') +']', 'g'), function(match) {
  return '&' + escapeChars[match] + ';';
 });
}
console.log( escapeHTML('<div>Blah blah blah</div>') );
// => "&lt;div&gt;Blah blah blah&lt;/div&gt";
```

其中使用了用构造函数生成的正则，然后替换相应的格式就行了，这个跟本章没多大关系。

倒是它的逆过程，使用了括号，以便提供引用，也很简单，如下：

```js
// 实体字符转换为等值的HTML。
function unescapeHTML(str) {
 var htmlEntities = {
   nbsp: ' ',
   cent: '¢',
   pound: '£',
   yen: '¥',
   euro: '€',
   copy: '©',
   reg: '®',
   lt: '<',
   gt: '>',
   quot: '"',
   amp: '&',
   apos: '\''
 };
 return str.replace(/\&([^;]+);/g, function(match, key) {
  if (key in htmlEntities) {
   return htmlEntities[key];
  }
  return match;
 });
}
console.log( unescapeHTML('&lt;div&gt;Blah blah blah&lt;/div&gt;') );
// => "<div>Blah blah blah</div>"
```

通过 `key` 获取相应的分组引用，然后作为对象的键。

### 匹配成对标签

要求匹配：

> <title>regular expression</title>
> <p>laoyao bye bye</p>

不匹配：

> <title>wrong!</p>

匹配一个开标签，可以使用正则 `<[^>]+>`，

匹配一个闭标签，可以使用 `<\/[^>]+>`，

但是要求匹配成对标签，那就需要使用反向引用，如：

```js
var regex = /<([^>]+)>[\d\D]*<\/\1>/;
var string1 = "<title>regular expression</title>";
var string2 = "<p>laoyao bye bye</p>";
var string3 = "<title>wrong!</p>";
console.log( regex.test(string1) ); // true
console.log( regex.test(string2) ); // true
console.log( regex.test(string3) ); // false
```

其中开标签 `<[^>]+>` 改成 `<([^>]+)>`，使用括号的目的是为了后面使用反向引用，而提供分组。闭标签使用了反向引用，`<\/\1>`。

另外 `[\d\D]` 的意思是，这个字符是数字或者不是数字，因此，也就是匹配任意字符的意思。

# 回溯法原理

## 没有回溯的匹配

假设我们的正则是 `/ab{1,3}c/`，其可视化形式是：

![img](./es-regexp/d92077b25d4faf8073d38999294f746c)

而当目标字符串是 "abbbc" 时，就没有所谓的“回溯”。其匹配过程是：

![img](./es-regexp/3f6e829c62fca181d818205e0e08bf73)

其中子表达式 `b{1,3}` 表示“b”字符连续出现 1 到 3 次。

## 有回溯的匹配

如果目标字符串是 "abbc"，中间就有回溯

![img](F:\OneDrive\JS\assets\e58356622f6087437f33cdce7ce7bd3d)

图中第 5 步有红颜色，表示匹配不成功。此时 `b{1,3}` 已经匹配到了 2 个字符“b”，准备尝试第三个时，结果发现接下来的字符是“c”。那么就认为 `b{1,3}` 就已经匹配完毕。

然后状态又回到之前的状态（即**第 6 步，与第 4 步一样**）

最后再用子表达式 `c`，去匹配字符“c”。当然，此时整个表达式匹配成功了。

图中的第 6 步，就是“回溯”。

你可能对此没有感觉，这里我们再举一个例子。正则是：

![img](./es-regexp/12da8829af2cb1d67ea78631d58be6ce)

目标字符串是 "abbbc"，匹配过程是：

![img](./es-regexp/dddfffaf633dd14c4eefba488f64400f)

其中第 7 步和第 10 步是回溯。**第 7 步与第 4 步一样**，此时 `b{1,3}` 匹配了两个 "b"，而**第 10 步与第 3 步一样**，此时 `b{1,3}` 只匹配了一个 "b"，这也是 `b{1,3}` 的最终匹配结果。

这里再看一个清晰的回溯，正则是：

![img](./es-regexp/a9e420776eb01c07979f1599e4060775)

目标字符串是："acd"ef，匹配过程是：

![img](./es-regexp/33b738b42fc9e74edc84b17479ac7000)

图中省略了尝试匹配双引号失败的过程。可以看出 `.*` 是非常影响效率的。

为了减少一些不必要的回溯，可以把正则修改为 `/"[^"]*"/`。

# 常见的回溯形式

正则表达式匹配字符串的这种方式，有个学名，叫回溯法。

回溯法也称试探法，它的基本思想是：从问题的某一种状态（初始状态）出发，搜索从这种状态出发所能达到的所有“状态”，当一条路走到“尽头”的时候（不能再前进），再后退一步或若干步，从另一种可能“状态”出发，继续搜索，直到所有的“路径”（状态）都试探过。这种不断“前进”、不断“回溯”寻找解的方法，就称作“回溯法”。（copy 于百度百科）。

本质上就是深度优先搜索算法。**其中退到之前的某一步这一过程，我们称为“回溯”。**从上面的描述过程中，可以看出，路走不通时，就会发生“回溯”。即，**尝试匹配失败时，接下来的一步通常就是回溯。**

道理，我们是懂了。那么 JS 中正则表达式会产生回溯的地方都有哪些呢？

## 贪婪量词

之前的例子都是贪婪量词相关的。比如 `b{1,3}`，因为其是贪婪的，尝试可能的顺序是从多往少的方向去尝试。首先会尝试 "bbb"，然后再看整个正则是否能匹配。不能匹配时，吐出一个 "b"，即在 "bb" 的基础上，再继续尝试。如果还不行，再吐出一个，再试。如果还不行呢？只能说明匹配失败了。

虽然局部匹配是贪婪的，但也要满足整体能正确匹配。否则，皮之不存，毛将焉附？

此时我们不禁会问，如果当多个贪婪量词挨着存在，并相互有冲突时，此时会是怎样？

答案是，先下手为强！因为深度优先搜索。测试如下：

```js
var string = "12345";
var regex = /(\d{1,3})(\d{1,3})/;
console.log( string.match(regex) );
// => ["12345", "123", "45", index: 0, input: "12345"]
```

其中，前面的 `\d{1,3}` 匹配的是 "123"，后面的 `\d{1,3}` 匹配的是 "45"。

## 惰性量词

惰性量词就是在贪婪量词后面加个问号。表示尽可能少的匹配，比如：

```js
var string = "12345";
var regex = /(\d{1,3}?)(\d{1,3})/;
console.log( string.match(regex) );
// => ["1234", "1", "234", index: 0, input: "12345"]
```

其中 `\d{1,3}?` 只匹配到一个字符 "1"，而后面的 `\d{1,3}` 匹配了 "234"。

虽然惰性量词不贪，但也会有回溯的现象。比如正则是：

![img](./es-regexp/0e29c26dd50349760d05935c5e93f07b)

目标字符串是 "12345"，匹配过程是：

![img](F:\OneDrive\JS\assets\a2af73fc275cddf7c9c5fb5a786861c0)

知道你不贪、很知足，但是为了整体匹配成，没办法，也只能给你多塞点了。因此最后 `\d{1,3}?` 匹配的字符是 "12"，是两个数字，而不是一个。

## 分支结构

我们知道分支也是惰性的，比如 `/can|candy/`，去匹配字符串 "candy"，得到的结果是 "can"，因为分支会一个一个尝试，如果前面的满足了，后面就不会再试验了。

分支结构，可能前面的子模式会形成了局部匹配，如果接下来表达式整体不匹配时，仍会继续尝试剩下的分支。这种尝试也可以看成一种回溯。

比如正则：

![img](./es-regexp/4aedaeda72a6d291b9a8685cc0170347)

目标字符串是 "candy"，匹配过程：

![img](./es-regexp/d69d02dfe0712ee3d22d5bb1afcda0a2)

上面第 5 步，虽然没有回到之前的状态，但仍然回到了分支结构，尝试下一种可能。所以，可以认为它是一种回溯的。

## 总结

其实回溯法，很容易掌握的。

简单总结就是，正因为有多种可能，所以要一个一个试。直到，要么到某一步时，整体匹配成功了；要么最后都试完后，发现整体匹配不成功。

1. 贪婪量词“试”的策略是：买衣服砍价。价钱太高了，便宜点，不行，再便宜点。
2. 惰性量词“试”的策略是：卖东西加价。给少了，再多给点行不，还有点少啊，再给点。
3. 分支结构“试”的策略是：货比三家。这家不行，换一家吧，还不行，再换。

既然有回溯的过程，那么匹配效率肯定低一些。相对谁呢？相对那些 DFA 引擎。

而 JS 的正则引擎是 NFA，NFA 是“非确定型有限自动机”的简写。

大部分语言中的正则都是 NFA，为啥它这么流行呢？

答：你别看我匹配慢，但是我编译快啊，而且我还有趣哦。

# 正则表达式的拆分

## 结构和操作符

编程语言一般都有操作符。只要有操作符，就会出现一个问题。当一大堆操作在一起时，先操作谁，又后操作谁呢？为了不产生歧义，就需要语言本身定义好操作顺序，即所谓的优先级。

而在正则表达式中，操作符都体现在结构中，即由特殊字符和普通字符所代表的一个个特殊整体。

## JS 正则中的结构

**字面量**，匹配一个具体字符，包括不用转义的和需要转义的。比如 a 匹配字符 "a"，又比如 `\n` 匹配换行符，又比如 `\.` 匹配小数点。

**字符组**，匹配一个字符，可以是多种可能之一，比如 `[0-9]`，表示匹配一个数字。也有 `\d` 的简写形式。另外还有反义字符组，表示可以是除了特定字符之外任何一个字符，比如 `[^0-9]`，表示一个非数字字符，也有 `\D` 的简写形式。

**量词**，表示一个字符连续出现，比如 `a{1,3}` 表示“a”字符连续出现 3 次。另外还有常见的简写形式，比如 `a+` 表示“a”字符连续出现至少一次。

**锚点**，匹配一个位置，而不是字符。比如^匹配字符串的开头，又比如 `\b` 匹配单词边界，又比如 `(?=\d)` 表示数字前面的位置。

**分组**，用括号表示一个整体，比如 `(ab)+`，表示 "ab" 两个字符连续出现多次，也可以使用非捕获分组 `(?:ab)+`。

**分支**，多个子表达式多选一，比如 `abc|bcd`，表达式匹配 "abc" 或者 "bcd" 字符子串。

**反向引用**，比如 `\2`，表示引用第 2 个分组。

## JS 正则中的操作符

转义符 `\`

括号和方括号 `(...)`、`(?:...)`、`(?=...)`、`(?!...)`、`[...]`

量词限定符 `{m}`、`{m,n}`、`{m,}`、`?`、`*`、`+`

位置和序列 `^` 、`$`、 `\元字符`、 `一般字符`

管道符（竖杠）`|`

**上面操作符的优先级从上至下，由高到低。**

### 示例

这里，我们来分析一个正则：

```js
/ab?(c|de*)+|fg/
```

由于括号的存在，所以，`(c|de*)` 是一个整体结构。

在 `(c|de*)` 中，注意其中的量词 `*`，因此 `e*` 是一个整体结构。

又因为分支结构“|”优先级最低，因此 `c` 是一个整体、而 `de*` 是另一个整体。

同理，整个正则分成了 `a`、`b?`、`(...)+`、`f`、`g`。而由于分支的原因，又可以分成 `ab?(c|de*)+` 和 `fg` 这两部分。

希望你没被我绕晕，上面的分析可用其 [可视化](https://link.juejin.im/?target=http%3A%2F%2Flink.zhihu.com%2F%3Ftarget%3Dhttps%253A%2F%2Fjex.im%2Fregulex%2F%2523%2521embed%253Dfalse%2526flags%253D%2526re%253Dab%25253F%2528c%25257Cde%252A%2529%25252B%25257Cfg) 形式描述如下：

![img](./es-regexp/4bb44a11e383047a027a234ee15663ad)

因为管道符的优先级是最低的，所以管道符两边都可以看作一个整体，相当于有一个小括号

## 注意要点

关于结构和操作符，还是有几点需要强调：

### 匹配字符串整体问题

因为是要匹配整个字符串，我们经常会在正则前后中加上锚字符 `^` 和 `$`。

比如要匹配目标字符串 "abc" 或者 "bcd" 时，如果一不小心，就会写成 `/^abc|bcd$/`。

而位置字符和字符序列优先级要比竖杠高，故其匹配的结构是：

![img](./es-regexp/173e8c288f0da4ed89df597551fa80db)

应该修改成:

![img](./es-regexp/c3bcb26f1b87e43bab1497322f5107e5)

### 量词连缀问题

假设，要匹配这样的字符串：

1. 每个字符为 a、b、c 任选其一
2. 字符串的长度是 3 的倍数

此时正则不能想当然地写成 `/^[abc]{3}+$/`，这样会报错，说 `+` 前面没什么可重复的：

![img](./es-regexp/a852f1068a8b2051bda5c75882c98f76)

此时要修改成：

![img](./es-regexp/678d0621feb4bdcea899a6f61628a521)

### 元字符转义问题

所谓元字符，就是正则中有特殊含义的字符。

所有结构里，用到的元字符总结如下：`^ $ . * + ? | \ / ( ) [ ] { } = ! : - ,`

当匹配上面的字符本身时，可以一律转义：

```js
var string = "^$.*+?|\\/[]{}=!:-,";
var regex = /\^\$\.\*\+\?\|\\\/\[\]\{\}\=\!\:\-\,/;
console.log( regex.test(string) ); 
// => true
```

其中 `string` 中的 `\` 字符也要转义的。

另外，在 `string` 中，也可以把每个字符转义，当然，转义后的结果仍是本身：

```js
var string = "^$.*+?|\\/[]{}=!:-,";
var string2 = "\^\$\.\*\+\?\|\\\/\[\]\{\}\=\!\:\-\,";
console.log( string == string2 ); 
// => true
```

简单的全转义正则，把除了单词字符以外的前面加上一个转义字符

```js
const name = name.replace(/(?=\W)/g,'\\')
```

现在的问题是，是不是每个字符都需要转义呢？否，看情况。

**字符组中的元字符**

+ 跟**字符组**相关的元字符有 `[]`、`^`、`-`。因此在会引起歧义的地方进行转义。例如开头的 `^` 必须转义，不然会把整个字符组，看成反义字符组。

    ```js
    var string = "^$.*+?|\\/[]{}=!:-,";
    var regex = /[\^$.*+?|\\/\[\]{}=!:\-,]/g;
    console.log( string.match(regex) );
    // => ["^", "$", ".", "*", "+", "?", "|", "\", "/", "[", "]", "{", "}", "=", "!", ":", "-", ","]
    ```

**匹配“[abc]”和“{3,5}”**

+ 我们知道 `[abc]`，是个字符组。如果要匹配字符串 "[abc]" 时，该怎么办？
+ 可以写成 `/\[abc\]/`，也可以写成 `/\[abc]/`，测试如下：

    ```js
    var string = "[abc]";
    var regex = /\[abc]/g;
    console.log( string.match(regex)[0] ); 
    // => "[abc]"
    ```

+ 只需要在第一个方括号转义即可，因为后面的方括号构不成字符组，正则不会引发歧义，自然不需要转义。
+ 同理，要匹配字符串 "{3,5}"，只需要把正则写成 `/\{3,5}/` 即可。
+ 另外，我们知道量词有简写形式 `{m,}`，却没有 `{,n}` 的情况。虽然后者不构成量词的形式，但此时并不会报错。当然，**匹配的字符串也是 "{,n}"**，测试如下：

    ```js
    var string = "{,3}";
    var regex = /{,3}/g;
    console.log( string.match(regex)[0] ); 
    // => "{,3}"
    ```

**其余情况**

+ 比如 `\=` `!` `:` `-` `,` 等符号，只要不在特殊结构中，也不需要转义。
+ 但是，括号需要前后都转义的，如 `/\(123\)/`。
+ 至于剩下的 `^` `$` `.` `*` `+` `?` `|` `\` `/` 等字符，只要不在字符组内，都需要转义的。

**总结**

+ 关于元字符转义问题，当自己不确定与否时，尽管去转义，总之是不会错的。

## 案例分析

### 身份证

正则表达式是：`/^(\d{15}|\d{17}[\dxX])$/`

因为竖杠“|”,的优先级最低，所以正则分成了两部分 `\d{15}` 和 `\d{17}[\dxX]`。

+ `\d{15}` 表示 15 位连续数字。
+ `\d{17}[\dxX]` 表示 17 位连续数字，最后一位可以是数字可以大小写字母 "x"。

可视化如下：

![img](./es-regexp/e47d20e904f9b5665942a01fc9d6111d)

### IPV4 地址

正则表达式是：

```js
/^((0{0,2}\d|0?\d{2}|1\d{2}|2[0-4]\d|25[0-5])\.){3}(0{0,2}\d|0?\d{2}|1\d{2}|2[0-4]\d|25[0-5])$/
```

这个正则，看起来非常吓人。但是熟悉优先级后，会立马得出如下的结构：

```js
((...)\.){3}(...)
```

上面的两个 `(...)` 是一样的结构。表示匹配的是 3 位数字。因此整个结构是：**3 位数.3 位数.3 位数.3 位数**

然后再来分析 `(...)`：

```js
(0{0,2}\d|0?\d{2}|1\d{2}|2[0-4]\d|25[0-5])(0{0,2}\d|0?\d{2}|1\d{2}|2[0-4]\d|25[0-5])
```

它是一个多选结构，分成 5 个部分：

+ `0{0,2}\d`，匹配一位数，包括 0 补齐的。比如，9、09、009；
+ `0?\d{2}`，匹配两位数，包括 0 补齐的，也包括一位数；
+ `1\d{2}`，匹配 100 到 199;
+ `2[0-4]\d`，匹配 200-249；
+ `25[0-5]`，匹配 250-255。

最后来看一下其可视化形式：

![img](./es-regexp/c0a482018c3bfa876b0b5a45ddd67c80)

# 正则表达式的构建

## 平衡法则

构建正则有一点非常重要，需要做到下面几点的平衡：

+ 匹配预期的字符串
+ 不匹配非预期的字符串
+ 可读性和可维护性
+ 效率

## 构建正则前提

### 是否能使用正则

正则太强大了，以至于我们随便遇到一个操作字符串问题时，都会下意识地去想，用正则该怎么做。但我们始终要提醒自己，正则虽然强大，但不是万能的，很多看似很简单的事情，还是做不到的。

比如匹配这样的字符串：1010010001....

虽然很有规律，但是只靠正则就是无能为力。

### 是否有必要使用正则

要认识到正则的局限，不要去研究根本无法完成的任务。同时，也不能走入另一个极端：无所不用正则。能用字符串 API 解决的简单问题，就不该正则出马。

比如，从日期中提取出年月日，虽然可以使用正则：

```js
var string = "2017-07-01";
var regex = /^(\d{4})-(\d{2})-(\d{2})/;
console.log( string.match(regex) );
// => ["2017-07-01", "2017", "07", "01", index: 0, input: "2017-07-01"]
```

其实，可以使用字符串的 `split` 方法来做，即可：

```js
var string = "2017-07-01";
var result = string.split("-");
console.log( result );
// => ["2017", "07", "01"]
```

比如，判断是否有问号，虽然可以使用：

```js
var string = "?id=xx&act=search";
console.log( string.search(/\?/) );
// => 0
```

其实，可以使用字符串的 `indexOf` 方法：

```js
var string = "?id=xx&act=search";
console.log( string.indexOf("?") );
// => 0
```

比如获取子串，虽然可以使用正则：

```js
var string = "JavaScript";
console.log( string.match(/.{4}(.+)/)[1] );
// => Script
```

其实，可以直接使用字符串的 `substring` 或 `substr` 方法来做：

```js
var string = "JavaScript";
console.log( string.substring(4) );
// => Script
```

### 是否有必要构建一个复杂的正则

比如密码匹配问题，要求密码长度 6-12 位，由数字、小写字符和大写字母组成，但必须至少包括 2 种字符。

在第 2 章里，我们写出了正则是：

```js
/(?!^[0-9]{6,12}$)(?!^[a-z]{6,12}$)(?!^[A-Z]{6,12}$)^[0-9A-Za-z]{6,12}$/
```

其实可以使用多个小正则来做：

```js
var regex1 = /^[0-9A-Za-z]{6,12}$/;
var regex2 = /^[0-9]{6,12}$/;
var regex3 = /^[A-Z]{6,12}$/;
var regex4 = /^[a-z]{6,12}$/;
function checkPassword(string) {
  // 是否符合：任意字符组成的6-12，否则返回false
 if (!regex1.test(string)) return false;
  // 是否符合：仅由数字组成的6-12,是则返回false
 if (regex2.test(string)) return false;
 if (regex3.test(string)) return false;
 if (regex4.test(string)) return false;
 return true;
}
```

## 准确性

所谓准确性，就是能匹配预期的目标，并且不匹配非预期的目标。

这里提到了“预期”二字，那么我们就需要知道目标的组成规则。

不然没法界定什么样的目标字符串是符合预期的，什么样的又不是符合预期的。

下面将举例说明，当目标字符串构成比较复杂时，该如何构建正则，并考虑到哪些平衡。

### 匹配固定电话

比如要匹配如下格式的固定电话号码：

```js
055188888888

0551-88888888

(0551)88888888
```

第一步，了解各部分的模式规则：

+ 上面的电话，总体上分为区号和号码两部分（不考虑分机号和 +86 的情形）。
+ 区号是 0 开头的 3 到 4 位数字，对应的正则是：`0\d{2,3}`
+ 号码是非 0 开头的 7 到 8 位数字，对应的正则是：`[1-9]\d{6,7}`
+ 因此，匹配 055188888888 的正则是：`/^0\d{2,3}[1-9]\d{6,7}$/`
+ 匹配 0551-88888888 的正则是：`/^0\d{2,3}-[1-9]\d{6,7}$/`
+ 匹配 (0551)88888888 的正则是：`/^\(0\d{2,3}\)[1-9]\d{6,7}$/`

第二步，明确形式关系：

这三者情形是或的关系，可以构建分支：

```js
/^0\d{2,3}[1-9]\d{6,7}$|^0\d{2,3}-[1-9]\d{6,7}$|^\(0\d{2,3}\)[1-9]\d{6,7}$/
```

提取公共部分：

```js
/^(0\d{2,3}|0\d{2,3}-|\(0\d{2,3}\))[1-9]\d{6,7}$/
```

进一步简写：

```js
/^(0\d{2,3}-?|\(0\d{2,3}\))[1-9]\d{6,7}$/
```

其可视化形式：

![img](./es-regexp/225f104c7293c5a174f7c9394d755f13)

上面的正则构建过程略显罗嗦，但是这样做，能保证正则是准确的。

上述三种情形是或的关系，这一点很重要，不然很容易按字符是否出现的情形把正则写成：

```
/^\(?0\d{2,3}\)?-?[1-9]\d{6,7}$/
```

虽然也能匹配上述目标字符串，但也会匹配 (0551-88888888 这样的字符串。当然，这不是我们想要的。

其实这个正则也不是完美的，因为现实中，并不是每个 3 位数和 4 位数都是一个真实的区号。

这就是一个平衡取舍问题，一般够用就行。

### 匹配浮点数

要求匹配如下的格式：

```js
1.23、+1.23、-1.23

10、+10、-10

.2、+.2、-.2
```

可以看出正则分为三部分。

+ 符号部分：`[+-]`
+ 整数部分：`\d+`
+ 小数部分：`\.\d+`

上述三个部分，并不是全部都出现。如果此时很容易写出如下的正则：

```
/^[+-]?(\d+)?(\.\d+)?$/
```

此正则看似没问题，但这个正则也会匹配空字符 ""。

因为目标字符串的形式关系不是要求每部分都是可选的。

+ 要匹配 1.23、+1.23、-1.23，可以用 `/^[+-]?\d+\.\d+$/`
+ 要匹配 10、+10、-10，可以用 `/^[+-]?\d+$/`
+ 要匹配.2、+.2、-.2，可以用 `/^[+-]?\.\d+$/`

因此整个正则是这三者的或的关系，提取公众部分后是：

```
/^[+-]?(\d+\.\d+|\d+|\.\d+)$/
```

其可视化形式是：

![img](./es-regexp/f48457e329e8598e113b42d699744c9b)

如果要求不匹配 +.2 和 -.2，此时正则变成：

![img](./es-regexp/8c7263b0465bc26e5062d9978906f578)

当然，`/^[+-]?(\d+\.\d+|\d+|\.\d+)$/` 也不是完美的，我们也是做了些取舍，比如：

+ 它也会匹配 012 这样以 0 开头的整数。如果要求不匹配的话，需要修改整数部分的正则。
+ 一般进行验证操作之前，都要经过 trim 和判空。那样的话，也许那个错误正则也就够用了。
+ 也可以进一步改写成：`/^[+-]?(\d+)?(\.)?\d+$/`，这样我们就需要考虑**可读性和可维护性**了。

## 效率

保证了准确性后，才需要考虑是否要优化。大多数情形是不需要优化的，除非运行的非常慢。什么情形正则表达式运行才慢呢？我们需要考察正则表达式的运行过程（原理）。

正则表达式的运行分为如下的阶段：

1. 编译
2. 设定起始位置
3. 尝试匹配
4. 匹配失败的话，从下一位开始继续第 3 步
5. 最终结果：匹配成功或失败

下面以代码为例，来看看这几个阶段都做了什么：

```JS
var regex = /\d+/g;
console.log( regex.lastIndex, regex.exec("123abc34def") );
console.log( regex.lastIndex, regex.exec("123abc34def") );
console.log( regex.lastIndex, regex.exec("123abc34def") );
console.log( regex.lastIndex, regex.exec("123abc34def") );
// => 0 ["123", index: 0, input: "123abc34def"]
// => 3 ["34", index: 6, input: "123abc34def"]
// => 8 null
// => 0 ["123", index: 0, input: "123abc34def"]
```

具体分析如下：

```JS
var regex = /\d+/g;
```

当生成一个正则时，引擎会对其进行编译。报错与否出现这这个阶段。

```JS
regex.exec("123abc34def")
```

当尝试匹配时，需要确定从哪一位置开始匹配。一般情形都是字符串的开头，即第 0 位。

但当使用 `test` 和 `exec` 方法，且正则有 `g` 时，起始位置是从正则对象的 `lastIndex` 属性开始。

因此第一次 `exec` 是从第 0 位开始，而第二次是从 3 开始的。

设定好起始位置后，就开始尝试匹配了。

比如第一次 `exec`，从 0 开始，去尝试匹配，并且成功地匹配到 3 个数字。此时结束时的下标是 2，因此下一次的起始位置是 3。

而第二次，起始下标是 3，但第 3 个字符是“a”，并不是数字。但此时并不会直接报匹配失败，而是移动到下一位置，即从第 4 位开始继续尝试匹配，但该字符是 b，也不是数字。再移动到下一位，是 c 仍不行，再移动一位是数字 3，此时匹配到了两位数字 34。此时，下一次匹配的位置是 d 的位置，即第 8 位。

第三次，是从第 8 位开始匹配，直到试到最后一位，也没发现匹配的，因此匹配失败，返回 `null`。同时设置 `lastIndex` 为 0，即，如要再尝试匹配的话，需从头开始。

从上面可以看出，匹配会出现**效率问题，主要出现在上面的第 3 阶段和第 4 阶段。**

因此，主要优化手法也是针对这两阶段的。

### 使用具体型字符组来代替通配符，来消除回溯

而在第三阶段，最大的问题就是回溯。

例如，匹配双引用号之间的字符。如，匹配字符串 123"abc"456 中的 "abc"。

如果正则用的是：`/".*"/`，，会在第 3 阶段产生 4 次回溯（粉色表示 `.*` 匹配的内容）：

![img](./es-regexp/5b677f04f2b8d5d776060cea3b045863)

如果正则用的是：`/".*?"/`，会产生 2 次回溯（粉色表示 `.*?` 匹配的内容）：

![img](./es-regexp/a064ff019c3c6cdde6005b6c83b60e7b)

因为回溯的存在，需要引擎保存多种可能中未尝试过的状态，以便后续回溯时使用。注定要占用一定的内存。

此时要使用具体化的字符组，来代替通配符 `.`，以便消除不必要的字符，此时使用正则 `/"[^"]*"/`，即可。

#### 使用非捕获型分组

因为括号的作用之一是，可以捕获分组和分支里的数据。那么就需要内存来保存它们。

当我们不需要使用分组引用和反向引用时，此时可以使用非捕获分组。例如：

```JS
/^[+-]?(\d+\.\d+|\d+|\.\d+)$/
```

可以修改成：

```JS
/^[+-]?(?:\d+\.\d+|\d+|\.\d+)$/
```

### 独立出确定字符

例如 `/a+/`，可以修改成 `/aa*/`。

因为后者能比前者多确定了字符 a。这样会在第四步中，加快判断是否匹配失败，进而加快移位的速度。

### 提取分支公共部分

比如/`^abc|^def/`，修改成 `/^(?:abc|def)/`。

又比如 `/this|that/`，修改成 `/th(?:is|at)/`。

这样做，可以减少匹配过程中可消除的重复。

### 减少分支的数量，缩小它们的范围

`/red|read/`，可以修改成 `/rea?d/`。此时分支和量词产生的回溯的成本是不一样的。但这样优化后，可读性会降低的。

# 正则相关 API

## 正则表达式的方法 #

### test()

**参数**

+ **str**：用来与正则表达式匹配的字符串

**返回值**

+ 如果正则表达式与指定的字符串匹配 ，返回 `true`；否则 `false`。

**描述**

+ ```js
    var regex = /^(\d{4})\D(\d{2})\D(\d{2})$/;
    var string = "2017-06-26";
    regex.test(string); // true
    console.log( RegExp.$1, RegExp.$2, RegExp.$3 );
    // => "2017" "06" "26"
    ```
+ 非标准用法：<https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/n>

### exec()

+ `exec()` 方法在一个指定字符串中执行一个搜索匹配。返回一个结果数组或 [`null`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/null)。

**参数**

+ **str**：要匹配正则表达式的字符串。

**返回值**

+ 如果匹配成功，`exec`() 方法返回一个数组，并**更新正则表达式对象的属性**。返回的数组将完全匹配成功的文本作为第一项，将正则括号里匹配成功的作为数组填充到后面。
+ 如果匹配失败，exec() 方法返回 [`null`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/null)。

**<span id='exec'>示例</span>**

+ ```js
    var re = /quick\s(brown).+?(jumps)/ig;
    var result = re.exec('The Quick Brown Fox Jumps Over The Lazy Dog');
    ```

**下面是这段脚本执行的返回值：**

![1554964018077](/img/user/programming/font-end/primitive/es/es-regexp/1554964018077.png)

### 当设置全局标志的正则使用 `test()` 和 `exex()`

如果正则表达式设置了全局标志，`test()` 的执行会改变正则表达式 [`lastIndex`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/lastIndex) 属性。连续的执行 `test()` 方法，后续的执行将会从 lastIndex 处开始匹配字符串，([`exec()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec) 同样改变正则本身的 `lastIndex属性值`)

```js
var regex = /foo/g;

// regex.lastIndex is at 0
regex.test('foo'); // true

// regex.lastIndex is now at 3
regex.test('foo'); // false
```

```js
var regex = /a/g;
console.log( regex.test("a"), regex.lastIndex );
console.log( regex.test("aba"), regex.lastIndex );
console.log( regex.test("ababc"), regex.lastIndex );
// => true 1
// => true 3
// => false 0
```

注意：上面代码中的第三次调用 `test`，因为这一次尝试匹配，开始从下标 `lastIndex` 即 3 位置处开始查找，自然就找不到了。

注意：不要把正则表达式字面量（或者 [`RegExp`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/RegExp) 构造器）放在 `while` 条件表达式里。由于每次迭代时 [`lastIndex`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/lastIndex) 的属性都被**重置**，如果匹配，将会造成一个死循环。并且要确保使用了 'g' 标记来进行全局的匹配，否则同样会造成死循环。

如果没有 `g`，自然都是从字符串第 0 个字符处开始尝试匹配：

```js
var regex = /a/;
console.log( regex.test("a"), regex.lastIndex );
console.log( regex.test("aba"), regex.lastIndex );
console.log( regex.test("ababc"), regex.lastIndex );
// => true 0
// => true 0
// => true 0
```

### 总结

这两个方法的 g，都不是真正意义上的 g，没有找出所有的匹配结果，都是找到一个就直接返回了，类似于 indexOf

## 字符串的正则方法

### ES6 API 模块化

ES6 将这 4 个方法，在语言内部全部调用 `RegExp` 的实例方法，从而做到所有与正则相关的方法，全都定义在 `RegExp` 对象上。

+ `String.prototype.match` 调用 `RegExp.prototype[Symbol.match]`
+ `String.prototype.replace` 调用 `RegExp.prototype[Symbol.replace]`
+ `String.prototype.search` 调用 `RegExp.prototype[Symbol.search]`
+ `String.prototype.split` 调用 `RegExp.prototype[Symbol.split]`

### search()

可以根据**正则表达式**去字符串中查找指定的内容

**参数**

+ **regexp**：一个 [正则表达式（regular expression）](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp) 对象。如果传入一个非正则表达式对象，则会使用 new RegExp(obj) 隐式地将其转换为正则表达式对象。

**返回值**

+ 将**第一个匹配**到的内容的**索引**返回
+ 如果没有匹配到任何内容，则返回 -1

**描述**

+ 当你想要知道字符串中是否存在某个模式（pattern）时可使用 `search`，类似于正则表达式的 [`test`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/test) 方法。
+ 当要了解更多匹配信息时，可使用 [`match`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/match)（会更慢），该方法类似于正则表达式的 [`exec`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec) 方法。

### match()

可以将**字符串中和正则表达式匹配**的内容提取出来

**参数**

+ 正则表达式，可以根据该正则表达式将字符串中符合要求的内容提取出来
+ 如果传入一个非正则表达式对象，则会隐式地使用 `new RegExp(obj)` 将其转换为一个 [`RegExp`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/RegExp) 。
+ 如果你没有给出任何参数并直接使用 match() 方法 ，你将会得到一 个包含空字符串的 [`Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Array) ：[""] 。

**返回值**

+ 如果使用 g 标志，则将返回与**完整正则表达式**匹配的所有结果（`Array`），但**不会返回捕获组**，或者未匹配 `null`。
+ 如果未使用 g 标志，则仅返回第一个完整匹配及其相关的**捕获组**（`Array`）。 在这种情况下 `str.match()` 将返回与 [`RegExp.exec()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec). 相同的结果

    > 更多信息请参考正则的<a href='#exec'>exec()</a>

**描述**

+ 如果正则表达式不包含 `g` 标志，`str.match()` 将返回与 [`RegExp.exec()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec). 相同的结果。

### matchAll()

+ <https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/@@matchAll>

### split()

`split()` 方法使用指定的分隔符字符串将一个 String 对象**分割**成字符串**数组**，以将字符串分隔为子字符串，以确定每个拆分的位置。

**参数**

+ **separator**：指定表示每个拆分应发生点的字符串。`separator` 可以是一个字符串或**正则表达式**。
    + 如果纯文本分隔符包含多个字符，则必须找到整个字符串来表示分割点。
    + 如果在 str 中省略或不出现分隔符，则返回的数组包含一个由整个字符串组成的元素。
    + 如果分隔符为空字符串，则将 str 原字符串中**每个字符**的数组形式返回。
+ **limit**：一个整数，限定返回的分割片段数量。当提供此参数时，split 方法会在指定分隔符的每次出现时分割该字符串，但在限制条目已放入数组时停止。如果在达到指定限制之前达到字符串的末尾，它可能仍然包含少于限制的条目。新数组中不返回剩下的文本。@@@

**返回值**

+ 返回原字符串以分隔符出现位置分隔而成的一个 [`Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Array)

**注意**

+ 如果 `separator` 包含**捕获括号**（capturing parentheses），则其**匹配结果**将会包含在返回的数组中。@@@

    ```js
    var myString = "2019-4-11";
    var splits = myString.split(/(-)/);
    
    console.log(splits); // Array(5) ["2019", "-", "4", "-", "11"]
    ```

### replace() #

**replace()** 方法返回一个由替换值（`replacement`）替换一些或所有匹配的模式（`pattern`）后的新字符串。

```js
str.replace(regexp|substr, newSubStr|function)
```

**参数**

+ `regexp`**(pattern)**：一个 [`RegExp`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/RegExp) 对象或者其字面量。该正则所匹配的内容会被第二个参数的**返回值**替换掉。
    + 在进行全局的搜索替换时，正则表达式需包含 `g` 标志。
+ `substr`**(pattern)**：一个将被 `newSubStr` 替换的 字符串。其被视为一整个字符串，而不是一个正则表达式。仅**第一个**匹配项会被替换。
+ `newSubStr` **(replacement)**：用于替换掉第一个参数在原字符串中的匹配部分的字符串
    + 该字符串中可以内插一些特殊的变量名字符串，捕获组的编号 @@@
+ `function` **(replacement)**：一个用来创建新子字符串的函数，该函数的返回值将替换掉**第一个**参数匹配到的结果。

**返回值**

+ 返回新的字符串，**同样的原字符串不会受到影响**

#### 使用字符串作为参数

```js
    var re = /(\w+)\s(\w+)/;
    var str = "John Smith";
    var newstr = str.replace(re, "$2, $1");
    // Smith, John
    console.log(newstr);
    ```
	
+ 当第二个参数是字符串时，如下的字符有特殊的含义：

    ```
    $1,$2,...,$99 匹配第1~99个分组里捕获的文本
    $& 匹配到的子串文本
    $` 匹配到的子串的左边文本 @@@
    $' 匹配到的子串的右边文本 @@@
    $$ 美元符号
    ```

+ 例如，把 "2,3,5"，变成 "5=2+3"：

    ```js
    var result = "2,3,5".replace(/(\d+),(\d+),(\d+)/, "$3=$1+$2");
    console.log(result);
    // => "5=2+3"
    ```

+ 又例如，把 "2,3,5"，变成 "222,333,555":

    ```js
    var result = "2,3,5".replace(/(\d+)/g, "$&$&$&");
    console.log(result);
    // => "222,333,555"
    ```

+ 再例如，把 "2+3=5"，变成 "2+3=2+3=5=5":

    ```js
    var result = "2+3=5".replace(/=/, "$&$`$&$'$&");
    console.log(result);
    // => "2+3=2+3=5=5"
    ```

#### 指定一个函数作为参数

你可以指定一个函数作为第二个参数。在这种情况下，当匹配执行后，该函数就会执行。 函数的返回值作为替换字符串。

> 注意：上面提到的特殊替换参数在这里不能被使用。

另外要注意的是，如果第一个参数是正则表达式，并且其为全局匹配模式，那么这个方法将被多次调用，每次匹配都会被调用。

下面是该函数的参数：

+ match：匹配的子串。（对应于上述的 $&。）
+ p1,p2, ...：假如 replace() 方法的第一个参数是一个 [`RegExp`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/RegExp) 对象，则代表第 n 个括号匹配的字符串。（对应于上述的\$1，$2 等。）例如，如果是用 `/(\a+)(\b+)/` 这个来匹配，`p1` 就是匹配的 `\a+`，`p2` 就是匹配的 `\b+`。
+ offset：匹配到的子字符串在原字符串中的偏移量。（比如，如果原字符串是 `'abcd'`，匹配到的子字符串是 `'bc'`，那么这个参数将会是 1）
+ string：被匹配到的原字符串

精确的参数个数依赖于 `replace()` 的第一个参数是否是一个正则表达式（[`RegExp`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/RegExp)）对象，以及这个正则表达式中**指定了多少个括号子串**。

**总的参数个数等于：(match + 捕获分组数 + offset + string) * 1**

下面的例子将会使 `newString` 变成 `'abc - 12345 - #$*%'`：

```js
function replacer(match, p1, p2, p3, offset, string) {
  // p1 is nondigits, p2 digits, and p3 non-alphanumerics
  return [p1, p2, p3].join(' - ');
}
var newString = 'abc12345#$*%'.replace(/(\D*)(\d*)(\W*)/, replacer);
console.log(newString);  // abc - 12345 - #$*%
```

如果没有匹配呢？没有匹配的话函数不会执行，replace 返回原字符串

## 正则表达式的四种操作

### 验证

验证是正则表达式最直接的应用，比如表单验证。

+ 在说验证之前，先要说清楚匹配是什么概念。
+ 所谓匹配，就是看目标字符串里是否有满足匹配的子串。因此，“匹配”的本质就是“查找”。
+ 有没有匹配，是不是匹配上，判断是否的操作，即称为“验证”。

比如，判断一个字符串中是否有数字

使用 search

```js
var regex = /\d/;
var string = "abc123";
console.log( !!~string.search(regex) );
// => true
```

使用 `test`

```js
var regex = /\d/;
var string = "abc123";
console.log( regex.test(string) );
// => true
```

使用 `match`

```js
var regex = /\d/;
var string = "abc123";
console.log( !!string.match(regex) );
// => true
```

使用 `exec`

```js
var regex = /\d/;
var string = "abc123";
console.log( !!regex.exec(string) );
// => true
```

其中，最常用的是 `test`

### 切分

匹配上了，我们就可以进行一些操作，比如切分。

所谓“切分”，就是把目标字符串，切成一段一段的。在 JS 中使用的是 `split`。

比如，目标字符串是 "html,css,javascript"，按逗号来切分：

```js
var regex = /,/;
var string = "html,css,javascript";
console.log( string.split(regex) );
// => ["html", "css", "javascript"]
```

又比如，如下的日期格式：

> 2017/06/26
>
> 2017.06.26
>
> 2017-06-26

可以使用 `split`“切出”年月日：

```js
var regex = /\D/;
console.log( "2017/06/26".split(regex) );
console.log( "2017.06.26".split(regex) );
console.log( "2017-06-26".split(regex) );
// => ["2017", "06", "26"]
// => ["2017", "06", "26"]
// => ["2017", "06", "26"]
```

### 提取

虽然整体匹配上了，但有时需要提取部分匹配的数据。

此时正则通常要使用分组引用（分组捕获）功能，还需要配合使用相关 API。

这里，还是以日期为例，提取出年月日。注意下面正则中的括号：

`match`

```js
var regex = /^(\d{4})\D(\d{2})\D(\d{2})$/;
var string = "2017-06-26";
console.log( string.match(regex) );
// =>["2017-06-26", "2017", "06", "26", index: 0, input: "2017-06-26"]
```

`exec`

```js
var regex = /^(\d{4})\D(\d{2})\D(\d{2})$/;
var string = "2017-06-26";
console.log( regex.exec(string) );
// =>["2017-06-26", "2017", "06", "26", index: 0, input: "2017-06-26"]
```

`test`

```js
var regex = /^(\d{4})\D(\d{2})\D(\d{2})$/;
var string = "2017-06-26";
regex.test(string);
console.log( RegExp.$1, RegExp.$2, RegExp.$3 );
// => "2017" "06" "26"
```

`search`

```js
var regex = /^(\d{4})\D(\d{2})\D(\d{2})$/;
var string = "2017-06-26";
string.search(regex);
console.log( RegExp.$1, RegExp.$2, RegExp.$3 );
// => "2017" "06" "26"
```

`repalce`

```js
var regex = /^(\d{4})\D(\d{2})\D(\d{2})$/;
var string = "2017-06-26";
var date = [];
string.replace(regex, function(match, year, month, day) {
 date.push(year, month, day);
});
console.log(date);
// => ["2017", "06", "26"]
```

### 替换

找，往往不是目的，通常下一步是为了替换。在 JS 中，使用 `replace` 进行替换。

比如把日期格式，从 yyyy-mm-dd 替换成 yyyy/mm/dd：

```js
var string = "2017-06-26";
var today = new Date( string.replace(/-/g, "/") );
console.log( today );
// => Mon Jun 26 2017 00:00:00 GMT+0800 (中国标准时间)
```

这里只是简单地应用了一下 `replace`。但，`replace` 方法是强大的，是需要重点掌握的。

## 相关 API 注意要点

从上面可以看出用于正则操作的方法，共有 6 个，字符串实例 4 个，正则实例 2 个：

```js
String#search

String#split

String#match

String#replace

RegExp#test

RegExp#exec
```

### Search 和 Match 的参数问题

我们知道字符串实例的那 4 个方法参数都支持正则和字符串。

但 `search` 和 `match`，会把字符串转换为正则的。

```js
var string = "2017.06.27";

console.log( string.search(".") );
// => 0
//需要修改成下列形式之一
console.log( string.search("\\.") );
console.log( string.search(/\./) );
// => 4
// => 4

console.log( string.match(".") );
// => ["2", index: 0, input: "2017.06.27"]
//需要修改成下列形式之一
console.log( string.match("\\.") );
console.log( string.match(/\./) );
// => [".", index: 4, input: "2017.06.27"]
// => [".", index: 4, input: "2017.06.27"]

console.log( string.split(".") );
// => ["2017", "06", "27"]

console.log( string.replace(".", "/") );
// => "2017/06.27"
```

具体的转换规则，貌似只是 `/${string}/`

### Match 返回结果的格式问题

`match` 返回结果的格式，与正则对象是否有修饰符 `g` 有关。

```js
var string = "2017.06.27";
var regex1 = /\b(\d+)\b/;
var regex2 = /\b(\d+)\b/g;
console.log( string.match(regex1) );
console.log( string.match(regex2) );
// => ["2017", "2017", index: 0, input: "2017.06.27"]
// => ["2017", "06", "27"]
```

没有 `g`，返回的是标准匹配格式，即，数组的第一个元素是整体匹配的内容，接下来是分组捕获的内容，然后是整体匹配的第一个下标，最后是输入的目标字符串。

有 `g`，返回的是所有匹配的内容。

当没有匹配时，不管有无 `g`，都返回 `null`。

### Exec 比 Match 更强大

当正则没有 `g` 时，使用 `match` 返回的信息比较多。但是有 `g` 后，就没有关键的信息 `index` 了。

而 `exec` 方法就能解决这个问题，它能接着上一次匹配后继续匹配：

```js
var string = "2017.06.27";
var regex2 = /\b(\d+)\b/g;
console.log( regex2.exec(string) );
console.log( regex2.lastIndex);
console.log( regex2.exec(string) );
console.log( regex2.lastIndex);
console.log( regex2.exec(string) );
console.log( regex2.lastIndex);
console.log( regex2.exec(string) );
console.log( regex2.lastIndex);
// => ["2017", "2017", index: 0, input: "2017.06.27"]
// => 4
// => ["06", "06", index: 5, input: "2017.06.27"]
// => 7
// => ["27", "27", index: 8, input: "2017.06.27"]
// => 10
// => null
// => 0
```

其中正则实例 `lastIndex` 属性，表示下一次匹配开始的位置。

比如第一次匹配了“2017”，开始下标是 0，共 4 个字符，因此这次匹配结束的位置是 3，下一次开始匹配的位置是 4。

从上述代码看出，在使用 `exec` 时，经常需要配合使用 `while` 循环：

```js
var string = "2017.06.27";
var regex2 = /\b(\d+)\b/g;
var result;
while ( result = regex2.exec(string) ) {
 console.log( result, regex2.lastIndex );
}
// => ["2017", "2017", index: 0, input: "2017.06.27"] 4
// => ["06", "06", index: 5, input: "2017.06.27"] 7
// => ["27", "27", index: 8, input: "2017.06.27"] 10
```

### Test 整体匹配时需要使用^和 $

这个相对容易理解，因为 `test` 是看目标字符串中是否有子串匹配正则，即有部分匹配即可。

如果，要整体匹配，正则前后需要添加开头和结尾：

```js
console.log( /123/.test("a123b") );
// => true
console.log( /^123$/.test("a123b") );
// => false
console.log( /^123$/.test("123") );
// => true
```

### Split 相关注意事项

`split` 方法看起来不起眼，但要注意的地方有两个的。

第一，它可以有第二个参数，表示结果数组的最大长度：

```JS
var string = "html,css,javascript";
console.log( string.split(/,/, 2) );
// =>["html", "css"]
```

第二，正则使用**分组**时，结果数组中是包含分隔符的：

```js
var string = "html,css,javascript";
console.log( string.split(/(,)/) );
// =>["html", ",", "css", ",", "javascript"]
```

### Replace 是很强大的

### 使用构造函数需要注意的问题

一般不推荐使用构造函数生成正则，而应该优先使用字面量。因为用构造函数会多写很多 `\`。

```js
var string = "2017-06-27 2017.06.27 2017/06/27";
var regex = /\d{4}(-|\.|\/)\d{2}\1\d{2}/g;
console.log( string.match(regex) );
// => ["2017-06-27", "2017.06.27", "2017/06/27"]

regex = new RegExp("\\d{4}(-|\\.|\\/)\\d{2}\\1\\d{2}", "g");
console.log( string.match(regex) );
// => ["2017-06-27", "2017.06.27", "2017/06/27"]
```

### 修饰符

ES5 中修饰符，共 3 个：

+ `g` 全局匹配，即找到所有匹配的，单词是 global
+ `i` 忽略字母大小写，单词 ingoreCase
+ `m` 多行匹配，只影响 `^` 和 `$`，二者变成行的概念，即行开头和行结尾。单词是 multiline

当然正则对象也有相应的只读属性：

```js
var regex = /\w/img;
console.log( regex.global );
console.log( regex.ignoreCase );
console.log( regex.multiline );
// => true
// => true
// => true
```

### Source 属性

正则实例对象属性，除了 `global`、`ingnoreCase`、`multiline`、`lastIndex` 属性之外，还有一个 `source` 属性。

它什么时候有用呢？

比如，在构建动态的正则表达式时，可以通过查看该属性，来确认构建出的正则到底是什么：

```js
var className = "high";
var regex = new RegExp("(^|\\s)" + className + "(\\s|$)");
console.log( regex.source )
// => (^|\s)high(\s|$) 即字符串"(^|\\s)high(\\s|$)"
```

## RegExp 构造函数

### 参数

参数是字符串，这时第二个参数表示正则表达式的修饰符（flag）

```js
var regex = new RegExp('xyz', 'i');
// 等价于
var regex = /xyz/i;
```

参数是一个正则表示式，这时会返回一个原有正则表达式的拷贝。

```js
var regex = new RegExp(/xyz/i);
// 等价于
var regex = /xyz/i;
```

### 注意

当参数是正则表达式时，ES5 不允许此时使用第二个参数添加修饰符，否则会报错。

```js
var regex = new RegExp(/xyz/, 'i');
// Uncaught TypeError: Cannot supply flags when constructing one RegExp from another
```

ES6 改变了这种行为。如果 `RegExp` 构造函数第一个参数是一个正则对象，那么可以使用第二个参数指定修饰符。而且，返回的正则表达式会忽略原有的正则表达式的修饰符，只使用新指定的修饰符。

```js
new RegExp(/abc/ig, 'i').flags
// "i" 
```

### 修饰符

+ 如果指定，标志可以具有以下值的任意组合：
    + **g**：全局匹配; 找到所有匹配，而不是在第一个匹配后停止
    + **i**：忽略大小写
    + **m**：多行; 将开始和结束字符（^和 $）视为在多行上工作（也就是，分别匹配每一行的开始和结束（由 \n 或 \r 分割），而不只是只匹配整个输入字符串的最开始和最末尾处。
    + **u**：Unicode; 将模式视为 Unicode 序列点的序列
    + **y**：粘性匹配; 仅匹配目标字符串中此正则表达式的 lastIndex 属性指示的索引 (并且不尝试从任何后续的索引匹配)。

### RegExp.prototype.flags 属性

+ ES6 为正则表达式新增了 `flags` 属性，会返回正则表达式的修饰符。

    ```js
    // ES5 的 source 属性
    // 返回正则表达式的正文
    /abc/ig.source
    // "abc"
    
    // ES6 的 flags 属性
    // 返回正则表达式的修饰符
    /abc/ig.flags
    // 'gi'
    ```

### RegExp.prototype.global

+ 是否开启全局匹配，也就是匹配目标字符串中所有可能的匹配项，而不是只进行第一次匹配。

### RegExp.prototype.ignoreCase

+ 在匹配字符串时是否要忽略字符的大小写。

### RegExp.prototype.lastIndex

下次匹配开始的字符串索引位置，理解为 `nextStartindex` 更加合理

### RegExp.prototype.multiline

+ 是否开启多行模式匹配（影响 ^ 和 $ 的行为）。

### RegExp.prototype.source

+ 正则对象的源模式文本。

### RegExp.prototype.sticky

+ 是否开启粘滞匹配。

## 构造函数静态属性 #

构造函数的静态属性基于所执行的最近一次正则操作而变化。除了是 `$1`,...,`$9` 之外，还有几个不太常用的属性（有兼容性问题）：

+ `RegExp.input` 最近一次目标字符串，简写成 `RegExp["$_"]`
+ `RegExp.lastMatch` 最近一次匹配的文本，简写成 `RegExp["$&"]`
+ `RegExp.lastParen` 最近一次捕获的文本，简写成 `RegExp["$+"]`
+ `RegExp.leftContext` 目标字符串中 `lastMatch` 之前的文本，简写 `RegExp["$`"]
+ `RegExp.rightContext` 目标字符串中 `lastMatch` 之后的文本，简写成 `RegExp["$'"]`

测试代码如下：

```js
var regex = /([abc])(\d)/g;
var string = "a1b2c3d4e5";
string.match(regex);

console.log( RegExp.input );
console.log( RegExp["$_"]);
// => "a1b2c3d4e5"

console.log( RegExp.lastMatch );
console.log( RegExp["$&"] );
// => "c3"

console.log( RegExp.lastParen );
console.log( RegExp["$+"] );
// => "3"

console.log( RegExp.leftContext );
console.log( RegExp["$`"] );
// => "a1b2"

console.log( RegExp.rightContext );
console.log( RegExp["$'"] );
// => "d4e5"
```

# 正则拆分

## 匹配双引号内的内容

参考匹配 id，`/id=".*"/`，必须使用惰性匹配，且回导致回溯，因此 `/id="[^"]*"/`

## 要求一个位置不是开头

参考数字的千位分隔符表示法，`/(?!^)/`，表示不是 开头 的其他所有位置

## 判断是否包含有某一种字符

参考验证密码问题

假设，要求的**必须包含数字**，怎么办？此时我们可以使用 `(?=.*[0-9])` 来做。

`(?=.*[0-9])` 代表：任意个任意字符后得跟着一个数字，在这样一段字符串的前面

+ 这意味着，在这个**位置**后面的字符串必须包含一个数子

`(?=.*[0-9])^`：意味着这个位置必须在开头，因为空字符是可以有多个的

+ 这意味着：整段字符串，必须包含一个数字

因此正则变成：

```js
var reg = /(?=.*[0-9])^[0-9A-Za-z]{6,12}$/;
```

**同时包含具体两种字符**

+ 比如同时包含数字和小写字母，可以用 `(?=.*[0-9])(?=.*[a-z])` 来做。

    ```js
    var reg = /(?=.*[0-9])(?=.*[a-z])^[0-9A-Za-z]{6,12}$/;
    ```

## 匹配 Style 字符串的某个属性

```js
        // 1. 考虑行内样式的空格
        // 2. 结尾的分号不一定有
        // 3. 捕获组使用惰性匹配
        // 4. replace 全局，开发者可能写多个 color
        const placeholderStyle = this.data.get('__placeholderStyle');
        // const regex = /\s*color\s*:\s*(\S*?)\s*;?\s*$/gi;
  // 分号不一定有，导致惰性匹配的这个无法匹配全部的
        const regex = /\s*color\s*:\s*([^;]*)\s*;?\s*/gi;
        let color;
        placeholderStyle.replace(regex, (match, p1) => {
            if (p1) {
                color = p1;
            }
        });
```

```js
" Color : red   ; height:100px; Color : blue"
```

san-native 里的做法

```js
    function getStyle (node: san.Element) {
        const styleMap = Object.create(null);
        let style = node.el.getAttribute('style');
        const styleArr = style && style.trim().split(/\s*;\s*/) || [];
        for (let i = 0; i < styleArr.length; i++) {
            const pair = styleArr[i].split(/\s*:\s*/);
            // 'height:;width:;' 这种属于不合法的style，过滤掉
            if (pair.length !== 2 || pair[1] === '') {
                continue;
            }
            styleMap[upperCase(pair[0])] = pair[1];
        }
        return styleMap;
    }
```

# 模板字符串

模板字符串（template string）是增强版的字符串，用反引号（`）标识。

## 当作普通字符串使用

它可以当作普通字符串使用，也可以用来定义多行字符串

  ```js
  // 普通字符串
  `In JavaScript '\n' is a line-feed.`

  // 多行字符串
  `In JavaScript this is
   not legal.`

  console.log(`string text line 1
  string text line 2`);
  ```

如果使用模板字符串表示多行字符串，所有的空格和缩进都会被保留在输出之中。比如 `<ul>` 标签前面会有一个换行。如果你不想要这个换行，可以使用 `trim` 方法消除它。

比如：

  ![string](/img/user/programming/font-end/primitive/es/es-regexp/string1.png)

注意，打印的结果中第一行是一个换行，你可以使用 trim 函数消除换行。消除后：

  ![string](/img/user/programming/font-end/primitive/es/es-regexp/string2.png)

## 在字符串中嵌入表达式

模板字符串中嵌入变量，需要将变量名写在 `${}` 之中。

  ```js
// 字符串中嵌入变量
let name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`
  ```

大括号内部可以放入任意的 JavaScript **表达式**，可以进行运算，以及引用对象属性。

模板字符串之中还能调用函数。

  ```js
  function fn() {
    return "Hello World";
  }

  `foo ${fn()} bar`
  // foo Hello World bar
  ```

如果大括号的值不是字符串，将按照一般的规则转为字符串。比如，大括号中是一个对象，将默认调用对象的 `toString` 方法。

由于模板字符串的大括号内部，就是执行 JavaScript 代码，因此如果大括号内部是一个字符串，将会原样输出。

  ```js
`Hello ${'World'}`
// "Hello World"
  ```

## 嵌套使用

模板字符串甚至还能嵌套。

  ```js
const tmpl = addrs => `
  <table>
  ${addrs.map(addr => `
    <tr><td>${addr.first}</td></tr>
    <tr><td>${addr.last}</td></tr>
  `).join('')}
  </table>
`;
  ```

上面代码中，模板字符串的变量之中，又嵌入了另一个模板字符串，使用方法如下。

  ```js
  const data = [
      { first: '<Jane>', last: 'Bond' },
      { first: 'Lars', last: '<Croft>' },
  ];

  console.log(tmpl(data));
  // <table>
  //
  //   <tr><td><Jane></td></tr>
  //   <tr><td>Bond</td></tr>
  //
  //   <tr><td>Lars</td></tr>
  //   <tr><td><Croft></td></tr>
  //
  // </table>
  ```

## 隐式转换

注意，在 li 标签中间多了一个逗号，这是因为当大括号中的值不是字符串时，会将其转为字符串，比如一个数组 [1, 2, 3] 就会被转为 1,2,3，逗号就是这样产生的。

如果你要消除这个逗号，你可以先 join 一下：

  ![string](/img/user/programming/font-end/primitive/es/es-regexp/string4.png)

## 标签模板

更高级的形式的模板字符串是带标签的模板字符串。标签使您可以用函数解析模板字符串。

用于该标签的函数的名称可以被命名为任何名字。

**参数**

+ 标签函数的第一个参数包含一个字符串值的数组。
+ 其余的参数与表达式相关。

**返回值**: 最后，你的函数可以返回处理好的的字符串（或者它可以返回完全不同的东西 , 如下个例子所述）。

**示例**

模板标签是一个非常重要的能力，模板字符串可以紧跟在一个函数名后面，该函数将被调用来处理这个模板字符串，举个例子：

  ```js
let x = 'Hi', y = 'Kevin';
var res = message`${x}, I am ${y}`;
console.log(res);
  ```

我们可以自定义 message 函数来处理返回的字符串:

  ```js
// literals 文字
// 注意在这个例子中 literals 的第一个元素和最后一个元素都是空字符串
function message(literals, value1, value2) {
	console.log(literals); // [ "", ", I am ", "" ]
	console.log(value1); // Hi
	console.log(value2); // Kevin
}
  ```

我们利用这些参数将其拼合回去：

  ```js
  function message(literals, ...values) {
  	let result = '';

  	for (let i = 0; i < values.length; i++) {
  		result += literals[i];
  		result += values[i];
  	}

  	result += literals[literals.length - 1];

  	return result;
  }
  ```

你也可以这样写：

  ```js
  function message(literals, ...values) {
  	let result = literals.reduce((prev, next, i) => {
  	    let value = values[i - 1];
  	    return prev + value + next;
  	});

  	return result;
  }
  ```

学着拼合回去是一件非常重要的事情，因为我们经过各种处理，最终都还是要拼回去的……

**includeArrays**

前面我们讲到为了避免 ${} 表达式中返回一个数组，自动转换会导致多个逗号的问题，需要每次都将数组最后再 join('') 一下，再看一遍例子：

利用标签模板，我们可以轻松的解决这个问题：

  ```js
  function includeArrays(template, ...expressions) {
      let result = template.reduce((prev, next, i) => {

          let expression = expressions[i - 1];

          if (Array.isArray(expression)) {
              expression = expression.join('');
          }

          return prev + expression + next;
      });

      result = result.trim();

      return result;
  }
  ```

## 报错

如果模板字符串中的变量没有声明，将报错。

  ```js
// 变量place没有声明
let msg = `Hello, ${place}`;
// 报错
  ```

# FAQ

#faq/js

## 匹配 Style 中的某个属性

```js
        const transitionReg = /\s*transition\s*:\s*([^;]*)\s*;?\s*/gi
        const transformReg = /\s*transform\s*:\s*([^;]*);?\s*/gi
```

因为 `[^;]` 也包括了\s，所以分号前的那个\s 是多余的，去掉也是 ok 的

如果保留的话就可以免除一个 trim，还是可以保留的，这里需要考虑一个效率的问题，\s 的存在和回溯的可能？哪个性能更好

不用考虑那些空格，直接按照

```js
        function cached(styleStr: string) {
            const styleMap = Object.create(null);
            const styleArr = styleStr && styleStr.trim().split(/\s*;\s*/);
            for (let i = 0; i < styleArr.length; i++) {
                const pair = styleArr[i].split(/\s*:\s*/);
                // 'height:;width:;' 这种属于不合法的style，过滤掉
                if (pair.length !== 2 || pair[1] === '') {
                    continue;
                }
                styleMap[upperCase(pair[0])] = pair[1];
            }
            return styleMap;
        }
```

## 匹配多个空格

```ts
const keywordList = value.split(/\s+/);
```

## 获取函数的第一个参数

```js
export const getFirstArgument = fn => (typeof fn === 'function'
    // ^[\s(] 
 && (/^[\s(]*function[^(]*\(\s*([^\s,)]+)/.exec(fn) || 0)[1]) || '';
```

## 匹配相对路径

`if (/^\.\.?\/|^[^/]+/.test(src) && !/^data:image\//.test(src)) {}`

## 使用构造函数生成正则表达式

我们知道要优先使用字面量来创建正则，但有时正则表达式的主体是不确定的，此时可以使用构造函数来创建。模拟 `getElementsByClassName` 方法，就是很能说明该问题的一个例子。

这里 `getElementsByClassName` 函数的实现思路是：

+ 比如要获取 className 为 "high" 的 dom 元素；
+ 首先生成一个正则：`/(^|\s)high(\s|$)/`；
+ 然后再用其逐一验证页面上的所有 dom 元素的类名，拿到满足匹配的元素即可。

代码如下 (可以直接复制到本地查看运行效果)：

```html
<p class="high">1111</p>
<p class="high">2222</p>
<p>3333</p>
<script>
function getElementsByClassName(className) {
 var elements = document.getElementsByTagName("*");
 var regex = new RegExp("(^|\\s)" + className + "(\\s|$)");
 var result = [];
 for (var i = 0; i < elements.length; i++) {
  var element = elements[i];
  if (regex.test(element.className)) {
   result.push(element)
  }
 }
 return result;
}
var highs = getElementsByClassName('high');
highs.forEach(function(item) {
 item.style.color = 'red';
});
</script>
```

## 使用字符串保存数据

一般情况下，我们都愿意使用数组来保存数据。但我看到有的框架中，使用的却是字符串。

使用时，仍需要把字符串切分成数组。虽然不一定用到正则，但总感觉酷酷的，这里分享如下：

```js
var utils = {};
"Boolean|Number|String|Function|Array|Date|RegExp|Object|Error".split("|").forEach(function(item) {
 utils["is" + item] = function(obj) {
  return {}.toString.call(obj) == "[object " + item + "]";
 };
});
console.log( utils.isArray([1, 2, 3]) );
// => true
```

## If 语句中使用正则替代&&

比如，模拟 ready 函数，即加载完毕后再执行回调（不兼容 ie 的）：

```js
var readyRE = /complete|loaded|interactive/;
function ready(callback) {
  if (readyRE.test(document.readyState) && document.body) {
    callback()
  } 
  else {
    document.addEventListener(
      'DOMContentLoaded', 
      function () {
        callback()
      },
      false
    );
  }
};
ready(function() {
  alert("加载完毕！")
});
```

## 使用强大的 Replace

因为 `replace` 方法比较强大，有时用它根本不是为了替换，只是拿其匹配到的信息来做文章。

这里以查询字符串（querystring）压缩技术为例，注意下面 `replace` 方法中，回调函数根本没有返回任何东西。

```js
function compress(source) {
 var keys = {};
 source.replace(/([^=&]+)=([^&]*)/g, function(full, key, value) {
  keys[key] = (keys[key] ? keys[key] + ',' : '') + value;
 });
 var result = [];
 for (var key in keys) {
  result.push(key + '=' + keys[key]);
 }
 return result.join('&');
}

console.log( compress("a=1&b=2&a=3&b=4") );
// => "a=1,3&b=2,4"
```

## 综合运用

最后这里再做个简单实用的正则测试器。

```js
<section>
 <div id="err"></div>
 <input id="regex" placeholder="请输入正则表达式">
 <input id="text" placeholder="请输入测试文本">
 <button id="run">测试一下</button>
 <div id="result"></div>
</section>
<style>
section{
 display:flex;
 flex-direction:column;
 justify-content:space-around;
 height:300px;
 padding:0 200px;
}
section *{
 min-height:30px;
}
#err {
 color:red;
}
#result{
 line-height:30px;
}
.info {
 background:#00c5ff;
 padding:2px;
 margin:2px;
 display:inline-block;
}
</style>
<script>
(function() {
 // 获取相应dom元素
 var regexInput = document.getElementById("regex");
 var textInput = document.getElementById("text");
 var runBtn = document.getElementById("run");
 var errBox = document.getElementById("err");
 var resultBox = document.getElementById("result");
 
 // 绑定点击事件
 runBtn.onclick = function() {
  // 清除错误和结果
  errBox.innerHTML = "";
  resultBox.innerHTML = "";
  
  // 获取正则和文本
  var text = textInput.value;
  var regex = regexInput.value;
  
  if (regex == "") {
   errBox.innerHTML = "请输入正则表达式";
  } else if (text == "") {
   errBox.innerHTML = "请输入测试文本";
  } else {
   regex = createRegex(regex);
   if (!regex) return;
   var result, results = [];
   
   // 没有修饰符g的话，会死循环
   if (regex.global) {
    while(result = regex.exec(text)) {
     results.push(result);
    }
   } else {
    results.push(regex.exec(text));
   }
      
   if (results[0] == null) {
    resultBox.innerHTML = "匹配到0个结果";
    return;
   }
   
   // 倒序是有必要的
   for (var i = results.length - 1; i >= 0; i--) {
    var result = results[i];
    var match = result[0];
    var prefix = text.substr(0, result.index);
    var suffix = text.substr(result.index + match.length);
    text = prefix 
     + '<span class="info">'
     + match
     + '</span>'
     + suffix;
   }
   resultBox.innerHTML = "匹配到" + results.length + "个结果:<br>" + text;
  }
 };
 
 // 生成正则表达式，核心函数
 function createRegex(regex) {
  try {
   if (regex[0] == "/") {
    regex = regex.split("/");
    regex.shift();
    var flags = regex.pop();
    regex = regex.join("/");
    regex = new RegExp(regex, flags);
   } else {
    regex = new RegExp(regex, "g");
   }
   return regex;
  } catch(e) {
   errBox.innerHTML = "无效的正则表达式";
   return false;
  }
 }
})();
</script>
```

<https://juejin.im/post/5965943ff265da6c30653879#heading-38>

## 将字符串处理成驼峰格式

```ts
/**
 * 将字符串处理成驼峰格式
 *
 * @example
 * '  left-center' => leftCenter
 *
 * @param str 待处理字符串
 * @returns
 */
function kebabToCamel (str:string) {
    return str.trim().replace(/-(.)/g, (m, x) => x.toUpperCase());
}
```

## 匹配字符的案例

![es-regexp](programming/font-end/primitive/es/es-regexp.md#匹配字符的案例)

## 匹配位置的案例

![es-regexp](programming/font-end/primitive/es/es-regexp.md#匹配位置的案例)

## 常用正则

[《前端表单验证常用的 15 个 JS 正则表达式》](http://caibaojian.com/form-regexp.html) [《JS 常用正则汇总》](https://www.jianshu.com/p/1cb5229325a7)

# Experience

#programming-experience

正则直接丢到 chatGPT 里去解析

扩展一下, 所有看不懂的代码都可以丢到里面去解析
