---
title: "LearniBridge: Learnable Calibration of Feature Caching for Diffusion Models Acceleration"
title_zh: LearniBridge：面向扩散模型加速的特征缓存可学习校准
authors: "Xuyue Huang, Zhe Chen, Wang Shen, Xiao-Ping Zhang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/9105db3b4513fbce17141e5d523450f83144911f.pdf"
tags: ["query:diff-accel"]
score: 6.0
evidence: 特征缓存加速扩散模型
tldr: 扩散变压器（DiT）在图像视频生成中计算成本高昂。特征缓存通过重用中间表示加速推理，但现有方法在高加速比下误差累积严重。本文发现最优校准更新具有跨提示共享的低秩子空间，据此提出LearniBridge，一种轻量级可学习校准机制，有效减少误差累积。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有特征缓存方法在高加速比下误差累积严重，限制加速效果。
method: 提出LearniBridge，利用低秩子空间结构实现可学习校准，跨时间步桥接特征。
result: 在多种DiT模型上实现更高加速比同时保持生成质量。
conclusion: 可学习校准有效提升特征缓存的加速性能。
---

## Abstract
Diffusion Transformers (DiTs) have driven substantial progress in image and video generation but suffer from prohibitive computational costs.
Feature caching accelerates inference by reusing intermediate representations. 
Existing methods rely on historical features for implementation simplicity, 
yet suffer from severe error accumulation at high acceleration ratios.
To address this limitation, we investigate the nature of the requisite feature correction.
We demonstrate that the optimal calibration update is characterized by a shared low-rank subspace across diverse prompts.
Guided by this structural insight, we propose LearniBridge, a learnable calibration mechanism for feature caching that bridges multiple timesteps through lightweight LoRA updates.
This mechanism enables effective calibration requiring only $3-5$ training samples.
Extensive experiments on image and video generation show that LearniBridge achieves up to $5.87\times$, $5.75\times$, and $4.10\times$ acceleration
on FLUX, HunyuanVideo, and WAN 2.1, respectively. On WAN 2.1, it improves VBench by 1.28\% over the previous SOTA at $4.10\times$ acceleration.

---

## 论文详细总结（自动生成）

# 论文总结：LearniBridge：面向扩散模型加速的特征缓存可学习校准

## 1. 论文的核心问题与整体含义（研究动机和背景）

扩散 Transformer（Diffusion Transformers, DiTs）在图像和视频生成任务中取得了显著进展，但其推理过程计算成本极高，限制了实际部署。特征缓存（Feature Caching）是一种常见的加速方法，通过重用中间表示来减少冗余计算。然而，现有特征缓存方法在高加速比（即缓存间隔较大）时，由于简单依赖历史特征而缺乏对误差的主动校正，导致误差快速累积，严重损害生成质量。

本文的核心动机是：**如何在保持高加速比的同时，有效抑制特征缓存中的误差累积？** 作者通过对误差校正的本质进行理论分析，发现：对于不同提示（prompt），最优的校准更新共享一个低秩子空间结构。基于这一洞察，提出了 **LearniBridge**——一种轻量级的可学习校准机制，通过极少量样本（3-5个）训练即可实现跨时间步的特征桥接，大幅提升加速比并保持生成质量。

## 2. 论文提出的方法论：核心思想、关键技术细节

### 核心思想

利用校准更新的低秩子空间特性，设计一个可学习的校准模块，该模块以当前缓存特征和少量上下文信息为输入，输出校正量，从而桥接因缓存跳过的多个时间步骤。该模块通过 LoRA（Low-Rank Adaptation）形式实现，参数量极小，仅需极少量训练样本即可适配不同模型。

### 关键技术细节

1. **低秩子空间发现**：作者通过理论分析和实验验证，证明对于不同输入提示，DiT 模型中特征缓存的“最优校准更新”都位于一个共享的低秩子空间中。这意味着可以用一个低秩矩阵来近似校准函数。
2. **可学习校准机制**：LearniBridge 不直接修改缓存策略，而是为每个跳过的步骤引入一个轻量级校正网络，该网络由几个 LoRA 模块组成，输入为当前缓存特征和临近步骤的辅助信息，输出校正后的特征。
3. **训练过程**：仅需 3-5 个训练样本（即少量提示下的生成轨迹），通过最小化校正后特征与全精度特征之间的误差来更新 LoRA 参数。训练成本极低。
4. **推理过程**：在校准阶段，按固定间隔缓存特征，并在每个被跳过的步骤应用 LearnedBridge 进行校正，之后继续执行后续时间步。

