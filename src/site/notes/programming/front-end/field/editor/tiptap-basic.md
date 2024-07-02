---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-11-04-Fri, 11:09:37 am","date-modified":"2023-04-29-Sat, 8:16:40 pm","permalink":"/programming/front-end/field/editor/tiptap-basic/","dgPassFrontmatter":true}
---


# 概述

始于 2019

[ueberdosis/tiptap: The headless editor framework for web artisans. (github.com)](https://github.com/ueberdosis/tiptap)

[@tiptap/core - npm (npmjs.com)](https://www.npmjs.com/package/@tiptap/core)

tiptap is a headless wrapper around [ProseMirror](https://prosemirror.net/) – a toolkit for building rich text WYSIWYG editors, which is already in use at many well-known companies such as _New York Times_, _The Guardian_ or _Atlassian_.

Create exactly the rich text editor you want out of customizable building blocks. Tiptap comes with sensible defaults, a lot of extensions and a friendly API to customize every aspect. It’s backed by a welcoming community, open source, and free.

## Key Features

### Headless

It’s headless and comes without any CSS. You are in full control over markup, styling and behaviour.

### Collaborative

Real-time collaboration, syncing between different devices and working offline isn’t hard anymore. Keep everything in sync with the magic of [Y.js](https://github.com/yjs/yjs).

### Framework-agnostic

Out of the box, Tiptap works with Vanilla JavaScript and Vue.js, but it’s also possible to use it in React, Svelte and others.

## Use Technique

For most cases it’s enough to say where Tiptap should be rendered (`element`), what functionalities you want to enable (`extensions`) and what the initial document should be (`content`).

### Extension Resources

[Headless WYSIWYG Text Editor – Tiptap Editor](https://tiptap.dev/extensions)

find awesome-tiptap

## 优点

- 可定制的用户界面。
- 键盘快捷键。
- 移动支持。
- 社区活跃
- ts 支持友好
- output 有三种形式, json, html, Y.js
- 支持与 tailwind 搭配
- 第三方的扩展, 会支持 vanilla js 吗? 会不会第三方的扩展对视图层有依赖呢? 理论上是可以做到不依赖的, 得看 tiptap api 的实现

## 缺点

仍然是测试版（但很稳定，并有一个快速增长的开发者社区的支持）。

目前为止没有看到描述 DOM 结构的数据是怎么样的形式, 不过既然是基于 Prosemirror 那应该是差不多的?

# Configuration

For most cases it’s enough to say:

1. where Tiptap should be rendered (`element`)
2. what functionalities you want to enable (`extensions`)
3. what the initial document should be (`content`)

tiptap 最基础的配置只涉及一下三个方面:

1. 指定 tiptap 需要渲染到哪个元素内 (`element`)
2. 指定需要的编辑器功能 (`extensions`)
3. 制定编辑器的初始内容 (`content`)

## Configure the Editor

To add your configuration, pass [an object with settings](https://tiptap.dev/api/editor) to the `Editor` class, like shown here:

```ts
import { Editor } from '@tiptap/core'
import Document from '@tiptap/extension-document'
import Paragraph from '@tiptap/extension-paragraph'
import Text from '@tiptap/extension-text'

new Editor({
  element: document.querySelector('.element'),
  extensions: [
    Document,
    Paragraph,
    Text,
  ],
  content: '<p>Example Text</p>',
  autofocus: true,
  editable: true,
  injectCSS: false,
})
```

This will do the following:

1. bind Tiptap to `.element`,
2. load the `Document`, `Paragraph` and `Text` extensions,
3. set the initial content,
4. place the cursor in the editor after initialization,
5. make the text editable (but that’s the default anyway), and
6. disable the loading of [the default CSS](https://github.com/ueberdosis/tiptap/tree/main/packages/core/src/style.ts) (which is not much anyway).

上面的配置指定了这样一个编辑器:

1. tipta 会渲染到 .element 内
2. 具有 Document, Paragraph, Text 扩展提供的基本功能
3. 设置了初始内容为 Example content
4. 初始化后自动聚焦
5. 设置编辑器可以编辑 (默认配置)
6. 禁用了默认的 css (本身也只是极少一部分 [the default CSS](https://github.com/ueberdosis/tiptap/tree/main/packages/core/src/style.ts))

## Configure Extensions

Most editing features are bundled as [node](https://tiptap.dev/api/nodes), [mark](https://tiptap.dev/api/marks) or [extension](https://tiptap.dev/api/extensions). Import what you need and pass them as an array to the editor.

Most extensions can be configured. Add a `.configure()` and pass an object to it.

```js
import { Editor } from '@tiptap/core'
import Document from '@tiptap/extension-document'
import Paragraph from '@tiptap/extension-paragraph'
import Text from '@tiptap/extension-text'
import Heading from '@tiptap/extension-heading'

new Editor({
  element: document.querySelector('.element'),
  extensions: [
    Document,
    Paragraph,
    Text,
    Heading.configure({
      levels: [1, 2, 3],
    }),
  ],
})
```

Have a look at the documentation of the extension you are using to learn more about their settings.

# Content

[SetContent removes styles · Issue #495 · ueberdosis/tiptap · GitHub](https://github.com/ueberdosis/tiptap/issues/495)

not support, must be add as extension parseHTML?

[getHTML does not preserve styling · Issue #331 · ueberdosis/tiptap · GitHub](https://github.com/ueberdosis/tiptap/issues/331)

can be solve by global attribute, just defined common attribute

[Can't figure out how to render HTML with inline styles. · Issue #319 · ueberdosis/tiptap · GitHub](https://github.com/ueberdosis/tiptap/issues/319)

[Schema – Tiptap Editor](https://tiptap.dev/api/schema/#schema)

# get start with React

[React WYSIWYG – Tiptap](https://tiptap.dev/docs/editor/installation/react)
# Menu

The editor provides a fluent API to trigger commands and add active states. You can use any markup you like. To make the positioning of menus easier, we provide a few utilities and components. Let’s go through the most typical use cases one by one.

+ fixed menu
+ bubble menu
+ floating menu
+ slash commands

tiptap 提供了一系列流畅的 API 来触发编辑器命令 (command), 以及文本的活动状态 (active state)

## Basic Example

```html
<button onclick="editor.chain().focus().toggleBold().run()">
  Bold
</button>
```

Oh, that’s a long command, right? Actually, it’s a [chain of commands](https://tiptap.dev/api/commands#chain-commands). Let’s go through this one by one:

1. `editor` should be a Tiptap instance,
2. `chain()` is used to tell the editor you want to execute multiple commands,
3. `focus()` sets the focus back to the editor,
4. `toggleBold()` marks the selected text bold, or removes the bold mark from the text selection if it’s already applied and
5. `run()` will execute the chain.
1. `editor` 是一个 tiptap 实例
2. `chain()` 用于告诉编辑器, 你需要执行多个命令, 开启链式调用
3. `focus()` 重新聚焦编辑器 (因为菜单是 editor 实例外的节点, 会导致浏览器聚焦至该 button)
4. `toggleBold()` 执行 toggleBold 命令, 将所选的文本设置为 bold, 或者将已经是 bold 的文本设置为 normal
5. `run()`  执行链式调用的所有命令

Which commands are available depends on what extensions you have registered with the editor. Most extensions come with a `set…()`, `unset…()` and `toggle…()` command. Read the extension documentation to see what’s actually available or just surf through your code editor’s autocomplete.

## The Active State

The editor provides an `isActive()` method to check if something is applied to the selected text already.

tiptap 提供了 `isActive()` 方法用于检测所选中的文本是否激活了某些状态, 适用于 `nodes` 和 `marks`. 通过这个方法可以很便捷的实现工具栏的 active 状态切换.

```vue
<button :class="{ 'is-active': editor.isActive('bold') }" @click="editor.chain().focus().toggleBold().run()">
  Bold
</button>
```

This toggles the `.is-active` class accordingly and works for nodes and marks. You can even check for specific attributes. Here is an example with the [`Highlight`](https://tiptap.dev/api/marks/highlight) mark, that ignores different attributes:

```js
editor.isActive('highlight', { color: '#ffa8a8' })
```

There is even support for regular expressions:

```js
editor.isActive('textStyle', { color: /.*/ })
```

You can even nodes and marks, but check for the attributes only. Here is an example with the [`TextAlign`](https://tiptap.dev/api/extensions/text-align) extension:

```js
editor.isActive({ textAlign: 'right' })
```

If your selection spans multiple nodes or marks, or only part of the selection has a mark, `isActive()` will return `false` and indicate nothing is active. This is how it is supposed to be, because it allows people to apply a new node or mark to that selection right-away.

## getAtrributes

```ts
    /**
     * 处理按钮的激活态
     */
    handleActive() {
        this.activeHandler = () => {
            console.log('font-family', this.editor.getAttributes('textStyle'));
        };

        this.editor.on('selectionUpdate', this.activeHandler);
        this.editor.on('transaction', this.activeHandler);

        this.editor.on('destroy', () => {
            console.log('editor destroy');
            this.editor.off('selectionUpdate', this.activeHandler);
            this.editor.off('transaction', this.activeHandler);
        });
    },

```

## Accessibility

- Make sure users can navigate the menu with their keyboard
- Use proper [title attributes](https://developer.mozilla.org/de/docs/Web/HTML/Global_attributes/title)
- Use proper [aria attributes](https://developer.mozilla.org/en-US/docs/Learn/Accessibility/WAI-ARIA_basics)
- List available keyboard shortcuts

# Styling

tiptap is headless, that means there is no styling provided. That also means, you are in full control of how your editor looks. The following methods allow you to apply custom styles to the editor.

## Style the Plain HTML

The whole editor is rendered inside of a container with the class `.ProseMirror`. You can use that to scope your styling to the editor content:

```css
/* Scoped to the editor */
.ProseMirror p {
  margin: 1em 0;
}
```

If you’re rendering the stored content somewhere, there won’t be a `.ProseMirror` container, so you can just globally add styling to the used HTML tags:

```css
/* Global styling */
p {
  margin: 1em 0;
}
```

## Add Custom Classes

You can control the whole rendering, including adding classes to everything.

Most extensions allow you to add attributes to the rendered HTML through the `HTMLAttributes` option. You can use that to add a custom class (or any other attribute). That’s also very helpful, when you work with [Tailwind CSS](https://tailwindcss.com/).

```js
new Editor({
  extensions: [
    Document,
    Paragraph.configure({
      HTMLAttributes: {
        class: 'my-custom-paragraph',
      },
    }),
    Heading.configure({
      HTMLAttributes: {
        class: 'my-custom-heading',
      },
    }),
    Text,
  ],
})
```

The rendered HTML will look like that:

```js
<h1 class="my-custom-heading">Example Text</p>
<p class="my-custom-paragraph">Wow, that’s really custom.</p>
```

If there are already classes defined by the extensions, your classes will be added.

### Editor

You can even pass classes to the element which contains the editor like that:

The editor works fine with Tailwind CSS, too. Find an example that’s styled with the `@tailwindcss/typography` plugin below.

可以通过编辑器配置, 直接向 editor container element 传递 class 样式类名, 这于 tailwindcss 搭配使用起来非常方便

```js
new Editor({
  editorProps: {
    attributes: {
      class: 'prose prose-sm sm:prose lg:prose-lg xl:prose-2xl mx-auto focus:outline-none',
    },
  },
})
```

### With Tailwind CSS

just past the className by editor/extension config

### Customize the HTML

Or you can customize the markup for extensions. The following example will make a custom bold extension that doesn’t render a `<strong>` tag, but a `<b>` tag:

```js
import Bold from '@tiptap/extension-bold'

const CustomBold = Bold.extend({
  renderHTML({ HTMLAttributes }) {
    // Original:
    // return ['strong', HTMLAttributes, 0]
    return ['b', HTMLAttributes, 0]
  },
})

new Editor({
  extensions: [
    // …
    CustomBold,
  ],
})
```

You should put your custom extensions in separate files, but I think you got the idea.

# Output

You can store your content as a JSON object or as a good old HTML string. Both work fine. And of course, you can pass both formats to the editor to restore your content. Here is an interactive example, that exports the content as HTML and JSON when the document is changed:

## JSON

JSON is probably easier to loop through, for example to look for a mention and it’s more like what Tiptap uses under the hood. Anyway, if you want to use JSON to store the content we provide a method to retrieve the content as JSON:

```js
const json = editor.getJSON()
```

You can store that in your database (or send it to an API) and restore the document initially like that:

```js
new Editor({
  content: {
    "type": "doc",
    "content": [
      // …
    ]
  },
})
```

JSON is probably easier to loop through, for example to look for a mention and it’s more like what Tiptap uses under the hood. Anyway, if you want to use JSON to store the content we provide a method to retrieve the content as JSON:

```js
const json = editor.getJSON()
You can store that in your database (or send it to an API) and restore the document initially like that:

new Editor({
  content: {
    "type": "doc",
    "content": [
      // …
    ]
  },
})
```

Or if you need to wait for something, you can do it later through the editor instance:

```js
editor.commands.setContent({
  "type": "doc",
  "content": [
    // …
  ]
})
```

Or if you need to wait for something, you can do it later through the editor instance:

```js
editor.commands.setContent({
  "type": "doc",
  "content": [
    // …
  ]
})
```

## HTML

HTML can be easily rendered in other places, for example in emails and it’s wildly used, so it’s probably easier to switch the editor at some point. Anyway, every editor instance provides a method to get HTML from the current document:

```js
const html = editor.getHTML()
```

This can then be used to restore the document initially:

```js
new Editor({
  content: `<p>Example Text</p>`,
})
```

Or if you want to restore the content later (e. g. after an API call has finished), you can do that too:

```js
editor.commands.setContent(`<p>Example Text</p>`)
```

## Y.js

Our editor has top notch support for Y.js, which is amazing to add features like [realtime collaboration, offline editing, or syncing between devices](https://tiptap.dev/guide/collaborative-editing).

Internally, Y.js stores a history of all changes. That can be in the browser, on a server, synced with other connected clients, or on a USB stick. But, it’s important to know that Y.js needs those stored changes. A simple JSON document is not enough to merge changes.

Sure, you can import existing JSON documents to get started and get a JSON out of Y.js, but that’s more like an import/export format. It won’t be your single source. That’s important to consider when adding Y.js for one of the mentioned use cases.

That said, it’s amazing and we’re about to provide an amazing backend, that makes all that a breeze.

## Not an Option: Markdown

Unfortunately, **tiptap doesn’t support Markdown as an input or output format**. We considered to add support for it, but those are the reasons why we decided to not do it:

- Both, HTML and JSON, can have deeply nested structures, Markdown is flat.
- Markdown standards vary.
- Tiptap’s strength is customization, that doesn’t work very well with Markdown.
- There are enough packages to convert HTML to Markdown and vice-versa.

You should really consider to work with HTML or JSON to store your content, they are perfectly fine for most use cases.

If you still think you need Markdown, ProseMirror has an [example on how to deal with Markdown](https://prosemirror.net/examples/markdown/), [Nextcloud Text](https://github.com/nextcloud/text) uses Tiptap 1 to work with Markdown. Maybe you can learn from them. Or if you are looking for a really good Markdown editor, try [CodeMirror](https://codemirror.net/).

> so, this is why obsidian chose CodeMirror. Because it is the best markdown editor. markdown is a kind of code , not rich-text

That said, Tiptap does support [Markdown shortcuts](https://tiptap.dev/examples/markdown-shortcuts) to format your content. Also you’re free to let your content look like Markdown, for example add a `#` before an `<h1>` with CSS.

## Listening for Changes

If you want to continuously store the updated content while people write, you can [hook into events](https://tiptap.dev/api/events). Here is an example how that could look like:

```js
const editor = new Editor({
  // intial content
  content: `<p>Example Content</p>`,

  // triggered on every change
  onUpdate: ({ editor }) => {
    const json = editor.getJSON()
    // send the content to an API here
  },
})
```

# Rendering

## Read-only Instance of Tiptap

To render the saved content, set the editor to read-only. That’s how you can achieve the exact same rendering as it’s in the editor, without duplicating your CSS and other code.

## Generate Html From ProseMirror JSON

If you need to render the content on the server side, for example to generate the HTML for a blog post which has been written in Tiptap, you’ll probably want to do just that without an actual editor instance.

That’s what the `generateHTML()` is for. It’s a helper function which renders HTML **without an actual editor instance.**

By the way, the other way is possible, too. The below examples shows how to generate JSON from HTML.

> [!question]
> what does this section mean? I don't understand why we need generateHTML, what's the different between getHTML?
> 
> It there some node must be render under the tiptap eidtor? I don't think so.
> 
> may be just a optional choice for render HTML.

## Migration

If you’re migrating existing content to Tiptap we would recommend to get your existing output to HTML. That’s probably the best format to get your initial content into Tiptap, because ProseMirror ensures there is nothing wrong with it. Even if there are some tags or attributes that aren’t allowed (based on your configuration), Tiptap just throws them away quietly.

We’re about to go through a few cases to help with that, for example we provide a PHP package to convert HTML to a compatible JSON structure: [ueberdosis/prosemirror-to-html](https://github.com/ueberdosis/html-to-prosemirror).

[Share your experiences with us!](mailto:humans@tiptap.dev) We’d like to add more information here.

# Security

There is no reason to use one or the other because of security concerns. If someone wants to send malicious content to your server, it doesn’t matter if it’s JSON or HTML. It doesn’t even matter if you’re using Tiptap or not. You should always validate user input.

# Working with Typescript

[Working with TypeScript – Tiptap Editor](https://tiptap.dev/guide/typescript)

# Api Reference

[Working with TypeScript – Tiptap Editor](https://tiptap.dev/guide/typescript)

# Awesome Tiptap

## All in One Example

react https://github.com/fantasticit/think

react https://github.com/sereneinserenade/placenoter/blob/main/package.json

vue https://github.com/nextcloud/text

## Interact with Backend

https://www.zhihu.com/question/544593291

如何实时同步富文本编辑器 (如 tiptap) 中的内容到服务器？

如何只发送必要的更改到服务端，就像 collaborative editing 那种。那么用 Y.js 能实现我的目的呢（我并不需要多用户协作的功能）？ 并且在服务端也只需要更改相应内容，也不需要 replace mongodb 中的整个 field
