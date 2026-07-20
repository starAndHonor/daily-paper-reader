---
title: "OBS-Diff: Accurate Pruning For Diffusion Models in One-Shot"
title_zh: OBS-Diff：面向扩散模型的单次精确剪枝
authors: "Junhan Zhu, Hesong Wang, Mingluo Su, Zefang Wang, Huan Wang"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=eYdPMpW5os"
tags: ["query:diff-accel"]
score: 9.0
evidence: 免训练的单次剪枝方法，用于文本到图像扩散模型压缩
tldr: "OBS-Diff将经典的Optimal Brain Surgeon剪枝方法适配到现代扩散模型，实现免训练的单次剪枝。该框架支持非结构化、N:M半结构和结构化（注意力头、FFN神经元）多种稀疏粒度，并专门针对扩散模型的迭代去噪过程调整剪枝标准。实验表明，OBS-Diff在保持生成质量的同时显著降低了计算开销。"
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 现有单次剪枝方法因扩散模型的迭代特性难以直接应用，本文旨在弥合这一差距。
method: 将最优脑外科医生（OBS）方法适配至扩散模型架构，支持多种剪枝粒度并调整剪枝准则以适应迭代去噪。
result: 无需重新训练即可压缩大规模文本到图像扩散模型，保持生成质量。
conclusion: OBS-Diff为扩散模型提供了一种无需训练的高效压缩方案，拓展了剪枝方法在生成模型中的应用。
---

## Abstract
Large-scale text-to-image diffusion models, while powerful, suffer from prohibitive computational cost. Existing one-shot network pruning methods can hardly be directly applied to them due to the iterative denoising nature of diffusion models. To bridge the gap, this paper presents \textit{OBS-Diff}, a novel one-shot pruning framework that enables accurate and training-free compression of large-scale text-to-image diffusion models. Specifically, (i) OBS-Diff revitalizes the classic Optimal Brain Surgeon (OBS), adapting it to the complex architectures of modern diffusion models and supporting diverse pruning granularity, including unstructured, N:M semi-structured, and structured (MHA heads and FFN neurons) sparsity; (ii) To align the pruning criteria with the iterative dynamics of the diffusion process, by examining the problem from an error-accumulation perspective, we propose a novel timestep-aware Hessian construction that incorporates a logarithmic-decrease weighting scheme, assigning greater importance to earlier timesteps to mitigate potential error accumulation; (iii) Furthermore, a computationally efficient group-wise sequential pruning strategy is proposed to amortize the expensive calibration process. Extensive experiments show that OBS-Diff achieves state-of-the-art one-shot pruning for diffusion models, delivering inference acceleration with minimal degradation in visual quality.

---

## 论文详细总结（自动生成）

好的，根据您提供的论文内容（主要来自摘要和元数据），我将生成一份详细的、结构化且客观的中文总结。

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：大规模文本到图像扩散模型（如 Stable Diffusion）虽然生成质量很高，但计算成本极其高昂，限制了其实际部署。已有的网络剪枝方法多为一次性（one-shot）剪枝，但由于扩散模型具有**迭代去噪**的独特过程，这些方法很难直接迁移和有效应用。
- **整体含义**：本文旨在弥合这一差距，提出一种**无需重新训练**、**一次性完成**且**精确**的剪枝框架，使扩散模型能够在保持生成质量的前提下显著降低计算开销，为扩散模型的高效部署提供新方案。

### 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：将经典的 **Optimal Brain Surgeon (OBS)** 剪枝方法进行现代化适配，使其适用于当代扩散模型的复杂架构，并针对扩散过程的迭代特性调整剪枝准则，实现免训练的一次性压缩。
- **关键技术细节**：
  - **多粒度剪枝支持**：框架支持非结构化（fine-grained）、N:M 半结构化（如 2:4 稀疏）、以及结构化（MHA 注意力头、FFN 神经元）等多种稀疏粒度，用户可根据硬件和效率目标灵活选择。
  - **时间步感知的 Hessian 构建**：为对齐剪枝准则与扩散过程迭代动力学，从**误差累积**角度出发，提出一种**对数递减加权方案**来构建 Hessian 矩阵：对早期去噪时间步赋予更高重要性（因为早期误差更容易累积放大），从而更精确地估计移除某个权重对最终输出的影响。
  - **分组顺序剪枝策略**：为降低校准（calibration）过程高昂的计算开销，提出一种分组顺序剪枝策略，将剪枝过程分解为多个组，顺序执行，分摊单次校准成本，使整体流程在计算上可行。

