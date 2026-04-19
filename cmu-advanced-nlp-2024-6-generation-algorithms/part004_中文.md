## 置信度与语义校准
模型的置信度得分(Confidence Score)通常仅反映单一确切词元序列(Token Sequence)的概率，而未能捕捉更广泛的语义空间(Semantic Space)。生成任务中的真实置信度应当考虑所有传达相同底层含义的序列的聚合概率(Aggregated Probability)。厘清这一区别，是应对当前持续挑战、校准语言模型(Language Model)以实现可靠长序列生成的基础。
![关键帧](keyframes/part004_frame_00000000.jpg)

## MBR 变体：输出集成与自洽性
最小贝叶斯风险(Minimum Bayes Risk, MBR)框架很自然地延伸到了各类推理时策略(Inference-time Strategies)中。跨模型**输出集成(Output Ensembling)**将来自不同模型架构(Model Architectures)的生成结果视为一个统一集合，利用嵌入相似度(Embedding Similarity)来识别达成跨模型共识的回答。另一个强大的变体是**自洽性(Self-Consistency)**，在数学或逻辑推理任务中尤为有效。该方法通过提示(Prompt)模型生成思维链(Chain-of-Thought)、采样多条推理路径，并忽略中间推导步骤，直接对最终答案进行多数投票(Majority Voting)，从而优先选择出现频率最高的结论。这实质上是一种采用精确匹配(Exact Match)指标的 MBR 方法，其核心在于倾向于选择高概率、低风险的共识结果，而非依赖单一推理轨迹(Reasoning Trajectory)。
![关键帧](keyframes/part004_frame_00033200.jpg)
![关键帧](keyframes/part004_frame_00038760.jpg)

## 推理范式与伪参考序列要求
文本生成技术通常可分为三大范式：直接采样(Direct Sampling)、结构化搜索(Structured Search，如集束搜索)和样本重排序(Sample Reranking，如 MBR)。一个常见的疑问是：为何仅对模型权重进行平均(Weight Averaging)不被归类为 MBR？MBR 的决定性特征在于，需将候选输出与多个伪参考序列(Pseudo-references)进行显式比较，以量化风险与一致性(Consistency)。权重平均仅仅是将多个概率分布合并为一个新的单一模型分布，完全跳过了基于共识进行选择时所必需的、对生成序列进行交叉比较的关键步骤。
![关键帧](keyframes/part004_frame_00058960.jpg)

## 朴素约束执行的挑战
除了优化通用质量(General Quality)外，实际应用场景往往施加严格的约束条件(Constraints)，例如禁止模型提及特定主题。尽管提示指令(Prompt Instructions)提供了一个初始切入点，但其固有的脆弱性(Fragility)众所周知；随着模型输出发生漂移或寻找到语义上的变通途径(Semantic Workarounds)，这些约束极易被绕过。一种更为直接的干预方法是 Logits 调整(Logit Adjustment)：在 Softmax 操作之前，对禁止词元(Forbidden Tokens)施加极大的负偏置，强制将其生成概率降为零。然而，该方法过于僵化。它缺乏上下文感知能力(Context Awareness)（例如会错误地屏蔽词汇在特定语境下的正面用法），难以处理多词元短语(Multi-token Phrases)，且无法应对生成序列后期出现的语义关联词或同义词。
![关键帧](keyframes/part004_frame_00094440.jpg)
![关键帧](keyframes/part004_frame_00100080.jpg)

## 拒绝采样的低效性
另一种常见策略是拒绝采样(Rejection Sampling)：批量生成候选输出，利用分类器(Classifier)或关键词过滤器(Keyword Filter)验证其合规性(Compliance)，并直接丢弃任何违反约束的结果。尽管事后验证(Post-hoc Verification)逻辑直接，但当约束条件与模型固有的训练倾向(Inherent Training Bias)发生冲突时，该方法的计算开销(Computational Overhead)将呈指数级增长，变得难以承受。若模型强烈倾向于生成被禁止的内容，为获取单个合规输出可能需生成并过滤数十万份样本，这使其完全无法胜任实时(Real-time)或大规模可扩展(Scalable)的推理任务。
![关键帧](keyframes/part004_frame_00121080.jpg)

## FUDGE：动态约束感知解码
为克服上述局限，**FUDGE** 等前沿方法将约束处理(Constraint Handling)直接集成至解码过程(Decoding Process)中。与传统屏蔽词元或事后过滤完整序列的做法不同，FUDGE 通过将基础模型(Base Model)的词元分布与来自约束预测模型(Constraint Predictor)的辅助分布(Auxiliary Distribution)相融合，实现对生成过程的主动引导。例如，若需强制文本语气正式，辅助模型会实时估算当前已生成的部分序列最终满足该约束的概率。通过将两者分布相乘，解码步骤会动态调整权重，优先选择在语言模型下概率较高、且极可能满足目标约束的词元。该方法无需依赖高昂的拒绝循环(Rejection Loop)成本，即可高效生成合规文本。
![关键帧](keyframes/part004_frame_00190399.jpg)
![关键帧](keyframes/part004_frame_00210560.jpg)
![关键帧](keyframes/part004_frame_00305840.jpg)
![关键帧](keyframes/part004_frame_00315560.jpg)
![关键帧](keyframes/part004_frame_00333400.jpg)

## 机制与预测性引导
FUDGE 的核心创新在于其能够基于部分生成序列(Partially Generated Sequence)，实时预测最终输出对约束条件的合规性。这种预测性引导(Predictive Guidance)在每一个词元生成步骤中持续重塑采样分布(Sampling Distribution)，确保所生成的序列从起始到终结均能自然地与目标属性(Target Attribute)保持一致。该方法的命名巧妙契合了其机制：它采用一种温和且前瞻的引导策略，在完整生成周期内持续推动模型满足约束条件，而非依赖突兀的硬性修正、僵化的词元屏蔽或低效的后处理过滤器(Post-processing Filters)。
![关键帧](keyframes/part004_frame_00355120.jpg)
![关键帧](keyframes/part004_frame_00399159.jpg)
![关键帧](keyframes/part004_frame_00430359.jpg)
![关键帧](keyframes/part004_frame_00502560.jpg)