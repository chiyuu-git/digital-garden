---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-04-26-Wed, 11:29:58 am","date-modified":"2023-12-03-Sun, 11:30:57 am","permalink":"/programming/front-end/primitive/html/canvas/","dgPassFrontmatter":true}
---


# 细节

默认背景颜色和谁一样

canvas 本身也是一张图片，可以保存为 png 格式

canvas 图片操作，必须要等到图片加载之后

canvas 的像素点，默认是黑色透明（0，0，0，0）

路径方法的参数都不需要单位，包括 `x,y` 的坐标，路径的宽高等

> 适配之后的影响？

canvas 是同步渲染的，其他是异步渲染

# 理解

`<canvas>` 是 HTML5 新增的 **元素**，可用于通过使用 **JavaScript** 中的脚本来绘制图形

可以用于绘制图形，创建动画。

`<canvas>` 最早由 Apple 引入 WebKit

我们可以使用 `<canvas>` 标签来定义一个 canvas 元素

- 使用 `<canvas>` 标签时，建议要成对出现，不要使用闭合的形式。
- canvas 元素默认具有高宽
- width:300px
- height:150px

# 替换内容

`<canvas>` 很容易定义一些替代内容。由于某些较老的浏览器（尤其是 IE9 之前的 IE 浏览器）不支持 HTML 元素 "canvas"，但在这些浏览器上你应该要给用户展示些替代内容。

这非常简单: 我们只需要在**`<canvas>`**标签中提供替换内容就可以。

- 支持**`<canvas>`**的浏览器将会忽略在容器中包含的内容，并且只是正常渲染 canvas。
- 不支持**`<canvas>`**的浏览器会显示代替内容

```html
<canvas>您的浏览器不支持canvas，请尽快升级</canvas>
```

# Canvas 宽高

`<canvas>` 看起来和 `<img> ` 元素很相像，唯一的不同就是它并 **没有 src 和 alt 属性**。

实际上，**`<canvas>`** 标签 **只有两个属性**—— width 和 height，这些都是可选的。

- 当没有设置宽度和高度的时候，canvas 会初始化宽度为 300 像素和高度为 150 像素。
- 宽高是 canvas 标签的属性，attribute，而不是 style 样式的一种

画布的宽高

- html 属性设置 width height 时只影响画布本身不影画布内容
- css 属性设置 width height 时不但会影响画布本身的高宽，还会使画布中的内容等比例缩放（缩放参照于画布默认的尺寸），只设置其中一个，另一个也会等比例缩放
- 在画布的宽高基础上绘制完成之后，再根据 style 的宽高进行等比例缩放
- 实际使用的时候，不建议在 css 属性设置画布的宽高

当 styleWidth 的值为 `<length>`、`<percentage>` ，且 styleHeigth 为 auto 时

需要根据 canvas 画布的宽高比，计算出相应的 styleHeight，维持画布的宽高比（styleWidth 为 auto 时类似）

当 styleWidth 和 styleHeight 均为 auto 时，canvas 画布的宽高即为 canvas 元素的宽高

# 渲染上下文（画笔）

`<canvas>` 元素只是创造了一个固定大小的画布，要想在它上面去绘制内容，我们需要找到它的渲染上下文

`<canvas>` 元素有一个叫做 **getContext()** 的方法，这个方法是用来获得渲染上下文和它的绘画功能。

getContext()**只有一个参数，**上下文的格式

```js
//获取方式
var canvas = document.getElementById('box');
var ctx = canvas.getContext('2d');
//检查支持性
var canvas = document.getElementById('tutorial');
if (canvas.getContext){
var ctx = canvas.getContext('2d');
} 
```

# 绘制矩形路径

canvas 只支持一种 **原生** 的图形绘制: 矩形。所有其他的图形的绘制都至少需要生成一条 **路径**

## 绘制矩形的方法

canvas 提供了三种方法绘制矩形:

1. 绘制一个填充的矩形（填充色默认为黑色）
    - `fillRect(x, y, width, height)`
    - 矩形路径 +fill 方法

