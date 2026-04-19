## 生成算法简介
![关键帧](keyframes/part000_frame_00000000.jpg)
今天的课程将全面介绍最常见的生成算法，探讨它们的实际应用及其背后的理论基础。讲座将按顺序逐一讲解核心概念，并在各章节之间预留互动与提问环节。

## 作为条件概率分布(Conditional Probability Distribution)的大语言模型
![关键帧](keyframes/part000_frame_00025320.jpg)
![关键帧](keyframes/part000_frame_00071240.jpg)
![关键帧](keyframes/part000_frame_00107240.jpg)
尽管现代模型架构的规模令人印象深刻（例如在数万亿词元(Token)上预训练的 70 亿参数模型），但其基本的数学本质却出奇地简单。这些模型的核心运作机制本质上就是条件概率分布。给定输入 `x` 和迄今已生成的序列，模型会计算下一个词元 `y_j` 的概率。根据链式法则(Chain Rule)，将这些逐步计算的概率相乘，即可确定在给定输入 `x` 的条件下，任意完整输出序列 `y` 的联合概率(Joint Probability)。最终，无论模型架构多么复杂、训练过程多么繁复，其本质都归结为一个单一且明确定义的数学函数：条件概率分布。

## 任务灵活性与概率输出的权衡
![关键帧](keyframes/part000_frame_00139119.jpg)
这种概率框架(Probabilistic Framework)赋予了模型非凡的灵活性。只需重新定义输入 `x` 和期望输出 `y` 的格式，同一个底层模型便能轻松适配机器翻译(Machine Translation)、文本摘要(Text Summarization)及复杂推理(Complex Reasoning)等多种任务。然而，这种设计也伴随着优势与权衡。其积极的一面在于，该分布为衡量模型置信度(Confidence)与校准度(Calibration)提供了天然的指标。对于 `2 + 2 =` 这类确定性提示(Deterministic Prompt)，概率质量(Probability Mass)会高度集中在正确的词元上，表明模型具有极高的置信度。相反，面对“预测某位历史人物最爱的颜色”这类开放式提示(Open-ended Prompt)，概率分布则会趋于平坦，反映出模型内在的不确定性。然而，其主要弊端在于，概率分布本质上会为错误、次优甚至冒犯性的输出分配非零概率。理论保证(Theoretical Guarantees)表明，无论训练数据多么纯净，总不可避免地会有部分概率质量“泄漏”至可能引发幻觉(Hallucination)或质量较低的词元中。

## 祖先采样(Ancestral Sampling)与长尾问题(Long-tail Problem)
![关键帧](keyframes/part000_frame_00234640.jpg)
![关键帧](keyframes/part000_frame_00248279.jpg)
![关键帧](keyframes/part000_frame_00274080.jpg)
![关键帧](keyframes/part000_frame_00316000.jpg)
![关键帧](keyframes/part000_frame_00346159.jpg)
![关键帧](keyframes/part000_frame_00376880.jpg)
为了从该分布中获取有效输出，我们需要引入采样技术(Sampling Techniques)。最直接的方法是祖先采样，即在每个解码步骤(Decoding Step)中，严格按照词元被分配的概率进行随机抽取。尽管该方法能严格遵循模型的真实分布，生成数学意义上准确的样本，但它存在一个致命缺陷：长尾问题。在拥有庞大词表(Vocabulary)的模型（例如包含 32,000 个词元）中，数千个词元各自仅被赋予极低的概率。然而，将这些低概率词元累加后，长尾部分往往能占据总概率质量的相当大比例，通常高达 50% 左右。因此，祖先采样极易选中无意义或低概率的词元，从而导致生成内容缺乏连贯性。为此，必须在采样前采取特定策略对分布进行剪枝(Pruning)或约束。

## Top-K 采样(Top-K Sampling)：基于词元数量的约束
![关键帧](keyframes/part000_frame_00421799.jpg)
![关键帧](keyframes/part000_frame_00439640.jpg)
解决长尾问题的一种直接方案是 Top-K 采样。该方法在每一步生成时，将候选词元池严格限制为概率最高的 `K` 个词元。通过直接截断低概率的尾部区域，该方法显著降低了生成无关或无意义文本的风险。然而，Top-K 采样的效果高度依赖于概率分布的形态。在具有多个合理续写选项的平坦分布(Flat Distribution)中，固定的 `K` 值（例如 6）可能仅能覆盖极少部分的概率质量。相反，在由一两个词元主导的尖峰分布(Peaked Distribution)中，同样的 `K` 值却可能覆盖接近 100% 的概率质量。这虽然使约束极为有效，但也可能导致采样过程过于僵化。

## Top-P 采样(Top-P Sampling)、Epsilon 采样(Epsilon Sampling)与分布整形(Distribution Shaping)
![关键帧](keyframes/part000_frame_00499800.jpg)
![关键帧](keyframes/part000_frame_00506719.jpg)
![关键帧](keyframes/part000_frame_00595040.jpg)
为克服 Top-K 采样的僵化缺陷，Top-P 采样（或称核采样(Nucleus Sampling)）引入了基于累积概率(Cumulative Probability)的动态阈值，不再依赖固定的词元数量。通过设定目标概率质量 `P`（例如 0.94），模型会按概率从高到低依次累加词元，直至累积总和达到或超过 `P`。该方法能够自适应概率分布的形态：当模型不确定性较高时，候选池会自动扩大以纳入更多词元；而当模型置信度较高时，候选范围则会迅速收缩至少数几个主导词元。另一种变体是 Epsilon 采样，它通过设定严格的绝对概率阈值(Absolute Probability Threshold)来过滤候选词元，确保仅保留概率不低于基线水平的词元。尽管这些方法成功裁剪了长尾，但它们并非优化生成的唯一手段。在进一步提升生成质量的逻辑链条中，下一步的关键操作是在采样执行前，对分布本身的形态（如峰值高度或尖锐程度）进行动态调整。