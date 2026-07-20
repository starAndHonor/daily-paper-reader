---
title: "SpecDiff: Accelerating Diffusion Model Inference with Self-Speculation"
title_zh: SpecDiff：基于自推测的扩散模型推断加速
authors: "Jiayi Pan, Jiaming Xu, Yongkang Zhou, Guohao Dai"
date: 2026-03-17
pdf: "https://ojs.aaai.org/index.php/AAAI/article/download/37771/41733"
tags: ["query:diff-accel"]
score: 9.0
evidence: 免训练特征缓存用于扩散加速
tldr: 针对特征缓存方法仅利用历史信息导致精度和速度受限的问题，提出SpecDiff，通过自推测引入未来信息，实现免训练的多级特征缓存加速。该方法无需额外训练，直接加速扩散模型推断，满足训练自由加速需求。
source: AAAI-2026-Accepted
selection_source: conference_retrieval
figures_json: "[{\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37771/fig-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 869, \"height\": 469, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37771/fig-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 1776, \"height\": 805, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37771/fig-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 1838, \"height\": 497, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37771/fig-004.webp\", \"caption\": \"\", \"page\": 0, \"index\": 4, \"width\": 1842, \"height\": 427, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37771/fig-005.webp\", \"caption\": \"\", \"page\": 0, \"index\": 5, \"width\": 1759, \"height\": 325, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37771/fig-006.webp\", \"caption\": \"\", \"page\": 0, \"index\": 6, \"width\": 1772, \"height\": 409, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37771/fig-007.webp\", \"caption\": \"\", \"page\": 0, \"index\": 7, \"width\": 1792, \"height\": 661, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37771/fig-008.webp\", \"caption\": \"\", \"page\": 0, \"index\": 8, \"width\": 897, \"height\": 258, \"label\": \"Figure\"}]"
tables_json: "[{\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-37771/table-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 832, \"height\": 183, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-37771/table-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 826, \"height\": 511, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-37771/table-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 1829, \"height\": 342, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-37771/table-004.webp\", \"caption\": \"\", \"page\": 0, \"index\": 4, \"width\": 831, \"height\": 335, \"label\": \"Table\"}]"
motivation: 现有特征缓存方法仅利用历史信息，精度和速度受限。
method: 基于相同时间步不同迭代次数的信息相似性，通过自推测引入未来信息进行多级缓存。
result: 在保持生成质量的同时显著减少计算量。
conclusion: SpecDiff实现了高效的免训练扩散模型加速。
---

## Abstract
Feature caching has recently emerged as a promising method for diffusion model acceleration. It effectively alleviates the inefficiency problem caused by high computational requirements by caching similar features in the inference process of the diffusion model. In this paper, we analyze existing feature caching methods from the perspective of information utilization, and point out that relying solely on historical information will lead to constrained accuracy and speed performance. And we propose a novel paradigm that introduces future information via self-speculation based on the information similarity at the same time step across different iteration times. Based on this paradigm, we present SpecDiff, a training-free multi-level feature caching strategy including a cached feature selection algorithm and a multi-level feature classification algorithm. (1) Feature selection algorithm based on self-speculative information. SpecDiff determines a dynamic importance score for each token based on self-speculative information and historical information, and performs cached feature selection through the importance score. (2) Multi-level feature classification algorithm based on feature importance scores.SpecDiff classifies tokens by leveraging the differences in feature importance scores and introduces a multi-level feature calculation strategy.
Extensive experiments show that SpecDiff achieves average 2.80×, 2.74×, and 3.17× speedup with negligible quality loss in Stable Diffusion 3, 3.5, and FLUX compared to RFlow on NVIDIA A800-80GB GPU. By merging speculative and historical information, SpecDiff overcomes the speedup-accuracy trade-off bottleneck, pushing the Pareto frontier of speedup and accuracy in the efficient diffusion model inference.

---

## 论文详细总结（自动生成）

# 论文总结：SpecDiff: Accelerating Diffusion Model Inference with Self-Speculation

## 1. 核心问题与整体含义（研究动机和背景）
- **问题**：扩散模型（如Stable Diffusion 3、FLUX）推理计算量大，现有特征缓存方法仅利用历史信息（如相邻步特征相似性）进行缓存和重用，导致重要标记（token）选择不准确，精度和速度受限。作者从信息利用角度分析，指出单纯依靠历史信息只能捕捉局部变化，无法预测全局突变，成为性能瓶颈。
- **研究动机**：如何引入未来信息来辅助特征缓存，从而更精准地选择重要标记，突破加速-精度权衡的Pareto前沿。受LLM中推测解码（speculative decoding）启发，提出利用少量自推测步骤（self-speculation）获取未来时刻的信息，实现免训练的多级特征缓存策略。

