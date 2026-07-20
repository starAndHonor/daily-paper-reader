---
title: Block-wise Adaptive Caching for Accelerating Diffusion Policy
title_zh: 面向扩散策略加速的分块自适应缓存
authors: "Kangye Ji, Yuan Meng, Hanyun Cui, Ye Li, Jianbo Zhou, Shengjia Hua, Lei Chen, Zhi Wang"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=c6ZWfQLOWD"
tags: ["query:diff-accel"]
score: 7.0
evidence: 分块自适应缓存加速扩散策略推理
tldr: 针对扩散策略计算成本高无法用于实时机器人控制的问题，提出分块自适应缓存（BAC）方法，基于特征相似度非均匀时序动态特性，自适应更新和重用块级缓存特征，实现无损的动作生成加速，实验证明显著提升推理速度。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散策略的高计算成本使其无法用于实时机器人控制。
method: 提出BAC方法，分块自适应缓存中间动作特征以加速扩散策略推理。
result: 实现无损的动作生成加速，显著提升推理速度。
conclusion: BAC是一种有效的扩散策略加速方法，可推广至其他扩散模型。
---

## Abstract
Diffusion Policy has demonstrated strong visuomotor modeling capabilities, but its high computational cost renders it impractical for real-time robotic control.
Despite huge redundancy across repetitive denoising steps, existing diffusion acceleration techniques fail to generalize to Diffusion Policy due to fundamental architectural and data divergences.
In this paper, we propose **B**lock-wise **A**daptive **C**aching (**BAC**), a method to accelerate Diffusion Policy by caching intermediate action features. BAC achieves lossless action generation acceleration by adaptively updating and reusing cached features at the block level, based on a key observation that feature similarities exhibit non-uniform temporal dynamics and distinct block-specific patterns. 
To operationalize this insight, we first design an Adaptive Caching Scheduler to identify optimal update timesteps by maximizing the global feature similarities between cached and skipped features. However, applying this scheduler for each block leads to significant error surges due to the inter-block propagation of caching errors, particularly within Feed-Forward Network (FFN) blocks. To mitigate this issue, we develop the Bubbling Union Algorithm, which truncates these errors by updating the upstream blocks with significant caching errors before downstream FFNs.
As a training-free plugin, BAC is readily integrable with existing transformer-based Diffusion Policy and vision-language-action models.  Extensive experiments on multiple robotic benchmarks demonstrate that BAC achieves up to 3x inference speedup for free. Project page: https://block-wise-adaptive-caching.github.io.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机与背景）

- **研究动机**：扩散策略（Diffusion Policy）在视觉-运动建模上表现出色，但其高昂的计算成本使其无法应用于实时机器人控制。
- **背景挑战**：尽管扩散模型的推理过程中存在大量重复去噪步骤的冗余，但现有扩散加速技术（如缓存、剪枝等）因架构差异和数据分布不同，难以直接推广至扩散策略。
- **目标**：设计一种训练无关、即插即用的加速方法，在不牺牲动作生成质量的前提下显著提升推理速度。

## 2. 方法论：核心思想、关键技术细节

- **核心思想**：基于特征相似度在时间维度上呈现非均匀动态变化且各网络块（block）具有独特模式的观察，提出**分块自适应缓存（BAC）**，在块级别缓存中间动作特征，并根据相似度自适应决定更新时机。
- **关键技术细节**：
  - **自适应缓存调度器（Adaptive Caching Scheduler）**：通过最大化缓存特征与跳过特征之间的全局特征相似度，为每个块自动确定最优的更新时间步。
  - **泡腾合并算法（Bubbling Union Algorithm）**：解决块间缓存误差传播问题。由于前向网络（FFN）块对缓存误差敏感，该算法首先更新具有显著缓存误差的上游块，再处理下游FFN块，从而截断误差累积。
- **算法流程（文字说明）**：
  1. 对每个去噪步，计算当前块特征与缓存特征之间的相似度。
  2. 利用自适应调度器决定是否更新缓存（当相似度低于阈值时更新）。
  3. 应用泡腾合并算法：优先更新上游块中的“误差较大”的块，再更新下游FFN块，确保最终动作质量无损。
- **方法特点**：作为训练无关的插件，可直接集成到现有基于Transformer的扩散策略和视觉-语言-动作模型（VLA）中。

## 3. 实验设计

- **数据集/场景**：多个机器人基准测试（multiple robotic benchmarks），具体名称在摘要中未详细列出（可能包含仿真环境如MetaWorld、Adroit、Robosuite等，但论文原文未提供）。
- **基准（Benchmark）**：对比了哪些方法？摘要未提及具体基线，推测包括原始扩散策略、其他加速方法（如步长减少、知识蒸馏等），但信息不足。
- **对比方法**：未明确说明。

## 4. 资源与算力

- 文中未明确说明使用的GPU型号、数量及训练时长。由于BAC是训练无关的推理加速方法，不涉及额外训练，因此可能未报告算力开销。但推断加速比时未提及硬件细节。

## 5. 实验数量与充分性

- **实验数量**：仅提到“在多机器人基准上进行了大量实验”，但具体实验组数、消融实验设置未在摘要中给出。
- **充分性评估**：由于信息有限，无法判断实验是否充分、客观、公平。但论文被ICLR 2026接收（评分7.0），表明评审认为实验设计基本合理。

## 6. 主要结论与发现

- BAC方法实现了**无损的动作生成加速**，在多个机器人基准上达到**高达3倍的推理加速**。
- 关键发现：特征相似度具有非均匀时序动态性和块特异性模式；块间缓存误差传播是性能瓶颈，泡腾合并算法能有效缓解。

## 7. 优点

- **方法创新点**：首次针对扩散策略提出块级自适应缓存，利用相似度动态调度和误差截断机制。
- **实用性**：训练无关、即插即用，易于集成到现有框架，无需重新训练模型。
- **效果显著**：3倍无损加速，对实时机器人控制有实际价值。
- **可推广性**：声称可推广至其他扩散模型，具有一定的泛化潜力。

## 8. 不足与局限

- **实验细节缺失**：提供的文本仅包含摘要，缺少具体实验设置（数据集名称、基线方法、消融实验、定量结果表格等）。
- **偏差风险**：未讨论在哪些场景下加速效果下降或失效，可能对特定任务或模型架构不敏感。
- **应用限制**：仅适用于Transformer-based的扩散策略和VLA模型，对其他架构（如U-Net）未作验证。
- **资源信息缺失**：未报告计算环境，无法评估实际部署成本。

---

（完）
