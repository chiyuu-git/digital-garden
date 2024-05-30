---
{"aliases":[],"tags":[],"review-dates":["2023-04-25"],"dg-publish":true,"date-created":"2023-04-20-Thu, 3:16:58 pm","date-modified":"2024-01-16-Tue, 5:32:44 pm","permalink":"/programming/front-end/primitive/css/flex/","dgPassFrontmatter":true}
---


旧版 flex 更常用于移动端

postcss 后置处理器 自动处理 兼容性问题

# 一维布局模型

一维布局模型，简单讲就是，在 **主轴** 方向确定的情况下，只有 **行**，没有 **列**。

我们熟悉的二维布局模型有哪些呢？有 `display: table` 和 `display: grid`。而我们今天要讲的 `display: flex` 就是妥妥的一维布局模型了。

![img](/img/user/programming/front-end/primitive/css/flex/01.png)

可以看到，Flexbox 有 **行** 的概念，却没有 **列** 的概念。这导致它的表达能力是受限的。

## 概念

CSS3 弹性盒子 (Flexible Box 或 Flexbox)，是一种用于在页面上布置元素的布局模式，使得当页面布局必须适应不同的屏幕尺寸和不同的显示设备时，元素可预测地运行/列。对于许多应用程序，弹性盒子模型提供了对块模型的改进，因为它不使用浮动，flex 容器的边缘也不会与其内容的边缘折叠。

老版本的我们通常称之为 box, 没有形成规范, 只有 -webkit-box 属性

新版本的我们通常称之为 flex, 形成了规范, 可以用 -webkit-flex 和 flex

容器与项目

- flex container
- flex item

项目永远是在主轴上排列的

主轴与侧轴: 由 flex-direction/-webkit-box-orient 确定

有了新版本后，为什么还需要老版本？（新版本比老版本要强大的很多）

- 很多移动端浏览器内核版本都超低

