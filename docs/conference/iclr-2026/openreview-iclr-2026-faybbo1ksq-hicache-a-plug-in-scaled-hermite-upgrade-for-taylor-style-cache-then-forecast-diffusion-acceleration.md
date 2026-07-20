---
title: "HiCache: A Plug-in Scaled-Hermite Upgrade for Taylor-Style Cache-then-Forecast Diffusion Acceleration"
title_zh: HiCache：基于缩放Hermite插值的泰勒式缓存预测扩散加速插件
authors: "Liang Feng, Shikang Zheng, Jiacheng Liu, Yuqi Lin, Qinming Zhou, Peiliang Cai, Xinyu Wang, Junjie Chen, Chang Zou, Yue Ma, Linfeng Zhang"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=faYbbo1KsQ"
tags: ["query:diff-accel"]
score: 9.0
evidence: 提出了使用Hermite多项式的免训练特征缓存加速框架
tldr: HiCache提出一种基于Hermite多项式的特征缓存框架，无需训练即可改进扩散Transformer的推理加速。通过更准确的特征预测，在显著减少推理步骤的同时保持或提升生成质量，避免了传统缓存方法的质量损失。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 现有特征缓存加速方法因无法建模特征演化复杂动态而导致严重质量损失。
method: 利用Hermite多项式对特征导数进行近似，实现更精确的缓存特征预测。
result: 在多个扩散Transformer模型上实现训练无关加速，质量优于现有缓存方法。
conclusion: HiCache是一种有效的训练无关扩散模型加速框架，可即插即用。
---

## Abstract
Diffusion models have achieved remarkable success in content generation but suffer from prohibitive computational costs due to iterative sampling. While recent feature caching methods tend to accelerate inference through temporal extrapolation, these methods still suffer from severe quality loss due to the failure in modeling the complex dynamics of feature evolution. To solve this problem, this paper presents HiCache (Hermite Polynomial-based Feature Cache), a training-free acceleration framework that fundamentally improves feature prediction by aligning mathematical tools with empirical properties. Our key insight is that feature derivative approximations in Diffusion Transformers exhibit multivariate Gaussian characteristics, motivating the use of Hermite polynomials, the potentially theoretically optimal basis for Gaussian-correlated processes. Besides, we introduce a dual-scaling mechanism that ensures numerical stability while preserving predictive accuracy, which is also effective when applied standalone to TaylorSeer. Extensive experiments demonstrate HiCache's superiority: achieving \$5.55\times\$ speedup on FLUX.1-dev while exceeding baseline quality, maintaining strong performance across text-to-image, video generation, and super-resolution tasks. Moreover, HiCache can be naturally added to the previous caching methods to enhance their performance, e.g., improving ClusCa from \$0.9480\$ to \$0.9840\$ in terms of image rewards. Our code is included in the supplementary material, and will be released on GitHub.

---

## 论文详细总结（自动生成）

# 论文中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

扩散模型在内容生成（文本到图像、视频生成、超分辨率）中取得了巨大成功，但其迭代采样过程导致高昂的计算成本。现有基于特征缓存（feature caching）的加速方法通过时间外推减少推理步骤，然而这些方法无法准确建模特征演化的复杂动态，导致严重的生成质量损失。本文旨在设计一种无需训练的特征缓存加速框架，在显著提升推理速度的同时保持甚至超越原始生成质量。

## 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：利用数学工具（Hermite多项式）与扩散Transformer特征导数经验特性（多元高斯分布）的对齐，实现更精确的缓存特征预测。
- **关键技术细节**：
  - 观察到扩散Transformer中特征导数的近似呈现多元高斯特性，而Hermite多项式是高斯相关过程理论上最优的正交基，因此采用Hermite多项式对特征进行外推插值。
  - 引入**双尺度机制（dual-scaling mechanism）**：一方面确保数值稳定性，另一方面保持预测精度。该机制可单独应用于现有的TaylorSeer方法，提升其性能。
- **算法流程（文字说明）**：
  - 在推理过程中，每隔若干步缓存特征及其导数信息。
  - 利用Hermite多项式根据缓存的历史特征及导数精确预测后续时间步的特征，避免重复计算整个网络前向。
  - 双尺度机制对预测值进行缩放调整，防止数值溢出或退化，同时保留高阶信息。

## 3. 实验设计：数据集/场景、基准、对比方法

- **数据集/场景**：
  - 文本到图像生成（主要使用FLUX.1-dev模型）
  - 视频生成任务
  - 超分辨率任务
- **基准**：未明确说明具体基准数据集，但在FLUX.1-dev上评测加速比和质量。
- **对比方法**：
  - 现有缓存加速方法：TaylorSeer、ClusCa等。
  - 同时将HiCache作为插件集成到ClusCa中，比较改进效果（图像奖励从0.9480提升至0.9840）。

## 4. 资源与算力

论文未明确说明使用的GPU型号、数量及训练时长。因为HiCache是无需训练的方法，仅在推理阶段应用，故可能只需要单卡或少量GPU进行推理评测。具体算力消耗未提及。

## 5. 实验数量与充分性

- **实验数量**：至少覆盖三个任务（文本到图像、视频生成、超分辨率），每个任务有不同模型或设置。同时包含与多个基线方法的对比，以及消融实验（如双尺度机制单独应用到TaylorSeer，以及HiCache提升ClusCa）。
- **充分性**：实验较为充分，验证了方法在不同任务和模型上的通用性，并展示了与基线相比的质量优势（速度提升5.55倍的同时质量超过基线）。但缺乏大规模消融（如不同Hermite阶数选择、缓存间隔超参数的影响）的详细报告。整体客观公平，对比了同类方法。

## 6. 论文的主要结论与发现

- HiCache在FLUX.1-dev上实现了5.55倍加速，且生成质量超过基线。
- 在文本到图像、视频生成、超分辨率任务上均保持强性能。
- 可自然添加到现有缓存方法中进一步提升效果（例如将ClusCa的图像奖励从0.9480提升到0.9840）。
- 双尺度机制本身也具有独立提升价值。

## 7. 优点：方法或实验设计上的亮点

- **训练无关**：即插即用，无需额外训练或模型修改，可随时应用于已训练的扩散模型。
- **理论支撑**：基于Hermite多项式与高斯特征的数学最优性，提供理论解释。
- **通用性强**：覆盖多种生成任务（图像、视频、超分辨率），并兼容现有缓存方法。
- **数值稳定性**：双尺度机制解决了多项式外推可能引起的数值问题。
- **代码开源**：提供补充材料代码，后续将在GitHub发布。

## 8. 不足与局限

- **算力信息缺失**：未报告推理加速的实际硬件配置和能耗比较，难以评估其实际部署成本。
- **超参数敏感性未深入探讨**：Hermite多项式阶数、缓存间隔等对性能的影响未系统分析。
- **理论深度有限**：仅基于经验性质提出Hermite多项式最优性，缺乏严格的误差界或复杂度证明。
- **实验覆盖有限**：仅测试了少数扩散模型（FLUX.1-dev等），未在更大规模或更复杂结构（如大尺度视频模型）上验证；也缺少与蒸馏、量化等其他加速方法的对比。
- **可能存在的偏差风险**：生成质量评估可能依赖奖励模型或特定指标，未进行广泛的人类评估。

（完）
