---
title: "SlimDiff: Training-Free, Activation-Guided Hands-free Slimming of Diffusion Models"
title_zh: SlimDiff：免训练的激活引导手自由扩散模型精简
authors: "Arani Roy, Shristi Das Biswas, Kaushik Roy"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=zAjcuqN31s"
tags: ["query:diff-accel"]
score: 9.0
evidence: 免训练的激活引导结构压缩，用于扩散模型
tldr: SlimDiff将扩散模型压缩视为频谱近似任务，利用去噪过程中激活协方差定义低秩子空间，在无需梯度和训练的情况下自动缩减注意力和前馈维度。该方法完全免训练，并通过激活引导实现自动化结构压缩，显著降低模型参数和计算量，同时保持生成性能。
source: ICLR-2026-Public
selection_source: conference_retrieval
motivation: 现有效率技术（量化、步数减少、剪枝）依赖微调或重训练，本文旨在实现完全免训练的结构压缩。
method: 将扩散模型压缩重构为频谱近似，利用激活协方差跨时间步定义低秩子空间，减少注意力和前馈维度。
result: 在无需任何训练或梯度的情况下有效压缩模型，保持生成质量。
conclusion: SlimDiff提供了一种全自动、免训练的结构压缩范式，可广泛适用于各类扩散模型。
---

## Abstract
Diffusion models (DMs), lauded for their generative performance, are computationally prohibitive due to their billion-scale parameters and iterative denoising dynamics. Existing efficiency techniques, such as quantization, timestep reduction, or pruning, offer savings in compute, memory, or runtime but are strictly bottle-necked by reliance on fine-tuning or retraining to recover performance. In this work, we introduce SlimDiff, an automated activation-informed structural compression framework that reduces both attention and feedforward dimensionalities in DMs, while being entirely gradient-free. SlimDiff reframes DM compression as a spectral approximation task, where activation covariances across denoising timesteps define low-rank subspaces that guide dynamic pruning under a fixed compression budget. This activation-aware formulation mitigates error accumulation across timesteps by applying module-wise decompositions over functional weight groups: query–key interactions, value–output couplings, and feedforward projections — rather than isolated matrix factorizations, while adaptively allocating sparsity across modules to respect the non-uniform geometry of diffusion trajectories. SlimDiff achieves up to 35% acceleration and ~100M parameter reduction over baselines, with generation quality on par with uncompressed models without any backpropagation. Crucially, our approach requires only about $500$ calibration samples, over 70X fewer than prior methods. To our knowledge, this is the first closed-form, activation-guided structural compression of DMs that is entirely training-free, providing both theoretical clarity and practical efficiency.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）
扩散模型（Diffusion Models, DMs）在生成任务中性能卓越，但因其参数规模达数十亿且需迭代去噪，计算开销巨大。现有效率提升技术（如量化、步数减少、剪枝）虽能节省计算、内存或运行时间，但严格受限于依赖微调或重训练来恢复性能。本文旨在提出一种**完全免训练、免梯度**的结构压缩方法，解决扩散模型部署中的计算瓶颈问题。

## 2. 方法论：核心思想、关键技术细节
**核心思想**：将扩散模型压缩重构为**频谱近似任务**，利用去噪过程中**激活协方差**跨时间步定义低秩子空间，在固定压缩预算下动态修剪注意力和前馈维度。

**关键技术细节**：
- **激活引导**：无需反向传播，仅需约500个校准样本，计算激活协方差以识别低秩方向。
- **功能组分解**：对查询-键交互、值-输出耦合、前馈投影等**功能权重组**进行模块级分解，而非孤立矩阵分解，从而缓解跨时间步的误差累积。
- **自适应稀疏分配**：根据扩散轨迹的非均匀几何特性，在模块间自适应分配稀疏度。
- **无梯度、闭式解**：整个压缩过程为闭式形式，无需任何训练或梯度计算。

（公式或算法流程：文中未给出具体公式，但核心在于利用协方差矩阵进行低秩近似，相当于一种激活感知的频谱分解。）

## 3. 实验设计
- **数据集/场景**：摘要未明确列出具体数据集，但提到“超过基线”“生成质量与未压缩模型相当”，推测使用了标准图像生成基准（如CIFAR-10、ImageNet等，需从原文确认，但摘要未详述）。
- **基准（Benchmark）**：对比的是未压缩的基线模型，以及现有剪枝、量化等效率方法。
- **对比方法**：与依赖微调或重训练的其他压缩技术对比，突出免训练优势。

## 4. 资源与算力
摘要中未明确说明使用的GPU型号、数量或训练时长。但强调**仅需约500个校准样本**，且**完全免训练**，算力需求远低于先前方法（样本数少于先前方法的1/70）。具体硬件细节需参考论文正文。

## 5. 实验数量与充分性
由于仅有摘要，无法获取实验数量。但摘要声称：
- 实现了**35%加速**和**约一亿参数减少**。
- 生成质量与未压缩模型相当。
- 仅需500个校准样本（优于先前方法的数万样本）。
从这些指标看，实验应覆盖多种扩散模型架构（如DDPM、Stable Diffusion等？），并进行了消融研究（如不同压缩预算、模块贡献等）。但摘要未提供具体实验组数，需阅读全文评估充分性。

## 6. 主要结论与发现
- SlimDiff是首个**完全免训练、闭式、激活引导的结构压缩**方案，可为扩散模型同时减少注意力和前馈维度。
- 在无需任何反向传播的情况下，实现显着的参数量减少和加速，同时保持生成质量。
- 方法具有理论清晰度和实际效率，所需校准样本极少。

## 7. 优点
- **完全免训练**：无需微调或重训练，极大降低压缩成本。
- **激活引导的自动结构化压缩**：基于激活协方差自动识别低秩子空间，无需手动设计剪枝策略。
- **模块级功能组分解**：比孤立矩阵分解更适应扩散模型的跨时间步误差累积。
- **样本效率高**：仅需500个校准样本，是先前方法的1/70。
- **理论简洁**：将压缩问题转化为频谱近似，提供闭式解。

## 8. 不足与局限
- **实验细节缺失**：摘要未给出具体数据集、模型架构、对比方法名称等，无法判断实验覆盖范围。
- **可能依赖校准数据**：虽然样本少，但仍需要一定量数据计算激活协方差，在零样本场景下可能受限。
- **仅适用于结构压缩**：未与量化、蒸馏等正交技术结合验证。
- **未讨论极端压缩比下的质量下降**：35%加速和~100M参数减少可能是在特定压缩比下，更高压缩比时质量下降情况未知。
- **应用限制**：可能仅适用于特定扩散模型架构（如U-Net？），对Transformer-based扩散模型是否有效需验证。
- **偏差风险**：摘要未提及随机种子、多次重复实验等，结果可能存在偶然性。

（完）
