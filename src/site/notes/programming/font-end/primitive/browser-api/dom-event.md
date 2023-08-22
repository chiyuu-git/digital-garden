---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/browser-api/dom-event/"}
---


2023-02-10

# 事件模型

事件指的是用户和浏览器之间的交互行为。比如：点击按钮、关闭窗口、鼠标移动。。。

事件是不用驼峰的

我们可以为事件来绑定回调函数来响应事件。

## 事件的绑定

### 标签属性

可以在标签的事件属性中设置相应的 JS 代码

\<button onclick="js 代码。。。">按钮\</button>

结构行为耦合，不推荐使用

### DOM0

可以通过为对象的指定事件属性设置回调函数的形式来处理事件

只能绑定一个事件，后绑定的会覆盖前面的

```html
  <button id="btn">按钮\</button>
  <script>
  var btn = document.getElementById("btn");
  btn.onclick = function(){};
  </script>
```

onxxx 又称为 事件句柄，相应的，addEventListener('xxx')，是事件监听

### DOM2

#### addEventListener()

使用 addEventListener() 可以同时为一个元素的相同事件同时绑定多个响应函数，这样当事件被触发时，响应函数将会 **按照函数的绑定顺序执行**

Dom2 事件，Dom2 标准定义的绑定方式

该方法不支持 ie8

**参数：**

1. 事件触发的字符串，**不需要 on**
2. 回调函数，当事件触发时，该函数会被调用
3. 是否在捕获阶段触发事件，需要一个布尔值，一般传 false

```js
   btn01.addEventListener(“click”,function(){},false);
```

**option**

- 第三个参数除了布尔值 `useCapture`，还可以是一个属性配置对象。该对象有以下属性。可用的选项如下：
  - `capture`: [`Boolean`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Boolean)，表示 `listener` 会在该类型的事件捕获阶段传播到该 `EventTarget` 时触发。
  - `once`: [`Boolean`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Boolean)，表示 `listener` 在添加之后最多只调用一次。如果是 ` true`，`listener` 会在其被调用之后自动移除。
  - `passive`: [`Boolean`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Boolean)，设置为 true 时，表示 `listener` 永远不会调用 `preventDefault()。如果 listener 仍然调用了这个函数，客户端将会忽略它并抛出一个控制台警告。`
  - ` mozSystemGroup`: 只能在 XBL 或者是 Firefox' chrome 使用，这是个 [`Boolean`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Boolean)，表示 `listener ` 被添加到 system group。
- 在旧版本的 DOM 的规定中， `addEventListener()` 的第三个参数是一个布尔值表示是否在捕获阶段调用事件处理程序。随着时间的推移，很明显需要更多的选项。与其在方法之中添加更多参数（传递可选值将会变得异常复杂），倒不如把第三个参数改为一个包含了各种属性的对象，这些属性的值用来被配置删除事件侦听器的过程。
- 因为旧版本的浏览器（以及一些相对不算古老的）仍然假定第三个参数是布尔值，你需要编写一些代码来有效地处理这种情况。你可以对每一个你感兴趣的 options 值进行特性检测。
- 如果你想检测 `passive` 值可以参考下面这个例子：

  ```js
  var passiveSupported = false;
  
  try {
    var options = Object.defineProperty({}, "passive", {
      get: function() {
        passiveSupported = true;
      }
    });
  
    window.addEventListener("test", null, options);
  } catch(err) {}
  ```

- 这段代码为 `passive` 属性创建了一个带有 getter 函数的 `options` 对象；getter 设定了一个标识， `passiveSupported`，被调用后就会把其设为 `true`。那意味着如果浏览器检查 `options` 对象上的 `passive` 值时， `passiveSupported` 将会被设置为 `true`；否则它将保持 `false`。然后我们调用 `addEventListener()` 去设置一个指定这些选项的空事件处理器，这样如果浏览器将第三个参数认定为对象的话，这些选项值就会被检查。
- 你可以利用这个方法检查 options 之中任一个值。只需使用与上面类似的代码，为选项设定一个 getter。
- 然后，当你想实际创建一个是否支持 options 的事件侦听器时，你可以这样做：

  ```js
  someElement.addEventListener("mouseup", handleMouseUp, passiveSupported
                                 ? { passive: true } : false);
  ```

