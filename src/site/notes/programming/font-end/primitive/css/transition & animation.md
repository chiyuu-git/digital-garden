---
{"dg-publish":true,"permalink":"/programming/font-end/primitive/css/transition-and-animation/"}
---


# Transition 动画\\过度动画

众所周知，css 效率极高，其变化的过程往往都是在一瞬间完成，速度极快。

CSS transition 提供了一种在更改 CSS 属性时控制动画速度的方法。 其可以让属性变化成为一个持续一段时间的过程，而不是立即生效的。比如，将一个元素的颜色从白色改为黑色，通常这个改变是立即生效的，使用 CSS transitions 后该元素的颜色将逐渐从白色变为黑色，按照一定的曲线速率变化。这个过程可以自定义

css 的过渡只关心开始状态和结束状态，**中间帧是没有办法控制的**

## Transition-property

指定运用过渡动画的属性

默认值：all

(https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_animated_properties)

这个属性很常用，需要精准的控制页面的效果

```css
transition:1s opacity width ease-in-out;
```

## Transition-duration

描述：过渡动画的持续时间

默认值：0s

transition-duration,不接受负值。一定要加单位 (不能写 0 一定要写 0s 1s,0s,1s)！异常：

当指定的持续时间个数小于指定的属性时，持续时间的数组会 **复制**

如果时长列表更长，那么该列表会被 **截断**。

这两种情况下声明的 CSS 属性都是有效的。

```css
transition-property:width,background,height;
transition-duration:3s,2s,[3s,2s];
```

## Transition-timing-function

默认值：ease

CSS 属性受到 transition 的影响，会产生不断变化的中间值，而 CSS transition-timing-function 属性用来描述这个中间值是怎样计算的。实质上，通过这个函数会建立一条加速度曲线，因此在整个 transition 变化过程中，变化速度可以不断改变

你可以规定多个 timing function，通过使用 transition-property 属性，可以根据主列表 (transition property 的列表) 给每个 CSS 属性应用相应的 timing function。

- 如果 timing function 的个数比主列表中数量少，缺少的值被设置为初始值（ease） 。
- 如果 timing function 比主列表要多，timing function 函数列表会被 **截断** 至合适的大小。
- 这两种情况下声明的 CSS 属性都是有效的。

**属性值：**

- ease：（加速然后减速）默认值，ease 函数等同于贝塞尔曲线 (0.25, 0.1, 0.25, 1.0).
- linear：（匀速），linear 函数等同于贝塞尔曲线 (0.0, 0.0, 1.0, 1.0).
- ease-in：(加速)，ease-in 函数等同于贝塞尔曲线 (0.42, 0, 1.0, 1.0).
- ease-out：（减速），ease-out 函数等同于贝塞尔曲线 (0, 0, 0.58, 1.0).
- ease-in-out：（加速然后减速），ease-in-out 函数等同于贝塞尔曲线 (0.42, 0, 0.58, 1.0)
- cubic-bezier： 贝塞尔曲线
- step-start：等同于 steps(1,start)
- step-end：等同于 steps(1,end)
- steps(`<integer>`,[,[start|end]]?)
  - 第一个参数：必须为正整数，指定函数的步数
  - 第二个参数：指定每一步的值发生变化的时间点（默认值 end）

## Transition-delay

默认值：0s

属性规定了在过渡效果开始作用之前需要等待的时间。

你可以指定多个延迟时间，每个延迟将会分别作用于你所指定的相符合的 css 属性。

- 如果指定的时长个数小于属性个数，那么时长列表会 **重复**。
- 如果时长列表更长，那么该列表会被 **截断**。
- 这两种情况下声明的 CSS 属性都是有效的。

属性值以秒（s）或毫秒（ms）为单位，表明动画过渡效果将在何时开始。取值为正时会延迟一段时间来响应过渡效果；取值为 **负** 时会导致过渡立即开始。

## 简写属性 Transition

transition 是一个简写属性

注意：当属性列表长度不一的时候，与时间相关的会重复，transition-timing-function 的时候使用的是默认值 ease

CSS 过渡，由简写属性 transition 定义是最好的方式，可以避免属性值列表长度不一，节省调试时间

**注意**

在 transition 属性中，各个值的书写顺序是很重要的：

- 第一个可以解析为时间的值会被赋值给 transition-duration
- 第二个可以解析为时间的值会被赋值给 transition-delay

