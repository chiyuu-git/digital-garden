---
{"dg-publish":true,"permalink":"/programming/ai-generator/stable-diffusion/stable-diffusion-basic/"}
---


[eSheep(内测中) - 一站式的AIGC社区](https://www.esheep.com/app)

[CG迷(CGMI.COM)](https://cgmi.com/)

[飞书云文档](https://waytoagi.feishu.cn/wiki/NuRCwmGfyig1QWk4VNmcH0NcnUc)

模型安装使用教程： https://www.bilibili.com/read/cv21362202

【常用网站】

AI 作图知识库 (教程): https://guide.novelai.dev/

【问题速查】

遇到问题请前往启动器内 - 疑难解答 进行扫描

# Sd 和 Mj 的区别

可以双修, 用 mj 生成, 再导入 sd 精修.

不过 SD 胜在免费 和其精准的控制

# 学习路线

## 最好的入门教程

[B站第一套系统的AI绘画课！零基础学会Stable Diffusion，这绝对是你看过的最容易上手的AI绘画教程 | SD WebUI 保姆级攻略\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1As4y127HW/?spm_id_from=333.337.search-card.all.click&vd_source=f8573a6196003ad3683f1c1a403d3431)

其他教程文档都没有必要看了. 有需要的时候直接带着问题找答案就行

## 秋叶

[【AI绘画】从零开始的AI绘画入门教程——魔法导论【持续更新】 - 哔哩哔哩](https://www.bilibili.com/read/cv22159609/?from=articleDetail)

【细致教程】

入门参数设置基础： https://guide.novelai.dev/guide/configuration/param-basic

常见安装问题: https://guide.novelai.dev/s/troubleshooting/install

常见跑图问题: https://guide.novelai.dev/s/troubleshooting/generate

怎么写提示词？ https://guide.novelai.dev/advanced/prompt-engineering/

怎么训练模型？ https://guide.novelai.dev/advanced/finetuning/

最新消息: https://guide.novelai.dev/newsfeed

# Prompt

## Prompt 语法

### 括号

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240406162958746.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240406163051944.png)

### 尖括号调用 Lora

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240406163133281.png)

### 下划线连接词

把多个词视为一个词组来生成图片. 牛奶蛋糕, 而不是一个牛奶和一个蛋糕

### 控制提示词的生效时间

#### `[FLOWERS: 0.7]`

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240406163656002.png)

花这个关键词只在采样进程的**后** 30% 生效, 生成的花朵就会比较少

#### `[FLOWERS:: 0.7]`

类似的, 参与采样进程的**前** 70%, 生成的花朵就会更多

#### `[STONES:FLOWERS:0.7]`

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240406163822655.png)

石头为主, 花朵为辅

#### `[RED|BLUE] HAIR`

有人说用斜杠才是正确的. 实际试了一下全是, 正反斜杠的交错效果都比 `|` 要好

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240406163903117.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240528103225267.png)

## 推荐格式

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240528103151061.png)

首行占比最重

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240406170551867.png)

### 画质词

```
画质词:
(masterpiece:1.2),best quality,
highres,extremely detailed CG,
perfect lighting,8k,wallpaper,
真实系:
photograph,photorealistic,
插画风:
illustration,painting,paintbrush,
二次元:
anime, comie, game CG,
三维场景:
3D,C4D render,
unreal engine,octane render,
```

### 画风词

```
赛博朋克:
Cybderpubk
像素风:
8bit pixel
16bit pixel
宫崎骏风格:
studio ghibli
皮克斯风格:
pixel style
水墨画:
Chinese link style
```

### 画面主体描述

第二行

根据实际需求填写即可.

人物、年龄、发型，头发颜色，

情绪表情，衣服裝束，正在于什么

### 环境, 场景, 灯光, 构图

第三行

人物特写镜头

### Lora

最后一行

### 负面提示词

```
lowres, bad anatomy, bad hands, text, error, missing fingers, extra digit, fewer digits, cropped, worst quality, low quality, normal quality, jpeg artifacts, signature, watermark, username, blurry
```

## Prompt 相关的辅助

### 自动补全 Tag 的插件

整合包内一般都会带一个自动补全 Tag 的插件

### 标签超市

如果你不知道那些 Tag 好，可以使用标签超市 

https://tags.novelai.dev/

### Prompt All in One

整合包中已经自动安装了. 可以翻译. 可以连接 gpt

### Dynamic Prompt

主打一个组合词, 批量生成.

另一个就是魔法 prompt, 疯狂抽卡了

### *One Buttom Prompt

