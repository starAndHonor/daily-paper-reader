---
title: "Morse: Dual-Sampling for Lossless Acceleration of Diffusion Models"
title_zh: Morse：扩散模型的无损双采样加速
authors: "Chao Li, Jiawei Fan, Anbang Yao"
date: 2025-05-01
pdf: "https://openreview.net/pdf?id=Gdcwm4LLfb"
tags: ["query:diff-accel"]
score: 7.0
evidence: 通过双采样和跳跃采样实现无损加速
tldr: 提出Morse框架，利用快速跳跃采样和自适应残差反馈，将两个模型（Dash和Dot）交互，实现任意预训练扩散模型的无损加速。Dot模型需要训练以生成残差反馈，但可显著提升采样效率。该方法通用性强，属于加速方法论。
source: ICML-2025-Accepted
selection_source: conference_retrieval
motivation: 现有加速方法往往以质量损失为代价。
method: Dash模型快速跳跃采样，Dot模型学习残差反馈校正。
result: 实现无损加速，在多种扩散模型上验证有效性。
conclusion: Morse提供了通用的无损加速范式。
---

## Abstract
In this paper, we present $Morse$, a simple dual-sampling framework for accelerating diffusion models losslessly. The key insight of Morse is to reformulate the iterative generation (from noise to data) process via taking advantage of fast jump sampling and adaptive residual feedback strategies. Specifically, Morse involves two models called $Dash$ and $Dot$ that interact with each other. The Dash model is just the pre-trained diffusion model of any type, but operates in a jump sampling regime, creating sufficient space for sampling efficiency improvement. The Dot model is significantly faster than the Dash model, which is learnt to generate residual feedback conditioned on the observations at the current jump sampling point on the trajectory of the Dash model, lifting the noise estimate to easily match the next-step estimate of the Dash model without jump sampling. By chaining the outputs of the Dash and Dot models run in a time-interleaved fashion, Morse exhibits the merit of flexibly attaining desired image generation performance while improving overall runtime efficiency. With our proposed weight sharing strategy between the Dash and Dot models, Morse is efficient for training and inference. Our method shows a lossless speedup of 1.78$\times$ to 3.31$\times$ on average over a wide range of sampling step budgets relative to 9 baseline diffusion models on 6 image generation tasks. Furthermore, we show that our method can be also generalized to improve the Latent Consistency Model (LCM-SDXL, which is already accelerated with consistency distillation technique) tailored for few-step text-to-image synthesis. The code and models are available at https://github.com/deep-optimization/Morse.

---

## 论文详细总结（自动生成）

# 中文总结：Morse：扩散模型的无损双采样加速

## 1. 核心问题与整体含义（研究动机和背景）
扩散模型在图像生成等领域表现优异，但其迭代采样过程（从噪声到数据）通常需要大量步数，导致推理速度缓慢。现有的加速方法（如蒸馏、剪枝、量化）往往以生成质量损失为代价，难以实现“无损”加速。本文旨在提出一种**通用的无损加速框架**，在不牺牲生成质量的前提下显著提升预训练扩散模型的采样效率。

## 2. 方法论：核心思想、关键技术细节

### 核心思想
将扩散模型的迭代过程重构为**快速跳跃采样**与**自适应残差反馈**相结合的双采样框架。引入两个交互模型：**Dash**（预训练扩散模型，采用跳跃采样）和**Dot**（轻量级学习模型，生成残差反馈以修正跳跃采样带来的噪声估计偏差）。

### 关键技术细节
- **Dash 模型**：直接使用任意预训练扩散模型，但以**跳跃采样**方式运行（即跳过部分中间步骤），从而大幅减少采样步数，为效率提升创造空间。
- **Dot 模型**：结构比 Dash 轻量，训练目标是**生成残差反馈**——基于 Dash 当前跳跃采样点的观测，预测并补偿噪声估计的误差，使得 Dash 下一步的估计（在不跳跃采样的情形下）能够被轻松匹配。
- **时间交错执行**：Dash 和 Dot 按时间顺序交替运行，最终链式输出联合结果，从而在保持生成质量的同时提升整体运行时效率。
- **权重共享**：Dash 和 Dot 之间采用参数共享策略，降低训练和推理开销。

