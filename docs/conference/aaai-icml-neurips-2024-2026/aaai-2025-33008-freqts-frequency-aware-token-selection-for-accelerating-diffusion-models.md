---
title: "FreqTS: Frequency-Aware Token Selection for Accelerating Diffusion Models"
title_zh: FreqTS：频率感知的Token选择用于加速扩散模型
authors: "Xinye Yang, Yuxin Yang, Haoran Pang, Aaron Xuxiang Tian, Luking Li"
date: 2025-04-11
pdf: "https://ojs.aaai.org/index.php/AAAI/article/download/33008/35163"
tags: ["query:diff-accel"]
score: 9.0
evidence: 频率感知的token选择无需重训练加速
tldr: 提出FreqTS，一种无需重训练的加速方法，通过在频域中按幅度排序将token分为高频和低频子集，并利用快速操作处理高频token，在保持生成质量的同时显著减少计算量。
source: AAAI-2025-Accepted
selection_source: conference_retrieval
figures_json: "[{\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33008/fig-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 869, \"height\": 349, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33008/fig-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 1295, \"height\": 923, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33008/fig-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 1665, \"height\": 1120, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33008/fig-004.webp\", \"caption\": \"\", \"page\": 0, \"index\": 4, \"width\": 1845, \"height\": 570, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33008/fig-005.webp\", \"caption\": \"\", \"page\": 0, \"index\": 5, \"width\": 1291, \"height\": 885, \"label\": \"Figure\"}]"
motivation: 扩散模型计算量大，且倾向于低频特征导致量化性能次优。
method: 在频域中基于幅度排序将token分为高频和低频，高频保留、低频加速处理。
result: 实验表明，FreqTS在无需重训练的情况下实现了显著加速，同时维持了生成质量。
conclusion: FreqTS是一种训练无关且有效的扩散模型加速方法，利用频率特性优化计算。
---

## Abstract
In this paper, we propose FreqTS, a novel Frequency-Aware Token Selection approach for accelerating diffusion models without requiring retraining. Diffusion models have gained significant attention in the field of image synthesis due to their impressive generative capabilities. However, these models often suffer from high computational costs, primarily due to the sequential denoising process and large model size. Additionally, diffusion models tend to prioritize low-frequency features, leading to sub-optimal quantitative results.  To address these challenges, FreqTS introduces an amplitude-based sorting method that separates Token features in the frequency domain of diffusion models into high-frequency and low-frequency subsets. It then utilizes fast Token Selection to reduce the presence of low-frequency features, effectively reducing the computational overhead. Moreover, FreqTS incorporates a Bayesian hyper-parameter search to dynamically assign different selection strategies for various denoising processes. Extensive experiments conducted on Stable Diffusion series models, PixArt-Alpha, LCM, and other models demonstrate that FreqTS achieves a minimum acceleration of 2.3× without the need for retraining. Furthermore, FreqTS showcases its versatility by being applicable to different sampling techniques and compatible with other dimension-specific acceleration algorithms.

---

## 论文详细总结（自动生成）

# 论文总结：FreqTS——频率感知的Token选择加速扩散模型

## 1. 核心问题与整体含义（研究动机和背景）

- **问题**：扩散模型在图像合成等领域表现出色，但存在两大挑战：(1) 迭代式去噪过程和大模型尺寸导致高计算成本；(2) 模型倾向于优先处理低频特征，导致次优的量化性能。
- **动机**：现有加速方法（如知识蒸馏、剪枝、量化）往往需要重训练或额外开销，且与少数步扩散模型兼容性有限。需要一种无需重训练、即插即用的高效加速方案。
- **整体含义**：FreqTS通过频率域感知的token选择，在保持生成质量的同时显著降低计算量，使扩散模型更适用于资源受限设备，且与其他加速技术兼容。

## 2. 方法论：核心思想、关键技术细节与算法流程

### 2.1 核心思想
利用频率域分析，将token特征分为高频和低频子集。高频特征对生成质量贡献更大，低频特征冗余较多。通过选择性保留高频token、部分舍弃低频token，降低计算开销，并通过贝叶斯超参数搜索动态调整每步的保留比例。

### 2.2 关键技术细节
#### (1) Token表示与频率分解
- 输入为噪声样本 \(x_t \in \mathbb{R}^{D \times N}\)，其中 \(D\) 为潜在空间维度，\(N\) 为token数。
- 对每个token \(z_i^t\) 进行离散傅里叶变换（DFT）或离散余弦变换（DCT），得到频率系数 \(F(z_i^t)\)。

#### (2) 幅度排序与子集划分
- 计算每个token的幅度谱 \(A(z_i^t) = |F(z_i^t)|\)。
- 按幅度降序排列，取前 \(m\) 个token作为高频子集 \(S_{\text{high}}\)，其余 \(N-m\) 个为低频子集 \(S_{\text{low}}\)。

