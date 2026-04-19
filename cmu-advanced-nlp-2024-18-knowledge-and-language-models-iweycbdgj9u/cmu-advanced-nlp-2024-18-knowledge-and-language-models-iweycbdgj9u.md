## 知识库简介
本讲座引入了从知识库中学习（Learning from Knowledge Bases）的主题，标志着讨论重心从此前对语言模型（Language Models）和神经网络（Neural Networks）的探讨，有意识地发生了转变。本部分将探索不同的信息源与相对独立的算法，为理解结构化数据如何融入机器学习流水线（Machine Learning Pipeline）提供了全新视角。
![关键帧](keyframes/part000_frame_00000000.jpg)

## 知识库的定义与核心研究问题
知识库（Knowledge Base）本质上是结构化的信息数据库。其中最常见的形式是由实体（Entity，即图中的节点）和关系（Relation，即连接节点的边）构成的关系型知识库（Relational Knowledge Base）。本部分的讨论主要围绕三个核心研究问题展开：如何利用神经网络（Neural Network）方法学习并扩充此类知识库？如何从知识库中提取信息并进行学习，以改进神经网络模型？以及如何有效利用结构化知识来解答复杂问题？
![关键帧](keyframes/part000_frame_00030400.jpg)

## 经典知识库：WordNet
讲座回顾了知识库的历史演变，首先从经典案例 WordNet 切入。WordNet 是一个大型词汇数据库，其中每个节点代表一个“同义词集”（Synset，即一组同义词），涵盖名词、动词和形容词。它建立了诸如“是一种”（Is-a，例如：掀背车是一种汽车）和“部分属于”（Part-of，例如：车轮是汽车的一部分）等层级关系，并严格区分了类型（Type）与实例（Instance）。动词间的关系按具体程度进行组织，而形容词间的关系则侧重于反义关系。历史上，WordNet 在自然语言处理（Natural Language Processing, NLP）领域发挥了重要作用，例如可通过遍历其层级结构，识别文本中某一概念的所有语义变体。
![关键帧](keyframes/part000_frame_00088800.jpg)
![关键帧](keyframes/part000_frame_00164200.jpg)

## 人工构建的衰落与现代替代方案
尽管 WordNet 具有奠基性作用，但其在现代 NLP 中的应用已逐渐减少。如今，稠密嵌入空间（Dense Embedding Space）和大型语言模型（Large Language Model, LLM）等技术能够以更高效率、更大规模的方式实现类似的语义匹配。讲座还强调了历史上人工构建常识库（Commonsense Knowledge Base）所面临的挑战：此类项目曾试图在数十年间，通过人工手动编码海量的人类知识。事实证明，此类项目的规模过于庞大，难以在实际中落地；由于人工成本无法实现规模化扩展，最终难以构建出全面且实用的系统。
![关键帧](keyframes/part000_frame_00349080.jpg)
![关键帧](keyframes/part000_frame_00368880.jpg)
![关键帧](keyframes/part000_frame_00385039.jpg)

## DBpedia：利用维基百科的人工构建数据
DBpedia 的出现标志着一个重要的范式转变（Paradigm Shift）。它不再依赖专门团队为机器手动编码规则，而是直接从维基百科（Wikipedia）中提取结构化数据。该方法充分利用了志愿者在创建维基百科内容时所投入的大量人力。由于贡献者本是为人类读者撰写内容，他们会自然而然地将信息整理为结构化格式，例如实体类型、格言（Motto）和成立日期等。这种由内在动机驱动的模式成功构建了一个高度可扩展且实用的数据库，涵盖了全球众多知名实体，且无需专门投入大量人工进行维护。
![关键帧](keyframes/part000_frame_00484039.jpg)

## Wikidata：大规模、互联与多语言图谱
在此基础上，Wikidata 已成为目前应用最广泛的现代知识库。尽管名称中带有“Wiki”，但它并不严格局限于维基百科；它还整合并提取了大量外部数据源的信息。Wikidata 的特点在于其构建了一个超大规模、高度互联且支持多语言的结构化数据库。它支持由社区驱动的持续更新，并为实体维护着丰富且相互关联的属性集。这一点在历史人物与当代人物全面且机器可读（Machine-Readable）的条目中得到了充分体现。
![关键帧](keyframes/part000_frame_00509719.jpg)

## 互动演示：在 Wikidata 中探索实体
为展示 Wikidata 的实际功能，演讲者根据观众建议进行了一次实时搜索，查询实体为“mamba”。查询结果指向“MAMBA”，即法国国家科学研究中心（CNRS）内一个专注于数学生物学（Mathematical Biology）的研究团队。该演示直观展示了现代知识库如何将现实世界中的概念表示为相互关联的图节点（Graph Node）。用户可以无缝浏览研究方向、机构隶属关系和领导职务等属性，充分展现了当代知识图谱（Knowledge Graph）的动态实用性、结构化特性以及实时可访问性。
![关键帧](keyframes/part000_frame_00527920.jpg)
![关键帧](keyframes/part000_frame_00542080.jpg)
![关键帧](keyframes/part000_frame_00572040.jpg)

---

