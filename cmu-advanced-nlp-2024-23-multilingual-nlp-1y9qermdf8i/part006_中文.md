## 词对齐简介
在处理多语言数据时，仅依赖简单的翻译往往不够，尤其是在处理复杂的词元级(Token-level)标注任务时。为此，必须引入词对齐(Word Alignment)技术，它是跨语言自然语言处理(Cross-lingual NLP)中的一项基础技术。词对齐旨在建立不同语言句子间单词或短语的映射关系，通常通过输出匹配的索引来构建词与词之间的对应链接。
![关键帧](keyframes/part006_frame_00000000.jpg)

## 无监督对齐方法
词对齐可通过无监督方法(Unsupervised Methods)实现，该类方法主要依赖平行语料库(Parallel Corpora)中的共现统计信息。GIZA++ 是该领域的经典工具，数十年来被广泛用于基于统计共现计算对齐概率(Alignment Probability)。近年来，一种更为现代且高效的方法利用了 SimAlign 等多语言表示模型(Multilingual Representation Models)。SimAlign 通过从多语言 BERT(mBERT)中提取跨语言上下文嵌入向量(Cross-lingual Contextual Embeddings)，识别相似度最高的词元表示并将其作为对齐链接。该方法的准确率显著优于传统的纯统计方法。
![关键帧](keyframes/part006_frame_00029319.jpg)
![关键帧](keyframes/part006_frame_00036640.jpg)

## 有监督对齐技术
为追求更高的对齐精度，有监督对齐(Supervised Alignment)技术会利用少量人工标注数据(Manually Annotated Data)来训练对齐模型。此类模型通常采用对比目标函数(Contrastive Objective Function)进行优化，旨在显式提升正确跨语言链接的权重，同时抑制错误链接的权重。与无监督方案相比，有监督方法通常能生成更为精准的对齐结果。尽管可通过提示工程(Prompt Engineering)驱动 GPT-4 等大型语言模型(LLMs)执行词对齐任务，但其推理成本高昂，且相较于经过专门对比训练的对齐专用工具，其性能提升极为有限。
![关键帧](keyframes/part006_frame_00077800.jpg)

## 枢轴中转与翻译-测试范式
当处理类型学距离(Typological Distance)较远或书写系统截然不同的语言对（如英语与日语）时，直接对齐极具挑战性。一种常见的变通方案是枢轴中转(Pivoting)，即借助英语等高资源语言作为中间枢纽(Hub Language)进行翻译或对齐中转。历史上，Google 翻译等系统曾采用此类黑盒枢轴策略(Black-box Pivoting)，在跨语言转换过程中偶尔会残留英语的语法或词汇痕迹。在特定下游任务中，“翻译-测试”(Translate-test)范式应用了该理念：首先将输入文本翻译为英语，交由性能强大的英语模型（如专用于问答或逻辑推理的模型）进行处理，随后将输出结果翻译回目标语言。尽管该范式在复杂推理任务中表现可行，但其整体性能通常仍逊色于原生训练的高质量多语言模型。
![关键帧](keyframes/part006_frame_00103320.jpg)
![关键帧](keyframes/part006_frame_00127520.jpg)

## 优化迁移语言选择
为跨语言迁移(Cross-lingual Transfer)选择合适的源语言至关重要。针对最优迁移策略的研究揭示了一个出人意料且极具预测力的指标：地理邻近性(Geographic Proximity)。尽管地理位置接近的语言在语言学上未必同源（例如西班牙语与巴斯克语），但统计学表明，地理邻近性与共享词汇及句法结构之间存在显著的正相关关系。因此，地理距离可作为一项高度可靠的启发式规则(Heuristic Rule)，用于筛选能够最大化下游任务性能的枢轴语言或源迁移语言。
![关键帧](keyframes/part006_frame_00139079.jpg)
![关键帧](keyframes/part006_frame_00144799.jpg)

## 基于国际音标（IPA）的跨书写系统规范化
针对使用完全不同书写系统的语言，另一种可行策略是将文本转换为国际音标(International Phonetic Alphabet, IPA)。通过将词汇规范化(Normalization)为语音表示，模型能够利用亲缘语言间的发音相似性，即便其正字法(Orthography)毫无重叠。然而，应用该技术需谨慎：在英语与法语等特定语言对中，其书面形式反而比实际发音更为接近。盲目地将文本转写为 IPA 可能会破坏预训练模型的词对齐能力并导致性能下降。因此，该策略属于高度依赖语言背景(Context-dependent)的专用技术，而非普适性解决方案。
![关键帧](keyframes/part006_frame_00193839.jpg)
![关键帧](keyframes/part006_frame_00204359.jpg)
![关键帧](keyframes/part006_frame_00209720.jpg)
![关键帧](keyframes/part006_frame_00219600.jpg)

## 参数共享架构
在设计多语言模型时，参数共享策略(Parameter Sharing Strategies)是核心的架构决策。最直接的方案是采用单一统一模型，在所有语言间共享全部参数。早期架构曾探索部分参数共享，例如仅共享编码器(Encoder)、注意力机制(Attention Mechanism)或特定的 Transformer 权重矩阵。更具前瞻性的研究则引入了参数生成器(Parameter Generators/Hypernetworks)，即通过一个独立的控制网络，根据特定语言的元数据动态生成目标模型的权重。尽管该理念在理论上极具潜力，但其巨大的参数开销使其在大规模实际部署中往往缺乏可行性。
![关键帧](keyframes/part006_frame_00327200.jpg)
![关键帧](keyframes/part006_frame_00338680.jpg)
![关键帧](keyframes/part006_frame_00345039.jpg)

## 语言专家与基于适配器的微调
当前多语言适配的最先进方法(State-of-the-Art)高度依赖参数高效(Parameter-Efficient)架构，例如语言专家(Language Experts)与适配器(Adapters)。该范式摒弃了为每种语言独立训练完整模型的做法，转而在共享的骨干模型(Backbone Model)特定层中插入轻量级模块化组件。这些适配器通常采用瓶颈设计(Bottleneck Design)，先对隐藏层表示(Hidden Representations)进行下投影(Down-projection)，随后再上投影(Up-projection)恢复（其机制类似于 LoRA(Low-Rank Adaptation) 或标准适配器模块）。该方法仅需微调极少量的参数，即可实现高效的特定语言适配，同时完整保留基础模型的跨语言通用知识。
![关键帧](keyframes/part006_frame_00499159.jpg)
![关键帧](keyframes/part006_frame_00555520.jpg)
![关键帧](keyframes/part006_frame_00563640.jpg)