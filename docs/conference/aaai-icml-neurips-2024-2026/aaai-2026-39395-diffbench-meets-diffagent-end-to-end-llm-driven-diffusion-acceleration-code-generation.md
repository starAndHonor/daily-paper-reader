---
title: "DiffBench Meets DiffAgent: End-to-End LLM-Driven Diffusion Acceleration Code Generation"
title_zh: DiffBench与DiffAgent：端到端LLM驱动的扩散加速代码生成
authors: "Jiajun Jiao, Haowei Zhu, Puyuan Yang, Jianghui Wang, Ji Liu, Ziqiong Liu, Dong Li, Yuejian Fang, Jun-Hai Yong, Bin Wang, Emad Barsoum"
date: 2026-03-17
pdf: "https://ojs.aaai.org/index.php/AAAI/article/download/39395/43356"
tags: ["query:diff-accel"]
score: 4.0
evidence: 基于LLM的扩散模型加速代码自动生成框架
tldr: 扩散模型加速方法多样，但组合困难。本文提出DiffBench基准和DiffAgent框架，利用大型语言模型自动生成加速代码并评估。系统覆盖多种扩散架构和优化组合，实现端到端自动化。虽然不直接加速推理，但为加速技术集成提供了工具。该方法有助于加速研究但本身不提供新加速算法。
source: AAAI-2026-Accepted
selection_source: conference_retrieval
figures_json: "[{\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-39395/fig-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 1567, \"height\": 509, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-39395/fig-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 850, \"height\": 409, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-39395/fig-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 880, \"height\": 376, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-39395/fig-004.webp\", \"caption\": \"\", \"page\": 0, \"index\": 4, \"width\": 868, \"height\": 439, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-39395/fig-005.webp\", \"caption\": \"\", \"page\": 0, \"index\": 5, \"width\": 861, \"height\": 547, \"label\": \"Figure\"}]"
tables_json: "[{\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-39395/table-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 1837, \"height\": 340, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-39395/table-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 1717, \"height\": 318, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-39395/table-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 853, \"height\": 261, \"label\": \"Table\"}]"
motivation: 组合多种加速技术存在巨大挑战。
method: 构建DiffBench基准和LLM驱动DiffAgent，自动生成并评估加速代码。
result: 实现了自动化的加速代码生成与评估流程。
conclusion: 该框架辅助加速技术集成，但本身不加速推理。
---

## Abstract
Diffusion models have achieved remarkable success in image and video generation. However, their inherently multiple step inference process imposes substantial computational overhead, hindering real-world deployment. Accelerating diffusion models is therefore essential, yet determining how to combine multiple model acceleration techniques remains a significant challenge. To address this issue, we introduce a framework driven by large language models (LLMs) for automated acceleration code generation and evaluation. First, we present DiffBench, a comprehensive benchmark that implements a three stage automated evaluation pipeline across diverse diffusion architectures, optimization combinations and deployment scenarios. Second, we propose DiffAgent, an agent that generates optimal acceleration strategies and codes for arbitrary diffusion models. DiffAgent employs a closed-loop workflow in which a planning component and a debugging component iteratively refine the output of a code generation component, while a genetic algorithm extracts performance feedback from the execution environment to guide subsequent code refinements. We provide a detailed explanation of the DiffBench construction and the design principles underlying DiffAgent. Extensive experiments show that DiffBench offers a thorough evaluation of generated codes and that DiffAgent significantly outperforms existing LLMs in producing effective diffusion acceleration strategies.

---

## 论文详细总结（自动生成）

# DiffBench Meets DiffAgent: 端到端LLM驱动的扩散加速代码生成 — 论文详细总结

## 1. 核心问题与整体含义（研究动机和背景）

- **问题背景**：扩散模型（如SD1.5、SDXL、DiT等）在图像和视频生成中取得了巨大成功，但其多步推理过程导致计算开销和延迟巨大，阻碍了实际部署。
- **核心挑战**：虽然已有多种加速技术（如快速采样器、特征复用、Token合并、半精度计算等），但如何将这些技术组合起来，针对特定模型架构和部署场景自动生成正确且高效的加速代码，仍然需要专家经验与大量手工调参。
- **研究空白**：现有LLM在代码生成方面表现出色，但面对扩散加速代码的生成——需要深度融合领域知识、参数调优和环境反馈——现有模型能力不足。缺乏专门针对此任务的基准和自动化框架。
- **整体含义**：本文提出首个用于LLM生成扩散加速代码的基准DiffBench，以及基于LLM的智能体框架DiffAgent，实现从用户需求到优化代码的端到端自动化，填补了该领域工具和评估的空白。

## 2. 方法论

