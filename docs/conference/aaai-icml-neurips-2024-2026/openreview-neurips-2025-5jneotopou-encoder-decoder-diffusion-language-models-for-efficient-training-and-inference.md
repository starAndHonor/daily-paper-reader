---
title: Encoder-Decoder Diffusion Language Models for Efficient Training and Inference
title_zh: 编码器-解码器扩散语言模型以实现高效训练和推理
authors: "Marianne Arriola, Yair Schiff, Hao Phung, Aaron Gokaslan, Volodymyr Kuleshov"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=5jneOToPou"
tags: ["query:diff-accel"]
score: 7.0
evidence: 通过编码器-解码器架构加速离散扩散推理
tldr: 离散扩散模型通常使用解码器架构，每次去噪步骤需调用完整网络，计算成本高。本文提出编码器-解码器架构，编码器负责表示干净token，轻量解码器迭代细化噪声序列，从而加速推理。实验表明在多个文本生成任务上，该方法在保持质量的同时显著提升速度。该架构为离散扩散模型的高效推理提供了新思路。
source: NeurIPS-2025-Accepted
selection_source: conference_retrieval
motivation: 现有解码器架构在每一步需要完整网络调用，计算成本高。
method: 提出编码器-解码器架构，分离干净token表示和去噪计算。
result: 实现更快的推理速度，同时保持生成质量。
conclusion: 编码器-解码器架构有效加速了离散扩散模型的推理。
---

## Abstract
Discrete diffusion models enable parallel token sampling for faster inference than autoregressive approaches. However, prior diffusion models use a decoder-only architecture, which requires sampling algorithms that invoke the full network at every denoising step and incur high computational cost. Our key insight is that discrete diffusion models perform two types of computation: 1) representing clean tokens and 2) denoising corrupted tokens, which enables us to use separate modules for each task. We propose an encoder-decoder architecture to accelerate discrete diffusion inference, which relies on an encoder to represent clean tokens and a lightweight decoder to iteratively refine a noised sequence. We also show that this architecture enables faster training of block diffusion models, which partition sequences into blocks for better quality and are commonly used in diffusion language model inference. We introduce a framework for **E**fficient **E**ncoder-**D**ecoder **D**iffusion (E2D2), consisting of an architecture with specialized training and sampling algorithms, and we show that E2D2 achieves superior trade-offs between generation quality and inference throughput on summarization, translation, and mathematical reasoning tasks.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
离散扩散模型能够以并行方式采样 token，从而在推理速度上优于自回归模型。然而，现有离散扩散模型普遍采用**解码器-only 架构**，每个去噪步骤都需要调用完整网络，计算成本高昂。论文的核心洞察是：离散扩散模型中存在两类计算——**表示干净 token** 与**去噪被破坏的 token**，因此可以将这两类任务分离到不同模块。基于此，作者提出了编码器-解码器架构，旨在加速离散扩散模型的推理和训练，并提升生成质量与吞吐量的平衡。

## 2. 论文提出的方法论：核心思想、关键技术细节
- **核心思想**：将模型分为编码器和解码器两部分。编码器负责表示干净 token（提供全局上下文），轻量级解码器则迭代地细化噪声序列。通过分离计算，每次去噪步骤只需运行轻量解码器，编码器的输出可复用，从而减少整体计算开销。
- **关键技术细节**：
  - 提出 **Efficient Encoder-Decoder Diffusion (E2D2)** 框架，包含专门设计的架构、训练算法和采样算法。
  - 训练时，编码器学习将干净序列映射到潜在表示；解码器基于该表示和当前噪声序列预测去噪结果。
  - 采样时，编码器只需执行一次或少数几次，解码器进行多次轻量迭代。
  - 同时，该架构支持更快速的**块扩散模型**（block diffusion models）训练：将序列划分为块进行建模，可在保持质量的同时提升效率。
- **公式/算法流程**（文字说明）：
  - 定义前向扩散过程：逐步向原始序列添加噪声。
  - 训练目标：最小化解码器预测噪声与真实噪声之间的差异，同时优化编码器表示。
  - 推理流程：输入部分噪声序列 → 编码器提取干净表示 → 解码器迭代去噪（每步输出更干净的 token 序列）。

## 3. 实验设计
- **使用数据集/场景**：摘要提及在三个任务上评估：**摘要生成（summarization）**、**机器翻译（translation）** 和 **数学推理（mathematical reasoning）**。具体数据集名称（如 CNN/DailyMail、WMT、GSM8K 等）未在摘要中给出，需参考原文。
- **Benchmark**：与已有的离散扩散模型（解码器-only 架构）以及可能自回归模型进行对比，重点比较生成质量（如 BLEU、ROUGE、准确率）与推理吞吐量（速度）。
- **对比方法**：未明确列出，但推测包括标准解码器-only 扩散模型（如 D3PM、MDLM、SSD-LM 等）和自回归基线。

## 4. 资源与算力
论文摘要及元数据中**未明确说明**使用的 GPU 型号、数量、训练时长等具体算力信息。需要查看全文细节。通常此类论文会在实验设置部分给出，但当前摘要缺失。这一点需要指出。

## 5. 实验数量与充分性
- 实验覆盖了三个不同领域的任务（摘要、翻译、数学推理），具有一定的多样性。
- 但未提及消融实验数量（如对编码器-解码器架构变体、不同噪声调度、块大小等的分析），也未给出具体的数据集规模或统计显著性检验。
- 从摘要看，仅声称取得了“superior trade-offs between generation quality and inference throughput”，缺乏详细的数字支撑和对比结果，因此实验充分性**尚不明确**。作为 NeurIPS 2025 接收论文，通常会有更全面的实验，但当前公开信息不足以判断是否完全充分、客观、公平。

## 6. 论文的主要结论与发现
- 编码器-解码器架构能够有效加速离散扩散模型的推理，同时保持或提升生成质量。
- E2D2 框架在摘要、翻译、数学推理任务上实现了生成质量与推理吞吐量之间的更优权衡。
- 该架构还加快了块扩散模型的训练速度，有利于实际应用。

## 7. 优点
- **方法创新**：将离散扩散模型中的两类计算（表示干净 token 和去噪）分离到不同模块，直觉明确且易于实现。
- **效率提升**：通过轻量解码器减少每次去噪步骤的计算量，有望显著降低推理延迟。
- **通用性**：可应用于多种文本生成任务，且与块扩散模型兼容，具有实际部署价值。
- **理论解释清晰**：论文明确指出了 decoder-only 架构的瓶颈，并提供了对应的解决方案。

## 8. 不足与局限
- **实验细节缺失**：未在摘要中提供具体数据集、基线方法、数值结果，难以直接评估有效性的强度。
- **算力资源未说明**：影响可复现性，无法判断方法在资源消耗上的实际优势。
- **潜在偏差风险**：仅报告了三个任务的正面结果，未提及失败场景或局限性（如对长文本或特殊领域的效果、编码器-解码器架构的参数量增加等）。
- **应用限制**：编码器需要一次或少量前向传播，但若序列极长或编码器本身计算量大，可能削弱加速效果。此外，模型依赖于离散噪声过程，可能不适用于连续扩散任务。

（完）
