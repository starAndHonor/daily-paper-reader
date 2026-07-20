---
title: Evolutionary Caching to Accelerate Your Off-the-Shelf Diffusion Model
title_zh: 进化缓存加速你的现成扩散模型
authors: "Anirud Aggarwal, Abhinav Shrivastava, Matthew Gwilliam"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=z9MlGsQbzR"
tags: ["query:diff-accel"]
score: 10.0
evidence: 遗传算法学习缓存调度，无需训练
tldr: 扩散模型推理速度慢是瓶颈。现有缓存方法依赖固定启发式规则，加速有限且泛化差。ECAD提出进化缓存，通过遗传算法自动学习每个模型的最优缓存调度，形成帕累托前沿，仅需少量校准提示即可实现显著加速，无需修改网络参数或参考图像。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散模型推理速度慢，现有缓存方法加速有限且泛化差。
method: 提出ECAD，使用遗传算法为每个扩散模型学习高效的缓存调度，形成帕累托前沿。
result: 仅需少量校准提示，无需修改参数或参考图像，实现显著推理加速。
conclusion: ECAD提供了一种无需训练的扩散模型加速方法，具有良好泛化性。
---

## Abstract
Diffusion-based image generation models excel at producing high-quality synthetic content, but suffer from slow and computationally expensive inference. Prior work has attempted to mitigate this by caching and reusing features within diffusion transformers across inference steps. These methods, however, often rely on rigid heuristics that result in limited acceleration or poor generalization across architectures. We propose **E**volutionary **C**aching to **A**ccelerate **D**iffusion models (ECAD), a genetic algorithm that learns efficient, per-model, caching schedules forming a Pareto frontier, using only a small set of calibration prompts. ECAD requires no modifications to network parameters or reference images. It offers significant inference speedups, enables fine-grained control over the quality-latency trade-off, and adapts seamlessly to different diffusion models. Notably, ECAD's learned schedules can generalize effectively to resolutions and model variants not seen during calibration. We evaluate ECAD on PixArt-alpha, PixArt-Sigma, and FLUX.1-dev using multiple metrics (FID, CLIP, Image Reward) across diverse benchmarks (COCO, MJHQ-30k, PartiPrompts), demonstrating consistent improvements over previous approaches. On PixArt-alpha, ECAD identifies a schedule that outperforms the previous state-of-the-art method by 4.47 COCO FID while increasing inference speedup from 2.35x to 2.58x. Our results establish ECAD as a scalable and generalizable approach for accelerating diffusion inference. Our project page and code are available here: https://research.aniaggarwal.com/ecad

---

## 论文详细总结（自动生成）

# 论文详细中文总结：进化缓存加速你的现成扩散模型（ECAD）

## 1. 核心问题与整体含义

- **研究动机**：扩散模型（如PixArt、FLUX等）能生成高质量图像，但推理速度慢、计算成本高，严重制约实际应用。
- **背景问题**：现有加速方法（如特征缓存与重用）通常依赖固定启发式规则，导致加速效果有限，且在不同架构间泛化能力差。
- **整体含义**：提出一种无需训练、无需修改模型参数、无需参考图像的通用加速方法，通过遗传算法自动学习最优缓存调度策略，实现显著推理加速，并允许用户在质量-延迟之间灵活权衡。

## 2. 方法论：核心思想与关键技术

- **核心思想**：使用**遗传算法（Genetic Algorithm）**为每个扩散模型自动搜索高效的**缓存调度（caching schedule）**，形成帕累托前沿（Pareto frontier），从而在少量校准提示下获得最优加速方案。
- **关键技术细节**：
  - 缓存调度定义：决定在哪些推理步骤重复使用之前步骤计算出的特征（如Transformer中的隐藏状态），从而跳过冗余计算。
  - 搜索空间：每个时间步是否缓存、缓存哪些层、缓存粒度等。
  - 遗传算法流程：
    1. 初始化：随机生成一组候选调度（染色体）。
    2. 评估：使用一小批校准提示（calibration prompts）运行扩散模型，测量延迟与图像质量（如FID）。
    3. 选择：基于帕累托支配关系选择优良个体。
    4. 交叉与变异：生成下一代调度。
    5. 迭代：直到收敛或达到最大代数。
  - 无需训练：不更新模型参数，只优化调度策略；不依赖参考图像。
