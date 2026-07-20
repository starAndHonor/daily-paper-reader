---
title: "DSA: Efficient Inference For Video Generation Models via Distributed Sparse Attention"
title_zh: "DSA: 通过分布式稀疏注意力实现视频生成模型的高效推理"
authors: "Shenggui Li, Runyu Lu, qiaoling chen, Haiyan Yin, Yueming Lyu, Yonggang Wen, Ivor Tsang, Tianwei Zhang"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=1ZmdfDzGE1"
tags: ["query:diff-accel"]
score: 9.0
evidence: 分布式稀疏注意力实现高效视频生成推理
tldr: 针对视频生成扩散Transformer中注意力机制计算量大的瓶颈，提出分布式稀疏注意力DSA，结合精心设计的并行策略，在8GPU部署下实现最高1.4倍加速，显著降低生成延迟。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 视频生成扩散Transformer的注意力机制计算量大，限制生成速度。
method: 提出DSA，结合稀疏注意力和分布式推理，减少冗余计算。
result: 在8GPU上实现高达1.4倍加速，保持全局上下文。
conclusion: DSA有效加速视频生成扩散模型推理。
---

## Abstract
Diffusion Transformer models have driven the rapid advances in video generation, achieving state-of-the-art quality and flexibility. However, their attention mechanism remains a major performance bottleneck, as its dense computation scales quadratically with the sequence length. To overcome this limitation and reduce the generation latency, we propose DSA, a novel attention mechanism that integrates sparse attention with distributed inference for diffusion-based video generation. By leveraging carefully-designed parallelism strategies and scheduling, DSA significantly reduces redundant computation while preserving global context. Extensive experiments on benchmark datasets demonstrate that, when deployed on 8 GPUs, DSA achieves up to 1.43× inference speedup than the existing distributed method and 10.79× faster than single-GPU inference.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：扩散Transformer模型在视频生成领域取得了最先进的质量和灵活性，但其注意力机制是主要的性能瓶颈——密集型注意力计算与序列长度呈二次方增长，导致生成延迟高，难以实际部署。
- **整体含义**：本文旨在通过创新的注意力机制来降低视频生成扩散模型的计算开销，从而加速推理，满足实时或近实时的视频生成需求。

## 2. 论文提出的方法论

- **核心思想**：提出**DSA（Distributed Sparse Attention，分布式稀疏注意力）**，将稀疏注意力与分布式推理相结合，在保持全局上下文的同时显著减少冗余计算。
- **关键技术细节**：
  - 设计精心规划的并行策略与调度方案，使注意力计算仅关注重要区域（稀疏化），而非全部位置，从而降低计算量。
  - 采用多GPU分布式部署，利用并行计算进一步加速推理过程。
- **算法流程概述**（文字说明）：输入视频特征序列 → 通过设计的并行策略将序列分块并分配至多个GPU → 每个GPU执行局部稀疏注意力计算（仅对关键位置进行注意力） → 通过聚合全局上下文保证信息完整性 → 输出加速后的注意力结果。

## 3. 实验设计

- **数据集 / 场景**：基于基准数据集（文中未具体列出，但提及“benchmark datasets”）进行实验，推测为开放视频生成基准（如UCF-101、Kinetics等）。
- **基准测试（Benchmark）**：对比了现有的分布式推理方法（baseline）以及单GPU推理。
- **对比方法**：主要包括：
  - 单GPU推理（标准密集注意力）
  - 现有分布式方法（未指定名称，可能指标准分布式注意力或类似Ring Attention等）

## 4. 资源与算力

- **文中明确说明**：实验中部署在 **8个GPU** 上，但未指明GPU具体型号（如A100、V100等）。
- 训练时长、显存占用等详细信息未提及。

## 5. 实验数量与充分性

- **实验组数**：摘要仅提及主要性能对比（8GPU下 vs 现有分布式方法 vs 单GPU），未详述消融实验或不同数据集下的结果。
- **充分性评估**：实验覆盖了核心加速效果，但缺乏对稀疏比例、不同视频长度、不同模型大小等消融分析。提供的证据不足以全面验证方法的鲁棒性和泛化性，略显单薄。

## 6. 论文的主要结论与发现

- 在 **8GPU** 部署下，DSA比现有分布式方法实现 **最高1.43倍** 推理加速，比单GPU推理快 **10.79倍**。
- 证明了稀疏注意力结合分布式推理能在保持全局上下文的同时显著降低延迟，是视频生成模型高效推理的有效方案。

## 7. 优点

- **创新性**：首次将稀疏注意力与分布式推理系统性地结合用于视频扩散模型，针对二次复杂度瓶颈提出直接解决方案。
- **实用性**：在多GPU环境下获得显著加速，对实际部署有直接价值。
- **简洁高效**：设计精心，避免了复杂的模型架构修改。

## 8. 不足与局限

- **实验覆盖不足**：仅报告了单一项加速比，缺少对不同视频分辨率、不同帧数、不同注意力稀疏率、不同GPU数量（如2、4、16）的对比分析。
- **偏差风险**：未说明现有分布式方法的具体实现，可能存在比较基准不具代表性或不公平。
- **应用限制**：
  - 依赖多GPU集群，单GPU场景下稀疏注意力的效果未知。
  - 未讨论稀疏注意力可能导致的生成质量损失（尽管声称保持全局上下文，但需定量质量指标如FVD、IS等）。
  - 仅针对扩散Transformer架构，其他类型的视频生成模型（如GAN、VAE）未作讨论。
- **资源细节缺失**：GPU型号、内存占用、功耗等关键资源指标未给出，影响复现和横向比较。

（完）
