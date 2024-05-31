---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2024-05-28-Tue, 5:45:05 pm","date-modified":"2024-05-31-Fri, 10:00:06 am","permalink":"/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/","dgPassFrontmatter":true}
---


> sd 迈向工业化的第一步

是一个扩展. 在 controlnet 出现之前, 模型最大的问题就在于难以控制. 顶多用于自娱自乐.

用于帮助我们施加一些用语言不太好描绘的精确内容

在 1.1 版本, 可以实现 14 种不同的控制, 这还不包括不同预处理器和多重控制网 Multi-ControlNet 能产生的复合控制效果.

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240528141603053.png)

ControlNet 更新至 1.12 版本以后，上方多了一排可以用于快速选择对应的预处理器和模型的功能按钮，非常方便。选中其中的一个会自动匹配到对应的预处理器和模型上（如果你安装了）

# 基本原理

在原理上, controlnet 和 lora 有很多相似之处. 定位都是对大扩散模型做微调的额外网络

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240526200758865.png)

举个例子: 姿势, 如果只是通过提示词输入一个跳舞, 人物可能会有无数的舞蹈姿势. 通过 controlNet 传入一张特殊的图片, 不同的点线颜色代表着不同的含义, 来让模型明白需要怎么样的姿势.

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240526200925427.png)

和图生图有点类似, 因为本质上来说, 都是通过一些方式给 AI 提供额外的信息, 但是 controlNet 记录的信息比图片更加纯粹, 排除了图片本身元素..

# 基本参数

推荐开启完美像素模式

主要影响的就是控制效果的强弱.

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240526202055852.png)

通过预处理器 Annotator 生成 Controlnet 需要的数据

## 引导时机

默认的 0 - 1 就是全程生效

# Openpose 骨骼图

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240526201806286.png)

通过 face, hand, 强调重点

openpose editor. 编辑骨骼图

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240529192225459.png)

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240529223235296.png)

# Depth 深度图

对场景还原

对于富有空间感的多层次场景, 还原的很好

涉及人体透视. 因为 openpose 无法识别出手臂的前后关系.

因此这种时候使用 Depth 可以识别出来

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240526203102744.png)

# Canny / Lineart

提取边缘, 草稿, 黑笔白线

controlNet 的作者认为在还原这个领域, Canny 是最重要的模型. 因为识别出来了完整的外形

## 线稿上色

直接 canny 会让边缘模糊. 线稿图直接使用 inrevert 就好了

lineart 专门基于动漫风格的线稿实现上色功能的预处理器于模型的组合

# Hed / Soft Edge

柔和边缘.

在建立边缘信息时, 会比 canny 更模糊, 让 ai 有更多的发挥空间.

柔和边缘可以让图片过度更加自然

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240526203738396.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240526203758990.png)

# Scribble

比 HED 更加自由和奔放的描摹. 有时候可以激发一些奇妙的化学反应

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240526203917886.png)

灵魂画手操作案例. 用几笔简化, 就勾勒出一个场景, 让 ai 去帮你丰富

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240526204107926.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240526204138227.png)

# inPaint

实现创新型局部重绘

加强重绘区域内外的关联, 让过渡更加自然

# Tile

在放大工作流种增加细节

[Multi Diffusion + Tiled VAE + ControlNet Tile模型，低显存打造AI绘画超高清6K分辨率体验！SD扩展插件教程\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1Su4y1d7Dp/?spm_id_from=333.999.0.0&vd_source=f8573a6196003ad3683f1c1a403d3431)

[配合 controlNet 使用](inpaint.md#配合%20controlNet%20使用)

# lineArt

线稿上色, 形体固定

基于线稿图生图, 使用不同的风格的大模型即可实现真人化或者动画化

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240530213937113.png)

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240530213941396.png)

# IP-Adapter

实现换肤, 风格迁移

图生图并非一种真正的提示, 只是塑造一种色彩上的相似性.

IP-Adapter 会去真正的理解你输入的图片的含义, 并利用他学习到到的东西去微调输出的结果.

从色彩, 形象, 内容, 已经, 都会更像参考图. 和其他模型组合起来使用, 实现**风格迁移**

不同的 IP-Adapter 模型, 迁移的内容不同, 注意甄别

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240529095654764.png)

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240529101426106.png)

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240529101415370.png)

