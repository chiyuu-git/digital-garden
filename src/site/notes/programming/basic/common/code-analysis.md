---
{"dg-publish":true,"permalink":"/programming/basic/common/code-analysis/"}
---


# Understand

没找到 understand 的 ignore 规则

官网指南：http://documentation.scitools.com/pdf/understand.pdf

官网指南分类别：https://support.scitools.com/

介绍 1:http://codemx.cn/2016/04/30/Understand01/

介绍 2:https://www.cnblogs.com/firstdream/p/4640658.html

## 层级关系视图分类：

https://blog.csdn.net/LOVE1055259415/article/details/80412934

1.Butterfly：如果两个实体间存在关系，就显示这两个实体间的调用和被调用关系；如下图为 Activity 中的一个方法的关系图：

2.Calls：展示从你选择的这个方法开始的整个调用链条；

3.Called By：展示了这个实体被哪些代码调用，这个结构图是从底部向上看或者从右到左看；

4.Calls Relationship/Calledby Relationship: 展示了两个实体之间的调用和被调用关系，操作方法：首先右键你要选择的第一个实体，然后点击另一个你要选择的实体，如果选择错误，可以再次点击其他正确即可，然后点击 ok；

5.Contains: 展示一个实体中的层级图，也可以是一个文件，一条连接线读作”x includes y“；

6.Extended By: 展示这个类被哪些类所继承，

7.Extends: 展示这个类继承自那个类：

## 结构关系视图分类：

1.Graph Architecture：展示一个框架节点的结构关系；

2.Declaration: 展示一个实体的结构关系，例如：展示参数，则返回类型和被调用函数，对于类，则展示私有成员变量（谁继承这个类，谁基于这个类）

3.Parent Declaration: 展示这个实体在哪里被声明了的结构关系；

4.Declaration File: 展示所选的文件中所有被定义的实体（例如函数，类型，变量，常量等）；

5.Declaration Type: 展示组成类型；

6.Class Declaration: 展示定义类和父类的成员变量；

7.Data Members: 展示类或者方法的组成，或者包含的类型；

8.Control Flow: 展示一个实体的控制流程图或者类似实体类型；

9.Cluster Control Flow: 展示一个实体的流程图或者类似实体类型，这个比上一个更具有交互性；

10.UML Class Diagram: 展示这个项目中或者一个文件中定义的类以及与这个类关联的类

11.UML Sequence Diagram: 展示两个实体之间的时序关系图；

12.Package: 展示给定包名中声明的所有实体

13.Task: 展示一个任务中的参数，调用，实体

14.Rename Declaration: 展示实体中被重命名的所有实体

## 可展示图表

流程图

用例图

ER 图

时序图

UML

# UML

比较全面的一个介绍：https://www.cnblogs.com/wolf-sun/p/UML-Sequence-diagram.html

简单来说就是用来描述 class 之间的关系的

![image-20220419151650712](/img/user/programming/basic/common/code-analysis/image-20220419151650712.png)

# 时序图

## 概述

### 定义

描述了对象之间以及对象与参与者实例之间**传递消息的时间顺序**, 用来表示用例中的**行为顺序**, 是**强调消息时间顺序**的交互图;

### 时序图描述的事物

时序图描述系统中类和类之间的交互, 将这些交互建模成**消息交换**，时序图描述了**类以及类之间的交换以完成的期望行为**的消息，时序图中每条消息都代表了类的一个操作或者引起状态机改变的触发事件;

## 使用场景

时序图的使用场景非常广泛，几乎各行各业都可以使用。当然，作为一个软件工作者，我这边主要列举和软件开发有关的场景。

1. 用对象间的交互来描述用例
2. 寻找类的操作

### 梳理业务流程

一般的软件开发都是为了支撑某个具体的业务。有时候业务的流程会比较复杂，涉及到多种角色，这时就可以使用时序图来梳理这个业务逻辑。这样会使业务看起来非常清晰，代码写起来也是水到渠成的事情了。

### 梳理开源软件

一般成熟框架的源代码调用深度都比较深，类之间的调用关系也比较复杂。

用时序图来梳理框架中这些对象之间的关系。比如再看 Tomcat 启动流程的过程中，我就时序图梳理了各个组件之间的关系，看起来层次非常清楚，也便于记忆。

## 元素

我们在画时序图时会涉及下面 7 种元素：

- 角色 (Actor)
- 对象 (Object)
- 生命线 (LifeLine)
- 控制焦点 (Activation)
- 消息 (Message)
- 自关联消息
- 组合片段

