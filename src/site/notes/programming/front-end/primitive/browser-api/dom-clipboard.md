---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-02-17-Fri, 11:21:59 am","date-modified":"2024-07-02-Tue, 11:39:50 am","permalink":"/programming/front-end/primitive/browser-api/dom-clipboard/","dgPassFrontmatter":true}
---


# Clipboard

安全上下文

提供了一个用于读写系统剪贴板上的文本和数据的接口. 规范中被称为异步剪贴板 API（Async Clipboard API）

异步剪贴板 API 是一个相对较新的 API, 浏览器仍在逐步实现它. 由于潜在的安全问题和技术复杂性, 大多数浏览器都未完全实现这个 API

主动获取剪贴板内容可以使用这个方法, 但是会向用户请求权限, 而且可能会被拒绝

# write()

[Clipboard: write() method - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard/write)

# ClipboardEvent

安全上下文

表示提供了有关剪贴板修改的信息的事件，也就是 [`cut`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/cut_event "cut")、[`copy`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/copy_event "copy")，和 [`paste`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/paste_event "paste")。规范中被称为剪贴板事件 API（Clipboard Event API）。

## 构造函数

[`ClipboardEvent()`](https://developer.mozilla.org/zh-CN/docs/Web/API/ClipboardEvent/ClipboardEvent "ClipboardEvent()")

用给定的参数创建了一个 `ClipboardEvent` 事件。

## ClipboardEvent.clipboardData

[`ClipboardEvent.clipboardData`](https://developer.mozilla.org/zh-CN/docs/Web/API/ClipboardEvent/clipboardData) 

是一个 [`DataTransfer`](https://developer.mozilla.org/zh-CN/docs/Web/API/DataTransfer) 对象，它包含了由用户发起的 [`cut`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/cut_event) 、 [`copy`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/copy_event) 和 [`paste`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/paste_event) 操作影响的数据，以及它的 MIME 类型。

描述哪些数据可以由 [`cut`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/cut_event) 和 [`copy`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/copy_event) 事件处理器放入剪切板，通常通过调用 [`setData(format, data)`](https://developer.mozilla.org/zh-CN/docs/Web/API/DataTransfer/setData "setData(format, data)") 方法；

### setData

![DataTransfer.setData()](dom-drag-event.md#DataTransfer.setData())

### getData

获取由 [`paste`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/paste_event) 事件处理器拷贝进剪切板的数据，通常通过调用 [`getData(format)`](https://developer.mozilla.org/zh-CN/docs/Web/API/DataTransfer/getData "getData(format)") 方法

![DataTransfer.setData()](dom-drag-event.md#DataTransfer.getData())

## Copy Event

The **`copy`** event fires when the user initiates a copy action through the browser's user interface.

The event's default action is to copy the selection (if any) to the clipboard.

A handler for this event can modify the clipboard contents by calling [`setData(format, data)`](https://developer.mozilla.org/en-US/docs/Web/API/DataTransfer/setData "setData(format, data)") on the event's [`ClipboardEvent.clipboardData`](https://developer.mozilla.org/en-US/docs/Web/API/ClipboardEvent/clipboardData) property, and cancelling the event's default action using [`event.preventDefault()`](https://developer.mozilla.org/en-US/docs/Web/API/Event/preventDefault "event.preventDefault()").

However, the handler cannot read the clipboard data.

It's possible to construct and dispatch a [synthetic](https://developer.mozilla.org/en-US/docs/Web/Events/Creating_and_triggering_events) `copy` event, but this will not affect the system clipboard.

```js
const source = document.querySelector('div.source');

source.addEventListener('copy', (event) => {
    const selection = document.getSelection();
    event.clipboardData.setData('text/plain', selection.toString().toUpperCase());
    event.preventDefault();
});
```

## Cut Event

The **`cut`** event is fired when the user has initiated a "cut" action through the browser's user interface.

If the user attempts a cut action on uneditable content, the `cut` event still fires but the event object contains no data.

The event's default action is to copy the current selection (if any) to the system clipboard and remove it from the document.

A handler for this event can _modify_ the clipboard contents by calling [`setData(format, data)`](https://developer.mozilla.org/en-US/docs/Web/API/DataTransfer/setData "setData(format, data)") on the event's [`ClipboardEvent.clipboardData`](https://developer.mozilla.org/en-US/docs/Web/API/ClipboardEvent/clipboardData) property, and cancelling the default action using [`event.preventDefault()`](https://developer.mozilla.org/en-US/docs/Web/API/Event/preventDefault "event.preventDefault()").

Note though that cancelling the default action will also prevent the document from being updated. So an event handler which wants to emulate the default action for "cut" while modifying the clipboard must also manually remove the selection from the document.

The handler cannot _read_ the clipboard data.

It's possible to construct and dispatch a [synthetic](https://developer.mozilla.org/en-US/docs/Web/Events/Creating_and_triggering_events) `cut` event, but this will not affect the system clipboard or the document's contents.

```js
const source = document.querySelector('div.source');

source.addEventListener('cut', (event) => {
    event.preventDefault();

    const selection = document.getSelection();
    event.clipboardData.setData('text/plain', selection.toString().toUpperCase());
    selection.deleteFromDocument();
});
```

## Paste Event

The **`paste`** event is fired when the user has initiated a "paste" action through the browser's user interface.

If the cursor is in an editable context (for example, in a [`<textarea>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/textarea) or an element with [`contenteditable`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/contenteditable) attribute set to `true`) then the default action is to insert the contents of the clipboard into the document at the cursor position.

A handler for this event can access the clipboard contents by calling [`getData()`](https://developer.mozilla.org/en-US/docs/Web/API/DataTransfer/getData "getData()") on the event's `clipboardData` property.

To override the default behavior (for example to insert some different data or a transformation of the clipboard contents) an event handler must cancel the default action using [`event.preventDefault()`](https://developer.mozilla.org/en-US/docs/Web/API/Event/preventDefault "event.preventDefault()"), and then insert its desired data manually.

It's possible to construct and dispatch a [synthetic](https://developer.mozilla.org/en-US/docs/Web/Events/Creating_and_triggering_events) `paste` event, but this will not affect the document's contents.

```js
const target = document.querySelector('div.target');

target.addEventListener('paste', (event) => {
    event.preventDefault();

    let paste = (event.clipboardData || window.clipboardData).getData('text');
    paste = paste.toUpperCase();
    const selection = window.getSelection();
    if (!selection.rangeCount) return;
    selection.deleteFromDocument();
    selection.getRangeAt(0).insertNode(document.createTextNode(paste));
    selection.collapseToEnd();
});

```

# 同时粘贴 Html 和文本到 Input

用户主动粘贴. 是可以同时粘贴 html 和文本的?

```ts
/**
 * 复制 html 内容到剪贴板. 同时写入 html 和纯文本
 */
export function copyHTML(htmlContent: string) {
  const htmlType = 'text/html';
  const htmlBlob = new Blob([htmlContent], { type: htmlType });

  const parser = new DOMParser();
  const doc = parser.parseFromString(htmlContent, 'text/html');
  const textContent = doc.body.textContent || '';

  const textType = 'text/plain';
  const textBlob = new Blob([textContent], { type: textType });

  const data = [new ClipboardItem({ [textType]: textBlob, [htmlType]: htmlBlob })];
  return navigator.clipboard.write(data);
}
```