**推荐抒写顺序**

- margin-right 2s ease-in-out .5s;
- `transition: 2s width 3s ,3s height;`
- 2s 是 width 的 duratoin，第一个 3s 是 width 的 delay，第二个 3s 是 height 的 duration，其他属性都没有过渡动画，即默认值

**兼容性**

- transition 可以不用厂商前缀，不过鉴于标准刚刚稳定，对于基于 Webkit 的浏览器仍然需要厂商前缀。如果想兼容旧版本的浏览器那么也需要厂商前缀（例如 Firefox 15 及之前版本, Opera 12 及之前版本)

## Transitionend 事件

当过渡完成时触发一个事件，在符合标准的浏览器下，这个事件是 transitionend, 在 WebKit 下是 webkitTransitionEnd

**每一个拥有过渡的属性** 在其完成过渡时都会 **触发一次**transitionend 事件

transtionend 事件（DOM2）， 在每个属性完成过渡时都会触发这个事件

transtionend 事件很容易触发，在 **回调函数内部停止自己**

```js
testNode.addEventListener("transitionend",function(){
  alert("end");
 })
testNode.addEventListener("webkitTransitionEnd",function(){
  alert("end");
 })
```

在 transition 完成前设置 display: none，事件同样不会被触发

```js
this.style.width = this.offsetWidth+100+"px";
this.style.background = "pink";
this.style.display="none"; //没有过渡动画，没有过渡事件

setTimeout(function(){
  testNode.style.display="none";
},2000)
```

本质：页面的渲染是异步操作，JS 代码已经执行完了，要渲染的内容已经优化后保存在内存中，页面才开始渲染。

既然是 display:none;，根本就没必要渲染宽度和背景，所以也就不存在过渡

## 过渡动画的坑 1：样式读取与渲染是异步的

```css
#test{
  width: 100px;
  height: 100px;
  background: pink;
  text-align: center;
  position: absolute;
  left: 0;
  right: 0;
  bottom: 0;
  top: 0;
  margin: auto;

  transition-property: width;
  transition-duration: 2s;
  transition-timing-function: linear;
 }

 body:hover #test{
  transition-property: height;
  width: 200px;
  height: 200px;
 }
```

移入的时候，hover 代码读取完毕，页面还没有开始渲染，此时内存中系统要渲染的属性已经变成了 height，所以移入的时候 height 有过渡动画，同理，移出的时候 width 有过渡动画

本质：页面的渲染是异步操作，代码已经执行完了，要渲染的内容已经优化后保存在内存中，页面才开始渲染

## 过渡动画的坑 2

transition 在元素首次渲染还没有结束的情况下是不会被触发的

本质：页面的渲染是异步操作，代码已经执行完了，要渲染的内容已经优化后保存在内存中，页面才开始渲染

相当于是直接渲染了 JS 代码指定的样式，所以不存在样式的变化，也就没有过渡动画了。同理：在 css 声明块中，改变样式，也不会有过渡效果

![1548826061400](/img/user/programming/font-end/primitive/css/css-3/1548826061400.png)

```js
window.onload=function(){
 var test = document.createElement("div");
 test.id="test";
 document.documentElement.appendChild(test);
 setTimeout(function(){
  test.style.width="300px";
 },2000)
  /*添加延时，观察过渡*/
```

## 过渡动画的坑 3：属性对应

在绝大部分变换样式切换时,如果变换函数的 **位置 个数** 不相同也不会触发过渡

```css
rotate(0deg),scale(1);
rotate(720deg); 
/*两种样式切换，不会有过渡动画，因为数量不一样*/

rotate(0deg),scale(1);
scale(1),rotate(720deg);

/*两种样式切换，不会有过渡动画，因为样式位置不一样*/
```

## Transtion 动画生效的前提

但是仔细思考之后, 发现只需要一个状态来控制就足够了. 因为可以把 transition 融合到 transform 的样式类中:

+ 样式 A 声明 transform
+ 样式 B 声明 transition + transform, 这样切换样式 B 有 transition 效果, 切换回样式 A 无 transition 效果

# 2D 变换

transform 属性允许你修改 CSS 视觉格式模型的坐标空间

transform 属性 , 只对 **block 级** 元素生效！

一般 transform 属性都会配合 transition 属性使用