## 探索 Wikidata 图结构
讨论进一步深入探讨了现代知识图谱(Knowledge Graph)的组织方式。实体(Entity)充当节点(Node)，连接它们的边(Edge)则明确标注了关系类型(Relation Type，例如“是……的实例”)，这些边将具体实体链接至更广泛的类别节点。像 Wikidata 这类平台的一个显著特征是其强大的多语言支持(Multilingual Support)；每个实体和关系均被映射至数十种语言，从而实现无缝的跨语言查询(Cross-lingual Query)与全球范围内的可访问性。该数据库的覆盖粒度极为精细，甚至延伸至小型学术机构或个人实体。例如，通过聚合数学谱系项目(Mathematics Genealogy Project)等历史数据集，系统能够保存并关联详尽的学术谱系(Academic Genealogy)，包括博士毕业院校与导师网络。这充分展示了结构化知识库如何持续从各类长期存在的数据源中摄取(Ingest)数据并构建互联。
![关键帧](keyframes/part001_frame_00000000.jpg)

## 使用 SPARQL 查询知识库
为使这些结构化数据库发挥实际效用，业界采用了专门的查询语言。SPARQL 是查询知识图谱的标准接口，其功能类似于传统关系型数据库(Relational Database)中的 SQL。它允许用户沿图边进行遍历，根据特定谓词(Predicate)过滤节点，并施加时间或类别约束(Constraint)。尽管编写语法正确的 SPARQL 查询具有一定挑战性（即便是 AI 辅助工具也常遇瓶颈），但掌握该语言将赋予用户精确且确定性(Deterministic)的数据检索能力，这是系统化知识提取(Knowledge Extraction)的基石。
![关键帧](keyframes/part001_frame_00110080.jpg)
![关键帧](keyframes/part001_frame_00121160.jpg)
![关键帧](keyframes/part001_frame_00154160.jpg)

## 结构化查询相较于语言模型的优势
引入知识库的一个核心动机在于其在处理复杂聚合(Complex Aggregation)与逻辑过滤(Logical Filtering)任务时的高可靠性。生成式语言模型(Generative Language Model)在处理需要精确枚举(Precise Enumeration)、空间推理(Spatial Reasoning)或多跳过滤(Multi-hop Filtering)的查询时往往表现欠佳，例如“列出所有出生在密西西比河以东的美国总统”。当大语言模型(Large Language Model, LLM)被要求生成大量精确数据时，常出现计数错误、地理边界误判或产生幻觉(Hallucination)。相比之下，正确构建的 SPARQL 查询能够以确定性方式遍历图结构，应用精确过滤器，并返回高保真(High-fidelity)且可验证的结果，从而避免了概率性文本生成(Probabilistic Text Generation)中固有的推理不一致(Reasoning Inconsistency)问题。
![关键帧](keyframes/part001_frame_00207359.jpg)

## 解决知识库的不完整性问题
尽管知识库规模庞大，但其本质上仍存在不完整性(Incompleteness)。以历史数据集 Freebase 为例，它表现出严重的稀疏性(Sparsity)，超过 70% 的人物实体缺失出生日期等基本属性(Property)。虽然知名人物通常拥有详尽记录，但长尾实体(Long-tail Entity)或涉及隐私的实体不可避免地面临数据覆盖不足的问题。这一结构性缺陷推动了自动化关系抽取(Relation Extraction)领域的深入研究，相关模型通过扫描非结构化文本(Unstructured Text)来识别、验证并补全缺失的图边。该方法对于扩充通用知识库(General-purpose Knowledge Base)，以及构建传统众包(Crowdsourcing)难以全面覆盖的高度专业化领域特定图谱(Domain-specific Knowledge Graph)至关重要。
![关键帧](keyframes/part001_frame_00214679.jpg)

## 知识图谱的嵌入表示学习
为实现结构化数据与神经网络的融合，研究人员致力于为知识图谱组件学习稠密向量表示(Dense Vector Representation)。借鉴 Word2Vec 的核心洞见（即向量空间运算可捕捉语义关系，如性别或单复数变化），现代嵌入技术旨在将图三元组(Graph Triplet)映射至连续向量空间(Continuous Vector Space)中。一种广泛采用的方法将关系建模为加法几何变换(Additive Geometric Transformation)：头实体(Head Entity)的向量加上关系向量(Relation Vector)，应近似等于尾实体(Tail Entity)的向量。此类模型采用基于间隔的铰链损失(Margin-based Hinge Loss)进行训练，通过最小化有效三元组(Valid Triplet)间的距离来优化嵌入空间，同时将破坏的(Broken)或错误三元组推离至更远处。
![关键帧](keyframes/part001_frame_00262400.jpg)

## 用于关系预测的早期神经网络架构
知识图谱嵌入(Knowledge Graph Embedding)技术的发展，历来是众多顶尖人工智能(AI)研究者的重要试验场。早期方法逐渐超越了简单的向量加法，引入了多层感知机(Multilayer Perceptron, MLP)和基于张量分解(Tensor Decomposition)的模型等神经网络架构。这些方法利用专用矩阵或多维张量，以捕捉实体对与关系类型之间复杂的非线性交互(Non-linear Interaction)。通过学习参数化变换(Parameterized Transformation)而非仅依赖固定的几何平移，这些早期神经模型显著提升了链接预测(Link Prediction)的准确性，并为将符号知识(Symbolic Knowledge)与深度学习流水线(Deep Learning Pipeline)相融合奠定了坚实基础。

