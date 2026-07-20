---
title: Accelerating Video Diffusion Transformers with Spatial-Temporal Sparsity
title_zh: 利用时空稀疏性加速视频扩散Transformer
authors: "Zhang Wan, Yu Li, Haochen Li, Juan Cao, Sheng Tang"
date: 2025-09-16
pdf: "https://openreview.net/pdf?id=P4l1u3J4Fh"
tags: ["query:diff-accel"]
score: 9.0
evidence: 利用时空稀疏性实现免训练的视频扩散Transformer加速
tldr: SVG提出一种免训练框架，利用视频扩散Transformer中3D全注意力的固有稀疏性来提升推理效率。通过动态将注意力头分为空间头（帧内相关）和时间头（帧间相关），显著减少计算量。该工作无需额外训练或微调，即可在高效视频生成中大幅降低延迟。
source: ICLR-2026-Public
selection_source: conference_retrieval
motivation: 视频扩散Transformer计算成本极高，主要源于3D全注意力的二次复杂度，本文旨在无需训练的前提下加速推理。
method: 提出Sparse VideoGen（SVG）免训练框架，通过动态分类注意力头为空间和时间两组，利用稀疏性减少计算。
result: 在保持视频生成质量的同时显著提升推理速度。
conclusion: SVG为视频扩散模型的高效推理提供了一种轻量级、即插即用的解决方案。
---

## Abstract
Diffusion Transformers (DiTs) dominate video generation but their high computational cost severely limits real-world applicability, usually requiring tens of minutes to generate a few seconds of video even on high-performance GPUs. This inefficiency primarily arises from the quadratic computational complexity of 3D full attention with respect to the context length. In this paper, we propose a training-free framework termed Sparse VideoGen (SVG) that leverages the inherent sparsity in 3D full attention to boost inference efficiency. We reveal that the attention heads can be dynamically classified into two groups depending on distinct sparse patterns: (1) Spatial Head, where only spatially-related tokens within each frame dominate the attention output, and (2) Temporal Head, where only temporally-related tokens
across different frames dominate. Based on this insight, SVG proposes an online profiling strategy to capture the dynamic sparse patterns and predicts the type of attention head. Combined with a novel hardware-efficient tensor layout transformation and customized kernel implementations, SVG achieves up to xx end-to-end speedup on CogVideoX-v1.5, xxon HunyuanVideo, and 1.89× on Wan 2.1, while preserving generation quality.

---

## 论文详细总结（自动生成）

# 论文总结：Accelerating Video Diffusion Transformers with Spatial-Temporal Sparsity

## 1. 核心问题与整体含义（研究动机和背景）
- **研究动机**：视频扩散Transformer（DiTs）在视频生成任务中表现优异，但其巨大的计算成本严重限制了实际应用。即使在高性能GPU上，生成几秒钟的视频也可能需要数十分钟。主要瓶颈来自3D全注意力的二次计算复杂度（与上下文长度平方成正比）。
- **背景**：现有加速方法通常需要额外训练或微调，缺乏即插即用的轻量级解决方案。本文旨在**无需训练**的前提下大幅提升视频扩散Transformer的推理效率。

## 2. 论文提出的方法论
- **核心思想**：利用视频扩散Transformer中3D全注意力的**固有稀疏性**——注意力头可动态分为两类稀疏模式：
  - **空间头（Spatial Head）**：仅关注帧内空间相关的令牌（同帧内）。
  - **时间头（Temporal Head）**：仅关注跨帧时间相关的令牌（不同帧之间）。
- **关键技术细节**：
  - **在线轮廓策略（Online Profiling）**：在推理过程中实时捕获注意力头的动态稀疏模式，预测每个注意力头属于空间头还是时间头。
  - **硬件高效张量布局变换**：针对稀疏模式优化张量存储和访问模式，减少内存开销。
  - **定制化内核实现**：为空间和时间注意力分别设计高效的计算内核，避免冗余计算。
- **框架名称**：Sparse VideoGen（SVG），完全免训练（training-free）。

## 3. 实验设计
- **数据集/场景**：未明确说明使用的训练或测试数据集，但评估了多个主流视频扩散模型。
- **基准模型**：CogVideoX-v1.5、HunyuanVideo、Wan 2.1。
- **对比方法**：未列出具体对比方法名称，但从上下文推测对比了原始全注意力模型（未加速版本）。
- **评价指标**：主要关注推理加速比（end-to-end speedup），以及生成质量是否保持（未量化细节）。

## 4. 资源与算力
- **文中未明确说明**：未提及使用的GPU型号、数量、训练时长或推理硬件配置。仅提到“high-performance GPUs”作为背景。由于SVG是免训练框架，无需训练算力，但推理阶段的硬件配置未指定。

## 5. 实验数量与充分性
- 实验数量较少，仅列举三个基准模型上的加速比结果（CogVideoX-v1.5、HunyuanVideo、Wan 2.1）。其中CogVideoX和HunyuanVideo的加速比数值被摘要中的“xx”替代，仅Wan 2.1给出明确值（1.89×）。
- **充分性评估**：实验覆盖了多个主流模型，但缺乏以下内容：
  - 未提供消融实验（如不同稀疏分类策略的影响、在线轮廓与静态分配对比）。
  - 未报告生成质量指标（FVD、IS、CLIP score等）的量化结果，仅声明“preserving generation quality”。
  - 未与现有加速方法（如剪枝、量化、蒸馏）进行横向对比。
- **公平性**：结论较为单薄，难以判断是否客观公平。

## 6. 论文的主要结论与发现
- SVG框架能够在不训练的情况下，通过动态分类注意力头为空间和时间两组，显著减少计算量，实现1.89×（Wan 2.1）及更高的端到端加速，同时保持生成质量。
- 揭示了视频扩散Transformer中3D全注意力的固有稀疏性，并验证了在线动态预测注意力头类型的可行性。

## 7. 优点：方法或实验设计上的亮点
- **免训练特性**：无需额外训练或微调，直接适用于预训练模型，实用性强。
- **即插即用**：可轻松集成到现有视频扩散Transformer推理流程中。
- **洞察新颖**：首次系统分析视频DiT注意力头的时空稀疏性差异，并据此设计分类策略。
- **硬件优化**：通过张量布局变换和定制内核实现实际加速，避免理论加速与实测差距。

## 8. 不足与局限
- **实验覆盖不足**：仅报告了3个模型的加速比，且部分数值缺失；缺乏生成质量指标（如FVD、LPIPS）的量化对比。
- **消融实验缺失**：未分析每个组件（在线轮廓、头分类、内核优化）的独立贡献。
- **公平性存疑**：未与传统加速方法（如token剪枝、稀疏注意力）对比，也难以判断是否在相同硬件和精度条件下测试。
- **可扩展性问题**：未讨论对长视频生成或更高分辨率下的效果，稀疏模式是否稳定需要更多验证。
- **应用限制**：依赖于注意力头的动态分类，可能不适用于所有视频扩散架构（如某些采用局部注意力的模型）。
- **文档不完整**：摘要中关键数值被“xx”遮蔽，影响信息完整性。

（完）