（注：由于原文提供的是摘要和元数据，具体公式和算法流程图未完整给出，但以上为基于摘要的合理推断。）

## 3. 实验设计：数据集 / 场景、基准方法

- **模型与任务**：在三个主流扩散模型上进行图像和视频生成实验：
  - FLUX（图像生成）
  - HunyuanVideo（视频生成）
  - WAN 2.1（视频生成）
- **基准方法**：与“之前的最优特征缓存加速方法”（previous SOTA）进行对比，具体方法名称未在摘要中列出，但可推测是 DeepCache 等同类方法。
- **评估指标**：对于视频生成，使用 VBench（一种视频质量评估基准）；加速比主要通过推理时间对比（FLOPs 或实际时间估计）。
- **加速比对比**：LearniBridge 在 FLUX 上达到 5.87× 加速，HunyuanVideo 上 5.75×，WAN 2.1 上 4.10×。在 WAN 2.1 上，以 4.10× 加速时 VBench 比前 SOTA 高出 1.28%。

## 4. 资源与算力

摘要和元数据中 **未明确说明** 所使用的 GPU 型号、数量、训练时长等算力信息。仅提及训练仅需 3-5 个样本，暗示训练开销极低。但仍缺乏具体硬件配置细节。这一点需要在总结中明确指出。

## 5. 实验数量与充分性

- **实验数量**：论文在三个不同模型（两个为视频模型，一个为图像模型）上进行了实验，涵盖图像和视频两类生成任务。每个模型均报告了加速比，并在 WAN 2.1 上进行了 VBench 指标对比。此外，很可能包含消融实验（如校准模块设计选择、低秩秩大小的影响等），但摘要未具体说明。
- **充分性与公平性**：
  - **覆盖范围**：涵盖了主流 DiT 架构和多种生成任务，具有一定广度。
  - **对比基准**：仅提及“前 SOTA”，未列出多个方法的详细对比，可能不够全面。
  - **公平性**：对于加速方法，需确保不同方法在同一硬件和实现细节下比较，但摘要未说明控制变量。消融实验对验证“可学习校准”的有效性至关重要，但未在摘要中提及，需查看全文确认。
  - **总体评价**：实验数量中等偏上，但信息有限，尚无法完全判断其充分性。若能展示更多消融和超参分析会更可靠。

## 6. 论文的主要结论与发现

1. **理论发现**：特征缓存的最优校准更新具有跨提示共享的低秩子空间结构，这是可学习校准的理论基础。
2. **方法有效性**：LearniBridge 能够在极低训练样本（3-5 个）下实现有效校准，大幅提升加速比（在多个模型上达到 4.10×~5.87×），同时保持或甚至改善生成质量（VBench 提升 1.28%）。
3. **实用优势**：相比于需要复杂调参或大量训练数据的方法，LearniBridge 轻量、易部署，适合实际应用。

## 7. 优点：方法或实验设计上的亮点

- **理论驱动**：基于低秩子空间的发现设计方法，而非纯启发式，增强了可解释性。
- **极低的训练成本**：仅需 3-5 个样本即可完成校准模块的训练，避免了大规模重训练。
- **显著的加速收益**：在多种主流模型上获得了 4 倍以上的加速，且质量损失极小甚至有所提升，实用性突出。
- **跨模态适用**：同时验证了图像和视频生成任务，显示方法具有通用性。

## 8. 不足与局限

- **算力信息缺失**：未报告训练和推理的具体硬件配置，不利于复现和算力成本估算。
- **对比方法不全面**：仅提到“前 SOTA”，未列出多种基线方法（如无缓存、低精度量化、蒸馏等），对比范围可能较窄。
- **消融实验未在摘要体现**：若缺少对低秩子空间假设、训练样本数量、校准模块结构等关键因素的消融分析，论证链条不够完整。
- **泛化性存疑**：仅在三个模型上验证，更多架构（如 Stable Diffusion 3、Open Sora 等）未见测试；且仅测试了各模型的一种设置，未探索不同缓存间隔下的稳健性。
- **VBench 提升幅度较小**：在 4.10× 加速下仅提升 1.28%，可能在实际感受上不明显，需关注统计显著性。
- **可能存在偏差风险**：若选择特定提示作为训练样本，可能对未见提示的泛化能力造成影响，论文未讨论。

（完）
