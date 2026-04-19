## 用于多模态集成的堆叠架构
![关键帧](keyframes/part007_frame_00000000.jpg)
突破传统刚性顺序流水线(Sequential Pipelines)的局限，“堆叠”(Stacking)架构使模型能够并行融合多种数据模态(Data Modalities)或中间表征(Intermediate Representations)。在堆叠式语音翻译(Stacked Speech Translation)系统中，翻译模型同时接收原始音频(Raw Audio)与转录文本(Transcribed Text)作为输入，以生成目标语言输出。该架构提供了一种内置的交叉验证机制(Cross-validation Mechanism)，使系统能够通过比对音频与文本模态来有效捕获识别错误(Recognition Errors)。此外，它能够有效恢复在纯文本转换过程中丢失的韵律特征(Prosodic Features)或副语言信息(Paralinguistic Information)。例如，相同的转录文本“I read the book”可能对应不同的时态（如过去式或现在时），但堆叠模型可利用音频线索(Audio Cues)在翻译前精准消除歧义(Disambiguation)，从而确定其真实语义。
![关键帧](keyframes/part007_frame_00052240.jpg)

## 迭代优化与扩散范式
迭代优化(Iterative Optimization)通过将模型输出递归地反馈至自身(Recursive Feedback)，实现生成质量的逐步提升。早期的神经网络实现（如推敲网络(Deliberation Networks)）通常结合强化学习(Reinforcement Learning)生成初始草稿，随后通过多轮迭代进行精细化润色。该迭代范式在概念上与现代扩散模型(Diffusion Models)高度契合，后者从纯高斯噪声(Pure Gaussian Noise)出发，通过逐步去噪(Denoising)过程生成连贯的高质量输出。尽管扩散模型训练稳定且在图像生成领域占据主导地位，但受限于自回归语言模型(Autoregressive Language Models)的显著成功，其在文本生成领域的应用仍相对有限；不过，非自回归文本扩散(Non-autoregressive Text Diffusion)依然是当前活跃的研究前沿(Active Research Frontier)。

## Self-Refine：反馈驱动的生成
![关键帧](keyframes/part007_frame_00168039.jpg)
迭代优化在现代大模型中的一个典型实现是 Self-Refine 框架。在该流程中，大语言模型(Large Language Models, LLMs)首先生成初始输出，随后通过生成结构化反馈(Structured Feedback)（如指出逻辑漏洞或代码缺陷）对结果进行自我审查(Self-Review)。随后，模型综合原始输出与自我生成的批评意见(Self-generated Critiques)生成修订版本，并循环此过程直至满足预设的质量标准。该方法在创意写作(Creative Writing)、代码生成(Code Generation)等多元化任务中均展现出显著成效。
![关键帧](keyframes/part007_frame_00187159.jpg)
然而，该方法存在一项关键前提：自我优化(Self-optimization)高度依赖基础模型具备极强的基线能力(Baseline Capability)。唯有处于当前技术前沿(State-of-the-Art, SOTA)的模型才具备充分的逻辑推理(Logical Reasoning)与自我纠错(Self-correction)能力，以稳定执行该迭代循环。能力较弱的模型通常难以准确诊断自身缺陷，往往导致多次迭代后性能停滞甚至出现模型退化(Model Degradation)。
![关键帧](keyframes/part007_frame_00236879.jpg)
![关键帧](keyframes/part007_frame_00248519.jpg)

## 权衡：堆叠系统与级联系统
![关键帧](keyframes/part007_frame_00271760.jpg)
尽管堆叠架构在防止信息丢失(Information Loss)方面具备显著优势，但级联系统(Cascaded Systems)依然被广泛采用，主要归因于两大因素：数据可用性(Data Availability)与实现复杂度(Implementation Complexity)。训练堆叠模型通常依赖严格对齐的多模态数据集(Strictly Aligned Multimodal Datasets)。若缺乏此类数据，工程实践者(Practitioners)往往需合成缺失模态或依赖预生成输出，这将引入高昂的计算开销(Computational Overhead)。相比之下，级联架构允许开发者针对各子任务(Sub-tasks)独立优化并部署专用的现成模型(Off-the-shelf Models)。因此，在数据稀缺或工程资源受限时，级联往往是更为务实(Pragmatic)的工程选择。尽管如此，在数据规模与算力资源(Data Scale and Compute Resources)充足的前提下，堆叠架构仍是性能最优的推荐方案。
![关键帧](keyframes/part007_frame_00279960.jpg)

## 评估贡献度及与 Boosting 的对比
![关键帧](keyframes/part007_frame_00386559.jpg)
评估集成系统中各模型的个体贡献(Individual Contribution)，最直观的方法是分析学习到的插值权重(Interpolation Weights)。这些权重系数直接量化了推理阶段(Inference Phase)系统对各模型预测结果的依赖程度。此外，也可采用消融实验(Ablation Studies)，通过测量添加或移除特定模型组件时整体准确率(Overall Accuracy)的变化量来量化其实际影响。对比迭代优化与传统 Boosting（提升法），两者在作用粒度与适用范围上存在显著差异。Boosting 通常作用于单一的标量分类输出(Scalar Classification Outputs)，通过调整弱分类器(Weak Classifiers)的权重分布来优化整体概率分布。相比之下，迭代优化直接作用于高度复杂的结构化输出(Structured Outputs)（如完整文本段落或代码库），要求模型主动进行内容重写(Content Rewriting)与结构重构(Structural Refactoring)，而非仅仅是对概率质量(Probability Mass)进行重新分配。
![关键帧](keyframes/part007_frame_00441000.jpg)