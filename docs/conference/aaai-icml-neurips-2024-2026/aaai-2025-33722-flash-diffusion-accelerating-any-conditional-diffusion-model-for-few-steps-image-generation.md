---
title: "Flash Diffusion: Accelerating Any Conditional Diffusion Model for Few Steps Image Generation"
title_zh: Flash Diffusion：加速任意条件扩散模型实现少步图像生成
authors: "Clément Chadebec, Onur Tasar, Eyal Benaroche, Benjamin Aubin"
date: 2025-04-11
pdf: "https://ojs.aaai.org/index.php/AAAI/article/download/33722/35877"
tags: ["query:diff-accel"]
score: 7.0
evidence: 加速条件扩散模型实现少步图像生成的方法
tldr: 提出一种通用蒸馏方法，加速预训练扩散模型至几步图像生成，在文本到图像、修复等多个任务上达到最先进性能，仅需极少训练。
source: AAAI-2025-Accepted
selection_source: conference_retrieval
figures_json: "[{\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33722/fig-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 884, \"height\": 177, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33722/fig-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 872, \"height\": 450, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33722/fig-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 870, \"height\": 254, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33722/fig-004.webp\", \"caption\": \"\", \"page\": 0, \"index\": 4, \"width\": 876, \"height\": 401, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33722/fig-005.webp\", \"caption\": \"\", \"page\": 0, \"index\": 5, \"width\": 1567, \"height\": 563, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33722/fig-006.webp\", \"caption\": \"\", \"page\": 0, \"index\": 6, \"width\": 806, \"height\": 841, \"label\": \"Figure\"}]"
tables_json: "[{\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-33722/table-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 1747, \"height\": 869, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-33722/table-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 878, \"height\": 406, \"label\": \"Table\"}]"
motivation: 扩散模型采样慢，现有蒸馏方法计算资源需求大。
method: 高效蒸馏方法，用少量训练步骤将多步扩散模型压缩为少步模型。
result: 在多个数据集和任务上达到少步生成的SOTA，FID和CLIP-Score优秀。
conclusion: 提出的蒸馏方法高效且通用，适用于多种扩散模型架构。
---

## Abstract
In this paper, we propose an efficient, fast, and versatile distillation method to accelerate the generation of pre-trained diffusion models. The method reaches state-of-the-art performances in terms of FID and CLIP-Score for few steps image generation on the COCO2014 and COCO2017 datasets, while requiring only several GPU hours of training and fewer trainable parameters than existing methods. In addition to its efficiency, the versatility of the method is also exposed across several tasks such as *text-to-image*, *inpainting*, *face-swapping*, *super-resolution* and using different backbones such as UNet-based denoisers (SD1.5,  SDXL), DiT (Pixart) and MMDiT (SD3), as well as adapters. In all cases, the method allowed to reduce drastically the number of sampling steps while maintaining very high-quality image generation.

---

## 论文详细总结（自动生成）

# Flash Diffusion：加速任意条件扩散模型实现少步图像生成 —— 论文总结

## 1. 核心问题与整体含义（研究动机与背景）

- **问题**：扩散模型（DM）在图像生成中表现优异，但其迭代式采样机制需要大量步数（通常 50 步以上），严重限制了实时应用和部署效率。
- **现有局限**：
  - 高效求解器（如 DPM-Solver）仍需至少 10 步才能获得满意结果。
  - 已有蒸馏方法（如渐进式蒸馏、一致性模型）通常需要大量训练资源（参数、GPU 时间），或需迭代更新教师模型，且多数方法仅针对特定任务（如文生图），通用性不足。
- **本文目标**：提出一种快速、稳健、且**高度通用**的蒸馏方法，能在极少步数（1-4 步）下保持高质量生成，并适用于多种任务（文生图、修复、超分、换脸）和多种模型架构（UNet、DiT、MMDiT），同时训练代价极低。

## 2. 方法论：核心思想、关键技术细节

### 核心思想
- 训练一个**学生模型**，使其一步预测教师模型经过多步去噪后的结果，并结合**对抗损失**和**分布匹配损失**，将学生分布拉近真实数据分布与教师分布。

