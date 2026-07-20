---
title: Curvature-Aware Residual Prediction for Stable and Faithful Diffusion Transformer Acceleration Under Large Sampling Intervals
title_zh: 曲率感知残差预测：大采样间隔下扩散变换器的稳定忠实加速
authors: "Bukun Huang, Benlei Cui, Zhizeng Ye, Xuemei Dong, Songlin Yang, Yihang Bao, Hui Xue, Longtao Huang, Haiwen Hong, Jingqun Tang"
date: 2025-09-05
pdf: "https://openreview.net/pdf?id=qZPp8acMVS"
tags: ["query:diff-accel"]
score: 8.0
evidence: 通过预测免训练加速扩散变换器
tldr: 本文针对扩散变换器推理缓慢的问题，提出曲率感知残差预测方法，有效应对大采样间隔下特征变化的误差累积。该方法在不重新训练的前提下，显著提升了缓存加速的稳定性和保真度，实现了高效高质量的生成。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 现有缓存方法在大采样间隔下误差累积严重，预测方法受限于截断误差。
method: 提出曲率感知残差预测，利用二阶信息校正特征变化。
result: 在大采样间隔下实现了稳定且保真的加速生成。
conclusion: 为扩散变换器提供了一种高效、无需训练的推理加速方案。
---

## Abstract
Diffusion Transformers have achieved remarkable performance in generative tasks, yet their large model size and multi-step sampling requirement lead to prohibitively expensive inference. Conventional caching methods reuse features across timesteps to reduce computation, but introduce approximation errors that accumulate during denoising—a problem exacerbated under large sampling intervals where significant feature variations amplify errors. Recent prediction-based approaches (e.g., TaylorSeers) improve efficiency but remain limited by sensitivity to feature variations across distant timesteps and the inherent truncation errors of Taylor expansions.
To address these issues, we propose a novel **C**urvature-**A**ware **R**esidual **P**rediction (CARP) framework, which shifts the prediction target from raw features to residual updates within Diffusion Transformer blocks.  We observe that residuals exhibit more stable and predictable dynamics over time compared to raw features, making them better suited for extrapolation. Our approach employs a rational function-based predictor, whose theoretical superiority over polynomial approximations is rigorously established: the numerator performs linear extrapolation using adjacent features, while the denominator incorporates discrete curvature to adaptively modulate the strength and behavior of the prediction. This design effectively captures the alternation between gradual refinements and abrupt transitions in diffusion denoising trajectories. Additionally, we introduce a curvature-guided gating mechanism that regulates the use of predicted values, enhancing robustness under large sampling steps. Extensive experiments on FLUX, DiT-XL/2, and Wan2.1 demonstrate our method's effectiveness. For instance, at 20 denoising steps, we achieve up to 2.88× speedup on FLUX, 1.46× on DiT-XL/2, and 1.72× on Wan2.1, while maintaining high quality across FID, CLIP, Aesthetic, and VBench metrics, significantly outperforming existing feature caching methods. In user studies on FLUX, CARP receives nearly 25\% more preference than the second-best method. These results underscore the advantages of residual-targeted prediction combined with a rational function-based extrapolator for efficient, training-free acceleration of diffusion models.

---

## 论文详细总结（自动生成）

# 论文中文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **问题**：扩散变换器（Diffusion Transformers）在生成任务中性能卓越，但其模型庞大且需要多步采样，导致推理成本极高。
- **现有方法局限**：
  - 传统缓存方法（caching）通过跨时间步复用特征来减少计算，但会引入近似误差，并在大采样间隔下因特征剧烈变化而加剧误差累积。
  - 近期基于预测的方法（如TaylorSeers）虽提升了效率，但仍受限于对远时间步特征变化的敏感性以及泰勒展开的固有截断误差。
- **研究目标**：在不重新训练的前提下，实现扩散变换器在**大采样间隔**下的稳定、忠实加速，同时保持生成质量。

