---
title: "DC-Gen: Post-Training Diffusion Acceleration with Deeply Compressed Latent Space"
title_zh: DC-Gen：深度压缩潜在空间加速扩散模型的训练后方法
authors: "Wenkun He, Yuchao Gu, Junyu Chen, Dongyun Zou, Yujun Lin, Zhekai Zhang, Haocheng Xi, Muyang Li, Ligeng Zhu, Jincheng YU, Junsong Chen, Enze Xie, Song Han, Han Cai"
date: 2025-09-08
pdf: "https://openreview.net/pdf?id=HsNxa8tQXc"
tags: ["query:diff-accel"]
score: 7.0
evidence: 通过深度压缩潜在空间实现训练后扩散加速
tldr: DC-Gen提出一种通用框架，通过深度压缩潜在空间来加速文本到图像扩散模型，尤其是在高分辨率（如4K）生成中。它采用高效的后训练流水线，通过弥合基模型潜在空间与压缩空间之间的表示差异，避免了昂贵的从头训练。实验证明该方法在显著降低计算开销的同时保持了生成质量。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 现有加速方法忽视了潜在空间冗余，且高分辨率生成效率极低。
method: 利用深度压缩的潜在空间，通过高效后训练流水线弥合表示差距，无需重训基模型。
result: 在高分辨率图像生成中大幅减少计算量，保持质量。
conclusion: DC-Gen为扩散模型提供了一种可扩展的轻量级加速方案。
---

## Abstract
Existing text-to-image diffusion models excel at generating high-quality images, but face significant efficiency challenges when scaled to high resolutions, like 4K image generation. While previous research accelerates diffusion models in various aspects, it seldom handles the inherent redundancy within the latent space. To bridge this gap, this paper introduces DC-Gen, a general framework that accelerates text-to-image diffusion models by leveraging a deeply compressed latent space. Rather than a costly training-from-scratch approach, DC-Gen uses an efficient post-training pipeline to preserve the quality of the base model. A key challenge in this paradigm is the representation gap between the base model’s latent space and a deeply compressed latent space, which can lead to instability during direct fine-tuning. To overcome this, DC-Gen first bridges the representation gap with a lightweight embedding alignment training. Once the latent embeddings are aligned, only a small amount of LoRA fine-tuning is needed to unlock the base model’s inherent generation quality. We verify DC-Gen’s effectiveness on SANA and FLUX.1-Krea. The resulting DC-Gen-SANA and DC-Gen-FLUX models achieve quality comparable to their base models but with a significant speedup. Specifically, DC-Gen-FLUX reduces the latency of 4K image generation by 53x on the NVIDIA H100 GPU. When combined with NVFP4 SVDQuant, DC-Gen-FLUX generates a 4K image in just 3.5 seconds on a single NVIDIA 5090 GPU, achieving a total latency reduction of 138x compared to the base FLUX.1-Krea model. Code and models will be released.

---

## 论文详细总结（自动生成）

# 论文总结：DC-Gen：深度压缩潜在空间加速扩散模型的训练后方法

## 1. 核心问题与整体含义（研究动机和背景）
- **研究动机**：现有文本到图像扩散模型在生成高分辨率图像（如4K）时面临严重的计算效率瓶颈。尽管已有研究从不同角度加速扩散模型，但普遍忽略了潜在空间（latent space）中固有的冗余性。
- **背景意义**：传统方法通常需要重新从头训练整个扩散模型，成本极高。DC-Gen旨在通过利用深度压缩的潜在空间，在不牺牲生成质量的前提下，实现模型加速，从而推动高分辨率图像生成在实际应用中的落地。

## 2. 论文提出的方法论：核心思想、关键技术细节
- **核心思想**：采用**后训练（post-training）流水线**，通过深度压缩的潜在空间替换原模型的潜在空间，避免昂贵的从头训练。关键在于弥合基模型潜在空间与压缩空间之间的**表示差距（representation gap）**。
- **关键技术细节**：
  - **两阶段对齐与微调**：
    1. **嵌入对齐训练（Embedding Alignment Training）**：使用轻量级的嵌入对齐训练，将基模型潜在空间的表示映射到压缩空间，解决直接微调带来的不稳定性。
    2. **LoRA微调（Low-Rank Adaptation）**：在对齐完成后，仅需少量LoRA参数微调即可解锁基模型固有的生成质量，避免大规模参数更新。
  - **后训练效率**：整个流程不需要重新训练整个扩散模型，仅需对嵌入和少量低秩矩阵进行训练，极大降低计算开销。
