## 理解 pass@k 计算与标准基准
![关键帧](keyframes/part002_frame_00000000.jpg)
仅使用单次采样输出(Single Sample Output)评估代码生成(Code Generation)会引入较高的方差(Variance)，使得结果的可靠性难以衡量。为解决这一问题，系统通常会生成多个输出（例如 10 个或 200 个样本(Samples)），并利用组合数学(Combinatorics)计算至少有一个解决方案通过测试的期望案例数。 
![关键帧](keyframes/part002_frame_00040880.jpg)
`pass@k` 指标对此进行了形式化定义(Formalized Definition)：将 `k` 视为生成样本的总数，`c` 视为其中正确解决方案的数量，并据此计算成功概率。 
![关键帧](keyframes/part002_frame_00048520.jpg)
该方法已成为该领域高度标准化的基准测试(Benchmark)，被持续用于评估新发布的大语言模型(Large Language Models, LLMs)并横向比较其代码生成能力。
![关键帧](keyframes/part002_frame_00054800.jpg)

## 大语言模型为何擅长代码生成
大语言模型在代码生成方面表现出色，并非源于其固有的架构优势(Architectural Advantages)，而是因为它们在预训练阶段(Pre-training Phase)被有针对性地输入了海量代码语料(Code Corpus)。这一设计选择主要受两个因素驱动：首先，代码生成是一项核心的实际应用场景，对于现代大语言模型而言具有显著的商业与实践价值；其次，大量研究表明，使用代码进行训练能显著提升模型的通用推理能力(General Reasoning Capabilities)与逻辑水平。像“The Pile”这样的数据集(Dataset)充分反映了这一优先级，其中代码数据几乎占据了训练语料的一半。归根结底，模型在编程任务上的熟练度，直接归功于预训练阶段对代码数据的大规模、针对性训练。

## 扩展评估范围：Conala 与 Odets 数据集
为了克服 HumanEval 等基准测试范围狭窄的局限性，研究人员引入了 Conala 及其基于执行的变体(Execution-based Variant) Odets 等数据集。这些数据集通过抓取并人工整理 Stack Overflow 上的问答对(Q&A Pairs)构建而成，从而能够覆盖更广泛的编程领域与第三方库(Third-party Libraries)。 
![关键帧](keyframes/part002_frame_00159440.jpg)
由于数据源自活跃的开发者社区帖子，其库分布(Library Distribution)自然映射了现实世界的使用习惯，大量涵盖了 pandas、NumPy 和 scikit-learn 等主流工具库。 
![关键帧](keyframes/part002_frame_00223519.jpg)
相比之下，HumanEval 几乎完全依赖 Python 标准库(Python Standard Library)，且不涉及任何外部依赖。因此，Odets 能够更真实、更具代表性地评估人工智能(AI)模型在处理实际开发者查询(Developer Queries)时的表现。

## 基于执行的评估的局限性
![关键帧](keyframes/part002_frame_00275559.jpg)
尽管基于执行的评估(Execution-based Evaluation)功能强大，但也带来了显著的工程挑战。首先，为真实场景中的程序生成可靠的单元测试(Unit Tests)通常十分困难，尤其是针对可视化库或服务器端框架(Server-side Frameworks)。例如，自动验证 matplotlib 生成的条形图是否正确渲染(Rendering)，或确认运行中的 Django 服务器状态，都需要设计复杂且高度依赖特定环境的验证逻辑。其次，基于执行的指标完全忽略了代码风格(Code Style)与可维护性(Maintainability)。模型可能会生成极其冗长或结构混乱的“面条代码(Spaghetti Code)”，虽然在技术上能通过所有单元测试，但远未达到工业级专业标准。这些缺陷凸显了引入互补性非执行评估方法(Non-execution Evaluation Methods)的必要性。

## 语法感知指标：BLEU 与 CodeBLEU
传统的文本重叠指标（如 BLEU）主要计算生成代码与人工编写的参考代码之间的 n-gram 相似度(n-gram Similarity)，但无法有效捕捉底层的编程逻辑。为解决这一问题，CodeBLEU 应运而生，旨在融入对代码结构与语义的深度理解。 
![关键帧](keyframes/part002_frame_00378520.jpg)
CodeBLEU 不仅评估表面字符串的重合度，还进一步考察抽象语法树(Abstract Syntax Tree, AST)的对齐情况以及语义数据流图(Semantic Data Flow Graph)的相似度。得益于内置的 AST 解析库，在 Python 等语言中提取这些语法树结构非常便捷。 
![关键帧](keyframes/part002_frame_00458080.jpg)
然而，一个关键的局限性依然存在：两个功能完全相同但语法结构或写法不同的实现，仍可能获得较低的评分。因此，业界通常建议将执行测试(Execution Testing)与结构指标(Structural Metrics)结合使用，以实现更全面、客观的模型评估。

## 基于嵌入的评估：Code-BERTScore
![关键帧](keyframes/part002_frame_00500439.jpg)
在编写单元测试成本过高或难以落地的场景中，基于嵌入的指标(Embedding-based Metrics)（如 Code-BERTScore）提供了一种稳健的替代方案。该指标由卡内基梅隆大学(Carnegie Mellon University, CMU)研发，通过计算生成代码与参考解决方案的词元嵌入(Token Embeddings)之间的余弦相似度(Cosine Similarity)来进行量化评估。借助 `code-bert`（在海量代码语料上继续进行预训练(Continued Pre-training)的 BERT 模型），该指标能够捕捉远比表层字符串匹配更深层的语义关系(Semantic Relationships)。研究表明，Code-BERTScore 与代码执行准确率(Execution Accuracy)以及人类对代码正确性的主观判断具有更强的相关性。此外，它对变量重命名等表层文本修改具有更强的鲁棒性(Robustness)，这使其在代码无法直接执行的场景下，成为评估代码质量的高效代理指标(Proxy Metric)。