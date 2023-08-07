---
{"dg-publish":true,"permalink":"/programming/font-end/field/editor/own-editor/"}
---


# 编辑器之间的内容同步

所有的富文本编辑器都是基于 HTML, 所以同步的基础就是 html. 只要能产出 html, 就可以同步

只要能控制产出的 html 就可以做到样式完全一致

百家号和微信公众号都是基于 Ueditor

# 菜单栏

## Icon / Dropdown / Select

## Hover Active 变色

## Hover Tooltip

## Undo Redo Disable

没有编辑之前, 就不能 undo

没有 undo 过就不能 redo

```ts
// Returns `true` if the undo command can be executed
editor.can().undo()
```

## Active-button

### Drop-down 统一替换成 Active-button

### Drop-down 选中态去除, 由 Active-button 控制

## 文本颜色

## Select

要把 active 态反显到 select-option, 光用 active-button 是不行的

光用 is-active 也可能不够用? 还得要获取到准确的状态才行, 但是如果能获取到准确的状态的话, 为什么还要使用 isActive 的形式呢? 毕竟有 6 个标题就要监听 6 个, 如果有 1000 个字体, 那就得监听 1000 次了, 还不如获取 state 然后赋值?

+ this.state, 一样是在检查所有, 感觉不好搞呀

最终还是用了监听的方案

## 点击菜单栏按钮编辑器不失焦

 在 onMouseDown 阻止默认行为即可

## Icon 也单独抽离一下

# 模板

## 基本需求

how to offer specific heading

模板切换失败的提示

切换模板时是直接清空的

## ~~PlanA: Support Attributes~~

`background-image: linear-gradient(to bottom, #fff 80%, rgba(255, 242, 218, .8) 0);`

需要对 style 做解析, 通过 schema 支持了, 才能成功复制

更多的样式和属性, 可以参考 line-height 和 text-align 提供支持, global attributes

一些多余的 span 节点要如何处理? 如果是图片的话同步会很困难? 要如何满足同步的需求呢? 完了, 只能想办法通过 schema 控制了

background-color

span widget triangle

background-image, font-weight, margin padding 等等属性, 在正常的编辑中是不需要的没有必要添加支持. 解藕逻辑, 所有的特殊样式都在 extension 内单独维护不影响正常的文本编辑

可以避免支持的 style class 无限膨胀, 导致混乱和无序

那个 span 三角形其实是可以被选中和删除的. 如果 widget 膨胀的话, 可能会遇到只想删除文本 但是却只能把 widget 也删除了的情况

## PlanB: All Node View

show in node-view

+ h1 view
+ h2 view
+ h3 view
+ p as template function not a node-view

output in plain html

insert template widget

如何保存 editable-content 内部输入的内容? 通过 content 和 0 占位符插入

### 注意 Shcema 的兼容性问题

需要提前考虑好, 假如以后有很多的 template 要如何命名

目前的想法是通过一个字段, 不同的 template 传入不同的值来指定 widget, widget 名字待定

每一个 extension + wrapper 都是一个单独的 widget. 每个 widget 都有一个单独的 id, 同时也是 extension name

+ widget id 要怎么取名呢? 得有一个规律才行, 因为 extension 的名字是不太好变更的, 需要更熟悉一点, 目前来看即使频繁变换名字, 问题也不大, 主要是 attribute 不能换, 换了就得做兼容.
+ 如果要做一千个控件, 就得起一千个名字, 还得保证这些名字不冲突
+ 如果名字是按照某种规律生成的, 必须保证这个函数对于相同的 widget 要永远返回相同的 name 然后还得兼容 attribute, 用一个 set 去保存, 也很难
+ data-widget="xxx"
+ 让字符串有规则, 比如 \${code}/\${widgetName}, 可以和 UE 讨论一下是否有这样的规则, 或者参考一下秀米编辑器
+ [秀米丨揭秘，原来图文模板都是这样被做出来的！_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1pa411U73m/?spm_id_from=333.999.0.0&vd_source=f8573a6196003ad3683f1c1a403d3431)
+ 这个问题直接抛给大家看看就行, 后面到底要不要做到这么复杂其实还没有确定下来.

widget 与模板之间没有绑定关系, 模板功能就是保存了一些 plainHTML 而已

# Image Extension

## 实现图片 Upload 功能

原生的 image extension, 仅仅是插入 img 元素, 不会上传到 server

我可以使用普通的方法上传图片, 点击一个按钮而已, 然后再把图片通过 editor 的 setImage 插入到文档中

使用 think 的 image 之前需要实现 wrapper, 才能渲染 san 组件

两种方案:

1. 一种是把逻辑放在 menu, 通过 input 伪装成按钮, 让用户插入图片, 之后调用 editor.command.setImage 插入图片即可
2. 另一种方案是把 input 放在 nodeView, 这样可以封装在 extension 里面作为 editor 的 extension 是更合理的方式, 因为 tiptap 理论上来说脱离了菜单也可以运作, 所以不应该把插入逻辑实现在菜单栏

因为我们的编辑器里 插入图片是在一级目录的, 如果不小心取消了, 直接点击工具栏再次插入就行, 没有必要在原地留下一个 dom, 提供给用户插入图片

## 插入多个 Img 而不是一个 imgList

每个 img 都需要是可编辑的, 所以得是单个 img node 才行, 尝试多次调用 setImage command

因为 handleChange 的触发时机问题, 所以需要避免多次插入才行.

还需要控制多个图片的顺序,

### 图片的插入顺序由谁决定?

目前在 mac 上无论怎么点选, 最终都是文件名的顺序

### 插入的图片后 光标的位置是在图片的下面

## 给图片加上一个浮动菜单

imgToolbar 层级应该在 menubar 上面, 不是层级问题, 而是被遮挡时应该出现在图片的其他位置

还是把 image 的 tool 放在 menu-bar 的下面

## Img 点击态的实现

ProseMirror-selectednode 是最合适的方案

如果 sotrage 是每个 extension 单独一份的话, 也是一个不错的选择

### Method Callee

handleSingleClick -> clickedLeaf -> updateSelection

今晚研究不出来就试试 storage 方案

sanNodeView selectNode add selected props, the best way

## 强制块级

```ts
	// 匹配嵌入到 p 标签中的 img
	{
		tag: 'p',
		priority: 1001,
		getAttrs(element) {
			const imgEl = (element as HTMLElement).querySelector('img');
			return imgEl !== null && null;
		},
	},
```


