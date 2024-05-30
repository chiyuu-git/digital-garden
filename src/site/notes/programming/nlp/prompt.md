---
{"aliases":["如何与gpt交谈"],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-06-11-Sun, 7:19:32 pm","date-modified":"2023-06-12-Mon, 10:23:24 am","permalink":"/programming/nlp/prompt/","dgPassFrontmatter":true}
---


# Prompt 技巧

这里的 Prompt 是一个比较长的描述，所以可以提出了第一个 Prompt 的技巧：`撰写明确而具体的指示`。这个 Prompt 的目的是给予 ChatGPT 要实现功能的背景及功能的大致实现思路，这样 ChatGPT 就可以根据这个背景去实现功能了。

在第二段中，我还要求它给予详细的实现步骤，并且不需要考虑初始化项目的步骤，因为这不是一个新项目，我们需要在已有代码的基础上去实现新的功能。所以这里的 Prompt 还有一个技巧：`给模型思考的时间`。**Step by step**是一个具有魔力的 Prompt 词汇，可以让 ChatGPT 花更多的时间去计算思考这个问题，而不是直接给出一个答案。

`迭代Prompt`。如果你发现 ChatGPT 没有正确的理解你的需求，那么你可以继续在这个会话中微调你的 Prompt，直到 ChatGPT 正确的理解你的需求为止。

这里可以提出第四个 Prompt 的技巧：`放弃方案`。如果你发现 ChatGPT 没有给出一个满意的答案，那么你可以放弃这个方案，直接开启新的会话探索另外一种方案。

我们又可以提出第五个 Prompt 的技巧：`给出代码片段`。如果你发现 ChatGPT 给出的代码不完整，那么你可以给出一个代码片段，让 ChatGPT 帮你完成这个代码片段。

我们又可以提出第六个 Prompt 的技巧：`代码解释`。如果你发现 ChatGPT 给出的代码你难以理解，那么你可以要求它给予完整的解释，或者用更简单的方式重写它的代码。这个过程中，还可以提升自己的技术能力，这也是结对编程的一个好处。

我用到了第七个 Prompt 的技巧：`few-shot`。这个 Prompt 的技巧可大有来头，因为这是 GPT-3 的论文 [Language Models are Few-Shot Learners](https://arxiv.org/abs/2005.14165) 标题的由来。如果你发现 ChatGPT 没有给出一个满意的答案，那么你可以给出一个更具体的例子，让 ChatGPT 帮你完成这个例子。

# Prompt 技巧

## 先举例再模仿

自然生长出现的能力: 理解了什么是例子, 可以去模仿

## 分步引导

gpt 生长出了思维链路, 逐步引导可以加增加正确率

[基于大模型的优质Prompt开发课 - 飞桨AI Studio](https://aistudio.baidu.com/aistudio/course/introduce/28604)

# 结对编程

在上面介绍了如何与 ChatGPT 以驾驶员观察者模式的方式结对编程，当然也可以以乒乓模式的方式结对编程，比如在 [这个对话](https://gist.github.com/madawei2699/d80d35f43df478c483ce1e5ed050114b#you-38) 里给 ChatGPT 提供一段函数代码，然后让它写单元测试。这个过程也可以反着来，这样就达到类似于乒乓模式的结对编程了。

使用 open api 来编写单元测试

[openai-cookbook/Unit\_test\_writing\_using\_a\_multi-step\_prompt.ipynb at main · openai/openai-cookbook · GitHub](https://github.com/openai/openai-cookbook/blob/main/examples/Unit_test_writing_using_a_multi-step_prompt.ipynb)

# Prompt 类应用

Prompt 是 GPT 技术的过渡产物，也许在未来我们都不需要 Prompt 了，但目前这也算 chatGPT 衍生出来的一类应用。比如收集好的 Prompt 然后分享给其他人使用，这类应用代表有：[OpenPrompt](https://openprompt.co/) 及很多 [awesome chatgpt prompts](https://github.com/f/awesome-chatgpt-prompts) 类的开源 Markdown 项目。
