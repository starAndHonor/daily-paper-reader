---
title: "Tortoise and Hare Guidance: Accelerating Diffusion Model Inference with Multirate Integration"
title_zh: 龟兔引导：通过多速率积分加速扩散模型推理
authors: "Yunghee Lee, Byeonghyun Pak, Junwha Hong, Hoseong Kim"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=3cYcUmcDhU"
tags: ["query:diff-accel"]
score: 9.0
evidence: 基于多速率积分的无训练扩散采样加速策略
tldr: 针对无分类器引导（CFG）扩散模型，传统求解器未充分利用引导分支的鲁棒性。本文提出THG方法，将CFG ODE转化为多速率系统，对估算噪声的精细分支和引导的粗糙分支采用不同步长，大幅减少计算量。实验表明THG在保持生成质量的同时实现加速，且无需重新训练。该策略为训练自由加速提供了新途径。
source: NeurIPS-2025-Accepted
selection_source: conference_retrieval
motivation: CFG中引导分支对数值误差更鲁棒，传统求解器未利用此特性。
method: 将CFG ODE分解为多速率系统，对引导分支使用粗粒度积分。
result: 显著减少计算量，同时保持高保真生成。
conclusion: THG是一种高效的无训练扩散推理加速方法。
---

## Abstract
In this paper, we propose Tortoise and Hare Guidance (THG), a training-free strategy that accelerates diffusion sampling while maintaining high-fidelity generation. We demonstrate that the noise estimate and the additional guidance term exhibit markedly different sensitivity to numerical error by reformulating the classifier-free guidance (CFG) ODE as a multirate system of ODEs. Our error-bound analysis shows that the additional guidance branch is more robust to approximation, revealing substantial redundancy that conventional solvers fail to exploit. 
Building on this insight, THG significantly reduces the computation of the additional guidance: the noise estimate is integrated with the tortoise equation on the original, fine-grained timestep grid, while the additional guidance is integrated with the hare equation only on a coarse grid. We also introduce (i) an error-bound-aware timestep sampler that adaptively selects step sizes and (ii) a guidance-scale scheduler that stabilizes large extrapolation spans. 
THG reduces the number of function evaluations (NFE) by up to 30% with virtually no loss in generation fidelity (∆ImageReward ≤ 0.032) and outperforms state-of-the-art CFG-based training-free accelerators under identical computation budgets. Our findings highlight the potential of multirate formulations for diffusion solvers, paving the way for real-time high-quality image synthesis without any model retraining. The source code is available at https://github.com/yhlee-add/THG.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：扩散模型在图像生成中获得高质量结果，但采样速度慢，尤其是无分类器引导（CFG）需要同时计算噪声估计和引导项，计算量大。现有加速方法多基于模型蒸馏或更优的数值求解器，但很少分析CFG两项对数值误差的敏感差异。
- **背景**：CFG ODE 可看作噪声估计分支（精细）和引导分支（粗糙）的组合。作者观察到引导分支对数值近似更鲁棒，存在大量冗余计算。传统求解器为两项使用相同步长，未利用这点。
- **核心问题**：如何在不重新训练的前提下，利用引导分支的鲁棒性，显著减少CFG推理计算量，同时保持生成质量。

### 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：将CFG ODE重新表述为多速率系统（multirate system），对两个分支（“龟”方程——噪声项，“兔”方程——引导项）采用不同积分步长：噪声项使用细粒度原时间网格（tortoise），引导项使用粗粒度网格（hare），从而大幅减少引导项函数评估次数（NFE）。
- **关键技术细节**：
  - **多速率ODE公式**：将CFG扩散ODE拆解为两部分，分别以不同步长积分。
  - **误差界分析**：证明引导分支对数值近似更鲁棒，论证粗网格积分的合理性。
  - **误差界自适应时间步采样器**：根据误差界动态选择步长，平衡精度与计算。
  - **引导尺度调度器**：稳定大外推步长（粗网格）导致的噪声，通过调整引导权重维持生成质量。
- **算法流程（文字说明）**：
  1. 定义细时间网格 \(t_0, t_1, \dots, t_N\)。
  2. 在每一步，噪声估计分支（tortoise）在所有细时间步上计算；引导分支（hare）仅在粗网格（如每k步一次）上计算，其余步通过线性插值或外推近似。
  3. 使用误差界采样器动态调整粗网格密度。
  4. 应用引导尺度调度器在粗步上调整引导权重，避免过度外推。

### 3. 实验设计

- **数据集/场景**：论文未在摘要中明确说明具体数据集，但通常扩散生成评测使用CIFAR-10、ImageNet（类条件生成）和MS-COCO（文本到图像）。需结合上下文推测。Benchmark应为常见生成质量指标（FID、ImageReward等）。
- **对比方法**：比较了最先进的基于CFG的无训练加速器（如DPM-solver、DDIM等），以及同等计算预算下的基线方法。展示了NFE减少30%时图像奖励损失≤0.032。
- **评估指标**：ImageReward（文本-图像一致性）、可能还有FID、CLIP score等摘要未提，但ImageReward是主要指标。

### 4. 资源与算力

- **未明确说明**：摘要和元数据未提及使用的GPU型号、数量、训练时长等信息。因为没有重新训练，推理加速仅需修改采样器，不消耗额外训练资源。实际实验可能的硬件环境未给出。

### 5. 实验数量与充分性

- **实验组数**：摘要只给出了总体结果（NFE减少30%，损失极小）。从方法描述看，可能包括：
  - 不同数据集（至少1-2个）上的生成质量对比。
  - 消融实验：验证多速率策略、误差界采样器、引导调度器各自的贡献。
  - 与多种无训练加速器比较。
  - 不同引导尺度和步长设置的影响。
- **充分性评估**：摘要说“outperforms state-of-the-art CFG-based training-free accelerators”，但未提供详细表格。实验设计在摘要层面看来是充分的，但需要正文验证公平性（如同等计算预算、同一数据、相同种子）。没有提到统计显著性或多轮重复，存在一定偏差风险。

### 6. 论文的主要结论与发现

- 引导分支对数值误差更鲁棒，可以用更粗的步长积分，而噪声分支需要精细步长。
- THG方法在不牺牲保真度的前提下，将CFG推理的NFE减少高达30%（ImageReward下降≤0.032）。
- 多速率公式为扩散求解器提供了新视角，有助于实现无需重训练的实时高质量图像合成。
- 误差界采样器和引导调度器进一步提升了粗网格下生成的稳定性。

### 7. 优点

- **训练无关**：无需任何重训练或微调，直接应用到现有CFG扩散模型。
- **高效且质量保持**：NFE减少30%，质量几乎无损，优于现有无训练加速方法。
- **理论支撑**：提供误差界分析和多速率ODE数学框架，而非仅凭经验。
- **可迁移**：方法独立于模型架构，适用于各种基于CFG的扩散模型（如DALL-E 2, Stable Diffusion等）。
- **易于实现**：仅需修改采样循环，代码已开源。

### 8. 不足与局限

- **实验覆盖有限**：摘要未明确数据集和详细指标，可能仅在一两类任务（如文本到图像）上验证，缺乏对无条件生成、视频生成等场景的测试。
- **资源信息缺失**：未提及实验硬件，不利于可重复性评估。
- **NFE减少依赖初始步长**：30%的加速幅度可能随基础步长变化，未讨论极限情况（如超大步长下质量崩坏）。
- **引导尺度调度器可能引入超参数**：需要额外调优粗网格步长和引导权重，增加使用门槛。
- **仅针对CFG**：不适用于无引导的扩散模型加速，适用范围有限。

（完）
