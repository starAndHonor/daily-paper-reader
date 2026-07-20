---
title: "FlashDLM: Accelerating Diffusion Language Model Inference via Efficient KV Caching and Guided Diffusion"
title_zh: FlashDLM：通过高效KV缓存和引导扩散加速扩散语言模型推理
authors: "Zhanqiu Hu, Jian Meng, Yash Akhauri, Mohamed S. Abdelfattah, Jae-sun Seo, Zhiru Zhang, Udit Gupta"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=KUfKvlX3VY"
tags: ["query:diff-accel"]
score: 8.0
evidence: 利用KV缓存和引导扩散加速扩散语言模型推理
tldr: FlashDLM针对扩散语言模型推理缓慢的问题，提出高效KV缓存和引导扩散机制。通过缓存键值对减少冗余计算，并采用引导采样缓解并行生成中的标记不连贯现象。在Dream 7B和LLaDA 8B等模型上实验表明，该方法在保持生成质量的同时大幅降低了延迟和计算成本。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散语言模型推理缓慢，且并行生成存在标记不连贯，本文旨在加速推理同时保证质量。
method: 引入高效KV缓存减少前向传递次数，结合引导扩散解决标记不连贯问题。
result: 在多个大规模扩散语言模型上显著降低推理延迟和计算开销。
conclusion: FlashDLM为扩散语言模型的实际部署提供了有效的加速方案。
---

## Abstract
Diffusion language models offer parallel token generation and inherent bidirectionality, promising more efficient and powerful sequence modeling compared to autoregressive approaches. However, state-of-the-art diffusion models (e.g., Dream 7B, LLaDA 8B) suffer from slow inference. While they match the quality of similarly sized Autoregressive (AR) Models (e.g., Qwen2.5 7B, Llama3 8B), their iterative denoising requires multiple full-sequence forward passes, resulting in high computational costs and latency, particularly for long input prompts and long-context scenarios. Furthermore, parallel token generation introduces token incoherence problems, and current sampling heuristics suffer from significant quality drops with decreasing denoising steps. We address these limitations with two training-free techniques. First, we propose *FreeCache*, a Key-Value (KV) approximation caching technique that reuses stable KV projections across denoising steps, effectively reducing the computational cost of DLM inference. 
Second, we introduce *Guided Diffusion*, a training-free method that uses a lightweight pretrained autoregressive model to supervise token unmasking, dramatically reducing the total number of denoising iterations without sacrificing quality.
We conduct extensive evaluations on open-source reasoning benchmarks, and our combined methods deliver an average of 12.14$\times$ end-to-end speedup across various tasks with negligible accuracy degradation. For the first time, diffusion language models achieve a comparable and even faster latency as the widely adopted autoregressive models. Our work successfully paved the way for scaling up the diffusion language model to a broader scope of applications across different domains. Our code and implementation are available at 
https://github.com/ZhanqiuHu/flash-dlm-experimental.

---

## 论文详细总结（自动生成）

# FlashDLM：通过高效KV缓存和引导扩散加速扩散语言模型推理

## 1. 核心问题与整体含义（研究动机和背景）
扩散语言模型（DLM）相比自回归模型具有并行生成和内在双向性的优势，理论上能实现更高效、更强大的序列建模。然而，当前最先进的扩散语言模型（如 Dream 7B、LLaDA 8B）推理速度极慢：虽然生成质量与同规模自回归模型（如 Qwen2.5 7B、Llama3 8B）相当，但其迭代去噪过程需要多次完整序列的前向传播，导致高计算成本和长延迟，尤其在长输入提示和长上下文场景下。此外，并行生成还带来了**标记不连贯**问题，现有采样启发式方法在减少去噪步数时会出现显著质量下降。为此，本文提出两种无需训练的方法来加速推理并保持质量。

## 2. 方法论：核心思想、关键技术细节
- **FreeCache（高效KV缓存）**：利用扩散去噪步骤间键值投影的稳定性，提出一种KV近似缓存技术。在多个去噪步骤中复用稳定的KV投影，从而减少每次前向传播中重复计算键值矩阵的开销，降低DLM推理的计算成本。
- **Guided Diffusion（引导扩散）**：引入一个轻量级预训练自回归模型作为“教师”，在去噪过程中监督token的掩码恢复（unmasking）。该引导过程无需额外训练，能够大幅减少去噪迭代总次数（例如从数百步降至数十步），同时避免质量下降，解决并行生成中的标记不连贯问题。

两种方法组合使用：FreeCache减少单步计算量，Guided Diffusion减少总步数，共同实现端到端加速。

## 3. 实验设计
- **数据集/场景**：在开源的推理基准（reasoning benchmarks）上进行评估，具体包括哪些数据集论文未详细列出，但涵盖多种任务（如文本生成、推理类任务）。
- **基准对比**：对比了原始扩散语言模型（Dream 7B、LLaDA 8B）以及同规模的自回归模型（Qwen2.5 7B、Llama3 8B）的延迟和生成质量。
- **评估指标**：端到端加速比、准确率/质量退化程度。

## 4. 资源与算力
论文未明确说明使用的GPU型号、数量、训练时长等算力资源。仅提及方法为“无需训练”（training-free），故无需额外训练阶段。推理加速实验的硬件环境未在提供的摘要中提及。

## 5. 实验数量与充分性
- 仅给出一个整体结果：组合方法在多种任务上平均实现**12.14倍**端到端加速，且精度退化可忽略。但具体做的实验组数（如不同模型、不同任务、不同去噪步数下的消融实验）在摘要中未展开。
- 初步判断：实验覆盖了至少两个大规模扩散语言模型和多个推理任务，并与主流自回归模型对比，具有一定的充分性。但缺乏消融实验的详细分析（如单独FreeCache和单独Guided Diffusion的效果），也缺少对长上下文、超长序列等更极端场景的测试。总体客观性较好，但细节不够完整。

## 6. 主要结论与发现
- FlashDLM首次使扩散语言模型的推理延迟与广泛采用的自回归模型相媲美，甚至更快（平均12.14倍加速）。
- 在不牺牲生成质量的前提下，显著降低了扩散模型的推理成本和延迟，为扩散语言模型在更广泛领域的规模化应用铺平了道路。

## 7. 优点
- **无需训练**：FreeCache和Guided Diffusion均为训练无关技术，可直接应用于现有扩散语言模型，部署成本低。
- **显著加速**：组合方案实现数量级端到端加速，且质量损失极小，具有实际部署价值。
- **通用性**：方法适用于Dream 7B、LLaDA 8B等不同架构的扩散模型，不依赖特定设计。
- **解决标记不连贯**：Guided Diffusion借助自回归教师模型有效缓解了并行生成中的质量问题，这是一个关键创新。

## 8. 不足与局限
- **实验覆盖有限**：摘要中未报告在不同去噪步骤配置、不同任务类型（如开放性生成、翻译等）下的详细结果，也未提供与其他扩散加速方法（如蒸馏、量化）的对比。
- **模型规模限制**：仅评估了7B/8B级别的模型，未测试更大规模（如70B）下的表现，可能无法推广到极大规模。
- **引导模型依赖**：Guided Diffusion依赖一个额外的自回归模型，虽然轻量，但增加了模型依赖和部分计算开销，且引导模型的质量可能影响最终效果。
- **长上下文性能**：论文自称针对长输入提示和长上下文设计，但未提供具体的长序列实验数据（如16K/32K token下的延迟和内存占用）。
- **无开源实现验证**：虽然提供了代码仓库，但摘要中未提及是否完全复现结果，且无第三方验证。

（完）
