---
{"dg-publish":true,"permalink":"/programming/basic/cs-basic/design-pattern/design-pattern/"}
---


https://zhuanlan.zhihu.com/p/134050236

# MV*

https://www.runoob.com/design-pattern/mvc-pattern.html

http://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html

https://blog.csdn.net/victoryzn/article/details/78392128

## MVC

### 组成部分

MVC 模式的意思是，软件可以分成三个部分。

![](/img/user/programming/basic/cs-basic/design-pattern/!design-pattern/image-20230103163811413.png)

视图（View）：用户界面。

控制器（Controller）：业务逻辑

模型（Model）：数据保存

### 通信方式

![](/img/user/programming/basic/cs-basic/design-pattern/!design-pattern/image-20230103163837314.png)

View 传送指令到 Controller

Controller 完成业务逻辑后，要求 Model 改变状态

Model 将新的数据发送到 View，用户得到反馈

### 特点

所有通信都是 **单向** 的

### 互动模式

接受用户指令时，MVC 可以分成两种方式。一种是通过 View 接受指令，传递给 Controller。

![img](/img/user/programming/basic/cs-basic/design-pattern/!design-pattern/bg2015020106.png)

另一种是直接通过 controller 接受指令。

![img](/img/user/programming/basic/cs-basic/design-pattern/!design-pattern/bg2015020107.png)

### 优点

+ 耦合性低，视图层和业务层分离，这样就允许更改视图层代码而不用重新编译模型和控制器代码。
+ 重用性高
+ 生命周期成本低
+ MVC 使开发和维护用户接口的技术含量降低
+ 可维护性高，分离视图层和业务逻辑层也使得 WEB 应用更易于维护和修改
+ 部署快

### 缺点

+ 不适合小型，中等规模的应用程序，花费大量时间将 MVC 应用到规模并不是很大的应用程序通常会得不偿失。
+ 视图与控制器间过于紧密连接，视图与控制器是相互分离，但却是联系紧密的部件，视图没有控制器的存在，其应用是很有限的，反之亦然，这样就妨碍了他们的独立重用。
+ 视图对模型数据的低效率访问，依据模型操作接口的不同，视图可能需要多次调用才能获得足够的显示数据。对未变化数据的不必要的频繁访问，也将损害操作性能。

### 实例

在 web app 流行之初， MVC 就应用在了 java（struts2）和 C#（ASP.NET）服务端应用中，后来在客户端应用程序中，基于 MVC 模式，AngularJS 应运而生。

