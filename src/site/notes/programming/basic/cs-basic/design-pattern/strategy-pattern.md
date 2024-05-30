---
{"aliases":["策略模式"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-09-26-Tue, 1:18:57 pm","date-modified":"2023-10-18-Wed, 7:49:10 pm","permalink":"/programming/basic/cs-basic/design-pattern/strategy-pattern/","dgPassFrontmatter":true}
---


[ES6+TS如何书写更优雅的设计模式 - 策略模式 - 掘金](https://juejin.cn/post/6844904198551633934)
# 引入

![](/img/user/programming/basic/cs-basic/design-pattern/strategy-pattern/image-20230926132052578.png)

## 让鸭子飞起来

好吧，看起来不难，我们只需要给 Duck 类加一个 fly() 方法并实现就可以了；确实这样一来所有继承 Duck 类的鸭子都会飞了，但是我们忽略了一个事实——不同子类之间的差异性，橡皮鸭子原本不会飞现在飞起来了！

对代码的局部修改 影响的不一定只停留在局部

当涉及“维护”时，为了“复用”目的而使用继承，结局并不完美

对超类的修改要谨慎，避免造成不必要的麻烦

![](/img/user/programming/basic/cs-basic/design-pattern/strategy-pattern/image-20230926132423653.png)

### 方案一: 继承

![](/img/user/programming/basic/cs-basic/design-pattern/strategy-pattern/image-20230926132624083.png)

### 利用继承来提供 Duck 的 行为导致的问题

1. 代码在多个子类中重复
2. 运行时的行为不容易改变
3. 改动会牵一发而动全身, 造成其他鸭子不想要改变的行为
4. 很难知道所有鸭子的全部行为

### 方法二: 接口

![](/img/user/programming/basic/cs-basic/design-pattern/strategy-pattern/image-20230926133045240.png)

但这一来重复的代码将变得更多，对于每一种会飞、会叫的鸭子都需要去实现对应的接口，此外还有不同飞行方法、不同叫声的叫法，这使得代码的可复用性降得很低。

## 设计原则一

找出应用中可能需要变化之处,把它们独立出来,不要和那些不需要变化的代码混在一起。

下面是这个原则的另一种思考方式:“把会变化的部分取出并封装起来,以便以后可以轻易地改动或扩充此部分,而不影响不需要变化的其他部分”。-->系统变得更有弹性

**这样的概念很简单,几乎是每个设计模式背后的精神所在**。所有的模式都提供了一套方法让“系统中的某部分改变不会影响其他部分”

## 分开变化和不会变化的部分

我们知道 Duck 类内的 fly() 和 quack() 会随着鸭子的不同而改变。

为了要把这两个行为从 Duck 类中分开，我们将把它们从 Duck 类中取出来，建立一组新类来代表每个行为。

![](/img/user/programming/basic/cs-basic/design-pattern/strategy-pattern/image-20230926133950238.png)

## 设计原则二

针对接口编程, 而不是针对实现编程

从现在开始，鸭孑的行为将被放在分开的类中， 此类专门提供某行为接口的实现

这样，鸭孑类就不再需 要知道行为的实现细节

我们利用接口代表每个行为，比方说，FlyBehavior 与 QuackBehavior,而行为的每个实现都将实现其中的所有的接口

所以这次鸭子类不会负责实现 Flying 与 Quacking 接口，反而是由我们制造一组其他类专门实现 FlyBehavior 与 QuackBehavior,这就称为“行为”类。由行为类而不是 Duck 类来实现行为接口。

这样的做法迥异于以往，以前的做法是：行为来自 Duck 超类的具体实现，或是继承某个接口并由子类自行实现而来。这两种 做法都是依赖干“实现”，我们被实现绑得死死的，没办法更改行为（除非写更多代码）。

在我们的新设计中，鸭子的子类将使用接口（FlyBehavior 与 QuackBehavior）所表示的行为，所以实际的“实现”不会被绑 死在鸭子的子类中。（换句话说，特定的具体行为编写在实现了 FlyBehavior 与 QuakcBehavior 的类中）

![](/img/user/programming/basic/cs-basic/design-pattern/strategy-pattern/image-20230926134800460.png)

### 针对超类型编程

这里所谓的**接口**有多个含义，接口是一个**概念**，也是一种 Java 的 interface 构造。你可以在不涉及 Java interface 的情况下**针对接口编程**.

关键就在多态. 利用多态，程序可以针对超类型编程，执行时会根据实际状况执行到真正的行为，不会被绑死在超类型的行为.

如此, 只要是具体实现此超类型的类所产生的对象, 都可以指定给这个变量. 这也意味着, 声明类时不用理会以后执行时的真正对象类型

![](/img/user/programming/basic/cs-basic/design-pattern/strategy-pattern/image-20230926135538173.png)

## 实现鸭子的行为

![](/img/user/programming/basic/cs-basic/design-pattern/strategy-pattern/image-20230926135807208.png)

这样的设计，可以让飞行和呱呱叫的动作被其他 的对象复用，因为这些行为己经与鸭子类无关了。

而我们可以新增一些行为，不会影响到既有的行 为类，也不会影响“使用”到飞行行为的鸭子类。

## 整合鸭子的行为

![](/img/user/programming/basic/cs-basic/design-pattern/strategy-pattern/image-20230926145452480.png)

![](/img/user/programming/basic/cs-basic/design-pattern/strategy-pattern/image-20230926145655347.png)

## 设计原则三

多用组合, 少用继承

每一个鸭子都有一个 FlyBehavior 和 一个 QuackBehavior, 好将匕行和呱呱叫委托给它们代为处理

当你将两个类结合起来使用，如同本例一般，这就是组合 (composition)。这种做法和“继承”不同的地方在于, 鸭子的行为不是继承来的，而是和适巧的行为对象**组合**来的。****

# 正式定义

策略模式定义了算法族，分别封装起来，让它们之间 可以互相替换，此模式让算法的变化独立于使用算法的客户

# FAQ
