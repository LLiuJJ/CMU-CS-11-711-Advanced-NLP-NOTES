## 平滑与折扣法基础
![关键帧](keyframes/part003_frame_00000000.jpg)
语言模型平滑(Smoothing)背后的核心思想在于模型如何根据上下文对统计证据进行加权。当观测到的计数(Count)总和较大时，模型会更依赖高阶分布(Higher-order distribution)；反之，若计数稀疏，模型则会采用回退(Backoff)策略，转而更依赖低阶分布(Lower-order distribution)。本质上，经验证据越充分，模型对特定上下文的依赖程度就越高。这一原则构成了各类平滑技术的基础。除了简单的插值法(Interpolation)，还有引入折扣系数(Discounting)的折扣法。该方法通过一个超参数从每个观测计数中减去一个固定值（例如 0.x）。实践证明，这种减法操作能更准确地从数学层面拟合自然语言固有的长尾分布(Long-tailed distribution)。该理论可通过严谨的数学推导得出，并在早期奠基性文献中有详细论述。

## Kneser-Ney 平滑与模型集成
![关键帧](keyframes/part003_frame_00096880.jpg)
在神经网络占据主导地位之前，Kneser-Ney平滑(Kneser-Ney Smoothing)曾是语言建模领域的最前沿技术。该方法不仅对高阶计数应用折扣，还会智能地调整低阶分布(Lower-order distribution)。具体而言，它根据一个词在完全*新颖(Novel)*上下文中出现的频率来调整其低阶计数。其核心逻辑在于：当模型遭遇未见过的或全新的高阶上下文时，应主要依赖低阶分布进行预测。在深刻理解这一回退(Backoff)机制后，研究者能够巧妙地重构低阶分布，从而在主要上下文信号较弱时最大化预测准确率。尽管神经模型在很大程度上已取代了这些传统技术，但其中蕴含的一个关键洞见对现代实践依然至关重要：在组合或集成(Model Ensemble)多个语言模型时，必须审慎评估各模型在特定上下文中的优势，因为不同模型天然会在各异的语言场景中表现出最佳性能。

## 推动向神经网络模型转变的局限性
![关键帧](keyframes/part003_frame_00136599.jpg)
![关键帧](keyframes/part003_frame_00149560.jpg)
n-gram模型的若干结构性缺陷直接促使业界转向神经语言模型(Neural Language Model)。首先，与早期的文本分类方法类似，n-gram模型无法在语义或句法相似的词之间共享统计强度(Sharing statistical strength)（例如，将 "buy" 和 "purchase" 视为毫无关联的词元(Token)）。其次，它们对中间上下文的鲁棒性较差。一旦上下文窗口中出现罕见或未见过的词，模型便会立即回退至一元分布(Unigram distribution)，从而导致预测质量大幅下降。第三，n-gram架构从根本上无法捕捉超出其固定窗口范围的长距离依赖(Long-range dependency)。在神经网络问世之前，研究人员针对上述问题提出了大量零散的修补方案。然而，将这些方案全部整合的工程实现过于复杂且难以维护，以至于多数从业者最终只能直接采用开箱即用(Out-of-the-box)的标准 n-gram 模型，而这使其难以满足现代应用对可扩展性(Scalability)的需求。

## N-Gram 模型的效率与记忆优势
![关键帧](keyframes/part003_frame_00229160.jpg)
尽管神经网络在统一应对上述语言建模挑战方面展现出显著的工程优势，但 n-gram 模型仍保有独特的实用价值。虽然神经模型通常能取得更优的生成性能，但 n-gram 模型的概率估计(Estimation)与推理(Inference)速度极快，且计算过程可实现完全并行化。此外，在建模极低频现象(Rare phenomena)时，n-gram 模型有时甚至能超越神经网络。它仅需单次观测即可“记住”特定序列并为其分配高概率。相比之下，神经模型往往难以有效记忆或从孤立样本中泛化(Generalization)，有时会彻底遗忘罕见模式。这一特性使得 n-gram 方法在要求精准且具备数据高效性(Data-efficiency)的检索任务中，依然具有不可替代的实用价值。

## KenLM 与现代大规模应用
![关键帧](keyframes/part003_frame_00352719.jpg)
![关键帧](keyframes/part003_frame_00360279.jpg)
![关键帧](keyframes/part003_frame_00379319.jpg)
训练与评估 n-gram 模型的标准工具包是 KenLM，其以惊人的运行速度而闻名业界。这也催生了业内的一则趣谈：部分招聘启事要求候选人“具备10年大语言模型(Large Language Models, LLMs)工作经验”。尽管大型神经模型问世不久，但 KenLM 的开发者实际上早已积累了同等量级的经验——他曾在词表规模超 10 万词元(Token)的 Web 级语料库(Web-scale corpus)上训练过庞大的 7-gram 模型。此类模型的理论参数空间(Parameter space)令许多现代神经 LLM 相形见绌，但由于绝大多数 n-gram 计数为零，模型依然保持了高度稀疏性(High sparsity)与内存高效性(Memory efficiency)。这种可扩展性(Scalability)在当今的现代应用中依然大放异彩。例如，近期论文 *Data Selection for Language Models via Importance Resampling*（基于重要性重采样的语言模型数据选择）便充分利用了 n-gram 的高效特性来处理海量网页数据集。由于在全互联网规模上直接运行神经模型计算成本过高，作者采用高效的 n-gram 模型对词频计数拟合高斯分布(Gaussian distribution)，从而为下游的神经模型训练实现了高效的大规模数据筛选。

## 语言模型评估指标
![关键帧](keyframes/part003_frame_00415919.jpg)
![关键帧](keyframes/part003_frame_00426199.jpg)
![关键帧](keyframes/part003_frame_00431799.jpg)
评估语言模型在核心任务上的表现需要标准化的评估指标，其中对数似然(Log-likelihood)是最基础的方法。计算该指标时，需对保留测试集(Held-out test set)中所有句子的概率取对数并求和。然而，原始对数似然极少直接用于模型对比，因其数值与数据集规模高度相关：语料库越大，累计的对数值越负（绝对值越大）。因此，词元级别对数似然(Token-level log-likelihood)成为了业界标准，其计算方式为将语料库的总对数概率除以词元(Token)总数。许多学术论文倾向于报告负对数似然(Negative Log-Likelihood, NLL)，因为它与标准的损失函数定义一致，即数值越低代表模型性能越优。另一项广泛使用的指标是交叉熵(Cross-Entropy)，用于量化模型在独立测试集上的泛化能力。传统上，交叉熵采用以 2 为底的对数进行计算。这一做法在很大程度上源于信息论(Information Theory)的历史沿革：在信息论框架下，概率分布直接对应最优数据压缩方案，因此交叉熵直观地衡量了对测试数据进行编码所需的平均比特数。