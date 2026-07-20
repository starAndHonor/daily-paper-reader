---
title: "Z-Cache: Accelerating Diffusion Transformers via  Self-Reflection"
title_zh: Z-Cache：通过自反射加速扩散Transformer
authors: "Zegang Cheng, Zhikai Wang, Jiacheng Liu, Chang Zou, Junjie Chen, Yue Ma, Zhiyuan Ma, Linfeng Zhang"
date: 2025-09-13
pdf: "https://openreview.net/pdf?id=2VWQKehe0D"
tags: ["query:diff-accel"]
score: 10.0
evidence: 通过自反射的特征缓存实现免训练加速
tldr: 扩散Transformer计算开销大。现有特征缓存方法在加速时导致质量下降。Z-Cache观察到缓存误差在每个完整计算步骤后急剧减小，据此预测未来缓存步骤的特征，实现高质量保持的加速，无需重新训练。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 扩散Transformer推理成本高，特征缓存导致质量下降。
method: 提出Z-Cache，利用自反射机制预测未来缓存步骤的特征，保持生成质量。
result: 在保持高质量的同时实现显著加速。
conclusion: Z-Cache提供了一种无需训练即可加速扩散Transformer的方法。
---

## Abstract
Diffusion transformers have become the most powerful models for visual generation, but still suffer from massive computation costs. To solve this problem, feature caching has been proposed to cache the features of diffusion models in the previous computation steps and then reuse them in the following caching steps, which brings significant acceleration but also degradation in generation quality. To address this problem, this paper proposes Z-cache as a feature caching method that can maintain high-quality generation through self-reflection. Concretely, we observe that the error from feature caching tends to be sharply reduced after each full computation. Based on this observation, Z-Cache is designed to first predict the features in the future caching steps and then perform a full computation. After that, Z-Cache returns to the caching steps and re-predicts them based on the previous and the current computation steps, which brings correction in features.  Experiments demonstrate that with Z-Cache, diffusion transformers achieve comparable generation quality to the original model  but with faster inference speed, for instance, 5.53$\times$ acceleration on FLUX-dev for text-to-image generation. Our codes have been released in the supplementary materials and will be released on GitHub.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义

- **研究动机**：扩散Transformer（Diffusion Transformers）已成为视觉生成领域最强大的模型，但其推理过程计算开销巨大，严重限制了实际部署效率。
- **核心问题**：现有特征缓存方法（feature caching）通过复用前序计算步骤的特征来加速，但会导致生成质量显著下降。
- **整体含义**：本文旨在提出一种无需重新训练（training-free）的特征缓存加速方法，在保持与原始模型相当生成质量的同时，实现大幅度的推理加速。

## 2. 论文提出的方法论

- **核心思想**：基于一个关键观察——特征缓存产生的误差在每个完整的计算步骤之后会急剧减小。利用这一特性，Z-Cache通过“自反射”（self-reflection）机制预测未来缓存步骤的特征，并利用完整计算步骤的结果对之前的预测进行修正。
- **关键技术细节**：
  1. **预测阶段**：Z-Cache首先根据当前完整计算步骤的特征，预测未来若干缓存步骤中的特征。
  2. **完整计算步骤**：执行一次完整的模型计算，获得精确的特征图。
  3. **重预测与修正**：回到之前缓存步骤的位置，基于当前完整计算步骤与之前计算步骤的特征，重新预测缓存步骤的特征，从而修正缓存误差。
- **算法流程（文字说明）**：
  1. 初始化：在第一步执行完整计算，获得特征。
  2. 对于后续步骤，交替执行：
     - 使用前一步完整计算的特征，预测接下来若干步的缓存特征（缓存模式）。
     - 在预测缓存步骤结束后，执行一次完整计算。
     - 利用本次完整计算的特征与上一个完整计算的特征，对之前所有缓存步骤的特征进行重新预测与修正。
  3. 重复上述过程直到生成结束。
- **公式/算法**：摘要中未给出具体数学公式，但算法逻辑如上所述。

## 3. 实验设计

- **数据集/场景**：图像生成任务，具体包括文本到图像生成（text-to-image generation）。文中明确以 FLUX-dev 模型进行文本到图像生成实验。
- **基准（Benchmark）**：与原始未加速的扩散Transformer模型进行对比，评估生成质量（如FID、CLIP score等常见指标，但摘要未列出具体指标数值）和推理速度（加速倍数）。
- **对比方法**：主要与已有的特征缓存方法进行对比（因摘要未列举具体方法名称，推测可能对比了如“传统特征缓存”、“FC”等基线）。

## 4. 资源与算力

- **明确提及的信息**：论文在摘要末尾提到代码已作为补充材料提交，并将在GitHub上开源。但**未明确说明**使用的GPU型号、数量、训练时长等算力资源。
- **备注**：由于本方法属于免训练加速（training-free），可能仅需推理阶段的GPU资源，但论文未给出具体配置。

## 5. 实验数量与充分性

- **实验数量**：摘要只给出了一个具体结果（在FLUX-dev上实现5.53×加速），未提及在其他模型或数据集上的实验。因此实验数量较少，可能只包含单一模型上的验证。
- **充分性评价**：实验不够充分。虽然结果展示了显著的加速比，但缺乏在更多扩散Transformer架构（如DiT、SD3等）上的对比，也未提供生成质量指标（如FID、CLIP Score）的量化对比，无法全面评估质量保持效果。消融实验、超参数分析等均未在摘要中体现。
- **客观性与公平性**：仅凭摘要信息无法判断实验的公平性（例如是否与基线方法采用相同设置）。论文被ICLR 2026拒稿，可能说明实验或方法存在不足。

## 6. 论文的主要结论与发现

- Z-Cache通过自反射机制可以在无需重新训练的前提下，显著加速扩散Transformer的推理过程。
- 在FLUX-dev文本到图像生成任务上，实现了5.53倍的加速，同时生成质量与原始模型相当。
- 关键发现是：特征缓存误差在每个完整计算步骤后急剧降低，为预测-修正策略提供了理论依据。

## 7. 优点

- **免训练**：无需对模型进行任何微调或训练，直接适用于预训练扩散Transformer，降低了部署成本。
- **高质量保持**：通过自反射修正特征缓存误差，解决了传统缓存方法导致生成质量下降的问题。
- **加速显著**：以FLUX-dev为例达到5.53倍加速，效果突出。
- **思路新颖**：将误差衰减现象与预测-修正机制结合，具有一定的学术创新性。

## 8. 不足与局限

- **实验覆盖极窄**：仅在一个模型（FLUX-dev）上报告了结果，未在多个主流扩散Transformer（如DiT、SD3、PixArt-α等）以及多个任务（如视频生成、图像编辑）上验证，泛化能力存疑。
- **质量指标缺失**：未报告生成质量的定量指标（如FID、CLIP Score、人工评估等），仅声称“comparable generation quality”，证据不足。
- **对比基线不充分**：未提及与哪些特征缓存方法对比，无法判断是否超越了SOTA。
- **消融研究缺乏**：未分析不同预测步长、修正策略、完整计算间隔等超参数的影响。
- **潜在偏差风险**：可能仅在特定设置下表现良好，存在报道偏差（仅报告最好结果）。
- **被拒稿事实**：论文被ICLR 2026拒绝，提示方法可能存在未被披露的弱点或实验不完整。
- **未讨论计算开销**：自反射机制增加了额外的重预测步骤，实际加速比的计算是否扣除了这部分时间？摘要未说明。

（完）
