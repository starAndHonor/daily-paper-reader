---
title: "Astraea: A Token-wise Acceleration Framework for Video Diffusion Transformers"
title_zh: Astraea：视频扩散Transformer的令牌级加速框架
authors: "Haosong Liu, Yuge Cheng, Wenxuan Miao, Zihan Liu, Aiyue Chen, Jing Lin, Yiwu Yao, Chen Chen, Jingwen Leng, Minyi Guo, Yu Feng"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=e8P4Oo8S6U"
tags: ["query:diff-accel"]
score: 9.0
evidence: 针对视频扩散Transformer的令牌级加速框架
tldr: 视频扩散Transformer计算开销大，现有启发式加速方法缺乏通用性。本文提出Astraea框架，通过轻量令牌选择机制和GPU并行稀疏注意力策略，在保持生成质量的同时实现线性加速。实验表明，该方法能显著减少执行时间，适用于多种视频生成任务。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 视频扩散Transformer计算开销大，现有加速方法依赖启发式规则，通用性差。
method: 提出Astraea框架，包含令牌选择和稀疏注意力，通过搜索近最优配置实现加速。
result: 在多种视频生成任务上取得显著加速效果，生成质量损失很小。
conclusion: Astraea提供了一种可扩展的视频扩散模型加速方案，兼顾效率和质量。
---

## Abstract
Video diffusion transformers (vDiTs) have made tremendous progress in text-to-video generation, but their high computational demands pose a major challenge for practical deployment. While existing studies propose acceleration methods to reduce workload at various granularities, they often rely on heuristics, limiting their applicability.
We introduce Astraea, a framework that searches for near-optimal configurations for vDiT-based video generation with a performance target. At its core, Astraea proposes a lightweight token selection mechanism and a memory-efficient, GPU-parallel sparse attention strategy, enabling linear reductions in execution time with minimal impact on generation quality. Meanwhile, to determine optimal token reduction for different timesteps, we further design a search framework that leverages a classic evolutionary algorithm to automatically determine the distribution of the token budget effectively. Together, Astraea achieves up to 2.4x inference speedup on a single GPU with great scalability (up to 13.2x speedup on 8 GPUs) while retaining better video quality compared to the state-of-the-art methods (<0.5% loss on the VBench score compared to the baseline vDiT models).

---

## 论文详细总结（自动生成）

# Astraea：视频扩散Transformer的令牌级加速框架——中文详细总结

## 1. 核心问题与整体含义（研究动机和背景）
- **问题**：视频扩散Transformer（vDiTs）在文本到视频生成中取得了巨大进展，但其计算开销极大，严重限制了实际部署。
- **背景**：现有加速方法多依赖启发式规则（如基于重要性或随机丢弃令牌），缺乏通用性和可扩展性，难以在不同模型和任务中稳定提升效率。
- **意义**：迫切需要一种可自动搜索最优配置、兼顾速度与质量的通用加速框架，以推动vDiTs走向实际应用。

## 2. 方法论：核心思想、关键技术细节
- **核心思想**：通过轻量令牌选择机制和GPU并行稀疏注意力，实现令牌级线性加速；并利用进化算法自动搜索不同时间步的最优令牌预算分布。
- **关键组件**：
  - **轻量令牌选择机制**：设计一种低计算开销的评分函数，用于识别每个时间步中信息量较低的令牌，从而决定是否丢弃或保留。该机制避免了传统全注意力计算，仅需少量额外操作。
  - **内存高效的GPU并行稀疏注意力**：将传统密集注意力计算改造为稀疏注意力，利用GPU并行能力高效处理选出的关键令牌，减少内存访问和计算量，实现线性时间缩减。
  - **进化搜索框架**：将令牌预算（即每个时间步保留的令牌比例）视为优化变量，采用经典进化算法（如遗传算法）自动搜索近优配置，以平衡质量与效率。搜索目标是满足给定性能目标（如加速比）下最大化生成质量。
