## 提示词基础介绍
提示词技术(Prompting)在过去几年中已演变为与人工智能(AI)模型交互的标准范式。其核心机制是向预训练模型(Pre-trained Model)提供一段文本提示(Prompt)，以指定任务并引导模型生成预测结果。这正是用户与 ChatGPT 及类似平台交互的标准方式。基础提示词的工作原理是将一段文本字符串附加到输入序列中，随后让模型生成后续内容。该续写过程可采用多种解码策略(Decoding Strategy)，例如束搜索(Beam Search)、祖先采样(Ancestral Sampling)、最小贝叶斯风险(MBR)或自一致性(Self-Consistency)。
![关键帧](keyframes/part000_frame_00000000.jpg)
![关键帧](keyframes/part000_frame_00010160.jpg)

## 模型规模的影响与生成参数
为了直观展示提示词在实际场景中的应用效果，我们可以使用提示词“当狗看到松鼠时，它通常会”来对比 GPT-2 Small 与 GPT-2 XL 的输出结果。参数较小的模型生成的回复略显碎片化，而规模更大的 XL 模型则能生成更为连贯的文本续写。这些初始示例采用了温度(Temperature)为 1 的祖先采样策略，且未启用 top-p 或 top-k 过滤(Filtering)，从而直观地展现了模型对下一个词元(Token)的原始概率预测。
![关键帧](keyframes/part000_frame_00026160.jpg)
随后，演示者展示了修改生成参数(Generation Parameters)如何直接改变输出结果。通过在代码中将 `top_k` 设置为 50、`top_p` 设置为 0.95，模型的解码策略随之发生变化。使用调整后的参数运行模型会生成截然不同的续写内容，这凸显了超参数选择对最终输出质量与风格的显著影响。
![关键帧](keyframes/part000_frame_00131000.jpg)
![关键帧](keyframes/part000_frame_00141079.jpg)
![关键帧](keyframes/part000_frame_00153119.jpg)
![关键帧](keyframes/part000_frame_00171440.jpg)

## 预训练语言模型与指令微调系统
此处选用 GPT-2 Small 和 XL 作为基础示例，是因为它们属于纯粹的预训练语言模型(Pre-trained Language Model)，仅针对下一个词元预测(Next-token Prediction)任务进行了优化。其输出结果本质上反映了模型从海量训练语料中学习到的统计概率。然而，现代大语言模型通常会经过指令微调(Instruction Fine-tuning)或基于人类反馈的强化学习(Reinforcement Learning from Human Feedback, RLHF)处理，这从根本上改变了它们对相同提示词的响应模式。例如，面对相同的输入提示，经过指令微调的模型可能会生成更具行动导向性、或出乎意料地带有更强语气（如攻击性）的回复。
![关键帧](keyframes/part000_frame_00199640.jpg)
![关键帧](keyframes/part000_frame_00216359.jpg)
通过调整生成参数，可以引导这些模型生成更符合语料分布特征或更贴合上下文的回复。这充分证明，在当代提示词工程(Prompt Engineering)中，模型的训练方法与解码设置均起着至关重要的作用。
![关键帧](keyframes/part000_frame_00235239.jpg)

## 面向 NLP 任务的提示词工作流
除了简单的文本续写（例如 Gmail 或手机输入法的自动补全功能），提示词技术还被广泛应用于解决结构化的自然语言处理(Natural Language Processing, NLP)任务。标准工作流通常包含三个步骤：使用实际输入填充提示词模板(Prompt Template)、引导模型生成预测结果，以及对输出进行后处理。例如，给定输入文本“I love this movie”，可将其嵌入模板“{input} Overall, it was ”中，格式化为完整的提示词。随后模型将自动补全空白处的内容，从而高效地充当零样本情感分类器(Zero-shot Sentiment Classifier)。
![关键帧](keyframes/part000_frame_00324520.jpg)