---

## 预测关系的神经网络架构
预测实体间关系的早期方法采用神经网络，通过关系两侧的专用矩阵对实体嵌入(Entity Embedding)进行处理。将处理后的嵌入输入非线性层(Non-linear Layer)，并将最终输出向量经 sigmoid 函数(sigmoid function)激活后，模型即可估算特定关系存在的概率。该领域的一项显著进展是神经张量网络(Neural Tensor Network, NTN)，它引入了双线性特征提取器(Bilinear Feature Extractor)来计算变换后嵌入之间的点积(Dot Product)。尽管该架构与注意力机制(Attention Mechanism)存在相似之处，且将双线性特征与标准多层感知机(Multilayer Perceptron, MLP)相结合，但实践证明其参数规模过于庞大。因此，该领域逐渐转向更简单高效的模型，主要依赖实体表示之间的线性投影(Linear Projection)。
![关键帧](keyframes/part002_frame_00000000.jpg)
![关键帧](keyframes/part002_frame_00078480.jpg)

## 用于自动生成数据的远程监督
关系抽取(Relation Extraction)领域的一个关键发展是远程监督(Distant Supervision)的引入，这是一种极具影响力的大规模合成训练数据(Synthetic Training Data)生成技术。该方法通过将现有知识库三元组(Knowledge Base Triplet)与非结构化文本(Unstructured Text)进行对齐，自动将任何同时提及两个目标实体的句子标记为该关系的正例(Positive Example)。例如，若知识库中记录 Steven Spielberg 是《拯救大兵瑞恩》的导演，则任何同时包含这两个实体的句子都会被自动赋予相应标签。这种方法本质上能以极低成本构建海量数据集，在无需人工标注(Human Annotation)的情况下，实现了结构化知识与自然语言处理的无缝对接。
![关键帧](keyframes/part002_frame_00110920.jpg)

## 可扩展性挑战与轻量级分类
尽管远程监督十分实用，但它也引入了显著的标签噪声(Label Noise)，因为文本中的实体共现(Entity Co-occurrence)并不能保证存在精确的语义关系（例如，需区分一般隶属关系与具体的导演职务）。此外，若为执行关系抽取而在整个互联网上运行大语言模型(Large Language Model, LLM)，其计算成本将难以承受。为解决这一问题，研究人员开发了轻量级、基于特征的神经分类器(Lightweight, Feature-based Neural Classifier)。现代实现通常借助 RoBERTa 或 Mistral 等高效模型，从实体跨度(Entity Span)的边界提取上下文嵌入(Contextual Embedding)。这些词元级(Token-level)表示随后被拼接(Concatenate)，并输入至一个简单的多层感知机(Multilayer Perceptron, MLP)中以预测关系类型，从而为庞大的生成式模型(Generative Model)提供了一种高度可扩展且经济高效的替代方案。
![关键帧](keyframes/part002_frame_00249799.jpg)
![关键帧](keyframes/part002_frame_00316000.jpg)

## 使用转移矩阵建模噪声标签
一个关键的研究方向专注于显式建模(Explicit Modeling)并缓解远程监督数据集中固有的噪声。一种有效的策略采用双数据集策略(Dual-dataset Strategy)：结合一个小规模人工构建的高质量数据集与一个大规模自动生成的噪声标签集。该模型引入了一个专门的噪声建模层，其中包含一个可学习的转移矩阵(Transition Matrix)。该矩阵对分类器的预测概率进行变换，通过校正初始概率分布(Probability Distribution)来修正系统性的标注错误(Annotation Error)。通过学习噪声如何扭曲真实的标签分布(Label Distribution)，模型能够动态调整训练信号(Training Signal)，从而在监督信号不完美的情况下依然保持强鲁棒性(Robustness)。
![关键帧](keyframes/part002_frame_00360120.jpg)
![关键帧](keyframes/part002_frame_00417200.jpg)

## 迹归一化与更广泛的适用性
为防止转移矩阵对噪声过拟合(Overfitting)或退化为任意变换，研究中引入了迹归一化(Trace Normalization)技术。这种正则化方法(Regularization Method)约束矩阵尽可能接近单位矩阵(Identity Matrix)（即恒等映射(Identity Mapping)），体现了这样一种先验假设(Prior Assumption)：尽管标注可能存在噪声，但大多数预测结果本身应当是正确的。优化过程在迹归一化约束与标准损失最小化(Loss Minimization)之间寻求平衡，从而有效平滑输出分布(Output Distribution)。这一噪声建模框架的应用远不止于关系抽取，它为任何依赖大规模、标注不完美(Imperfectly Labeled)数据集的机器学习任务，提供了一种通用范式(General Paradigm)。
![关键帧](keyframes/part002_frame_00551040.jpg)

