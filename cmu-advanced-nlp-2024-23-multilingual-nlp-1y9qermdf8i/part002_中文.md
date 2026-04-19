## 共享分词器与跨语言迁移
多语言大语言模型通常依赖于所有支持语言共享的同一个分词器(Tokenizer)。当语言之间存在大量共同词汇时（例如英语和法语），它们的词元嵌入(Token Embeddings)会自然共享，从而促进有效的跨语言知识迁移(Cross-lingual Knowledge Transfer)。然而，共享词表(Vocabulary)对于迁移学习(Transfer Learning)并非绝对必要，研究表明，即使词表没有重叠，模型仍能通过多语言训练获得显著的收益。

![关键帧](keyframes/part002_frame_00000000.jpg)

## 分词差异问题
由于书写系统(Writing Systems)和字符复杂度的差异，不同语言的分词效率(Tokenization Efficiency)存在巨大差异。一项使用 OpenAI GPT-3.5/4 分词器的实验凸显了这种不均衡：一段特定的英文文本仅产生 58 个词元(Token)。当相同内容被翻译成缅甸语时，词元数量激增至 617 个。这是因为分词器对于核心词表(Core Vocabulary)之外的字符默认采用字节级编码(Byte-level Encoding)。由于缅甸语字符在 UTF-8 编码中通常需要三个字节，每个字节都被视为独立的词元，导致序列长度(Sequence Length)大幅膨胀。

![关键帧](keyframes/part002_frame_00058840.jpg)

## 带来的影响：成本、速度与模型容量
分词效率低下不仅会带来实际应用的瓶颈，还会暴露模型架构的缺陷。首先，处理低资源文字(Low-resource Scripts)文本的成本会增加十倍以上，因为 API 调用和算力成本通常按词元数量计算。其次，文本生成速度会相应下降，因为自回归模型(Autoregressive Models)以固定速率逐词元生成。更为关键的是，字节级分词(Byte-level Tokenization)迫使模型将大量模型容量(Model Capacity)浪费在将碎片化的字节重新组合成语义单元(Semantic Units)上，而非处理具有语言学意义的子词(Subwords)。这种碎片化不仅增加了出错概率，还消耗了宝贵的参数容量，最终导致整体准确率和性能下降。

![关键帧](keyframes/part002_frame_00162239.jpg)

## 用于均衡训练的温度采样
为了缓解这些差异，研究人员通常会采用启发式数据采样(Heuristic Data Sampling)方法，其中最常见的是温度采样(Temperature Sampling)。该技术通过计算各语言数据频率的 $1/T$ 次方（其中 $T$ 为温度参数），并重新进行归一化(Normalization)，来调整从不同语言中采样数据的概率。较低的温度会保留原始的长尾分布(Long-tail Distribution)，使采样高度偏向高资源语言(High-resource Languages)。提高温度会使概率分布趋于平坦，从而确保低资源语言被更频繁地采样。在极高温度下，所有语言的采样概率几乎趋于均匀。该策略在模型训练和初始词表构建阶段均可有效应用。

![关键帧](keyframes/part002_frame_00298400.jpg)

## 词表扩展与现实应用
现代开放权重语言模型(Open-weight Language Models)积极采用这些均衡策略，以提升多语言公平性(Multilingual Fairness)。XLM-R 和 Qwen 等架构在构建词表时利用温度采样，刻意对英语等主导语言进行降采样(Downsampling)，同时对低资源语言进行上采样(Upsampling)，以防止其文本被过度碎片化。此外，Qwen 将其词表大小(Vocabulary Size)大幅扩展至超过 20 万个词元（相比之下，标准规模通常为 3.2 万个），从而实现更广泛的文字覆盖，并在多样化的语系中实现更高效、更符合语言学规律的分词。

![关键帧](keyframes/part002_frame_00315640.jpg)

## 批次构建与训练稳定性
在为机器翻译(Machine Translation)或多语言预训练(Multilingual Pre-training)构建批次(Batch)时，通常会避免单个批次内的语言分布完全均匀。按单一语言分组数据或混合比例失衡会导致梯度方差(Gradient Variance)过高，从而使随机梯度下降(Stochastic Gradient Descent, SGD)优化过程不稳定，并引发训练动态的剧烈波动。标准的最佳实践是在批次形成*之前*执行按比例采样(Proportional Sampling)。先进的数据加载器(Data Loaders)通常会预生成、打乱并缓存多样化的批次，以在整个训练过程中保持梯度稳定和学习动态(Learning Dynamics)的一致性。

## 基于梯度对齐的动态数据均衡
除了静态的启发式采样，最新研究还通过梯度对齐(Gradient Alignment)引入了动态数据均衡(Dynamic Data Balancing)方法。该方法在多个特定语言的训练集(Training Sets)上计算梯度，并将其与目标开发集(Development Set)的梯度进行比较。通过测量训练梯度与开发梯度之间的余弦对齐度(Cosine Alignment)，系统可以判断特定语言的数据是否推动模型朝着有益的方向更新。梯度对齐良好的数据会被动态增加权重，而对齐不佳或产生负面影响的数据则会被降低权重。这使得模型能够在训练过程中自动调整采样权重，防止在小规模低资源数据集上发生过拟合(Overfitting)，并且相比固定的温度调节，能够以更高的适应性来优化整体性能。