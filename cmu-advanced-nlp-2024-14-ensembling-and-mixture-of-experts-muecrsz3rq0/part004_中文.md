## 贪婪平均与均匀平均的性能对比
关于模型融合(Model Merging)的研究，尤其是《Model Soups》（模型汤）一文的发现，凸显了不同平均策略之间显著的性能差异。贪婪平均策略(Greedy Averaging)（即选择性纳入能提升验证得分(Validation Score)的模型）在 ImageNet 等目标数据集上，其表现始终优于单一最优模型(Single Best Model)与均匀平均(Uniform Averaging)。相反，均匀平均在训练分布(Training Distribution)上的准确率通常略低，但在面对存在分布偏移(Distribution Shift)的评估数据集时，却展现出更强的鲁棒性(Robustness)。这揭示了一个根本性权衡(Fundamental Trade-off)：贪婪优化旨在最大化分布内准确率(In-Distribution Accuracy)，而均匀平均则有效增强了模型在不同数据环境中的泛化能力(Generalization Capability)。
![关键帧](keyframes/part004_frame_00000000.jpg)

## 参数平均与集成的权衡
尽管参数平均(Parameter Averaging)的性能与传统集成(Traditional Ensembling)高度相关，但其在绝对准确率上很少能超越后者。参数平均的核心优势在于计算效率(Computational Efficiency)，它能提供更精简的推理过程(Inference Process)，且无需承担同时运行多个模型所带来的显存(Memory)与运行时(Runtime)开销。然而，这种效率提升是有前提的；在部分场景下，合并参数反而可能导致性能逊于保持模型独立运行。值得注意的是，相关实验通常采用源自同一预训练初始化(Pre-training Initialization)的模型，从而有效规避了置换不变性(Permutation Invariance)引发的参数错位问题。尽管存在这种结构对齐(Structural Alignment)，参数平均本质上仍是传统集成的一种近似(Approximation)实现，仍需经过严格验证以避免准确率出现显著下滑。
![关键帧](keyframes/part004_frame_00217359.jpg)

## 在 Transformer 架构中的适用性
尽管基础的模型融合实验最初是基于卷积神经网络(Convolutional Neural Networks, CNN)的视觉模型展开的，但上述原理同样能高效迁移至现代 Transformer 架构。尽管理论上曾有担忧认为 Transformer 的特征表征(Feature Representations)可能高度集中于潜在空间(Latent Space)，但参数融合在大型语言模型(Large Language Models, LLMs)上的应用已被证实极为成功，Hugging Face 排行榜(Leaderboard)上持续霸榜的融合模型便是有力佐证。虽然标准平均(Standard Averaging)在实践中已表现出色，但开发专门针对 Transformer 参数空间(Parameter Space)独特几何特性(Geometric Properties)的融合算法仍具巨大潜力，这无疑是未来研究中一个极具前景的方向。
![关键帧](keyframes/part004_frame_00370080.jpg)

## 任务向量：定义与属性相减
超越简单的参数平均，“任务向量”(Task Vectors)的概念为模型操控(Model Manipulation)提供了一种更精细、且在数学上更具灵活性的方法。任务向量被定义为微调模型参数(Fine-tuned Model Parameters)与其原始预训练基础模型参数(Pre-trained Base Parameters)之间的差值。这种差分表示(Differential Representation)支持强大的代数运算(Algebraic Operations)，例如可针对性地剔除不良模型行为(Undesirable Model Behaviors)。例如，若模型表现出有害内容生成倾向或存在隐私泄露(Privacy Leakage)风险，通过减去对应的任务向量，可有效中和(Neutralize)这些特定能力，从而实现精确的模型编辑(Precise Model Editing)，而无需执行完整的重新训练(Retraining)或进行架构修改。

## 多任务组合与冲突消解
任务向量还通过实现多个专业领域模型(Specialized Models)的策略性组合(Strategic Combination)，有力推动了先进的多任务学习(Multi-task Learning)。通过对不同的任务向量进行相加或平均，从业者(Practitioners)能够合成具备多目标处理能力的新模型。该过程面临的核心挑战是方向冲突(Direction Conflict)，即不同的任务向量试图将参数空间(Parameter Space)推向截然相反的方向。先进的融合算法通过识别发生强烈冲突的参数分量，并计算出一个能化解冲突张力(Conflict Tension)的统一向量来解决此问题。这种冲突感知方法(Conflict-aware Approach)的效果始终优于简单平均，能够确保融合后的模型在所有预期任务上同步维持或提升性能。
![关键帧](keyframes/part004_frame_00486640.jpg)