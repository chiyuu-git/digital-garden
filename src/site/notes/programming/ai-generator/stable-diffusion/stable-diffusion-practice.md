---
{"aliases":["sd实际应用","sd实践"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2024-05-27-Mon, 4:12:43 pm","date-modified":"2024-06-14-Fri, 4:22:36 pm","permalink":"/programming/ai-generator/stable-diffusion/stable-diffusion-practice/","dgPassFrontmatter":true}
---


# AI 绘画合成文字形体特效

[“牛逼”的教程来了！一次学会AI二维码+艺术字+光影光效+创意Logo生成，绝对是B站最详细的Stable Diffusion特效设计流程教学！AI绘画进阶应用_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1gX4y1J7ei/?p=13&spm_id_from=pageDriver)

用一些东西汇聚成文字形体: Depth Midas + Depth 组合

文字, logo ,二维码都可以

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240527183433706.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240528215446720.png)

## AI 材质艺术字

controlNet 权重越低, 形体会越自由

引导时机影响生成插手的时间

除了处理文字, 还可以用于处理 logo 等任意形体, 艺术字体

![|300](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240602190026647.png)

--- start-multi-column: ID_e0e8

```column-settings
Number of Columns: 3
Largest Column: standard
```

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240601212436618.png)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240601212409867.png)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240601212838687.png)

--- end-multi-column

```
stone,rock,coal,sand,from above,
```

```
flower,dappled,sunlight,from above,bunch of flowers,outdoors,grasslands,
```

## Brightness 和 Illumination

controleNet 模型

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240527184059681.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240527184454756.png)

### Brightness

控制亮度, 哪些地方亮, 哪些地方暗

只要文字部分更亮一点, 就可以做到信息的传递

### Illumination

更自然, 更柔和

## 二维码

qr toolkit 可以方便的改变二维码的样式

# 风格转换

## SDXL 自带的风格转换功能

通过 prompt 词来切换不同的风格, 搭配 style selector 即可愉快使用.

重点参数就只有一个, 重绘幅度 0.5-0.7. 0.7 基本是一个阈值,超过 0.7 之后就和原图非常不像了

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240606225348850.png)

## 切换大模型或者 Lora 模型改变风格

## controlNet Tile 模型

![Tile](work-flow-unit/control-net.md#Tile)

# 真人化/ 动画化 / 赛博 Coser / 实现风格迁移

[AI打开新世界大门！立绘一键“真人化”，还能换皮肤！ControlNet LineArt 线稿上色+换装换背景，Stable Diffusion系统教程_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1oB4y1R7Ms/?p=14&spm_id_from=pageDriver)

## controlNet lineArt

![control-net](programming/ai-generator/stable-diffusion/work-flow-unit/control-net.md#lineArt)

要注意画风偏向的问题

![control-net](programming/ai-generator/stable-diffusion/work-flow-unit/control-net.md#画风偏向)

## IP-Adapter 实现换肤, 风格迁移

![control-net](programming/ai-generator/stable-diffusion/work-flow-unit/control-net.md#IP-Adapter)

## InPaint 换背景

启用人物蒙版 + controlNet 局部重绘, 可以加强背景和人物之间的关联

再开启人物 + 背景的 controlNet lineArt 等等, 把背景的形状和人物的形体也都固定下来

[AI打开新世界大门！立绘一键“真人化”，还能换皮肤！ControlNet LineArt 线稿上色+换装换背景，Stable Diffusion系统教程_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1oB4y1R7Ms/?spm_id_from=333.999.0.0&vd_source=f8573a6196003ad3683f1c1a403d3431)

# LayerDiffusion

github.com/layerdiffusion/sd-forge-layerdiffuse

传统的 ps 工作流中, 图片往往是分层的, 但是 sd 的图片虽然精美却无法分层

controlNet 作者的又一力作. 可以生成透明背景的 png 图片.

带有透明通道的平面素材, 可以广泛应用到平面设计, 游戏制作等各种场景里. 工业化的又一大里程碑

通过 Forge UI 插件可以轻松使用 Layer Diffusion. 全称是 SD WebUI Forge

暂时不支持 Web UI. Forge UI 看起来和 Web UI 差别也不大呀. ComfyUI 到是能用

类似 lora 的形式, 不会有额外的性能负担. 但是最初的版本只支持了 SDXL. 也可以用 SD 1.5 版本的模型, 但是效果会稍微差一点

## 基本使用

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240527162728757.png)

## 抠图的本质区别是什么

和一些智能抠图的工具不同, 在生成阶段就将透明通道的信息加入了运算.

### 生成半透明素材

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240603102630592.png)

透明玻璃瓶的内部也是有 alpha 通道的

在传统的 ps 等抠图软件, 要实现类似的效果需要结合抠图和混合模式才能实现

### 生成粒子素材

极大的减轻抠图难度. 说白了, 就是一些抠图比较困难的场景, 都可以使用 layerDiffusion 直接生成

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240603102639066.png)

## 进阶操作

## 前景和背景以及融合

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240527170253946.png)

