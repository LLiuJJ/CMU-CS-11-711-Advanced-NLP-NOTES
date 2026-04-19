## NLP 课程的演进与实践重心的转变
讲师在开场答疑环节，重点指出了自上期课程以来，自然语言处理(Natural Language Processing)实践领域发生的重大转变。过去，对于致力于构建实用系统的开发者而言，模型微调(Fine-tuning)曾是必不可少的环节；而如今，它已在很大程度上转变为可选步骤。这一观察源于实际工程经验，反映了当前业界的主流工作流(Workflow)：从业者正将重心前移至数据处理流水线(Data Pipeline)的早期阶段。尽管探讨这一趋势的学术文献可能尚不充分，讲师仍鼓励学生在课程的 Piazza 讨论区分享相关论文或见解，以促进协作交流。
![关键帧](keyframes/part002_frame_00000000.jpg)
![关键帧](keyframes/part002_frame_00032240.jpg)
![关键帧](keyframes/part002_frame_00040640.jpg)

## 引入基于规则的情感分析
随后，课程进入构建基于规则的情感分析(Sentiment Analysis)系统环节。讲师特意将这种方法形容为实际生产环境(Production Environment)中的“糟糕主意”，并以此作为教学切入点，旨在在引入机器学习(Machine Learning)方案之前，充分暴露基于规则的方法所固有的局限性与复杂性。课程强调，针对客户评论的情感分析是现代 NLP 中最具价值的任务之一，可直接赋能产品开发、客户满意度追踪以及社交媒体监控。该任务被正式定义为一个句子级分类(Sentence-level Classification)问题，旨在将文本输入映射为三类输出标签(Output Labels)：正面、中性或负面。
![关键帧](keyframes/part002_frame_00104120.jpg)

## 理论框架：特征、打分与决策规则
任何分类模型(Classification Model)的架构均依赖于三个按序执行的组件：特征提取(Feature Extraction)、得分计算(Scoring)与决策函数(Decision Function)。特征提取负责从输入文本中抽取出显著属性。得分计算则通过数学运算实现，通常表现为计算特征向量(Feature Vector)与权重向量(Weight Vector)（针对二分类任务(Binary Classification)）或权重矩阵(Weight Matrix)（针对多分类任务(Multi-class Classification)）之间的点积(Dot Product)。最后，决策规则将这些得分映射为最终输出。标准决策规则包括：针对二分类任务的基于阈值的分类(Threshold-based Classification)（针对置信度较低的模糊得分可设置弃权机制(Abstention Mechanism)），以及针对多分类任务的选取最高得分类别(argmax)。讲师指出，诸如自一致性(Self-consistency)和最小贝叶斯风险(Minimum Bayes Risk)等更为复杂的决策机制，将在后续讲解文本生成(Text Generation)时进行深入探讨。
![关键帧](keyframes/part002_frame_00218560.jpg)

## 代码演示：特征提取与权重分配
进入实操演示环节，讲师在 VS Code 环境中编写并实现了该基于规则的分类器(Rule-based Classifier)。代码从特征提取开始，使用了硬编码(Hard-coded)的正面（如“good”）与负面（如“bad”）词表(Lexicon)。系统仅统计这些词汇在每条输入句子中出现的频次(Frequency)。为完善特征表示(Feature Representation)，代码追加了一个固定值为 1 的偏置(Bias)特征，最终生成一个三维特征向量：`[count_good, count_bad, bias]`。该结构化向量为后续的打分阶段做好了数据准备，并构建了一个清晰且具备可解释性(Interpretability)的特征空间(Feature Space)。
![关键帧](keyframes/part002_frame_00347200.jpg)
![关键帧](keyframes/part002_frame_00363639.jpg)
![关键帧](keyframes/part002_frame_00370599.jpg)
![关键帧](keyframes/part002_frame_00377679.jpg)
![关键帧](keyframes/part002_frame_00393199.jpg)

## 打分执行与决策逻辑实现
讲师设定了明确的特征权重(Feature Weights)以计算最终分类得分：每个正面词计 +1 分，每个负面词计 -1 分，偏置项计 -0.5 分。得分通过特征向量与权重向量之间的点积运算得出。随后应用基于阈值的决策规则：得分大于零判定为正面（标签 1），小于零判定为负面（标签 -1），等于零则判定为中性（标签 0）。将该分类器应用于电影评论数据集(Movie Review Dataset)进行测试（数据集中包含一条明显正面、赞扬演员潜力的评论）后，程序顺利执行。然而，最终准确率(Accuracy)仅徘徊在 43% 左右，直接暴露出这种朴素方法(Naive Approach)的显著缺陷。
![关键帧](keyframes/part002_frame_00470159.jpg)
![关键帧](keyframes/part002_frame_00504640.jpg)

## 错误分析的关键作用
在本节末尾，讲师着重强调了 NLP 工程中的一项基础最佳实践(Best Practice)：全面的错误分析(Error Analysis)。在针对特定任务优化系统时，系统性地审查失败案例往往是实现性能提升的最有效途径。演示中展示了一个基础的错误分析流程，该流程会随机采样预测正确与错误的样本，并将真实标签(Ground Truth)与模型输出(Model Predictions)进行比对。通过人工检视这些误分类(Misclassification)样本，开发者能够识别出僵化的基于规则的系统所无法捕捉的语言模式、上下文歧义(Contextual Ambiguity)或特征缺失，从而为构建更稳健、数据驱动(Data-driven)的解决方案奠定基础。
![关键帧](keyframes/part002_frame_00586520.jpg)