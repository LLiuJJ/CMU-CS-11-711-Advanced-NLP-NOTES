## 语言模型与数据压缩
![关键帧](keyframes/part004_frame_00000000.jpg)
正如 gzip、bz2 或 zip 等工具可以将文件压缩成更小的体积一样，任何概率语言模型(Probabilistic Language Model)从根本上都可用于数据压缩(Data Compression)，将数据转化为更紧凑的表示形式。其核心原理非常直观：高概率的序列（无论是句子还是更长的文档）可被编码(Encoding)为更短的输出字符串。在信息论(Information Theory)中，这代表了一种理想的数据压缩范式。通过为高概率序列分配较短的编码，为低概率序列分配较长的编码，模型有效地最小化了存储或传输文本所需的总比特长度，从而在概率建模(Probabilistic Modeling)与最优压缩策略(Optimal Compression Strategy)之间建立了直接联系。

## 算术编码与区间细分
![关键帧](keyframes/part004_frame_00028160.jpg)
实现这种最优压缩的核心机制是算术编码(Arithmetic Coding)。举例而言，假设词表(Vocabulary)中仅包含三个词元(Token)，其对应概率分别为：Token A 占 50%，Token B 占 33%，Token C 约占 17%。单位区间(Unit Interval) `[0, 1]` 会依据这些概率按比例进行划分。若目标序列仅包含单个 token，算法会递归地细分区间，直至找到一个完全落入该 token 概率范围内的最短二进制字符串。在该概率分布下，Token A 直接映射至二进制前缀(Binary Prefix) `0`；Token B 需进一步细分，映射至 `10`；Token C 概率最低，占据最小区间，映射至 `111`。一旦某个二进制序列完全落入特定 token 的概率区间内，该序列即成为该 token 的压缩表示(Compressed Representation)。

## 上下文概率与编码效率
![关键帧](keyframes/part004_frame_00288999.jpg)
当将算术编码应用于多词元序列(Multi-token Sequence)时（无论采用一元模型(Unigram Model)、n-gram 模型还是神经模型(Neural Model)），该过程会根据上下文动态调整概率分布。若模型判定 Token C 紧跟在 Token B 之后的条件概率(Conditional Probability)极高，则 C 对应的概率区间将显著扩大。区间越大，意味着只需更短的二进制字符串即可唯一标识该范围，从而大幅缩短该序列的编码长度。对于长文本而言，总编码长度将直接收敛至由模型熵值(Entropy)决定的理论比特数。尽管在简化示例中，某些二进制前缀（如示例中的 `110`）可能看似未被使用或存在冗余，但整体编码机制确保了在完整序列中，更高的预测概率始终会转化为更短、更高效的编码。

## 通过预测练习理解困惑度
![关键帧](keyframes/part004_frame_00426400.jpg)
衍生自上述概念的一项核心评估指标是困惑度(Perplexity)，其数学定义通常表示为 $2^{\text{entropy}}$ 或 $e^{-\text{word-level log likelihood}}$（词级对数似然(Word-level Log-likelihood)）。为直观理解困惑度的物理意义，可参考一个交互式预测任务：给定提示语(Prompt) *"If one sees a squirrel, it will usually..."*（如果一个人看到松鼠，它通常会……），人类往往倾向于猜测 *bark*（吠叫）、*run*（跑）或 *chase*（追逐）等符合直觉的动词。然而，像 GPT-2 这类经过充分训练的语言模型则极有可能预测 *start* 或系动词(Copula)。这一现象揭示了人类作为下一词元预测器(Next-token Predictor)时，往往存在认知偏差且概率校准不佳(Poorly Calibrated)。困惑度正是对这一预测难度的量化：它代表了在正确猜出真实下一词元之前，需从模型分布(Model Distribution)中抽样的等效随机选项数量。若模型为正确的词元分配了高概率，其困惑度便会较低（例如约 4.66），这意味着平均而言仅需约 4.66 次猜测即可“命中”正确答案。

## 评估最佳实践与词汇一致性
![关键帧](keyframes/part004_frame_00577200.jpg)
在使用困惑度或对数似然(Log-likelihood)对比不同模型（如 Llama 与 GPT）时，严谨的评估规范(Evaluation Protocol)对于确保基准测试(Benchmarking)的公平性至关重要。其中最关键的因素是，确保所有参与对比模型的归一化分母完全一致。无论困惑度是按词(Word)、词元(Token)还是字符(Character)进行计算，用于除总对数概率的分母（即序列长度）必须保持严格匹配。测试集(Test Set)长度计算方式的细微差异，可能会人为地抬高或压低报告的指标分数，从而导致直接的性能比较(Performance Comparison)产生误导。因此，在解读或发布语言模型评估结果时，统一的分词策略(Tokenization Strategy)与标准化的分母计算是不可妥协的硬性要求。