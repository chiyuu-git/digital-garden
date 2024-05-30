---
{"aliases":["代理模式"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-10-19-Thu, 11:33:45 am","date-modified":"2023-10-21-Sat, 9:24:18 am","permalink":"/programming/basic/cs-basic/design-pattern/proxy-pattern/","dgPassFrontmatter":true}
---


代理模式为另一个对象提供 i 个替身或占位符以控 制对这个对象的访问

使用代理模式创建代 表 (representative) 对象,让代表对象控 制某对象的妨同，被 代理的对象可区是运 程的对象、创建开箱 大的对象或需要安全 控制的时象。

我们已经看到代理模式是如何为另一个对象提供替身的。我们 也将代理描述成另一个对象的“代表” O

但是代理控制访问怎么解释呢？这听起来有一点奇怪。别担 心，在糖果机的例子中，代理控制了对远程对象的访问。代理 之所以需要控制访问，是因为我们的客户 (监视器) 不知道如 何和远程对象沟通。从某个方面来看，远程代理控制访问，好 帮我们处理网络上的细节。正如同刚刚说过的，代理模式有许 多变体，而这些变体几乎都和“控制访问”的做法有关。稍后 我们会对此讨论得更详细，目前我们还是先看看几种代理控制 访问的方式：

就像我们已经知道的，远程代理控制访问远程对象。

虚拟代理控制访问创建开销大的资源。

保护代理基 F 权限控制对资源的访问。

使用代理模式创建代 表 (representative) 对象,让代表对象控 制某对象的妨同，被 代理的对象可区是运 程的对象、创建开箱 大的对象或需要安全 控制的时象。

现在你已经有其本的概念了，来看看类图……

![](/img/user/programming/basic/cs-basic/design-pattern/proxy-pattern/image-20231019114834253.png)

首先是 Subject,它为 RealSubject 和 Proxy 提供了接口。通过实现同一接口， Proxy 在 RealSubject 出现的地方取代它。

RealSubject 是真正做事的对象，它是被 proxy 代理和控制访问的对象。

Proxy 持有 RealSubject 的引用。在某些例子中，Proxy 还会负责 RealSubject 对 象的创建与销毁。客户和 RealSubject 的交互都必须通过 Proxy。因为 Proxy 和 RealSubject 实现相同的接口 (Subject),所以任何用到 RealSubject 的地方，都可 以用 Proxy 取代。Proxy 也控制了对 RealSubject 的访问，在某些情况下,我们可能 需要这样的控制。这些情况包括 RealSubject 是远程的对象、RealSubject 创建开销 大，或 RealSubject 需要被保护。

你已经了解了一般的代理模式，现在让我们看看，除了远程代理之外，代理模式 还有哪些用法

![](/img/user/programming/basic/cs-basic/design-pattern/proxy-pattern/image-20231019115125743.png)