## 对话提示词格式与 OpenAI 消息结构
当前广泛流行的提示词风格是对话提示词格式(Conversational Prompt Format)，该格式专为聊天机器人微调(Chatbot Fine-tuning)而设计，但同样适用于基础模型。此类格式通常遵循 OpenAI 消息结构(Message Structure)，将交互过程组织为包含 `role`（角色）与 `content`（内容）键值对的字典列表。其中，`system` 角色用于提供全局指令以设定模型行为基调，`user` 角色承载用户输入，而 `assistant` 角色则记录模型的回复。这种通过交替排列用户与助手消息的结构，天然支持多轮对话，并能清晰界定对话历史上下文。
![关键帧](keyframes/part000_frame_00361080.jpg)

## 不同模型的底层模板结构
尽管结构化的对话格式看似复杂，但大多数模型实际上仅在后台将这些消息转换为原始的字符串序列。不同的模型架构依赖特定的模板语法来正确解析指令。例如，Llama 对话模型会使用专用特殊令牌(Special Tokens)包裹指令与系统消息，从而清晰界定系统提示词的边界与用户输入的起始位置。相比之下，Alpaca 模型采用更为简洁的 `### Instruction:` 与 `### Response:` 标记格式，并未严格区分系统提示与用户输入角色。确保提示词模板与模型预训练或微调时的格式严格对齐，是发挥模型最佳性能的关键。
![关键帧](keyframes/part000_frame_00421280.jpg)
![关键帧](keyframes/part000_frame_00437640.jpg)
![关键帧](keyframes/part000_frame_00472039.jpg)
![关键帧](keyframes/part000_frame_00501760.jpg)

## 使用提示词工程工具包进行统一调用
为应对不同模型架构间差异化的模板需求，开发者通常依赖 `litellm` 等统一接口工具包(Unified Interface Toolkit)。此类工具允许用户直接使用标准的 OpenAI 格式编写提示词，工具包内部会自动将其转换为目标模型所需的特定模板格式或 API 调用，兼容模型涵盖 Alpaca、Llama 2 Chat、Ollama 及 Mistral 等。尽管各模型的底层语法存在细微差异，但确保提示词格式与模型预期输入严格匹配，始终是保证输出行为稳定可靠及任务精准执行的核心前提。
![关键帧](keyframes/part000_frame_00531880.jpg)
![关键帧](keyframes/part000_frame_00543080.jpg)
![关键帧](keyframes/part000_frame_00599480.jpg)

---

## 系统消息的目的与提示词注入
现代提示词格式中的“system”（系统消息）主要由 OpenAI 引入，旨在防范提示词注入(Prompt Injection)和越狱(Jailbreaking)等安全漏洞(Security Vulnerabilities)。早期版本中，用户能够通过特定输入操控模型，导致其泄露预设的隐藏指令。为解决该问题，模型经过专门训练，使其在利用系统消息引导行为的同时，严格防止这些底层指令出现在生成的输出文本中。这一设计在确保底层提示词安全性的前提下，依然能够有效引导模型生成符合预期的回复。
![关键帧](keyframes/part001_frame_00000000.jpg)
![关键帧](keyframes/part001_frame_00007240.jpg)
![关键帧](keyframes/part001_frame_00044760.jpg)

## 手动模板创建与部署工作流
在开发阶段，提示词模板(Prompt Template)几乎完全依赖人工设计，而非动态生成。针对指令微调(Instruction Fine-tuning)模型，开发者通常直接编写明确的指令；而对于基础模型(Base Model)，模板则经过精心构思，使其生成的自然文本续写(Natural Text Continuation)能够自然导向期望的答案。尽管模板结构是预先设计并固定不变的，但在测试或实际部署阶段，真实的用户数据会被动态填入模板的占位符(Placeholder)中。
![关键帧](keyframes/part001_frame_00136040.jpg)
![关键帧](keyframes/part001_frame_00265719.jpg)

## 答案预测与生成
将格式化后的提示词输入模型后，模型会调用预设的解码算法(Decoding Algorithm)生成回复。然而，大语言模型(Large Language Model, LLM)的原始输出极少是单一、简洁的词元(Token)。模型往往不会直接输出如“fantastic”这般简短的词汇，而是倾向于生成完整句子，例如“overall, it was a really fantastic movie that I liked a lot.”。这种固有的冗长特性意味着，原始生成内容仅为提取有效结果的第一步。
![关键帧](keyframes/part001_frame_00285000.jpg)
![关键帧](keyframes/part001_frame_00317760.jpg)
![关键帧](keyframes/part001_frame_00323400.jpg)