### 前景补充背景生成融合图

在 prompt 里输入完整的提示词, 即是 blending 状态的提示词, 会自动补全背景和光影融合

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240527171132468.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240527170355636.png)

### 前景与融合图做 Diff 产出背景

在提示词里去掉关于人的描述, 或者加入 no human 等限定词

### 缺陷

对于透视的理解不够, 人物比例和背景的融合度不足. 需要一些微调

### SD 1.5 Anyhing Together

一次输出三张图. 单次生成需要是 3 的倍数. 因为 sd1.5 语义理解偏弱, 所以需要有三个额外的 prompt 提示框. 上方的提示词, 此时只需要保留一些基本的画质词就好了. 其他可以拆分到下面

## Attention Injection 与 Conv Injection

在模型训练的时候, 一个是针对常规注意力层, 另一个是针对卷积层. 参考 github 作者的描述即可

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240527170957944.png)

## 与其他模型搭配

并没有要求. 都可以搭配, 效果都挺好的, 多多抽卡

## 未来可期

生成多部分的 layer. 或者结合 video , 生成带有透明通道的 live 2D 动图, 3d 模型等等

# AI 写真 EasyPhoto

lora 训练

![stable-diffusion-term](programming/ai-generator/stable-diffusion/stable-diffusion-term.md#EasyPhoto)

# SD 与 PS 结合使用

反复导入导出太浪费时间了

![stable-diffusion-term](programming/ai-generator/stable-diffusion/stable-diffusion-term.md#Auto%20Photoshop%20Stable%20Diffusion%20Plugin)

# 多人场景

[AI再进化，这次竟然学会摄影了！一秒生成胶片风、拍立得，还能智能“修脸”！Stable Diffusion AI绘画真实系人像模型+LoRA推荐_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1DP41167bZ/?spm_id_from=pageDriver&vd_source=f8573a6196003ad3683f1c1a403d3431)

# AI 上色, AI 照片修复

## 放大

--- start-multi-column: ID_adrm

```column-settings
Number of Columns: 2
Largest Column: right
```

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240529213645043.png)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240529213710070.png)

--- end-multi-column

Tiled diffusion 超分放大, 重构容易造成一些内容的变形

stable SR 真实系的超分扩展

## 上色

--- start-multi-column: ID_kztx

```column-settings
Number of Columns: 2
Largest Column: standard
```

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240529215402123.png)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240529215335174.png)

--- end-multi-column

传统的上色效果还是生图. 不如 controlNet Recolor.

Recolor 模型的作用原理还是基于扩散对图片内容进行重新生成, 但它会根据黑白照片的亮度 (Luminance) 或者强度 (Intensity) 来指导新的图片进行生成. 生成完了之后, 它会将彩色版本的图片盖在原来的这张图片上 (以某种方式混合), 从而保留原有形象轮廓, 做到只变颜色

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240603173044626.png)

使用 sketch 来上色

即使不是黑白问题也可以 recolor

