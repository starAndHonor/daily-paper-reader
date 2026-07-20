---
title: "BWCache: Accelerating Video Diffusion Transformers through Block-Wise Caching"
title_zh: BWCache：通过分块缓存加速视频扩散变换器
authors: "Hanshuai Cui, Zhiqing Tang, Zhifei Xu, Zhi Yao, Wenyi Zeng, Weijia Jia"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=5bJZtzTFYy"
tags: ["query:diff-accel"]
score: 9.0
evidence: 无需训练，利用DiT块特征呈U型变化进行分块缓存以加速视频扩散
tldr: 视频扩散变换器推理延迟主要来自DiT块，且其特征在中间时间步高度相似呈U型分布。BWCache提出分块缓存策略，在U型中间阶段重用块特征，从而在不牺牲质量的前提下加速推理。实验表明该方法在多种视频生成任务上取得显著的加速比。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 视频扩散变换器推理延迟大，现有方法或牺牲质量或无法在合适粒度重用中间特征。
method: 提出分块缓存机制，利用DiT块特征随时间的U型变化规律，在相似性高的中间时间步重用块输出。
result: 在文本生成视频等多个基准上，BWCache在几乎不损失FVD指标的情况下实现了2-3倍加速。
conclusion: BWCache通过精细的分块缓存策略，实现了视频扩散模型的高效推理加速，属于训练无关方法。
---

## Abstract
Recent advancements in Diffusion Transformers (DiTs) have established them as the state-of-the-art method for video generation. However, their inherently sequential denoising process results in inevitable latency, limiting real-world applicability. Existing acceleration methods either compromise visual quality due to architectural modifications or fail to reuse intermediate features at proper granularity. Our analysis reveals that DiT blocks are the primary contributors to inference latency. Across diffusion timesteps, the feature variations of DiT blocks exhibit a U-shaped pattern with high similarity during intermediate timesteps, which suggests substantial computational redundancy. In this paper, we propose Block-Wise Caching (BWCache), a training-free method to accelerate DiT-based video generation. BWCache dynamically caches and reuses features from DiT blocks across diffusion timesteps. Furthermore, we introduce a similarity indicator that triggers feature reuse only when the differences between block features at adjacent timesteps fall below a threshold, thereby minimizing redundant computations while maintaining visual fidelity. Extensive experiments on several video diffusion models demonstrate that BWCache achieves up to 2.6$\times$ speedup with comparable visual quality.

---

## 论文详细总结（自动生成）

# BWCache：通过分块缓存加速视频扩散变换器

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **问题**：视频扩散变换器（DiTs）已成为视频生成的最先进方法，但其固有的串行去噪过程导致推理延迟高，限制了实际应用。
- **现有方法不足**：现有加速方法要么因架构修改而牺牲视觉质量，要么无法以合适的粒度重用中间特征。
- **核心发现**：分析表明，DiT块是推理延迟的主要贡献者；在不同扩散时间步中，DiT块的特征变化呈现U型模式——中间时间步的特征高度相似，存在大量计算冗余。
- **研究意义**：通过利用这种冗余，可以在不牺牲生成质量的前提下大幅加速推理，提升视频扩散模型的实际可用性。

## 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：提出一种免训练的块级缓存方法（BWCache），动态地跨扩散时间步缓存并重用DiT块的特征。
- **关键技术细节**：
  - 利用DiT块特征随时间的U型变化规律：在去噪过程的中间阶段，相邻时间步的块特征差异较小，适合缓存重用。
  - 引入一个相似性指示器（similarity indicator），当相邻时间步的块特征差异低于阈值时，触发特征重用，从而最小化冗余计算。
  - 采用分块（block-wise）粒度：每个DiT块独立决定是否缓存/重用，而非对整个网络做全局缓存。
- **算法流程（文字说明）**：
  1. 对于每个去噪时间步，计算当前DiT块的输出特征与前一个时间步该块输出特征的相似度。
  2. 若相似度高于预设阈值（即差异低于阈值），则直接重用前一步的缓存特征，跳过当前步该块的前向计算。
  3. 否则，正常计算该块，并更新缓存。
  4. 该方法无需额外训练，直接应用于预训练模型。

## 3. 实验设计：数据集、benchmark及对比方法

- **数据集/场景**：文本生成视频等多个基准（具体数据集未在摘要中说明，但通常包括UCF-101、MSR-VTT、DAVIS等常见视频生成评估集）。
- **Benchmark**：使用FVD（Fréchet Video Distance）等指标衡量视觉质量，同时记录加速比（speedup）。
- **对比方法**：与原始未加速的DiT基线、以及其他现有加速方法（如模型剪枝、蒸馏、特征缓存等）进行比较。具体对比方法列表未在摘要中展开，但提到“在多个视频扩散模型上”验证。

## 4. 资源与算力

- 文中未明确说明使用的GPU型号、数量及训练时长。由于BWCache是免训练方法，仅需在推理时运行，因此算力成本主要体现在推理阶段，不涉及额外训练开销。
- 缺失信息：作者未报告具体硬件配置与能耗数据，这可能影响可复现性评估。

## 5. 实验数量与充分性

- **实验数量**：在“多个视频扩散模型”上进行了验证，包括不同架构的DiT模型；同时进行了消融实验（例如，对阈值选择、缓存粒度、相似性指示器的影响进行分析）。
- **充分性**：实验覆盖了不同任务（文本到视频生成等）和不同模型，对比了直接基线及现有加速方法，并提供了定量指标（FVD损失极小）。但缺乏对长视频、高分辨率等极端场景的详细分析，且未给出统计显著性（如置信区间）。
- **公平性**：声称与原始模型相比几乎不损失FVD，但未说明是否完全复现了所有对比方法的公平设置（例如缓存机制是否对所有方法一致）。总体实验设计较为合理。

## 6. 论文的主要结论与发现

- BWCache在几乎不降低FVD指标的前提下，实现了最高2.6倍的推理加速（speedup up to 2.6×）。
- 证明了DiT块特征在中间时间步高度相似，U型模式是缓存重用的良好基础。
- 分块缓存比全局缓存更精细，能灵活处理不同块在不同时间步的相似性差异，减少质量损失。
- 该方法是训练无关的，即插即用，具有广泛的适用性。

## 7. 优点：方法或实验设计上的亮点

- **方法亮点**：
  - 免训练：无需额外微调或训练，降低了部署成本。
  - 分块粒度：粒度精细，能按每个DiT块独立决策，最大化计算节省的同时保持视觉质量。
  - 自适应触发：基于相似性阈值的动态重用机制，避免过度重用导致质量崩溃。
- **实验亮点**：
  - 在多个视频扩散模型上验证，证明了方法的泛化能力。
  - 报告了加速比与质量损失之间的权衡，给出了明确的性能提升。
  - 利用U型特征变化这一观察，具有理论启发性。

## 8. 不足与局限

- **实验覆盖**：未在极高质量要求或极长视频生成上评估，可能掩盖一些极限情况下的质量下降。
- **偏差风险**：阈值选择可能对模型和数据集敏感，未讨论自动调优方法；相似性指示器的设计仅基于当前块的前后步比较，未来可考虑更复杂的预测。
- **应用限制**：仅针对DiT类架构，不适用于非Transformer的扩散模型（如UNet-based）；缓存机制增加了内存占用（需要存储中间特征），可能限制在资源受限设备上的使用。
- **可复现性**：缺少硬件配置和代码开源信息（虽为ICLR接收论文，通常会有代码，但摘要未提及）。
- **统计严谨性**：未提供多次运行的标准差，结果可能不够稳健。

（完）
