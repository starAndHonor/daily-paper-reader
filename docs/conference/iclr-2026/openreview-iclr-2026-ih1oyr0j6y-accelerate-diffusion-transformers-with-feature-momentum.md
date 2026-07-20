---
title: Accelerate Diffusion Transformers with Feature Momentum
title_zh: 利用特征动量加速扩散Transformer
authors: "Jiaxin Fang, Chang Zou, Jiacheng Liu, Yuanhuiyi Lyu, Xuming Hu, Linfeng Zhang"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=ih1oyr0j6y"
tags: ["query:diff-accel"]
score: 8.0
evidence: 免训练的特征动量方法加速扩散Transformer
tldr: FeMo利用特征动量机制稳定扩散模型在不同时间步的动态，并根据历史特征准确预测未来时间步的特征，从而减少计算量。该方法无需重新训练，可自适应搜索最优参数，在图像和视频生成任务上有效加速了扩散Transformer的推理过程。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 扩散模型计算负担重，本文利用特征空间连续性，旨在无训练前提下加速。
method: 提出FeMo动量机制，利用历史信息预测未来时间步特征，并扩展自适应版本搜索最优系数。
result: 在多个扩散Transformer模型上实现显著加速，保持生成质量。
conclusion: FeMo为扩散模型提供了一种简单有效的免训练加速手段。
---

## Abstract
Diffusion models have demonstrated outstanding generative capabilities in image and video synthesis. However, their heavy computational burden, particularly due to the sequential denoising process and large model sizes, makes them challenging to meet real-time application demands. In this paper, motivated by the continuity of diffusion models in the feature space, we introduce FeMo, which employs a momentum mechanism to stabilize the dynamics of diffusion models in different timesteps, allowing us to accurately predict the features in the future timesteps based on the historical information. Additionally, we further propose an Adapted-FeMo, which allows for adaptive searching for the optimal coefficient for each generated sample. Extensive experiments demonstrate its effectiveness, e.g., a 4.99$\times$ acceleration on FLUX with 0.86% improvements on image reward.Under the condition of maintaining generation quality, Adapted-FeMo achieves a maximum speedup of 7.10$\times$ on DiT and 6.24$\times$ on FLUX. Our codes are available in the supplementary material and will be released on Github.

---

## 论文详细总结（自动生成）

# 论文总结：Accelerate Diffusion Transformers with Feature Momentum（利用特征动量加速扩散Transformer）

## 1. 核心问题与研究动机
- **问题**：扩散模型（尤其是扩散Transformer）在图像和视频生成中性能卓越，但其顺序去噪过程和大模型尺寸导致**计算负担极重**，难以满足实时应用需求。
- **动机**：观察到扩散模型在**特征空间具有连续性**——不同时间步的特征变化平滑，因此可以借助历史信息预测未来时间步的特征，从而跳过部分计算步骤，实现免训练的推理加速。

## 2. 方法论
- **核心思想**：利用**动量机制（FeMo）** 稳定扩散模型在不同时间步的动态，通过历史特征（如过去几步的特征）准确预测未来时间步的特征，减少实际计算量。
- **关键技术细节**：
  - **FeMo**：在推理过程中，定义动量更新公式，用历史特征（如加权平均）来近似当前或未来时间步的特征，从而跳过部分网络的前向计算。
  - **Adapted-FeMo**：进一步扩展，为每个生成样本**自适应搜索最优动量系数**，无需手动调参，可在保持生成质量前提下最大化加速比。
- **公式/算法流程（文字说明）**：
  1. 在去噪过程的若干连续时间步中，存储前几步的隐藏特征。
  2. 利用动量公式（例如：当前特征 = α × 历史特征 + (1-α) × 当前计算特征，或直接预测未来特征）生成下一时间步的近似特征。
  3. 对于Adapted-FeMo，在推理时对一个样本进行少量试探性计算，搜索使生成质量损失最小的α。
- **优势**：无需重新训练，即插即用，适用于多种扩散Transformer架构。

## 3. 实验设计
- **数据集/场景**：图像生成（DiT、FLUX模型）和视频生成任务（文中提及“image and video synthesis”，但摘要主要报告图像结果）。
- **基准（Benchmark）**：对比原始未加速的扩散Transformer（DiT、FLUX等），以及可能的相关加速方法（未在摘要中列具体名称，但从上下文推测是标准baseline）。
- **对比方法**：文中未明确列出其他方法，但FeMo与原始模型对比，可能也与先行加速工作对比（如Cache、蒸馏等）。

## 4. 资源与算力
- **文中未明确说明**使用的GPU型号、数量及训练时长。仅提到代码已在补充材料中，并将在GitHub发布。考虑到该方法为**免训练**方式，推理加速无需额外训练算力，仅需推理时的GPU资源。

## 5. 实验数量与充分性
- **实验数量**：至少涵盖了DiT和FLUX两个主流扩散Transformer模型；分别报告了加速比与生成质量指标（Image Reward等）。此外，Adapted-FeMo版本也进行了测试。但摘要中仅提供了部分数值（如4.99× on FLUX, 7.10× on DiT）。
- **充分性评价**：实验覆盖了不同规模模型和加速比，但**缺乏与更多加速方法的对比**（如蒸馏、剪枝、Early Exit等），也未在更多数据集（如ImageNet、MS-COCO）上验证。消融实验（不同动量系数、自适应搜索的影响）可能已在完整论文中，但摘要未详述。总体而言，初步证明有效性，但**全面性有待加强**。

## 6. 主要结论与发现
- FeMo在**免训练**条件下实现了显著加速：在FLUX上达到**4.99×加速**，同时Image Reward提升0.86%；在DiT上可达**7.10×加速**，FLUX上达**6.24×加速**（保持生成质量）。
- Adapted-FeMo自适应机制可自动找到最优系数，避免手动调参，在保持质量的前提下获得最大加速。

## 7. 优点
- **方法简洁高效**：基于动量预测，不引入额外训练成本，易于集成到现有扩散Transformer中。
- **自适应版本**：无需手动调节超参数，提升实用性与泛化能力。
- **效果显著**：在多种模型上实现数倍加速，且生成质量未降甚至微升（可能因动量平滑减少了噪声）。
- **代码开源**：可复现。

## 8. 不足与局限
- **实验覆盖有限**：仅在DiT和FLUX上评估，未在更多架构（如U-Net扩散模型）或其他模态（文本到图像、视频）充分展示。
- **缺乏与现有加速方法（如蒸馏、缓存、步长调度）的系统对比**：无法判断相对优势。
- **潜在偏差风险**：动量假设可能在特征剧烈变化的时间步（如去噪早期）失效，导致质量下降，文中未讨论失败案例。
- **应用限制**：依赖于特征空间的连续性，可能不适用于所有扩散模型或采样调度（如高噪声非连续场景）。
- **未提供算力资源细节**，影响复现公平性。

（完）