### 核心思想
- 构建一个闭环工作流：**规划 → 编码 → 调试 → 评估 → 遗传优化**，让LLM在环境反馈的指导下迭代生成和优化扩散加速代码。
- 将扩散加速代码生成形式化为一个多级任务，难度逐级递增，覆盖基础流水线、单方法加速、组合加速、带显式速度目标、带延迟约束等场景。

### 关键技术细节

#### (1) DiffBench：评估基准
- **任务格式**：输入为用户提示（指定模型、采样步数、加速方法、目标加速比、硬件平台） + 参考代码（模型定义、推理流程、预处理/后处理）。输出为满足要求的加速推理实现。
- **五级难度**：
  - Level 1（41个任务）：基础流水线生成，无加速。
  - Level 2（116个任务）：单方法加速。
  - Level 3（261个任务）：组合加速（如快速采样器+特征复用）。
  - Level 4（93个任务）：显式速度目标（最小加速比 + 质量损失≤5%）。
  - Level 5（93个任务）：延迟约束（在指定硬件上最大允许延迟）。
- **构建方法**：从真实部署场景中收集604个任务，覆盖U-Net（SD1.5, SD2.1, SDXL）和Transformer（DiT, PixArt-α/Σ）架构；支持文本到图像、类到图像、图像到图像条件；分辨率256×256到1024×1024；加速方法包括DDIM、DPM-Solver、UniPC采样器，以及Token Merging（ToMe）、特征复用（DeepCache）、门控激活（T-Gate）、半精度（FP16）。对于Level 4/5，先搜索最优配置（50轮，36样本验证）生成“medium”样本，再按比例缩放得到“easy”和“hard”样本。
- **评估三阶段流水线**：
  1. **静态参数检查**：验证关键属性（pipeline类、模型ID、调度器、步数、加速方法等）与ground truth一致。
  2. **绝对性能测量**：在10个样本上计算CLIP-Score，低于阈值δ（5%）的失败。
  3. **相对性能分析**：计算质量损失L和加速比U（Level 4）或原始延迟τ（Level 5）。定义通过率Sp（top-1正确概率）和成就率Sa（实际加速比/需求加速比的最小值）。

#### (2) DiffAgent：代码生成与优化智能体
- **四个核心组件**：
  - **规划代理（Planning Agent）**：根据用户提示生成编码计划。对优化任务，先生成基准计划，再生成条件加速计划（选择、排序、参数化）。在迭代中，接收M个有前途的子代的计划、反馈报告和先前调优经验，同时生成P-M个全新计划（默认P=7, M=4）。
  - **编码代理（Coding Agent）**：根据计划生成扩散推理代码。利用参考代码模板（从现有库编译）辅助生成。
  - **调试代理（Debugging Agent）**：基于Reflexion架构进行纠错。最多尝试Tdebug=3次，失败则返回编码代理重新生成（最多Tcode=5次），再失败则回退到规划代理（绿色箭头）。
  - **基于遗传算法的选择器（Genetic Algorithm Based Selector）**：计算每个实现的质量损失和加速比，加权得到适应度分数。若满足用户要求则终止；否则归一化适应度分数作为抽样概率，选择M个有前途的子代，生成反馈报告，传递给规划代理进行下一轮。最多迭代Tsel=5次。

### 算法流程（文字说明）
1. 用户输入任务（提示+参考代码）→ 规划代理生成基准计划和加速计划（P个）。
2. 编码代理根据每个计划生成基准代码和加速代码（P个变体）。
3. 调试代理验证代码可运行性，最多循环Tcode×Tdebug次。
4. 遗传选择器在实际环境中运行代码，计算适应度分数，检查是否满足目标。
5. 若满足，返回代码；否则，选择M个子代，生成反馈，规划代理结合反馈和之前的经验生成新计划（加上P-M个全新计划），重复步骤2-5最多Tsel次。

## 3. 实验设计

### 数据集/场景
- **基准**：DiffBench（604个任务，5个难度级别），使用COCO数据集（10个样本用于绝对性能测量）。
- **硬件平台**：未明确指定具体GPU型号，但Level 5要求“在指定硬件平台上的最大延迟”。
- **评估指标**：通过率Sp（所有任务）和成就率Sa（hard任务）。

### 对比方法
- **基线LLM**：GPT-4.1、Claude Sonnet 4、Gemini 2.5 Flash、o3-mini，直接用于代码生成（无额外定制）。
- **本文方法**：上述LLM + DiffAgent框架。

