---
{"dg-publish":true,"permalink":"/programming/font-end/framework/engineering/eslint/lint-js/"}
---


所有的代码规范文件都是类似的, 如果可以使用 eslint 规则定义就直接用 eslint

所以这里还留着的更多是一些权衡时的优选

# 结构

## 缩进

**[建议] 在语句的行长度超过 `120` 时，根据逻辑条件合理缩进。**

```js
// 较复杂的逻辑条件组合，将每个条件独立一行，逻辑运算符放置在行首进行分隔，或将部分逻辑按逻辑组合进行分隔。
// 建议最终将右括号 ) 与左大括号 { 放在独立一行，保证与 `if` 内语句块能容易视觉辨识。
if (user.isAuthenticated()
    && user.isInRole('admin')
    && user.hasAuthority('add-admin')
    || user.hasAuthority('delete-admin')
) {
    // Code
}

// 按一定长度截断字符串，并使用 + 运算符进行连接。
// 分隔字符串尽量按语义进行，如不要在一个完整的名词中间断开。
// 特别的，对于 HTML 片段的拼接，通过缩进，保持和 HTML 相同的结构。
var html = '' // 此处用一个空字符串，以便整个 HTML 片段都在新行严格对齐
    + '<article>'
    +     '<h1>Title here</h1>'
    +     '<p>This is a paragraph</p>'
    +     '<footer>Complete</footer>'
    + '</article>';

// 也可使用数组来进行拼接，相对 `+` 更容易调整缩进。
var html = [
    '<article>',
        '<h1>Title here</h1>',
        '<p>This is a paragraph</p>',
        '<footer>Complete</footer>',
    '</article>'
];
html = html.join('');

// 当参数过多时，将每个参数独立写在一行上，并将结束的右括号 ) 独立一行。
// 所有参数必须增加一个缩进。
foo(
    aVeryVeryLongArgument,
    anotherVeryLongArgument,
    callback
);

// 也可以按逻辑对参数进行组合。
// 最经典的是 baidu.format 函数，调用时将参数分为“模板”和“数据”两块
baidu.format(
    dateFormatTemplate,
    year, month, date, hour, minute, second
);

// 当函数调用时，如果有一个或以上参数跨越多行，应当每一个参数独立一行。
// 这通常出现在匿名函数或者对象初始化等作为参数时，如 `setTimeout` 函数等。
setTimeout(
    function () {
        alert('hello');
    },
    200
);

order.data.read(
    'id=' + me.model.id,
    function (data) {
        me.attchToModel(data.result);
        callback();
    },
    300
);

// 链式调用较长时采用缩进进行调整。
$('#items')
    .find('.selected')
    .highlight()
    .end();

// 三元运算符由3部分组成，因此其换行应当根据每个部分的长度不同，形成不同的情况。
var result = thisIsAVeryVeryLongCondition
    ? resultA : resultB;

var result = condition
    ? thisIsAVeryVeryLongResult
    : resultB;

// 数组和对象初始化的混用，严格按照每个对象的 `{` 和结束 `}` 在独立一行的风格书写。
var array = [
    {
        // ...
    },
    {
        // ...
    }
];
```

## Return

return 字符数较多，换行很别扭，可以参考 react，用一个括号包起来，然后换行

# 命名

对象的名字，不要用 name 这种，直接使用对应的，groups 对象，就用 group，member，seiyuu

一个对象可以由很多定语，但是只需要在关键的导出和导入完全定义即可，或者取决于上下文是否有歧义，否则尽可能精简

```js
const CharaPixivIllustTotalRank = 123;
// 如果上下文中没有其他类型的 totalRank， 其实简写为 totalRank 就行
```

其实可以使用 name，但是要在引用的时候修正 {name:groupName}，不然引用之后就丢失了语义

**[强制] `枚举变量 ` 使用 `Pascal 命名法 `，` 枚举的属性 ` 使用 ` 全部字母大写，单词间下划线分隔` 的命名方式。**

```
var TargetState = {
    READING: 1,
    READED: 2,
    APPLIED: 3,
    READY: 4
};
```

**[强制] `类 ` 使用 `Pascal 命名法`。**

```
function TextNode(options) {
}
```

**[建议] `函数名 ` 使用 `动宾短语`。**

```
function getStyle(element) {
}
```