## 2. 方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：将预测目标从原始特征（raw features）转向扩散变换器块内的**残差更新（residual updates）**。观察发现残差随时间演化更稳定、更可预测，更适合外推。
- **关键技术**：提出**曲率感知残差预测（CARP）** 框架，包含两个核心组件：
  1. **基于有理函数的预测器**：
     - 分子：使用相邻时间步的特征进行线性外推。
     - 分母：引入离散曲率（discrete curvature）自适应调节预测的强度和行为。
     - 理论优势：比多项式近似（泰勒展开）更优，能有效捕捉扩散去噪轨迹中“逐步细化”与“突变”的交替过程。
  2. **曲率引导的门控机制**：
     - 根据曲率信息调控预测值的使用程度，增强大采样步长下的鲁棒性。
- **算法流程简述**：在每个扩散时间步，对扩散变换器块的残差进行预测，而不是直接预测原始特征；通过曲率信息决定是否以及多大程度使用预测值，从而减少计算量并抑制误差累积。

## 3. 实验设计

- **场景与模型**：在三个主流扩散变换器模型上进行测试：
  - FLUX（图像生成）
  - DiT-XL/2（图像生成）
  - Wan2.1（可能为视频生成，文中未明确但属于生成任务）
- **评估指标**：FID、CLIP Score、Aesthetic Score、VBench（视频基准）。
- **对比方法**：现有特征缓存方法（feature caching methods），但未具体列出名称（如可能包括TaylorSeers等）。
- **用户研究**：在FLUX上进行了用户偏好研究，CARP获得比第二优方法高出近25%的偏好率。
- **加速效果**：
  - 在20步去噪下，FLUX加速2.88倍，DiT-XL/2加速1.46倍，Wan2.1加速1.72倍。
- **总体评价**：实验覆盖了不同模型、不同任务类型，并包含定量指标和用户研究，较为充分。

## 4. 资源与算力

- 论文摘要及元数据中**未明确说明**使用了多少GPU、型号、训练时长或推理硬件细节。仅提到“无需训练”（training-free），因此主要体现推理时的加速效果，不涉及训练资源。具体算力信息缺失。

## 5. 实验数量与充分性

- **实验组数**：至少包含三个不同模型的加速对比实验，每个模型可能有多组采样步长设置；同时进行了用户研究（定性评估）。
- **消融研究**：摘要未明确提及消融实验，但“曲率引导的门控机制”暗示可能有相关消融，但未在摘要中展示具体结果。
- **充分性**：实验覆盖了图像和视频生成任务，使用了多种标准指标，并与现有方法对比，整体设计较为客观公平。但缺乏对缓存方法具体名称的列举及超参数敏感性的详细分析。

## 6. 主要结论与发现

- CARP通过将预测目标从原始特征转为残差，并利用有理函数预测器和曲率门控，能够在大采样间隔下实现**稳定且忠实**的加速，误差累积显著降低。
- 在FLUX上获得最高2.88倍加速，且生成质量优于现有缓存方法；用户研究证实CARP有更高的视觉偏好。
- 该方法无需重新训练，是一种高效、即插即用的推理加速方案。

## 7. 优点

- **理论创新**：首次从曲率角度设计预测器，有理函数优于多项式近似，理论上有严谨论证。
- **免训练**：无需额外训练或微调，即插即用，适用于现有扩散变换器模型。
- **鲁棒性强**：曲率门控机制有效应对大采样步长下的不稳定问题。
- **通用性**：在图像和视频生成模型（FLUX、DiT、Wan2.1）上均有效，证明方法通用。
- **用户偏好显著**：主观评估中大幅领先第二名，说明实际生成质量更好。

## 8. 不足与局限

- **实验细节不透明**：论文摘要未公开对比方法的具体名称、实验设置（如采样步数范围、硬件配置），可能影响可复现性。
- **消融实验缺失**：未明确展示曲率门控、预测目标选择等组件的消融结果，难以定量评估每个模块的贡献。
- **仅针对扩散变换器**：方法是否适用于其他架构（如U-Net扩散模型）未验证，泛化性有待拓展。
- **大采样间隔的定义**：未明确“大采样间隔”的具体数值范围，可能导致其他研究者难以复现相同条件。
- **计算资源未说明**：虽然无需训练，但推理加速的硬件环境未知，无法评估其实际部署成本。

（完）
