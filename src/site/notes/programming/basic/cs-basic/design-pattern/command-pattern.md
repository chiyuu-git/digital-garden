---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-10-14-Sat, 1:57:35 pm","date-modified":"2023-10-15-Sun, 12:35:23 pm","permalink":"/programming/basic/cs-basic/design-pattern/command-pattern/","dgPassFrontmatter":true}
---


# 定义

将**请求**封装成对象，以便使用不同的请求、 队列或者日志来参数化其他对象。命令模式也支持可撤销的操作。

现在，仔细看这个定义。我们知道一个命令对象通过在特定接收者上绑定一组动作来封装一个请求。要达到这一点，命令对象将动作和接收者包进对象中。这个对象只暴露出一个 execute() 方法，当此方法被调用的时候，接收者就会进行这些动作。

从外面来看，其他对象不知道究竟哪个接收者进行了哪些动作，只知道如果调用 execute() 方法，请求的目的就能达到。

![](/img/user/programming/basic/cs-basic/design-pattern/command-pattern/image-20231014141918690.png)

# 延伸出宏命令

在一个 execute 方法中封装多个 command 即可

# 实现队列请求

![](/img/user/programming/basic/cs-basic/design-pattern/command-pattern/image-20231014143125313.png)

[promise 并发任务队列](../../../front-end/primitive/es/es-async/promise.md#Promise%20all) 实际上就是一种命令模式的实践

# 死机恢复 日志系统 事务系统

![](/img/user/programming/basic/cs-basic/design-pattern/command-pattern/image-20231015111514999.png)

# FAQ

## 感觉像是多个适配器模式的组合

真要说的话, 感觉 event bus 的 NA 和 JS 的处理, 更像是命令模式, 而不是适配器模式.

因为没有适配器这个类, 相当于是同样的 on 方法, 在 NA 和 JS 走的是不同的代码而已

## 实际操作时，很常见使用“聪明”命令对象，也就是直接实现了请求， 而不是将工作委托给接收者