- **算法流程（文字描述）**：
  1. 输入预训练vDiT模型和加速目标（如目标FPS或延迟）。
  2. 利用进化搜索，对每个扩散时间步生成一套令牌保留比例配置。
  3. 在推理过程中，对每个时间步：a) 使用轻量评分函数计算每个令牌的重要性；b) 根据该时间步的预算保留高重要性令牌；c) 对保留令牌执行GPU并行稀疏注意力计算；d) 跳过或合并低重要性令牌。
  4. 输出加速后的视频生成结果。

## 3. 实验设计：数据集、基准、对比方法
- **数据集/场景**：文本到视频生成任务（具体数据集未在摘要中明确，但推测为常见视频生成基准如UCF-101、Kinetics-600等，或标准文本-视频对数据集）。文中提及使用VBench评分作为质量指标。
- **基准模型**：基准vDiT模型（如OpenAI的Video DiT或其他主流视频扩散Transformer）。
- **对比方法**：与最先进（SOTA）加速方法对比（未具名，但强调“state-of-the-art methods”），包括启发式令牌丢弃、重要性采样等。
- **评价指标**：推理速度（单GPU/多GPU加速比）、生成质量（VBench分数，损失<0.5%）。

## 4. 资源与算力
- 文中提及单GPU加速最高2.4倍，8 GPU可达13.2倍加速，表明实验使用了多GPU环境（推测为NVIDIA A100或类似高端GPU）。
- **未明确说明**：具体GPU型号、数量、训练/搜索耗时、显存占用等细节。但可以推断进化搜索过程需要一定计算资源，但搜索本身是离线进行的，推理时开销极低。

## 5. 实验数量与充分性
- **实验数量**：至少包括：
  - 单GPU加速比实验（对比SOTA方法，显示Astraea优于启发式方法）。
  - 多GPU可扩展性实验（显示线性加速效果）。
  - 质量评估：VBench分数对比，表明质量损失极小（<0.5%）。
  - 可能还有消融实验（如去除稀疏注意力或进化搜索的影响）。
- **充分性与公平性**：摘要声称“retaining better video quality compared to the state-of-the-art methods”，暗示与SOTA方法公平对比。但缺少详细实验设置（如随机种子、视频长度、分辨率等），需全文验证。总体上，实验覆盖了速度和质量的权衡，较充分。

## 6. 主要结论与发现
- Astraea框架能显著加速vDiT推理，单GPU达2.4倍，多GPU可扩展至13.2倍。
- 与现有启发式方法相比，Astraea在保持甚至略好视频质量的同时实现更高加速（质量损失不超过0.5% VBench分数）。
- 令牌选择机制和稀疏注意力策略可线性减少执行时间，进化搜索自动找到令牌预算的最优分布，避免了手动调参。

## 7. 优点
- **通用性**：不依赖特定模型或启发式规则，通过搜索适应不同任务和模型。
- **效率与质量兼顾**：轻量选择+稀疏注意力，实现线性加速，质量损失极小。
- **可扩展性**：在8 GPU上接近线性加速，适合分布式部署。
- **自动化**：进化搜索框架自动确定每个时间步的令牌预算，减轻人工成本。

## 8. 不足与局限
- **实验细节缺失**：未明确提及具体数据集、对比方法名称、GPU型号等，需查看全文才能评估公平性。
- **令牌选择机制开销**：虽然宣称“轻量”，但评分函数可能仍引入额外延迟，尤其在高分辨率视频中是否保持优势待验证。
- **质量评估指标单一**：仅使用VBench分数，可能未覆盖所有生成质量维度（如动作一致性、时序连贯性）。
- **应用限制**：当前仅针对vDiT，是否适用于其他扩散模型（如图像DiT、视频U-Net）未提及。
- **搜索成本**：进化搜索虽然离线，但仍需一定计算资源，且搜索空间可能随时间步增多而变大。

（完）
