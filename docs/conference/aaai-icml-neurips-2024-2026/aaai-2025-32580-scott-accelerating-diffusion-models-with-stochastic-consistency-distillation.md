---
title: "SCott: Accelerating Diffusion Models with Stochastic Consistency Distillation"
title_zh: SCott：基于随机一致性蒸馏的扩散模型加速
authors: "Hongjian Liu, Qingsong Xie, Tianxiang Ye, Zhijie Deng, Chen Chen, Shixiang Tang, Xueyang Fu, Haonan Lu, Zheng-Jun Zha"
date: 2025-04-11
pdf: "https://ojs.aaai.org/index.php/AAAI/article/download/32580/34735"
tags: ["query:diff-accel"]
score: 8.0
evidence: 通过随机一致性蒸馏加速扩散模型
tldr: 针对扩散模型迭代采样延迟大问题，提出随机一致性蒸馏（SCott），将随机微分方程求解器融入一致性蒸馏，以2-4步甚至单步生成高质量图像。该方法需要学生模型训练，但显著加速了教师模型的推断过程，属于强力加速方法。
source: AAAI-2025-Accepted
selection_source: conference_retrieval
figures_json: "[{\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-32580/fig-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 1835, \"height\": 752, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-32580/fig-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 883, \"height\": 571, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-32580/fig-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 583, \"height\": 133, \"label\": \"Figure\"}]"
tables_json: "[{\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-32580/table-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 722, \"height\": 207, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-32580/table-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 879, \"height\": 301, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-32580/table-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 1797, \"height\": 577, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-32580/table-004.webp\", \"caption\": \"\", \"page\": 0, \"index\": 4, \"width\": 882, \"height\": 240, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-32580/table-005.webp\", \"caption\": \"\", \"page\": 0, \"index\": 5, \"width\": 844, \"height\": 316, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-32580/table-006.webp\", \"caption\": \"\", \"page\": 0, \"index\": 6, \"width\": 845, \"height\": 207, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-32580/table-007.webp\", \"caption\": \"\", \"page\": 0, \"index\": 7, \"width\": 845, \"height\": 321, \"label\": \"Table\"}]"
motivation: 扩散模型迭代采样导致高延迟。
method: 将SDE求解器整合到一致性蒸馏中，训练学生模型实现少步生成。
result: 2-4步即可生成高质量文本到图像，性能优于单步蒸馏。
conclusion: SCott有效加速了扩散模型生成，但需要训练。
---

## Abstract
The iterative sampling procedure employed by diffusion models (DMs) often leads to significant latency. To address this, we propose Stochastic Consistency Distillation (SCott) to enable accelerated text-to-image generation, where high-quality generations can be achieved with just 2-4 sampling steps or even1 step, and further improvements can be obtained by additional cost, e.g., 4 steps. In contrast to vanilla consistency distillation (CD) which distills the ordinary differential equation solvers-based sampling process of a pre-trained teacher model into a student, SCott explores the possibility and validates the efficacy of integrating stochastic differential equation (SDE) solvers into CD to fully unleash the potential of the teacher. SCott is augmented with elaborate strategies to control the noise strength and sampling process of the SDE solver. An adversarial loss is further incorporated to strengthen the sample quality with rare sampling steps. Empirically, on the MSCOCO-2017 5K dataset with a Stable Diffusion-V1.5 teacher, SCott achieves an FID of 21.9, surpassing that of the 1-step InstaFlow (23.4) and the 4-step UFOGen (22.1). Moreover, SCott can yield more diverse samples than other consistency models for high-resolution image generation, with up to 16% improvement in a qualified metric.

---

## 论文详细总结（自动生成）

# 论文总结：SCott：基于随机一致性蒸馏的扩散模型加速

## 1. 核心问题与整体含义（研究动机和背景）

扩散模型（DMs）在图像生成等领域表现出色，但其迭代采样过程导致推理延迟高，限制了实际应用。已有的加速方法（如ODE求解器、渐进式蒸馏、一致性蒸馏等）在极少步数（如2-4步）下生成质量有限，且部分方法（如InstaFlow、UFOGen）仅支持单步生成，无法通过增加步数进一步提升效果。论文旨在实现一个**统一模型**，能在2-4步甚至1步内生成高质量图像，同时保留通过增加步数提升质量的能力。核心思路是将随机微分方程（SDE）求解器融入一致性蒸馏（CD），因为对于训练良好的扩散模型，SDE求解器在足够步数下通常优于ODE求解器，但直接使用SDE求解器会导致训练不稳定。SCott通过控制噪声强度、多步采样策略和对抗损失解决了这一问题。

## 2. 方法论：核心思想、关键技术细节

### 核心思想

将SDE求解器替代传统CD中的ODE求解器（如DDIM），构造更强的教师模型，引导学生模型学习从噪声到数据的直接映射。同时结合对抗学习，进一步提升稀少步数下的采样质量。

### 关键技术细节

1. **SDE求解器用于CD的理论保证**：论文给出定理1，证明若学生模型逼近由SDE定义的连续映射，且SDE求解器局部误差为O((Δt)^(p+1))，则当CD损失为零时，学生模型与真实映射的误差为O((Δt)^p)。这为SDE-CD的可行性提供了理论支撑。

2. **控制噪声强度**：通过调节DDIM中噪声系数η（η越大噪声越强）或采用ER-SDE求解器（其噪声缩放函数可控制强度），在享受SDE噪声校正误差的同时避免过大噪声导致训练不稳定。