## 2. 方法论：核心思想、关键技术细节
- **核心思想**：在相同时间步的不同迭代轮次中，特征高度相似。通过预先执行少量完整模型的推测步骤，获得未来时刻的注意力信息，与历史信息融合，动态评估每个标记的重要性，并采用多级特征计算策略，避免简单复用带来的累积误差。
- **关键技术细节**：
  - **特征选择算法（基于自推测信息）**：
    - 历史重要性分数 `his(x_i)`：上一轮迭代中该标记在所有层的注意力分数之和。
    - 未来重要性分数 `fut(x_i)`：从自推测步骤（2~4步）中获取的未来时间步（最近时间步）的注意力分数。
    - 饥饿分数 `star(x_i) = e^{c f(x_i)}`，其中 `c f(x_i)` 是标记被缓存的频率，防止长尾标记被永久忽略。
    - 综合重要性分数：`Score(x_i) = his(x_i) · fut(x_i) · star(x_i)`。
    - 根据缓存比例 `CR`，选择分数最高的 `1-CR` 比例标记进行全计算，其余缓存。
  - **多级特征分类算法**：
    - **C1：需计算标记**（最高分，按缓存比例确定）。
    - **C2：直接复用标记**（最低分，占总分前10%），直接使用上一迭代的特征。
    - **C3：近似计算标记**（其余90%），利用前3个时间步的噪声特征加权平均：  
      `F(x_cached_t) = Σ_{i=1}^3 W_{t+i} · F(x_cached_{t+i})`，权重 `W_{t+i} = e^{-i(T_{t+i} - T_t)} / Σ e^{-i(T_{t+i} - T_t)}`。
    - 该方法基于观察：低分标记的误差变异系数低，可直接复用；高分标记误差变异系数高，需要更精确的计算；而噪声特征在连续3步内余弦相似度较高，可用加权近似。

## 3. 实验设计：数据集/场景、基准、对比方法
- **数据集**：COCO 2014 val 数据集，随机选取5000个文本-图像对，生成1024×1024图像。
- **模型**：Stable Diffusion 3、Stable Diffusion 3.5、FLUX.1 Dev（主流DiT架构文本到图像模型）。
- **基准**：RFlow（完整推理，无缓存加速）；对比方法包括：
  - **RAS**（Region-Adaptive Sampling，基于两步历史信息）
  - **TaylorSeer**（基于N步历史信息近似，SOTA特征缓存方法）
- **评测指标**：
  - 质量：FID（越低越好）、CLIP Score（越高越好）、VQA Score（越高越好）
  - 额外补充指标（用于FLUX对比）：SSIM、PSNR、内存占用
  - 加速比：端到端推理速度归一化。

## 4. 资源与算力
- 文中明确说明：实验在 **8块 NVIDIA A800 80GB GPU** 上进行，生成速度实验在 **单块 A800 GPU** 上执行。
- 训练时长未提及（方法为训练-free，无需训练，只涉及推理开销），自推测步骤消耗<5%额外推理时间和<0.1%额外内存。

## 5. 实验数量与充分性
- **主要实验组数**：
  - 在SD3和SD3.5上，对三种缓存比例（50%/55%、75%/92%、87.5%/99%）分别对比RAS和SpecDiff。
  - 在FLUX上，对比TaylorSeer（两种配置N5O1, N6O1）与SpecDiff（85%、95%缓存比例）。
  - 设计空间探索实验：在SD3上测试不同推测步数（2/3/4步）对质量和速度的影响。
  - 消融实验：分别评估多级特征分类算法（Token Hierarchy）和特征预测机制（Feature Prediction Mechanism）对精度和速度的贡献。
- **充分性评价**：实验覆盖了三个主流模型、多种缓存比例、两种SOTA基线、多组评测指标，并进行了消融和超参数探索，设计较为全面；对比方法均为公开发表的代表性工作（RAS、TaylorSeer），实验设置公平（相同推理步数28、相同CFG等）。但仅使用COCO数据集，未涵盖其他类型数据集（如DrawBench、PartiPrompt）或多分辨率场景，可能存在一定偏差。

## 6. 主要结论与发现
- SpecDiff在保持生成质量（CLIP Score下降<1.5%，VQA Score下降<3%）的前提下，实现了显著加速：
  - SD3：平均2.80×加速
  - SD3.5：平均2.74×加速
  - FLUX：平均3.17×加速
- 相比仅使用历史信息的方法（RAS、TaylorSeer），SpecDiff在相同或更高加速比下取得更优的生成质量，成功推高了Pareto前沿。
- 自推测步骤数设为2时，取得最佳速度-精度权衡；推测步骤增加虽提升质量但降低速度。
- 多级特征分类和特征预测机制分别贡献了约1.34×和2.36×的加速（消融实验）。

## 7. 优点
- **创新性**：首次从信息利用角度系统分析特征缓存，并提出利用自推测引入未来信息，思路新颖。
- **实用性**：免训练、低额外开销（<5%时间，<0.1%内存），可直接应用于现有DiT模型。
- **设计简洁有效**：重要性分数结合历史、未来和饥饿信息，多级分类策略针对不同重要性标记采用不同计算方式，避免了简单复用导致的误差累积。
- **实验结果扎实**：在多个主流模型上验证，对比充分，并展示了可视化示例，直观说明生成质量保持良好。

## 8. 不足与局限
- **实验局限性**：仅评估了单一数据集（COCO 2014 val），未在更广泛场景（如多样化提示、不同分辨率、视频生成）中验证泛化能力。
- **指标偏差**：FID指标在SpecDiff上表现不如CLIP和VQA突出，作者解释可能由于高CFG使风格趋于统一，导致与真实图像分布偏差，但未提供深入分析或消融实验验证。
- **推测步骤的局限性**：自推测步骤虽开销小，但需要先执行2~4步完整推理，对于超低延迟场景可能增加首次推理延迟；且推测步骤数对性能有影响，未给出自适应选择策略。
- **应用限制**：方法针对DiT架构设计，对UNet-based扩散模型的适用性未验证；需要注意力分数作为输入，对于无注意力机制的变体可能不兼容。
- **对比方法时效性**：对比的RAS和TaylorSeer均为2025年工作，但论文发表于AAAI-2026，可能缺少更近期方法的对比。

（完）