2. 绘制一个矩形的边框（默认边框为: 一像素实心黑色）
    - `strokeRect(x, y, width, height)`
    - 矩形路径 +stroke 方法

  3. 清除指定矩形区域，让清除部分 **完全透明**。
     - 本质: 画了一个 **底色填充** 的矩形
     - `clearRect(x, y, width, height)`

x 与 y 指定了在 canvas**画布上** 所绘制的矩形的 **左上角**（相对于 **画布的左上角**）的坐标

**不可以加单位 px**，canvas 没有单位，像素，弧度都不需要单位

width 和 height 设置矩形的尺寸。（存在边框的话，**边框会在 width 上占据半个边框的宽度**，height 同理，border-box）

## strokeRect 时，边框像素渲染问题

按理渲染出的边框应该是 1px 的，

canvas 在渲染矩形边框时，边框宽度是平均分在偏移位置的两侧。

`context.strokeRect(10,10,50,50)` 边框会渲染在 10.5 和 9.5 之间,浏览器是不会让一个像素只用自己的一半的，相当于边框会渲染在 9 到 11 之间

`context.strokeRect(10.5,10.5,50,50)` 边框会渲染在 10 到 11 之间

  ```js
//拿到画布
var canvas = document.querySelector("#test");
if(canvas.getContext){
  var ctx = canvas.getContext("2d");
  //注意不加单位
  //填充的矩形
  ctx.fillRect(0,0,100,100) //2px边框
  //带边框的矩形  
  // 100	: 99.5 --- 100.5(99-101)
  // 100.5: 100  --- 101 
  ctx.strokeRect(100.5,100.5,100,100) //1px边框
  
  /*
  刚好留下1px的边框
  ctx.strokeRect(100,100,100,100)
  ctx.clearRect(100,100,100,100)
  */
}  
  ```

## 添加样式和颜色

fillStyle() : 设置图形的 **填充** 颜色。

- 作用于 fillRect 方法的画笔
- 作用于 fill()

strokeStyle() : 设置图形 **轮廓** 的颜色。

- 作用于 strokeRect 方法的画笔
- 作用于描边路径
- 默认情况下，线条和填充颜色都是黑色（CSS 颜色值 `#000000`）

lineWidth() : 这个属性设置当前 **绘线** 的粗细。属性值必须为正数

- 作用于 strokeRect 方法的画笔
- 作用于描边路径
- 描述线段宽度的数字。 0、 负数、 Infinity 和 NaN 会被忽略。
- 默认值是 1.0。
- 同样会被均分到偏移边界的两边

## Canvas 独特的渲染机制

因为 JS 性能远大于浏览器渲染的能力，所以在渲染之前，相关的渲染信息都被保存到内存里了，再渲染到页面

- 异步渲染

Canvas 自己的渲染机制，同样性能极高，绘制的样式必须在绘制之前指定

- 同步渲染，画图形之前画笔就沾好颜色、浓度了

    ```js
    ctx.fillStyle="deeppink";
    ctx.strokeStyle="pink";
    ctx.lineWidth=25;
    ctx.lineJoin="round";
    
    ctx.strokeRect(100,100,100,100)
    ctx.fillRect(0,0,100,100)
    
    ctx.fillStyle="blue";
    ctx.strokeStyle="blue";
    ctx.lineWidth=1;
    ctx.lineJoin="round";
    
    //还是粉色系的
    ```

## lineWidth & 覆盖渲染

类似于 ps 的图层，所有的矩形都是一层层画上去的，后面的会覆盖前面的

所以 clearRect 必须指定在后面才有用

## lineJoin

只作用于 strokeRect 方法的画笔

设定线条与线条间接合处的样式（默认是 miter）

- round : 圆角
- bevel : 斜角
- miter : 直角

# 绘制直线路径

图形的基本元素是 **路径**。路径是通过不同颜色和宽度的线段或曲线相连形成的不同形状的点的集合。

## 步骤

    1. 首先，你需要创建路径 **起始点**。
    2. 然后你使用画图命令去画出路径
    3. 之后你把路径 **封闭**。
    4. 一旦路径生成，你就能通过描边或填充路径区域来渲染图形。

## **moveTo(x, y)**

