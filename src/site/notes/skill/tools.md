---
{"aliases":["实用工具","实用软件"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-09-20-Tue, 3:08:11 pm","date-modified":"2024-06-20-Thu, 10:42:51 am","permalink":"/skill/tools/","dgPassFrontmatter":true}
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
