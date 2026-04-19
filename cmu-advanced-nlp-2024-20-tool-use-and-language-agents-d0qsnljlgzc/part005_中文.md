## 管理大型 API 集合与上下文约束
在促使语言模型生成可执行动作时，结合少样本示例(Few-Shot Examples)与详尽 API 文档的提示工程(Prompt Engineering)是一项基础方法。然而，当面对数以千计的潜在 API 时，系统会面临显著的扩展性(Scalability)挑战。将所有函数定义直接嵌入提示词中极易突破上下文窗口限制(Context Window Limits)，并大幅增加 Token 成本(Token Costs)。为应对这一难题，研究人员主张引入外部记忆系统(External Memory Systems)。通过基于当前任务上下文动态检索数据库，系统能够精准获取并注入最相关的 API 定义，从而在控制提示词长度的同时显著提升计算效率。
![关键帧](keyframes/part005_frame_00000000.jpg)
![关键帧](keyframes/part005_frame_00018799.jpg)

## 直接代码生成与自然语言轨迹
相较于生成自然语言推理轨迹(Natural Language Reasoning Traces)，一种更为稳健的范式是直接提示(Prompting)模型编写可执行代码(Executable Code)（例如 Python 脚本）。该方法将推理(Reasoning)、规划(Planning)与动作执行(Action Execution)无缝整合至统一的流程中。它不再要求模型手动将抽象思维转化为离散的 API 调用，而是生成一个高度内聚的程序，直接调用预置库并在内部处理复杂逻辑。现代 AI 平台通常内置代码解释器(Code Interpreter)以安全运行此类脚本，借助标准编程结构，其控制流(Control Flow)的管理远比单纯依赖提示推理更为可靠。
![关键帧](keyframes/part005_frame_00128000.jpg)
![关键帧](keyframes/part005_frame_00150119.jpg)

## 评估语言模型智能体的挑战
从模型构建转向评估阶段，我们发现评估语言模型智能体(Language Model Agents)依然极具挑战。许多现有基准测试(Benchmarks)依赖于过度简化的环境与初级任务，致使模型性能迅速达到饱和(Performance Saturation)。对于诸如查询天气或调用标准 API 安排会议等直白请求，当代模型已能以近乎完美的准确率(Accuracy)轻松应对。当基准测试仅聚焦于此类基础能力时，追求 100% 的准确率对学术研究而言已呈现边际收益递减(Diminishing Marginal Returns)，这不仅难以有效区分模型间的性能差异，更无法推动高级智能体(Agent)研发的实质性突破。
![关键帧](keyframes/part005_frame_00189320.jpg)

## 无状态基准数据集的局限性
以 Mind2Web 为代表的主流评估框架普遍采用无状态(Stateless)且非交互(Non-interactive)的环境设定。此类基准测试通常通过将智能体预测的动作序列与固定的真实参考序列(Ground-truth Sequences)进行严格比对来评估性能，强制要求动作与参数必须完全一致。针对复杂的现实任务，该评估方法存在根本性缺陷。在实际应用中，许多任务目标完全可通过并行步骤或多条替代路径达成。若对偏离单一预设路径的操作施以严苛惩罚，评估系统将不可避免地忽略那些仅执行顺序不同但功能正确(Functionally Correct)的有效解法，从而严重低估智能体的真实泛化与执行能力。
![关键帧](keyframes/part005_frame_00307799.jpg)

## 短周期交互环境与性能饱和
为突破无状态环境的局限，部分基准测试引入了交互式网页环境(Interactive Web Environments)，但这些评估往往仍局限于短周期任务(Short-Horizon Tasks)。以 WebShop 为例，该平台模拟了一个简化版的电子商务网站，要求智能体完成特定的商品检索与购买流程。尽管任务涵盖页面导航、关键词搜索与条件筛选，但通常仅需寥寥数步即可完成。因此，即便是未经专门提示或微调(Fine-tuning)的通用大模型，也能在此类环境中取得近乎完美的任务成功率。这种迅速的性能饱和(Performance Saturation)表明，低复杂度、短周期的测试环境已无法作为有效的压力测试，难以客观衡量具备高级规划与长程交互能力(Long-Horizon Interaction Capabilities)的复杂智能体。
![关键帧](keyframes/part005_frame_00443159.jpg)
![关键帧](keyframes/part005_frame_00489200.jpg)
![关键帧](keyframes/part005_frame_00560680.jpg)

## 下一代智能体基准测试的要求
构建稳健的评估框架(Evaluation Frameworks)亟需转向更为严苛且贴近真实应用场景的标准。未来的基准测试必须构建完全交互的环境(Fully Interactive Environments)，重点考核实际的执行结果与最终任务达成率，而非仅仅校验中间步骤的动作标签。此外，测试场景需涵盖跨领域的高度多样化功能，严防模型在在线购物等狭窄、重复性任务上产生过拟合(Overfitting)。尤为关键的是，新一代基准测试必须引入长周期任务(Long-Horizon Tasks)与动态约束条件(Dynamic Constraints)，从而迫使智能体在冗长的交互序列中充分展现其持续规划、错误恢复(Error Recovery)以及复杂问题求解的综合性能力。
![关键帧](keyframes/part005_frame_00570120.jpg)
![关键帧](keyframes/part005_frame_00589520.jpg)