注意，设为 Flex 布局以后，子元素的 float、clear 和 vertical-align 属性将失效。
{ #przfr4}


`display` 有两个和 Flexbox 相关的属性，分别是 `display: flex` 和 `display: inline-flex`。

对于容器内部的项目来说, 效果是一样的. 它们的区别在于容器自身应该以块元素还是行内元素的身份立命. 包括 `table` 也有这样的区分, 就不多讲了

## 对比

### Float

在元素自身上设置

当父元素宽度不够的时候：换行

![1548839240109|100](/img/user/programming/front-end/primitive/css/flex/1548839240109.png)

### display：-webkit-box

在父元素上设置

当父元素宽度不够的时候：溢出

![1548839283255|200](/img/user/programming/front-end/primitive/css/flex/1548839283255.png)

### display：-webkit-flex

当父元素宽度不够的时候：压缩

![1548839234030|150](/img/user/programming/front-end/primitive/css/flex/1548839234030.png)

# 容器

## Flex-direction: 容器的布局方向&容器排列方向

flex-direction: row;

- 与 x 轴平行的向量

flex-direction: column;

flex-direction:row-reverse;

- 同时指定了主轴，和主轴的方向
- 老版本需要两个属性，各两个属性值
- 新版本一个属性，4 个属性值

flex-direction:column-reverse;

## Justify-content: 富裕空间的管理（主轴）

初始值：justify-content: normal; 不可继承

>把所有项目当作一个整体, 所以是 content

### 属性值

flex-start

- 项目在容器的主轴的开始位置
- 表现为：富裕空间在主轴的结束位置

flex-end

- 项目在容器的主轴的结束位置
- 表现为：富裕空间在主轴的开始位置

center

- 项目在容器的主轴的中间位置
- 表现为：富裕空间在主轴的两边

space-between

- space 直接控制富裕空间的属性
- 富裕空间在项目之间

space-around(box 没有的)

- space 直接控制富裕空间的属性
- 富裕空间在项目两边（包围）

## Align-items: 富裕空间的管理（侧轴）

默认值：align-items: normal; 不可继承

- 默认值确实是 normal，但是 normal 的表现在项目没有高度的时候和 stretch 一致，在项目有高度的时候表现为 start，受到侧轴方向的影响
- 因此，一旦项目不设置高度，就会自动铺满容器的高度，实现等高布局

猜测：推出默认值 normal 的原因，就是为了避免项目的高度存在与否导致属性值的两个不同变化，用 normal 自身的特性就是如此来解释

### 属性值

flex-start

- 项目在容器的侧轴的开始位置
- 表现为：富裕空间在侧轴的结束位置

flex-end

- 项目在容器的侧轴的结束位置
- 表现为：富裕空间在侧轴的开始位置

center

- 项目在容器的侧轴的中间位置
- 表现为：富裕空间在侧轴的中间位置

baseline

- 项目按照基线（font）对齐

stretch

- 项目不能有高度属性
- 通过延伸项目的高度，表现为：富裕空间的被填充
- css2.1 的等高布局：高度无限，再负 margin，overflow:hidden

## Align-content

align-content 属性定义弹性容器的侧轴方向上有额外空间时，如何排布 **每一行/列**

- 当弹性容器只有一行/列时无作用
- 把每一行/每一列看做一个整体，管理的是 **每一行/列的富裕空间**
- 其实 align-items、-webkit-box-align 也是，只是只有一行罢了

默认值：normal 不可继承

### 属性值

normal

- 每一行之间、最后一行与侧轴的终点之间分配相同的富裕空间

flex-start

- 所有行/列从第一行/列开始，在侧轴起点开始填充。
- 接下来的每一行/列紧跟前一行/列。
- 第一行/列的侧轴起点边和容器的侧轴起点边对齐。
- 表现为：侧轴上的富裕空间在侧轴的结束位置

flex-end

- 所有行/列从最后一行/列开始，在侧轴末尾开始填充。
- 接下来的每一行/列紧跟后一行/列
- 最后一行/列的侧轴终点和容器的侧轴终点对齐。
- 表现为：侧轴上的富裕空间在侧轴的开始位置

center

- 所有行/列从中间一行/列开始，在侧轴的中心开始向两边填充。
- 每行/列互相紧挨
- 容器的侧轴起点边和第一行/列的距离相等于容器的侧轴终点边和最后一行/列的距离。
- 表现为：侧轴上的富裕空间，两边相等

space-between

- 所有行/列在容器中平均分布。相邻两行/列间距相等。
- 容器的侧轴起点边和终点边分别与第一行/列和最后一行/列的边对齐。
- 表现为：富裕空间在大项目之间

space-around

- 所有行/列在容器中平均分布，相邻两行/列间距相等。
- 容器的侧轴起点边和终点边分别与第一行/列和最后一行/列的距离是相邻两行/列间距的一半。
- 表现为：富裕空间在大项目两边

stretch

- 拉伸所有行/列来填满剩余空间。剩余空间平均的分配给每一行/列
- 同样的项目在侧轴方向上不能有高/宽
- 表现为：富裕空间的被填充

## Flex-wrap

新版本属性项目溢出会压缩项目，当定义了换行之后，侧轴的方向影响项换行之后的位置，需要新属性来控制侧轴的方向

flex-wrap 属性控制了容器为 **单行/列** 还是 **多行/列**。并且定义了 **侧轴的方向**，新行/列将沿侧轴方向堆砌。

默认值：nowrap 不可继承

**属性值**: nowrap | wrap | wrap-reverse

## Flex-flow

flex-direction 和 flex-wrap 的简写属性，同时定义主轴和侧轴以及方向

```css
flex-flow: column-reverse wrap;
```

## [Grid-gap](grid.md#Grid-gap)

也可以用于 flex 布局.

# 项目

## Order

order 属性规定了弹性容器中的可伸缩项目在布局时的顺序。元素按照 order 属性的值的 **增序** 进行布局。拥有相同 order 属性值的元素按照它们在源代码中出现的顺序进行布局

order 越大越后

![1548840687678](/img/user/programming/front-end/primitive/css/flex/1548840687678.png)

可以设置负数

默认值：0 不可继承

## Align-self

管理单个项目自己的富裕空间

align-self 会对齐当前 flex 行中的 flex 元素，并覆盖 align-items 的值.

如果任何 flex 元素的侧轴方向 margin 值设置为 auto，则会忽略 align-self。
{ #4tk93x}


默认值：auto 不可继承

### 属性值

auto: 设置为父元素的 align-items 值，如果该元素没有父元素的话，就设置为 normal。

normal: 有高度，表现为 stretch，没有高度表现为 start

flex-start: flex 元素会对齐到 侧轴（cross-axis） 的开始位置。

flex-end: flex 元素会对齐到 cross-axis 的结束位置。

center: flex 元素会对齐到 cross-axis 的中间位置，如果该元素的 cross-size 的尺寸大于 flex 容器，将在两个方向均等溢出。

baseline: 所有的 flex 元素会沿着基线对齐，

stretch: flex 元素将会基于容器的宽和高，按照自身 margin box 的 cross-size 拉伸

## Flex-grow

该属性来设置, 当父元素的宽度大于所有子元素的宽度的和时 (**即父元素有剩余空间**), 子元素如何分配父元素的剩余空间

**默认值**：0, 不会分配剩余空间, 也就是不会 grow

**不可继承**

**属性值**：\<number> 类型 负值非法 & Global values

**描述**

- 剩余空间的指的容器的 content，padding border 是不会被分配的，且不受 box-sizing 的影响 @@@

## Flex-shrink

该属性来设置, 当父元素的宽度小于所有子元素的宽度的和时（即子元素会超出父元素）, 子元素如何缩小自己的宽度的

**默认值**：1, 所以默认情况下溢出会收缩

**不可继承**

**属性值**：\<number> 类型 负值非法 & Global values

**描述**

- **项目溢出时选择收缩而不是溢出或者换行的真正原因**

    > 但是当项目收缩到 0 的时候，项目会选择溢出，因为不可能删除项目的，只能选择溢出了

- **只有当 flex-wrap 为 nowrap 的时候才有效**
- 当富余空间不够时，Flexbox 只会挤压弹性项目的 `content`，其余如 `padding` 、`margin` 和 `border` 完全不受影响。
- 同时弹性项目没有显式声明宽度的情况下，Flexbox 也不会挤压文字。**只会收缩空白的 content 部分**，同样不受 box-sizing 的影响

## Flex-basis

flex-basis 指定了 flex 元素在主轴方向上的初始大小

如果不使用 [`box-sizing`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/box-sizing) 改变盒模型的话，那么这个属性就决定了 flex 元素的内容盒（content-box）的尺寸。

**默认值** ：auto

**不可继承**

### 属性值

auto: 取项目的宽/高, \<width>，width 值可以是\<length>，该值也可以是一个相对于其父弹性盒容器主轴尺寸的 [`百分数`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/percentage) 。负值是不被允许的，默认为 0

> 如果指定为 0，项目的宽度会消失，除非有内容撑开
> length 类型的值，表现为 number + 长度单位，如果是 0，可以不加长度单位

keyword：

- fill
- content
- max-content
- min-content
- fit-content

## Flex-grow 计算

restSpace = (容器大小 - 所有项目 flex-basis 的总和)

单位扩展空间 = (restSpace / 所有项目 flex-grow 的总和)

每项伸缩大小 = (flex-basis + (单位扩展空间 * flex-grow))

> 当所有元素的 flex-grow 之和小于 1 的时候（注意是 1，也就是说每个元素的 flex-grow 都是一个小数如 0.2 这样的），上面式子中的 sum flex-grow 将会使用 1 来参与计算，而不论它们的和是多少。也就是说，当所有的元素的 flex-grow 之和小于 1 的时候，剩余空间不会全部分配给各个元素。
>
> 实际上用来分配的空间是 sum(flex-grow) / 1 * 剩余空间，这些用来分配的空间依然是按 flex-grow 的比例来分配。

### 实例

<iframe height="300" style="width: 100%;" scrolling="no" title="flex-grow" src="https://codepen.io/chiyu-git/embed/xxyqJNJ?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true"></iframe>

容器的宽度为 400px, 子项 1 的占用的基础空间 (flex-basis) 为 50px, 子项 2 占用的基础空间是 70px, 子项 3 占用基础空间是 100px, 剩余空间为 400-50-70-100 = 180px.

其中子项 1 的 flex-grow: 0(未设置默认为 0), 子项 2flex-grow: 2, 子项 3flex-grow: 1, 剩余空间分成 3 份, 子项 2 占 2 份 (120px), 子项 3 占 1 份 (60px).

所以 子项 1 真实的占用空间为: 50+0 = 50px， 子项 2 真实的占用空间为: 70+120 = 190px， 子项 3 真实的占用空间为: 100+60 = 160px。

## Flex-shrink 计算

~~每项 flex 收缩大小 = flex-basis - (flex-shrink / sum(flex-shrink) * 溢出的空间)~~

**上面的等式是错误的**

原因在于收缩的比例还会收到自身大小，也就是 flex-basis 的影响，每个项目的 flex-basis 都不一样，所以不可以约分。而 flex-grow 分配则不受 flex-basis 的影响

1. 计算收缩因子与基准值乘的总和

    - var arr1 = 每一项 flex-shrink * flex-basis 之和
    - 所以：当 flex-basis 设置为 0 的时候，flex-shrink 不会起作用，相当于每个项目都没有宽度，就不存在收缩了

2. 计算收缩因数

    - 收缩因数=（项目 flex-shrink * flex-basis）/ 第一步计算总和
    - var arr2 = (每一项 flex-shrink\*flex-basis) / arr1

3. 移除空间的计算

    - 移除空间= 项目收缩因数 x 负溢出的空间
    - var arr3 = b * 负溢出空间

## 总结

如果父级的空间足够：`flex-grow` 有效，`flex-shrink` 无效。

如果父级的空间不够：`flex-shrink` 有效，`flex-grow` 无效。

flex-grow 属性指定项目的 **拉伸因子**，默认时不拉伸，因此默认值为 0

flex-shrink 属性指定项目的的 **收缩因子**，默认是收缩，因此默认值为 1

实例：[https://github.com/junruchen/junruchen.github.io/wiki/CSS-Flex%E5%B8%83%E5%B1%80%E5%B8%B8%E8%A7%81%E5%BA%94%E7%94%A8](https://github.com/junruchen/junruchen.github.io/wiki/CSS-Flex布局常见应用)

## Flex 简写属性

flex-grow ; flex-shrink; flex-basis

flex-basis：0；flex-grow：1；

设置 flex-basis 为 0，相当于是把每个项目的宽度都清空了，然后设置 flex-grow 为 1，相当于是把富裕空间（此时是父元素的宽度）均分到 **该项目** 之上

| 简写形式     | 完整形式         |
| ------------ | ---------------- |
| 默认值       | flex : 0 1 auto; |
| flex : 1     | flex : 1 1 0%;   |
| flex: auto   | flex : 1 1 auto; |
| flex : none  | flex: 0 0 auto;  |
| flex : 0%    | flex : 1 1 0%;   |
| flex : 2 3   | flex : 2 3 0%;   |
| flex : 2 3px | flex : 2 1 3px;  |

注意：重点理解 flex:1 跟 flex:auto 的区别，本质上是 flex-basis 的理解。

flexbox 在分配剩余空间时，需要计算剩余空间的大小，这依赖于 flex-basis。

flex:1 时 flex-basis 是 0%，也就是元素就算设置了 width 也不会起作用，基准宽度为 0，元素的最终宽度是分配了剩余宽度后得到的尺寸。

flex:auto 时，flex-basis 是 auto，基准宽度为元素设置的 width，最终宽度等于基准宽度 + 剩余宽度分配后的宽度。

# Margin

参考: https://www.cnblogs.com/coco1s/p/10910588.html

## BFC 下 `margin: auto` 垂直方向无法居中元素的原因

查看 CSS 文档，原因如下，在 BFC 下：

If both margin-left and margin-right are auto, their used values are equal, causing horizontal centring.

> —CSS2 Visual formatting model details: 10.3.3

If margin-top, or margin-bottom are auto, their used value is 0.

> —[CSS2 Visual formatting model details: 10.6.3](https://www.w3.org/TR/CSS2/visudet.html#Computing_heights_and_margins)

在块格式化上下文中，如果 `margin-left` 和 `margin-right` 都是 auto，则它们的表达值相等，从而导致元素的水平居中。( 这里的计算值为元素剩余可用剩余空间的一半)

而如果 `margin-top` 和 `margin-bottom` 都是 auto，则他们的值都为 0，当然也就无法造成垂直方向上的居中。

## FFC 下 `margin: auto` 垂直方向可以居中元素的原因

本文暂且不谈 grid 布局，我们业务中需求中更多的可能是使用 flex 布局，下文将着重围绕 flex 上下文中自动 margin 的一些表现。

> 嗯，也有很多前端被戏称为 flex 工程师，什么布局都 flex 一把梭。

查看 CSS 文档，原因如下，在 `dispaly: flex` 下：

Prior to alignment via justify-content and align-self, any positive free space is distributed to auto margins in that dimension.

> [CSS Flexible Box Layout Module Level 1 -- 8.1. Aligning with auto margins](https://www.w3.org/TR/2018/CR-css-flexbox-1-20181119/#auto-margins)

**注意**: 在 flex 格式化上下文中，设置了 `margin: auto` 的元素，在通过 `justify-content` 和 `align-self` 进行对齐之前，任何正处于空闲的空间都会分配到该方向的自动 margin 中去

这里，很重要的一点是，margin auto 的生效不仅是水平方向，垂直方向也会自动去分配这个剩余空间。

Note: If free space is distributed to auto margins, the alignment properties will have no effect in that dimension because the margins will have stolen all the free space left over after flexing.

> [CSS Flexible Box Layout Module Level 1 -- 8.1. Aligning with auto margins](https://www.w3.org/TR/2018/CR-css-flexbox-1-20181119/#auto-margins)

**注意**: 如果任意方向上的可用空间分配给了该方向的自动 margin ，则对齐属性（justify-content/align-self）在该维度中不起作用，因为 margin 将在排布后窃取该纬度方向剩余的所有可用空间。

也就是使用了自动 margin 的 flex 子项目，它们父元素设置的 `justify-content` 以及它们本身的 `align-self` 将不再生效，也就是这里存在一个优先级的关系。

**注意**: 单个方向上的自动 margin 也非常有用，它的计算值为该方向上的剩余空间

## 使用 Margin 实现 Justify-content

### Space-around

<iframe height="300" style="width: 100%;" scrolling="no" title="margin auto 实现 flex 下的 space-around" src="https://codepen.io/chiyu-git/embed/rNqyZVd?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true"></iframe>

### Space-between

<iframe height="300" style="width: 100%;" scrolling="no" title="margin auto 实现 flex 下的 space-between" src="https://codepen.io/chiyu-git/embed/RwepYWb?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true"></iframe>

使用自动 margin 实现 flex 下的 `align-self: flex-start | flex-end | center`

## 实现不规则两端对齐布局

![ui-faq](programming/FAQ/ui-faq.md#盒子不规则对齐)

# -webkit-box

容器设置 display 为 -webkit-box

## -webkit-box-orient: 容器的布局方向（主轴是哪根）

-webkit-box-orient: horizontal;

- horizontal 是默认值，horizontal: 与 x 轴平行的向量

-webkit-box-orient: vertical;

- vertical: 与 y 轴平行的向量

**注意:**

- 项目永远是在主轴上排列的
- -webkit-box-orient 属性本质上确定了主轴是哪一根

## -webkit-box-direction: 容器排列方向（主轴的正方向）

-webkit-box-direction: normal;

- 默认值

-webkit-box-direction: reverse;

- 颠倒

**注意:**

- 项目永远是沿着主轴的正方向排列的→，改变的是主轴的方向，而 **不是项目排列的方向**
- -webkit-box-direction 属性本质上改变了主轴方向，**不会改变主轴是哪根；**
- 老板本，项目会溢出容器，不存在换行，所以侧轴的方向也不重要，因为侧轴上没有东西排列，所以没必要有对应的改变侧轴方向的属性
- 所以老版本 box 要实现换行，只能使用多个盒子

## -webkit-box-pack: 富裕空间的管理（主轴）

默认值：-webkit-box-pack:start; 不可继承

不会给项目区分配空间，只是确定富裕空间的位置

### 属性值

start

- 项目在容器的 x 轴（y）的开始位置
- 表现为：富裕空间在 x 轴（y）结束的位置

end

- 项目在容器的 x 轴（y）的结束位置
- 表现为：富裕空间在 x 轴（y）开始的位置

center

- 项目在容器的 x 轴（y）的中间位置
- 表现为：富裕空间在 x 轴（y）的两边

justify

- 仅作用于富裕空间的属性
- 富裕空间在项目之间

### 注意

老版本的富裕空间管理不受主轴方向的影响，因为即使不收主轴方向影响一样可以实现所有的布局，只不过是 start 变成了 end 而已

因为在老版本 **不存在换行** 所以没有问题

但是在 **涉及到换行**（新版 flex）的时候，虽然富裕空间的位置同样可以实现，但是如果想改变元素内部的排序，必须要新的管理侧轴的属性

例子：富裕空间在上方

`flex-wrap: wrap;align-content: flex-end;`

![1548839689386|150](/img/user/programming/front-end/primitive/css/flex/1548839689386.png)

`flex-wrap: wrap-reverse;align-content: flex-start;`

![1548839702976|150](/img/user/programming/front-end/primitive/css/flex/1548839702976.png)

## -webkit-box-align: 富裕空间的管理（侧轴）

默认值：-webkit-box-align:stretch；不可继承

- 默认值确实是 stretch，仅仅是因为设置了高度，导致属性失效,失效之后表现为 start，因为老版本无法控制侧轴的方向

不会给项目区分配空间，只是确定富裕空间的位置

### 属性值

start

- 项目在容器的 y 轴（x）的开始位置
- 表现为：富裕空间在 y 轴（x）结束的位置

end

- 项目在容器的 y 轴（x）的结束位置
- 表现为：富裕空间在 y 轴（x）开始的位置
- center 富裕空间在两边
- 项目在容器的 y 轴（x）的中间位置
- 表现为：富裕空间在 y 轴（x）的两边

baseline

- 项目按照基线（font）对齐

stretch

- 实现等高布局
- 项目不能有高度属性
- 通过延伸项目的高度，表现为：富裕空间的被填充
- css2.1 的等高布局：高度无限，再负 margin，overflow:hidden

## -webkit-box-flex: 弹性空间的管理

将主轴上的富裕空间按比例分配到各个项目上

注意容器的富裕空间管理的相关属性不会影响项目的宽高，而项目的弹性空间属性会直接影响项目的宽高

- 主轴与 x 轴平行，分配到项目的宽
- 主轴与 y 轴平行，分配到项目的高

-webkit-box-flex: 1;

弹性因子：为 1

默认值:0 不可继承

即可控制拉伸也可以控制收缩，用的是 flex-grow 的规则，所以可能会把元素给压缩到消失

负值无效

# Faq

#faq/ui

一般遇到等分、等比例的事情，都可以考虑 flex

做等分布局的语法糖

两列三列布局

固定的设固定值，可变的 flex-grow 为 1

## Flex Froggy

http://blog.xiaoboswift.com/flexbox/

## 为什么没有 Justify-items

如果你同时了解 `display: flex` 和 `display: grid`，也许你会发现它们都有 `xx-items` 和 `xx-content` 属性。但是别急，再进一步深究，发现 Flexbox 少了一个 `justify-items`。

我们先来阐述一下 `xx-items` 和 `xx-content` 作用范围有什么区别。

- `xx-items` 作用于项目，这意思很明朗。
- `xx-content` 又是作用于什么呢？肯定是比项目更大的单位，又联想 Flexbox 的 `align-content` 是作用于行，我们可以大胆猜测 `xx-content` 作用于行或者列。

`align` 用于定义行的对齐，`jusitfy` 用于定义列的对齐

> 行的对齐是以竖直的线为基准的，左对齐，右对齐
>
> 列的对齐...

点破到这里，大家应该有点眉目了吧？Flexbox 是一维布局模型，它根本没有列的概念。

你说不对呀，既然 Flexbox 没有列的概念，那不是应该没有 `justify-content` 属性，而应该有 `justify-items` 属性么？

话是这么说，我当初也是这么认为的。

但后来仔细想一想，Flexbox 的 `align-items` 声明的是一行内的项目如何垂直对齐，与之相对，`justify-items` 声明的就应该是一列内的项目如何水平对齐。好像更离谱。而如果将弹性容器一行内的每个项目都当做一列，`justify-content` 似乎就说的通了。

这就好比讨论螃蟹的螯到底是不是手一样，怎么都觉得别扭。只能找一个相对更合理的说法了。

提出这个问题没别的意思，只是想加深大家对 Flexbox 的理解。

## 几个空间概念对比

富裕空间：由项目的内容区？决定的

弹性空间：项目吸收了富裕空间之后整个项目的内容区？

可扩展空间：单个项目吸收多少富裕空间

可用空间：受 flex-basis 影响，和富裕空间不一样

## 几个 align-API 对比

[align-items和align-content的区别 - 掘金](https://juejin.cn/post/6844903911690600456)

align-items、align-self 和 aling-content，都是管理侧轴方向上的项目和富裕空间

如果是单行/单列 `flex-wrap:no-wrap`：则 align-items、align-self 生效，align-self 优先级更高，align-content 不起作用

如果是多行/多列 `flex-wrap:wrap`：则 align-content 生效, 侧轴上的剩余空间被 **均分** 到每一行, **行与行之间没有空隙**，整体对齐，align-items 在每一行中分别对齐，如果当每一行/列之间存在富裕空间时，align-self 生效，但是不能脱离该行/列的富裕空间

```css
width: 100px; //项目宽为50px
flex-wrap: wrap;
```

![1548841107511|200](/img/user/programming/front-end/primitive/css/flex/1548841107511.png) ![1548841110796](F:\OneDrive\JS\assets\1548841110796.png) （图 2 中的 1 开启了 align-self）

富裕空间的这种表现是因为 align-content 的默认值 normal

如果项目设置了高/宽，则 stretch 失效，表现为 normal，而不是默认值是 stretch

normal：每一行之间和最后一行与侧轴的终点之间分配相同的富裕空间

## Flex 1 能有效的占据剩余空间 但是却没有办法指定 Max-width 匹配 Flex 1 分配到的宽度

这个时候可以考虑使用 calc 动态计算出剩余的空间分配给 max-widht