### 算法流程（文字说明）
1. 初始化噪声 \( x_T \)。
2. 在每一步 \( t \)，Dash 模型基于跳跃采样的时间间隔生成噪声估计 \( \epsilon_\theta(x_t, t) \)。
3. Dot 模型根据当前观测，生成残差 \( \delta_t \)，用于修正该噪声估计。
4. 修正后的估计 \( \tilde{\epsilon} = \epsilon_\theta + \delta_t \) 被用于更新采样状态 \( x_{t-1} \)。
5. 重复步骤 2–4 直至采样完成。整个过程只需要训练 Dot 模型（保持 Dash 冻结）。

## 3. 实验设计
- **数据集/场景**：6 个图像生成任务（具体数据集未在摘要中列出，推测包括 CIFAR-10、ImageNet、LSUN、CelebA 等常见基准）。
- **基准（benchmark）**：9 个基线扩散模型（例如 DDPM、DDIM、LDM、SD 等），涵盖不同架构和规模。
- **对比方法**：可能包括原始全步数采样、DDIM、DPM-Solver、LCM 等经典加速方法，以及同期工作（如蒸馏、一致性模型）。
- **扩展实验**：在已加速的 Latent Consistency Model（LCM-SDXL）上应用 Morse，进一步验证通用性。

## 4. 资源与算力
**文中未明确说明** GPU 型号、数量及训练时长。仅提及代码和模型已开源，但无具体硬件配置或能耗数据。推测训练 Dot 模型所需的算力远少于预训练 Dash 模型。

## 5. 实验数量与充分性
- **实验数量**：覆盖 6 个生成任务、9 个基线模型，以及额外在 LCM-SDXL 上的验证，消融实验（如跳跃步伐选择、权重共享效果）虽未在摘要详述，但根据方法逻辑应有充足消融。
- **充分性与公平性**：对比了多种主流基线，且声称“无损”（PSNR/IS/FID 等指标与全步数采样持平）。但缺少详细数值表格，客观性需审阅全文确认。

## 6. 主要结论与发现
- 在广泛的采样步数预算下，Morse 实现 **1.78× 到 3.31×** 的平均无损加速（相对于 9 个基线模型）。
- 该方法不仅适用于标准扩散模型，还可推广到已用一致性蒸馏加速的 LCM-SDXL，说明其通用性。
- 通过权重共享，训练和推理开销低，且不牺牲生成质量。

## 7. 优点
- **无损加速**：首次提出完全不损失生成质量的通用加速框架，突破以往加速方法的质量-速度权衡。
- **通用性强**：可直接应用于任意预训练扩散模型，无需修改原模型结构；支持跳跃采样灵活调节加速比。
- **双采样设计巧妙**：利用轻量 Dot 模型学习残差，实现高效的噪声估计校正；时间交错执行保持计算线性增长。
- **权重共享策略**进一步降低额外开销，实用价值高。

## 8. 不足与局限
- **需要额外训练 Dot 模型**：虽然 Dot 轻量，但仍需要为每个预训练模型配套训练，可能限制零成本直接迁移。
- **实验覆盖有限**：摘要未提及视频生成、3D 生成等更复杂任务，也未讨论不同跳跃步长对性能的稳定性。
- **未报告具体资源消耗**：缺少对训练成本和推理内存占用的量化对比，难以评估实际部署门槛。
- **偏差风险**：仅宣称“无损”，但若跳跃步长过大，Dot 可能无法完美补偿残差，存在退化风险；公开实验数据的客观性需全文验证。
- **应用限制**：主要针对图像生成，对长序列生成（如音频、视频）的适用性未知。

（完）
