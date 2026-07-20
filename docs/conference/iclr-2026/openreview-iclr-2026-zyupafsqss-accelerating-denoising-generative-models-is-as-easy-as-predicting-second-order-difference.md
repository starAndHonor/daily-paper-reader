---
title: Accelerating Denoising Generative Models is as Easy as Predicting Second-Order Difference
title_zh: 加速去噪生成模型如同预测二阶差分一样简单
authors: "Yixiao Wang, Ting Jiang, Zishan Shao, Hancheng Ye, Jingwei Sun, Mingyuan Ma, Qinsi Wang, Jianyi Zhang, Yiran Chen, Hai Helen Li"
date: 2025-09-19
pdf: "https://openreview.net/pdf?id=ZYuPaFsqsS"
tags: ["query:diff-accel"]
score: 9.0
evidence: 通过二阶差分预测实现免训练加速
tldr: 本文揭示了在有限预算下加速去噪生成模型的最简原则：利用二阶差分预测。理论上证明了该预测器的精确性，无需任何训练即可实现超过两倍的采样加速。实验在多个扩散和流模型上验证了其有效性，兼顾速度与质量。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 扩散模型推理延迟高，现有加速方法复杂或需训练。
method: 利用每个三步窗口中仅有一个去噪器调用的结构，提出二阶差分预测器。
result: 在多个模型上实现超过两倍加速，不损失生成质量。
conclusion: 提供了一种理论最优且无需训练的通用加速方法。
---

## Abstract
High-fidelity diffusion and flow models remain latency-bound at inference, motivating acceleration that leaves pretrained weights untouched. We ask: what is the $\\textit{minimal yet principled}$ way to accelerate sampling? Under a simple and mild budget, when uniform reduction targets more than $2\\times$ speedup, each three-step window contains at most one fresh denoiser call, creating a structural scarcity of real signals.
From this constraint, we isolate the $\\textit{observed}$ information at step $t$—the fresh output $\\psi_t$ and its backward difference $\\Delta \\psi_{t}^{(1)}=\\psi_t-\\psi_{t+1}$—and show it induces a uniquely minimal, affine-exact second-order predictor $\\hat\\psi_{t-1}=2 \\psi_t- \\psi_{t+1}$.
We prove that, under this scarcity, the two-point second-order rule is the information-consistent optimum: it is BLUE among linear two-point estimators.
Naively chaining this predictor across consecutive steps destabilizes sampling by compounding approximation errors.
We resolve this by $\textit{reusing the observed tuple}$ in an interleaved zig–zag schedule that prevents back-to-back extrapolations and controls variance. 
The resulting method, $\textbf{ZEUS}$, is a zero-overhead, backbone- and parameterization-agnostic plug-in requiring no retraining, no feature caches, and no architectural changes.
Across images and video, ZEUS consistently moves the speed–fidelity Pareto frontier outward versus recent state-of-the-art, delivering up to $3.2\\times$ end-to-end speedup while improving perceptual similarity.

---

## 论文详细总结（自动生成）

# 加速去噪生成模型如同预测二阶差分一样简单：中文总结

## 1. 核心问题与整体含义（研究动机和背景）
- **问题**：扩散模型和流模型在推理时面临高延迟瓶颈，现有加速方法要么复杂（如蒸馏、级联），要么需要重新训练或改动模型架构。
- **动机**：在保持预训练权重不变的前提下，寻找一种**最简且原则性**的加速采样方法。
- **背景**：当均匀减少采样步数以实现超过2倍加速时，每个三步窗口中最多只能进行一次去噪器调用，导致真实信号的结构性稀缺。作者利用这一约束，提出仅通过**二阶差分预测**即可实现加速。

