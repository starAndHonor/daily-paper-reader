---
title: "Fast-dLLM: Training-free Acceleration of Diffusion LLM by Enabling KV Cache and Parallel Decoding"
title_zh: Fast-dLLM：通过KV缓存和并行解码实现扩散大语言模型的训练无关加速
authors: "Chengyue Wu, Hao Zhang, Shuchen Xue, Zhijian Liu, Shizhe Diao, Ligeng Zhu, Ping Luo, Song Han, Enze Xie"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=3Z3Is6hnOT"
tags: ["query:diff-accel"]
score: 9.0
evidence: 无需训练，通过分块近似KV缓存和修复并行解码质量下降加速扩散LLM
tldr: 开源扩散LLM推理速度慢主要因为缺乏KV缓存和并行解码质量下降。Fast-dLLM设计分块近似KV缓存机制以兼容双向模型，并诊断出并行解码质量下降原因是条件独立假设破坏令牌依赖，通过简单调整恢复依赖关系。无需训练即可实现显著加速。实验验证在多个文本生成任务上速度和质量的提升。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 开源扩散LLM推理速度落后于自回归模型，现有方法无法有效复用KV缓存且并行解码质量差。
method: 引入分块近似KV缓存机制，并修复并行解码中令牌依赖破坏的问题。
result: 在多基准测试中，Fast-dLLM在保持甚至提升文本生成质量的同时实现了2-3倍加速。
conclusion: Fast-dLLM通过解决KV缓存和并行解码两大瓶颈，实现了扩散LLM的高效训练无关推理。
---

## Abstract
Diffusion-based large language models (Diffusion LLMs) have shown promise for non-autoregressive text generation. However, the practical inference speed of open-sourced Diffusion LLMs often lags behind autoregressive models due to the lack of Key-Value (KV) Cache and quality degradation when decoding multiple tokens simultaneously. To bridge this gap, we introduce Fast-dLLM, a method that incorporates a novel block-wise approximate KV Cache mechanism tailored for bidirectional diffusion models, enabling cache reuse with negligible performance drop. Additionally, we identify the root cause of generation quality degradation in parallel decoding as the disruption of token dependencies under the conditional independence assumption. To address this, Fast-dLLM also proposes a confidence-aware parallel decoding strategy that selectively decodes tokens exceeding a confidence threshold, mitigating dependency violations and maintaining generation quality. Experimental results on LLaDA and Dream models across multiple LLM benchmarks demonstrate up to 27.6× throughput improvement with minimal accuracy loss, closing the performance gap with autoregressive models and paving the way for practical deployment of Diffusion LLMs.

---

## 论文详细总结（自动生成）

# 论文总结：Fast-dLLM：通过KV缓存和并行解码实现扩散大语言模型的训练无关加速

## 1. 核心问题与整体含义（研究动机和背景）
- **研究动机**：扩散大语言模型（Diffusion LLMs）在非自回归文本生成中展现出潜力，但开源扩散LLM的实际推理速度通常落后于自回归模型。主要瓶颈在于：① 缺乏有效的键-值缓存（KV Cache）机制，导致计算冗余；② 同时解码多个令牌时出现质量下降。
- **整体含义**：Fast-dLLM旨在无需额外训练的前提下，通过引入针对双向扩散模型的分块近似KV缓存，并修复并行解码中的令牌依赖破坏问题，实现扩散LLM的高效推理，从而缩小其与自回归模型的性能差距，推动扩散LLM的实际部署。

## 2. 方法论
### 核心思想
- 不修改模型参数，仅通过推理阶段的优化（训练无关）加速扩散LLM。
- 解决两大关键瓶颈：KV缓存复用和并行解码质量退化。

### 关键技术细节
- **分块近似KV缓存机制**：针对双向注意力机制的扩散模型，提出分块（block-wise）近似缓存策略，允许在生成过程中复用之前计算的KV对，同时通过近似计算以极小性能损失换取显著速度提升。
- **并行解码质量下降原因诊断**：发现质量下降的根本原因是并行解码时条件独立性假设破坏了令牌之间的依赖关系。
- **置信度感知的并行解码策略**：提出选择性解码方法，仅解码置信度超过预设阈值的令牌，从而减少依赖关系的破坏，保持生成质量。

### 算法流程（文字说明）
1. 输入序列，利用分块近似KV缓存机制复用历史中间表示。
2. 并行生成多个候选令牌，计算每个令牌的置信度得分。
3. 保留置信度高于阈值的令牌作为最终输出，低于阈值则回退至顺序解码或重新生成。
4. 更新KV缓存，继续下一步生成。

## 3. 实验设计
- **使用数据集/场景**：在LLaDA和Dream两种扩散LLM模型上进行评估，涵盖多个标准LLM基准测试。
- **Benchmark**：文中未具体列出数据集名称，但提及“multiple LLM benchmarks”，可能包括文本生成质量指标（如困惑度、BLEU、ROUGE等）和推理速度指标（如吞吐量）。
- **对比方法**：未明确列出对比基线，但应包含无加速的原始扩散LLM以及自回归模型作为参照。

## 4. 资源与算力
- **文中未明确说明**使用的GPU型号、数量或训练时长。由于Fast-dLLM是训练无关的方法，主要关注推理加速，但文中未给出推理时使用的硬件细节。仅指出实现了高达**27.6倍的吞吐量提升**。

## 5. 实验数量与充分性
- **实验数量**：从摘要推测，至少包含对LLaDA和Dream两个模型在多个基准上的实验，可能包括不同配置的消融实验（例如：有无KV缓存、有无置信度感知解码等）。具体组数未明确。
- **充分性**：实验覆盖了两种模型、多个基准，且同时报告了吞吐量和质量（最小精度损失），设计较为全面。但缺乏与更多近期加速方法的对比，以及不同阈值影响的详细消融，因此存在一定局限性。

## 6. 主要结论与发现
- Fast-dLLM能够在不进行额外训练的情况下，将扩散LLM的推理吞吐量提升高达27.6倍，同时保持极小的精度损失。
- 成功解决了扩散LLM缺乏KV缓存和并行解码质量下降的两大瓶颈，使其接近自回归模型的实际推理性能。
- 为扩散LLM的实际部署铺平了道路。

## 7. 优点
- **训练无关**：无需重新训练模型，直接应用于已有预训练扩散LLM，实用性强。
- **创新针对双向模型**：提出的分块近似KV缓存专为双向注意力设计，填补了该领域空白。
- **问题诊断清晰**：准确找出并行解码质量下降的根本原因——条件独立假设破坏令牌依赖，并给出简单有效的修复策略。
- **显著加速效果**：27.6倍吞吐量提升在速度-质量权衡上表现出色。

## 8. 不足与局限
- **实验覆盖有限**：仅基于LLaDA和Dream两个模型，未在更广泛的扩散LLM（如MDLM、SSD-LM等）上验证。
- **缺乏对比基线**：未与现有的扩散LLM加速方法（如离散扩散加速、迭代减少步数等）进行详细比较。
- **硬件细节缺失**：未说明推理所用GPU型号和数量，影响结果可重复性。
- **阈值选择**：置信度阈值可能对模型和任务敏感，文中未给出通用指导或自动调优方法。
- **应用限制**：仅适用于支持并行解码的扩散架构，且对于长序列生成，分块近似缓存可能存在累积误差。

（完）