需要结合智能画布来做细节处理, 难度很大, 不是光靠 ai 就可以完美处理的. 很多细节都只能导入 ps 中做优化

[我用AI修复了50年前的亚运会老照片！Stable Diffusion智能上色+高清修复教程！StableSR + ControlNetTile&Recolor_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1mm4y1575y/?p=16&spm_id_from=pageDriver)

### 漫画上色

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240603173239091.png)

# AI 电商 / AI 服装设计 / AI 模特

[靠AI开一家服装店？超详细AI衣服款式设计+“AI模特”换装教程 | Stable Diffusion商业落地实践&男女装电商应用，一秒生成“摄影级”产品图_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1994y1W7Tg/?spm_id_from=333.999.0.0&vd_source=f8573a6196003ad3683f1c1a403d3431)

## AI 印花图案设计

logo lora redmond 生成的图片更像是一个简单背景下的小图案

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240602194051763.png)

> [Logo.Redmond - Logo Lora for SD XL 1.0 - v2.0 | Stable Diffusion LoRA | Civitai](https://civitai.com/models/124609/logoredmond-logo-lora-for-sd-xl-10)

![|300](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240602193856906.png)

```
A logo of a boy and a girl in astronaut costume traveling around the universe, <lora:LogoRedmond_LogoRedAF:1>,
logo, LogoRedAF, minimalist color, (isolated on white background, simple background)
Steps: 20, Sampler: DPM++ 2M Karras, CFG scale: 7, Seed: 716255618, Size: 1024x1024, Model hash: 31e35c80fc, Model: Stable Diffusion SDXL_1.0, Style Selector Enabled: True, Style Selector Randomize: False, Style Selector Style: base, Version: v1.5.1
```

style selector

作用的本质是往提示词里添加了一系列的前缀词, 后缀词, 从而调用 SDXL 模型原生的艺术风格

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240529221411185.png)

全自动抽卡

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240529221429462.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240529221443924.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240529221602596.png)

## AI 服装设计

服装设计其实很多时候需要的是灵感, 而 AI 最不缺的就是灵感. 在疯狂抽卡的过程中, 一个又一个灵感会不断迸发出来

```
concept art (fashion costume design sheet, three views, multi-angle display, clothing design, blueprint),An elegant cocktail dress with a sweetheart neckline,lace details,and a pencil skirt,. digital artwork,illustrative,painterly,matte painting,highly detailed,
```

 ![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240529221807087.png) --- start-multi-column: ID_cgqx

```column-settings
Number of Columns: 3
Largest Column: standard
```

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240601215725899.png)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240601220416810.png)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240529221835577.png)

--- end-multi-column

### Clothes Wildcards

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240529221011605.png)

> [https://civitai.com/models/73184/clothes-wildcards](https://civitai.com/models/73184/clothes-wildcards)

wildCard 由其他人为你准备好的提示词合集.

服装 wildCard, 使用通配符作为 prompt, 拉大生成批次, 就会随机从通配符选择 prompt 生成

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240601221026212.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240601220837704.png)

### 配色

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240529221018115.png)

> [https://civitai.com/models/122976/various-wildcards](https://civitai.com/models/122976/various-wildcards)

在生成了满意的服装之后, 固定 seed, 单批数量拉大. (而不是批次拉大) 通过配色 wildscard 来抽卡. 注意将颜色通配符放在服装款式之前组合为一个完整的 prompt

```
best quality,masterpiece,detailed,highres,simple background,white background,no humans,(__variousWildcards_v11/colors/Basecolors__) trench coat,
```

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240601222740207.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240601220020493.png)

### 展望与局限性

除了外观和配色, 还需要考虑衣服的材料, 结构, 等等很多的东西. 这些不是一张像模像样的效果图就可以解决的. 更多还是提供灵感

做图案, 做款式, 挖掘灵感

## AI 模特

[靠AI开一家服装店？超详细AI衣服款式设计+“AI模特”换装教程 | Stable Diffusion商业落地实践&男女装电商应用，一秒生成“摄影级”产品图\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1994y1W7Tg/?spm_id_from=333.999.0.0&vd_source=f8573a6196003ad3683f1c1a403d3431)

