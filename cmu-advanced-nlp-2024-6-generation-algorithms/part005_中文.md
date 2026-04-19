## FUDGE 与未来判别器简介
FUDGE 一词源于“未来判别器”(Future Discriminator)。该方法通过在句子前缀(Sentence Prefix)上训练模型，以预测最终生成的完整序列是否满足特定约束条件(Constraint，例如语体正式程度)。通过将正式与非正式数据集中的句子拆分为前缀序列，判别器能够学习估算部分生成的序列最终属于目标类别的概率。
![关键帧](keyframes/part005_frame_00000000.jpg)

## 从 RLHF 到贝叶斯推断的转变
尽管受限解码(Constrained Decoding)传统上侧重于结构约束，但其另一核心目标是使模型输出与人类偏好保持一致，这通常通过基于人类反馈的强化学习(Reinforcement Learning from Human Feedback, RLHF)实现。RLHF 将基础模型(Base Model)的概率分布与奖励模型(Reward Model)的评估信号相结合，经微调后得到一个倾向于生成高奖励序列的“已对齐模型”(Aligned Model)。本质上，该过程可视为贝叶斯推断(Bayesian Inference)，其目标分布在数学上等价于基础模型的似然函数(Likelihood Function)与满足特定奖励条件概率的结合。
![关键帧](keyframes/part005_frame_00060120.jpg)
![关键帧](keyframes/part005_frame_00082600.jpg)
![关键帧](keyframes/part005_frame_00100040.jpg)

## 无需强化学习的解码时对齐
在认识到 RLHF 本质上是概率分布的融合后，研究人员提出了在解码阶段(Decoding Stage)直接施加外部约束的方法，从而避免了高昂的模型微调成本。近期的一种方法在生成的每一步都会预测序列的预期奖励。通过利用基于前缀训练的奖励预测器（其机制类似于未来判别器），系统可评估每个候选词元(Token)的潜在奖励。最终词元的选择，是通过将基础语言模型(Base Language Model)的输出概率与这些预测奖励进行加权融合后计算得出的。
![关键帧](keyframes/part005_frame_00152519.jpg)

## 解码时约束整合的优势
这种解码时策略(Decoding-time Strategy)无需进行实际的强化学习训练，即可实现 RLHF 的诸多对齐优势。它将人类偏好匹配与约束满足转化为模块化的动态参数，可在生成过程中实时施加。这种灵活性不仅显著降低了计算开销(Computational Overhead)，也大幅简化了对齐语言模型在多场景下的部署流程。
![关键帧](keyframes/part005_frame_00210519.jpg)

## 问答：判别器训练与计算效率
约束判别器的训练需针对每种约束条件提供标注的正负样本(Positive/Negative Samples)。尽管引入新约束通常需要重新训练模型，但其计算负担依然较低。判断序列是否违反约束是一项远轻于预测下一个词元(Next-token Prediction)的任务，因此开发者可采用参数量相对较小的分类器(Classifier)。即便需处理多个约束（例如输出 100 维向量），与标准词表上的 Softmax 操作（维度通常超 32,000）相比，其额外引入的推理成本(Inference Cost)也微乎其微。

## 问答：处理负向约束与奖励模型适配
为强制执行负向约束(Negative Constraints，如规避特定主题)，可训练统一的多标签判别器(Multi-label Discriminator)，或聚合多个专用模型的评分，并利用最大违规得分(Maximum Violation Score)来引导或阻断生成过程。在解码时对齐框架中，奖励模型通常基于标准的 RLHF 偏好数据集进行适配。该模型不再对完整序列进行整体打分，而是基于序列前缀进行训练以预测下游奖励值；期间常辅以数据增强(Data Augmentation)技术来提升预测精度，且无需对基础模型进行全量重训。
![关键帧](keyframes/part005_frame_00394400.jpg)
![关键帧](keyframes/part005_frame_00407239.jpg)

## 问答：实际应用与自我验证策略
该技术的实际应用范围广泛，涵盖提升对话系统的共情能力(Empathy)到过滤有害或冒犯性内容。相较于训练独立判别器，一种高效的替代方案是直接利用语言模型进行自我验证(Self-verification)。通过提示(Prompting)模型评估其自身生成的前缀是否存在违规约束（例如是否涉及暴力主题），开发者可实施一种轻量级的“采样-检查”(Sample-and-Check)策略。由于验证文本是否满足约束通常远比从头生成受控文本更为简单，这种自洽性(Self-consistency)方法已被证明兼具高效性与资源节约优势。
![关键帧](keyframes/part005_frame_00533160.jpg)
![关键帧](keyframes/part005_frame_00572400.jpg)

## 问答：数据收集与启发式标注
针对如何获取约束判别器的训练数据，演讲者强调，现有的精选数据集(Curated Datasets)通常是主要来源。对于公开数据匮乏的高度特定或长尾约束，开发者可构建启发式规则(Heuristic Rules，如关键词匹配、模式检测(Pattern Detection)或基于规则的过滤)来自动标注正负样本。这一流程使得针对特定领域快速、低成本地训练定向约束预测器成为可能。