# 一行多图 / 图片横排

## 如流方案

图片是放在 card 里的, card 左右各有一个 span, 可以聚焦, 显示光标, 图片再放到 card 里面

除了纯文本都可以放在 card 里面

![](/img/user/programming/font-end/field/editor/own-editor/image-20230209172111711.png)

## Node-view-card

其实就是把左右光标的逻辑抽离到 card 上, 不和 image 耦合, 可以做成类似于 node-view-wrapper 的 san 组件

其实概念上和 node-view-wrap 类似

### 左右光标如何实现

span + contenteditable

### 点击左右两侧的空白, 聚焦到 Span 上

想到的最直接的方案就是 getBoundingClientRect, 当点在了外部的容器身上时, 判断一下在哪个位置

然后给父节点左右添加一下事件

最终方案: 加了两个 div, 充当热区

### 仅支持插入图片, 其他工具置灰

聚焦到 span 时, 通过 activeElement 和 selection.anchorNode 都不能正确的获取到当前聚焦的节点

只能是依赖 editor 了, 聚焦之后更新 editor 的属性, 让 menubar 获取到这个更新作为置灰依据

现在的关键点就是寻找一个合适的位置传递信息

最终方案: eventEmitter

#### 置灰逻辑放在哪里比较合适?

是放在 menu-bar 统一处理呢? 还是放在单独的组件内部处理?

各个组件自己监听事件, 考虑逻辑复用, 考虑 mixin decorator, 这样就太麻烦了, 还是在 menubar 统一处理吧

最终方案: menubar 统一通过 disabled 属性处理

### 拦截输入

一旦输入, 要拦截输入, 然后拆到上下两个空行上

如果基于 keydown 拦截就要通过按键还原用户的输入

[javascript - 限制input输入的方法（监听键盘事件） - 个人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000023543967)

如果基于 input ,现在还不知道怎么拦截

同时拦截 paste 事件就好了, 如果是正常输入就用 key 拦截, 如果是 paste 就从 paste 上拿就好了