**抬起画笔**，将笔触移动到指定的坐标 x 以及 y 上

**设置起点**（路径）

当 canvas 初始化或者 beginPath() 调用后，你通常会使用 moveTo() 函数设置起点

## lineTo(x, y)

**不抬起画笔**，将笔触移动到指定的坐标 x 以及 y 上

**绘制** 一条从当前位置到指定 x 以及 y 位置的 **路径**。

**设置锚点**

## closePath()

闭合 **路径** 之后图形绘制命令又重新指向到 **上下文** 中。

闭合路径 closePath(),不是必需的。这个方法会通过绘制一条从 **当前点到开始点的直线** 来闭合图形。

如果图形是已经闭合了的，即当前点为开始点，该函数什么也不做

当你调用**fill()**函数时，所有没有闭合的形状都会自动闭合，所以你不需要调用 closePath() 函数。

但是调用**stroke()**时不会自动闭合

## stroke()

通过线条来绘制图形轮廓。

给 **路径** 填色

不会自动调用 closePath()，所以不会有当前点到开始点的线条

  ```js
var ctx = canvas.getContext("2d");

ctx.strokeStyle="deeppink";
ctx.lineWidth=10;

ctx.moveTo(100,100);
ctx.lineTo(100,200);
ctx.lineTo(200,200);
// ctx.closePath();
ctx.stroke();
  ```

![1547519122564](/img/user/programming/front-end/primitive/html/canvas/1547519122564.png)

## fill()

通过填充路径的内容区域生成 **实心的** 图形。

自动调用 closePath()

## **beginPath()**

新建一条路径，生成之后，图形绘制命令被指向到路径上准备生成路径。

生成路径的第一步叫做 beginPath()。本质上，路径是由很多子路径构成，这些子路径都是在一个列表中，

所有的子路径（线、弧形、等等）构成图形。而每次这个方法调用之后，**列表清空** 重置，然后我们就可以重新绘制新的图形。

**moveTo,lineTo,**本质上画的都是** 路径**，路径本身是没有颜色、粗细的，所以看不见，stroke 是给路径填色，fill 是给路径封闭的区域填色

  ```js
ctx.moveTo(100,100);
ctx.lineTo(100,200);
ctx.lineTo(200,200);
ctx.closePath();
ctx.fill();

//清空路径容器
ctx.beginPath();
ctx.moveTo(200,200);
ctx.lineTo(200,300);
ctx.lineTo(300,300);
ctx.closePath();
ctx.stroke(); //上一个三角形不会被描边
  ```

  ```js
ctx.strokeStyle="pink";
ctx.fillStyle="green"
ctx.lineWidth=4;

ctx.moveTo(100,100);
ctx.lineTo(50,200);
ctx.lineTo(150,80);
ctx.lineTo(150,100);

ctx.moveTo(200,200); //新的路径起点，同一个路径容器
// ctx.lineTo(200,200);
ctx.lineTo(200,300);
ctx.stroke();
  ```

## rect(x, Y, Width, height)

绘制一个左上角坐标为（x,y），宽高为 width 以及 height 的 **矩形路径**。

当该方法执行的时候，**moveTo()**方法自动设置坐标参数（0,0）。

也就是说，当前笔触自动重置会默认坐标

## lineCap

lineCap 是 Canvas 2D API 指定如何绘制每一条线段末端的属性。

有 3 个可能的值，分别是:

    1. butt : 线段末端以方形结束。
    2. round : 线段末端以圆形结束，**增加了** 半径为厚度一半的一个圆形区域
    3. square: 线段末端以方形结束，但是 **增加了** 一个宽度和线段相同，高度是线段厚度一半的矩形区域

默认值是 butt。

## **save**

save() 是 Canvas 2D API 通过将当前状态放入 **栈** 中，保存 canvas 全部状态的方法

保存到栈中的绘制状态有下面部分组成:

    1. 当前的变换矩阵，Canvas的变换也是以矩阵的形式进行的

    2. 当前的剪切区域。
    3. 当前的虚线列表.
    4. 以下属性当前的值:

       - strokeStyle, 
       - fillStyle,  
       - lineWidth, 
       - lineCap, 
       - lineJoin...

