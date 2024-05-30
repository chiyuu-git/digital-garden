---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-04-16-Sun, 9:13:14 pm","date-modified":"2024-05-07-Tue, 5:00:17 pm","permalink":"/programming/front-end/field/editor/prose-mirror-basic/","dgPassFrontmatter":true}
---


[ProseMirror Guide](https://prosemirror.net/docs/guide/)

[「译」 ProseMirror 中文指南 - Xheldon Blog](https://www.xheldon.com/tech/prosemirror-guide-chinese.html)

[ProseMirror Docs](https://prosemirror.xheldon.com/docs/)

[探索Prosemirror的插件系统：解锁编辑器的无限可能性 - 掘金](https://juejin.cn/post/7291485045080178700)

# 介绍

作者 **Marijn** 是 `codemirror` 编辑器和 `acorn` 解释器的作者，前者已经在 `Chrome` 和 `Firefox` 自带的调试工具里使用了，后者则是 `babel` 的依赖，他还撰写了一本广受欢迎的 JavaScript 编程入门书籍《Eloquent JavaScript》

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20230416211402236.png)

﻿

ProseMirror 依赖 contentEditable，不过非常厉害的是 ProseMirror 将主流的前端的架构理念应用到了编辑器的开发中，比如彻底使用纯 JSON 数据描述富文本内容，引入不可变数据以及 Virtual DOM 的概念，还有插件机制、分层、Schemas（范式）等等。

## 特点

- 模块化设计
- 实时协作编辑
- 拼写检查和样式格式化
- 内置功能丰富 (撤销/重做、拖拽/复制/粘贴、快捷键等)
- 注重性能 (高效更新)

ProseMirror 是一个不同以往的富文本编辑器，虽然同样基于浏览器的 contenteditable 属性提供富文本编辑的视图，但 ProseMirror 的文档不是浏览器产生的 HTML

ProseMirror 抽象出一个类似（但不同于）DOM 的分层节点数据结构，既支持基于树的递归查询，又支持扁平化索引定位，用来存储清晰、无歧义且语义丰富的文档

ProseMirror 让开发者完全控制文档内容和编辑行为，通过 Schema 可以扩展内容类型，通过状态事务集中处理对编辑器内容的修改

ProseMirror 文档是不可变数据结构，每次更新都会产生新事务、创建新文档，在对新旧版本文档进行比较后再对 DOM 进行差异更新，因此性能非常高

ProseMirror 原生支持协同编辑，提供了高度灵活和可扩展的插件架构

## 核心模块

- **prosemirror-model 模型层**: 定义编辑器的文档模型，用来描述编辑器内容的数据结构
- **prosemirror-state 状态层**: 描述编辑器整体状态，包括文档数据、选区, 事务等
- **prosemirror-view 视图层**: 用于将编辑器状态展现为可编辑的元素，处理用户交互
- **prosemirror-transform 事务层**: 通过事务的方式修改文档，并支持修改记录、回放、重排等

## 其他模块

- prosemirror-commands 基本编辑命令
- prosemirror-keymap 键绑定
- prosemirror-history 历史记录
- prosemirror-inputrules 输入宏
- prosemirror-collab 协作编辑
- prosemirror-schema-basic 简单文档模式
- prosemirror-gapcursor 可聚焦于非常规选区的光标

## 新手礼包

+ prosemirror-schema-basic：提供基础 schema，包括块节点、块标记等。
+ prosemirror-schema-list：提供 HTML 列表的 schema，包括列表节点、列表标记等。
+ prosemirror-example-setup：提供一个基本的编辑器，包括菜单、快捷键等。

# 基本使用

[Prosemirror的基本使用](https://www.yangfuzhang.online/prosemirror/basic-usage)

# proseMirror React

[GitHub - nytimes/react-prosemirror: A library for safely integrating ProseMirror and React.](https://github.com/nytimes/react-prosemirror)

[The Future of @nytimes/react-prosemirror - discuss.ProseMirror](https://discuss.prosemirror.net/t/the-future-of-nytimes-react-prosemirror/5855)

[Announcing React ProseMirror - Show - discuss.ProseMirror](https://discuss.prosemirror.net/t/announcing-react-prosemirror/5328)

## Rendering ProseMirror Views Within React

该库提供了一组 React context 和用于 consuming 它们的 hooks，以确保从 React 组件安全访问 EditorView。这使我们能够构建包含 ProseMirror 视图的 React 应用，即使 EditorState 被提升到 React 状态或像 Redux 这样的全局状态管理系统中。

利用这些上下文的最简单方式是使用 `<ProseMirror/>` 组件。`<ProseMirror/>` 组件可以是受控的或非受控的，并且接受一个 "mount" 属性，用于指定 ProseMirror EditorView 应该挂载在哪个 DOM 节点上。

```jsx
import { EditorState } from "prosemirror-state";
import { schema } from "prosemirror-schema-basic";
import { ProseMirror } from "@nytimes/react-prosemirror";

const defaultState = EditorState.create({ schema });
export function ProseMirrorEditor() {
  // It's important that mount is stored as state,
  // rather than a ref, so that the ProseMirror component
  // is re-rendered when it's set
  const [mount, setMount] = useState<HTMLElement | null>(null);

  return (
    <ProseMirror mount={mount} defaultState={defaultState}>
      <div ref={setMount} />
    </ProseMirror>
  );
}
```

The EditorState can also easily be lifted out of the ProseMirror component and passed as a prop.

```jsx
import { EditorState } from "prosemirror-state";
import { schema } from "prosemirror-schema-basic";
import { ProseMirror } from "@nytimes/react-prosemirror";

export function ProseMirrorEditor() {
  const [mount, setMount] = useState<HTMLElement | null>(null);
  const [state, setState] = useState(EditorState.create({ schema }));

  return (
    <ProseMirror
      mount={mount}
      state={state}
      dispatchTransaction={(tr) => {
        setState((s) => s.apply(tr));
      }}
    >
      <div ref={setMount} />
    </ProseMirror>
  );
}
```

ProseMirror 组件将确保在每个渲染周期之后，EditorView 始终使用最新的 EditorState 进行更新。因为同步 EditorView 是一个副作用，它必须发生在 React 渲染生命周期的效果阶段，即在所有 ProseMirror 组件的子组件运行其渲染函数之后。

这意味着必须特别小心地从其他 React 组件中访问 EditorView。为了抽象出这种复杂性，React ProseMirror 提供了两个 hooks：useEditorEffect 和 useEditorEventCallback。这两个 hooks 都可以从 ProseMirror 组件的任何子组件中使用。

## useEditorEffect

经常需要相对于 ProseMirror 文档中的特定位置定位 React 组件。例如，您可能有一些小部件需要定位在用户的光标处。为了确保在 EditorView 与最新的 EditorState 同步时进行定位，我们可以使用 useEditorEffect。

```jsx
// SelectionWidget.tsx
import { useRef } from "react"
import { useEditorEffect } from "@nytimes/react-prosemirror"

export function SelectionWidget() {
  const ref = useRef()

  useEditorEffect((view) => {
    if (!ref.current) return

    const viewClientRect = view.dom.getBoundingClientRect()
    const coords = view.coordsAtPos(view.state.selection.anchor))

    ref.current.style.top = coords.top - viewClientRect.top;
    ref.current.style.left = coords.left - viewClientRect.left;
  })

  return (
    <div
      ref={ref}
      style={{
        position: "absolute"
      }}
    />
  )
}

// ProseMirrorEditor.tsx
import { EditorState } from "prosemirror-state";
import { schema } from "prosemirror-schema-basic";

import { SelectionWidget } from "./SelectionWidget.tsx";

export function ProseMirrorEditor() {
  const [mount, setMount] = useState<HTMLElement | null>(null);
  const [state, setState] = useState(EditorState.create({ schema }))

  return (
    <ProseMirror
      mount={mount}
      state={state}
      dispatchTransaction={(tr) => {
        setState(s => s.apply(tr))
      }}
    >
      {/*
        We have to mount all components that need to access the
        EditorView as children of the ProseMirror component
      */}
      <SelectionWidget />
      <div ref={setMount} />
    </ProseMirror>
  )
}
```

## useEditorEventCallback

经常需要根据用户操作（如鼠标点击和键盘事件）来触发事务的分发或执行副作用。注意：如果您需要从 contenteditable 元素内部响应键盘事件，则应改为使用 useEditorEventListener。

然而，如果您需要根据从 React 组件中分发的某些事件（如工具提示或工具栏按钮）来分发事务，您可以使用 useEditorEventCallback 来创建一个稳定的函数引用，该引用可以安全地访问 EditorView 的最新值。

```jsx
// BoldButton.tsx
import { toggleMark } from "prosemirror-commands";
import { useEditorEventCallback } from "@nytimes/react-prosemirror";

export function BoldButton() {
  const onClick = useEditorEventCallback((view) => {
    const toggleBoldMark = toggleMark(view.state.schema.marks.bold);
    toggleBoldMark(view.state, view.dispatch, view);
  });

  return <button onClick={onClick}>Bold</button>;
}

// ProseMirrorEditor.tsx
import { EditorState } from "prosemirror-state";
import { schema } from "prosemirror-schema-basic";

import { BoldButton } from "./BoldButton.tsx";

export function ProseMirrorEditor() {
  const [mount, setMount] = useState<HTMLElement | null>(null);
  const [state, setState] = useState(EditorState.create({ schema }));

  return (
    <ProseMirror
      mount={mount}
      state={state}
      dispatchTransaction={(tr) => {
        setEditorState((s) => s.apply(tr));
      }}
    >
      {/*
        We have to mount all components that need to access the
        EditorView as children of the ProseMirror component
      */}
      <BoldButton />
      <div ref={setMount} />
    </ProseMirror>
  );
}
```

## `useEditorEventListener`

useEditorEventCallback 生成的函数可以作为事件处理程序传递给 React 组件。然而，如果您需要监听源自 contenteditable 节点内部的事件，则这些事件监听器需要使用 EditorView 的 handleDOMEvents 属性进行注册。

您可以使用 useEditorEventListener hook 来实现这一点。它接受一个 eventType 和一个事件监听器。事件监听器遵循 ProseMirror 的 handleDOMEvents 属性的通常语义：

- 返回 true 或调用 event.preventDefault 将阻止其他监听器运行。
- 返回 true 不会自动调用 event.preventDefault；如果要阻止默认的 contenteditable 行为，必须调用 event.preventDefault。 您可以使用此 hook 在 NodeViews 中实现自定义行为：

```jsx
import { useEditorEventListener } from "@nytimes/react-prosemirror";

function Paragraph({ node, children }) {
  const nodeStart = useNodePos();

  useEditorEventListener("keydown", (view, event) => {
    if (event.code !== "ArrowDown") {
      return false;
    }
    const nodeEnd = nodeStart + node.nodeSize;
    const { selection } = view.state;
    if (selection.anchor < nodeStart || selection.anchor > nodeEnd) {
      return false;
    }
    event.preventDefault();
    alert("No down keys allowed!");
    return true;
  });

  return <p>{children}</p>;
}
```

## Building nodeViews with React

集成 React 和 ProseMirror 的另一种方式是使用 React 组件让 ProseMirror 渲染 NodeViews。这比前面的部分略微复杂一些。该库提供了一个 useNodeViews hook，一个用于将 NodeView 构造函数与 React 组件增强的工厂，以及一个 react，用于维护 React 组件层次结构的 ProseMirror 插件。

useNodeViews 接受一个从节点名称到扩展的 NodeView 构造函数的映射。NodeView 构造函数必须至少返回一个 dom 属性和一个 component 属性，但也可以返回任何其他 NodeView 属性。下面是一个使用示例：

```jsx
import {
  useNodeViews,
  useEditorEventCallback,
  NodeViewComponentProps,
  react,
} from "@nytimes/react-prosemirror";
import { EditorState } from "prosemirror-state";
import { schema } from "prosemirror-schema-basic";

// Paragraph is more or less a normal React component, taking and rendering
// its children. The actual children will be constructed by ProseMirror and
// passed in here. Take a look at the NodeViewComponentProps type to
// see what other props will be passed to NodeView components.
function Paragraph({ children }: NodeViewComponentProps) {
  const onClick = useEditorEventCallback((view) => view.dispatch(whatever));
  return <p onClick={onClick}>{children}</p>;
}

// Make sure that your ReactNodeViews are defined outside of
// your component, or are properly memoized. ProseMirror will
// teardown and rebuild all NodeViews if the nodeView prop is
// updated, leading to unbounded recursion if this object doesn't
// have a stable reference.
const reactNodeViews = {
  paragraph: () => ({
    component: Paragraph,
    // We render the Paragraph component itself into a div element
    dom: document.createElement("div"),
    // We render the paragraph node's ProseMirror contents into
    // a span, which will be passed as children to the Paragraph
    // component.
    contentDOM: document.createElement("span"),
  }),
};

const state = EditorState.create({
  schema,
  // You must add the react plugin if you use
  // the useNodeViews or useNodePos hook.
  plugins: [react()],
});

function ProseMirrorEditor() {
  const { nodeViews, renderNodeViews } = useNodeViews(reactNodeViews);
  const [mount, setMount] = useState<HTMLElement | null>(null);

  return (
    <ProseMirror mount={mount} nodeViews={nodeViews} defaultState={state}>
      <div ref={setMount} />
      {renderNodeViews()}
    </ProseMirror>
  );
}
```

# Concept in proseMirror

![](prose-mirror-basic/proseMirror%20实现原理.pdf)

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20230416212929283.png)

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

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20230416211754209.png)

Prosemirror 中, 内联元素被表示成一个 **扁平** 的模型, 他们的节点标记被作为 metadata 信息附加到相应 node 上

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20230416211823640.png)

**好处**：可以使用 **字符串偏移量** 而不是 **树节点路径** 来表示在段落中的位置、spliting 内容、改变内容 style 操作变的容易 (树操作 ->数组下标)

Prosemirror document 就是一颗 block nodes 的树, 它的大多数 leaf nodes 是 textblock 类型, 该节点是包含 text 的 block nodes。Node 对象有一系列属性来表示它们在文档中的行为，如 _isBlock、isInline、inlineContent、isTextBlock、isLeaf_ 等。

**缺点**：开发者重新学习它独有的描述 DOM 的范式。

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

[prose-mirror-schema](prose-mirror-schema.md)

## Document in proseMirror

Prosemirror 定义了它自己的﻿[数据结构](https://prosemirror.xheldon.com/docs/ref/#model.Node)﻿来表示 document 内容. 因为 document 是构建一个编辑器的核心元素, 因此理解 document 是如何工作的很有必要. document 是一个 ﻿[node](https://prosemirror.xheldon.com/docs/ref/#model.Node)﻿ 类型, 它含有一个 ﻿[fragment](https://prosemirror.xheldon.com/docs/ref/#model.Fragment)﻿ 对象, fragment 对象又包含了 0 个或更多子 node.

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20230416211930917.png)

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429111708699.png)

### 创建文档

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429111828372.png)

### 索引、位置与偏移量

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429111958615.png)

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429112041113.png)

