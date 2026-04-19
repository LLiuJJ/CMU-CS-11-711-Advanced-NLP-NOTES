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