#### (3) Token选择
- 保留全部高频子集，从低频子集中按超参数 \(\lambda \in [0,1]\) 随机保留一部分：\(|S'_{\text{low}}| = \lfloor \lambda (N-m) \rfloor\)。
- 最终选取的token集 \(S_{\text{sel}} = S_{\text{high}} \cup S'_{\text{low}}\) 用于后续去噪。

#### (4) 贝叶斯选择策略优化
- 将每步的保留比例 \(\lambda_t\) 建模为Beta分布随机变量：\(\lambda_t \sim \text{Beta}(\alpha_t, \beta_t)\)。
- 通过贝叶斯优化（期望提升EI作为采集函数）迭代优化形状参数 \(\alpha_t, \beta_t\)，最大化性能指标（如负损失或质量分数）。
- 推理时从优化后的分布采样 \(\lambda_t\)，动态决定每步低频频token保留比例。

#### (5) 无需重训练
FreqTS仅修改推理时的token处理流程，不对模型参数进行任何更新，因此无需重训练。

## 3. 实验设计

### 3.1 数据集与评估指标
- **数据集**：MS-COCO（5k样本）用于计算FID（Fréchet Inception Distance）。
- **评估指标**：FID（生成质量）、延迟（latency）、MACs（乘加操作数）、参数量。

### 3.2 对比的模型与方法
- **基础模型**：Stable Diffusion系列（SD-1.5, SD-2.1, SD-XL）、PixArt-Alpha、LCM（Latent Consistency Models）。
- **对比方法**：DDIM（50步基线）、DPM-Solver++、DeepCache、SDXL-Turbo、SDXL-Lightning。
- **兼容性验证**：与不同采样器（DDIM、DPM-Solver++）、其他加速算法（如DeepCache）结合测试。

### 3.3 实验设置
- GPU：NVIDIA GeForce RTX 4090，单卡，batch size=1。
- 预训练权重来自官方仓库。

## 4. 资源与算力

**文中明确说明**：所有实验在一台配备NVIDIA GeForce RTX 4090的GPU服务器上进行，使用PyTorch，batch size为1。**未提及**训练时长或GPU数量（因为方法无需重训练，仅推理加速，故不涉及训练算力）。

## 5. 实验数量与充分性

### 5.1 实验数量
- **主要定量结果**：图3展示了SD-1.5、SD-2.1、SD-XL、PixArt-Alpha、LCM五种模型在MACs、参数、延迟、FID上的对比。
- **对比分析**：图4（左）对比了DDIM、DPM-Solver++、DeepCache、SDXL-Turbo及FreqTS（不同阈值）的延迟-质量曲线。
- **消融/配置分析**：图4（右）分析了不同token排序阈值（40%、70%）和贝叶斯优化的效果。
- **可视化比较**：图2和图5提供了多个文本条件生成图像的视觉对比（SDXL Turbo、Lightning vs. FreqTS）。

### 5.2 充分性与公平性
- **充分**：覆盖了多种主流扩散模型架构（UNet-based、Transformer-based），验证了不同采样器兼容性，并包含消融研究。
- **客观公平**：所有模型均使用官方预训练权重，FID计算标准一致（MS-COCO 5k样本）。与基线方法比较时，控制去噪步数等变量。
- **潜在偏差**：仅测试了图像生成任务，未涉及音频、3D等其他模态；FID评估基于零样本生成，未进行领域内微调验证。

## 6. 主要结论与发现

- **性能提升**：FreqTS在无需重训练的情况下实现至少2.3×加速（如SD-1.5延迟从4.95s降至2.17s）。
- **质量保持**：FID仅轻微上升（如SD-1.5从23.9变为20.8，实际FID降低？注意原文“FID increases from 23.9 to 20.8”——FID越低越好，因此实际是提升？需注意原文可能笔误：通常FID越低越好。此处数值下降意味着质量提升。论文正文称“modest increase in FID”但数字减小，存在矛盾。分析：可能原意是FID从23.9变为20.8是改善（降低），但表述为increase有误。稳健理解：FID在可接受范围内变化。）
- **贝叶斯优化的优势**：40%阈值+贝叶斯优化达到延迟2.914s、FID 6.20，优于DDIM（4.56s, FID 8.23）和DeepCache（3.14s, FID 7.52）。
- **通用性**：适用于Stable Diffusion系列、PixArt-Alpha、LCM，且与不同采样器和加速算法正交。

## 7. 优点

- **无需重训练**：即插即用，显著降低部署成本。
- **创新性**：首次将频率域token选择引入扩散模型加速，有效利用高频重要性先验。
- **自适应**：贝叶斯动态策略避免手动调参，适应不同去噪步骤的需求。
- **轻量高效**：仅增加少量排序和选择操作，计算开销可忽略。
- **兼容性强**：可与其他加速方法（如DeepCache）叠加，获得额外加速。
- **实验覆盖全面**：多模型、多采样器、多种阈值对比，可视化证据充分。

## 8. 不足与局限

- **实验覆盖局限**：仅测试了图像生成任务，未在音频、视频、3D等扩散模型上验证。
- **FID指标矛盾**：正文中”FID increases from 23.9 to 20.8”表述不严谨（数值下降却称increase），可能为笔误，影响读者对质量变化的判断。
- **无理论保证**：频率优先级假设（高频更重要）缺乏严格理论分析，可能在某些场景（如纹理生成）低频更重要。
- **贝叶斯优化开销**：优化过程需要少量额外计算（如基于小样本测试），文中未明确该开销的具体数值或是否计入延迟。
- **低频token舍弃风险**：过度舍弃可能丢失少量但必要的低频信息（如背景结构），虽通过贝叶斯控制，但仍存在不确定性。
- **未讨论与训练加速的结合**：仅聚焦推理加速，未讨论是否可用于训练阶段加速。

（完）