实际项目往往采用更灵活的方式，以 [Backbone.js](http://documentcloud.github.com/backbone) 为例。

![img](/img/user/programming/basic/cs-basic/design-pattern/!design-pattern/bg2015020108.png)

1. 用户可以向 View 发送指令（DOM 事件），再由 View 直接要求 Model 改变状态。
2. 用户也可以直接向 Controller 发送指令（改变 URL 触发 hashChange 事件），再由 Controller 发送给 View。
3. Controller 非常薄，只起到路由的作用，而 View 非常厚，业务逻辑都部署在 View。所以，Backbone 索性取消了 Controller，只保留一个 Router（路由器）

## MVP

### 组成部分

MVP 模式将 Controller 改名为 Presenter，同时改变了通信方向。

![img](/img/user/programming/basic/cs-basic/design-pattern/!design-pattern/bg2015020109.png)

### 通讯方式

各部分之间的通信，都是双向的。

View 与 Model 不发生联系，都通过 Presenter 传递。

View 非常薄，不部署任何业务逻辑，称为 " 被动视图 "（Passive View），即没有任何主动性，而 Presenter 非常厚，所有逻辑都部署在那里。

### 特点

M、V、P 之间双向通信。

View 与 Model 不通信，都通过 Presenter 传递。Presenter 完全把 Model 和 View 进行了分离，主要的程序逻辑在 Presenter 里实现。

Presenter 与具体的 View 是没有直接关联的，而是通过定义好的接口进行交互，从而使得在变更 View 时候可以保持 Presenter 的不变，这样就可以重用。不仅如此，还可以编写测试用的 View，模拟用户的各种操作，从而实现对 Presenter 的测试–从而不需要使用自动化的测试工具。

### 优点

+ 模型与视图完全分离，我们可以修改视图而不影响模型；
+ 可以更高效地使用模型，因为所有的交互都发生在一个地方——Presenter 内部；
+ 我们可以将一个 Presenter 用于多个视图，而不需要改变 Presenter 的逻辑。这个特性非常的有用，因为视图的变化总是比模型的变化频繁；
+ 如果我们把逻辑放在 Presenter 中，那么我们就可以脱离用户接口来测试这些逻辑（单元测试）。

### 缺点

+ 视图和 Presenter 的交互会过于频繁，使得他们的联系过于紧密。也就是说，一旦视图变更了，presenter 也要变更。

### MVP 应用

可应用与 Android 开发

## MVVM

参考： https://www.zhihu.com/question/310674885

MVVM 是 Model-View-ViewModel 的简写。微软的 **WPF**(Windows Presentation Foundation–微软推出的基于 Windows 的用户界面框架) 带来了新的技术体验, 使得软件 UI 层更加细节化、可定制化。与此同时，在技术层面，WPF 也带来了 诸如：

+ Binding（绑定）
+ Dependency Property（依赖属性）
+ Routed Events（路由事件）
+ Command（命令）
+ DataTemplate（数据模板）
+ ControlTemplate（控制模板）等新特性

MVVM 模式其实是 MV 模式与 WPF 结合的应用方式时发展演变过来的一种新型架构模式。它 **立足于原有 MVP 框架并且把 WPF 的新特性糅合进去，以应对客户日益复杂的需求变化。**

MVVM 模式将 Presenter 改名为 ViewModel，基本上与 MVP 模式完全一致

![img](/img/user/programming/basic/cs-basic/design-pattern/!design-pattern/bg2015020110.png)

唯一的区别是，它采用双向绑定（data-binding）：View 的变动，自动反映在 ViewModel，反之亦然。[Angular](https://angularjs.org/) 和 [Ember](http://emberjs.com/) 都采用这种模式。

### 组成部分

MVVM 典型特点是有四个概念：Model、View、ViewModel、绑定器。MVVM 可以是单向绑定也可以是双向绑定甚至是不绑定

+ Model 层：模型，定义数据结构。
+ Controller 层：实现业务逻辑，数据的增删改查。在 MVVM 模式中一般把 C 层算在 M 层中，（只有在理想的双向绑定模式下，Controller 才会完全的消失。这种理想状态一般不存在）
+ ViewModel 层：顾名思义是视图 View 的模型、映射和显示逻辑（如 if for 等，非业务逻辑），另外绑定器也在此层。ViewModel 是基于视图开发的一套模型，如果你的应用是给盲人用的，那么也可以开发一套基于 Audio 的模型 AudioModel。
+ View 层：将 ViewModel 通过特定的 GUI 展示出来，并在 GUI 控件上绑定视图交互事件，V(iew) 一般由 MVVM 框架自动生成在浏览器中。
+ **绑定器**：声明性的数据和命令，存在于 ViewModel 之中，让 ViewModel 和 Model 二者进行自动或手动通信，接下来的“MVVM 在 React 中对应关系”小节有举例说明。

MVVM 本质上是 M-V-C-VM，它是在 MVC 的基础上增加了一层 VM，只不过 C 变弱了，被并入到 M 概念中，VM 用于分离 V 和 M，并且让用户避免由于直接操作 V 层的 DOM 而带来的繁琐和效率低下，MVVM 使开发更高效，结构更清晰，增加代码的复用性。

在不同的 GUI（图形用户界面）上进行展示时，Model、Controller、View-Model 能够复用，只需把 View 层进行替换。

在不同类型的 UI（用户界面）上进行展示时，Model、Controller 能够复用，只需把 View-Model、View 层进行替换。比如：假设我们开发的是一款针对盲人的应用，那么输出设备或许我们需要考虑使用扬声器来代替显示器，输入设备使用麦克风，这时我们只需将上述的 View-Mode l 替换为 Audio-Model 作为语音模型，将 View 层替换为 Audio 层用于播放语音和接收语音输入。

个人认为：在基于 MVVM 框架的项目中，不管是双向数据绑定还是单向数据绑定，你在开发中实际要面对的都是 ViewModel 和 Model 层之前的通信，因为 View 和 ViewModel 层之间的映射和通信都是由框架自动完成的，

### React 仅仅是 View

React 不是 MVVM，就算加上 React-router 和 Redux，也勉强是一个 MVC

#### MVVM 在 React 中对应关系

+ Model：对应组件的方法或生命周期函数中实现的业务逻辑和 this.state 中保存的本地数据，如果 React 集成了 redux +react-redux，那么组件中的业务逻辑和本地数据可以完全被解耦出来单独存放当做 M 层，如业务逻辑放在 Reducer 和 Action 中。
+ View-Model：对应组件中的 JSX，它实质上是 Virtual DOM 的语法糖。React 负责维护 Virtual DOM 以及对其进行 diff 运算，而 React-dom 会把 Virtual DOM 渲染成浏览器中的真实 DOM
+ View：对应框架在浏览器中基于虚拟 DOM 生成的真实 DOM（并不需要我们自己书写）以及我们书写的 CSS
+ 绑定器：对应 JSX 中的命令以及绑定的数据，如 className={ this.props.xxx }、{this.props.xxx} 等等

### MVVM 的双绑和单绑区别

一般，只有 UI 表单控件才存在双向数据绑定，非 UI 表单控件只有单向数据绑定。

单向数据绑定是指：M 的变化可以自动更新到 ViewModel，但 ViewModel 的变化需要手动更新到 M（通过给表单控件设置事件监听）

双向数据绑定是指念：M 的变化可以自动更新到 ViewModel，ViewModel 的变化也可以自动更新到 M

双向绑定 = 单向绑定 + UI 事件监听。双向和单向只不过是框架封装程度上的差异，本质上两者是可以相互转换的。

优缺点：在表单交互较多的情况下，双向数据绑定的优点是数据更易于跟踪管理和维护，缺点是代码量较多比较啰嗦。

单向数据绑定的优缺点和单向绑定正好相反。

### 三大框架的异同

三大框架都是数据驱动型的框架

vue 及 angular 是双向数据绑定；react 是单向数据流，并没有实现绑定

Vuex、Redux 都是单项数据绑定的，即 M 的变化可以自动更新到 V，但 V 的变化必须手动触发事件更新到 M，这种单项数据绑定使数据更易于跟踪管理和维护。

+ 

# 单例模式

模块导出的都是同一个，无论是 commonjs 还是 es module

能实现多例吗？我想要每次 import require 导入的都是不同的对象，这样是不是有点偏离设计？

**参考：**<https://www.runoob.com/design-pattern/singleton-pattern.html>

## 概念

单例模式（Singleton Pattern）是 Java 中最简单的设计模式之一。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。

这种模式涉及到一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。

**注意：**

1. 单例类只能有一个实例。
2. 单例类必须自己创建自己的唯一实例。
3. 单例类必须给所有其他对象提供这一实例。
4. 自行实例化（主动实例化）
5. 可推迟初始化，即延迟执行（与静态类/对象的区别）

**主要解决**：一个全局使用的类频繁地创建与销毁，控制实例数目，节省系统资源的时候

**思路**：判断系统是否已经有这个单例，如果有则返回，如果没有则创建。

**应用实例：**

1. 一个班级只有一个班主任。
2. Windows 是多进程多线程的，在操作一个文件的时候，就不可避免地出现多个进程或线程同时操作一个文件的现象，所以所有文件的处理必须通过唯一的实例来进行。
3. 一些设备管理器常常设计为单例模式，比如一个电脑有两台打印机，在输出的时候就要处理不能两台打印机打印同一个文件。

**优点**

1. 在内存里只有一个实例，减少了内存的开销，尤其是频繁的创建和销毁实例（比如管理学院首页页面缓存）。
2. 避免对资源的多重占用（比如写文件操作）。

**缺点**：没有接口，不能继承，与单一职责原则冲突，一个类应该只关心内部逻辑，而不关心外面怎么样来实例化。

## 全局变量

JavaScript 是一门非正规面向对象的语言，并没有类的定义。而单例模式要求一个 “唯一” 和 “全局访问” 的对象，在 JavaScript 中类似全局对象，刚好满足单例模式的两个特点：“唯一” 和 “可全局访问”。虽然它不是正规的单例模式，但不可否认确实具备类单例模式的特点。

```js
// 全局对象
var globaObj = {};
```

**使用全局变量会有以下问题：**

1. 命名空间污染（变量名冲突）
2. 维护时不方便管控（容易不小心覆盖）

**全局变量问题折中的应对方案：**

1. 使用命名空间
2. 闭包封装私有变量（利用函数作用域）
3. ES6 的 const/symbol

虽然全局变量可以实现单例，但因其自身的问题，不建议在实际项目中将其作为单例模式的应用，特别是中大型项目的应用中，全局变量的维护该是考虑的成本。

## 模式实现

module 的特性：若同一个应用中的其他模块打算从 `example.js` 导入绑定，则那些模块都会使用这段代码中所用的同一个模块实例。

```js
// mod.js
function C() {
  this.sum = 0;
  this.add = function () {
    this.sum += 1;
  };
  this.show = function () {
    console.log(this.sum);
  };
}

export let c = new C();
```

上面的脚本 `mod.js`，输出的是一个 `C` 的实例。不同的脚本加载这个模块，得到的都是同一个实例。

```javascript
// x.js
import {c} from './mod';
c.add();

// y.js
import {c} from './mod';
c.show();

// main.js
import './x';
import './y';
```

现在执行 `main.js`，输出的是 `1`。

```bash
$ babel-node main.js
1
```

这就证明了 `x.js` 和 `y.js` 加载的都是 `C` 的同一个实例。

## 完备单例实现

https://juejin.im/post/6844903874210299912

# 主从模式 Master-slave

https://www.cnblogs.com/dmego/p/9068734.html

# 更多模式

参考最开始的网站, 有空总结一下

# Domain Driven Design

# 控制反转（IoC）和依赖注入（DI）

IoC 的全称为 **Inversion of Control**，意为 **控制反转**，它是 OOP 中的一种原则（虽然不在 n 大设计模式中，但实际上 IoC 也属于一种设计模式），它可以很好的解耦代码。

故事风格介绍：https://zhuanlan.zhihu.com/p/33492169

OOP 六大原则：https://zhuanlan.zhihu.com/p/64591313

架构整洁之道：

+ https://www.zhihu.com/question/301498382/answer/527555292
+ https://blog.csdn.net/xxscj/article/details/93194482

前端趋势：https://www.zhihu.com/question/468249924/answer/196607436

直接学习 nest 的依赖注入

https://juejin.cn/post/6930898274639593480#heading-0

https://juejin.cn/post/7013173275526168589

现在我的疑问是，那 IOC 容器不就得同时依赖 A 和 B 吗？以后 A 有变化，需要改 IOC 容器，B 有变化也需要改 IOC 容器

本质：强制让代码依赖接口和抽象，而不是直接依赖实现

如果没有 IoC 容器，我们会直接调用这个实例上的方法，在运行时有这个实例方法就不会报错

如果有 IoC 容器，实际上是强制我们依赖了这个实例的接口，我们没有办法直接依赖实例的方法了，就是一种解耦，解耦

https://www.zhihu.com/question/441680476

## 依赖注入与手动 New 的区别

### 依赖关系树更加清晰

如果没有依赖倒置，我需要在每个调用了实例的方法，确认他到底用了哪些方法，我得把这些方法都实现了，才能替换。如果想节约时间，新类就得继承旧类，重写旧类的方法。这里继承就会导致新的耦合。会导致依赖树变得更加复杂，我依赖的是实例的父类，实际调用的是实例的子类。

如果有依赖倒置，我只需要确认接口上有哪些方法，实现接口上的方法即可，新类就可以替换旧类了。依赖树更加清晰，无论是调用旧类还是新类，面向的都是接口。旧类和新类之间没有关系

可以用不同的实例满足接口。比如人依赖交通工具上班，无论是公交、地铁、单车都可以满足接口。

如果没有依赖倒置的话，人必须依赖交通工具这个父类，然后用多态去支持，这样就产生了依赖树

### 强制依赖接口

依赖接口之后，即时没有真正的实现，用到的地方也不会报错。但是实例内部会提示没有实现

### 不需要知道如何创建实例

调用实例时，不需要知道如何去创建这个实例。只要 IoC 容器知道就可以了。这样减少了工作量，也减少了耦合。因为 new 一个实例，可能需要引入这个 class 依赖的多个其他 class，这些显然不是调用方需要去关心的

### 竞对

当然，可以用 static method 或者 singleton 去实现，然后在某个时刻时统一初始化。只是每多一个服务对象，就要去改初始化代码，面对复杂的依赖时，还要知道初始化顺序，实际上就是手动做一个 bean factory，只是耦合的更严重了。

## IoC 容器

我们现在能够知道 IoC 容器大概的作用了：容器内部维护着一个对象池，管理着各个对象实例，当用户需要使用实例时，容器会自动将对象实例化交给用户。

## DI

DI 的全称为 **Dependency Injection**，即 **依赖注入**。依赖注入是控制反转最常见的一种应用方式，就如它的名字一样，它的思路就是在对象创建时自动注入依赖对象。

## DI 与装饰器 @link Es-next-2

## DI 与 Reflect.metadata @link Es-next-2

# 状态机

## 什么是状态机

当处理的情况特别多，我们把每种情况的处理逻辑封装成一个状态，然后不同情况之间的转换变成状态的转换。这种代码组织形式就是状态机。

当每个状态知道输入某一段内容时转到哪一个状态，在一个循环内自动进行状态的流转和不同状态的处理，这种叫做状态自动机（automation），如果一个状态在一种输入下只有一个后续状态，这种就叫做确定性有限状态自动机（DFA）。

状态之间的流转可以通过状态转换图来表示。

## 基础示例

### Typescript 源码中的状态机

typescript compiler 就是通过状态机来组织整个编译流程的：

首先 tsc 划分了很多状态，每种状态处理一种逻辑。比如：

+ CreateProgram 把源码 parse 成 ast
+ SyntaxDiagnostics 处理语法错误
+ SemanticDiagnostics 处理语义错误
+ Emit 生成目标代码

![](/img/user/programming/basic/cs-basic/design-pattern/!design-pattern/image-20220904141256473.png)

typescript 就通过这种状态的修改来完成不同处理逻辑的流转，如果处理到结束状态就代表流程结束。

![](/img/user/programming/basic/cs-basic/design-pattern/!design-pattern/image-20220904141315058.png)

这样使得整体流程可以很轻易的扩展和修改，比如想扩展一个阶段，只要增加一个状态，想修改某种状态的处理逻辑，只需要修改下状态机的该状态的转向。而不是大量的 if else 混杂在一起，难以扩展和修改。

可以看到，状态机使得 typescript 的编译步骤可以灵活的扩展和修改。

### 词法分析中的状态机

其实状态机最常用的地方是用于词法分析，因为每个 token 都是一种处理情况，自然会有很多 if else。

像下面这样用 if else 来做分词自然也可以，这是 wenyan 的词法分析逻辑，但是代码很难维护。

![](/img/user/programming/basic/cs-basic/design-pattern/!design-pattern/image-20220904141421994.png)

更好的做法是使用状态机（DFA）来做分词，把每一种 token 的处理封装成一个状态。通过边界条件的判断来做状态流转，比如某个 wxml parser 分了这些状态：

![](/img/user/programming/basic/cs-basic/design-pattern/!design-pattern/image-20220904141433459.png)

每种状态处理一种情况的 token 的识别：

![](/img/user/programming/basic/cs-basic/design-pattern/!design-pattern/image-20220904141447964.png)

![](/img/user/programming/basic/cs-basic/design-pattern/!design-pattern/image-20220904141453141.png)

通过状态的变化驱动处理逻辑的流转：

![](/img/user/programming/basic/cs-basic/design-pattern/!design-pattern/image-20220904141509312.png)

这样不断的进行各状态之间的流转，当处理到字符串的末尾的时候，就完成了所有的分词。

### 业务代码中的状态机

业务代码中当遇到各种 if else 的判断的时候同样可以用状态机来优化。把每种情况封装成一个状态，通过某一种条件触发状态的流转，然后在状态机里面选择不同的状态处理逻辑进行处理。

![](/img/user/programming/basic/cs-basic/design-pattern/!design-pattern/image-20220904141535981.png)

不管是游戏中不同状态做不同的处理逻辑，还是在 ui 项目中不同状态做不同的渲染，当代码逻辑复杂时，难免会有很多 if else，这时候都可以用状态机的思路来做优化。

这样，当后续扩展处理逻辑、修改不同条件下的处理逻辑都变得简单和清晰很多。