本质上操作的都是图形渲染基于的坐标轴

## 旋转

正值: 拉 **正斜杠方向** 的两个角

负值: 拉 **反斜杠方向** 的两个角

斜率为正，正斜杠，斜率为负，反斜杠 @@@

transform:rotate(angle);

- 正值: 顺时针旋转 rotate(360deg)
- 负值: 逆时针旋转 rotate(-360deg)

只能设单值，正数表示顺时针旋转，负数表示逆时针旋转

绕着 **Z 轴** 旋转，z 从屏幕里到外

## 平移

X 方向平移:transform: translateX(tx)

Y 方向平移:transform: translateY(ty)

二维平移：transform: translate(tx[, ty])； 如果 ty 没有指定，它的值默认为 0。

- 可设单值，也可设双值。
- 正数表示 XY 轴正向位移，负数为反向位移。设单值表示只 X 轴位移，Y 轴坐标不变，
- 例如 transform: translate(100px); 等价于 transform: translate(100px,0);

translate 的性能比 left top 更好，而且很多时候不会触发重新布局

## 倾斜

transform:skewX(45deg);

![1548827557516](/img/user/programming/font-end/primitive/css/css-3/1548827557516.png)

X 方向倾斜:transform: skewX(angle)

skew**X**(45deg): 参数值以 deg 为单位 代表与 **y** 轴之间的角度

Y 方向倾斜:transform: skewY(angle)

skew**Y**(45deg): 参数值以 deg 为单位 代表与 **x** 轴之间的角度

二维倾斜:transform: skew(ax[, ay]); 如果 ay 未提供，在 Y 轴上没有倾斜

- skew(45deg,15deg)
- 参数值以 deg 为单位
- 第一个参数代表与 **y** 轴之间的角度（x 轴扭曲）
- 第二个参数代表与 **x** 轴之间的角度

单值时表示只 X 轴扭曲，Y 轴不变

- 如 transform: skew(30deg); 等价于 transform: skew(30deg, 0);
- 考虑到可读性，不推荐用单值，应该用 transform: skewX(30deg);

→X 轴 ↓Y 轴 正方向

## 缩放

X 方向缩放:transform: scaleX(sx);

Y 方向缩放:transform: scaleY(sy);

二维缩放 :transform: scale(sx[, sy]); (如果 sy 未指定，默认认为和 sx 的值相同)

要缩小请设 0.01～0.99 之间的值，要放大请设超过 1 的值。

- 缩小一倍可以 transform: scale(.5);
- 放大一倍可以 transform: scale(2);

如果只想 X 轴缩放，可以用 scaleX(.5) 相当于 scale(.5, 1)。

想 Y 轴缩放，可以用 scaleY(.5) 相当于 scale(1, .5)

正值: 缩放的程度

负值: 不推荐使用（有上下颠倒的效果）

![img](/img/user/programming/font-end/primitive/css/css-3/608782-20190809162611846-150362027.gif)

单值时表示只 X 轴,Y 轴上缩放程度一样，如 transform: scale(2); 等价于 transform: scale(2,2);

## 基点的变换

transform-origin CSS 属性让你更改一个元素变形的基点，会影响到 **除了位移之外的属性**

**一个值：**

- 必须是 `<length>`，`<percentage>`，或 left, center, right, top, bottom 关键字中的一个。

**两个值：**

- 其中一个必须是 `<length>`，`<percentage>`，或 left, center, right 关键字中的一个。
- 另一个必须是 `<length>`，`<percentage>`，或 top, center, bottom 关键字中的一个。

**三个值：**

- 前两个值和只有两个值时的用法相同。
- 第三个值必须是 `<length>`。它始终代表 Z 轴偏移量。
- 像素值和百分比值都是按照自己本身的大小

  ```css
  transform: scale(3);
  transform-origin: top left;
  ```

## 矩阵

在底层，所有的变换都是以矩阵为基础

在 2D 变换 中，矩阵变换函数 matrix() 接受 6 个值，语法形式如下：

transform: matrix(a, b, c, d, e, f);![1548827958050](/img/user/programming/font-end/primitive/css/css-3/1548827958050.png)

这相当于，对元素应用一个如下的变换矩阵：