**[建议] `boolean` 类型的变量使用 `is` 或 `has` 开头。**

```
var isReady = false;
var hasMoreCommands = false;
```

**[建议] `Promise对象 ` 用 `动宾短语的进行时` 表达。**

```
var loadingData = ajax.get('url');
loadingData.then(callback);
```

**[强制] 自定义事件的 `事件名` 必须全小写。**

在 JavaScript 广泛应用的浏览器环境，绝大多数 DOM 事件名称都是全小写的。为了遵循大多数 JavaScript 开发者的习惯，在设计自定义事件时，事件名也应该全小写。

# 文档化注释

## 细节注释

对于内部实现、不容易理解的逻辑说明、摘要信息等，我们可能需要编写细节注释。

**[建议] 细节注释遵循单行注释的格式。说明必须换行时，每行是一个单行注释的起始。**

```js
function foo(p1, p2, opt_p3) {
    // 这里对具体内部逻辑进行说明
    // 说明太长需要换行
    for (...) {
        ....
    }
}
```

**[强制] 有时我们会使用一些特殊标记进行说明。特殊标记必须使用单行注释的形式。下面列举了一些常用标记：**

解释：

1. TODO: 有功能待实现。此时需要对将要实现的功能进行简单说明。
2. FIXME: 该处代码运行没问题，但可能由于时间赶或者其他原因，需要修正。此时需要对如何修正进行简单说明。
3. HACK: 为修正某些问题而写的不太好或者使用了某些诡异手段的代码。此时需要对思路或诡异手段进行描述。
4. XXX: 该处存在陷阱。此时需要对陷阱进行描述。

# 变量

[强制] 变量、函数在使用前必须先定义。

[强制] 每个 `var,let,const` 只能声明一个变量。

**[强制] 变量必须 `即用即声明`，不得在函数或其它形式的代码块起始位置统一声明所有变量。**

变量声明与使用的距离越远，出现的跨度越大，代码的阅读与维护成本越高。虽然 JavaScript 的变量是函数作用域，还是应该根据编程中的意图，缩小变量出现的距离空间。

```js
// good
function kv2List(source) {
    var list = [];

    for (var key in source) {
        if (source.hasOwnProperty(key)) {
            var item = {
                k: key,
                v: source[key]
            };

            list.push(item);
        }
    }

    return list;
}

// bad
function kv2List(source) {
    var list = [];
    var key;
    var item;

    for (key in source) {
        if (source.hasOwnProperty(key)) {
            item = {
                k: key,
                v: source[key]
            };

            list.push(item);
        }
    }

    return list;
}
```

# Falsy 值与条件判断

可用性判断 0 null undefined 的边界值的处理

必要性判断 0 null undefined 等边界值的处理，状态的判断，然后再执行函数

[空值合并运算符（??） - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing)

比 `||` 更好一点

表驱动法：<https://juejin.im/post/5dcbed6af265da309e6f19de#heading-4>

[强制] 在 Equality Expression 中使用类型严格的 `\===`。仅当判断 `null` 或 `undefined` 时，允许使用 `\== null`

[建议] 按执行频率排列分支的顺序。

**[建议] 对于相同变量或表达式的多值条件，用 `switch` 代替 `if`。**

**[建议] 尽可能使用简洁的表达式。**

字符串为空

```js
// good
if (!name) {
    // ......
}

// bad
if (name === '') {
    // ......
}
```

字符串非空

```js
// good
if (name) {
    // ......
}

// bad
if (name !== '') {
    // ......
}
```

数组非空

```js
// good
if (collection.length) {
    // ......
}

// bad
if (collection.length > 0) {
    // ......
}
```

布尔不成立

```js
// good
if (!notTrue) {
    // ......
}

// bad
if (notTrue === false) {
    // ......
}
```

null 或 undefined

```js
// good
if (noValue == null) {
  // ......
}

// bad
if (noValue === null || typeof noValue === 'undefined') {
  // ......
}
```

# 循环

**[建议] 不要在循环体中包含函数表达式，事先将函数提取到循环体外。**

循环体中的函数表达式，运行过程中会生成循环次数个函数对象。

```js
// good
function clicker() {
    // ......
}

for (var i = 0, len = elements.length; i < len; i++) {
    var element = elements[i];
    addListener(element, 'click', clicker);
}

// bad
for (var i = 0, len = elements.length; i < len; i++) {
    var element = elements[i];
    addListener(element, 'click', function () {});
}
```