## **restore**

restore() 是 Canvas 2D API 通过在绘图状态栈中 **弹出** 顶端的状态，将 canvas 恢复到最近的保存状态的方法。

如果没有保存状态，此方法不做任何改变。

## 样式栈、样式容器（堆）、路径容器（堆）

每次调用样式 api 时,都会往样式容器里做登记

save 方法，把当前样式容器的状态压入到栈中

restore 把栈顶的状态弹出，覆盖当前样式容器的状态

路径容器与绘图状态无关，仅保存路径相关的信息，每次调用路径 api 时,都会往路径容器里做登记，调用 beginPath 时,清空整个路径容器

  ```js
/*canvas绘制模板*/
ctx.save();
//关于样式的设置
//save  restore成对出现
ctx.beginPath();
//关于路径
ctx.restore();
  ```

## Demo: 签名

  ```js
window.onload=function(){
  var canvas =document.getElementById("test");
  if(canvas.getContext){
    var ctx = canvas.getContext("2d");
  }

  canvas.onmousedown=function(event){
    event = event || window.eventent;
    if(canvas.setCapture){
      canvas.setCapture();
    }
    ctx.beginPath();
    ctx.moveTo(event.clientX -canvas.offsetLeft,event.clientY -canvas.offsetTop);
    
    document.onmousemove=function(event){
      ctx.save();
      ctx.strokeStyle="pink";
      event = event || eventent;
      ctx.lineTo(event.clientX -canvas.offsetLeft,event.clientY -canvas.offsetTop);
      ctx.stroke();
      ctx.restore();
    }
    
    document.onmouseup=function(){
      document.onmousemove=document.onmouseup=null;
      if(document.releaseCapture){
        document.releaseCapture();
      }
    }
    return false;
  }
}
  ```

# 绘制曲线路径

## 角度与弧度的 Js 表达式:

**radians=(Math.PI/180)*degrees**

## arc(x, Y, Radius, startAngle, endAngle, anticlockwise)

画一个以（x,y）为圆心的以 radius 为半径的圆弧（圆），从 startAngle 开始到 endAngle 结束，

按照 anticlockwise 给定的方向（默认为顺时针）来生成。

- ture: 逆时针
- false: 顺时针

x,y 为绘制圆弧所在圆上的圆心坐标

radius 为半径

startAngle 以及 endAngle 参数用弧度定义了开始以及结束的 **弧度**。这些都是以 x 轴为基准

参数 anticlockwise 为一个布尔值。为 true 时，是逆时针方向，否则顺时针方向。

`ctx.arc(100,100,50,0,90*Math.PI/180,true);`

## arcTo(x1, Y1, X2, Y2, radius)

根据控制点和半径绘制圆弧路径，使用当前的描点 (前一个 moveTo 或 lineTo 等函数的 **止点**)。根据当前描点与给定的控制点 1 连接的直线，和控制点 1 与控制点 2 连接的直线，作为使用指定半径的圆的 **切线**，画出两条切线之间的弧线路径。

  ```js
var ctx = canvas.getContext("2d");
ctx.beginPath();
ctx.moveTo(50,50);
ctx.lineTo(300,0);
ctx.lineTo(200,200);
ctx.stroke();

ctx.beginPath();
ctx.moveTo(50,50)
ctx.arcTo(300,0,200,200,50);
ctx.stroke();
  ```

## 二次贝塞尔:quadraticCurveTo(cp1x, cp1y, X, y)

绘制二次贝塞尔曲线

+ cp1x,cp1y 为一个控制点
+ x,y 为结束点。

起始点为 moveTo 时指定的点

  ```js
var ctx = canvas.getContext("2d");
ctx.beginPath();
ctx.moveTo(50,50);
ctx.lineTo(300,0);
ctx.lineTo(200,200);
ctx.stroke();

ctx.beginPath();
ctx.moveTo(50,50)
ctx.quadraticCurveTo(300,0,200,200);
ctx.stroke();
  ```

## 三次贝塞尔:bezierCurveTo(cp1x, cp1y, cp2x, cp2y, X, y)

