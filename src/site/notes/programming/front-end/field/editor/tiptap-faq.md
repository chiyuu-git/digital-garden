---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-03-16-Thu, 3:33:15 pm","date-modified":"2024-05-08-Wed, 5:01:34 pm","permalink":"/programming/front-end/field/editor/tiptap-faq/","dgPassFrontmatter":true}
---


# Must Have Extension

```npm
pnpm add @tiptap/extension-bold @tiptap/extension-bullet-list @tiptap/extension-dropcursor @tiptap/extension-hard-break @tiptap/extension-horizontal-rule @tiptap/extension-list-item @tiptap/extension-ordered-list @tiptap/extension-document @tiptap/extension-gapcursor @tiptap/extension-text @tiptap/extension-history @tiptap/extension-font-family @tiptap/extension-text-style @tiptap/extension-italic @tiptap/extension-underline @tiptap/extension-strike @tiptap/extension-color @tiptap/extension-highlight @tiptap/extension-text-align @tiptap/extension-heading

pnpm i prosemirror-model prosemirror-view prosemirror-state
```

先无脑 pnpm add 再执行依赖清除就好

# 在当前 Node 的上方或者下方插入内容

```ts
        // 不加 nodeSize 在上方插入, 加了就在下方插入
        editor.chain().insertContentAt(isPrev ? getPos() : endPos, {type: 'paragraph'}).run();
```

# getNode on Click

[how do get the current node on click? · Discussion #2826 · ueberdosis/tiptap · GitHub](https://github.com/ueberdosis/tiptap/discussions/2826)

# Wrapper As Block Inline Content

与 parseHTML 和 renderHTML, 以及实际的布局都抢相关, 毕竟一个 inline 的 wrapper 不可能再嵌套 block, 而一个 block wrapper 也不能插入到 inline 中.

看下 mention extension 是怎么做到的

在 vanilla js 的写法当中, 是没有 wrapper 的存在的, 为什么组件里面需要包在 wrapper 里呢?

![tiptap-advanced](tiptap-advanced.md#^3n3ix9)

![tiptap-advanced](tiptap-advanced.md#^pyo1t4)

![tiptap-advanced](tiptap-advanced.md#^hajt6t)

# Extension Editable Question

if the editor is readonly, how to set all the extension, especially the nodeView extension, not able to interact?

I don't think is a question now. In vanilla JS, you must control editable yourself

according to the section, editable is normal case, check why

I think it is because image, editable just control in your component

figure it out the rule of editable or not

# Node-view-content

类比组件的 slot, 用于把标签体的内容插入和占位的概念, 还有就是是否可编辑

# parseHTML or parseHTML Str

![tiptap-advanced](tiptap-advanced.md#Are%20element%20rendered)

# 富文本编辑器中是如何解决 XSS 攻击的

![network-security](programming/basic/cs-basic/network-security.md#富文本编辑器中是如何解决%20XSS%20攻击的)

# 如何控制 Clipboard 的内容

prosemirror 已经帮忙拦截了默认的复制行为. 现在的复制行为是 proseMirror 代理了一层了. 控制的地方在 editorProps

+ handlePaste
+ transformPastedHTML
+ clipboardParser
+ transformPastedText
+ clipboardTextParser
+ transformPasted
+ **transformCopied**
+ clipboardSerializer
+ **clipboardTextSerializer**

目前来看只能整个 editor 控制. 没办法控制单个 nodeView 吗?

[Modify specific node on copy and paste (in clipboard) - discuss.ProseMirror](https://discuss.prosemirror.net/t/modify-specific-node-on-copy-and-paste-in-clipboard/4901/4)

[Copy NodeSelected nodes - discuss.ProseMirror](https://discuss.prosemirror.net/t/copy-nodeselected-nodes/1513)

默认进剪贴板的是 renderHTML 走 text/html, 但是 text 却不是 renderText 可以直接影响的. 是拼接逻辑

## 按钮复制的正确姿势

现在我复制 customLink 是通过在 link component 埋了一个看不见的 a 标签. 这样直接复制 innerHTML 过去就行

但是合理的做法应该是:

1. 解析 node view, node-view 带上 attr 做数据
2. 通过 editorView slice 获取 content, 然后 getHTML from content?

## 如何影响编辑器外的复制行为?
