---
{"dg-publish":true,"permalink":"/skill/knowledge-management/"}
---


# 闪念

就保存在手机的备忘录就行, 也没有必要发送到如流了, 统一保存在备忘录, 如果是手机端刷到的网页, 就保留 url

# 领域知识管理

对于特定领域知识的管理其实很简单的，就像编程，不断的提升 common 就行，领域知识更像是技能，切实的掌握之后，索引起来会非常迅速。

很多时候使用目录形式就可以管理了，加上 obsidian 双链，基本上就问题不大了。

思维导图进阶版：氢图，可以无限嵌套的，就是我心中所想的形态，语雀的思维图

## Programming Experience Management Methodology

头部是 经验 section, programming experience 通过双链汇总这篇文章的所有经验性的总结，包括但不限于：

+ 需要注意的坑、注意事项
+ xx 使用经验、总结
+ 面试中可能出现的要点

中间是正文，正常的深度知识管理

最尾部是 faq section，总结面试中常见的问题、工具函数、常见的组合、用法

应用实例：[es-array](../programming/font-end/primitive/es/es-array.md)

现在我的 array 文章是总结在 js 的目录下，但是其实 array 就和算法一样，是编程语言的比较通用的结构，可以考虑把算法中的数组相关的题目和 js 的 array 的相关的知识都放在一起

# 媒体 / 素材管理

最难管理的知识是：音乐、电影、小说这种复杂的载体。

一个优秀的电影，剧情、人物、音乐、美术风格都会有自己的亮点，而故事音乐人物又会有不同的特征、标签，很难进行管理。

所以需要二维、多维的管理模型：只需要 metaInfo 和 tag 其实就够用了，metaInfo 保存文件本身的属性，比如音乐文件的作者、时间信息等固有属性的，最好是能自动生成不用我手动维护。我手动维护的就是 tag

metaInfo 和 tag 都不应该以文件夹的形式去组织，暂时考虑都用 obsidian 去维护，完善 book note 和 video note 即可，音乐和图片再考虑其他办法。

## 媒体形式

### 游戏（剧情、人物、音乐）

### 电影（剧情、人物、音乐）

### 小说（剧情、人物）

### 音乐

罐头音乐

### 美术风格

包括但不限于：摄影风格、服化道、插画、CG 等等

但是美术风格一般是服务于剧情和人物的？音乐也是一样？

### 背景设定

如科幻、异能、写实等

### 单独抽取剧情，统一分析

剧情有不同的风格，32 种剧情

### 单独抽取音乐，统一分析

音乐有不同的风格，甚至不同乐器同一个风格，也是有区别的

### 单独抽取人物，统一分析

人物也有不同的风格，也就是人设和属性

## 管理形式

豆瓣

everything 看起来只是一个搜索工具，没有提供元数据的功能，可以试试看看样子，进阶版的 Listary

FileLocator Lite：可以对文档的内容进行搜索

