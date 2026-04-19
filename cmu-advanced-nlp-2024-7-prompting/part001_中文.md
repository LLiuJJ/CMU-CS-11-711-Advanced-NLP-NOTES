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