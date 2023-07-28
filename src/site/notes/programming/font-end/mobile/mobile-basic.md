---
{"dg-publish":true,"permalink":"/programming/font-end/mobile/mobile-basic/"}
---


# 基本概念

webApp

混合 App

native App

## 移动端浏览器

移动端浏览器大约有 30 多种，其中 20 多种处于边缘化的状态

### 内置浏览器

每部手机都有内置浏览器，这个浏览器属于设备的固件，通常由操作系统的厂商开发, 而且大多数内置浏览器都被紧密的集成到底层的操作系统中去了

所以：我们没有办法单独升级浏览器，只能借助于更新操作系统

安卓

  - 安卓 webkit
  - 三星 三星 webkit→三星 chrome
  - 华为 华为 webkit→华为 chrome
  - 小米 小米 webkit→小米 chrome
  - 中兴 中兴 webkit→中兴 chrome
  - 索尼 索尼 webkit→索尼 chrome

IOS - safari

### 可下载浏览器

在实践中，只有安卓才有可下载浏览器，因为 IOS 上不允许安装其他的渲染引擎，而且没有厂商会为其他小的平台生产可下载浏览器

可下载浏览器相比于内置浏览器有一个小优势，就是只要有了版本更新就可以更新，在国内可下载浏览器是会占据一定移动端浏览器份额的，一个常见的原因是这些浏览器提供了对当地社会更好的整合

QQ 浏览器 UC 浏览器

### Webview

webview 是独立程序，是留给 **原生应用** 的一个操作系统接口，用了内置浏览器很多底层的组件（渲染引擎）

ios 的操作系统默认不允许在它身上有多余的渲染引擎，因此其他浏览器想在 ios 上运行就必须使用 ios 的 webview

ios 端的 qq 浏览器和 uc 浏览器使用的都是 ios 的 webview

### 混合浏览器

- 省流量模式（代理浏览器）
- 非省流量模式（完备浏览器）

## 安卓

![1549002399159](/img/user/programming/font-end/mobile/mobile-basic/1549002399159.png)

![1549002625737](/img/user/programming/font-end/mobile/mobile-basic/1549002625737.png)

### 结论

使用内置浏览器调试

内置浏览器版本比较低，常常需要使用 webkit 前缀

# 像素&视口

参考：

https://blog.csdn.net/aiolos1111/article/details/51967744

https://github.com/kaola-fed/blog/issues/133

http://yunkus.com/physical-pixel-device-independent-pixels/

https://github.com/jawil/blog/issues/21

# 像素

像素只是一个呈像单位，并没有规定 1 像素等于多少厘米，**它是图像显示的基本单元，既不是一个确定的物理量，也不是一个点或者小方块，而是一个抽象概念**。所以在谈论像素时一定 **要清楚它的上下文**！一定要清楚它的上下文！一定要清楚它的上下文！

## 设备像素 (DP)

物理概念

- 含义：device pixel，指的是设备中使用的 **物理像素**(Physic pixel)
- 单位：px
- 相关性：
  - 同样一个设备上，每 1 个设备像素所代表的物理长度 (如英寸) 是固定不变的 (**即设备像素的绝对性**)
    - 在厂商制作设备的时候就已经确定
  - 不同的设备之间，每 1 个设备像素所代表的物理长度 (如英寸) 是可以变化的 (**即设备像素的相对性**)
    - 不同设备，设备像素点，大小不一定相同，高清屏，超清屏
- 父集：物理像素，在语义明确的时候，移动端所指的物理像素其实就是设备像素
- 例子：不同英寸大小的移动设备上，分辨率却相同的原因了————因为 **设备像素的相对性**

