---
{"aliases":["局部重绘"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2024-05-28-Tue, 4:32:31 pm","date-modified":"2024-06-15-Sat, 8:22:01 pm","permalink":"/programming/ai-generator/stable-diffusion/work-flow-unit/inpaint/","dgPassFrontmatter":true}
---


# 基本参数

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/inpaint/image-20240607191953696.png)

## 蒙版模式

## 蒙版区域内容处理

填充是做无中生有

> 实测最有效的是扩大重绘幅度, prompt 改不改都无所谓的

原本是做修复用的

![comfyUI](programming/ai-generator/stable-diffusion/comfyUI.md#^6vrcp8)

## 重绘区域

重绘区域一般都是选择整张图片, 含义可以理解为 prompt 是匹配整张图片的, 然后蒙版部分需要重绘, 并且要和周围羽化融合. 不用担心非蒙版区域被修改. 起到协调填充的区域和原图的作用

仅蒙版区域一般是要删改 prompt 专门做修复用的

# 改变人物表情 / 修复人脸 / 修复手崩的问题

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/inpaint/image-20240602220051661.png)

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/inpaint/image-20240602220106602.png)

> 不知道为什么需要重绘整张图片才能改成闭眼

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240528114402158.png)

## 脸崩的原因

面部分配的分辨率不够. 面部修复功能一般

采样分配不够

分辨率过低, 模型没有足够的操作空间, 无法为这一块区域分配更多的细节

## 高清修复

图片在一个较小的合理范围, 通过高清修复, 把图片放大两倍来拉高分辨率.

并不是最优解, 这个画面一起提高分辨率, 脸部区域仅仅获得了少部分的采样提升

## 局部重绘

把整个脸部涂抹上. 人脸, 仅蒙板, 会按照重绘区域的分辨率来绘制.

但是不能解决多人画面的问题, 每次只能单独修复一个人脸

否则每张人脸都会是一样的.

局部重绘的局限性比较大, 用于重新生成的不确定性太大了. 能划坏一次, 就可以划坏无数次

## ADetailer

24 年 3 月的秋叶整合包, 已经包含了类似的功能, 但是不知道具体是哪个插件

感觉是一个局部重绘插件

![stable-diffusion-term](programming/ai-generator/stable-diffusion/stable-diffusion-term.md#ADTailer)

## 反向提示词

embeddings.

优点: 方便快捷

缺点: 可控性差, 效果一般, 会改变画面

# Sketch 局部重绘 (手涂蒙版, 涂鸦重绘)

使用涂鸦引导作画

[AI绘画的救星！定向修手修脸，手把手教你玩转局部重绘！| StableDiffusion WebUI 保姆级教程·inPainting功能详解·含PS操作\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1uL411e7Uk/?p=7&spm_id_from=pageDriver)

sketch 也可以单独使用, 相当于是把蒙版的区域加入到图中, 做一次图生图. 灵魂画手效果

 --- start-multi-column: ID_60j9

```column-settings
Number of Columns: 3
Largest Column: standard
```

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240528114633211.png)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240528114725045.png)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240528114646397.png)

--- end-multi-column

最大的特点是可以定义颜色和结构

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/inpaint/image-20240607194941406.png)

# 上传重绘蒙版

通过精确的蒙版, 告诉模型哪些部分需要重绘. 因为 webUI 自带的蒙版, 还挺粗糙的. 需要有更专业的

--- start-multi-column: ID_j30b

```column-settings
Number of Columns: 3
Largest Column: standard
```

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240528114047468.png)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240528114051948.png)

--- column-break ---

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/inpaint/image-20240528164419170.png)

--- end-multi-column

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/inpaint/image-20240528164257799.png)

## Ps 智能抠图

选择 - 主体, 智能抠图

对象选择工具, 自动扣选取部分的主体

![](/img/user/programming/ai-generator/stable-diffusion/stable-diffusion-basic/image-20240526104258855.png)

确定选取之后, 图层 - 新建填充图层 - 纯色

## Segment Anything

通过语义智能抠图, 比如 the girl 就能把女孩从图中抠出来. 特别适用于工作流场景批量处理

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/inpaint/image-20240607195458033.png)

segment 换衣服感觉没有啥必要. 有涂鸦之类的更好处理的. 具体的用途有点想不明白

# 配合 controlNet 使用

[inPaint](control-net.md#inPaint)

# 应用

## 使用局部重绘将物件引入到画面中

Cyberhelmet wearable Lora

如果开的是全图模式, 那就维持原来的 prompt 和参数都不用变, 加入物件 lora 以及对应的触发词, 关键词即可

如果是仅蒙版模式, 可以把内容部分的提示词去掉, 只保留蒙版内需要重绘的部分提示词, 重绘得到的结果会更加精确

## 换背景

![stable-diffusion-practice](programming/ai-generator/stable-diffusion/stable-diffusion-practice.md#InPaint%20换背景)

新增人物的光影是与周围的建筑融为一体的. 但是人物是属于前景有正面的大光源, 所以还是需要细节调整. 但是如果仅观察新增人物部分的话, 确实是融入进去了

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/inpaint/image-20240528164419170.png)

# FAQ

## 局部重绘的区域和旁边的区域有较明显的色彩/断层感，该如何解决？

- 增大蒙版边缘模糊（Mask Blur），可以提供更为柔和的过渡效果；
- 尽量选择与原图风格相近的大模型来进行重绘；
- 降低重绘幅度；如果是 AI 生成图片，使用相同的种子也会有一定的帮助；
- 使用**ControlNet 中的 Inpaint 模型**辅助进行生成；

## 使用局部重绘的时候，为什么在重绘区域内多出了一整个人来？

- 你可以选择降低重绘幅度来避免，但太低可能会造成重绘效果差。
- 如果你绘制的部分明确不包括一整个人，可以在提示词里去掉人相关的描述，并加入诸如 no_humans 这样的提示词来规避，并强化对应的描述成分（比如画手，可以加入大量关于 hand 的描述词）。
