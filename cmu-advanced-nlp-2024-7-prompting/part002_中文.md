## 通过预训练对齐优化输出映射
在为分类(Classification)或评分(Rating)任务设计提示词输出格式时，使其与模型的预训练数据分布(Pre-training Data Distribution)保持一致至关重要。例如，将数值型的 1 至 5 星评分转换为描述性标签(Descriptive Labels)（如“极好、好、一般、差、极差”），能显著提升模型评估回复时的准确性。这种改进源于描述性词元(Token)在评论文本之后自然出现的频率，远高于孤立的数字。提示词工程(Prompt Engineering)中的一条基本经验法则是：避免强迫大语言模型(Large Language Model, LLM)输出其在预训练阶段极少接触的格式。依据自然语料中的词频与共现概率来设定预期输出，能够获得更加稳定且准确的模型性能。
![关键帧](keyframes/part002_frame_00000000.jpg)
![关键帧](keyframes/part002_frame_00083520.jpg)

## 少样本提示与上下文学习基础
少样本提示(Few-shot Prompting)常与上下文学习(In-context Learning)互换使用，指在提供核心指令的同时，附带少量任务示例(Demonstrations)。尽管能力较强的模型通常能有效遵循零样本(Zero-shot)指令，但少样本示例在规范输出格式方面具有极高价值。对于参数规模较小或能力较弱的模型而言，示例的引导作用更为关键，否则模型极易生成冗长或偏离格式的回复。这些术语仅侧重点不同：“零样本”指不提供任何示例，“少样本”指在提示词中包含若干示例，而“上下文学习”则强调模型是利用上下文窗口(Context Window)动态学习任务模式，而非依赖传统的参数微调(Parameter Fine-tuning)。
![关键帧](keyframes/part002_frame_00168839.jpg)

## API 实现：OpenAI 与开源架构的差异
实现少样本提示需精心设计 API 调用结构，以防止模型将示例与真实输入混淆。针对 OpenAI 模型，官方推荐的做法是利用 `example_user` 和 `example_assistant` 等特定的 `name` 字段，将示例嵌入至系统消息(System Message)中。该机制确保模型将示例识别为结构性指导(Structural Guidance)，而非连续对话历史(Conversation History)的一部分，从而在保障数据隐私性的同时提升了提示词的稳定性。相比之下，诸多开源模型(Open-source Models)依赖固定的提示词模板(Prompt Template)，可能无法正确识别或解析上述 `name` 字段。若将 OpenAI 专属格式直接套用于开源架构，极易引发系统消息解析错误。因此，开发者必须针对特定模型调整少样本结构，以严格契合其原生模板语法。
![关键帧](keyframes/part002_frame_00346280.jpg)
![关键帧](keyframes/part002_frame_00418039.jpg)

## 对示例构建与标签分布的敏感性
大语言模型(LLM)对上下文示例的构建细节表现出高度敏感性。研究表明，仅对相同示例进行重排序(Reordering)就可能导致性能指标(Performance Metrics)产生显著波动，该现象在参数规模较小的模型中尤为突出。标签分布平衡(Label Distribution Balance)同样至关重要；对于存在严重类别倾斜(Class Imbalance)的数据集（如亚马逊评论），在示例中镜像其真实分布有助于提升准确率。反之，对于类别均衡的数据集，采用混合标签的示例策略通常表现更优。此外，标签覆盖率(Label Coverage)在多分类任务(Multi-class Classification)或回归任务(Regression Task)中亦不可或缺。例如，在评估机器翻译(Machine Translation)质量时，提供涵盖高、中、低分段的示例，能有效辅助模型校准其连续型回归输出(Continuous Regression Output)。
![关键帧](keyframes/part002_frame_00428320.jpg)

## 示例优化的不可预测性
尽管学术界已开展大量实证研究，但在构建最优上下文示例方面，至今仍缺乏普适的“金标准”(Gold Standard)。示例排序、标签平衡与覆盖率的影响程度，高度依赖于具体任务、数据集偏斜度(Data Skew)及模型架构，这使得示例筛选过程充满不确定性。尽管已有诸多研究提出自动化示例选择或优化算法，但在工程实践中，开发者通常仍需针对具体应用场景进行对照实验，并通过迭代调整(Iterative Refinement)少样本示例，方能获取最稳健且一致的模型表现。
![关键帧](keyframes/part002_frame_00572040.jpg)