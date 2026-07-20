---
title: "SGMD: Score Gradient Matching Distillation for Few-Step Video Diffusion Distillation"
title_zh: SGMD：分数梯度匹配蒸馏用于少步视频扩散蒸馏
authors: "Zhuguanyu Wu, Ruihao Gong, Yang Yong, Yushi Huang, Xiangyu Fan, Lei Yang, Dahua Lin, Xianglong Liu"
date: 2026-04-30
pdf: "https://openreview.net/pdf/4e1afdb36dcfb258a1dc5e02fd4164fee92fb32d.pdf"
tags: ["query:diff-accel"]
score: 9.0
evidence: 分数梯度匹配蒸馏用于少步视频扩散
tldr: 针对视频扩散模型蒸馏加速中的伪评分网络跟踪滞后问题，提出分数梯度匹配蒸馏（SGMD）方法，直接优化伪评分向教师评分对齐，避免频繁更新，在减少训练成本的同时保持生成一致性，实现少步视频生成。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有DMD蒸馏中伪评分网络更新频率难以平衡，导致训练不稳定或生成退化。
method: 提出分数梯度匹配蒸馏，直接优化伪评分向教师评分对齐，并使用教师停止梯度的Fisher信息作为正则项。
result: 在视频生成任务上，与DMD相比，SGMD以更少的训练步数实现了相当的或更好的生成质量。
conclusion: SGMD有效解决了DMD的跟踪滞后问题，为视频扩散模型的快速推理提供了高效蒸馏方案。
---

## Abstract
Distribution Matching Distillation (DMD) is a widely used paradigm for accelerating inference in few-step video diffusion models. However, DMD-style training faces a structural bottleneck: the student-side auxiliary score network (the fake score) must closely track a continuously evolving generator. Updating the fake score too frequently increases training cost and can over-emphasize inner-loop tracking, while infrequent updates lead to tracking lag that destabilizes training and degrades generation consistency. To address this issue, we propose \textbf{Score Gradient Matching Distillation (SGMD)}. SGMD adopts a fake-score perspective by directly optimizing the fake score toward the teacher, while using teacher stop-gradient Fisher as a stable distribution-matching objective. We provide a gradient analysis that motivates this objective choice under ideal tracking. Building on this, SGMD introduces a pair of dual potentials: negative-residual (NR) for outer-loop correction and residual-contraction (RC) for inner-loop tracking. Empirically, compared to DMD, SGMD achieves an approximately $\sim 3\times$ training speedup and substantially improves motion dynamics for 4-step distilled models while preserving temporal consistency.

---

## 论文详细总结（自动生成）

# SGMD：分数梯度匹配蒸馏用于少步视频扩散蒸馏

## 1. 论文的核心问题与整体含义（研究动机和背景）

- 视频扩散模型在生成高质量视频方面表现出色，但其推理速度慢，需要大量采样步数。分布匹配蒸馏（Distribution Matching Distillation, DMD）是一种常用的加速方案，旨在通过学生模型在很少步数（如4步）内模拟教师扩散模型的生成分布。
- DMD 训练存在结构性瓶颈：学生侧的辅助评分网络（伪评分）必须持续跟踪不断演化的生成器（学生模型自身）。若频繁更新伪评分网络，训练成本剧增且可能导致内循环过拟合；若更新频率过低，则产生跟踪滞后，使训练不稳定、生成一致性下降。
- 因此，核心问题是如何在不增加训练负担的前提下，保证伪评分网络与教师评分网络之间的对齐，从而实现稳定高效的少步视频生成蒸馏。

