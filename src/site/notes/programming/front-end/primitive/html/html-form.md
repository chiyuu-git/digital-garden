---
{"aliases":[],"tags":[],"review-dates":["2023-04-26"],"dg-publish":true,"date-created":"2022-09-03-Sat, 10:07:26 pm","date-modified":"2023-08-04-Fri, 5:24:47 pm","permalink":"/programming/front-end/primitive/html/html-form/","dgPassFrontmatter":true}
---


# 表单

[html 表单指南](https://developer.mozilla.org/zh-CN/docs/Learn/HTML/Forms)

表单的作用就是用来将用户信息提交给服务器的, 比如：百度的搜索框 注册 登录这些操作都需要填写表单

## 基础概念

表单域指的是包含表单元素的区域，在 h5 中由 form 标签来定义

统一使用 **表单控件** 来表达输入框（单行或者多行）、选择框、按钮、复选框或单选按钮等等

统一使用 **表单组件** 来表达小程序表单组件

表单是用户和小程序之间交互的主要内容之一，主要作用是搜集用户提交的数据，并发送到服务器。

表单是由一个或多个表单域组成，表单域由 form 标签定义。

表单域包含多个表单控件，比如输入框（单行或者多行）、选择框、按钮、复选框或单选按钮等等。

为了方便开发者使用表单，这些表单控件在小程序中被封装成了一个个表单组件。

## 通用属性

大部分用来定义表单小部件的元素都有一些他们自己的属性。然而，在所有表单元素中都有一组通用属性，它们可以对这些小部件进行控制。下面是这些通用属性的列表:

| 属性名称    | 默认值    | 描述                                                                                                                                                                          |
| :---------- | :-------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `autofocus` | (_false_) | 这个布尔属性允许您指定当页面加载时元素应该自动具有输入焦点，除非用户覆盖它，例如通过键入不同的控件。文档中只有一个与表单相关的元素可以指定这个属性。                          |
| `disabled`  | (_false_) | 这个布尔属性表示用户不能与元素交互。如果没有指定这个属性，元素将从包含它的元素继承设置，例如 fliedset; 如果没有包含在设定了 `disabled` 属性的元素里，那么这个元素就是可用的。 |
| `form`      |           | 小部件与之相关联的表单元素。属性值必需是同个文档中的 form 元素的 `id` 属性。理论上，它允许您在 form 元素之外设置一个表单小部件。然而，在实践中，没有任何支持该特性的浏览器。  |
| `name`      |           | 元素的名称; 这是跟表单数据一起提交的。                                                                                                                                        |
| `value`     |           | 元素的初始值。                                                                                                                                                                |

## Form 标签

使用 form 标签创建一个表单

form 标签中必须指定一个 **action 属性**，该属性指向的是一个服务器的地址，当我们提交表单时将会提交到 action 属性对应的地址

指定 `method` 属性，属性定义了发送数据的 HTTP 方法 (它可以是“get”或“post”).

如果希望表单项中的数据会提交到服务器中，还必须给表单项指定一个 **name 属性**，name 表示提交内容的名字 用户填写的信息会 **附在 url 地址的后边** 以查询字符串的形式发送给服务器

![1548727021828](/img/user/programming/front-end/primitive/html/html-form/1548727021828.png)

格式：url 地址?查询字符串

属性名=属性值&属性名=属性值&属性名=属性值&属性名=属性值

使用 form 创建的仅仅是一个空白的表单，我们还需要向 form 中添加不同的 **表单项**

# Input 标签

display:inline-block

## 文本框

使用 input 来创建一个文本框，它的 type 属性是 text

在文本框中也可以指定 **value 属性值**，该值将会作为文本框的 **默认值** 显示

通过伪类设置 placeholder 的样式

```css
::-webkit-input-placeholder {
  /* WebKit, Blink, Edge */
  color: #909;
}
:-moz-placeholder {
  /* Mozilla Firefox 4 to 18 */
  color: #909;
}
::-moz-placeholder {
  /* Mozilla Firefox 19+ */
  color: #909;
}
:-ms-input-placeholder {
  /* Internet Explorer 10-11 */
  color: #909;
}
```

outline 输入框 获取到焦点时会高亮, 设置属性值为 none

内阴影, 设置 border 为 none 可以去除

![1549447847294](/img/user/programming/front-end/primitive/html/html-form/1549447847294.png)

```html
<input type="text" autocomplete="off" />
```

## 密码框

```html
<input type="password" />
```

输入的内容以 \* 显示

# 单选按钮

使用 input 来创建一个单选按钮，它的 type 属性使用 radio；收音机只有一个能摁下去

单选按钮通过 **name 属性** 进行 **分组**，name 属性相同是一组按钮

像这种需要用户选择但是不需要用户直接填写内容的表单项，还 **必须指** 定一个 **value 属性**，这样被选中的表单项的 **value 属 - 性值** 将会最终提交给服务器

如果希望在单选按钮或者是多选框中指定默认选中的选项， 则可以在希望选中的项中添加 **checked="checked" 属性**

# 多选框

使用 input 创建一个多选框，它的 type 属性使用 checkbox

# 普通按钮

使用 input type=button 可以用来创建一个单纯的按钮，这个按钮没有任何功能，只能被点击

除了使用 input，也可以使用 button 标签来创建按钮这种方式和使用 input 类似，只不过由于它是成对出现的标签，使用起来更加的灵活

```html
<button type="submit">提交</button>
<button type="reset">重置</button>
<button type="button">按钮</button>
```

button 有默认的边框

文本框和按钮的基线对的不是很齐

一个 float left

一个 float right

改变盒模型即可解决

> 一般不使用 button 元素，因为默认样式的表现比较奇怪，需要进行许多初始化操作
>
> 非表单按钮使用 a 标签

# 提交按钮

提交按钮可以将表单中的信息提交给服务器，使用 input 创建一个提交，它的 type 属性值是 submit

在提交按钮框中也可以指定 **value 属性值**，该值将会作为按钮的文字显示

`<input type="submit" />` 这样的按钮用户点击之后会自动提交 form，除非你写了 javascript 阻止它。

# 重置按钮

input type="reset" 可以创建一个重置按钮，

点击重置按钮以后表单中内容将会恢复为默认值

# Type='week'

https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/input/week

# Select 标签

## 下拉列表

使用 **select** 来创建一个下拉列表， 下拉列表的 **name** 属性需要指定给 select**，**而**value 属性需要指定给 option**

## 列表项

在下拉列表中使用 option 标签来创建一个一个列表项

可以通过在 option 中添加**selected="selected"**来将选项设置为默认选中

## 多选列表

当为 select 添加一个 multiple="multiple"，则下拉列表变为一个多选的下拉列表

在 select 中可以使用 optgroup 对选项进行分组，同一个 optgroup 中的选项是一组

## Label 属性

可以通过 **label 属性** 来指定分组的名字

```html
<select name="" id="" multiple size="10">
  <optgroup label="女明星">
    <!-- 在下拉列表中使用option标签来创建一个一个列表项 -->
    <option value="fbb">范冰冰</option>
    <option value="lxr">林心如</option>
    <option value="zw">赵薇</option>
  </optgroup>

  <optgroup label="男明星">
    <option value="zbs" selected="selected">赵本山</option>
    <option value="ldh">刘德华</option>
    <option value="pcj">潘长江</option>
  </optgroup>
</select>
```

## Size 属性

默认值为 1，多选时默认值为 5

![1556867910098|200](/img/user/programming/front-end/primitive/html/html-form/1556867910098.png)

# Textarea 标签

## 文本域

使用 textarea 标签创建一个文本域

resize:none;

# 辅助标签

## Label 标签

该标签可以指定一个 **for 属性**，该属性的值需要指定一个 **表单项的 id 值**

设置了 for 属性，你可以单击关联的标签来聚焦或者激活 input，以及 input 本身。这种增加的命中区域为激活 input 提供了方便，包括那些使用触摸屏设备的。

```html
<label for="pwd">密码 </label> <input type="password" id="pwd" name="pwd" />
```

另外，你也可以将 `<input>` 直接放在 `<label>` 里，这种情况就不需要 `for` 和 `id` 属性了，因为这时关联是隐含的：

```html
<div class="preference">
  <label>Do you like peas?<input type="checkbox" name="peas" /></label>
</div>
```

### 注意

标签标记的表单控件称为标签元素的 **标签控件**。一个 input 可以与多个标签相关联。

标签本身并不与表单直接相关。它们只通过与它们相关联的控件间接地与表单相关联。

当点击或者触碰（tap）一个与表单控件相关联的 `<label>` 时，关联的控件的 `click` 事件也会触发。

### Form

表示 label 元素关联的 form 元素（它的表单拥有者）。此属性值必须是同一文档中 form 标签的 ID。如果未指定此属性值，`<label>` 元素必须是 form 标签的后代。应用此属性值，你可以将 label 元素放置在文档的任何位置，而不仅仅是作为它的拥有者 form 元素的后代。

## Fieldset

在表单中可以使用 fieldset 来为表单项进行分组，可以将表单项中的同一组放到一个 fieldset 中

在 fieldset 可以使用 legend 子标签，来指定组名

```html
<form action="test.php" method="post">
  <fieldset>
    <legend>Title</legend>
    <input type="radio" id="radio" /> <label for="radio">Click me</label>
  </fieldset>
</form>
```

![1556868310769](/img/user/programming/front-end/primitive/html/html-form/1556868310769.png)

https://developer.mozilla.org/zh-CN/docs/Learn/HTML/Forms/How_to_structure_an_HTML_form

上文关于 fieldset 的描述，非常实用

# 新增表单控件

## 列表标签:datalist

datalist 会包含一组 **option 元素**，这些元素表示其表单控件的可选值，它的 id 必须要和 **input 中的 list 属性** 一致

```html
<input type="text" placeholder="你最喜欢的女明星是？" list="zdy"></input>
<!--placehoder其实是h5新增的属性-->
<datalist id="zdy">
  <option value="1">10岁的周冬雨</option>
  <option value="2">20岁的周冬雨</option>
  <option value="3">30岁的周冬雨</option>
  <option value="4">40岁的周冬雨</option>
</datalist>
```

![1553166177305|200](/img/user/programming/front-end/primitive/html/html-form/1553166177305.png)

## 列表标签:details

一个 ui 小部件，用户可以从其中检索附加信息。

open**属性** 来控制附加信息的显示与隐藏

summary**标签**: 用作 一个 `<details>` 元素的一个内容摘要（标题）

```html
<details>
  <summary>尚硅谷男生张晓飞</summary>
  <p>一个神一样的男人</p>
  <p>一个神一样的男人</p>
  <p>一个神一样的男人</p>
</details>
```

![1548061076971|200](/img/user/programming/front-end/primitive/html/html-form/1548061076971.png)

## Type:email :email 地址类型

当格式不符合 email 格式时，提交是不会成功的，会出现提示；只有当格式相符时，提交才会通过

在移动端获焦的时候会切换到英文键盘

## Type:tel : 电话类型

在移动端获焦的时候会切换到数字键盘

没有正则验证

## Type:url :url 类型

当格式不符合 url 格式时，提交是不会成功的，会出现提示；只有当格式相符时，提交才会通过

## Type:search : 搜索类型

有清空文本的按钮

## Type:range : 特定范围内的数值选择器

属性:min、max、step

## 其他

- type:number: 只能包含数字的输入框
- type:color : 颜色选择器
- type:datetime: 显示完整日期 (移动端浏览器支持)
- type:datetime-local : 显示完整日期，不含时区
- type:time : 显示时间，不含时区
- type:date : 显示日期
- type:week : 显示周
- type:month : 显示月

# 表单属性

input 元素由于拥有诸多属性而异常强大，其中前文举例说明的 `type` 属性尤其重要。由于所有元素，无论是哪种 `type` ，都基于 [`HTMLInputElement`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLInputElement) 接口，所以理论上说，它们共享一套相同的属性。但实际上大部分属性只作用于特定一组 `type`。此外，一些属性作用于 input 的方式取决于 input 的 type 属性，不同的 `type` 有不同的效果。

下面的表格列出了所有属性，每个属性都有简短的描述。表格后的列表更详细地描述了各个属性及它们与哪些 ` type` 相关。

- 与大部分或者全部 `type` 都相关的属性会讲述更多细节。
- 一些针对特定 `type` 的属性，或者所有 `type` 都有，但在特定的 ` type` 上有特定表现的属性，会在相应的 `type` 页面中说明。
- 这个元素包含全局属性，一些针对 input 元素有额外意义的全局属性也会特别说明。

# 新增表单属性

**placeholder** : 输入框提示信息, 适用于 form,以及 type 为 text,search,url,tel,email,password 类型的 input

**autofocus** : 指定表单获取输入焦点

**required** : 此项必填，不能为空

pattern : 正则验证，pattern="\d{1,5}"，不用转义

formaction: 在 submit 里定义提交地址

```html
<form action="http://www.baidu.com">
  <input type="submit" value="提交" />
  <input type="submit" value="提交" formaction="http://atguigu.com" />
</form>
```

# 表单验证反馈

validity 对象，通过下面的 valid 可以查看验证是否通过，如果八种验证都通过返回 true，一种验证失败返回 false

node.addEventListener("invalid",fn1);

表单验证失败时触发事件

```js
window.onload = function () {
  var inputEl = document.querySelector("input");
  inputEl.addEventListener("invalid", function () {
    console.log(this.validity);
  });
};
```

![1548063481942|200](/img/user/programming/front-end/primitive/html/html-form/1548063481942.png)

valueMissing: 输入值为空时返回 true

typeMismatch: 控件值与预期类型不匹配返回 true, 比如: 邮箱格式错误

## 以下 4 个比较鸡肋

patternMismatch: 输入值不满足 pattern 正则返回 true

tooLong: 超过 **maxLength 属性** 最大限制返回 true, 设置了就不可能验证失败

rangeUnderflow: 验证的 range 最小值返回 true

rangeOverflow: 验证的 range 最大值返回 true

stepMismatch: 验证 range 的当前值 是否符合 min、max 及 step 的规则返回 true

## 自定义验证

customError: 不符合自定义验证返回 true

**setCustomValidity()**

```js
window.onload = function () {
  var inputEl = document.querySelector("input");
  var submit = document.querySelectorAll("input")[1];
  submit.onclick = function () {
    var val = inputEl.value;
    if (val == "xfz") {
      submit.setCustomValidity("请不要输入敏感词");
    } else {
      //传入空串才能通过验证
      submit.setCustomValidity("");
    }
  };
  submit.addEventListener("invalid", function () {
    console.log(submit.validity);
  });
};
```

## 关闭验证

formnovalidate 属性

# Form 数据类型

在学习 PHP 文件上传的过程中发现，HTML 表单需要设置 enctype="multipart/form-data" 这个属性，虽然不这么设置的确无法上传，但这是为什么呢？

HTML 表单如何打包数据文件是由 enctype 这个属性决定的。enctype 有以下几种取值：

- application/x-www-form-urlencoded 在发送前编码所有字符（默认）（空格被编码为’+’，特殊字符被编码为 ASCII 十六进制字符）
- multipart/form-data 不对字符编码。在使用包含文件上传控件的表单时，必须使用该值。
- text/plain 空格转换为 “+” 加号，但不对特殊字符编码。

默认 enctype=application/x-www-form-urlencoded，所以表单的内容会按 URL 规则编码，然后根据表单的提交方法：

- method=’get’ 编码后的表单内容附加在请求连接后
- method=’post’ 编码后的表单内容作为 post 请求的正文内容

我们通过抓包软件来分析一下这几种方式产生的请求的差别

## 实验一

`method='get'`

`enctype=application/x-www-form-urlencoded`

结果：

- `"hello world"` 被编码为 `%22hello+world%22`，特殊字符和空格都被编码
- `type='file'` 提交的文件内容并没有被提交，只是把文件名编码到了 URL 中

## 实验二

`method='post'`

`enctype=application/x-www-form-urlencoded`

结果：

- `"hello world"` 被编码为 `%22hello+world%22`，特殊字符和空格都被编码
- `type='file'` 提交的文件内容并没有被提交，只是把文件名编码到了 **正文** 中

## 实验三

`method='get'`

`enctype='multipart/form-data'`

结果：实验一一模一样，说明 get 和 multipart/form-data 配合无效

## 实验四

`method='post'`

`enctype=multipart/form-data`

只有使用 `enctype="multipart/form-data"`,表单才会把文件的内容编码到 HTML 请求中。

<https://blog.csdn.net/mazhibinit/article/details/49667511>

# Image Input

input elements of type **image** are used to create graphical submit buttons, i.e. submit buttons that take the form of an image rather than text.

```html
<input
  type="image"
  id="image"
  alt="Login"
  src="/media/examples/login-button.png"
/>
```

# File Input 标签

https://developer.mozilla.org/zh-CN/docs/Web/API/File

在 HTML 当中，`<input type="file">` 允许用户从他们的存储设备中选择一个或多个文件以提交表单的方式上传到服务器上, 或者通过 Javascript 的 [File API](https://developer.mozilla.org/en-US/docs/Web/API/File/Using_files_from_web_applications) 对文件进行操作 。

```html
<input type="file" />
```

由于该标签的 `value` 属性是只读的， 所以它是 React 中的一个 **非受控组件**。我们会把它和其他非受控组件一起在 [后面的章节](https://reactjs.org/docs/uncontrolled-components.html#the-file-input-tag) 进行详细的介绍。

## 访问被选择的文件

```html
<input type="file" id="input" />
```

通过 File API，我们可以访问 [`FileList`](https://developer.mozilla.org/zh-CN/docs/Web/API/FileList)，它包含了表示用户所选文件的 [`File`](https://developer.mozilla.org/zh-CN/docs/Web/API/File) 对象

如果用户只选择了一个文件，那么只需要考虑列表中的第一个文件。

### DOM

使用传统的 DOM 选择器访问一个被选择的文件：

```js
const selectedFile = document.getElementById("input").files[0];
```

![](https://raw.githubusercontent.com/chiyu-git/chiyuu-pic/master/20200321174523.png)

### Change 事件

```html
<input type="file" id="input" onchange="handleFiles(this.files)" />
```

当用户选择一个文件时，`handleFiles()` 方法会用一个 [`FileList`](https://developer.mozilla.org/zh-CN/docs/Web/API/FileList) 对象作为参数被调用，[`FileList`](https://developer.mozilla.org/zh-CN/docs/Web/API/FileList) 对象包含表示用户选择的文件的 [`File`](https://developer.mozilla.org/zh-CN/docs/Web/API/File) 对象。

如果你想让用户选择多个文件，只需在 `input` 元素上使用 `multiple` 属性：

```html
<input type="file" id="input" multiple onchange="handleFiles(this.files)" />
```

在这个例子中，对于每个用户选择的文件，传递给 `handleFiles()` 方法的文件列表都包含一个对应的 [`File`](https://developer.mozilla.org/zh-CN/docs/Web/API/File) 对象。

### 访问被选择的 Json 文件

访问其实可以理解成获取文件的路径，之后可以通过 Fetch 或者 XHR 去拿到数据

```js
const selectedFile = fileInput.current.files[0];
const reader = new FileReader(); //这里是核心！！！读取操作就是由它完成的。
reader.readAsText(selectedFile); //读取文件的内容
reader.onload = function () {
  console.log("读取结果：", this.result); //当读取完成之后会回调这个函数，然后此时文件的内容存储到了result中。直接操作即可。
  const list = JSON.parse(this.result);
  setCandidateList([...candidateList, ...list]);
};
```

### 使用隐藏的 File Input 元素

可以通过给 input 元素添加 `display:none` 的样式，再调用 input 元素的 `click()` 方法来实现。

# 表单校验

Yup 工具库

# Faq

#faq/ui

如何判断一个 input 为空？是空字符串？还是 null 呢？

空的 text input 是一个空字符串

**提示**：除非你需要进行传统的表单提交，否则无需指定 checkbox 的 name 属性。San 仅以 checked 作为分组的依据。

**提示**：你需要手工指定分组 radio 的 name 属性，使浏览器能处理 radio 选择的互斥。可以把它设置成与绑定数据的名称相同。

## 限制用户输入个数

到达一定个数之后，slice(0,n)

到达一定个数之后，preventDefault

利用这个方法，可以为文本输入框设置校验条件。如果用户的输入不符合条件，就无法将字符输入文本框。

```js
// <input type="text" id="my-input" />
var input = document.getElementById("my-input");
input.addEventListener("keypress", checkName, false);

function checkName(e) {
  if (e.charCode < 97 || e.charCode > 122) {
    e.preventDefault();
  }
}
```

上面代码为文本框的 `keypress` 事件设定监听函数后，将只能输入小写字母，否则输入事件的默认行为（写入文本框）将被取消，导致不能向文本框输入内容。

## Form 表单当前页面无刷新提交

使用 target 属性取值为 iframe 元素的 name 属性值。具体如下：

在当前页面建一个 iframe 并隐藏（display:none）

给这个 iframe 取名（name="id_iframe"）

设置 form 表单的 target 属性（target="id_iframe"）

提交表单，就是无刷新

## 表单到底要怎么提交？

如果是原生 HTML，没办法控制各个表单的值，做缓存之类的好麻烦？

如果用 spa 一个个双向绑定，又会非常麻烦

## 让表单不可输入

### 方案一

用 slice() 不断的截取输入的，然后赋值回去

### 方案二

可以输入是 input 的默认行为，当达到字数限制之后，return false

变种：**使得文本框只能输入字母**，在文本框中输入内容，属于 `onkeydown` 的默认行为如果在 `onkeydown` 中取消了默认行为，则输入的内容，不会出现在文本框中

```js
//使得文本框中只能输入字母
if (event.keyCode >= 48 && event.keyCode <= 57) {
  return false;
}
```

### 方案三

onfocus=this.blur() 点击事件触发失去焦点事件，相当于无法选中

```html
<input type="text" *name*="input1" value="中国" *onfocus*="this.blur()" />
```

### 方案四

readonly ：不可以修改，可以选中复制

```html
<input type="text" name="input1" value="中国" readonly="readonly " />
```

### 方案五

disabled：不可用，不可以点击

```html
<input type="text" name="input1" value="中国" disabled="disabled" />
```

### 方案六

H5 表单验证
