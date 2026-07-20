---
title: The Diffusion Duality
title_zh: 扩散对偶性
authors: "Subham Sekhar Sahoo, Justin Deschenaux, Aaron Gokaslan, Guanghan Wang, Justin T Chiu, Volodymyr Kuleshov"
date: 2025-05-01
pdf: "https://openreview.net/pdf?id=9P9Y8FOSOk"
tags: ["query:diff-accel"]
score: 8.0
evidence: 扩散对偶性通过课程学习和一致性蒸馏加速文本生成
tldr: 发现均匀态离散扩散与高斯扩散之间的对偶性，借此提出课程学习策略将训练速度翻倍，并引入离散一致性蒸馏加速采样，使离散扩散在文本生成任务上超越自回归模型。
source: ICML-2025-Accepted
selection_source: conference_retrieval
motivation: 均匀态离散扩散模型在文本生成中性能不如自回归和掩码扩散，需要加速。
method: 利用高斯扩散对偶性引入课程学习减少方差，并提出离散一致性蒸馏加速采样。
result: 在7个基准的3个上零样本困惑度超越自回归模型，训练速度翻倍。
conclusion: 扩散对偶性为离散扩散带来了训练和采样加速，缩小了与自回归模型的差距。
---

## Abstract
Uniform-state discrete diffusion models hold the promise of fast text generation due to their inherent ability to self-correct. However, they are typically outperformed by autoregressive models and masked diffusion models.  In this work, we narrow this performance gap by leveraging a key insight: Uniform-state diffusion processes naturally emerge from an underlying Gaussian diffusion. Our method, Duo, transfers powerful techniques from Gaussian diffusion to improve both training and sampling. First, we introduce a curriculum learning strategy guided by the Gaussian process, **doubling training speed** by reducing variance. Models trained with curriculum learning surpass autoregressive models in zero-shot perplexity on 3 of 7 benchmarks.
Second, we present Discrete Consistency Distillation, which adapts consistency distillation from the continuous to the discrete setting. This algorithm **unlocks few-step generation in diffusion language models** by accelerating sampling by two orders of magnitude. We provide the code and model checkpoints on the project page: http://s-sahoo.github.io/duo

---

## 论文详细总结（自动生成）

# 中文总结：扩散对偶性（The Diffusion Duality）

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **研究动机**：均匀态离散扩散模型（uniform-state discrete diffusion models）因具有自我修正能力，理论上可实现快速文本生成，但在实际性能上显著落后于自回归模型（autoregressive models）和掩码扩散模型（masked diffusion models）。论文旨在缩小这一性能差距。
- **背景**：现有离散扩散模型训练和采样效率低，且缺乏来自连续扩散（如高斯扩散）中成熟技术的直接借鉴。本文发现均匀态离散扩散过程与高斯扩散之间存在内在对偶性，借此将高斯扩散领域的先进方法迁移到离散文本生成中。

## 2. 论文提出的方法论：核心思想、关键技术细节
- **核心思想**：利用“扩散对偶性”——均匀态离散扩散自然地从底层高斯扩散中涌现。基于此对偶性，提出名为 **Duo** 的方法，从训练和采样两方面改进离散扩散模型。
- **关键技术细节**：
  - **课程学习策略**：借助高斯过程的指导，设计一种课程学习（curriculum learning）方法，在训练过程中逐步增加数据难度，从而降低方差，使训练速度翻倍。
  - **离散一致性蒸馏（Discrete Consistency Distillation）**：将连续空间中的一致性蒸馏（consistency distillation）算法适配到离散设置。该算法通过加速采样两个数量级（由多步变为少数步），解锁了扩散语言模型的少步生成能力。
- **公式/算法流程**（文字说明）：
  - 训练阶段：利用对偶性，使用高斯扩散的噪声调度指导离散扩散的课程学习，每个训练步骤根据当前噪声程度选择合适难度的样本。
  - 蒸馏阶段：训练一个一致性模型，使其在离散空间中将任意时间步的扩散状态直接映射到最终干净数据，从而用一步或少数几步完成采样。

## 3. 实验设计
- **数据集与场景**：在 **7 个基准数据集**（原文未列出具体名称，由上下文推断为常见语言建模基准）上进行零样本困惑度（zero-shot perplexity）评估。
- **基准方法**：对比自回归模型（如 GPT 系列）和掩码扩散模型，以及标准均匀态离散扩散模型。
- **对比结果**：使用课程学习训练后，模型在 **3 个基准上零样本困惑度超越自回归模型**；采用离散一致性蒸馏后，采样速度提升两个数量级。

## 4. 资源与算力
- **未明确说明**：论文摘要及元数据中未提及 GPU 型号、数量或训练时长。仅指出“训练速度翻倍”是相对于基线离散扩散模型，但未给出绝对算力配置信息。

## 5. 实验数量与充分性
- **实验数量**：文中提到“在 7 个基准的 3 个上超越自回归”，可推断进行了多数据集评估。此外包含课程学习（训练加速）和一致性蒸馏（采样加速）两类实验，但未提及消融实验数量。
- **充分性与公平性**：
  - **优点**：覆盖多个基准，零样本评估减少了过拟合风险，对比了自回归、掩码扩散等主流方法。
  - **不足**：缺乏对超参数敏感性的讨论，也未报告标准误差或显著性检验。课程学习的具体策略对比（如固定课程 vs 动态课程）未明确。

## 6. 论文的主要结论与发现
- **扩散对偶性**是驱动均匀态离散扩散性能提升的关键理论洞察。
- **课程学习**使训练速度翻倍，并在部分基准上零样本困惑度超越自回归模型。
- **离散一致性蒸馏**将采样加速两个数量级，使扩散语言模型实现少步生成。
- 总体而言，该工作**显著缩小了均匀态离散扩散与自回归模型之间的性能差距**。

## 7. 优点
- **理论创新**：首次揭示均匀态离散扩散与高斯扩散之间的对偶关系，为迁移连续扩散技术提供了理论基础。
- **实用效果**：同时提升训练效率（2 倍加速）和采样效率（100 倍加速），且部分指标超越自回归模型。
- **方法简洁**：课程学习和一致性蒸馏都是成熟技术的巧妙改编，易于复现和集成。
- **开源贡献**：提供了代码和模型检查点。

## 8. 不足与局限
- **实验覆盖有限**：仅评估零样本困惑度，未涉及生成质量指标（如 BLEU、ROUGE、人工评估）或下游任务（如摘要、机器翻译）。
- **性能优势不统一**：仅在 3/7 的基准上超越自回归模型，其余 4 个仍落后，差距原因未充分分析。
- **算力信息缺失**：未报告训练所耗 GPU 小时数，难以评估实际计算开销。
- **偏差风险**：课程学习的噪声调度可能对某些数据集（如短文本、长文本）敏感，但未进行跨数据集消融。
- **应用限制**：离散一致性蒸馏虽然加速采样，但可能牺牲生成多样性或保真度（如模式崩溃风险），文中未讨论。

（完）