### 关键技术细节
1. **蒸馏损失**（Distillation Loss）：
   - 对给定噪声隐变量 \( z_t \)（由真实样本 \( z_0 \) 加噪得到），学生模型预测去噪版本 \( \tilde{z}_0^{\text{student}} \)。
   - 目标为教师模型经 ODE 求解器多步生成的 \( \tilde{z}_0^{\text{teacher}} \)（去噪结果）。
   - 损失函数：\( \mathcal{L}_{\text{distil}} = \mathbb{E} \left[ \| f_\theta(z_t, t) - \tilde{z}_0^{\text{teacher}}(z_t) \|^2 \right] \)。

2. **时间步采样策略**（Timesteps Sampling）：
   - 将连续时间 \( [0,1] \) 离散化为 \( K \) 个均匀时间步（通常 16/32/64）。
   - 使用**混合高斯分布**定义概率密度函数 \( \pi(t) \)，在关键时间步（如 \( t=0.25,0.5,0.75,1.0 \)）上超采样，同时保持对其他时间步的覆盖，避免过拟合和多样性下降。
   - 训练过程中从**预热阶段**（侧重小噪声）逐渐向**全噪声**偏移，以利于单步生成。

3. **对抗损失**（Adversarial Loss）：
   - 在隐空间中训练判别器 \( D_\nu \)，将学生生成样本与真实样本区分。
   - 判别器输入为经过**再噪声**（re-noise）的样本（噪声水平从 \( [0.01,0.25,0.5,0.75] \) 均匀采样），有利于区分高低频细节。
   - 使用 LSGAN 形式，鲁棒性强。

4. **分布匹配损失**（Distribution Matching Distillation, DMD）：
   - 最小化学生分布与教师分布之间的 KL 散度。
   - 无需额外训练另一个扩散模型：学生自身提供学生分数，教师提供教师分数，计算梯度更新。

5. **参数高效训练**：
   - 学生模型使用 **LoRA**（低秩适配）进行微调，仅更新少量参数（约 26.4M/900M 总参数），大幅降低训练成本。

6. **训练总损失**：
   \[
   \mathcal{L} = \mathcal{L}_{\text{distil}} + \lambda_{\text{adv}} \mathcal{L}_{\text{adv}} + \lambda_{\text{DMD}} \mathcal{L}_{\text{DMD}}
   \]

### 算法流程（文字描述）
- 输入：真实图像 → VAE 编码得到隐变量 \( z_0 \)。
- 采样时间步 \( t \sim \pi(t) \)，加噪得到 \( z_t \)。
- 教师模型对 \( z_t \) 进行多步去噪，生成 \( \tilde{z}_0^{\text{teacher}} \)（使用 CFG，引导尺度随机采样）。
- 学生模型一步预测 \( \tilde{z}_0^{\text{student}} \)。
- 计算蒸馏损失。
- 将学生预测再噪声，分别送入判别器（对抗损失）和教师/学生（DMD 损失）。
- 更新学生（LoRA）和判别器参数。

## 3. 实验设计：数据集、基准、对比方法

### 数据集与基准
- **文生图**：
  - COCO2014（30,000 prompt，FID-30k）
  - COCO2017（5,000 prompt，FID-5k + CLIP Score）
- **其他任务**：
  - 模型训练在 LAION 数据集（美学分数 >6）上进行。
- **评估指标**：FID（越低越好）、CLIP Score（越高越好）。

### 对比方法
- **基线方法**：原始扩散模型（SD1.5, SDXL, Pixart-α, SD3）在不同步数下的结果。
- **蒸馏方法**：Progressive Distillation、InstaFlow、CFG Distillation、UFOGen、DMD、LCM-LoRA、SDXL-Lightning、Hyper-SD、Turbo 等。
- **任务扩展**：在修复、超分（×4）、换脸、T2I-Adapter、现有 LoRA 组合上进行视觉比较。

## 4. 资源与算力

