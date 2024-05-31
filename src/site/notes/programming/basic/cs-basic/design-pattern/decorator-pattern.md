---
{"aliases":["装饰者模式"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-09-26-Tue, 8:54:18 pm","date-modified":"2024-05-31-Fri, 11:48:08 am","permalink":"/programming/basic/cs-basic/design-pattern/decorator-pattern/","dgPassFrontmatter":true}
---


# 引入

![](/img/user/programming/basic/cs-basic/design-pattern/decorator-pattern/image-20230926215144082.png)

![](/img/user/programming/basic/cs-basic/design-pattern/decorator-pattern/image-20230926215234091.png)

![](/img/user/programming/basic/cs-basic/design-pattern/decorator-pattern/image-20230926215338504.png)

![](/img/user/programming/basic/cs-basic/design-pattern/decorator-pattern/image-20230926215537279.png)

问题:

1. 调料价钱的改变会使我们更改现有代码.
2. 一旦出现新的调料, 我们就需要加上新的方法, 并改变超类中的 cost() 方法
3. 以后可能会开发出新的饮料, 对这些饮料而言, 某些调料可能并不合适, 但是在这个设计方案中, Tea 子类仍将继承那些不合适的方法, 例如, hasWhip() 是否加奶泡.
4. 万一顾客想要双倍摩卡咖啡, 怎么办?

## 认识装饰者模式

好了,我们已经了解利用继承无法完全解决问题,在星巴兹遇到的问题有：类数量爆炸、设计死板,以及基类加入的新功能并不适用于所有的子类。

所以，在这里要采用不一样的做法：我们要以饮料为主体，然后在运 行时以调料来**装饰** (decorate) 饮料。比方说,如果顾客想要摩卡和奶泡深焙咖啡，那么，要做的是:

1. 以奶泡 (Whip) 对象装饰它
2. 拿一个深焙咖啡 (DarkRoast) 对象
3. 以摩卡 (Mocha) 对象装饰它
4. 调用 cost 方法，并依赖委托 (delegate) 将调料的价 钱加上去

![](/img/user/programming/basic/cs-basic/design-pattern/decorator-pattern/image-20230926222243527.png)

![](/img/user/programming/basic/cs-basic/design-pattern/decorator-pattern/image-20230927124449498.png)

## 关键点

1. 装饰着和被装饰对象有相同的超类型
2. 可以用一个或者多个装饰着包装一个对象
3. 既然装饰者和被装饰者有相同的超类型, 所以在任何需要原始对象的场合, 都可以用装饰过的对象代替它
4. 装饰者可以在所委托被装饰者的行为之前与/或之后，加上自己的行为，以达到特定的目的
5. 对象可以在任何时候被装饰，所以可以在运行时动态地、不限量地用你喜欢的装饰者来装饰对象

# 定义

装饰者模式动态地将责任附加到对象上。 若要扩展功能，装饰者提供了比继承更有弹性 的替代方案

![](/img/user/programming/basic/cs-basic/design-pattern/decorator-pattern/image-20230927125323366.png)

![](/img/user/programming/basic/cs-basic/design-pattern/decorator-pattern/image-20230927125417455.png)

# 疑问

![](/img/user/programming/basic/cs-basic/design-pattern/decorator-pattern/image-20230927142439784.png)

虽然三个疑问指出了装饰器模式的一点问题, 但是感觉太模糊了

你会发现“输出”流的设计方式也是一样的。你可能还会发现 Reader/ Writer 流（作为基于字符数据的输入输出）和输入流/输出流的类相当类 似（虽然有一些小差异和不一致之处，但是相当雷同，所以你应该可以 了解这些类）。

但是 Java I/O 也引出装饰者模式的一个“缺点”：利用装饰者模式，常 常造成设计中有大盘的小类，数量实在太多，可能会造成使用此 API 程 序员的困扰。但是，现在你已经了解了装饰者的工作原理，以后当使用 别人的大量装饰的 API 时，就可以很容易地辨别出他们的装饰者类是如 何组织的，以方便用包装方式取得想要的行为

## 如何改变装饰者类应对这样的需求？

我们在星巴兹的朋友决定开始在菜单上加上咖啡的容量大小，供顾客 可以选择小杯 (tall),中杯 (grande)、大杯 (venti)。星巴兹认为 这是任何咖啡都必须具备的，所以在 Beverage 类中加上了 getSize。与 setSize()o 他们也希望调料根据咖啡容量收费，例如：小中大杯的咖啡 加上豆浆，分别加收 0.10、0.15. 0.20 美金。

## 装饰器模式带来的问题

有能力为设计注入弹性，这是毋庸置疑的，但我也有“黑暗 面”。有时候我会在设计中加入大量的小类，这偶尔会导致别人不容易了解我的设计方式。

以 Java I/O 库来说，人们第一次接触到这个库时，往往无法轻易地理解它。但是如果 他们能认识到这些类都是用来包装 InpulStream 的，一切都会变得简单多了。

还有类型问题。有些时候，人们在客户代码中依赖某种特殊类型， 然后忽然导入装饰者，却又没有周详地考虑一切。现在，我的一个优点是，你通常可以透明地 插入装饰者，客户程序甚至不需知道它是在和装饰者打交道。但是，如我刚刚所说的，有些代 码会依赖特定的类型，而这样的代码•导入装饰者，嘴！出状况了！

还有一个问题，就是采用装饰者在实例化组件时，将 增加代码的攵杂度。一旦使用装饰者模式，不只需要实例化组件，还要把此组件包装进装饰者 中，天晓得有几个。

## 与工厂模式和生成器模式的关系

访谈工厂 (Factory) 模式和生成器 (Builder) 模式，我听说他们对这个 问题有很大的帮助

# 洋葱模型

# 高阶函数

# Processor
