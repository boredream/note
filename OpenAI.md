

https://platform.openai.com/

https://platform.openai.com/docs/api-reference



## 垂直数据语料库

### 为什么？

* ChatGPT只到2021年数据，最新的没有
* ChatGPT的训练数据是公开的部分，如果我想针对公司/个人做垂直化的专有数据库，没法做到
* ChatGPT可以记住上下文，但有限制。且本身api的token长度也有限制



### 怎么做？

官方提供方案里找

#### fine-tuning

Fine-tuning是指使用一个已经训练好的模型，将其进一步训练以适应新的任务或数据集。具体来说，将一个已经在一个大型数据集上训练好的模型（例如预训练的语言模型），在一个特定的任务或数据集上进行微调，以提高其性能。

在fine-tuning过程中，通常会使用较小的学习率，以确保新的数据不会太快地破坏原有的权重，同时也可以在更少的训练步骤内达到良好的性能。

Fine-tuning是机器学习和深度学习中常用的技术之一，它可以加快模型训练的速度和提高模型性能，同时也能够更好地满足特定任务的需求。



问题

暂只支持 GPT-3



费用

| Model   | Training            | Usage               |
| ------- | ------------------- | ------------------- |
| Ada     | $0.0004 / 1K tokens | $0.0016 / 1K tokens |
| Babbage | $0.0006 / 1K tokens | $0.0024 / 1K tokens |
| Curie   | $0.0030 / 1K tokens | $0.0120 / 1K tokens |
| Davinci | $0.0300 / 1K tokens | $0.1200 / 1K tokens |



> 和我尝试路线一样，先fine-tuning，后 langchain+embedding
>
> https://dagster.io/blog/chatgpt-langchain



#### Embedding

https://platform.openai.com/docs/guides/embeddings





## 完整方案

OpenAI 的 API 只提供单次查询接口，因此如果要携带context信息的话，也需要在这个查询接口中自己提供。

如果我有海量历史数据希望作为参考的话，在单次请求中肯定是不能把这些历史数据都带上的，一来 API按文字量收费、二来API有数量上限限制。



因此，需要一个自己的私有服务，保存所有的存量信息。它可以根据问题，自己去存量信息中找到相关文本，挑出来这一部分作为context，再加上问题本身一起发给OpenAI的API，最终得到数据。



### 举例

借助 langchain 库

```python
from langchain.llms import OpenAI
from langchain.chains.qa_with_sources import load_qa_with_sources_chain
from langchain.docstore.document import Document
import requests

# get_wiki_data 略，是从wiki上获取文本信息。这里获取了多篇文档
sources = [
    get_wiki_data("Unix", True),
    get_wiki_data("Microsoft_Windows", True),
    get_wiki_data("Linux", True),
    get_wiki_data("Seinfeld", True),
]

# 需要提前配置 OPENAI_API_KEY 的环境变量，填写自己的key
# 把数据喂给langchain封装的 qa_with_sources ，并传入OpenAI这个大语言模型
chain = load_qa_with_sources_chain(OpenAI(temperature=0))

# 发起请求，参数分别是原始数据，和你提的问题
def print_answer(question):
    print(chain({
        'input_documents': sources,
        'question': question,
    }, return_only_outputs=True)['output_text'])


# question = 'Who were the writers of Seinfeld?"'
# print_answer(question)
```



假设没有langchain，最直接的做法是把获取到的所有原始数据+问题一股脑直接给OpenAI的API，但量过大



而langchain会对数据进行加工，判断你的问题需要检索哪几个文档，然后只把需要的一个或多个文档作为参考语料库+问题一起发给OpenAI，最后获得答案



---



上述方案是每次读取文件，然后检索再发给OpenAI。

正常情况下我们会把历史数据保存在本地，但直接保存文本文件每次提交前检索的话，效率不高。



应该把数据转换成OpenAI生成的向量数据保存。

https://platform.openai.com/docs/guides/embeddings/how-can-i-retrieve-k-nearest-embedding-vectors-quickly

按照官方的最佳方案，你可以先用OpenAI的Embedding类，把文本转换成向量，再用自己的向量数据库保存。当然，因为OpenAI Token限制问题，发送文本前需要对文本进行分段，且对于中文信息还需要进行分词等预处理。





