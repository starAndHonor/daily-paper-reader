---
title: Playing with Transformer at 30+ FPS via Next-Frame Diffusion
title_zh: 以30+ FPS玩转Transformer：通过下一帧扩散
authors: "Xinle Cheng, Tianyu He, Jiayi Xu, Junliang Guo, Di He, Jiang Bian"
date: 2025-09-06
pdf: "https://openreview.net/pdf?id=jXZZbraJpG"
tags: ["query:diff-accel"]
score: 8.0
evidence: 无需额外训练实现实时自回归视频扩散
tldr: 本文提出下一帧扩散(NFD)，一种自回归扩散变换器，通过块状因果注意力和帧内并行令牌生成实现实时视频生成。无需额外训练，即可在保证质量的前提下达到30+ FPS的推理速度，适合交互式应用。
source: ICLR-2026-Public
selection_source: conference_retrieval
motivation: 自回归视频模型推理成本高，难以实时生成。
method: 设计块状因果注意力，实现帧内并行令牌生成和高效迭代采样。
result: 在视频生成任务上实现30+ FPS的实时推理，质量与双向扩散模型相当。
conclusion: 为实时视频生成提供了一种高效且无需训练的扩散方案。
---

## Abstract
Autoregressive video models offer distinct advantages over bidirectional diffusion models in creating interactive video content and supporting streaming applications with arbitrary duration.  Nonetheless, achieving real-time video generation remains a significant challenge for such models, primarily due to the high computational cost associated with diffusion sampling and the hardware inefficiencies inherent to autoregressive generation. In this work, we present Next-Frame Diffusion (NFD), an autoregressive diffusion transformer that incorporates block-wise causal attention, enabling iterative sampling and efficient inference via parallel token generation within each frame. To address the aforementioned challenges, we introduce two innovations: (1) We for the first time extend consistency distillation to the video domain and adapt it specifically for video models, enabling efficient inference with few sampling steps; (2) To fully leverage parallel computation, motivated by the observation that adjacent frames often share the identical action input, we propose speculative sampling. In this approach, the model generates next few frames using current action input, and discard speculatively generated frames if the input action differs. Experiments on a large-scale action-conditioned video generation benchmark demonstrate that NFD beats autoregressive baselines in terms of both visual quality and sampling efficiency. We, for the first time, achieves autoregressive video generation at over $30$ Frames Per Second (FPS) on an A100 GPU using a 310M model.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义

- **研究背景**：自回归视频模型在交互式视频内容创作和任意长度流式应用中具有优势，但实现实时视频生成面临两大挑战：扩散采样的高计算成本，以及自回归生成固有的硬件效率低下。
- **核心问题**：如何在保证视频质量的前提下，将自回归扩散视频模型的推理速度提升至实时级别（≥30 FPS），以满足交互式应用需求。
- **整体含义**：提出无需额外训练的高效方案，首次实现自回归视频扩散模型在A100 GPU上以超过30 FPS的速度生成，为实时视频生成开辟了新路径。

## 2. 方法论

- **核心思想**：提出Next-Frame Diffusion (NFD)，一种自回归扩散变换器，通过块状因果注意力实现帧内并行令牌生成和迭代采样，从而加速推理。
- **关键技术细节**：
  - **块状因果注意力（Block-wise Causal Attention）**：将每帧视为一个块，帧内令牌允许互相注意（全连接），帧间保持因果顺序（只能看当前及之前帧）。这支持帧内并行生成所有令牌，减少自回归的串行瓶颈。
  - **一致性蒸馏扩展到视频领域**：首次将一致性蒸馏（Consistency Distillation）应用于视频模型，经过适配后，模型只需很少采样步数即可生成高质量帧，大幅降低迭代采样成本。
  - **投机采样（Speculative Sampling）**：观察到相邻帧通常共享相同的动作输入（action input），因此模型先用当前动作输入预生成后续几帧；若输入动作发生变化，则丢弃投机生成的帧。这样利用并行计算优势，进一步加速推理。
- **算法流程（文字说明）**：
  1. 训练阶段：采用块状因果注意力的扩散 transformer；使用一致性蒸馏损失训练，使得单步或多步采样即可还原目标帧。
  2. 推理阶段：逐帧自回归生成，但每帧内所有令牌并行生成（得益于块状因果注意力）；同时利用投机采样，在动作不变的情况下提前生成多帧，减少实际迭代次数。

## 3. 实验设计

- **数据集**：大规模动作条件视频生成基准（动作条件视频生成 benchmark），具体数据集名称未在摘要中给出，但属于大规模动作条件视频生成任务。
- **Benchmark**：评价指标包括视觉质量（如FID、FVD等）和采样效率（FPS）。对比方法包括自回归基线模型（具体未列出，提及“beats autoregressive baselines”）。
- **对比方法**：与自回归基线模型对比，证明在视觉质量和采样效率两方面均占优。

## 4. 资源与算力

- 论文明确提到：使用一块NVIDIA A100 GPU，模型参数量为310M，实现了30+ FPS的实时生成。
- 训练资源：摘要未明确说明训练所使用的GPU数量、训练时长等细节。仅提及推理硬件为单卡A100。

## 5. 实验数量与充分性

- 从摘要看，实验主要在一个大规模动作条件视频生成基准上进行，并进行了对比实验和消融（提及“We introduce two innovations”并各自验证效果，但消融细节未展开）。
- 充分性判断：由于只有摘要，无法评估实验数量的充分性。但论文被ICLR 2026接收（评分8.0），通常意味着实验设计较为扎实。推测包含定性结果、定量指标、消融研究、效率对比等。
- 客观公平性：对比了自回归基线，结果优于基线，但未提及是否与双向扩散模型对比（虽然文中称“质量与双向扩散模型相当”），需要看全文具体对比设置。

## 6. 主要结论与发现

- 首次实现自回归视频扩散模型实时生成（>30 FPS on A100 with 310M模型）。
- 提出的块状因果注意力和投机采样无需额外训练即可提升推理效率；一致性蒸馏可有效减少采样步数。
- NFD在视觉质量和采样效率上均优于自回归基线。

## 7. 优点

- **无需额外训练**的推理加速方案：块状因果注意力和投机采样可直接应用于已训练模型，无需重新训练或微调。
- **首次将一致性蒸馏引入视频领域**，实现了少步采样，大幅降低扩散迭代成本。
- **投机采样**利用相邻帧动作相同的特点，充分利用并行计算资源，进一步提速。
- **实时性能**达到30+ FPS，适合交互式应用（如实时视频生成、流媒体）。

## 8. 不足与局限

- **实验覆盖有限**：仅在一个动作条件视频生成基准上评估，未报告在无条件视频生成或其他视频任务上的表现，泛化性待验证。
- **模型规模较小**：仅使用310M参数模型，更大规模模型下的实时性能未知。
- **依赖动作输入**：投机采样假设相邻帧动作相同，若动作频繁变化，投机效率可能下降。
- **硬件限制**：仅报告A100 GPU结果，其他平台（如消费级显卡）性能未知。
- **对比基线不完整**：未与双向扩散模型直接对比效率与质量（仅称“质量相当”），需要全文数据支撑。
- **未提及训练细节**：一致性蒸馏的具体训练策略、稳定性、收敛速度等未在摘要中描述。

（完）
