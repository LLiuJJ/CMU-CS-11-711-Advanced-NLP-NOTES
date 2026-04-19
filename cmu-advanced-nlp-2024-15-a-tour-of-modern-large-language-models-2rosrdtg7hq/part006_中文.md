## 专用数学模型：Lemma 与 DeepSeek
讨论首先聚焦于专注于数学推理(Mathematical Reasoning)的高度专用模型。由 EleutherAI 开发的 Lemma 被定位为一个完全开源且透明的数学模型，卡内基梅隆大学(CMU)的 Sean Welleck 也为其做出了重要贡献。另一款备受瞩目的模型是 DeepSeek 的数学专用模型，其性能已可与 GPT-4 相媲美。DeepSeek 通过训练专用分类器从海量互联网数据中识别并提取数学相关内容，并结合 ProofWiki 等高质量黄金标准数据集(Gold-standard Datasets)进行训练。尽管基于正确性验证的基于人类反馈的强化学习(Reinforcement Learning from Human Feedback, RLHF)等先进技术可能发挥了作用，但其核心优势仍在于精心构建的领域特定数据(Domain-specific Data)与针对性的训练策略。
![关键帧](keyframes/part006_frame_00000000.jpg)

## Galactica：多模态科学与公众反响
转向科学应用领域，Meta 的 Galactica 被介绍为一款专为科学研究设计的雄心勃勃的多模态模型(Multimodal Model)。尽管其发布时遭遇了巨大的公众反弹与公关危机——这在很大程度上是因为早期大语言模型(LLMs)的幻觉问题(Hallucination)在被建议用于辅助撰写学术论文时被暴露出来——但演讲者仍为其技术价值进行了辩护。Galactica 旨在处理多种科学模态，涵盖原始文本、LaTeX 公式、代码，以及分子式、蛋白质结构(如胶原蛋白)和 DNA 序列等复杂结构表征(Complex Structural Representations)。即便其首次发布的时机欠佳，它在生物与化学数据建模方面的创新方法，仍为未来领域专用 AI(Domain-specific AI)的发展提供了宝贵的蓝图。
![关键帧](keyframes/part006_frame_00106680.jpg)
![关键帧](keyframes/part006_frame_00200600.jpg)

## 闭源模型：透明度限制与 GPT-4 的主导地位
讲座随后转向闭源模型(Closed-source Models)，在此类模型中，训练方法与数据构成在很大程度上仍不透明。相关公司通常仅公开评估基准(Evaluation Benchmarks)与安全/红队测试报告(Red-teaming Reports)，而非详细的架构或训练细节。在此背景下，GPT-4 依然是通用推理(General Reasoning)与实际应用(Utility)方面的事实行业标准。作为 ChatGPT Plus 版本的底层驱动模型，它针对对话交互(Conversational Interactions)进行了深度优化，并已演进为原生支持图像输入，同时能够通过强大的函数调用(Function-calling)接口执行外部操作。
![关键帧](keyframes/part006_frame_00207959.jpg)
![关键帧](keyframes/part006_frame_00232480.jpg)

## 多模态能力与工具集成
实际演示展示了 GPT-4 在文本生成之外的多样化能力。其多模态视觉理解能力(Multimodal Vision Capabilities)允许用户直接输入视觉数据，例如直接粘贴研究论文中的表格，模型即可瞬间将其转换为结构化的 JSON 格式(Structured JSON Format)。此外，GPT-4 还能与 DALL-E 3 等生成式工具无缝集成以创建自定义图像，并可按需编写、执行和可视化代码——例如自动生成 Python 脚本，将提供的数值数据绘制为直方图。这些功能凸显了现代大语言模型如何作为核心编排中枢(Central Orchestrator)，将逻辑推理、多模态感知与外部工具执行(External Tool Execution)无缝融合。
![关键帧](keyframes/part006_frame_00329479.jpg)
![关键帧](keyframes/part006_frame_00353679.jpg)
![关键帧](keyframes/part006_frame_00361039.jpg)
![关键帧](keyframes/part006_frame_00369800.jpg)
![关键帧](keyframes/part006_frame_00394799.jpg)
![关键帧](keyframes/part006_frame_00424039.jpg)

## 深度学习的演进：视觉处理与工具调用
在提及一篇引发广泛争议、声称“深度学习正遭遇瓶颈”的文章时，演讲者巧妙地利用 GPT-4 生成了一张“深度学习突破砖墙”的图像。该演示旨在阐明现代 AI 架构中一个关键的技术分野：多模态视觉处理与工具调用(Tool Calling)。在分析用户上传的图像时，模型会将视觉数据直接编码(Encode)至其词元流(Token Stream)中进行内部处理；相反，在生成新图像时，大语言模型则充当智能体(Agent)，构思精确的文本描述，并通过 API 调用外部图像生成模型（如 DALL-E 3）。这种双重能力——内部多模态理解与外部工具调度(Tool Orchestration)的深度结合——正定义着当前大语言模型发展的前沿范式(Cutting-edge Paradigm)。
![关键帧](keyframes/part006_frame_00496400.jpg)
![关键帧](keyframes/part006_frame_00586800.jpg)
![关键帧](keyframes/part006_frame_00601280.jpg)