---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-09-03-Sat, 10:08:21 pm","date-modified":"2023-04-29-Sat, 8:15:49 pm","permalink":"/programming/front-end/field/editor/code-mirror/","dgPassFrontmatter":true}
---


# 代码编辑器的比较

https://sq.163yun.com/blog/article/184733100361850880

# 官网

<https://codemirror.net/doc/manual.html>

# 基本使用

引入主体

```html
<script src="lib/codemirror.js"></script>
```

引入样式文件

```html
<link rel="stylesheet" href="lib/codemirror.css">
import 'codemirror/lib/codemirror.css'
```

引入语言支持

```html
<script src="mode/javascript/javascript.js"></script>
import 'codemirror/mode/javascript/javascript'
```

声明 codeMirror 实例

```js
const myCodeMirror = CodeMirror(document.body,{
  value: "function myScript(){return 100;}\n",
  mode:  "javascript"
})
console.log(myCodeMirror)
```

引入智能提示，通过 c+space 使用智能提示

```js
import 'codemirror/addon/hint/show-hint.css';  
import 'codemirror/addon/hint/show-hint.js';  
```

```js
import 'codemirror/addon/hint/javascript-hint.js'; 
```

# Option 可使用的配置：

**value: string | CodeMirror.Doc**

- 编辑器的初始值（文本），可以是字符串或者 CodeMirror 文档对象 (不同于 HTML 文档对象)。

**mode: string | object**