### nodeSize

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429112139512.png)

### 文档切片

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429112214389.png)

## 不可变数据

ProseMirror document 和 DOM 树不同，它被设计成 **immutable**，不可变的，nodes 仅仅是 values，它不跟当前数据结构绑定。这意味着每次你更新 document, 你就会得到一个新的 document。大多数情况下, 你需要使用 ﻿[transform](https://prosemirror.xheldon.com/docs/guide/#transform)﻿ 去更新 document 而不用直接修改 nodes。

**优点**：state 更新的时候编辑器始终可用，因为新的 state 就代表了新的 document， 新旧状态可以瞬间切换，这种机制使得协同编辑成为可能，新旧虚拟 dom 也可以通过 diff 算法实现高效的更新﻿[update](https://prosemirror.xheldon.com/docs/ref/#view.EditorView.update)﻿ DOM。

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

### Doc

状态的 doc 属性保存文档, 一个只读的分层节点数据结构, 类似 (但不同于) DOM 树, 最简单的文档可能就是 doc 节点包含两个 paragraph 节点, 后者各自包含一个 text 节点

创建状态时可以提供一个除湿文档. 此时 schema 字段是可选的, 因为可以从文档中获取. 其实就是 schema 传给了 DOMPaser

```ts
import {DOMParser} from 'prosemirror-model';

const content = document.querySelector('#content');
const state = EditorState.create({
  doc: DOMParse.fromSchema(schema).parse(content);
})
```

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

### 基础事务

默认请夸下, 上述过程都是在后台隐式发生的, 但是通过编写插件或者配置视图, 可以访问到这些事务.

```ts
import { schema } from '@tiptap/pm/schema-basic';
import { EditorState } from '@tiptap/pm/state';
import { EditorView } from '@tiptap/pm/view';

const state = EditorState.create({ schema });
const view = new EditorView(document.body, {
  state,
  dispatchTransaction(tr) {
    console.log('文档长度: ', tr.before.content.size, tr.doc.content.size);
    const newState = view.state.apply(tr);
    view.updateState(newState);
  },
});
```

## View 视图层

Prosemirror 的 ﻿[editor view](https://prosemirror.xheldon.com/docs/ref/#view.EditorView)﻿ 是一个用户界面的 component, 它展示 ﻿[editor state](https://prosemirror.xheldon.com/docs/guide/#state)﻿ 给用户, 同时允许用户对其执行编辑操作。

### Editable DOM

- 基于浏览器 contenteditable

### 数据流

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20230416212554780.png)

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

# EditorProps

编辑器可接受的属性, 控制整个编辑器的行为. 比如这次遇到的复制需求.

# 文档变换

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429113704902.png)

## 步骤

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429113722387.png)

## 变换

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429113839593.png)

## 位置映射

### Step 的 Map

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429114238473.png)

### Tr 的 Mapping

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429114700949.png)

### Rebase

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429114849602.png)

和代码的 rebase 差不多

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429115025019.png)

# 编辑器状态

## 编辑器状态中包含什么

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429115411353.png)

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429115523441.png)

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429115607920.png)

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429141714691.png)

## 选区

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429142331332.png)

## Plugin

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429142819374.png)

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429142945760.png)

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429143136699.png)

# 视图组件

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429143306705.png)

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429143427931.png)

## 视图属性

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429143606153.png)

## 视图装饰

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429144651478.png)

高亮, 划词评论

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429144935261.png)

## nodeView

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429145102357.png)

img 自闭合节点就是没有 contentDOM 的

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429145318362.png)

# 命令

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429145550138.png)

## 编辑器视图

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429145749203.png)

## 命令生成函数

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429145836295.png)

## 应用命令

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429145939270.png)

# 协同编辑

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429151749787.png)

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429152207455.png)

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429153433338.png)

![](/img/user/programming/front-end/field/editor/prose-mirror-basic/image-20240429153503874.png)
