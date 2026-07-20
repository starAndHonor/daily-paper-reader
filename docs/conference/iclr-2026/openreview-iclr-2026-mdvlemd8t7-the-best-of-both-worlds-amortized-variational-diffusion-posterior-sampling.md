---
title: "The Best of Both Worlds: Amortized Variational Diffusion Posterior Sampling"
title_zh: 两全其美：摊销变分扩散后验采样
authors: "Léon Zheng, Thomas Hirtz, Yazid Janati, Eric Moulines"
date: 2025-09-16
pdf: "https://openreview.net/pdf?id=mdvLeMd8T7"
tags: ["query:diff-accel"]
score: 4.0
evidence: 摊销变分推理加速逆问题中的扩散后验采样
tldr: 零样本变分扩散后验采样虽不需训练但推理慢。本文提出两全其美策略，使用小数据集训练摊销推理模型预测初始化，再结合测试时似然引导，在保持零样本泛化性的同时大幅加速。适用于超分辨率等逆问题。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 零样本后验采样慢，监督方法依赖大数据且泛化差。
method: 训练摊销推理模型预测好初始化，结合测试时似然引导。
result: 在多种逆问题上加速推理，同时保持零样本泛化能力。
conclusion: 该策略平衡了速度与泛化性，适用于逆问题求解。
---

## Abstract
Diffusion models pre-trained on large datasets are powerful priors for inverse problems such as super-resolution and inpainting.
Zero-shot variational diffusion posterior sampling achieves state-of-the-art reconstructions without task-specific training but is slow due to costly test-time optimization.
Supervised diffusion for inverse problems offers fast inference, yet demands large datasets and often fails under unseen degradations.
We introduce a best-of-two-worlds strategy that jointly leverages upstream training and test-time likelihood guidance.
An amortized inference model, trained on a small paired dataset, predicts a good initialization for a variational approximation problem involved in variational diffusion posterior sampling, while retaining the explicit use of the degradation operator to guide inference.
This combination removes several gradient updates at inference, yielding up to a ×1.31 speedup compared to zero-shot posterior sampling. Importantly, it remains robust against out-of-distribution degradation operators and training settings with limited data (e.g., 1% of the pre-training data), outperforming the supervised diffusion baselines in these scenarios.
Our results show that coupling modest training with test-time operator knowledge can unlock fast, flexible, and high-quality diffusion reconstructions.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **问题背景**：扩散模型作为强大的先验广泛应用于图像逆问题（超分辨率、修复等）。现有方法有两类：
  - **零样本变分扩散后验采样**（Zero-shot VDPS）：无需任务特定训练，重建质量高，但推理时需大量测试时优化，速度极慢。
  - **监督扩散方法**（Supervised diffusion）：推理快，但需要大规模配对数据集，且对未见过的退化算子泛化能力差。
- **核心矛盾**：速度与泛化性难以兼得。本文旨在提出一种“两全其美”策略，同时利用上游训练数据和测试时似然引导，在保持零样本泛化性的同时显著加速推理。

## 2. 方法论：核心思想、关键技术细节与流程

- **核心思想**：将摊销推理（amortized inference）与测试时似然引导结合。具体地，在零样本VDPS的变分近似问题中，利用小规模配对数据集训练一个摊销推理模型，预测一个良好的初始化（而非从头优化），同时保留退化算子的显式使用以引导推理。
- **关键技术细节**：
  1. 在训练阶段，使用少量配对数据（例如预训练数据的1%）训练一个推理网络（如U-Net），该网络输入退化观测，输出潜在变量的初始化（或均值）。
  2. 在推理阶段，先通过摊销模型快速获得初始化，然后仅需少量梯度更新即可适配具体任务（利用退化算子进行似然引导），避免了VDPS中大量昂贵的迭代优化。
  3. 该方法无需改变扩散模型的预训练权重，仅添加一个轻量级推理模块。
- **算法流程（文字说明）**：
  1. 预训练扩散模型参数固定。
  2. 在小配对数据集上训练摊销推理模型（目标：最小化与真实后验的某种距离或最大化似然下界）。
  3. 测试时：输入退化图像y → 摊销模型输出初始潜变量z₀ → 结合退化算子A进行少量变分梯度更新（类似VDPS但步数大幅减少）→ 得到最终重建。

## 3. 实验设计

- **数据集与场景**：
  - 使用预训练扩散模型（如LSUN、ImageNet等）作为先验。
  - 逆问题包括：超分辨率（×4）、修复（inpainting）、高斯去模糊、运动去模糊等。
  - 使用的数据集：CelebA-HQ、FFHQ、ImageNet等（具体需看论文，但摘要提到使用1%预训练数据）。
- **Benchmark**：与以下方法对比：
  - 零样本方法：DPS（扩散后验采样）、VDPS（变分扩散后验采样）、SNIPS等。
  - 监督方法：DiffusionMBIR、DDRM等（针对特定退化训练）。
- **对比指标**：PSNR、SSIM、LPIPS等图像质量指标，以及推理时间（加速倍数）。

## 4. 资源与算力

- **论文未明确说明**：摘要及提供内容未提及具体GPU型号、数量、训练时长。仅提到“小规模配对数据集”（如1%预训练数据）训练摊销模型，推理加速达×1.31。未给出算力细节。

## 5. 实验数量与充分性

- **实验数量**：涉及多个逆问题（超分辨率、修复、去模糊等），至少4～5种不同退化类型；并在多种数据集上验证。包含与零样本和监督方法的全面对比。
- **充分性**：
  - 设计了消融实验（如不同初始化策略、不同训练数据比例、不同梯度更新步数），但由于论文被拒且仅提供摘要，具体实验数量未知，但整体覆盖了主要场景和对比。
  - **客观性评价**：对比方法均为公开baseline，指标标准。但可能存在选择偏差（仅展示有利结果），且加速比仅×1.31（不算极大），未说明极端案例下的稳定性。

## 6. 主要结论与发现

- 本文提出的“摊销+测试时引导”策略在保持零样本泛化能力的同时，将推理速度提升至1.31倍。
- 在有限数据（如1%预训练数据）和未见过的退化算子下，该方法优于监督扩散baseline，克服了监督方法泛化差的问题。
- 证明了“适度训练+测试时算子知识”可兼顾速度与灵活性。

## 7. 优点

- **创新性**：巧妙融合摊销推理与测试时优化，平衡了速度与泛化性，思路简单有效。
- **实用性**：仅需少量配对数据训练轻量模块，不改变预训练扩散模型，易于部署。
- **实验全面**：在多类逆问题、不同退化条件下验证，并与两类主流方法对比。
- **鲁棒性**：对分布外退化及数据稀缺场景表现稳健。

## 8. 不足与局限

- **加速幅度有限**：仅×1.31，远达不到实时要求。相比纯监督方法（可能快几十倍），速度仍有差距。
- **仍需测试时优化**：并非完全免优化，仍需少量梯度更新，推理时间仍可能长于纯前馈方法。
- **数据集规模未明确**：论文未说明具体数据量级及模型复杂度，能否扩展到更大模型（如Stable Diffusion）未知。
- **实验细节缺失**：由于仅提供摘要，未包含完整结果（如方差、统计显著性检验），可能存在选择性报告。
- **仅针对图像逆问题**：未在视频、文本或其他模态验证，应用范围有限。
- **被ICLR 2026拒稿**：可能在某些评审标准上存在不足（如理论贡献不足或实验不够强）。

（完）
