---
title: "AdaDiff: Adaptive Step Selection for Fast Diffusion Models"
title_zh: AdaDiff：自适应步长选择实现快速扩散模型
authors: "Hui Zhang, Zuxuan Wu, Zhen Xing, Jie Shao, Yu-Gang Jiang"
date: 2025-04-11
pdf: "https://ojs.aaai.org/index.php/AAAI/article/download/33075/35230"
tags: ["query:diff-accel"]
score: 8.0
evidence: 自适应步长选择加速扩散模型
tldr: 扩散模型生成过程需要大量去噪步骤，计算开销大。AdaDiff提出一种轻量级框架，通过策略梯度方法学习样本特定的步数使用策略，根据输入文本的丰富度自适应调整生成步数。实验表明，该方法在加速生成的同时保持了图像/视频质量，优于固定步数方案。该工作为扩散模型的自适应加速提供了新思路。
source: AAAI-2025-Accepted
selection_source: conference_retrieval
figures_json: "[{\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33075/fig-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 788, \"height\": 645, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33075/fig-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 1472, \"height\": 730, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33075/fig-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 801, \"height\": 385, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33075/fig-004.webp\", \"caption\": \"\", \"page\": 0, \"index\": 4, \"width\": 1548, \"height\": 1170, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33075/fig-005.webp\", \"caption\": \"\", \"page\": 0, \"index\": 5, \"width\": 801, \"height\": 363, \"label\": \"Figure\"}]"
tables_json: "[{\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-33075/table-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 843, \"height\": 341, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-33075/table-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 874, \"height\": 340, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-33075/table-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 866, \"height\": 419, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-33075/table-004.webp\", \"caption\": \"\", \"page\": 0, \"index\": 4, \"width\": 873, \"height\": 340, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-33075/table-005.webp\", \"caption\": \"\", \"page\": 0, \"index\": 5, \"width\": 875, \"height\": 355, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-33075/table-006.webp\", \"caption\": \"\", \"page\": 0, \"index\": 6, \"width\": 869, \"height\": 323, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-33075/table-007.webp\", \"caption\": \"\", \"page\": 0, \"index\": 7, \"width\": 884, \"height\": 281, \"label\": \"Table\"}]"
motivation: 扩散模型生成需多步去噪，计算昂贵，现有加速方法采用固定步数缺乏样本适应性。
method: 提出AdaDiff框架，用策略梯度学习每个样本的步数使用策略，以平衡速度与质量。
result: 在图像和视频生成任务上，AdaDiff在保持质量的同时显著减少推理步数。
conclusion: 自适应步长选择可有效加速扩散模型生成，且优于统一固定步数方法。
---

## Abstract
Diffusion models, as a type of generative model, have achieved impressive results in generating images and videos conditioned on textual conditions. However, the generation process of diffusion models involves denoising dozens of steps to produce photorealistic images/videos, which is computationally expensive. Unlike previous methods that design ``one-size-fits-all'' approaches for speed up, we argue denoising steps should be sample-specific conditioned on the richness of input texts. To this end, we introduce AdaDiff, a lightweight framework designed to learn instance-specific step usage policies, which are then used by the diffusion model for generation. AdaDiff is optimized using a policy gradient method to maximize a carefully designed reward function, balancing inference time and generation quality. We conduct experiments on three image generation and two video generation benchmarks and demonstrate that our approach achieves similar visual quality compared to the baseline using a fixed 50 denoising steps while reducing inference time by at least 33%, going as high as 40%.
Furthermore, our method can be used on top of other acceleration methods to provide further speed benefits.
Lastly, qualitative analysis shows that AdaDiff allocates more steps to more informative prompts and fewer steps to simpler prompts.

---

## 论文详细总结（自动生成）

### 论文详细中文总结

#### 1. 核心问题与整体含义（研究动机和背景）
- **研究动机**：扩散模型（如Stable Diffusion、Sora）在文本到图像/视频生成中性能优异，但生成过程需要数十步迭代去噪，计算开销巨大。现有加速方法（如更快的调度器、步蒸馏、模型剪枝等）普遍采用“一刀切”策略，对所有输入提示使用相同步数，忽视了提示文本的丰富程度差异。
- **核心问题**：能否根据输入文本的语义复杂度动态调整去噪步数，在保证生成质量的同时大幅减少推理时间？
- **研究背景**：论文指出，简单提示（如单个物体）用较少步数即可生成满意结果；复杂提示（多物体、详细描述、交互关系）需要更多步数。因此，自适应步长选择是提升扩散模型效率的新思路。