### 主要实验
1. **基线评估**：四个LLM在DiffBench上的原始性能（Table 1）。
2. **错误模式分析**：将失败归为五类（编译错误、关键属性错误、绝对质量错误、相对质量错误、相对速度错误），并对比DiffAgent前后的变化（Figure 4）。
3. **DiffAgent性能**：四个LLM集成DiffAgent后的通过率Sp（Table 1），以及增益百分比。
4. **消融实验**：去除知识库（Knowledge Base）、遗传算法（GA）、调试代理（Debugging Agent）对Sp和Sa的影响（Table 2）。
5. **遗传算法超参数分析**：不同人口大小P和迭代次数Tsel对Sp和Sa的影响（Table 3）。
6. **定性分析**：展示代码生成示例（Figure 5），对比原始LLM输出和DiffAgent修正后的代码。

## 4. 资源与算力

- **文中未明确说明**使用的GPU型号、数量、训练时长等算力信息。仅在Level 5任务中提到了“在指定硬件平台上测量延迟”，但未给出具体硬件规格。超参数实验中Tsel和P的选择是基于平衡效果和运行时间，但未报告实际运行时间。

## 5. 实验数量与充分性

- **实验数量**：
  - 主实验：4个基础LLM × 5个难度级别，共20组通过率数据。
  - 错误分析：对Claude Sonnet 4进行了5种错误类别 × 5个级别的详细分析。
  - 消融实验：3个组件（知识库、GA、调试代理）的leave-one-out实验，报告Sp和Sa。
  - 超参数分析：2个因素（P=4,7,10；Tsel=2,4,6），共9组结果。
  - 定性案例：1个示例。
- **充分性评估**：
  - 覆盖了主流的扩散架构和加速方法，任务数量适中（604个）。
  - 消融实验验证了每个组件的重要性。
  - 但缺乏跨硬件平台的比较（如不同GPU型号），也缺乏与其他自动化代码生成工具（如AutoTune、KernelBench等）的对比，仅在LLM级别比较。此外，未报告统计显著性检验。

## 6. 主要结论与发现

1. **现有LLM在扩散加速代码生成上表现不佳**：即使最强的Claude Sonnet 4在DiffBench上的平均通过率也仅为54.30%（Level 1~5平均），在Level 4/5上跌至5%-8%左右。
2. **DiffAgent显著提升性能**：集成DiffAgent后，所有LLM的平均通过率提升54.30%至81.59%（Claude Sonnet 4提升最大）。在复杂任务（Level 4/5）上，相对质量错误和相对速度错误大幅减少。
3. **遗传算法是复杂任务的关键**：去除GA后，Level 4/5的通过率几乎归零（4.30%），说明参数优化依赖环境反馈驱动的迭代。
4. **知识库和调试代理均贡献显著**：去除知识库导致平均通过率下降16.69%；去除调试代理使Level 5通过率降低约30%。
5. **超参数设置**：P=7、Tsel=4为最佳平衡点。

## 7. 优点

1. **问题定义清晰**：首次将扩散加速代码生成形式化为多级任务，并给出严格评估协议。
2. **基准构建严谨**：DiffBench覆盖多种架构、加速方法、条件类型和分辨率，并包含静态、绝对、相对三级评估，避免单一指标偏差。
3. **端到端自动化框架**：DiffAgent将LLM的代码生成能力与环境反馈（遗传算法）相结合，实现了无需人工干预的自动优化。
4. **模块化设计**：规划、编码、调试、选择器可独立替换或扩展，便于未来集成新加速技术或LLM。
5. **实验充分**：包含基线对比、错误分析、消融、超参数分析、定性案例，证明了每个组件的有效性。
6. **代码开源意图**：虽未明确链接，但论文暗示了可扩展性。

## 8. 不足与局限

1. **算力与实验可重复性**：未报告具体GPU型号、运行时间、能耗等，其他研究者难以复现或对比效率。
2. **缺乏与现有工具的直接对比**：仅比较了不同LLM，未与传统的自动调优工具（如Optuna、Bayesian优化）或针对GPGPU的代码生成框架（如Triton、TensorRT）进行对比。
3. **评估指标局限性**：仅使用CLIP-Score作为质量度量，可能无法全面反映生成图像的感知质量；加速比仅基于推理时间，未考虑内存占用、功耗等其他资源。
4. **基准的覆盖范围**：未包含最新的扩散模型（如Flux、Sora等）或更复杂的条件（如视频、3D生成）。加速方法也限于少数几种，未涉及量化、蒸馏、模型剪枝等。
5. **泛化性风险**：所有实验基于公开库参考代码，若用户需求超出预设范围（如自定义加速方法组合），DiffAgent可能失效。遗传算法的搜索空间依赖初始计划质量，可能存在局部最优。
6. **LLM的失败模式未完全解决**：即使使用DiffAgent，Level 4/5的通过率仍只有33%-63%，仍有大量任务无法满足精确的加速目标。
7. **假设用户提供参考代码和明确需求**：实际开发中，用户可能只有模糊的需求，需要更智能的需求理解。

（完）
