## 线性插值与混合权重
![关键帧](keyframes/part001_frame_00000000.jpg)
模型组合(Model Combination)的基础依赖于贝叶斯公式(Bayes' Theorem)：$P(w_t) = \sum_m P(w_t|M_m) P(M_m)$。其中，$P(w_t|M_m)$ 表示模型 $M_m$ 生成的下一词元(Token)概率，而 $P(M_m)$ 表示在给定时间步(Time Step)选择该特定模型的概率。定义 $P(M_m)$ 最直接的方法是采用固定混合权重(Fixed Mixing Weights)。这些权重为预定义数值，取值范围在0到1之间且总和为1，从而允许在所有时间步上对模型输出进行静态插值(Static Interpolation)。

## 动态权重建模与实现
相较于依赖静态权重，模型选择概率 $P(M_m)$ 可根据当前上下文(Context)进行动态学习(Dynamic Learning)。这使得系统能够为最适合特定预测任务的模型分配更高的概率。该方法具备极高的工程实用性(Engineering Practicality)：开发者可预先计算每个基模型(Base Model)在各时间步的词元概率。因此，权重模型(Weight Model)可独立进行训练，无需在训练阶段将庞大的基模型加载至内存(Memory)中。

## 对数线性插值与重归一化
![关键帧](keyframes/part001_frame_00164760.jpg)
作为线性概率组合(Linear Probability Combination)的替代方案，对数线性插值(Log-linear Interpolation)首先聚合各模型的对数概率(Log-probabilities)，随后将其转换回有效的概率分布(Probability Distribution)。由于在对数空间(Log-space)内对数值求和会破坏概率的归一化约束（数值总和不再自动为1），因此必须严格执行 Softmax 重归一化(Softmax Renormalization) 步骤。尽管该过程在 PyTorch 等框架(Frameworks)中计算实现十分直接，但它对维持数学上严谨的概率分布至关重要。此处的插值系数(Interpolation Coefficients)同样可设置为固定常数或通过动态学习获得。

## 有限数据下的高效调优
学习动态插值系数具有极高的数据效率(Data Efficiency)。由于权重模型仅需在少量候选模型（例如对5个选项进行 Softmax 计算）上预测概率分布，而非在庞大的词表(Vocabulary)上进行预测，因此其自由度(Degrees of Freedom)极低。这使得仅需少量样本即可有效优化系数，甚至可将其配置为上下文无关(Context-independent)的静态参数。例如，当将一个1.3B参数的医疗领域语言模型(Medical Domain Language Model)与一个70B参数的通用英语语言模型(General-purpose English Language Model)结合时，系统能够快速学习：在遇到专业术语(Domain-specific Terminology)时提升医疗模型的权重，而在处理标准语法时依赖通用大模型。这种机制使其在训练数据有限的领域适应(Domain Adaptation)场景中表现尤为出色。
![关键帧](keyframes/part001_frame_00251959.jpg)
![关键帧](keyframes/part001_frame_00258399.jpg)

## 线性与对数线性：逻辑“或”与“与”
线性插值与对数线性插值的选择可从逻辑门(Logical Gates)的视角进行理解。线性插值的行为类似于逻辑“或”(OR)运算：只要任一模型对某词元赋予高概率，融合后的得分便会维持在较高水平。该特性在捕捉模型多样性、提升通用模型可能忽略的罕见词汇，或安全处理词表不匹配(Vocabulary Mismatch)的模型（避免陷入零概率陷阱(Zero-probability Trap)）时具有显著优势。相反，对数线性插值的行为类似于逻辑“与”(AND)运算，要求所有模型达成共识(Consensus)方可赋予高分。该特性在约束输出空间(Output Space)或实现安全过滤器(Safety Filters)时尤为有效。例如，通过确保任一模型的强烈负向信号(Negative Signals)能大幅拉低组合概率，从而有效拦截有害语言(Harmful Language)的生成。
![关键帧](keyframes/part001_frame_00329760.jpg)
![关键帧](keyframes/part001_frame_00336400.jpg)
![关键帧](keyframes/part001_frame_00341800.jpg)