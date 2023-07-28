---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/browser-api/dom-drag-event/"}
---


# DragEvent

The **`DragEvent`** interface is a [`DOM event`](https://developer.mozilla.org/en-US/docs/Web/API/Event "DOM event") that represents a drag and drop interaction.

This interface inherits properties from [`MouseEvent`](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent) and [`Event`](https://developer.mozilla.org/en-US/docs/Web/API/Event).

![](/img/user/programming/font-end/primitive/browser-api/dom-event/image-20230210163400125.png)

## Constructors

Although this interface has a constructor, it is not possible to create a useful DataTransfer object from script, since [`DataTransfer`](https://developer.mozilla.org/en-US/docs/Web/API/DataTransfer) objects have a processing and security model that is coordinated by the browser during drag-and-drops.

You can only creates a synthetic and untrusted DragEvent.

## Event Types

![](/img/user/programming/font-end/primitive/browser-api/dom-drag-event/image-20230212154027116.png)

[`drag`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/drag_event "drag") : This event is fired when an element or text selection is being dragged.

[`dragend`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dragend_event "dragend") : This event is fired when a drag operation is being ended (by releasing a mouse button or hitting the escape key).

[`dragenter`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dragenter_event "dragenter") : This event is fired when a dragged element or text selection enters a valid drop target.

[`dragleave`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dragleave_event "dragleave") : This event is fired when a dragged element or text selection leaves a valid drop target.

[`dragover`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dragover_event "dragover") : This event is fired continuously when an element or text selection is being dragged and the mouse pointer is over a valid drop target (every 50 ms WHEN mouse is not moving ELSE much faster between 5 ms (slow movement) and 1ms (fast movement) approximately. This firing pattern is different than [`mouseover`](https://developer.mozilla.org/en-US/docs/Web/API/Element/mouseover_event "mouseover") ).

[`dragstart`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dragstart_event "dragstart") : This event is fired when the user starts dragging an element or text selection.

[`drop`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/drop_event "drop") : This event is fired when an element or text selection is dropped on a valid drop target.## [Event types](https://developer.mozilla.org/en-US/docs/Web/API/DragEvent#event_types)

### 注意

+ 拖拉过程只触发以上这些拖拉事件，尽管鼠标在移动，但是鼠标事件不会触发。
+ 将文件从操作系统拖拉进浏览器，不会触发 `dragstart` 和 `dragend` 事件。
+ `dragenter` 和 `dragover` 事件的监听函数，用来取出拖拉的数据（即允许放下被拖拉的元素）。由于网页的大部分区域不适合作为放下拖拉元素的目标节点，所以这两个事件的默认设置为当前节点不允许接受被拖拉的元素。如果想要在目标节点上放下的数据，首先必须阻止这两个事件的默认行为。阻止其中之一即可. 如果不阻止默认行为, drop 事件依然可以触发, 但是 dropEffect 会为 null, 导致拖拽无法生效?
{ #zs8ntu}


```html
<div ondragover="return false">
<div ondragover="event.preventDefault()">
```

> 上面代码中，如果不取消拖拉事件或者阻止默认行为，就不能在 `div` 节点上放下被拖拉的节点。

# MouseEvent

dragEvent 继承了 mouseEvent

并且在拖拽过程中 mouseEvent 不会触发

所以我们可以通过经典的 mouseEvent 的 clientX 处理部分拖拽定位相关的逻辑.

# DragEvent.dataTransfer

[DataTransfer - Web API 接口参考 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/DataTransfer)

Read only

The data that is transferred during a drag and drop interaction.

放置元素和拖动元素分别绑定了自己的事件，可如何将拖拽元素和放置元素 **建立联系** 以及 **传递数据**？

在拖放交互期间传输的数据主要通过 dataTransfer 属性完成. 保存着拖拽操作中的数据, 例如拖拽事件的类型（如拷贝 copy 或者移动 move）, 拖拽的数据（一个或者多个项）和每个拖拽项的类型（MIME 类型）

拖拉的数据分成两方面: 数据的种类（又称格式）和数据的值. 数据的种类是一个 MIME 字符串（比如 `text/plain`、`image/jpeg`）, 数据的值是一个字符串. 一般来说, 如果拖拉一段文本, 则数据默认就是那段文本；如果拖拉一个链接，则数据默认就是链接的 URL。

拖拉事件开始时，开发者可以提供数据类型和数据值。拖拉过程中，开发者通过 `dragenter` 和 `dragover` 事件的监听函数，检查数据类型，以确定是否允许放下（drop）被拖拉的对象。比如，在只允许放下链接的区域，检查拖拉的数据类型是否为 `text/uri-list`。

发生 `drop` 事件时，监听函数取出拖拉的数据，对其进行处理。

## DataTransfer 的实例属性

### DataTransfer.dropEffect

`DataTransfer.dropEffect` 属性用来设置放下（drop）被拖拉节点时的效果，会影响到拖拉经过相关区域时鼠标的形状。它可能取下面的值。

- copy：复制被拖拉的节点
- move：移动被拖拉的节点
- link：创建指向被拖拉的节点的链接
- none：无法放下被拖拉的节点

除了上面这些值，设置其他的值都是无效的。

```javascript
target.addEventListener('dragover', function (e) {
  e.preventDefault();
  e.stopPropagation();
  e.dataTransfer.dropEffect = 'copy';
});
```

上面代码中，被拖拉元素一旦 `drop`，接受的区域会复制该节点。

`dropEffect` 属性一般在 `dragenter` 和 `dragover` 事件的监听函数中设置，对于 `dragstart`、`drag`、`dragleave` 这三个事件，该属性不起作用。因为该属性只对接受被拖拉的节点的区域有效，对被拖拉的节点本身是无效的。进入目标区域后，拖拉行为会初始化成设定的效果。

对于 [`drop`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement/drop_event) 和 [`dragend`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement/dragend_event) 事件，`dropEffect` 会被设置为想要得到的值，即最后一次 [`dragenter`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement/dragenter_event) 或 [`dragover`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement/dragover_event) 事件后 `dropEffect` 的值。例如，在一个 [`dragend`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement/dragend_event) 事件中，如果期望得到的 dropEffect 是“move”，那么被拖拽的数据会从源中移除。

> 根据这一点, 可以通过 dragend 事件中的 dropEffect 属性来判断节点的 drop 行为结果. 如果是 drop 失败, dropEffect 是为 none

设置 `dropEffect` 属性为 "move"，只是告诉浏览器在拖放过程中，元素的拖放效果为移动，而不是复制或链接。但是它并不会自动将拖动的元素移动到放置目标位置，这需要通过 JavaScript 手动实现。

在 `drop` 事件中，我们需要手动将拖动的元素移动到放置目标位置。这是因为，`drop` 事件的默认行为是打开链接，而不是移动元素。因此，我们需要通过调用 `appendChild()` 或 `insertBefore()` 等方法，将拖动的元素移动到放置目标位置。

另外需要注意的是，在某些浏览器版本中，当 `dropEffect` 属性设置为 "move" 时，即使在 `drop` 事件中手动移动了元素，也可能不会生效，因为浏览器在这种情况下默认禁止了元素的移动。为了解决这个问题，我们需要在 `drop` 事件中手动调用 `event.preventDefault()`，以取消浏览器的默认行为。这样，即使浏览器禁止了元素的移动，我们也可以通过 JavaScript 手动移动元素。

### DataTransfer.effectAllowed

`DataTransfer.effectAllowed` 属性设置本次拖拉中允许的效果。它可能取下面的值。

- copy：复制被拖拉的节点
- move：移动被拖拉的节点
- link：创建指向被拖拉节点的链接
- copyLink：允许 `copy` 或 `link`
- copyMove：允许 `copy` 或 `move`
- linkMove：允许 `link` 或 `move`
- all：允许所有效果
- none：无法放下被拖拉的节点
- uninitialized：默认值，等同于 `all`

如果某种效果是不允许的，用户就无法在目标节点中达成这种效果。

这个属性与 `dropEffect` 属性是同一件事的两个方面。`effectAllowed` 设置被拖拉的节点允许的效果，后者设置接受拖拉的区域的效果，它们往往配合使用。

`dragstart` 事件的监听函数，可以用来设置这个属性。其他事件的监听函数里面设置这个属性是无效的。

```javascript
source.addEventListener('dragstart', function (e) {
  e.dataTransfer.effectAllowed = 'move';
});

target.addEventListener('dragover', function (e) {
  e.dataTransfer.dropEffect = 'move';
});
```

只要 `dropEffect` 属性和 `effectAllowed` 属性之中，有一个为 `none`，就无法在目标节点上完成 `drop` 操作

### DataTransfer.files

包含数据传输中可用的所有本地文件的列表（FileList 对象）。如果拖动操作不涉及拖动文件，则此属性为空列表。

下面就是一个接收拖拉文件的例子。

```javascript
// HTML 代码如下
// <div id="output" style="min-height: 200px;border: 1px solid black;">
//   文件拖拉到这里
// </div>

var div = document.getElementById('output');

div.addEventListener("dragenter", function( event ) {
  div.textContent = '';
  event.stopPropagation();
  event.preventDefault();
}, false);

div.addEventListener("dragover", function( event ) {
  event.stopPropagation();
  event.preventDefault();
}, false);

div.addEventListener("drop", function( event ) {
  event.stopPropagation();
  event.preventDefault();
  var files = event.dataTransfer.files;
  for (var i = 0; i < files.length; i++) {
    div.textContent += files[i].name + ' ' + files[i].size + '字节\n';
  }
}, false);
```

上面代码中，通过 `dataTransfer.files` 属性读取被拖拉的文件的信息。如果想要读取文件内容，就要使用 `FileReader` 对象。

```javascript
div.addEventListener('drop', function(e) {
  e.preventDefault();
  e.stopPropagation();

  var fileList = e.dataTransfer.files;
  if (fileList.length > 0) {
    var file = fileList[0];
    var reader = new FileReader();
    reader.onloadend = function(e) {
      if (e.target.readyState === FileReader.DONE) {
        var content = reader.result;
        div.innerHTML = 'File: ' + file.name + '\n\n' + content;
      }
    }
    reader.readAsBinaryString(file);
  }
});
```

### DataTransfer.types

`DataTransfer.types` 属性是一个只读的数组，每个成员是一个字符串，里面是拖拉的数据格式（通常是 MIME 值）。比如，如果拖拉的是文字，对应的成员就是 `text/plain`。

下面是一个例子，通过检查 `dataTransfer` 属性的类型，决定是否允许在当前节点执行 `drop` 操作。

```javascript
function contains(list, value){
  for (var i = 0; i < list.length; ++i) {
    if(list[i] === value) return true;
  }
  return false;
}

function doDragOver(event) {
  var isLink = contains(event.dataTransfer.types, 'text/uri-list');
  if (isLink) event.preventDefault();
}
```

上面代码中，只有当被拖拉的节点有一个是链接时，才允许在当前节点放下。

### DataTransfer.items

`DataTransfer.items` 属性返回一个类似数组的只读对象（DataTransferItemList 实例），每个成员就是本次拖拉的一个对象（DataTransferItem 实例）。如果本次拖拉不包含对象，则返回一个空对象。

DataTransferItemList 实例具有以下的属性和方法。

- `length`：返回成员的数量
- `add(data, type)`：增加一个指定内容和类型（比如 `text/html` 和 `text/plain`）的字符串作为成员
- `add(file)`：`add` 方法的另一种用法，增加一个文件作为成员
- `remove(index)`：移除指定位置的成员
- `clear()`：移除所有的成员

DataTransferItem 实例具有以下的属性和方法。

- `kind`：返回成员的种类（`string` 还是 `file`）。
- `type`：返回成员的类型（通常是 MIME 值）。
- `getAsFile()`：如果被拖拉是文件，返回该文件，否则返回 `null`。
- `getAsString(callback)`：如果被拖拉的是字符串，将该字符传入指定的回调函数处理。该方法是异步的，所以需要传入回调函数。

下面是一个例子。

```javascript
div.addEventListener('drop', function (e) {
  e.preventDefault();
  if (e.dataTransfer.items != null) {
    for (var i = 0; i < e.dataTransfer.items.length; i++) {
      console.log(e.dataTransfer.items[i].kind + ': ' + e.dataTransfer.items[i].type);
    }
  }
});
```

## DataTransfer 的实例方法

### DataTransfer.setData()

`DataTransfer.setData()` 方法用来设置拖拉事件所带有的数据。该方法没有返回值。

```javascript
event.dataTransfer.setData('text/plain', 'Text to drag');
```

上面代码为当前的拖拉事件加入纯文本数据。

该方法接受两个参数, 都是字符串. 第一个参数表示数据类型（比如 `text/plain`）, 第二个参数是具体数据. 如果指定类型的数据在 `dataTransfer` 属性不存在，那么这些数据将被额外加入, 否则原有的数据将被新数据替换。

如果是拖拉文本框或者拖拉选中的文本，会默认将对应的文本数据，添加到 `dataTransfer` 属性，不用手动指定。

```html
<div draggable="true">
  aaa
</div>
```

上面代码中，拖拉这个 `<div>` 元素会自动带上文本数据 `aaa`。

使用 `setData` 方法，可以替换到原有数据。

```html
<div
  draggable="true"
  ondragstart="event.dataTransfer.setData('text/plain', 'bbb')"
>
  aaa
</div>
```

上面代码中，拖拉数据实际上是 `bbb`，而不是 `aaa`。

下面是添加其他类型的数据。由于 `text/plain` 是最普遍支持的格式，为了保证兼容性，建议最后总是保存一份纯文本格式的数据。

```javascript
var dt = event.dataTransfer;

// 添加链接
dt.setData('text/uri-list', 'http://www.example.com');
dt.setData('text/plain', 'http://www.example.com');

// 添加 HTML 代码
dt.setData('text/html', 'Hello there, <strong>stranger</strong>');
dt.setData('text/plain', 'Hello there, <strong>stranger</strong>');
dt.setData('text/html', event.currentTarget.outerHTML);

// 添加图像的 URL
dt.setData('text/uri-list', imageurl);
dt.setData('text/plain', imageurl);
```

可以一次提供多种格式的数据。

```javascript
var dt = event.dataTransfer;
dt.setData('application/x-bookmark', bookmarkString);
dt.setData('text/uri-list', 'http://www.example.com');
dt.setData('text/plain', 'http://www.example.com');
```

上面代码中，通过在同一个事件上面，存放三种类型的数据，使得拖拉事件可以在不同的对象上面，`drop` 不同的值。注意，第一种格式是一个自定义格式，浏览器默认无法读取，这意味着，只有某个部署了特定代码的节点，才可能 `drop`（读取到）这个数据。

### DataTransfer.getData()

`DataTransfer.getData()` 方法接受一个字符串（表示数据类型）作为参数，返回事件所带的指定类型的数据（通常是用 `setData` 方法添加的数据）。如果指定类型的数据不存在，则返回空字符串。**通常只有 `drop` 事件触发后，才能取出数据**

下面是一个 `drop` 事件的监听函数，用来取出指定类型的数据。

```javascript
function onDrop(event) {
  var data = event.dataTransfer.getData('text/plain');
  event.target.textContent = data;
  event.preventDefault();
}
```

上面代码取出拖拉事件的文本数据，将其替换成当前节点的文本内容。注意，这时还必须取消浏览器的默认行为，因为假如用户拖拉的是一个链接，浏览器默认会在当前窗口打开这个链接。

`getData` 方法返回的是一个字符串，如果其中包含多项数据，就必须手动解析。

```javascript
function doDrop(event) {
  var lines = event.dataTransfer.getData('text/uri-list').split('\n');
  for (let line of lines) {
    let link = document.createElement('a');
    link.href = line;
    link.textContent = line;
    event.target.appendChild(link);
  }
  event.preventDefault();
}
```

上面代码中，`getData` 方法返回的是一组链接，就必须自行解析。

类型值指定为 `URL`，可以取出第一个有效链接。

```javascript
var link = event.dataTransfer.getData('URL');
```

下面的例子是从多种类型的数据里面取出数据。

```javascript
function doDrop(event) {
  var types = event.dataTransfer.types;
  var supportedTypes = ['text/uri-list', 'text/plain'];
  types = supportedTypes.filter(function (value) { types.includes(value) });
  if (types.length) {
    var data = event.dataTransfer.getData(types[0]);
  }
  event.preventDefault();
}
```

#### RTF

富文本格式（Rich Text Format）即 RTF 格式，又称多文本格式，是由 [微软](https://baike.baidu.com/item/%E5%BE%AE%E8%BD%AF/124767?fromModule=lemma_inlink) 公司开发的 [跨平台](https://baike.baidu.com/item/%E8%B7%A8%E5%B9%B3%E5%8F%B0/8558902?fromModule=lemma_inlink) 文档格式。大多数的 [文字处理软件](https://baike.baidu.com/item/%E6%96%87%E5%AD%97%E5%A4%84%E7%90%86%E8%BD%AF%E4%BB%B6/4719058?fromModule=lemma_inlink) 都能读取和保存 RTF 文档。它是一种方便于不同的设备、系统查看的文本和图形文档格式。

RTF 使用美国国内标准协会（[ANSI](https://baike.baidu.com/item/ANSI/14955?fromModule=lemma_inlink)）、 PC-8、 [Mac](https://baike.baidu.com/item/Mac/173?fromModule=lemma_inlink)intosh（mac[苹果](https://baike.baidu.com/item/%E8%8B%B9%E6%9E%9C/6011224?fromModule=lemma_inlink)），或 [IBM](https://baike.baidu.com/item/IBM/9190?fromModule=lemma_inlink) 的 PC 字符设置控制显示形式和打印形式。在不同的操作系统下创建的 RTF 文档可以在多种操作系统和 [应用程序](https://baike.baidu.com/item/%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F/5985445?fromModule=lemma_inlink) 之间互相传输、查看。其作为 [MS-DOS](https://baike.baidu.com/item/MS-DOS/1120792?fromModule=lemma_inlink)、 [Microsoft Windows](https://baike.baidu.com/item/Microsoft%20Windows/3304184?fromModule=lemma_inlink)、 [OS/2](https://baike.baidu.com/item/OS%2F2/1958699?fromModule=lemma_inlink)、 Macintosh 苹果系统，应用程序之间处理文档的特殊 [翻译软件](https://baike.baidu.com/item/%E7%BF%BB%E8%AF%91%E8%BD%AF%E4%BB%B6/1320769?fromModule=lemma_inlink)。

### DataTransfer.clearData()

`DataTransfer.clearData()` 方法接受一个字符串（表示数据类型）作为参数，删除事件所带的指定类型的数据。如果没有指定类型，则删除所有数据。如果指定类型不存在，则调用该方法不会产生任何效果。

```javascript
event.dataTransfer.clearData('text/uri-list');
```

上面代码清除事件所带的 `text/uri-list` 类型的数据。

该方法不会移除拖拉的文件，因此调用该方法后，`DataTransfer.types` 属性可能依然会返回 `Files` 类型（前提是存在文件拖拉）。

注意，该方法只能在 `dragstart` 事件的监听函数之中使用，因为这是拖拉操作的数据唯一可写的时机。

### DataTransfer.setDragImage()

拖动过程中（`dragstart` 事件触发后），浏览器会显示一张图片跟随鼠标一起移动，表示被拖动的节点。这张图片是自动创造的，通常显示为被拖动节点的外观，不需要自己动手设置。

`DataTransfer.setDragImage()` 方法可以自定义这张图片。它接受三个参数。第一个是 `<img>` 节点或者 `<canvas>` 节点，如果省略或为 `null`，则使用被拖动的节点的外观；第二个和第三个参数为鼠标相对于该图片左上角的横坐标和纵坐标。

下面是一个例子。

```javascript
/* HTML 代码如下
 <div id="drag-with-image" class="dragdemo" draggable="true">
   drag me
 </div>
*/

var div = document.getElementById('drag-with-image');
div.addEventListener('dragstart', function (e) {
  var img = document.createElement('img');
  img.src = 'http://path/to/img';
  e.dataTransfer.setDragImage(img, 0, 0);
}, false);
```

# HTML Draggable 属性

这个属性是枚举类型，而不是布尔类型。这意味着必须显式指定值为 true 或者 false，而不能简写。

- 拖拽选中文本、拖拽图像和拖拽链接时，会使用默认拖拽行为。
- 拖拽图像或链接时，图像或链接的 URL 被设定为拖拽数据。
- 对于其他元素，**只有当它们作为被选中的一部分时，才会触发默认拖拽行为。**

除了图像、链接和选择的文本默认的可拖拽行为之外，其他元素在默认情况下是不可拖拽的。如果要使其他的 HTML 元素可拖拽：

1. 将想要拖拽的元素的 draggable 属性设置成 draggable="true"。
2. 为 dragstart 事件添加监听。
3. 在定义的监听中设置拖拽数据。

```html
<p draggable="true" ondragstart="event.dataTransfer.setData('text/plain', 'This text may be dragged')">
  This text <strong>may</strong> be dragged.
</p>
```

# 拖拽前置处理

![dom-drag-event](dom-drag-event.md#HTML%20Draggable%20属性)

![dom-drag-event](dom-drag-event.md#^zs8ntu)

# 基于 mouseEvent 实现拖拽

[原生拖拽太拉跨了，纯JS自己手写一个拖拽效果，纵享丝滑 - 掘金](https://juejin.cn/post/7145447742515445791#heading-8)

# Sortablejs

[GitHub - SortableJS/Sortable: Reorderable drag-and-drop lists for modern browsers and touch devices. No jQuery or framework required.](https://github.com/SortableJS/Sortable)

得好好过一遍文档, 看看有什么可以利用的了

[Sort when dropped and not during move · Issue #1244 · SortableJS/Sortable · GitHub](https://github.com/SortableJS/Sortable/issues/1244)

# FAQ

#faq/ui

## mouseEvent 和 dragEvent

在实现拖拽上, 有什么区别? 如何根据不同的场景选择合适的方案?

## 拖拽删除

运用了基础的拖拽事件, 关键是 dragStart 时记录拖拽的元素, 发生 drop 事件时通过常规的 dom 操作删除该元素

<iframe height="300" style="width: 100%;" scrolling="no" title="drag to remove" src="https://codepen.io/chiyu-git/embed/GRBVQYx?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true"></iframe>

## 拖拽排序

<iframe height="300" style="width: 100%;" scrolling="no" title="darg to sort" src="https://codepen.io/chiyu-git/embed/VwBoQRM?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true"></iframe>

## 预览拖拽的图片

<iframe height="300" style="width: 100%;" scrolling="no" title="Untitled" src="https://codepen.io/chiyu-git/embed/GRBVQaG?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true"></iframe>

## DragEnter 和 dragLeave 的冒泡问题

[jquery - 'dragleave' of parent element fires when dragging over children elements - Stack Overflow](https://stackoverflow.com/questions/10867506/dragleave-of-parent-element-fires-when-dragging-over-children-elements)

dragLevel 是个不错的方案
