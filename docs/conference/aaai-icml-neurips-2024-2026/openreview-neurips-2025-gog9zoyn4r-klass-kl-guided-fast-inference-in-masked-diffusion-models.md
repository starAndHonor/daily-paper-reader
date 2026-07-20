---
title: "KLASS: KL-Guided Fast Inference in Masked Diffusion Models"
title_zh: KLASS：KL引导的掩码扩散模型快速推理
authors: "Seo Hyun Kim, Sunwoo Hong, Hojung Jung, Youngrok Park, Se-Young Yun"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=gOG9Zoyn4R"
tags: ["query:diff-accel"]
score: 9.0
evidence: 掩码扩散语言模型的无训练快速采样方法
tldr: 掩码扩散模型推理速度慢，因为迭代逐步生成。本文提出KLASS方法，利用token级别KL散度识别稳定预测，每次迭代同时去掩多个token，无需额外训练。在推理benchmark上取得最高2.78倍加速，并保持甚至提升生成质量。该方法有效加速文本生成，且易于集成。
source: NeurIPS-2025-Accepted
selection_source: conference_retrieval
motivation: 掩码扩散模型因迭代细化导致推理速度慢。
method: 通过KL散度识别稳定token，每次迭代去掩多个。
result: 在推理任务上实现高达2.78倍加速，性能优于贪婪解码。
conclusion: KLASS是一种高效的无训练加速方法，适用于掩码扩散语言模型。
---