- 我们在 `someElement` 这里添加了一个 `mouseup`。对于第三个参数，如果 `passiveSupported` 是 `true` ，我们传递了一个 `passive` 值为 `true` 的 `options` 对象；如果相反的话，我们知道要传递一个布尔值，于是就传递 `false` 作为 `useCapture` 的参数。
- 如果你愿意，你可以用一个类似 [Modernizr](https://modernizr.com/docs) 或 [Detect It](https://github.com/rafrex/detect-it) 的第三方库来帮助你做这项测试。

#### **attachEvent()**

- 支持全部 ie5-10，其他浏览器不支持
  - 非标准没有捕获阶段
  - 从 ie6-10 有捕获阶段
- 这个方法也可以同时为一个事件绑定多个处理函数，不同的是它是 **后绑定先执行，执行顺序和 addEventListener() 相反**
  - 标准 ie 正序
  - 非标准倒序
- 如果顺序很重要的，写成一个，如果不重要的就用这个

**参数：**

1. 事件的字符串，**要 on**
2. 回调函数

- **addEventListener() 中的 this，是绑定事件的对象**
- **attachEvent() 中的 this，是 window**
- 兼容：需要统一两个方法 this，传入的参数与是否调用

  ```js
  function bind(){…obj.attachEvent(obj,function(){}}
  
  function bind(obj, evname, fn) {
  	if (obj.addEventListener) {//除ie低版本外都可以进入
  		obj.addEventListener(evname, fn, false);
  	} else {
  		obj.attachEvent('on' + evname, function() {
  			fn.call(obj);
  		});
  	}
  }
  
  bind(document, 'click', fn1); 
  ```

### DOM0 和 DOM2 两种绑定方式的区别

| 方法             | 冒泡                       | 取消冒泡                  | cbCount | 禁止默认行为的方法   |
| ---------------- | -------------------------- | ------------------------- | ------- | -------------------- |
| DOM0             | 默认冒泡                   | event.calcelBubble = true | 一个    | return false         |
| attachEvent()    |                            |                           |         |                      |
| addEventListener | 通过 option 控制，默认冒泡 | event.stopPropagation()   | 多个    | event.preventDefault |
|                  |                            |                           |         |                      |

给属性定义函数，无论如何都不会报错的，所以给事件绑定监听一定要等提示，不然 onload 拼错了，能 debugger 一个上午

通过 event.cancelable 查看默认行为是否可以被取消

this

- DOM0 事件模型还涉及到直接写在 html 中的事件，因此不会传入 event 对象，同时，this 指向的是 window，不再是触发事件的 dom 对象。
- **addEventListener() 中的 this，是绑定事件的对象**, [事件](../../../FAQ/js-faq.md#addEventListener This)
- **attachEvent() 中的 this，是 window**

## 解除事件的绑定

DOM0: 想解除事件就相当简单了，只需要再注册一次事件，把值设成 null

  - 原理就是最后注册的事件要覆盖之前的，最后一次注册事件设置成 null，也就解除了事件绑定。
  - 因为使用 DOM0，重复绑定会覆盖前面的，也就不需要每次都解绑

DOM2:removeEventListener

  - 解除事件语法：btn.removeEventListener(" 事件名称 ", " 事件回调 ", " 捕获/冒泡 ");
  - 如果需要解除事件绑定，就需要保留回调函数的引用
  - 如果重复绑定不会覆盖，要注意解除事件的绑定

**注意**: 即使引用都相同, capture 的事件解绑时也必须使用 capture

DOM2:detachEvent(ie)

## 事件的冒泡（Bubble）

事件的冒泡指的是事件向上传导，当后代元素上的事件被触发时，将会导致其祖先元素上的同类事件（相同响应条件）也会触发。

> 亲了小姑娘一口，被小姑娘打了，被小姑娘爸爸打了，被小姑娘爷爷打了

事件的冒泡大部分情况下都是有益的，如果需要取消冒泡，则需要使用事件对象来取消

DOM2 级

  ```js
  元素.事件 = addEventListener(function(event){
  event = event || window.event;
  event.stopPropagation();
  });
  ```

DOM 0 级 可以通过取消事件的默认行为来阻止冒泡

  ```js
  元素.事件 = function(event){
  	return false
  }
  // 好像并不可以取消
  ```

DOM 0 级 还可以可以将事件对象的 cancelBubble 设置为 true，即可取消冒泡，IE 特性，但是 chrome 也实现了

  ```js
  元素.事件 = function(event){
  event = event || window.event;
  event.cancelBubble = true;
  };
  ```

## 事件的传播

微软公司认为事件应该是由内向外传播，也就是当事件触发时，应该先触发当前元素上的事件，然后再向当前元素的祖先元素上传播，也就说事件应该在冒泡阶段执行。所以 ie 的 **attachEvent()**，没有 true or false

网景公司认为事件应该是由外向内传播的，也就是当前事件触发时，应该先触发当前元素的最外层的祖先元素的事件，然后在向内传播给后代元素

W3C 综合了两个公司的方案，将事件传播分成了三个阶段

1. 捕获阶段: 在捕获阶段时从最外层的祖先元素，向目标元素进行事件的捕获，但是默认此时不会触发事件
2. 目标阶段: 事件捕获到目标元素，捕获结束开始在目标元素上触发事件
3. 冒泡阶段: 事件从目标元素向他的祖先元素传递，依次触发祖先元素上的事件

![1546564409847](/img/user/programming/font-end/primitive/browser-api/dom-event/1546564409847.png)

如果希望在捕获阶段就触发事件，可以将 **addEventListener() 的第三个参数设置为 true**，一般情况下我们不会希望在捕获阶段触发事件，所以这个参数一般都是 false

### 事件流

单击蓝色框后，开始事件捕获阶段：

1. 从最外层的 document 对象（浏览器其实是从 window 对象开始的）向内捕获事件，路过红色框时，查看到红色框有事件，
2. ​但是红色框说：“我是在冒泡阶段执行，现在是捕获阶段，等你回来再说吧。”
3. ​接下来是黄色框：“我在捕获阶段执行，就是现在执行！在控制台输“黄色框”吧~~”
4. 接下来到达目标阶段：
5. ​“DOM2 级事件”规范要求捕获阶段不会涉及事件目标即我们点击的那个最具体的元素，
6. ​但 IE9、Chrome 等浏览器都会在捕获阶段触发事件对象上的事件。执行目标对象的事件函数，控制台输出“蓝色
7. 框”。

 最后是冒泡阶段：

1. ​由目标对象向外传递，到达黄色框，黄色框说：“我在捕获阶段执行过了，你走吧...”
2. ​然后到达红色框，红色框说：“你终于回来了，现在就执行我的事件！”控制台输出“红色框”。
3. ​然后继续向外传播，直到到达 document 对象后停止。

其他：更改了元素绑定事件代码的顺序，执行顺序也和上面表现的一致。

## 事件的委派

希望只绑定一次事件，即可以应用到多个事件上，即使事件是后添加的

绑定给共同的祖先元素，点击后代可以冒泡至祖先，触发响应函数

```js
event.target 返回触发事件的对象
if(event.target.className == );
```

this 返回绑定事件的对象: 事件监听绑定在父元素上，事件也是发生在父元素身上，只是点击子元素也可以触发, 因为有冒泡

# EventTarget 接口

事件的本质是程序各个组成部分之间的一种通信方式，也是异步编程的一种实现。DOM 支持大量的事件，本章开始介绍 DOM 的事件编程。

## 概述

DOM 的事件操作（监听和触发），都定义在 `EventTarget` 接口。所有节点对象都部署了这个接口，其他一些需要事件通信的浏览器内置对象（比如，`XMLHttpRequest`、`AudioNode`、`AudioContext`）也部署了这个接口。

该接口主要提供三个实例方法。

  - `addEventListener`：绑定事件的监听函数
  - `removeEventListener`：移除事件的监听函数
  - `dispatchEvent`：触发事件

## EventTarget.dispatchEvent()

 `EventTarget.dispatchEvent` 方法在当前节点上触发指定事件，从而触发监听函数的执行。该方法返回一个布尔值，只要有一个监听函数调用了 `Event.preventDefault()`，则返回值为 `false`，否则为 `true`。

```js
target.dispatchEvent(event)
```

`dispatchEvent` 方法的参数是一个 `Event` 对象的实例 (详见 [事件对象 (event)](dom-event.md#事件对象%20(event)) 章节)

```js
  para.addEventListener('click', hello, false);
  var event = new Event('click');
  para.dispatchEvent(event);
```

上面代码在当前节点触发了 `click` 事件。

如果 `dispatchEvent` 方法的参数为空，或者不是一个有效的事件对象，将报错。

下面代码根据 `dispatchEvent` 方法的返回值，判断事件是否被取消了。

```js
  var canceled = !cb.dispatchEvent(event);
  if (canceled) {
    console.log('事件取消');
  } else {
    console.log('事件未取消');
  }
```

# 事件对象 (event)

事件发生以后，会产生一个事件对象，作为参数传给监听函数。浏览器原生提供一个 `Event` 对象，所有的事件都是这个对象的实例，或者说继承了 `Event.prototype` 对象。

这个事件对象中封装了当前事件的相关信息，比如：鼠标的坐标，键盘的按键，鼠标的按键，滚轮的方向。

可以在响应函数中定义一个形参，来使用 **事件对象**，但是在 IE8 以下浏览器中事件对象没有做完实参传递，而是作为 window 对象的属性保存 （不兼容火狐）

```js
  元素.事件 = function(event){ event = event || window.event; };
  元素.事件 = function(e){ e = e || event; };
```

`Event` 对象本身就是一个构造函数，可以用来生成新的实例。

```js
  event = new Event(type, options);
```

**参数**

- 第一个参数 `type` 是字符串，表示事件的名称；
- 第二个参数 `options` 是一个对象，表示事件对象的配置。该对象主要有下面两个属性。
  - `bubbles`：布尔值，可选，默认为 `false`，表示事件对象是否冒泡。
  - `cancelable`：布尔值，可选，默认为 `false`，表示事件是否可以被取消，即能否用 `Event.preventDefault()` 取消这个事件。一旦事件被取消，就好像从来没有发生过，不会触发浏览器对该事件的默认行为。

```js
  var ev = new Event(
    'look',
    {
      'bubbles': true,
      'cancelable': false
    }
  );
  document.dispatchEvent(ev);
```

+ 上面代码新建一个 `look` 事件实例，然后使用 `dispatchEvent` 方法触发该事件。
+ 注意，如果不是显式指定 `bubbles` 属性为 `true`，生成的事件就只能在“捕获阶段”触发监听函数。

## 实例属性

### Event.bubbles，Event.eventPhase

`Event.bubbles` 属性返回一个布尔值，表示当前事件是否会冒泡。该属性为只读属性，一般用来了解 Event 实例是否可以冒泡。前面说过，除非显式声明，`Event` 构造函数生成的事件，默认是不冒泡的。

`Event.eventPhase` 属性返回一个整数常量，表示事件目前所处的阶段。该属性只读。

  + 0，事件目前没有发生。
  + 1，事件目前处于捕获阶段，即处于从祖先节点向目标节点的传播过程中。
  + 2，事件到达目标节点，即 `Event.target` 属性指向的那个节点。
  + 3，事件处于冒泡阶段，即处于从目标节点向祖先节点的反向传播过程中。

### Event.cancelable

`Event.cancelable` 属性返回一个布尔值，表示事件是否可以取消。该属性为只读属性，一般用来了解 Event 实例的特性。

大多数浏览器的原生事件是可以取消的。比如，取消 `click` 事件，点击链接将无效。但是除非显式声明，`Event` 构造函数生成的事件，默认是不可以取消的。

  ```js
  var evt = new Event('foo');
  evt.cancelable  // false
  ```

当 `Event.cancelable` 属性为 `true` 时，调用 `Event.preventDefault()` 就可以取消这个事件，阻止浏览器对该事件的默认行为。

如果事件不能取消，调用 `Event.preventDefault()` 会没有任何效果。所以使用这个方法之前，最好用 `Event.cancelable` 属性判断一下是否可以取消。

### Event.cancelBubble

`Event.cancelBubble` 属性是一个布尔值，如果设为 `true`，相当于执行 `Event.stopPropagation()`，可以阻止事件的传播。

### Event.defaultPrevented

`Event.defaultPrevented` 属性返回一个布尔值，表示该事件是否调用过 `Event.preventDefault` 方法。该属性只读。

### Event.currentTarget，Event.target

事件发生以后，会经过捕获和冒泡两个阶段，依次通过多个 DOM 节点。因此，任意时点都有两个与事件相关的节点，一个是事件的原始触发节点（`Event.target`），另一个是事件当前正在通过的节点（`Event.currentTarget`）。前者通常是后者的后代节点。

`Event.currentTarget` 属性返回事件当前所在的节点，即事件当前正在通过的节点，也就是当前正在执行的监听函数所在的那个节点。随着事件的传播，这个属性的值会变。

`Event.target` 属性返回原始触发事件的那个节点，即事件最初发生的节点。这个属性不会随着事件的传播而改变。

事件传播过程中，不同节点的监听函数内部的 `Event.target` 与 `Event.currentTarget` 属性的值是不一样的。

### Event.type

`Event.type` 属性返回一个字符串，表示事件类型。事件的类型是在生成事件的时候指定的。该属性只读。

  ```js
  var evt = new Event('foo');
  evt.type // "foo"
  ```

### Event.timeStamp

`Event.timeStamp` 属性返回一个毫秒时间戳，表示事件发生的时间。它是相对于网页加载成功开始计算的。

```js
  var evt = new Event('foo');
  evt.timeStamp // 3683.6999999995896
```

它的返回值有可能是整数，也有可能是小数（高精度时间戳），取决于浏览器的设置。

下面是一个计算鼠标移动速度的例子，显示每秒移动的像素数量。

```
  var previousX;
```

### Event.isTrusted

`Event.isTrusted` 属性返回一个布尔值，表示该事件是否由真实的用户行为产生。比如，用户点击链接会产生一个 `click` 事件，该事件是用户产生的；`Event` 构造函数生成的事件，则是脚本产生的。

```js
  var evt = new Event('foo');
  evt.isTrusted // false
```

上面代码中，`evt` 对象是脚本产生的，所以 `isTrusted` 属性返回 `false`。

### Event.detail

`Event.detail` 属性只有浏览器的 UI （用户界面）事件才具有。该属性返回一个数值，表示事件的某种信息。具体含义与事件类型相关。比如：

  + 对于 `click` 和 `dblclick` 事件，`Event.detail` 是鼠标按下的次数（`1` 表示单击，`2` 表示双击，`3` 表示三击）
  + 对于鼠标滚轮事件，`Event.detail` 是滚轮正向滚动的距离，负值就是负向滚动的距离，返回值总是 3 的倍数。

## 实例方法

### Event.preventDefault()

`Event.preventDefault` 方法取消浏览器对当前事件的默认行为。比如点击链接后，浏览器默认会跳转到另一个页面，使用这个方法以后，就不会跳转了；再比如，按一下空格键，页面向下滚动一段距离，使用这个方法以后也不会滚动了。该方法生效的前提是，事件对象的 `cancelable` 属性为 `true`，如果为 `false`，调用该方法没有任何效果。

注意，该方法只是取消事件对当前元素的默认影响，不会阻止事件的传播。如果要阻止传播，可以使用 `stopPropagation()` 或 `stopImmediatePropagation()` 方法。

### Event.stopPropagation()

`stopPropagation` 方法阻止事件在 DOM 中继续传播，防止再触发定义在别的节点上的监听函数，但是不包括在当前节点上其他的事件监听函数。

### Event.stopImmediatePropagation()

`Event.stopImmediatePropagation` 方法阻止同一个事件的其他监听函数被调用，不管监听函数定义在当前节点还是其他节点。也就是说，该方法阻止事件的传播，比 `Event.stopPropagation()` 更彻底。

如果同一个节点对于同一个事件指定了多个监听函数，这些函数会根据添加的顺序依次调用。只要其中有一个监听函数调用了 `Event.stopImmediatePropagation` 方法，其他的监听函数就不会再执行了。

```js
  function l1(e){
    e.stopImmediatePropagation();
  }
  
  function l2(e){
    console.log('hello world');
  }
  
  el.addEventListener('click', l1, false);
  el.addEventListener('click', l2, false);
```

上面代码在 `el` 节点上，为 `click` 事件添加了两个监听函数 `l1` 和 `l2`。由于 `l1` 调用了 `event.stopImmediatePropagation` 方法，所以 `l2` 不会被调用。

### Event.composedPath()

`Event.composedPath()` 返回一个数组，成员是事件的最底层节点和依次冒泡经过的所有上层节点。

# MouseEvent

## 鼠标事件的种类

鼠标事件指与鼠标相关的事件，继承了 `MouseEvent` 接口。具体的事件主要有以下一些。

+ `click`：按下鼠标（通常是按下主按钮）时触发。
+ `dblclick`：在同一个元素上双击鼠标时触发。
+ `mousedown`：按下鼠标键时触发。
+ `mouseup`：释放按下的鼠标键时触发。
+ `mousemove`：当鼠标在一个节点内部移动时触发。当鼠标持续移动时，该事件会连续触发。为了避免性能问题，建议对该事件的监听函数做一些限定，比如限定一段时间内只能运行一次。
+ `mouseenter`：鼠标进入一个节点时触发，进入子节点 **不会触发** 这个事件
+ `mouseleave`：鼠标离开一个节点时触发，离开父节点 **不会触发** 这个事件
+ `mouseover`：鼠标进入一个节点时触发，进入子节点会 **再一次触发** 这个事件
+ `mouseout`：鼠标离开一个节点时触发，离开父节点也会 **再一次触发** 这个事件

  > over 和 out 两个 o 开头的是一组的，他们会由于子元素的冒泡而再次触发，想象 over 就像是爬楼梯一样，每一个都会触发一次

+ `contextmenu`：按下鼠标右键时（上下文菜单出现前）触发，或者按下“上下文菜单键”时触发。
+ `wheel`：滚动鼠标的滚轮时触发，该事件继承的是 `WheelEvent` 接口。

`click` 事件指的是，用户在同一个位置先完成 `mousedown` 动作，再完成 `mouseup` 动作。因此，触发顺序是，`mousedown` 首先触发，`mouseup` 接着触发，`click` 最后触发。

`dblclick` 事件则会在 `mousedown`、`mouseup`、`click` 之后触发。

`mouseover` 事件和 `mouseenter` 事件，都是鼠标进入一个节点时触发。两者的区别是：

+ `mouseenter` 事件只触发一次，不是通过冒泡触发，只要进入子节点同时也会被判断为进入了父节点
+ `mouseover` 事件会在子节点上触发，通过冒泡导致父节点触发该事件，着重的是 hover 在哪个元素身上
+ 无论子节点是否定位在父节点外，在 DOM 结构上都是父节点的一部分

```html
<div id="over">
  <div class="inner"></div>
  <div class="outer" id='stopped'></div>
</div>
<div id="enter">
  <div class="inner"></div>
  <div class="outer" id='stopEnter'></div>
</div>

<style>
  #over{
    position: absolute;
    left:200px;
    width: 300px;
    height: 300px;
    background-color: pink;
  }
  #enter{
    position: absolute;
    top:300px;
    width: 300px;
    height: 300px;
    background-color: green;
  }
  .inner{
    position: absolute;
    top:200px;
    width: 50px;
    height: 50px;
    background-color: orange;
  }
  .outer{
    position: absolute;
    left:350px;top:200px;
    width: 50px;
    height: 50px;
    background-color: red;
  }
</style>

<script>
  enter.onmouseenter = function(){
    console.log('enter')
  }
  enter.onmouseleave = function(){
    console.log('leave')
  }
  over.onmouseover = function(){
    console.log('over')
  }
  over.onmouseout = function(){
    console.log('out')
  }
  stopped.onmouseover = function(ev){
    ev.cancelBubble = true
    return false
  }
  stopEnter.addEventListener('mouseenter',function(ev){
    console.log('enter outer')
    ev.stopPropagation()
  })
</script>
```

## MouseEvent 接口概述

`MouseEvent` 接口代表了鼠标相关的事件，单击（click）、双击（dblclick）、松开鼠标键（mouseup）、按下鼠标键（mousedown）等动作，所产生的事件对象都是 `MouseEvent` 实例。此外，滚轮事件和拖拉事件也是 `MouseEvent` 实例。

`MouseEvent` 接口继承了 `Event` 接口，所以拥有 `Event` 的所有属性和方法。它还有自己的属性和方法。

### MouseEvent()

浏览器原生提供一个 `MouseEvent` 构造函数，用于新建一个 `MouseEvent` 实例。

```
var event = new MouseEvent(type, options);
```

`MouseEvent` 构造函数接受两个参数。第一个参数是字符串，表示事件名称；第二个参数是一个事件配置对象，该参数可选。除了 `Event` 接口的实例配置属性，该对象可以配置以下属性，所有属性都是可选的。

下面是一个例子。

```js
function simulateClick() {
  var event = new MouseEvent('click', {
    'bubbles': true,
    'cancelable': true
  });
  var cb = document.getElementById('checkbox');
  cb.dispatchEvent(event);
}
```

上面代码生成一个鼠标点击事件，并触发该事件。

## MouseEvent 接口的实例属性

### altKey，ctrlKey，metaKey，shiftKey

`MouseEvent.altKey`、`MouseEvent.ctrlKey`、`MouseEvent.metaKey`、`MouseEvent.shiftKey` 这四个属性都返回一个布尔值，表示事件发生时，是否按下对应的键。它们都是只读属性。

+ `altKey` 属性：Alt 键
+ `ctrlKey` 属性：Ctrl 键
+ `metaKey` 属性：Meta 键（Mac 键盘是一个四瓣的小花，Windows 键盘是 Windows 键）
+ `shiftKey` 属性：Shift 键

### button，buttons

`MouseEvent.button` 属性返回一个数值，表示事件发生时按下了鼠标的哪个键。该属性只读。

- 0：按下主键（通常是左键），或者该事件没有初始化这个属性（比如 `mousemove` 事件）。
- 1：按下辅助键（通常是中键或者滚轮键）。
- 2：按下次键（通常是右键）。

`MouseEvent.buttons` 属性返回一个三个比特位的值，表示同时按下了哪些键。它用来处理同时按下多个鼠标键的情况。该属性只读。

- 1：二进制为 `001`（十进制的 1），表示按下左键。
- 2：二进制为 `010`（十进制的 2），表示按下右键。
- 4：二进制为 `100`（十进制的 4），表示按下中键或滚轮键。

同时按下多个键的时候，每个按下的键对应的比特位都会有值。比如，同时按下左键和右键，会返回 3（二进制为 011）。

### screenX，screenY

`MouseEvent.screenX` 属性返回鼠标位置相对于屏幕左上角的水平坐标（单位像素）

`MouseEvent.screenY` 属性返回垂直坐标。这两个属性都是只读属性。

### movementX，movementY

`MouseEvent.movementX` 属性返回当前位置与上一个 `mousemove` 事件之间的水平距离（单位像素）。数值上，它等于下面的计算公式。

```
currentEvent.movementX = currentEvent.screenX - previousEvent.screenX
```

`MouseEvent.movementY` 属性返回当前位置与上一个 `mousemove` 事件之间的垂直距离（单位像素）。数值上，它等于下面的计算公式。

```
currentEvent.movementY = currentEvent.screenY - previousEvent.screenY。
```

这两个属性都是只读属性。

### clientX，clientY

`MouseEvent.clientX` 只读

- 鼠标指针在点击元素（DOM）中的 X 坐标
- clientX 事件触发时鼠标的 x 坐标（始终相对于浏览器窗口 0,0，滚动条滚下去了，那么就 0,0 就跟着变到下面）
- 简写：`MouseEvent.X`

`MouseEvent.clientY` 只读

- 鼠标指针在点击元素（DOM）中的 Y 坐标
- 简写：`MouseEvent.Y`

### pageX，pageY

[`MouseEvent.pageX`](https://developer.mozilla.org/zh-CN/docs/Web/API/MouseEvent/pageX) 只读（IE9）

- 鼠标指针相对于整个文档的 X 坐标；
- 滚动条滚下去了，那么就 0,0 还是在上面

[`MouseEvent.pageY`](https://developer.mozilla.org/zh-CN/docs/Web/API/MouseEvent/pageY) 只读（IE9）

- 鼠标指针相对于整个文档的 Y 坐标；

```js
/* HTML 代码如下
  <style>
    body {
      height: 2000px;
    }
  </style>
*/
document.body.addEventListener(
  'click',
  function (e) {
    console.log(e.pageX);
    console.log(e.pageY);
  },
  false
);
```

上面代码中，页面高度为 2000 像素，会产生垂直滚动条。滚动到页面底部，点击鼠标输出的 `pageY` 值会接近 2000。

### offsetX，offsetY

`MouseEvent.offsetX` 属性返回鼠标位置与目标节点左侧的 `padding` 边缘的水平距离（单位像素）

`MouseEvent.offsetY` 属性返回与目标节点上方的 `padding` 边缘的垂直距离。这两个属性都是只读属性。

### relatedTarget

`MouseEvent.relatedTarget` 属性返回事件的相关节点。对于那些没有相关节点的事件，该属性返回 `null`。该属性只读。

下表列出不同事件的 `target` 属性值和 `relatedTarget` 属性值义。

| 事件名称   | target 属性    | relatedTarget 属性 |
| :--------- | :------------- | :----------------- |
| focusin    | 接受焦点的节点 | 丧失焦点的节点     |
| focusout   | 丧失焦点的节点 | 接受焦点的节点     |
| mouseenter | 将要进入的节点 | 将要离开的节点     |
| mouseleave | 将要离开的节点 | 将要进入的节点     |
| mouseout   | 将要离开的节点 | 将要进入的节点     |
| mouseover  | 将要进入的节点 | 将要离开的节点     |
| dragenter  | 将要进入的节点 | 将要离开的节点     |
| dragexit   | 将要离开的节点 | 将要进入的节点     |

```js
/*
  HTML 代码如下
  <div id="outer" style="height:50px;width:50px;border-width:1px solid black;">
    <div id="inner" style="height:25px;width:25px;border:1px solid black;"></div>
  </div>
*/

var inner = document.getElementById('inner');
inner.addEventListener('mouseover', function (event) {
  console.log('进入' + event.target.id + ' 离开' + event.relatedTarget.id);
}, false);
inner.addEventListener('mouseenter', function (event) {
  console.log('进入' + event.target.id + ' 离开' + event.relatedTarget.id);
});
inner.addEventListener('mouseout', function () {
  console.log('离开' + event.target.id + ' 进入' + event.relatedTarget.id);
});
inner.addEventListener("mouseleave", function (){
  console.log('离开' + event.target.id + ' 进入' + event.relatedTarget.id);
});

// 鼠标从 outer 进入inner，输出
// 进入inner 离开outer
// 进入inner 离开outer

// 鼠标从 inner进入 outer，输出
// 离开inner 进入outer
// 离开inner 进入outer
```

## MouseEvent 接口的实例方法

### getModifierState()

`MouseEvent.getModifierState` 方法返回一个布尔值，表示有没有按下特定的功能键。它的参数是一个表示 [功能键](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/getModifierState#Modifier_keys_on_Gecko) 的字符串。

```js
document.addEventListener('click', function (e) {
  console.log(e.getModifierState('CapsLock'));
}, false);
```

上面的代码可以了解用户是否按下了大写键。

## WheelEvent 接口

### **非标准滚轮事件**

onmousewheel（DOM0）

chrome、ie 都支持，火狐不兼容该特性

火狐中可以使用：DOMMouseScroll，该事件需要通过 addEventListenner() 方法（DOM2）

```js
bind(box1,"DOMMouseScroll",box1.onmousewheel);
//把其他浏览器的函数作为回调函数传入火狐的函数中，视作普通函数对象进行调用
```

event.wheelDelta

- 向上滚是 120；向下滚是 -120，速度过快可能会出现 240
- 不用看大小，只看正负即可
- 火狐不支持，火狐用的是 event.detail
  - 向上滚 -3，向下滚是 3，速度过快可能会 6

### 概述

WheelEvent 接口继承了 MouseEvent 实例，代表鼠标滚轮事件的实例对象。目前，鼠标滚轮相关的事件只有一个 `wheel` 事件，用户滚动鼠标的滚轮，就生成这个事件的实例。

**该事件为标准规定的事件接口。**早期的浏览器实现过 [`MouseWheelEvent`](https://developer.mozilla.org/zh-CN/docs/Web/API/MouseWheelEvent) 和 [`MouseScrollEvent`](https://developer.mozilla.org/zh-CN/docs/Web/API/MouseScrollEvent) 两种滚轮事件接口，但这两种接口皆非标准，加之各浏览器间对其兼容性极差。因而开发者应用该标准事件接口取代这两个非标准接口。

**注意事项：** 请勿想当然依据滚轮方向（即该事件的各 delta 属性值）来推断文档内容的滚动方向，因标准未定义滚轮事件具体会引发什么样的行为，引发的行为实际上是各浏览器自行定义的。即便滚轮事件引发了文档内容的滚动行为，也不表示滚轮方向和文档内容的滚动方向一定相同。因而通过该滚轮事件获知文档内容滚动方向的方法并不可靠。要获取文档内容的滚动方向，可在文档内容滚动事件（`scroll`）中监视 [`scrollLeft`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollLeft) 和 [`scrollTop`](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollTop) 二值变化情况，即可推断出滚动方向了。

### WheelEvent()

浏览器原生提供 `WheelEvent()` 构造函数，用来生成 `WheelEvent` 实例。

```
var wheelEvent = new WheelEvent(type, options);
```

`WheelEvent()` 构造函数可以接受两个参数，第一个是字符串，表示事件类型，对于滚轮事件来说，这个值目前只能是 `wheel`。第二个参数是事件的配置对象。该对象的属性除了 `Event`、`UIEvent` 的配置属性以外，还可以接受以下几个属性，所有属性都是可选的。

- `deltaX`：数值，表示滚轮的水平滚动量，默认值是 0.0。
- `deltaY`：数值，表示滚轮的垂直滚动量，默认值是 0.0。
- `deltaZ`：数值，表示滚轮的 Z 轴滚动量，默认值是 0.0。
- `deltaMode`：数值，表示相关的滚动事件的单位，适用于上面三个属性。`0` 表示滚动单位为像素，`1` 表示单位为行，`2` 表示单位为页，默认为 `0`。

### 实例属性

`WheelEvent` 事件实例除了具有 `Event` 和 `MouseEvent` 的实例属性和实例方法，还有一些自己的实例属性，但是没有自己的实例方法。

下面的属性都是只读属性。

- `WheelEvent.deltaX`：数值，表示滚轮的水平滚动量。
- `WheelEvent.deltaY`：数值，表示滚轮的垂直滚动量。
- `WheelEvent.deltaZ`：数值，表示滚轮的 Z 轴滚动量。
- `WheelEvent.deltaMode`：数值，表示上面三个属性的单位，`0` 是像素，`1` 是行，`2` 是页。

### 默认行为

当滚轮滚动时，如果浏览器有滚动条，滚动条会随之滚动，

这是浏览器的默认行为，如果不希望发生，则可以取消默认行为

return false;

使用 addEventListener() 方法绑定的函数，不能使用 return false 取消默认行为，需要使用 event.preventDefault();

## 实践

pointer-events:none, 代表\<a>标签上的事件都取消了, cursor 属性自然也没有了

pointer-events 这个 CSS3 属性,对浏览器的兼容并不好 IE11

### **滚轮事件**

- onmousewheel
- chrome、ie 都支持，火狐不兼容该特性
- 火狐中可以使用：DOMMouseScroll，该事件需要通过 addEventListenner() 方法

```js
bind(box1,"DOMMouseScroll",box1.onmousewheel);
//把其他浏览器的函数作为回调函数传入火狐的函数中，视作普通函数对象进行调用

PubSub.subscribe("deleComment",(msg,index)=>{
  this.deleteComment(index)
})

deleteComment(index){}

//可以简写，因为第二个参数接受一个回调函数，这个回调函数有两个参数，第一个是msg，第二个是index，
//如果deleteComment也设计成这样（msg，index），其实就是和上面那种麻烦的形式是一样的
PubSub.subscribe("deleComment",deleteComment)

deleteComment(msg,index){}
```

- event.wheelDelta
    - 向上滚是 120；向下滚是 -120
    - 不用看大小，只看正负即可
    - 火狐不支持，火狐用的是 event.detail
        - 向上滚 -3，向下滚是 3

# KeyboardEvent

## 键盘事件的种类 @@@

键盘事件由用户击打键盘触发，主要有 `keydown`、`keypress`、`keyup` 三个事件，它们都继承了 `KeyboardEvent` 接口。

- `keydown`：按下键盘时触发。
- `keypress`：按下有值的键时触发，即按下 Ctrl、Alt、Shift、Meta 这样无值的键，这个事件不会触发。对于有值的键，按下时先触发 `keydown` 事件，再触发这个事件。
- `keyup`：松开键盘时触发该事件。

如果用户一直按键不松开，就会连续触发键盘事件，触发的顺序如下。

1. keydown
2. keypress
3. keydown
4. keypress
5. ...（重复以上过程）
6. keyup

## KeyboardEvent 接口概述

`KeyboardEvent` 接口用来描述用户与键盘的互动。这个接口继承了 `Event` 接口，并且定义了自己的实例属性和实例方法。

### KeyboardEvent()

浏览器原生提供 `KeyboardEvent` 构造函数，用来新建键盘事件的实例。

```
new KeyboardEvent(type, options)
```

`KeyboardEvent` 构造函数接受两个参数。第一个参数是字符串，表示事件类型；第二个参数是一个事件配置对象，该参数可选。除了 `Event` 接口提供的属性，还可以配置以下字段，它们都是可选。

- `key`：字符串，当前按下的键，默认为空字符串。
- `code`：字符串，表示当前按下的键的字符串形式，默认为空字符串。
- `location`：整数，当前按下的键的位置，默认为 `0`。
- `ctrlKey`：布尔值，是否按下 Ctrl 键，默认为 `false`。
- `shiftKey`：布尔值，是否按下 Shift 键，默认为 `false`。
- `altKey`：布尔值，是否按下 Alt 键，默认为 `false`。
- `metaKey`：布尔值，是否按下 Meta 键，默认为 `false`。
- `repeat`：布尔值，是否重复按键，默认为 `false`。

## KeyboardEvent 的实例属性

### altKey，ctrlKey，metaKey，shiftKey

以下属性都是只读属性，返回一个布尔值，表示是否按下对应的键。

- `KeyboardEvent.altKey`：是否按下 Alt 键
- `KeyboardEvent.ctrlKey`：是否按下 Ctrl 键
- `KeyboardEvent.metaKey`：是否按下 meta 键（Mac 系统是一个四瓣的小花，Windows 系统是 windows 键）
- `KeyboardEvent.shiftKey`：是否按下 Shift 键

下面是一个示例

```js
function showChar(e) {
  console.log('ALT: ' + e.altKey);
  console.log('CTRL: ' + e.ctrlKey);
  console.log('Meta: ' + e.metaKey);
  console.log('Shift: ' + e.shiftKey);
}

document.body.addEventListener('keydown', showChar, false);
```

### KeyboardEvent.code

`KeyboardEvent.code` 属性返回一个字符串，表示当前按下的键的字符串形式。该属性只读。

下面是一些常用键的字符串形式，其他键请查 [文档](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/code#Code_values)。

- 数字键 0 - 9：返回 `digital0` - `digital9`
- 字母键 A - z：返回 `KeyA` - `KeyZ`
- 功能键 F1 - F12：返回 `F1` - `F12`
- 方向键：返回 `ArrowDown`、`ArrowUp`、`ArrowLeft`、`ArrowRight`
- Alt 键：返回 `AltLeft` 或 `AltRight`
- Shift 键：返回 `ShiftLeft` 或 `ShiftRight`
- Ctrl 键：返回 `ControlLeft` 或 `ControlRight`

### KeyboardEvent.key

`KeyboardEvent.key` 属性返回一个字符串，表示按下的键名。该属性只读。

+ 如果按下的键代表可打印字符，则返回这个字符，比如数字、字母。
+ 如果按下的键代表不可打印的特殊字符，则返回预定义的键值，比如 Backspace，Tab，Enter，Shift，Control，Alt，CapsLock，Esc，Spacebar，PageUp，PageDown，End，Home，Left，Right，Up，Down，PrintScreen，Insert，Del，Win，F1～F12，NumLock，Scroll 等。
+ 如果同时按下一个控制键和一个符号键，则返回符号键的键名。比如，按下 Ctrl + a，则返回 `a`；按下 Shift + a，则返回大写的 `A`。
+ 如果无法识别键名，返回字符串 `Unidentified`。

### KeyboardEvent.location

`KeyboardEvent.location` 属性返回一个整数，表示按下的键处在键盘的哪一个区域。它可能取以下值。

- 0：处在键盘的主区域，或者无法判断处于哪一个区域。
- 1：处在键盘的左侧，只适用那些有两个位置的键（比如 Ctrl 和 Shift 键）。
- 2：处在键盘的右侧，只适用那些有两个位置的键（比如 Ctrl 和 Shift 键）。
- 3：处在数字小键盘。

### KeyboardEvent.repeat

`KeyboardEvent.repeat` 返回一个布尔值，代表该键是否被按着不放，以便判断是否重复这个键，即浏览器会持续触发 `keydown` 和 `keypress` 事件，直到用户松开手为止。

### KeyboardEvent.keyCode *

被废弃了

## KeyboardEvent 的实例方法

### KeyboardEvent.getModifierState()

`KeyboardEvent.getModifierState()` 方法返回一个布尔值，表示是否按下或激活指定的功能键。它的常用参数如下。

- `Alt`：Alt 键
- `CapsLock`：大写锁定键
- `Control`：Ctrl 键
- `Meta`：Meta 键
- `NumLock`：数字键盘开关键
- `Shift`：Shift 键

```js
if (
  event.getModifierState('Control') +
  event.getModifierState('Alt') +
  event.getModifierState('Meta') > 1
) {
  return;
}
```

上面代码表示，只要 `Control`、`Alt`、`Meta` 里面，同时按下任意两个或两个以上的键就返回。

# 进度事件

## 进度事件的种类

进度事件用来描述资源加载的进度，主要由 AJAX 请求、`<img>`、`<audio>`、`<video>`、`<style>`、`<link>` 等外部资源的加载触发，继承了 `ProgressEvent` 接口。它主要包含以下几种事件。

- `abort`：外部资源中止加载时（比如用户取消）触发。如果发生错误导致中止，不会触发该事件。
- `error`：由于错误导致外部资源无法加载时触发。
- `load`：外部资源加载成功时触发。
- `loadstart`：外部资源开始加载时触发。
- `loadend`：外部资源停止加载时触发，发生顺序排在 `error`、`abort`、`load` 等事件的后面。
- `progress`：外部资源加载过程中不断触发。
- `timeout`：加载超时时触发。

注意，除了资源下载，文件上传也存在这些事件。

**示例**

下面是一个例子。

```js
image.addEventListener('load', function (event) {
  image.classList.add('finished');
});

image.addEventListener('error', function (event) {
  image.style.display = 'none';
});
```

上面代码在图片元素加载完成后，为图片元素添加一个 `finished` 的 Class。如果加载失败，就把图片元素的样式设置为不显示。

有时候，图片加载会在脚本运行之前就完成，尤其是当脚本放置在网页底部的时候，因此有可能 `load` 和 `error` 事件的监听函数根本不会执行。所以，比较可靠的方式，是用 `complete` 属性先判断一下是否加载完成。

```js
function loaded() {
  // ...
}

if (image.complete) {
  loaded();
} else {
  image.addEventListener('load', loaded);
}
```

由于 DOM 的元素节点没有提供是否加载错误的属性，所以 `error` 事件的监听函数最好放在 `<img>` 元素的 HTML 代码中，这样才能保证发生加载错误时百分之百会执行。

```
<img src="/wrong/url" onerror="this.style.display='none';" />
```

`loadend` 事件的监听函数，可以用来取代 `abort` 事件、`load` 事件、`error` 事件的监听函数，因为它总是在这些事件之后发生。

```
req.addEventListener('loadend', loadEnd, false);

function loadEnd(e) {
  console.log('传输结束，成功失败未知');
}
```

`loadend` 事件本身不提供关于进度结束的原因，但可以用它来做所有加载结束场景都需要做的一些操作。

另外，`error` 事件有一个特殊的性质，就是不会冒泡。所以，子元素的 `error` 事件，不会触发父元素的 `error` 事件监听函数。

## ProgressEvent 接口

`ProgressEvent` 接口主要用来描述外部资源加载的进度，比如 AJAX 加载、`<img>`、`<video>`、`<style>`、`<link>` 等外部资源加载。进度相关的事件都继承了这个接口。

### ProgressEvent()

浏览器原生提供了 `ProgressEvent()` 构造函数，用来生成事件实例。

```
new ProgressEvent(type, options)
```

`ProgressEvent()` 构造函数接受两个参数。第一个参数是字符串，表示事件的类型，这个参数是必须的。第二个参数是一个配置对象，表示事件的属性，该参数可选。配置对象除了可以使用 `Event` 接口的配置属性，还可以使用下面的属性，所有这些属性都是可选的。

- `lengthComputable`：布尔值，表示加载的总量是否可以计算，默认是 `false`。
- `loaded`：整数，表示已经加载的量，默认是 `0`。
- `total`：整数，表示需要加载的总量，默认是 `0`。

`ProgressEvent` 具有对应的实例属性。

- `ProgressEvent.lengthComputable`
- `ProgressEvent.loaded`
- `ProgressEvent.total`

如果 `ProgressEvent.lengthComputable` 为 `false`，`ProgressEvent.total` 实际上是没有意义的。

**示例**

下面是一个例子。

```js
var p = new ProgressEvent('load', {
  lengthComputable: true,
  loaded: 30,
  total: 100,
});

document.body.addEventListener('load', function (e) {
  console.log('已经加载：' + (e.loaded / e.total) * 100 + '%');
});

document.body.dispatchEvent(p);
// 已经加载：30%
```

上面代码先构造一个 `load` 事件，抛出后被监听函数捕捉到。

下面是一个实际的例子。

```js
var xhr = new XMLHttpRequest();

xhr.addEventListener('progress', updateProgress, false);
xhr.addEventListener('load', transferComplete, false);
xhr.addEventListener('error', transferFailed, false);
xhr.addEventListener('abort', transferCanceled, false);

xhr.open();

function updateProgress(e) {
  if (e.lengthComputable) {
    var percentComplete = e.loaded / e.total;
  } else {
    console.log('不能计算进度');
  }
}

function transferComplete(e) {
  console.log('传输结束');
}

function transferFailed(evt) {
  console.log('传输过程中发生错误');
}

function transferCanceled(evt) {
  console.log('用户取消了传输');
}
```

上面是下载过程的进度事件，还存在上传过程的进度事件。这时所有监听函数都要放在 `XMLHttpRequest.upload` 对象上面。

```js
var xhr = new XMLHttpRequest();

xhr.upload.addEventListener('progress', updateProgress, false);
xhr.upload.addEventListener('load', transferComplete, false);
xhr.upload.addEventListener('error', transferFailed, false);
xhr.upload.addEventListener('abort', transferCanceled, false);

xhr.open();
```

# compositionstart

文本合成系统如 [input method editor](https://developer.mozilla.org/zh-CN/docs/Glossary/Input_method_editor)（即输入法编辑器）开始新的输入合成时会触发 **`compositionstart`** 事件。

例如，当用户使用拼音输入法开始输入汉字时，这个事件就会被触发。

此外还有 compositionupdate, 和 compositionend 事件

[compositionstart - Web API 接口参考 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/compositionstart_event)

这几个事件, 兼容性非常好, 可以放心使用

## 和 Change Input 的区别是什么?

# beforeInput

DOM 事件 **`beforeinput`** 在 [`<input>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/Input), [`<select>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/select) 或 [`<textarea>`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/textarea) 的值即将被修改前触发。这个事件也可以在 [`contenteditable`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement/contentEditable "contenteditable") 被设置为 `true` 的元素和打开 [`designMode`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/designMode "designMode") 后的任何元素上被触发。

In the case of `contenteditable` and `designMode`, the event target is the **editing host**. If these properties apply to multiple elements, the editing host is the nearest ancestor element whose parent isn't editable.

兼容性其实一般, 还是一个在实验状态的 api

相比于 keydown, 最主要的优势就是不需要判断是否是功能按键, 能在用户真正输入时才触发

# 自定义事件

自定义事件有三种方法,一种是使用 `new Event()`, 另一种是 `createEvent('CustomEvent')` , 另一种是 `new customEvent()`

## New Event()

```js
let btn = document.querySelector('#btn');
let ev = new Event('alert', {
    bubbles: true,    //事件是否冒泡;默认值false
    cancelable: true, //事件能否被取消;默认值false
    composed: false
});
btn.addEventListener('alert', function (event) {
    console.log(event.bubbles); //true
    console.log(event.cancelable); //true
    console.log(event.detail); //undefined
}, false);
btn.dispatchEvent(ev);
```

以及各个 Event 接口的构造函数

## createEvent('CustomEvent')

要创建自定义事件，可以调用 `createEvent('CustomEvent')`，返回的对象有 initCustomEvent 方法，接受以下四个参数:

- type: 字符串，表示触发的事件类型，如此处的 'alert'
- bubbles: 布尔值： 表示事件是否冒泡
- cancelable: 布尔值，表示事件是否可以取消
- detail: 任意值，保存在 event 对象的 detail 属性中

```js
let btn = document.querySelector('#btn');
let ev = btn.createEvent('CustomEvent');
ev.initCustomEvent('alert', true, true, 'button');
btn.addEventListener('alert', function (event) {
    console.log(event.bubbles); //true
    console.log(event.cancelable);//true
    console.log(event.detail); //button
}, false);
btn.dispatchEvent(ev);
```

## customEvent()(DOM4)

使用起来比 `createEvent('CustomEvent')` 更加方便

```js
var btn = document.querySelector('#btn');
/*
 * 第一个参数是事件类型
 * 第二个参数是一个对象
 */
var ev = new CustomEvent('alert', {
    bubbles: 'true',
    cancelable: 'true',
    detail: 'button'
});
btn.addEventListener('alert', function (event) {
    console.log(event.bubbles); //true
    console.log(event.cancelable);//true
    console.log(event.detail); //button
}, false);
btn.dispatchEvent(ev);
```

# FAQ

#faq/js

