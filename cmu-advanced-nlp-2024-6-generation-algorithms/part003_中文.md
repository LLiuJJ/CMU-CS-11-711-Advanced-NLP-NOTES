## 概率质量与最大似然
在评估生成输出时，我们不应仅局限于单个词元序列(Token Sequence)，而应着眼于更广泛的概率分布(Probability Distribution)。例如，给定一个关于猫的提示(Prompt)，概率最高的单一输出可能是“猫坐了下来”，而“跑开了”、“飞奔而去”或“溜走了”等其他选项的概率则略低。像“它长出了翅膀”这类语义荒谬的选项，其概率则最低。若严格遵循最大似然解码(Maximum Likelihood Decoding)，我们将直接输出“坐了下来”。然而，该方法忽略了一个关键细节：表达“猫离开了该区域”这一语义的概率质量(Probability Mass)实际上分散在了三个不同的有效序列中。将这三者的概率相加，其总概率将是单个最高概率序列的两倍。最大似然搜索无法捕捉到这种语义层面的共识(Semantic Consensus)。
![关键帧](keyframes/part003_frame_00000000.jpg)

## 重新思考输出质量：共识与风险
这引出了一个根本性问题：若概率并非衡量生成质量的唯一指标，那么何种标准才能使输出真正卓越？我们需要区分单一的高概率孤立序列(Isolated Sequence)与一组共享相似语义的中等概率输出集合。采用基于输出集群(Output Clustering)的解码策略，本质上具有更低的风险。试想一个假设性类比：当你向同伴征集答案时，若仅有一名顶尖学生给出答案 A，而其余所有同学均给出答案 B，那么选择答案 B 能最大程度地降低决策风险。孤立序列虽可能极其准确，但也可能是罕见的错误，而共识则提供了更为稳妥的选择。因此，我们的目标应是寻找在相对高概率与低风险（即高一致性/High Consistency）之间取得最佳平衡的输出。
![关键帧](keyframes/part003_frame_00036440.jpg)
![关键帧](keyframes/part003_frame_00059400.jpg)
![关键帧](keyframes/part003_frame_00081720.jpg)

## 通过采样估计概率与风险
为将这一理念付诸实践，我们需要可靠的方法来估计概率与风险。概率可通过蒙特卡洛采样(Monte Carlo Sampling)进行近似估计：从模型中抽取大量但数量可控的序列（例如 100 个），即可通过统计频率来近似底层概率分布(Underlying Distribution)。风险估计(Risk Estimation)则将这些采样输出视为伪参考序列(Pseudo-references)。随后，利用成熟的评估指标(Evaluation Metrics)计算候选输出与样本集中其余输出之间的一致性或相似度。
![关键帧](keyframes/part003_frame_00139159.jpg)

## 最小贝叶斯风险（MBR）解码
这一理论框架自然引出了最小贝叶斯风险(Minimum Bayes Risk, MBR)解码。MBR 通过在采样分布中最小化期望损失(Expected Loss)（或最大化期望相似度(Expected Similarity)），将“选择低风险、高共识输出”的直觉进行了数学形式化。该方法会依据每个伪参考序列的概率，对其相似度得分进行加权处理。实践表明，在文本摘要任务中应用 MBR（以 ROUGE 指标作为相似度度量，并抽取 100 个样本），其表现稳定优于标准解码、集束搜索(Beam Search，束宽为 5 或 10)以及多样性集束搜索(Diverse Beam Search)。尽管当 MBR 优化指标与下游评估指标(Downstream Evaluation Metrics)一致时性能提升最为显著，但即便两者不同，其性能改进依然保持稳健。
![关键帧](keyframes/part003_frame_00235839.jpg)
![关键帧](keyframes/part003_frame_00296200.jpg)
![关键帧](keyframes/part003_frame_00306159.jpg)

## 权衡：计算成本与集束搜索的诅咒
MBR 的主要缺点是计算成本(Computational Cost)高昂。与单次解码(Single-pass Decoding)方法相比，在推理(Inference)阶段生成并交叉比对 100 个候选序列不仅速度更慢，且资源消耗显著增加。此外，课程还探讨了一个被称为“集束搜索的诅咒(Curse of Beam Search)”的著名现象。尽管理论上增加束宽(Beam Width)应能更有效地逼近真正的最大似然序列，但这往往会导致下游任务的性能不升反降。这是因为概率分布的绝对数学众数(Mathematical Mode)往往并非人类最偏好或上下文语境中最优的输出，这进一步凸显了采用基于共识的解码方法（如 MBR）的必要性。
![关键帧](keyframes/part003_frame_00359359.jpg)

## 训练目标与模型退化
这一解码挑战与语言模型(Language Model)的训练方式密切相关。最大似然估计(Maximum Likelihood Estimation, MLE)旨在优化模型准确拟合潜在答案完整概率分布的能力，而非显式地最大化单一最佳回答的质量。面对开放式或复杂的提示，安全且通用的回答（例如“我不知道”）所占据的概率质量通常远超任何具体、详实回答的概率。因此，采用 MLE 训练的模型可能会表现出退化行为(Degenerate Behavior)，例如生成过短文本、陷入重复循环(Repetition Loop)，甚至输出空序列。从数学角度看，这些结果可能恰恰代表了模型所学分布的真实众数。
![关键帧](keyframes/part003_frame_00596640.jpg)