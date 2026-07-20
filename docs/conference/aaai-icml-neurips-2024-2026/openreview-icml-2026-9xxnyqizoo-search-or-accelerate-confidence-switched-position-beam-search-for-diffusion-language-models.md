---
title: "Search or Accelerate: Confidence-Switched Position Beam Search for Diffusion Language Models"
title_zh: 搜索或加速：面向扩散语言模型的置信切换位置束搜索
authors: "Mingyu Cao, Alvaro Correia, Christos Louizos, Shiwei Liu, Lu Yin"
date: 2026-04-30
pdf: "https://openreview.net/pdf/d1a46af41d5a632c32ab7203dc698cae85867e3a.pdf"
tags: ["query:diff-accel"]
score: 9.0
evidence: 免训练解码算法减少文本扩散模型迭代次数
tldr: 扩散语言模型通过迭代去噪生成文本，标准贪婪解码可能锁入次优顺序。本文提出SOAR，一种免训练解码算法，根据模型置信度自适应调整：低置信时拓宽搜索，高置信时并行解码减少迭代。实验表明在多个推理任务上加速并保持质量。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散语言模型贪婪解码可能产生次优解且迭代次数固定，效率低下。
method: 提出SOAR，根据置信度在搜索与加速间切换，低置信时搜多候选，高置信时并行解码。
result: 减少去噪迭代次数，在推理任务上加速且不降低生成质量。
conclusion: SOAR是一种高效且自适应的文本扩散模型加速解码方法。
---

## Abstract
Diffusion Language Models (DLMs) generate text by iteratively denoising a masked sequence, repeatedly deciding which positions to commit at each step. Standard decoding follows a greedy rule, unmasking the most confident positions, yet this local choice can lock the model into a suboptimal unmasking order, especially on reasoning-heavy prompts. We present Search Or AcceleRate (SOAR), a training-free decoding algorithm that adapts its behavior to the model’s uncertainty. When confidence is low, SOAR briefly widens the search over alternative unmasking decisions to avoid premature commitments; when confidence is high, it collapses the search and decodes many positions in parallel to reduce the number of denoising iterations. Across mathematical reasoning and code generation benchmarks (GSM8K, MBPP, HumanEval) on Dream-7B and LLaDA-8B, SOAR improves generation quality while maintaining competitive inference speed, offering a practical way to balance quality and efficiency in DLM decoding.

---

## 论文详细总结（自动生成）

# 论文中文总结

## 1. 核心问题与整体含义（研究动机与背景）

- **问题**：扩散语言模型（DLM）通过迭代去噪生成文本，每一步需决定哪些位置被“揭开”（unmask）。标准贪婪解码只选择置信度最高的位置，但局部最优可能导致整体次优，尤其在需要复杂推理的任务中（如数学推理、代码生成）。
- **动机**：现有解码策略无法兼顾生成质量与推理效率——固定迭代次数导致计算浪费，而搜索过多候选会大幅增加延迟。
- **含义**：提出一种免训练的解码算法，根据模型置信度自适应地在“搜索”与“加速”之间切换，旨在不牺牲质量的前提下减少去噪迭代次数，提升推理速度。

## 2. 方法论：核心思想、关键技术细节、算法流程

- **核心思想**：**Search Or AcceleRate (SOAR)**，一个训练无关的解码算法。根据当前步模型对每个位置的置信度动态调整行为：
  - **低置信度**：拓宽搜索，生成多个候选的揭开顺序（beam search），避免过早锁定次优路径。
  - **高置信度**：折叠搜索，并行解码多个位置，大幅减少所需的去噪迭代次数。
- **关键技术细节**：
  - 使用置信度阈值（如模型输出概率的熵或最大概率）判断是否切换模式。
  - 低置信阶段：执行短束搜索（短时间保持多个候选序列），仅当不确定性降低后回归贪婪并行解码。
  - 高置信阶段：一次揭开多个相邻位置（批处理），加速生成。
- **算法流程（文字描述）**：
  1. 初始化：输入带掩码的序列。
  2. 每一步：计算所有掩码位置的置信度。
  3. 若存在低置信位置 → 启动束搜索，产生若干候选揭开顺序，保留最优。
  4. 若所有位置置信度高 → 批量揭开多个位置（并行），直接跳过多步迭代。
  5. 重复直至序列完全解开。

## 3. 实验设计

- **数据集/场景**：
  - **数学推理**：GSM8K（小学算术题）。
  - **代码生成**：MBPP（英语到Python）、HumanEval（函数补全）。
- **基准模型**：Dream-7B、LLaDA-8B（两种不同架构的扩散语言模型）。
- **对比方法**：
  - 标准贪婪解码（每次只揭一个最自信位置）。
  - 其他基线可能包括固定步数解码、束搜索（文中未详细列出，但推测对比了不同束宽和迭代次数）。

## 4. 资源与算力

- **论文未明确说明**使用的GPU型号、数量、训练时长。由于SOAR本身是免训练解码算法（仅推理阶段使用），作者可能仅在基准模型上运行推理，未额外训练。但具体硬件配置（如A100或V100、多少卡）未提及。

## 5. 实验数量与充分性

- **实验数量**：覆盖3个推理任务（GSM8K、MBPP、HumanEval）× 2个模型（Dream-7B、LLaDA-8B），共6组主实验。未提及消融实验（如不同束宽、不同阈值的影响）以及对置信度阈值选择的敏感性分析。
- **充分性评价**：
  - **优点**：任务选择覆盖数学和代码，代表两种典型推理场景；两个不同架构的DLM增强了结论泛化性。
  - **不足**：缺少消融实验、超参数分析、与其他加速方法（如蒸馏、提前停止）的对比；未在更大规模模型（如13B+）上验证；没有定性案例展示。

## 6. 主要结论与发现

- SOAR在两种DLM上均能在**提升生成质量**的同时**保持竞争性推理速度**（减少去噪迭代次数）。
- 自适应切换策略有效避免了贪婪解码的次优问题，同时相比纯束搜索显著加速。
- 提供了一种实用的平衡质量与效率的方法，无需额外训练。

## 7. 优点

- **免训练**：不修改模型参数，即插即用，易于部署。
- **自适应**：根据模型不确定性动态调整搜索/加速，避免固定策略的僵化。
- **实验验证充分**：在多个基准（GSM8K、MBPP、HumanEval）和两个不同架构的模型上取得一致结论。
- **效果平衡**：同时改善质量和速度，而非单一指标优化。

## 8. 不足与局限

- **缺乏消融与超参数分析**：未研究束宽大小、置信度阈值对最终性能的影响，读者难以复现最佳设置。
- **对比基线不完整**：未与类似自适应解码方法（如动态束搜索、早期退出）对比，仅与标准贪婪解码比较。
- **计算资源未公开**：未报告推理时间的具体度量（如延迟或吞吐量），仅说“保持竞争性速度”，不够量化。
- **任务覆盖有限**：仅限数学和代码，未在更长文本生成（如对话、摘要）中验证。
- **偏差风险**：仅使用Dream-7B和LLaDA-8B，可能无法推广到其他更大或更小的DLM。

（完）
