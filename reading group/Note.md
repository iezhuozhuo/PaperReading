### [A Knowledge-Enhanced Pretraining Model for Commonsense Story Generation](https://arxiv.org/pdf/2001.05139.pdf)

#### Filed

常识故事生成。假设一个单句故事从上下文开始，模型应该继续用合理的情节完成一个单句故事。

#### Problem

通常做法是选择一系列的事件，以合理的逻辑或情节形成一个故事。但是**输入信息有限**，预训练model如GPT-2仍然**遭受重复，逻辑冲突，以及在生成的故事中缺乏长期的连贯性**。推测原因：有限的输入信息很难将相关的常识联系起来，理解因果关系，以及以适当的时间顺序规划实体和事件。现有的model **Good at** local coherence，但是**Struggle to**: 规划一个连贯的情节，并在整个故事中保持一个合理的事件顺序，也受限制于generic plot

#### Model

##### Tip

- We  **enhance  GPT-2  with such  knowledge  by  post-training**  the  model  on the  knowledge  examples  constructed  from  these knowledge  bases,  which  can  provide  additional crucial information for story generation.
- we  adopt  multi-task  learning  to  address the problem of **handling causal and temporal dependencies**. 

##### Incorporate knowledge into GPT-2

以前所有工作都假设训练数据和知识库之间存在对齐，造成的结果(1)很难将训练数据中提取出来的事件与KB中存储的事件进行匹配。(2)知识图中多跳三跳的学习和利用，由于规模较大，时间开销较大。(3)大部分知识库三元组没有出现在特定任务的训练数据中，所以那些没有出现的三元组没有被充分利用。

原文隐性的隐入知识(ConceptNet[h r t]\ATOMIC[if-then trip])用于post-train。为了solve上面的problem，将三元组变成句子(通过模板，不直接拼接是因为出现 unknown token或者打破了预训练的句法特征syntactic)。

##### Multi-Task learning

为了improve story的 logic  and  coherence， 设计鉴别器区分fake和true story. Fake story的构建是随机删除，随机替换，打乱顺序--(Top k个句子，但是不包含第一个句子)。

#### Question

- 如何multi-Task learning
- 知识如何选择？怎么有效利用三元组？
  origin paper: we  randomly  selected  stories  and  knowledge sentences for training/validation/test respectively.

### [Pre-training Text Encoders as Discriminators Rather Than Generators](https://arxiv.org/pdf/2003.10555.pdf)

#### Field

改进bert等预训练的MLM任务，通用的text encoder。

#### Problem

由于学习双向表示法，这些掩码语言建模(MLM)方法比传统的语言模型预训练更有效，但由于每个example，model只学习15%的标记，因此会**产生大量的计算成本**。另外因为pretrain阶段使用了人工[MASK]，导致了pretrain和下游的**mismatch**。

#### Model

提出一个新的pretrain task -- replaced token detection。区别于MLM，原文model通过使用来自proposal distribution的采样替换一些token来破坏输入，而这些采样token通常是一个小型掩码语言模型的输出，然后将网络预训练成一个鉴别器，可以预测每一个令牌是原始的还是替换的。这样的关键优势是，模型从所有的输入token中学习，而不仅仅是小的mask-out集，这使得它的计算效率更高。与现有的生成语言表示学习方法相比，**原文的重要claim区分真实数据和具有挑战性的负样本的表征任务具有更高的计算效率和参数效率**。同量级model比较上更快而且下游任务的表现上更好，改进的小model单卡4天可以训练完(与同量级bert比效果更好)。

Model包含generator以及discriminator，generator是一个小型的MLM model。流程是对输入随机mask（15%或者25%），然后使用MLM将其预测出来(利用最大似然函数，仅仅对mask的词处理，原来公式有问题)，可能预测对也可能预测错，这样就得到了corrupted inputs，最后送到鉴别器discriminator里二分类(origin or replace)。















