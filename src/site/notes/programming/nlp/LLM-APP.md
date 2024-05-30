---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-06-11-Sun, 7:03:00 pm","date-modified":"2023-06-13-Tue, 3:13:31 pm","permalink":"/programming/nlp/LLM-APP/","dgPassFrontmatter":true}
---


gpt 的衍生应用非常的多, 一些好用的应用暂时在这里记录一下

# 阅读助手

| 应用      | 用途          | 官网 |
| --------- | ------------- | ---- |
| chatpdf   | pdf summary   |      |
| chatpaper | paper summary |      |

 那个 AIREADER 简直垃圾, 不太好用

# 翻译

想要一款基于 gpt 3.5 的翻译工具

因为翻译网页和文章会占用大量的 token , 估计很难找到免费好用的

# 代码分析: 导入代码

导入过程很简单，直接使用这个 [GithubRepositoryReaderDemo.ipynb](https://github.com/jerryjliu/llama_index/blob/main/examples/data_connectors/GithubRepositoryReaderDemo.ipynb) 里的代码即可，导入我们要分析的项目代码

[GitHub - jerryjliu/llama\_index: LlamaIndex (GPT Index) is a data framework for your LLM applications](https://github.com/jerryjliu/llama_index)

# 生成 Mock 数据

prompt 中指定生成的格式即可
