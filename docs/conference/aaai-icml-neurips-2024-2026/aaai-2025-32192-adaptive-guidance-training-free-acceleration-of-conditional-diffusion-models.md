---
title: "Adaptive Guidance: Training-free Acceleration of Conditional Diffusion Models"
title_zh: 自适应引导：条件扩散模型的免训练加速
authors: "Angela Castillo, Jonas Kohler, Juan C. Pérez, Juan Pablo Pérez, Albert Pumarola, Bernard Ghanem, Pablo Arbeláez, Ali Thabet"
date: 2025-04-11
pdf: "https://ojs.aaai.org/index.php/AAAI/article/download/32192/34347"
tags: ["query:diff-accel"]
score: 9.0
evidence: 条件扩散模型的免训练加速
tldr: 本文针对文本条件扩散模型中分类器无关引导（CFG）效率低的问题，提出自适应引导（AG），通过神经架构搜索发现CFG在去噪后期冗余，因此可以跳过部分CFG评估，实现免训练加速。实验表明在不牺牲质量的前提下大幅减少计算量，直接满足训练自由扩散模型推断加速的需求。
source: AAAI-2025-Accepted
selection_source: conference_retrieval
figures_json: "[{\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-32192/fig-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 1344, \"height\": 571, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-32192/fig-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 1108, \"height\": 618, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-32192/fig-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 609, \"height\": 439, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-32192/fig-004.webp\", \"caption\": \"\", \"page\": 0, \"index\": 4, \"width\": 1789, \"height\": 196, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-32192/fig-005.webp\", \"caption\": \"\", \"page\": 0, \"index\": 5, \"width\": 862, \"height\": 424, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-32192/fig-006.webp\", \"caption\": \"\", \"page\": 0, \"index\": 6, \"width\": 574, \"height\": 409, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-32192/fig-007.webp\", \"caption\": \"\", \"page\": 0, \"index\": 7, \"width\": 861, \"height\": 310, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-32192/fig-008.webp\", \"caption\": \"\", \"page\": 0, \"index\": 8, \"width\": 753, \"height\": 351, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-32192/fig-009.webp\", \"caption\": \"\", \"page\": 0, \"index\": 9, \"width\": 755, \"height\": 350, \"label\": \"Figure\"}]"
tables_json: "[{\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-32192/table-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 876, \"height\": 218, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-32192/table-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 873, \"height\": 147, \"label\": \"Table\"}]"
motivation: CFG在所有扩散步骤中应用导致冗余计算，尤其是后半段。
method: 利用可微神经架构搜索发现高效引导策略，仅在必要步骤应用CFG。
result: 在文本到图像生成中显著减少计算量，保持生成质量。
conclusion: AG实现了条件扩散模型的高效免训练加速。
---

## Abstract
This paper presents a comprehensive study on the role of Classifier-Free Guidance (CFG) in text-conditioned diffusion models from the perspective of inference efficiency. In particular, we relax the default choice of applying CFG in all diffusion steps and instead propose to search for more efficient guidance policies. We formulate the discovery of such policies in the framework of differentiable neural architecture search. Our findings suggest that, as denoising progresses, the updates produced by CFG become increasingly aligned with simple conditional steps, which renders CFG's additional neural network evaluation redundant, especially in the second half of the denoising process. Building upon this insight, we propose "Adaptive Guidance" (AG), an efficient variant of CFG that adaptively omits network evaluations when the denoising process displays convergence. Our experiments demonstrate that AG preserves CFG's image quality while reducing computation by 25%. Thus, AG constitutes a plug-and-play alternative to Guidance Distillation, achieving 50% of the speed-ups of the latter, while being training-free and retaining the capacity to handle negative prompts. We conclude by uncovering further redundancies of CFG in the first half of the diffusion process, showing that entire neural network evaluations can be replaced by simple affine transformations of past score estimates.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **核心问题**：条件扩散模型（如文本到图像生成）中，Classifier-Free Guidance (CFG) 虽然在每个去噪步骤中同时评估条件分数和无条件分数，显著提升生成质量，但导致计算量翻倍（每个步骤需两次网络评估，即NFE翻倍）。这种默认在所有步骤应用CFG的做法存在冗余，尤其是在去噪后半段，条件与无条件分数趋于一致，CFG的额外开销变得不必要。

- **研究动机**：在保持生成质量的同时，减少CFG带来的计算负担，实现推理加速。现有方法如Guidance Distillation (GD) 需要重新训练且不支持负向提示和图像编辑，而本文旨在提出一种无需训练、即插即用的加速方案。

- **整体含义**：本文揭示了CFG在去噪过程中的冗余规律，并基于此提出自适应引导（Adaptive Guidance, AG），以及更激进的Linear AG变体，实现了条件扩散模型的高效免训练加速，为实际部署提供了实用方案。

