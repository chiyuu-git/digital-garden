---
{"aliases":["扩图"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2024-05-30-Thu, 6:33:47 pm","date-modified":"2024-06-08-Sat, 2:18:17 pm","permalink":"/programming/ai-generator/stable-diffusion/work-flow-unit/outpatin/","dgPassFrontmatter":true}
---


[【AI绘画】用Stable Diffusion三步扩图！帮你无中生有，轻松一键扩图！\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1mt421W7kb/?spm_id_from=pageDriver&vd_source=f8573a6196003ad3683f1c1a403d3431)

利用图生图的缩放模式来实现

# 图生图 Outpaint

![stable-diffusion-basic](programming/ai-generator/stable-diffusion/stable-diffusion-basic.md#缩放后填充空白)

# 局部重绘 Outpaint

图生图太容易改变原图的部分了. 仅仅是做 Outpaint 的话, 局部重绘更加合适. 讲整张图盖上蒙版, 然后让重绘非蒙版部分, 即可实现符合预期的扩图

内容处理选择填充, 本质上是无中生有. 如果仅仅是修复的话, 应该选择原图

重绘幅度 0.5, 如果出现的效果比较拉就拉高一点重绘幅度. prompt 不用改也行的

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/outpatin/image-20240607194828056.png)

![](/img/user/programming/ai-generator/stable-diffusion/work-flow-unit/outpatin/image-20240607194841437.png)

搭配 controlNet inpatin lama 使用效果更佳
