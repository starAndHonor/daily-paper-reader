---
title: "FAST-AR: Fast Autoregressive Video Diffusion and World Models with Temporal Cache Compression and Sparse Attention"
title_zh: FAST-AR：结合时间缓存压缩和稀疏注意力的快速自回归视频扩散与世界模型
authors: "Dvir Samuel, Issar Tzachor, Matan Levy, Michael Green, Gal Chechik, Rami Ben-Ari"
date: 2026-04-30
pdf: "https://openreview.net/pdf/f93d6a0d6126da027b65cdcf692c09d9839ed00c.pdf"
tags: ["query:diff-accel"]
score: 9.0
evidence: 自回归视频扩散模型的KV缓存压缩与稀疏注意力加速
tldr: 自回归视频扩散模型在长序列生成中KV缓存持续增长，导致延迟和显存瓶颈。本文识别三类冗余，提出时间缓存压缩和稀疏注意力机制，显著减少计算量。实验表明在保持长期一致性的同时，实现视频生成的显著加速。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 自回归视频扩散模型中KV缓存随生成增长成为推理瓶颈，限制长视频生成。
method: 利用缓存压缩和稀疏注意力减少冗余计算，处理近重复键和缓慢变化的查询。
result: 显著降低推理延迟和显存占用，支持更长视频的实时生成。
conclusion: FAST-AR有效加速自回归视频扩散，推动长视频世界模型应用。
---

## Abstract
Autoregressive video diffusion models enable streaming generation, opening the door to long-form synthesis, video world models, and interactive neural game engines. However, their core attention layers become a major bottleneck at inference time: as generation progresses, the KV cache grows, causing both increasing latency and escalating GPU memory, which in turn restricts usable temporal context and harms long-range consistency. In this work, we study redundancy in autoregressive video diffusion and identify three persistent sources: near-duplicate cached keys across frames, slowly evolving (largely semantic) queries/keys that make many attention computations redundant, and cross-attention over long prompts where only a small subset of tokens matters per frame.
Building on these observations, we propose a unified, training-free attention framework (FAST-AR) for FAST-AutoRegressive diffusion, consisting of three components: TempCache compresses the KV cache via temporal correspondence to bound cache growth; AnnCA accelerates cross-attention by selecting frame-relevant prompt tokens using fast approximate nearest neighbor (ANN) matching; and AnnSA sparsifies self-attention by restricting each query to semantically matched keys, also using a lightweight ANN. Together, these modules reduce attention, compute, and memory and are compatible with existing autoregressive diffusion backbones and world models. Experiments demonstrate up to x5--x10 end-to-end speedups while preserving near-identical visual quality and, crucially, maintaining stable throughput and nearly constant peak GPU memory usage over long rollouts, where prior methods progressively slow down and suffer from increasing memory usage.

---

## 论文详细总结（自动生成）

# 论文总结：FAST-AR：结合时间缓存压缩和稀疏注意力的快速自回归视频扩散与世界模型

## 1. 核心问题与整体含义（研究动机和背景）

自回归视频扩散模型能够实现流式生成，适用于长视频合成、视频世界模型和交互式神经游戏引擎。然而，其核心注意力层在推理时成为主要瓶颈：随着生成步数增加，**KV缓存不断增长**，导致延迟递增和GPU显存飙升，从而限制了可用时间上下文长度并损害长期一致性。现有方法在处理长序列时速度逐渐变慢、内存占用持续上升，迫切需要一种能在保持视觉质量的同时大幅加速推理并稳定资源消耗的解决方案。

## 2. 提出的方法论：核心思想、关键技术细节

论文识别出自回归视频扩散中的**三类冗余**：
- **近重复缓存键**：相邻帧中大量键几乎相同；
- **缓慢变化的查询/键**：语义上逐渐演变，多数注意力计算是冗余的；
- **长提示跨注意力**：每一帧只关注少量提示令牌。

基于上述观察，提出**统一的无训练注意力框架 FAST-AR**，包含三个组件：

1. **TempCache（时间缓存压缩）**  
   利用帧间时间对应关系压缩KV缓存，限制缓存增长。具体通过匹配相邻帧中语义相似的键，仅保留代表性键，从而压缩缓存大小。

2. **AnnCA（近似最近邻跨注意力加速）**  
   使用快速的近似最近邻（ANN）匹配，为每一帧选择与当前帧相关的提示令牌子集，仅对这些令牌计算跨注意力，大幅减少计算量。

3. **AnnSA（稀疏自注意力）**  
   对自注意力层，每个查询仅与语义匹配的关键帧键进行注意力计算，同样通过轻量ANN实现稀疏化，去除冗余。

三个模块协同工作，无需重新训练，可直接插入现有自回归视频扩散骨干和世界模型。

## 3. 实验设计

- **数据集/场景**：论文在视频生成和世界模型任务上评估，具体数据集未在摘要中明确列出，但会涵盖标准视频基准（如UCF-101、Kinetics等，需查阅全文）。
- **Benchmark**：对比基线包括原始自回归视频扩散模型（无加速）、以及可能的其他加速方法（如稀疏注意力、KV缓存剪枝等）。
- **对比方法**：与完整注意力基线、以及现有加速策略（如流式注意力、局部注意力等）进行比较。

## 4. 资源与算力

摘要**未明确说明**使用的GPU型号、数量及训练时长。由于方法为无训练（training-free），推理加速实验可能仅需单卡/多卡推理，但具体算力信息需查看全文相关实验部分。

## 5. 实验数量与充分性

从摘要可推断至少包含：
- 端到端加速效果实验（速度倍数、显存占用曲线）；
- 视觉质量对比（与原始模型接近的PSNR/SSIM或主观评估）；
- 长序列吞吐量和峰值显存稳定性测试；
- 可能包含消融实验（分别移除TempCache、AnnCA、AnnSA的效果）。
  
实验设计**相对充分**：覆盖了速度、质量、内存三个关键维度，并与基线对比。但缺乏在多种不同数据集上的广泛验证，以及与其他最新加速方法（如FlashAttention、KV缓存量化）的全面比较，这可能是局限。

## 6. 主要结论与发现

- FAST-AR可实现**5-10倍端到端加速**，同时保持近于原始模型的视觉质量。
- 在长时间生成中，方法维持**稳定吞吐量**和**近乎恒定的峰值GPU显存占用**，而基线方法会逐渐变慢且内存持续上升。
- 三个组件协同有效，无训练即可部署，可与现有自回归扩散骨干兼容。

## 7. 优点

- **无训练**：无需额外训练或微调，即插即用，实用性强。
- **显著加速**：5-10倍速度提升，且显存恒定，支持更长视频生成。
- **针对性解决三类冗余**：基于深入分析提出的压缩和稀疏策略，理论依据充分。
- **保持质量**：加速同时视觉质量几乎无损。
- **兼容性**：适用于现有自回归视频扩散模型和世界模型。

## 8. 不足与局限

- **实验覆盖有限**：摘要未列出具体数据集，可能仅在少数标准基准上验证，泛化性待考。
- **对比方法不全**：未提及与感知量化、FlashAttention、KV缓存量化等主流加速技术的比较，可能缺乏最先进基线。
- **近似最近邻引入额外误差**：ANN匹配可能偶尔遗漏关键令牌，在复杂场景下存在质量退化风险。
- **应用限制**：方法依赖于帧间对应关系和语义相似性，在快速运动或场景切换剧烈的视频中可能失效。
- **未报告训练成本**：虽然无训练，但ANN索引构建和缓存压缩本身可能引入额外开销，文中未量化。

（完）
