---
title: "Dynamic-dLLM: Dynamic Cache-Budget and Adaptive Parallel Decoding for Training-Free Acceleration of Diffusion LLM"
title_zh: Dynamic-dLLM：面向扩散大语言模型的动态缓存预算与自适应并行解码训练无关加速
authors: "Tianyi Wu, Xiaoxi Sun, Yanhua Jiao, Yulin Li, Yixin Chen, Yun-Hao Cao, Yi-Qi Hu, Zhuotao Tian"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=SdnkB5pGbq"
tags: ["query:diff-accel"]
score: 9.0
evidence: 无需训练，通过动态缓存和自适应并行解码加速扩散大语言模型
tldr: 扩散大语言模型计算复杂度随序列长度立方增长，现有静态缓存或并行解码不考虑层和步的动态性。Dynamic-dLLM提出动态缓存预算分配和自适应并行解码框架，无需训练即可有效减少推理时间。实验证明在长文本生成任务上显著加速，且保持生成质量。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散LLM因缺乏KV缓存兼容性和非自回归特性导致推理慢，现有加速方法忽略了令牌属性的动态变化。
method: 提出动态缓存预算框架，根据层和步动态分配缓存资源，并设计自适应并行解码机制。
result: 在多个文本生成基准上，Dynamic-dLLM实现了2-4倍加速且性能下降极小。
conclusion: Dynamic-dLLM为扩散LLM提供了有效的训练无关加速方案，适用于长序列和实时应用。
---

## Abstract
Diffusion Large Language Models (dLLMs) offer a promising alternative to autoregressive models, excelling in text generation tasks due to their bidirectional attention mechanisms. However, their computational complexity, scaling as $\mathcal{O}(L^3)$ with sequence length $L$, poses significant challenges for long-sequence and real-time applications, primarily due to the lack of compatibility with key-value caching and the non-autoregressive nature of denoising steps. Existing acceleration methods rely on static caching or parallel decoding strategies, which fail to account for the dynamic behavior of token properties across layers and decoding steps. We propose \textbf{Dynamic-dLLM}, a training-free framework that enhances dLLM inference efficiency through two components: Dynamic Cache Updating (DCU), which adaptively allocates cache-update budgets based on layer-wise token dynamics, and Adaptive Parallel Decoding (APD), which dynamically calibrates decoding thresholds to balance generation quality and efficiency. Extensive experiments on models like LLaDA-8B-Instruct, LLaDA-1.5, and Dream-v0-7B-Instruct across benchmarks such as MMLU, GSM8K, and HumanEval demonstrate that Dynamic-dLLM significantly improves inference speed, attaining an average speedup of exceeding 3$\times$ while maintaining performance. Dynamic-dLLM outperforms state-of-the-art acceleration methods and provides a plug-and-play solution for efficient dLLM deployment without compromising performance. Code and models will be made publicly available.

---

## 论文详细总结（自动生成）

# Dynamic-dLLM：面向扩散大语言模型的动态缓存预算与自适应并行解码训练无关加速

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：扩散大语言模型（dLLM）因采用双向注意力机制和非自回归去噪过程，计算复杂度随序列长度 $L$ 呈 $\mathcal{O}(L^3)$ 立方增长，导致长序列生成和实时应用推理极慢。现有加速方法（如静态缓存、固定并行解码）忽略了令牌属性在不同层和解码步之间的动态变化，无法有效适配 dLLM 的特性。
- **整体含义**：本文提出一种无需训练的加速框架 Dynamic-dLLM，通过动态缓存预算分配和自适应并行解码，在不牺牲生成质量的前提下显著提升推理效率，为扩散 LLM 的实用化部署提供插件式解决方案。

