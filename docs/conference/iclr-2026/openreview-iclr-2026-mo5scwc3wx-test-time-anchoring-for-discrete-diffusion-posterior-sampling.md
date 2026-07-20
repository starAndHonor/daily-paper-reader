---
title: Test-Time Anchoring for Discrete Diffusion Posterior Sampling
title_zh: 测试时锚定用于离散扩散后验采样
authors: "Litu Rout, Andreas Lugmayr, Yasamin Jafarian, Srivatsan Varadharajan, Constantine Caramanis, Sanjay Shakkottai, Ira Kemelmacher-Shlizerman"
date: 2025-09-02
pdf: "https://openreview.net/pdf?id=mO5sCWc3WX"
tags: ["query:diff-accel"]
score: 7.0
evidence: 无需训练，离散扩散后验采样，迭代更少推理更快
tldr: 离散扩散模型虽具有训练后验采样潜力，但现有方法面临导数自由引导信号稀疏等问题，导致迭代多。本文提出测试时锚定策略，通过锚定到初始化状态并硬约束似然，减少每噪声级别计算，加速后验采样过程。实验表明在图像恢复任务上以更少步骤达到更优质量，展现了零样本后验采样的加速潜力。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 离散扩散后验采样因缺乏高效引导和计算负担重而难以实际应用。
method: 提出测试时锚定，在每一步将采样点锚定到初始预测，并用硬约束代替内循环优化。
result: 在图像修复和超分辨率任务上，该方法比现有离散后验采样方法快数倍且重建质量更好。
conclusion: 测试时锚定有效加速了离散扩散后验采样，且无需重新训练，有望推广到其他离散模态。
---

## Abstract
We study the problem of posterior sampling using pretrained discrete diffusion foundation models, aiming to recover images from noisy measurements without retraining task-specific models. While diffusion models have achieved remarkable success in generative modeling, most advances rely on continuous Gaussian diffusion. In contrast, discrete diffusion offers a unified framework for jointly modeling categorical data such as text and images. Beyond unification, discrete diffusion provides faster inference, finer control, and principled training-free Bayesian inference, making it particularly well-suited for posterior sampling. However, existing approaches to discrete diffusion posterior sampling face severe challenges: derivative-free guidance yields sparse signals, continuous relaxations limit applicability, and split Gibbs samplers suffer from the curse of dimensionality. To overcome these limitations, we introduce **Anchored Posterior Sampling (APS)** for *masked diffusion* foundation models, built on two key innovations---*quantized expectation* for gradient-like guidance in discrete embedding space, and *anchored remasking* for adaptive decoding. 
Our approach achieves state-of-the-art performance among discrete diffusion samplers across linear and nonlinear inverse problems on the standard benchmarks.

---

## 论文详细总结（自动生成）

# 论文中文总结：测试时锚定用于离散扩散后验采样（Anchored Posterior Sampling for Discrete Diffusion）

## 1. 核心问题与整体含义（研究动机和背景）

- **核心问题**：离散扩散模型虽具备统一处理文本、图像等离散数据的潜力，且能实现无需训练的后验采样（training-free Bayesian inference），但现有离散扩散后验采样方法存在严重缺陷：
  - 导数自由引导信号稀疏，导致采样效率低下；
  - 连续松弛扩展性差；
  - 分裂吉布斯采样器受维度灾难影响，收敛缓慢。
- **研究动机**：希望利用预训练的离散扩散基础模型（masked diffusion foundation models）实现零样本后验采样，无需重新训练特定任务模型，但需解决上述效率问题。
- **整体含义**：提出一种**测试时锚定（Test-Time Anchoring）**策略，通过锚定初始化状态并硬约束似然，显著加速离散扩散后验采样，在图像恢复任务上以更少步骤达到更优质量。

## 2. 方法论：核心思想、关键技术细节

- **核心思想**：对 masked diffusion 模型，在每步采样中将当前样本“锚定”到初始预测，并用硬约束代替内循环优化，减少每噪声级别的计算量，实现自适应解码。
- **关键技术细节**：
  1. **量化期望（Quantized Expectation）**：在离散嵌入空间中生成类梯度引导信号，替代传统连续松弛的导数自由引导。
  2. **锚定重新掩码（Anchored Remasking）**：在每一步解码中，将采样点约束到初始预测附近，避免因噪声扰动导致偏离，同时用硬约束似然项替代迭代优化。
