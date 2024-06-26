---
{"aliases":["模板方法模式"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-10-17-Tue, 7:34:35 pm","date-modified":"2023-10-17-Tue, 9:13:02 pm","permalink":"/programming/basic/cs-basic/design-pattern/template-method-pattern/","dgPassFrontmatter":true}
---


# 引入

模板方法定义了一个算法的步骤, 并允许子类为一个或多个步骤提供实现

## 不使用模板方法

1. Coffee 和 Tea 主导一切；它们控制了算 法
2. Coffee 和 Tea 之间存在着近复的代码。
3. 对于算法所做的代码改变，需要打开子类修改许多地方。
4. 由于类的组织方式不具有弹性，所以加入新种类的咖啡因饮料需要做许多工作
5. 算法的知识和它的实现会分散在许多类中。

## 使用模板方法

1. 由 CaffcineBeveragc 类主导一切，它拥 有算法，而且保护这个算法。
2. 对子类来说，CaffcineBeverage 类的存 在，可以将代码的复用最大化。
3. 算法只存在于一个地方，所以容易修改。
4. 这个模板方法提供了一个框架，可以让 其他的咖啡因饮料插进来。新的咖啡因饮料只需要实现白己的方法就可以了。
5. CaffeincBevcrage 类专注在算法本身， 而由子类提供完整的实现。

# 定义

你已经看到了在茶和咖啡的例子中如何使用模板方法模式。现在，就让我们来看 看这个模式的正式定义和所有的细节：模板方法模式在一个方法中定义一个算法的骨架，而 将一些步骤延迟到子类中.模板方法使得子类可以在不改变 算法结构的情况下，重新定义算法中的某些步骤

这个模式是用来创建一个算法的模板。什么是模板？如你所见的，模板就是一 个方法。更具体地说，这个方法将算法定义成一组步骤，其中的任何步骤都可 以是抽象的，由子类负责实现。这可以确保算法的结构保持不变，同时由子类 提供部分实现

![](/img/user/programming/basic/cs-basic/design-pattern/template-method-pattern/image-20231017194040071.png)

# 钩子

钩子是一种被声明在抽象类中的方法，但 只有空的或者默认的实现。钩子的存在， 可以让于类有能力对算法的不同点进行挂 钩。要不要挂钩，由于类自行决定

为 r 使用钩子，我们在子类中覆盖它。在这里，钩子控制了咖啡因饮料是 否执行某部分算法，说得更明确一些，就是饮料中是否要加进调料

我们如何得知顾客是否想要调料呢？开口问不就行了

# 模板方法的实例

java Array.sort 方法其实就是一个模板方法. 接受的 对比函数就相当于是我们在子类中要重写的那个方法.

只不过为了让一切数组都可以被 sort 方法排序, 所以挂载在了 Array 上, 成为了其静态方法.

## 这真的是一个模板方法 模式吗？还是你的想象力太丰富了

这个模式的重点在于提 供一个算法,并让子英实现某些步骤 而数组的排序做法很明显地并非如 此. 但是, 我们都知道，荒厅中的模 式并非总是如同教科书例子一般地中 规中矩，为了符合当前的环境和实现 的约束，它们总是要被适当地修改。 这个 Array 类 sort() 方法的设计者受到 一些约束。通常我们无法设计一个美 继承 Java 数组，而 sort。方法希望能够适用于所有的数姐 (每个数组都是不同的类)。所以它们定义了一个静态方法，而由被排序的对象内的每个元素自行提供比较大小的算法部分

所以，这虽然不是教科书上的模板方 法，但它的实现仍然符合模板方法模 式的精神。再者,由于不需要继承数 组就可以使用这个算法，这样便得排 序变得更有弹性、更有用

## 排序的实现实际上看起 来更像是策略模式，而不是横板方法 模式。为什么我们要将它归为模板方 法

你之所以会这么认为.可能是因为策略模式使用对象组合。

在某种程度上.你是对的——我们使用数组对象排序我们的数组，这部分和策喀模式,非常相似。但是请记 住，在策略模式中，你所组合的臭实现了整个算法。数组所实现的排序算法并不完祭，它需要一个类填补 compareTo() 方法的实现。因此，我们认为这更像模板方法

## 我们知道应该多用组合，少用继承，对吧？ sort。模板方法的实现决定不使用继承，sort 方法被 实现成一个静态的方法，在运行时和 Comparable 组合。这样的做法有何优缺点？你如何处置这 个难题？难道 Java 数组让这一切变得特别麻烦吗

## 想一想另一个模式，它是模板方法的一种特殊状况，原语操作用来创建并返回对象。这是什么模式？

工厂方法即是特殊的模板方法

# FAQ

## 当我创建一个模板方法 时，怎么才能知道什么时候该使用抽 象方法.什么时候使用钩子呢

当你的子类“必须”提 供算法中某个方法或步骤的实现时， 就使用抽象方法。如果算法的这个部 分是可选的，就用钩子。如果是钩子 的话.子类可以选择实现这个钩子， 但并不强制这么做

## 使用钩子的真正目的是什么

钩子有几种用法。如我们之前所说的.钩子可以让子类实 现算法中可选的部分，或者在钩子对 于子美的实现并不重要的时候.子 类可以对此钩子无之不理。钩子的另 一个用法，是让子类能够有机会对模 板方法中某些即将发生的（或刚刚发 生的）步骤作出反应。比方说，名为 **justReOrdcrcdList()** 的钩子方法允许子 美在内部列表重新组织后执行某些动 作（例如在屏第上重新显示数据）。 正如你刚刚看到的，钩子也可以让子 类有能力为其抽象类作一些决定

## 似乎我应该保持抽象方 法的数目越少越好，否则.在子类中 实现这些方法将会很麻烦

当你在写模板方法的时 候，心里要随时记得这一点。想要做 到这一点，可以让算法内的步骤不要 切割得太细，但是扣果步骤太少的 话.会比较没有弹性、所以要看情况 折发。

也请记住,某些步骤是可选的，所以 你可以耨这些步骤实现成钩子，而不 是实现成抽拿方法,这样就可以让抽 象美的子类的负荷减轻

## 模板方法模式与策略模式的区别

我想他们知道我是谁。 不过为了你，我再说一次好了：我定义一个算法 家族，并让这些算法可以互换。正因为每一个算 法都被封装起来了，所以客户可以轻易地使用不 同的算法

嘿！听起来好像是我在做的事情。但是我的意图 和你有点不太一样：我的工作是要定义一个算法 的大纲，而由我的子类定义其中某些步骤的内 容。这么一来，我在算法中的个别步骤可以有不 同的实现细节，但是算法的结构依然维持不变。 不过你就不一样了，似乎你必须放弃对算法的控 制。

我不确定话可以这么说……更何况，我并不是使 用继承进行算法的实现，我是通过对象组合的方 式，让客户可以选择算法实现

这我记得。但是我对算法有更多的控制权，而且 不会重复代码。事实上，除了极少的一部分之外, 我的算法的每一个部分都是相同的，所以我的类 比你的有效率得多。会重复使用到的代码，都被 我放进了超类中，好让所有的子类共享。

你或许更有效率一点（只是一点点），也的确需 要更少的对象。和我所采用的委托模型比起来, 你也没那么复杂。但是因为我使用对象组合，所 以我更有弹性。利用我，客户就可以在运行时改 变他们的算法，而客户所需要做的，只是改用不 同的策略对象罢了。拜托.作者选择把我摆在第 1章，这不是没有道理的！

好吧，我真替你感到高兴，但是你别忘了，环顾 四周，我可是最常被使用的模式。为什么呢？因 为我在超类中提供了 •个基础的方法，达到代码 的复用，并允许乎类指定行为。我相信你会看到 这一点在创建框架时是非常棒的！

也许呢……但是，别忘了依赖！你的依赖程度比 我高。

这话怎么说？我的超类是抽象的。

但是你必须依赖超类中的方法的实现，因为这是 你算法中的一部分。但我就不同了，我不依赖任 何人，整个算法我自己搞定！

