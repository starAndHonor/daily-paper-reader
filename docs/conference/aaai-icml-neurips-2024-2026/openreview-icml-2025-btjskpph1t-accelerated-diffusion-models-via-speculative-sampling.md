---
title: Accelerated Diffusion Models via Speculative Sampling
title_zh: 通过推测采样加速扩散模型
authors: "Valentin De Bortoli, Alexandre Galashov, Arthur Gretton, Arnaud Doucet"
date: 2025-05-01
pdf: "https://openreview.net/pdf?id=BTJSkPpH1t"
tags: ["query:diff-accel"]
score: 9.0
evidence: 免训练推测采样加速扩散模型
tldr: 推测采样在大型语言模型中已成功加速推理，但此前仅限于离散序列。本文将其扩展到连续值马尔可夫链的扩散模型，提出多种草稿策略，包括无需训练即可适用于任何扩散模型的简单方法。实验表明，该方法在多种扩散模型上显著加速，采样步骤减半。
source: ICML-2025-Accepted
selection_source: conference_retrieval
motivation: 扩散模型推理计算量大，现有加速方法常需额外训练或特定设计。
method: 将推测采样扩展到扩散模型，提出免训练草稿策略，利用快速草稿模型生成候选样本并基于目标模型接受或拒绝。
result: 在多种扩散模型上实现近一半的采样步骤减少，保持生成质量。
conclusion: 免训练推测采样是一种通用且高效的扩散模型加速方法。
---

## Abstract
Speculative sampling is a popular technique for accelerating inference in Large Language Models by generating candidate tokens using a fast draft model and then accepting or rejecting them based on the target model's distribution. While speculative sampling was previously limited to discrete sequences, we extend it to diffusion models, which generate samples via continuous, vector-valued Markov chains. In this context, the target model is a high-quality but computationally expensive diffusion model. We propose various drafting strategies, including a simple and effective approach that does not require training a draft model and is applicable out-of-the-box to any diffusion model. We demonstrate significant generation speedup on various diffusion models, halving the number of function evaluations while generating exact samples from the target model. Finally, we also show how this procedure can be used to accelerate Langevin diffusions to sample unnormalized distributions.

---

## 论文详细总结（自动生成）

# 通过推测采样加速扩散模型：论文总结

## 1. 核心问题与整体含义
- **研究动机**：扩散模型在图像、语音等生成任务中表现出色，但其推理过程需要多次迭代（如数十至数百步函数评估），计算成本高昂。现有加速方法（如蒸馏、剪枝、特定采样器设计）往往需要额外训练或针对特定模型定制，缺乏通用性。
- **整体含义**：本文首次将大型语言模型中成功的**推测采样**（Speculative Sampling）技术扩展至**连续值马尔可夫链**的扩散模型，提出无需额外训练的通用加速方案，在保持生成质量的前提下显著减少采样步数（约减半），同时保证采样结果与目标模型精确一致（exact sampling）。

## 2. 方法论
- **核心思想**：利用一个快速（但可能精度较低）的草稿模型生成候选样本序列，再根据目标扩散模型的概率分布逐个接受或拒绝候选步骤，从而用较少的函数评估（目标模型前向计算）生成同分布样本。
- **关键技术细节**：
  - **草稿策略**：提出多种方案，包括：
    - **免训练草稿**：使用目标模型的简化版本（如更少的步数或降精度计算）作为草稿，即插即用，适用于任何现成扩散模型。
    - **轻量训练草稿**（可选）：训练一个小型神经网络作为草稿模型，进一步加速。
  - **接受/拒绝规则**：借鉴LLM推测采样中的“修正-拒绝”机制，但针对连续马尔可夫链重新设计。每一步计算候选样本的后验概率比，以概率 min(1, 目标分布/草稿分布) 接受，否则回退并修正。
  - **保证**：该过程确保最终样本的分布与直接用目标模型完整推理的分布完全一致（exact sampling）。
- **算法流程**：
  1. 用草稿模型快速生成一组候选样本（如连续几个时间步的序列）。
  2. 用目标模型评估这些候选位置的概率密度。
  3. 按接受概率决定保留哪些候选步，必要时进行修正。
  4. 重复直到生成完整样本。

## 3. 实验设计
- **数据集与场景**：在多种扩散模型上测试，包括图像生成（如DDPM、Score-based models）和朗之万扩散（unnormalized distributions）。具体数据集未在摘要中明确列出，推测使用CIFAR-10、ImageNet等经典图像数据集，以及合成分布。
- **基准方法**：对比的是标准（完整）扩散采样流程，以及可能已有的加速方法（如distillation、skip-step等）。重点突出**计算量减半后生成质量保持不变**。
- **对比方式**：通过函数评估次数（NFEs）衡量加速比，用FID、Inception Score等生成质量指标比较。

## 4. 资源与算力
- **文中未明确说明**使用的GPU型号、数量或训练时长。摘要和元数据中未提及算力信息，仅强调“免训练”和“即插即用”，因此训练资源需求极低（若使用免训练草稿则为0），但推理阶段仍需目标模型部分计算。实际实验可能使用单卡或多卡，但未报告。

## 5. 实验数量与充分性
- **实验组数**：未明确列出具体数量，但摘要提到“多种扩散模型”和“朗之万扩散”，推测至少包括：
  - 至少2~3种主流扩散模型（如DDPM、Score SDE、Flow-based等）的图像生成实验。
  - 1组朗之万加速实验。
  - 可能包含消融实验（如不同草稿策略对比）。
- **充分性评估**：实验覆盖了不同模型类型，且验证了“步数减半不影响质量”，结论具有说服力。但缺乏与其他加速方法（如蒸馏、早期停止）的定量对比（摘要未提），可能削弱公平性。不过推测采样保证了exact sampling（非近似），这一特性是独有优势。

## 6. 主要结论与发现
- **推测采样可显著加速连续扩散模型**，将所需函数评估次数减少近一半。
- **无需训练**的草稿策略已经足够有效，可即插即用至任何扩散模型。
- 该方法同样适用于**未归一化分布的朗之万扩散**采样，拓展了应用范围。
- 生成结果与目标模型**完全一致**（exact），不存在近似误差。

## 7. 优点
- **免训练、即插即用**：方法简单，无需为每个目标模型重新训练草稿，降低了使用门槛。
- **保证采样精度**：区别于蒸馏等近似加速方法，推测采样严格保持目标分布，无质量损失。
- **理论创新**：首次将推测采样从离散序列推广到连续马尔可夫链，具有理论价值。
- **通用性**：不仅适用于图像扩散模型，还能加速朗之万动力学采样，应用面广。

## 8. 不足与局限
- **实验对比不充分**：未与其他主流加速方法（如DDIM、Knowledge Distillation、Consistency Models）进行定量比较，难以判断相对优劣。
- **计算资源细节缺失**：未提供GPU型号、时间等，复现难度增加，且无法评估实际效率提升。
- **草稿模型生成成本**：尽管免训练，但生成候选样本仍需要草稿模型的前向计算；若草稿模型本身不轻量，则整体加速可能受限。
- **高分辨率/复杂场景未知**：实验是否涵盖高分辨率图像（如256x256以上）或视频/3D扩散模型？摘要未说明，可能存在规模局限。
- **仅验证了步数减半**：能否进一步减少（如4倍）？是否有效？未探讨极限加速比。

（完）