绘制三次贝塞尔曲线，cp1x,cp1y 为控制点一，cp2x,cp2y 为控制点二，x,y 为结束点。

起始点为 moveTo 时指定的点

  ```js
var ctx = canvas.getContext("2d");
ctx.beginPath();
ctx.moveTo(50,50);
ctx.lineTo(300,0);
ctx.lineTo(0,300);
ctx.lineTo(300,300);
ctx.stroke();


ctx.beginPath();
ctx.moveTo(50,50)
ctx.bezierCurveTo(300,0,0,300,300,300);
ctx.stroke();
  ```

![1547527356325](/img/user/programming/front-end/primitive/html/canvas/1547527356325.png)

# Canvas 变换

## 注意

需要变换的是 **画布里的图像**，CSS3 只能控制画布，所以 Canvas 需要有自己的变换

类似的，画布里的图像是没有办法添加事件的，事件只能给到画布

虽然是画笔上的 API，但是，实际操作的是 **画布，坐标轴**

## translate(x, y)

我们先介绍 translate 方法，它用来移动 canvas 的 **原点** 到一个不同的位置。

translate 方法接受两个参数。x 是左右偏移量，y 是上下偏移量，

在 canvas 中 translate 是累加的

  ```js
var ctx = canvas.getContext("2d");
/*
ctx.translate(50,50);
ctx.translate(50,50);
*/
//因为累加，相当于
ctx.translate(100,100)
ctx.beginPath();
ctx.fillRect(0,0,100,100);
  ```

![1547528365743](/img/user/programming/front-end/primitive/html/canvas/1547528365743.png)

## rotate(angle)

这个方法只接受一个参数: 旋转的 **弧度**(angle)，它是顺时针方向的，以弧度为单位的值。

旋转的中心点始终是 canvas 的原点，如果要改变它，我们需要用到 translate 方法

在 canvas 中 rotate 是累加的

  ```js
var ctx = canvas.getContext("2d");
ctx.translate(50,50);
ctx.rotate(45*Math.PI/180)
/*
ctx.rotate(45*Math.PI/180)
ctx.translate(50,50)
交换顺序，表现不一样
*/

ctx.beginPath();
ctx.fillRect(100,100,100,100);
  ```

![1547530367971](/img/user/programming/front-end/primitive/html/canvas/1547530367971.png)

## scale(x, y)

scale 方法接受两个参数。x,y 分别是 **横轴和纵轴** 的缩放因子，它们都必须是正值。

值比 1.0 小表示缩小，比 1.0 大则表示放大，值为 1.0 时什么效果都没有。

缩放一般我们用它来增减图形在 canvas 中的 **像素数目**，对形状，位图进行缩小或者放大

- 这里的像素数目，其实是一个抽象概念，屏幕物理上的像素没有改变

在 canvas 中 scale 是累乘的

累加、累乘，都是同步性的表现

## Demo 变换实例

  ```js
var ctx = canvas.getContext("2d");

/* ctx.save();
				ctx.translate(150,150);

				ctx.beginPath();

				ctx.fillRect(-50,-50,100,100);


				ctx.restore();
 				*/
setInterval(function(){
  flag++;
  ctx.clearRect(0,0,canvas.width,canvas.height);
  ctx.save();
  ctx.translate(150,150);
  ctx.rotate(flag*Math.PI/180);


  if(scale==100){
    flagScale=-1;
  }else if(scale==0){
    flagScale=1;
  }
  scale+=flagScale;
  ctx.scale(scale/50,scale/50);

  ctx.beginPath();
  ctx.fillRect(-50,-50,100,100);
  ctx.restore();
},10)
  ```

## Demo 表盘

  1. 初始化

     将圆心调整到画布的中间

     由于 canvas 中画圆与旋转所参照的坐标系于正常坐标系有出入，将整个画布逆时针旋转 90 度

     初始化一些样式数据

  ```js
ctx.lineWidth = 8;
ctx.strokeStyle = "black";
ctx.lineCap = "round";
  ```

    2. 外层空心圆盘
    3. 时针刻度
    4. 分针刻度
    5. 时针
    6. 分针
    7. 秒针
    8. 中心实心圆盘
    9. 秒针头
    10. Date
    11. 定时器，清除画布，clock()

