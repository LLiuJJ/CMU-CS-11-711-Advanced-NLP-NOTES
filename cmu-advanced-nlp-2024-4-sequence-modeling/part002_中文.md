## 架构范式：循环、卷积与注意力
序列建模的基础建立在三种主要的条件预测(Conditional Prediction)策略之上。循环(Recurrence)机制通过逐步传递整个序列的累积历史信息来进行预测。卷积(Convolution)机制则基于围绕当前词元(Token)的固定局部上下文窗口进行预测。相比之下，注意力机制(Attention Mechanism)通过计算序列中所有词元的加权平均值，并依据学习到的相关性动态调整各词元对当前预测的影响权重。
![关键帧](keyframes/part002_frame_00000000.jpg)

## 计算复杂度与并行化的权衡
这些架构的计算成本随序列长度 $n$ 的变化呈现出不同的增长规律。循环神经网络(Recurrent Neural Network, RNN)表现出线性复杂度(Linear Complexity) $O(n)$，使其在理论上处理超长序列时极具效率。卷积的复杂度随窗口大小 $w$ 线性增长，为 $O(n \cdot w)$。由于注意力机制涉及所有词元之间的两两交互(Pairwise Interaction)，其复杂度呈二次方增长(Quadratic Complexity) $O(n^2)$。然而，渐近复杂度(Asymptotic Complexity)并不能完全反映实际性能：RNN 必须按顺序处理词元，无法实现并行计算(Parallelization)。相比之下，卷积和注意力机制具有高度的可并行性，尽管其理论复杂度较高，但在实际训练中往往能实现更快的速度。
![关键帧](keyframes/part002_frame_00217000.jpg)
![关键帧](keyframes/part002_frame_00224640.jpg)

## 循环神经网络工作机制
尽管基于注意力的模型占据主导地位，但这三种机制仍被广泛使用，且经常以混合架构(Hybrid Architecture)的形式结合。循环神经网络(RNN)于 1990 年左右提出，专为捕捉并维持序列记忆(Sequence Memory)而设计。与孤立处理输入的标准前馈网络(Feedforward Network)不同，Elman 式 RNN(Elman RNN)会将前一时刻的隐藏状态(Hidden State)纳入当前计算中。具体实现方式为：对前一隐藏状态进行线性变换(Linear Transformation)后，将其与当前输入的变换结果相加，最终通过 tanh 或 ReLU 等非线性激活函数(Non-linear Activation Function)进行处理。
![关键帧](keyframes/part002_frame_00325760.jpg)

## 跨时间步的参数共享
在序列处理过程中，RNN 从初始隐藏状态（通常初始化为零向量或通过学习获得）开始，并在每个时间步(Time Step)迭代应用完全相同的转换函数。RNN 的一个核心特征是参数共享(Parameter Sharing)：无论序列长度或词元处于何种位置，控制循环更新的权重矩阵始终保持一致。这种架构设计确保了模型拥有固定且有限的参数量，从而避免了因输入序列无限增长而导致的参数爆炸(Parameter Explosion)问题。

## 序列训练的损失聚合
在序列标注(Sequence Labeling)等任务上训练 RNN 时，需处理序列上的多个预测节点。在每个时间步，模型会生成一个概率分布(Probability Distribution)并计算独立损失（例如，基于真实标签的负对数似然损失(Negative Log-Likelihood Loss)）。为适配基于梯度的标准优化算法，这些逐时间步的损失会被累加为一个标量损失值(Scalar Loss)。这种聚合操作将顺序计算转化为具有统一终端节点的有效有向无环图(Directed Acyclic Graph, DAG)，从而使其能够无缝兼容标准的反向传播(Backpropagation)算法。
![关键帧](keyframes/part002_frame_00469520.jpg)

## 随时间反向传播（BPTT）
计算出总损失后，梯度将沿展开的计算图(Unrolled Computational Graph)反向流动。由于 RNN 的权重在所有时间步上共享，每个参数的梯度会从前向传播中应用该权重的所有位置进行累加。这一被称为沿时间反向传播(Backpropagation Through Time, BPTT)的过程，确保了共享参数的更新能够综合考量其对整个序列级损失(Sequence-level Loss)的累积贡献，而非仅依赖孤立时间步的误差。
![关键帧](keyframes/part002_frame_00499960.jpg)

## 双向 RNN 与计算扩展
对于需要完整上下文感知(Context Awareness)的任务（如序列标注），通常采用双向循环神经网络(Bidirectional RNN, BiRNN)。该架构并行运行两个独立的 RNN：一个沿正向处理序列，另一个沿反向处理序列。在每一步中，两个方向的隐藏状态会被拼接(Concatenation)，从而生成同时融合过去与未来词元信息的上下文表示。尽管该方法会使计算量与内存占用翻倍，但并未改变其渐近线性复杂度(Asymptotic Linear Complexity) $O(n)$，因为在大O符号(Big O Notation)中，常数因子会被忽略。
![关键帧](keyframes/part002_frame_00573640.jpg)

## 梯度消失挑战
标准 RNN 极易受到梯度消失(Gradient Vanishing)问题的影响，这一关键局限性深刻塑造了后续网络架构的设计。当梯度在连续多个时间步中反向传播时，会反复与小于 1 的权重相乘，导致其呈指数级衰减并趋近于零。这使得网络难以根据早期输入有效更新权重，严重阻碍了长距离依赖(Long-Distance Dependencies)的学习。深入理解这一现象，是掌握现代序列模型为何引入门控机制(Gating Mechanism)或转向注意力架构的关键前提。
![关键帧](keyframes/part002_frame_00598400.jpg)