## 总结与向知识增强模型的过渡
对知识库构建与关系抽取的探索，揭示了该领域从复杂的神经张量网络(Neural Tensor Network)向更精简的嵌入方法(Embedding Method)的演进，同时也凸显了远程监督在连接结构化数据与非结构化文本方面的关键作用。通过开发轻量级分类器与先进的噪声缓解技术(Noise Mitigation Technique)，研究人员已建立起用于大规模抽取关系知识(Relational Knowledge)的稳健流水线(Robust Pipeline)。随着这些构建和扩充知识库的基础方法日益成熟，研究重心自然转向下一个关键阶段：如何利用这些结构化知识库来直接指导、约束并增强神经语言模型(Neural Language Model)，即向知识增强模型(Knowledge-enhanced Model)的过渡。
![关键帧](keyframes/part002_frame_00575160.jpg)

---

## 利用现有词典改进嵌入(Embedding)
![关键帧](keyframes/part003_frame_00000000.jpg)
提升语言表示(Language Representation)的第一种方法涉及利用外部词典(External Lexicon)或知识图谱(Knowledge Graph)对现有嵌入进行修正。该技术通常应用于 Word2Vec 等非上下文嵌入(Non-contextual Embedding)，并采用具有双重目标的后处理变换(Post-processing Transformation)。其中的正则化项(Regularization Term)确保更新后的嵌入与原始初始值保持相近，同时拉近语义邻居(Semantic Neighbor)（如通过 WordNet 识别的同义词）之间的距离，并推远反义词之间的距离，从而在整合词汇知识(Lexical Knowledge)与保留原始嵌入空间(Embedding Space)之间实现平衡。

## 实体链接(Entity Linking)与处理多种实体表述形式
该方法可扩展至现代神经网络(Neural Network)模型和知识图谱嵌入(Knowledge Graph Embedding)，以处理现实世界中的实体变体(Entity Variant)。例如，指代“乔·拜登”的同一人物在文档中可能以多种形式出现（如 Joseph Biden、Joseph R. Biden、第46任总统等）。 
![关键帧](keyframes/part003_frame_00123600.jpg)
![关键帧](keyframes/part003_frame_00130600.jpg)
通过执行实体链接并识别这些变体字符串(Variant String)，可以显式地引导模型将同一实体的所有实例映射至更相近的嵌入表示(Embedding Representation)中。这减少了不必要的语义区分(Semantic Distinction)，并显著提升了问答(Question Answering)等下游任务(Downstream Task)的性能。

## 跨度嵌入(Span Embedding)与共指消解(Coreference Resolution)
![关键帧](keyframes/part003_frame_00198079.jpg)
![关键帧](keyframes/part003_frame_00205119.jpg)
![关键帧](keyframes/part003_frame_00215519.jpg)
当模型遇到缺乏单一预训练嵌入(Pre-trained Embedding)的多词实体(Multi-word Entity)或短语时，会面临一个常见挑战。 
![关键帧](keyframes/part003_frame_00229919.jpg)
![关键帧](keyframes/part003_frame_00235760.jpg)
![关键帧](keyframes/part003_frame_00241280.jpg)
解决方案在于共指消解和显式的跨度(Span)建模。共指消解用于识别不同的文本跨度何时指向同一个底层实体(Underlying Entity)（例如，识别出“乔·拜登”和后文的“拜登”指向同一个人）。 
![关键帧](keyframes/part003_frame_00254760.jpg)
为了生成鲁棒性(Robust)强的跨度嵌入，一种广泛采用的方法是从任意给定的词元(Token)跨度中提取三个向量：起始边界(Start Boundary)的嵌入、结束边界(End Boundary)的嵌入，以及跨度内所有词元的平均嵌入(Mean Embedding)。 
![关键帧](keyframes/part003_frame_00292440.jpg)
这三个表示经过拼接(Concatenation)后，通过一个神经变换层(Neural Transformation Layer)，从而生成统一的、具备上下文感知能力(Context-aware)的跨度嵌入。

## 跨度建模(Span Modeling)的应用
![关键帧](keyframes/part003_frame_00345919.jpg)
![关键帧](keyframes/part003_frame_00358280.jpg)
生成后，这些跨度嵌入可用于多种自然语言处理(NLP)任务。它们既可用于查询外部知识库(External Knowledge Base)，也可将成对的跨度嵌入输入分类器(Classifier)，以预测它们是否对应同一实体，从而判定共指关系(Coreference Relation)。 
![关键帧](keyframes/part003_frame_00382520.jpg)
![关键帧](keyframes/part003_frame_00410280.jpg)
这种对跨度及其相互关系进行建模的统一框架，为解决包括词性标注(Part-of-Speech Tagging)、命名实体识别(Named Entity Recognition, NER)和关系抽取(Relation Extraction)在内的多样化任务，提供了多功能的主干架构(Backbone Architecture)。

## 向语言模型(Language Model)注入知识
另一个关键领域是将结构化知识(Structured Knowledge)直接集成到语言模型中。一种直观的方法是，从知识图谱中检索相关事实，并通过提示(Prompting)技术将其注入模型。 
![关键帧](keyframes/part003_frame_00435599.jpg)
![关键帧](keyframes/part003_frame_00441880.jpg)
然而，提示方法在处理低频或长尾实体(Long-tail Entity)时往往表现不佳，因为模型难以从学习不足的表示中进行有效泛化(Generalization)。为克服这一局限，研究人员提出生成结构化的占位符(Placeholder)，而非直接输出原始文本。模型不再直接预测单词，而是输出诸如 `[birth name]` 或 `[family name]` 之类的语义标签。 
![关键帧](keyframes/part003_frame_00494919.jpg)

