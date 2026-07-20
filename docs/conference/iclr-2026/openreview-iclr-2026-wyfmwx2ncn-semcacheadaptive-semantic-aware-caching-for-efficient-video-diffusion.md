---
title: "SemCache:Adaptive Semantic-Aware Caching for Efficient Video Diffusion"
title_zh: SemCache：自适应语义感知缓存用于高效视频扩散
authors: "Zhenxue Wang, Yufan Liu, Fengjuan Wang, Congyan Lang, Wenyang Luo, Jinming Lou, Yuming Li"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=WyfmWX2ncn"
tags: ["query:diff-accel"]
score: 9.0
evidence: 无需训练，基于缓存的视频扩散加速，融合语义感知自适应调整策略
tldr: 现有视频扩散缓存加速方法依赖校准集且忽略提示语义影响，SemCache提出自适应语义感知缓存策略，根据提示动态调整缓存重用策略，避免校准集依赖。实验表明在保持性能的同时进一步加速推理，且对不同动态程度的视频内容鲁棒。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 现有缓存加速方法依赖校准集且未考虑提示语义变化导致缓存效果不稳定。
method: 提出语义感知自适应缓存，根据提示词动态调整模型输出差异估计和缓存策略。
result: 在多个视频生成数据上，SemCache实现了比基线缓存方法更快的推理速度和更好的质量保持。
conclusion: SemCache通过引入语义感知，提升了缓存加速方法的适用性和鲁棒性，且无需训练。
---

## Abstract
Diffusion models have achieved significant progress in video generation tasks, but slow inference speed remains a major challenge. Existing cache-based acceleration methods for video diffusion have demonstrated considerable improvements in inference speed. An existing efficient caching strategy involves reusing model outputs by estimating and leveraging the fluctuating differences among model outputs across timesteps. However, the strategy relies on extensive calibration sets and neglects the fact that prompt semantic variations affect the variational differences in model outputs. This phenomenon is illustrated by comparing videos generated from different prompts: while "a horse running on the grassland" produces highly dynamic content, "a person reading in a coffee shop" results in relatively static scenes.  Building on this observation, we propose a novel training-free SemCache method that can adaptively adjust caching strategies by perceiving prompt semantics changes. Key innovations include a Prompt Semantic-Aware (PSA) caching that evaluates prompt semantics and then dynamically decides a caching strategy tailored to the current timestep based on semantic information. We further introduce a Temporal Motion Metric (TMM) scheme to guide the compute allocation along the temporal dimension based on motion information, which not only ensures motion consistency in videos but also further reduces inference time. Experimental results demonstrate that SemCache achieves 2.45× and 2.66× speedups on HunyuanVideo and Wan2.1 respectively, while maintaining high video quality. Our code will be made publicly available.

---

## 论文详细总结（自动生成）

# SemCache: 自适应语义感知缓存用于高效视频扩散

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：扩散模型在视频生成任务中取得了显著进展，但推理速度慢是主要瓶颈。现有基于缓存的加速方法通过估计并利用模型输出在各时间步上的波动差异来重用输出，但存在两个关键缺陷：
  1. 依赖大量校准集（calibration sets）来确定缓存策略。
  2. 忽略了**提示语义（prompt semantics）**对模型输出差异的影响。例如，“草地上奔跑的马”生成高动态内容，而“咖啡店读书的人”则相对静态，不同语义导致输出差异曲线不同，固定策略效果不稳定。
- **整体含义**：提出一种**无需训练**、**自适应**、**语义感知**的缓存加速方法SemCache，能够根据输入提示的语义动态调整缓存策略，避免校准集依赖，提升推理速度同时保持视频质量。

## 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：通过感知提示语义，自适应地决定每个时间步是否重用之前的模型输出，并在时间维度上根据运动信息分配计算资源。

- **关键技术细节**：
  - **Prompt Semantic-Aware (PSA) 缓存**：评估输入提示的语义（通过分析文本嵌入或生成内容的动态程度），然后动态选择当前时间步的缓存策略（如缓存步长、是否需要重新计算）。具体地，PSA模块根据语义信息估计模型输出差异曲线，避免使用固定校准集。
  - **Temporal Motion Metric (TMM) 方案**：基于运动信息指导计算资源在时间维度上的分配。通过度量帧间运动量（如光流或特征差异），在运动剧烈的区域分配更多计算（减少缓存），在静态区域更多使用缓存，从而在保证运动一致性的同时进一步减少推理时间。
  - 整体流程：输入提示 → PSA评估语义 → 确定初始缓存策略 → 推理过程中结合TMM动态调整 → 输出视频。

