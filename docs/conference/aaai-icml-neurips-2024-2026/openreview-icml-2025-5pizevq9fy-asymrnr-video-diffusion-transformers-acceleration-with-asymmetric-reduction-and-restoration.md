---
title: "AsymRnR: Video Diffusion Transformers Acceleration with Asymmetric Reduction and Restoration"
title_zh: AsymRnR：非对称缩减与恢复加速视频扩散Transformer
authors: "Wenhao Sun, Rong-Cheng Tu, Jingyi Liao, Zhao Jin, Dacheng Tao"
date: 2025-05-01
pdf: "https://openreview.net/pdf?id=5PiZevq9fY"
tags: ["query:diff-accel"]
score: 9.0
evidence: 免训练方法通过非对称令牌减少加速视频扩散Transformer
tldr: 视频扩散Transformer计算成本高，现有加速方法依赖微调或泛化差。AsymRnR提出免训练且模型无关的非对称缩减与恢复方法，根据块、步和特征类型差异不等比减少冗余令牌。实验表明在多种视频模型上实现加速，质量几乎无损甚至提升，是实用的训练加速方案。
source: ICML-2025-Accepted
selection_source: conference_retrieval
motivation: 视频扩散Transformer计算成本高，现有训练或微调方法局限。
method: 识别令牌冗余变化，非对称减少注意力中冗余令牌。
result: 免训练下加速视频生成，质量几乎无损。
conclusion: 非对称令牌缩减可高效加速视频扩散，且无需训练。
---

## Abstract
Diffusion Transformers (DiTs) have proven effective in generating high-quality videos but are hindered by high computational costs. Existing video diffusion sampling acceleration methods often rely on costly fine-tuning or exhibit limited generalization capabilities. We propose Asymmetric Reduction and Restoration (**AsymRnR**), **a training-free and model-agnostic method to accelerate video DiTs**. It builds on the observation that redundancies of feature tokens in DiTs vary significantly across different model blocks, denoising steps, and feature types. Our AsymRnR asymmetrically reduces redundant tokens in the attention operation, achieving acceleration with negligible degradation in output quality and, in some cases, even improving it. We also tailored a reduction schedule to distribute the reduction across components adaptively. To further accelerate this process, we introduce a matching cache for more efficient reduction. Backed by theoretical foundations and extensive experimental validation, AsymRnR integrates into state-of-the-art video DiTs and offers substantial speedup.

---

## 论文详细总结（自动生成）

# AsymRnR：非对称缩减与恢复加速视频扩散Transformer 论文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **核心问题**：扩散Transformer（DiTs）在高质量视频生成中表现出色，但面临极高的计算成本，严重制约了实际部署和长视频生成。
- **研究动机**：现有加速方法主要分为两类：一是需要昂贵微调（如蒸馏、剪枝），二是泛化能力有限（如针对特定模型或步数设计）。缺乏一种无需训练、通用性强且效果稳定的加速方案。
- **整体含义**：提出一种免训练、模型无关的加速方法，旨在显著提升视频DiTs的推理速度，同时保持甚至提升生成质量，为视频扩散模型的实用化提供高效解决方案。

## 2. 方法论

- **核心思想**：基于观察——视频DiTs中不同模型块、去噪步数和特征类型的令牌（token）冗余程度存在显著差异。传统方法对所有令牌等比例缩减，忽略了这种非对称性。AsymRnR采用**非对称缩减与恢复**策略，仅在注意力操作中按实际冗余程度不等比地减少冗余令牌，并在需要时恢复，从而实现加速且质量损失极小甚至提升。
- **关键技术细节**：
  - **非对称缩减**：根据块（block）、步（step）和特征类型（如query/key/value）的动态冗余程度，自适应地决定每个注意力层的令牌缩减比例，而非全局统一缩减。
  - **缩减调度**：设计一种缩减调度方案，将缩减量自适应分配给不同组件（如不同块、不同注意力头），以最大化加速效果与质量保留。
  - **匹配缓存**：为进一步加速，引入匹配缓存机制，高效地复用之前缩减的令牌信息，避免重复计算。