**[建议] 对循环内多次使用的不变值，在循环外用变量缓存。**

```js
// good
var width = wrap.offsetWidth + 'px';
for (var i = 0, len = elements.length; i < len; i++) {
    var element = elements[i];
    element.style.width = width;
    // ......
}

// bad
for (var i = 0, len = elements.length; i < len; i++) {
    var element = elements[i];
    element.style.width = wrap.offsetWidth + 'px';
    // ......
}
```

**[建议] 对有序集合进行遍历时，缓存 `length`。**

虽然现代浏览器都对数组长度进行了缓存，但对于一些宿主对象和老旧浏览器的数组对象，在每次 `length` 访问时会动态计算元素个数，此时缓存 `length` 能有效提高程序性能。

```js
for (var i = 0, len = elements.length; i < len; i++) {
    var element = elements[i];
    // ......
}
```

**[建议] 对有序集合进行顺序无关的遍历时，使用逆序遍历。**

逆序遍历可以节省变量，代码比较优化。

> 就节省一个变量？

```js
var len = elements.length;
while (len--) {
    var element = elements[len];
    // ......
}
```

# 基本类型

## 类型检测

**[建议] 类型检测优先使用 `typeof`。对象类型检测使用 `instanceof`。`null` 或 `undefined` 的检测使用 `\== null`。**

## 类型转换

**[建议] 转换成 `string` 时，使用 `+ ''`。**

**[建议] 转换成 `boolean` 时，使用 `!!`**

**[建议] 转换成 `number` 时，通常使用 `+`。**

```js
// good
+str;

// bad
Number(str);
```

**[建议] `string` 转换成 `number`，要转换的字符串结尾包含非数字并期望忽略时，使用 `parseInt`。**

**[强制] 使用 `parseInt` 时，必须指定进制。**

**[建议] `number` 去除小数点，使用 `Math.floor` / `Math.round` / `Math.ceil`，不使用 `parseInt`。**

## 字符串

**[强制] 字符串开头和结束使用单引号 `'`**

**解释：**

1. 输入单引号不需要按住 `shift`，方便输入。
2. 实际使用中，字符串经常用来拼接 HTML。为方便 HTML 中包含双引号而不需要转义写法。

```js
var str = '我是一个字符串';
var html = '<div class="cls">拼接HTML可以省去双引号转义</div>';
```

**[建议] 使用 `数组` 或 `+` 拼接字符串。**

**解释：**

1. 使用 `+` 拼接字符串，如果拼接的全部是 StringLiteral，压缩工具可以对其进行自动合并的优化。所以，静态字符串建议使用 `+` 拼接。
2. 在现代浏览器下，使用 `+` 拼接字符串，性能较数组的方式要高。
3. 如需要兼顾老旧浏览器，应尽量使用数组拼接字符串。

```js
// 使用数组拼接字符串
var str = [
    // 推荐换行开始并缩进开始第一个字符串, 对齐代码, 方便阅读.
    '<ul>',
        '<li>第一项</li>',
        '<li>第二项</li>',
    '</ul>'
].join('');

// 使用 `+` 拼接字符串
var str2 = '' // 建议第一个为空字符串, 第二个换行开始并缩进开始, 对齐代码, 方便阅读
    + '<ul>',
    +    '<li>第一项</li>',
    +    '<li>第二项</li>',
    + '</ul>';
```

**[建议] 使用字符串拼接的方式生成 HTML，需要根据语境进行合理的转义。**

**[建议] 复杂的数据到视图字符串的转换过程，选用一种模板引擎。**

使用模板引擎有如下好处：

1. 在开发过程中专注于数据，将视图生成的过程由另外一个层级维护，使程序逻辑结构更清晰。
2. 优秀的模板引擎，通过模板编译技术和高质量的编译产物，能获得比手工拼接字符串更高的性能。
3. 模板引擎能方便的对动态数据进行相应的转义，部分模板引擎默认进行 HTML 转义，安全性更好。

- artTemplate: 体积较小，在所有环境下性能高，语法灵活。
- dot.js: 体积小，在现代浏览器下性能高，语法灵活。
- etpl: 体积较小，在所有环境下性能高，模板复用性高，语法灵活。
- handlebars: 体积大，在所有环境下性能高，扩展性高。
- hogon: 体积小，在现代浏览器下性能高。
- nunjucks: 体积较大，性能一般，模板复用性高。

