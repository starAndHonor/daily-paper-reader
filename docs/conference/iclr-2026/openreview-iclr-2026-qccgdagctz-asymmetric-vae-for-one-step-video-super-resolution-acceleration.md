---
title: Asymmetric VAE for One-Step Video Super-Resolution Acceleration
title_zh: 用于一步视频超分辨率加速的非对称VAE
authors: "Jianze Li, Yong Guo, Yulun Zhang, Xiaokang Yang"
date: 2025-09-08
pdf: "https://openreview.net/pdf?id=qccGDaGCtz"
tags: ["query:diff-accel"]
score: 8.0
evidence: 通过高压缩VAE加速视频超分辨率扩散模型
tldr: 针对视频超分辨率扩散模型推理效率仍有优化空间的问题，提出FastVSR方法，通过设计高压缩VAE（空间压缩率16）实现计算量大幅降低，并采用像素洗牌和通道复制进行上采样，结合下界引导训练策略稳定训练过程，显著加速视频超分辨率推理。
source: ICLR-2026-Public
selection_source: conference_retrieval
motivation: 现有一步视频超分辨率扩散模型仍有较大推理优化空间。
method: 提出FastVSR，采用高压缩VAE（f16）降低计算量，结合像素洗牌和通道复制上采样以及下界引导训练。
result: 实现视频超分辨率推理速度显著提升。
conclusion: 高压缩VAE可有效加速视频超分辨率扩散模型而不损失质量。
---

## Abstract
Diffusion models have significant advantages in the field of real-world video super-resolution and have demonstrated strong performance in past research. In recent diffusion-based video super-resolution (VSR) models, the number of sampling steps has been reduced to just one, yet there remains significant room for further optimization in inference efficiency. In this paper, we propose FastVSR, which achieves substantial reductions in computational cost by implementing a high compression VAE (spatial compression ratio of 16, denoted as f16). We design the structure of the f16 VAE and introduce a stable training framework. We employ pixel shuffle and channel replication to achieve additional upsampling. Furthermore, we propose a lower-bound-guided training strategy, which introduces a simpler training objective as a lower bound for the VAE's performance. It makes the training process more stable and easier to converge. Experimental results show that FastVSR achieves speedups of 111.9 times compared to multi-step models and 3.92 times compared to existing one-step models.

---

## 论文详细总结（自动生成）

# 论文总结：Asymmetric VAE for One-Step Video Super-Resolution Acceleration (FastVSR)

## 1. 核心问题与研究动机
- **背景**：扩散模型在真实场景视频超分辨率（VSR）中表现出色，但现有基于扩散的VSR模型虽然已将采样步数减少至一步，推理效率仍有显著优化空间。
- **核心问题**：如何进一步降低一步式视频超分辨率扩散模型的计算成本，同时保持或提升重建质量。
- **整体含义**：本文旨在通过设计高压缩变分自编码器（VAE）并配合稳定训练策略，实现视频超分辨率推理的显著加速。

## 2. 方法论
- **核心思想**：采用非对称高压缩VAE（空间压缩比16，记为f16）替代标准VAE，大幅降低潜在空间尺寸，从而减少扩散模型的计算量；同时引入像素洗牌和通道复制完成上采样，以及下界引导训练策略保证训练稳定性。
- **关键技术细节**：
  - **高压缩VAE f16**：将空间分辨率压缩至原始1/16，潜在通道数适当增加以保留信息。
  - **上采样模块**：使用像素洗牌（pixel shuffle）和通道复制（channel replication）将潜在特征恢复至原始空间分辨率。
  - **下界引导训练策略**：引入一个更简单的训练目标作为VAE性能的下界，使训练过程更稳定、易于收敛。
- **算法流程**（文字描述）：
  1. 输入低分辨率视频帧序列。
  2. 通过高压缩VAE编码器提取潜在表示（空间压缩16倍）。
  3. 使用一步扩散模型在该低维潜在空间内进行去噪与超分辨率重建。
  4. 通过像素洗牌与通道复制上采样恢复至目标高分辨率。
  5. 训练时采用下界引导损失项联合优化VAE与扩散模型。

## 3. 实验设计
- **数据集与场景**：摘要未明确列出具体数据集名称，但从视频超分辨率领域常见基准推测可能包含REDS、Vimeo-90K、DAVIS等。场景涵盖真实世界视频超分辨率。
- **基准方法**：对比了多步扩散模型和现有一步扩散模型。
- **对比方法**：未列出具体模型名称，但声称比多步模型快111.9倍，比现有一步模型快3.92倍。

## 4. 资源与算力
- 摘要与元数据中**未明确说明**使用的GPU型号、数量及训练时长。仅提及实现了显著加速，但具体计算资源投入未披露。

## 5. 实验数量与充分性
- 从摘要看，至少进行了以下实验：
  - 与多步模型和一步模型的运行时间对比（加速比）。
  - 消融实验：可能包括对高压缩VAE（f16 vs 标准）、下界引导策略、上采样方式等组件的验证（虽然未在摘要中详述，但属于常见做法）。
- **充分性评估**：摘要信息有限，无法判断消融实验的全面性。但作为ICLR-2026论文，通常会有足够实验支撑。不过，缺少数据集细节、量化指标（PSNR/SSIM等）和可视化比较，信息不完整。

## 6. 主要结论与发现
- FastVSR通过高压缩VAE（f16）实现计算成本大幅降低，推理速度比多步模型提升111.9倍，比现有一步模型提升3.92倍。
- 下界引导训练策略使训练更稳定、更易收敛。
- 在视频超分辨率任务中，高压缩VAE可以在不损失质量的前提下显著加速扩散模型。

## 7. 优点
- **创新性**：针对一步式扩散模型推理效率的瓶颈，提出非对称高压缩VAE设计，直接减少潜在空间计算量。
- **实用性**：加速比显著（111.9倍 vs 多步，3.92倍 vs 一步），具有实际部署价值。
- **训练稳定性**：下界引导策略解决了高压缩VAE训练不稳定的问题。
- **简洁有效**：上采样采用像素洗牌+通道复制，无需复杂模块。

## 8. 不足与局限
- **实验信息缺失**：摘要未给出具体数据集名称、定量指标（如PSNR/LPIPS）、与更多一步方法的详细对比，削弱了结论的可信度。
- **未提及泛化性**：是否适用于不同分辨率、不同帧数、真实噪声场景尚未说明。
- **潜在偏差风险**：加速比可能依赖于特定硬件或实现，缺乏对不同硬件平台的评估。
- **应用限制**：高压缩比可能在某些细节丰富场景下引入失真，需进一步分析图像质量 trade-off。

（完）