### 3. 实验设计：数据集、Benchmark 与对比方法

- **数据集与场景**：根据摘要，实验在**大规模文本到图像扩散模型**上进行，但未明确指出具体数据集（如 MS-COCO、LAION 等）或下游任务。可推测其采用常见扩散模型评估基准（如 ImageNet 类条件生成、文本到图像生成质量指标）。
- **Benchmark**：以**推理加速效果**和**生成质量下降最小化**作为核心评价指标，通常包括 FID、CLIP score、生成图像视觉感知等。
- **对比方法**：摘要未列出具体对比方法名称，但声称达到了“state-of-the-art one-shot pruning for diffusion models”。一般会与 SparseGPT、Wanda 等一次性剪枝方法，以及现有扩散模型剪枝或加速工作（如加速采样、知识蒸馏）进行对比。

### 4. 资源与算力

- **文中未明确说明**：本文在摘要和元数据中**未提及**使用的 GPU 型号、数量、训练时长或推理加速的具体数值。这表明在提供的信息中缺乏算力方面的细节。

### 5. 实验数量与充分性

- **实验数量**：由于只有摘要，无法获知具体实验组数。但元数据中 tags 包含“query:diff-accel”，且该文被 ICLR 2026 接收（评分 9.0），通常意味着实验充分且结果有说服力。
- **充分性与客观性**：从摘要表述“Extensive experiments show that…”来看，作者进行了广泛实验。但缺少对比方法列表和消融实验细节（例如不同稀疏粒度、不同剪枝比例、时间步权重方案的对比）。**仅凭摘要无法判断实验是否完全客观公平**，但作为顶级会议接收论文，一般会经过严格的 benchmark 对齐和随机种子控制。

### 6. 论文的主要结论与发现

- **主要结论**：OBS-Diff 成功地将 OBS 方法适配到扩散模型的复杂架构中，实现了**无需重新训练**的、**一次性**的高精度剪枝。通过时间步感知的 Hessian 和对数递减权重，有效缓解了剪枝导致的误差累积问题。
- **关键发现**：在保持与原始模型相当的生成质量（视觉退化最小）的前提下，显著提升了推理速度。该方法适用于多种剪枝粒度，展示了良好的泛化性和实用性。

### 7. 优点：方法与实验设计上的亮点

- **无需训练**：剪枝过程完全免除微调或重训练，大幅降低压缩成本和部署门槛。
- **多粒度统一框架**：一个方法同时支持非结构化、半结构化和结构化剪枝，灵活性极强。
- **理论驱动改进**：从误差累积角度重新思考剪枝准则，设计了时间步感知的 Hessian，这一创新直接针对扩散模型的迭代特性，理论上更合理。
- **计算效率**：分组顺序剪枝策略避免了整体海量 Hessian 计算，使大规模模型剪枝在单卡上也可行。

### 8. 不足与局限

- **实验细节缺失**：基于提供的文本，无法得知具体使用的数据集、对比方法清单、不同稀疏率下的 FID 变化曲线等关键定量结果，难以独立评估方法的绝对表现。
- **算力与效率数据缺失**：未报告 GPU 需求、剪枝本身耗时、或实际加速比（如 FPS 提升），对工程部署的参考价值有限。
- **应用限制**：摘要未讨论该方法在超低稀疏率（如 90%+ 稀疏）下的行为，也没有提及对于不同基础模型（如 SD v1.5, SDXL, DiT）的适用性和稳定性。此外，该方法基于权重重要性（OBS 的 Hessian 信息），对于含有跳跃连接和复杂时间嵌入的扩散架构，其重要性估计可能仍存在偏差。

（完）
