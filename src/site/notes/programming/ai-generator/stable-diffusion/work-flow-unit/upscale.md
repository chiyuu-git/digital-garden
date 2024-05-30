---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2024-05-28-Tue, 5:38:48 pm","date-modified":"2024-05-29-Wed, 5:46:12 pm","permalink":"/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/","dgPassFrontmatter":true}
---


2K 4K 6K 8K 等

[第8节：如何生成细节度拉满的高质感画面，最终生成8K超高分辨率的图片小技巧分享\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1iZ421z7F9?p=8&spm_id_from=pageDriver&vd_source=f8573a6196003ad3683f1c1a403d3431)

# 直接生成大分辨率图片

分辨率过高容易出现多头, 多手, 多脚的问题. 因为模型训练的时候使用的图片分辨率比较低. 所以当想要输出大分辨率的图片的时候, 模型会认为需要对图像进行拼接, 导致出现了这种情况

容易暴显存

# 文生图 - 高分辨率修复

AI 基本上无法生成超级大图，想要生成高清图片正确的做法是分辨率调小，比如 512x768，然后开启 “高清修复”。

如果你不会用，照抄我的参数就可以了。放大倍数请考虑自己的显存大小调整，太大会爆显存

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240405171852621.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240525205424456.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/90832dcf5cbe2a88815cba6f54569ae7539e8d601af6596779680e9cd3a34c91.png)

## 本质

先生成一张低分辨率的图片, 然后再进行图生图

重绘算法影响的就是图生图阶段的操作

## 重绘算法

具体用哪种, 看模型介绍里作者有没有推荐. 如果没有, 可以用 R-ESRGAN 系列的, 分别对应真人和动画风格

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240525205649789.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240525205847912.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240525205909383.png)

# 图生图 - 高清修复

没有默认给出的高清修复选项, 因为重本质上来说, 图生图就是一种高清修复

在设置 - 放大 可以定义图生图的放大算法, 选项就是和文生图的高清修复一样的

# ~~SD Upscale 脚本 (deprecated)~~

更大, 更精细

肯定是和他的原理有关. 不是模型层的?

确实是一个脚本, 因为他的原理是分块处理. 一块块的重绘再拼接到一起. 拉大缓冲区, 减小分块的边缘生硬感

用类似的方式, 可以把图片整得非常大, 但是不会明显的增加显存的压力

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240528112641339.png)

## 缺点

裁切成小块之后, ai 很难让 prompt 自动对号入座, 很容易让画面变得混乱

解决方法就是降低重绘幅度, 并且增大缓冲区尺寸

## 已废弃

推荐使用 tile 系列. 不能说原理是一致的, 只能说思路是一致的. 都是分块处理再拼接, 但是后来者算法更好, 效果更强大

# 附加功能放大

在单独的 tab 里. 一般用于图片生成之后的处理上, 就像是一个重绘幅度为 0 的高清修复. 更像是传统的 ai 算法做的高清修复. 在 diffusion 出来之前, 就已经有好多这种的了, 不涉及 diffusion, 速度很快

优势在于简单方便, 负担小.

传统的放大算法

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240528112657890.png)

# Tiled Diffusion

tiled 瓷砖, 平铺. 有点点像 up scaler 拆成一块块单独处理, 但是效果会比 up scaler 更好

## 基本原理

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240527174625631.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240527174743592.png)

## 基本使用

高清化对真实系照片非常重要, 因为只要足够多的细节, 才能体验出真实性. 相反二次元要求就没有那么高
{ #fdpum6}


tiled diffusion 的配置就是用默认的就行

但是图生图的重绘幅度需要调整得低一点

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240527175302721.png)

## 优点

产出同样分辨率的图, 显存占用更小, 生成速度更快

2023-06-13 最佳的放大方案

## 进阶参数

### VAE

分快大小是降低显存的关键所在. 更多可以参考 github 介绍. 如果变灰了可以开启

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240527175554746.png)

### Diffusion

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240527175649565.png)

如果完全没有重叠, 接缝感会很强

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240527182046664.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240527182125306.png)

## 超高清&细节拉满的放大实践

使用高清修复, 现将图片的细节拉到 1080P 水平

然后再使用 tile 系列

[Multi Diffusion + Tiled VAE + ControlNet Tile模型，低显存打造AI绘画超高清6K分辨率体验！SD扩展插件教程\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1Su4y1d7Dp/?spm_id_from=333.788&vd_source=f8573a6196003ad3683f1c1a403d3431)

## 与 ControlNet Tile 结合使用

因为分区块绘制不是那么容易控制细节元素. 很可能会多人物.

controleNet Tile 在讲大图分块的过程中, 会主动去识别每一张图片的内容. 如果识别出来的内容和全局提示词不匹配, 就会减少全局提示词的影响

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240527183105001.png)

生成时间会增常, tile 可以让 controlNet 去 CPU 运算

## 分区提示词