**save restore 与 同步累加性**

  ```js
/* 时针刻度*/
/* 
//画法一:
//save在内部，每次画刻度都是分开的，所以需要*I
for (let i = 0; i < 12; i++) {
	ctx.save()
	ctx.lineWidth=8
	// 每次坐标轴都旋转360/12=30度
	ctx.rotate(30*i*Math.PI/180)
	ctx.beginPath()
	ctx.moveTo(0, -75)
	ctx.lineTo(0,-55)
	ctx.stroke()
	ctx.restore()
} 
*/

//画法二:
//save在外，相当于内部执行了12次累加的旋转，不用*i，也可以画出刻度
ctx.save()
ctx.lineWidth=8
for (let i = 0; i < 12; i++) {
  // 每次坐标轴都旋转360/12=30度
  ctx.rotate(30*Math.PI/180)
  ctx.beginPath()
  ctx.moveTo(0, -75)
  ctx.lineTo(0,-55)
  ctx.stroke()
}
ctx.restore()
  ```

**canvas 动画，只能重绘整个画布**

  ```js
setInterval(() => {
  ctx.clearRect(-150, -150, 300, 300)
  clock ()
  // 画之前清除上一次的，不然等于没画
  // ctx.clearRect(-150, -150, 300, 300)
},1000)
  ```

# Canvas 图片操作

## drawImage(image, X, Y, Width, height)

在 canvas 中插入图片 (需要 image 对象)

    1. 其中 image 是 image 或者 canvas 对象
       - canvas 操作图片时，必须要等图片加载完才能操作
       - img.onload= () =>{}
    2. x 和 y 是其在目标 canvas 里的起始坐标。
    3. 这个方法多了 2 个参数:width 和 height，这两个参数用来控制 当向 canvas 画入时应该缩放的大小

  ```js
var ctx = canvas.getContext("2d");

var img = new Image();
img.src="tg.png";
img.onload=function(){
  draw();
}

function draw(){
  ctx.drawImage(img,0,0,img.width,img.height)
}
  ```

## createPattern(image, repetition)

在 canvas 中设置背景 (需要 image 对象)

    1. image: 图像源
    2. epetition:

       - "repeat"
       - "repeat-x"
       - "repeat-y"
       - "no-repeat"

一般情况下，我们都会将 createPattern 返回的对象作为 **fillstyle** 的值

  ```js
var ctx = canvas.getContext("2d");

var img = new Image();
img.src="tg.png";
img.onload=function(){
  draw();
}

function draw(){
  var pattern = ctx.createPattern(img,"no-repeat")
  ctx.fillStyle=pattern;
  ctx.fillRect(0,0,300,300);
}
  ```

## createLinearGradient(x1, Y1, X2, y2)

canvas 渐变（线性渐变）

表示渐变的起点 (x1,y1) 与终点 (x2,y2)

`gradient.addColorStop(position, color)`

gradient :createLinearGradient 的返回值

addColorStop 方法接受 2 个参数，

    1. position 参数必须是一个 0.0 与 1.0 之间的数值，表示渐变中颜色所在的相对位置。
       - 例如，0.5 表示颜色会出现在正中间。
    2. color 参数必须是一个有效的 CSS 颜色值（如 `#FFF`，` rgba(0,0,0,1)`，等等）

  ```js
var gradient =  ctx.createLinearGradient(0, 0, 200, 200);
gradient.addColorStop(0,"red");
gradient.addColorStop(0.5,"yellow");
gradient.addColorStop(0.7,"pink");
gradient.addColorStop(1,"green");
ctx.fillStyle=gradient;
ctx.fillRect(0,0,300,300);
  ```

## createRadialGradient(x1, Y1, R1, X2, Y2, r2)