## 2. 方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：通过可微神经架构搜索（Differentiable NAS）发现，CFG在去噪早期至关重要，但在后半段与条件分数的更新高度一致（余弦相似度趋近1），因此可以自适应地停止CFG评估，仅使用条件分数。此外，无条件分数的估计可以通过历史分数迭代的仿射变换近似，进一步减少计算。

- **关键技术细节**：
  - **搜索高效引导策略**：将去噪过程建模为一系列步骤选择，每个步骤可选择无条件分数、条件分数或带不同强度的CFG分数（共k+2个选项）。通过连续松弛（softmax加权）和可微优化，搜索最优策略α*，目标是最小化生成图像与基线CFG图像的差异（如SSIM）并加入NFE的稀疏正则化。
  - **Adaptive Guidance (AG)**：基于搜索发现，定义一个阈值γ̄（余弦相似度阈值）。在去噪过程中，实时计算当前步条件与无条件分数的余弦相似度γ_t，当γ_t ≥ γ̄时，停止CFG评估，仅使用条件分数。公式：ϵ_AG(xt, c) = ϵ_cfg(xt, c, s) if γ_t < γ̄, else ϵ_θ(xt, c)。这样实现了自适应截断，减少了约25%的NFE。
  - **Linear AG**：为了进一步减少前半段计算，利用无条件分数在去噪路径上的高度规律性，学习一个线性回归模型，基于过去若干步的条件分数和无条件分数估计当前步的无条件分数ϵ̂(xt, ∅)。在采样时，交替使用真实CFG（2 NFEs）和基于估计的CFG（1 NFE），可节省至多75%的GD计算量。该线性模型通过200张图像的轨迹快速拟合得到（20分钟内）。

- **算法流程（文字说明）**：
  1. 预处理：训练一个线性回归模型，参数基于少量采样轨迹。
  2. 采样初始化：x_T ~ N(0, I)。
  3. 对每个去噪步骤t（从T到1）：
     a. 计算条件分数ϵ(xt, c)和无条件分数ϵ(xt, ∅)（如果AG决定使用CFG）。
     b. 计算余弦相似度γ_t，若γ_t ≥ γ̄，则下一步使用条件分数；否则使用CFG分数。
     c. （若使用Linear AG）在偶数步骤中，无条件分数由线性回归估计得到，仅需一次网络评估。
  4. 返回x_0。

## 3. 实验设计：数据集、基准与对比方法

- **数据集**：
  - 训练搜索策略：使用CC3M（Conceptual Captions）数据集中的10k噪声-图像对。
  - 评估：使用OUI数据集（Dai et al. 2023）的1k真实用户提示，包含多样化、实用的文本描述。
  - 额外：针对FM-256模型，使用内部256×256贴纸风格图像数据集。

- **基准模型**：
  - 主要模型：Stable Diffusion (LDM-512)，在内部数据集上从零训练，900M参数，512×512分辨率。
  - 迁移验证：EMU-768（2.7B参数，768×768）、SDXL-1024（2.5B参数，1024×1024）、FM-256（基于流匹配的模型）。
  - 图像编辑任务：基于InstructPix2Pix的EMU Edit模型。

- **对比方法**：
  - 基线：标准CFG（每步2 NFEs，共40 NFEs，T=20）。
  - 朴素加速：直接减少总去噪步数（如从20步减至16或15步，保持每步CFG）。
  - 消融：AG在不同阈值γ̄下的表现，Linear AG。
  - 人类评价：AG (30 NFEs) vs CFG (40 NFEs)，由5名训练标注员对1k张图像进行投票。

## 4. 资源与算力

- **搜索阶段**：使用Quadro RTX 8000 GPU，搜索耗时约1.5天。
- **训练LDM-512**：未明确说明GPU数量与具体训练时长，仅提及从零训练，使用10k噪声-图像对。
- **对比模型**：GD在EMU-768上需要约10k迭代（batch size >32），在A100上约4天。
- **推理测试**：多型号GPU（A100、H100、V100、RTX 8000、GTX 1080Ti）进行延迟测量，均使用半精度和PyTorch 2。
- **Linear AG**：生成200张图像的轨迹用于学习线性回归系数，总耗时<20分钟。
- **注意**：论文未说明搜索阶段和主要实验的完整GPU资源消耗细节（如搜索使用的GPU数量、LDM训练的详细资源）。

## 5. 实验数量与充分性