## 后处理与输出格式化
后处理(Post-processing)旨在将模型的原始生成内容转化为可操作的、结构化的数据。在面向用户的应用中，这一过程通常依赖 Markdown 语法；当要求模型生成表格或 JSON 数据时，模型会输出由 `###` 标题标记或三个反引号包裹的文本，前端随后将其渲染为排版整洁且具备语法高亮(Syntax Highlighting)的格式。鉴于大语言模型本质上只能输出词元序列，采用基于文本的标记语言(Markup Language)成为连接原始生成内容与人类可读界面(Human-readable Interface)的高效桥梁。
![关键帧](keyframes/part001_frame_00365200.jpg)
![关键帧](keyframes/part001_frame_00371840.jpg)
![关键帧](keyframes/part001_frame_00414039.jpg)
![关键帧](keyframes/part001_frame_00443120.jpg)
在自动化流水线(Automated Pipeline)中，后处理则依赖于启发式提取(Heuristic Extraction)来分离出有效信号。具体方法包括识别用于情感分类(Sentiment Classification)的关键词、提取用于回归任务(Regression Task)的数值，或捕获反引号间的代码片段(Code Snippet)以供后续程序执行。此类提取策略在生产环境应用与学术基准测试(Benchmark Testing)中均属标准实践。
![关键帧](keyframes/part001_frame_00451599.jpg)

## 将生成文本映射到结构化标签
最后阶段通常涉及将提取的文本映射至标准化的类别标签(Categorical Label)或连续值(Continuous Value)。例如，可通过程序将生成的关键词（如“fantastic”）映射至“positive（积极）”这一情感类别。该技术在执行从评论文本中提取一至五星级评分等结构化任务(Structured Task)时至关重要。通过在多样化的描述性短语与固定标签之间建立稳健的多对一映射(Many-to-one Mapping)，开发者能够从本质上具有随机性(Stochastic Nature)的语言模型中获取更一致、更可靠的输出结果。
![关键帧](keyframes/part001_frame_00537480.jpg)
![关键帧](keyframes/part001_frame_00543280.jpg)
![关键帧](keyframes/part001_frame_00566720.jpg)
![关键帧](keyframes/part001_frame_00582240.jpg)
![关键帧](keyframes/part001_frame_00590400.jpg)
![关键帧](keyframes/part001_frame_00603080.jpg)

---

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

---

## 上下文学习生效的机制
探究上下文学习(In-context Learning)为何有效，揭示了关于模型行为的深刻见解。即使提供的示例包含完全随机或错误的标签（准确率(Accuracy)为0%），模型的表现依然显著优于零样本基线(Zero-shot Baseline)。这表明模型并非在传统梯度更新(Gradient Update)的意义上从示例中“学习”；相反，它利用这些示例来理解预期的输出格式、结构模式(Structural Patterns)以及标签的命名惯例。这种能力的根源在于预训练数据(Pre-training Data)的分布特性。互联网语料中包含海量重复且结构化的内容，例如数学题与问答对(Question-Answer Pairs)。为了准确预测下一个词元(Next-token Prediction)，模型在预训练阶段自然地习得了识别并遵循此类模式化结构的能力，从而使其能够在推理(Inference)阶段动态适应新的少样本提示(Few-shot Prompting)。
![关键帧](keyframes/part003_frame_00000000.jpg)
![关键帧](keyframes/part003_frame_00007160.jpg)

