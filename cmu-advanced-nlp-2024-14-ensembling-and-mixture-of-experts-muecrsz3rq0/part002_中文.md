## 处理零概率与词表不匹配问题
![关键帧](keyframes/part002_frame_00000000.jpg)
在模型组合(Model Combination)过程中，为某些词元(Token)分配零概率(Zero Probability)会带来显著的评估挑战(Evaluation Challenges)，因为这实际上会阻止模型在解码时选择这些词元。若两个模型拥有完全不同的词表(Vocabulary)，协调它们的输出将变得极其复杂。然而，线性插值(Linear Interpolation)提供了一种实用的变通方案：通过仅聚焦于重叠词表(Overlapping Vocabulary)，系统可安全忽略不匹配的词元。在此配置下，即便某一模型对词表外(Out-Of-Vocabulary, OOV)词元分配了零概率，另一模型仍可提供有效的概率分布(Probability Distribution)，从而防止组合输出失效。尽管具备此灵活性，整合词表差异巨大的模型仍需在架构层面进行审慎考量。

## 误差相关性对集成的影响
![关键帧](keyframes/part002_frame_00148799.jpg)
![关键帧](keyframes/part002_frame_00203239.jpg)
![关键帧](keyframes/part002_frame_00212319.jpg)
模型集成(Model Ensembling)带来的性能增益在很大程度上依赖于一个核心假设：即各模型的误差(Error)是不相关的(Uncorrelated)。若误差完全相关(Perfectly Correlated)——例如复制同一模型并重复运行两次——集成将无法带来任何改进，但通常也不会损害原有性能。尽管理论上可构造降低准确率(Accuracy)的干预手段，但在关于训练数据分布(Training Data Distribution)与模型行为的合理假设下，组合多个预测器(Predictors)始终能为整体性能提供稳健的提升。

## 带负权重的对数线性插值
![关键帧](keyframes/part002_frame_00233479.jpg)
![关键帧](keyframes/part002_frame_00240359.jpg)
对数线性插值(Log-linear Interpolation)的一个高效扩展在于支持负插值系数(Negative Interpolation Coefficients)。与严格要求权重为正且总和为1的标准线性组合(Standard Linear Combination)不同，对数线性插值在对数空间(Log-space)中运作，天然允许系数为负。这使得特定模型能够充当“负向证据”(Negative Evidence)，主动抑制其他模型分配的预测概率。该技术自21世纪00年代中期起便已成功应用于机器翻译(Machine Translation)研究。通常，其集成架构围绕一个核心模型(Core Model)、一个正向模型(Positive Model，用于提升期望输出)以及一个负向模型(Negative Model，用于抑制非期望模式)来构建。

## 实际应用：领域适应与安全过滤
![关键帧](keyframes/part002_frame_00285879.jpg)
![关键帧](keyframes/part002_frame_00486840.jpg)
![关键帧](keyframes/part002_frame_00494919.jpg)
负权重在领域适应(Domain Adaptation)与内容审核(Content Moderation)等实际场景中表现尤为出色。在机器翻译中，当特定领域的平行语料(Parallel Corpora)稀缺时（例如医疗文本相较于通用新闻），可将核心翻译模型与正向的领域内(In-domain)语言模型及负向的领域外(Out-of-domain)语言模型相结合。通过在数学运算上减去领域外概率并叠加领域内概率，系统无需经历昂贵的重新训练(Retraining)过程即可快速适应新语境。类似地，DExperts框架将此方法应用于安全性控制(Safety Control)：将一个强大的基础模型(Base Model)与在有毒文本(Toxic Text)上训练的负向模型，以及在清洁文本(Clean Text)上训练的正向模型进行配对。负权重会主动抑制有害语言(Harmful Language)的生成，从而有效地对文本生成流水线(Generation Pipeline)进行“去毒化”(Detoxification)。

## 生成模型多样性：Dropout 与 Bagging
![关键帧](keyframes/part002_frame_00575400.jpg)
除了训练完全独立的网络架构(Network Architectures)外，还可从现有网络中高效生成多个模型变体(Model Variants)。Dropout 传统上作为一种在训练期间随机停用节点的正则化技术(Regularization Technique)，在推理阶段(Inference Phase)同样可被重新利用。通过在生成过程中重复应用 Dropout，单个网络实质上便转化为一个由不同子网络(Subnetworks)构成的集成，其预测结果会被动态聚合——这一理念恰恰是当初提出 Dropout 的理论动机所在。另一项基础技术是 Bagging（Bootstrap Aggregating，自助聚合法），该方法通过对训练数据集(Training Dataset)进行有放回重采样(Sampling with Replacement)，构建多个规模一致的数据子集。在每个自助子集(Bootstrap Subset)上独立训练模型，自然会衍生出具备多样性的预测器(Predictors)，这些预测器在组合使用时往往能发挥极佳效果。