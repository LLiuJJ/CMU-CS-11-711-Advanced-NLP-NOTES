## 人类反馈学习(Learning from Human Feedback)简介
![关键帧](keyframes/part000_frame_00000000.jpg)
本节首先概述了从人类反馈中学习的宏观图景。尽管基于人类反馈的强化学习(Reinforcement Learning from Human Feedback, RLHF)在当前讨论中占据主导地位，但它仅是众多方法中的一种。在深入探讨模型如何有效利用反馈信号进行训练之前，本文将首先详细介绍专为生成任务(Generation Tasks)量身定制的人类反馈收集方法，此类任务的复杂度远高于标准的分类问题。

## 最大似然估计(Maximum Likelihood Estimation, MLE)的局限性
历史上，语言模型一直采用最大似然估计(Maximum Likelihood Estimation, MLE)进行训练，该方法旨在最大化给定上文语境下预测下一个正确词元(Token)的概率。然而，MLE 本质上对所有词元级错误施加同等程度的惩罚，未能区分不同错误在实际语义或语用后果上的严重性差异。例如，轻微的语法失误，其危害远小于篡改关键实体（如将目的地从“匹兹堡”改为“东京”）或将礼貌用语替换为不当言辞。MLE 这种对错误统一加权的方式，使其难以胜任那些需要精细调控且容错率极低的高风险生成任务。
![关键帧](keyframes/part000_frame_00165440.jpg)

## 有缺陷的黄金标准数据(Gold Standard Data)问题
MLE 的另一大缺陷在于其高度依赖往往不完美或含有噪声的参考数据集。训练语料库中常混杂着有害评论、虚假信息或低质量的机器生成文本（例如早期模型的过时自动翻译结果）。当模型严格从这些存在缺陷的“黄金标准”中学习时，不可避免地会内化并复现不良的语言模式。这凸显了引入高质量纯净数据，或转向依赖反馈驱动(Feedback-driven)训练信号的必要性。
![关键帧](keyframes/part000_frame_00209720.jpg)

## 曝光偏差(Exposure Bias)与生成质量退化
MLE 训练还面临曝光偏差(Exposure Bias)问题，即训练阶段与推理阶段的输入条件存在不匹配。在训练过程中，模型始终接收完美的真实前序词元(Ground-truth Tokens)作为输入（即教师强制机制），这意味着它们从未学习过如何从自身生成的错误中恢复。这常导致推理阶段的性能退化，尤其是出现重复循环现象(Repetition Loops)（例如无休止地重复“我要去匹兹堡”）。引入生成完整序列、对其进行打分并对劣质输出施加惩罚的替代性训练范式，有助于提升模型对自身生成错误的鲁棒性(Robustness)。
![关键帧](keyframes/part000_frame_00226079.jpg)

## 评估输出质量的方法
![关键帧](keyframes/part000_frame_00309240.jpg)
要突破 MLE 的局限，必须建立可靠的方法来评估模型输出。现有的评估策略通常可划分为四类：客观评估(Objective Evaluation)、人类主观标注(Human Annotation)、基于机器预测的偏好建模(Machine-Predicted Preference Modeling)以及下游任务效用(Downstream Task Utility)。对于具有明确标准答案的任务（如数学求解或文本分类），客观评估极为有效，因为此类任务的评判标准通常是二值化(Binary)且易于验证的。
![关键帧](keyframes/part000_frame_00357960.jpg)

## 人类评估(Human Evaluation)与直接评估法(Direct Assessment, DA)的挑战
对于缺乏单一标准答案的开放式生成任务，人类评估(Human Evaluation)是主要的基准方法。其中广泛应用的一种技术是直接评估法(Direct Assessment, DA)，即由标注者为生成输出的整体质量分配一个标量分数(Scalar Score)（例如 1-10 分）。尽管该方法概念直观，但在保证标注者间一致性(Inter-annotator Agreement)和规避主观解读偏差方面面临显著困难。在实践中，若缺乏高度标准化的评分细则(Rubrics)与专家级评估人员，想要获得可靠且一致的数值评分极具挑战性。
![关键帧](keyframes/part000_frame_00384640.jpg)
![关键帧](keyframes/part000_frame_00392080.jpg)

## 多维度特征评估(Multi-dimensional Feature Evaluation)
为克服整体评分的局限性，现代评估框架将生成质量拆解为具体、可量化的特征维度。常见的评估维度包括：流畅度(Fluency，指语言表达的自然程度)、充分性(Adequacy，指对源输入语义的忠实度)、事实性(Factuality，指基于给定语境或常识的准确程度)以及连贯性(Coherence，指上下文逻辑的连贯与通顺程度)。这些特征维度的相关性与权重高度依赖于具体应用场景，在机器翻译(Machine Translation)、对话式人工智能(Conversational AI)与文本摘要(Text Summarization)等任务中存在显著差异。
![关键帧](keyframes/part000_frame_00406240.jpg)
![关键帧](keyframes/part000_frame_00411599.jpg)

## 实施的最佳实践(Best Practices)
强烈建议研究开发者在设计自定义评估指标(Custom Evaluation Metrics)前，优先参考目标领域内成熟的评估文献。通过采用经过实证检验且贴合特定任务的评估框架，开发者能够确保为模型训练构建更具一致性且可操作的反馈闭环(Feedback Loop)。本节内容至此告一段落，欢迎就反馈收集、训练算法及评估设计等议题展开提问与交流。
![关键帧](keyframes/part000_frame_00599680.jpg)