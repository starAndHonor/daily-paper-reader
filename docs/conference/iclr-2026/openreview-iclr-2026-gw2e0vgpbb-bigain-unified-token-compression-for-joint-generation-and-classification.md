---
title: "BiGain: Unified Token Compression for Joint Generation and Classification"
title_zh: BiGain：面向生成与分类联合优化的统一令牌压缩
authors: "Jiacheng Liu, Shengkun Tang, Jiacheng Cui, Dongkuan Xu, Zhiqiang Shen"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=GW2e0VGPBb"
tags: ["query:diff-accel"]
score: 9.0
evidence: 免训练的令牌压缩用于加速扩散模型
tldr: 现有令牌压缩加速方法常忽略模型的判别能力。本文提出BiGain，一种免训练即插即用框架，通过频率分离将特征映射到频域表示，在不损害生成质量的同时提升加速扩散模型的分类能力。实验表明，BiGain在多种模型和数据集上均有效。
source: ICLR-2026-Public
selection_source: conference_retrieval
motivation: 现有令牌压缩优化生成质量但忽略判别能力。
method: 提出频率感知的令牌压缩，通过拉普拉斯算子分离细节与语义。
result: 在加速扩散模型上保持生成质量，且分类准确率显著提升。
conclusion: BiGain是一种通用加速工具，兼顾生成与判别任务。
---

## Abstract
Acceleration methods for diffusion models (e.g., token merging or downsampling) typically optimize for synthesis quality under reduced compute, yet they often ignore the model's latent discriminative capacity. We revisit token compression with a joint objective and present ${\bf BiGain}$, a training-free, plug-and-play framework that preserves generation quality while markedly improving classification in accelerated diffusion models. Our key insight is frequency separation: mapping feature-space signals into a frequency-aware representation disentangles fine detail from global semantics, enabling compression that respects both generative fidelity and discriminative utility. BiGain reflects this principle with two frequency-aware operators: (1) *Laplacian-gated token merging*, which encourages merges among spectrally smooth tokens while discouraging merges of high-contrast tokens, thereby retaining edges and textures; and (2) *Interpolate–Extrapolate KV Downsampling*, which downsamples keys/values via a controllable interpolation-extrapolation between nearest and average pooling while keeping queries intact, thereby conserving attention precision without retraining. Across DiT- and U-Net–based backbones and multiple datasets of ImageNet-1K, ImageNet-100, Oxford-IIIT Pets, and COCO-2017, our proposed operators consistently improve the speed–accuracy trade-off for diffusion-based classification, while maintaining, sometimes even enhancing generation quality under comparable acceleration. For instance, on ImageNet-1K, with a token merging ratio of 70\% on Stable Diffusion 2.0, BiGain improves classification accuracy by **7.1\%** while also reduces FID for generation by 0.56 (**3.1\%**).  Our comprehensive analyses indicate that balanced spectral retention, preserving high-frequency detail alongside low/mid-frequency semantic content is a reliable design rule for token compression in diffusion models. To our knowledge, BiGain is the first framework to jointly study and advance both generation and classification under accelerated diffusion, offering a practical solution to deployable, dual-purpose generative systems.

---

## 论文详细总结（自动生成）

# BiGain：面向生成与分类联合优化的统一令牌压缩 — 详细论文总结

## 1. 核心问题与整体含义（研究动机和背景）
- **研究动机**：现有扩散模型加速方法（如令牌合并、特征下采样）通常只针对生成质量进行优化，忽略了模型潜在判别能力（如分类任务）。加速后的模型在生成任务上表现良好，但在分类等判别任务上性能显著下降。
- **整体含义**：BiGain 提出一种免训练、即插即用的框架，旨在同时保持生成质量并提升分类准确率，使加速扩散模型成为兼具高效生成与判别能力的双用途系统。

