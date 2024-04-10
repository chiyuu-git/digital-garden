---
{"dg-publish":true,"permalink":"/programming/ai-generator/stable-diffusion/"}
---


[高手](https://bytedance.larkoffice.com/docx/NRbGdrkiTo0QqYx8ColcsGcIngh)

cgmi.com

wonderstudio

[CG迷(CGMI.COM)](https://cgmi.com/)

模型安装使用教程： https://www.bilibili.com/read/cv21362202

【模型分享】

常用模型分享下载链接： https://pan.quark.cn/s/32f374eef667

2023 年 4 月模型分享： https://www.bilibili.com/video/BV1em4y1z7Dg

2023 年 5-6 月模型分享： https://www.bilibili.com/video/BV1Nk4y1T7ft

2023 年 7-9 月模型分享： https://www.bilibili.com/video/BV1v8411y7uR

2024 年 1 月模型分享： https://www.bilibili.com/video/BV14a4y127DA

【常用网站】

AI 作图知识库 (教程): https://guide.novelai.dev/

标签超市 (解析组合): https://tags.novelai.dev/

原图提取标签: https://spell.novelai.dev/

【细致教程】

入门参数设置基础：https://guide.novelai.dev/guide/configuration/param-basic

常见安装问题: https://guide.novelai.dev/s/troubleshooting/install

常见跑图问题: https://guide.novelai.dev/s/troubleshooting/generate

怎么写提示词？ https://guide.novelai.dev/advanced/prompt-engineering/

怎么训练模型？ https://guide.novelai.dev/advanced/finetuning/

最新消息: https://guide.novelai.dev/newsfeed

【问题速查】

遇到问题请前往启动器内 - 疑难解答 进行扫描

# Sd 和 Mj 的区别

可以双修, 用 mj 生成, 再导入 sd 精修.

# 秋叶的学习路线

[【AI绘画】从零开始的AI绘画入门教程——魔法导论【持续更新】 - 哔哩哔哩](https://www.bilibili.com/read/cv22159609/?from=articleDetail)

AI 做得到的

通过文字描述凭空生成一张图片（文生图功能），在基础图片上重新绘制或修改一幅图片（图生图功能）

AI 做不到的

AI 是无法做到你给他一张图片就直接会照着画出来无数张这个物体或人物的图片的。

就比如说，我这里有一个立绘，你想让 AI 只根据这一个立绘就完美画出来各种动作角度，抱歉，做不到或者说是技术限制，目前根本就做不好（这有个专业术语叫 one shot）。想法是美好的，现实是残酷的。目前局限性很大。

AI 经过一定调教可以做到的（训练模型）

继续上文，AI 虽然无法根据单一张图片就完美复刻物体，但是在拥有足够的素材进行训练以后，就可以学习到一个人物或者画师的画风。

通常来说，要拥有较好的效果，人物学习最低需要 10 张以上。画风学习需要 50 张以上。

# Prompt 语法

## 关键词包裹括号

![](/img/user/programming/ai-generator/stable-diffusion/image-20240406162958746.png)

![](/img/user/programming/ai-generator/stable-diffusion/image-20240406163051944.png)

## 尖括号调用 Lora

![](/img/user/programming/ai-generator/stable-diffusion/image-20240406163133281.png)

## 下划线连接词

把多个词视为一个词组来生成图片. 牛奶蛋糕, 而不是一个牛奶和一个蛋糕

## 控制提示词的生效时间

### `[FLOWERS: 0.7]`

![](/img/user/programming/ai-generator/stable-diffusion/image-20240406163656002.png)

花这个关键词只在采样进程的后 30% 生效, 生成的花朵就会比较少

### `[FLOWERS:: 0.7]`

类似的, 参与采样进程的前 70%, 生成的花朵就会更多

### `[STONES:FLOWERS:0.7]`

![](/img/user/programming/ai-generator/stable-diffusion/image-20240406163822655.png)

石头为主, 花朵为辅

### `[RED|BLUE] HAIR`

有人说用斜杠才是正确的. 实际试了一下全是, 正反斜杠的交错效果都比 `|` 要好

![](/img/user/programming/ai-generator/stable-diffusion/image-20240406163903117.png)

## 推荐格式

首行占比最重

### 画质词

![](/img/user/programming/ai-generator/stable-diffusion/image-20240406165730865.png)

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

![](/img/user/programming/ai-generator/stable-diffusion/image-20240406170551867.png)

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

# 采样器

只有少数采样器值得使用.

[第4节：stable diffusion采样器教程详细介绍\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1iZ421z7F9?p=4&vd_source=f8573a6196003ad3683f1c1a403d3431)

## 老派采样器

![](/img/user/programming/ai-generator/stable-diffusion/image-20240407181950507.png)

### A 标识的含义

a 代表祖先采样器, ancestor, 不收 敛

![](/img/user/programming/ai-generator/stable-diffusion/image-20240407181950507.png)

## 2022 年发布的 DPM 算法

### DPM2

画面有提升, 但是时间翻倍. 可以用一代算法加上更多的 step 来代替

### Karras

改进. 在第 8 步之后躁点更少

![](/img/user/programming/ai-generator/stable-diffusion/image-20240407182529915.png)

### 2S / 2M

S 代表单步算法

M 代表多步算法, M 是 S 的升级版本

### 推荐的采样算法

![](/img/user/programming/ai-generator/stable-diffusion/image-20240407182352361.png)

DPM++SDE Karras 可以很好的生成高逼真度的图像. 一般在渲染真实系图片, 最求画质的时候会用这个

Exponential 1.6 版本新增的算法

3M 也是 1.6 版本新增的, 需要更多的采样采样部署, 适当调低一点点 CFG 会有更好的效果

![](/img/user/programming/ai-generator/stable-diffusion/image-20240407182953457.png)

## 2023 年新推出的采样器

![](/img/user/programming/ai-generator/stable-diffusion/image-20240407183046950.png)

![](/img/user/programming/ai-generator/stable-diffusion/image-20240407183141037.png)

# 大模型

sd 的大模型分为了三代, v1, v2, sdxl.

sdxl 对标 midjourney 的全能大模型, 但是细节还是差很多

## Sdxl

![](/img/user/programming/ai-generator/stable-diffusion/image-20240407185133464.png)

![](/img/user/programming/ai-generator/stable-diffusion/image-20240407185204823.png)

![](/img/user/programming/ai-generator/stable-diffusion/image-20240407185107512.png)

### 特点

![](/img/user/programming/ai-generator/stable-diffusion/image-20240407185327242.png)

## 如何升级到 Sdxl

与旧 Lora 不通用.

webUI 版本和插件版本直接在秋月启动器一键更新即可

## Sdxl 模型推荐

[第7节：最新版高清分辨率stablediffusion weibui 1.6版本功能介绍\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1iZ421z7F9?p=7&spm_id_from=pageDriver&vd_source=f8573a6196003ad3683f1c1a403d3431)

## Sdxl Turbo 模型

已开源. 1 步采样, 但是最佳分辨率是 512 512

## Lcm 模型

出图速度超快, 2-4 步出效果

## LCM 采样器

[第13节：LCM模型介绍\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1iZ421z7F9?p=13&spm_id_from=pageDriver&vd_source=f8573a6196003ad3683f1c1a403d3431)

不能加快采样速度

## LCM Lora

能降低采样步数

大批量出图, 再高清化. 结合 animeDiff 效果也很好

# Lora 模型?推荐

二次元模型是 sd 的强项

[第9节：lora模型训练讲解\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1iZ421z7F9?p=9&vd_source=f8573a6196003ad3683f1c1a403d3431)

# 文生图

[【AI绘画】一张图看懂新手入门文生图如何使用 - 哔哩哔哩](https://www.bilibili.com/read/cv22661198/?from=articleDetail)

![](/img/user/programming/ai-generator/stable-diffusion/image-20240405170347406.png)

## 采样步数

采样步数不需要太大，一般在 50 以内。通常 28 是一个不错的值。

采样器没有优劣之分，但是他们速度不同。全看个人喜好。推荐的是图中圈出来的几个，速度效果都不错

## 提示词相关性

提示词相关性代表你输入的 Tag 对画面的引导程度有多大，可以理解为 “越小 AI 越自由发挥”

太大会出现锐化、线条变粗的效果。太小 AI 就自由发挥了，不看 Tag

## 随机种子

随机种子是生成过程中所有随机性的源头 每个种子都是一幅不一样的画。默认的 -1 是代表每次都换一个随机种子。由随机种子，生成了随机的噪声图，再交给 AI 进行画出来。

## 为生成的图片进行放大

AI 基本上无法生成超级大图，想要生成高清图片正确的做法是分辨率调小，比如 512x768，然后开启 “高清修复”。

如果你不会用，照抄我的参数就可以了。放大倍数请考虑自己的显存大小调整，太大会爆显存

![](/img/user/programming/ai-generator/stable-diffusion/image-20240405171852621.png)

按照这样，基本上就已经非常高清了。如果你还想继续放大，可以等到出图后点击这里的发送到后期处理，再选择放大算法进行放大

![](/img/user/programming/ai-generator/stable-diffusion/image-20240405171912780.png)

# 图生图

在某一图片上进行修改（全部修改、部分修改、指定颜色修改）

# 查询图片参数

AI 生成图片会自动保存全部参数到原图中，可以在 WebUI 的 “图片信息” 一栏内通过解析原图查看到。也可以使用我写的这个工具：https://spell.novelai.dev/

非 AI 生成图片或经过压缩的图片推荐使用 WD14Tagger 来尝试反推 tag。

Tagger 是一个插件，在新版的整合包内也帮你装好了，可以在顶栏找到。打开后拖入图片，AI 会识别出一些 tag。如果没有这个插件的话需要你自行安装，安装教程参考下面的插件管理部分。

![](/img/user/programming/ai-generator/stable-diffusion/image-20240405172017092.png)

# 使用 ControlNet 精细控制画面

# 插件

## Latent Couple

我用文生图的时候想要生成一男一女，可结果不是动作反了就是服装反了，更有甚者男的长出胸部来了，请问这种问题 up 是怎么解决的啊

有个插件叫 latent couple，也可以用更高级的 comfyui 自己解决

# 相关概念

## Embeddings

![](/img/user/programming/ai-generator/stable-diffusion/image-20240406154808468.png)

就在整合包的 embeddings 目录. 不太明确具体的定位, 和插件, extension 的区别是什么?

可以在 c 站下载和更新

## Extensions

插件... 都放在这个目录

## Models

大模型, 自然是放在 models 目录

下面细分目录还有

+ hypernetwork
+ lora
+ stable-diffusion 真正放大模型的目录. 都是 safetensor 结尾的
+ vae 调色预设

在 c 站分别选择不同的标签来下载

![](/img/user/programming/ai-generator/stable-diffusion/image-20240406155759013.png)

## Clip Skip

不同的模型, 有推荐的 clip skip. 可以根据需要调节, 原理暂时不明

## Theme

# 修复人脸 / 改变人物表情

[第5节：ADetailer脸部修复插件的使用方法说明\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1iZ421z7F9?p=5&spm_id_from=pageDriver&vd_source=f8573a6196003ad3683f1c1a403d3431)

## 脸崩的原因

面部分配的分辨率不够. 面部修复功能一般, sd 1.6 作者把该功能隐藏了.

采样分配不够

## 方法一: 高清修复

图片在一个较小的合理范围, 通过高清修复, 把图片放大两倍来拉高分辨率.

并不是最优解, 这个画面一起提高分辨率, 脸部区域仅仅获得了少部分的采样提升

## 方法二: 局部重绘

把整个脸部涂抹上. 人脸, 仅蒙板, 会按照重绘区域的分辨率来绘制.

但是不能解决多人画面的问题, 每次只能单独修复一个人脸

 否则每张人脸都会是一样的.

## ADetailer

24 年 3 月的秋叶整合包, 已经包含了类似的功能, 但是不知道具体是哪个插件

感觉是一个局部重绘插件

[第5节：ADetailer脸部修复插件的使用方法说明\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1iZ421z7F9?p=5&vd_source=f8573a6196003ad3683f1c1a403d3431)

# 手指调整和修复

[第10节：stable diffusion手指调整、手指修复的各种方法对比介绍。\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1iZ421z7F9?p=10&vd_source=f8573a6196003ad3683f1c1a403d3431)

## 反向提示词

embeddings.

优点: 方便快捷

缺点: 可控性差, 效果一般, 会改变画面

## controlNet + Openpose

## 局部重绘 +controlerNet + Openpose

# 如何生成高质量图片

[第8节：如何生成细节度拉满的高质感画面，最终生成8K超高分辨率的图片小技巧分享\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1iZ421z7F9?p=8&spm_id_from=pageDriver&vd_source=f8573a6196003ad3683f1c1a403d3431)

# AnimeDiff

## Prompt-tarvel

![](/img/user/programming/ai-generator/stable-diffusion/image-20240408183849502.png)

## 不同版本的区别

![](/img/user/programming/ai-generator/stable-diffusion/image-20240408184101784.png)

# FAQ

## 如何生成一个角色的同人图, 没有 Lora?

先用一张已有的图片, 反推 tag. 然后继续使用图生图, 基本上就能生成一些差不多的图片了. 然后抽一抽卡, 就可能会出现满意的.

搜集足够多的照片, 然后训练自己的 lora.

## 如何生成同一个角色, 不同服装, 不同动作的图片

## 如何生成多个角色在同一个场景的图片? 如何组合他们?