## 上下文长度敏感性与收益递减
增加示例数量并不总能带来性能提升，有时甚至会显著降低准确率。在二分类(Binary Classification)等任务中，盲目增加示例往往会导致性能下降。这是因为过长的上下文窗口(Context Window)会将核心指令推离模型注意力机制(Attention Mechanism)的聚焦范围，导致模型“遗忘”关键信息或受到上下文噪声(Contextual Noise)的干扰。因此，构建最优提示词(Optimal Prompt)仍更像一门实践艺术，而非精确科学。输出结果往往难以预测，从业者应意识到，随着上下文长度的增加，模型性能通常呈现波动趋势，而非线性增长。
![关键帧](keyframes/part003_frame_00137279.jpg)
![关键帧](keyframes/part003_frame_00169799.jpg)

## 标签覆盖率与数据分布
当探讨上下文示例是否应严格遵循目标数据集的自然分布时，业界共识倾向于优先考虑标签覆盖率(Label Coverage)，而非绝对的分布代表性。即便某些少数类标签(Minority Class Labels)在实际测试集中极为罕见，将其纳入提示词中依然大有裨益。为所有潜在类别提供示例，能确保模型充分理解各标签的具体表现形式，这对于参数量更大、能力更强的高阶模型尤为关键。相较于极端偏斜分布可能导致的边缘情况(Edge Cases)遗漏，或因遇到陌生输出而使模型产生困惑，广泛的类别覆盖率通常能带来更稳健的性能表现。
![关键帧](keyframes/part003_frame_00215559.jpg)

## 思维链提示词：分解与计算时间
思维链提示词(Chain-of-Thought Prompting, CoT)通过要求模型在输出最终答案前显式阐明其推理过程，彻底改变了模型处理复杂任务的方式。模型不再直接跳跃至结论，而是逐步生成中间解释，从而在数学计算与逻辑推理任务上显著提升了准确率。该技术的有效性主要归因于两点。首先，它允许模型将复杂问题分解为更简单、有序的子问题(Sub-problems)，从而降低求解难度。其次，它赋予了模型“自适应计算时间”(Adaptive Computation Time)的能力。由于Transformer架构在处理每个词元时的网络层数是固定的，复杂任务往往受限于固有的计算深度。通过生成中间推理词元(Intermediate Reasoning Tokens)，模型实际上为自己争取了更多的计算步骤以解决复杂查询，而无需依赖规模更大、计算资源消耗更高的模型架构。
![关键帧](keyframes/part003_frame_00252959.jpg)

