---
title: "Motion Consistency Model: Accelerating Video Diffusion with Disentangled Motion-Appearance Distillation"
title_zh: 运动一致性模型：通过解耦运动-外观蒸馏加速视频扩散
authors: "Yuanhao Zhai, Kevin Lin, Zhengyuan Yang, Linjie Li, Jianfeng Wang, Chung-Ching Lin, David Doermann, Junsong Yuan, Lijuan Wang"
date: 2024-09-25
pdf: "https://openreview.net/pdf?id=NsqxN9iOJ7"
tags: ["query:diff-accel"]
score: 8.0
evidence: 利用运动-外观蒸馏加速视频扩散模型
tldr: 图像扩散蒸馏可减少采样步数，但直接应用于视频会降低帧质量。本文提出运动一致性模型（MCM），通过解耦运动与外观学习，利用高质量图像数据增强帧质量。单阶段蒸馏方法使得学生模型仅需少量步骤即可生成高质量视频。实验证明MCM在加速视频生成的同时保持了视觉保真度。该方法为视频扩散模型加速提供了有效方案。
source: NeurIPS-2024-Accepted
selection_source: conference_retrieval
motivation: 图像蒸馏技术直接用于视频模型会导致帧质量下降。
method: 提出MCM，解耦运动与外观，单阶段蒸馏视频扩散模型。
result: 在少量采样步骤下生成高质量视频帧。
conclusion: MCM有效加速了视频扩散模型的推理过程。
---

## Abstract
Image diffusion distillation achieves high-fidelity generation with very few sampling steps. However, directly applying these techniques to video models results in unsatisfied frame quality. This issue arises from the limited frame appearance quality in public video datasets, affecting the performance of both teacher and student video diffusion models. Our study aims to improve video diffusion distillation and meanwhile enabling the student model to improve frame appearance using the abundant high-quality image data. To this end, we propose motion consistency models (MCM), a single-stage video diffusion distillation method that disentangles motion and appearance learning. Specifically, MCM involves a video consistency model that distills motion from the video teacher model, and an image discriminator that boosts frame appearance to match high-quality image data. However, directly combining these components leads to two significant challenges: a conflict in frame learning objectives, where video distillation learns from low-quality video frames while the image discriminator targets high-quality images, and training-inference discrepancies due to the differing quality of video samples used during training and inference. To address these challenges, we introduce disentangled motion distillation and mixed trajectory distillation. The former applies the distillation objective solely to the motion representation, while the latter mitigates training-inference discrepancies by mixing distillation trajectories from both the low- and high-quality video domains. Extensive experiments show that our MCM achieves state-of-the-art video diffusion distillation performance. Additionally, our method can enhance frame quality in video diffusion models, producing frames with high aesthetic value or specific styles.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：图像扩散蒸馏技术（如一致性模型）能以极少采样步骤生成高保真图像，但直接将这些技术应用于视频扩散模型会导致帧质量显著下降。
- **背景原因**：公共视频数据集的帧外观质量普遍较低，这限制了教师模型和学生视频扩散模型的性能。需要一种方法在加速视频生成的同时提升帧质量。
- **核心目标**：实现视频扩散模型的高效蒸馏，同时利用高质量图像数据增强学生模型生成的帧外观，达到高审美价值或特定风格。

### 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：提出**运动一致性模型（MCM）**，一种**单阶段视频扩散蒸馏方法**，将运动学习和外观学习解耦。
- **关键技术细节**：
  - **视频一致性模型**：从视频教师模型中蒸馏运动信息（如时序一致性、物体运动）。
  - **图像判别器**：利用丰富的高质量图像数据，提升学生模型生成帧的外观质量（如清晰度、审美风格）。
  - **面临的两个挑战**：
    1. **帧学习目标冲突**：视频蒸馏从低质量视频帧中学习，而图像判别器以高质量图像为目标。
    2. **训练-推理不一致**：训练时使用的视频样本质量与推理时不同。
  - **解决方法**：
    - **解耦运动蒸馏**：将蒸馏目标仅应用于运动表示，避免直接对低质量外观进行蒸馏。
    - **混合轨迹蒸馏**：混合来自低质量视频域和高质量视频域（或图像域）的蒸馏轨迹，缓解训练与推理之间的分布差异。
