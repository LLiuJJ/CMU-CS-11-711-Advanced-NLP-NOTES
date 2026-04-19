## 硬件限制(Hardware Limitations)与框架兼容性(Framework Compatibility)
![关键帧](keyframes/part002_frame_00000000.jpg)
尽管量化(Quantization)在理论上能带来显著的显存(VRAM)节省与推理速度提升，但其实际落地严重受制于底层硬件(Hardware)与软件生态(Software Ecosystem)。许多处理器缺乏对非标准位宽(Bit-width)（如 3-bit 整数）的原生支持(Native Support)，导致计算时默认回退(Fallback)至 4-bit 处理，从而完全抵消了预期的加速收益。此外，PyTorch 等深度学习框架(Deep Learning Frameworks)对原生量化的支持参差不齐；部分网络架构（如循环神经网络(Recurrent Neural Networks, RNN)）或特定算子可能完全不支持量化操作。因此，算法工程师在生产环境(Production Environment)部署压缩模型前，必须严格验证 GPU 硬件能力、框架兼容性(Framework Compatibility)以及自定义加速器(Custom Accelerators)的特定要求。

## 量化感知训练(Quantization-Aware Training, QAT)与二值网络(Binary Networks)
![关键帧](keyframes/part002_frame_00041960.jpg)
为缓解训练后量化(Post-training Quantization)固有的精度损失(Precision Loss)，量化感知训练(Quantization-Aware Training, QAT)将量化约束(Quantization Constraints)直接嵌入至训练循环(Training Loop)中。即便在极端二值化(Extreme Binarization)场景下（即所有权值(Weights)与激活值(Activations)被严格限制为 -1 或 1），该方法仍展现出显著成效。通过引入特定的梯度近似技术(Gradient Approximation Techniques)以处理反向传播(Backpropagation)过程中的离散梯度(Discrete Gradients)，研究证实完全二值化的网络同样能达到当时的最先进水平(State-of-the-Art, SOTA)。例如，在 CIFAR-10 数据集上实现了约 10% 的测试误差(Test Error)，有效匹配甚至超越了同期的全精度模型(Full-precision Models)。这一结果构成了强有力的概念验证(Proof of Concept)，表明在训练阶段充分纳入精度约束，能在大幅压缩模型体积的同时，完好保留其表征能力(Representational Capacity)。

## 用于量化的逐层蒸馏(Layer-wise Distillation)
![关键帧](keyframes/part002_frame_00076000.jpg)
另一种先进的压缩策略是采用逐层顺序训练(Layer-by-Layer Sequential Training)的方式对量化模型进行优化，以拟合其全精度对应模型(Full-precision Counterpart)的行为。该方法不单纯依赖最终输出进行端到端优化(End-to-End Optimization)，而是直接提取原始模型各层的隐藏状态(Hidden States)与未归一化预测值(Logits)作为监督目标(Supervision Targets)。通过逐步将量化层的输出与完整模型精确的中间表示(Intermediate Representations)及数据流(Data Flow)对齐，这种逐层蒸馏技术能够有效捕获训练过程中可能丢失的细微特征，进而构建出更稳健、更精准的压缩模型架构。

## 现代参数高效量化(Parameter-Efficient Quantization)：QLoRA(Quantized Low-Rank Adaptation)
![关键帧](keyframes/part002_frame_00090440.jpg)
基于上述基础技术，QLoRA(Quantized Low-Rank Adaptation)等近期突破彻底革新了大型语言模型(Large Language Models, LLMs)的实际部署范式(Deployment Paradigm)。通过将参数高效微调(Parameter-Efficient Fine-Tuning, PEFT)技术与高度量化（通常为 4-bit）的基础模型(Base Model)相结合，QLoRA 使得在消费级硬件(Consumer-grade Hardware)上微调超大规模网络成为现实，且精度损失(Precision Loss)微乎其微。凭借其卓越的计算效率(Computational Efficiency)、极高的社区采纳率(Community Adoption Rate)以及稳健的经验性能(Empirical Performance)，QLoRA 已成为当前现代 NLP 应用中最受推荐且最具实用价值的量化微调方案之一。

## 模型剪枝(Model Pruning)简介
![关键帧](keyframes/part002_frame_00152319.jpg)
与量化不同，模型剪枝(Model Pruning)在模型压缩(Model Compression)领域采取了截然不同的技术路径。它并非均匀降低所有参数的数值精度，而是通过将特定权重精确置零(Zeroing out)来实现选择性剔除，其余参数则保持原始精度不变。最直观的策略是幅值剪枝(Magnitude Pruning)，即按照预设比例，将绝对值最小的权重直接置零。在机器翻译(Machine Translation)等领域的实证研究(Empirical Studies)表明，模型近半数的参数均可被安全剪除，且对下游任务性能(Downstream Performance)几乎无负面影响。这一现象进一步印证了深度神经网络(Deep Neural Networks)在训练阶段存在显著的过参数化(Overparameterization)特性，而在实际推理(Inference)阶段，维持模型功能所需的活跃权重(Active Weights)实际上远少于总参数量。

## 彩票假说(Lottery Ticket Hypothesis)
![关键帧](keyframes/part002_frame_00204559.jpg)
非结构化剪枝(Unstructured Pruning)与著名的“彩票假说(Lottery Ticket Hypothesis)”密切相关。该假说提出，在大型随机初始化(Random Initialization)的神经网络内部，天然隐藏着更适合优化过程(Optimization Process)的稀疏子网络(Sparse Subnetworks)（即所谓的“中奖彩票(Winning Lottery Ticket)”）。引人注目的是，当一个稠密模型(Dense Model)被剪枝至原始规模的一小部分（例如保留 20%），并恢复至其初始随机权重进行重新训练(Retraining)时，其最终性能有时反而能获得更优的泛化能力(Generalization Ability)，甚至超越原始未剪枝网络。尽管剪枝的核心目标在于提升计算效率与缩减模型体积，而非单纯追求精度超越，但策略性地识别并重训这些关键子网络充分表明：稠密模型往往包含大量非必要冗余，而定向的稀疏化(Sparsification)策略能够更有效地释放模型的表征潜力。

## 激活感知剪枝(Activation-aware Pruning)：Wanda 方法
![关键帧](keyframes/part002_frame_00288560.jpg)
由卡内基梅隆大学(Carnegie Mellon University, CMU)近期提出的 Wanda(Weight and Activation)方法等研究，对传统基于幅值的剪枝(Magnitude-based Pruning)逻辑提出了有力挑战。传统方法通常假设权重(Weights)的重要性仅取决于其自身绝对值，却完全忽略了与之相乘的输入激活值(Input Activations)的幅值规模。 
![关键帧](keyframes/part002_frame_00329879.jpg)
Wanda 算法通过将权重幅值(Weight Magnitude)与对应输入激活范数(Input Activation Norm)相乘，以此作为评估权重重要性(Weight Importance)的新指标，从而有效弥补了这一评估盲区。 
![关键帧](keyframes/part002_frame_00351760.jpg)
这种激活感知策略(Activation-aware Strategy)的核心洞察在于：连接至高活跃神经元(Highly Active Neurons)的小权重，其对最终输出的贡献往往远大于连接至休眠神经元(Dormant Neurons)的大权重。通过充分考量输入激活规模的系统性差异，Wanda 实现了更优的稀疏化效果，且完全免去了传统剪枝中为恢复模型精度而必须执行的、计算成本高昂的重训练(Retraining)过程。
![关键帧](keyframes/part002_frame_00358679.jpg)
![关键帧](keyframes/part002_frame_00541600.jpg)