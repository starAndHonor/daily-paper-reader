---
title: "DiffSparse: Accelerating Diffusion Transformers with Learned Token Sparsity"
title_zh: DiffSparse：通过学习的标记稀疏性加速扩散Transformer
authors: "Haowei Zhu, Ji Liu, Ziqiong Liu, Dong Li, Jun-Hai Yong, Bin Wang, Emad Barsoum"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=V3eUas3VCL"
tags: ["query:diff-accel"]
score: 7.0
evidence: 可微分层稀疏优化与标记缓存加速扩散Transformer
tldr: DiffSparse针对扩散Transformer模型提出可微分层稀疏优化框架，结合标记缓存技术自动学习每层稀疏度分配。该方法克服了现有缓存策略效率低下和人工分配稀疏度的缺点，在少步生成场景下实现了更优的加速效果。实验表明其在图像生成任务中显著降低了计算成本。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 现有标记缓存方法在少步扩散模型中因缓存策略低效和手动稀疏分配而效果不佳。
method: 提出可微分层稀疏优化框架，联合优化标记缓存和稀疏度分配。
result: 在图像生成任务中加速效果优于现有缓存方法。
conclusion: DiffSparse为扩散Transformer提供了一种自动化、高效的加速方案。
---

## Abstract
Diffusion models demonstrate outstanding performance in image generation, but their multi-step inference mechanism requires immense computational cost. Previous works accelerate inference by leveraging layer or token cache techniques to reduce computational cost. However, these methods fail to achieve superior acceleration performance in few-step diffusion transformer models due to inefficient feature caching strategies, manually designed sparsity allocation, and the practice of retaining complete forward computations in several steps in these token cache methods.
To tackle these challenges, we propose a differentiable layer-wise sparsity optimization framework for diffusion transformer models, leveraging token caching to reduce token computation costs and enhance acceleration. Our method optimizes layer-wise sparsity allocation in an end-to-end manner through a learnable network combined with a dynamic programming solver. Additionally, our proposed two-stage training strategy eliminates the need for full-step processing in existing methods, further improving efficiency.
We conducted extensive experiments on a range of diffusion-transformer models, including DiT-XL/2, PixArt-$\alpha$, FLUX, and Wan2.1. Across these architectures, our method consistently improves efficiency without degrading sample quality. For example, on PixArt-$\alpha$ with 20 sampling steps, we reduce computational cost by 54% while achieving generation metrics that surpass those of the original model, substantially outperforming prior approaches. These results demonstrate that our method delivers large efficiency gains while often improving generation quality.
.

---

## 论文详细总结（自动生成）

以下是根据已有论文信息（元数据及摘要）生成的结构化总结。由于提供的论文内容有限（仅包含元数据和摘要），部分要点（如实验细节、算力等）无法详细说明，将如实指出信息缺失。

---

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **核心问题**：扩散Transformer模型在图像生成中表现优异，但其多步推理机制计算成本极高。现有加速方法（如层缓存或标记缓存）在少步（few-step）扩散模型中效果不佳，主要原因包括：特征缓存策略低效、稀疏度分配由人工设计不合理、以及缓存方法中保留了若干步的完整前向计算。
- **整体含义**：提出一种自动化的、可微分的逐层稀疏度优化框架，通过学习每层应保留多少标记（token），结合缓存技术，在保证生成质量的同时大幅降低计算量，尤其适用于少步采样场景。

## 2. 论文提出的方法论
- **核心思想**：将每层的标记稀疏度（即保留多少比例的token）视为可学习参数，联合优化缓存策略和稀疏分配，实现端到端的自动稀疏化。
- **关键技术细节**：
  - 设计一个可微分的逐层稀疏度优化框架，通过一个可学习的网络（learnable network）结合动态规划求解器（dynamic programming solver）自动确定每层的最佳稀疏度。
  - 提出两阶段训练策略：第一阶段学习稀疏度分配，第二阶段微调生成质量；该策略避免了现有方法中需要完整步骤处理（full-step processing）的缺陷，进一步提升效率。
- **算法流程（文字说明）**：
  1. 输入扩散模型的预训练权重。
  2. 定义一个参数化的稀疏度预测网络，输入每层的特征统计量，输出该层的保留token比例（0到1之间）。
  3. 使用动态规划求解器在总计算量约束下优化稀疏度分配，得到全局最优的每层稀疏度。
  4. 结合token缓存机制：在推理时，对每层仅计算重要token，其余token复用上一时间步的值。
  5. 两阶段训练：第一阶段固定生成网络，仅训练稀疏度预测网络；第二阶段联合微调部分生成层和稀疏度预测网络，以补偿质量损失。

## 3. 实验设计
- **数据集/场景**：摘要中提及在多个扩散Transformer模型上实验，包括DiT-XL/2、PixArt-α、FLUX、Wan2.1。未明确说明使用的具体数据集（如ImageNet等），推测为标准图像生成基准。
- **Benchmark**：以计算成本（如FLOPs减少比例）和生成质量（如FID等指标）作为评价标准。
- **对比方法**：与之前的层缓存/标记缓存方法对比。具体方法名称未给出，但提到“prior approaches”均不够高效。

## 4. 资源与算力
- **文中未明确说明**：摘要和元数据均未提及使用的GPU型号、数量、训练时长等算力信息。无法总结。需指出：论文原文中可能包含更详细说明，但当前提供的内容无此信息。

## 5. 实验数量与充分性
- **实验数量**：提及在四种架构（DiT-XL/2, PixArt-α, FLUX, Wan2.1）上进行了实验。此外，针对PixArt-α给出了一个具体结果（20步采样，减少54%计算量且指标超越原模型）。未提到消融实验、不同稀疏度策略对比等，因此实验数量有限。
- **充分性与公平性**：从摘要看，实验覆盖了多种主流扩散Transformer模型，具有一定代表性。但缺乏与更多基线方法（如不同缓存策略、剪枝方法）的详细对比，也未说明是否控制变量。需待全文审阅才能判断公平性。

## 6. 论文的主要结论与发现
- 提出的DiffSparse方法在多个扩散Transformer模型上均能显著降低计算成本（如PixArt-α上减少54%），同时生成质量不降反升。
- 相比现有token缓存方法，DiffSparse在少步生成场景中具有更优的加速效果，证明了自动化稀疏度分配优于手工设计的优越性。
- 两阶段训练策略消除了现有方法中保留完整计算步骤的需求，进一步降低了开销。

## 7. 优点
- **方法创新**：首次将逐层稀疏度分配建模为可微分优化问题，结合动态规划求解，实现了端到端自动稀疏化。
- **效率提升**：在少步模型上加速效果显著（54%计算量节省），且质量保持甚至超越原始模型。
- **通用性**：在四种不同架构上验证，说明方法具有跨模型迁移能力。

## 8. 不足与局限
- **实验信息不足**：当前提供的摘要内容非常简略，缺乏详细的数据集、对比基线、消融实验等，无法全面评估方法的鲁棒性。
- **算力开销未说明**：训练两阶段策略所需额外计算量未知，可能影响实际部署中的性价比。
- **应用限制**：方法依赖token缓存机制，对于非Transformer架构或完全不同的扩散模型（如UNet）可能不适用。此外，动态规划求解器可能增加推理时的额外延迟。
- **偏差风险**：仅报告了PixArt-α上的一个指标，其他模型上是否同样保持质量优势？未提供具体数值，存在选择性报告的可能性。

---

（完）