正面平铺产品图, 结合 controlNet

但是很呆板, 模特动作很僵硬, 无法体现模特的身材

--- start-multi-column: ID_emum

```column-settings
Number of Columns: 3
Largest Column: standard
```

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240601223012354.png)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240601223017708.png)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240601223022164.png)

--- end-multi-column

最好的方式是真的把衣服穿到身上. 然后拍照重绘

AI 换模特, 用 ai 补足模特的不足, 可以用更便宜的模特. 或者用人体模型把衣服套上就行了

--- start-multi-column: ID_aghc

```column-settings
Number of Columns: 3
Largest Column: standard
```

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240601223225515.png)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240601223250158.png)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240601223314985.png)

--- end-multi-column

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240601223325377.png)

最大的问题还是生图与实物不符, 所以需要配合蒙版抠出衣服并且谨慎使用

# AI 小说推文

[zhuanlan.zhihu.com/p/695204247](https://zhuanlan.zhihu.com/p/695204247)

[一键搞定AI小说推文,超详细教程 | Sora最佳学习网站](https://soraor.com/archives/yi-jian-gao-ding-aixiao-shuo-tui-wen-chao-xiang-xi-jiao-cheng)

[Stable Diffusion+Midjourney+chatGPT,这三个工具已经开始帮我全自动赚钱了！-叁心笔记](https://www.5aivip.com/151.html)

[自媒体福音！AI绘画小说推文神器-Stable Diffusion漫画助手插件，一键生成漫画风人物，无脑级操作！（附资料）_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV18e411E7aB/?spm_id_from=333.337.search-card.all.click&vd_source=f8573a6196003ad3683f1c1a403d3431)

[百度网盘 请输入提取码](https://pan.baidu.com/s/1ur04Z5MfGHUaPdmjZ7Q80g?pwd=7799)

[一个插件啥都能画？漫画助手5.0发布~~_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV12k4y1F79k/?spm_id_from=333.999.0.0&vd_source=f8573a6196003ad3683f1c1a403d3431)

[当我尝试用AI做小说推文后，真的能搞到钱💰 - 知乎](https://zhuanlan.zhihu.com/p/688062635)

[绘唐科技——用户文档 - 飞书云文档](https://wv5o0g1m55v.feishu.cn/wiki/QDDfwmYJKihDMtkENDfcsNJIn7f)

![](stable-diffusion-practice/漫画助手5.01.rar)

推文场景人物的一致性, 似乎不那么重要. 只需要头发颜色可以保持一致, 人物的年龄不要波动太大就行.

在抖音上甚至延申除了柳如烟这个网络 meme 角色. 所有的银发大小姐都起名叫柳如烟. 虽然各家的画风可能不太一致. 但是只要叫这个名字的银发女角色, 观众都能立马在脑海中脑补出她的经典角色形象. 也给小说推文的任务一致性降低了要求.

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240602090205693.png)

本质上只是一个批量出图的脚本

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240602092508145.png)

--- start-multi-column: ID_pefz

```column-settings
Number of Columns: 3
Largest Column: standard
```

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240602092448163.png)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240602092433813.png)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240602092422401.png)

--- end-multi-column

# 控制人物一致性

说白了就是脸要一致, 身材大差不差就行

二次元只要发色, 瞳色, 肤色, 发型一致, 基本上就区分不出来饿了

## 凭空造人

利用名人的面部确实效果不错，但是我们有时候也不太想这么去做，一方面有局限，另一方面也有潜在危险，所以如果能在直接生成不存在的人物同时还能保持一致性的话，就比较理想了。

AI 绘图的玩家们在后面的尝试中发现：其实我们只要给定一个不存在的（凭空创造的）名字，也能够实现面部的一致性。

比如，我们乱编一个名字叫做 Jauny Doey：

```
1girl, [Jauny Doey], ((dressed in detailed rusty silver armor)), upper body, studio light, medieval age armor, angry, black hair, brown eyes, high quality, ((battlefield background)), blurry background,
Negative prompt: helmet
Steps: 30, Sampler: Euler a, CFG scale: 10, Seed: 1151986892, Face restoration: CodeFormer, Size: 768x512, Model hash: 9aba26abdf, Model: deliberate_v2, Clip skip: 2, ENSD: 31337
```

就可以生成这个独特的角色

--- start-multi-column: ID_7fpj

```column-settings
Number of Columns: 3
Largest Column: standard
```

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240602195945755.png)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240602195935209.png)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-practice/image-20240602195918942.png)