Mac 平台自带的全局搜索功能「Spotlight（聚焦）」已经足够好用，按下 Command + [空格键](https://www.zhihu.com/search?q=%E7%A9%BA%E6%A0%BC%E9%94%AE&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A1157564935%7D) 即可呼出。

fences 分栏管理工具，可以把文件像看版视图一样，拖拽来管理，特别适用于处理短期产生的大量文件、处理中、未处理、处理完成等 Coodesker

totalcmd：批量重命名

QTTabBar 提供 tabBar

日期命名法：比较脑残，明明是可以自动生成的元数据类型，却需要手动维护

eagle 图片管理工具

billfish 多媒体素材库

# 卢曼卡片盒笔记法

灵感笔记、文献笔记、永久笔记

# Book Management

pdf 阅读器、纯文本、markdown 所有的电子书形式都要可以索引，不要做无意义的摘抄，直接引用，要能返回原文的那种

搭建个人书库: https://www.bilibili.com/video/BV1YT4y167dL?spm_id_from=333.999.0.0&vd_source=f8573a6196003ad3683f1c1a403d3431

[爆肝100小时，全网最易懂的Logseq指南！双向链接笔记软件科普_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1DU4y1g7Bf/?spm_id_from=333.337.search-card.all.click&vd_source=f8573a6196003ad3683f1c1a403d3431)

要建立从笔记到原文的引用

## 读书笔记

其实可以直接用 logseq 那种原生支持作读书笔记的, 只要产出的都是 markdown , 应该问题不大

## 电子书

电子书资源：https://www.zhihu.com/question/460822842/answer/2395045519

https://search.zhelper.net/

https://docs.zhelper.net/search/

### 微信读书

有很多插件可以用

### Pdf

pdf 是一个比较特殊的存在。他压根就不是为电纸书存在的格式，它是为了纸质书存在的。从他出身的地方就知道，“印刷前置文本”。PDF 的各种特性放在电纸书中，放在电脑、手机、平板上，就是反人类的存在。不可以更改、不可以改变排版方式、不可以改变字体等等。这些都是因为，PDF 是图书印刷出版的前置格式，本来就是为了防止修改，防止不同系统、不同电脑、不同印刷设备而导致印刷变形的一种强制措施。

如果不是因为大量的印刷书籍都只有 PDF 格式，估计随着 [交互式文档](https://www.zhihu.com/search?q=%E4%BA%A4%E4%BA%92%E5%BC%8F%E6%96%87%E6%A1%A3&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A1794518104%7D) 渐渐兴起，PDF 早就开始没落了吧

[【明狗娃】近乎完美的PDF转WORD方法，不花钱就能搞定！_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1HV411v75e/?spm_id_from=333.337.search-card.all.click&vd_source=f8573a6196003ad3683f1c1a403d3431)

### Epub

epub 是个好东西，也是现在最为主流的电纸书格式。为什么，两个原因，一是开源，二是内容丰富。除了文本、图片、目录、框架之外，还可以至此音频、视频，甚至新版的还至此部分交互式内容了。

### Epub 2 Docx

EPUB 转 Docx：EPUB 轉 DOC (WORD) 轉換器。在线自由 - Convertio

### Docx 2 Md

1 ， 安装软件

官网：Pandoc - Installing pandoc

2，打开 cmd，切换到 word 文件所在的目录

这个不会可以自行百度不难。。。

3，在根文件目录下输入如下这行代码

pandoc -f docx -t markdown -o output.md input.docx

-f docx：指定源文件为 docx 格式（from）

-t markdown：指定我们要转为 md 格式（to）

-o output.md：表示输出的文件名为 output.md（output）

input.docx：表示要转换的文件为 input.docx

## Annotation

在阅读模式下才能获得较好的观看体验

annotation-target 路径发生了变动时, 不好调整, 最好是不要用本地路径了

[Johnny学OB 第13集 - 在OB里直接标注PDF，可以高亮，标注，加页面备注，当然也可以加标签。大大提高用PDF资料进行学习的体验。Obsidian教程_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1YQ4y1C7Fy/?spm_id_from=333.337.search-card.all.click&vd_source=f8573a6196003ad3683f1c1a403d3431)

## Zotero

Z 本身就是开源软件, 也是科研界实用软件, 作为读书笔记 pdf, 标记的首选, 并无问题

zotero 本身可以导出 md 标记 [一键提取包含 pdf 页码的 pdf 笔记 | 笔记神器 Obsidian 完全指南_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1Sh411v7XQ/?spm_id_from=333.999.0.0&vd_source=f8573a6196003ad3683f1c1a403d3431)

[科研文献阅读——Zotero和Obsidian联动最优解决方案：Better BibTex+BibNotes Formatter+Zotero Obsidian Citations - 知乎](https://zhuanlan.zhihu.com/p/480911605)

[Obsidian插件-Obsidian-Zotero-Integration - 知乎](https://zhuanlan.zhihu.com/p/553864286)

[如何用obsidian+zotero搞科研？ - 知乎](https://zhuanlan.zhihu.com/p/489713119)

# Target Management

## 方法总览

### 自上而下执行 (OKRT)

objective: Ambitious qualitative goals - typically with an achievement rate around 70%

key results: Measurable progress indicators, with two five per Objective.

frequency: Usually set quarterly, with three per terms.

长期目标管理通过 OKR 进行任务拆解，拆分到每个季度的 OKR 当中

每个 O 都有若干个 KR，KR 下还可以拆解子 KR 或者是 Task

每个 Task 都通过番茄钟记录消耗的人力

宏观微观转换法：这种方法做起来也很简单，我们在思考一些宏观问题的时候往往会感觉到紧张，比如你在给一个学期做学习计划的时候，你会感觉到紧张，这是因为你想到一个学习要完成那么多事情，压力自然就产生了。为了减少紧张，我们可以将宏观的事情微观化，不要做整个学期的计划了，做一个星期的计划，做完一个星期的计划以后，就去执行，一个星期以后，完成这个计划。然后再安排一个星期的计划。这样就是一个微观化的过程。

### 自下而上复盘 (TKRO)

every single promodoro has the same point

what I need to review when I complete a tasks ?

+ It's the cost match the project ? over or less ? and why
+ from small task to huge project both suit for this review rule
+ record working emotion, status, 专注程度等个人状态的信息

KR review auto associate with task review、task cost

O review auto associate with KR review、KR cost

### Day Management

合理的计算 Task 的优先级，展示在 roadMap、kanban、board 等视图中。

+ 是否要限制 roadMap 中只允许出现 Task ？限制
+ roadMap 是 day management 最重要的组成部分，只聚焦一天内 / 短时间内需要完成的任务即可
+ 运用四象限工作法，等 kanban 空了，就去 OKR 找 Task

一些浅层的知识点直接记录在 2022-Q3 文档里，深度总结的内容再整合到 ob

vision 上只记录心情、拍照留念、日记等就可以了（方便做时光机），没有必要关联 task 和笔记。vision 只用来记录效率即可，深度知识可以在脑内和 graph view 完成索引无需，关联具体的 task okr 啥的意义不大。

参考 2022-Q3 一些零散的片段是没有意义的，只有被整合成深度的只是才会有意义。

增加每日提醒和 simple todo，提醒一些原则性、规则性的观点，增加一个 simple todo，也可以用非关联 Task 解决

### Tag System

标签选择、标签管理界面、多级标签、OKRT 标签继承父级

标签合并功能，方便整合相似的标签

### Achievement

10000 hours achievement

+ 依赖 rescueTime like 做全局的统计、离线统计，记录的是表层时间
+ 依赖 Task 和番茄钟的精确统计，记录的是深度工作的时间
+ 番茄钟和 rescueTime 结合可以产出深度专注值等数据
+ 依赖标签系统自动建立联系
+ 允许手动建立联系
+ [【塞尔达】7小时萌新和700小时老流氓之间的亿点区别~第五期！_哔哩哔哩bilibili_塞尔达传说](https://www.bilibili.com/video/BV1US4y1s7aG?spm_id_from=333.1007.tianma.1-1-1.click&vd_source=f8573a6196003ad3683f1c1a403d3431)

### 番茄币

每完成一个番茄钟，就可以获得一枚番茄币，番茄币可以在游戏商场购买虚拟物品 / 现实物品

### Time Machine

 ![Time Machine](project-idea.md#Time%20Machine)

## Pomodoro Technique

I can take balance between focus work and rest through this technique.

It is not important that just focus on one thing during pomodoro period.

Focusing on what kind of thing isn't important either. As long as I log all my time by rescueTime.

All the productivity time are focusing work time

## rescueTime

### Skill and App 1 : 1 / 1:n

skill that binding unique app

 rescueTime can fully cover unique app. such as monster hunter world.

	1. as long as I using the app, I must push myself to use pomodoro technique.

	2. once using time of the unique app reach 10000 hours, I can declare that I master such skill.

rescueTime combine multi app that relate to the skill can handle 1 : n case

### Skill and App N : 1 / N : N

such as common IDE, vscode can satisfy JS、NodeJS、C++ and so on

I can use unique IDE, such as InJ IDEA VS

> if a skill do not has strong relative app, it is truly need to master ?

aggregation website , such as bilibili and zhihu, I will using it to take entertainment or learning . this kind of website has a strong feature, fragmented. It is hard to tell what you are doing.

# 碎片知识 & Todo 知识管理

平时逛知乎的时候, 刷到的技术文章, 或者搜索问题遗留下来的深入文章, 需要有一个地方统一管理, 方便无遗漏的回顾, 目前就是 notion 的 todo list, 先把知乎的清空了. 然后后续有新增的都统一放到 notion 上

如果是可以归类的 todo, 就放到各个文档下面吧, 想要提升的时候, 自然会打开这个文档, 打开之后自然就可以看见了.

如果是一些总结的很完善的文章, 直接吸纳, 如果是一些意见性质, todo 性质的就添加到 todo

# Spaced-reprtition
