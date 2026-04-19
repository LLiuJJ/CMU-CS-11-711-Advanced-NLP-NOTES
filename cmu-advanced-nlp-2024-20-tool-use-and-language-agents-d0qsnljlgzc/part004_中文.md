## 具身任务中的观测-行动循环(Observation-Action Loop)
语言模型智能体(Language Model Agents)的基础范式围绕环境感知与交互的持续循环展开。在具身仿真任务(Embodied Simulation Tasks)中，智能体会接收描述当前周围环境的文本或视觉观测信息(Observations)（例如识别家具或空间布局）。基于这些输入，智能体预测并执行特定动作(Actions)以推进任务目标。该动作使智能体状态发生转移，进而触发新一轮的观测，从而形成持续迭代的决策过程(Decision-making Process)，这是在复杂环境中实现有效导航的必备机制。
![关键帧](keyframes/part004_frame_00000000.jpg)

## 智能体在游戏与仿真中的应用
该交互框架已成功应用于各类游戏与虚拟仿真基准测试(Virtual Simulation Benchmarks)中。以 MineDojo 平台为例，它旨在训练智能体理解自然语言指令(Natural Language Instructions)，并在《我的世界》(Minecraft) 环境中自主执行复杂的建造或生存任务。类似地，DeepMind 的研究展示了基于文本的提示(Text-based Prompts)如何直接驱动游戏机制(Game Mechanics)，例如指挥智能体瞄准并射击特定小行星。此类仿真环境为评估智能体将抽象语言指令转化为精确、有序的游戏内操作(In-game Operations)的能力，提供了高度可靠的测试平台。
![关键帧](keyframes/part004_frame_00036520.jpg)
![关键帧](keyframes/part004_frame_00055200.jpg)

## 软件开发与 UI 自动化中的 AI 智能体
突破虚拟世界的界限，自主智能体(Autonomous Agents)正逐步重塑软件工程与桌面交互模式。以 Devin 为代表的 AI 工具通过与代码编辑器(Code Editors)、命令行终端(Command-line Terminals)及网页浏览器深度集成，扮演着“虚拟软件工程师(Virtual Software Engineer)”的角色。当接收到高层级(High-level)的自然语言提示时，这些智能体能够自主拆解需求、执行终端命令、检索在线技术文档，并直接生成可执行代码。该应用范式同样延伸至通用图形用户界面(Graphical User Interface, GUI)自动化领域，智能体仅凭自然语言对话指令即可实现网页浏览、桌面应用程序操控以及多媒体任务管理。
![关键帧](keyframes/part004_frame_00069320.jpg)
![关键帧](keyframes/part004_frame_00130879.jpg)

## 智能体感知的多模态输入
为在高度多样化的环境中高效运行，智能体必须具备处理多模态输入(Multimodal Inputs)的能力。文本观测信息用于提供环境状态的上下文概述，而截图或实时游戏画面等视觉输入(Visual Inputs)则赋予智能体直接的空间感知(Spatial Perception)能力。音频输入(Audio Inputs)对于捕捉屏幕外事件或不可见的环境线索同样至关重要。此外，结构化数据输入(Structured Data Inputs)（如 HTML 文档对象模型(Document Object Model, DOM)树或应用程序界面布局代码)能够实现对网页及桌面界面的精准导航(Precise Navigation)。这种多模态处理需求凸显了先进多模态大语言模型(Multimodal Large Language Models, MLLMs)作为现代智能体底层架构的核心价值。
![关键帧](keyframes/part004_frame_00146519.jpg)
![关键帧](keyframes/part004_frame_00218679.jpg)
![关键帧](keyframes/part004_frame_00229279.jpg)
![关键帧](keyframes/part004_frame_00241439.jpg)
![关键帧](keyframes/part004_frame_00278680.jpg)

## 推理、行动与 ReAct 框架
将静态语言模型转化为动态智能体，关键在于实现高层规划(High-level Planning)与底层执行(Low-level Execution)的有效衔接。借助思维链(Chain-of-Thought, CoT)提示等技术，模型能够将复杂任务拆解为一系列逻辑严密的子步骤。例如，针对“将胡椒罐放置在抽屉上”的指令，模型会首先推理出在执行放置动作前必须先定位胡椒罐的具体位置。然而，脱离环境交互的纯文本推理往往存在局限。**ReAct 框架(Reasoning and Acting Framework)** 通过将推理(Reasoning)与行动(Acting)交替循环来解决这一问题：模型首先生成推理轨迹(Reasoning Traces)，随后输出具体的动作调用(Action Calls)（例如 `go to cabinet one`）；在执行动作并观察环境状态更新后（例如发现柜中是面包而非胡椒罐），模型会持续迭代此“推理-行动-观察”循环，直至最终达成任务目标。

## 通过免训练提示实现可执行动作
生成准确且可执行的工具调用(Tool Calls)并非必须依赖模型微调(Model Fine-tuning)，完全可以通过精心设计的上下文提示(Contextual Prompting)来实现。通过向模型提供标准的 API 库(API Libraries)（如天气、地理位置或交通数据接口）以及类似“今天适合去徒步吗？”的自然语言查询(Natural Language Queries)，模型能够自主推理并识别所需填补的信息缺口。它会精准判断对位置与天气上下文的需求，生成规范的 API 调用指令，解析返回的响应数据（例如“西雅图多云”），并最终综合输出具备上下文感知能力(Context-awareness)的行动建议。这一过程充分证明，在提供精确的接口文档与演示示例的前提下，语言模型能够无缝地将用户意图转化为经逻辑验证、可直接执行的程序化操作(Programmatic Operations)。
![关键帧](keyframes/part004_frame_00514760.jpg)