## 2. 方法论：核心思想、关键技术细节
- **核心思想**：在有限预算（三步窗口内仅一次真实去噪）下，利用当前步的观测信息（去噪器输出 \(\psi_t\) 及其向后一阶差分 \(\Delta \psi_t^{(1)} = \psi_t - \psi_{t+1}\)）构造一个**二阶差分预测器**，预测下一步的 \(\hat{\psi}_{t-1}\)。
- **关键技术细节**：
  - 推导出**信息一致的最优二阶预测规则**：\(\hat{\psi}_{t-1} = 2\psi_t - \psi_{t+1}\)。该规则是基于两个点的线性估计下的最优无偏估计（BLUE）。
  - 直接链式应用该预测器会导致误差累积和采样不稳定。作者提出**交错锯齿（interleaved zig–zag）调度**：重复使用已观测的三元组，避免连续的预测，从而控制方差。
  - 最终方法命名为**ZEUS**：**零额外开销**（zero-overhead），与主干网络、参数化形式无关，无需重新训练、无需特征缓存、无需架构改变。
- **算法流程**（文字说明）：
  1. 设定一个均匀减少的采样步数，使得每个三步窗口最多只有一个真实的去噪器调用。
  2. 在每个窗口内，只调用一次去噪器，得到 \(\psi_t\) 和 \(\psi_{t+1}\)（通过前一步或初始步获得）。
  3. 使用二阶差分公式预测 \(\hat{\psi}_{t-1}\)。
  4. 按照交错锯齿调度，避免连续预测，确保采样路径稳定。
  5. 重复直至生成完整样本。

## 3. 实验设计
- **数据集/场景**：图像和视频生成任务（论文未列出具体数据集名称，但提到across images and video）。
- **Benchmark**：与近期**最先进方法（SOTA）** 对比，比较速度与保真度之间的帕累托前沿。
- **对比方法**：未具体列出，但声称ZEUS在保持或提升感知相似度的同时，实现了高达**3.2倍端到端加速**。

## 4. 资源与算力
- 论文中**未明确说明**使用的GPU型号、数量或训练时长。由于方法是“无需训练”的插件，可能仅需推理测试，但具体实验算力未提及。

## 5. 实验数量与充分性
- **实验数量**：摘要中提到在图像和视频上进行了实验，但未给出具体实验组数（如不同数据集、不同模型、不同加速倍率下的消融实验等）。
- **充分性评价**：从摘要看，实验覆盖了图像和视频两类任务，并与SOTA对比，显示了加速和质量的提升。但缺乏详细表格和消融研究。由于论文被ICLR-2026拒稿，可能实验部分不够充分或存在不足。需要更多公开细节才能判断公平性。

## 6. 主要结论与发现
- **发现**：在有限预算下，二阶差分预测是信息一致的最优线性估计；通过交错锯齿调度可避免误差积累。
- **结论**：ZEUS是一种零开销、与模型无关的插件，能够在不牺牲生成质量的前提下，实现超过2倍（最高3.2倍）的端到端加速，并在速度-保真度帕累托前沿上超越近期SOTA。

## 7. 优点
- **方法简洁性**：只用到简单二阶差分公式，无需训练、无需额外缓存或架构修改。
- **通用性**：主干和参数化无关，适用于扩散模型和流模型。
- **理论保证**：证明了在信号稀缺条件下，该预测器是BLUE（最佳线性无偏估计）。
- **实际加速显著**：最高3.2倍，而质量不降反升（感知相似度提升）。

## 8. 不足与局限
- **实验细节缺失**：论文简短，未展示具体数据集、对比方法、定量指标（如FID、IS等），也未进行消融研究，难以全面评估。
- **理论假设**：依赖于“三步窗口最多一次去噪器调用”的稀疏性设定，对加速比小于2倍的情况可能不适用或效果变差。
- **应用限制**：仅适用于均匀减少步数的场景，对于非均匀调度或自适应步数的方法可能不兼容。
- **论文被拒稿**：虽然评分9.0，但被ICLR-2026拒绝，可能说明审稿人认为实验不充分或存在其他未被揭示的缺陷。
- **可复现性**：未提供代码或详细实验配置，复现难度较大。

（完）
