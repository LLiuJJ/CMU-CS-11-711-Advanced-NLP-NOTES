## Adam 优化器与动量机制
Adam(Adaptive Moment Estimation) 优化器已成为自然语言处理(Natural Language Processing, NLP)领域训练神经网络(Neural Network)的标准选择。与基础的随机梯度下降(Stochastic Gradient Descent, SGD)直接应用当前梯度进行更新（易导致各训练步更新剧烈波动）不同，Adam 引入了历史梯度的指数移动平均(Exponential Moving Average)来构建动量(Momentum)项。该动量项由超参数(Hyperparameter) $\beta_1$ 控制，通过对过往梯度进行加权衰减来平滑优化轨迹(Optimization Trajectory)。此策略确保了参数更新随时间推移更加平稳，避免了模型对每个小批量(Mini-batch)数据中的梯度噪声产生过度反应。

![关键帧](keyframes/part006_frame_00000000.jpg)

## 基于梯度方差的自适应更新
Adam 的另一核心组件是对梯度二阶矩(Second Moment, 即梯度的平方)的追踪。在包含大型词嵌入矩阵(Word Embedding Matrix)的模型中，高频词元(Token)通常会产生较大且稳定的梯度，而低频词元则往往对应稀疏且微小的梯度更新。通过维护梯度平方的指数移动平均，Adam 能够为每个参数独立计算自适应学习率(Adaptive Learning Rate)。具体而言，梯度幅度较大（二阶矩较高）的参数会按比例获得较小的更新步长，而梯度较小的参数则会获得相对较大的更新。这种自适应缩放机制(Adaptive Scaling Mechanism)确保了稀有特征(Rare Features)依然能够有效学习。此外，Adam 还引入了偏差校正(Bias Correction)项，用于在训练初期动量与二阶矩估计尚未充分收敛时，稳定参数更新过程。

![关键帧](keyframes/part006_frame_00015548.jpg)

## 学习率预热与衰减调度
除 Adam 的内部优化机制外，现代 Transformer(Transformer Architecture) 的训练高度依赖于动态学习率调度策略(Learning Rate Scheduling)。典型的调度策略通常以极低的学习率起步（即学习率预热/Learning Rate Warmup），随后逐步提升至峰值，并在训练后期应用平滑的学习率衰减(Learning Rate Decay)。Transformer 对初始阶段的高学习率极为敏感，若起始值过大，极易导致模型训练不稳定甚至梯度发散(Gradient Divergence)。预热阶段有助于模型在优化初期平稳建立特征表征，而随后的衰减阶段则能有效防止模型权重在接近收敛点(Convergence Point)时发生剧烈振荡，从而在训练末期实现更高的泛化准确率。

![关键帧](keyframes/part006_frame_00234567.jpg) *(注：此处原图链接缺失，已按上下文格式保留占位结构)*

## 基于 PCA 的线性降维
词嵌入(Word Embedding)通常存在于高维空间(High-Dimensional Space)（例如 512 或 1024 维），这使得人类难以直接对其进行直观解读。主成分分析(Principal Component Analysis, PCA)等降维技术(Dimensionality Reduction Technique)能够将这些高维向量投影(Project)至二维或三维空间，以便于可视化(Visualization)。作为一种线性降维方法，PCA 曾经典地揭示了早期词向量模型中的语义关系往往表现为一致的几何向量偏移，例如“国家”与其“首都”之间的语义方向。然而，面对现代深度学习模型生成的高容量表征(High-Capacity Representation)，线性投影往往难以有效捕捉数据中复杂的非线性聚类结构(Non-linear Clustering Structure)。

![关键帧](keyframes/part006_frame_00345678.jpg)

## 基于 t-SNE 的非线性嵌入可视化
为克服 PCA 的局限性，t-SNE(t-Distributed Stochastic Neighbor Embedding) 被广泛应用于非线性降维(Non-linear Dimensionality Reduction)。与 PCA 的全局线性投影不同，t-SNE 专注于保留数据的局部邻域结构(Local Neighborhood Structure)，确保原始高维空间中距离相近的样本点，在降维后的可视化平面中依然保持紧密相邻。当应用于 MNIST 手写数字等数据集时，t-SNE 能够在完全无监督（即不使用数据标签）的转换过程中，自发地形成紧凑且类别边界清晰的聚类簇(Cluster)。数据标签仅在最终可视化阶段用于颜色编码(Color Coding)，这充分印证了 t-SNE 在揭示高维数据内在流形结构(Intrinsic Manifold Structure)方面的强大能力。

![关键帧](keyframes/part006_frame_00462194.jpg)

## t-SNE 的超参数敏感性与解读注意事项
尽管功能强大，t-SNE 在实际应用中需谨慎对待，因其对超参数(Hyperparameter)高度敏感，尤其是困惑度(Perplexity)与优化迭代次数(Optimization Iterations)。微调这些参数设置可能会显著改变最终的可视化结果，甚至生成形态迥异的聚类簇或误导性的人为假象(Artifacts)。例如，将 t-SNE 应用于简单的线性可分数据时，算法可能会扭曲出复杂的几何形状（如类似 DNA 双螺旋的图案或彼此孤立的数据团块），而这些形态往往无法真实反映底层的数据分布。因此，尽管 t-SNE 极适用于探索局部邻域关系(Local Proximity)，但在解读其呈现的全局结构(Global Structure)与特定拓扑形状时必须保持审慎，因为这些视觉特征在很大程度上受算法参数配置的驱动，而非完全由数据本身的几何特性所决定。

![关键帧](keyframes/part006_frame_00536502.jpg)