## 对象

**[强制] 使用对象字面量 `{}` 创建新 `Object`。**

```js
// good
var obj = {};

// bad
var obj = new Object();
```

**[建议] 对象创建时，如果一个对象的所有 `属性` 均可以不添加引号，建议所有 `属性` 不添加引号。**

**[建议] 对象创建时，如果任何一个 `属性` 需要添加引号，则所有 `属性` 建议添加 `'`。**

如果属性不符合 Identifier 和 NumberLiteral 的形式，就需要以 StringLiteral 的形式提供。

```js
// good
var info = {
    'name': 'someone',
    'age': 28,
    'more-info': '...'
};

// bad
var info = {
    name: 'someone',
    age: 28,
    'more-info': '...'
};
```

**[强制] 不允许修改和扩展任何原生对象和宿主对象的原型。**

**[建议] 属性访问时，尽量使用 `.`。**

属性名符合 Identifier 的要求，就可以通过 `.` 来访问，否则就只能通过 `[expr]` 方式访问。

通常在 JavaScript 中声明的对象，属性命名是使用 Camel 命名法，用 `.` 来访问更清晰简洁。部分特殊的属性（比如来自后端的 JSON ），可能采用不寻常的命名方式，可以通过 `[expr]` 方式访问。

```
info.age;
info['more-info'];
```

**[建议] `for in` 遍历对象时, 使用 `hasOwnProperty` 过滤掉原型中的属性。**

# 数组

**[强制] 使用数组字面量 `[]` 创建新数组，除非想要创建的是指定长度的数组。**

**[强制] 遍历数组不使用 `for in`。**

**[建议] 不因为性能的原因自己实现数组排序功能，尽量使用数组的 `sort` 方法。**

自己实现的常规排序算法，在性能上并不优于数组默认的 `sort` 方法。以下两种场景可以自己实现排序：

1. 需要稳定的排序算法，达到严格一致的排序结果。
2. 数据特点鲜明，适合使用桶排。

**[建议] 清空数组使用 `.length = 0`。**

## 合并数组使用 Push 方法

使用 `arr1.push(...arr2)` 可比 `arr1 = arr1.concat(arr2)` 节省时间

> 合并拥有大小为 10 的数组 10000 次，`.concat` 的速度为 0.40 ops/sec（操作每秒），而 `.push` 的速度是 378 ops/sec。也就是说 `push` 比 `concat` 快了整整 945 倍！这种差异可能不是线性的，但在这种小规模数据量上已经很明显了。

`Array.prototype.push.apply(arr1, arr2)` 和 `arr1.push(...arr2)` 之间的性能差异基本可以忽略。

### 原因

显然答案与它们的运行机制有很大的关系：在合并数组的时候，`.concat` 创建了一个新的数组，而 `.push` 只是修改了第一个数组。这些额外的操作（将第一个数组的元素添加到返回的数组里）就是拖慢了 `.concat` 速度的关键。

详见：<https://juejin.cn/post/6844903842056765454#heading-19>

### 副作用

如果是比较小的数组，使用 concat 不会产生副作用

## 预分配数组长度

预分配数组长度为什么更快？因为数组是连续内存存储，如果不预先设定长度，则在实际元素超过预分配的大小的时候，会进行数组的迁移，这也就导致性能下降了。

元祖也是类似的道理

# 函数

## 声明函数

向外暴露的用 函数表达式 const，因为不会提升，所以可以认为在模块内部，没有被其他函数引用，因为很容易出错嘛

不向外暴露的内部函数，用 function 声明，因为会提升，很有可能是被其他函数引用了

## 函数长度

**[建议] 一个函数的长度控制在 `50` 行以内。**

- 将过多的逻辑单元混在一个大函数中，易导致难以维护。一个清晰易懂的函数应该完成单一的逻辑单元。复杂的操作应进一步抽取，通过函数的调用来体现流程。
- 特定算法等不可分割的逻辑允许例外。

## 参数设计

**[建议] 一个函数的参数控制在 `6` 个以内。**

