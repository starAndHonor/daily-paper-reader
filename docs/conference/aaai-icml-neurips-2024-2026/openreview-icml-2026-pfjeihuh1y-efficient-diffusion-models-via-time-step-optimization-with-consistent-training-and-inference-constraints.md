---
title: Efficient Diffusion Models via Time Step Optimization with Consistent Training and Inference Constraints
title_zh: 通过一致性训练与推理约束的时间步优化实现高效扩散模型
authors: "Binrui Wu, Zihao Cheng, Yuesen Liao, WEIZHONG ZHANG"
date: 2026-04-30
pdf: "https://openreview.net/pdf/03f9fca13d6572d2de383ef43c135fc8dbd97666.pdf"
tags: ["query:diff-accel"]
score: 9.0
evidence: 通过时间步优化实现免训练扩散模型加速
tldr: 提出一种时间步优化方法，解决训练与推理之间的不一致性，无需重新训练即可改进免训练数值求解器，从而加速扩散模型。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散模型采样步骤多，现有训练-free求解器时间步选择次优。
method: 优化时间步选择，对齐前向扩散和反向去噪过程。
result: 在不训练的情况下提高采样效率，保持生成质量。
conclusion: 时间步优化是一种有效且免训练的加速手段。
---

## Abstract
Diffusion probabilistic models (DPMs)’ sampling process is often inefficient, requiring hundreds to thousands of iterative steps to accurately approximate the diffusion trajectory. This inefficiency limits their practical applicability. Although recent advances in sampling efficiency—such as numerical solvers for diffusion ordinary differential equations (ODEs)—have made progress, significant challenges remain: training-free numerical solvers suffer from the suboptimality of manually designed timestep selection rules and the inherent inconsistency between the forward diffusion process (typically involving thousands of steps) and the reverse denoising process (usually limited to tens of steps).
Since timestep selection is inherently a discrete problem and cannot be optimized via gradients, we propose an innovative approach—reparameterizing the timestep scheduling through probabilistic masking, thereby enabling gradient-based optimization of sampling timesteps. To circumvent backpropagation, we employ policy gradient methods. Furthermore, to address the inconsistency between forward diffusion (training) and reverse denoising (inference), we extend this framework into a bilevel optimization paradigm: the inner loop performs additional lightweight training on the model at specific timesteps determined by the outer mask to align forward and reverse processes, while the outer loop optimizes the timestep distribution via probabilistic masking and policy gradient based on generation quality.
Under mild assumptions, we theoretically analyze the convergence of the proposed algorithm. Extensive experiments across diverse datasets and samplers demonstrate that this framework effectively enhances sampling efficiency and generation quality while maintaining compatibility with various DPM architectures and advanced ODE solvers.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

扩散概率模型（DPMs）在生成质量上表现出色，但其采样过程需要数百到数千步迭代，严重限制了实际应用。近年来，虽然基于扩散常微分方程（ODE）的数值求解器在采样效率上取得进展，但**训练无关（training-free）的数值求解器仍面临两大挑战**：
- **时间步选择规则的次优性**：手动设计的时间步调度（timestep scheduling）没有针对特定模型和求解器进行优化；
- **前向扩散过程与反向去噪过程的不一致性**：训练时前向扩散通常使用数千步，而推理时反向去噪仅用数十步，导致分布偏移。

整体而言，论文瞄准**无需重新训练模型即可加速采样**这一关键目标，试图通过优化时间步选择来弥合训练与推理之间的鸿沟，提升免训练求解器的效率。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

**核心思想**：将时间步选择视为离散优化问题，并引入概率掩码（probabilistic masking）重参数化时间步调度，使得原本不可导的离散选择可通过梯度进行优化。同时，设计双层优化框架同时解决不一致性问题。

**关键技术细节**：
- **重参数化与策略梯度**：将每个采样时间步的选择建模为可学习的概率分布（掩码），通过策略梯度（如REINFORCE）进行优化，避免对离散采样过程进行反向传播。
- **双层优化范式（Bilevel Optimization）**：
    - **内层循环**：根据外层掩码确定的时间步，对模型进行轻量级微调（额外训练），以对齐前向扩散和反向去噪过程；
    - **外层循环**：基于生成质量（如FID、IS等）作为奖励，使用策略梯度优化时间步的概率掩码。
- **理论分析**：在温和假设下，论文证明了所提算法的收敛性。