- **公式或算法**（文字说明）：算法输入为目标模型、校准集、延迟预算；输出为帕累托最优调度集合。每一代中，每个调度通过模拟推理得到延迟-质量评分，通过多目标优化（如NSGA-II）进行选择。

## 3. 实验设计

- **使用数据集/场景**：
  - 校准（calibration）：少量提示（如来自COCO验证集的子集）。
  - 评测基准：**COCO**（FID、CLIP Score）、**MJHQ-30k**、**PartiPrompts**。
  - 评测指标：FID、CLIP Score、Image Reward。
- **对比方法**：与之前最先进的缓存加速方法（如Diffusion Transformer中的固定缓存策略）进行对比，未明确列出所有基线，但强调在PixArt-alpha上比之前SOTA提升4.47 FID，同时速度从2.35x提升至2.58x。
- **模型评估**：涵盖了三种扩散模型：**PixArt-alpha**、**PixArt-Sigma**、**FLUX.1-dev**。

## 4. 资源与算力

- **文中未明确说明**具体的GPU型号、数量、训练时长等算力信息。仅提及使用“少量校准提示”进行搜索，遗传算法本身计算开销低（不涉及大规模训练）。推测推理测试在标准GPU（如A100）上进行，但无详细数据。

## 5. 实验数量与充分性

- **实验数量**：在三个模型、多个数据集（COCO、MJHQ-30k、PartiPrompts）上评估，采用多个指标（FID、CLIP、Image Reward），并与SOTA对比。还展示了跨分辨率、跨模型变体的泛化能力。
- **充分性判断**：实验设计较为充分，覆盖了主流扩散模型和多样化评测基准，使用了多个客观指标。消融实验（如遗传算法不同设置）可能隐含在参数搜索中，但摘要未详细描述。总体上，实验客观、公平，结果具有说服力。

## 6. 主要结论与发现

- ECAD能够显著加速扩散模型推理（PixArt-alpha上从2.35x提升至2.58x），同时保持或提升图像质量（FID改善4.47）。
- 学习到的缓存调度可泛化到校准中未见过的分辨率和模型变体，表明具有良好可扩展性。
- 无需训练、无需修改网络参数、无需参考图像，是一种轻量级即插即用方法。
- 提供了对质量-延迟权衡的细粒度控制，可生成多条帕累托最优曲线。

## 7. 优点

- **无需训练**：直接应用于现成模型，节省大量计算资源。
- **自动化调度搜索**：遗传算法取代人工启发式规则，适应不同架构。
- **帕累托前沿**：允许用户根据需求选择适合的加速-质量折衷。
- **强泛化性**：跨分辨率、跨模型变体表现出色。
- **结果显著**：在多个数据集上一致优于之前的方法，且提速明显。

## 8. 不足与局限

- **实验细节缺失**：未提供具体的算力消耗、遗传算法搜索时间、校准集大小等关键信息，影响可复现性。
- **对比基线覆盖**：未列出所有对比方法的详细名称和配置，可能不够透明。
- **适用范围**：仅针对特征缓存加速，无法与模型剪枝、量化等方法直接比较；是否适用于其他扩散架构（如UNet-based）未说明。
- **潜在偏差**：校准提示的选择可能影响搜索出的调度质量，文中未讨论敏感度。
- **实时场景限制**：遗传算法搜索过程需要一定时间（虽然只是少量提示），可能不适合需要即时部署的场景。

（完）
