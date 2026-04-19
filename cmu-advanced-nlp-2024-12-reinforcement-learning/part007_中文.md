## 预测样本难度与基线概念
为在训练过程中有效校准奖励，模型可*预先*预测输入样本的固有难度，并据此对奖励信号进行归一化(Normalization)。这引出了**基线模型(Baseline Model)**的核心概念。引入基线的根本目的并非改变策略梯度的期望值(Expected Policy Gradient)，而是显著降低奖励信号的方差(Variance)，从而提升强化学习(Reinforcement Learning)训练过程的稳定性与可靠性。
![关键帧](keyframes/part007_frame_00000000.jpg)

## 基线的实现：回归模型与批次平均
实现基线计算主要有两种广泛采用的方法。其一是训练一个独立的回归模型(Regression Model)，严格依据输入提示(Prompt)或解码器(Decoder)的隐藏状态来预估最终奖励。关键在于，该基线模型**绝不能**窥探策略模型实际生成的输出，否则将破坏基线估计的理论有效性。此方法既可在全局句子层面应用，也可在局部解码步长(Decoding Step)中实施。其二更为简便，即直接计算当前训练批次(Training Batch)的平均奖励，并从每个独立样本的奖励中扣除该均值，从而实现反馈信号的零中心化(Zero-centering)。
![关键帧](keyframes/part007_frame_00021159.jpg)

## 成对偏好学习与 DPO
基线建模的一种进阶扩展是针对同一输入对比成对输出(Pairwise Outputs)。通过直接引入成对人类偏好(Pairwise Human Preferences)，训练过程本质上趋于稳定，因为回复的相对质量得到了明确量化。这一原则构成了**直接偏好优化(Direct Preference Optimization, DPO)**的基石。DPO 作为一种主流方法，有效绕过了传统强化学习中计算密集的奖励建模(Reward Modeling)循环，大幅简化了模型对齐(Model Alignment)流程。其主要代价在于，DPO 强依赖高质量整理的配对数据集，其中需明确标注被偏好(Chosen)与被拒绝(Rejected)的输出。
![关键帧](keyframes/part007_frame_00110840.jpg)

## 直接偏好优化的机制
DPO 的核心机制在于计算新策略与参考模型(Reference Model)针对每个生成序列的概率比(Probability Ratio)。其目标函数(Objective Function)旨在明确提升偏好（“更优”）输出的似然(Likelihood)，同时抑制被拒绝（“较差”）输出的似然。通过基于 Sigmoid 的损失函数直接优化该比率，DPO 能够实现与 PPO 或基于 KL 散度正则化的强化学习相媲美的对齐效果，且其优化地形(Optimization Landscape)更为简洁平稳。
![关键帧](keyframes/part007_frame_00181519.jpg)

## Beta 超参数与 KL 正则化的作用
DPO 的目标函数高度依赖于 `beta` 超参数(Hyperparameter)，它在此充当缩放因子与温度参数的角色。`Beta` 值直接调节作用于对数概率比(Log Probability Ratio)的 Sigmoid 函数的陡峭度(Steepness)。较高的 `beta` 值会放大细微的概率差异，进而控制梯度更新的步幅。从理论层面看，该项衍生自标准 RLHF(Reinforcement Learning from Human Feedback) 中的 KL 散度正则化(KL Divergence Regularization)约束，旨在确保模型在拟合人类偏好的过程中，不会灾难性地偏离其原始的语言建模(Language Modeling)先验分布。
![关键帧](keyframes/part007_frame_00223199.jpg)
![关键帧](keyframes/part007_frame_00272159.jpg)
![关键帧](keyframes/part007_frame_00279799.jpg)

## 通过增大批次大小来降低方差
受强化学习固有的随机采样(Stochastic Sampling)特性影响，其训练过程表现出远高于标准最大似然估计(Maximum Likelihood Estimation, MLE)的方差。一种直接且高效的缓解策略是增大批次大小(Batch Size)或增加每次参数更新所执行的轨迹采样(Rollout)数量。若在引入基线或正则化项后仍面临稳定性瓶颈，单纯提升单步采样规模通常即可有效抑制梯度噪声(Gradient Noise)并改善模型收敛(Convergence)。
![关键帧](keyframes/part007_frame_00286039.jpg)
![关键帧](keyframes/part007_frame_00317000.jpg)

## 利用 Rollout 缓存提升效率
在强化学习流水线(Reinforcement Learning Pipeline)中，实时生成 Rollout 并收集奖励信号的计算开销极高。为最大化训练效率，开发者通常会对历史生成结果进行缓存(Caching)。通过在多个训练迭代中复用这些已保存的 Rollout，模型能够在免除持续实时生成成本的前提下，构造出更大的有效批次大小(Effective Batch Size)，进而实现更平滑、数据利用率更高(Data-Efficient)且资源节约的优化过程。
![关键帧](keyframes/part007_frame_00345680.jpg)

## 结语与问答
至此，关于自然语言处理(Natural Language Processing, NLP)中强化学习技术的概述暂告一段落。尽管强化学习为优化复杂的不可微目标(Non-differentiable Objectives)及推动模型与人类偏好对齐提供了强大范式，但其成功落地高度依赖于方差缩减(Variance Reduction)技术、精细的正则化(Regularization)策略以及高效的数据处理流程。
![关键帧](keyframes/part007_frame_00360520.jpg)