## 模板化生成(Templated Generation)与训练目标(Training Objective)
![关键帧](keyframes/part003_frame_00575160.jpg)
在模型预测出这些占位符后，一个后处理系统(Post-processing System)会利用知识库中经过验证的数据对其进行填充。例如，在起草人物简介时，模型可以稳定地预测出如 `[birth name], born [birth date]` 之类的固定模板结构。由于这些模式具有高度一致性，该方法能够生成基于事实的高置信度(High-confidence)输出，从而显著缓解模型幻觉(Model Hallucination)问题。训练过程中的主要挑战在于设计目标函数(Objective Function)，即确定何时以及如何替换特定的文本跨度。通过对各种可能的替换策略进行边缘化处理(Marginalization)，该问题得到了有效解决，从而使模型能够学习最优的模板利用方式。

---

## 将文本语料库作为可追溯知识库进行推理
![关键帧](keyframes/part004_frame_00000000.jpg)
现代自然语言处理(Natural Language Processing, NLP)和检索增强生成(Retrieval-Augmented Generation, RAG)系统面临的一个长期挑战是：如何以传统应用于结构化知识库(Structured Knowledge Base)的精度，对非结构化文本语料库(Unstructured Text Corpus)进行推理。一种有效的方法通过对实体提及(Entity Mention)进行相关性匹配，将大型文本集合视为可追溯的知识存储库。该方法构建“提及向量”(Mention Vector)，以聚合特定实体在整个语料库中的所有出现实例。通过预训练模型为所有提及生成稠密嵌入(Dense Embedding)，系统能够高效检索上下文证据(Contextual Evidence)。
![关键帧](keyframes/part004_frame_00051880.jpg)
该检索机制依赖于专门优化的稠密查询向量(Dense Query Vector)，用于精准定位能够解答复杂问题的提及。例如，查询“《感恩至死》乐队与鲍勃·迪伦的专辑何时发行？”会被编码为特定实体的嵌入表示。模型经过训练，能够将这些查询向量与语料库嵌入进行匹配，优先返回包含最相关事实信息的句子，从而有效解答实体-关系查询(Entity-Relation Query)。

## 通过弱监督训练稠密检索模型
训练此类稠密检索(Dense Retrieval)架构需要海量标注数据，在实际应用中通常通过弱监督(Weak Supervision)技术来实现。借助现有的大规模知识图谱(Knowledge Graph)，研究人员无需人工标注即可自动生成监督信号(Supervision Signal)。例如，给定已知三元组(Triple)“史蒂文·斯皮尔伯格执导了《拯救大兵瑞恩》”，可通过程序自动生成基于模板的问题（如“谁执导了《拯救大兵瑞恩》？”）。随后，模型在训练过程中会提升语料库（如维基百科）中目标实体与关系自然共现的句子嵌入权重。这使得合成生成的查询(Synthetically Generated Query)能够与其潜在的文本答案(Textual Answer)对齐，为知识增强的检索系统(Knowledge-Enhanced Retrieval System)提供了一种高度可扩展的训练范式(Training Paradigm)。

## 固定模式的局限性与无模式抽取
![关键帧](keyframes/part004_frame_00230839.jpg)
![关键帧](keyframes/part004_frame_00247679.jpg)
传统知识图谱（如 Wikidata）基于固定的预定义模式(Predefined Schema)运行。所有可能的关系（如“属于……的实例”(Instance Of)、“国籍”(Nationality)、“签名”(Signature)等）均由数据库策展人(Database Curator)事先设定。尽管这些静态模式在通用知识方面较为全面，但难以覆盖高度专业化、技术性强或快速发展的新兴领域。
![关键帧](keyframes/part004_frame_00288799.jpg)
![关键帧](keyframes/part004_frame_00336240.jpg)
随着应用向垂直领域(Domain-Specific Application)扩展，预先定义所有必需的实体或关系变得越来越不切实际。例如，针对大语言模型(Large Language Model, LLM)的技术属性“位置嵌入变体”极不可能出现在通用知识库中。这一局限性催生了无模式(Schema-free)抽取技术，其旨在从原始文本中联合发现(Jointly Discover)底层关系结构，并同步提取目标信息。

## 开放信息抽取（OpenIE）
![关键帧](keyframes/part004_frame_00354280.jpg)
在无模式抽取技术中，最主流的范式是开放信息抽取(Open Information Extraction, OpenIE)。OpenIE 不将文本映射到预定义本体(Predefined Ontology)，而是直接将实体之间的字面文本跨度(Literal Text Span)视为关系本身。例如，从“United has a hub in Chicago”中抽取的关系即为字面表述“has a hub in”。虽然该方法在无模式约束下能够捕捉细粒度的语言变化，但缺乏语义抽象(Semantic Abstraction)能力。同一事实的不同句法表述（如“Chicago is the headquarters of United Continental Holdings”）会被视为完全不同的关系，这使得跨文档的知识规范化(Knowledge Normalization)与聚合变得十分困难。