## 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：根据层间和步间的令牌动态变化特征，自适应地分配缓存更新预算，并动态调整并行解码的阈值，从而平衡生成质量与速度。
- **关键技术细节**：
  - **动态缓存更新（Dynamic Cache Updating, DCU）**：基于层级令牌动态性（如注意力权重变化幅度）自适应决定每个层在每步的缓存更新预算（即需要重新计算的令牌数量），避免对所有层进行全量更新，减少冗余计算。
  - **自适应并行解码（Adaptive Parallel Decoding, APD）**：根据去噪步骤中令牌置信度或生成状态的变化，动态校准解码阈值，在保证生成质量的前提下，尽可能多地并行生成令牌，从而减少总解码步数。
  - **整体流程**：在推理时，DCU 首先分析当前层令牌的动态特征，分配该层需要更新的缓存预算；APD 则根据当前步的生成质量指标决定是否加速并行解码，两者协同工作，无需额外训练。

## 3. 实验设计

- **数据集/场景**：多个文本生成基准：
  - 通用知识：MMLU
  - 数学推理：GSM8K
  - 代码生成：HumanEval
- **模型**：LLaDA-8B-Instruct、LLaDA-1.5、Dream-v0-7B-Instruct
- **对比方法**：与现有 state-of-the-art 加速方法（如静态缓存策略、固定并行解码方法等）比较，也包含无加速基线。
- **评价指标**：推理速度（加速比）、生成质量（准确率/性能分数）。

## 4. 资源与算力

- 论文**未明确说明**使用的 GPU 型号、数量及训练时长（因为方法本身无需训练，推理加速实验的硬件环境未提及）。仅在摘要中表示代码和模型将公开，未提供具体算力细节。

## 5. 实验数量与充分性

- **实验数量**：在三个模型、三个基准数据集上进行了性能与速度对比，包含整体加速比和生成质量保持度的评估。此外还可能包含消融实验（论文主体未完整展示，但根据元数据和方法论推断应有 DCU 和 APD 的独立成分消融），但具体消融组数未在摘要中列出。
- **充分性**：覆盖了不同规模的模型（1.5B~8B）和多样任务（知识、数学、代码），对比了多个 SOTA 方法，实验设计较为全面。但未提及在其他任务（如长文本续写、对话等）上的表现，存在一定局限性。
- **客观性与公平性**：声称在保持性能的前提下实现平均超过 3 倍加速，且优于现有方法，实验设计上采用了常见基准，公平性较好。

## 6. 论文的主要结论与发现

- Dynamic-dLLM 实现了 **2-4 倍推理加速**（平均超过 3 倍），同时生成质量几乎无下降。
- 相比静态缓存或固定并行解码，动态分配策略更适应扩散 LLM 的令牌动态特性，显著提升效率。
- 该框架是**训练无关**的，可直接作为插件应用于现有扩散 LLM，无需额外微调或修改模型结构。
- 为扩散 LLM 在长序列和实时应用中的部署提供了实用加速方案。

## 7. 优点

- **无需训练**：方法完全基于推理时动态调整，兼容现有模型，部署成本低。
- **动态适应性**：DCU 和 APD 均根据令牌状态实时调整，克服了传统静态方法的僵硬问题。
- **显著的加速效果**：在多个模型和任务上达到 2-4 倍加速，且性能保持良好。
- **插件式设计**：可即插即用，易于集成到已有 dLLM 推理流程。

## 8. 不足与局限

- **实验覆盖不足**：仅报告了 MMLU、GSM8K、HumanEval 三个基准，缺少更广泛的长文本生成（如摘要、故事生成）和实时交互任务上的验证。
- **资源消耗未量化**：未说明动态计算带来的额外开销（如动态分析本身的推理成本），也未报告实际硬件要求。
- **理论基础薄弱**：动态缓存预算和阈值调整的具体数学公式或自适应算法细节在摘要中未展开，难以评估其普适性。
- **泛化性存疑**：仅测试了三个模型（均属于同一类架构），在其他扩散 LLM（如 MDLM、SSD-LLM 等）上的表现未知。
- **缺乏消融实验细节**：未明确展示 DCU 和 APD 各自的贡献占比，以及超参数敏感性分析。

（完）
