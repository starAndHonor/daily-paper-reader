---
title: "SADA: Stability-guided Adaptive Diffusion Acceleration"
title_zh: SADA：稳定性引导的自适应扩散加速
authors: "Ting Jiang, Yixiao Wang, Hancheng Ye, Zishan Shao, Jingwei Sun, Jingyang Zhang, Zekai Chen, Jianyi Zhang, Yiran Chen, Hai Li"
date: 2025-05-01
pdf: "https://openreview.net/pdf?id=ThMQfsBnje"
tags: ["query:diff-accel"]
score: 8.0
evidence: 稳定性引导的自适应扩散加速
tldr: 提出SADA，一种训练无关的扩散加速范式，通过单一稳定性准则统一决策步长和token剪枝，解决了现有逐步计算减少方法忠实度低的问题，在加速的同时保持了生成保真度。
source: ICML-2025-Accepted
selection_source: conference_retrieval
motivation: 现有训练无关加速方法在减少每步计算时不考虑ODE公式，导致保真度下降。
method: 利用稳定性准则联合优化步长选择和token稀疏化。
result: SADA在图像生成任务上实现了与基线相当的保真度，同时大幅减少了计算量。
conclusion: SADA为训练无关扩散加速提供了稳定性和保真度的理论保障。
---

## Abstract
Diffusion models have achieved remarkable success in generative tasks but suffer from high computational costs due to their iterative sampling process and quadratic‐attention costs. 
Existing training-free acceleration strategies that reduce per-step computation cost, while effectively reducing sampling time, demonstrate low faithfulness compared to the original baseline. 
We hypothesize that this fidelity gap arises because (a) different prompts correspond to varying denoising trajectory, and (b) such methods do not consider the underlying ODE formulation and its numerical solution. 
In this paper, we propose **Stability-guided Adaptive Diffusion Acceleration (SADA)**, a novel paradigm that unifies step-wise and token-wise sparsity decisions via a single stability criterion to accelerate sampling of ODE-based generative models (Diffusion and Flow-matching). 
For (a), SADA adaptively allocates sparsity based on the sampling trajectory. 
For (b), SADA introduces principled approximation schemes that leverage the precise gradient information from the numerical ODE solver.
Comprehensive evaluations on SD‐2, SDXL, and Flux using both EDM and DPM++ solvers reveal consistent $\ge 1.8\times$ speedups with minimal fidelity degradation (LPIPS $\leq 0.10$ and FID $\leq 4.5$) compared to unmodified baselines, significantly outperforming prior methods.
Moreover, SADA adapts seamlessly to other pipelines and modalities: It accelerates ControlNet without any modifications and speeds up MusicLDM by \(1.8\times\) with \(\sim 0.01\) spectrogram LPIPS.
Our code is available at: [https://github.com/Ting-Justin-Jiang/sada-icml](https://github.com/Ting-Justin-Jiang/sada-icml).

---

## 论文详细总结（自动生成）

# 论文详细总结：SADA：稳定性引导的自适应扩散加速

## 1. 核心问题与整体含义

**研究动机与背景**  
扩散模型在生成任务中取得了显著成功，但其迭代采样过程和二次注意力机制导致计算成本高昂。现有的**训练无关加速策略**（如减少每步计算量）虽然能有效缩短采样时间，但与原始模型相比**保真度明显下降**。作者认为这一保真度差距来源于两个原因：(a) 不同提示（prompt）对应不同的去噪轨迹，而现有方法无法自适应调整；(b) 这些方法未考虑底层的ODE公式及其数值解法。为此，论文提出**SADA**（稳定性引导的自适应扩散加速），通过一个统一的稳定性准则来联合决定步长和token稀疏度，从而在保持生成保真度的同时显著加速采样。

## 2. 提出的方法论

**核心思想**  
SADA是一种**训练无关的扩散加速范式**，利用**单一稳定性准则**将步长选择和token剪枝决策统一在一个框架下，针对ODE-based生成模型（扩散模型和流匹配）进行加速。

**关键技术细节**  
- **自适应稀疏分配**：根据采样轨迹动态调整每步的稀疏度，解决不同prompt对应不同去噪轨迹的问题。  
- **原理性近似方案**：利用数值ODE求解器提供的精确梯度信息来指导稀疏化，从而保证忠实度。  
- **联合决策**：稳定性准则同时决定（a）何时跳过某些步（步长选择）和（b）哪些token可以被剪枝（token-wise稀疏性），两者共享同一准则。  
- **不依赖训练**：无需额外训练或微调，直接应用于预训练模型。  
- **支持多种求解器**：适用于EDM和DPM++等主流ODE求解器。

**公式与算法流程**（文字说明）  
算法输入为预训练扩散模型、ODE求解器、输入条件。每一采样步中：  
1. 计算当前状态的稳定性度量（如基于ODE梯度变化或注意力映射的差异）。  
2. 若稳定性超过阈值，则跳过该步（步长自适应）；否则保留该步并进一步评估token级别稳定性，对低稳定性token进行剪枝（即省略其注意力计算）。  
3. 生成器根据简化后的计算图执行前向传播，利用求解器梯度信息保持数值精度。  
具体实现细节参见论文和开源代码。

## 3. 实验设计

**数据集/场景**  
- **图像生成**：在SD-2、SDXL、Flux三个主流扩散模型上评估。  
- **求解器**：使用EDM和DPM++两种ODE求解器。  
- **拓展应用**：ControlNet（无需修改直接加速）、MusicLDM（音频生成，加速1.8×且频谱LPIPS ≈ 0.01）。

**基准与方法对比**  
- **基准**：未修改的完整采样流程（原始模型）。  
- **对比方法**：之前提出的训练无关加速方法（如步长剪枝或token剪枝的基线），摘要中未列出具体名称，但声称SADA显著优于它们。  
- **评价指标**：LPIPS（≤0.10）、FID（≤4.5），以及加速倍数（≥1.8×）。

## 4. 资源与算力

论文摘要及元数据中**未明确说明**使用的GPU型号、数量、训练时长或推理所需算力。仅提到代码已开源（GitHub），但未提供具体的硬件配置。推测此项工作主要关注推理加速，自身不涉及训练，因此算力消耗体现在推理阶段的节省。

## 5. 实验数量与充分性

**实验数量**  
- 主要实验覆盖了3个图像生成模型（SD-2、SDXL、Flux）× 2种求解器（EDM、DPM++），共6组主实验。  
- 额外展示了在ControlNet和MusicLDM上的泛化实验。  
- 推测包含消融实验（例如不同稳定性阈值、不同稀疏度设置等），但摘要中未列举具体数字。

**充分性与客观性**  
- 覆盖了多个主流模型和多模态任务（图像、音频），较为全面。  
- 使用了两个常见求解器，提高了结论的泛化性。  
- 评价指标（LPIPS、FID）是图像生成领域的标准保真度指标。  
- 但缺少与其他训练无关方法的详细对比（仅说“显著优于”），如果摘要外的完整论文有详细对比，则更充分。整体而言，实验设计合理，公平性较好。

## 6. 主要结论与发现

- SADA能在**保持极低保真度**（LPIPS ≤ 0.10，FID ≤ 4.5）的前提下实现**≥1.8倍的加速**。  
- 该方法**统一了步长和token稀疏性决策**，并通过稳定性准则提供了理论保障。  
- 适用于多种ODE-based生成模型（扩散模型和流匹配）及不同求解器。  
- 能**无缝扩展到ControlNet等管道**和**MusicLDM等音频生成任务**，无需任何修改或重训练。  
- 相比现有训练无关加速方法，SADA在忠实度上具有显著优势。

## 7. 优点

- **训练无关**：无需额外训练或微调，即插即用。  
- **自适应**：根据采样轨迹动态调整，对不同输入自适应分配计算资源。  
- **理论保障**：基于稳定性准则的设计考虑了ODE数值解法的精度，保真度有据可依。  
- **统一框架**：将步长选择与token剪枝统一在一个准则下，简化了加速策略。  
- **通用性强**：在多个主流模型、求解器及多模态任务上验证有效，扩展性好。  
- **代码开源**：便于复现和进一步研究。

## 8. 不足与局限

- **算力细节缺失**：未提供完整的推理加速所需的硬件配置和具体加速比（仅说≥1.8×），不利于与其他方法精确对比效率。  
- **实验对比信息有限**：摘要中未列出对比的具体基线方法，缺乏保真度-加速比曲线等详细分析，可能影响读者判断改进程度。  
- **极端情况未讨论**：例如在高加速比（>2×）下保真度是否仍能保持、对非常复杂的prompt是否稳定。  
- **应用限制**：仅适用于基于ODE的生成模型，对于离散扩散或无ODE公式的变体可能不适用；稳定性准则的设计可能依赖于特定求解器特性。  
- **潜在偏差风险**：实验仅覆盖图像和音频两种模态，未在视频、3D等更大规模任务上验证；评估指标单一（LPIPS、FID），未包括主观评测。  
- **消融实验细节未知**：稳定性准则中阈值选取的策略、不同组件贡献等分析在摘要中没有体现，可能影响方法复现的严谨性。

（完）