- 点 (Xi,Yi,1) 进行变换后的新坐标
- ![1548827973791](/img/user/programming/font-end/primitive/css/css-3/1548827973791.png)
- -1 法则，横的竖的对应位置分别相乘后相加
- 即根据变换矩阵进行变换之后点 (xi,yi) 的坐标是 (axi+cyi+e,bxi+dyi+f)

**平移**

- 对某一元素应用旋转变换 translate(X, Y)，相当于对其应用如下变换矩阵：![1548828061782](/img/user/programming/font-end/primitive/css/css-3/1548828061782.png)
- 即等价于使用矩阵变换函数：matrix(1, 0, 0, 1, X, Y)。

**旋转**

- 对某一元素应用旋转变换 rotate(θ)，相当于对其应用如下变换矩阵：![1548828099912](/img/user/programming/font-end/primitive/css/css-3/1548828099912.png)
- 即等价于矩阵变换函数：matrix(cosθ, sinθ, -sinθ, cosθ, 0, 0)。
- ```css
  transform: matrix(0.7071067811865476, 0.7071067811865475, -0.7071067811865475, 0.7071067811865476, 0, 0);
  ```
- 接收的值是弧度值

**倾斜**

- 对某一元素应用倾斜变换 skew(α, β)，相当于对其应用如下变换矩阵：![1548828143336](/img/user/programming/font-end/primitive/css/css-3/1548828143336.png)
- 即等价于使用矩阵变换函数：matrix(1, tanβ, tanα,1, 0, 0)。

**缩放**

- 对某一元素应用缩放变换 scale(scaleX, scaleY)，相当于对其应用如下变换矩阵：![1548828174766](/img/user/programming/font-end/primitive/css/css-3/1548828174766.png)
- 即等价于使用矩阵变换函数：matrix(scaleX, 0, 0, scaleY, 0, 0)

## 变换组合

简写属性：顺序不一样，变化结果也不一样，矩阵的运算是不可逆的

组合的时候，计算方向从右往左

```css
transform:scale(.5) translateX(100px);
transform:translateX(100px) scale(.5)
```

理解：实现的原理是从右往左进行矩阵变化，但是从结果来看可以这样理解：从左往右，坐标轴也跟着缩放了，所以移动了 50px，而平移不会影响坐标轴，100px，缩放

```css
transform: rotate(180deg) translateX(100px);
transform: translateX(100px) rotate(180deg) ;
```

理解：从右往左，坐标轴也会跟着旋转，所以 1 变成向左位移，2 右位移

## Demo 扇形导航

坑 3，动画样式 A 元素触发 B 元素，改变 A 样式，B 元素点击触发动画，改变 B 样式，为了避免坑 3，AB 样式的值两个动画都要有

不可以把 A 的事件绑定给 wrap，因为这种情况下 B 的冒泡也会触发 A 的事件

定义专门的组件处理这种事件，vue 的那个 transform 动画

## Demo 时钟

刻度：调整基点，旋转 li

可以画的东西其实很多，可以填充 Box，可以填充背景，不一定都要是导入的图片，现在 css 很多样式了

```js
move(); //在定时器开始之前，先执行一次，把位置占好
setInterval(move,1000);
function move(){
 var date = new Date();
 var s = date.getSeconds();
 var m = date.getMinutes()+s/60;
 var h = date.getHours()+m/60;
 
 hourNode.style.transform="rotate("+(30*h)+"deg)";
 minNode.style.transform="rotate("+(6*m)+"deg)";
 secNode.style.transform="rotate("+(6*s)+"deg)";
}
```

# 3D 变换

在浏览器中，X 轴是从左到右，Y 轴是从上到下，Z 轴是从里到外

![1548828412144](/img/user/programming/font-end/primitive/css/css-3/1548828412144.png)

## 景深 (perspective)

景深（英语：Depth of field, DOF）景深是指相机对焦点前后相对清晰的成像范围。在光学中，尤其是录影或是摄影，是一个描述在空间中，可以清楚成像的距离范围。虽然透镜只能够将光聚 到某一固定的距离，远离此点则会逐渐模糊，但是在某一段特定的距离内，影像模糊的程度是肉眼无法察觉的，这段距离称之为景深。当焦点设在超焦距处时，景深 会从超焦距的一半延伸到无限远，对一个固定的光圈值来说，这是最大的景深

简单的理解，景深就是我们的肉眼距离显示器的距离，景深越大，元素离我们越远，效果就不好，在我们 CSS3 中，perspective 用于 **激活一个 3D 空间**，属性值就是景深大小（默认 none 无景深）