## 大规模基于规则与基于序列的抽取
![关键帧](keyframes/part004_frame_00413280.jpg)
![关键帧](keyframes/part004_frame_00432320.jpg)
![关键帧](keyframes/part004_frame_00438799.jpg)
为了在 Web 规模(Web-scale)上执行 OpenIE，基于规则的系统（如 TextRunner 和 Reverb）历来作为高效的基线(Baseline)。这些系统利用句法解析器(Syntactic Parser)强制执行语法约束，例如要求关系必须包含动词谓语(Verbal Predicate)，且实体必须是结构完整的名词短语(Noun Phrase)。由于基于规则的抽取计算成本低廉，且能保持较高的精确率(Precision)与召回率(Recall)，它在处理海量语料库时依然极具实用价值。后续研究将这些基于规则系统的高质量输出作为弱监督信号，用于训练速度更快、效率更高的序列到序列(Sequence-to-Sequence, seq2seq)神经模型，以更高效地复现信息抽取流程。

## 启发式验证与向神经模型的转变
![关键帧](keyframes/part004_frame_00512240.jpg)
OpenIE 面临的一个关键挑战是 Web 规模数据固有的噪声问题，抽取出的事实可能存在事实性错误(Factual Error)或语境误导(Contextual Misleading)。为缓解这一问题，系统采用启发式证据聚合(Heuristic Evidence Aggregation)与基于频率的过滤策略(Frequency-based Filtering)。其核心假设基于统计学原理：如果在大型语料库中极少观察到两个常见实体之间的特定关系，则该关系极可能是错误的；反之，频繁共现的模式则被视为可靠。这些启发式方法能有效剔除模型幻觉(Model Hallucination)或虚假抽取结果，提升整体的事实可靠性。
![关键帧](keyframes/part004_frame_00589080.jpg)
尽管启发式验证颇具价值，但其本质存在局限性，且高度依赖浅层统计信号。这推动了近期研究向能够捕捉更深层语义关系的神经 OpenIE 架构(Neural OpenIE Architecture)转型。然而，当前的主要瓶颈仍在于获取高质量、大规模的训练数据。学界正持续探索先进的弱监督、自训练(Self-training)与多跳推理(Multi-hop Reasoning)技术以弥合这一差距，推动信息抽取超越僵化的句法规则，迈向更鲁棒(Robust)且具备上下文感知能力(Context-aware)的新阶段。

---

## 通过众包简化关系抽取(Relation Extraction)
![关键帧](keyframes/part005_frame_00000000.jpg)
传统上，由于标注界面(Annotation Interface)的复杂性，获取高质量的关系抽取训练数据一直充满挑战。要求众包标注员(Crowdsourced Annotator)手动选择头实体(Head Entity)、关系跨度(Relation Span)和尾实体(Tail Entity)通常需要大量的前期培训，且容易导致标注结果不一致。一种更有效的方法是将任务转化为简单、直观的问题。与其进行复杂的跨度选择，不如直接向标注员提出诸如“谁完成了某事？”的直接问题，或提示他们识别句子中的语义角色(Semantic Role)。这种简化显著降低了标注员的认知负荷(Cognitive Load)，从而能够快速构建大规模数据集，用于训练鲁棒的(Robust)监督式序列模型(Supervised Sequence Model)。

## 用于链接预测(Link Prediction)的张量分解(Tensor Factorization)
![关键帧](keyframes/part005_frame_00111640.jpg)
为了克服开放信息抽取(Open Information Extraction, OpenIE)缺乏抽象能力的问题，研究人员利用张量分解技术挖掘了知识图谱(Knowledge Graph)的内在结构。该方法将关系数据建模为三维张量(3D Tensor)，其中三个轴分别代表左实体(Left Entity)、右实体(Right Entity)和关系类型。已知事实标记为1，缺失或未验证的事实标记为0。通过对该张量进行低秩近似(Low-Rank Approximation)，模型能够最小化重构误差(Reconstruction Error)，从而有效地填补知识空白。原本标记为0但重构值接近1的条目，将被预测为潜在关系。这种数学方法使系统能够推断缺失的链接，并在不同关系间实现泛化(Generalization)，而无需完全依赖表层的文本模式。

## 用于多源知识集成的通用模式(Universal Schema)
![关键帧](keyframes/part005_frame_00295719.jpg)
基于张量推理，通用模式的概念将来自多个结构化知识库(Structured Knowledge Base)（如 Freebase、Wikidata、TAC KBP）的关系与非结构化的 OpenIE 抽取结果整合到一个共享嵌入空间(Shared Embedding Space)中。这一统一框架使模型既能利用知识图谱中高质量、人工整理(Curated)的数据，又能同时受益于从网络(Web)抽取的海量关系数据。通过对所有模式下的已知正负样本(Positive and Negative Samples)进行训练，模型能够预测那些在结构化数据库中缺乏条目的实体的关系存在性；在此情况下，模型将转而依赖来自 OpenIE 的文本证据。关键在于，这些预测可以追溯回原始源句子，从而实现人在回路(Human-in-the-Loop)的验证，并确保事实的可靠性。
![关键帧](keyframes/part005_frame_00387120.jpg)

