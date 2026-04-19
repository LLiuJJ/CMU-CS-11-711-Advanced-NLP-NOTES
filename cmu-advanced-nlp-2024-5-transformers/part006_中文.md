## 激活函数：从 ReLU 到 Swish/SiLU
讲座首先探讨了 Transformer 前馈网络(Feed-Forward Network)中使用的激活函数(Activation Function)。原始 Transformer 架构采用了线性整流单元(Rectified Linear Unit, ReLU)，其数学定义为 `max(0, x)`。 
![关键帧](keyframes/part006_frame_00000000.jpg)
尽管计算效率(Computational Efficiency)较高，但 ReLU 存在一个显著缺陷：它对所有负输入均输出零梯度(Zero Gradient)，这可能导致优化停滞并引发神经元“死亡”(Dying Neuron)现象。为解决该问题，Llama 等现代架构广泛采用了 Swish 或 SiLU(Sigmoid Linear Unit) 激活函数。 
![关键帧](keyframes/part006_frame_00068760.jpg)
Swish/SiLU 的数学表达式为 `x * sigmoid(βx)`（通常设 `β=1`）。在正值区间，其行为与 ReLU 高度相似；但在负值区间，它能保持平滑且非零的梯度(Smooth Non-zero Gradient)。这种柔和的曲率(Curvature)特性为反向传播提供了有益的“推力”，有效缓解了梯度消失、防止神经元死亡，并在实践中显著提升了大规模模型(Large-scale Models)的训练稳定性。

## 优化 Transformer 的固有挑战
尽管 Transformer 表征能力强大，但其优化过程(Optimization Process)众所周知地极不稳定且难以驾驭，尤其是在模型参数量(Parameters)膨胀至数千亿级别时。讲师引用了 Meta 公开的 1750 亿参数 OPT 模型训练日志(Training Logs)，将其作为剖析真实世界深度学习工程挑战的典型案例。 
![关键帧](keyframes/part006_frame_00093920.jpg)
即便拥有顶尖的工程团队，大模型训练(Large Model Training)仍频繁遭遇硬件故障(Hardware Failures)、损失函数突然发散(Loss Divergence)以及需要手动回滚检查点(Manual Checkpoint Rollback)等状况。深刻认识到优化不稳定性是超大规模训练生命周期中的常态而非偶然故障，凸显了部署稳健优化策略(Robust Optimization Strategies)与实施主动监控机制(Proactive Monitoring)的必要性。

## 学习率调度与 AdamW 优化器
早期 Transformer 训练主要依赖随机梯度下降(Stochastic Gradient Descent, SGD)或标准 Adam 等优化器(Optimizer)，并辅以精细调参的学习率调度策略(Learning Rate Scheduling)。2017 年的原始论文引入了线性预热(Linear Warmup)阶段（例如 4000 步），随后进行衰减(Decay)，以确保模型平稳度过早期的训练动态(Training Dynamics)阶段。 
![关键帧](keyframes/part006_frame_00161719.jpg)
尽管现代预归一化(Pre-Normalization)架构在一定程度上降低了对激进学习率预热(Aggressive Learning Rate Warmup)的依赖，但采用预热机制仍被业界视为训练最佳实践(Training Best Practice)。近年来，AdamW 优化器在很大程度上已取代标准 Adam，成为 Transformer 训练的主流选择。 
![关键帧](keyframes/part006_frame_00238920.jpg)
AdamW 的核心改进在于将权重衰减(Weight Decay)与梯度更新(Gradient Update)过程解耦(Decoupled)，使其作为独立的 L2 正则化项(L2 Regularization Term)直接作用于权重，从而避免了标准 Adam 中动量(Momentum)与自适应学习率(Adaptive Learning Rate)对正则化效果的干扰。这一数学修正显著增强了模型的泛化能力(Generalization Ability)，有效防止了权重数值异常膨胀，并大幅缓解了大规模训练中的过拟合(Overfitting)问题。

## 低精度训练：FP16 与 BF16
使用完整的 32 位浮点数(FP32)训练超大规模模型在计算算力(Compute Power)与显存占用(Memory Footprint)上均难以承受，这使得 16 位精度训练(16-bit Precision Training)成为现代深度学习工作流(Deep Learning Workflow)的标准配置。标准的半精度浮点数(FP16)仅分配 5 位给指数(Exponent)部分、10 位给尾数(Mantissa)部分，这严重限制了其动态范围(Dynamic Range)，导致模型在处理极端梯度值(Extreme Gradient Values)时容易出现数值溢出或下溢。 
![关键帧](keyframes/part006_frame_00340840.jpg)
为突破这一瓶颈，Google Brain 研发了 BF16(Brain Floating Point 16) 格式。BF16 重新分配了位宽(Bit-width)，采用 8 位指数与 7 位尾数的组合。 
![关键帧](keyframes/part006_frame_00371440.jpg)
该设计以轻微牺牲尾数精度(Precision)为代价，大幅扩展了可表示的数值范围(Numeric Range)，使其动态范围与 FP32 完全对齐。BF16 能够更稳健地处理梯度尖峰(Gradient Spikes)与微小权重更新(Weight Updates)，从而提供卓越的训练稳定性(Training Stability)，现已成为现代 GPU 加速训练(GPU-accelerated Training)工作流中强烈推荐的行业标准。

## 监控梯度范数与训练恢复
即便采用了最优的架构设计、优化器(Optimizer)与数据精度格式(Data Precision Format)，模型训练仍可能意外发散(Training Divergence)。一项至关重要的诊断工具是在训练全周期持续监控梯度范数(Gradient Norm)。 
![关键帧](keyframes/part006_frame_00462560.jpg)
正如 Meta 的 OPT 模型训练日志(Training Logs)所示，梯度范数的突然急剧飙升(Spike)通常是优化过程即将陷入不稳定(Instability)的早期预警信号。 
![关键帧](keyframes/part006_frame_00476880.jpg)
尽管在出现梯度尖峰后，损失函数(Loss Function)可能短暂地继续下降，但模型参数实际上已滑入退化的参数空间(Degraded Parameter Space)，且无法依靠自身优化轨迹恢复。标准的故障恢复流程(Fault Recovery Pipeline)通常包括：回滚(Rollback)至尖峰发生前保存的模型检查点(Model Checkpoint)，跳过或重新采样部分训练数据(Training Data)，随后恢复训练。这一操作为后续的梯度更新注入了有益的随机扰动(Stochastic Perturbation)，通常能在避免从头训练(Training from Scratch)的前提下重新稳定训练轨迹(Training Trajectory)。尽管现代自动化训练平台(Automated Training Platforms)已能越来越多地自动执行此类回滚与恢复逻辑，但开发人员仍须严格审查底层代码实现，以从根本上排查并修复潜在的工程缺陷(Engineering Bugs)。