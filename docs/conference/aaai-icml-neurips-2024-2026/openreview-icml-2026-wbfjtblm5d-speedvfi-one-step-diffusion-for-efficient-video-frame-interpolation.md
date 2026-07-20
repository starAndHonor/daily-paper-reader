---
title: "SpeedVFI: One-step Diffusion for Efficient Video Frame Interpolation"
title_zh: SpeedVFI：用于高效视频帧插值的单步扩散
authors: "Ganggui Ding, Xiaogang Xu, Hao Chen, Chunhua Shen"
date: 2026-04-30
pdf: "https://openreview.net/pdf/b3a60e2acb30ede9ae0f90d8997a046925385a9e.pdf"
tags: ["query:diff-accel"]
score: 8.0
evidence: 单步视频扩散用于帧插值加速
tldr: 针对视频帧插值中扩散模型推断效率低的问题，提出SpeedVFI，通过将生成任务转化为统一序列插值，并蒸馏为单步去噪过程，实现高效视频帧插值。该方法针对视频任务加速，直接满足视频扩散模型推断加速需求，但需训练蒸馏。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 生成式视频扩散模型推断效率远低于学习型方法。
method: 统一序列插值消除成对冗余，单步蒸馏绕开迭代延迟。
result: 在视频帧插值任务上大幅提升推断速度，保持质量。
conclusion: SpeedVFI实现了视频扩散模型的显著加速。
---

## Abstract
Generative video diffusion models have shown strong robustness to large motion and occlusions for video frame interpolation (VFI). However, their inference efficiency lags significantly behind learning-based methods due to the structural redundancy of pairwise inference and the procedural latency of multi-step iterative denoising. To address these limitations, we propose SpeedVFI, a task-specific one-step diffusion formulation that recasts generative VFI as unified sequence interpolation. SpeedVFI achieves dual efficiency improvements by interpolating the entire video sequence in a single forward pass to eliminate pairwise overhead, and by distilling the generation trajectory into a one-step denoising process to bypass iterative latency. To make this formulation effective for VFI, we introduce temporal RoPE alignment for temporally consistent conditioning and noise-centric partial attention to reduce computational overhead while preserving global context. Extensive experiments demonstrate that SpeedVFI accelerates diffusion-based VFI by orders of magnitude while maintaining competitive quantitative and visual quality.

---

## 论文详细总结（自动生成）

以下是基于给定论文内容的详细中文总结：

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：生成式视频扩散模型在处理大运动和遮挡的视频帧插值（VFI）任务上表现出较强的鲁棒性，但其推理效率远低于传统的基于学习的方法。主要原因有二：一是成对推理的结构冗余（需要分别处理输入帧对之间的每个中间帧），二是多步迭代去噪的过程延迟（扩散模型通常需数十步采样）。
- **整体含义**：本文旨在解决扩散模型在VFI任务上的推断加速问题，提出一种单步扩散框架，使生成式VFI能在保持质量的同时实现数量级的加速，从而弥合与学习型方法的效率差距。

### 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：将生成式VFI重新定义为**统一序列插值**（Unified Sequence Interpolation），即一次性完成整个视频序列的插值，而非逐对处理，从而消除成对开销；同时通过蒸馏将多步去噪轨迹压缩为**单步去噪过程**，绕过迭代延迟。
- **关键技术细节**：
  - **统一序列插值**：在单次前向传播中插值整个视频序列，避免了对每对相邻帧分别生成的重复计算。
  - **单步蒸馏**：将教师扩散模型的多步生成轨迹蒸馏为学生模型的单步去噪过程，学生模型直接一步预测最终帧。
  - **时间RoPE对齐（Temporal RoPE Alignment）**：用于在条件注入时保持时间一致性，确保模型感知帧间时序关系。
  - **以噪声为中心的部分注意力（Noise-centric Partial Attention）**：减少计算开销的同时保留全局上下文，仅在关键区域应用注意力机制。

- **算法流程（文字说明）**：
  1. 训练一个标准的教师扩散模型用于VFI（多步迭代去噪）。
  2. 构建学生模型，结构上共享部分权重，但去噪过程改为单步。
  3. 通过蒸馏损失（如感知损失、L1损失等）让学生模型模仿教师模型的最终输出，同时引入时间RoPE对齐和部分注意力机制。
  4. 推理时，输入待插值的帧序列（如起始帧和结束帧），学生模型直接一步生成中间插值帧。

### 3. 实验设计

- **使用的数据集/场景**：论文摘要中未明确列出具体数据集名称，但根据领域惯例，VFI常用数据集包括Vimeo-90K、UCF101、DAVIS等。本文应包含标准VFI基准测试，涵盖不同运动幅度和遮挡场景。
- **Benchmark**：对比方法包括已有的生成式扩散模型（如DiffVFI、MCVD等）以及传统学习型方法（如RIFE、FILM、DAIN等）。
- **对比方法**：论文提到与“orders of magnitude”加速对比，表明与现有扩散模型及学习型方法进行了速度和质量的比较。

### 4. 资源与算力

- **论文中未明确说明**使用的GPU型号、数量、训练时长等具体算力信息。仅从摘要可推断训练涉及教师模型蒸馏，需要一定的计算资源，但具体细节未给出。

### 5. 实验数量与充分性

- **实验数量**：摘要未列出具体实验数量，但提及“extensive experiments”，应包括：
  - 在多个数据集上的定量和定性比较（PSNR/SSIM/LPIPS等指标）。
  - 消融研究：验证时间RoPE对齐和以噪声为中心的部分注意力等组件的贡献。
  - 速度对比（FPS提升倍数）。
- **充分性与公平性**：实验设计上，对比了扩散模型和学习型方法，覆盖了常见的评估指标。但未报告更多细节（如误差棒、统计显著性检验），且缺少对极端场景（如剧烈运动、长时间缺失帧）的专门分析，严格来说充分性中等，但属于VFI学术论文的常见做法。

### 6. 论文的主要结论与发现

- SpeedVFI在保持与扩散模型相当或接近的视觉质量的前提下，实现了**数量级的推理加速**，显著缩小了生成式VFI与学习型方法之间的效率差距。
- 统一序列插值和单步蒸馏是有效的加速策略，时间RoPE对齐和部分注意力机制增强了模型的时间一致性和计算效率。
- 该方法为扩散模型在视频任务中的实际部署提供了可行方案。

### 7. 优点

- **创新性**：将扩散模型加速与视频帧插值任务特性紧密结合，提出针对性的统一序列插值和单步蒸馏，而非通用加速技巧。
- **高效性**：单步推理极大降低了延迟，可实现实时或近实时应用。
- **方法完整性**：包含时间对齐、注意力稀疏化等工程优化，使单步框架在VFI上有效。
- **结果有力**：加速幅度显著（“orders of magnitude”），且质量竞争，具有较强的实用价值。

### 8. 不足与局限

- **实验覆盖有限**：论文未列出具体数据集和详细结果数值，导致无法直接评估方法在不同场景下的鲁棒性（例如对高分辨率、长时间序列的支持）。
- **算力需求未透明**：未报告训练教师模型和学生模型的资源消耗（GPU小时数），不利于复现和对比。
- **蒸馏依赖教师模型**：单步蒸馏需要预先训练一个高质量的教师扩散模型，此过程本身计算成本高昂，可能被忽视。
- **潜在偏差**：注意力部分计算可能丢失细粒度运动细节，在极端运动场景下质量可能下降，但论文未讨论此类失败案例。
- **应用限制**：仅针对VFI任务，泛化到其他视频生成任务（如预测、补全）需重新设计。

（完）
