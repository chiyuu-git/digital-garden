---
{"dg-publish":true,"permalink":"/programming/nlp/chat-gpt/"}
---


# 大型语言模型 LLM

围棋 AI 匹配率

# GPT 背景介绍

![](/img/user/programming/nlp/chat-gpt/image-20230412193812477.png)

## GPT1

![](/img/user/programming/nlp/chat-gpt/image-20230412194044972.png)

文字接龙, 一个字一个字往外出

谷歌的方法是完形填空, 需要上下文. gpt 则是接龙, 只需要上文

无标注的预训练模型 => 本地微调 Fine-tuning (半监督学习)

1. 不知道用怎么样的优化函数, 模板函数和子任务
2. 如何把学习到的内容迁移到子任务上

![](/img/user/programming/nlp/chat-gpt/image-20230412194410240.png)

不同的技术路线的竞争

### 无标注预训练

根据前面的 K 个字, 计算第 K+1 个字最有可能出现的是啥

gpt 就是 transformer 的解码器

### Fine-tuning

让有标注的真实的目标的概率最大化

1. 给一个序列, 预测序列的下一个词
2. 给一个完整的序列, 预测这个词的标号

两个任务加起来, 效果最好

### 子任务

![](/img/user/programming/nlp/chat-gpt/image-20230425193832522.png)

## GPT2

![](/img/user/programming/nlp/chat-gpt/image-20230412194444680.png)

去掉模型的微调和下游任务的, 无监督学习, zero-shot, 做下游任务不作调整

用自然语言本身做分隔符, 避免引入特殊符号导致对模型的微调. => Prompt

用 reddit

无限的输入, 就有机会回答出来, 不需要本地微调, 也就是常说的力大砖飞

![](/img/user/programming/nlp/chat-gpt/image-20230412194602518.png)

根据自己已有的知识, 找出哪个是斑马

依赖优质的语料

![](/img/user/programming/nlp/chat-gpt/image-20230412194751477.png)

prompt 提示词

![](/img/user/programming/nlp/chat-gpt/image-20230412194850068.png)

## GPT3

![](/img/user/programming/nlp/chat-gpt/image-20230412195026047.png)

![](/img/user/programming/nlp/chat-gpt/image-20230412195059190.png)

![](/img/user/programming/nlp/chat-gpt/image-20230425200328481.png)

![](/img/user/programming/nlp/chat-gpt/image-20230425200430117.png)

![](/img/user/programming/nlp/chat-gpt/image-20230412195233550.png)

## InstructGPT (GPT-3.5)

![](/img/user/programming/nlp/chat-gpt/image-20230412195320792.png)

![](/img/user/programming/nlp/chat-gpt/image-20230412195403374.png)

![](/img/user/programming/nlp/chat-gpt/image-20230412195639382.png)

![](/img/user/programming/nlp/chat-gpt/image-20230412195713248.png)

![](/img/user/programming/nlp/chat-gpt/image-20230412195803371.png)

![](/img/user/programming/nlp/chat-gpt/image-20230412195855819.png)

![](/img/user/programming/nlp/chat-gpt/image-20230412195930265.png)

![](/img/user/programming/nlp/chat-gpt/image-20230412200104396.png)

# Fine-tuning

对模型进行精调

![](/img/user/programming/nlp/chat-gpt/image-20230412200748640.png)

![](/img/user/programming/nlp/chat-gpt/image-20230412200804492.png)

![](/img/user/programming/nlp/chat-gpt/image-20230412200834713.png)

# Prompt

![](/img/user/programming/nlp/chat-gpt/image-20230412201143948.png)

提示词越具体, 回答越符合

![](/img/user/programming/nlp/chat-gpt/image-20230412201314818.png)

prompt 也是一种经验准测, 都是大家总结出来的

# Fine-tuning 和 Prompt

![](/img/user/programming/nlp/chat-gpt/image-20230412201515286.png)

Finetuning: 是预训练语言模型迁就各种下游任务

Prompting: 是人迁就模式

![](/img/user/programming/nlp/chat-gpt/image-20230412201602384.png)

prompt 并不是最终形态, prompt 工程师可能很快就会消退.

fint-tuning 会改变模型, prompt 并不会

# 基于 GPT 的产品应用

# OPENAI 的发展历程

up 主视频

![](/img/user/programming/nlp/chat-gpt/image-20230412203702989.png)

closeAI

# Prompt 技巧

## 先举例再模仿

自然生长出现的能力: 理解了什么是例子, 可以去模仿

## 分步引导

gpt 生长出了思维链路, 逐步引导可以加增加正确率

[基于大模型的优质Prompt开发课 - 飞桨AI Studio](https://aistudio.baidu.com/aistudio/course/introduce/28604)