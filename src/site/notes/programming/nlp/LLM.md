---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-04-12-Wed, 7:31:39 pm","date-modified":"2023-06-13-Tue, 10:35:10 am","permalink":"/programming/nlp/LLM/","dgPassFrontmatter":true}
---


# NLP 沿革

[ChatGPT背后的语言模型简史 · BMPI](https://www.bmpi.dev/dev/deep-learning/nlp-language-models/)

![](/img/user/programming/nlp/LLM/image-20230611200223435.png)

## 机器学习

要让机器能够理解人类语言，目前常用的方法是从机器学习开始。传统的机器学习方法有先天假定，需要人工确定数据特征（即特征工程），这可能需要大量的人工工作，并且存在错误假设的可能性。

在机器学习领域中，深度学习是一种基于神经网络的机器学习技术。其中的主要特点是能够自动学习数据的特征，通过将特征学习任务交给模型进行训练来实现自动学习过程。

机器学习一开始有很多路线，不过逐渐人们发现深度学习的效果非常好，很多机器学习领域要解决的问题都可以用深度学习解决。

## 神经网络

![](/img/user/programming/nlp/LLM/image-20230612103415816.png)

神经网络是一种模拟人脑神经元网络的机器学习模型，它最早于 1943 年被提出，它使用数学模型对神经元进行建模，神经元是人脑的基本单元，它接收输入信号，经过处理后产生输出信号，神经元之间通过突触连接，突触可以增强或抑制神经元之间的连接强度。

最简单的感知器模型如下所示：

![](/img/user/programming/nlp/LLM/image-20230612103445381.png)

如果用数学公式来表达如下所示：

![](/img/user/programming/nlp/LLM/image-20230612103517146.png)

看起来不可思议，人类的智慧竟然可以用这么简单的公式来表达。这个公式的意思是，如果输入的两个信号的加权和大于阈值，那么输出 1，否则输出 0。这个公式就是感知器的数学模型，它是最早的神经网络模型。

单个的神经元很简单，但通过多个神经元的组合，就可以构成复杂的神经网络，这样的神经网络就可以解决复杂的问题。如下图所示是一个简单的神经网络：

![](/img/user/programming/nlp/LLM/image-20230612103559303.png)

如何理解这个神经网络呢？首先，这个神经网络有 3 层，第一层是输入层，第二层是隐含层，第三层是输出层。输入层有两个神经元，隐含层有两个神经元，输出层有一个神经元。每个神经元都有一个阈值，每个连接线都有一个权重，这些权重和阈值都是需要训练的参数。

这个简单的神经网络通过叠加多个隐含层，就可以完成复杂的人工智能任务，比如识别图片中的手写数字，这个流程如下：

1. 第一层输入层接收图片的像素点作为输入信号，比如图片的大小是 28x28，那么输入层就有 784 个神经元；
2. 隐含层将输入信号进行处理，层层传递后给输出层产生信号；
3. 输出层的信号就是图片的内容，比如输出层有 10 个神经元，那么输出层的信号就是 10 个数字，每个数字代表图片识别的概率，概率最大就代表整个神经网络的识别结果。

这个流程看起来很简单，但是神经网络的训练过程非常复杂，需要大量的数据和计算资源，所以在神经网络被提出后，很长一段时间没有得到应用。而且为了取得好的效果，神经网络的层数就需要很多，深度学习就是指神经网络的层数很多，所以深度学习的训练过程更加复杂。

## 深度学习

![](/img/user/programming/nlp/LLM/image-20230612104004575.png)

深度学习就是加深神经网络的层数，当然在特定领域的处理中，除了加深层数还需要处理特定的问题，比如在自然语言处理领域，就需要处理文本的序列问题，这个时候就需要采用 RNN、LSTM、GRU、seq2seq、Transformer 等模型。

这些模型并不是独立的，而是相互关联的，比如 seq2seq 模型就是由 RNN 和 LSTM 组成的，Transformer 模型就是由 seq2seq 模型演变而来的。

当 ChatGPT 与人交流时，它会根据上下文不断预测并生成下一个单词。这个上下文包括了之前生成的所有单词，而不是一次性生成整个句子。我们可以通过如下数学公式来表示这个过程：

![](/img/user/programming/nlp/LLM/image-20230612104053371.png)

该公式表示生成整个句子 w1​,w2​,…,wn​的概率等于每个单词的生成概率之积（联合概率），其中每个单词的概率都是基于其前面所有单词来计算的。以上公式能给出单词序列发生的概率，这意味着语言模型可以通过概率来评估一个单词序列发生的可能性，也意味着可通过此概率评估一个句子在多大程度上是自然的单词序列。通过这种概率的计算，让 ChatGPT 这类语言模型能够产生连贯而自然的对话回复。

### 从文本到词向量 -word2vec

从上面神经网络的例子中，我们可以看到神经网络的输入和输出都是数字，而且神经网络的每个神经元都是一个数字，这些数字都是浮点数，所以神经网络的输入和输出都是浮点数。

但自然语言处理的输入输出都是文本，所以需要将文本转换为数字，这个过程就是文本的向量化，也就是将文本转换为向量。文本向量化的方法有很多，比如 one-hot、TF-IDF、word2vec 等。

word2vec 是一种用于生成词向量的神经网络模型，它最早于 2013 年被提出。该模型主要用于自然语言处理任务中，如文本分类、文本生成等。相比于 one-hot 和 TF-IDF，word2vec 可以将文本中的每个单词转换为一个固定长度的向量，这个向量可以用于神经网络的输入。

有趣的是，词向量表示只是 word2vec 模型的副产品，word2vec 模型的主要目的是通过训练神经网络来学习词向量，而不是生成词向量。word2vec 模型通过训练神经网络来预测输入文本序列中下一个单词，当模型训练的能成功预测下一个单词时，说明神经网络已经学习到了文本中单词的语义信息，这个时候神经网络的隐含层（输入权重）就是输入文本序列的词向量，这也意味着在不同的语料库中，相同单词的词向量是不同的，但是相似的单词的词向量是相似的。

### 循环处理文本序列 -RNN

将文本转换为词向量后，另一个问题是目前神经网络都是前馈神经网络，它们都是将输入数据从输入层传递到输出层，而没有反馈，所以无法处理序列数据，比如文本序列。于是就有了循环神经网络（RNN）。

RNN 模型指的是循环神经网络（Recurrent Neural Network），它最早于 1982 年被提出。该模型主要用于自然语言处理任务中，如文本分类、文本生成等。循环神经网络通过使用循环结构，将当前时刻的输出作为下一时刻的输入，从而实现对序列数据的处理。

循环神经网络的每个神经元都有两个输入，一个是当前时刻的输入，一个是上一时刻的输出，这样就可以将序列数据的信息传递到下一时刻。循环神经网络的结构如下图所示：

![](/img/user/programming/nlp/LLM/image-20230612104336193.png)

如上图所示，循环神经网络可以从输入序列 X 中逐个读取输入 Xt​，并将当前时刻的输入 Xt​和上一时刻的输出 Ht−1​作为输入，然后输出当前时刻的输出 Ht​，这样就可以将序列数据的信息传递到下一时刻。

不过原始的 RNN 模型存在记忆消失的问题，在长序列的情况下，模型无法学习到序列中较早的信息（可以简单理解为 RNN 在输入序列的循环过程中，在每次的输入转换中有信息的损失），所以后来又有了 LSTM 和 GRU 模型，这些模型通过使用门控结构（输入门、遗忘门与输出门），解决了 RNN 的记忆问题。

> 神经网络模型经常使用反向传播和梯度下降算法进行训练，以便更新模型参数来获得更接近真实值的输出结果。然而，由于存在梯度消失和梯度爆炸的问题，导致模型无法正确训练和收敛。在 RNN 模型中，这些问题尤为突出。
>
> 梯度消失是指在反向传播过程中，梯度值逐渐减小，最终使得模型无法训练。与之相对，梯度爆炸是指反向传播中梯度值变得极大，同样也会导致模型无法训练。了解这些问题后，我们需要寻找一些方法来解决它们。例如，可以通过采用梯度裁剪技术来解决梯度爆炸的问题；使用 LSTM 等网络结构可以有效缓解梯度消失的问题。此外，ReLU 激活函数也被广泛使用，因为它能够防止梯度消失并加快训练速度。

### 从序列到序列 -seq2seq

有了 RNN 模型，就可以处理序列数据了，但是 RNN 模型只能处理单个序列，而不能处理两个序列之间的关系，于是就有了 seq2seq 模型。

seq2seq 模型指的是序列到序列模型（Sequence-to-Sequence Model），它最早于 2014 年被提出。该模型主要用于自然语言处理任务中，如机器翻译、对话系统等。

在传统的机器翻译方法中，通常采用基于短语或句法分析的方法将源语言句子转化为目标语言句子。这种方法存在大量的人工特征工程和规则定义，效果依赖于语言专家和领域知识，难以适应不同的语言和领域。

seq2seq 模型通过使用编码器 - 解码器架构，将两个序列之间的关系转化为两个向量之间的关系，从而实现对两个序列之间的关系进行建模。在不需要手工特征工程的情况下，自动地学习源语言到目标语言之间的映射关系。其中，编码器将源语言序列映射到一个固定长度的向量表示，解码器利用这个向量表示生成目标语言序列。这种端到端的训练方式具有很强的可扩展性和泛化能力，并且随着数据量的增加，模型表现也会逐渐提升。

因此，seq2seq 模型既解决了传统机器翻译方法中需要注重规则和特征工程的缺陷，同时也支持了更加复杂和灵活的自然语言处理任务。

它的架构如下所示：

![](/img/user/programming/nlp/LLM/image-20230612104600823.png)

如上图所示，这是一个翻译汉语到英文的语言任务，输入的是汉语句子你好，输出的是英文句子 Hello。首先，将输入的汉语句子经过编码器，得到一个固定长度的向量表示，然后将这个向量表示输入给解码器，解码器将这个向量表示转化为英文句子。

以下是 Seq2Seq 模型在生成文本时的训练与生成流程：

1. 输入数据预处理: 首先，需要将输入的原始文本经过分词、去除停用词等预处理步骤，并将其转化为词向量表示，得到输入序列 `X=(x1​,x2​,…,xT​)`
2. 编码器: 将输入序列 X 输入给编码器（Encoder），编码器使用 RNN（通常采用 LSTM 或 GRU）将词向量序列转化为单个**上下文向量** z，即最后一个时间步的隐藏状态。这个向量包含所有输入信息的综合表示，它将被用于之后的解码器中。
3. 解码器: 通过读取上下文向量 z，并且使用另一个 RNN 来生成输出序列 `Y=(y1​,y2​,…,yT′​)`. 这里的输出序列长度不一定和输入序列相同。例如，在翻译中，从英语到意大利语的句子长度可能会改变。
4. 训练: 对于每个目标输出序列′Y′（即参考答案），训练的目的是尽可能地使生成的输出序列 Y 最大程度地接近′Y′。因此，我们需要定义一个**损失函数**，用来衡量以 Y 作为输出时产生的误差，通常使用**交叉熵损失函数**。
5. 生成: 在生成时，我们使用训练好的编码器和解码器来处理新的输入，并生成一个输出序列。具体地，首先将输入字符串经过相同的预处理转化为词向量序列 X。然后，将 X 拿去给编码器，得到上下文向量 z。最后，利用解码器对 z 进行解码，即可得到生成的输出序列 Y。

seq2seq 模型的优点是可以处理变长的序列，但是它也存在一些缺点：

1. 编码器只能通过一个固定长度的向量来表示输入序列，这个向量包含了输入序列的所有信息。因此，编码器的向量表示可能会存在信息损失，这会影响到解码器的性能。
2. 编码器和解码器都是使用 RNN 来实现的，因此它们都存在梯度消失和梯度爆炸的问题。这会导致模型的训练变得困难，同时也会影响到模型的性能。
3. 编码器和解码器都是使用 RNN 来实现的，因此它们都是串行的结构，无法并行计算，导致训练速度较慢。

seq2seq 模型的缺点主要是由于 RNN 的缺点导致的，因此我们需要一种新的模型来解决这些问题，这就是 Transformer 模型。

### 注意力是全部 -Transformer

RNN 网络模型的问题主要在于，串行处理输入序列，无法并行计算，同时也无法捕捉到输入序列中的长距离依赖关系。而 Transformer 模型则是完全基于注意力机制来实现的，因此它不仅能够并行计算，而且能够更好地捕捉输入序列的长距离依赖关系，从而提升模型的性能。

在 RNN 中，序列中的每个单元都是按顺序依次进行计算的，后一个单元的计算需要等待前一个单元计算完成后才能开始，因此无法并行化计算。而在 Transformer 中，自注意力机制的计算可以并行计算，因为每个单词的表示向量是独立计算的，它们的计算不需要等待其它单词的计算完成，因此可以同时进行。此外，注意力权重的计算也可以并行化，因为每个单词与其它单词的相似度可以同时计算，因此 Transformer 的自注意力机制可以更快地处理大规模序列数据。

对比 RNN，举例来解释 Transformer 的注意力机制：

假设我们有一组输入序列，包含 5 个单词：“I”, “like”, “to”, “eat”, “pizza”。

1. 我们将每个单词转化为向量表示。可以使用词向量来表示每个单词。
2. 将每个单词向量应用于三个全连接层，以获得表示向量。每个全连接层都采用不同的权重矩阵，用于捕捉不同方面的单词特征。
3. 为了计算向量之间的相似性，我们将每个表示向量与相邻单词的表示向量计算点积。对于句子中的每个单词，它将与前后单词的表示向量进行点积。
4. 我们将每组点积值传递到 Softmax 函数中，以得出每个单词与相邻单词的注意力权重。这个步骤计算了每个单词与相邻单词的重要性，并为序列中的每个单词分配了注意力权重。
5. 将每个单词向量与其对应的注意力权重相乘，然后将它们相加。这将形成一个自注意力池化向量，该向量是所有输入单词的加权和。这个自注意力池化向量可以更好地捕捉单词之间的语义关系，同时维护整个输入序列的完整性。

通过这种方式，自注意力机制允许模型更加全面地考虑输入序列中不同单词之间的关系，并获取单词之间的上下文信息，从而为序列到序列转换任务提供更好的表现力和准确性。

Transformer 模型的架构如下所示：

![](/img/user/programming/nlp/LLM/image-20230612105537264.png)

如上图所示，Transformer 模型的编码器和解码器都使用了自注意力机制，它们的输入都是整个序列，而不会像 RNN 那样串行处理序列。因此，Transformer 模型可以并行计算，从而提升模型的训练速度。

Transformer 模型将语言模型的训练带入了大语言模型时代，因为它的训练计算复杂度不会随着输入序列的长度而指数增加，因此可以训练更大的模型，从而提升模型的性能。

同时随着 GPU 计算能力的提升，Transformer 模型的训练时间也大大缩短，因此 Transformer 模型的训练成本也大大降低，于是一个大语言模型的时代拉开了序幕。

![](/img/user/programming/nlp/LLM/image-20230611200252973.png)

![](/img/user/programming/nlp/LLM/image-20230611200326183.png)

![](/img/user/programming/nlp/LLM/image-20230611200349219.png)

# GPT 沿革

[国泰君安-计算机行业专题研究：ChatGPT研究框架（2023），生成式AI迎来拐点，商用落地前景可期](LLM/国泰君安-计算机行业专题研究：ChatGPT研究框架（2023），生成式AI迎来拐点，商用落地前景可期.pdf)

![](/img/user/programming/nlp/LLM/image-20230612105724298.png)

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

### Decoder

GPT 使用了 Transformer 模型的右侧解码器，因为其被证明是一种非常适合自然语言处理任务的模型，并且能够在处理长序列时表现出色。

Transformer 模型中有两个主要的组件：编码器和解码器。而编码器被设计用于从输入序列中提取信息，而解码器则可从该信息生成输出序列。 在机器翻译等任务中，需要将源语言序列（即输入）转换为目标语言序列（即输出）。因此，传统的神经机器翻译系统使用编码器 - 解码器架构来进行这些任务。

然而，在语言生成任务如文本生成、对话模型等方面，只需要根据历史上下文生成新的单词/字符串。 在这些任务中，只需要生成一个序列，而不是翻译两个不同语言之间的不同的序列信息。因此，使用 Transformer 模型中解码器部分的权重，可以更好地拟合文本生成类任务，同时也可以更好地利用它的多头自注意力机制和残差连接来处理长序列，并且可以解决学习时间步骤之间长距离依赖关系的挑战。

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

# 损失函数

用于评判好坏的标准

例如：平均绝对误差 MAE、均方误差 MSE、最大似然法 MLE、交叉熵等等

GPT 将输入序列中的每个位置视为一个预测目标，然后使用自回归的方式逐个预测下一个词。对于每个位置，模型会输出一个概率分布，表示下一个可能的词及其对应的概率。损失函数的目标是最大化实际下一个词的概率，即最大化正确预测的概率。因此可以说，GPT 使用最大似然估计作为损失函数，并通过交叉熵来度量模型的预测与实际结果之间的差异，以优化模型参数。

# 优化算法

找出最好的函数——优化算法 Optimization

例如：梯度下降 Gradient Descent（Adam、AdamW、...）、遗传算法 Genetic Algorithm 等等

![](/img/user/programming/nlp/LLM/image-20230613103446096.png)

![](/img/user/programming/nlp/LLM/image-20230613103450597.png)

梯度下降算法基本思想：计算损失函数关于参数的梯度，然后沿着梯度的反方向更新参数，以此来逐步减小损失函数的值。这个过程会持续进行，直到达到一个足够小的误差或者迭代次数达到预设值。

在 GPT 的训练中，损失函数通过反向传播算法计算梯度，然后使用 Adam 优化器进行更新，Adam 是基于梯度下降的优化算法，但引入了动量和自适应学习率两个改进，使得其在许多情况下的效果优于简单的梯度下降。
