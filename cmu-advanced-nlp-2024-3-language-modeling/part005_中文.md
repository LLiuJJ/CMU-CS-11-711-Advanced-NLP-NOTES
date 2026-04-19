## 公平模型比较与词元化面临的挑战
在比较 Llama 和 GPT-2 等语言模型(Language Model) 时，评估的公平性至关重要。不同的模型采用不同的分词器(Tokenizer)，导致相同文本的词元(Token) 数量存在差异。以不同的分母比较性能指标本质上是不公平的。此外，若允许模型输出未知词或字符（实质上等同于未预测出有效词元），则必须在评估框架(Evaluation Framework) 中对此行为进行明确记录并加以考量。
![关键帧](keyframes/part005_frame_00000000.jpg)

## 基于特征的语言模型
作为传统神经网络与词袋分类器(Bag-of-Words Classifier) 的替代方案，基于特征的模型(Feature-based Model) 会从上下文中提取显式特征(Explicit Feature)，例如前一两个词的具体词汇标识。模型会引入偏置项(Bias Term)，计算得分(Score) 并推导概率(Probability)。特征权重通过随机梯度下降(Stochastic Gradient Descent, SGD) 进行优化。从概念上看，这相当于一个用于预测下一个词元的多分类(Multi-class Classification) 词袋分类器，只是其输出类别被扩展至数万乃至数十万个。这一基础方法约由 Roni Rosenfeld 于 27 年前率先提出，彰显了统计语言模型(Statistical Language Model) 深厚的历史根基。
![关键帧](keyframes/part005_frame_00101920.jpg)

## 训练机制与固有局限性
尽管在结构上与词袋分类器相似，但特征模型引入了偏置项以及基于特定前序词的条件概率向量(Conditional Probability Vector)，而非仅仅依赖无序的词频统计。训练过程遵循标准范式：计算损失函数(Loss Function) 相对于参数的梯度(Gradient)，并应用反向传播算法(Backpropagation) 更新权重。该架构成功解决了上下文中存在间隔词时的条件建模问题，允许直接进行上下文条件化(Context Conditioning)，而无需强制采用僵化的 n-gram 组合。然而，该模型无法在语义相似的词之间共享统计强度(Statistical Strength)，且仍严格受限于固定的上下文窗口(Context Window)，因此未能解决长距离依赖(Long-range Dependency) 问题。
![关键帧](keyframes/part005_frame_00118480.jpg)
![关键帧](keyframes/part005_frame_00142599.jpg)
![关键帧](keyframes/part005_frame_00163679.jpg)
![关键帧](keyframes/part005_frame_00188839.jpg)

## 前馈神经网络语言模型
在转向神经方法(Neural Approach) 后，前馈语言模型(Feedforward Language Model) 使用稠密词嵌入(Dense Word Embedding) 取代了离散的特征查找。这些词嵌入被拼接(Concatenation) 后，经由中间变换层(Transformation Layer) 处理以提取高阶特征，随后进行权重矩阵乘法与偏置相加，最后通过 Softmax 层转换为概率分布。该架构的一大优势在于具备学习组合特征(Compositional Feature) 的能力（例如，识别能够提升目标词元概率的特定词对）。它实现了强大的统计强度共享：相似的输入词会被映射为相似的嵌入向量(Embedding Vector)，而相似的输出词则在 Softmax 权重矩阵中占据相近的行向量。因此，相似的隐藏状态(Hidden State) 能够自然而然地捕获类似的上下文模式(Contextual Pattern)。
![关键帧](keyframes/part005_frame_00233119.jpg)
![关键帧](keyframes/part005_frame_00258440.jpg)

## 通过词嵌入绑定提升参数效率
神经语言模型中广泛采用的一种优化技术是“词嵌入绑定(Embedding Tying)”，即在输入词嵌入查找矩阵与输出 Softmax 投影矩阵之间共享参数。该技术带来两大关键优势：首先，它有效使词嵌入学习的训练信号(Training Signal) 翻倍，因为该矩阵在词汇作为上下文出现和作为目标词被预测时均会得到更新；其次，它显著降低了参数量(Parameter Count) 与内存开销(Memory Footprint)。考虑到词汇表(Vocabulary) 规模常超数十万，且嵌入维度(Embedding Dimension) 可达数百，仅这两个矩阵就可能包含数百万参数。对它们进行绑定不仅缓解了由低频词更新引起的稀疏性(Sparsity) 问题，还大幅提升了计算效率(Computational Efficiency)。
![关键帧](keyframes/part005_frame_00441200.jpg)

## 应对长距离依赖与未来架构探索
尽管架构有所改进，前馈神经模型仍因其固有的固定上下文窗口，在处理长距离依赖时显得捉襟见肘。若线性扩展该窗口，会导致模型规模线性膨胀，并引发严重的计算瓶颈(Computational Bottleneck)。为突破这一限制，研究重心已转向专为长序列建模(Long-sequence Modeling) 设计的架构：循环神经网络(Recurrent Neural Network, RNN)、卷积神经网络(Convolutional Neural Network, CNN) 以及基于注意力机制(Attention Mechanism) 的 Transformer。尽管 Transformer 目前主导着现代自然语言处理(Natural Language Processing, NLP) 领域，但许多前沿的长上下文(Long-context) 模型仍在积极融合循环或卷积架构的原理。深入理解这些架构之间的协同关系及其各自优势，依然至关重要。
![关键帧](keyframes/part005_frame_00449679.jpg)
![关键帧](keyframes/part005_frame_00460759.jpg)
![关键帧](keyframes/part005_frame_00482479.jpg)

## 模型校准的关键概念
除架构设计与上下文长度外，部署可靠语言模型的一项基本要求是模型校准(Model Calibration)。校准旨在确保模型“清楚自己何时真正知道答案”，即模型输出的预测置信度(Predictive Confidence) 能够准确反映其真实的正确概率。严格来说，校准良好(Well-calibrated) 的模型对某一给定答案输出的概率，应与该答案在历次评估中实际正确的经验频率(Empirical Frequency) 精确吻合。掌握并量化模型校准指标(Calibration Metric)，对于构建透明、可信的 AI 系统不可或缺，也将是后续讨论的核心重点。
![关键帧](keyframes/part005_frame_00574520.jpg)