canvas 渐变（径向渐变）

    1. 前三个参数则定义另一个以 (x1,y1) 为原点，半径为 r1 的圆，
    2. 后三个参数则定义另一个以 (x2,y2) 为原点，半径为 r2 的圆。

  ```js
var gradient = ctx.createRadialGradient(150, 150, 50, 150, 150, 100)
gradient.addColorStop(0,"red");
gradient.addColorStop(0.5,"yellow");
gradient.addColorStop(0.7,"pink");
gradient.addColorStop(1,"green");
ctx.fillStyle=gradient;
ctx.fillRect(0,0,300,300);
}
  ```

## Demo 飞鸟

# Canvas 文本操作

## fillText(text, X, y)

在指定的 (x,y) 位置填充指定的文本

## strokeText(text, X, y)

在指定的 (x,y) 位置绘制 **文本边框**

一步空心文字

## Font = Value

当前我们用来绘制 **文本的样式**. 这个字符串使用和 CSS font 属性相同的语法.

默认的字体是 10px sans-serif。

font 属性在指定时，**必须** 要有大小和字体 缺一不可

- 字体可以随意指定，**无效字体会自动使用 sans-serif**
- canvas 只支持趁线字体？

## textAlign = Value

**文本对齐** 选项. 可选的值包括: left, right center.

- left 文本左对齐。
- right 文本右对齐
- center 文本居中对齐

文本的对齐是基于你在 fillText 的时候所给的 x 的值，

textAlign="center" 时文本一半在 x 的左边，一半在 x 的右边

left 时，x 在全部文字的最左边

right，x 在全部文字的最右边

## textBaseline = Value

描述绘制文本时，当前 **文本基线** 的属性。

- top 文本基线在文本块的顶部。
- middle 文本基线在文本块的中间
- bottom 文本基线在文本块的底部

## **measureText**

measureText() 方法返回一个 TextMetrics 对象，包含关于文本尺寸的信息（例如文本的宽度）

  ```js
var ctx = canvas.getContext("2d");
ctx.fillStyle="green";
ctx.font="60px sans-serif";
ctx.fillText("邱海峰",50,50);
debugger
var obj = ctx.measureText("邱海峰");
  ```

## Canvas 文本水平垂直居中

  ```js
var oC =document.getElementById('c1');
var oGC = oC.getContext('2d');
oGC.font = '60px impact';
oGC.textBaseline = 'middle';  //middle bottom
var w = oGC.measureText('尚硅谷').width;
oGC.fillText('尚硅谷',(oC.width - w)/2 , (oC.height - 60)/2);
  ```

## shadowOffsetX = Float

## shadowOffsetY = Float

shadowOffsetX 和 shadowOffsetY 用来设定**阴影 (文本阴影&盒模型阴影)**在 X 和 Y 轴的延伸距离，

它们默认都为 0。

## shadowBlur = Float

shadowBlur 用于设定阴影的模糊程度，**其数值并不跟像素数量挂钩，也不受变换矩阵的影响**，默认为 0。

## shadowColor = color(必需项)

shadowColor 是标准的 CSS 颜色值，用于设定阴影颜色效果，默认是全透明的黑色。

# Canvas 像素操作

到目前为止，我们尚未深入了解 Canvas 画布真实像素的原理，事实上，你可以直接通过 **ImageData 对象** 操纵像素数据，直接读取或将数据数组写入该对象中

## getImageData(sx, Sy, Sw, sh)

获得一个包含画布场景 **像素数据** 的 **ImageData 对像**,它代表了画布区域的对象数据

1. sx: 将要被提取的图像数据矩形区域的左上角 x 坐标。
2. sy: 将要被提取的图像数据矩形区域的左上角 y 坐标。
3. sw: 将要被提取的图像数据矩形区域的宽度。
4. sh: 将要被提取的图像数据矩形区域的高度。

## ImageData 对象

ImageData 对象中存储着 canvas 对象真实的像素数据，它包含以下几个只读属性:

1. width: 图片宽度，单位是像素
2. height: 图片高度，单位是像素
3. data:**Uint8ClampedArray 类型** 的 **一维数组**，包含着 RGBA 格式的整型数据，范围在 0 至 255 之间（包括 255）

   - R:0 --> 255(黑色到白色)
   - G:0 --> 255(黑色到白色)
   - B:0 --> 255(黑色到白色)
   - A:0 --> 255(透明到不透明)

