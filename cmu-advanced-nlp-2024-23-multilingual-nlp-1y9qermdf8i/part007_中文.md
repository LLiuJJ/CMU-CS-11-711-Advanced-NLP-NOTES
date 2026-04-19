## 语言特定参数与基于任务的适配器
![关键帧](keyframes/part007_frame_00000000.jpg)
![关键帧](keyframes/part007_frame_00007800.jpg)
该框架为每种语言引入了语言特定参数(Language-Specific Parameters)，并结合了基于任务的适配器(Task-Based Adapters)。根据所解决的具体任务，适配器会被插入到模型的相应位置。研究还表明，可以在预训练阶段引入语言特定参数，使其在适配器架构中发挥有效作用。

## 用于摘要生成的前缀微调与 LoRA
![关键帧](keyframes/part007_frame_00041280.jpg)
类似的方法已被应用于文本摘要生成(Text Summarization)任务，相关研究对比了前缀微调(Prefix-Tuning)与 LoRA(Low-Rank Adaptation) 方法。结果表明，可以训练一个统一的单一模型，其中每种语言保留其专属的前缀微调或 LoRA 参数。这种参数高效(Parameter-Efficient)的策略已被证明在提升模型整体容量和适应性方面极为有效。

## 用于低资源数据创建的主动学习
受限于时间，此处需重点强调的另一个关键策略是主动式数据构建。尽管人工标注(Human Annotation)是一种可行方案，但为低资源语言(Low-Resource Languages)获取大规模数据集依然极为困难。为此，可采用主动学习(Active Learning)方法。其核心理念是：利用初始标注数据训练基线模型(Baseline Model)，将其应用于大量未标注数据(Unlabeled Data)，并筛选出模型预测不确定性(Uncertainty)较高的样本进行后续标注。这种定向策略能够高效地识别出当前模型信心不足或表现欠佳的数据样本。尽管主动学习并非多语言学习(Multilingual Learning)所独有，但在标注预算(Annotation Budget)严格受限的情况下，它显得尤为宝贵。
![关键帧](keyframes/part007_frame_00107760.jpg)
这一基本原理在二分类(Binary Classification)场景中得到了清晰展示。随机标注(Random Annotation)往往难以充分捕捉关键的决策边界(Decision Boundary)，可能导致模型在稀疏且缺乏代表性(Representativeness)的数据点上进行训练，从而影响模型准确性。相比之下，主动学习能够策略性地优先对决策边界附近的数据进行标注，从而生成更高效的训练样本。

## 平衡不确定性与代表性
驱动这一数据选择过程的两个核心概念是：不确定性(Uncertainty)与代表性(Representativeness)。目标是筛选出模型预测不确定性较高且具备数据代表性的样本。仅关注代表性仍能取得相对不错的效果；例如，在机器翻译(Machine Translation)任务中选择富含高频短语的数据，可提升模型对常见表达(Common Expressions)的覆盖范围。反之，关注不确定性有助于识别模型当前的知识盲区。然而，若完全依赖不确定性，可能会引入噪声数据(Noisy Data)或无关数据(Irrelevant Data)（例如仅包含表情符号的样本），这些数据对训练鲁棒(Robust)模型并无益处。

## 多语言嵌入覆盖与总结
![关键帧](keyframes/part007_frame_00196399.jpg)
![关键帧](keyframes/part007_frame_00202200.jpg)
为有效实施这一策略，不确定性可通过模型置信度分数(Confidence Scores)来衡量，而代表性则侧重于在包含所有可用数据的嵌入空间(Embedding Space)中实现全面覆盖。 
![关键帧](keyframes/part007_frame_00214359.jpg)
此外，这些主动学习策略可扩展至多语言场景，并与跨语言迁移(Cross-Lingual Transfer)技术相结合，从而在跨语系范围内最大化数据利用效率。 
![关键帧](keyframes/part007_frame_00221599.jpg)
随附的演示文稿中提供了更多关于如何执行该流程的实际示例，作为本次内容的总结。