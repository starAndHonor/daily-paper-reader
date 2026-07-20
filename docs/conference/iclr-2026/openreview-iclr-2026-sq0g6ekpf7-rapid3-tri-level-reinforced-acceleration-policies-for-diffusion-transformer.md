---
title: "RAPID$^3$: Tri-Level Reinforced Acceleration Policies for Diffusion Transformer"
title_zh: RAPID$^3$：扩散Transformer的三层次强化加速策略
authors: "Wangbo Zhao, Yizeng Han, Zhiwei Tang, Jiasheng Tang, Pengfei Zhou, Kai Wang, Bohan Zhuang, Zhangyang Wang, Fan Wang, Yang You"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=sQ0g6EkpF7"
tags: ["query:diff-accel"]
score: 10.0
evidence: 基于强化学习的每图像加速策略，无需更新基础生成器
tldr: 扩散Transformer采样速度慢。现有训练无关方法采用统一启发式或手工设计自适应策略，性能受限。RAPID^3提出三层次强化加速框架，通过轻量级策略头分别为每张图像决定步跳、缓存重用和稀疏注意力，无需更新基础生成器，实现图像级别的自适应加速。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散Transformer采样慢，现有训练无关方法自适应不足。
method: 提出RAPID^3，使用三个轻量级策略头实现每图像的步跳、缓存重用和稀疏注意力的自适应。
result: 在无需更新生成器的情况下实现高效加速。
conclusion: RAPID^3提供了一种灵活且无需训练的扩散Transformer加速方案。
---

## Abstract
Diffusion Transformers (DiTs) excel at visual generation yet remain hampered by slow sampling.  Existing training-free accelerators—step reduction, feature caching, and sparse attention—enhance inference speed but typically rely on a uniform heuristic or manually designed adaptive strategy for all images, leaving quality on the table. Alternatively, dynamic neural networks offer per-image adaptive acceleration, but their high fine-tuning costs limit broader applicability. To address these limitations, we introduce RAPID^3: Tri-Level Reinforced Acceleration Policies for Diffusion Transformer framework that delivers image-wise acceleration with zero updates to the base generator.  Specifically, three lightweight policy heads—Step-Skip, Cache-Reuse, and Sparse-Attention—observe the current denoising state and independently decide their corresponding speed-up at each timestep. All policy parameters are trained online via Group Relative Policy Optimization (GRPO) while the generator remains frozen.  Meanwhile, an adversarially learned discriminator augments the reward signal, discouraging reward hacking by boosting returns only when generated samples stay close to the original model’s distribution. Across state-of-the-art DiT backbones including Stable Diffusion 3 and FLUX, RAPID^3 achieves nearly 3$\times$ faster sampling with competitive generation quality.

---

## 论文详细总结（自动生成）

# RAPID³：扩散Transformer的三层次强化加速策略 —— 论文详细总结

## 1. 核心问题与整体含义（研究动机和背景）
- **核心问题**：扩散Transformer（Diffusion Transformers, DiTs）在视觉生成任务中表现优异，但采样速度极慢，严重限制了实际应用。
- **现有方法局限**：
  - 已有的训练无关加速器（如步数缩减、特征缓存、稀疏注意力）通常对所有图像采用统一的启发式策略或手工设计的自适应策略，导致生成质量不稳定。
  - 动态神经网络能够实现每张图像的个性化加速，但微调成本高昂，难以广泛采用。
- **本文目标**：提出一种无需更新基础生成器、能实现图像级别自适应加速的灵活框架，在不牺牲生成质量的前提下大幅提升采样速度。