3. **多步SDE求解器采样**：在计算教师预测时，将单步SDE求解扩展为多步（h步），缩小离散化步长，降低离散化误差，同时利用随机噪声修正累积误差。论文中设置tm = tn - 24，h=3。

4. **引入GAN增强**：设计了一个基于LoRA的判别器，初始化自预训练U-Net，仅训练U-Net解码器的LoRA参数（仅占3.6%），并加入时间步和文本条件。对抗损失采用hinge loss，与CD损失联合优化：L_SCott = L_CD + λ_adv * L_adv，λ_adv=0.4。

### 公式与算法流程（文字说明）

- 学生模型f_θ以噪声zt_n和时间步t_n、文本c、引导尺度ω为输入，直接预测干净图像ˆz_0。
- 教师模型ϵ_θ（预训练扩散模型）使用多步SDE求解器从zt_n生成接近的中间状态ˆzt_m。
- CD损失：L_CD = E[λ(t_n) ||f_θ(zt_n, t_n) - f_θ⁻(ˆzt_m, t_m)||²]，其中θ⁻是EMA参数。
- 判别器D_ϕ以真实图像z_0或生成图像ˆz_0、时间步、文本为条件，使用hinge损失进行对抗训练。

## 3. 实验设计

### 数据集

- **LAION-Aesthetics-6+**：用于训练SCott模型（文本-图像对）。
- **MSCOCO-2017 5K**：用于零样本评估（验证集）。
- **MSCOCO-2014 30K**：用于额外基准比较。
- **MJHQ-5K**：从MJHQ-30K中随机选择，用于评估高图像质量和对齐。

### Benchmark

与以下方法比较：
- ODE求解器：DDIM（50步）、DPM++（25步）
- 蒸馏方法：渐进式蒸馏（PD）、Classifire-aware蒸馏（CAD）
- 一致性模型：LCM
- 单步方法：InstaFlow（0.9B和1.7B）、UFOGen
- 其他：SwiftBrush、DMD

### 对比方法数量

共对比了约9种方法（包括变体），并在三个数据集上进行了评估。

## 4. 资源与算力

论文明确说明：使用4张A100 GPU，批量大小40，训练40K迭代。学生模型和判别器的学习率分别为8e-6和2e-5。未提及总训练时长（小时数），但根据批量和迭代可推算大约数天。

## 5. 实验数量与充分性

- **主要结果**：在MSCOCO-2017 5K上报告FID、CLIP Score（CS）、Coverage（CR）。在MSCOCO-2014 30K上报告FID。在MJHQ-5K上报告FID、CS、CR。
- **消融实验**：
  - 不同SDE求解器（DDIM η=0/0.1/0.2/0.3/0.6，ER-SDE 5）对比（表5）。
  - 多步SDE采样步数（1/2/3步）对比（表6）。
  - 判别器设计（无GAN、全参数判别器、无条件判别器、LoRA判别器）对比（表7）。
- **定性比较**：附录中提供生成图像示例（正文未显示，但提及图5）。
- **公平性**：与LCM的对比基于官方实现，并复现在相同设置下。其他方法引用原始论文指标。然而，PD、CAD、InstaFlow、UFOGen的CR值缺失，且部分方法（如ADD）因训练细节未公开而无法比较。整体实验较充分，消融覆盖关键组件。

### 是否客观公平

论文对比了多个同类方法，并对自己的方法在不同步数下均报告结果，同时指出一些方法无法增加步数提升质量。消融实验控制变量合理。但部分基线指标可能来自不同论文，存在略微差异。

## 6. 主要结论与发现

- SCott在2步采样下FID达到21.9（MSCOCO-2017 5K，SD1.5教师），优于1步InstaFlow（23.4）、2步LCM（30.4）和4步UFOGen（22.1）。
- 在MSCOCO-2014 30K上，2步SCott FID 11.13，4步10.68，超越单步方法（DMD 11.49、InstaFlow 13.10等）。
- 在MJHQ-5K上，2步SCott FID 24.9，远优于2步LCM（37.2），且Coverage指标高出16%（0.8794 vs 0.7016）。
- SCott能够通过增加步数（从1到4）持续改善FID和CS，而UFOGen等无法做到。
- 样本多样性优于LCM，甚至超过50步DDIM，得益于SDE引入的随机性。

## 7. 优点

- **方法创新**：首次将SDE求解器系统性地引入一致性蒸馏，并给出理论保证。
- **性能优越**：在极少数步数下（2步）即达到或超越以往单步/多步方法，且保留多步提升潜力。
- **多样性好**：由于SDE随机性，生成的样本多样性显著高于基于ODE的CD模型。
- **高效训练**：LoRA判别器仅更新3.6%参数，训练稳定且计算高效。
- **实验设计全面**：在多个数据集上评估，包括高分辨率复杂场景（MJHQ），并报告覆盖度指标。

## 8. 不足与局限

- **实验覆盖有限**：未在更大规模模型（如SDXL）或更广泛任务（如视频生成）上验证。
- **对比方法不全**：未与最新的蒸馏方法（如ADD、CTM）进行公平比较（因训练细节不公开或不可复现）。
- **训练成本**：虽然推理加速，但训练仍需4张A100和40K迭代，对资源有一定要求。
- **理论分析深度**：定理1假设SDE求解器局部误差界，但未给出具体p值，实际中可能影响收敛保证。
- **偏差风险**：使用LAION-Aesthetics-6+数据集，可能引入美学偏向，生成图像可能过度追求美学而忽视某些真实场景。
- **应用限制**：目前仅针对文本到图像生成，其他模态（如文本到视频）未探讨。

（完）