如果值为 100px，就是设定肉眼里显示器是 100px 距离，如果元素运动到 Z 100，就会完全贴在显示器上（相当于是贴在我们眼睛上的效果）

应用景深的元素称为“**舞台元素**”，舞台元素的所有后代元素都会受影响，（**如果后代元素中也添加了 perspective 属性，效果会叠加而不是覆盖**）

## Transform: perspective(depth);

depth 的默认值是 none，可以设置为一个长度值，这个长度是沿着 Z 轴距离坐标原点的距离。**1000px 被认为是个正常值**

**如果和元素的大小一样，会在屏幕上留下拖影**

若使用 perspective() 函数，**那么他必须被放置在 transform 属性的首位**，如果放在其他函数之后，则会被忽略，还要注意过渡动画的坑 3，保留其他过渡属性的临界值，所以不推荐使用

## Perspective: Depth;

同 perspective() 函数一样，depth 的默认值是 none，可以设置为一个长度值，这个长度是沿着 Z 轴距离坐标原点的距离。

他们唯一的区别是，**perspective 属性是被用于元素的后代元素，而不是元素本身；**就是说，为某个元素设置 perspective 属性后，是对这个元素的后代元素起作用，而不是这个元素本身。 @@@

不可继承属性

## 景深叠加问题

尽量避免景深叠加

景深作用于后代元素，祖先元素最好只有一个开启景深

## Perspective-origin

景深的基点：控制视角的位置

同 perspective 属性，也是设置在父元素上，**对后代元素起** 作用。 这个属性来设置你在 X, Y 轴坐标确定的那个点来看这个元素，Z 轴是被 perspective 属性设置的

有默认值，跟盒模型有关，50% 50% “XXX-box”

## 灭点

指的是立体图形各条边的延伸线所产生的相交点。透视点的消失点

![1548833842422](/img/user/programming/font-end/primitive/css/css-3/1548833842422.png)

景深的本质：控制灭点的远近

- 景深越大 灭点越远 元素变形更小
- 景深越小 灭点越近 元素变形更大

## 3D 旋转

CSS3 中的 3D 旋转主要包括四个功能函数

**rotateX(angle)**

- 本质：把 Box 压向 X 轴，加了景深之后才会有立体感
- ![1548833880823](/img/user/programming/font-end/primitive/css/css-3/1548833880823.png)

**rotateY(angle)**

- 本质：把 Box 压向 Y 轴，加了景深之后才会有立体感
- ![1548833897825](/img/user/programming/font-end/primitive/css/css-3/1548833897825.png)

**rotateZ(angle)**

- 等价于 rotate(angle)
- ![1548833924544](/img/user/programming/font-end/primitive/css/css-3/1548833924544.png)

**rotate3d(x,y,z,angle)**

- x, y, z 分别接受一个数值 (number),用来计算 **矢量方向**(direction vector)
- **矢量方向** 是三维空间中的一条线，从坐标系原点到 x, y, z 值确定的那个点，元素围绕这条线旋转 angle 指定的值
- 如果传入一个 **正角度**，元素沿着轴 **逆时针** 转动
- 轴上的点都是一样的效果，发出射线之后与轴重合了，

## 3D 平移

transform: translateZ(length) 是 3D Transformaton 特有的，其他两个 2D 中就有

translateZ 它不能是百分比 值; 那样的移动是没有意义的。因为百分比参照自己的厚度，没有厚度，所以百分比没有意义

transform: translate3d(translateX,translateY,translateZ);

一旦使用了 translate3d，必须 xyz 都指定，否则无效

translate3d，三个都要指定，0 或者 0px 都可以，margin 也是 0 和 0px 都可以，但是秒数的指定 0s，指定 0 会报错 @@@

## Transform-style

这个属性指定了 **子元素** 如何在空间中展示，只有两个属性值：flat（默认）和 preserve-3d

flat 表示所有 **子元素** 在 2D 平面呈现，

preserve-3d 表示所有子元素在 3D 平面呈现，

如果被扁平化，则子元素不会独立的存在于三维空间。

因为该属性不会被（自动）继承，**所以必须为元素所有需要 3D 呈现的后代设置该属性**

## Backface-visibility

backface-visibility 属性用来设置，是否显示元素的背面，默认是显示的。

