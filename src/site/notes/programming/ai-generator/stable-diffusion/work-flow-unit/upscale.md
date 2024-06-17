---
{"aliases":["高清修复","细节增强"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2024-05-28-Tue, 5:38:48 pm","date-modified":"2024-06-11-Tue, 7:11:19 pm","permalink":"/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/","dgPassFrontmatter":true}
---


2K 4K 6K 8K 等

[第8节：如何生成细节度拉满的高质感画面，最终生成8K超高分辨率的图片小技巧分享\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1iZ421z7F9?p=8&spm_id_from=pageDriver&vd_source=f8573a6196003ad3683f1c1a403d3431)

# 直接生成大分辨率图片

分辨率过高容易出现多头, 多手, 多脚的问题. 因为模型训练的时候使用的图片分辨率比较低. 所以当想要输出大分辨率的图片的时候, 模型会认为需要对图像进行拼接, 导致出现了这种情况

容易暴显存

# 文生图 - 高分辨率修复

AI 基本上无法生成超级大图，想要生成高清图片正确的做法是分辨率调小，比如 512x768，然后开启 “高清修复”。

如果你不会用，照抄我的参数就可以了。放大倍数请考虑自己的显存大小调整，太大会爆显存

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202529184.png)

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202529322.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240609202647592.png)

## 本质

先生成一张低分辨率的图片, 然后再进行图生图

重绘算法影响的就是图生图阶段的操作

## 重绘算法

具体用哪种, 看模型介绍里作者有没有推荐. 如果没有, 可以用 R-ESRGAN 系列的, 分别对应真人和动画风格

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202529588.png)

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202529694.png)

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202529812.png)

[知识链接：关于放大中的几种插值处理方式（Antialiasing、Bicubic等） - 飞书云文档](https://gf66fxi6ji.feishu.cn/wiki/B5BUw1L60iIdTvknQHYckOAcncd)
# 图生图 - 高清修复

没有默认给出的高清修复选项, 因为重本质上来说, 图生图就是一种高清修复

在设置 - 放大 可以定义图生图的放大算法, 选项就是和文生图的高清修复一样的

不能说原理是一致的, 只能说思路是一致的. 都是分块处理再拼接, 但是后来者算法更好, 效果更强大

# 附加功能放大

在单独的 tab 里. 一般用于图片生成之后的处理上, 就像是一个重绘幅度为 0 的高清修复. 更像是传统的 ai 算法做的高清修复. 在 diffusion 出来之前, 就已经有好多这种的了, 不涉及 diffusion, 速度很快

优势在于简单方便, 负担小.

传统的放大算法

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202529913.png)

# ~~SD Upscale 脚本 (deprecated)~~

更大, 更精细

肯定是和他的原理有关. 不是模型层的?

确实是一个脚本, 因为他的原理是分块处理. 一块块的重绘再拼接到一起. 拉大缓冲区, 减小分块的边缘生硬感

用类似的方式, 可以把图片整得非常大, 但是不会明显的增加显存的压力

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202530038.png)

## 缺点

裁切成小块之后, ai 很难让 prompt 自动对号入座, 很容易让画面变得混乱

解决方法就是降低重绘幅度, 并且增大缓冲区尺寸. 重绘幅度小了, 增强细节的作用有限. 重绘幅度大了就会开始胡编乱造.

## 已废弃

推荐使用 tile 系列.

# Tiled Diffusion

tiled 瓷砖, 平铺. 有点点像 up scaler 拆成一块块单独处理, 但是效果会比 up scaler 更好

高清化对真实系照片非常重要, 因为只要足够多的细节, 才能体验出真实性. 相反二次元要求就没有那么高
{ #fdpum6}


## 基本原理

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202530152.png)

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202530335.png)

## 基本使用

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202530448.png)

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202530566.png)

### 分块区域

tiled diffusion 的配置就是用默认的就行.

宽高一般设置为图片的 1/8.

重叠的区块一般是分块的一半.

> 其实都是些经验值, 影响没有那么巨大

但是图生图的重绘幅度需要调整得低一点

### 图片尺寸

保持原图尺寸, 然后通过放大算法来放大倍数就行

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202530676.png)

### VAE

保持默认参数就行, 如果出现显存不足的情况, 可以降低编码器的分块大小

分快大小是降低显存的关键所在. 更多可以参考 github 介绍.

如果变灰了可以开启颜色修复

## 优点

