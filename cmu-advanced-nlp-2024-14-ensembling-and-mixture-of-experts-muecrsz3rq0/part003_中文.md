## 基于 Bagging 与检查点的多样化模型生成
![关键帧](keyframes/part003_frame_00000000.jpg)
在传统集成技术(Ensembling Techniques)的基础上，Bagging（自助聚合法(Bootstrap Aggregating)）与利用训练检查点(Training Checkpoints)等方法提供了一条高效途径，无需设计全新架构即可生成多个模型变体(Model Variants)。Bagging 涉及对训练数据集(Training Dataset)进行有放回的重采样(Sampling with Replacement)以训练独立模型，确保每个模型接触到略微不同的数据分布(Data Distribution)。同样，保存并融合来自不同训练检查点的模型参数，也能提供多样化的预测视角(Predictive Perspectives)。这些方法通过平滑各模型的个体差异来有效提升整体鲁棒性(Robustness)，因为每个变体在训练过程中能够捕捉到不同的数据模式(Data Patterns)，或以不同方式应对噪声(Noise)。

## 传统集成的计算瓶颈
![关键帧](keyframes/part003_frame_00052680.jpg)
![关键帧](keyframes/part003_frame_00060520.jpg)
![关键帧](keyframes/part003_frame_00068360.jpg)
尽管标准集成(Standard Ensembling)能带来精度提升，但由于其推理成本(Inference Cost)随模型数量呈线性增长，在实际部署中面临重大挑战。在推理阶段(Inference Phase)同时运行多个模型会大幅增加计算开销(Computational Overhead)与显存需求(Memory Requirements)，通常需要额外的 GPU 基础设施，并使服务部署(Service Deployment)变得更为复杂。这使得传统的运行时集成(Runtime Ensembling)在许多生产环境(Production Environments)中变得不切实际。因此，研究重点逐渐转向探索既能保留集成带来的性能增益(Performance Gains)，又能维持单一模型计算占用(Computational Footprint)与内存效率(Memory Efficiency)的解决方案。

## 参数平均的前提条件：置换不变性
![关键帧](keyframes/part003_frame_00319080.jpg)
解决这一瓶颈最直接的方法是参数平均(Parameter Averaging)，即直接将多个模型的权重(Weights)合并至统一的网络架构中。然而，该技术限制极大：它仅在模型具备完全相同的网络架构(Network Architecture)，且使用了完全相同的随机初始化(Random Initialization)时才有效。神经网络存在置换对称性(Permutation Symmetry)特性，这意味着采用相同架构但不同初始化的模型虽能学习到功能等效的表示(Functionally Equivalent Representations)，但其内部神经元的权重映射会被随机打乱。若直接对不同初始化模型的参数进行平均，会在数学上导致对应神经元(Neurons)发生错位(Misalignment)，从而生成一个毫无意义且性能严重退化(Performance Degradation)的模型。因此，严格来说，参数平均仅适用于源自同一预训练权重(Pre-trained Weights)或相同初始化状态的模型。

## 实际应用：检查点平均与微调模型合并
![关键帧](keyframes/part003_frame_00346240.jpg)
鉴于上述限制，参数平均在以下两个主要场景中仍极具应用价值。首先，对单次训练过程中的多个检查点(Checkpoints)进行平均，可有效抑制随机梯度下降(Stochastic Gradient Descent, SGD)固有的随机噪声(Random Noise)，从而在不增加额外计算开销的前提下，获得更稳定、精度更高的最终模型。其次，该技术适用于微调模型合并(Fine-tuned Model Merging)，即组合多个共享相同基础权重(Base Weights)的指令微调变体(Instruction-tuned Variants)（例如 Llama 2 7B 的不同垂直领域版本）。这使得从业者(Practitioners)能够融合模型的不同能力，或平滑特定微调过程中产生的过拟合偏差(Overfitting Bias)。尽管该技术在理论上可应用于安全对齐(Safety Alignment)，但操作时必须极为谨慎。将经过深度安全微调的模型与原始基础模型进行平均，可能会削弱其安全保护约束(Safety Constraints)，并导致生成处于中间态且可靠性较低的输出。

## 高级平均策略：均匀平均与贪婪选择
![关键帧](keyframes/part003_frame_00547080.jpg)
![关键帧](keyframes/part003_frame_00555080.jpg)
近期研究，尤其是《Model Soups》（模型汤）论文，对面向现代大语言模型(Large Language Models, LLMs)的参数平均技术进行了系统化形式化与扩展。该研究对比了两种核心策略：均匀平均(Uniform Averaging，即所有候选模型赋予相等权重)与更具选择性的贪婪平均方法(Greedy Averaging)。贪婪方法会迭代地将候选模型逐一加入“模型汤”中，并评估每次添加是否能在独立验证集(Independent Validation Set)上提升整体性能。若某模型的加入无法提升组合性能，则将其剔除。这种选择性集成(Selective Ensembling)机制确保仅保留能带来正向增益的微调变体，其效果始终优于简单的均匀平均，从而最大化了最终合并模型的性能与效率。