## 2. 论文提出的方法论
- **核心思想**：频率分离（frequency separation）——将特征空间的信号映射为频率感知表示，解耦高频细节（边缘、纹理）与低频/中频全局语义，使得令牌压缩既能保留生成保真度，又能维持判别效用。
- **关键技术细节**：
  - **Laplacian-gated token merging（拉普拉斯门控令牌合并）**：利用拉普拉斯算子计算每个令牌的频谱平滑度，鼓励光谱平滑的令牌合并，阻止高对比度令牌合并，从而保留边缘和纹理细节。
  - **Interpolate–Extrapolate KV Downsampling（插值-外推 KV 下采样）**：对注意力机制中的键/值（K/V）进行可控的下采样，结合最近邻插值和平均池化的插值-外推，保持查询（Q）不变，在不重新训练的情况下保留注意力精度。
- **流程说明**：将两个算子作为即插即用模块插入现有扩散模型，无需额外训练；在每次前向传播中自动执行频率感知的令牌合并和 KV 下采样，实现加速。

## 3. 实验设计
- **数据集**：ImageNet-1K、ImageNet-100、Oxford-IIIT Pets、COCO-2017。
- **骨干网络**：基于 DiT 和 U-Net 的扩散模型（如 Stable Diffusion 2.0）。
- **基准方法**：对比了现有的令牌压缩加速方法（如令牌合并、下采样等），但摘要中未给出具体方法名称；实验通过分类准确率（Accuracy）和生成质量（FID）两项指标衡量。
- **典型案例**：在 ImageNet-1K 上，使用 70% 令牌合并比的 Stable Diffusion 2.0，BiGain 提升分类准确率 **7.1%**，同时降低 FID **0.56**（相对改善 3.1%）。

## 4. 资源与算力
- 论文中**未明确说明**所使用的 GPU 型号、数量或训练时长。
- 由于 BiGain 是**免训练**（training-free）框架，仅需计算拉普拉斯算子和插值/池化操作，算力需求主要来自扩散模型本身的推理。文中未给出具体推理加速倍数或计算开销对比。

## 5. 实验数量与充分性
- **实验数量**：覆盖 4 个数据集、2 种骨干架构，并提供了典型指标（准确率、FID）的数值提升。文中提到“comprehensive analyses”表明进行了消融研究，分析了频谱保留平衡的影响。
- **充分性与公平性**：
  - 数据集多样性较高（自然图像、细粒度、多类别、通用目标检测场景）。
  - 对比了多种加速方法（未列出名称但隐含对比），且在同一加速比下比较指标。
  - 不足：未提供与多种最新加速方法的全面对比（如 ToMe、DeepCache 等），也未列出具体基线数值和推理时间节省。

## 6. 论文的主要结论与发现
- **核心发现**：平衡的频谱保留（高频细节 + 低频/中频语义）是扩散模型令牌压缩的可靠设计原则。
- **性能提升**：BiGain 在所有测试数据集和骨干网络上均能改善分类准确率，同时不损害（甚至提升）生成质量。
- **通用性**：首次实现加速扩散模型中生成与分类任务联合优化的框架，适用于双用途的部署系统。

## 7. 优点
- **免训练、即插即用**：无需重新训练或微调，可直接应用于预训练扩散模型。
- **频率感知的精细压缩**：从频域角度区分重要细节和冗余信息，避免传统合并导致的边缘模糊。
- **双任务兼顾**：在生成质量保持/提升的同时，显著提升分类准确率，突破了现有加速方法仅关注生成的上限。
- **理论直觉清晰**：通过拉普拉斯算子实现简单有效的频率分离，可解释性强。

## 8. 不足与局限
- **算力与速度量化缺失**：未报告推理加速的实际倍数、计算开销增加量，难以评估实际工程效率。
- **对比实验不够全面**：未列出具体基线方法名称和指标表格，缺乏与主流令牌压缩方法（如 ToMe、token pruning）的详细比较。
- **模型与任务覆盖有限**：仅在扩散模型（DiT/U-Net）上验证，未测试其他生成模型（如 GANs、自回归模型）；判别任务仅涉及分类，未扩展到语义分割、目标检测等。
- **消融分析细节不足**：虽然提到“comprehensive analyses”，但摘要中未给出消融实验的具体结果或对两个算子贡献度的定量分离。
- **潜在偏差风险**：在 ImageNet-1K 上的 7.1% 准确率提升可能依赖于特定数据集和加速比，泛化性需进一步验证。

（完）