- **主要实验组**：
  1. **搜索策略有效性**：在LDM-512上比较搜索得到的策略与AG、朴素CFG缩减步骤的性能（图6），覆盖NFE从22到40的范围。
  2. **AG阈值分析**：多个γ̄值（0.991, 0.994, 0.996等）下的SSIM与NFE trade-off（图6、表2）。
  3. **跨模型验证**：在EMU-768、SDXL-1024、FM-256上验证余弦相似度趋势（图5），以及在EMU-768上的人机评估（表2）。
  4. **图像编辑任务**：在EMU Edit上展示AG应用（图8），定性比较。
  5. **Linear AG**：与AG和朴素CFG进行定性比较（图9），给出25 NFEs下的视觉质量。
  6. **消融**：比较AG与CFG在相同NFE下的SSIM，以及替代策略（如仅前5步CFG+后15步条件）的退化（图9a）。
  7. **效率测试**：多个GPU上的运行时表（表1）。

- **充分性评价**：
  - 实验覆盖了不同规模模型（从800M到9.5B参数）、不同分辨率、不同任务（文本到图像、图像编辑）。
  - 使用1k真实用户提示进行评估，具有代表性。
  - 人类评价使用5名标注员，并且进行了统计检验（Wilcoxon检验，p=0.603），表明AG与CFG无显著差异。
  - **客观性与公平性**：搜索策略使用可微优化，避免了手动调参偏差；AG的阈值选择基于搜索发现的规律，而非人工猜测。对比方法（朴素CFG缩减步数、GD）是直接竞争方法，比较公平。但未与GD进行定量指标（FID/CLIP score）对比，仅提及速度提升比例和功能差异；且人类评价仅针对30 NFEs的AG vs 40 NFEs的CFG，缺少更全面的NFE/质量曲线。

## 6. 论文的主要结论与发现

- **主要发现**：条件与无条件分数在去噪过程中余弦相似度逐渐上升，趋于1，表明CFG在后期冗余。可微搜索证实CFG在早期重要。
- **方法结论**：
  - AG通过自适应阈值γ̄跳过后半段CFG评估，保留质量的同时减少25% NFEs。
  - AG实现50%的GD速度提升（25% vs 50%），且无需训练、支持负向提示和图像编辑。
  - Linear AG通过线性回归替代前半段无条件分数评估，可节省高达75% GD计算量（25 NFEs vs 20 NFEs of GD），但可能引入轻微质量损失。
- **性能总结**：
  - 在EMU-768上，AG（~30 NFEs）与CFG（40 NFEs）的人类偏好无显著差异（498 vs 502）。
  - 运行时分析显示，AG带来与NFE减少接近1:1的延迟改善（表1）。

## 7. 优点

- **方法创新**：
  - 将NAS思想引入扩散模型引导策略搜索，为理解CFG动态提供新视角。
  - AG简单、即插即用、无需训练，适用性广（支持负向提示、图像编辑、组合引导）。
  - Linear AG揭示分数轨迹的线性规律，为未来加速提供新方向。
- **实验亮点**：
  - 跨模型、跨分辨率、跨任务的迁移验证，增强了结论的鲁棒性。
  - 包含人机评估和统计检验，验证了主观质量不下降。
  - 效率测试覆盖多种GPU，显示实际落地潜力。
- **实用性**：与GD相比，AG避免重新训练，且保留负向提示功能（对负责任AI重要），更易部署。

## 8. 不足与局限

- **实验覆盖不足**：
  - 仅使用SSIM和人机评估作为质量指标，未使用FID、CLIP score等标准生成质量指标，可能与社区对比脱节。
  - 未与GD进行同条件下的定量对比（如相同NFE下的FID/CLIP），仅给出速度比例和功能比较。
  - 图像编辑实验仅展示定性示例，缺乏定量指标。
  - Linear AG仅展示定性结果，未做大规模定量评估或人机评估。

- **方法局限**：
  - AG性能依赖于阈值γ̄，该阈值需针对不同模型和任务调整（尽管文中表明通用性）。
  - Linear AG的线性回归模型基于少量数据（200张图像）和无条件分数，可能对复杂分布或低概率条件表现不佳；且无法精确复现基线，存在误差累积。
  - 搜索阶段耗时（1.5天），虽然是一次性成本，但对大规模部署可能形成门槛。

- **偏差风险**：
  - 搜索策略基于LDM-512在CC3M数据集上完成，可能对特定模型/数据域过拟合。
  - 人类评估仅使用五名标注员，可能存在主观偏差；且评估仅针对30 NFEs下是否“更偏好”，未区分细节差异。

- **应用限制**：
  - AG无法完全消除CFG计算（仍需要前半段评估），对于追求极致延迟的场景（如实时生成）帮助有限。
  - Linear AG不支持动态负向提示，因为回归模型基于空负向提示训练。

（完）