- 除去不定长参数以外，函数具备不同逻辑意义的参数建议控制在 `6` 个以内，过多参数会导致维护难度增大。
- 某些情况下，如使用 AMD Loader 的 `require` 加载多个模块时，其 `callback` 可能会存在较多参数，因此对函数参数的个数不做强制限制。

**[建议] 通过 `options` 参数传递非数据输入型参数。**

有些函数的参数并不是作为算法的输入，而是对算法的某些分支条件判断之用，此类参数建议通过一个 `options` 参数传递。

## 闭包 @@@

**[建议] 在适当的时候将闭包内大对象置为 `null`。**

在 JavaScript 中，无需特别的关键词就可以使用闭包，一个函数可以任意访问在其定义的作用域外的变量。需要注意的是，函数的作用域是静态的，即在定义时决定，与调用的时机和方式没有任何关系。

闭包会阻止一些变量的垃圾回收，对于较老旧的 JavaScript 引擎，可能导致外部所有变量均无法回收。

首先一个较为明确的结论是，以下内容会影响到闭包内变量的回收：

- 嵌套的函数中是否有使用该变量。
- 嵌套的函数中是否有 **直接调用 eval**。
- 是否使用了 with 表达式。

Chakra、V8 和 SpiderMonkey 将受以上因素的影响，表现出不尽相同又较为相似的回收策略，而 JScript.dll 和 Carakan 则完全没有这方面的优化，会完整保留整个 LexicalEnvironment 中的所有变量绑定，造成一定的内存消耗。

由于对闭包内变量有回收优化策略的 Chakra、V8 和 SpiderMonkey 引擎的行为较为相似，因此可以总结如下，当返回一个函数 **fn** 时：

1. 如果 fn 的 `[[Scope]]` 是 ObjectEnvironment（with 表达式生成 ObjectEnvironment，函数和 catch 表达式生成 DeclarativeEnvironment），则：
   1. 如果是 V8 引擎，则退出全过程。
   2. 如果是 SpiderMonkey，则处理该 ObjectEnvironment 的外层 LexicalEnvironment。
2. 获取当前 LexicalEnvironment 下的所有类型为 Function 的对象，对于每一个 Function 对象，分析其 FunctionBody：
   1. 如果 FunctionBody 中含有 **直接调用 eval**，则退出全过程。
   2. 否则得到所有的 Identifier。
   3. 对于每一个 Identifier，设其为 **name**，根据查找变量引用的规则，从 LexicalEnvironment 中找出名称为 **name** 的绑定 binding。
   4. 对 binding 添加 **notSwap** 属性，其值为 `true`。
3. 检查当前 LexicalEnvironment 中的每一个变量绑定，如果该绑定有 notSwap 属性且值为 `true`，则：
   1. 如果是 V8 引擎，删除该绑定。
   2. 如果是 SpiderMonkey，将该绑定的值设为 `undefined`，将删除 **notSwap** 属性。

对于 Chakra 引擎，暂无法得知是按 V8 的模式还是按 SpiderMonkey 的模式进行。

如果有 **非常庞大** 的对象，且预计会在 **老旧的引擎** 中执行，则使用闭包时，注意将闭包不需要的对象置为空引用。

**[建议] 使用 `IIFE` 避免 `Lift 效应`。**

在引用函数外部变量时，函数执行时外部变量的值由运行时决定而非定义时，最典型的场景如下：

```js
var tasks = [];
for (var i = 0; i < 5; i++) {
    tasks[tasks.length] = function () {
        console.log('Current cursor is at ' + i);
    };
}

var len = tasks.length;
while (len--) {
    tasks[len]();
}
```

以上代码对 tasks 中的函数的执行均会输出 `Current cursor is at 5`，往往不符合预期。

此现象称为 **Lift 效应** 。解决的方式是通过额外加上一层闭包函数，将需要的外部变量作为参数传递来解除变量的绑定关系：

```js
var tasks = [];
for (var i = 0; i < 5; i++) {
    // 注意有一层额外的闭包
    tasks[tasks.length] = (function (i) {
        return function () {
            console.log('Current cursor is at ' + i);
        };
    })(i);
}

var len = tasks.length;
while (len--) {
    tasks[len]();
}
```

## 空函数

**[建议] 空函数不使用 `new Function()` 的形式。**

```js
var emptyFunction = function () {};
```

**[建议] 对于性能有高要求的场合，建议存在一个空函数的常量，供多处使用共享。**