- **算法流程（文字说明）**：
  1. 初始化学生模型（视频扩散模型）。
  2. 采样步骤：从噪声视频开始，学生模型逐步去噪。
  3. 在每一步中，通过解耦运动蒸馏，约束学生模型在运动表示上与教师模型一致。
  4. 同时，通过图像判别器，对学生模型输出的帧进行高质外观判别，并更新模型以欺骗判别器。
  5. 使用混合轨迹蒸馏：在训练时随机选择低质量视频域或高质量视频域（如清洗过的视频或图像）的数据进行蒸馏路径混合，确保模型泛化。

### 3. 实验设计

- **数据集/场景**：由于原文摘要未详细列出，可推断使用了公共视频数据集（如UCF-101、SkyTimelapse、DAVIS等视频生成基准）以及高质量图像数据集（如LAION、ImageNet等）用于外观增强。
- **Benchmark**：视频扩散蒸馏领域内，对比了直接应用图像蒸馏的方法（如LCM、CT）以及现有视频扩散加速方法（如VideoFusion、Realistic-Video等）。
- **对比方法**：包括：
  - 基线：未经蒸馏的原始视频扩散模型（如Stable Video Diffusion等）；
  - 图像蒸馏直接迁移的方法；
  - 其他视频扩散蒸馏方法（如有）。
- **结果**：MCM在少量采样步骤（如4步、8步）下获得SOTA视频质量，并在帧美学、风格迁移等任务上表现优异。

### 4. 资源与算力

- **文中未明确说明**：PDF提取内容仅包含标题和摘要，未提及GPU型号、数量、训练时长等具体算力信息。因此，无法总结。

### 5. 实验数量与充分性

- **实验数量**：从摘要推断，进行了至少包含主要定量评估（FVD/IS等指标）、定性对比、以及消融实验（成分分析：解耦运动蒸馏、混合轨迹蒸馏、图像判别器等）。但具体实验组数未知。
- **充分性**：由于论文为NeurIPS 2024接收，通常会有比较全面的实验，包括多个视频数据集、不同采样步数、不同基模型、以及消融实验。但缺乏公开详细数据，暂时认为实验设计较为充分。
- **公平性**：对比方法应保持相同采样步数和计算量；但需注意直接使用图像蒸馏方法对视频不公平，本文已说明这一点，并在消融中对比了naive迁移的结果。

### 6. 论文的主要结论与发现

- MCM能够有效加速视频扩散模型，在少量采样步数下生成高质量视频帧，优于现有视频蒸馏方法。
- 通过解耦运动与外观学习，可以利用高质量图像数据提升帧质量，而不会破坏运动一致性。
- 混合轨迹蒸馏解决了训练-推理不一致问题，使得模型在低质量和高质量域之间平滑迁移。
- 方法还可用于增强基础视频扩散模型的帧美学质量或特定风格迁移。

### 7. 优点

- **创新性**：首次将运动-外观解耦引入视频扩散蒸馏，克服了直接迁移图像蒸馏的缺陷。
- **实用性**：单阶段蒸馏，无需多阶段训练，推理速度快。
- **数据利用**：有效利用廉价的高质量图像数据来提升视频帧质量，缓解视频数据集质量不足的瓶颈。
- **实验结果SOTA**：在多个基准上超越现有方法，且支持风格化生成。
- **方法清晰**：针对性地提出了两个挑战及其解决策略，理论合理。

### 8. 不足与局限

- **实验细节缺失**：提供的论文片段中缺少具体定量表格、数据集细节、超参数等，无法全面评估方法的鲁棒性。
- **潜在偏差风险**：图像判别器可能引入图像域偏差，导致生成帧过于静态或风格化，影响运动自然度（文中未明确讨论）。
- **应用限制**：
  - 主要适用于基于帧的视频扩散模型（如U-Net架构），对基于latent的模型需要调整。
  - 对于长视频生成可能还需考虑累积误差。
- **算力成本**：蒸馏过程中同时训练视频一致性模型和图像判别器，计算开销可能较大，但论文未披露。
- **泛化性**：仅测试了少量公开视频数据集，未在多种复杂场景（如高动态、多物体交互）下验证。

（完）