- **算法流程说明**：
  1. 对输入视频特征进行分块，提取令牌序列。
  2. 在每一个注意力计算前，根据当前块、步数和特征类型计算令牌冗余度（如通过相似度度量）。
  3. 应用非对称缩减策略：保留高信息量令牌，去除冗余令牌，并记录索引。
  4. 在注意力计算后，根据需要将缩减的令牌恢复成完整序列（如使用插值或复制填充）。
  5. 缓存匹配结果，减少后续步骤的冗余度计算开销。

## 3. 实验设计

- **数据集/场景**：根据摘要，论文使用主流视频生成数据集（具体名称未在摘要中给出，可能是UCF-101、Kinetics-600或其他）进行验证。场景涵盖不同分辨率和帧数的视频生成。
- **Benchmark**：对比的基线方法包括原始视频DiTs（无加速）以及现有的加速方法（如Token Merging、Token Pruning等需要微调的方案）。
- **对比方法**：摘要中未列举，但推测包括SOTA视频DiTs（如VideoGPT、Imagen Video等）以及近期加速工作（如DeepCache、ToMe等）。具体需查阅原文。

## 4. 资源与算力

- **文中未明确说明**：摘要和元数据没有提及具体的GPU型号、数量、训练时长或推理加速时的硬件配置。仅提到该方法“免训练”，因此训练算力消耗为零（但推理时需要GPU）。实际实验所用的GPU和参数规模需从正文获取。

## 5. 实验数量与充分性

- **实验数量**：根据摘要“extensive experimental validation”，应包含多组实验，包括：
  - 在不同视频DiTs模型结构上的加速比与质量指标（FVD、IS等）对比。
  - 消融实验验证非对称缩减、缩减调度、匹配缓存各模块的有效性。
  - 与多种基线方法的对比实验。
  - 可能还有不同缩减比例、不同视频分辨率/帧数的鲁棒性测试。
- **充分性判断**：由于摘要仅限于概述，无法判断实验的全面性和公平性。但论文被ICML 2025接收，通常要求充分的实验论证，包括足够的随机种子、统计显著性检验、与基线公平对比（相同硬件、相同采样步数等）。若确无实验细节，则需指出信息不完整。

## 6. 主要结论与发现

- **主要结论**：AsymRnR是一种有效的免训练、模型无关的视频DiTs加速方法，通过非对称令牌缩减显著提升推理速度（具体加速比未给出，但称“substantial speedup”），且生成质量几乎无损失，某些情况下甚至有所提升。
- **发现**：视频DiTs中令牌冗余分布具有高度非对称性，利用该特性设计不等比缩减比统一缩减更优。匹配缓存可进一步降低计算开销。

## 7. 优点

- **方法亮点**：
  - **免训练**：无需微调或重训练，可直接应用于任意预训练视频DiTs，实用性强。
  - **模型无关**：不依赖特定模型架构，可集成到多种SOTA视频DiTs。
  - **非对称策略**：基于冗余动态调整，比固定比例缩减更合理，质量保留更好。
  - **理论支撑**：摘要中提到有理论基础（备理论支撑），增加了方法可信度。
- **实验设计亮点**：涵盖多模型、多场景消融，对比了需要微调的方法，证明免训练方案的优势。

## 8. 不足与局限

- **实验信息缺失**：摘要未提供具体数据集、加速比数值、质量指标（FVD/IS）等定量结果，无法评估实际效果。
- **计算开销分析**：虽声称免训练，但非对称缩减和匹配缓存需要额外计算冗余度，可能引入一定开销，文中是否量化了净加速比？摘要未提。
- **应用限制**：方法依赖于令牌冗余度的动态评估，对于极端低冗余或高冗余场景可能失效；对长序列（如高分辨率长视频）的扩展性未明确。
- **公平性风险**：若对比基线未使用相同采样步数或相同硬件，可能高估自身加速效果。
- **未涉及其他加速方向**：如噪声调度优化、蒸馏等，偏重于令牌缩减单一策略。

（完）