backface-visibility: keyword;

keyword 有两个值，hidden 和 visible，默认值是 visible。

CSS reset 有的会设置标签 a 的该值为 hidden

![1548834193344](/img/user/programming/font-end/primitive/css/css-3/1548834193344.png)

开启了透明之后可以看到背后的元素

## 立方体

**方法一**

- 六个面，每个面都是一个 div
- 控制每个面的位置，然后折叠成一个立方体
- transform-origin:bottom

**方法二**

- 直接在开启了定位，重合的基础上，设置基点为立方体的中心点旋转即可

## 定义正立方体组件

找到立方体的中心，然后各个面平均旋转，顶面和底面先不做处理

旋转的度数是 N 个外角 360/角数 得到每一个外角的大小

旋转的基点：中线的交点，重心

取消基点的过渡

因为子元素在父元素内部的 3d 舞台，所以子元素改变了宽高，父元素也要做相应的改变

**替换成照片 3D 展示效果**

# Animation 动画\帧动画

人类具有视觉暂留的特性，人的眼睛在看到一幅画或一个物体后，在 0.34 秒内不会消失。

动画原理：

- 通过把人物的表情、动作、变化等分解后画成许多动作瞬间的画幅，利用视觉暂留的原理，
- 在一幅画还没有消失前，播放下一幅画。就会给人造成一种流畅的视觉变化效果。

## Css3 动画

使元素从一种样式逐渐变化为另一种样式的效果

animation 属性是一个简写属性形式: （可以用来描述可动画的属性）

可动画属性的列表：

- > https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_animated_properties

简写属性 animation

- animation-name
- animation-duration
- animation-timing-function
- animation-delay
- animation-iteration-count
- animation-direction
- animation-fill-mode
- animation-play-state

在每个动画定义中，顺序很重要：可以被解析为 `<time>` 的第一个值被分配给 animation-duration, 第二个分配给 animation-delay。

## 关键帧（@keyframes）

```css
@keyframes animiationName{
  keyframes-selector{ /*keyframes-selector：必写项，动画持续时间的百分比*/
    css-style;
  }
}

@keyframes move{/*animiationName:必写项，定义动画关键帧的名称*/
  from{ /*0%*/
    transform: rotate(0deg);
  }
  to{ /*100%*/
    transform: rotate(300deg);
  }
}

@keyframes move{
  0%{
    transform: translateY(-100px);
  }
  25%{
    transform: translateY(-90px);
  }
  50%{
    transform: translateY(0px);
  }
  75%{
    transform: translateY(90px);
  }
  100%{
    transform: translateY(100px);
  }
}
```

百分比是动画持续时间的百分比

每个百分比之间被称为一个 **关键帧周期**，全部关键帧在一起就是动画周期

**animation-timing-function 作用与一个关键帧周期**

## Animation-name

animation-name 属性指定应用的一系列动画，每个名称代表一个由@keyframes 定义的动画序列

**值:**

- none 特殊关键字，表示无关键帧。
- keyframename 标识动画的字符串

## Animation-duration

animation-duration 属性指定一个 **动画周期** 的时长。

默认值为 0s，表示无动画。

值：一个动画周期的时长，单位为秒 (s) 或者毫秒 (ms)，无单位值无效。

注意：负值无效，浏览器会忽略该声明，但是一些早起的带前缀的声明会将负值当作 0s

## Animation-timing-function

animation-timing-function 属性定义 CSS 动画在每一动画周期中执行的节奏。

对于关键帧动画来说，timing function 作用于一个 **关键帧周期** 而非整个动画周期，即从关键帧开始，到关键帧结束。

对于一个动画周期而言，会作用到每个关键帧，每个关键帧都是 ease-in 由慢到快，类似于兔子跳

动画的默认效果：由慢变快再变慢

**属性值：**

