---
title: "Sparse-vDiT: Unleashing the Power of Sparse Attention to Accelerate Video Diffusion Transformers"
title_zh: Sparse-vDiT：利用稀疏注意力加速视频扩散Transformer
authors: "Pengtao Chen, Xianfang Zeng, Maosen Zhao, Mingzhu Shen, Wei Cheng, Gang Yu, Tao Chen"
date: 2026-03-17
pdf: "https://ojs.aaai.org/index.php/AAAI/article/download/37287/41249"
tags: ["query:diff-accel"]
score: 9.0
evidence: 稀疏注意力加速视频扩散Transformer
tldr: 视频扩散Transformer因注意力二次复杂度推理缓慢。Sparse-vDiT通过分析注意图，发现对角、多对角和竖条等稀疏模式，并据此跳过冗余注意力头。该框架无需训练，可显著加速视频生成，且质量下降可忽略。实验证明其在多个视频生成任务上取得加速比提升。
source: AAAI-2026-Accepted
selection_source: conference_retrieval
figures_json: "[{\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37287/fig-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 876, \"height\": 622, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37287/fig-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 1481, \"height\": 482, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37287/fig-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 1460, \"height\": 431, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37287/fig-004.webp\", \"caption\": \"\", \"page\": 0, \"index\": 4, \"width\": 1566, \"height\": 463, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37287/fig-005.webp\", \"caption\": \"\", \"page\": 0, \"index\": 5, \"width\": 1555, \"height\": 699, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37287/fig-006.webp\", \"caption\": \"\", \"page\": 0, \"index\": 6, \"width\": 1733, \"height\": 670, \"label\": \"Figure\"}]"
tables_json: "[{\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-37287/table-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 772, \"height\": 459, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-37287/table-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 1815, \"height\": 868, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-37287/table-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 885, \"height\": 389, \"label\": \"Table\"}]"
motivation: 视频扩散Transformer中注意力机制导致推理延迟大。
method: 识别注意力稀疏模式，设计模式优化的稀疏注意力框架跳过冗余头。
result: 在保持生成质量的前提下显著加速视频生成。
conclusion: 利用固有稀疏性可有效加速视频扩散模型。
---

## Abstract
While Diffusion Transformers (DiTs) have achieved breakthroughs in video generation, this long sequence generation task remains constrained by the quadratic complexity of attention mechanisms, resulting in significant inference latency. Through detailed analysis of attention maps in Video Diffusion Transformer (vDiT), we identify three recurring sparsity patterns: diagonal, multi-diagonal, and vertical-stripe structures.   And even 3-6% attention heads can be skipped. Crucially, these patterns exhibit strong layer-depth and head-position correlations but show limited dependence on the input content. Leveraging these findings, we propose Sparse-vDiT, a sparsity acceleration framework for vDiT comprising: 1) Pattern-optimized sparse kernels that replace dense attention with computationally efficient implementations for each identified sparsity pattern. 2) An offline sparse diffusion search algorithm that selects the optimal sparse computation strategy per layer and head via hardware-aware cost modeling. After determining the optimal configuration, we fuse heads within the same layer that share the same attention strategy, enhancing inference efficiency. Integrated into state-of-the-art vDiT models (CogVideoX1.5, HunyuanVideo, and Wan2.1), Sparse-vDiT achieves 2.09×, 2.38×, and 1.67× theoretical FLOP reduction, and actual inference speedups of 1.76×, 1.85×, and 1.58×, respectively, while maintaining high visual fidelity, with PSNR values reaching 24.13, 27.09, and 22.59. Our work demonstrates that latent structural sparsity in vDiTs can be systematically exploited for long video synthesis.

---

## 论文详细总结（自动生成）

# Sparse-vDiT: 中文总结

## 1. 核心问题与整体含义（研究动机和背景）

视频扩散Transformer（vDiT）虽在视频生成中取得突破，但由于注意力机制的二次复杂度，长序列生成任务面临巨大的推理延迟。例如，HunyuanVideo生成5秒720p视频需约50分钟，其中注意力模块占延迟的77%~81%。作者通过分析注意力图，发现vDiT存在结构化的稀疏模式（对角、多对角、竖条），且部分注意力头可跳过而不显著影响质量。因此，论文提出Sparse-vDiT，利用这些固定稀疏性加速推理，在保持视觉保真度的同时显著提升速度。

## 2. 方法论

- **核心思想**：利用vDiT中注意力头冗余和注意力图的固定稀疏模式，结合头跳过和模式优化的稀疏核，通过离线搜索为每个头选择最佳计算策略，再对同一层内相同模式的头进行融合，实现加速。