提供一些预设的插件, 但是实际使用起来, 有点垃圾. 还不如直接使用 sd 生成的

只能作为提示词参考了...

## Prompt 示例

保存几张生成好的图片 天然就是 prompt 示例了

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/90832dcf5cbe2a88815cba6f54569ae7539e8d601af6596779680e9cd3a34c91.png)

```text
1girl,outdoors,skirt,solo,hair ornament,shirt,long hair,hair flower,blonde hair,flower,yellow skirt,smile,white shirt,short sleeves,looking at viewer,bangs,pleated skirt,holding,open mouth,tree,day,blush,cowboy shot,breasts,collarbone,:d,standing,shirt tucked in,braid,white flower,purple eyes,bush,outstretched arms,blurry,hair intakes,outstretched arm,sky,paintbrush,blurry background,blue eyes,arms up,medium breasts,teeth,  
Negative prompt: NSFW,(worst quality:2),(low quality:2),(normal quality:2),lowres,normal quality,((grayscale)),skin spots,acnes,skin blemishes,age spot,(ugly:1.331),(duplicate:1.331),(morbid:1.21),(mutilated:1.21),(tranny:1.331),mutated hands,(poorly drawn hands:1.5),blurry,(bad anatomy:1.21),(bad proportions:1.331),extra limbs,(disfigured:1.331),(missing arms:1.331),(extra legs:1.331),(fused fingers:1.61051),(too many fingers:1.61051),(unclear eyes:1.331),lowers,bad hands,missing fingers,extra digit,bad hands,missing fingers,(((extra arms and legs))),  
Steps: 20, Sampler: DPM++ 2M Karras, CFG scale: 7.0, Seed: 1474817915, Size: 512x512, Model: 192_3750_4424@7c819b6d13, Version: 1.8.0-RC
```

# 文生图

