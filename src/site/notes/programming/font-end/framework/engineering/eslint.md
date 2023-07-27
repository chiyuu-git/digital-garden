---
{"dg-publish":true,"permalink":"/programming/font-end/framework/engineering/eslint/"}
---


# Global Experience Collection

希望可以通过 dataview 查询所有的经验 section 自动汇总到这里

+ 首先想到的方案就是给 section 打上标签，然后 query 标签即可，再动态拼接 section 即可
+ 但是 ob 的 tag 是 page 维度的, 需要再研究其他方法

| File                                                              | overview                   |
| ----------------------------------------------------------------- | -------------------------- |
| [[programming/font-end/primitive/es/es-function\|es-function]] | [[programming/font-end/primitive/es/es-function#experience\|es-function#experience]] |
| [[programming/font-end/primitive/es/es-array\|es-array]]       | [[programming/font-end/primitive/es/es-array#experience\|es-array#experience]]    |
| [[programming/font-end/primitive/es/es-number\|es-number]]     | [[programming/font-end/primitive/es/es-number#experience\|es-number#experience]]   |
| [[programming/font-end/primitive/es/es-regexp\|es-regexp]]     | [[programming/font-end/primitive/es/es-regexp#experience\|es-regexp#experience]]   |

{ .block-language-dataview}

# 编程经验

为什么编程实践要放在 esilnt 下？因为这些实践不应该死记硬背，应该直接通过 linter 固化

抽取重复的是为了后期的维护，应该先使得功能实现了，再抽取相同的部分封装，直接就写封装然后分别运用，境界很高的

> 就像做数学，一开始每次都把公式抄一遍，要做的很熟练，才可以跳过公式

但是上线的代码，还是要做好设计和抽离，不然维护起来成本太大了

了解一个概念的方法：是什么，有哪些作用，实际中怎么使用，为什么会这么设计

## 哲学

### 加不加分号

在下面 2 种情况下不加分号会有问题

1. 小括号开头的前一条语句

    ```js
    var a = 3
    ;(function () {
    
    })()
    //解释器理解(错误理解)：
    var a = 3(function () {
    })();
    //把3看成了一个函数，去调用后面的function
    //TypeError:3 is not a function
    ```

2. 中方括号开头的前一条语句

    ```js
    var b = 4
    ;[1, 3].forEach(function () {
    })
    //解释器理解(错误理解)：
    var b = 4[3].forEach(function () {
    })
    //把4[3]看成了一个变量，去调用后面的forEach方法
    //TypeError:4[3] is undefined
    ```

解决办法: 在行首加分号

强有力的例子: vue.js 库

知乎热议: https://www.zhihu.com/question/20298345

### 空格 Vs Tab

使用空格还是 tab 的这个问题，如同程序员之间的『语言之争』，『vim/emacs 编辑器之争』一样是个永远的圣战，这个争论不会有结果，你怎么选择都有自己的道理，只是看你选择认同谁而已。

就我而言，我提倡尽可能用空格（除了少数必须用 tab 的情形以外）。理由如下：

1. 空格在各种情况下代码都是你想要的样子。而 tab 仅仅当你与代码作者的 tab 尺寸设定为相同时，代码才好看。 修改 tab 尺寸并不能解决这个问题，因为你很难做到每打开一个文件就修改一次 tab 尺寸，而每个人通常有不同的习惯（POSIX/Unix 标准的 tab 应当为 8 字符宽度，Linus 大神也规定 Linux 内核中所有代码的 tab 尺寸为 8）。如果存在行尾注释，则 tab 尺寸更加是必须设定为与作者相同，这就意味着你看不同的代码需要经常修改 tab 尺寸。我看过许多代码，其使用的 tab 尺寸有从 2,3,4,5,6,8,16 甚至 32 的，如果你使用的 tab 尺寸与作者不同，外观将很不理想。
2. 靠谱的编辑器都能解决前进后退增加减少缩进的问题，即便是四个空格，一个退格键也能全退了，所以在使用的方便性方面根本不存在问题。——如果抱怨删除调整还不能有效解决的，你需要研究一下你的编辑器了。实际上增加减少缩进在主流编辑器中都直接有快捷键，无论是 tab 还是空格还是退格都很少直接被用于缩进。
3. tab 是制表符而不是缩进符，正如在 html 页面中大量使用 `<table>` 进行布局是个不好的编程习惯一样，在编程中大量使用制表符布局通常也不是个好习惯。
4. 如果代码需要压缩发布，使用空格的代码通常具有更好的压缩率。各位不信的可以使用批处理工具把代码用全空格或者全 tab 走一遍。——这里面的原理是信息量，使用 tab 缩进的代码中，仍然不可避免的含有空格（运算符之间的间隔，注释等等），但使用空格的代码中根本不含有 tab，这使得 tab 缩进代码虽然不压缩的时候更小，但熵更高，因而压缩率较差，压缩之后反而更大。——当然，压缩发布代码仅仅对开源软件有意义，商业软件可以无视。

## Falsy 值

可用性判断 0 null undefined 的边界值的处理

必要性判断 0 null undefined 等边界值的处理，状态的判断，然后再执行函数

[空值合并运算符（??） - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing)

比 `||` 更好一点

## 回调函数的简写形式

```js
PubSub.subscribe("deleComment",(msg,index)=>{
  this.deleteComment(index)
})

deleteComment(index){}

//可以简写，因为第二个参数接受一个回调函数，这个回调函数有两个参数，第一个是msg，第二个是index，
//如果deleteComment也设计成这样（msg，index），其实就是和上面那种麻烦的形式是一样的
PubSub.subscribe("deleComment",deleteComment)

deleteComment(msg,index){}
```

## 如何避免循环依赖

一个模块依赖另一个模块，这两个模块互相依赖

## If Else 简化

把不同的 if else clause 封装成不同的状态，通过 [状态机](../../../basic/cs-basic/design-pattern.md#状态机) 来解决

### 复杂判断

[JavaScript 复杂判断的更优雅写法 - 知乎](https://zhuanlan.zhihu.com/p/52110677)

## 代码短路逻辑

一种情况是直接 throw error，会停止 js 线程的执行，后续的逻辑没有必要每次都判断是否存在了。但是问题在于代码复用之后经常是无法短路的

第二种情况就是短路返回默认值，让默认值和代码逻辑的返回值保持类型一致即可。但是后续的代码都需要检查默认值，比如空数组、空对象

如果一个返回值不可能为 null 或者 undefined，那么抛出一个 error 会是一个不错的选择。此时 ts 不会认为可能存在边界值如 null undefined，因为此时会短路 throw。这样后续的逻辑就不需要针对 null 做判断了

## 凡是监听，一定要取消监听

逻辑层因为是所有通用的，如果不取消监听的话，很容易内存泄漏

视图层是每个 page 单独，整个 context 都一起销毁了，所以没那么容易出事。

## 命名基准

只有数组使用复数，或者是 xxList，其他所有变量都是单数

如果是对象就用 xxMap

对大小写敏感的使用驼峰，不敏感的使用下划线，什么时候用短横线？无关的词组合形成一个全新的词的时候

- userName，表示 user 的 name
- react-redux ，表示一个组合成新的名词，双击不可以全选
- react_redux，标识一个单词，双击可以全选

expandHeightWithContent 其实就是为了解决高度塌陷的问题，欣姐说不要用这个函数解决的了什么问题来命名，而是应该描述这个函数做了什么，其实很有道理

## 性能技巧

访问变量远远快于访问属性

因为性能问题，减少 gc，这次从 foreach 改为 for 循环

```ts
Object.keys(methodList).forEach 

for keys of Object.keys(methodList)

arr.forEach((val,i) => {})
for i len 
```

相较于 foreach，少了一个匿名函数，foreach 在某些函数可以并行执行，而 for 系列无法做这种优化

### 数组

虽说 js 的数据不需要指定长度，但是数组固定长度性能还是有优化的，如果可以指定还是指定一下吧

### 避免使用 Delete

[为什么说在 JS 中要避免使用 delete - 掘金](https://juejin.cn/post/6844903918804172814)

[[JS]为什么不建议用delete删除对象属性 - 掘金](https://juejin.cn/post/7048070062980202503)

可以赋值为 undefined, 或者重新创建赋值一个有值字段对象

### 循环和递归

时间效率上，看具体语言实现了，有尾递归优化的几乎一样。没有 [尾递归优化](https://www.zhihu.com/search?q=%E5%B0%BE%E9%80%92%E5%BD%92%E4%BC%98%E5%8C%96&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%22128969831%22%7D) 就要看调用和返回的时间成本大小，但也慢不多，主要是有额外的空间成本。

## 约定优于配置

约定优于配置（convention over configuration），也称作按约定编程，是一种软件设计范式，旨在减少软件开发人员需做决定的数量，获得简单的好处，而又不失灵活性。

> 约定优于配置也被称为习惯优于配置、约定大于配置

本质来说，系统、类库或框架应该假定合理的默认值，而非要求提供不必要的配置。比如说模型中有一个名为 User 的类，那么数据库中对应的表就会默认命名为 user。只有在偏离这一个约定的时候，例如想要将该表命名为 system_user，才需要写有关这个名字的配置。

简单来说，如果你所用工具的约定和你的期待相符，就可以省去配置；不符合的话，你就要通过相关的配置来达到你所期待的结果。

约定优于配置意味着通用化，标准化，意味着开发者都需要遵循同一套约定。这样，当一个开发者要看另一个开发者写的程序的时候，就会很容易上手，因为他了解同一套约定，减少了重新学习的成本。

# JavaScript 编码规范

## 结构

### 缩进

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

### Return

return 字符数较多，换行很别扭，可以参考 react，用一个括号包起来，然后换行

## 命名

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

## 文档化注释

### AMD 模块注释 @@@

**[强制] AMD 模块使用 `@module` 或 `@exports` 标识。**

@exports 与 @module 都可以用来标识模块，区别在于 @module 可以省略模块名称。而只使用 @exports 时在 namepaths 中可以省略 module: 前缀。

```js
define(
    function (require) {

        /**
         * foo description
         *
         * @exports Foo
         */
        var foo = {
            // TODO
        };

        /**
         * baz description
         *
         * @return {boolean} return description
         */
        foo.baz = function () {
            // TODO
        };

        return foo;

    }
);
```

也可以在 exports 变量前使用 @module 标识：

```js
define(
    function (require) {

        /**
         * module description.
         *
         * @module foo
         */
        var exports = {};


        /**
         * bar description
         *
         */
        exports.bar = function () {
            // TODO
        };

        return exports;
    }
);
```

如果直接使用 factory 的 exports 参数，还可以：

```js
/**
 * module description.
 *
 * @module
 */
define(
    function (require, exports) {

        /**
         * bar description
         *
         */
        exports.bar = function () {
            // TODO
        };
        return exports;
    }
);
```

**[强制] 对于已使用 `@module` 标识为 AMD 模块 的引用，在 `namepaths` 中必须增加 `module:` 作前缀。**

namepaths 没有 module: 前缀时，生成的文档中将无法正确生成链接。

```js
/**
 * 点击处理
 *
 * @fires module:Select#change
 * @private
 */
Select.prototype.clickHandler = function () {
    /**
     * 值变更时触发
     *
     * @event module:Select#change
     * @param {Object} e e描述
     * @param {string} e.before before描述
     * @param {string} e.after after描述
     */
    this.fire(
        'change',
        {
            before: 'foo',
            after: 'bar'
        }
    );
};
```

**[建议] 对于类定义的模块，可以使用 `@alias` 标识构建函数。**

```js
/**
 * A module representing a jacket.
 * @module jacket
 */
define(
    function () {

        /**
         * @class
         * @alias module:jacket
         */
        var Jacket = function () {
        };

        return Jacket;
    }
);
```

**[建议] 多模块定义时，可以使用 `@exports` 标识各个模块。**

```js
// one module
define('html/utils',
    /**
     * Utility functions to ease working with DOM elements.
     * @exports html/utils
     */
    function () {
        var exports = {
        };

        return exports;
    }
);

// another module
define('tag',
    /** @exports tag */
    function () {
        var exports = {
        };

        return exports;
    }
);
```

**[建议] 对于 exports 为 Object 的模块，可以使用 `@namespace` 标识。**

使用 @namespace 而不是 @module 或 @exports 时，对模块的引用可以省略 module: 前缀。

**[建议] 对于 exports 为类名的模块，使用 `@class` 和 `@exports` 标识。**

```js
// 只使用 @class Bar 时，类方法和属性都必须增加 @name Bar#methodName 来标识，与 @exports 配合可以免除这一麻烦，并且在引用时可以省去 module: 前缀。
// 另外需要注意类名需要使用 var 定义的方式。

/**
 * Bar description
 *
 * @see foo
 * @exports  Bar
 * @class
 */
var Bar = function () {
    // TODO
};

/**
 * baz description
 *
 * @return {(string|Array)} return description
 */
Bar.prototype.baz = function () {
    // TODO
};
```

### 细节注释

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

## 语言特性：变量/条件/循环

### 变量

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

### 条件

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

### 循环

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

## 基本类型

### 类型检测

**[建议] 类型检测优先使用 `typeof`。对象类型检测使用 `instanceof`。`null` 或 `undefined` 的检测使用 `\== null`。**

### 类型转换

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

### 字符串

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

### 对象

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

## 数组

**[强制] 使用数组字面量 `[]` 创建新数组，除非想要创建的是指定长度的数组。**

**[强制] 遍历数组不使用 `for in`。**

**[建议] 不因为性能的原因自己实现数组排序功能，尽量使用数组的 `sort` 方法。**

自己实现的常规排序算法，在性能上并不优于数组默认的 `sort` 方法。以下两种场景可以自己实现排序：

1. 需要稳定的排序算法，达到严格一致的排序结果。
2. 数据特点鲜明，适合使用桶排。

**[建议] 清空数组使用 `.length = 0`。**

### 合并数组使用 Push 方法

使用 `arr1.push(...arr2)` 可比 `arr1 = arr1.concat(arr2)` 节省时间

> 合并拥有大小为 10 的数组 10000 次，`.concat` 的速度为 0.40 ops/sec（操作每秒），而 `.push` 的速度是 378 ops/sec。也就是说 `push` 比 `concat` 快了整整 945 倍！这种差异可能不是线性的，但在这种小规模数据量上已经很明显了。

`Array.prototype.push.apply(arr1, arr2)` 和 `arr1.push(...arr2)` 之间的性能差异基本可以忽略。

#### 原因

- 显然答案与它们的运行机制有很大的关系：在合并数组的时候，`.concat` 创建了一个新的数组，而 `.push` 只是修改了第一个数组。这些额外的操作（将第一个数组的元素添加到返回的数组里）就是拖慢了 `.concat` 速度的关键。
- 详见：<https://juejin.cn/post/6844903842056765454#heading-19>

#### 副作用

- 如果是比较小的数组，使用 concat 不会产生副作用

### 预分配数组长度

预分配数组长度为什么更快？因为数组是连续内存存储，如果不预先设定长度，则在实际元素超过预分配的大小的时候，会进行数组的迁移，这也就导致性能下降了。

元祖也是类似的道理

## 函数

### 声明函数

向外暴露的用 函数表达式 const，因为不会提升，所以可以认为在模块内部，没有被其他函数引用，因为很容易出错嘛

不向外暴露的内部函数，用 function 声明，因为会提升，很有可能是被其他函数引用了

### 函数长度

**[建议] 一个函数的长度控制在 `50` 行以内。**

- 将过多的逻辑单元混在一个大函数中，易导致难以维护。一个清晰易懂的函数应该完成单一的逻辑单元。复杂的操作应进一步抽取，通过函数的调用来体现流程。
- 特定算法等不可分割的逻辑允许例外。

### 参数设计

**[建议] 一个函数的参数控制在 `6` 个以内。**

- 除去不定长参数以外，函数具备不同逻辑意义的参数建议控制在 `6` 个以内，过多参数会导致维护难度增大。
- 某些情况下，如使用 AMD Loader 的 `require` 加载多个模块时，其 `callback` 可能会存在较多参数，因此对函数参数的个数不做强制限制。

**[建议] 通过 `options` 参数传递非数据输入型参数。**

有些函数的参数并不是作为算法的输入，而是对算法的某些分支条件判断之用，此类参数建议通过一个 `options` 参数传递。

### 闭包 @@@

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

### 空函数

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

## 面向对象

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

### 公有方法和私有方法先后顺序是否有要求

类静态变量、实体变量应定义在类的顶部。类内方法定义顺序依次是：公有方法或保护方法 > 私有方法 > getter/setter 方法。

存疑，我觉得 getter setter 其实就和变量是类似的

## 动态特性：eval/new Func/with/传递引用

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

# ESNEXT

## 模块

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

## 集合

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

## 异步

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

## 环境

### 运行环境

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

# HTML

## 命名

**[强制] `class` 必须单词全字母小写，单词间以 `-` 分隔。**

**[强制] `class` 必须代表相应模块或部件的内容或功能，不得以样式信息进行命名。**

```html
<!-- good -->
<div class="sidebar"></div>

<!-- bad -->
<div class="left"></div>
```

**[强制] 元素 `id` 必须保证页面唯一。**

同一个页面中，不同的元素包含相同的 `id`，不符合 `id` 的属性含义。并且使用 `document.getElementById` 时可能导致难以追查的问题。

**[建议] `id` 建议单词全字母小写，单词间以 `-` 分隔。同项目必须保持风格一致。**

**[建议] `id`、`class` 命名，在避免冲突并描述清楚的前提下尽可能短。**

```html
<!-- good -->
<div id="nav"></div>
<!-- bad -->
<div id="navigation"></div>

<!-- good -->
<p class="comment"></p>
<!-- bad -->
<p class="com"></p>

<!-- good -->
<span class="author"></span>
<!-- bad -->
<span class="red"></span>
```

**[强制] 禁止为了 `hook 脚本`，创建无样式信息的 `class`。**

不允许 `class` 只用于让 JavaScript 选择某些元素，`class` 应该具有明确的语义和样式。否则容易导致 CSS class 泛滥。

使用 `id`、属性选择作为 hook 是更好的方式。

**[强制] 同一页面，应避免使用相同的 `name` 与 `id`。**

IE 浏览器会混淆元素的 `id` 和 `name` 属性， `document.getElementById` 可能获得不期望的元素。所以在对元素的 `id` 与 `name` 属性的命名需要非常小心。

一个比较好的实践是，为 `id` 和 `name` 使用不同的命名法。

```html
<input name="foo">
<div id="foo"></div>
<script>
// IE6 将显示 INPUT
alert(document.getElementById('foo').tagName);
</script>
```

**[强制] 标签名必须使用小写字母。**

```js
<!-- good -->
<p>Hello StyleGuide!</p>

<!-- bad -->
<P>Hello StyleGuide!</P>
```

**[强制] 属性名必须使用小写字母。**

```html
<!-- good -->
<table cellspacing="0">...</table>

<!-- bad -->
<table cellSpacing="0">...</table>
```

**[强制] 属性值必须用双引号包围。**

```html
<!-- good -->
<script src="esl.js"></script>

<!-- bad -->
<script src='esl.js'></script>
<script src=esl.js></script>
```

## 标签

**[强制] 标签使用必须符合标签嵌套规则。**

比如 `div` 不得置于 `p` 中，`tbody` 必须置于 `table` 中。

详细的标签嵌套规则参见 [HTML DTD](http://www.cs.tut.fi/~jkorpela/html5.dtd) 中的 `Elements` 定义部分。

> 有几个特殊的块级元素只能包含内嵌元素，不能再包含块级元素 h1、h2、h3、h4、h5、h6、p、dt
>
> 块级元素不能放在标签 p 里面
>
> li 标签可以包含 div 标签，因为 li 和 div 标签都是装载内容的容器
>
> <https://www.zhihu.com/question/34952563/answer/60672228>

**[建议] 布尔类型的属性，建议不添加属性值。**

```html
<input type="text" disabled>
<input type="checkbox" value="1" checked>
```

**[建议] 自定义属性建议以 `xxx-` 为前缀，推荐使用 `data-`。**

使用前缀有助于区分自定义属性和标准定义的属性。

```html
<ol data-ui-type="Select"></ol>
```

## 通用

### DOCTYPE

**[强制] 使用 `HTML5` 的 `doctype` 来启用标准模式，建议使用大写的 `DOCTYPE`。**

```
<!DOCTYPE html>
```

**[建议] 启用 IE Edge 模式。**

```
<meta http-equiv="X-UA-Compatible" content="IE=Edge">
```

[建议] 在 `html` 标签上设置正确的 `lang` 属性。

有助于提高页面的可访问性，如：让语音合成工具确定其所应该采用的发音，令翻译工具确定其翻译语言等。

```
<html lang="zh-CN">
```

### 编码

**[强制] 页面必须使用精简形式，明确指定字符编码。指定字符编码的 `meta` 必须是 `head` 的第一个直接子元素。**

见 [HTML5 Charset能用吗](http://www.qianduan.net/html5-charset-can-it.html) 一文。

```html
<html>
    <head>
        <meta charset="UTF-8">
        ......
    </head>
    <body>
        ......
    </body>
</html>
```

### CSS 和 JavaScript 引入

与优化一致

## Head

**[强制] 保证 `favicon` 可访问。**

在未指定 favicon 时，大多数浏览器会请求 Web Server 根目录下的 `favicon.ico` 。为了保证 favicon 可访问，避免 404，必须遵循以下两种方法之一：

1. 在 Web Server 根目录放置 `favicon.ico` 文件。
2. 使用 `link` 指定 favicon。

```html
<link rel="shortcut icon" href="path/to/favicon.ico">
```

**[建议] 有下载需求的图片采用 `img` 标签实现，无下载需求的图片采用 CSS 背景图实现。**

解释：

1. 产品 logo、用户头像、用户产生的图片等有潜在下载需求的图片，以 `img` 形式实现，能方便用户下载。
2. 无下载需求的图片，比如：icon、背景、代码使用的图片等，尽可能采用 CSS 背景图实现。

## 表单

**[建议] 尽量不要使用按钮类元素的 `name` 属性。**

由于浏览器兼容性问题，使用按钮的 `name` 属性会带来许多难以发现的问题。具体情况可参考 [此文](http://w3help.org/zh-cn/causes/CM2001)。

**[建议] 当使用 JavaScript 进行表单提交时，如果条件允许，应使原生提交功能正常工作。**

当浏览器 JS 运行错误或关闭 JS 时，提交功能将无法工作。如果正确指定了 `form` 元素的 `action` 属性和表单控件的 `name` 属性时，提交仍可继续进行。

```html
<form action="/login" method="post">
    <p><input name="username" type="text" placeholder="用户名"></p>
    <p><input name="password" type="password" placeholder="密码"></p>
</form>
```

**[建议] 在针对移动设备开发的页面时，根据内容类型指定输入框的 `type` 属性。**

根据内容类型指定输入框类型，能获得能友好的输入体验。

```php+HTML
<input type="date">
```

## 多媒体

## 模板中的 HTML

## DOM 数

根据 Google 的说法，最佳 DOM 树是：

- 少于 1500 个节点
- 深度少于 32 级
- 父节点拥有少于 60 个子节点

# CSS

## 命名

css 类名的第一个字符最好不要使用数字。它无法在 Mozilla Firefox 中起作用

less 变量不可以以数字开头，不支持特殊字符

## 空格

自动化处理

## 选择器

**[强制] 当一个 rule 包含多个 selector 时，每个选择器声明必须独占一行。**

```css
/* good */
.post,
.page,
.comment {
    line-height: 1.5;
}

/* bad */
.post, .page, .comment {
    line-height: 1.5;
}
```

**[强制] `>`、`+`、`~` 选择器的两边各保留一个空格。**

**[强制] 如无必要，不得为 `id`、`class` 选择器添加类型选择器进行限定。**

在性能和维护性上，都有一定的影响。

```js
/* good */
#error,
.danger-message {
    font-color: #c00;
}

/* bad */
dialog#error,
p.danger-message {
    font-color: #c00;
}
```

> <https://segmentfault.com/q/1010000003749652>

## 属性

**[建议] 在可以使用缩写的情况下，尽量使用属性缩写。**

```js
/* good */
.post {
    font: 12px/1.5 arial, sans-serif;
}

/* bad */
.post {
    font-family: arial, sans-serif;
    font-size: 12px;
    line-height: 1.5;
}
```

**[建议] 使用 `border` / `margin` / `padding` 等缩写时，应注意隐含值对实际数值的影响，确实需要设置多个方向的值时才使用缩写。**

`border` / `margin` / `padding` 等缩写会同时设置多个属性的值，容易覆盖不需要覆盖的设定。如某些方向需要继承其他声明的值，则应该分开设置。

## 属性顺序

[建议] 同一 rule set 下的属性在书写时，应按功能进行分组，并以 **Formatting Model（布局方式、位置） > Box Model（尺寸） > Typographic（文本相关） > Visual（视觉效果）** 的顺序书写，以提高代码的可读性。

解释：

- Formatting Model 相关属性包括：`position` / `top` / `right` / `bottom` / `left` / `float` / `display` / `overflow` 等
- Box Model 相关属性包括：`border` / `margin` / `padding` / `width` / `height` 等
- Typographic 相关属性包括：`font` / `line-height` / `text-align` / `word-wrap` 等
- Visual 相关属性包括：`background` / `color` / `transition` / `list-style` 等

另外，如果包含 `content` 属性，应放在最前面。

```css
.sidebar {
    /* formatting model: positioning schemes / offsets / z-indexes / display / ...  */
    position: absolute;
    top: 50px;
    left: 0;
    overflow-x: hidden;

    /* box model: sizes / margins / paddings / borders / ...  */
    width: 200px;
    padding: 5px;
    border: 1px solid #ddd;

    /* typographic: font / aligns / text styles / ... */
    font-size: 14px;
    line-height: 20px;

    /* visual: colors / shadows / gradients / ... */
    background: #f5f5f5;
    color: #333;
    -webkit-transition: color 1s;
       -moz-transition: color 1s;
            transition: color 1s;
}
```

## 清除浮动

## Z-index

**[建议] 将 `z-index` 进行分层，对文档流外绝对定位元素的视觉层级关系进行管理。**

同层的多个元素，如多个由用户输入触发的 Dialog，在该层级内使用相同的 `z-index` 或递增 `z-index`。

建议每层包含 100 个 `z-index` 来容纳足够的元素，如果每层元素较多，可以调整这个数值。

**[建议] 在可控环境下，期望显示在最上层的元素，`z-index` 指定为 `999999`。**

可控环境分成两种，一种是自身产品线环境；还有一种是可能会被其他产品线引用，但是不会被外部第三方的产品引用。

不建议取值为 `2147483647`。以便于自身产品线被其他产品线引用时，当遇到层级覆盖冲突的情况，留出向上调整的空间。

**[建议] 在第三方环境下，期望显示在最上层的元素，通过标签内联和 `!important`，将 `z-index` 指定为 `2147483647`。**

第三方环境对于开发者来说完全不可控。在第三方环境下的元素，为了保证元素不被其页面其他样式定义覆盖，需要采用此做法。

## 值与单位

### 数值

**[强制] 当数值为 0 - 1 之间的小数时，省略整数部分的 `0`。省略可省的单位（长度单位如 `px`、`em`，不包括时间、角度等如 `s`、`deg`）。**

```css
/* good */
panel {
    opacity: .8;
}

/* bad */
panel {
    opacity: 0.8;
}
```

### Url

**[强制] `url()` 函数中的路径不加引号。**

```css
body {
    background: url(bg.png);
}
```

**[建议] `url()` 函数中的绝对路径可省去协议名。**

```css
body {
    background: url(//baidu.com/img/bg.png) no-repeat 0 0;
}
```

### 长度

**[强制] 长度为 `0` 时须省略单位。 (也只有长度单位可省)** @@@

```css
/* good */
body {
    padding: 0 5px;
}

/* bad */
body {
    padding: 0px 5px;
}
```

### 颜色

**[强制] RGB 颜色值必须使用十六进制记号形式 `#rrggbb`。不允许使用 `rgb()`。**

带有 alpha 的颜色信息可以使用 `rgba()`。使用 `rgba()` 时每个逗号后必须保留一个空格。

**[强制] 颜色值可以缩写时，必须使用缩写形式。**

**[强制] 颜色值不允许使用命名色值。**

### 2D 位置

**[强制] 必须同时给出水平和垂直方向的位置。**

2D 位置初始值为 `0% 0%`，但在只有一个方向的值时，另一个方向的值会被解析为 center。为避免理解上的困扰，应同时给出两个方向的值。[background-position属性值的定义](http://www.w3.org/TR/CSS21/colors.html#propdef-background-position)

```css
/* good */
body {
    background-position: center top; /* 50% 0% */
}

/* bad */
body {
    background-position: top; /* 50% 0% */
}
```

## 文本编排

### 字体族

# Typescript

## 接口名是否应该以 `I` 开头

接口一般首字母大写。[有的编程语言中会建议接口的名称加上 `I` 前缀](https://msdn.microsoft.com/en-us/library/8bc1fexb(v=vs.71).aspx)。

统一使用 Pascal

## 实践总结

原始数据类型，定义成具体的字面量或许会更合适

## 值与类型同名

很常见的写法，值小写驼峰，类型大写驼峰

## 隐藏的 Any

未赋值的变量会被推断成 any，要注意避免

## 未使用的变量

为什么有些定义了的变量（比如使用 `enum` 定义的变量）未使用，ESLint 却没有报错？

因为无法支持这种变量定义的检查。建议在 `tsconfig.json` 中添加以下配置，使 `tsc` 编译过程能够检查出定义了未使用的变量：

```json
{
    "compilerOptions": {
        "noUnusedLocals": true,
        "noUnusedParameters": true
    }
}
```

启用了 noUnusedParameters 之后，只使用了第二个参数，但是又必须传入第一个参数，这就会报错了

第一个参数以下划线开头即可，参考 <https://github.com/Microsoft/TypeScript/issues/9458>

## --strictNullChecks

顺便说一句，如果将 `--strictNullChecks` 标志添加到 `tsconfig文件`，TypeScript 将应用非空性规则。只有被声明 null 后才能被赋值：

```ts
// 关闭 --strictNullChecks
let s: string = "foo";
s = null; // 正常

// 开启 --strictNullChecks
s = null; // Error: Type 'null' is not assignable to type 'string'. 
```

## 空对象

使用 `Record<string, unknown>` 代替空对象

还是有一点区别的：联合类型的时候空对象会直接消失，但是 `Record<string, unknown>` 不会

## 只有 null，没有 Undefined

# 后端

mvc 模式的命名和意义

1. 是否使用复数取决于上下文即可，不要纠结了
2. 我突然决定一律使用单数，哪个脑残再说复数的？
3. <https://qastack.cn/programming/338156/table-naming-dilemma-singular-vs-plural-names>
4. 复数仅用于变量名，表示是一个数组，其他场合一律不适用复数，因为复数表示数组比 xxxArray 简单多了

数据库的命名

字段的命名风格，驼峰？

字段是否需要带上表明？ 比如 user 下的 id 字段，是直接 userid ？ 还是直接 id？

表名、字段名必须使用小写字母或数字，禁止出现数字开头

表名用单数

字段名单数还是复数？字段名不再重复表名，比如 project 表里的 name 即可。主键需要重复表名，因为主键会出现在其他表中

大小写不敏感

长查询语句，关键字单独一行，查询列表一个一行，通过快捷键对齐

<https://www.cnblogs.com/daihang2366/p/13560596.html>

<https://cloud.tencent.com/developer/article/1054482>

# Restful Api

## URL 命名

### 形式

URL 命名通常有三种：

- 驼峰命名法 (serverAddress)，由于 URL 是大小写敏感的，如果用驼峰命名在输入的时候就要求区分大小写，一个是增加输入难度，另外也容易输错，报 404。
- 蛇形命名法 (server_address)，在输入的时候需要切换 shfit，同时下划线容易被文本编辑器的下划线掩盖，支付宝用的是蛇形命名法
- 脊柱命名法 (server-address)，stackoverflow.com 和 github.com 用的是脊柱命名法（例如<https://help.github.com/articles/why-are-my-commits-linked-to-the-wrong-user/#commits-are-not-linked-to-any-user 和 (https://stackoverflow.com/questions/5262224/how-are-reddit-and-hacker-news-ranking-algorithms-used>）

在 RESTful 接口中，对于 URI 一个普遍接受的规则是：全部小写，用中划线连接

之所以不混用大小写字母，是因为早期的 URI 一般都是表示服务器上的文件路径，而不同服务器对大小写的敏感性是不同的，为了兼容不同服务器所以才规定不能混用大小写字母，然后用中划线或下划线连接多个单词

我们如今开发的 api，URI 一般不再表示服务器上的文件了，而是一种程序使用的路由，而各类开发程序对字符串的判断也都是区分大小写的，所以就算 URI 混用了大小写一般也不会有问题。但是为了与那些仍旧表示服务器文件路径的 URI 保持一致，更多的人仍旧愿意沿用全部使用小写字母的规则。至于连接多个单词使用中划线还是下划线，我只能说，中划线使用得更加广泛

### 语义

Server 提供的 RESTful API 中，URL 中只使用名词来指定资源，原则上不使用动词。为什么呢？因为 REST 对于信息的核心抽象是资源，而表示资源的词语天然的就是名词，所以这不是什么技术问题，而是常识

单数复数 大部分人认为应该统一使用复数

### 复杂条件查询

- 对于 collection 的所有查询 `Action Name`，都需要以 list 开头。
- 查询的条件中，如果条件为一到两个，使用 `By` 和 `And`。eg.: listByUserIdAndName
- 如果查询条件大于 3 个，则使用 `ByOptions`，查询条件作为请求体传入。eg: `listByOptions`

# SQL

<https://choerodon.io/zh/docs/contributor-guide/development/conventions/sql/>

# Eslint

文档：<http://eslint.cn/docs/user-guide/configuring>

## 基本规则

### 使用

ESLint 可以安装在当前项目中或全局环境下，因为代码检查是项目的重要组成部分，所以我们一般会将它安装在当前项目中。可以运行下面的脚本来安装：

```bash
npm install --save-dev eslint
```

ESLint 需要一个配置文件来决定对哪些规则进行检查，配置文件的名称一般是 `.eslintrc.js` 或 `.eslintrc.json`。

`--quiet` 这个选项允许你禁止报告警告。如果开启这个选项，ESLint 只会报告 error 级别错误。

### 规则的错误等级有三种

```js
rules: {
    '@typescript-eslint/consistent-type-definitions': [
        "error",
        "interface"
      ],
}
```

规则的取值一般是一个数组（上例中的 `@typescript-eslint/consistent-type-definitions`），其中第一项是 `off`、`warn` 或 `error` 中的一个，表示关闭、警告和报错。后面的项都是该规则的其他配置。

如果没有其他配置的话，则可以将规则的取值简写为数组中的第一项（上例中的 `no-var`）。

关闭、警告和报错的含义如下：

- 关闭：禁用此规则
- 警告：代码检查时输出错误信息，但是不会影响到 exit code
- 报错：发现错误时，不仅会输出错误信息，而且 exit code 将被设为 1（一般 exit code 不为 0 则表示执行出现错误）
- 0：关闭规则。
- 1：打开规则，并且作为一个警告（信息打印黄色字体）
- 2：打开规则，并且作为一个错误（信息打印红色字体）

### Eslint 注释

文件级注释

```js
/* eslint-disable fecs-camelcase */
```

一个 disable，一个 enable 两个一起可以当作块级注释

行级注释

```js
// eslint-disable-next-line no-unused-vars
```

### 获取单个文件的全部 Eslint 配置

````
npx eslint --print-config xxx.ts > all-eslint-confing.json
````

使用 npx 智能查找 eslint 命令

设置成 json 文件可以快速的查找出重复的 rule？不行，eslint 会输出最终使用的规则，只能看下默认的配置有没有这项规则，没有的话再加上，或者重写这样

## 开启 ESLint 检查

检查分为两种：一种是编辑器智能提示，一种是主动运行 ESLint 命令进行检查

### 检查整个项目的 Ts 文件

我们的项目源文件一般是放在 `src` 目录下，所以需要将 `package.json` 中的 `eslint` 脚本改为对一个目录进行检查。由于 `eslint` 默认不会检查 `.ts` 后缀的文件，所以需要加上参数 `--ext .ts`：

```json
{
    "scripts": {
        "eslint": "eslint src --ext .ts"
    }
}
```

此时执行 `npm run eslint` 即会检查 `src` 目录下的所有 `.ts` 后缀的文件。

# Markdownlint

## Custom Rule

按理说，这些都会提供一个 cli 脚手架的，克隆下来之后直接 run 就可以开始 debugger 基础示例了

我可以先随便新建一个项目，然后依赖 一个第三方的自定义 rule，一个 markdownlint-cli ，这样就可以在这个项目内通过命令行调试代码

必备的环境基础：

1. 测试用的 markdown 文件
2. 一个 markdownlint 的环境，让我可以通过命令行执行 lint 检查

其实只要这两点就够了，那是不是 vscode 的环境也行呢？ 我直接复制一个 js 文件作为 custom rule 每次保存就可以跑起来了。不太行，没有起环境打 log，还是新建一个项目好了

参考 rule：

MD022

MD032/blanks-around-lists: Lists should be surrounded by blank lines

# 依赖分析

依赖分析工具 madge：https://juejin.cn/post/6844904047024177159

https://www.jianshu.com/p/38694618b2e6

```
madge -i app.svg app.js
```

看 app.js 文件的依赖关系

依赖分析工具 dependency-cruiser https://juejin.cn/post/7096650413209813029

```
npx depcruise --output-type dot --max-depth 1 ./logic | dot -T svg > app.svg
```

```
npx depcruise --output-type dot --max-depth 1 logger | dot -T svg > app.sv
```

看 src 目录下的依赖关系

需要设置 ts config、webpack config 这样才能识别路径别名

[代码分析工具](../../../basic/common/代码分析工具.md)

# 如何阅读源码

[如何阅读源码 —— 以 Vetur 为例 - 知乎](https://zhuanlan.zhihu.com/p/395405684)
