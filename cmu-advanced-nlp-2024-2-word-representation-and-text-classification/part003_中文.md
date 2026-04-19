## 连续词嵌入的学习目标
训练连续词嵌入(Continuous Word Embeddings)的根本目标是将稀疏的、类别型(Categorical)的词表示转化为能够捕捉语言意义的稠密向量(Dense Vectors)。一个训练良好的嵌入空间(Embedding Space)应满足两个关键属性：首先，具有相似语义或句法特征(Semantic or Syntactic Features)的词在向量空间(Vector Space)中必须彼此靠近；其次，这些向量的各个维度理想情况下应对应有意义的语言学特征，如有生性(Animacy)、情感极性(Sentiment Polarity)或语法结构。尽管这些潜在特征(Latent Features)未必总能被人类明确解释，但向量空间的几何结构对于实现高级语义推理(Advanced Semantic Reasoning)和泛化能力(Generalization Capability)至关重要。
![关键帧](keyframes/part003_frame_00000000.jpg)

## 嵌入查找操作：索引 vs. 矩阵乘法
在实际的深度学习框架(Deep Learning Frameworks)中，获取词嵌入(Word Embeddings)在计算上通常被优化为直接从大型参数矩阵(Parameter Matrix)中进行索引查找的操作。然而，这种索引查找在数学上等价于将嵌入矩阵(Embedding Matrix)与表示目标词索引的独热向量(One-Hot Vector)相乘。理解这种等价性能够为模型赋予强大的建模能力。例如，若生成模型输出的是潜在下一个词的软分布(Soft Distribution)（即概率分布），而非单一的离散标记(Discrete Token)，那么将嵌入矩阵与该软分布相乘将得到一个期望嵌入向量(Expected Embedding Vector)。这使得模型能够综合多个候选词的语义信息，从而实现更为灵活的概率推理(Probabilistic Reasoning)。
![关键帧](keyframes/part003_frame_00196829.jpg)
![关键帧](keyframes/part003_frame_00203770.jpg)

## 转向梯度下降进行模型训练
尽管简单的线性分类器(Linear Classifiers)可采用结构化感知机算法(Structured Perceptron Algorithm)等启发式更新规则(Heuristic Update Rules)进行训练，但复杂的现代神经网络架构则需要系统化的优化框架(Optimization Frameworks)。深度学习模型(Deep Learning Models)主要通过梯度下降法(Gradient Descent)进行训练。该过程首先定义一个可微的损失函数(Differentiable Loss Function)以量化预测值与真实标签之间的差异，随后计算该损失相对于所有可训练参数(Trainable Parameters)（含嵌入权重(Embedding Weights)）的梯度(Gradients)，并沿最小化整体损失的方向迭代更新这些参数。
![关键帧](keyframes/part003_frame_00278744.jpg)

## 二分类损失：铰链损失
在二分类任务(Binary Classification Tasks)中，通常采用两种主流损失函数(Loss Functions)。第一种是铰链损失(Hinge Loss)，其定义为 0 与真实标签和模型预测得分乘积之间的最大值。从几何特性来看，铰链损失函数是非平滑且分段线性(Piecewise Linear)的。当模型预测正确且置信度(Confidence)达到预设边界时，该损失值恰好为零，这使得模型在达到理想分类边界后能有效停止参数更新。随着预测误差的增大，损失值呈线性增长，从而以与分类准确率(Accuracy)高度一致的方式直接惩罚误分类样本。

## 二分类损失：Sigmoid 与负对数似然
第二种主流方法将 Sigmoid 激活函数(Sigmoid Activation Function)与负对数似然损失(Negative Log-Likelihood Loss, NLL)相结合。Sigmoid 函数将模型的原始得分(Logits)映射为介于 0 到 1 之间的平滑连续概率值。对该概率取负对数可构建一条平滑的凸损失曲线(Convex Loss Curve)，该曲线渐近逼近于零但永不为零。与铰链损失不同，Sigmoid 结合 NLL 的公式为模型置信度提供了直接的概率解释(Probabilistic Interpretation)，这对于下游决策、分类阈值(Threshold)调整以及 ROC-AUC(Receiver Operating Characteristic - Area Under Curve)等性能指标的评估具有极高价值。

## 梯度行为与优化动态
损失函数的选择从根本上决定了训练过程中的梯度传播(Gradient Flow)与优化动态(Optimization Dynamics)。铰链损失虽与分类准确率紧密相关，但一旦样本预测正确，其梯度即降为零，这可能导致模型学习过程过早停滞。相反，Sigmoid 结合 NLL 损失在整个定义域内均保持非零梯度(Non-zero Gradients)，且梯度幅值与预测误差(Prediction Error)成正比。这一特性确保了更为平滑、稳定的优化过程(Optimization Process)，并能持续微调模型的置信度估计。在以铰链损失对词袋模型(Bag-of-Words Model)进行梯度解析推导时，参数更新规则可简化为：仅当样本违反间隔约束(Margin Violation)时才调整权重。这充分凸显了损失函数的数学形式如何直接驱动嵌入向量(Embedding Vectors)与分类参数(Classification Parameters)的迭代修正。
![关键帧](keyframes/part003_frame_00536101.jpg)
![关键帧](keyframes/part003_frame_00595727.jpg)