```js
var EMPTY_FUNCTION = function () {};

function MyClass() {
}

MyClass.prototype.abstractMethod = EMPTY_FUNCTION;
MyClass.prototype.hooks.before = EMPTY_FUNCTION;
MyClass.prototype.hooks.after = EMPTY_FUNCTION;
```

# 面向对象

**[强制] 类的继承方案，实现时需要修正 `constructor`。**

**[建议] 声明类时，保证 `constructor` 的正确性。**

**[建议] 属性在构造函数中声明，方法在原型中声明。**

原型对象的成员被所有实例共享，能节约内存占用。所以编码时我们应该遵守这样的原则：原型对象包含程序不会修改的成员，如方法函数或配置项。

```js
function TextNode(value, engine) {
    this.value = value;
    this.engine = engine;
}

TextNode.prototype.clone = function () {
    return this;
};
```

**[强制] 自定义事件的 `事件名` 必须全小写。**

在 JavaScript 广泛应用的浏览器环境，绝大多数 DOM 事件名称都是全小写的。为了遵循大多数 JavaScript 开发者的习惯，在设计自定义事件时，事件名也应该全小写。

**[强制] 自定义事件只能有一个 `event` 参数。如果事件需要传递较多信息，应仔细设计事件对象。**

一个事件对象的好处有：

1. 顺序无关，避免事件监听者需要记忆参数顺序。
2. 每个事件信息都可以根据需要提供或者不提供，更自由。
3. 扩展方便，未来添加事件信息时，无需考虑会破坏监听器参数形式而无法向后兼容。

**[建议] 设计自定义事件时，应考虑禁止默认行为。**

常见禁止默认行为的方式有两种：

1. 事件监听函数中 `return false`。
2. 事件对象中包含禁止默认行为的方法，如 `preventDefault`。

## 公有方法和私有方法先后顺序是否有要求

类静态变量、实体变量应定义在类的顶部。类内方法定义顺序依次是：公有方法或保护方法 > 私有方法 > getter/setter 方法。

存疑，我觉得 getter setter 其实就和变量是类似的

# 动态特性：eval/new Func/with/传递引用

**[强制] 避免使用直接 `eval` 函数。**

直接 `eval`，指的是以函数方式调用 `eval` 的调用方法。直接 `eval` 调用执行代码的作用域为本地作用域，应当避免。

如果有特殊情况需要使用直接 `eval`，需在代码中用详细的注释说明为何必须使用直接 `eval`，不能使用其它动态执行代码的方式，同时需要其他资深工程师进行 Code Review。

**[建议] 使用 `new Function` 执行动态代码。**

通过 `new Function` 生成的函数作用域是全局使用域，不会影响当当前的本地作用域。如果有动态代码执行的需求，建议使用 `new Function`。

**[建议] 尽量不要使用 `with`。**

使用 `with` 可能会增加代码的复杂度，不利于阅读和管理；也会对性能有影响。大多数使用 `with` 的场景都能使用其他方式较好的替代。所以，尽量不要使用 `with`。

**[建议] 减少 `delete` 的使用。**

如果没有特别的需求，减少或避免使用 `delete`。`delete` 的使用会破坏部分 JavaScript 引擎的性能优化。

**[建议] 处理 `delete` 可能产生的异常。**

对于有被遍历需求，且值 `null` 被认为具有业务逻辑意义的值的对象，移除某个属性必须使用 `delete` 操作。

在严格模式或 IE 下使用 `delete` 时，不能被删除的属性会抛出异常，因此在不确定属性是否可以删除的情况下，建议添加 `try-catch` 块。

**[建议] 避免修改外部传入的对象。**

JavaScript 因其脚本语言的动态特性，当一个对象未被 seal 或 freeze 时，可以任意添加、删除、修改属性值。

但是随意地对 非自身控制的对象 进行修改，很容易造成代码在不可预知的情况下出现问题。因此，设计良好的组件、函数应该避免对外部传入的对象的修改。

**[建议] 具备强类型的设计。**

解释：

- 如果一个属性被设计为 `boolean` 类型，则不要使用 `1` 或 `0` 作为其值。对于标识性的属性，如对代码体积有严格要求，可以从一开始就设计为 `number` 类型且将 `0` 作为否定值。
- 从 DOM 中取出的值通常为 `string` 类型，如果有对象或函数的接收类型为 `number` 类型，提前作好转换，而不是期望对象、函数可以处理多类型的值。