[小tip: 如何让contenteditable元素只能输入纯文本 « 张鑫旭-鑫空间-鑫生活](https://www.zhangxinxu.com/wordpress/2016/01/contenteditable-plaintext-only/)

如何在拦截输入之后保证输入体验

通过隐藏 paragraph 保障输入体验, 左右两侧的 span 是光标动画, 用于迷惑用户

最终方案: 通过 onbeforeInput, 使用原生的 selection api 保证转移焦点的及时性. 如果使用 tiptap 提供的聚焦相关 api, 有点慢了

> 如果是 input , 光标无法通过方向键左右移动到外部

### TODO

#### 只是置灰了, 还没有真正禁用

#### Node-view-card 是 Editor 组件, 接受 Editor Props 做更完善的封装

完了, 那裁剪状态 还得取消图片的左右 span 点击

编辑器只读的时候, 左右光标也得禁用

#### 光标删除

通过 editor 的 commands 删除是最优先考虑的方案, 但是此时是聚焦在 span 上的, editor 的 commands 不太好使. 想要 editor 的 commands 生效需要先使用 focus 命令, 但是这会导致聚焦的位置无法控制

editor 的 commands 行不通, 那就只能使用 dom api 了, san 的 api 也是一个方案, 直接把节点 detach 掉就好了

如果是 sanNodeView 的 deleteNode 方案, 会有 js error , 导致生命周期不能完整销毁, 可能会有内存泄漏的问题.

现在只能使用 selection 方案了, 先用 dom selection , 然后用 editor deleteview

直接用 selection range 虽然可以删掉 node ,但是这个和用 dom api 删除是一个道理, 没有触发 nodeview 的生命周期, 可能还是会有内存泄漏

最后方案: 使用 node 的 pos 选中后再进行删除

#### 光标要如何移动到图片左右两侧的 Span 里?

这个方案聚焦比鼠标点击更可靠, 因为如果图片是占满一行的, 就没有地方可以靠鼠标点击命中了

可以退而求其次, 先实现点击 image 后, selection 态, 左右键, 上下键的先不实现, 再看看有没有更好的方法

如果下一行是 card, 也需要做特殊处理.

直接全局监听 arrowKey 就可以了

#### 热区鼠标样式改变

#### 拦截输入 判断空行

如果是空行就不插入了, 没必要, 有空再考虑

直接用 dom api 读取更好, prev 和 next 的逻辑可以保持一致

```ts
        // 如果上一行是文本, domAtPos 可以获取正确, 如果下一行是文本, nodeDOM 可以获取正确
        const targetPositionNode = isPrev
            ? editor.view.domAtPos(endPos).node
            : editor.view.nodeDOM(endPos);

        console.log('targetPositionNode: ', targetPositionNode);
        if (!targetPositionNode) {
            // 相当于一聚焦就失焦
            return;
        }
```

## uploadImage

### 点击上传图片 editor.focus 如何支持 Focus 到 Span

一种方案是不失焦, 但是点击其他元素, 浏览器一定会触发 span 的失焦, 而且失焦时的 event 相关 target 一定是 span 本身, 如何知道是点击了哪个元素导致失焦的呢? relatedTarget 失败, 不能判断工具栏的元素, 不知道为啥一定都是 null

如何点击其他元素, input 不失焦? 或者给 img icon 加一个 label, 点击了又会自动聚焦到自己这个 span 上

最终方案: 通过禁用 mousedown 的默认行为来阻止失焦

### Img Src 传递

整个流程都使用统一的 sourceList 来处理 图片列表数据

### deleteNode()

上传结束时触发, 文件浏览器点击取消时触发

shang chu aan jie shu 上传结束 shi 时 chu fa 触发, dian jiwen jian liu lan qi 文件浏览器 dian ji 点击 qu xiao 取消 shi 时 chu fa 触发

图片上传取消, 还是要 deleteNode

dom 判断 input files, santd 判断 fileList

### Uploader Wrapper 的位置

pos 是总是放在 0? 这样好像会有点问题, 就是上传完之后默认的行为都会从 0 这个位置开始, 所以改成插入到 pos, 职业会把节点插入到当前 imageNodeView 之前的一个位置

## Image-gallery

其实最基础的形态就是一个类似于 image-list 的组件, 插入的位置做一下变换就 ok 了

内部的 img 是否有必要是 node-view-image ?

+ 难点在于不清楚如何在 node-view 内部 插入 node-view
+ 不要插入 image-gallery , 而是插入多张 inline image, 但是 inlineImage 的问题又会无法避免
	+ 只允许 block 内容的话, 甚至无法编辑, prosemirror 最多只允许 在 block 里面插入 inline
	+ 想要在 block 内 insert block 级别, 就只能在 command 级别 insert html 了
	 + 如果要做成 inline img , 难点就是我不知道该在什么时候处理成 inline img , 什么时候是 block img 了.
  + 因为 block 级别 image 也是用 p 包裹的, 不是很好从 p 中 再获取 p 去解析 gallery, 现在越来越倾向于非 node-view 方案了

如果不是 node-view

+ 好处就是普通 img, 容易实现
+ 难点就在于拖拽的时候, 拖进去和拖出来可能都要分别处理好
+ 以及所有的 img attrs 都要存在单个 node-view 上, 对单个 img 的删除和添加都要转变成对 attrs 的操作

目前看来, inline image 也许是比较可行的方案, 通过 option 控制插入的是 inline 还是 block? 不行, 在 extension 注册的时候就确定了, 只能是创建一个新 extension

最终方案: 所有的 Image 都是 ImageGallery, 内部通过 sourceList 维护 imageList

### Image 和 imageGallery 合并

这样拖拽成行就只是 sortablejs 就可以支持了, 不需要做额外的操作

反正都是渲染成 p

### 一行多图等高比例如何实现

先统一高度, 然后统一缩放宽度和高度, 直到和容器宽度保持一致

还是想用 inline image, 尝试在各个 image 都设置通用的属性, 隔离, gallery 和 image 之间不需要互相感知

+ gallery 如何感知所有的 inline Image 都已经 attach ? 这样才能去做整体的缩放
+ 如果不感知, 又要如何支持呢? gallery 不知道什么时候需要去更新内部所有的 img 的宽高

因为这个原因只能是采用非 node-view 方案.

### 单张图片等宽

### 操作 Dom Vs 更新 San Data

操作 dom 的好处是预创建的 img 可以派上用场, 不用重复创建

如果是更新 data, 也还是要等 img 全部 loaded 了才能更新

因为要所有图片都 loaded 了才能计算出宽度占比和高度, 所以不存在需要 div 提前占位的问题, 无论是 dom 方案还是 data 方案都是全部 loaded 了再按照顺序排列.

那还是 dom 方案会更好一点

最终方案: setData

### 为什么这些很常见的 Image Component 逻辑要重复实现?

因为需要和 editor 有机结合 起来, 而且是 san 组件的形式

如果是 react 组件, 说不定已经有别人结合好的了

### 单张图片的点击态

点击之后根据事件, 拿到单张的点击态就可以了

hover 的时候, gallery 显示一个浅色的 border

根据 selected porps, 如果被 selected 了再做内部的点击态. 当 selected 为 false 时要重置所有的点击态.

拖拽结束之后应该选中当前的 node ,这样才能触发 selected false

的取消逻辑.

判断 node 是否被选中不能使用 src 属性, 因为用户有可能复制图片, 多张图片的 src 实是相同的

工具栏, 点击之后应该选中当前悬浮的图片.

### renderHTML

map list, add data-aspect-ratio data-width-percentage

需要包含 width percentage 才能在 C 端正确渲染. 但是宽度百分比需要 image load 事件完成才能计算, 这里是一个异步的过程. 如果一个数据异步更新了之后又要再次更新自己, 很容易造成死循环, 所以要把 widthPercentage 单独维护, 做成 computed sourceList 的类型.

aspectRatio 还是和 src 强绑定的属性, 所以还是放在 sourceList 里比较合适

img 设置 pointer event 为 null, 禁止用户长按复制图片

### Todo2

#### 考虑图片加载失败?

展示占位图

#### 裁剪更改了宽高比, 也需要更新到。sourceList 上

也可以不更新, 只是性能会差点

所以宽高比维护在 extension 内更加合适

但是还是得使用 nutralWidth 和 nutralHeight

一行多图不可以裁剪, 所以不需要考虑太复杂, 但是还是在裁剪之后保存一下宽高比

拖拽进来的图片, 只能是 img 自身, 不太可能包含 imageExtension 上的信息, 所以获取宽高比的操作还是得放在 iamgeExtension 内, 而不是提升到 attrs

## AddToImageGallery

有没有办法实现成异步的呢? 每次想要上传图片, 都需要创建一个 uploadder 然后通过 command 处理, 好麻烦, 像 add 这种场景, 最好是能一直在 imageGalleryWrapper 的上下文里

我发起一个 promise, 在 promise 内插入一个 imageUploader, 在 uploader 做出选择之后 resolve

把 resolve 和 reject 作为 attrs 传给 uploader

set 逻辑拆散到 uploader 里面挺好的, 毕竟不能放在 menubar 上

add 逻辑通过 promise 在 gallery 内部维护吧? 但是这样就会出现 uploader 入参不统一, 做成统一的其实也没啥?

因为只有 add 是可以选择 promise 路线的, 其他都是 insertContent 需要封装成 command 从而避免把逻辑耦合到 menubar

最终方案: 还是统一入参, 走 pos 路线吧, 插入图片也还是通过 pos 获取 node-view, 更新 sourceList 的形式来做

## addImageToGallery

### updateAttributes

要如何 update 呢? 能 push 么? 想要 push 需要先获取 attrs

先 selected 然后可以在外部通过 updateAtrributes 更新, 问题是外部要如何触发 push 逻辑呢?

在 san 组件内部更新 attrs? 那样需要先拿到 san 组件的实例, 但是现在 san 组件实例是藏在 nodeView 实例当中的, 好像并没有暴露出来, 通过 editor 的 getNode 获取到的 node 感觉好难使用

本身就是在 imge 内部发起的 upload, 为什么不直接返回 src。信息呢/ 这样 image 就可以在内部处理 addToImageGallery 的逻辑了, promise 路线

因为外部路线不好获取到当前的 list, 不好做 push 操作, 只能 set, 所以 promise 路线是比较好的选择的, 这个路线的问题就是有点怪, promise resolve 的地方太诡异了, 而且有可能用户上传多张图, 那其实和 promise 的预期并不一致

可以在当前的 image 内把 sourceList 传过去, 通过 updateAtrribute 从外部更新, sourceList 传过去, 那个 宽高比也得传, 然后再通过 attr 注入进来, 有没有可能宽高比会过时? 不可能, 同一个 src 宽高比一定是固定的, 所以 aspectratio 是一个固定的属性, 可以为 undefined 就完事了

新增的图片永远是单张的, 也就是说 sourceList 里只可能有一张图片是需要重新计算宽高比的.

保存的时候也保存一下宽高比, 这样重新显示就会好处理一点

## Sortable

1. 拖拽到其他 imageGallery 的时候不用预先插入
2. dragImage 永远是单张的 image

要根据 sourceList 的长度灵活的控制 core 的 dragHandle 是否生效

禁止 put , 自己用 over 控制

drag sortable 的交互其实是符合要求的, 虚位触发之后就直接落位了, 触发一下重排就好了

多个拖拽组之间的交互到底要怎么处理比较好呢?

+ 也许可以先克隆一份过去, 然后触发重排, 如果又移出去了呢?

拖拽组之间的交互还是对其如流, 从交互来看也是这样的效果? 因为我们有等高排列的要求, 一拖拽过去, 还没 drop 就生效, 其实不太符合预期的. 所以只能是不生效.

结论:

1. 单个拖拽之间可以用 sortablejs
2. 拖拽组之间的交互对其如流, 然后就会发现还不如全都自己写, 因为拖拽组之间的逻辑和内部的逻辑是一样的呀, 都是拖着一个, 在悬浮, 然后用动画营造虚位, 节点都是不动的.
3. sortablejs 则是还没 drop 就直接生效了

明天看看如流知识库是怎么处理的, 不然就用 ghostEl false 法, 再不行就自己写完事

sortablejs 为我提供了什么? 提供了 sort 和 threshold, 但是我还是要自己写 threshold 的逻辑才能显示位置

写完之后就不知道 sortablejs 的意义是啥了

### Container & Item

item 要通过 message 派发给 container ,如果是通过 冒泡, 有可能 开发者在传入的 img 上绑定了相应的事件, 那冒泡上去的 currentTarget 就会变成开发者的那个节点, 而不是 item, 只有 message 可以保障到了 container 可以操作的一定是 item

### 如何知道 Item 确实 Drop 了, 需要把 Item 删除?

现在看来是没有办法知道的, drop 的容器有可能是 editor, 甚至是 editor 外部的节点, 根本没办法知道最终 el 是否 drop 成功了, 就算知道他 drop 成功了, 如何通知 container 删除元素也是一个问题.

所以方案就只剩下一个了, 就是 dragStart 的时候直接把 imgEntityList 里的元素删除了, 然后不更新视图, 等到 drop 的时候更新 imgEntityList, dragEnd 之后刷新视图自然就正常了

结论: 通过 dragEnd 事件的 dropEffect 可以判断, 如果是 move 就认为是 drop 成功了, 如果是 none 就复原 imageEntity

可以让相同拖拽组, 和不同拖拽组走不一样的逻辑, 这样就知道该 move 还是该 add 的

### 如何避免 Item 的 dragEnter 和 dragLeave ?

pointer-event none 会导致 over 事件也不触发, 就无法执行 translate 逻辑了.

dragLevel

### sourceList 空了要删除自己

只有一张图片时不能销毁再重建 img, 因为图片第一次裁剪底图是 originSrc, 取消裁剪之后 src 还是 originSrc 没变, 这个时候触发了图片销毁之后, 因为 src 没变, 导致不会重新创建? 而且只有一张图点裁剪重新创建的话会闪烁一下

imgList 从 2 张拖拽变成只剩下一张, 这张图片的 index 应该更新为 0 的, 但是因为限制设置了 sourceList 只有 1 就不需要更新, 所以他的 index 还是原来的 index , 只是只有一张图片的 index 本身也是没有意义的, 所以更新了也没有用, 可能还特别难更新, 所以暂时不用管了.

但是这就导致通过 delete 删除的时候, 如果剩下两张图片, 拖拽走了一张, 剩下一张 index 错乱的图片, 就不能通过 updateSourceLIst 来删除 node-view 了, 只能直接调用 deleteNode

空数组触发 deleteNode 会导致 outside fragment 问题, 不清楚是什么原因.

### S-for 的 Index 会自己更新么?

sort 的时候, itemIndex 可以正确维护, 因为 san 只会更新 src, 所以 itemIndex 相当于不需要更新, 位置改变的 item 只是变更了 src 而已, itemIndex 不需要更新

必须要调整 dropin 的 item 到 padding item 之前 index 才能正确更新, 空的 sort-item 是否可以只是一个假想的 item ? 不需要真实存在? 因为它的存在导致了 item attach index 不好更新, 它最主要的作用是绑定 indicator, 所以必须存在

如果把空 item 放在开头, 就会导致所有的 itemIndex 都比真实的 entityList 里的 item index 大 1, 但是这样可以解决 dropin 导致的 index 无法刷新的问题, 因为 drop 进来的元素一定会比空 index 晚, 所以 index 可以正确刷新.

如果我依然想把空 item 放在末尾, 就需要解决 dropin item 晚进去, index 却要大于空 item 的问题, 他们实际插入的位置还是在空 item 之前的, 因为 slot 一定是在空 item 之前, 所以我只需要根据他们实际的 dom 位置更新 index 就行.

> 有一个解决方案就是 i 让 s-for 来指定 itemIndex, 但是这样好像会增加用户的使用成本, 而且会导致空 item 没有 item-index
> 还有一个方案就是更新的时候先销毁空 item ,插入完再重新创建, 这个方案的问题是无法感知 upload 形态的插入, 好像空 item 的存在导致 upload 插入的 itemIndex 一定是错误的, 而且无法感知更新, 每次 list 更新的时候销毁 空 item, 更新完重新创建

s-for 有时候会通过触发 detached 的方式来更新列表, 就算自己完全维护好 itemIndex 也不一定能处理好这个问题. dragLvel 一旦过早更新就特别容易复现.

不可能同时触发 item 的 datach 并且把 itemIndex 更新好的吧? 除非是我的代码起到的作用

sortable item index 的更新逻辑改成不依赖重建之后, 需要确定 s-for 的 index 能否正确更新.

因为 sortable-item 维护的是 itemIndex, 是拖拽排序的内部变量, 外部的 s-for 的 index 是点击态使用的

sort 要做 dom 的位移效果, 让 san update silent

```
这次的需求会对商品的图文详情做升级, 支持图文模板和一行多图, 升级之后C端会可以直接使用B端下发的html富文本渲染. 但是对于存量的商品之前C端会对下发的html富文本做改动, 比如把图片的宽度调整成和屏幕宽度一致. 所以这次升级需要C端对新旧数据做不同的处理. 目前想到几个方案:
1. B端和C端server都新增一个字段, 用于标识新的图文详情, C端前端根据该字段是否存在做不同的兼容处理 (长远来看这种方案比较合适, 可以考虑做成类似版本号的字段? 以后的需求升级都有可能造成兼容性问题)
2. C端前端解析下发的html,获取所有的 img, 根据 img 是否指定了 width 做出不同的处理. (新数据 img width 均为百分数)

主要是看下 server 支持方案1是否还来得及, 如果来不及本期可以先用方案2兼容, 后续再考虑方案 1 或者有更加合适的方案
```

### 流程梳理

itemAttach: dispatch message to container, push item to list, and maintainIndex

itemDragStart: set draggingItem, for calculate dragover index

containerDragEnter / containerDragLeave: maintain dragLevel, for item leave check

itemDragOver: getOverItem by e.currentTarget, with draggingItem, calculate the dropIndex

+ if dropIndex is null, make dropEffect to none, then on item dragEnd, we will know that is item drop or not

containerDrop: handle drop

itemDragEnd: according to dragLevel & dropEffect, determine drag type is dropout or sort

+ item sort: update sourceList
+ itemDropOut:
	+ sing image sourceList on dropout, no matter drop in editor or other gallery, new Image is create in right pos, delete origin Node
	+ multi image sourceList on dropout, update sourceList

### dropIndex 逻辑

在末尾加入一个空的 sortable-item, 这样所有的可插入位置都代表着一个 item 的 index, 都带着一个 indicator, 这样 translate 的逻辑和 indicator 的逻辑都是完美契合的了.

拖拽的时候隐藏 editor drag 的指示线, 每个 itemdd 后面都插入一个 indicator 这样就喝 dropIndex 保持一致了, 也不用计算位移. 但是这个逻辑目前只能写到 gallery 上, 得想个办法补充到 container 里. 要么是用 dom 要么是拆出去.

### 清理拖拽过程中的临时数据

对于 sort , dragEnd 和 drop 事件都可以认为是拖拽结束了, 有 dragItem

对于 dropout, droupout 的容器只有 dragItem 的 dragEnd 事件可以认为是结束了, 不会触发 drop 事件. dropIn 的容器只有 drop 事件可以认为是结束了, 没有 dragItem, 不会触发 dragEnd 事件.

结论: 如果有 dragItem, 就在 dragEnd 事件清理, 如果没有 dragItem 就在 drop 事件清理.

### 能否不使用 Editor 的 dragStart?

editor 的 dragStart 会先 clear dataTransfer 然后再设置自己的, 导致单张图片拖拽进入 gallery 时无法获取 ImageSource, 要么是做 parseHTML 要么是寻找其他方法

不行, drop 到 editor 上我无法监听 drop 事件, 这样 节点 dragEnd 之后就必须执行删除节点:

+ 单独的 gallery 先删除, 后自清理, 每次都是新的 nodeView
+ 多个 gallery 仅删除

如果使用 editor 的 dragstart, 不用自己删除原节点, nodeView 是进行了移动, 而不是重新创建? 也是重新创建的, 所以不如全都用我自己的 dragStart 这样 datatransfer 保持统一

需要去除 item 的 dragstart 这样 datatransfer 才能完全由外部控制, 而且传入的 html 还得是 tiptap 格式的, 否则无法兼容 editor 自身的处理.

所以就是要么传入 html 的时候 hack editor 的处理, 要么是接受 html 的时候, hack html 的处理.

因为 解析 html 的时候只有字符串, 如果不 attache 到页面上, 估计很难进行 querySelector 那样的读取信息, 而在传入的时候 hack 只需要记录一下 tiptap 的 hack html 就可以了.

无论是哪个时机解析, 都需要注意 tiptap 更新带来的影响, 有可能改变了 datatransfer 的 html 导致需要代码兼容.

发现就只是写入了 html

如果是自己的 dragStart 发现有 uri-list, html ,special

即是去掉了两个我自己写的 也依然是还有 uri-list 和 html ,冒泡关系和覆盖关系是怎么样的?

最终方案: 统一使用自定义的 dragStart, gallery 通过 imageSource, editor 自行通过 parseHTML 解析拖拽的 datatransfer 中的 html

### 新增 List Prop 和 List-update 事件

所有的处理都可以挪动到 container 内部, 挪完 再补充 index 更新逻辑

## dropCursor

dropCursor extension

## TODO

图片上传组件统一迁移

还有好多浏览器兼容性问题

系统模板, h2 h3 光标颜色改一下

okr-review

![own-editor](own-editor.md#TODO)

![own-editor](own-editor.md#Todo2)

html string 改成 nodes 形式, 性能可能会好点

工具栏, 点击之后应该选中当前悬浮的图片.

node-view gap cursor 和 dropcursor 优化, 既然 dropcursor 可以挪到 node-view 之间, 那 gap curosr 肯定也可以

extension priority

点击最顶部的空白容易触发尾部聚焦, 需要处理一下

imageSourceList 和 drag 逻辑可以封装到 image-gallery 内部, 外部的 index 只保留 updateAttribute 就可以了

container 其实是可以自己更新 index 的, 不依赖外部的逻辑, 优先度不高之后再实现吧. 参考 table column, 改成传一个数组, container 去帮忙更新? sort 时高度不变, 可以给一个高度撑着.

sys-template indent

# 图片复制

其他复制进来的图片应该也是类似的, 都在 imageExtension 解决

反正截图复制是在 paste, 那 word 的 html 解析也还是放在 paste 吧? 因为解析需要的信息是要从 paste 里面哪个的, 光是依靠 file 协议是不能获取到图片数据的

## 从 Word 复制图片

[富文本编辑器复制word文档中的图片_Jioho_的博客-CSDN博客_富文本编辑器哪些可以一键复制word图文](https://blog.csdn.net/Jioho_chen/article/details/119223556)

[paste-from-word/lib at main · ihwf/paste-from-word · GitHub](https://github.com/ihwf/paste-from-word/tree/main/lib)

### File 协议的问题

从 word 文档复制进来的内容的图片都是 `file:///` 协议，这时候如果我们的页面是 `http://` 或者 `https://` 协议的话，就不允许读取图片了。

除非页面也是本地文件打开的（但是实际项目中基本上是不可能的了）:

![](/img/user/programming/font-end/field/editor/own-editor/image-20230217111650298.png)

因为安全性: ckeditor 在怎么强大也不可能从 `http/https` 协议下的网址读取 `file:///` 的文件。原因也很简单，如果能读取的话，岂不是网站能把我们全部的资料都读到

### Word 文档内部的东西

word 文档其实只需要把后缀改为 `.zip`。然后打开对应的目录，你会发现图片就存在里面，而且 `word` 目录下还有一个 `webSettings.xml` 里面就存放着 word 文档的信息。感兴趣的就自己找一个看看把

 [[programming/font-end/primitive/browser-api/dom-clipboard\|../../primitive/browser-api/dom-clipboard]]

`clipdata.getData('text/html')` 也就是我们富文本用的方法，获取粘贴的内容的 html 代码

`clipdata.getData('text/rtf')` 获取的东西更加乱了，不过里面就记载着我们的图片信息（我的文档就 2 张图片，11mb.可怕）

### replaceImagesSourceWithBase64 方法

该方法在：`src/filters/image.js`

在 `replaceImagesSourceWithBase64` 函数中，和图片相关的方法是: `findAllImageElementsWithLocalSource` 查找全部的 file:/// 开头的图片

> createRangeIn、new Matcher、这些方法都不用太过于关注，因为复制进来的都是文本，这些可能是 ckeditor 核心代码中转换为 dom 节点的方法
> 我们直接粗暴点渲染为真实 dom，然后在操作真实 dom 就是了
> 第 12 行，获取 src 是 file:// 开头的 dom 节点

接着执行 `replaceImagesFileSourceWithInlineRepresentation` 方法。在这之前还会执行 `extractImageDataFromRtf`

### extractImageDataFromRtf 方法

同样是在 `src/filters/image.js`

这部分代码是把我们从剪贴板中 `getData('text/rtf')` 获取到的值做一个加工，提取里面的图片信息

> regexPictureHeader 这段正则中，在以前的时候还是可以用的，可能最近 rtf 又更新了，导致匹配失败，无法生成图片
于是进过一番探索，根据旧的正则自己删减了一部分匹配规则，进过测试 office 和 wps 都能识别。
旧的写法： `const regexPictureHeader = /{\pict[\s\S]+?\bliptag-?\d+(\blipupi-?\d+)?({\*\blipuid\s?[\da-fA-F]+)?[\s}]_?/;``
新的写法：`const regexPictureHeader = /{\pict[\s\S]+?({\*\blipuid\s?[\da-fA-F]+)[\s}]_/;`

```js
function extractImageDataFromRtf(rtfData) {
  if (!rtfData) {
    return []
  }

   // 旧的写法
  // const regexPictureHeader = /{\\pict[\s\S]+?\\bliptag-?\d+(\\blipupi-?\d+)?({\\\*\\blipuid\s?[\da-fA-F]+)?[\s}]*?/
  // 新删减后的写法
  const regexPictureHeader = /{\\pict[\s\S]+?({\\\*\\blipuid\s?[\da-fA-F]+)[\s}]*/
  const regexPicture = new RegExp('(?:(' + regexPictureHeader.source + '))([\\da-fA-F\\s]+)\\}', 'g')
  const images = rtfData.match(regexPicture)
  const result = []

  if (images) {
    for (const image of images) {
      let imageType = false

      if (image.includes('\\pngblip')) {
        imageType = 'image/png'
      } else if (image.includes('\\jpegblip')) {
        imageType = 'image/jpeg'
      }

      if (imageType) {
        result.push({
          hex: image.replace(regexPictureHeader, '').replace(/[^\da-fA-F]/g, ''),
          type: imageType
        })
      }
    }
  }

  return result
}

```

### replaceImagesFileSourceWithInlineRepresentation

传入的参数第一个是 src 为 `file://` 的图片节点数组，第二个从 rtf 提取的图片信息数组，第三个就是 ckeditor 自己的方法了，用来显示文本的，不用管他

还用到了一个 `_convertHexToBase64` 方法，把 hex 转换为 base64

接着就是一顿循环了，对应的节点替换为对应的 base64，设置到图片节点的的 src 上，只是这里他们用了自身封装的 `writer`。

```js
function replaceImagesFileSourceWithInlineRepresentation(imageElements, imagesHexSources, writer) {
  // Assume there is an equal amount of image elements and images HEX sources so they can be matched accordingly based on existing order.
  if (imageElements.length === imagesHexSources.length) {
    for (let i = 0; i < imageElements.length; i++) {
      const newSrc = `data:${imagesHexSources[i].type};base64,${_convertHexToBase64(imagesHexSources[i].hex)}`
      writer.setAttribute('src', newSrc, imageElements[i])
    }
  }
}

function _convertHexToBase64(hexString) {
  return btoa(
    hexString
      .match(/\w{2}/g)
      .map(char => {
        return String.fromCharCode(parseInt(char, 16))
      })
      .join('')
  )
}

```

## 动手实践，获取图片信息并展示

上面分析了一些 ckeditor 代码之后，其实我们要用的也就是

- `findAllImageElementsWithLocalSource`
    - 这个方法被改造了一下，直接读取实际的 dom 节点，拿到图片节点
- `replaceImagesFileSourceWithInlineRepresentation`
    - 这个方法在最后赋值的时候也改了下，因为我们已经记录了实际的 dom 节点，所以直接使用 **.setAttribute(‘src’,newSrc)**
- `extractImageDataFromRtf`
- `_convertHexToBase64`

整理过后的代码如下：

```js
window.addEventListener("paste", function (e) {
      const clipdata = e.clipboardData || window.clipboardData;
      document.querySelector('#preview').innerHTML = clipdata.getData("text/html")
      let rtf = clipdata.getData('text/rtf')

      let imgs = findAllImageElementsWithLocalSource()

      replaceImagesFileSourceWithInlineRepresentation(imgs, extractImageDataFromRtf(rtf))

    });

    function findAllImageElementsWithLocalSource() {
      let imgs = document.querySelectorAll('img')
      return imgs;
    }

    function extractImageDataFromRtf(rtfData) {
      if (!rtfData) {
        return [];
      }

       // 旧的写法
	  // const regexPictureHeader = /{\\pict[\s\S]+?\\bliptag-?\d+(\\blipupi-?\d+)?({\\\*\\blipuid\s?[\da-fA-F]+)?[\s}]*?/
	  // 新删减后的写法
	  const regexPictureHeader = /{\\pict[\s\S]+?({\\\*\\blipuid\s?[\da-fA-F]+)[\s}]*/
      const regexPicture = new RegExp('(?:(' + regexPictureHeader.source + '))([\\da-fA-F\\s]+)\\}', 'g');
      const images = rtfData.match(regexPicture);
      const result = [];

      if (images) {
        for (const image of images) {
          let imageType = false;

          if (image.includes('\\pngblip')) {
            imageType = 'image/png';
          } else if (image.includes('\\jpegblip')) {
            imageType = 'image/jpeg';
          }

          if (imageType) {
            result.push({
              hex: image.replace(regexPictureHeader, '').replace(/[^\da-fA-F]/g, ''),
              type: imageType
            });
          }
        }
      }

      return result;
    }

    function _convertHexToBase64(hexString) {
      return btoa(hexString.match(/\w{2}/g).map(char => {
        return String.fromCharCode(parseInt(char, 16));
      }).join(''));
    }

    function replaceImagesFileSourceWithInlineRepresentation(imageElements, imagesHexSources, writer) {
      // Assume there is an equal amount of image elements and images HEX sources so they can be matched accordingly based on existing order.
      if (imageElements.length === imagesHexSources.length) {
        for (let i = 0; i < imageElements.length; i++) {
          const newSrc = `data:${imagesHexSources[i].type};base64,${_convertHexToBase64(imagesHexSources[i].hex)}`;

          imageElements[i].setAttribute('src',newSrc)
        }
      }
    }
```

## Article

[How to upload or disable pasting images in Tiptap - Codemzy's Blog](https://www.codemzy.com/blog/tiptap-pasting-images)

# Draggable

给 element 绑定 tiptap 提供的 dragStartHandler, 给 element 设置 data-drag-handle 标明需要的元素

img 有多余的空行, 最好是想办法去掉

还是用 p 标签去匹配, 通过 contentELemnt 获取 imge 来判断是否可以搞定就行了

[Dropcursor – Tiptap Editor](https://tiptap.dev/api/extensions/dropcursor)

drag handler 只有一个, 通过 style 控制位移

拖砖移动的本质是, 剪贴当前的 node, 在 drop 的位置复制出来

## selectNodeByDom Padding

在 padding 中时如何正确的 select

只要 select 成功就会有 dragHandle, 没有说明失败了

在 padding 中 mousemove 时 eventTarget 是 root, 而不是具体那行的节点, 所以没有继续显示 drager,

为什么 think 上可以继续呢? 因为 react?

## nodeView 如何 Drag

activeNode 是可以捕捉到的, 看来是拖拽复制粘贴的过程除了问题

drag 成功了, 现在研究 copy & paste'

## Todo

padding 时就找不到 element 了

el 的样式有问题

其实不用在 mousemove 总是触发的, 更加常见的交互就是 hover 的

```css
[data-drag-handle] {
    position: absolute;
    z-index: 100;
    display: inline;
    width: 16px;
    height: 16px;
    cursor: move;
    // opacity: 0;
    transition: opacity 0.3s ease-out;
    // background-image: url("data:image/svg+xml;charset=utf-8,%3Csvg width='16' height='16' fill='none' xmlns='http://www.w3.org/2000/svg'%3E%3Crect x='3' y='1' width='3' height='3' rx='1.5' fill='%23111'/%3E%3Crect x='10' y='1' width='3' height='3' rx='1.5' fill='%23111'/%3E%3Crect x='3' y='6' width='3' height='3' rx='1.5' fill='%23111'/%3E%3Crect x='10' y='6' width='3' height='3' rx='1.5' fill='%23111'/%3E%3Crect x='3' y='11' width='3' height='3' rx='1.5' fill='%23111'/%3E%3Crect x='10' y='11' width='3' height='3' rx='1.5' fill='%23111'/%3E%3C/svg%3E");
    background-color: #000;
    background-size: contain;
    background-position: center 0;
    background-repeat: no-repeat;

    // &.show {
    //     opacity: 0.3;

    //     &:hover {
    //         opacity: 1;
    //     }
    // }

    // &.hide {
    //     opacity: 0;
    // }
}
```

# Copy & Paste

copy 没啥好说的, 就是 renderHTML

关键是对 paste 的剪贴板内容进行解析

[Copy or Drag and Drop multiple images to TipTap Editor for Vue | by Kailaash Balachandran | Medium](https://kailaash.medium.com/copy-or-drag-and-drop-multiple-images-to-tiptap-editor-for-vue-3bdce8bf7e38)

语雀如何自动将粘贴的内容识别为代码块？ https://www.zhihu.com/question/559921635/answer/2718482855

## 复制成行

paste 事件时使用相同的逻辑

先把 复制成行 做了吧, 完成之后就可以写方案了

针对 image 专门做处理, 所以还是给 image 的 copy 事件绑定一下比较好, 这样只需要判断 image 自身即可.

如果不绑定 copy 就需要从 clipboard 里面直接读取, 但是貌似浏览器不太让这样读? 所以其实可行的就只有上面的方案.

tiptap 自身的 copy & paste 已经足够好用了, selected image 然后复制啥的, 操作都非常流畅

在 image 内并不能监听到 copy 事件

[Is there a way to call cut, copy, paste, and select all from code? · ueberdosis/tiptap · Discussion #802 · GitHub](https://github.com/ueberdosis/tiptap/discussions/802)

监听 document 的 copy 事件感觉不太合适, 可能得监听 editor 的事事, 如果还是没有就不实现了

editor 的 copy 可以监听到, 但是这样的话, 每个 iamge 都会监听一遍, 感觉还是有点问题, 不太合适的

之后再考虑支持吧, 现在能做到插入到下一行已经不错了, 天然的就实现了 prev 插入到前方, next 插入到后方, 太强了

但是为了方案的完整性, 考虑一下实现拖拽成行吧

# Rest Feature List

可以复用的插件: 撤销, 字体, 字号, 加粗, 标题, 斜体, 下划线, 删除线, 文字颜色, 背景颜色, 分割线, 对齐方式, 缩进, 有序列表, 无序列表, 清除格式 - 2 天

- [x] 处理 nodeViewWrapper: 3 天
- [x] 编辑器模板部分 3 天
- [x] activeButton , type text type icon
- [x] select search
- [x] tooltip

## Trailing Node

gap cursor 可以在 img 前后生成一个可插入光标的位置

需要考虑文章的末尾.

https://tiptap.dev/experiments/trailing-node

https://gist.github.com/jelleroorda/2a3085f45ef75b9fdd9f76a4444d6bd6

addNewLine: https://stackoverflow.com/questions/65668815/tiptap-how-to-create-a-paragraph-p-on-shift-enter-instead-of-a-br

下面的链接都是 2019 年的讨论了, 都是 tiptap1 的内容

https://discuss.prosemirror.net/t/enforce-empty-trailing-block/2072

https://github.com/ueberdosis/tiptap/issues/143

## Line-height

参考 text-align 来做就可以了, 问题是在于视觉没有给, 可以先实现功能, 视觉也随便整一套, 后面再优化.

先整一个 font-size 就行

## Indent

需要好好学习和 review 一下

## Rest

- [x] 执行了 destory 就会解绑所有事件, 所以没有必要一个个 off, 关键是找时机 destory
- [x] 确认 active-button 事件监听的时机: editor change 的时候是否会重新绑定监听? 其实不会, 因为没有触发 san 的 attached 了, 只 attached 了一次
- [x] 空白区域的左右两侧点击聚焦, 在 firefox 上点击 margin 也会聚焦, 也许用 ProseMirror padding 更好
- [x] indent
- [x] 字数统计

# 遗留问题

## 可能的优化点

compress_size 同步到接口 url 上

把 image extension 挪到 operation 里去, 确实没必要放在 public, 下次处理吧.

[GitHub - TypeCellOS/BlockNote: A "Notion-style" block-based extensible text editor built on top of Prosemirror and Tiptap.](https://github.com/TypeCellOS/BlockNote)

图片上传之前压缩一下

我们的行高是固定的, 并不会根据字体大小变化

方向键 selected, 图片选中态

 [Rich text editor | Mantine](https://mantine.dev/others/tiptap/)

font-size line-height font-family 这种提升到最外面, 不要每个 p 标签都加, 可以不在编辑器家, 但是还是想预留好能力.

图片上传失败时的体验优化

widget 命名优化

menu-bar 层级改到高于 image tool bar

删除 nodeview 时为啥 Destroy 没有触发, 是否会存在内存泄漏

node-view attrs 类型优化, 没有地方可以传入范型, 只能是 extension 自己定义好 attrs 的类型, 然后在 san 组件指定好

清理 uploader node ,避免 拖拽时再次出发上传

文本自动换行, 不能无限输入

active-button ts 类型

indent 的 0px 是否有必要?

disabled Checker 逻辑不太对, 是不是应该 editor udpated 的时候触发 san 的 rerender ? 把这个逻辑写在 tiptap-san 里?

下划线和删除线互斥的问题, 同样需要确认, 在技术方案描述先不互斥, 后期有必要再处理

sanRender updateProps 这里是否需要 silent? 如果要加的话, 加在 contentComponent 上

active-button props 抽成对象, 看着更清晰一点, 不要直接传

在当前节点的下方插入一个节点: [javascript - tiptap - Insert node below / at the end of the current one - Stack Overflow](https://stackoverflow.com/questions/68146588/tiptap-insert-node-below-at-the-end-of-the-current-one)

extension priority 抽离到常量文件统一管理

开发规范总结:

1. 尽量使用 tiptap editor 提供的 api 来操作 node, 如果 tiptap editor 能力无法满足, 允许使用只读的 dom api 读取 dom 信息, 但是不建议使用 dom api 直接修改 editor 内的节点.
2. 与 renderHTML 和 parseHTML 相关的更新到 attrs, 再注入到 san 组件中. 如果是无关的, 就保存在 san data 内即可

## 编辑器能力

### Font-family List

### 分割线

~~额外实现, think 已有, horizontal rule~~

### 格式刷

### Paste & Copy

### 自动保存

## 菜单栏

更多的是写 san 组件

### Line-height

### Color Picker

编辑器背景颜色 rgb 为 0,0,0 时，无法通过拖动来改变数值

编辑器背景颜色中，在输入框拼音状态下输入内容会在图文详情中输入 miniapp-offline-2943

Color Picker Alpha

colorPicker 只作用于当前行和选中行 , 回车之后就不要再继续了

究竟是实现成可以任意输入 rgba, 还是给几个有限的选择, 需要 PM 定. 原生的 color-picker 因为依赖 textStyle 设置 inclusive 并不方便, 所以还是得提供无背景色的按钮, 需要另外实现 color-picker

[GitHub - mdbassit/Coloris: A lightweight and elegant JavaScript color picker. Written in vanilla ES6, no dependencies. Accessible.](https://github.com/mdbassit/Coloris)

[统一认证中心](https://console.cloud.baidu-int.com/devops/icafe/issue/miniapp-offline-2943/show?source=drawer-header)

[统一认证中心](https://console.cloud.baidu-int.com/devops/icafe/issue/miniapp-offline-2942/show?source=drawer-header)

## Img

width height 改成 option 传入, 支持 parse 的 image 也自动设置宽高

一些错误提示 , 需要拿到图片原本的宽高, 用于提示变形

> 不需要在 beforeUpload 的时候拿, 也不需要服务端返回, 可以直接通过 src 拿到 image 的属性

如果多个图片每个都报错, 应该是像 kibana 那种 tips 一样逐个堆叠, 顺序显示

@link image

tooltip 位置的问题, 果然还是应该实现成动态的, 不是层级问题, 看看有没有组件可以利用

+ 现在的 toolbar 是被 overflow 了
+ tooltip
+ 渲染到 body 上, 动态插入到当前的位置

图片上传的地址, 改成在 extension 配置

img options attribute default width height 究竟应该在什么时机指定?

+ extension 注册时
+ command 调用时

p>img 问题复盘, 不应该用 skip, 会导致空行消失, 可以利用这个特性做消除?

![own-editor](own-editor.md#图片的插入顺序由谁决定)

### 图片限制

图片大小的限制, 通过 upload 组件的 maxSize 实现

图片形变的处理, 貌似不用处理, 我直接用 img 元素限制它的宽高就行了

### 复制

复制自动上传

### 用 Selection Api 替代 Tiptap Focus

## Think Todo

~~placehoder & focus ~~

bubble menu

eslint disable 清理

## 小编的真实使用场景

push pm 配合检查编辑器的使用体验.

结合

参考如流文档的编辑器的保证基本的使用体验

先写一个 word 给老板审核, 再给视觉, 视觉出了图, 再上传

font-family 和 font-size 视觉确定一下

# 自测回归点即是 featureList