### **角色 (Actor)**

系统角色，可以是人或者其他系统和子系统。以一个小人图标表示。

### **对象 (Object)**

对象位于时序图的顶部,以一个矩形表示。对象的命名方式一般有三种：

- 对象名和类名。例如：华为手机: 手机、loginServiceObject:LoginService；
- 只显示类名，不显示对象，即为一个匿名类。例如：: 手机、:LoginSservice。
- 只显示对象名，不显示类名。例如：华为手机:、loginServiceObject:。

#### **对象创建的两种时机**

对象可以在交互开始的时候创建, 也可以在交互过程中进行创建;

+ 处于顶部 : 如果对象的位置在时序图顶部, 说明在交互开始的时候对象就已经存在了;
+ 不在顶部 : 如果对象的位置不在顶部, 那么对象在交互过程中创建的;

![image](/img/user/programming/basic/common/code-analysis/931580-20160819171541250-311726404.png)

#### **对象有三种状态**

激活、运行（存在）和销毁。

![image](/img/user/programming/basic/common/code-analysis/931580-20160819171543125-73014731.png)

#### 对象的左右排列位置

对象的左右顺序并不重要，但是为了画图画的清晰整洁起见，通常应遵循一下两个原则：

（1）把交互频繁的对象尽可能地靠拢。

（2）把初始化整个交互活动的对象（有时是一个参与者）放置在最左边。

![image](/img/user/programming/basic/common/code-analysis/931580-20160819171544734-257750018.png)

### **生命线 (LifeLine)**

时序图中每个对象和底部中心都有一条垂直的虚线，这就是对象的生命线 (对象的时间线)。以一条垂直的虚线表。

生命线是一个时间线, 从时序图顶部一直到底部都存在, 其长度取决于交互的时间。

![image](/img/user/programming/basic/common/code-analysis/931580-20160822102452230-674957490.png)

### 控制焦点 (Activation)

控制焦点代表时序图中在对象时间线上某段时期执行的操作。以一个很窄的矩形表示。

代表生命线上的窄矩形条被称为激活生命线（也称为控制焦点或方法调用框，表明正在由目标对象/类执行处理以完成消息）。

当对象处于激活时期, 生命线可以拓宽为矩形, 这个矩形条成为激活条;

当一个对象没有被激活时，该对象处于休眠（空闲）状态，什么事都不做，但它仍然存在，等待新的消息来激活它。

矩形框的高度表示对象执行一个操作所经历的时间段，矩形的顶部表示动作的开始，底部表示动作的结束。

![image](/img/user/programming/basic/common/code-analysis/931580-20160822102454105-319986154.png)

### 消息 (Message)

表示对象之间发送的信息。消息分为三种类型。

- 同步消息 (Synchronous Message)：
    - 消息的发送者把控制传递给消息的接收者，然后停止活动，等待消息的接收者放弃或者返回控制。用来表示同步的意义。
    - 以**一条实线和实心箭头表示**
- 异步消息 (Asynchronous Message)：
    - 消息发送者通过消息把信号传递给消息的接收者，然后继续自己的活动，不等待接受者返回消息或者控制。异步消息的接收者和发送者是并发工作的。
    - 以**一条实线和大于号表示**
- 返回消息 (Return Message) ：
    - 返回消息表示从过程调用返回
    - 以**小于号和虚线表示**

![image](/img/user/programming/basic/common/code-analysis/931580-20160822101409073-1419061822.png)

#### 消息的功能

消息是定义交互和协作中交换信息的类，用于对实体间的通信内容建模，消息允许在实体间传递信息 (传递参数), 允许实体请求其它服务, 对象之间通过发送和接收消息进行通信。消息可以触发操作, 唤起信号, 或使目标对象创建或销毁。

消息的功能：

1. 自调用
2. 创建对象
3. 同步调用其他对象
4. 异步调用其他对象
5. 返回
6. 销毁对象

![image](/img/user/programming/basic/common/code-analysis/931580-20160822101410886-511692129.png)

### 自关联消息

表示方法的自身调用或者一个对象内的一个方法调用另外一个方法。以一个半闭合的长方形 + 下方实心剪头表示。

下面举例一个时序图的列子，看下上面几种元素具体的使用方式。

![image](/img/user/programming/basic/common/code-analysis/931580-20160822101412714-545952800.png)

### 无对象消息

**无触发对象消息（Found Message）：**用活动条开始断点上的实心球箭头来表示。（用在开始）