## 多跳推理(Multi-hop Reasoning)与关系路径建模(Relational Path Modeling)
![关键帧](keyframes/part005_frame_00454400.jpg)
超越单一关系归纳，先进的知识推理专注于对多跳关系路径进行建模，以推断复杂的依赖关系。例如，为了给一篇论文推荐发表渠道，系统可以遍历诸如 `Keyword → Paper → Journal` 或 `Keyword → Paper → Author → Paper → Journal` 的路径。这些多步路径提供了极具表达力(Expressiveness)且富含上下文的信号，这是单一的成对关系(Pairwise Relation)所无法捕捉的。通过扩展并聚合这些路径，系统将其输入预测模型以识别高概率关系，从而有效利用知识图谱的传递性(Transitivity)与组合性(Compositionality)特征。
![关键帧](keyframes/part005_frame_00506760.jpg)
![关键帧](keyframes/part005_frame_00517480.jpg)
近期的进展使这种基于路径的推理实现了完全的端到端(End-to-End)可训练与可微分(Differentiable)，从而摆脱了僵化且脆弱的演绎逻辑规则(Deductive Logical Rules)。该框架并未强制要求绝对的真值条件(Truth Condition)，而是采用了一种概率化方法(Probabilistic Approach)：逻辑路径仅增加关系存在的可能性，而非绝对保证其成立。例如，一名学生“在 CMU 就读”这一事实与“从 CMU 毕业”高度相关，但仍存在例外情况。通过将逻辑约束松弛(Relaxation)为可微操作，模型能够学会稳健地权衡多条关系路径，从而有效处理现实世界中的噪声与反例。
![关键帧](keyframes/part005_frame_00547160.jpg)

---

## 逻辑规则与结构化知识图谱
在基于知识推理(Knowledge-based Reasoning)中，某些路径具有高度的概率性。例如，当某人就读于特定大学时，其学习计算机科学(Computer Science)的可能性远大于医学。然而，逻辑规则极少绝对严格，例外情况总是存在。为了对此进行建模，研究人员将每条推理路径视为一系列带有相关规则权重的矩阵乘法(Matrix Multiplication)。这种数学方法最终能够预测给定的逻辑规则是否成立。从历史上看，该方法主要在结构化知识空间与知识图谱(Knowledge Graphs)中进行开发与测试。
![关键帧](keyframes/part006_frame_00000000.jpg)

## 将可微逻辑应用于语言模型
目前，直接将可微逻辑(Differentiable Logic)规则应用于语言模型的研究仍较为有限。这主要是因为文本的非结构化特性导致数据对齐困难，且实现复杂度显著较高。尽管面临这些挑战，该研究方向依然极具吸引力。许多语言模型在稳健推理(Robust Reasoning)方面仍存在不足，如何提升其逻辑能力仍是人工智能(Artificial Intelligence)领域的一个开放性难题。借鉴早期在结构化领域取得成功的方法，并将其适配至非结构化的语言模型空间，为技术演进提供了一条充满希望的路径。
![关键帧](keyframes/part006_frame_00036040.jpg)
![关键帧](keyframes/part006_frame_00041960.jpg)

## 探测语言模型中的内部知识
近期研究的一个主要焦点是探测(Probing)语言模型内部究竟存储了哪些知识。通过利用全面的知识库，研究人员可以系统地评估模型对特定事实的实际“掌握”程度。传统的问答(Question Answering)系统与检索增强生成(Retrieval-Augmented Generation, RAG)模型通常依赖维基百科文章等外部资源来回答问题。现代研究的一个关键转变在于提出如下问题：在不使用RAG或外部检索的情况下，我们能否准确判断哪些知识是原生编码在模型参数中的？我们又能在多大程度上可靠地提取它们？
![关键帧](keyframes/part006_frame_00071040.jpg)

## LAMA 基准测试与基于提示词的评估
应对这一挑战的一篇奠基性论文引入了LAMA(Language Model Analysis)基准测试。该研究将结构化数据库查询（如SQL或SPARQL）与大语言模型的自然语言提示词(Prompt)进行对比，实质上开创了早期基于提示词的评估范式。该方法使用诸如“但丁出生于[MASK]”的模板，并让掩码语言模型(Masked Language Model, MLM)预测正确的客体(Object)（例如“佛罗伦萨”）。通过将知识库条目作为标准答案，研究人员测试了这些信息能否从神经网络中被成功还原。针对41种关系（例如“X成立于Y”）采用人工编写的提示词进行测试，ELMo、Transformer-XL和BERT Base等早期模型的准确率最高仅为31%，从而为内部知识探测确立了基准。

## 多语言知识与检索二分法
一项后续研究将此基准扩展至多种语言，揭示了一个有趣的现象：模型内部编码的知识与其实际检索知识的能力之间存在显著差异。研究人员利用多语言知识库，以不同语言提出了相同的事实性问题。结果显示，在非英语、低资源(Low-resource)或语言类型距离较远的语种上，模型性能显著下降。即使忽略输出语言的差异，仅以答案正确与否作为评分标准，模型的表现依然不理想。鉴于模型能够可靠地用英语作答，这表明它们本质上“知晓”该事实。其他语言上的性能差距表明，瓶颈在于检索机制而非知识储备不足，这引出了关于如何改进跨语言信息获取(Cross-lingual Information Retrieval)的重要问题。
![关键帧](keyframes/part006_frame_00249239.jpg)
![关键帧](keyframes/part006_frame_00262719.jpg)

