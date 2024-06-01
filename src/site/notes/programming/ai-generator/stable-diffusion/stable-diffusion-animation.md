---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2024-05-31-Fri, 3:52:03 pm","date-modified":"2024-06-01-Sat, 9:29:08 am","permalink":"/programming/ai-generator/stable-diffusion/stable-diffusion-animation/","dgPassFrontmatter":true}
---


[请问有什么软件能够AI自动生成视频？ - 知乎](https://www.zhihu.com/question/634639314/answer/3470548197)

# Mov2Mov

23 年 7 月之前就已经有了

# SVD

[重视视角的重要性，才能生成效果炸裂的svd视频。The importance of perspective for SVD 1.1 - YouTube](https://www.youtube.com/watch?app=desktop&v=rWiRU1oFKGg)

显存要求太高啦. 基本上需要 16 GB. 本地跑不动. 只能用 esheep 白嫖一些了. 不然就只能等以后云部署熟练了再尝试了

[Stable Video | Generate Videos with AI](https://www.stablevideo.com/login?create=true) svd 官网每天白嫖

不可控

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-animation/image-20240531183337143.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-animation/image-20240531175350244.png)

## 配置要求

绘制 14 帧的视频显存需求在 12 GB 左右. 想要生成 25 帧的视频, 显存要求就更高了

基本上本地玩需要 16 GB 显卡

## 原理

给 ai 大量投喂视频片段, 让 ai 学习这些视频在不同时间节点上的静态帧的差异, 让动态在 ai 眼里有迹可循. 让 ai 有能力去预测这张静态的图片在接下来的时间里会发生的运动

## 图生视频

是 SVD 最基本的操作. 让图片动起来

![](stable-diffusion-animation/SVD：图生视频.json)

初始图片的尺寸比例最好和最后生成的视频的比例保持一致

视频尺寸默认的 1024 * 576 是 SVD 训练素材的尺寸

## 视频参数

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-animation/image-20240531191317011.png)

### motion_bucket_id

最直观的控制视频运动幅度的参数, 默认 127. 取值从 1 到 255.

如果运动幅度过大导致画面变形了, 可以考虑降低运动桶 id 的值

### Cfg

classifier free guidance

和运动幅度间接相关的一个参数.

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-animation/image-20240531191549884.png)

有一个 min_cfg 和一个在 KSampler 里的 cfg, 是因为 SVD 采用了随帧数动态控制 CFG 的思路.

在绘制第一帧的时候应用最小值, 然后逐渐增大, 到最后一帧时变成最终的 cfg 数值

官方解释作用是保持原始图像的忠实程度, 低了画面会更自由, 高了画面会更稳定.

nenly 实践发现 CFG 不会影响运动的构成, 但是会影响细节. 如果发现画面糊成一团, 可以适当增大 cfg 的数值. min_cfg 和 最终 cfg 都可以增大试试, 抽抽卡

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-animation/image-20240531192124705.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-animation/image-20240531192229013.png)

> [!NOTE] CFG
> 数值相比静态图片的 CFG 7 要低很多. 算是一种实践吧, 太高了生成视频会有很多**伪影**

尽量还是维持在正常的区间. 比如默认值的区间

### augmentation_level

噪声增强水平, 添加到图片中的噪声量, 它越高, 视频与初始帧的差异就越大, 可以通过增加该值来获得更多的运动.

该参数对数值的调整很敏感, unit 0.01. 一般不会超过 1.

但是当生成的视频尺寸与标准训练尺寸不同的时候, 可以尝试增大到 0.2 ~ 0.3 来避免视频错乱

## 文生图生视频 = 文生视频

![](stable-diffusion-animation/SVD：文生视频.json)

## 结合蒙版指定运动区域

运动笔刷, 动态笔刷

![](stable-diffusion-animation/SVD：运动笔刷%20.json)

![](stable-diffusion-animation/SVD：智能选取%20.json)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-animation/image-20240531194301180.png)

利用 prompt 智能选区物体创建蒙版, 然后对蒙版区域进行运动指令. 如果扣取的区域太小, 是不会有动态效果的. 而如果扣取的是人像, 则因为区域限得太死, 导致一些奇怪的运动. 需要降低几个运动参数的幅度再反复抽卡试试

## 缺点

可控性太差 - 结合蒙版

现阶段无法控制 SVD 生成的视频是如何运动

生成的视频帧率和分辨率都一般. 帧率默认只有 6, 最佳尺寸只有 1024 * 576 - 结合补帧放大工作流

纯靠抽卡, 比较适合一些物件的运镜视频, 或者变化幅度不大的人像

## 抽卡技巧

当我们的画面里有比较突出的人物主体, 比较显著的动态元素乃至比较有空间层次感的场景时, 产出自然流畅的运动的概率就会高很多.

生成一些静物, 远景, 出好卡的概率更高.

细致的动作就不太行

# FAQ

## SVD 和 animeDiff 哪个好?

几乎是同一时间出现的. 支持的能力也有差异. 与其分个高低, 不如都试试, 探索合适的场景, 甚至是互相搭配使用, 取长补短