**算法流程（文字描述）**：
1. 初始化时间步掩码分布（如均匀分布）；
2. 重复以下步骤直至收敛：
   - 从当前掩码分布中采样一组时间步；
   - **内循环**：固定掩码，在采样得到的时间步上对预训练扩散模型进行少量训练（如几步梯度更新），以减小前向与反向过程的差异；
   - **外循环**：使用微调后的模型进行采样，计算生成质量指标（如FID），用策略梯度更新掩码分布参数；
3. 得到优化后的时间步调度，直接用于任意免训练求解器。

## 3. 实验设计：使用了哪些数据集 / 场景，它的 benchmark 是什么，对比了哪些方法

根据摘要，实验在**多种数据集和多种采样器**上进行，具体包括：
- **数据集**：摘要未明确列举，但常见扩散模型基准数据集包括CIFAR-10、ImageNet、LSUN等；推测作者使用了至少两个标准图像生成数据集。
- **Benchmark**：以生成质量（如FID、Inception Score）和采样步骤数（NFE，即函数评估次数）作为主要评估指标。
- **对比方法**：与多种**免训练ODE求解器**（如DDIM、DPM-Solver、DEIS等）以及**默认的时间步调度**（如均匀时间步、手动设计的调度）进行对比。同时可能对比了其他免训练加速方法（如渐进蒸馏、步长调整）。

由于摘要未提供详细实验表，具体对比方法数量与名称无法确认。

## 4. 资源与算力

论文摘要与元数据中**未明确说明**所使用的GPU型号、数量、训练时长等算力信息。仅可以推断：内循环的轻量训练需要额外计算，但总体开销应远小于从头训练扩散模型。外层策略梯度更新仅需采样和指标计算，计算量较低。因此，该方法的算力需求相对适中。

## 5. 实验数量与充分性

论文声称进行了“extensive experiments across diverse datasets and samplers”，但摘要中未给出具体实验组数或消融实验数量。从常见ICML论文惯例推测，可能包含：
- 至少2~3个数据集上的主实验；
- 与多种基线求解器的对比（如DDIM、DPM-Solver、DEIS等）；
- 消融实验：验证概率掩码 vs. 固定调度、内循环训练的必要性、策略梯度 vs. 其他优化方法等。

**充分性评估**：由于缺少具体数字，无法做出准确判断。但从“extensive”表述及被ICML 2026接收（且得分9.0）来看，实验设计应当较为充分且公平，覆盖了主要场景和对比基线。

## 6. 论文的主要结论与发现

- 所提出的时间步优化框架能显著提升免训练数值求解器的采样效率，在不额外训练模型参数的前提下（仅微调少量步骤）实现更高质量的生成。
- 通过概率掩码重参数化，成功将离散时间步选择转化为可梯度优化的问题。
- 双层优化有效缓解了前向-反向不一致性，进一步提升了生成质量。
- 该框架与多种DPM架构和高级ODE求解器兼容，具有通用性。
- 理论上证明了算法在合理假设下的收敛性。

## 7. 优点

- **无需重新训练完整模型**：仅优化时间步调度或对模型进行少量微调，计算开销小，易于在已有模型上部署。
- **创新性地将离散优化连续化**：概率掩码+策略梯度解决了时间步选择不可导的难题，思路巧妙。
- **从根本上解决训练-推理不一致问题**：通过内循环对齐，使得免训练求解器能获得更好的初始点或调度。
- **理论收敛保证**：增强了方法的可信度。
- **通用性强**：不依赖特定模型或求解器，容易集成到现有流水线。

## 8. 不足与局限

- **实验细节缺失**：摘要未报告具体数据集、对比方法数量、FID等数值结果，无法独立验证其性能增益幅度。
- **算力消耗未明确**：虽然声称“轻量训练”，但未给出内循环训练的实际步数与耗时，可能在实际部署中仍需一定计算资源。
- **可能的风险**：策略梯度优化可能存在方差大、收敛慢等问题，论文虽证明了收敛性，但未讨论稳定性或对超参数的敏感性。
- **应用限制**：只能优化免训练求解器的时间步，对于需要重新训练蒸馏模型的方法（如渐进蒸馏）并不直接适用；且内循环微调可能影响原始模型的泛化能力。
- **评估覆盖不足**：仅从摘要看，未提及对高分辨率、视频、文本等模态的扩展实验，应用场景可能局限于图像生成。

（完）