# 模块

**[强制] `export` 与内容定义放在一起。**

何处声明要导出的东西，就在何处使用 `export` 关键字，不在声明后再统一导出。

```js
// good
export function foo() {
}

export const bar = 3;


// bad
function foo() {
}

const bar = 3;

export {foo};
export {bar};
```

**[强制] 所有 `import` 语句写在模块开始处。**

import 自定义的写在一起 第三方的写在一起 中间给个空行

# 集合

**[建议] 不要使用数组展开进行数组的复制操作。**

使用数组展开语法进行复制，代码可读性较差。推荐使用 `Array.from` 方法进行复制操作。

```js
// good
let otherArr = Array.from(arr);

// bad
let otherArr = [...arr];
```

**[强制] 当键值有可能不是字符串时，必须使用 `Map`；当元素有可能不是字符串时，必须使用 `Set`。**@@@

使用普通 Object，对非字符串类型的 `key`，需要自己实现序列化。并且运行过程中的对象变化难以通知 Object。

**[建议] 当需要遍历功能时，使用 `Map` 和 `Set`。** @@@

`Map` 和 `Set` 是可遍历对象，能够方便地使用 `for...of` 遍历。不要使用使用普通 Object。

```js
// good
let membersAge = new Map([
    ['one', 10],
    ['two', 20],
    ['three', 30]
]);
for (let [key, value] of map) {
}

// bad
let membersAge = {
    one: 10,
    two: 20,
    three: 30
};
for (let key in membersAge) {
    if (membersAge.hasOwnProperty(key)) {
        let value = membersAge[key];
    }
}
```

**[建议] 程序运行过程中有添加或移除元素的操作时，使用 `Map` 和 `Set`。**

使用 `Map` 和 `Set`，程序的可理解性更好；普通 Object 的语义更倾向于表达固定的结构。

```js
// good
let membersAge = new Map();
membersAge.set('one', 10);
membersAge.set('two', 20);
membersAge.set('three', 30);
membersAge.delete('one');

// bad
let membersAge = {};
membersAge.one = 10;
membersAge.two = 20;
membersAge.three = 30;
delete membersAge['one'];
```

# 异步

**[强制] 不得为了编写的方便，将可以并行的 IO 过程串行化。**

并行 IO 消耗时间约等于 IO 时间最大的那个过程，串行的话消耗时间将是所有过程的时间之和。

```js
requestData().then(function (data) {
    renderTags(data.tags);
    renderArticles(data.articles);
});

// good
async function requestData() {
    const [tags, articles] = await Promise.all([
        requestTags(),
        requestArticles()
    ]);
    return {tags, articles};
}

// bad
async function requestData() {
    let tags = await requestTags();
    let articles = await requestArticles();

    return Promise.resolve({tags, articles});
}
```

# 环境

## 运行环境

**[建议] 持续跟进与关注运行环境对语言特性的支持程度。**

<https://kangax.github.io/compat-table/es6/>

ES 标准的制定还在不断进行中，各种环境对语言特性的支持也日新月异。了解项目中用到了哪些 ESNext 的特性，了解项目的运行环境，并持续跟进这些特性在运行环境中的支持程度是很有必要的。这意味着：

1. 如果有任何一个运行环境（比如 chrome）支持了项目里用到的所有特性，你可以在开发时抛弃预编译。
2. 如果所有环境都支持了某一特性（比如 Promise），你可以抛弃相关的 shim，或无需在预编译时进行转换。
3. 如果所有环境都支持了项目里用到的所有特性，你可以完全抛弃预编译。

无论如何，在选择预编译工具时，你都需要清晰的知道你现阶段将在项目里使用哪些语言特性，然后了解预编译工具对语言特性的支持程度，做出选择。

**[强制] 在运行环境中没有 `Promise` 时，将 `Promise` 的实现 `shim` 到 `global` 中。**

当前运行环境下没有 `Promise` 时，可以引入 `shim` 的扩展。如果自己实现，需要实现在 `global` 下，并且与标准 API 保持一致。

这样，未来运行环境支持时，可以随时把 `Promise` 扩展直接扔掉，而应用代码无需任何修改。
