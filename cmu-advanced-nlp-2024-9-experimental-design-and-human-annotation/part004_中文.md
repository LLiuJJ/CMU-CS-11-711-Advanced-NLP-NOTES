## 配对检验与非配对检验
理解配对检验(Paired Tests)与非配对检验(Unpaired Tests)的区别，是进行严谨的自然语言处理(Natural Language Processing, NLP)实验设计(Experimental Design)的基础。非配对检验用于比较两个完全独立数据集(Data Splits)上某项评估指标的均值，旨在帮助研究者判断观测到的差异是源于数据本身的真实分布差异，还是仅由抽样噪声(Sampling Noise)引起。相比之下，配对检验则在完全相同的数据样本上评估两种不同条件（如两个竞争模型(Competing Models)）。该领域普遍倾向于使用配对检验，因为它能够充分控制样本实例层面的变异性。通过分析模型在每个独立样本上的表现差异，而非仅仅对比总体均值，配对检验能大幅提升统计功效(Statistical Power)，从而更灵敏地检测出真实的性能提升。
![关键帧](keyframes/part004_frame_00000000.jpg)
![关键帧](keyframes/part004_frame_00068640.jpg)

## 用于显著性检验的自助法（Bootstrap）
评估统计显著性(Statistical Significance)的一种高度通用且被广泛采用的方法是自助法(Bootstrap Resampling)。与依赖严格数据分布假设的参数检验(Parametric Tests)不同，自助法具备指标无关性(Metric-agnostic)，可无缝适配各类评估指标(Evaluation Metrics)，无论是准确率(Accuracy)、F值(F1-score)、BLEU分数(BLEU Score)还是词错误率(Word Error Rate, WER)。该方法的核心机制是对原始测试集(Test Set)进行有放回的重采样(Resampling with Replacement)，从而生成数千个自助样本(Bootstrap Samples)。通过在这些重采样迭代中计算目标指标，研究者能够以经验方式推导出95%置信区间(Confidence Interval)（通常取第2.5至第97.5百分位数），或计算出精确的p值(p-value)。这为判断观测到的性能差距(Performance Gap)是否具有统计学意义上的可靠性，抑或仅是随机波动的结果，提供了一种稳健且数据驱动(Data-driven)的评估手段。
![关键帧](keyframes/part004_frame_00189760.jpg)

## 重采样动态与数据集规模
自助法的实际运行机制可通过一个简单的分类任务案例加以说明。当在极小规模的数据集上评估两个系统时，有放回的重采样会产生方差极大的子样本。在某次随机重采样中，系统A可能显著优于系统B；而在下一次迭代中，结果可能发生逆转或趋于持平。由于这种高方差(High Variance)特性，只有当某一系统在绝大多数（例如 >95%）自助法迭代中始终保持一致(Consistently)的优越表现时，才能断言其具有统计显著性。这揭示了一个关键的实验现实：小规模测试集本质上会导致置信区间宽泛且不稳定，从而极难证实统计显著性。随着数据集规模的扩大，重采样后的经验分布会自然趋于收敛，使研究者能够可靠地将模型的真实性能提升与抽样假象(Sampling Artifacts)区分开来。
![关键帧](keyframes/part004_frame_00450159.jpg)

## 指标兼容性与检验方法选择
尽管传统参数检验（如学生t检验(Student's t-test)）在处理可加性指标(Additive Metrics)（例如准确率，其单个样本的正确或错误预测可直接求和并取平均）时计算效率较高，但在应对非可加性或复杂评估指标时则会从根本上失效。以F值(F-score)为例，该指标是对精确率(Precision)与召回率(Recall)进行调和平均计算，无法清晰拆解为独立的样本级贡献，这直接违背了参数检验的核心假设。在此类场景下，自助法重采样便成为不可或缺的工具。该方法通过直接估计经验抽样分布(Empirical Sampling Distribution)，绕开了复杂的解析数学推导，确保无论评估指标多么复杂或呈非线性，严谨的统计验证(Statistical Validation)依然可行且准确。
![关键帧](keyframes/part004_frame_00527480.jpg)
![关键帧](keyframes/part004_frame_00533120.jpg)

## 用于确定测试集规模的功效分析
在选定合适的检验方法后，研究者必须解决一个关键的实际问题：究竟需要多少标注测试数据(Annotated Test Data)？统计功效分析(Statistical Power Analysis)为此提供了严谨的数学框架。通过设定目标显著性阈值(Significance Threshold)（通常为 p < 0.05）、可接受的统计功效水平以及预估的效应量(Effect Size)（例如基线模型(Baseline Model)与新提出模型之间的预期性能差距），研究者即可精确计算出可靠检测出该差异所需的最小测试集规模。例如，若经验表明基线模型的预期准确率为90%，而新方法旨在实现微小但具有实际意义的性能提升，功效分析便能精确量化所需收集的测试样本数量。这种前瞻性规划能够有效避免因统计功效不足(Underpowered)而导致结论模糊的实验，同时通过规避不必要的大规模、高成本标注工作，实现计算与标注资源的最优配置。