- linear: 线性过渡，等同于贝塞尔曲线（0,0,1,1）
- ease: 平滑过渡，等同于贝塞尔曲线（0.25,0.1,0.25,1.0）
- ease-in: 由慢到快，等同于贝塞尔曲线（0.42,0,1,1）
- ease-out: 由快到慢，等同于贝塞尔曲线（0,0,0.58,1）
- ease-in-out: 由慢到快再到慢，等同于贝塞尔曲线（0.42,0,0.58,1）
- cubic-bezier(1,1,2,3)
- steps(n,[start|end])
  - 传入一到两个参数，第一个参数意思是把动画分成 n 等分，然后动画就会平均地运行。
  - 第二个参数 start 表示从动画的开头开始运行，相反，end 就表示从动画的结尾开始运行
  - 默认值为 end(相当于一步跳到关键帧结束的状态)
  - steps(5, end)
    - There is 5 treads, the last one happens right before the end of the animation.
    - 如果不到 end 动画结束，最后一帧的关键帧动画不会执行，而回等待，当指定了 direction 属性的时候会发生

## Animation-delay

定义动画开始前等待的时间,以秒或毫秒计 (属于动画外的范畴)

值:`<time>`

从动画样式应用到元素上到元素开始执行动画的时间差。该值可用单位为秒 (s) 和毫秒 (m s)。如果未设置单位，定义无效

## Animation-iteration-count

定义了动画执行的次数（属于动画内的范畴）

即使定义了延时，也只会延迟一次，因为 delay 属于动画外范畴

重复的是关键帧

**值**

- 默认值 为 1
- infinite 无限循环 **播放动画**.
- `<number>` 动画播放的次数 不可为负值.

## Animation-direction

定义了动画执行的方向

**属性值**

- normal
  - 每个循环内动画向前循环，换言之，每个动画循环结束，动画重置到起点重新开始，这是默认属性。
- alternate
  - 动画交替反向运行，反向运行时，动画按步后退，同时，带时间功能的函数也反向，比如，ease-in 在反向时成为 ease-out。计数取决于开始时是奇数迭代还是偶数迭代
- reverse
  - 反向运行动画，每周期结束动画 **由尾到头运行**。
  - 反转的是关键帧和 animation-timing-function
  - 加速运动，变成减速运动
- alternate-reverse
  - 反向交替， 反向开始交替

## Animation-fill-mode

属于动画外的范畴，定义动画在动画外的状态

- from 之前，to 之后

**属性值：**

- animation-fill-mode: none
- animation-fill-mode: backwards
  - 动画开始前和 from 的状态保持一致
- animation-fill-mode: forwards
  - 动画结束后和 to 之后的状态一致
- animation-fill-mode: both
  - both
  - ![1548837008768](/img/user/programming/font-end/primitive/css/css-3/1548837008768.png)

## Animation-play-state

定义了动画执行的运行和暂停

**属性值**

- running
  - 当前动画正在运行。
- paused
  - 当前动画以被停止。

```css
#wrap:hover #test{
  animation-play-state: paused;
 }
```

## Animation 简写属性

**初始值：**

- [`animation-name`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-name): `none`
- [`animation-duration`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-duration): `0s`
- [`animation-timing-function`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-timing-function): `ease`
- [`animation-delay`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-delay): `0s`
- [`animation-iteration-count`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-iteration-count): `1`
- [`animation-direction`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-direction): `normal`
- [`animation-fill-mode`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-fill-mode): `none`
- [`animation-play-state`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-play-state): `running`

**适用元素：**all

**是否可继承：**no

- 在每个动画定义中，顺序很重要：
  - 可以被解析为 `<time>` 的第一个值被分配给 animation-duration,
  - 第二个分配给 animation-delay。

## Animationend

## Animationstart

## Animated-properties

https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_animated_properties

## 与 Trasition 的对比

## 兔斯基

position -576px 0 ，拉到了尽头，最后一帧是空白，通过 step（12，end）巧妙的跳过最后一帧

## 开机动画

transform 只对块级元素有效，所以无法用 transform 控制 span 的移动，开启定位，用 top

过渡动画的坑，多属性全部都要带上

资源加载的百分比

获取后台的所有资源的地址的数组

每次请求成功 flag 加 1，与数组的长度做比较

# FAQ

#faq/ui

## Transform 读写函数

**描述**

+ 因为 transform 不同的属性值顺序会对最终效果造成影响，每次修改单个属性值都需要恰当的位置插入，比较繁琐，所以封装要给函数用于读写 transform 的属性值

**基本逻辑**

1. 在 DOM 结点上保存一个 transform 对象，transform 对象的各个属性值对应
2. 写操作，修改 transform 对象的属性值，然后循环拼串输出对象的每个属性，赋值给 style
3. 读操作，返回 transform 对象的对应值即可

**实现**

+ 改成 codePen 的 demo
