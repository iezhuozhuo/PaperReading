### Pretrain Model for Response Generation

####  DIALOGPT : Large-Scale Generative Pre-trainingfor Conversational Response Generation

##### 摘要

DIALOGPT是在2005~2017的Reddit评论链中提取的147M个对话上进行训练的，扩展了Hugging Face PyTorch Transformer ，使其在单轮对话的自动评估和人工评估方面都达到了接近人类表现的性能。生成了比baseline systems更高相关性、丰富度和上下文一致性的回复。

##### 介绍

OpenAI的GPT-2表明：在非常大的数据集上训练的Transformer 模型可以捕获文本数据中的长期依赖关系，并生成流畅、词汇多样和内容丰富的文本。这样的模型有能力捕捉具有细粒度的文本数据，并产生高分辨率的输出，从而更好地模仿人类编写的真实文本。

自然语言回复生成是文本生成的一个子类别，目标都是生成与提示句相关的更自然的文本。但是，模拟对话的独特挑战在于人类对话包含了两个参与者可能相互竞争的目标（encapsulates the possibly competing goals of two participants），本质上更多样化。因此，与其他文本生成任务（如NMT神经网络机器翻译，文本总结等）相比，一对多问题（one-to-many problem）更大。而一般来说，人类的对话也比较非正式、嘈杂，而且，当以文本聊天的形式出现时，往往包含非正式的缩写或句法/词汇错误。

大多数开放域回复生成系统都存在内容或样式不一致。缺乏长期的语境信息，以及回复过于平淡（安全回复）的问题。虽然这些问题可以通过专门为提高信息内容而设计的建模策略来缓解，但基于Transformer 的结构，如GPT-2，使用多层自注意力机制，采用全连接对跨越注意力（allow fully-connected cross-attention to the full context）进行高效计算来关注整个上下文，似乎是探索更一般解决方案的自然选择。

Transformer 模型可以更好地保存长期依赖信息，从而提高内容的一致性。同时也因为深层结构有着更大的模型容量，可以比RNN模型更好地利用大规模数据集。

与GPT-2的对比

- 相同点：DIALOGPT也是一个自回归语言模型，采用多层Transformer 作为模型体系结构…
- 不同点：是对在从Reddit评论链中提取的大规模对话对/会话上进行训练的。作者推测这样可以更好地获取P(Target,Source)在具有更细粒度的对话流中的联合分布（This should enable DIALOGPT to capture the joint distribution of P(Target,Source) in conversational flow with finer granularity）。

##### 数据集

Reddit评论可以自然地扩展为树结构的回复链，因为回复一个线程的线程形成了后续线程的根节点。我们将每条路径从根节点提取到叶节点，作为包含多轮对话的训练实例。

对以下情形进行过滤：

- 对话中存在URL
- 含有三个以上的单词重复
- 如果回复中不包含至少一个 top-50最频繁使用的英语单词(eg., “the”, “of”, “a”)，这代表着可能不是一个英文句子
- 回复包含特殊标记，如“[”或“]”，因为这可能是标记语言。
- 提问或回复超过200个单词
- 存在黑名单中的敏感词

##### 模型结构

在采用通用Transformer 结构的GPT-2架构上利用堆叠的masked多头自注意层来训练大量的网络文本数据。

###### **Tricks**

- layer normalization
- 根据我们改进的模型深度计算的初始化方案（a initialization scheme that accounts
  for model depth that we modified）
- 字节对编码（byte pairencodings）
- 互信息最大化:计算maximum mutual information (MMI) 来应对生成的回复信息量低的问题。采用一个预训练的反向模型来从给定的答句预测出问句P(Source|target)。先使用top-K采样来生成一系列假设，然后再利用$P(Source|Hypothesis)$来对假设进行重排，以此来惩罚安全回复。













