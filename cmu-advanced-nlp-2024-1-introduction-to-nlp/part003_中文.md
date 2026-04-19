## 手动词典扩展与启发式方法的局限
课程伊始，讲师分析了一篇措辞微妙的电影评论。该评论未使用明显的正面或负面关键词，却传达了中性情感(Sentiment)。为展示基于规则系统(Rule-based System)的脆弱性，讲师尝试通过向情感词典(Sentiment Lexicon)中迭代添加“crass”（粗俗）和“engaging”（引人入胜）等特定词汇，来手动修补分类器。在训练集(Training Set)和开发集(Development Set)上评估更新后的模型后，准确率仅实现微幅提升。这一练习凸显了人工维护词表的繁琐与低效，证明迭代式的词典修补并非解决现实自然语言处理(Natural Language Processing, NLP)任务的可扩展方案(Scalable Solution)。
![关键帧](keyframes/part003_frame_00000000.jpg)
![关键帧](keyframes/part003_frame_00057120.jpg)
![关键帧](keyframes/part003_frame_00066640.jpg)
![关键帧](keyframes/part003_frame_00075160.jpg)
![关键帧](keyframes/part003_frame_00094600.jpg)

## 情感分析中的核心语言学挑战
讲师系统性地归纳了僵化的基于规则方法在处理复杂文本时失效的根本原因。**低频词(Low-frequency Words)**（例如“purport”或“mucking up”等生僻短语）极易绕过简单的词表匹配，这要求系统整合海量的外部情感资源。**词形变化与形态学(Inflection and Morphology)**构成了另一道障碍；若缺乏专门的词干提取(Stemming)或词形还原(Lemmatization)流水线(Pipeline)，能够匹配“magnificence”（宏伟）的系统将无法识别其派生词“magnificently”（宏伟地）。**否定表达(Negation)**会彻底反转语义极性(Semantic Polarity)（例如“not nearly as dreadful”[远没那么可怕] 或“not serving up laughs”[未能带来欢笑]），这需要复杂的句法解析(Syntactic Parsing)或语义解析(Semantic Parsing)才能正确理解。此外，**隐喻与类比(Metaphor and Analogy)**（如“has all the depth of a wading pool”[像涉水池一样毫无深度]，或暗示导演会为下一部电影“rob people”[坑骗观众]）高度依赖文化背景与隐含意义，这是基础词法规则(Lexical Rules)无法捕捉的。最后，处理**多语言内容(Multilingual Content)**暴露出该方法完全缺乏可扩展性(Scalability)，因为每引入一门新语言都需要从零开始定制一套全新的规则集。
![关键帧](keyframes/part003_frame_00113560.jpg)
![关键帧](keyframes/part003_frame_00127520.jpg)
![关键帧](keyframes/part003_frame_00136399.jpg)
![关键帧](keyframes/part003_frame_00142239.jpg)
![关键帧](keyframes/part003_frame_00168719.jpg)
![关键帧](keyframes/part003_frame_00174399.jpg)
![关键帧](keyframes/part003_frame_00236760.jpg)

## 基于规则原型的诊断价值与向机器学习的过渡
尽管存在上述严重局限，讲师仍强调基于规则的系统在教学与实际工程中具有重要价值。在项目初期构建一个简单的启发式模型(Heuristic Model)可作为快速诊断工具，帮助工程师清晰识别哪些语言现象增加了任务难度，以及手动规则工程(Rule Engineering)在何处遭遇瓶颈。在明确这些系统边界后，课程正式转向机器学习范式(Machine Learning Paradigm)。该方法从手工设计规则转向数据驱动优化(Data-driven Optimization)，并严格采用训练集、开发集与测试集(Test Set)的独立划分。模型通过标准学习算法，联合优化特征表示与权重参数，并自动学习决策规则(Decision Rules)。
![关键帧](keyframes/part003_frame_00245799.jpg)
![关键帧](keyframes/part003_frame_00296440.jpg)

## 作为机器学习基线的词袋模型
课程介绍的首个机器学习基线模型(Baseline Model)是**词袋模型（Bag-of-Words, BoW）**。在该架构中，每个词被映射为独热向量(One-hot Vector)，这些向量在句子层面进行累加，从而生成词频向量(Term Frequency Vector)。随后，该向量与学习到的权重向量(Weight Vector)进行点积运算，以计算分类得分(Classification Score)。关键在于，尽管 BoW 的特征提取(Feature Extraction)过程是固定的（即非数据驱动学习），但其权重会在训练过程中自动优化。讲师引导全班思考：前述的语言学挑战（低频词、词形变化、否定或隐喻）中，哪些可以通过这种基于学习的方法(Learning-based Approach)得到缓解。讨论指出，尽管 BoW 能够凭借数据覆盖自然适应词汇变体与频率分布，但诸如否定和隐喻等更深层次的句法结构与上下文依赖(Contextual Dependency)问题，对于简单的固定特征模型(Fixed-feature Model)而言依然充满挑战。
![关键帧](keyframes/part003_frame_00390799.jpg)
![关键帧](keyframes/part003_frame_00412000.jpg)
![关键帧](keyframes/part003_frame_00424880.jpg)
![关键帧](keyframes/part003_frame_00450280.jpg)
![关键帧](keyframes/part003_frame_00480560.jpg)