- **关键技术细节**：
  1. **稀疏模式识别**：分析注意力图，发现四种模式：全注意力、对角稀疏、多对角稀疏、竖条稀疏。
  2. **头跳过**：直接跳过部分贡献极小的头，输出置零。
  3. **模式优化的稀疏核**：针对每种稀疏模式设计专用核（如窗口注意力、重排）。
  4. **离线稀疏扩散搜索**：对每个层的每个头，使用少量样本计算M0~M4五种模式的输出（全注意力、跳过、三种稀疏），通过MSE距离和稀疏惩罚项选择最优模式。公式：$L_i = MSE(O_i - O_0) + \lambda \times (1 - S_i)$，若所有$L_i$超过阈值$\epsilon$则保留全注意力，否则选择损失最小的模式。
  5. **头融合**：搜索完成后，同一层内相同模式的头被融合，减少kernel launch开销。

## 3. 实验设计

- **数据集/场景**：文本到视频生成。使用GPT增强的VBench prompts评估CogVideoX1.5和Wan2.1；使用Penguin Video Benchmark prompts评估HunyuanVideo。
- **基准模型**：CogVideoX1.5（81帧，1360×768）、HunyuanVideo（129帧，1280×720）、Wan2.1（81帧，1280×720）。
- **对比方法**：MInference、WinAttn（空间/时间窗口）、PAB（仅CogVideoX）、SVG（当前SOTA稀疏加速vDiT方法）。分别对比重建保真度（PSNR、SSIM、LPIPS）、视觉质量（ImageQual）、时间一致性（SubConsist）、计算效率（PFLOPS、延迟、加速比）。

## 4. 资源与算力

- CogVideoX1.5和HunyuanVideo使用单张A800 GPU推理，Wan2.1使用单张H800 GPU，batch size=1。
- 论文未提及训练/搜索的具体时长和GPU数量；仅说明离线搜索需少量样本（未给出精确数量）。

## 5. 实验数量与充分性

实验较为充分：
- 主要结果（表2）在三个主流vDiT模型上对比了五种基线方法，涵盖重建、视觉质量、时间一致性、效率共7个指标。
- 消融实验（表3）仅针对CogVideoX1.5，探索了$\lambda$和$\epsilon$两个超参数对质量-效率权衡的影响，验证了方法鲁棒性。
- 提供了可视化对比（图6）和t-SNE分析（图4），证明注意力模式与层深强相关、与输入无关。
- 总体公平客观：所有基线使用官方代码和默认设置，PAB由于不支持Hunyuan/Wan而被排除；WinAttn窗口大小与SVG一致。

## 6. 主要结论与发现

- vDiT注意力头存在冗余，可跳过3%~6%而质量可接受。
- 注意力图存在三种固定稀疏模式（对角、多对角、竖条），且与层深度和头位置强相关，与输入文本无关。
- Sparse-vDiT在CogVideoX1.5上实现2.09×理论FLOPs降低和1.76×实际加速，PSNR 24.13；HunyuanVideo上2.38× FLOPs降低和1.85×加速，PSNR 27.09；Wan2.1上1.67× FLOPs降低和1.58×加速，PSNR 22.59。
- 在所有指标上优于或持平现有稀疏加速方法，尤其重建保真度和时间一致性表现突出。

## 7. 优点

- **新颖性**：首次systematic利用vDiT注意力的固定结构稀疏性进行加速，提出头跳过与模式化稀疏核结合。
- **实用性**：离线搜索仅需少量样本，搜索后模式固定，可直接在实际推理中应用，无需训练或在线调整。
- **高效性**：头部融合进一步减少kernel开销，实际加速接近理论值。
- **通用性**：在三个不同规模、不同帧数的vDiT模型上均表现有效，且与现有缓存/蒸馏方法正交。

## 8. 不足与局限

- **实验覆盖**：消融实验仅基于CogVideoX1.5，未在HunyuanVideo和Wan2.1上做同样的$\lambda,\epsilon$消融，可能导致泛化性未充分验证。
- **偏差风险**：搜索仅依赖MSE与稀疏惩罚，可能忽略感知质量差异；阈值$\epsilon$需人工调整以平衡速度和质量，缺乏自适应机制。
- **应用限制**：仅针对特定vDiT架构（MM-DiT范式），若模型注意力模式不同（如纯时空分离注意力），可能需重新搜索或设计新核。此外，方法依赖离线预定义模式，无法处理动态稀疏性。
- **资源开销**：虽然搜索快速，但仍需额外的前向推理计算稀疏模式输出，对超大模型可能仍需一定算力。

（完）