## 零样本思维链与实时演示
触发思维链推理甚至无需提供明确的示例。一篇极具影响力的开创性论文证明，仅需在零样本提示词(Zero-shot Prompt)后附加“让我们一步步思考(Let's think step by step)”这一短语，即可成功引导模型进行逐步推理。其生效机制在于，该短语在模型的预训练语料库(Pre-training Corpus)中，与详尽的解释说明具有高度的共现相关性。实际测试表明，将该提示词应用于 ChatGPT 处理复杂计算任务时，能可靠地触发模型生成结构化推理过程（甚至包含可执行代码），从而精准解决问题。这充分证明，微小的语言线索(Linguistic Cues)即可解锁大语言模型中复杂的认知行为(Cognitive Behaviors)。
![关键帧](keyframes/part003_frame_00443760.jpg)
![关键帧](keyframes/part003_frame_00522040.jpg)
![关键帧](keyframes/part003_frame_00533640.jpg)

---

## 指令微调与基座模型的重要性
在现场演示(Live Demo)中，演讲者向 ChatGPT 提出了一个复杂的计算问题，且未使用“让我们一步步思考”等显式推理提示词(Explicit Reasoning Prompt)。然而，模型依然自主生成了代码以解决问题。该现象源于现代 GPT 模型经历了大规模的指令微调(Instruction Fine-tuning)；它们在监督微调数据(Supervised Fine-tuning, SFT)上进行了针对性训练，因此即便缺乏特定的用户提示，也能展现出逐步推理(Step-by-step Reasoning)与代码生成(Code Generation)的能力。
![关键帧](keyframes/part004_frame_00000000.jpg)
对于从事提示词工程(Prompt Engineering)实验的学生或研究人员而言，充分考量此类固有先验偏差(Inherent Prior Bias)至关重要。若旨在受控环境中评估新型提示词技术，应优先选用原始基座模型(Base Model)（例如未经过对话微调(Dialogue Fine-tuning)的 Llama 2），而非指令微调版本。因为后者已内嵌了特定的行为偏好(Behavioral Bias)，极易导致实验结果产生偏差。
![关键帧](keyframes/part004_frame_00073320.jpg)
![关键帧](keyframes/part004_frame_00080760.jpg)

## 代码与结构化格式的有效性
强制模型输出程序代码或结构化数据格式(Structured Data Format)，能显著提升任务性能，即便该任务本身与编程无关。在一项针对程序性知识依赖关系(Procedural Knowledge Dependencies)预测（例如生成派的制作步骤）的研究中，研究人员对比了自然文本(Natural Text)、DOT 图格式(DOT Graph Format)与 Python 代码的输出效果。结果显示，Python 代码始终取得了最佳性能。
![关键帧](keyframes/part004_frame_00120360.jpg)
该成功主要归因于两大因素。首先，模型在预训练(Pre-training)阶段学习了海量 Python 代码语料，使其极擅长生成语法正确(Syntax-correct)的代码。其次，代码语言本身具备高度结构化特性。当模型切换至“代码生成模式”时，其更倾向于严格引用已定义的变量并维持逻辑依赖关系。相较于自由形态的自然语言，该模式显著降低了模型产生幻觉(Hallucination)或输出随意内容的倾向。

## JSON 的可靠性与程序辅助语言模型
当任务要求精确的结构化输出(Structured Output)时，强制模型输出 JSON 格式(JavaScript Object Notation)是一种高度可靠的策略。尽管模型时常忽略自然语言形式的格式约束，但得益于 JSON 在训练语料中占据极高的比例，模型几乎总能生成语法有效的 JSON 对象(Valid JSON)。这使得 JSON 成为通过编程方式解析模型回复的理想媒介。
鉴于代码生成的显著优势，程序辅助语言模型(Program-Aided Language Models, PAL)方法更进一步：它引导大语言模型(Large Language Models, LLMs)直接生成可执行代码(Executable Code)以解决问题，而非仅依赖纯文本形式的思维链推理(Chain-of-Thought Reasoning)。该方法在处理数值计算、数学推导或逻辑推理任务时表现出极高的精准度。ChatGPT 的代码解释器(Code Interpreter)或高级数据分析(Advanced Data Analysis)等现代实现已将此流程自动化：模型自主决策何时生成代码，在隔离的沙盒环境(Sandbox Environment)中执行代码并返回结果，甚至能动态生成数据可视化图表(Data Visualization Charts)（如直方图）。
![关键帧](keyframes/part004_frame_00298919.jpg)
![关键帧](keyframes/part004_frame_00321240.jpg)

## 提示词工程：手动微调与格式敏感性
提示词工程(Prompt Engineering)可通过人工设计或自动搜索算法(Automated Search Methods)（如探索离散文本空间(Discrete Text Space)或连续嵌入空间(Continuous Embedding Space)）来实现。然而，人工调优(Manual Tuning)依然发挥着关键作用，尤其在把控格式细节方面。研究证实，模型对提示词中微小的结构变动极其敏感。
细微的调整，例如在字段标识符(Field Identifiers)后增删冒号、更改大小写(Capitalization)，甚至在段落与问题间插入单个空格，均可能导致模型准确率(Accuracy)发生剧烈波动。在一项实验中，格式错乱的提示词(Malformatted Prompt)致使准确率趋近于零，而仅添加一个必要的空格后，模型性能便跃升至 75% 以上。
![关键帧](keyframes/part004_frame_00436440.jpg)
此类敏感性在不同模型架构间具有普遍性，这为提示词工程提供了核心启示：对格式细节的严谨把控绝非可有可无。微小的语法调整(Syntax Adjustments)足以根本性地改变模型解析上下文(Context Parsing)与生成回复的机制。因此，严谨的提示词设计(Rigorous Prompt Design)是获取最优模型性能的先决条件。
![关键帧](keyframes/part004_frame_00514360.jpg)

---

## 格式敏感性与标准提示词设计
实证分析表明，尽管大多数合理的提示词变体(Prompt Variants)平均准确率较低，但少数特定格式的变体却能展现出卓越的性能。这些高性能格式通常与模型在预训练(Pre-training)或指令微调(Instruction Fine-tuning)阶段所接触的结构模式高度契合。因此，采用特定模型架构官方推荐的标准提示词模板(Standard Prompt Templates)至关重要。尽管 GPT-4 等能力强大且经过大规模训练的模型对格式变化展现出较强的鲁棒性(Robustness)，但参数量较小的模型或基座模型(Base Models)依然对语法细节高度敏感。在使用此类模型时，严格遵循既定格式规范是防止性能显著下降的首要步骤。
![关键帧](keyframes/part005_frame_00000000.jpg)
![关键帧](keyframes/part005_frame_00034880.jpg)

## 清晰精准指令的艺术
撰写高效提示词与清晰的人类沟通(Human Communication)同理：指令必须明确、简洁且无歧义(Unambiguous)。有效引导先进的大语言模型(Large Language Models, LLMs)需要有意识地训练任务表述的清晰度。一项可靠的策略是，从使用明确动作动词（如“撰写”、“分类”或“总结”）的简明指令入手。具体化(Specificity)能显著提升输出质量。诸如“保持简短”这类模糊请求，其效果远不及“用两到三句话向高中生解释提示词工程”这样的精确约束(Precise Constraints)。与人类协作者不同，当前的大语言模型在指令模糊时不会主动请求澄清，因此确保表述精准的责任完全落在提示词设计者(Prompt Designer)身上。
![关键帧](keyframes/part005_frame_00124680.jpg)
![关键帧](keyframes/part005_frame_00145039.jpg)
![关键帧](keyframes/part005_frame_00162239.jpg)

## 自动提示词改写
除手动调优外，自动搜索技术(Automated Search Techniques)可系统化地优化提示词性能。一种易于上手的方法是提示词改写(Prompt Rewriting)，即利用改写模型或大语言模型对初始提示词进行处理，批量生成自然语言变体(Natural Language Variants)。通过在验证集(Validation Set)上评估数十个候选版本并择优选用，用户可稳定提升任务准确率。该流程可进行迭代扩展(Iterative Expansion)：生成一批改写版本，筛选出高性能候选，并将其作为下一轮迭代的种子(Seed Prompts)。这种进化式迭代方法通常比单次尝试更能产出高效的提示词，同时保持了文本的人类可读性(Human Readability)。
![关键帧](keyframes/part005_frame_00231439.jpg)
![关键帧](keyframes/part005_frame_00242359.jpg)
![关键帧](keyframes/part005_frame_00296120.jpg)

## 基于梯度的离散提示词搜索与对抗性攻击
一种更为复杂的优化技术是基于梯度的离散提示词搜索(Gradient-based Discrete Prompt Search)。该方法将输入词元(Input Tokens)视为可训练的嵌入向量(Embedding Vectors)，并通过反向传播(Backpropagation)进行优化，以最大化任务准确率。优化完成后，这些连续嵌入向量会被映射回模型词汇表(Vocabulary)中最邻近的离散词元。有趣的是，该过程通常会生成极其不自然甚至看似无意义的提示词，却能实现卓越的性能表现。该技术已成为对抗性人工智能(Adversarial AI)研究的基础，尤其在生成“越狱”提示词(Jailbreak Prompts)方面应用广泛。研究表明，优化后的对抗性提示词(Adversarial Prompts)能够成功绕过异构模型架构（如同时影响开源 Llama 与闭源 GPT 模型）的安全对齐(Safety Alignment)机制，揭示了超越单一训练流程的系统性漏洞(Systemic Vulnerabilities)。
![关键帧](keyframes/part005_frame_00379639.jpg)
![关键帧](keyframes/part005_frame_00397759.jpg)
![关键帧](keyframes/part005_frame_00442680.jpg)

## 提示词微调与前缀微调
基于梯度优化的进一步延伸催生了提示词微调(Prompt Tuning)与前缀微调(Prefix Tuning)，二者有效弥合了提示词工程与全参数微调(Full-parameter Fine-tuning)之间的鸿沟。在提示词微调中，优化后的连续嵌入向量将被直接应用，无需映射回词汇表中的离散词元。这些学习到的嵌入向量仅被前置拼接(Prepended)至模型的输入层。通过仅训练极少量参数（如 10-20 个词元的嵌入序列），而非整个数十亿参数(Billion-parameter)的模型，即可实现高效的任务适配(Task Adaptation)。前缀微调在此基础上进一步扩展，它将可训练的前缀向量注入至 Transformer 架构的*每一层*隐藏状态(Hidden States)中，而非仅限于输入层。这使得前缀微调成为表达能力更强、功能更完善的变体，为跨多数据集与多任务的传统模型微调，提供了一种高度可扩展、参数高效(Parameter-Efficient)的替代方案。
![关键帧](keyframes/part005_frame_00453920.jpg)
![关键帧](keyframes/part005_frame_00543960.jpg)
![关键帧](keyframes/part005_frame_00587800.jpg)
![关键帧](keyframes/part005_frame_00595840.jpg)

---

## 提示词到微调的演进谱系
提示词微调(Prompt Tuning)与前缀微调(Prefix Tuning)并非孤立的技术，而是参数高效微调(Parameter-Efficient Fine-Tuning, PEFT)这一更广泛范畴下的具体实现。该范畴还涵盖了低秩自适应(Low-Rank Adaptation, LoRA)和适配器(Adapters)等方法。这一视角将提示词本身重新定义为一种规范化且结构化的微调机制。完整的分类体系清晰勾勒出这些技术的演进路径：从人工编写的自然语言提示词(Natural Language Prompts)出发，演进至用于广泛探索的自动提示词改写(Automatic Prompt Rewriting)，再发展为可能生成非自然语言词元(Token)序列的离散提示词搜索(Discrete Prompt Search)。该体系进一步向上延伸，历经连续提示词微调(Continuous Prompt Tuning)与多层前缀微调(Multi-layer Prefix Tuning)，最终演进至完整的模型全量微调(Full Fine-tuning)。
![关键帧](keyframes/part006_frame_00000000.jpg)
![关键帧](keyframes/part006_frame_00007480.jpg)
![关键帧](keyframes/part006_frame_00013879.jpg)
![关键帧](keyframes/part006_frame_00029880.jpg)
![关键帧](keyframes/part006_frame_00035280.jpg)

## 提示词作为贝叶斯先验
批评者常将手动提示词工程(Manual Prompt Engineering)视为一种“经验性试错”，并将其与数学上更为严谨的微调方法(Fine-tuning Methods)相对立。然而，提示词实际上发挥着深刻的理论作用，其内在原理与贝叶斯统计学(Bayesian Statistics)高度契合。精心设计的提示词能够为模型构建强有力的先验概率分布(Prior Probability Distribution)，在模型开始生成文本前，即明确界定了任务上下文(Task Context)与预期行为(Expected Behavior)。这一先验机制使得模型无需更新任何权重(Weights)即可直接部署用于推理(Inference)，为充分调用模型预训练知识(Pre-trained Knowledge)提供了一条高效基线(Efficient Baseline)。
![关键帧](keyframes/part006_frame_00112240.jpg)
![关键帧](keyframes/part006_frame_00198880.jpg)

## 提示词与基于梯度训练的融合
将提示词的明确指导(Explicit Guidance)与基于梯度的微调(Gradient-based Fine-tuning)的统计建模能力相结合，能够构建出最为稳健的工作流(Robust Workflow)。从业者无需将提示词工程与模型训练(Model Training)视为互斥选项，而是可利用人工设计的提示词初始化模型对任务的理解，随后在特定数据集(Specific Dataset)上进行微调。模式利用训练(Pattern-Exploiting Training, PET)等方法将这种协同效应(Synergy)进行了规范化。它使开发者能够借助自然语言先验(Natural Language Priors)快速搭建系统原型，并无缝过渡至数据驱动优化(Data-driven Optimization)阶段，从而最大化模型的准确率与可靠性。
![关键帧](keyframes/part006_frame_00212999.jpg)