[【AI绘画】一张图看懂新手入门文生图如何使用 - 哔哩哔哩](https://www.bilibili.com/read/cv22661198/?from=articleDetail)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240405170347406.png)

## 采样步数

采样步数不需要太大，一般在 50 以内。通常 28 是一个不错的值。

采样器没有优劣之分，但是他们速度不同。全看个人喜好。推荐的是图中圈出来的几个，速度效果都不错

## 提示词相关性

提示词相关性代表你输入的 Tag 对画面的引导程度有多大，可以理解为 “越小 AI 越自由发挥”

太大会出现锐化、线条变粗的效果。太小 AI 就自由发挥了，不看 Tag

## 随机种子

随机种子是生成过程中所有随机性的源头 每个种子都是一幅不一样的画。默认的 -1 是代表每次都换一个随机种子。由随机种子，生成了随机的噪声图，再交给 AI 进行画出来。

**通过同一个种子, 不同的提示词来不断改善, 逐步得到自己想要的图像**

## Clip Skip

不同的模型, 有推荐的 clip skip. 可以根据需要调节, 原理暂时不明

# 图生图

在某一图片上进行修改（全部修改、部分修改、指定颜色修改）.

使用的表单与文生图基本相同, 只是多了一个输入图片的地方

## 参考图 Prompt 反推

图生图旁边的按钮有两种反推, 但是效果不如 Tragger

[查询图片参数](stable-diffusion-basic.md#查询图片参数)

![|300](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240528110409078.jpeg)

![|300](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240528110413594.png)

## 灵魂画手, 利用图生图优化

[controlNet Model](stable-diffusion-basic.md#controlNet%20Model)

[inpaint sketch](programming/ai-generator/stable-diffusion/stable-diffusion-basic.md#Sketch%20局部重绘%20手涂蒙版%20涂鸦重绘)

# 查询图片参数

AI 生成图片会自动保存全部参数到原图中，可以在 WebUI 的 “图片信息” 一栏内通过解析原图查看到。也可以使用我写的这个工具：https://spell.novelai.dev/

非 AI 生成图片或经过压缩的图片推荐使用 WD14Tagger 来尝试反推 tag。

Tagger 是一个插件，在新版的整合包内也帮你装好了，可以在顶栏找到。打开后拖入图片，AI 会识别出一些 tag。如果没有这个插件的话需要你自行安装，安装教程参考下面的插件管理部分。

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240405172017092.png)

# 局部重绘

![inpaint](work-flow-unit/inpaint.md)

# 图片超分

![upscale](work-flow-unit/upscale.md)

[使用 ControlNet 精细控制画面](使用%20ControlNet%20精细控制画面.md)# 一致性控制大法

[【AI绘画】SD角色一致性控制大法，只要一张参考图，无限生成统一风格角色，再也不用担心崩脸！AI绘画 SD教程 SD安装\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1Sx4y1Y7CV/?spm_id_from=333.1007.tianma.1-1-1.click&vd_source=f8573a6196003ad3683f1c1a403d3431)

# ControlNet

![control-net](work-flow-unit/control-net.md)

# AnimeDiff

## Prompt-tarvel

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240408183849502.png)

## 不同版本的区别

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240408184101784.png)

# FAQ

## 如何生成一个角色的同人图, 没有 Lora?

先用一张已有的图片, 反推 tag. 然后继续使用图生图, 基本上就能生成一些差不多的图片了. 然后抽一抽卡, 就可能会出现满意的.

搜集足够多的照片, 然后训练自己的 lora.

## 如何生成同一个角色, 不同服装, 不同动作的图片

## 如何生成多个角色在同一个场景的图片? 如何组合他们?

![stable-diffusion-term](stable-diffusion-term.md#Additional%20Network%20扩展)

## 人物不变, 只改背景

固定 [随机种子](stable-diffusion-basic.md#随机种子), 不断修改背景的 prompt 即可

## 基本工作流: 小图抽卡, 然后再放大

## 模型的缩略图

在模型旁边放一张同名的图片文件即可

## 高清修复, 局部重绘, controlNet, 文生图, 图生图之间的关系

待梳理, 总感觉有一些大一统理论的操作空间

首先, 文生图也是图生图.

高清修复的修复人脸还是去掉吧, 扔到局部重绘里去. 常说的高清修复更加倾向于将低分辨率的图片放大之后, 锐化之类的. 而不是企图通过高清修复去修复人脸. 虽然原理上说得通, 但是起不到主要作用

局部重绘就是局部重绘

controlNet 更加贴近 lora 的概念, 与局部重绘是正交的

# 目前适合用来做什么, 不适合用来做什么

## AI 做得到的

通过文字描述凭空生成一张图片（文生图功能），在基础图片上重新绘制或修改一幅图片（图生图功能）

## AI 做不到的

AI 是无法做到你给他一张图片就直接会照着画出来无数张这个物体或人物的图片的。

就比如说，我这里有一个立绘，你想让 AI 只根据这一个立绘就完美画出来各种动作角度，抱歉，做不到或者说是技术限制，目前根本就做不好（这有个专业术语叫 one shot）。想法是美好的，现实是残酷的。目前局限性很大。

## AI 经过一定调教可以做到的（训练模型）

继续上文，AI 虽然无法根据单一张图片就完美复刻物体，但是在拥有足够的素材进行训练以后，就可以学习到一个人物或者画师的画风。

通常来说，要拥有较好的效果，人物学习最低需要 10 张以上。画风学习需要 50 张以上。

## 视频封面, 视觉图

主要还是生图

[三视图](stable-diffusion-term.md#三视图)

## 如何获取模型

[https://huggingface.co/models](https://huggingface.co/models) 类似于 npm, github

深度学习和人工智能网站，全世界 AI 研究与模型共享的前沿阵地，但专业性较强，检索起来不是很直观；

[https://civitai.com/](https://civitai.com/)

全世界最受欢迎的 AI 绘画模型分享网站，除了模型还有很多优秀作品展示

+ trained 一手训练模型
+ merge 合并模型, 通常叫做 mix

## 应用重点之一: 人物设计, 三视图, 服饰 Lora

## 适合二次元场景

因为 controlNet 的大佬, 是一个二次元, 专门实现了 Lineart 用于处理二次元线稿上色

二次元天然的对角色立绘, 角色同人图有需求.

在开源生态上更加活跃

## 配合 Ps 成为了生产力

[stable-diffusion-term](programming/ai-generator/stable-diffusion/stable-diffusion-term.md#Auto%20Photoshop%20Stable%20Diffusion%20Plugin)

## 在提高生产力的道路上狂奔的 Stable Diffusion 开源社区

说明早期的 ai 生图用户, 生产力导向是很明确的. 这部分人也都有能力去学习和掌握 stable diffusion 以及各种进阶的用法, 来提高自己的生产力. 不然就会被时代的洪流淘汰了

趋势应该是超分默认化, adtailer 默认化, 然后 controlNet 规范化, inPaint 规范化, 再与 ps 深度结合

1. controlNet
2. ps script
3. layerDiffusion

# Dreamina 最大的局限性就是政治风险...

ai 生成的内容太不可控了. 这也导致 ai 生成在国内很难发挥...