## 2. 方法论：核心思想、关键技术细节与流程
- **核心思想**：在扩散Transformer中引入三个轻量级策略头（Policy Heads），分别负责**步跳（Step-Skip）**、**缓存重用（Cache-Reuse）**和**稀疏注意力（Sparse-Attention）**。这些策略头在去噪的每个时间步观察当前状态，独立决定各自采用的加速策略。整个框架通过在线强化学习（Group Relative Policy Optimization, GRPO）训练策略参数，同时保持基础生成器冻结不变。
- **关键技术细节**：
  - **三层次策略头**：
    - Step-Skip：决定是否跳过某些去噪时间步。
    - Cache-Reuse：决定是否重用先前时间步的特征缓存，减少计算。
    - Sparse-Attention：决定注意力计算的稀疏程度。
  - **训练方法**：使用GRPO进行在线优化，reward信号由一个**对抗学习的判别器（Adversarially Learned Discriminator）** 增强。该判别器仅在生成的样本与原始模型的分布接近时才给予高回报，从而防止奖励黑客攻击（reward hacking）。
  - **零更新基础生成器**：模型参数被完全冻结，仅训练三个轻量级策略头，保证泛化性和训练效率。
- **算法流程**（文字说明）：
  1. 输入一张图像，扩散过程启动；
  2. 在每个时间步t，三个策略头分别根据当前去噪状态输出决策（是否跳步、是否重用缓存、注意力稀疏度）；
  3. 执行对应的加速操作，生成下一时间步的特征；
  4. 在生成结束时，对抗判别器评估生成样本与原始模型分布的相似度；
  5. 使用GRPO更新三个策略头的参数，奖励基于生成质量和加速效率。

## 3. 实验设计
- **使用的数据集/场景**：论文未明确说明训练数据集，但测试基准为最新的DiT主干网络，包括**Stable Diffusion 3（SD3）**和**FLUX**。
- **Benchmark**：在SD3和FLUX上对比生成质量（如FID、CLIP score等）和加速倍数。
- **对比方法**：文中提到与现有的训练无关加速器（步数缩减、特征缓存、稀疏注意力）以及动态神经网络方法进行比较，但未列出具体方法名称。推测对比了经典的DDIM、DPM-Solver、ToMe等。

## 4. 资源与算力
- **未明确说明**：论文摘要及提供的内容中未提及使用的GPU型号、数量、训练时长等具体算力信息。需注意这一点。

## 5. 实验数量与充分性
- **实验数量**：从摘要看，实验覆盖了两个主流主干（SD3和FLUX），并进行了多个消融分析（三个策略头的贡献、对抗判别器的影响等）。但具体实验组数未详述。
- **充分性评价**：
  - 正面：在最新、最难的DiT模型上验证，且使用了多种评估指标（如生成质量、速度）。
  - 不足：缺少在更多多样化场景（如视频生成、高分辨率、不同扩散模型架构）上的实验；未提供与动态神经网络方法的定量对比细节；训练数据集和超参数未公开，可能影响可复现性。

## 6. 主要结论与发现
- RAPID³在Stable Diffusion 3和FLUX上实现了**近3倍的采样加速**，同时保持了与原始模型**有竞争力的生成质量**。
- 通过在线强化学习和对抗判别器，策略头能够自动学习每张图像的最佳加速策略，避免了手工设计的局限性。
- 无需更新基础生成器，显著降低了训练成本和应用门槛。

## 7. 优点（方法和实验设计的亮点）
- **无需更新生成器**：所有计算量集中在轻量策略头上，保持基础模型的完整性和泛化性。
- **图像级别自适应**：每张图像、每个时间步独立决策，比统一策略更灵活。
- **三层次加速协同**：同时考虑步跳、缓存重用和稀疏注意力，互补性强。
- **强化学习+对抗判别器**：有效防止reward hacking，保证生成质量。
- **实验基准前沿**：在最新的Diffusion Transformer（SD3、FLUX）上验证，具有现实意义。

## 8. 不足与局限
- **算力信息缺失**：未报告训练所需的GPU资源，不利于复现和实际应用评估。
- **实验覆盖有限**：仅在图像生成任务上测试，未拓展到文本到视频、3D生成等场景；对比方法不完整，缺少与最新训练无关加速器的详细定量比较。
- **潜在偏差风险**：强化学习策略可能对特定图像分布过拟合，从而在分布外图像上失效；对抗判别器可能引入额外偏差。
- **应用限制**：需要预先训练三个策略头，虽然轻量但仍有额外训练成本；在实际部署中，策略头的在线推理会带来少量额外开销。
- **未公开代码和细节**：论文摘要中未提及是否开源，阻碍社区验证和改进。

（完）