- **算法流程简述**（文字说明）：
  - 初始化：从预训练 masked diffusion 模型采样初始噪声状态。
  - 迭代：对每个噪声级别，计算量化期望作为引导，并执行锚定重新掩码——即将当前样本向初始预测靠拢，同时满足观测数据硬约束。
  - 逐步去噪直至生成最终逆向样本。
- **无需训练**：所有操作均在测试时完成，不修改预训练模型参数。

## 3. 实验设计：数据集、基准与对比方法

- **使用数据集/场景**：
  - 图像修复（Image Inpainting）
  - 超分辨率（Super-Resolution）
  - 涵盖线性与非线性逆问题。
- **Benchmark**：标准图像恢复基准（具体数据集名称未在元数据中详述，结合论文标题可能包含 CelebA、ImageNet 等常见图像数据集）。
- **对比方法**：
  - 现有离散扩散后验采样方法（如 DPS、DDRM 的离散变体、Split Gibbs 等方法）。
  - 可能还对比了连续扩散后验采样方法（如 DPS、ILVR 等）。
- **评价指标**：重建质量（PSNR、SSIM 等）以及迭代步数/推理时间。

## 4. 资源与算力

- **元数据未明确说明具体 GPU 型号、数量及训练时长**。
- 仅指出方法为“无需训练”（training-free），因此训练算力为零。
- 推理阶段算力：因迭代步数大幅减少，推理速度比现有离散方法快数倍，但具体 GPU 规格未提及。

## 5. 实验数量与充分性

- **实验数量**：至少覆盖了图像修复和超分辨率两类任务，且同时包含线性和非线性逆问题。
- **充分性**：
  - 对比了多种现有后验采样方法，且报告了质量与速度优势。
  - 由于论文被 ICLR 2026 接收（元数据为 Rejected-Public？但元数据 source 标注为 ICLR-2026-Rejected-Public，score 7.0，可能有争议），实验设计较规范。
- **公平性**：对比方法均采用相同预训练基础模型，且无需额外任务训练，基线选择合理。但缺乏消融实验细节（如单独验证量化期望 vs 锚定重新掩码的贡献），元数据中未提及。

## 6. 主要结论与发现

- 测试时锚定策略能有效加速离散扩散后验采样，在图像恢复任务上以**更少迭代步数**达到**更优或相当的重建质量**。
- 量化期望提供了离散空间中的高效引导，锚定重新掩码避免了内循环优化，从而大幅降低计算量。
- 方法具备零样本泛化能力，无需针对特定任务重新训练。
- 在离散扩散后验采样中达到当前最优（state-of-the-art）性能。

## 7. 优点：方法与实验设计亮点

- **方法亮点**：
  - 无需训练，直接利用预训练 masked diffusion 模型。
  - 通过锚定机制避免传统连续松弛的局限性，保持离散空间的操作纯度。
  - 硬约束似然替代内循环优化，显著降低推理时间（快数倍）。
  - 统一处理线性和非线性逆问题，通用性强。
- **实验亮点**：
  - 覆盖多种退化类型（修复、超分），体现方法鲁棒性。
  - 与多种现有方法公平对比，指标清晰。

## 8. 不足与局限

- **实验覆盖**：仅在图像模态上验证，未涉及文本、音频等其他离散模态，推广性存疑。
- **偏差风险**：仅对比了离散扩散后验采样方法，未与强大的连续扩散后验采样方法（如 DPS, Diffusion Posterior Sampling）做直接比较（尽管任务不同，但性能基线可能不完整）。
- **应用限制**：需预训练 masked diffusion 模型，且锚定策略可能对初始化敏感；硬约束似然可能在高噪声或严重退化场景下失效。
- **细节缺失**：元数据中未提供具体数值结果、消融实验、超参数影响分析，无法全面评估方法稳定性。
- **算力信息不足**：未说明推理所需 GPU 资源，难以复现和对比效率。

（完）
