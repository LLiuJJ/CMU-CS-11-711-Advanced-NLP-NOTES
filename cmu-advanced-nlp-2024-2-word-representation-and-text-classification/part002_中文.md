## 管理多语言数据不平衡
为了解决多语言语料库(Multilingual Corpora)中的词汇分布不均问题，一种常见的应对策略是主动调整训练数据的分布。英语等主流语言会被有意降采样(Downsampling)，而低资源语言(Low-Resource Languages)则会被升采样(Upsampling)。这种数据重平衡(Data Rebalancing)确保了分词算法能够为低频语言分配充足的词汇表配额(Vocabulary Quota)，防止其文本被过度切分为低效的字符级序列(Character-level Sequences)，进而避免模型性能受损。
![关键帧](keyframes/part002_frame_00000000.jpg)

## 应对分词任意性与模型可扩展性
BPE 等子词算法(Subword Algorithms)有时会产生不稳定的分词边界，尤其是在频率相近的字符对相互竞争时。为了缓解这种任意性并提升模型的鲁棒性(Robustness)，研究人员通常会采用子词正则化(Subword Regularization)技术。该技术在训练过程中会对多种合理的分词方案进行采样，而非依赖单一的确定性切分(Deterministic Tokenization)。该功能已在广泛使用的 `SentencePiece` 库中原生实现。此外，当需要将已训练的分词器(Tokenizers)扩展至新语言时，一元模型(Unigram Models)的概率特性使得词汇表插值(Vocabulary Interpolation)变得十分直接。另一种实现跨语言迁移(Cross-lingual Transfer)的方法是冻结预训练模型(Pre-trained Models)的主体参数，仅针对新增语言学习全新的词元嵌入(Token Embeddings)。

## 子词与字节级分词的普及应用
子词分词(Subword Tokenization)已成为几乎所有现代自然语言处理(NLP)架构的基础预处理步骤(Preprocessing Step)。尽管早期实现主要侧重于字符级切分，但以 Llama 和 GPT 为代表的当前最先进(State-of-the-Art, SOTA)模型现已普遍采用字节级分词(Byte-level Tokenization)。基于字节的方法将原始字节流(Byte Stream)直接视为离散标记(Discrete Tokens)，从而彻底规避了 Unicode 编码(Unicode Encoding)带来的复杂性。这使得模型能够无缝、统一地处理各类书写系统、Emoji 表情符号及特殊字符，且无需依赖特定语言的显式预处理规则(Explicit Preprocessing Rules)。

## 从稀疏向量到连续嵌入
本讲内容从离散标记化(Discrete Tokenization)过渡到连续词表示(Continuous Word Representations)，标志着语言模型处理文本信息方式的根本性转变。在传统方法中，词汇被编码为稀疏的独热向量(Sparse One-Hot Vectors)，其中仅有一个维度被激活，以对应特定的词表项(Vocabulary Items)。现代方法则用稠密的连续嵌入向量(Dense Continuous Embeddings)取代了这一方式。在连续词袋模型(Continuous Bag-of-Words, CBOW)中，各个词元的嵌入向量会被聚合，并与可学习的权重矩阵(Learnable Weight Matrices)相乘。这使得模型能够捕捉丰富的分布式表示(Distributed Representations)，而不再局限于孤立的离散类别标识。
![关键帧](keyframes/part002_frame_00440306.jpg)
![关键帧](keyframes/part002_frame_00447113.jpg)

## 嵌入空间中的几何直觉与特征表示
连续嵌入(Continuous Embeddings)的核心目标是构建特定的向量空间结构(Vector Space Structure)，使语义或句法相似的词汇在几何空间中彼此邻近。稠密向量中的每一个维度都可视为潜在的隐特征(Latent Features)，有望捕捉到有生性(Animacy)、词性(Part-of-Speech)或情感极性(Sentiment Polarity)等语言学属性。例如，在假设的二维投影(Two-Dimensional Projection)中，某一坐标轴可能用于区分“有生”与“无生”，而与之正交(Orthogonal)的另一坐标轴则编码“积极”与“消极”的情感倾向。这种空间布局使得向量运算(Vector Operations)能够自然映射复杂的语言关系，从而为神经网络(Neural Networks)中的高级语义推理(Advanced Semantic Reasoning)奠定了坚实的数学基础。
![关键帧](keyframes/part002_frame_00530930.jpg)