## 多头注意力中的高效张量计算
直接在三维张量(3D Tensor)上执行注意力计算，比使用标准循环遍历(Iterative Loop)分割后的组件要高效得多。其核心机制通过计算查询向量(Query Vector)与键向量(Key Vector)的点积得到注意力分数(Attention Scores)，经归一化后与值向量(Value Vector)相乘，再对结果求和，从而生成加权表示(Weighted Representation)。 
![关键帧](keyframes/part002_frame_00000000.jpg)
输出表示的序列维度与查询(Query)数量保持一致，因为每个查询位置都会生成独立的上下文表示。随后，这些来自并行注意力头(Attention Head)的输出会被拼接(Concatenated)成一个更大的复合向量。在进入最终的线性投影层(Linear Projection Layer)之前，每个头都会贡献其独特且已学习到的特征表示。

## 实现流程与注意力可视化
实际实现流程首先通过线性投影(Linear Projection)生成查询(Query)、键(Key)和值(Value)矩阵。随后对这些矩阵进行重塑(Reshape)，以分离出指定数量的注意力头(Attention Heads)。 
![关键帧](keyframes/part002_frame_00069560.jpg)
![关键帧](keyframes/part002_frame_00090880.jpg)
在所有注意力头上并行执行注意力计算后，结果将被拼接(Concatenation)并传入最终的输出线性层(Output Linear Layer)。Vaswani 等人原始论文中的可视化图(Visualization)有效展示了自注意力(Self-Attention)如何消解诸如“making”一词的歧义。 
![关键帧](keyframes/part002_frame_00135439.jpg)
![关键帧](keyframes/part002_frame_00150480.jpg)
不同的注意力头专注于捕获不同类型的上下文信息：部分头聚焦于远距离的语义线索(Semantic Cues)以区分具体含义（例如，“making something difficult”与“making a cake”），而其他头则关注邻近的句法成分(Syntactic Components)或词元(Token)本身。这种跨头的功能专门化(Functional Specialization)正是多头注意力(Multi-Head Attention)的核心优势。

## 序列长度与注意力头数量约束
一个常见的架构疑问是：当输入序列长度(Input Sequence Length)小于注意力头数量时是否会引发问题。答案是否定的，因为多头分割操作是沿着特征/嵌入维度(Embedding Dimension)进行的，而非沿着序列长度(Sequence Length)维度。 
![关键帧](keyframes/part002_frame_00218920.jpg)
模型设计者通常会确保隐藏维度(Hidden Dimension)的大小（如 512、768）能被注意力头数量（如 8）整除，从而保证无论输入序列长短，都能实现规整的维度划分。尽管历史上曾提出过细粒度注意力(Fine-grained Attention)等变体（即为每个嵌入维度分配独立的注意力头），但因计算开销(Computational Overhead)过大而被广泛弃用。

## 掩码注意力与架构演进
注意力掩码机制(Attention Masking)的发展深刻影响了现代模型的设计。早期的注意力模型曾尝试将双向循环神经网络(Bi-directional RNN)与因果注意力(Causal Attention)相结合。掩码注意力(Masked Attention)在原始 Transformer 论文中被正式引入并标准化，最初仅应用于解码器(Decoder)内部。 
![关键帧](keyframes/part002_frame_00295640.jpg)
![关键帧](keyframes/part002_frame_00302200.jpg)
GPT 等仅解码器架构(Decoder-Only Architecture)在自回归生成(Autoregressive Generation)过程中利用该掩码机制，以防止当前词元(Token)关注到未来的位置（即防止信息泄露）。 
![关键帧](keyframes/part002_frame_00319559.jpg)
相比之下，编码器-解码器架构(Encoder-Decoder Architecture)在输入序列处理上保留了双向注意力(Bi-directional Attention)。一种被称为前缀语言建模(Prefix Language Modeling)的混合训练策略，曾尝试通过结合掩码与非掩码注意力阶段来训练仅解码器架构以提升性能，但其引入的额外复杂性限制了该方法的广泛采用。
![关键帧](keyframes/part002_frame_00340439.jpg)
![关键帧](keyframes/part002_frame_00368560.jpg)
![关键帧](keyframes/part002_frame_00376400.jpg)

## 可扩展性与序列无关处理
注意力机制(Attention Mechanism)的根本优势在于其数学上的一致性：无论输入序列长度(Input Sequence Length)如何变化，它都执行完全相同的权重计算与特征变换。 
![关键帧](keyframes/part002_frame_00394760.jpg)
![关键帧](keyframes/part002_frame_00400879.jpg)
这种统一性使得 Transformer 能够灵活地泛化(Generalize)至任意长度的序列。早期试图通过将句子切分为固定块(Fixed Chunks)或等分段落来处理可变长度输入的方法被证明十分脆弱且难以扩展，而注意力机制固有的长度无关特性(Length-Agnostic Property)则为不同尺寸的序列提供了稳健且一致的性能表现。
![关键帧](keyframes/part002_frame_00446200.jpg)
![关键帧](keyframes/part002_frame_00452919.jpg)

## 位置编码的整合
由于 Transformer 完全依赖注意力机制(Attention Mechanism)，且缺乏循环神经网络(RNN)或卷积结构的归纳偏置(Inductive Bias)，因此其架构本身不具备对词元顺序(Token Order)的感知能力。 
![关键帧](keyframes/part002_frame_00484479.jpg)
为解决这一问题，在进入注意力层之前，位置编码(Positional Encoding)会以逐元素相加(Element-wise Addition)的方式与输入嵌入(Input Embeddings)进行融合。 
![关键帧](keyframes/part002_frame_00544520.jpg)
若缺乏这些编码，模型会将相同词汇但语序不同的句子视为无法区分的词袋(Bag-of-Words)表示，因为标准的注意力机制本质上具有排列不变性(Permutation Invariance)。 
![关键帧](keyframes/part002_frame_00554240.jpg)
位置编码为模型注入了必要的序列结构信息，使其能够有效捕获句法关系(Syntactic Relationships)与词序(Word Order)。