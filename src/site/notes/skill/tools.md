---
{"aliases":["实用工具","实用软件"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-09-20-Tue, 3:08:11 pm","date-modified":"2024-07-01-Mon, 5:37:13 pm","permalink":"/skill/tools/","dgPassFrontmatter":true}
---


# PS

输出带有混合模式效果的单个图层：混合模式搞定之后，盖印图层 CAS+E，然后 C+ 左键 选中图层选取，去到盖印图层，选择返乡，删除或者添加蒙版也可以

让图层的画布大小和图片一样 C+ 左键 选取 然后裁切 透明 左上右下

# 电脑

1. 新建文件夹：右键 -W-F
2. 显示文件格式：我的电脑 - 工具 - 文件夹选项 - 查看 - 勾选（或否）隐藏文件扩展名
3. Excel 冻结窗口：视图 - 冻结窗格
4. 贴吧:#(滑稽)
5. Excel C+↓ c+→
6. tracert [www.baidu.com](http://www.baidu.com) 可以在命令提示符处 追踪

# 格式转换

同事找到了 webp 格式的图片素材，找不到在线转换图片格式转换成 png

我：安装 [sharp](https://www.zhihu.com/search?q=sharp&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A2480669121%7D)，3 行代码 1 秒钟，webp 转 png

```text
const sharp = require('sharp')

sharp('1.webp').png().toFile('1.png')
```

“以后你要转图片就放个 1.webp 在这个文件夹下，运行一下 png 图片就转好了”

# Typora

ctrl+tab 切换标签页

免费版安装包地址：<https://limbopro.com/archives/416.html>

**Windows 离线安装包** [typora-update-x32-1117.exe.zip](https://limbopro.com/usr/uploads/2021/12/1423353763.zip) / [typora-update-x64-1117.exe.zip](https://limbopro.com/usr/uploads/2021/12/1075124544.zip)；

**Mac 离线安装包** [Typora-0.11.18.(5941).dmg](https://limbopro.com/usr/uploads/2021/12/2009290104.dmg)；

**Linux 离线安装包** [typora_0.11.18_amd64.deb.zip](https://limbopro.com/usr/uploads/2021/12/2022339376.zip)；

去官网下载免费版本：[https://typora.io/windows/dev_release.html](https://typora.io/windows/dev_release.html) （Windows/Linux）；

去官网下载免费版本：[https://typora.io/dev_release.html](https://typora.io/dev_release.html) （Mac）；

## LaTeX

### 下标和上标

$A_{1}→A_{2},A_{2}→A_{3},...,A_{i}→A_{i+1}$

$A^{1}→A^{2}$

### 标题序号

<https://www.jianshu.com/p/bf52cb523aaf>

# Obsidian

块的概念：笔记的基本组成结构不是文件，而是一个个块，块可以是一段文字、图片、视频、网页，也可以是另一个文件

相对于文件夹的组织形式而言，双向链接是二维的组织结构，而块则让基本组成更加细粒度，可以做到相当复杂的结构组合，但是同时也需要注意解耦

如何避免过度耦合？二维的行和列，需要定义清晰，不要重复

社区：<https://github.com/obsidianzh/forum/discussions?page=3>

开发计划：<https://trello.com/b/Psqfqp7I/obsidian-roadmap>

## 竞品

affine

感觉在 pdf 读书笔记上, 还是有缺陷

在素材管理上也不如数据库 notion 方便

## 双向链接

可以使用标准的 markdown 链接

双向链接：块与块之间通过双向链接进行链接

+ 链接到标题 #
+ 链接到片段 ^
+ 别名 |

双向同步：`![[]]`

## My Key Map

`<c+r>` reload obsidian

`<c+t>` toggle tabs plugin

## 标签功能

可以针对段落打标签，只要在标题后面跟上就行

## 模板功能

<https://publish.obsidian.md/help-zh/%E6%8F%92%E4%BB%B6/%E6%A8%A1%E6%9D%BF>

### 日记随笔

tasks 插件： <https://obsidian-tasks-group.github.io/obsidian-tasks/>

与 reminders 插件配合，可以实现任务提醒功能

与 calendar 插件结合，快速回顾日记。通过开启 weekly，也可以扩展为周记

## Dataview Plugin

course:

+ <https://zhuanlan.zhihu.com/p/373623264>
+ <https://zhuanlan.zhihu.com/p/508432406>

treat vault as database

query yaml / metaInfo

### Basic Usage

```plainText
list 
 | table (file.date as date, file.tags) 
 | task
from "daily" | #tag
where conatiner() and time = xxx or
group by xxx
sort asc desc, xxx asc
```

above are the support keyword

other sql-like keyword can implement by js query

### Meta Info

```yaml
file:name
file:flolder
file:path \ size \ ctime  \ tags \ etags
file: mtime #最后修改的事件，一定程度上可以体现文章的活跃程度，但是不够全面
```

[full list](https://blacksmithgu.github.io/obsidian-dataview/data-annotation/)

### Yaml

```yaml
title: {{title}}
tags:
 - 工具
publish: xxx
cssclass: xxx
aliases: xxx
key: value
arr: [one, two, three] 
```

maintain a yaml template

## Vim Plugin

## Kanban Plugin

kanban plugin can also implement task management.

it is similar to notion's board panel.

## Publish

github or share to notion

## 全局引用功能

```
![[^^花 酒 人]]
```

## Ai 能力

[几个 AI 插件 Text Generator, Smart Connections, Copilot 的简单调查 - 经验分享 - Obsidian 中文论坛](https://forum-zh.obsidian.md/t/topic/31483)

关注帖子的最新进展即可. 感觉目前阶段几个 ai 对于中文都不是太好用

![](/img/user/skill/tools/image-20240620104250083.png)

## Projects Plugin

# 如何解决 GitHub 作图床国内无法显示的问题

## Github Host

read://https_ip.chinaz.com/?url=https%3A%2F%2Fip.chinaz.com%2Fraw.githubusercontent.com

查询 raw.githubusercontent.com 的真实 ip, 因为会经常变动, 然后更新 host

C:\Windows\System32\drivers\etc

ipconfig /flushdns

## 镜像

最开始我用 WordPress 是直接将图片放到 VPS 上，后来发现转移有些不方便也不好管理，再加上一直是便宜主机，容量也有限。后来我又转到 [ya.ru](https://mianao.info/go/aHR0cHM6Ly95YS5ydQ)，上传方式和速度都不理想。

最后还是用了 GitHub，管理方便又免费，速度也不错。不过最近发现 **raw.githubusercontent.com** 和 **github.com** 在国内很多地方连接都有问题，导致图片无法正常显示，不得已研究了一下。

### 借助 Cloudflare 做镜像网站

我开始是利用 Cloudflare 的 Worker 做了个镜像，具体实现见这里 [省钱秘笈之巧用 Cloudflare 免费做个镜像网站](https://mianao.info/2020/03/24/%E7%9C%81%E9%92%B1%E7%A7%98%E7%AC%88%E4%B9%8B%E5%B7%A7%E7%94%A8Cloudflare%E5%85%8D%E8%B4%B9%E5%81%9A%E4%B8%AA%E9%95%9C%E5%83%8F%E7%BD%91%E7%AB%99) 。

因为我的 GitHub 图片地址都是如 **raw.githubusercontent.com/harry3633/blogmianao/master/openwrt/menu.png** 这样的，所以我把 **raw.githubusercontent.com** 做了个镜像，然后用 **xxxxx.xxxxx.workers.dev** 替换就可以了。

虽然很方便，Cloudflare 免费账户每天 10 万次请求，对我这样的 blog 来说也是足够用了。但是，就怕别人盗了图片链接，可能很快就没了。

### 借助 jsDelivr 的 CDN

jsDelivr 是国外的一家优秀的公共 CDN 服务提供商，它可以加速 Github 仓库的文件，比如图片， js 或 css 等。

[官方网站：www.jsdelivr.com](https://mianao.info/go/aHR0cHM6Ly93d3cuanNkZWxpdnIuY29t)

实现方式也是很简单，无须注册或改代码，直接替换链接即可。

例如：

我的图片原始链接：**[https://raw.githubusercontent.com/harry3633/blogmianao/master/openwrt/menu.png](https://raw.githubusercontent.com/harry3633/blogmianao/master/openwrt/menu.png)**

**harry3633** 是 GitHub 账号名，**blogmianao** 是仓库名，**openwrt/menu.png** 是图片在仓库的具体位置。

更新链接为：

**[https://cdn.jsdelivr.net/gh/harry3633/blogmianao@master/openwrt/menu.png](https://cdn.jsdelivr.net/gh/harry3633/blogmianao@master/openwrt/menu.png)**

**master** 可选择仓库的不同分支，如果像我一样仓库只有 master 的分支，**@master** 是可以省略的，直接用图片链接：**[https://cdn.jsdelivr.net/gh/harry3633/blogmianao/openwrt/menu.png](https://cdn.jsdelivr.net/gh/harry3633/blogmianao/openwrt/menu.png)**。

如果在仓库选择了 **release** 进行发布，那么 **@** 后面可以写版本号，如 **[https://cdn.jsdelivr.net/gh/harry3633/blogmianao@1.0/openwrt/menu.png](https://cdn.jsdelivr.net/gh/harry3633/blogmianao@1.0/openwrt/menu.png)**，一般来说作为图库没必要搞这么复杂，大概只有一些 css，js 文件加速才会用到吧。

注意 jsDeliver 不支持加载超过 20M 的资源，所以图片还是不要搞大了，我一般都是先在 [tinypng.com](https://mianao.info/go/aHR0cHM6Ly90aW55cG5nLmNvbS8) 压缩后再放到 GitHub，据说 Github 仓库的容量有 1G 的上限，也有说硬性限制是 100G，反正对我的 blog 来说够用了，不够再开个仓库了。

# 图床

picGo 太垃圾了, 每次用都要调试很久, 心累了, 再也不碰

[路过图床](https://imgse.com/account/awaiting-confirmation)

# Quicker

[Mac 有没有类似 Quicker 的软件？ - 知乎](https://www.zhihu.com/question/362216392)

alfred workflow

# 飞书多维表格

高级字段: 创建人, 创建时间, 父记录, 比较好用

[快速上手多维表格](https://www.feishu.cn/hc/zh-CN/articles/697278684206-%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8B%E5%A4%9A%E7%BB%B4%E8%A1%A8%E6%A0%BC)

## 子标签, 子分类

飞书也许不需要子分类, 直接用多组合来处理. 比如先用一级分类分组, 再用二级分类分组, 就可以获取一个多级分类的标签.

[使用单选、多选字段实现多级联动](https://www.feishu.cn/hc/zh-CN/articles/208458816653-%E4%BD%BF%E7%94%A8%E5%8D%95%E9%80%89-%E5%A4%9A%E9%80%89%E5%AD%97%E6%AE%B5%E5%AE%9E%E7%8E%B0%E5%A4%9A%E7%BA%A7%E8%81%94%E5%8A%A8)

# 使用工具网站 Utils

powertoy

[LKs 网站推荐合集](https://lkssite.vip/)

# 远程控制电脑

日光 + 月光的组合似乎是免费 + 门槛高的搭配

parsec

都有公网 ipv6 了为什么还需要其他呢?

[开始游戏：远程串流一文通关 | 机核 GCORES](https://www.gcores.com/articles/162422)

## 原理：简单搞懂远程串流是什么？

在本章节中，我将会介绍市面上常见的两种串流方式：经过服务端的中转、P2P 直连，并针对常见家庭的网络布局进行说明。

实际上主流的远程控制或串流主要区分为两种类型：经过服务端的中转模式和 P2P 直连模式。

### 经过服务端的中转模式

中转模式在我们日常生活中其实接触的最多，从最早的 QQ 桌面共享控制、Windows 桌面控制，到工作生活中如今越来越多的腾讯会议、飞书会议的共享操控，再到更专业一些的向日葵、Todesk 等工具，本质上都是这一类型的远程串流方式。

简单来讲，就是宿主（Host）和用户（Server）之间要进行消息传输时，需要通过中转服务器（Server）来进行解析和转发。

![](/img/user/skill/tools/image-20240701101651674.png)

在整个过程中，宿主的桌面显示会被捕获到中转服务器然后展示给用户，用户在本机上直接的操作，比如鼠标的拖拽点击、键盘的按键、快捷键等，又会被用户端的程序捕获、解析、转发给中转服务器，然后将对应的操作在宿主端复现出现，实现一个完整的交互闭环。

这种形式自然有其优劣势，从优点角度来看主要突出一点——便捷：

- 通常中转服务端有独立完整的客户端可以下载，安装和使用比较便捷，上手成本低。
- 中转服务由专业服务器提供，只要双端能够正常访问网络，就可以使用，并不受特定端口、IP 类型等限制。

但是，你肯定也发现了其中的局限性：

- 中转服务器会捕获、解析、转发相应数据，本身可能存在一定安全风险（不要对企业的职业素养有任何奢求）
- 为了控制成本，这类免费的服务会有带宽限制，满足日常 PPT 演示等场景足够，但是对于复杂的游戏画面并且要满足帧数流畅的要求，远远不够；而专门针对游戏场景的模式，通常需要额外付费，且费用不低。
- 由于多数不是针对游戏场景，也自然缺少手柄映射等支持能力。

于是，我们将目光转向 P2P 类型的服务，看如何来满足我们的游戏需求。

### P2P（peer-to-peer) 直连模式

> 对等式网络（英语：peer-to-peer， 简称 P2P），又称点对点技术，是 [去中心化](https://www.gcores.com/link?target=https%3A%2F%2Fzh.wikipedia.org%2Fwiki%2F%25E5%258E%25BB%25E4%25B8%25AD%25E5%25BF%2583%25E5%258C%2596)、依靠用户群（peers）交换信息的互联网体系，它的作用在于，减低以往网路传输中的节点，以降低资料遗失的风险。与有中心服务器的中央网络系统不同，对等网络的每个用户端既是一个节点，也有服务器的功能，任何一个节点无法直接找到其他节点，必须依靠其户群进行信息交流 *。——维基百科 Wiki

通过术语理解 P2P 可能较为生硬，我们换个角度思考：既然在第一种方法里，我们要追求更直接、更快速的链接方式，并且尽可能让远程串流的带宽不成为画面传输的限制因素，我们有一个很简单的方式：抛弃中转服务器，直接让宿主和用户端链接。

这种没有中转服务器的方式，就可以理解为 P2P 式直连。

![](/img/user/skill/tools/image-20240701101902644.png)

#### 内网、公网有何分别？

毕竟我们不是专业的网络工程师，不需要对这些有非常准确的定义，只要能帮助我们建立起一些基础概念并运用在可能出现的问题排查中即可。

简单来讲，内网，就是俗称的局域网，以前黑网吧的《CS》、《War3》就是用的局域网技术。你能在这个网吧内找到同样上机的玩家，但是没法和别的网吧的同学一起联机；

公网，即因特网、互联网，就像后来出现的浩方、11、VS，即便你和同学各自在家上网，也能开个房间一起玩。

在我们上述介绍的场景里，内网其实就对应“家里面的设备”，在光猫和路由器下接入的 PC、智能电视、NAS 等等都是我们家庭内网中的设备；而在地铁、大巴、机场上，用手机、热点、或者其他商家提供的 WiFi 时，我们实际上加入的就是不同的“局域网”（移动、联通、电信在没有公网 IP 的时候也可以理解为一个大内网）。

我们已知 P2P 是一种设备的直连方式，那么我们就需要在茫茫互联网中通过一串字符地址找到一台固定的设备来进行连接。如果你有固定的 IPV4 的地址，那你在网络上已经是让人艳羡的富豪啦，互联网的世界向你打开！

当然，我们更多普通玩家拥有一个属于自己的 IPV6 地址也是非常简单的，如今三大运营商在光猫拨号时通常会默认下发一个 ipv6 的公网地址，你可以通过拨号上网的设备后台确认到这一点。

![](/img/user/skill/tools/image-20240701102112067.png)

### NAT 和网络穿透

NAT 是网络地址转换（Network Address Translation）的缩写，是一种将私有 IP 地址转换成公共 IP 地址的技术，它主要用于局域网与公网之间的通信。NAT 的作用是在一个私有网络中，通过转发指定端口的方式将多个内部设备共享一个公共 IP 地址，从而减少公共 IP 地址的使用量，同时外部用户访问该地址时无法判断有多少内部设备，也增强了网络的安全性。

网络穿透，俗称“打洞”，也就是在 NAT 环境下用于建立设备之间直接通信连接的技术。

在 NAT 环境下，一个具体的私有 IP 地址无法直接被公共网络访问，因此需要通过一些技术手段，如端口映射、DMZ、虚拟服务器、UPnP、NAT-PMP 等，来建立公共网络与私有网络、私有网络与私有网络之间的通信连接。

通过将宿主机 PC 的部分端口转发到路由器上，我们就可以实现在公网连接路由的 IPv6 地址，直接连接到宿主机上，实现远程串流控制。

### 常见家庭网络拓扑

家庭中最常接触的网络设备其实主要分为两种：

- 光猫：通常由运营商提供，负责将入户的光纤 - 光信号，转化为可以被多数设备解码的电信号，通常承担家庭网络拨号的功用；
- 路由器：通常由家庭购买，将光猫的网络信号转换为 WiFi 信号使用，也支持上网设备直接通过连接网线的方式进行上网； 对外，光猫负责使用我们的帐号密码进行拨号上网（PPPOE），将我们的网络注册到运营商内，再通过运营商访问公网；对内，路由器将光猫的宽带信号进行桥接转发，并在自己的网关下连接各式各样的设备，实现各类设备的上网访问。

因此，我们就可以得到一个简单的网络拓扑图：

![](/img/user/skill/tools/image-20240701102355958.png)

也许你还会有一些其他设备，比如 UU 盒子、软路由、交换机等等，这样网络拓扑就会更为复杂，本文不做展开。提供一个判断依据：只要你的 PC 不是通过这些设备再连接的路由器，就可以按照下文的方法进行远程串流的设置。 如果你的网络拓扑无法简化成以上图片样式，建议尽可能做物理调整。

## 光猫桥接与路由器设置（可选）

> 请注意，以下这些操作为非必要操作！

在上述基础网络拓扑图的基础上，我们可以通过设置光猫桥接与路由器拨号，对网络质量进行提高。

如果面对以下的问题：

- Parsec 可直接连接，但 Network 延迟较高需要优化；
- Parsec 无法直连，报错误代码为 6023 的问题；

我建议在做好充足备份的前提下，进行相关操作：在宿主机上登录光猫网关，网关地址可以在光猫背后查看，同时记录下对应光猫的设备型号，方便后续搜索超管密码，建议拍照省事。

1. 获取光猫超级管理员帐号密码，并确认自己宽带账号密码。
2. 登录光猫后台，寻找宽带设置部分，将原有拨号方式改为桥接。

- 找到网络 - 宽带设置，不同型号光猫配置位置不同，但是界面相似；
- 找到写有“INTERNET”的链接，进行勾选，此时下方表单会刷新会对应的配置内容，截图保存备份；
- 将 IP 协议版本改为“IPV4/IPV6”，这样后续拨号时可以获取到 IPV6 的公网地址；将连接模式改为桥接；
- 保存配置，退出光猫后台。

![](/img/user/skill/tools/image-20240701102601292.png)

3. 登录路由器后台，进行相关设置。

- 找到上网设置，将网络连接方式改为“PPPOE”，填入宽带账号和密码；宽带的帐号密码可以打电话给运营商获取

![](/img/user/skill/tools/image-20240701102612521.png)

- （可选）找到防火墙设置，将 IPV6 部分的防火墙进行关闭；
- IPV6 防火墙可能会对后续串流转发存在一定影响，初始步骤中可以不进行设置，如果出现 6101 或 6023 问题后再来进行设置，具体可参考常见问题排查 。
- 点击保存，完成路由器宽带设置。
- 选择高级设置 - 其他设置，开启 UPNP 功能，开启后路由器自动将软件用得到端口进行转发。

![](/img/user/skill/tools/image-20240701102621300.png)

![](/img/user/skill/tools/image-20240701102636481.png)

开启 Upnp 可以解决 90% 问题，部分不支持 upnp 的工具需要手动转发端口

4. 完成以上操作，在 PC 上能够正常联网访问内容就代表配置生效啦！

了解清楚以上这些后，我们要通过软件和公网的能力，在宿主和用户之间建立起联系。

来，让我们开始操作！

## 选择方案，开始操作

本章节中，我将会根据自己之前使用的经验来介绍以下几种主流远程串流的方案和相关配置操作：

- Parsec
- Parsec+Zerotier
- Moonlight+Zerotier

[ZeroTier Central](https://my.zerotier.com/network/565799d8f6c5d440)

565799d8f6c5d440

### Parsec

官网：[Connect to Work or Games from Anywhere | Parsec](https://www.gcores.com/link?target=https%3A%2F%2Fparsec.app%2F)

适用平台：Windows、Web、MacOS、Android、Linux

优点：

- 对显卡驱动没有要求
- 可直接展示桌面，并进行远程操作
- 支持手动设置分辨率与串流带宽
- 串流延迟低，对 Switch Pro、Xbox 手柄识别和支持良好（没有 PS 手柄，因而并未测试）
- 宿主机上也可直接操作，不会与远程串流产生冲突

缺点:

- 登录时需要解决网络连接问题
- 建议直接外接手柄或键鼠操作，触屏操作兼容差

### Moonlight

官网：[Moonlight Game Streaming: Play Your PC Games Remotely](https://www.gcores.com/link?target=https%3A%2F%2Fmoonlight-stream.org%2F)

适用平台：Windows、MacOS、Android、LInux

优点：

- 对 N 卡游戏等识别应该有独立优化，个人体感上比 Parsec 更顺畅
- 支持手动设置分辨率与串流带宽
- 支持虚拟手柄映射，勉强能玩玩文字类的简单游戏；

缺点：

- 仅支持 N 卡，A 卡用户无法使用
- 未尝试宿主机上直接操作，不确定是否会与串流操作冲突
- 需要手动设置才可展示桌面，若不设置仅能选择 GeForce Experience 识别出的游戏进行游玩；
- Moonlight 在宿主端识别上问题较多（我经常出现识别不到的情况，需要删除后重新添加），每次删除主机重连需要手动在宿主端输入验证码；

### Zerotier

官网：[ZeroTier – Global Area Networking](https://www.gcores.com/link?target=https%3A%2F%2Fwww.zerotier.com%2F)

适用平台：Windows、MacOS、Android、IOS、Linux、FreeBSD、NAS

优点：

- 通过 Web 端可以直接管理虚拟网下设备情况
- 免费版对网速和带宽没有限制，支持添加 30 个设备；

缺点：

- 设备入网之后一定要记得在 Web 端确认并允许访问网络！！！

在实际使用过程中，我仍建议配合向日葵进行一些辅助操作。从实践经验来看，以上两款串流软件虽然在游戏表现极佳，但是如果要做相关的界面操作仍需要依赖键盘鼠标，在平板、手机上的交互并不理想，而向日葵在移动设备上对手势操作有比较好的支持。

## 方案一：仅 Parsec

此方法要求宿主端与用户端均拥有公网 IPv6 网段，才可直接连接。

## 方法二：Parsec+Zerotier

Zerotier 注册与使用

1. 在 [Zerotier](https://www.gcores.com/link?target=https%3A%2F%2Fmy.zerotier.com%2F) 官网完成注册和登录后，进入到个人页面。
2. 点击“Create A Network”，创建一个虚拟网，系统会默认创建你的网络 ID 和名称。
3. 取消选中“IPv6 自动分配”部分中的所有复选框（如果选中），到这一步虚拟网已经可以使用了。
4. 在宿主端和用户端下载 Zerotier 应用，打开后选择 “Join New NetWork”，复制刚才创建的网络 ID 粘贴，即可完成虚拟网的加入申请。
![](/img/user/skill/tools/image-20240701105317892.png)
5. 重新回到 Zerotier 的网络详情页面，滚动找到“Members”这一栏。你会看到设备已经被添加到对应网络中，并分配了一个虚拟网的 IP 地址，此时需要勾选最前端的方框进行授权。
 ![](/img/user/skill/tools/image-20240701105448515.png)
6. 勾选宿主、用户端的地址进行授权，此时机器已顺利加入虚拟网。

## 方法三：Moonlight+Zerotier

1. 开启 Zerotier，将用户设备加入到虚拟网；
2. 开启 Moonlight，此时将会自动搜索局域网内开启了相应功能的 PC，若连接正常，将自动识别出 PC 设备，并在屏幕上以加密形式展示；若无法连接，同一局域网下请输入 PC 的路由 IP，若通过 Zerotier 组网，请输入 Zerotier 中的 Manged IPs。
![](/img/user/skill/tools/image-20240701110118948.png)

3. 单击弹出窗口，将对应 PIN 码在宿主端输入，即可完成串流匹配。
4. 在正式开始串流游戏前，推荐进入设置手动修改码流带宽。以手机作为游玩端为例，通常建议将分辨率设置在 720p，码流设置在 5-8Mbps 即可，默认 20Mbps 的码流实际有些过高。若对帧数有要求，记得勾选高级设置中解锁全部可用帧率。

## 完整的远程控制流

[IT Service](https://it.bytedance.com/portal/articles/195815457481162808?lang=2&source=6)

[关于Moonlight(月光串流）DDNS与远程开机配置 - 哔哩哔哩](https://www.bilibili.com/read/cv12151227/)

完成以上网络配置和操作之后，我们仅需要将电脑与智能插座连接，就可以实现随时随地远程开机串流玩游戏的目的了。此处以我自己日常玩游戏的流程举例说明（我使用了向日葵 C1PRO）：

1. 打开向日葵远程控制 App，直接远程命令宿主机开机；
2. 开机后直接通过向日葵登录 PC，完成 Steam 开启游戏等需要鼠标操作的动作（我会开个模拟器挂机用）；
3. 切换 Parsec，直接用蓝牙连接手柄进行游戏。
4. 游戏完毕后，直接向日葵控制远程关机即可。

### 智能插线板

- 价格：< 100
- 支持直接连接公网，推荐向日葵 C1Pro 或向日葵智能插线板；
- 如果购买小米智能插线板，需要确认家中是否有蓝牙网关（小米音响等），如果仅购买一个智能插线板是无法使用的！（小米你罪大恶极！）

### Macos 开机启动

sunshine 需要使用终端启动, 有点麻烦

zeroTier 可以直接设置开机自动启动, 没有问题. 每次启动, 内网 ip 还会变化. 导致需要重新使用 pin 连接

统一使用的端口: 47990

mac 的远程唤醒, 除非是使用向日葵那种高权限的. 不然不好搞的样子. remote wake up 都是要钱的.

要么就是使用向日葵这种公司禁止的软件算了.

## FAQ

### MacOS 运行 Zerotier 后提示:Waiting For Service

1. 搜索打开 “终端”或“Terminal”，在其中输入以下语句：

```shell
cd /Library/Application\ Support/ZeroTier/One

sudo ./zerotier-one -d
```

2. 提示 Password，此时输入解锁密码并回车确认。由于输入内容不会以 * 号方式展示，所以大家输错的话重新试几次就可以。
3. 输入成功后，应该不显示任何内容，如下图所示：
4. 此时在右上角选择 Zerotier 服务，即可正常加入网络并进行使用。