## putImageData(myImageData, Dx, dy)

[CanvasRenderingContext2D.putImageData() - Web API 接口参考 | MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/putImageData)

putImageData() 方法去对场景进行 **像素数据的写入**。

dx 和 dy 参数表示你希望在场景内左上角绘制的像素数据所得到的设备坐标

  ```js
//ctx.fillStyle="rgba(255, 192, 203,1)";	
// 获得(0,0)处，宽高为100px的像素数据
var imageData = ctx.getImageData(0,0,100,100);
for(var i=0;i<imageData.data.length;i++){
  // 更改每一组的B值
  imageData.data[4*i+3]=100;
}
// 将修改后的像素数据注入到(0,0)点
ctx.putImageData(imageData,0,0)
  ```

## ctx.createImageData(width, height)

创建一个 ImageData 对象

1. width : ImageData 新对象的宽度。
2. height: ImageData 新对象的高度。

默认创建出来的是透明的

画布上像素点默认也是黑色透明

  ```js
var imageData = ctx.createImageData(100,100);
for(var i=0;i<imageData.data.length;i++){
imageData.data[4*i+3]=255;
}
ctx.putImageData(imageData,100,100)
  ```

## Demo 操作单个像素（行与列）

## Demo 马赛克

用 for 循环模拟网格

# 合成

## globalAlpha = Value

全局透明度的设置

这个属性影响到 canvas 里所有图形的透明度，

有效的值范围是 0.0 （完全透明）到 1.0（完全不透明）

默认是 1.0

## globalCompositeOperation 属性

定义合成: 多个图形重合的时候，如何展现

source: 新的图像 (源，属性定义之后的)

destination: 已经绘制过的图形 (目标，属性定义之前的**，一开始 (0,0,0,0)？**)

  **可选值:**

    1. source-over(默认值): 源在上面,新的图像层级比较高
    2. source-in : 只留下源与目标的重叠部分 (源的那一部分)
    3. source-out : 只留下源超过目标的部分
    4. source-atop: 砍掉源溢出的部分
    5. destination-over: 目标在上面,旧的图像层级比较高
    6. destination-in: 只留下源与目标的重叠部分 (目标的那一部分)
    7. destination-out: 只留下目标超过源的部分
    8. destination-atop: 砍掉目标溢出的部分

  ```js
ctx.fillStyle="pink";
ctx.fillRect(50,50,100,100);
ctx.globalAlpha=.5;
ctx.globalCompositeOperation="destination-atop";
ctx.fillStyle="green";
ctx.fillRect(100,100,100,100);
  ```

## Demo 刮刮卡

# Canvas Others

## toDataURL

将画布导出为图像

注意是 **canvas 元素** 接口上的方法

得到图片的 base64 编码 url

```js
var canvas = document.querySelector("#test");
if(canvas.getContext){
  var ctx = canvas.getContext("2d");
  ctx.fillRect(0,0,199,199);
  var result =  canvas.toDataURL();
  console.log(result);
}
```

## ctx.isPointInPath(x, y)

事件操作，判断在当前路径中是否包含检测点

    1. x: 检测点的 X 坐标
    2. y: 检测点的 Y 坐标

注意，此方法只作用于最新画出的 canvas 图像

  ```js
var ctx = canvas.getContext("2d");
ctx.beginPath();
ctx.arc(100,100,50,0,360*Math.PI/180);
ctx.fill();

ctx.beginPath(); //如果没有这个beginPath，则下面的fill()方法，会画两个圆，都是最新的，都可以触发
ctx.arc(200,200,50,0,360*Math.PI/180);
ctx.fill();

canvas.onclick=function(ev){
  ev = ev||event;
  var x = ev.clientX - canvas.offsetLeft;
  var y = ev.clientY - canvas.offsetTop;
  if(ctx.isPointInPath(x,y)){
    alert(123);
  }
}
  ```

有了 beginPath() 方法之后，想要让第一个圆，也可以满足 isPointInPath()，需要使用到原型的知识

# 绘制隐藏元素

[隐藏页面中的某个元素](../../../FAQ/ui-faq.md#隐藏页面中的某个元素)