#### 2. 论文提出的方法论
- **核心思想**：训练一个轻量级的**步长选择网络**，对每个输入文本预测最优去噪步数，使扩散模型按需生成。该网络通过强化学习（策略梯度）优化，奖励函数平衡生成质量与计算成本。
- **关键技术细节**：
  - **步长选择网络**：由三个自注意力层和一个MLP组成，输入文本编码（来自CLIP等），输出一个N维概率向量（N=10，对应步数集合{5,10,15,...,50}）。训练时按分类分布采样，测试时取最大概率对应步数。
  - **奖励函数**：由两部分构成——**图像质量奖励**（使用预训练质量评估模型IQS，同时考虑文本-图像对齐和感知保真度）和**步数奖励**（节省步数越多奖励越高）。最终奖励R(u) = O(u) + λQ(u)（高质量图像）或 -γ（低质量图像）。其中“高质量”通过相对排名定义：对同一提示用所有候选步数生成图像，IQS得分排前k（default=3）视为高质量。
  - **训练算法**：使用蒙特卡洛采样近似策略梯度∇wL ≈ 1/B Σ[R(u) ∇w log π(u|p)]，Adam优化器。
  - **视频生成扩展**：对视频扩散模型（ModelScopeT2V），视频质量奖励取所有帧IQS得分的平均值，其余类似。

#### 3. 实验设计
- **数据集**：
  - 图像：MS COCO 2017（训练118K文本，测试25K）、Laion-COCO（训练200K，测试20K）、DiffusionDB（类似）。
  - 视频：MSR-VTT（训练6,651，测试2,870）、InternVid（训练24,911）。
- **Benchmark**：与多种基线对比，包括：
  - **固定步数**（50步、与AdaDiff速度相近的步数）
  - **随机策略**（按AdaDiff分布随机分配步数）
  - **启发式策略**（根据词数或困惑度分配步数）
  - **其他加速方法**：DPMSolver、SDXL-Lightning、SSD-1B、DeepCache等。
- **评估指标**：推理步数/时间、IQS、CLIP Score、IS、FID、NIQE。

#### 4. 资源与算力
- **明确说明**：步选择网络参数量25.71M，计算量1.93 GFLOPs，远小于SD-v2.1-base的865M/35,140 GFLOPs。训练步选择网络需200 epoch，batch size 256，初始学习率1e-5。针对不同基模型，训练成本为**16~80 A100 GPU小时**。与其他方法对比时，SDXL-Lightning训练需1000+ A100 GPU小时。

#### 5. 实验数量与充分性
- **实验组数**：共进行**3类图像生成**（COCO、Laion-COCO、DiffusionDB）和**2类视频生成**（MSR-VTT、InternVid）评估；包含不同基模型（SD-v2.1、SDXL、SDXL-Turbo、SSD-1B）的对比；消融实验（奖励函数设计、top-k/λ参数影响）；零样本迁移测试；定性分析。
- **充分性与公平性**：
  - 对比方法覆盖主流加速范式，并保持基模型一致。
  - 消融实验针对奖励函数各组件和阈值设定，验证了设计选择的有效性。
  - 在多个数据集和基模型上均取得一致结论，有5组独立运行取平均，结果可靠。
  - 不足：未在更大规模基模型（如Sora）或更复杂场景（如长视频、高分辨率视频）验证。

#### 6. 主要结论与发现
- AdaDiff在COCO上比固定50步的SD快39.7%，质量几乎持平（IQS 0.412 vs 0.419）；在视频生成上快35.8%且质量略优。
- 与随机、启发式、困惑度策略相比，AdaDiff在相同推理速度下显著提升图像/视频质量。
- 可叠加其他加速方法（如SDXL-Turbo、SSD-1B），获得额外加速（41%~53%）。
- 策略可零样本迁移：在COCO上训练的策略可直接用于Laion-COCO，保持类似加速比且质量无显著下降。
- 定性分析显示：简单提示分配10~20步，中等30步，复杂40~50步。

#### 7. 优点
- **创新性**：首次将强化学习用于扩散模型推理步数的自适应选择，而非传统固定步数或均匀加速。
- **效率**：步选择网络轻量，训练成本远低于步蒸馏等方法，且推理无额外开销。
- **通用性**：适用于图像/视频扩散模型，可与现有加速方法正交叠加。
- **奖励函数设计**：采用相对排名机制避免绝对阈值偏差，结合文本-图像对齐与感知保真度，有效引导策略学习。

#### 8. 不足与局限
- **实验覆盖**：仅测试了SD系列和ModelScopeT2V，未在最先进的大模型（如Sora、Hunyuan-DiT、VideoPoet）上验证。
- **偏差风险**：质量评估模型IQS本身可能存在偏好，可能导致步选择对某些提示类型不敏感。
- **应用限制**：步数离散集合（5~50步，步长5）可能不是最优粒度；更高分辨率/超长视频场景的计算成本未评估。
- **可解释性**：策略网络为何对特定提示选择某步数缺乏深层解释。

（完）
