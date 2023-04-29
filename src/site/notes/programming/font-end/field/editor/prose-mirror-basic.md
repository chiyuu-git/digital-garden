---
{"dg-publish":true,"permalink":"/programming/font-end/field/editor/prose-mirror-basic/"}
---


[ProseMirror Guide](https://prosemirror.net/docs/guide/)

[「译」 ProseMirror 中文指南 - Xheldon Blog](https://www.xheldon.com/tech/prosemirror-guide-chinese.html)

[ProseMirror Docs](https://prosemirror.xheldon.com/docs/)

# 介绍

作者 **Marijn** 是 `codemirror` 编辑器和 `acorn` 解释器的作者，前者已经在 `Chrome` 和 `Firefox` 自带的调试工具里使用了，后者则是 `babel` 的依赖，他还撰写了一本广受欢迎的 JavaScript 编程入门书籍《Eloquent JavaScript》

![](/img/user/programming/font-end/field/editor/prose-mirror-basic/image-20230416211402236.png)

﻿

ProseMirror 依赖 contentEditable，不过非常厉害的是 ProseMirror 将主流的前端的架构理念应用到了编辑器的开发中，比如彻底使用纯 JSON 数据描述富文本内容，引入不可变数据以及 Virtual DOM 的概念，还有插件机制、分层、Schemas（范式）等等。

## 特点

- 模块化设计
- 实时协作编辑
- 拼写检查和样式格式化
- 内置功能丰富 (撤销/重做、拖拽/复制/粘贴、快捷键等)
- 注重性能 (高效更新)

## 核心模块

- **prosemirror-model 模型层**: 定义编辑器的文档模型，用来描述编辑器内容的数据结构
- **prosemirror-state 状态层**: 描述编辑器整体状态，包括文档数据、选区等
- **prosemirror-view 视图层**: 用于将编辑器状态展现为可编辑的元素，处理用户交互
- **prosemirror-transform 事务层**: 通过事务的方式修改文档，并支持修改记录、回放、重排等

## 其他模块

- prosemirror-commands 基本编辑命令
- prosemirror-keymap 键绑定
- prosemirror-history 历史记录
- prosemirror-inputrules 输入宏
- prosemirror-collab 协作编辑
- prosemirror-schema-basic 简单文档模式

# Concept in proseMirror

![](/img/user/programming/font-end/field/editor/prose-mirror-basic/image-20230416212929283.png)

1. Document: 即 Prosemirror 所在的整个文档, 通常 editor.view.state.doc 保持对其的引用.
2. Schema: Prosemirror 的骨架对象, 定义了编辑器的各种规则来约束文档, 有时候你需要手动处理以适应这些规则, 而大部分情况下 Prosemirror 会帮你处理以适应这些规则.
3. State: Prosemirror 的数据结构对象, 相当于是 react 的 state, 有 view 的 state 和 plugin 的局部 state 之分. 如上面的 schema 就定义在其上: state.schema.
4. View: Prosemirror 的视图对象, 其上有一些更新视图的方法, state 是其上一个属性: view.state.
5. Transform: 可以理解为存放文档变化的容器对象, 另外其上还有一些方法可以修改变化. 而 transaction 是其子类, 后者是针对整个编辑器的 state 变化的.
6. Selection: 即选区对象, 什么也不选的时候可以表示光标, 有多个位置相关的属性和方法.
7. Range: 多个节点对象的容器, 通常用来处理一段选区中包含多个类型的节点和 Mark 的情况.
8. Slice: 主要用来处理选区选到一半时导致不符合 schema 结构的问题的对象.
9. Node: Prosemirror 的基本元素, 可以通过 schema 来定义各种类型的节点, 至少包含 doc(根节点) 和 text(文本节点) 这两种节点.
10. NodeType: Prosemirror 的节点类型, 一般用来新建节点用, 定义了某个类型节点上的属性.
11. XXXSpec: 定义 XXX 时候的配置对象, 如 NodeSpec, MarkSpec 等.
12. Mark: Prosemirror 将行内文本视作扁平结构而非 DOM 类似的树状结构说是为了方便计数和操作, 因此 Mark 表示某个行内节点的属性, 如 font-size, bold 等, 支持自定义.
13. MarkType: 同节点类型, 定义了 Mark 的一些属性, 其上的一些方法可以用来创建 mark.
14. DOMOutputSpec: 就是在 schema 中的 toDOM 指定的返回值, [官方说明](https://prosemirror.xheldon.com/docs/ref/#model.DOMOutputSpec).
15. ResolvedPos: Prosemirror 解析位置信息 (详见下面的位置计数一节) 返回的对象, 包含了一些位置相关的信息.
16. Plugin: 通常使用 Plugin 实现一些行为, 如点击/粘贴/撤销等, Plugin 还可以直接定义节点.
17. Decoration: 通常用来生成与文档状态无关的视图, 可以用来做一些特效而不影响文档结构.

## 结构

在 HTML 中, 一个 paragraph 及其中包含的标记, 表现形式就像一个树, 比如有以下 HTML 结构:

`<p>This is <strong>strong text with <em>emphasis</em>﻿</strong>﻿</p>`

![](/img/user/programming/font-end/field/editor/prose-mirror-basic/image-20230416211754209.png)

Prosemirror 中, 内联元素被表示成一个 **扁平** 的模型, 他们的节点标记被作为 metadata 信息附加到相应 node 上

![](/img/user/programming/font-end/field/editor/prose-mirror-basic/image-20230416211823640.png)

**好处**：可以使用 **字符串偏移量** 而不是 **树节点路径** 来表示在段落中的位置、spliting 内容、改变内容 style 操作变的容易 (树操作 ->数组下标)

Prosemirror document 就是一颗 block nodes 的树, 它的大多数 leaf nodes 是 textblock 类型, 该节点是包含 text 的 block nodes。Node 对象有一系列属性来表示它们在文档中的行为，如 _isBlock、isInline、inlineContent、isTextBlock、isLeaf_ 等。

**缺点**：开发者重新学习它独有的描述 DOM 的范式。

## Document in proseMirror

Prosemirror 定义了它自己的﻿[数据结构](https://prosemirror.xheldon.com/docs/ref/#model.Node)﻿来表示 document 内容. 因为 document 是构建一个编辑器的核心元素, 因此理解 document 是如何工作的很有必要. document 是一个 ﻿[node](https://prosemirror.xheldon.com/docs/ref/#model.Node)﻿ 类型, 它含有一个 ﻿[fragment](https://prosemirror.xheldon.com/docs/ref/#model.Fragment)﻿ 对象, fragment 对象又包含了 0 个或更多子 node.

![](/img/user/programming/font-end/field/editor/prose-mirror-basic/image-20230416211930917.png)

## 不可变数据

ProseMirror document 和 DOM 树不同，它被设计成 **immutable**，不可变的，nodes 仅仅是 values，它不跟当前数据结构绑定。这意味着每次你更新 document, 你就会得到一个新的 document。大多数情况下, 你需要使用 ﻿[transform](https://prosemirror.xheldon.com/docs/guide/#transform)﻿ 去更新 document 而不用直接修改 nodes。

**优点**：state 更新的时候编辑器始终可用，因为新的 state 就代表了新的 document， 新旧状态可以瞬间切换，这种机制使得协同编辑成为可能，新旧虚拟 dom 也可以通过 diff 算法实现高效的更新﻿[update](https://prosemirror.xheldon.com/docs/ref/#view.EditorView.update)﻿ DOM。

## Schema 骨架 / 文档约束

每个 Prosemirror ﻿[document](https://prosemirror.xheldon.com/docs/guide/#doc)﻿ 都有一个与之相关的 ﻿[schema](https://prosemirror.xheldon.com/docs/ref/#model.Schema)﻿. 这个 schema 描述了存在于 document 中的 nodes 类型, 和 nodes 们的嵌套关系. **schema** 是骨架模版，nodes 是不同类型的积木，通过组合搭积木的方式完成编辑器的组装。

```js
const schema = new Schema﻿(﻿{
  nodes﻿: {
    doc﻿: {﻿content﻿: "block+"﻿}﻿,
    paragraph﻿: {﻿group﻿: "block"﻿, content﻿: "text*"﻿, marks﻿: "_"﻿}﻿, // 允许所有marks
    heading﻿: {﻿group﻿: "block"﻿, content﻿: "text*"﻿, marks﻿: ""﻿}﻿, // 0个多个 不允许使用marks
    blockquote﻿: {﻿group﻿: "block"﻿, content﻿: "block+"﻿}﻿, // 1个多个
    text﻿: {﻿inline﻿: true﻿}
  }﻿,
  marks﻿: {
    strong﻿: {﻿}﻿,
    em﻿: {﻿}
  }
}﻿)
// 传入state
let state = EditorState.﻿create﻿(﻿{schema}﻿)
```

- 内容表达式 content expressions
- 标记 Marks
- 序列化与解析 Serialization and Parsing node spec 中指定 ﻿[toDOM](https://prosemirror.xheldon.com/docs/ref/#model.NodeSpec.toDOM)﻿ 和 ﻿[parseDOM](https://prosemirror.xheldon.com/docs/ref/#model.NodeSpec.parseDOM)﻿实现解析

## State 状态层

```ts
import {schema} from "prosemirror-schema-basic"
import {EditorState} from "prosemirror-state"
﻿
let state = EditorState.﻿create﻿(﻿{schema}﻿)
console.﻿log﻿(state.doc.﻿toString﻿(﻿)﻿) // An empty paragraph
console.﻿log﻿(state.selection.from) // 1, the start of the paragraph
```

- [doc](https://prosemirror.xheldon.com/docs/ref/#state.EditorState.doc)﻿ 当前文档
- [selection](https://prosemirror.xheldon.com/docs/ref/#state.EditorState.selection)﻿ 当前选区。

## Transactions

```ts
let tr = state.tr
console.﻿log﻿(tr.doc.content.size) // 25
tr.﻿insertText﻿(﻿"hello"﻿) // Replaces selection with 'hello'
let newState = state﻿.﻿apply﻿(tr)
console.﻿log﻿(tr.doc.content.size) // 30
```

## Transform 事务层

﻿﻿[Transform](https://prosemirror.xheldon.com/docs/ref/#transform.Transform)﻿ 系统是 Prosemirror 的核心工作方式. 它是 ﻿[transactions](https://prosemirror.xheldon.com/docs/guide/#state.transactions)﻿ 的基础, 其使得编辑历史跟踪和协同编辑成为可能。

1. 配合 Immutable 数据结构 可以使代码的保持清晰
2. transform 系统可以保留 document 更新的痕迹，便于实现 undo history 这种历史记录
3. 为了实现协同编辑

### Steps 原子操作

一个编辑行为可能会产生一个或者多个 steps。例如: ﻿[ReplaceStep](https://prosemirror.xheldon.com/docs/ref/#transform.ReplaceStep)﻿、 ﻿[AddMarkStep](https://prosemirror.xheldon.com/docs/ref/#transform.AddMarkStep)﻿。

```ts
console.﻿log﻿(myDoc.﻿toString﻿(﻿)﻿) // → p("hello")
// 删除了 position 在 3-5 的 setp
let step = new ReplaceStep﻿(﻿3﻿, 5﻿, Slice.empty)
let result = step﻿.﻿apply﻿(myDoc)
console.﻿log﻿(result.doc.﻿toString﻿(﻿)﻿) // → p("heo")
```

### Transforms

Transforms = (Steps+) 一个编辑行为可能会产生一个或者多个 steps。支持链式调用

常见的方法 ﻿[deleteing](https://prosemirror.xheldon.com/docs/ref/#transform.Transform.delete)﻿ 和 ﻿[replaceing](https://prosemirror.xheldon.com/docs/ref/#transform.Transform.replace)﻿, ﻿[adding](https://prosemirror.xheldon.com/docs/ref/#transform.Transform.addMark)﻿ 和 ﻿[removeing marks](https://prosemirror.xheldon.com/docs/ref/#transform.Transform.removeMark)﻿, 操作树数据结构的方法如 ﻿[splitting](https://prosemirror.xheldon.com/docs/ref/#transform.Transform.split)﻿, ﻿[joining](https://prosemirror.xheldon.com/docs/ref/#transform.Transform.join)﻿, ﻿[lifting](https://prosemirror.xheldon.com/docs/ref/#transform.Transform.lift)﻿, 和 ﻿[wrapping](https://prosemirror.xheldon.com/docs/ref/#transform.Transform.wrap)﻿等

```ts
let tr = new Transform﻿(myDoc)
tr.﻿delete﻿(﻿5﻿, 7﻿) // Delete between position 5 and 7
tr.﻿split﻿(﻿5﻿)     // Split the parent node at position 5
console.﻿log﻿(tr.doc.﻿toString﻿(﻿)﻿) // The modified document
console.﻿log﻿(tr.steps.length)   // → 2
```

## View 视图层

Prosemirror 的 ﻿[editor view](https://prosemirror.xheldon.com/docs/ref/#view.EditorView)﻿ 是一个用户界面的 component, 它展示 ﻿[editor state](https://prosemirror.xheldon.com/docs/guide/#state)﻿ 给用户, 同时允许用户对其执行编辑操作。

### Editable DOM

- 基于浏览器 contenteditable

### 数据流

![](/img/user/programming/font-end/field/editor/prose-mirror-basic/image-20230416212554780.png)

- 保证 DOM Selection 和 editor state 的 selection 一致性
- 大部分事件比如光标移动、鼠标事件、输入事件都交给浏览器处理，浏览器处理完后，Prosemirror 检测当前 DOM 或者 selection 的变化，然后把这些变化部分转化为 transaction

### Dispatch

派发一个 transaction。会调用 ﻿[dispatchTransaction](https://prosemirror.xheldon.com/docs/ref/#view.DirectEditorProps.dispatchTransaction)﻿ (如果设置了)，否则默认应用该 transaction 到当前 state， 然后将其结果作为参数，传入 ﻿[updateState](https://prosemirror.xheldon.com/docs/ref/#view.EditorView.updateState)﻿ 方法，类似 redux。

```ts
// The app's state
let appState = {
  editor﻿: EditorState.﻿create﻿(﻿{schema}﻿)﻿,
  score﻿: 0
}
let view = new EditorView﻿(document.body, {
  state﻿: appState.editor,
  dispatchTransaction﻿(﻿transaction﻿) {
    update﻿(﻿{﻿type﻿: "EDITOR_TRANSACTION"﻿, transaction}﻿)
  }
}﻿)
﻿
// A crude app state update function, which takes an update object,
// updates the `appState`, and then refreshes the UI.
function update﻿(﻿event﻿) {
  if (event.type == "EDITOR_TRANSACTION"﻿)
    appState.editor = appState.﻿editor﻿.﻿apply﻿(event.transaction)
  else if (event.type == "SCORE_POINT"﻿)
    appState.score++
  draw﻿(﻿)
}
// An even cruder drawing function
function draw﻿(﻿) {
  document.﻿querySelector﻿(﻿"#score"﻿)﻿.textContent = appState.score
  view.﻿updateState﻿(appState.editor)
}
```

### Efficient Updating

Prosemirror 内部有一些高效的更新策略。比如 diff 新旧虚拟 dom 只更新变化部分、比如更新输入的文本，这些文本通过 dom 的方式被修改，Prosemirror 监听 DOM change 事件, 然后由此触发 transaction 将 DOM 的输入变化同步过来, 不需要再修改 DOM 等。

### Decorations

Decorations 给了你绘制你的 document view 方面的一些能力

- [Node decorations](https://prosemirror.xheldon.com/docs/ref/#view.Decoration%5Enode)﻿ 增加样式或者其他 DOM 属性到单个 node 的 DOM 上去.
- [Widget decorations](https://prosemirror.xheldon.com/docs/ref/#view.Decoration%5Ewidget)﻿ 在给定位置插入一个 DOM node, 其不是实际文档的一部分
- [Inline decorations](https://prosemirror.xheldon.com/docs/ref/#view.Decoration%5Einline)﻿ 在给定的 range 中的行内元素增加样式或者属性, 和 node decoration 类似, 不过只针对行内元素.

```ts
let purplePlugin = new Plugin﻿(﻿{
  props﻿: {
    decorations﻿(﻿state﻿) {
      return DecorationSet.﻿create﻿(state.doc, [
        Decoration.﻿inline﻿(﻿0﻿, state.doc.content.size, {﻿style﻿: "color: purple"﻿}﻿)
      ]﻿)
    }
  }
}﻿)
```

### Node Views

一系列小型且独立的 node 的 UI component

```ts
let view = new EditorView﻿(﻿{
  state,
  nodeViews﻿: {
    image﻿(﻿node﻿) { return new ImageView﻿(node) }
  }
}﻿)
class ImageView {
  constructor﻿(﻿node﻿) {
    // The editor will use this as the node's DOM representation
    this﻿.dom = document.﻿createElement﻿(﻿"img"﻿)
    this﻿.dom.src = node.attrs.src
    this﻿.dom.﻿addEventListener﻿(﻿"click"﻿, e => {
      console.﻿log﻿(﻿"You clicked me!"﻿)
      e.﻿preventDefault﻿(﻿)
    }﻿)
  }
  stopEvent﻿(﻿) { return true }
}
```

## Commands

﻿﻿[prosemirror-commands](https://prosemirror.xheldon.com/docs/ref/#commands)﻿ 模块提供了大量的编辑 commands，可以让用户通过按一些联合按键来执行操作或者菜单交互行为，通过工具函数函数 ﻿[chainCommands](https://prosemirror.xheldon.com/docs/ref/#commands.chainCommands)﻿ 可以实现命令的组合，除此外也支持用户自定义 command 函数。

```ts

/// Delete the selection, if there is one.
export const deleteSelection﻿: Command = (﻿state, dispatch﻿) => {
  if (state.selection.empty) return false
  if (dispatch) dispatch﻿(state.tr.﻿deleteSelection﻿(﻿)﻿.﻿scrollIntoView﻿(﻿)﻿)
  return true
}
```

## Dev Tools

[ProseMirror dev tools extension](https://chrome.google.com/webstore/detail/prosemirror-developer-too/gkgbmhfgcpfnogoeclbaiencdjkefonj/related)