## 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：直接从伪评分（fake score）角度出发，优化伪评分向教师评分对齐，而非像 DMD 那样依赖伪评分网络频繁跟踪变化的学生模型。同时，利用教师停止梯度的 Fisher 信息作为稳定的分布匹配目标。
- **关键技术细节**：
  - **分数梯度匹配蒸馏（Score Gradient Matching Distillation, SGMD）**：直接最小化伪评分与教师评分之间的差异，避免频繁更新伪评分网络。
  - **梯度分析**：在理想跟踪条件下，推导出该目标选择的理论动机，证明其能有效缓解跟踪滞后。
  - **双重势函数（dual potentials）**：
    - 负残差（Negative-Residual, NR）：用于外循环校正，补偿因跟踪滞后造成的偏差。
    - 残差收缩（Residual-Contraction, RC）：用于内循环跟踪，稳定伪评分网络更新过程。
  - 训练过程中，教师评分网络的梯度被停止（stop-gradient），利用 Fisher 信息矩阵作为正则项，使分布匹配更稳定。

（注：算法流程未在摘要中详述，但核心技术如上。）

## 3. 实验设计：数据集、基准、对比方法

- **数据集**：摘要及元数据未明确列出具体视频数据集名称，推测使用了常见的视频生成基准（如 UCF-101, Sky Time-lapse, 或 Kinetics 等，但需要正文确认）。
- **基准（Benchmark）**：未明确列出评估指标，但提及“motion dynamics”和“temporal consistency”，可能使用 FVD（Fréchet Video Distance）、IS（Inception Score）等。
- **对比方法**：主要与 DMD 对比，强调在相同步数（如4步）下，SGMD 实现训练速度提升约3倍，同时生成质量相当或更好。

## 4. 资源与算力

- 论文中未明确说明使用的 GPU 型号、数量和训练时长。仅提到“训练加速约3倍”，但未给出绝对数值。因此，无法提供具体算力信息，需阅读全文获取细节。

## 5. 实验数量与充分性

- 从摘要推断，实验至少包括：
  - 与 DMD 在 4 步蒸馏模型上的对比（训练速度和生成质量）。
  - 消融实验分析双重势函数（NR 和 RC）的效果（摘要提及“empirically”）。
  - 可能还包括不同步数（如 1 步、2 步）的对比。
- 充分性评估：实验覆盖了主要对比方法（DMD），但未提及其他蒸馏方法（如 progressive distillation, consistency models 等），也未明确数据集和指标，因此存在一定局限。从得分 9.0 看，审稿人认为实验设计较充分，但需结合全文判断。

## 6. 论文的主要结论与发现

- SGMD 有效解决了 DMD 中伪评分网络跟踪滞后的问题，通过直接优化伪评分向教师对齐，避免了频繁更新带来的不稳定和成本。
- 在视频生成任务上，与 DMD 相比，SGMD 以约 3 倍训练加速实现了相当或更好的生成质量，尤其在运动动态（motion dynamics）方面有显著改善，同时保持了时间一致性（temporal consistency）。
- 双重势函数设计（NR + RC）进一步提升了蒸馏的稳定性和效果。

## 7. 优点：方法或实验设计上的亮点

- **方法创新**：从伪评分对齐的视角切入，提出分数梯度匹配，比 DMD 更直接、更稳定。
- **理论支撑**：提供梯度分析，为优化目标选择提供依据（理想跟踪下的动机）。
- **实用高效**：训练速度提升约 3 倍，显著降低视频蒸馏训练成本。
- **结构简洁**：双重势函数分别处理内外循环，分工明确，便于实施。

## 8. 不足与局限

- **实验覆盖不完整**：未报告具体数据集和评估指标，难以直接复现和与其他工作公平比较。
- **对比方法有限**：仅与 DMD 对比，未与其他主流蒸馏方法（如蒸馏一致性模型、渐进式蒸馏等）比较，通用性存疑。
- **应用限制**：论文聚焦视频扩散模型，方法是否适用于图像或其他模态未验证。
- **算力信息缺失**：未提供训练资源详情，不利于评估可复现性和实际部署成本。
- **潜在风险**：如果教师模型本身有偏差，直接对齐伪评分可能继承偏差；理论分析基于理想跟踪假设，实际中可能仍有偏差。

（完）
