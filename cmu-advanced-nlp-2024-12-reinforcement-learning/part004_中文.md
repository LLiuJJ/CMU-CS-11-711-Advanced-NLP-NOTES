## 文本生成中的可微性与组合挑战
模型本质上是可微的(Differentiable)，这使得基于梯度的优化(Gradient-based Optimization)变得直接明了。然而，将其直接应用于文本生成会带来巨大的计算障碍：对输出空间中的所有潜在序列进行求和在数学上是难解的(Intractable)。例如，当序列长度(Sequence Length)仅为 50、词表大小(Vocabulary Size)为 30,000 时，模型将面临 $30,000^{50}$ 种可能的组合，这使得在整个输出空间上计算精确的数学期望变得不可能。

![关键帧](keyframes/part004_frame_00000000.jpg)

## 最小风险训练及其与强化学习的联系
最小风险训练(Minimum Risk Training)被引入为一个旨在显式最小化期望风险(Expected Risk)的框架。这一概念与强化学习(Reinforcement Learning)天然契合，因为许多现代强化学习方法（尤其是策略梯度方法(Policy Gradient)）都共享相同的最小化风险目标。在探讨当今更为复杂的强化学习算法之前，首先从风险最小化的角度切入，能够提供一个逻辑清晰且易于理解的基础。

## 使用采样技术近似风险
为使风险优化在计算上可行，采样(Sampling)被用作一种实用的近似手段。模型不再对所有可能的输出进行求和，而是从假设空间中抽取一小部分子集，将其概率重新归一化，并基于这个规模可控的集合进行优化。尽管祖先采样(Ancestral Sampling)在理论上成立，但将采样温度(Sampling Temperature)设为 1 通常会导致模型探索大量低质量输出区域，从而降低生成质量。因此，从业者通常倾向于使用集束搜索(Beam Search)或调整温度参数来生成更高质量的候选序列。关键在于，采样过程必须进行去重或采用无放回抽样(Sampling without replacement)，以避免重复输出干扰概率归一化。

![关键帧](keyframes/part004_frame_00217359.jpg)

## 面向自然语言处理的强化学习定义
强化学习(Reinforcement Learning)的运行依赖于一个环境(Environment)，智能体(Agent)在其中观察状态(State, $X$)，采取动作(Action, $A$)，并获得延迟奖励(Delayed Reward, $R$)。以经典的 Pong 游戏为例：$X$ 为屏幕图像，$A$ 代表控制球拍向上或向下移动，$R$ 则是最终的胜负信号。在自然语言处理(Natural Language Processing)的语境下，该框架可被直观地映射：$X$ 转化为对话历史或输入上下文，$A$ 对应下一个词元(Token)的生成，而 $R$ 则是一个反馈信号，它通常在序列生成的后期才会出现，而非在每个词元生成后立即给出。

![关键帧](keyframes/part004_frame_00318440.jpg)

## 实际应用：上下文、动作与奖励
将该框架应用于代码生成(Code Generation)等任务时，充分凸显了强化学习奖励设计的灵活性。在此场景下，$X$ 涵盖编译器环境与周围的代码上下文，$A$ 仍为每个代码词元(Code Token)的生成，而 $R$ 则可来自多个维度：代码是否编译成功、可读性评分或执行效率。这种灵活的奖励设计使模型能够直接针对实际效用和特定质量指标进行优化，展现出标准最大似然估计(Maximum Likelihood Estimation, MLE)难以企及的优势。

![关键帧](keyframes/part004_frame_00418840.jpg)

## 自然语言处理中强化学习的三个核心场景
在自然语言处理中，强化学习在以下三个主要场景中极具价值：
1. **延迟的人类反馈(Delayed Human Feedback)：** 对话系统(Conversational Systems)天然契合强化学习，因为奖励信号（如用户的点赞/点踩或呼叫中心任务是否成功达成）是在多轮交互结束后才获得的，而非针对每个词元实时给出。
2. **隐变量(Latent Variables)与思维链(Chain-of-Thought)：** 在思维链提示(Chain-of-Thought Prompting)中，中间的推理步骤属于隐变量。由于最终答案正确并不代表推理过程严谨，强化学习可基于最终的任务表现，反向优化底层的推理逻辑。
3. **序列级评估指标(Sequence-level Evaluation Metrics)：** 许多标准的 NLP 评估指标要求先生成完整的输出序列后才能计算得分。在缺乏强化学习的延迟奖励结构时，传统的词元级监督(Token-level Supervision)往往难以直接优化这些序列级目标。

![关键帧](keyframes/part004_frame_00587080.jpg)

## 将强化学习与监督 MLE 损失相衔接
在明确了强化学习在自然语言处理中的应用动机与结构映射后，论述回归至基础概念。标准的监督最大似然估计(Supervised Maximum Likelihood Estimation)损失被重新审视，并在强化学习范式下进行了形式化重构，这为后续推导策略梯度(Policy Gradient)优化算法奠定了理论基础。