低重绘幅度的时候, 可以增强细节, 消除不了阴影. 重绘幅度比较大的时候, 也可以通过参数控制不像 upscale 那样去胡乱生成.

产出同样分辨率的图, 显存占用更小, 生成速度更快

2023-06-13 最佳的放大方案

## 超高清&细节拉满的放大实践

1. 采样器和采样步数不要修改
2. 随机种子可变可不变, 影响不大
3. tile diffusion 的参数按照上面的配置好

使用高清修复, 现将图片的细节拉到 1080P 水平

然后再使用 tile 系列

[Multi Diffusion + Tiled VAE + ControlNet Tile模型，低显存打造AI绘画超高清6K分辨率体验！SD扩展插件教程\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1Su4y1d7Dp/?spm_id_from=333.788&vd_source=f8573a6196003ad3683f1c1a403d3431)

### CFG 最重要的参数

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202530808.png)

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202530927.png)

在高清放大的过程中, CFG 可以调动令人惊讶的图像细节

如果不希望图片拥有过多的细节, CFG 可以保持在 7 不变

如果希望可以增强细节, 可以将 CFG 调大到 14, 而且此时重绘幅度越大, 增加的细节也就越多

> [!NOTE] 重绘幅度一般设置在 0.2-0.35
> 在没有其他措施帮助的情况下, 重绘幅度一般设置不超过 4, 否则就会有很多妖魔鬼怪

--- start-multi-column: ID_5ran

```column-settings
Number of Columns: 2
Largest Column: standard
```

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202531069.jpeg)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202531184.jpeg)

--- end-multi-column

### 拉高重绘幅度, 增强细节

CFG14, 重绘幅度低, 虽然可以增强画质, 但是细节还是不够丰富. 想要丰富的细节, 只能是拉高重绘幅度, 给 ai 更多的发挥空间

为此, 需要辅以一下措施, 减少不想要的细节

#### 删除正向提示词里一些比较具体的对象

保留 masterpiece 等正向提示词即可

#### 开启 Tile Diffusion 噪声反转

这里所有的参数都是用来控制简洁的画面和丰富的细节之间的平衡

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202531310.png)

反转步数步数越低, 画面越整洁, 步数越高细节越丰富

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202531423.png)

修复程度, 修复程度越高, 越忠于原图, 在删除一些重绘幅度过高导致原图没有的东西

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202531550.png)

重复噪声强度, 越高细节越多, 越低画面越整洁

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202531669.png)

> [!NOTE] 推荐参数: CFG 14, 重绘幅度 0.75, 开启反转噪声, 步数 25, 重铺噪声 0, 修复程度灵活调整
> 有多余的内容, 就提高修复程度, 或者去局部重绘修复一下

### 与 ControlNet Tile 结合使用

因为分区块绘制不是那么容易控制细节元素. 很可能会多人物.

controleNet Tile 在讲大图分块的过程中, 会主动去识别每一张图片的内容. 如果识别出来的内容和全局提示词不匹配, 就会减少全局提示词的影响

生成时间会增常, tile 可以让 controlNet 去 CPU 运算.

在 ControlNet Tile 的加持下, 即使直接使用 CFG 14 加高重绘幅度, 也不会出现一些妖魔鬼怪的图片

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202531800.png)

比 tile diffusion 单独作用, 多了一份细节, 少了一份干净.

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202531915.png)

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202532050.png)

> [!NOTE] 最佳实践其一
> 此时 CFG 7, 重绘幅度 0.75, 重铺噪声强度为 0, 搭配 CN Tile
> 高清放大实践里, 参数一致, 生成的图片就每次都是一样的? 几乎看不出区别来

--- start-multi-column: ID_kn4b

```column-settings
Number of Columns: 2
Largest Column: standard
```

CFG 14 的, 树都长出花纹了

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202532164.jpeg)

--- column-break ---

CFG 7 的确实不错

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/upscale/image-20240609202532300.jpeg)

--- end-multi-column

> 挂载 tile 模型之后会发生色彩的问题, 这个是模型的特性带来的. 作者也无能为力

#### 分区提示词

# Local Latent Couple 用于局部的精细化 - LLuL

[飞书 - 登录](https://bytedance.larkoffice.com/wiki/Fedewe77gizYuMkiXHRcsQnknkg)

# cutoff

[飞书 - 登录](https://bytedance.larkoffice.com/wiki/HNofwwMhai7cAjkCydkcgmPNn2g)