--- end-multi-column

## ControlNet

[Stable Diffusion 制作一致性角色差异姿势\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV19V411w7iv/?vd_source=f8573a6196003ad3683f1c1a403d3431)

[人物一致性：7分钟掌握ComfyUI InstantID x 云端一键克隆\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1hE421V7yx/?spm_id_from=333.999.0.0&vd_source=f8573a6196003ad3683f1c1a403d3431)

![control-net](programming/ai-generator/stable-diffusion/work-flow-unit/control-net.md#^9kex7j)

![control-net](programming/ai-generator/stable-diffusion/work-flow-unit/control-net.md#^z01ii5)

![control-net](programming/ai-generator/stable-diffusion/work-flow-unit/control-net.md#Ip-adapter%20Face%20Id)

## Lora

[【Stable Diffusion】角色设计的福音！绘制一致性多角度头像 - UIED用户体验学习平台](https://www.uied.cn/33458.html)

[🐼 stable diffusion角色设计 用Controlnet分别在SD WEBUI和ComfyUI中绘制多角度人像的方法 - YouTube](https://www.youtube.com/watch?v=xtIyH8Lj6As&list=PL4L5yXcAegdxwcD2RRffQntmXygv26auT&index=40)

最佳解决方案一定是训练 lora. 其他方案都是一些在特定场景可以发挥作用的控制而已.

用到最后还得是 lora 和 reactor。其他的感觉都一般般。

## PulID

## ReActor

## 对比

faceid 出现的最早, 效果也不赖, 主要是显存依赖小

instantdid 有的人认为是效果最好的, 但是显存依赖比较大, 跑起来很慢

# IC_Light

[知乎 IC-Light 介绍](https://www.zhihu.com/question/655431668/answer/3493306758)

[IC-Light 控制光源位置 替换背景 ComfyUI工作流\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV12n4y1d7xC/?spm_id_from=333.999.0.0&vd_source=f8573a6196003ad3683f1c1a403d3431)

[IC-Light + IPAdapter 控制光源位置 ComfyUI工作流\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1fD421G7VK/?spm_id_from=333.788.0.0&vd_source=f8573a6196003ad3683f1c1a403d3431)

[【独家】IC-LIGHT换指定背景时特别假？优化方法来啦！\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV11D421G7K5/?spm_id_from=333.788.0.0&vd_source=f8573a6196003ad3683f1c1a403d3431)

```
cinematic lighting 电影光
cinematic lighting动感光
volumetric lighting体积光
mood lighting气氛光
bright明亮
Soft illumination柔光照明
soft lights瑞光千条
rays of shimmering light闪烁的光线
Crepuscular Ray云隙光(耶稣光)
bioluminescence生物发光
bisexual lighting气氛光
Rembrandt Lighting人物的45度角侧向光
Split Lighting高对比的侧面光
front lighting正面光顺光
Back lighting 逆光
clean background trending 斜逆光
rimlights边缘光
global illuminations 全局光
Warming lighting暖光灯
Natural lighting 戏剧灯光
```

# 加速出图的手段

硬件层的加速就是和 英伟达或者 amd 合作的各种手段: tensorRT

ui 层的加速就是 comfyUI 了

模型层的加速: xl + lora + KSampler [超越LCM，TCD登场！盘点Stable diffusion加速出图四大法宝！\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1gD421L7BQ/?spm_id_from=333.337.search-card.all.click&vd_source=f8573a6196003ad3683f1c1a403d3431)