| 手机类型 | [华为荣耀8](https://www.baidu.com/s?wd=%E5%8D%8E%E4%B8%BA%E8%8D%A3%E8%80%808&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd) | IPhone6S Plus |
| -------- | ------------------------------------------------------------ | ------------- |
| 屏幕尺寸 | 5.2 英寸                                                      | 5.5 英寸      |
| 分辨率   | 1920x1080                                                    | 1920 x 1080   |
| PPI      | 423+                                                         | 401           |

反映显示屏的硬件条件，反映的就是显示屏内部 led 灯的数量，可以简单理解，一组三色 led 代表一个物理像素，当然根据屏幕物理属性以及处理 led 的方法不一样。强调这是物理的，因为这是一个纯硬件指标。比如我把屏幕锯了一半，物理像素就只有一半。

## 设备分辨率\\屏幕分辨率

物理概念

- 单位：设备分辨率的单位就是设备像素 (px)。
- 相关性：
  - 在同一个手机或电脑设备上，它的设备像素是固定的，这是厂商在出厂时就设置好了的————即一个设备的分辨率是固定不变的。
  - 所以可以调整电脑屏幕的分辨率 (手机就没这个功能！)？那只是你的眼睛在欺骗你，本质类似于放大
  - 注意，我们通常所说的 **显示器分辨率**，其实是指 **桌面设定的分辨率**，而不是显示器的 **物理分辨率**。只不过现在液晶显示器成为主流，由于液晶的显示原理与 `CRT` 不同，只有在桌面分辨率与物理分辨率一致的情况下，显示效果最佳，所以现在我们的桌面分辨率几乎总是与显示器的物理分辨率一致了。

## CSS 像素 (css pixel)

抽象概念

- 含义：指的是 CSS 样式代码中使用的逻辑像素
- 单位：px
- 相关性：
  - 在同样一个设备上，每 1 个 CSS 像素所代表的物理像素是可以变化的 (**即 CSS 像素的第一方面的相对性**);
    - 用户的缩放行为，当用户把页面放大一倍，那么 css 中 1px 所代表的物理像素也会增加一倍；反之把页面缩小一倍，css 中 1px 所代表的物理像素也会减少一倍。
    - 现代浏览器中实现缩放的方式都是「拉伸」像素：即每 CSS 像素单位代表的长度发生变化，而像素总数字不变。所以缩放会引起 CSS 像素的变化。
    - 变化的是 CSS 坐标系的单位长度
  - 在不同的设备之间，每 1 个 CSS 像素所代表的物理像素是可以变化的 (**即 CSS 像素的第二方面的相对性**);
    - 高清屏

## 屏幕尺寸

物理概念

含义：对角线的长度（英寸，1 英寸=2.54 厘米）

## DPI&PPI(像素密度)

物理概念

- 含义：DPI(dots per inch) 为打印机每英寸可以喷的墨汁点数，用于印刷行业中度量空间点的密度

  > 只要知道 **当用于描述显示器设备时 ppi 与 dpi 是同一个概念** 。

- 含义：PPI(pixels per inch) 为屏幕每英寸的物理像素数量 (即在一个对角线长度为 1 英寸的正方形内所拥有的物理像素数)，用于度量计算机显示屏上像素的密度

  > 注意：设备的英寸都是指的 **对角线**，所以不可以用 **边** 来计算 PPI

- 相关性：

  ![1555592021951](/img/user/programming/font-end/mobile/mobile-basic/1555592021951.png)

  - 由设备分辨率、设备尺寸决定，是一个固定值，
  - 每款设备的 PPI 都是固定的，绝对性
  - 不同的设备可以由不同的 PPI ，相对性

## DPR

- 含义：全称设备像素比 (Device Pixel Ratio）。在移动端浏览器中以及某些桌面浏览器中，window 对象有一个 devicePixelRatio 属性，它的官方的定义为：设备物理像素和设备独立像素的比例，也就是 devicePixelRatio = 物理像素 / 独立像素。
- 由上述公式计算出 ppi 是为了得到密度分界，获得 **默认缩放比例**，即设备像素比 DPR

在 120-160 之间的手机被归为低密度手机，160-240 被归为中密度，240-320 被归为高密度，320 以上被归为超高密度（Apple 给了它一个高大上的名字——Retina）

获取苹果的设备像素比比较简单：**先规定密度分界基数为 160，然后设备像素比=PPI 除以密度分界基数 160**

## DPR 与 PPI 的关系

综合上述安卓和苹果的密度分界图，可以得到的 DPR 与 PPI 的关系如下：**DPR = PPI/160**

这里求出的是 **默认的**DPR

## 设备像素与 CSS 像素之间的关系

获得设备像素比（dpr）后，便可得知设备像素与 CSS 像素之间的比例。当这个比率为 1:1 时，使用 1 个设备像素显示 1 个 CSS 像素。当这个比率为 2:1 时，使用 4 个设备像素显示 1 个 CSS 像素，当这个比率为 3:1 时，使用 9（3\*3）个设备像素显示 1 个 CSS 像素。

所以，有如下公式： **DPR = 设备像素/CSS 像素**

这里求出的是 **实时的**DPR

## 设备独立像素 (DIP)

含义：Device independent Pixel，设备独立像素（也叫 **密度无关** 像素，逻辑像素），可以认为是计算机坐标中的一个点，这个点代表一个可以由程序使用的虚拟像素，（比如 css 像素），然后由相关关系转换为物理像素。

简单地来说设备独立像素就是：独立于设备的用于逻辑上衡量像素的单位。

根据上述设备像素与 CSS 像素之间的关系、及 DPR 的官方定义，我们可以推断出：**CSS 像素 =设备独立像素 = 逻辑像素**

+ 这个单位就是 ios 开发的 px
+ 安卓开发的 dp
+ 对于 pc，包括 win（8+） linux，mac，由各自系统的或者对应软件（比如 webview 内部）提供的图像界面处理引擎处理进行缩放

相关性：

  - 设备物理像素和设备独立像素的比例：
    - 对于 i3 和 i4，他们的设备独立像素都是一样的
    - 对于 i3，原本就是一个设备独立像素由一个物理像素显示
    - 对于 i4，因为高清屏的存在，变成一个设备独立像素由 4 个物理像素显示
  - 设备像素是 **固定不变** 的，有默认的像素比，就有默认的设备独立像素，任何时候都有像素比，任何时候都有设备独立像素，设备独立像素对于前端而言，就是完完全全的 CSS 像素本身，只不过一个是前端的概念，一个是 DPR 官方定义时用到的说法

1. 每一款设备的 PPI 在其出厂时就已经决定
2. 由 PPI，根据密度分界，得出默认的像素比
3. 由默认的像素比，因为设备像素是固定的，得出默认的设备独立像素
![](/img/user/programming/font-end/mobile/mobile-basic/image-20230203153930422.png)

Dips are the abstract pixels that are used to feed information to the width/height media queries and the meta viewport device-width. They are best explained by taking a look at the difference between retina and non-retina devices.

All non-retina iPhones have a width of 320 physical pixels in portrait mode. When you use `<meta name="viewport" content="width=device-width">` it sets the width of the [layout viewport](https://www.quirksmode.org/mobile/viewports2.html) to 320px, so that the page naturally fits into the screen.

Thus, on non-retina iPhones there are 320 physical pixels and also 320 dips. Thus `<window.devicePixelRatio>` equals 1.

Retina iPhones have a width of 640 physical pixels in portrait mode. Still, websites that use the meta viewport should not become 640px wide, but instead remain at 320, which is the optimal reading size for the iPhone.

Thus the number of dips is still 320, even though the number of physical pixels is 640. `window.devicePixelRatio` now equals 2.

**相同的设备独立像素，带来了稳定的布局和更清晰的成像结果**

## 位图像素

位图图像（bitmap），是由一个个像素点构成

位图图像需要一个像素点对应一个设备像素才清晰。所以假设一张 100 x 100 的图片放在普通屏上看是清晰的，放到高清/Retina 屏上就会显得比较模糊，那是因为本来 100 x 100 的图片在普通屏上图片像素与设备像素一一对应，而到了高清/Retina 屏上一个图片像素却要对应多个设备像素，这样一来看起来图片就比较模糊。

![](/img/user/programming/font-end/mobile/mobile-basic/image-20230203154058661.png)

如图所示，如果一个图片像素要对应多个设备像素的话，那这些设备像素只能显示成跟这个图片像素差不多的颜色，导致看起来会模糊。

既然知道了问题产生的原因，那解决方法也很简单，位图图像需要一个像素点对应一个设备像素才清晰嘛，那就本来是 100 x 100 的图片在 DPR 为 1 的屏幕上显示清晰，在 DPR 为 2 的屏幕上显示模糊，那就在 DPR 为 2 的屏幕上放 200 x 200 的图好了，这样就一一对应了。

# 视口

所有的视口都是抽象概念，他们的大小全都是用 css 像素来衡量的，既然是人为抽象出来的概念，那么一定是为了解决某种问题而存在的，下面就让我们逐个讲解。

## 布局视口 (layout viewport)

### 目的

首先，移动设备上的浏览器认为自己必须能让所有的网站都正常显示，即使是那些不是为移动设备设计的网站。但如果以浏览器的可视区域作为默认 viewport 的话，因为移动设备的屏幕都不是很宽，所以那些为桌面浏览器设计的网站放到移动设备上显示时，必然会因为移动设备的 viewport 太窄，而挤作一团，甚至布局什么的都会乱掉。

  > 也许有人会问，现在不是有很多手机分辨率都非常大吗，比如 768x1024，或者 1080x1920 这样，那这样的手机用来显示为桌面浏览器设计的网站是没问题的吧？前面我们已经说了，css 中的 1px 并不是代表屏幕上的 1px，你分辨率越大，css 中 1px 代表的物理像素就会越多，devicePixelRatio 的值也越大，这很好理解，因为你分辨率增大了，但屏幕尺寸并没有变大多少，必须让 css 中的 1px 代表更多的物理像素，才能让 1px 的东西在屏幕上的大小与那些低分辨率的设备差不多，不然就会因为太小而看不清。所以在 1080x1920 这样的设备上，在默认情况下，也许你只要把一个 div 的宽度设为 300 多 px（视 devicePixelRatio 的值而定），就是满屏的宽度了。

回到正题上来，如果把移动设备上浏览器的可视区域设为 viewport 的话，某些网站就会因为 viewport 太窄而显示错乱，所以这些浏览器就决定默认情况下把 viewport 设为一个较宽的值，比如 980px，这样的话即使是那些为桌面设计的网站也能在移动浏览器上正常显示了。ppk 把这个浏览器默认的 viewport 叫做 layout viewport。

![](/img/user/programming/font-end/mobile/mobile-basic/image-20230203155511697.png)

## 视觉视口 (visual viewport)

因为浏览器可视区域 (visual viewport) 的宽度比这个 layout viewport 的宽度要小，带来的后果就是浏览器会出现横向滚动条，所以我们还需要一个 viewport 来代表浏览器可视区域的大小，ppk 把这个 viewport 叫做 visual viewport。

视觉视口的宽高是默认的设备独立像素，大小固定不变，布局视口的大小也是不变的，两者就像是放大镜，只是调整距离而已

## 手机浏览器渲染网页的过程

1. 渲染————将整个页面渲染在一个 layout viewport 中，以保证页面排版正确。
2. 将整个 layout viewport 缩放到 visual viewport 大小，以保证页面在手机屏幕上被完整显示出来

为了防止出现横向的滚动条，所以才会有第二步，这个渲染过程是针对为 pc 端设计的网站的

其实布局视口的大小是没有改变的，否则布局就会混乱了，改变的是视觉视口的大小，视觉视口变大了，大到足以容纳下整个布局视口，但在我们的手机屏幕大小是无法改变的，所以看起来就像是 layout viewport 缩小了。

在 pc 端没有这些布局视口、视觉视口的区别，当我们使用滚轮缩放页面的时候，页面的布局会发生改变。而移动端 css 布局不改变也是因为移动端进行重新布局的成本太高，包括电量和流量

## 理想视口 (ideal viewport)

又因为现在越来越多的网站都会为移动设备进行单独的设计，所以必须还要有一个能完美适配移动设备的 viewport。

1. 不需要用户缩放和横向滚动条就能正常的查看网站的所有内容
2. 显示的文字的大小是合适，比如一段 14px 大小的文字，不会因为在一个高密度像素的屏幕里显示得太小而无法看清，理想的情况是这段 14px 的文字无论是在何种密度屏幕，何种分辨率下，显示出来的大小都是差不多的。当然，不只是文字，其他元素像图片什么的也是这个道理

ppk 把这个 viewport 叫做 ideal viewport，也就是第三个 viewport——移动设备的理想 viewport。

ideal viewport 并没有一个固定的尺寸，不同的设备拥有有不同的 ideal viewport。所有的 iphone 的 ideal viewport 宽度都是 320px，无论它的屏幕宽度是 320 还是 640，也就是说，在 iphone 中，css 中的 320px 就代表 iphone 屏幕的宽度。

但是安卓设备就比较复杂了，有 320px 的，有 360px 的，有 384px 的等等，关于不同的设备 ideal viewport 的宽度都为多少，可以到 http://viewportsizes.com 去查看一下，里面收集了众多设备的理想宽度。

## 理想视口与设备独立像素的关系

1. 每一款设备的 PPI 在其出厂时就已经决定
2. 由 PPI，根据密度分界，得出默认的像素比
4. 由默认的像素比，因为设备像素在屏幕宽高上的 **个数** 是固定的，得出默认的设备独立像素对应的个数，即是：**理想视口的宽高的 CSS 像素个数**

只要在 css 中把某一元素的宽度设为 ideal viewport 的宽度，那么这个元素的宽度就是设备屏幕的宽度了，也就是宽度为 100% 的效果。

  > 来看一个常见的疑问。一个分辨率为 320x480 的手机理想 viewport 的宽度是 320px，而另一个屏幕尺寸相同但分辨率为 640x960 的手机的理想 viewport 宽度也是为 320px，那为什么分辨率大的这个手机的理想宽度要跟分辨率小的那个手机的理想宽度一样呢？这是因为，只有这样才能保证同样的网站在不同分辨率的设备上看起来 **大小** 都是一样或差不多的。分辨率的提高带来的是 **精度** 的提升，而不是简单的堆砌像素点，这一点体现在了更高的像素比身上，精度越高的屏幕，有着更高的像素比

## 如何获取三个视口的值

### PC 端

只有布局视口和视觉视口，且布局视口和视觉视口任何时候都是一致的，

获取方法：`document.documentElement.clienWidth`

### 移动端

`const layout = document.documentElement.clientWidth`

  - 没有兼容性问题
  - 不包括滚动条、边框、外边距
  - 实际使用只会用这一个

`const visual = window.innerWidth`

  - 接近全部支持
  - 包括滚动条、边框、外边距

`const ideal= screen.width`

  - 一般浏览器代表理想视口的宽度，一般浏览器代表设备的分辨率，有很大的兼容性问题

## Meta 标签控制 Viewport

在开发移动端设备的网站时，我们常常会使用如下的 meta 标签

  ```js
	<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">
  ```

### User-scalable

meta 标签里的 user-scalable=no 值就表示禁止缩放。

建议不要禁用，因为用户有时候有需要，争议大这里暂时不讨论。

### Initial-scale

设置 initial-scale 这条规则实际上做了如下 2 件事：

  1. 将页面初始缩放因子设置为给定的值，由 ideal viewport 的宽，计算得到 visual viewport 的宽。
  2. 设置 layout viewport 的宽等于刚刚计算出来的 visual viewport 的宽

初始缩放因子为 1，即是：visual viewport 的宽度 等于 ideal viewport 的宽度，然后再设置 layout viewport 的宽度为 ideal viewport 的宽度

### width=device-width

设置 width=device-width 这条规则实际上做了如下 2 件事:

  1. 将 layout viewport 的宽设置为设备屏幕的宽度（即屏幕的设备独立像素的宽度，本人注）
  2. visual viewport 默认包裹这 layout viewport ，也变成了 ideal viewport 的大小

### 为什么要用 2 个相同功能的规则

大家应该发现 width=device-width 和 initial-scale=1.0 的功能基本一样，那为什么都要用到呢？

由于存在兼容性问题，所以，最完美的写法应该是，两者都写上去，这样就 initial-scale=1 解决了 iphone、ipad 的毛病，width=device-width 则解决了 IE 的毛病。

使用 **理想视口**(有 width=device-width 和 initial-scale=1.0) 解决太大的元素 **超过视觉视口** 后不出滚动条的问题

理想视口不仅仅只能解决旋转时的问题。如果你页面中存在太大的元素，你的 meta 标签只使用 width=device-width，initial-scale=1.0 中的一个，有些浏览器会扩展布局视口的宽度来容纳这个元素，这里的兼容性很复杂，但你两个都使用了，大部分浏览器都不会改变布局视口了

### 定义宽度规则时的冲突

浏览器解决这个冲突的方法是：宽度最大优先原则 (横屏或竖屏模式下均是)。

### 缩放因子

上面提到了一个概念”页面初始缩放因子”，现在我们说明一下什么是缩放因子：

先给出 PPK 大神关于缩放因子的公式：zoom factor = ideal viewport width / visual viewport width

从这个公式，我们可以得到一个结论：页面缩放是相对于 ideal viewport 来进行缩放的

### 旋转

当用户旋转设备时，布局视口的宽度通常会发生改变，因为横屏和竖屏的理想视口尺寸不一样，但是 ios 的 safari 基于性能的考虑，布局视口不会发生变化

但是 ios 的 safari 在 initial-scale 设置的情况下，当用户选择旋转设备时，safari 的布局视口的宽度会跟着改变，但是：如果只设置 initial-scale，**ie10** 在旋转是就会出现问题

## Meta 标签的其他属性

name 为 viewport 的 meta 标签，是苹果发明的，桌面浏览器不支持

```html
  <meta name="viewport" content=""/>
  <!--content属性里的属性，大多是没有默认值的-->
  ```

width[pixel_value|device-width]

  - 直接设置具体数值，大部分浏览器不支持，但是 IOS 是支持的
  - 95% 的浏览器都支持 device-width

initial-scale：初始缩放比例

user-scalable[no|yes]：是否允许缩放行为，默认是允许的

  - ios10 不支持 user-scalable

minimum-scale：允许缩放的最大比例

maximum-scale：允许缩放的最小比例

  - 设置成相同，即可兼容 ios10
  - 没有这些指令，默认为：20%-500%
  - 使用了这些指令后，可以扩大到：10%-1000%
  - 安卓 webkit 不支持 minimum-scale 属性（默认范围：25%-400%）
  - ie 根本没有这两个属性

target-densitydpi

  - dpi_value：70-400，每英寸像素点的个数
  - device-dpi：设备默认像素密度 2
  - high-dpi：高像素密度
  - medium-dpi：中等像素密度
  - low-dpi：低像素密度
  - webkit 内核不在支持，chrome 和 ios 都不在支持 target-densitydpi 属性了
height，不考虑

## 等比问题

没有加 name 为 viewport 的 meta 标签

  - 一个相同 css 像素大小的区域，在不同的设备上占据的实际 **物理大小**（英寸）**不一样**
  - 在不同的设备是等比的，占据的宽度 **百分比** 是 **相同的**
  - 但是不方便浏览，用户还需要放大页面

加 name 为 viewport 的 meta 标签

  - 一个相同 css 像素大小的区域,在不同的设备上占据的实际 **物理大小**（英寸）是 **一样的**
  - 在不同的设备是不等比的，占据的宽度 **百分比** 是 **不相同的**
  - 用户不用进行放大了，但是页面布局困难

等比是不是一个必须的需要？

  - 百分百还原设计图 ---> 在不同设备上要等比（文字要完美清晰的展示）
  - 加 meta 标签（导致不等比，**统一了三个视口，用户不用再进行放大**）
  - 进行适配（使得加上 meta 标签后也是等比，为的是百分百还原设计图，使得在不同像素比的设备上都能完整清晰的显示网页）

## 图片模糊问题

### 产生原因

我们平时使用的图片大多数都属于位图（`png、jpg...`），位图由一个个像素点构成的，每个像素都具有特定的位置和颜色值

理论上，位图的每个像素对应在屏幕上使用一个物理像素来渲染，才能达到最佳的显示效果。

而在 `dpr > 1` 的屏幕上，位图的一个像素可能由多个物理像素来渲染，然而这些物理像素点并不能被准确的分配上对应位图像素的颜色，只能取近似值，所以相同的图片在 `dpr > 1` 的屏幕上就会模糊:

### 解决方案

为了保证图片质量，我们应该尽可能让一个屏幕像素来渲染一个图片像素，所以，针对不同 `DPR` 的屏幕，我们需要展示不同分辨率的图片。

如：在 `dpr=2` 的屏幕上展示两倍图 `(@2x)`，在 `dpr=3` 的屏幕上展示三倍图 `(@3x)`

### Media 查询

```css
  .avatar{
    background-image: url(conardLi_1x.png);
  }
  @media only screen and (-webkit-min-device-pixel-ratio:2){
    .avatar{
      background-image: url(conardLi_2x.png);
    }
  }
  @media only screen and (-webkit-min-device-pixel-ratio:3){
    .avatar{
      background-image: url(conardLi_3x.png);
    }
  }
```

### Image-set

```css
  .avatar {
      background-image: -webkit-image-set( "conardLi_1x.png" 1x, "conardLi_2x.png" 2x );
  }
  ```

### Srcset

使用 `img` 标签的 `srcset` 属性，浏览器会自动根据像素密度匹配最佳显示图片：

```html
<img src="conardLi_1x.png"
   srcset=" conardLi_2x.png 2x, conardLi_3x.png 3x"/>
```

### JavaScript 拼接图片 Url

使用 `window.devicePixelRatio` 获取设备像素比，遍历所有图片，替换图片地址：

```js
const dpr = window.devicePixelRatio;
const images =  document.querySelectorAll('img');
images.forEach((img)=>{
    img.src.replace(".", `@${dpr}x.`);
})
```

### 使用 Svg

`SVG` 的全称是可缩放矢量图（`Scalable Vector Graphics`）。不同于位图的基于像素，`SVG` 则是属于对图像的形状描述，所以它本质上是文本文件，体积较小，且不管放大多少倍都不会失真。

除了我们手动在代码中绘制 `svg`，我们还可以像使用位图一样使用 `svg` 图片：

```html
  <img src="conardLi.svg">
  
  <img src="data:image/svg+xml;base64,[data]">
  
  .avatar {
    background: url(conardLi.svg);
  }
```

## 适配异形屏

### 安全区域

在 `iPhoneX` 发布后，许多厂商相继推出了具有边缘屏幕的手机。

这些手机和普通手机在外观上无外乎做了三个改动：圆角（`corners`）、刘海（`sensor housing`）和小黑条（`Home Indicator`）。为了适配这些手机，安全区域这个概念变诞生了：安全区域就是一个不受上面三个效果的可视窗口范围。

为了保证页面的显示效果，我们必须把页面限制在安全范围内，但是不影响整体效果。

### content='viewport-fit'

`viewport-fit` 是专门为了适配 `iPhoneX` 而诞生的一个属性，它用于限制网页如何在安全区域内进行展示。

**属性值**

- `contain`: 可视窗口完全包含网页内容
- `cover`：网页内容完全覆盖可视窗口
- 默认情况下或者设置为 `auto` 和 `contain` 效果相同

### env、constant

我们需要将顶部和底部合理的摆放在安全区域内，`iOS11` 新增了两个 `CSS` 函数 `env、constant`，用于设定安全区域与边界的距离。

函数内部可以是四个常量：

  1. `safe-area-inset-left`：安全区域距离左边边界距离
  2. `safe-area-inset-right`：安全区域距离右边边界距离
  3. `safe-area-inset-top`：安全区域距离顶部边界距离
  4. `safe-area-inset-bottom`：安全区域距离底部边界距离

注意：我们必须指定 `viweport-fit` 后才能使用这两个函数：

```html
  <meta name="viewport" content="viewport-fit=cover">
```

`constant` 在 `iOS < 11.2` 的版本中生效，`env` 在 `iOS >= 11.2` 的版本中生效，这意味着我们往往要同时设置他们，将页面限制在安全区域内：

```css
body {
    padding-bottom: constant(safe-area-inset-bottom);
    padding-bottom: env(safe-area-inset-bottom);
}
```

当使用底部固定导航栏时，我们要为他们设置 `padding` 值：

## 横屏适配

### JavaScript 检测横屏

`window.orientation`: 获取屏幕旋转方向

```js
  window.addEventListener("resize", ()=>{
      if (window.orientation === 180 || window.orientation === 0) { 
        // 正常方向或屏幕旋转180度
          console.log('竖屏');
      };
      if (window.orientation === 90 || window.orientation === -90 ){ 
         // 屏幕顺时钟旋转90度或屏幕逆时针旋转90度
          console.log('横屏');
      }  
  }); 
```

### CSS 检测横屏

```css
  @media screen and (orientation: portrait) {
    /*竖屏...*/
  } 
  @media screen and (orientation: landscape) {
    /*横屏...*/
  }
  
```

# 适配

## Rem

### Em

em: 自身的 fontsize

谷歌下字体的默认大小为 16px

谷歌下字体的最小大小为 12px

### Rem

rem：相当于根标签的 fontsize

本质上只是一个相对单位, 和等比例适配无关

### Rem 适配

```html
<script type="text/javascript">
  var html = document.querySelector("html");
  //1rem  = html.style.fontSize = 1/16 的布局视口宽度
  //布局视口的宽度就是16rem
  //设计图的宽就是16rem
  html.style.fontSize = document.documentElement.clientWidth/16+"px"
  
  //rem适配原理：改变了一个元素在不同设备上占据的css像素的个数，使得符合等比
  /*rem适配的优缺点
			优点:没有破坏完美视口
			缺点:px值到rem的转换太复杂*/

  ;(function(){
    var styleNode = document.createElement("style");
    var w = document.documentElement.clientWidth/16;
    styleNode.innerHTML = "html{font-size:"+w+"px!important}";
    document.head.appendChild(styleNode);
  })()
</script>
```

使用

```js
16rem=viewWidth
16rem=1080px
那么100px等于？ 100/(1080*16)rem
```

## Viewport 适配

```js
//将所有设备布局视口的宽度调整为设计图的宽度
var targetW = 640;
var scale = screen.width/targetW;
var meta = document.createElement("meta");
meta.name = "viewport";
meta.content="initial-scale="+scale+",minimum-scale="+scale+",maximum-scale="+scale+",user-scalable=no";
document.head.appendChild(meta);


//viewport
/*viewport适配的原理：
			viewport适配方案中，每一个元素在不同设备上占据的css像素的个数是一样的。但是css像素和物理像素的比例是不一样的，等比的*/
/*viewport适配的优缺点：
			优点:所量即所得
			缺点:没有使用完美视口*/
(function(){
  var targetW = 640;
  var scale = document.documentElement.clientWidth/targetW;
  var meta =	document.querySelector("meta[name='viewport']");
  meta.content="initial-scale="+scale+",minimum-scale="+scale+",maximum-scale="+scale+",user-scalable=no";
})()
```

## 百分比

参照物, 局部最优方案

## 弹性布局

固定的 px 值，不做任何适配，在任何设备 1ccs 像素 占的大小都是一样的，因为布局视口的初始大小都是 980px

通过流体布局和固定布局以及 flex 布局，可以实现弹性布局

## VW 适配

参考 https://aotu.io/notes/2017/04/28/2017-4-28-CSS-viewport-units/index.html

参考 http://www.cnblogs.com/wengxuesong/archive/2016/05/16/5497653.html

rem 2 年前 2017 过时

未读：<https://blog.csdn.net/qq_21729177/article/details/79466951>

### 概念

**视口（viewport）单位**，使 **流体排版** 在页面上应用变为可能。视口单位是根据浏览器的视口尺寸的百分比来定义的。

根据 [CSS3规范](https://drafts.csswg.org/css-values-3/#viewport-relative-lengths)，视口单位主要包括以下 4 个：

  + **vw** : 1vw 等于视口宽度的 1%
  + **vh** : 1vh 等于视口高度的 1%
  + **vmin** : 选取 vw 和 vh 中最小的那个
  + **vmax** : 选取 vw 和 vh 中最大的那个

视口单位区别于 `%` 单位，视口单位是依赖于视口的尺寸，根据视口尺寸的百分比来定义的；而 `%` 单位则是依赖于元素的祖先元素。

![](/img/user/programming/font-end/mobile/mobile-basic/image-20230203165200286.png)

例如，在 **桌面端浏览器** 视口尺寸为 650px，那么 1vw = 650 * 1% = 6.5px（这是理论推算的出，如果浏览器不支持 0.5px，那么实际渲染结果可能是 7px）。

### 兼容性

其兼容性如下图所示，可以知道：**在移动端 iOS 8 以上以及 Android 4.4 以上获得支持，并且在微信 x5 内核中也得到完美的全面支持。**![caniuse_viewport.png](/img/user/programming/font-end/mobile/mobile-basic/caniuse_viewport.png)

### 仅使用 Vw 作为 CSS 单位

对于设计稿的尺寸转换为 vw 单位

```js
  100vw=viewWidth
  100vw=1080px // 设计稿宽度
  那么100px等于？ 100/(1080*100)vw
```

使用的方法就和使用 rem 一样

### 搭配 Vw 和 rem，布局更优化

这样的页面虽然看起来适配得很好，但是你会发现由于它是利用视口单位实现的布局，依赖于视口大小而自动缩放，无论视口过大还是过小，它也随着视口过大或者过小，失去了最大最小宽度的限制。

于是，联想到不如结合 rem 单位来实现布局？rem 弹性布局的核心在于动态改变根元素大小，那么我们可以通过：

  1. 给根元素大小设置随着视口变化而变化的 vw 单位，这样就可以实现动态改变其大小。
  2. 限制根元素字体大小的最大最小值，配合 body 加上最大宽度和最小宽度

代码实现如下 https://juejin.im/post/5b29f476e51d455892718380

# 适配方案选型

## 为什么不推荐使用 Flexible Rem 单位?

- 本文建议读者不要使用 flexible 或者其他修改 viewport 致使 viewport width 不等于 device-width 的方案（因为这会导致一些 bug）
- 本文建议读者不要使用 **以 rem 或者小程序 rpx 来实现等比缩放为主** 的布局手段，而使用 **面向逻辑像素 px** 为主，**面向等比缩放的 vx 和 vxxx（vw/vh/vmax/vmin）为辅助** 的布局单位，搭配一些 flex 等布局手段
- 本文建议读者一般情况遵循：同样观看距离情况下，大屏看的更多而不是大屏看的更大的设计最佳实践来进行布局，并且以这种最佳实践作为理论依据来传递给设计师（当然你觉得等比缩放才是合理的，请看其他回答，该回答不适合你）
- 本文没有鼓吹读者使用 [响应式布局](https://www.zhihu.com/search?q=%E5%93%8D%E5%BA%94%E5%BC%8F%E5%B8%83%E5%B1%80&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A628236155%7D)，也没有鼓吹读者盲目忽略兼容性使用 vx 和 vxxx，同时也没有反对读者使用 rem 这个单位本身原有的定义来实现一些布局

你们老是鼓吹 rem 的，醒醒吧，别再看网上大片大片的 rem 文章

flexible 方案已经废坑了，已经废坑了，已经废坑了

对，flexible 已经不维护了，原因自己看 flexible 的 github

px 没有问题，用 rem 来实现自适应才有问题（因为它是 vx，vxxx 单位的备胎），能问这种问题的人，应该认真研究一下 viewport。
## 使用 rem 布局的优缺点

优点：
在屏幕分辨率千差万别的时代，只要将rem与屏幕分辨率关联起来就可以实现页面的整体缩放，使得在设备上的展现都统一起来了。
而且现在浏览器基本都已经支持rem了，兼容性也非常的好。

缺点：
（1）在奇葩的dpr设备上表现效果不太好，比如一些华为的高端机型用rem布局会出现错乱。
（2）使用iframe引用也会出现问题。
（3）rem在多屏幕尺寸适配上与当前两大平台的设计哲学不一致。即大屏的出现到底是为了看得又大又清楚，还是为了看的更多的问
题。

详细资料可以参考： [《css3 的字体大小单位 rem 到底好在哪？》](https://www.zhihu.com/question/21504656) [《VW:是时候放弃 REM 布局了》](https://www.jianshu.com/p/e8ae1c3861dc) [《为什么设计稿是 750px》](https://blog.csdn.net/Honeymao/article/details/76795089) [《使用 Flexible 实现手淘 H5 页面的终端适配》](https://github.com/amfe/article/issues/17)

## 面向逻辑像素开发的基本流程

1. 在 head 设置 width=device-width 的 viewport
2. 在 css 中使用 px
3. 在适当的场景使用 flex 布局，或者配合 vw 进行自适应
4. 在 **跨设备类型的时候**（pc <-> 手机 <-> 平板）使用媒体查询
5. 在跨设备类型如果交互差异太大的情况，考虑分开 [项目开发](https://www.zhihu.com/search?q=%E9%A1%B9%E7%9B%AE%E5%BC%80%E5%8F%91&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A628236155%7D)

## 如何读懂设计图

一般国内的设计师，给出手机版的设计图，一般是 750px，注意这里的 px，并不是我们的 px（逻辑像素），其实是**物理像素，**因为设计师是根据 iphone6 或者同等设备来进行设计的，那么要知道 iphone6 的逻辑像素其实是 375，dpr 是 2，那么拿到手的设计稿，转换为逻辑像素，就得除以 2，我们叫这种设计图，叫两倍图

同理，如果是 375 + 375 + 375 大小，那么我们就得除以 3，叫三倍图

如果设计团队有使用 [墨刀](https://www.zhihu.com/search?q=%E5%A2%A8%E5%88%80&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A628236155%7D) 或者蓝湖，你可以在两者里边设置你的查看尺寸，得到我们需要的逻辑像素

如果设计师不用蓝湖等工具，给你的并不是 375 的倍数怎么办，我先说办法，原因你们自己琢磨，我不细致分析

最简单的方法，设计师给你的图的物理宽度 w，除以一个数 x，如果得的出来的商在 360 - 414 之间，那么你换算的公式为【你在设计图测量出来的物理像素数除以 x】，那么 dpr 就是 x，也就是 x 倍图

那么朋友又会问，不同设备逻辑像素也不一样呀

对，不一样，但问题为什么我们要将它们弄成一样？其实，不一样，才是合理的。我暂且不说原因，原因后面的文章解释

那么不一样的情况，怎么布局？那就靠你们技术手段，flex，流式布局，vw 等

# 车联网适配

## 横竖屏 适应

车联网 自研 自适应

![image-20201104191515801](/img/user/programming/font-end/mobile/mobile-basic/image-20201104191515801.png)

![image-20201104191550578](/img/user/programming/font-end/mobile/mobile-basic/image-20201104191550578.png)

![image-20201104191611349](/img/user/programming/font-end/mobile/mobile-basic/image-20201104191611349.png)

![image-20201104191629537](/img/user/programming/font-end/mobile/mobile-basic/image-20201104191629537.png)

人的操作范围

![image-20201104191647031](/img/user/programming/font-end/mobile/mobile-basic/image-20201104191647031.png)

![image-20201104191715163](/img/user/programming/font-end/mobile/mobile-basic/image-20201104191715163.png)

8:4 的走 8 ：3

![image-20201104191750274](/img/user/programming/font-end/mobile/mobile-basic/image-20201104191750274.png)

先用标准配适配，然后缩放比例

![image-20201104191814024](/img/user/programming/font-end/mobile/mobile-basic/image-20201104191814024.png)

通用标准故居

![image-20201104191837854](/img/user/programming/font-end/mobile/mobile-basic/image-20201104191837854.png)

![image-20201104191918318](/img/user/programming/font-end/mobile/mobile-basic/image-20201104191918318.png)

单排列表 拉伸 可能变成双排列表

![image-20201104192018535](/img/user/programming/font-end/mobile/mobile-basic/image-20201104192018535.png)

![image-20201104192109901](/img/user/programming/font-end/mobile/mobile-basic/image-20201104192109901.png)

固定了容器的类型，布局的类型也会相对固定

![image-20201104192245807](/img/user/programming/font-end/mobile/mobile-basic/image-20201104192245807.png)

![image-20201104192315501](/img/user/programming/font-end/mobile/mobile-basic/image-20201104192315501.png)

![image-20201104192347854](/img/user/programming/font-end/mobile/mobile-basic/image-20201104192347854.png)

![image-20201104192434085](/img/user/programming/font-end/mobile/mobile-basic/image-20201104192434085.png)

![image-20201104192449950](/img/user/programming/font-end/mobile/mobile-basic/image-20201104192449950.png)

![image-20201104192549614](/img/user/programming/font-end/mobile/mobile-basic/image-20201104192549614.png)

![image-20201104192557545](/img/user/programming/font-end/mobile/mobile-basic/image-20201104192557545.png)

没有做删哥化 vw vh

4:3 vw vh 跑在 16:9 结合 medie query 和 grid 布局

通用实践 可以提供出来 组件做到自适应化

提供一个 类似于的 react

开发方面的需求 怎么样框架整起来会方便一点，融合进框架

转屏 设备也不会转 小程序也不会转 有的车厂 横竖不一

希望适配这边 由框架来做 纯靠框架不能解决适配

我感觉这种东西不应该放进框架里面吧 框架只需要提供组件就好了 适配还是让开发者来做最好

或者是给一个框架组件，在框架塞组件即可

# 移动端点击事件

https://juejin.cn/post/6844903846418841608

https://juejin.cn/post/6844903633528553485

## 移动端 300ms 点击延迟

移动端浏览器会有一些默认的行为，比如双击缩放、双击滚动。这些行为，尤其是双击缩放，主要是为桌面网站在移动端的浏览体验设计的。而在用户对页面进行操作的时候，移动端浏览器会优先判断用户是否要触发默认的行为。

**重点**：由于移动端会有双击缩放的这个操作，因此浏览器在 click 之后要等待 300ms，看用户有没有下一次点击，也就是这次操作是不是双击

### 浏览器开发商的解决方案

#### 方案一：禁用缩放

当 HTML 文档头部包含如下 `meta` 标签时：

```html
<meta name="viewport" content="user-scalable=no"> <meta name="viewport" content="initial-scale=1,maximum-scale=1">
```

表明这个页面是不可缩放的，那双击缩放的功能就没有意义了，此时浏览器可以禁用默认的双击缩放行为并且去掉 300ms 的点击延迟。

**缺点**：就是必须通过完全禁用缩放来达到去掉点击延迟的目的，然而完全禁用缩放并不是我们的初衷，我们只是想禁掉默认的双击缩放行为，这样就不用等待 300ms 来判断当前操作是否是双击。但是通常情况下，我们还是希望页面能通过双指缩放来进行缩放操作，比如放大一张图片，放大一段很小的文字。

#### 方案二：更改默认的视口宽度

一开始，为了让桌面站点能在移动端浏览器正常显示，移动端浏览器默认的视口宽度并不等于设备浏览器视窗宽度，而是要比设备浏览器视窗宽度大，通常是 980px。我们可以通过以下标签来设置视口宽度为设备宽度。

```html
<meta name="viewport" content="width=device-width">
```

因为双击缩放主要是用来改善桌面站点在移动端浏览体验的，而随着响应式设计的普及，很多站点都已经对移动端坐过适配和优化了，这个时候就不需要双击缩放了，如果能够识别出一个网站是响应式的网站，那么移动端浏览器就可以自动禁掉默认的双击缩放行为并且去掉 300ms 的点击延迟。如果设置了上述 `meta` 标签，那浏览器就可以认为该网站已经对移动端做过了适配和优化，就无需双击缩放操作了。

这个方案相比方案一的好处在于，它没有完全禁用缩放，而只是禁用了浏览器默认的双击缩放行为，但用户仍然可以通过双指缩放操作来缩放页面。

#### 方案三：CSS Touch-action

`touch-action` 这个 CSS 属性。这个属性指定了相应元素上能够触发的用户代理（也就是浏览器）的默认行为。如果将该属性值设置为 `touch-action: none`，那么表示在该元素上的操作不会触发用户代理的任何默认行为，就无需进行 300ms 的延迟判断

### 社区的解决方案

#### 方案一：指针事件的 Polyfill

现在除了 IE，其他大部分浏览器都还不支持指针事件。有一些 JS 库，可以让我们提前使用指针事件，比如

- Google 的 [Polymer](https://link.juejin.cn?target=https%3A%2F%2Flink.jianshu.com%3Ft%3Dhttps%3A%2F%2Fgithub.com%2FPolymer%2FPointerEvents "https://link.jianshu.com?t=https://github.com/Polymer/PointerEvents")
- 微软的 [HandJS](https://link.juejin.cn?target=https%3A%2F%2Flink.jianshu.com%3Ft%3Dhttp%3A%2F%2Fhandjs.codeplex.com%2F "https://link.jianshu.com?t=http://handjs.codeplex.com/")
- [@Rich-Harris](https://link.juejin.cn?target=https%3A%2F%2Flink.jianshu.com%3Ft%3Dhttps%3A%2F%2Fgithub.com%2FRich-Harris "https://link.jianshu.com?t=https://github.com/Rich-Harris") 的 [Points](https://link.juejin.cn?target=https%3A%2F%2Flink.jianshu.com%3Ft%3Dhttps%3A%2F%2Fgithub.com%2FRich-Harris%2FPoints "https://link.jianshu.com?t=https://github.com/Rich-Harris/Points")

然而，我们现在关心的不是指针事件，而是与 300ms 延迟相关的 CSS 属性 `touch-action`。由于除了 IE 之外的大部分浏览器都不支持这个新的 CSS 属性，所以这些指针事件的 polyfill 必须通过某种方式去模拟支持这个属性。一种方案是 JS 去请求解析所有的样式表，另一种方案是将 `touch-action` 作为 html 标签的属性。

#### 方案二：FastClick

[FastClick](https://link.juejin.cn?target=https%3A%2F%2Flink.jianshu.com%3Ft%3Dhttps%3A%2F%2Fgithub.com%2Fftlabs%2Ffastclick "https://link.jianshu.com?t=https://github.com/ftlabs/fastclick") 是 [FT Labs](https://link.juejin.cn?target=https%3A%2F%2Flink.jianshu.com%3Ft%3Dhttp%3A%2F%2Flabs.ft.com%2F "https://link.jianshu.com?t=http://labs.ft.com/") 专门为解决移动端浏览器 300 毫秒点击延迟问题所开发的一个轻量级的库。FastClick 的实现原理是在检测到 touchend 事件的时候，会通过 DOM 自定义事件立即出发模拟一个 click 事件，并把浏览器在 300ms 之后的 click 事件阻止掉

## 点击穿透

说完移动端点击 300ms 延迟的问题，还不得不提一下移动端点击穿透的问题。可能有人会想，既然 click 点击有 300ms 的延迟，那对于触摸屏，我们直接监听 touchstart 事件不就好了吗？

使用 touchstart 去代替 click 事件有两个不好的地方。

1. touchstart 是手指触摸屏幕就触发，有时候用户只是想滑动屏幕，却触发了 touchstart 事件，这不是我们想要的结果；
2. 使用 touchstart 事件在某些场景下可能会出现点击穿透的现象。

### 什么是点击穿透

假如页面上有两个元素 A 和 B。B 元素在 A 元素之上。我们在 B 元素的 touchstart 事件上注册了一个回调函数，该回调函数的作用是隐藏 B 元素。我们发现，当我们点击 B 元素，B 元素被隐藏了，随后，A 元素触发了 click 事件。

这是因为在移动端浏览器，事件执行的顺序是 touchstart > touchend > click。而 click 事件有 300ms 的延迟，当 touchstart 事件把 B 元素隐藏之后，隔了 300ms，浏览器触发了 click 事件，但是此时 B 元素不见了，所以该事件被派发到了 A 元素身上。如果 A 元素是一个链接，那此时页面就会意外地跳转。

### 移动端浏览器事件触发的顺序

touchstart --> mouseover(有的浏览器没有实现) --> mousemove(一次) -->mousedown --> mouseup --> click -->touchend

## 解决方案

### 只用 Touch

最简单的解决方案，完美解决点击穿透问题

把页面内所有 click 全部换成 touch 事件

需要特别注意:

+ a 标签，a 标签的 href 也是 click，需要去掉换成 js 控制的跳转，或者直接改成 span + tap 控制跳转。如果要求不高，不在乎滑走或者滑进来触发事件的话，span + touchend 就可以了，毕竟 tap 需要引入第三方库
+ 不用 a 标签其实没什么，移动 app 开发不用考虑 SEO，即便用了 a 标签，一般也会去掉所有默认样式，不如直接用 span

#### 小程序的方案

即没有 a 标签, 也没有 click 事件, 还有个 tap 事件

#### 为什么需要 Tap 事件?

因为仅仅只是滑动, 也会触发 touchstart 事件, 所以需要一个 tap 来保证点击行为

### 只用 Click

下下策

因为会带来 300ms 延迟，页面内任何一个自定义交互都将增加 300 毫秒延迟，想想都慢

不用 touch 就不会存在 touch 之后 300ms 触发 click 的问题，如果交互性要求不高可以这么做，

强烈不推荐 ，快一点总是好的

### Tap 后延迟 350ms 再隐藏 Mask

改动最小，缺点是隐藏 mask 变慢了，350ms 还是能感觉到慢的

只需要针对 mask 做处理就行，改动非常小，如果要求不高的话，用这个比较省力

### Pointer-events

比较麻烦且有缺陷，

不建议使用

mask 隐藏后，给按钮下面元素添上 `pointer-events: none;` 样式，让 click 穿过去，350ms 后去掉这个样式，恢复响应

缺陷是 mask 消失后的的 350ms 内，用户可以看到按钮下面的元素点着没反应，如果用户手速很快的话一定会发现

### 在下面元素的事件处理器里做检测（配合全局 flag）

比较麻烦，

不建议使用:

全局 flag 记录按钮点击的位置（坐标点），在下面元素的事件处理器里判断 event 的坐标点，如果相同则是那个可恶的 click，拒绝响应

上面说的只是想法，没测试过，实在不行就用记录时间戳判断，等待 350ms，这样就和 `pointer-events` 差不多

### Fastclick

好用的解决方案，不介意多加载几 KB 的话，

不建议使用: 因为有人遇到了 bug，首先引入 fastclick 库，再把页面内所有 touch 事件都换成 click，其实稍微有点麻烦，建议引入这几 KB 就为了解决点透问题不值得，不如用第一种方法呢

# FAQ

#faq/ui

遇到问题先区分是真机的问题还是模拟器的问题

触屏事件

- touchstart mousedown
- touchmove（不可能单独触发） mousemove（可以单独触发）
- touchend mouseup

指针事件

event.cancelable

## 全面禁止事件的默认行为

通过 event.cancelable 查看默认行为是否可以被取消

由于浏览器必须要在执行事件处理函数之后，才能知道有没有掉用过 preventDefault() ，这就导致了浏览器不能及时响应滚动，略有延迟。

所以为了让页面滚动的效果如丝般顺滑，从 chrome56 开始，在 window、document 和 body 上注册的 touchstart 和 touchmove 事件处理函数，会 **默认为是 passive: true**。浏览器忽略 preventDefault() 就可以第一时间滚动了。

```js
  window.onload=function(){
    document.addEventListener("touchstart",function(ev){
      ev=ev||event;
      ev.preventDefault();
    },{passive:false})
  }
```

应用 CSS 属性 `touch-action: none;` 这样任何触摸事件都不会产生默认行为，但是 touch 事件照样触发。

touch-action 还有很多选项，详细请参考 [touch-action](https://w3c.github.io/pointerevents/#the-touch-action-css-property)

隐患：

- 连系统的滚动条也会被禁止掉，挺好的，正好使用更人性化的自定义滚动条代替之
- a 标签的跳会被禁止，一直冒泡除非所有的父级都没有禁止默认行为，只要有一个禁止默认行为，就不会跳转

## 禁止右键菜单

```html
  <body>
    <div id="wrap">
      <div id="inner">
  
      </div>
    </div>
  </body>
  <script type="text/javascript">
    window.onload=function(){
      document.oncontextmenu=function(){
        return false;
      }
  
      var wrap = document.querySelector("#wrap");
      var inner = document.querySelector("#inner");
      inner.oncontextmenu=function(ev){
        // inner禁止冒泡，无法触发document的同名事件：禁止默认行为，结果：inner内部依然可以触发浏览器的默认行为，inner以外的地方则无法触发默认行为
        ev.stopPropagation();
      }
      /*wrap.oncontextmenu=function(ev){
  				ev = ev||event;
  				ev.stopPropagation();
  			}*/
    }
  </script>
```

## 事件点透

移动端也可以使用 click 事件，但是肯定还是 touchstart 才是合理的，但是 touchstart 事件存在点透的问题

为什么会有 300 毫秒的延迟？为了判断是触发单击事件还是双击事件，判断用户是否想要整体缩放移动端页面

PC 事件也可以在移动端触发，但是 **PC 端事件** 会有 300 毫秒延迟，引入 js 库解决

- click 事件在触发的条件：点击和抬起在同一个像素点
- 点一下然后长按再松开，移动端不会有 click 事件，pc 端如果一直在同一个像素点会触发 click 事件

移动端事件不会有延迟

事件点透：可以触发重叠的兄弟的事件

touchstart --> mouseover(有的浏览器没有实现) --> mousemove(一次) -->mousedown（长按） --> mouseup --> click -->touchend

https://juejin.im/post/5b3cc9836fb9a04f9a5cb0e0

https://juejin.im/post/5cdf84e3f265da1bb564c79a

解决方案:

  1. 移动端禁止全局默认事件，全部使用 touch 事件，再使用自定的行为完成跳转等
  2. 引入 fastclick 库，FastClick 的实现原理是在检测到 touchend 事件的时候，会通过 DOM 自定义事件立即出发模拟一个 click 事件，并把浏览器在 300ms 之后真正的 click 事件阻止掉。

## 移动端跳转方案

```js
// 禁止默认行为，a标签无法跳转
window.onload=function(){
  document.addEventListener("touchstart",function(ev){
    ev=ev||event;
    ev.preventDefault();
  },{passive:false})

  //移动端a标签的跳转方案  解决误触
  var aNodes = document.querySelectorAll("a");
  for(var i=0;i<aNodes.length;i++){
    aNodes[i].addEventListener("touchstart",function(){
      this.isMoved=false;
    })
    aNodes[i].addEventListener("touchmove",function(){
      this.isMoved=true;
    })
    aNodes[i].addEventListener("touchend",function(){
      if(!this.isMoved){
        // 没有滑动，正常跳转
        location.href=this.href;
      }
    })
  }
  
}
```

## Event 3 类手指列表

```js
window.onload=function(){
  document.addEventListener("touchstart",function(ev){
    ev=ev||event;
    ev.preventDefault();
  })


  var item = document.querySelector(".item");	
  //changedTouches:触发当前事件的手指列表
  //targetTouches:触发当前事件时元素上的手指列表
  //touches:触发当前事件时屏幕上的手指列表
  item.addEventListener("touchend",function(ev){
    ev = ev||event;
    var item = document.querySelector(".item");
    item.innerHTML="changed:"+ev.changedTouches.length+"<br/>";
    item.innerHTML+="target:"+ev.targetTouches.length+"<br/>";
    item.innerHTML+="touches:"+ev.touches.length+"<br/>";

    console.log(ev)
  })
}
```

## 常见问题

### 禁止电话与邮箱

- 移动端数字和邮箱会自动变成可点击的，并且点击后唤醒电话或邮箱 app
- ```html
  <meta name="format-detection" content="telephone=no,email=no"/>
  
  <a href="tel:110">1383838438</a>
  <a href="mailto:13888@qq.com">1383838438</a>
  ```

### 链接点击高亮的问题

- 链接点击的时候，会有高亮的默认背景，可以通过 -webkit-tap-highlight-color 设置该值
- ![1549266152492](/img/user/programming/font-end/mobile/mobile-basic/1549266152492.png)
- ```css
  a{
  	-webkit-tap-highlight-color:rgba(0,0,0,0);
  }
  
  ```

### 按钮过圆的问题

- ![1549266259374](/img/user/programming/font-end/mobile/mobile-basic/1549266259374.png)
- ```css
  input{
    webkit-appearance:none;
    border-radius:5px;
  }
  ```
- ![1549266352148](/img/user/programming/font-end/mobile/mobile-basic/1549266352148.png)

### Font Boosting

**什么是 Font Boosting**

- Font Boosting 是 Webkit 给移动端浏览器提供的一个特性：当我们在手机上浏览网页时，很可能因为原始页面宽度较大，在手机屏幕上缩小后就看不清其中的文字了。而 Font Boosting 特性在这时会 **自动** 将其中的 **文字字体变大**，
- 保证在即不需要左右滑动屏幕，也不需要双击放大屏幕内容的前提下，也可以让人们方便的阅读页面中的文本。

**Font Boosting 的计算规则伪代码如下：**

```
multiplier = Math.max(1, deviceScaleAdjustment * textScalingSlider * systemFontScale * clusterWidth / screenWidth);
if (originFontSize < 16) {
		computedFontSize = originFontSize * multiplier;
}
else if (16 <= originFontSize <= (32 * multiplier - 16)) {
		computedFontSize = (originFontSize / 2) + (16 * multiplier - 8);
}
else if (originFontSize > (32 * multiplier - 16)) {
		computedFontSize = originFontSize;
}

originFontSize:原始字体大小

computedFontSize:经过计算后的字体大小

multiplier:换算系数，值由以下几个值计算得到

deviceScaleAdjustment:当指定 viewport width=device-width 时此值为 1，否则值在 1.05 - 1.3 之间，有专门的计算规则

textScalingSlider:浏览器中手动指定的缩放比例，默认为 1

systemFontScale:系统字体大小，Android设备可以在「设备 - 显示 - 字体大小」处设置，默认为 1

clusterWidth:应用 Font Boosting 特性字体所在元素的宽度

screenWidth:设备独立像素（DIPs, Density-Independent Pixels），如 iPhone 5 为 320
```

- Font Boosting 仅在未限定尺寸的文本流中有效，给元素指定宽高，就可以避免 Font Boosting 被触发。
- 但是文本内容不可能都指定宽高。不过还好，我们通过指定 max-height 就可以无副作用的禁掉 Font Boosting 特性。用类似 p { max-height: 999999px; } 的方式来处理

```css
p{
  font-size: 24px;
  /*height: 200px;*/
  max-height: 999999px; 
}
```

## 1 物理像素

https://zhuanlan.zhihu.com/p/163444644

https://segmentfault.com/q/1010000008839008

### 全局缩放

+ 使用 rem 单位的都是原始像素，使用 px 单位的实际上只有原来的一半

  ```js
  // 全局模式，淘宝方案
  window.onload=function(){
    (function(){
      var dpr  = window.devicePixelRatio||1;
      var styleNode = document.createElement("style");
      // 预先还原整体的rem值，保持布局
      var w = document.documentElement.clientWidth*dpr/16;
      styleNode.innerHTML="html{font-size:"+w+"px!important}";
      document.head.appendChild(styleNode);
  
      // 对整体进行缩放，使得px值变小
      var scale = 1/dpr;
      var meta = document.querySelector("meta[name='viewport']");
      meta.content="width=device-width,initial-scale="+scale;
    })()
  }
  ```

### Css Transform

+ ```css
  /* 纯css模式，动态类名，常用方案，兼容性稍差*/
  *{
    margin: 0;
    padding: 0;
  }
  #test{
    width: 100%;
    height:1px ;
    margin-top: 50px;
    background: black;
  }
  @media only screen and (-webkit-device-pixel-ratio:2 ) {
    #test{
      transform: scaleY(.5);
    }
  }
  @media only screen and (-webkit-device-pixel-ratio:3 ) {
    #test{
      transform: scaleY(.333333333333333333);
    }
  }
  
  /* 纯css模式，动态类名，常用方案，兼容性稍差，使用伪元素*/
  #test{
    position: relative;
    width: 200px;
    height: 200px;
    background: pink;
  }
  #test:before{
    position: absolute;
    bottom: 0px;
    content: "";
    display: block;
    width: 100%;
    height: 1px;
    background: black;
  }
  
  @media only screen and (-webkit-device-pixel-ratio:2 ) {
    #test:before{
      transform: scaleY(.5);
    }
  }
  @media only screen and (-webkit-device-pixel-ratio:3 ) {
    #test:before{
      transform: scaleY(.33333333333);
    }
  }
  ```

### 边框

left top bottom right 百分比参考包含块

```html
<style>
    .custom-border{
        width:200px;
        margin:10px auto;
        height:100px;
        border:1px solid #333;
        background-color:#eee;
        padding:10px;
    }
    .scale-border{
        margin:10px auto;
        height:100px;
        position:relative;
        padding:10px;
        width: 200px;
    }
    .border{
        position:absolute;
        -webkit-transform:scale(0.5);
        transform:scale(0.5);
        border:1px solid #333;
        top:-50%;
        bottom:-50%;
        right:-50%;
        left:-50%;
        border-radius: 10px;
        background-color:#eee;
    }
    .content{
        position:relative;
        z-index:2;
    }
</style>

<div class="custom-border">边框宽度1px</div>

<div class="scale-border">
    <div class="content">边框宽度0.5px</div>
    <div class="border"></div>
</div>
```

### 图片方案

+ border-image
+ background-image
+ 需要单独准备图片，而且圆角不是很好处理，但是可以应对大部分场景。
