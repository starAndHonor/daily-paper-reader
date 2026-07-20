---
title: "TraCache: Trajectory-Aware Feature Prediction for Training-Free Diffusion Transformer Acceleration"
title_zh: TraCache：面向免训练扩散Transformer加速的轨迹感知特征预测
authors: "Chenxi Du, Yongheng Deng, Ju Ren, Yaoxue Zhang"
date: 2025-09-12
pdf: "https://openreview.net/pdf?id=td24jA8Kui"
tags: ["query:diff-accel"]
score: 10.0
evidence: 轨迹感知特征预测实现图像和视频的免训练扩散加速
tldr: 扩散Transformer在各种生成任务上表现优异但推理成本高。现有特征重用方法在时间跨度增大时因轨迹漂移导致质量下降。TraCache提出轨迹感知缓存框架，拟合局部特征轨迹并外推精确预测，在激进加速下保持质量，在图像和视频生成上显著超越现有方法。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 扩散Transformer推理成本高，现有特征重用方法在加速时质量下降。
method: 提出TraCache，拟合局部特征轨迹并外推预测，避免漂移。
result: 在图像和视频生成上显著优于现有方法，实现高质量加速。
conclusion: TraCache实现了无需训练的高效加速，且适用于多种生成任务。
---

## Abstract
Diffusion transformers have achieved remarkable success across various generative tasks but suffer from high inference costs. A promising line of work addresses this by reusing features across timesteps to minimize computational redundancy. However, existing methods degrade quality as temporal gaps increase due to trajectory shifts in the feature space. We propose TraCache, a trajectory-aware caching framework that models feature evolution across timesteps. Instead of direct reuse, TraCache fits local feature trajectories and extrapolates accurate predictions, mitigating drift while maintaining quality under aggressive acceleration. Extensive experiments on image and video generation show that TraCache significantly outperforms prior cache-based methods, especially in high skip-rate regimes. For instance, TraCache accelerates PixArt-$\alpha$ by 3.86$\times$ and Open-Sora by 3.74$\times$, while on DiT-XL/2, it provides a 4.51$\times$ acceleration with near-original visual fidelity.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **问题**：扩散Transformer（Diffusion Transformer, DiT）在图像和视频生成任务上取得了卓越性能，但其推理过程需要多次迭代去噪，计算成本极高，难以实际部署。
- **现有方法局限**：为了加速，已有工作通过跨时间步重用特征（如缓存）减少冗余计算。然而，随着时间间隔增大（即激进加速），特征在空间上会发生“轨迹漂移”（trajectory shift），直接重用导致生成质量严重下降。
- **整体含义**：本文提出一种无需额外训练的加速框架，通过建模特征演化轨迹来预测而非直接重用，在保持高质量的同时实现数倍加速，解决了现有缓存方法在高跳跃率场景下的损害问题。

## 2. 论文提出的方法论：核心思想、关键技术细节、算法流程

- **核心思想**：TraCache（Trajectory-aware Cache）将特征在不同时间步的演化视为局部轨迹，通过历史时间步的特征值拟合该轨迹，并外推（extrapolate）得到当前时间步的预测特征，从而避免因直接重用而积累漂移误差。
- **关键技术细节**：
  - **局部轨迹建模**：对于每个特征位置，在连续时间步上收集多个历史特征值，使用低阶多项式（如线性或二次函数）拟合局部变化趋势。
  - **外推预测**：基于拟合的轨迹，外推出当前时间步的特征值，替代完全由模型计算的特征。
  - **缓存机制**：仅每隔一定步数完整计算特征，中间步数使用预测值，从而大幅减少计算量。
- **算法流程（文字说明）**：
  1. 设定跳跃步数（skip rate），决定每几个时间步计算一次真实特征。
  2. 在完整计算的时间步，存储所有特征位置的原始值。
  3. 对于中间步骤，对每个位置，从缓存中取出前若干步的值，拟合局部轨迹函数（例如线性回归），然后将当前时间步的步数代入，得到预测特征。
  4. 使用预测特征替代真实特征执行后续注意力或前馈计算，直到下一个完整计算步更新缓存。
  5. 模型整体仍保持原有架构，无需额外训练。