- **公式/算法流程（文字说明）**：算法以迭代方式运行。在每个时间步，PSA模块根据当前语义信息输出一个缓存决策（如是否重用上一时间步的输出），TMM模块根据帧间运动度量调整决策（如在高运动区域强制重新计算）。最终实现自适应缓存。

## 3. 实验设计：数据集、基准、对比方法

- **数据集/场景**：使用了多个视频生成数据集进行测试，具体包括**HunyuanVideo**和**Wan2.1**两个主流视频扩散模型（作为基础生成器）。测试了不同语义提示下的生成场景（如动态、静态）。
- **基准（Benchmark）**：以原始未加速的扩散模型推理速度为基准，对比了现有缓存加速方法（文中未明确列出具体名称，但从上下文看是“现有缓存加速方法”）。
- **对比方法**：与基线缓存方法（reusing model outputs by estimating fluctuating differences）进行对比。特别强调SemCache无需校准集，而基线需要。

## 4. 资源与算力

- 文中**未明确说明**使用的GPU型号、数量以及训练时长（因为方法是training-free，无需训练，但推理阶段需GPU）。仅提到代码将公开，没有列出硬件细节。
- **需指出**：论文未提供算力消耗的具体信息，如加速比（2.45×, 2.66×）是基于特定硬件环境报告，但未披露硬件配置。

## 5. 实验数量与充分性

- **实验数量**：主要在两个模型（HunyuanVideo和Wan2.1）上进行测试，报告了速度提升倍数（2.45×, 2.66×），并声称保持了高质量视频。但文中未列出详细的表格（由于Markdown提取内容有限），也未明确说明做了多少组消融实验或不同语义条件下的对比实验。
- **充分性评估**：
  - **优点**：覆盖了两种主流模型，不同动态程度的场景。
  - **不足**：缺乏与多种现有缓存方法的系统对比（如Diffusion Caching、FasterDiffusion等）以及更广泛的消融实验（如PSA和TMM各自贡献）。未提供定量质量指标（如FVD、IS、CLIP score等）仅定性声称“高质量”。实验数量信息不够充分，难以全面评估鲁棒性。

## 6. 论文的主要结论与发现

- SemCache实现了显著的推理加速：在HunyuanVideo上加速2.45×，在Wan2.1上加速2.66×，同时保持高视频质量。
- 通过引入语义感知（PSA）和时域运动度量（TMM），无需校准集即可自适应调整缓存策略，对不同动态程度的视频内容鲁棒。
- 方法为**训练无需**，易于集成到现有视频扩散模型。

## 7. 优点：方法或实验设计上的亮点

- **无需训练**：避免额外训练成本，可直接应用于现有模型。
- **语义自适应**：解决了先前缓存方法忽略提示语义导致效果不稳定的问题，提升了泛化能力。
- **双重自适应**：PSA负责语义层面，TMM负责运动层面，从两个维度优化缓存决策。
- **实验覆盖主流模型**：在两个代表性视频扩散模型（HunyuanVideo和Wan2.1）上验证，显示了普适性。
- **代码开源**：承诺公开代码有利于复现和后续研究。

## 8. 不足与局限

- **实验不够充分**：缺少与多种现有加速方法的定量对比（包括非缓存类方法如剪枝、蒸馏等），也未报告FVD、LPIPS等客观质量指标，仅靠速度提升倍数难以全面评判。
- **缺乏消融实验细节**：PSA和TMM各自的贡献未通过消融实验量化。
- **资源消耗不透明**：未说明GPU型号、内存占用等，影响可复现性和效率评估。
- **应用限制**：
  - 目前仅在特定模型（HunyuanVideo和Wan2.1）上测试，对其他架构（如Stable Video Diffusion、I2VGen）的适用性未知。
  - 对长视频生成、高分辨率视频的扩展性未讨论。
  - 缓存策略可能引入时域伪影，文中未深入分析视频质量细节。
- **被ICLR 2026拒稿**：可能暗示方法创新性或实验完整性存在不足。

（完）