## 角色设定提示与性能波动
关于提示词框架如何影响知识检索的研究也探讨了角色设定(Role-playing)的作用。添加诸如“我是一位老人”、“我是一位年轻女性”或提及特定背景等上下文，会大幅改变模型准确回答问题的能力。这凸显了一个反直觉的现实：模型虽具备底层知识，但提示的框架在很大程度上决定了输出的成败。这种现象具有两面性：尽管误导性的角色设定会降低性能，但策略性的提示则能有效提升表现。例如，在代码生成(Code Generation)任务中，附加类似“我已仔细检查，所有单元测试均已通过”的“魔法提示词(Magic Prompts)”，能将准确率提高数个百分点。本质上，引导模型进入正确的“状态”或上下文，能够直接优化其推理与检索能力。
![关键帧](keyframes/part006_frame_00495800.jpg)

## 面向知识任务的微调
除提示词工程(Prompt Engineering)外，提升知识检索能力的另一种直接方法是针对性微调(Fine-tuning)。研究表明，在合成生成的基于知识的问答数据上微调语言模型，能显著提升其回答与结构化知识库相关查询的能力。通过在训练过程中让模型接触格式化的问答对，其内部用于检索和表述事实信息的路径将变得更加可靠与准确。
![关键帧](keyframes/part006_frame_00526240.jpg)
![关键帧](keyframes/part006_frame_00532880.jpg)

## 模型参数内的多跳推理
最后一个讨论领域聚焦于直接在语言模型参数内部进行的多跳推理(Multi-hop Reasoning)。传统的多跳推理通常涉及遍历外部知识库中的显式推理链，而最新研究则测试语言模型能否在内部串联多个事实以回答复杂查询。例如，给定类似“国家 → 美国 → 总统 → 出生日期”的推理链，该研究评估了模型在不依赖外部参考的情况下推导这些概念步骤的能力。这一研究方向对于深入理解现代神经网络架构中结构化推理的整合深度与可访问性至关重要。
![关键帧](keyframes/part006_frame_00539560.jpg)

---

## 从知识库构建多跳问题
研究人员可通过遍历结构化知识库(Structured Knowledge Base)中的关系路径，系统地生成多跳问题(Multi-hop Questions)。利用预定义模板(Pre-defined Templates)，研究者可将单一的事实查询无缝组合为复合问题(Composite Questions)。例如，第一个子问题(Sub-question)用于识别录制某首特定歌曲的艺术家，第二个子问题则询问该艺术家的居住地。这些问题可被整合为一个复杂的多跳提示词(Multi-hop Prompt)：*“录制《[歌曲]》的艺术家居住在佐治亚州的哪个地区？”* 这种结构化方法提供了一个受控环境(Controlled Environment)，用于测试模型如何处理层级化信息(Hierarchical Information)。
![关键帧](keyframes/part007_frame_00000000.jpg)

## 评估多跳推理的预期表现
为评估语言模型(Language Model)的能力，研究人员测量了模型在单个子问题及最终复合问题上的表现。在将模型视为完美知识处理器(Knowledge Processor)的理想场景下，成功回答复合问题应严格依赖于对两个前置子问题的正确解答。若模型掌握两部分的答案，理应能推导出复合答案；若任一环节出错，最终输出自然应为错误。这为推理链(Reasoning Chain)的预期行为确立了一个清晰的基准(Baseline)。
![关键帧](keyframes/part007_frame_00030000.jpg)

## 第二跳的不成比例影响
令人惊讶的是，跨多种问题类型的实验结果彻底推翻了理想模型的假设。模型正确解答复合问题的能力并非同时取决于两个子问题的答案，而是与它在*第二跳(Second Hop)*子问题上的成功率高度相关。第一子问题的准确率与最终结果之间几乎不存在统计学意义上的关联。本质上，模型似乎主要依赖最后一步推理(Reasoning Step)的上下文、结构或答案来推导正确的复合答案，而非遵循严格的顺序逻辑推理(Sequential Logical Reasoning)。

## 理解推理瓶颈
这一现象与实际推理过程中的局限性相符。若第二个子问题要求生成冗长列表（例如*“所有美国总统”*），直接求解将异常困难。相反，若第二问针对的是如首都城市这类特定单一事实，即便第一步的推理仍显模糊，模型也可能正确推断或猜测出答案。最后一跳的难度与答案空间(Answer Space)不成比例地决定了整体成功率。这表明语言模型并非总是执行僵化的逐步逻辑链接，而是高度依赖最终查询的上下文线索(Contextual Clues)。

## 作为结构化探测工具的知识库
尽管存在上述推理反常现象，知识库依然是系统评估语言模型实际知识储备、推理能力及其逻辑边界(Logical Boundaries)的宝贵资源。借助结构化数据，研究人员可设计高度受控的实验，以严谨且可复现的方式探测(Probing)语言模型的能力、记忆检索(Memory Retrieval)与推理逻辑。至此，关于现代语言模型中知识整合(Knowledge Integration)与推理评估(Reasoning Evaluation)的探讨暂告一段落。