## 3. 实验设计

- **使用数据集/场景**：
  - **图像生成**：使用 PixArt-α（类文本到图像模型）、DiT-XL/2（类条件图像生成）。
  - **视频生成**：使用 Open-Sora（文本到视频生成）。
- **Benchmark**：主要衡量加速倍数和生成质量（如FID、CLIP score、视觉保真度等，原文虽未列出具体指标，但提到“near-original visual fidelity”）。
- **对比方法**：与之前基于缓存（cache-based）的特征重用方法进行比较（例如DeepCache、Cache等），未明确列出具体名称，但说明“prior cache-based methods”。

## 4. 资源与算力

- **声明**：论文明确提及该方法为“training-free”（免训练），意味着无需在额外数据集上训练模型，仅利用已有的预训练扩散Transformer。因此**没有涉及训练所需算力**。
- **推理资源**：文中**未提供**具体使用的GPU型号、数量、运行时长等硬件细节。可能仅需标准推理硬件（如单张A100等），但无明示。

## 5. 实验数量与充分性

- **实验数量**：覆盖三个典型模型（PixArt-α、DiT-XL/2、Open-Sora），涉及图像和视频两个任务。文中提到“extensive experiments”，但未给出消融实验的具体组数。根据ICLR会议论文的常规，应该包含不同跳跃率对比、与先前方法的定量比较、可能的消融（如轨迹拟合阶数、缓存窗口大小等），但摘要中未呈现详细结果。
- **充分性和公平性**：
  - 涵盖了不同模型和任务，一定程度上说明了方法的通用性。
  - 对比对象是同类缓存方法，对比公平。
  - 但缺乏对更多基线（如蒸馏、紧凑模型）的比较，且没有给出资源消耗（如额外内存、计算开销）的量化对比，可能不够全面。
  - 由于是ICLR 2026被拒稿件，可能存在实验不足或瑕疵（如未在更大规模模型、更高分辨率上验证）。

## 6. 论文的主要结论与发现

- **结论**：TraCache通过轨迹感知的外推预测，显著优于现有缓存类方法，尤其在激进加速（高跳跃率）下仍能保持接近于原始模型的生成质量。
- **具体数据**：
  - PixArt-α：加速3.86倍。
  - Open-Sora：加速3.74倍。
  - DiT-XL/2：加速4.51倍，且视觉保真度接近原始。
- **发现**：特征在时间步上的演化存在局部的平滑轨迹，可以被低阶模型有效拟合，外推预测的误差远小于直接重用引起的漂移累积。

## 7. 优点

- **无需训练**：直接对预训练模型进行推理加速，无需额外数据或微调，实用性强。
- **方法简洁有效**：仅需在推理时增加少量拟合计算，即可大幅减少主计算量，复杂度低。
- **通用性**：同时适用于图像和视频生成，涵盖多种扩散Transformer架构（如DiT及其变体）。
- **高质量加速**：在4倍左右加速下仍保持近似原始质量，远超之前缓存方法。

## 8. 不足与局限

- **实验覆盖有限**：仅在三类模型上测试，未在更多扩散Transformer（如U-ViT、MDT等）以及更复杂的任务（如高分辨率图像、长视频生成）中验证。
- **资源与效率细节缺失**：未说明轨迹拟合的额外计算开销与原始计算缩减的比值，也未比较总推理时间/内存占用，可能导致公平性疑问。
- **偏差风险**：可能对特定跳跃率或架构进行了调优，泛化到其他设置时效果未知。另外，元数据标明该论文在ICLR 2026被拒，可能反映了审稿人对其新颖性或实验充分性的疑虑。
- **应用限制**：仅适用于基于时间步迭代的扩散模型，对于非自回归或单步生成模型（如GAN、一致性模型）不适用。
- **理论基础**：轨迹拟合的合理性缺乏理论保证，仅依赖经验观察，可能在某些退化场景失效。

（完）
