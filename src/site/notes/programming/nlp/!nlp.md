---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-06-10-Sat, 1:53:53 pm","date-modified":"2023-06-12-Mon, 7:23:29 pm","permalink":"/programming/nlp/!nlp/","dgPassFrontmatter":true}
---


# 对于大语言模型相关还是论文最直接：

## 书籍：

"Speech and Language Processing" by Daniel Jurafsky and James H. Martin

"Deep Learning" by Ian Goodfellow, Yoshua Bengio, and Aaron Courville

## 网站：

OpenAI Blog

Google AI Blog

arXiv.org for preprints of research papers in the field

[https://ar5iv.labs.arxiv.org/html/2107.02137](https://ar5iv.labs.arxiv.org/html/2107.02137) ERNIE 3.0: Large-scale Knowledge Enhanced Pre-training for Language Understanding and Generation

[https://arxiv.org/abs/2001.08361v1](https://arxiv.org/abs/2001.08361v1) Scaling Laws for Neural Language Models

## 知乎上前人总结的文档：

[自然语言处理（NLP）相关学习资料/资源](https://zhuanlan.zhihu.com/p/97191442)

# 深度学习 - 路线

## 理论部分

### Overview

Andrej Karpathy 前一段时间在微软 Build 大会上的演讲是一个挺不错：

[【精校版】Andrej Karpathy微软Build大会精彩演讲： GPT状态和原理 - 解密OpenAI模型训练_哔哩哔哩_bilibili](https://link.zhihu.com/?target=https%3A//www.bilibili.com/video/BV1ts4y1T7UH/%3Fshare_source%3Dcopy_web%26vd_source%3Da0e91ceeeddaf4fc60dc364143cb1828)

### Details

之后可以直接参考 [李沐](https://www.zhihu.com/search?q=%E6%9D%8E%E6%B2%90&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3063230236%7D) 老师精读论文的视频，看完视频可以再去刷原文：

Transformer：[https://youtu.be/nzqlFIcCSWQ](https://link.zhihu.com/?target=https%3A//youtu.be/nzqlFIcCSWQ)

GPT：[https://youtu.be/t70Bl3w7bxY](https://link.zhihu.com/?target=https%3A//youtu.be/t70Bl3w7bxY)

InstructGPT：[https://youtu.be/zfIGAwD1jOQ](https://link.zhihu.com/?target=https%3A//youtu.be/zfIGAwD1jOQ)

### 书籍

《Python 自然语言处理》（作者：Steven Bird, Ewan Klein, Edward Loper）：这本书是自然语言处理领域的经典入门书籍，详细介绍了使用 Python 进行自然语言处理的基本方法。

《深度学习》（作者：Ian Goodfellow, Yoshua Bengio, Aaron Courville）：这本书是深度学习领域的经典教材，详细介绍了深度学习的各种方法和技术。

### 在线课程

吴恩达的《深度学习专项课程》（Coursera）：这个课程详细介绍了深度学习的基本概念和方法，包括神经网络、卷积神经网络（CNN）、循环神经网络（RNN）等。

李宏毅的《深度学习课程》（YouTube）：这个课程详细介绍了深度学习的各种进阶技术，包括注意力机制、Transformer、BERT 等。

### 在线资源

[斯坦福大学的CS224n课程网站](http://web.stanford.edu/class/cs224n/)：这个课程是自然语言处理领域的经典课程，详细介绍了使用深度学习进行自然语言处理的各种方法和技术。

[Hugging Face的Transformers文档](https://huggingface.co/docs/transformers/index)：这个文档详细介绍了如何使用 Transformers 库进行自然语言处理任务，包括文本分类、命名实体识别、问答系统等。

[【精校版】Andrej Karpathy微软Build大会精彩演讲： GPT状态和原理 - 解密OpenAI模型训练\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1ts4y1T7UH/?vd_source=db8a4b4129af2e1d7a3e3f6357bb4d45):

## 实践部分

理论学了一大堆，还是要实践一下才能真正融会贯通。自己 train 一个真正的 foundation model 不现实，但可以复现一个小的玩一玩。

### nanoGPT

Andrej Karpathy 写的 [nanoGPT](https://link.zhihu.com/?target=https%3A//github.com/karpathy/nanoGPT)，CPU 和 GPU 上都能跑，硬件门槛非常低。可以用来了解下预训练。另外 Andrej 还搞了一个 [教程](https://link.zhihu.com/?target=https%3A//youtu.be/kCc8FmEb1nY) 上传到了 youtube，非常贴心。

### Alpaca & Alpaca-lora

Stanford 基于 LLaMA 搞的 [alpaca](https://link.zhihu.com/?target=https%3A//github.com/tatsu-lab/stanford_alpaca)，可以了解下 SFT。基于 alpaca 衍生的 [alpaca-lora](https://link.zhihu.com/?target=https%3A//github.com/tloen/alpaca-lora) 可以在消费级显卡上跑，硬件门槛也比较低。

### DeepSpeed Chat

感谢

[@Harvey.Peng](//www.zhihu.com/people/19905574e18fbe60c23c1685133c1bfc)

的补充。

[DeepSpeed Chat](https://link.zhihu.com/?target=https%3A//github.com/microsoft/DeepSpeed/tree/master/blogs/deepspeed-chat/chinese) 有完整的 RLHF 实现，也有 step by step 的案例。根据文档中的叙述，也可以在消费级显卡（_A6000-48G_）上跑 OPT-1.3B。

# Open AI API 使用指南

[GitHub - openai/openai-cookbook: Examples and guides for using the OpenAI API](https://github.com/openai/openai-cookbook)

[DLAI - Learning Platform Beta](https://learn.deeplearning.ai/chatgpt-building-system/lesson/1/introduction)

# LangChain

[DLAI - Learning Platform Beta](https://learn.deeplearning.ai/langchain/lesson/1/introduction)