- 通用的或者在 CodeMirror 中使用的与 mode 相关联的 mime，当不设置这个值的时候，会默认使用第一个载入的 mode 定义文件。一般地，会使用关联的 mime 类型来设置这个值；除此之外，也可以使用一个带有 name 属性的对象来作为值（如：{name: “[javascript](http://www.hyjiacan.com/tag/javascript/)”, json: true}）。可以通过访问 CodeMirror.modes 和 CodeMirror.mimeModes 获取定义的 mode 和 MIME。

**theme: string**

- 配置编辑器的主题样式。要使用主题，必须保证名称为 .cm-s-[name] (name 是设置的 theme 的值) 的样式是加载上了的。当然，你也可以一次加载多个主题样式，使用方法和 html 和使用类一样，如： theme: foo bar，那么此时需要 cm-s-foo cm-s-bar 这两个样式都已经被加载上了。
- 需要引入相应的样式表

    ```html
    <link rel="stylesheet" href="lib/codemirror/theme/material.css">
    ```

**lineNumbers: boolean**

- 是否在编辑器左侧显示行号。

**readOnly: boolean|string**

编辑器是否只读。如果设置为预设的值 “nocursor”，那么除了设置只读外，编辑区域还不能获得焦点。

**lineSeparator: string|null**

明确指定编辑器使用的行分割符（换行符）。默认（值为 null）情况下，文档会被 CRLF(以及单独的 CR, LF) 分割，单独的 LF 会在所有的输出中用作换行符（如：getValue）。当指定了换行字符串，行就只会被指定的串分割。

**indentUnit: integer**

缩进单位，值为空格数，默认为 2 。

**smartIndent: boolean**

自动缩进，设置是否根据上下文自动缩进（和上一行相同的缩进量）。默认为 true。

**tabSize: integer**

tab 字符的宽度，默认为 4 。

**indentWithTabs: boolean**

在缩进时，是否需要把 n*tab 宽度个空格替换成 n 个 tab 字符，默认为 false 。

**electricChars: boolean**

在输入可能改变当前的缩进时，是否重新缩进，默认为 true （仅在 mode 支持缩进时有效）。

**specialChars: RegExp**

需要被占位符 (placeholder) 替换的特殊字符的正则表达式。最常用的是非打印字符。默认为：/[\u0000-\u0019\u00ad\u200b-\u200f\u2028\u2029\ufeff]/。

**specialCharPlaceholder: function(char) → Element**

这是一个接收由 specialChars 选项指定的字符作为参数的函数，此函数会产生一个用来显示指定字符的 DOM 节点。默认情况下，显示一个红点（•），这个红点有一个带有前面特殊字符编码的提示框。

**rtlMoveVisually: boolean**

Determines whether horizontal cursor movement through right-to-left (Arabic, Hebrew) text is visual (pressing the left arrow moves the cursor left) or logical (pressing the left arrow moves to the next lower index in the string, which is visually right in right-to-left text). The default is false on Windows, and true on other platforms.（这段完全不晓得搞啥子鬼）

**keyMap: string**

配置快捷键。默认值为 default，即 codemorrir.js 内部定义。其它在 [key map](http://codemirror.net/keymap/) 目录下。

**extraKeys: object**

给编辑器绑定与前面 keyMap 配置不同的快捷键。

**lineWrapping: boolean**

在长行时文字是换行 (wrap) 还是滚动 (scroll)，默认为滚动 (scroll)。

**lineNumbers: boolean**

是否在编辑器左侧显示行号。

**firstLineNumber: integer**

行号从哪个数开始计数，默认为 1 。

**lineNumberFormatter: function(line: integer) → string**

使用一个函数设置行号。

**gutters: array\<string>**

用来添加额外的 gutter（在行号 gutter 前或代替行号 gutter）。值应该是 CSS 名称数组，每一项定义了用于绘制 gutter 背景的宽度（还有可选的背景）。为了能明确设置行号 gutter 的位置（默认在所有其它 gutter 的右边），也可以包含 CodeMirror-linenumbers 类。类名是用于传给 setGutterMarker 的键名 (keys)。

**fixedGutter: boolean**

设置 gutter 跟随编辑器内容水平滚动（false）还是固定在左侧（true 或默认）。

**scrollbarStyle: string**

设置滚动条。默认为”native”，显示原生的滚动条。核心库还提供了”null”样式，此样式会完全隐藏滚动条。Addons 可以设置更多的滚动条模式。

**coverGutterNextToScrollbar: boolean**

当 fixedGutter 启用，并且存在水平滚动条时，在滚动条最左侧默认会显示 gutter，当此项设置为 true 时，gutter 会被带有 CodeMirror-gutter-filler 类的元素遮挡。

**inputStyle: string**

选择 CodeMirror 处理输入和焦点的方式。核心库定义了 textarea 和 contenteditable 输入模式。在移动浏览器上，默认是 contenteditable，在桌面浏览器上，默认是 textarea。在 contenteditable 模式下对 IME 和屏幕阅读器支持更好。

**showCursorWhenSelecting: boolean**

在选择时是否显示光标，默认为 false。

**lineWiseCopyCut: boolean**

启用时，如果在复制或剪切时没有选择文本，那么就会自动操作光标所在的整行。

**undoDepth: integer**

最大撤消次数，默认为 200（包括选中内容改变事件） 。

**historyEventDelay: integer**

在输入或删除时引发历史事件前的毫秒数。

**tabindex: integer**

编辑器的 tabindex。

**autofocus: boolean**

是否在初始化时自动获取焦点。默认情况是关闭的。但是，在使用 textarea 并且没有明确指定值的时候会被自动设置为 true。

# 低级选项

下面的选项仅用于一些特殊情况。

**dragDrop: boolean**

是否允许拖放，默认为 true。

**allowDropFileTypes: array\<string>**

默认为 null。当设置此项时，只接收包含在此数组内的文件类型拖入编辑器。文件类型为 MIME 名称。

**cursorBlinkRate: number**

光标闪动的间隔，单位为毫秒。默认为 530。当设置为 0 时，会禁用光标闪动。负数会隐藏光标。

**cursorScrollMargin: number**

当光标靠近可视区域边界时，光标距离上方和下方的距离。默认为 0 。

**cursorHeight: number**

光标高度。默认为 1，也就是撑满行高。对一些字体，设置 0.85 看起来会更好。

**resetSelectionOnContextMenu: boolean**

设置在选择文本外点击打开上下文菜单时，是否将光标移动到点击处。默认为 true。

**workTime, workDelay: number**

通过一个假的后台线程高亮 workTime 时长，然后使用 timeout 休息 workDelay 时长。默认为 200 和 300 。（完全不懂这个功能是在说啥）

**pollInterval: number**

指明 CodeMirror 向对应的 textarea 滚动（写数据）的速度（获得焦点时）。大多数的输入都是通过事件捕获，但是有的输入法（如 IME）在某些浏览器上并不会生成事件，所以使用数据滚动。默认为 100 毫秒。

**flattenSpans: boolean**

默认情况下，CodeMirror 会将使用相同 class 的两个 span 合并成一个。通过设置此项为 false 禁用此功能。

**addModeClass: boolean**

当启用时（默认禁用），会给每个标记添加额外的表示生成标记的 mode 的以 cm-m 开头的 CSS 样式类。例如，XML mode 产生的标记，会添加 cm-m-xml 类。

**maxHighlightLength: number**

当需要高亮很长的行时，为了保持响应性能，当到达某些位置时，编辑器会直接将其他行设置为纯文本 (plain text)。默认为 10000，可以设置为 Infinity 来关闭此功能。

**viewportMargin: integer**

指定当前滚动到视图中内容上方和下方要渲染的行数。这会影响到滚动时要更新的行数。通常情况下应该使用默认值 10。可以设置值为 Infinity 始终渲染整个文档。注意：这样设置在处理大文档时会影响性能。

# 实例声明和 DOM

在声明 CM 实例的同时，可以把编辑器添加到 DOM 结构中

## CodeMirror(element,options)

通过这种方法可以把编辑器附加到 DOM 结构中，但是会产生多余的 div 节点

```js
const myCodeMirror = CodeMirror(document.body,{
  value: "function myScript(){return 100;}\n",
  mode:  "javascript"
})
console.log(myCodeMirror)
```

![image-20200216120521958](/img/user/programming/front-end/field/editor/code-mirror/image-20200216120521958.png)

## CodeMirror(fun(cmDom){},options)

通过 DOM API 替换原有的 DOM 节点

```js
var myCodeMirror = CodeMirror(function(cmcmDom {  myTextArea.parentNode.replaceChild(cmDom, myTextArea);}, {value: myTextArea.value});
```

这样原本的 textarea 就会被替换成 cmDom

## CodeMirror.fromTextArea(textarea,options)

因为上面的形式经常使用，所以衍生出了这样一个简化的 API

```js
var myCodeMirror = CodeMirror.fromTextArea(myTextArea);
```

这将确保在提交表单（如果它是表单的一部分）时，使用编辑者的内容更新 textarea 的值。有关此方法的完整说明，请参见 [API参考](https://codemirror.net/doc/manual.html#fromTextArea)。

![image-20200216125532955](/img/user/programming/front-end/field/editor/code-mirror/image-20200216125532955.png)

此时 value 属性无效

# 样式相关 API

## cm.setSize(width,height)

接受一个 px 字符串，用于设置 editor 的大小

## cm.setOption

动态指定 options

## 使用 CSSAPI

cm 实例生成之后，会生成一个 div，`class` 为 `CodeMirror cm-s-material CodeMirror-wrap`

通过给 `CodeMirror-wrap` 添加样式来控制即可

编辑器本身的 z-index 为 3

# 文本操作的相关 API

## 概述

在官方文档中表现为 `doc.getValue()` 的形式，虽然如此，但是感觉和 `editor.getValue()` 没有区别，仅仅是语义上的表达

## doc.getValue(?separator: string)

仅仅单纯获取编辑器的文本内容，不能识别换行及一些特殊符号的转义

获取当前编辑器的文本内容。你也可以传递一个参数，用于分割文本行，默认是 `"\n"`

因此默认获取的是一个包含换行排版的字符串

## setValue()

## replaceRange()

**参数**

+ replacement: string,
+ from: {line, ch},
+ to: {line, ch},
+ ?origin: string

**描述**

+ Replace the part of the document between `from` and `to` with the given string.
+ `from` and `to` must be `{line, ch}` objects.
+ `to` can be left off to simply insert the string at position `from`.
+ When `origin` is given, it will be passed on to [`"change"` events](https://codemirror.net/doc/manual.html#event_change), and its first letter will be used to determine whether this change can be merged with previous history events, in the way described for [selection origins](https://codemirror.net/doc/manual.html#selection_origin).

# 事件

## instance.on(eventName,handler)

通过 `cmIstance.on(eventName,handler)` 进行绑定

## Change 事件

**handler 参数**

- instance:CodeMirror
- changeObj：包含了发生变化时的相关信息
    - from，一个 position 对象，包含 line，ch，sticky，xRel 属性
    - to，同上
    - text，一个数组，元素是增加的文本，按行分割
    - removed，一个数组，元素是删除或者被覆写的文本，按行分割
    - origin：
        - '+input'：输入
        - 'cut'：剪切
        - 'paste'：黏贴

![1570519733792](/img/user/programming/front-end/field/editor/code-mirror/1570519733792.png)

**描述**

- 该事件在单个操作结束之后，DOM 更新之前派发

## Changes 事件

**handler 参数**

- Instance
- changes：array\<changeObj>
- 与 change 事件类似，但是该事件会批量处理一批操作，传递一个数组包含该操作的所有改变

**描述**

- This event is fired after the operation finished, and display changes it makes will trigger a new operation.
- 回车键会触发两次 change 事件，在 changes 的监听中被合并到一个数组中，其他操作和 change 事件表现类似

## beforeChange

**handler 参数**

- instance
- changeObj

## cursorActivity

**handler 参数**

- instance: CodeMirror
- Will be fired when the cursor or selection moves, or any change is made to the editor content.

## keyHandled

- (instance: CodeMirror, name: string, event: Event)
- Fired after a key is handled through a key map. `name` is the name of the handled key (for example `"Ctrl-X"` or `"'q'"`), and `event` is the DOM `keydown` or `keypress` event.

# 插件

## 代码折叠

相关样式

```html
<link rel="stylesheet" href="codemirror-5.12/addon/fold/foldgutter.css"/>
```

相关脚本

```html
<script src="codemirror-5.12/addon/fold/foldcode.js"></script><script src="codemirror-5.12/addon/fold/foldgutter.js"></script><script src="codemirror-5.12/addon/fold/brace-fold.js"></script><script src="codemirror-5.12/addon/fold/comment-fold.js"></script>
```

相关配置

```js
lineWrapping:true,foldGutter: true,gutters:["CodeMirror-linenumbers", "CodeMirror-foldgutter"],
```

## 绑定 Vim

 ```html
    <script src="codemirror-5.12/addon/dialog/dialog.js"></script>
    ```

相关设置


```js
//绑定VimkeyMap:"vim"
```

## 括号匹配

```js
import 'codemirror/addon/edit/matchbrackets'// optionsmatchBrackets:true
```

## 自动补全括号

```js
// 自动补全括号import 'codemirror/addon/edit/closebrackets'autoCloseBrackets:true,
```

## 当前行高亮

```js
// 当前行背景高亮import 'codemirror/addon/selection/active-line'// optionsstyleActiveLine:true,
```

# 源码

## 高亮逻辑

https://www.cnblogs.com/zgqys1980/archive/2011/10/25/2223492.html

# 扩展操作

## 代码同步

### 示例代码

```js
editor.on('change',function(instance,changeObj){  const {from,to,text} = changeObj  receiver.replaceRange(text,from,to)})
```

### Input 类型

使用 `doc.replaceRange()`

`text[0]` 是单个字符串，`from` 和 `to` 的值相等，操作的结果是用 `text` 替换 `from(to)` 处的空串

```jsx
editor.on('change',function(instance,changeObj){  const {from,to,text} = changeObj  receiver.replaceRange(text[0],from,to)})
```

![1570681300840](/img/user/programming/front-end/field/editor/code-mirror/1570681300840.png)

### Delete 类型

text[0] 是空串，from 和 to 的值不相等，sticky 的意义？

```js
editor.on('change',function(instance,changeObj){  const {from,to,text} = changeObj  receiver.replaceRange(text[0],from,to)})
```

![1570681288647](/img/user/programming/front-end/field/editor/code-mirror/1570681288647.png)

### 复制、黏贴、剪切、框选删除

![1570681419391](/img/user/programming/front-end/field/editor/code-mirror/1570681419391.png)

 ![1570681432866](/img/user/programming/front-end/field/editor/code-mirror/1570681432866.png)

### 回车键

直接传入 `text` 参数，一次回车键会触发两次 `change` 事件，具体原理是怎样的呢？

当 `text` 的值为 `['','']` 时，就会触发换行，第二次 `change` 事件是用作 `indent`

```js
editor.on('change',function(instance,changeObj){  const {from,to,text} = changeObj  console.log(changeObj)  receiver.replaceRange(text,from,to)})
```

![1570681757793](/img/user/programming/front-end/field/editor/code-mirror/1570681757793.png)

## 代码运行

参考：ES- 函数 - 实践

## 代码编译

https://juejin.im/post/5bd7fcc6e51d45174218bf86
