---
{"dg-publish":true,"permalink":"/programming/font-end/field/editor/tiptap-faq/"}
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