- **训练配置**：26 H100 GPU 小时（针对 SD1.5 模型，LoRA 训练）。
- **学生参数**：26.4M 可训练参数（占教师模型总参数 900M 的约 3%）。
- **其他架构**：SDXL、Pixart、SD3 的 LoRA 训练参数相应调整，文中未逐一列出精确 GPU 小时数，但整体训练代价较低。
- **注意**：文中未明确说明完整训练所需的总 GPU 型号数量（仅给出 H100 小时数），也未详细对比其他方法的具体训练成本（硬件、时长），仅提到多数竞争者需训练整个 UNet。

## 5. 实验数量与充分性

- **主要实验组**：
  - 文生图：SD1.5 在 COCO2017 和 COCO2014 上的定量对比（约 10 种方法 + 多步数比较）。
  - 消融实验：损失项、时间步采样分布、蒸馏损失类型、对抗损失类型、K 值选择、引导尺度等（共 7 个子实验表格 + 图）。
  - 架构泛化：SDXL、Pixart-α、SD3 三类不同骨干网络（UNet/DiT/MMDiT）分别在 COCO2014 上的定量结果及视觉对比。
  - 任务泛化：修复、超分、换脸、T2I-Adapter、LoRA 兼容性（各给出视觉样本）。
- **充分性评价**：
  - **优点**：消融实验全面（损失组合、时间步策略、K 值、引导尺度），对比方法覆盖主流蒸馏方法，且在不同架构和任务上验证了通用性，实验较充分。
  - **不足**：部分对比中仅引用他人结果（如 COCO2014 中标注†结果），未在统一设置下重新训练所有基线；修复/超分/换脸任务仅提供视觉示例，缺乏定量指标（如 PSNR、SSIM 等）；LoRA 兼容性仅展示图片，无量化评估。

## 6. 论文的主要结论与发现

1. **性能领先**：在 COCO2017（2 NFE）上 FID=22.6，CLIP=30.6；在 COCO2014（2 NFE）上 FID=12.27，均达到当时 SOTA（少步生成）。
2. **高效训练**：仅需 26.4M 参数和 26 H100 GPU 小时，远低于多数需训练整个 UNet 的方法。
3. **关键设计有效**：
   - 蒸馏损失是稳定性基石；GAN 损失提升图像质量；DMD 提升提示对齐。
   - 所提出的时间步混合高斯采样（超采样关键步+覆盖其他步）显著优于均匀/高斯/仅4步采样。
   - LSGAN 作为对抗损失最优；MSE 优于 LPIPS。
4. **通用性强**：成功应用于 UNet（SD1.5, SDXL）、DiT（Pixart）、MMDiT（SD3）以及多种条件任务，并可与现有 LoRA 和 T2I-Adapter 无缝结合。

## 7. 优点

- **方法创新性**：巧妙结合蒸馏、GAN、分布匹配三种损失，并通过时间步动态采样策略提升效率。
- **参数高效**：通过 LoRA 仅微调少量参数，训练成本极低，易于迁移到新模型。
- **通用性突出**：涵盖多种主流架构和任务，是当前少有的跨架构/跨任务的扩散蒸馏方法。
- **消融实验完整**：深入分析了每个组件的影响，验证了设计合理性。
- **引导尺度灵活性**：训练时随机采样引导尺度，避免了固定引导的过拟合。

## 8. 不足与局限

- **数值不完整**：在修复、超分、换脸等任务上缺乏定量指标（如 PSNR, SSIM, FID 等），仅提供视觉样本，难以严格评估。
- **基线复现不全面**：部分对比结果直接引用原文（如 COCO2014 中 UFOGen、DMD 等），未在相同训练设置下复现，可能存在偏差。
- **训练成本对比模糊**：未详细列出竞争方法的训练参数和 GPU 时间，公平性难以精确判断。
- **超参数敏感性**：引导尺度、时间步数量 K 等仍需手动调整，泛化到新任务时可能需要重新调参。
- **LoRA 兼容性评估**：仅展示图片，未定量测试与现有 LoRA 结合后的性能损失。
- **应用限制**：方法基于教师模型，仍依赖于教师的质量；若教师模型本身存在偏差（如风格偏好），学生可能继承这些偏差。

（完）
