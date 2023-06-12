---
{"dg-publish":true,"permalink":"/programming/nlp/llm/"}
---


# NLP 沿革

[国泰君安-计算机行业专题研究：ChatGPT研究框架（2023），生成式AI迎来拐点，商用落地前景可期](LLM/国泰君安-计算机行业专题研究：ChatGPT研究框架（2023），生成式AI迎来拐点，商用落地前景可期.pdf)

![](/img/user/programming/nlp/LLM/image-20230611200223435.png)

![](/img/user/programming/nlp/LLM/image-20230611200252973.png)

![](/img/user/programming/nlp/LLM/image-20230611200326183.png)

![](/img/user/programming/nlp/LLM/image-20230611200349219.png)

# GPT 沿革

![](/img/user/programming/nlp/LLM/image-20230611200017576.png)

## GPT1

![](/img/user/programming/nlp/LLM/image-20230611200506383.png)

![](/img/user/programming/nlp/LLM/image-20230412194044972.png)

文字接龙, 一个字一个字往外出

谷歌的方法是完形填空, 需要上下文. gpt 则是接龙, 只需要上文

无标注的预训练模型 => 本地微调 Fine-tuning (半监督学习)

1. 不知道用怎么样的优化函数, 模板函数和子任务
2. 如何把学习到的内容迁移到子任务上

![](/img/user/programming/nlp/LLM/image-20230412194410240.png)

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

![](/img/user/programming/nlp/LLM/image-20230425193832522.png)

## GPT2

![](/img/user/programming/nlp/LLM/image-20230412194444680.png)

去掉模型的微调和下游任务的, 无监督学习, zero-shot, 做下游任务不作调整

用自然语言本身做分隔符, 避免引入特殊符号导致对模型的微调. => Prompt

用 reddit

无限的输入, 就有机会回答出来, 不需要本地微调, 也就是常说的力大砖飞

![](/img/user/programming/nlp/LLM/image-20230412194602518.png)

根据自己已有的知识, 找出哪个是斑马

依赖优质的语料

![](/img/user/programming/nlp/LLM/image-20230412194751477.png)

prompt 提示词

![](/img/user/programming/nlp/LLM/image-20230412194850068.png)

## GPT3

![](/img/user/programming/nlp/LLM/image-20230412195026047.png)

![](/img/user/programming/nlp/LLM/image-20230412195059190.png)

![](/img/user/programming/nlp/LLM/image-20230425200328481.png)

![](/img/user/programming/nlp/LLM/image-20230425200430117.png)

![](/img/user/programming/nlp/LLM/image-20230611200610271.png)

## InstructGPT (GPT-3.5)

![](/img/user/programming/nlp/LLM/image-20230412195320792.png)

![](/img/user/programming/nlp/LLM/image-20230412195403374.png)

![](/img/user/programming/nlp/LLM/image-20230412195639382.png)

![](/img/user/programming/nlp/LLM/image-20230412195713248.png)

![](/img/user/programming/nlp/LLM/image-20230412195803371.png)

![](/img/user/programming/nlp/LLM/image-20230412195855819.png)

![](/img/user/programming/nlp/LLM/image-20230412195930265.png)

![](/img/user/programming/nlp/LLM/image-20230611200659010.png)

# Fine-tuning

对模型进行精调

![](/img/user/programming/nlp/LLM/image-20230412200748640.png)

![](/img/user/programming/nlp/LLM/image-20230412200804492.png)

![](/img/user/programming/nlp/LLM/image-20230412200834713.png)

# Prompt

![](/img/user/programming/nlp/LLM/image-20230412201143948.png)

提示词越具体, 回答越符合

![](/img/user/programming/nlp/LLM/image-20230412201314818.png)

prompt 也是一种经验准测, 都是大家总结出来的

# Fine-tuning 和 Prompt

![](/img/user/programming/nlp/LLM/image-20230412201515286.png)

Finetuning: 是预训练语言模型迁就各种下游任务

Prompting: 是人迁就模式

![](/img/user/programming/nlp/LLM/image-20230412201602384.png)

prompt 并不是最终形态, prompt 工程师可能很快就会消退.

fint-tuning 会改变模型, prompt 并不会

# 词向量检索

词向量检索，也称为向量空间模型检索，是一种文本检索方法，其基本思想是将输入的文本转换为向量形式，并使用数学模型对文本相似度进行计算，从而寻找匹配的文本集合。

通常情况下，词向量检索分为以下几个步骤：

文本预处理：对原始文本进行分词、去除停用词等操作，以得到可供后续处理的单词列表。

向量表示：利用某种算法（如 word2vec）将每个单词映射为一个向量。这些向量通常具有较高的维数，并包含了单词的语义信息。

查询处理：将用户输入的查询文本转换为向量表示，与预处理过的文本向量进行相似度计算，以得到最相关的文本。

结果呈现：将计算出来的相似度得分排列，呈现给用户最相关的文本列表。

词向量检索在自然语言处理领域被广泛应用，常用于搜索引擎、推荐系统等场景。它能够通过对文本的向量化处理，更准确地衡量文本之间的相似度，达到更精确和快速的文本检索效果。

[Improving Document Retrieval with Contextual Compression](https://blog.langchain.dev/improving-document-retrieval-with-contextual-compression/)

增强词向量检索能力

# Embedding

OpenAI 的 Embedding 是一种将词语或句子转换成密集向量的技术。这些向量可以用于自然语言处理任务，如文本分类，情感分析和机器翻译等。

在 OpenAI 的 Embedding 中，每个单词或句子都会被映射到一个向量空间中的点。与传统的 one-hot 编码不同，Embedding 可捕获词语之间的相似性和关系，并使得机器学习算法能够更好地理解自然语言表达的含义。

例如，在情感分析中，通过使用 Embedding，我们可以将具有相似情感倾向的单词或短语映射到相似的向量空间中的位置。这样，我们就可以使用这些向量来训练机器学习模型，以帮助它们更准确地预测文本的情感。
