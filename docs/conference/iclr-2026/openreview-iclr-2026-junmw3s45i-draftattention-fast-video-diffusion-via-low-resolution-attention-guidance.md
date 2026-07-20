---
title: "DraftAttention: Fast Video Diffusion via Low-Resolution Attention Guidance"
title_zh: 草稿注意力：通过低分辨率注意力引导实现快速视频扩散
authors: "Xuan Shen, Chenxia Han, Yufa Zhou, Yanyue Xie, Yifan Gong, Quanyi Wang, Yiwei Wang, Yanzhi Wang, Pu Zhao, Jiuxiang Gu"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=jUNmW3s45i"
tags: ["query:diff-accel"]
score: 9.0
evidence: 提出使用低分辨率查询/键的草稿注意力，实现无需训练的视频扩散变换器加速
tldr: DraftAttention针对视频扩散变换器计算量大的问题，提出一种免训练的稀疏注意力加速框架：通过计算降采样后的低分辨率草稿注意力，大幅减少自注意力计算开销，在8秒720p视频生成中将延迟从数十分钟降至数分钟，且无需额外训练。
source: ICLR-2026-Public
selection_source: conference_retrieval
motivation: "视频扩散变换器中注意力计算占总延迟80%以上，导致生成速度极慢。"
method: 设计低分辨率草稿注意力机制，基于降采样后的查询和键计算近似注意力图，动态稀疏化全分辨率注意力。
result: 在视频生成任务上实现数倍加速，同时保持生成质量。
conclusion: DraftAttention是一种高效且实用的视频扩散模型加速方法。
---

## Abstract
Video generation models based on diffusion transformers have recently attracted widespread attention for their excellent generation quality.
Despite recent progress, their computational expense remains the principal bottleneck. In particular, attention alone accounts for more than 80\% of the overall latency, and the synthesis of only 8 seconds 720p video takes tens of minutes, which severely restricts practical applicability and scalability.
To address this, we propose **DraftAttention**, a training-free framework for the acceleration of video diffusion transformers with dynamic sparse attention on GPUs.
The key idea is to compute the low-resolution draft attention based on the downsampled low-resolution query and key with minor computational overhead. The draft attention exposes redundancy both spatially within each feature map and temporally across frames, thus identifying the most important areas in the attention map.
The resulting low-resolution sparse mask then guides full-resolution sparse attention computations.
To align region-level sparsity with token-level computations, we further propose a deterministic reordering of tokens such that entries in each region become contiguous in memory, ensuring hardware-friendly execution of sparse attention.
Our theoretical analysis demonstrates that the low-resolution draft attention closely approximates the full attention, providing reliable guidance for constructing accurate sparse attention.
Experimental results show that our method outperforms existing sparse attention approaches in video generation quality and achieves up to 2x end-to-end speedup on GPUs.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义

- **研究动机**：基于扩散变换器（Diffusion Transformer）的视频生成模型虽生成质量优异，但计算开销极大——单次注意力计算占总体延迟的80%以上，合成一段8秒720p视频需耗时数十分钟，严重限制了实际应用与规模扩展。
- **核心问题**：如何在不重新训练模型的前提下，显著加速视频扩散模型中的自注意力计算，同时保持生成质量。

### 2. 论文提出的方法论

- **核心思想**：通过计算低分辨率“草稿注意力”来识别注意力图中的冗余区域（空间与时间维度），生成稀疏掩码，进而指导全分辨率下的稀疏注意力计算，从而大幅减少计算量。
- **关键技术细节**：
  - **低分辨率草稿注意力**：将查询（Query）和键（Key）进行降采样，用较低分辨率计算近似注意力图，仅引入少量额外开销。
  - **稀疏掩码生成**：草稿注意力暴露了特征图内空间冗余与帧间时间冗余，据此标记出注意力图中最重要的区域，形成稀疏掩码。
  - **确定性重排序**：为使区域级稀疏性与 token 级计算对齐，对 token 进行确定性重排序，使同一区域内的条目在内存中连续，确保稀疏注意力在 GPU 上高效执行。
- **理论分析**：证明了低分辨率草稿注意力能够紧密逼近全分辨率注意力，为构建准确的稀疏注意力提供可靠指导。

### 3. 实验设计

- **数据集与场景**：未在摘要中明确说明使用的具体视频数据集或生成场景（如文本到视频、无条件生成等）。
- **基准（Benchmark）**：未列出具体基准测试，但方法在视频生成任务上评估。
- **对比方法**：与现有的稀疏注意力方法进行了比较，实验表明所提方法在生成质量上优于这些方法，并实现了最高2倍的端到端 GPU 加速。

### 4. 资源与算力

- **文中未明确说明**：未提及使用的 GPU 型号、数量、训练时长等具体算力信息。仅指出方法是“训练无关”（training-free），因此无需额外训练资源（仅推理阶段加速）。

### 5. 实验数量与充分性

- **实验数量**：摘要中仅概括性提到“实验表明”，未列举具体实验组数（如不同数据集、不同分辨率、消融实验等）。信息十分有限。
- **充分性与公平性**：从现有信息无法判断实验是否充分、客观。缺乏消融研究、超参数敏感性分析、多数据集验证等常见实验设计。对比方法也仅泛泛提及，未说明是否公平控制变量（如相同推理步数、相同模型等）。

### 6. 论文的主要结论与发现

- DraftAttention 是一种无需训练的高效视频扩散注意力加速框架。
- 在 GPU 上可实现最高2倍的端到端加速。
- 在生成质量上优于现有稀疏注意力方法。
- 低分辨率草稿注意力能近似全分辨率注意力，提供可靠稀疏指引。

### 7. 优点

- **无需训练**：可直接应用于预训练模型，避免昂贵训练成本。
- **低额外开销**：降采样计算草稿注意力仅占用很小计算量。
- **理论保障**：提供了近似性分析，增强方法可靠性。
- **硬件友好**：确定性重排序保证稀疏注意力在 GPU 上高效执行。
- **加速显著**：将数十分钟延迟降至数分钟（基于原文描述，实际2倍加速可能对应不同场景）。

### 8. 不足与局限

- **实验覆盖不足**：摘要未报告具体数据集、指标（FVD/IS 等）、分辨率、推理步数，无法全面评估方法的泛化能力。
- **偏差风险**：仅提及“优于现有稀疏注意力方法”，未说明对比基线是否充分（如是否包含全注意力、其他加速方法如 token 剪枝、KV-缓存等）。
- **加速倍数有限**：2倍端到端加速在视频生成领域仍不够极致，尤其原始延迟基数大（数十分钟→数十分钟/2 = 仍然较长）。
- **应用限制**：方法依赖低分辨率草稿注意力的近似质量，可能对某些复杂动态场景（如快速运动、精细纹理）的近似准确性下降，导致生成质量损失。
- **资源信息缺失**：缺乏具体算力环境描述，难以复现或评估实际效率。

（完）