**无接收对象消息（lost message）：**用箭头加实心球表示。（用在结束）

![image](/img/user/programming/basic/common/code-analysis/931580-20160822101414433-724058889.png)

## 组合片段

复杂元素

组合片段用来解决交互执行的条件和方式，它允许在序列图中直接表示逻辑组件，用于通过指定条件或子进程的应用区域，为任何生命线的任何部分定义特殊条件和子进程。组合片段共有 13 种，名称及含义如下：

+ ref：引用其他地方定义的组合片段；
+ alt：在一组行为中根据特定的条件选择某个交互；
+ opt：表示一个可选的行为；
+ break：提供了和编程语言中的 break 类拟的机制；
+ par：支持交互片段的并发执行；
+ seq：强迫交互按照特定的顺序执行；
+ strict：明确定义了一组交互片段的执行顺序；
+ neg：用来标志不应该发生的交互；
+ region：标志在组合片段中先于其他交互片断发生的交互；
+ ignore：明确定义了交互片段不应该响应的消息；
+ consider：明确标志了应该被处理的消息
+ assert：标志了在交互片段中作为事件唯一的合法继续者的操作数；
+ loop：说明交互片段会被重复执行

### alt（Alternative）

表示条件选择的意思，类似 if else。alt 需填写参数。

![image](/img/user/programming/basic/common/code-analysis/931580-20160822103627839-942696278.png)

### ref（InteractionUse）

表示引用的意思，某部分交互被定义在另一个图中。可将一个规模较大的图划分为若干个规模较小的图，方便图的管理和复用。ref 不用要填写参数。

![image](/img/user/programming/basic/common/code-analysis/931580-20160822103629151-887250584.png)

引用的身份认证时序图：

![image](/img/user/programming/basic/common/code-analysis/931580-20160822103630948-1280699634.png)

### opt（Option）

表示当警戒值为真（符合条件）的情况下进行执行处理的意思。opt 需要填写参数

![image](/img/user/programming/basic/common/code-analysis/931580-20160822103632464-1835056227.png)

### Loop

表示循环执行的意思，当条件为真的时候执行循环。也可以写成 loop(n) 来表示循环 n 次，与 java 或者 C#等中的 for 循环比较相似。loop 需填写参数。

![image](/img/user/programming/basic/common/code-analysis/931580-20160822103635339-1193582168.png)

### break（Break）

表示中断处理，跳转的意思，类似 java 代码中 break 语句。break 需填写参数。

![image](/img/user/programming/basic/common/code-analysis/931580-20160822103637105-1654199822.png)

### par（Parallel）

表示并行处理，同时发生的意思。par 可不用填写参数。

![image](/img/user/programming/basic/common/code-analysis/931580-20160822103638558-1936838945.png)

参考：

[使用场景 & 元素](https://zhuanlan.zhihu.com/p/249005031)

[组合片段—其6](https://www.cnblogs.com/whylaughing/p/5794693.html)

[实例分析](https://www.cnblogs.com/whylaughing/p/5794831.html)

# 代码可视化工具

## [semantic](https://github.com/github/semantic) 源码 -> Ast

## Visual Studio 代码分析功能

https://docs.microsoft.com/zh-cn/visualstudio/debugger/map-methods-on-the-call-stack-while-debugging-in-visual-studio?view=vs-2022

# JS 代码可视化工具

## [js-code-to-svg-flowchart](https://github.com/Bogdan-Lyashenko/js-code-to-svg-flowchart)

静态工具，代码不需要可运行，可以使用伪代码

可以视为根据伪代码画快速生成流程图的一个工具

## JS Event Loop 可视化 @link

https://www.jsv9000.app/

http://latentflip.com/loupe/?code=JC5vbignYnV0dG9uJywgJ2NsaWNrJywgZnVuY3Rpb24gb25DbGljaygpIHsKICAgIHNldFRpbWVvdXQoZnVuY3Rpb24gdGltZXIoKSB7CiAgICAgICAgY29uc29sZS5sb2coJ1lvdSBjbGlja2VkIHRoZSBidXR0b24hJyk7ICAgIAogICAgfSwgMjAwMCk7Cn0pOwoKY29uc29sZS5sb2coIkhpISIpOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0KCkgewogICAgY29uc29sZS5sb2coIkNsaWNrIHRoZSBidXR0b24hIik7Cn0sIDUwMDApOwoKY29uc29sZS5sb2coIldlbGNvbWUgdG8gbG91cGUuIik7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D