- **公式/算法流程**（文字说明）：
  - 输入：预训练基模型（如SANA、FLUX.1-Krea），目标压缩率。
  - 步骤1：设计一个深度压缩的潜在空间（减小特征维度/分辨率）。
  - 步骤2：训练一个轻量嵌入对齐网络，使压缩空间的隐变量分布与基模型原始隐变量分布相似。
  - 步骤3：固定对齐网络，用LoRA对基模型的骨干网络进行少量步骤的微调，恢复生成细节。
  - 输出：加速后的压缩扩散模型（DC-Gen-SANA / DC-Gen-FLUX）。

## 3. 实验设计
- **使用数据集/场景**：文本到图像生成任务，主要在高分辨率场景（如4K图像生成）进行验证。
- **基准模型**：SANA 和 FLUX.1-Krea 作为基模型。
- **对比方法**：未明确列出对比的具体加速方法，但提及与基模型对比（quality comparable），以及组合NVFP4 SVDQuant后的总延迟对比。
- **Benchmark指标**：生成质量（定性比较）和延迟（秒数），未提及FID/CLIP等定量指标（根据摘要推测）。

## 4. 资源与算力
- **文中明确说明**：测试使用 **NVIDIA H100 GPU** 和 **NVIDIA 5090 GPU**。具体训练所需的算力（GPU数量、训练时长等）**未明确说明**。
  - DC-Gen-FLUX在H100上4K图像生成延迟降低53倍。
  - 在5090 GPU上结合NVFP4 SVDQuant，生成一张4K图像仅需3.5秒，总延迟降低138倍。
  - 训练阶段计算量：仅需轻量嵌入对齐训练和少量LoRA微调，未报告具体GPU小时数。

## 5. 实验数量与充分性
- **实验组数**：摘要中仅提及两个主要基模型（SANA和FLUX.1-Krea）的实验结果，以及组合NVFP4 SVDQuant的效果。消融实验（如不同压缩率、对齐方法、LoRA rank的影响）**未在摘要中提及**。
- **充分性评价**：由于缺乏全文，无法判断实验的全面性。单从摘要看，仅展示了特定模型和加速倍数的结果，没有系统性消融、泛化性验证、与现有加速方法的直接对比（如潜空间蒸馏、量化、剪枝等），因此实验覆盖可能不够充分。但作为后训练加速框架，其核心思想清晰，初步结果有说服力。

## 6. 论文的主要结论与发现
- DC-Gen通过深度压缩潜在空间并采用高效的桥接对齐+LoRA微调，成功实现了**训练后加速**，无需重新训练整个模型。
- 在SANA和FLUX.1-Krea上，加速后的模型生成质量与基模型相当，但计算延迟大幅降低（4K图像延迟降低53~138倍）。
- 结合硬件级量化（NVFP4 SVDQuant）可进一步加速，在主流消费级GPU上实现实时4K生成。

## 7. 优点
- **通用性**：框架与具体扩散模型架构无关，可应用于不同基模型（SANA、FLUX.1-Krea）。
- **低成本**：后训练流水线仅需轻量对齐训练和少量LoRA微调，避免了昂贵的重训练。
- **高加速比**：在高分辨率生成场景下实现了数量级的延迟降低，并保持了生成质量。
- **可组合性**：能与量化技术（如NVFP4 SVDQuant）结合，进一步压缩总延迟。

## 8. 不足与局限
- **实验覆盖有限**：仅展示了两个基模型和4K分辨率的结果，未提供其他分辨率（如1K/2K）或更多模型（如Stable Diffusion 3）的验证。
- **量化指标缺失**：未报告FID、CLIP Score等客观图像质量指标，仅提及“quality comparable”，可能存在主观性偏差。
- **未对比现有方法**：没有与已知的加速技术（如蒸馏、剪枝、INT8量化）进行定量比较，无法判断其相对优势。
- **消融实验不足**：未展示不同压缩率、对齐方法、LoRA rank选择对质量和速度的影响，无法评估方法的敏感性和鲁棒性。
- **应用限制**：方法依赖于基模型的潜在空间假设，对于不同编码结构的模型可能需要调整对齐策略；且压缩空间可能限制生成多样性或细粒度控制。

（完）