# Seg

语义分割, semantic Segmenttation Model. 是一种将标签或类别与图片的像素信息关联的一种深度学习算法

> seg 绘画应该是未来的趋势, 但是还需要简化. 比起单纯用 prompt 描述画面, 如果未来可以通过 sketch seg + 标注描述画面, 那将绝杀. 需要一个自动取色器, prompt seg search 然后可以自动替换画笔颜色即可

利用语义分割去识别一张图片上的每个部分分别是什么东西

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240529163211164.png)

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240527214825521.png)

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240529163328376.png)

通过 ps 以及 seg, 完成语义分割的画作, 再使用 sd 生成成品

# ReColor

![stable-diffusion-practice](programming/ai-generator/stable-diffusion/stable-diffusion-practice.md#上色)

# InstantId

保持人物一致性

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240530094537063.png)

InstantID 使用 InsightFace 从参考人脸中检测、裁剪和提取人脸 embedding 。然后 embedding 与 IP-Adapter 一起使用来控制图像生成。这部分与 IP-Adapter Face ID 非常相似。不过，它还使用 ControlNet 检测并修复多个面部标志（眼睛、鼻子和嘴巴）。

结合使用 IP-Adapter Face ID 和 ControlNet，可以高保真度地复制我们提供的参考图像，从而最终实现人物角色的一致性。

注意：InstantID 需要使用 SDXL 大模型，目前还没有 Stable Diffusion 1.5 对应的版本。

## 操作实例

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240530100448628.png)

controlNet unit 0 使用 embedding 预处理器 + ip-adapter_instant_id_sdxl

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240530100304384.png)

controlNet unit 0 使用 instant_id_face_keypoints 预处理器 +control_instant_id_sdxl

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240530100412262.png)

```text
high quality,masterpiece,rich details,realistic photography,8k,high-definition image quality,
watercolors portrait of a woman,artistry,
```

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240530101650787.png)

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240530101703450.png)

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240530101709558.png)

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240530101712649.png)

切换不同风格, 即可生成同一个人的不同画风的头像

比如 instantid 如果用来做人物头像, 其实是没有问题的, 可以生成同一个人各种各样的人物头像. 或者是半身照, 证件照等等. 但是用作全身像时因为面部采样分配不足, 很难做到继续控制人物一致
{ #9kex7j}


> [!question] 要怎么才能全身像然后人脸也一致呢?

# Reference Only

出现得最早的用于人物一致性的尝试. 效果不好才有了后续的 ip-adapter 和 instand_id

用于固定特征, 也可以用于保持人物一致性, 但是泛用性更强一点. 类似于很多个长得很像的人, 穿搭也是一个调调, 但是仔细看就能发现每次脸都不一样? 但是在小说推文场景, 可以用作角色的创建工作, 作为人物管理的基础. 还是够用的
{ #z01ii5}


[如何保证每次画出的都同一张人脸：Stable Diffusion的Reference only教程 - AI魔法学院](https://www.wehelpwin.com/article/4343)

[Stable Diffusion | ControlNet：Reference“垫图”功能，不炼丹也能保持同一人物\_controlnet reference-CSDN博客](https://blog.csdn.net/cxyxx12/article/details/136524175)

[Stable Diffusion仿制神器,Reference Only评测 - 知乎](https://zhuanlan.zhihu.com/p/629980765)

# Ip-adapter Face Id

[【AI绘画】Stable Diffusion轻松生成一致性角色！一键设定人物长相！强到离谱！（附安装包，插件）\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1Bv421i7cq/?vd_source=f8573a6196003ad3683f1c1a403d3431)

很不错. 配合 openpose 使用, 就可以摆 pose 了. 很舒服. 比 reference only 效率高, 泛用性强

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240531093807370.png)

````col
```col-md
flexGrow=1
===
![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240531093745838.png)
```
```col-md
flexGrow=1
===
![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240531093741159.png)
```
````

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240531093831488.png)

````col
```col-md
![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240531093206635.png)
```
```col-md
![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/control-net/image-20240530191259074.png)
```
````

# Multi ControlNet

## Openpose + Depth

openpose 无法判断手在前还是在后. 而 Depth 可以识别.

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240526204527339.png)

权重的调节需要更加谨慎
