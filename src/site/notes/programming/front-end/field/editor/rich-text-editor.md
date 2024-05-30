---
{"aliases":["富文本编辑器"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-10-10-Mon, 8:39:56 am","date-modified":"2024-04-29-Mon, 10:16:21 am","permalink":"/programming/front-end/field/editor/rich-text-editor/","dgPassFrontmatter":true}
---


# 实现原理

## 概述

不同实现方式的难度排名：`contenteditable + document.execCommand()` < `contenteditable + Selection + Range` < DOM 完全自己写 <<<<<<< Canvas

Google Doc 的新方案已经不是一般团队能够实现的了，完整的界面 UI 乃至光标的闪动都是用 div 标签重新绘制的。用户交互极其繁杂，边界 case 极其之多，如果要实现需要耗费的精力是十分大的。

所以我们的精力还是会集中在怎么改造浏览器原生实现的 contentEditable，让其变得更好用的方向上。

## Contenteditable + document.execCommand()

### 基础

`contentEditable` 实际上是浏览器厂商提供的一个富文本编辑器的实现方案。通过设置一个 dom 的 contenteditable 属性为 true，可以让这个 DOM 变得可以编辑。

当一段 document 被转为 designMode（contentEditable 和 input 框）的时候，`document.execCommand` 的 command 会作用于光标选区（可以进行加粗，斜体等操作），也可能会插入一个新的元素，也可能会影响当前行的内容，取决于 command 是啥。

### 优点

1. 技术门槛低。只要 **使用** 以上两个 API ，就可以让网页具备编辑能力。 基本上只需要处理用户命令（比如加粗操作），然后调用 document.execCommand() 就可以了。文档在这里：[Document.execCommand() - Web APIs | MDN (mozilla.org)](https://link.zhihu.com/?target=https%3A//developer.mozilla.org/en-US/docs/Web/API/Document/execCommand)。
2. 基于浏览器原生编辑能力，输入非常流畅。
3. 没有令人头疼的组合输入问题。

### 缺陷一：依赖浏览器实现

这些 api 比较依赖浏览器行为，不同浏览器下的 execCommand 可能会产生不同的结果。当我们在一个空的 contentEditable 的 dom 里面打一个回车，那么预期的表现是什么？换行。那么承载这个新的行的标签是什么？

- Chrome/Safari 是 div 标签
- Firefox 在 60 版本之前，是在当前的行级标签中加一个<br/>
- Firefox 在 60 版本之后，趋同于 Chrome/Safari，是 div 标签
- IE/Opera 是 p 标签

当然这个问题是能解决的，在空的 contentEditable 的 dom 添加 `<p><br/></p>` 就可以解决。用户在一个块级标签里面输入\n（回车），就会根据当前的块级标签新建下一行的标签，实际上大部分编辑器都是通过这个方案解决新建行标签问题的。

### 缺陷二：obsolete Api

此外就是根据 MDN 文档显示，这个 API 已经过时了，不应再使用，说不定将来会把这个 API 废弃掉。

### 其他缺陷

 1. 输出富文本内容是 HTML ，不利于管理数据。
 2. 没有办法实现协同编辑。

## Contenteditable + Selection + Range`

这种方式比上面的 execCommand 好处就在可以不过度依赖浏览器的行为，使用 Selection 获取用户选区，使用 Range API 来操控选区（比如简单的加粗操作就调用 Range.surroundContents()）

不过用这种方式就需要你自己去处理各种选区情况了（比如节点内操作、跨节点操作等），反正也是有一堆坑的

另外附上 Selection & Range API 一个比较好的教程：[选择（Selection）和范围（Range） (javascript.info)](https://link.zhihu.com/?target=https%3A//zh.javascript.info/selection-range)

### 传统模式

DOM 树等于数据，调用各种 DOM API 进行操作

典型产品：CKEditor 4、TinyMCE、 UEditor

> 语雀原本是使用基于 Slate 的模式，但是因为疑难杂症多、排查链路长、新增功能困难等问题又改用了传统模式。
>
 > 文中也总结了相当多传统模式的坑

![](rich-text-editor/富文本编辑器的技术演进-罗龙浩.pdf)

### MVC 模式

数据和渲染分离，实现一套操作数据模型的方法，数据变更带动渲染

典型产品： CKEditor 5、Draft、Slate、ProseMirror

劣势：引起数据和渲染不同步的问题，因为这个机制需要以完全控制用户输入为前提，实际上基于 contenteditable 没办法控制用户的所有输入，第三方输入法，壳浏览器会让用户输入不可控。详见: [难以回避的输入法问题](2022-Q4.md#难以回避的输入法问题)

## 摒弃 `contenteditable`

### 纯 DOM

案例：[Typora](https://link.zhihu.com/?target=https%3A//typora.io/)、知乎文章编辑器、vscode 编辑器 manaco

这种肯定也用到了 Selection & Range API，但是最大的难点应该是输入光标的定位，还要自己处理各种输入事件（文字输入、回车换行等）。因为完全不使用 contenteditable，也就完全摆脱了对浏览器行为的依赖。

### Canvas

案例：[腾讯文档](https://link.zhihu.com/?target=https%3A//docs.qq.com/)、Google Docs

project-init

# 多人协同

调研对象：Google Docs、Etherpad、 CKEditor 5、Slate、Quill

结论：都用 OT（Operational Transformation） 或类似的技术，将操作转化成 OP （operations），发送到协作服务，再转发给其它在线用户。所以都具备原子化的操作 API，所有的高级操作都通过原子化 API 完成，实时协同只需要将这些原子化 API 的调 用信息转化成 OP 即可

Quill：insert、delete、retain、format

Slate：insert_text、remove_text、insert_node、merge_node、 remove_node、move_node、set_node、split_node

CKEditor 5：insert、move、detach、merge、split、attribute

# 编辑器原理分析

[ProseMirror、Slate 和 Lexical 实现原理分析] (https://juejin.cn/post/7140921781380415501u)

[Notion 编辑器原理分析 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/359122473)

ContentEditable 困境与破局 https://zhuanlan.zhihu.com/p/123341288

## 基于 Contenteditable 的问题

[从流行的编辑器架构聊聊 Web 富文本编辑器的困境 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/226002936)

### 视觉内容（用户所见）与实际内容（DOM）的一对多关系

这是大家都熟知的，下面的几个标签最终的表示效果是一样的

```html
<strong><em>aaaa</em></strong>
<em><strong>aaaa</strong></em>
<b><i>aaaa</i></b>
<i><b>aaaa</b></i>
<strong><em>aa</em><em>aa</em></strong>
...
```

这带来的问题是，用户在继续输入的时候，新增的文本到底应该是 em，还是 strong，还是 em + strong 的结构

上述的种种只是 contentEditable 坑的冰山一角，在没有明确的规则约束的前提下，用户在 contentEditable 的 dom 中编写出什么样的结构都是有可能的。这给我们带来的问题就是

- **视觉上等价，但是在 DOM 结构上不是等价的。**
- **contentEditable 生成的 DOM 不总是符合我们的预期的。**

### 视觉选区与实际选区的多对多关系

选区的情况则更加糟糕，一个视觉选区可能映射为多种 DOM 选区，而一个 DOM 选区也可能映射为多个视觉选区，例如我们的 HTML 为:

```html
his name was <strong><em>Baggins</em></strong>
```

如果用户看到光标落在 `Baggins` 之前，那么 DOM 选区可以是：

```html
his name was <cursoe /><strong><em>Baggins</em></strong>
his name was <strong><cursoe /><em>Baggins</em></strong>
his name was <strong><em><cursoe />Baggins</em></strong>
```

在光标位置插入字符 `I`，上面不同的 DOM 选区就会让用户看到不同的文本

- his name was I _**Baggins**_
- his name was **I**_Baggins_
- his name was **_IBaggins_**

而在文本：

> The hobbit was a very well-to-do hobbit, and his name was **_Baggins_**.

的 `well-to-` 后回车，用户将看到：

> The hobbit was a very well-to do hobbit, and his name was Baggins.

从第一行末尾到第二行开头的 DOM 选区，在视觉上看有两种可能，要么在第一行末尾，要么在第二行开头。面对这样的 “悬摆 DOM 选区”，你无法告诉浏览器将其映射为哪个视觉选区。

让浏览器自己实现 contenteditbale 元素视觉与实际的统一，已经颇为不易，如果还期盼不同的对 contenteditbale 有一致的处理，就难上加难了。这也就是为什么时至今日，浏览器在功能和性能上已经大幅发展了，开发者还是不信任它的富文本处理能力，社区的 Web 富文本编辑器也还是层出不穷。

## MVC 型编辑器的基本原理

### 核心机制: 双向同步

Why ContentEditable is Terrible? 还有一个副标题： _How Medium Editor Works?_，它不仅仅描述了 contenteditable 存在的缺陷，还以此为引，阐述了 Medium Editor 的工作机制。近年来社区活跃度颇高的 Slate.js，Draft.js 、ProseMirror 以及 CKEditor 等也使用了类似的机制，来规避 contenteditable 缺陷，这个机制可以被简单描述为：

- **模型与视图相分离**
- 让编辑器决定数据结构（内容 + 选区），保证视觉与实际一致
- 方便同样的内容在不同设备设备展示
- **自定义命令**
- 脱离实现不一的浏览器命令（`document.execCommand`），自行划定编辑器的操作范围

这种机制最小程度使用了 contenteditble —— 仅仅让一个节点的 HTML 内容允许被编辑，而如何编辑，编辑器结果则收敛到了编辑器治理

### 同步策略

编辑器使用了视图模型相分离的架构，就需要处理视图与模型间的同步，当用户在编辑器完成了操作后，编辑器据此更新数据模型，再通过数据模型渲染出新的页面视图即对视图：

编辑器是如何知道用户执行了哪个操作呢？答案就是 DOM 事件，假设监听到了 `keyCode = 8` 的 keydown 事件（Event），就推测用户是在执行退格（Intent），从而调用自定义的删除指令（Command），修改数据模型，最终用户看到光标前的字符被成功删除了。

基于事件拦截的数据同步，是理想型的同步链路，先更新模型，再渲染视图，由于模型和视图一一对应的关系，用户执行了相同的操作，总能获得一致的 DOM 结构，

然而，由于 DOM Event 的规范仍然在发展，各个浏览器对其的实现也不尽相同，因此，基于事件拦截的同步也不完全可靠。

举个例子来说，beforeinput event 是非常有用的一个事件，该事件在 [Input Events Level 1](https://link.zhihu.com/?target=https%3A//www.w3.org/TR/2017/WD-input-events-1-20170321/) 被定义，并在 [Input Events Level 2](https://link.zhihu.com/?target=https%3A//www.w3.org/TR/input-events-2/) 中获得完善，规范希望能帮助类似 Web 富文本编辑器这样的应用更容易地处理用户输入。

beforeinput event 能让开发者在输入反馈到 UI 之前，通过事件的 `inputType` 属性知悉用户输入意图（例如，当 `inputType` 是 `deleteContentBackward` 时，我们就知道用户正在向前删除内容）， 但尴尬的是：

- 要么浏览器不支持这个事件
- 要么部分支持这个事件：例如覆盖到的 input type 不全

因此，DOM Event 本身的不完美，也就让编辑器难以完全摸清用户意图。而对用户意图的模糊，也会造成：

- **无法理解**：编辑器忽略了某个用户操作
- **理解错误**：将用户的操作理解为了另一个操作，例如用户是在输入，但被编辑器理解为了删除，造成数据模型发生非预期变更，用户看到错误的视图

因此，基于事件的同步模型也是一个理想型模型，受限于浏览器支持，在实际应用中并不完全可靠。

## 难以回避的输入法问题

在编辑器的生态中，作为文本合成（Text Composition）的主要手段，输入法能让编辑器展示和处理更多的语言，是编辑器的不可获取的伙伴。然后输入法本身又不受控于编辑器，它完全由第三方厂商实现，其实现也只和操作系统有关，而你编辑器的生死存亡，却不是它的义务

我们看到，当生态中加入输入法之后，链路变得更加复杂了：输入法和操作系统交互来合成文本，浏览器也通过操作系统，获得输入法状态，在输入过程中抛出对应事件。

其次，抛开这个链路不看，输入法自己也不是个 “省油的灯”：

- **输入法繁多**：不同的操作系统版本、浏览器内核，和输入法能形成众多的组合，每种组合在 contenteditable 元素上进行文本合成的时候，难以形成一致的事件流
- **输入方式多样**：除了键盘能够进行文本合成，还要支持手写、语音等输入方式

而 Android 因为其生态紊乱，系统版本分布广泛，使用不同内核的浏览器众多，应用市场的第三方输入法层出不穷，让基于事件进行同步的策略更加脆弱。举个例子，我们在 Android 下使用某个第三方浏览器配合上某个第三方输入法编写内容时，可能遇到：

- **浏览器不会抛出 beforeinput 事件**：编辑器无法通过 beforeinput 事件中的 inputType 属性知悉用户行为
- **浏览器没有传递正确的虚拟键盘响应**：很多 Andorid 输入法都支持字词联想，当虚拟键盘呼出后，开始进入 Composing 状态，此时 keydown 事件的 `keyCode` 都会被设置为 229，那么编辑器无法也无法从 keydown 事件知悉用户行为

> [W3C KeyCode 规范](https://link.zhihu.com/?target=https%3A//lists.w3.org/Archives/Public/www-dom/2010JulSep/att-0182/keyCode-spec.html) 定义了 keyCode 为 229 的 Keyboard Event 是表示输入法正在处理按键输入

这就会造成比较恶性的现象，举个例子，当用户将光标放置在图片之后时，输入法被唤起，用户通过虚拟键盘按下了退格键希望能够删除图片。但由于编辑器既无法拦截 beforeinput 事件来判断用户是不是在执行删除，也无法通过 keydown 事件判断判断键盘的退格是否被按下，因此，也就不会生成对应的命令去修改数据模型，删除对应节点。此时，用户无论怎么疯狂点按退格，都不能把图片删掉。

因此，基于事件的同步策略，在面对输入法时，变得更加不可靠，社区目前流行的开源编辑器也仍在和 Android 输入法作斗争

## 针对输入法问题的同步策略

编辑器之所以希冀于事件去同步数据模型和视图，是期望在浏览器对页面执行变更前，尽早将用户行为同步到数据模型上，实现实际内容与视觉内容的统一。但我们发现，这个本就不健壮的策略再遇到输入法之后，无法响应用户操作的概率更高了。

如果回到架构之前，仍然托管 contenteditable 的控制权给浏览器，那么，我们发现，输入法的增删改查还是能正确响应的，这也就说明了，托管给浏览器去处理 contenteditable，用户最基本的编辑诉求还是可以保障的。但我们又不能放任浏览器这么做，原因在开篇回顾 Why ContentEditable is Terrible 中也说了，因此，当浏览器处理了用户的行为并更新了视图后，我们要尽快的去 “纠正” 数据模型，亡羊补牢，犹未为晚。

浏览器提供了 [Mutation Observer](https://link.zhihu.com/?target=https%3A//developer.mozilla.org/en-US/docs/Web/API/MutationObserver) 来让开发者监听 DOM 变更，它的 API 非常简单，初始化观察者之后，在适当时机开始观察即可，每次观察到的变更序列，其中的元素都是 [MutationRecord](https://link.zhihu.com/?target=https%3A//developer.mozilla.org/en-US/docs/Web/API/MutationRecord) 对象。

借助于 Mutation Observer，编辑器在观察到 DOM 变化后，可以根据变化（现象）来反推用户行为，从而生成对应的命令，去修缮数据模型：

![](./rich-text-editor/image-20221002191001110.png)

例如，经过反复的验证发现，当用户在某个段落顶部按下退格并合并了上下两个段落后，observer 将观察到一个 mutation 序列，这个序列的特征为：

- 第一个 mutation record 的类型为 `childList`，且 removeNodes 不为空
- mutation 序列包含了多个 mutation record

那么，我们就告诉编辑器，当观察到这样的 mutation 序列时，就是用户完成了合并段落，你需要去执行一条 `merge-block` 的指令，合并数据模型上的两个段落。

这种 **事后同步**，弥补了浏览器无法派发正确事件时，我们仍能推测用户行为 —— 通过现象去反推用户行为。但是，由于现象的成因可能有多个，因此我们就可能造成错误的推断：

![](image-20221002191044970.png)

就拿上面的举例的 mutation 序列来说，编辑器中的图片，在加载完成之前，会先展示 loading 占位，当加载完毕后，observer 也会观察到同样特征的 mutation 序列。但是，编辑器先前已经被告知这样的序列是在合并段落，它会调用 `merge-block` 指令去修改模型，假设该指令内部还可能调用到 `delete` 指令，那么就会造成刚加载完成的图片又被删除了，用户永远也无法在编辑器中上传图片。为此，我们又得在算法中告诉编辑器如何识别图片的这个特征。

其实，算法的工作过程非常类似于机器学习中的分类问题：

**提取特征 -> 分析特征 -> 获得所属分类**

这种算法需要被不断地被训练（发现并扩充特征），才能让最后的预测更加精准。只是与机器学习不同的是，我们的是人肉学习，为了获得更多特征，去更精确的映射用户行为，开发者将疲于应付不同的 Android 设备，不同的浏览器，以及不同的输入法形成的种种组合。

综上，基于现象去同步数据模型，是更不牢靠的手段， 即便开发者投入了大量精力去获得现象和意图间的关联关系，但难免还是有 “漏网之鱼”，导致模型 **同步错误**，这也许比不能响应操作更加严重。而基于事件的同步模型，因为事件本身就表达了用户意图，所以推测会更加精准，所以，只有当事件系统不完善时（例如在 Android 设备下），我们才使用基于现象的同步做兜底，保证用户在编辑器能进行最基本的输入。

## DOM 的虚实调和

刚才所讨论的内容，还聚焦在使用了 contenteditable 的编辑器上，如果编辑器在视图层使用了更高的抽象，例如选用了类似 React 这样的基于虚拟 DOM 的解决方案，那么问题还将加剧。

现代化的前端开发中，已经很少使用真实 DOM 来表达一个组件了，如果选用了类似 React 这样的解决方案去描述实现编辑器视图（Slate.js、Draft.js 就倾向于此），就会让视图的表达 “虚实并存”：

- **虚**：React 根据组件属性和状态，创建并且维护了一棵组件对应的 virtual DOM tree，并使用 virtual DOM tree 渲染节点内容
- **实**：对 contenteditable 的节点的修改会被浏览器托管，绕开了 React 去修改 DOM

所以，当你尝试用与下面类似的代码创建一个 contenteditale 组件时：

React 会警告你要自行管控 contenteditable 的节点变更：

> Warning: A component is contentEditable and contains children managed by React. It is now your responsibility to guarantee that none of those nodes are unexpectedly modified or duplicated. This is probably not intentional.

所以我们在使用诸如 Slate.js 或者 Draft.js 这样的框架时，会遇到下面这样的异常，此时整个编辑器无法继续工作：

> DOMException: Failed to execute 'removeChild' on 'Node': The node to be removed is not a child of this node.

我们姑且称这个现象为：**虚实不调，阴阳失衡**。

在 Android 设备上，为了解决输入法问题，编辑器将输入先托管给浏览器，并 Mutation Observer 观察 DOM 变化，而后再去同步数据模型。在这样的策略下，用户的输入过程在没有结束之前，都是浏览器在处理，当中发生许许多多的变更，不再受 React 控制，很容易造成 “虚实不调”，引起编辑器崩溃。

因此，当使用了类似 React 这样的方案之后，编辑器需要更加 **谨小慎微地** 处理数据同步，在容易引起虚实不调的场合（例如段落分裂与合并），强刷（rerender）编辑器组件，调和虚实，让 DOM 与 VDOM 保持一致。而对于预料不到的场合，可能还需要借助 `componentDidCatch` 这样的 hook 去俘获异常，强刷组件。有 React 开发经验的同学都知道，对于编辑器这样内部子孙繁多的组件，重绘的开销是非常大的，最终，在终端用户一侧，就是看到页面闪烁和卡顿。

## 前路漫漫，任重道远

上文对 Web 富文本编辑器困局的讨论，还仅仅局限单机版的富文本编辑器，如果还要为编辑器支持多人实时协同，那么面对的挑战更加艰巨。

面对这样的生态，做不到抛弃，编辑器只能去适应和妥协，但在其领域范围内，似乎还可以有一些突破：

- **是否能通过交互绕开缺陷？**：上面我们举例的图片无法通过退格删除的问题，但键盘不是唯一的 I/O，如果在交互上，能多一个删除图片的方式，就不会阻塞用户。
- **是否能脱离 contenteditable 的架构？**：更大程度的以模型为核心，完全自绘内容和选区，脱离 contenteditable，让视图层更加可控。

但是无论哪种解法，对编辑器的产品逻辑和开发者的技术要求都是非常大的。创建一个优秀的 Web 富文本编辑器，依然任重道远。

contenteditable is terrible, 但是编辑器已经最小化了对它的使用，比之更为严峻的是，操作系统、浏览器、输入法相互组合形成的紊乱生态 —— 一个编辑器无法控制的，但产品又期望在上面开出繁花的生态。所以才说，Web 富文本编辑器是前端的天坑之一。

# Slate

始于 2016 年

https://github.com/ianstormtaylor/slate

[slate - npm (npmjs.com)](https://www.npmjs.com/package/slate)

[Introduction - Slate (slatejs.org)](https://docs.slatejs.org/)

教程： https://zhuanlan.zhihu.com/p/324209467

基本上就是 L1 MVC 型的佼佼者，多数公司的产品都是基于 Slate 使用的

Slate 是一个编辑器框架，而不是开箱即用的编辑器工具。作为晚辈的 Slate 集合了前辈们的许多优点，从 Draft.js 那里参考的 Immutable 数据、插件机制和 React 视图层，又从 ProseMirror 借鉴了嵌套数据结构和 Schema 约束规则。整合了许多编辑器框架的核心特性，又加上框架理念先进和作者对架构的追求（时至今日 2022 ，仍然是 beta 的状态，还没到 1.0），Slate 在社区上还是比较受欢迎的。

```json
{
    type: 'paragraph',
    children: [
      { text: 'This is editable ' },
      { text: 'rich', bold: true },
      { text: ' text, ' },
      { text: 'much', italic: true },
      { text: ' better than a ' },
      { text: '<textarea>', code: true },
      { text: '!' },
    ],
}
```

Slate 通过插件化管理，将所有功能抽象成插件，不限定渲染框架。官方给出的是 `slate-react` 进行渲染，但是也可以用 [Angular](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fworktile%2Fslate-angular "https://github.com/worktile/slate-angular")、[Vue](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fmarsprince%2Fslate-vue "https://github.com/marsprince/slate-vue") 等前端框架实现视图层的渲染。

It can do this because all of its logic is implemented with a series of plugins, so you aren't ever constrained by what is or isn't in "core". You can think of it like a pluggable implementation of `contenteditable` built on top of [React](https://facebook.github.io/react/). It was inspired by libraries like [Draft.js](https://facebook.github.io/draft-js/), [Prosemirror](http://prosemirror.net/) and [Quill](http://quilljs.com/).

> 🤖 **Slate is currently in beta**. Its core API is usable now, but you might need to pull request fixes for advanced use cases. Some of its APIs are not "finalized" and will (breaking) change over time as we find better solutions.

## Key Features

### First-class Plugins

### Schema-less Core

Slate's core logic assumes very little about the schema of the data you'll be editing, which means that there are no assumptions baked into the library that'll trip you up when you need to go beyond the most basic use cases.

### Nested Document Model & Parallel to the DOM

The document model used for Slate is a nested, recursive tree, just like the DOM itself. This means that creating complex components like tables or nested block quotes are possible for advanced use cases. But it's also easy to keep it simple by only using a single level of hierarchy.

Slate's data model is based on the DOM—the document is a nested tree, it uses selections and ranges, and it exposes all the standard event handlers. Pretty much anything you can do in the DOM, you can do in Slate.

### Intuitive Commands

### Collaboration-ready Data Model

### Clear "core" Boundaries

1. 非常简洁的支持嵌套的数据模型, 产生 JSON 输出，使其更容易与其他模块集成。
3. 插件机制支持开发强大的功能
4. 整体的设计理念与 DOM 很像
5. 自有概念和一些 Commands 更精简更抽象，改名为 Transforms

## 优点

- 它的嵌套文档模型支持更复杂的内容结构，如表格、分页符和其他自定义功能。
- 有很好的描述性 [文档](https://link.juejin.cn?target=https%3A%2F%2Fdocs.slatejs.org%2Fwalkthroughs%2F01-installing-slate "https://docs.slatejs.org/walkthroughs/01-installing-slate") 和互动演示。
- 受到 Salesforce 和 Airtable 的信任。

## 缺点

- 仍处于测试阶段（截至 2022 年 10 月），这对于信任和在生产网站上实施可能是个坏消息。
- 需要更多的初始 UI 设置来适应编辑器控制。
依赖于视图层框架, 没有 vanilla js 的实现, 如果 san 需要使用的话, 得基于 san 实现一套. 目前 slate-vue , [**svelte-slate**](https://github.com/nathanfaucett/svelte-slate)
    , slate-angular 使用的人数非常少, 我们重新造一个的可行性, 不太好评估. 参考其他视图层框架的代码库, 初步估计需要 2~3 周

# Tiptap

[tiptap-basic](tiptap-basic.md)

# EditorJs

始于 2017 年

[Editor.js (editorjs.io)](https://editorjs.io/)

[@editorjs/editorjs - npm (npmjs.com)](https://www.npmjs.com/package/@editorjs/editorjs)

## Why Block-style

what's the killer feature provide by block-style?

All of us saw permanent bugs with moving text fragments or scaling images, while page parts are jumping and twitches.

Or highlighting big parts of the text in the case when you just want to make few words to be a heading or bold.

### For Clean Data

because all the element are single block, It can be easily described row by row, block by bock compare to classic L1 MVC editor.

There are only data we need: a list of structural Blocks with their content description.

## Key Features

基于 vanilla js

[Base concepts (editorjs.io)](https://editorjs.io/base-concepts)

### Block-style

block-style + contenteditable 的 MVC editor, 每个 block 都是 contenteditable

The Editor.js workspace consists of separate Blocks: paragraphs, headings, images, lists, quotes, etc. Each of them is an independent `contenteditable` element (or more complex structure) provided by Plugin and united by Editor's Core.

There are dozens of [ready-to-use Blocks](https://github.com/editor-js) and the [simple API](https://editorjs.io/creating-a-block-tool) for creation any Block you need. For example, you can implement Blocks for Tweets, Instagram posts, surveys and polls, CTA-buttons and even games.

### Clean Data Output in JSON

Given data can be used as you want: render with HTML for `Web clients`, render natively for `mobile apps`, create templates for `Facebook Instant Articles` or `Google AMP`, generate an `audio version` and so on.

Also, the clean data can be useful for backend processing: sanitizing, validation, injecting an advertising or other stuff, extracting Headings, make covers for social networks from Image Blocks, and other.

see the JSON output at homepage: [Editor.js (editorjs.io)](https://editorjs.io/)

### API Pluggable

It designed to be extendable and pluggable with a simple API

A key value of the Editor is the API. All main functional units of the editor — Blocks, Inline Formatting Tools, Block Tunes — are provided by external plugins that use Editor's API.

We decide to extract all these Tools to separate scripts to make Editor's Core more abstract and make API more powerful. Any challenges and tasks you are facing can be implemented by your own plugins using the API. 

At the same time, API is created to be easy-to-understand and simple-to-use.

## Use Technique

### Available Blocks

You can find some available Blocks [here](https://github.com/editor-js). Select the Blocks you need and follow the installation guide in their README.md files.

1. [Header](https://github.com/editor-js/header)
2. [Link embeds](https://github.com/editor-js/link)
3. [Raw HTML blocks](https://github.com/editor-js/raw)
4. [Simple Image](https://github.com/editor-js/simple-image) (without backend requirement)
5. [Image](https://github.com/editor-js/image)
6. [Checklist](https://github.com/editor-js/checklist)
7. [List](https://github.com/editor-js/list)
8. [Embeds](https://github.com/editor-js/embed)
9. [Quote](https://github.com/editor-js/quote)

After Tools installation, you should connect them to the Editor via the configuration object.

### Awesome-editorjs

[editor-js/awesome-editorjs: 🤩 A curated list of awesome Editor.js tools, libraries and resources. (github.com)](https://github.com/editor-js/awesome-editorjs)

[Editor.js is The Best Modular Open-source Content Block Editor for 2022 (medevel.com)](https://medevel.com/editor-js/)

an commercial editor base on editor.js

[WP Front User Submit ✅ Post Submission and editing Plugin (wpfronteditor.com)](https://wpfronteditor.com/)

## 优点

数据结构简单, 解析起来比较容易

接入确实是挺方便的

支持阅读模式, 不用重新实现预览功能

## 缺点

虽然 star 数量很多,但是 npm 下载量很一般, 社区感觉也不够强大

类型声明不完善, 最常用的插件模块, header list, 都没有人维护类型声明

没有顶部的 toolbar, 都是 inline toolbar

感觉插件不是很够, 可能很多功能都得自己实现了

目前没有协作编辑的实现

样式自定义不够直接

# 其他 Rich Text Editor RTE

UEditor：停止维护

厂内 bjh-editor：停止维护 https://console.cloud.baidu-int.com/devops/icode/repos/baidu/bjh/bjh-editor/tree/master

https://gamma.app/ ppt creator

## Froala

案例：百度文库

基于 jQuery

https://froala.com/wysiwyg-editor/

[厂内已经众筹购买](https://console.cloud.baidu-int.com/devops/icode/repos/baidu/third-party/froala-editor/tree/master)： 可以使用到 3.1 版本，使用 3.2 以上版本会提示过期

> key 应该与一级域名绑定的，所以泄漏了别人也用不了，看了下厂内在用的都是直接写死在前端，没啥问题。

### 优点

1. API 和 文档 非常全面，开发者很容易上手。
2. 自带 30 + 有用的插件：https://www.froala.com/wysiwyg-editor/plug...
3. 可以自定义主题和插件，比如控制它的 UI：https://www.froala.com/wysiwyg-editor/cust...
4. 支持 Inline 编辑，可以点击这个链接去试试：[https://www.froala.com/wysiwyg-editor/inli...](https://www.froala.com/wysiwyg-editor/inline)
5. 内置图片管理器和图片编辑器

### 缺点

1. 看起来是 L0 的编辑器，可以肯定 `eidtor.doc.execCommand` 部分就是 L0 的命令
2. 在实例上看一下 `editor.format` 是啥，感觉这一部分是 L1 的，因为看到了 selection range 啥的
3. 不支持模板

## TinyMCE

[https://www.tiny.cloud/docs/demo/full-featured/](https://www.tiny.cloud/docs/demo/full-featured/)

这个编辑器是一个商业库，带有大量的铃声和口哨。如果你有一些额外的钱，并希望拥有一个不头痛的强大的文本编辑器，TinyMCE 是一个不错的选择。

但是公司直接就是用的 github 上开源版本，没有购买商业版本

### 优点

- 支持实时协作。
- 支持提及和评论。
- 先进的表格和复杂内容支持。
- 增强的媒体嵌入支持。
- 自动链接检查器。

### 缺点

- 需要购买订阅才能使用高级功能。

## Quill

始于 2012 年

https://github.com/quilljs/quill

[quill - npm (npmjs.com)](https://www.npmjs.com/package/quill)

[https://surmon-china.github.io/vue-quill-editor/](https://link.zhihu.com/?target=https%3A//surmon-china.github.io/vue-quill-editor/)

小程序 editor 组件： https://ku.baidu-int.com/knowledge/HFVrC7hq1Q/xjYiDpu--c/DNAh7_KwUh/Osq9TCThnoS1mZ?source=101

基本功能都有，图片和视频上传、输入公式等功能需要进一步定制化开发。

Quill 对 DOM Tree 以及数据的修改操作进行了抽象，从而实际使用时不需要我们对 DOM 操作，而是通过 Quill 的 API 进行操作，对应的关系如下：

+ Editor Document ====> **Parchment**
+ DOM Node ====> **Blot**

```json
{
    "ops": [
        {
            "attributes": {
                "bold": true
            },
            "insert": "Check"
        },
        {
            "insert": " "
        },
        {
            "attributes": {
                "link": "https://donaldxdonald.xyz/"
            },
            "insert": "this"
        },
        {
            "insert": " out ~"
        }
    ]
}
```

  Delta 的扁平化结构其实是协同编辑中的 OT 模型的一种实现，因此 Quill 也是生来就是为了协同编辑而设计的。扁平化带来的好处是对性能提升有帮助，弊端则是在表示一些复杂的嵌套内容时会比较吃力。

### 特点

1. 引入了一层抽象的数据结构用以描述内容以及行为
2. 对协同编辑支持良好
3. 输出结构可以是字符串也可以是 Delta （JSON），但 Delta 作为数据模型可读性不高

### 优点

- 开箱即用
- 由于其 API 驱动的设计，不需要像其他文本编辑器那样解析 HTML 或不同的 DOM 树。
- 支持自定义内容和格式，有预设的编辑器样式。

### 缺点

- 功能的定制是有限的。
- 较少的更新和补丁。

## ProseMirror

始于 2015 年

ProseMirror 不是一个开箱即用的富文本编辑器，是一个基于 ContentEditable 的所见即所得 HTML 编辑器，功能强大，支持协作编辑和自定义文档模式 ProseMirror 库由多个单独的模块组成。ProseMirror 试着在 Markdown 编辑体验和传统的 WYSIWYG 编辑体验中寻找一种融合的方法。

prosemirror 是一套工具包提供给开发者，方便开发者在此之上开发富文本编辑器的。它的主要原则是开发者享有文档及事件变更的控制权。这里的文档是自定义的数据结构，只包含你允许的元素，用来描述内容本身及其变化，所有的变化都是可追溯到的。

ProseMirror 是有 schema （范式）的，所以定义好了 schema 以后 ProseMirror 可以替你实现自动化 parser 。框架层面定义好了新引入一个 Node 需要什么属性和方法，比如 `nodeFromJSON` 方法做结构到 JSON 的转换，`toDOM` 方法定义如何将结构数据转换为 DOM（有点类似 JSX ）。ProseMirror 就在中间这一层做了 JSON 数据到 DOM 的变更管理。

```json
{
    "type": "paragraph",
    "content": [
        {
            "type": "text",
            "marks": [
                {
                    "type": "strong"
                }
            ],
            "text": "Check"
        },
        {
            "type": "text",
            "text": " "
        },
        {
            "type": "text",
            "marks": [
                {
                    "type": "link",
                    "attrs": {
                        "href": "https://donaldxdonald.xyz/",
                        "title": ""
                    }
                }
            ],
            "text": "this"
        },
        {
            "type": "text",
            "text": " out ~"
        }
    ]
}
```

### 特点

1. 更抽象的 JSON 文档模型。ProseMirror 只定义了可配置的模型框架，具体的结构可以在实际开发的时候自定义。
2. 嵌套的树形结构。能支持复杂结构的内容。
3. 对协同编辑的良好支持。从诞生之初，ProseMirror 就开始关注着协同编辑的支持。
4. 1.0 后加入了不可变数据，使得编辑器的数据处理有了一个完整的数据流，稳定且可控。

## Draft

始于 2016 年

https://github.com/facebook/draft-js

因为当年能够匹配 react & 质量较好的就只有 [draft.js](https://www.zhihu.com/search?q=draft.js&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222048279517%22%7D) ，但 draft.js 只能实现扁平 [数据结构](https://www.zhihu.com/search?q=%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222048279517%22%7D)，对于表格、多层嵌套的实现非常困难，这也导致知乎对 markdown 的支持不好，现在已经有了 prosemirror、[slatejs](https://www.zhihu.com/search?q=slatejs&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A%222048279517%22%7D) 等方案了

彼时还叫 Facebook 的 Meta 开源了 Draft.js ，既然都是同一个公司的，Draft.js 就在视图层方面使用了 React 渲染 UI 。这也是第一个 React + 编辑器结合的案例，React 的流行也让使用者可以快速地直接基于 Draft.js 进行二次开发。

Draft.js 不仅外表有 React ，内里也是有很深的 React 的影子，类似 Redux 等状态管理的 `EditorState` 和 `ContentState` ，在数据层使用 `Immutable` 等特性。JS 对象的属性是可以随意赋值的，也就是 mutable 可变的。而相对地，不可变的数据类型不允许随意赋值，每次通过 Immutable API 的修改，都会生成一个新的引用。

### 特点

1. 依赖浏览器原生编辑能力 `contentEditable` (L1)
2. 用 React 来实现视图层
3. 内容的存储和渲染逻辑分离
4. 使用 Immutable 数据
5. 虽然也抽象了基于 JSON 的数据模型，但是对于嵌套数据的支持有些弱

### 优点

- 高度的可扩展性和可定制性，有建立在 Draft.js 之上的插件。
- 扁平的内容模型很容易理解。
- 由于自 2016 年以来得到 Facebook 支持的大型、不断增长的开源开发者社区，因此有许多教程和支持。

### 缺点

- 当需要复杂的内容结构（如表格）时，编辑器会变得很慢，代码也会变得复杂。
- OSX 的自定义键绑定问题。
- 没有官方的移动支持。

## Lexical

始于 2022

Lexical 同 Slate 也使用了插件化。官方通过 `lexical-react` 进行渲染，但是并 [不拘泥于特定框架](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Ffacebook%2Flexical%2Fblob%2Fmain%2Fdocs%2Fdesign.md "https://github.com/facebook/lexical/blob/main/docs/design.md") 实现视图层。由于 Lexical 的数据结构是 Map 映射集合而不是普通对象，在渲染时需要先使用 [`reconcileNode()`](https://link.juejin.cn?target=) 这个方法进行节点映射集合的遍历。

Lexical 的节点是通过 Map 存储的（如下图），这和 Slate、ProseMirror 的树状数据结构有本质差异，主要体现在单个节点修改的效率和内存占用上。

### 特点

1. 保留了 Draft.js 中的一些概念（EditorState）
2. 不与 React 绑定了，可以用各种框架实现视图层
3. 整个框架挺轻便的，几乎没有什么其他的依赖

### 优点

Map 结构存储的内容能够很快增删改某个特定节点，而对于树状数据结构，为了保证数据是持久化的 [Single source of truth](https://link.juejin.cn?target=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FSingle_source_of_truth "https://en.wikipedia.org/wiki/Single_source_of_truth")，必须按照不可变数据的理念（Immutable）去生成一个新对象，造成内存占用增大的问题。

### 缺点

相应地，由于存储 Map 的结构不能够很好地表达实际渲染出来 DOM 结果的层次，所以在每次渲染的时候，需要做一次协调（Reconcilation）去生成层次结构，[可以把它想象成 React](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Ffacebook%2Flexical%2Fblob%2Fmain%2Fdocs%2Fdesign.md "https://github.com/facebook/lexical/blob/main/docs/design.md")，它通过双重缓存实现单向数据流渲染。

# FAQ

## @mention 功能的实现