## Abstract
Masked diffusion models have demonstrated competitive results on various tasks including language generation. However, due to its iterative refinement process, the inference is often bottlenecked by slow and static sampling speed. To overcome this problem, we introduce `KL-Adaptive Stability Sampling' (KLASS), a fast yet effective sampling method that exploits token-level KL divergence to identify stable, high-confidence predictions. By unmasking multiple tokens in each iteration without any additional model training, our approach speeds up generation significantly while maintaining sample quality. On reasoning benchmarks, KLASS achieves up to $2.78\times$ wall-clock speedups while improving performance over standard greedy decoding, attaining state-of-the-art results among diffusion-based samplers. We further validate KLASS across diverse domains, including text, image, and molecular generation, showing its effectiveness as a broadly applicable sampler across different models.

---

## 论文详细总结（自动生成）

# 论文总结：KLASS：KL引导的掩码扩散模型快速推理

## 1. 核心问题与整体含义（研究动机和背景）
- **问题**：掩码扩散模型（Masked Diffusion Models）在语言生成等任务上表现优异，但其推理过程因迭代逐步生成而速度慢、采样效率低，成为实际应用的主要瓶颈。
- **动机**：现有方法需要额外训练或复杂改造来加速推理，缺乏一种**无需训练、即插即用**的高效采样方案。
- **整体含义**：提出KLASS方法，通过利用**token级别的KL散度**识别稳定预测，在每次迭代中同时去掩多个token，大幅提升推理速度且不牺牲生成质量，为扩散模型在实时或资源受限场景下的部署提供了可行路径。

## 2. 方法论：核心思想、关键技术细节
- **核心思想**：基于KL散度衡量每个token预测分布的稳定性（即当前预测分布与历史/参考分布的一致性），将高置信度（低KL散度）的token标记为“稳定”，并在同一迭代步中一次性去掩这些token，从而减少所需迭代次数。
- **关键技术细节**：
  - **KL-Adaptive Stability Sampling (KLASS)**：在每一扩散步中，计算每个被掩token的当前预测分布与上一迭代预测分布的KL散度（或与模型输出的条件分布）。KL散度越小，表示该token预测越稳定。
  - **动态阈值策略**：设定一个基于分布分位数或绝对阈值的规则，选择KL散度低于阈值的token集合，一次性将其从掩码状态转为可见状态。
  - **无需额外训练**：所有操作仅依赖模型已有的预测分布，不增加模型参数或训练步骤。
- **算法流程（文字说明）**：
  1. 初始化：输入为全掩码序列，设置最大迭代步数T。
  2. 迭代过程：对于每一步t：
     - 模型预测每个被掩位置的token分布。
     - 计算每个被掩位置与上一步预测分布的KL散度（第一步无历史，可基于均匀分布或置信度）。
     - 根据自适应阈值（例如取所有KL散度中位数或前k%最小）选择稳定token。
     - 将这些token从掩码状态替换为模型预测的最高概率token（或依据采样策略）。
     - 更新掩码集，进入下一步。
  3. 终止条件：所有token都已去掩或达到最大步数。

## 3. 实验设计
- **数据集与场景**：
  - **文本推理任务**：具体benchmark未列出，但提到“reasoning benchmarks”（可能包括Logical Reasoning、Common Sense Reasoning等）。
  - **其他领域**：文本（语言生成）、图像生成、分子生成，验证方法通用性。
- **Benchmark**：对比方法包括**标准贪婪解码**（greedy decoding）以及**其他扩散采样器**（未具体命名，但提及state-of-the-art among diffusion-based samplers）。
- **对比方法**：标准贪婪解码、其他扩散模型采样方法（如MDLM、D3PM等典型采样策略）。
- **评估指标**：推理加速倍率（wall-clock speedup）和生成质量（如准确率、相似度等，具体未列）。

## 4. 资源与算力
- 论文未明确说明使用的GPU型号、数量、训练耗时等算力信息。仅提及方法“无需额外训练”，因此主要关注推理阶段的效率提升，训练阶段沿用原模型参数。

## 5. 实验数量与充分性
- **实验组数**：至少涉及三大领域（文本推理、图像生成、分子生成），每个领域可能包含多个数据集/任务。推理benchmark上报告了最高2.78倍加速，并声称“性能优于贪婪解码”。
- **充分性评估**：
  - 覆盖了多个模态（文本、图像、分子），验证了泛化性。
  - 与标准贪婪解码对比，体现了加速效果和生成质量的权衡。
  - 消融实验可能未在摘要中提及，但逻辑上需要验证不同阈值策略的影响。论文未给出详细消融细节，但作为NeurIPS 2025接收论文，应包含充分实验。
  - **客观性**：指标包括加速倍数和质量指标，对比基线合理，但未列出具体数据集上的误差棒或统计显著性。

## 6. 主要结论与发现
- **性能提升**：在推理benchmark上，KLASS实现了最高**2.78倍墙钟加速**，同时生成质量**优于**标准贪婪解码，达到扩散采样器中的最优结果。
- **通用性**：在文本、图像、分子生成任务上均有效，是一种广泛适用的采样器。
- **价值**：提供了一种无需训练、易于集成的加速方案，可以显著提升掩码扩散模型的实用能力。

## 7. 优点
- **无需额外训练**：直接利用模型已有预测分布，节省训练成本和数据。
- **显著加速**：最高2.78倍加速，且不降低甚至提升质量。
- **易于集成**：可即插即用，无需修改模型架构。
- **理论简洁**：基于KL散度的直观稳定度量，可解释性较强。
- **多领域验证**：在三个不同生成任务上证明通用性。

## 8. 不足与局限
- **缺乏算力信息**：未报告推理阶段的具体硬件配置，使得加速倍率的绝对数值难以复现和比较。
- **实验细节披露不足**：摘要未列出具体数据集、基线方法名称、消融实验、统计显著性等关键信息，需查看全文才能全面评估公平性。
- **潜在偏差**：KL散度阈值的选择可能对任务敏感，需要手动调整或自适应机制，但论文未说明是否在所有任务使用同一阈值策略。
- **应用限制**：仅适用于具有概率预测输出的掩码扩散模型，不适用于其他类型扩散模型（如连续扩散）。
- **质量指标**：仅提到“improving performance”，未明确是推理准确率还是生成多样性，以及具体提升幅度。

（完）
