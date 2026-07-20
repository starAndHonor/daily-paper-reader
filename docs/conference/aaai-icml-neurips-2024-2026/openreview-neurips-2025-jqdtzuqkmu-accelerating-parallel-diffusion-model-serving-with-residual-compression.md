---
title: Accelerating Parallel Diffusion Model Serving with Residual Compression
title_zh: 基于残差压缩的并行扩散模型服务加速
authors: "Jiajun Luo, Yicheng Xiao, Jianru Xu, Yangxiu You, Rongwei Lu, Chen Tang, Jingyan Jiang, Zhi Wang"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=jqDtzUQkmu"
tags: ["query:diff-accel"]
score: 6.0
evidence: 通过残差压缩加速并行扩散模型服务
tldr: 并行扩散模型推理中激活值交换带来巨大通信开销。CompactFusion利用相邻步骤的强时间冗余，对激活值进行残差压缩，显著减少通信量。实验证明在保持生成质量的同时大幅提升并行推理效率。该方法聚焦于服务加速，但涉及硬件通信，不完全符合免训练硬件无关加速要求。
source: NeurIPS-2025-Accepted
selection_source: conference_retrieval
motivation: 扩散模型并行推理中通信开销大，限制可扩展性。
method: 利用激活值时间冗余进行残差压缩，减少设备间通信。
result: 在保持生成质量下显著降低通信量，提升并行推理效率。
conclusion: 残差压缩可有效加速并行扩散模型服务，但引入硬件依赖。
---

## Abstract
Diffusion models produce realistic images and videos but require substantial computational resources, necessitating multi-accelerator parallelism for real-time deployment. However, parallel inference introduces significant communication overhead from exchanging large activations between devices, limiting efficiency and scalability. We present CompactFusion, a compression framework that significantly reduces communication while preserving generation quality. Our key observation is that diffusion activations exhibit strong temporal redundancy—adjacent steps produce highly similar activations, saturating bandwidth with near-duplicate data carrying little new information. To address this inefficiency, we seek a more compact representation that encodes only the essential information. CompactFusion achieves this via Residual Compression that transmits only compressed residuals (step-wise activation differences). Based on empirical analysis and theoretical justification, we show that it effectively removes redundant data, enabling substantial data reduction while maintaining high fidelity. We also integrate lightweight error feedback to prevent error accumulation. CompactFusion establishes a new paradigm for parallel diffusion inference, delivering lower latency and significantly higher generation quality than prior methods. On 4$\times$L20, it achieves $3.0\times$ speedup while greatly improving fidelity. It also uniquely supports communication-heavy strategies like sequence parallelism on slow networks, achieving $6.7\times$ speedup over prior overlap-based method. CompactFusion applies broadly across diffusion models and parallel settings, and integrates easily without requiring pipeline rework. Portable implementation demonstrated on xDiT is publicly available at https://github.com/Cobalt-27/CompactFusion

---

## 论文详细总结（自动生成）

# 基于残差压缩的并行扩散模型服务加速——论文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **问题**：扩散模型（Diffusion Models）在生成高质量图像和视频时需要大量计算资源，实时部署必须依赖多加速器并行推理。然而，并行推理过程中设备间交换激活值（activations）会引入巨大的通信开销，严重限制系统的可扩展性和效率。
- **动机**：现有方法未能充分利用扩散模型推理步骤间的内在冗余，导致通信带宽被近似重复的数据饱和。因此，需要一种能够减少通信量同时保持生成质量的压缩框架。

## 2. 论文提出的方法论：CompactFusion

- **核心思想**：利用扩散模型相邻步骤激活值存在强时间冗余（temporal redundancy）的特点，仅传输压缩后的残差（即相邻步骤的激活差异）而非完整激活值，从而大幅减少通信量。
- **关键技术细节**：
  - **残差压缩（Residual Compression）**：将当前步骤的激活值与上一步骤的激活值的差异进行压缩编码，只传输残差信息，去除大量重复数据。
  - **错误反馈机制（Lightweight Error Feedback）**：为防止误差随时间累积，引入轻量级的错误反馈，保证生成质量。
  - 该方法不依赖特定的并行策略或流水线重排，可轻松集成到现有系统（如 xDiT）中。
- **公式/算法流程**（文字描述）：
  1. 在并行推理的每个时间步，设备计算得到当前激活值。
  2. 计算当前激活值与上一步激活值的残差（逐元素差值）。
  3. 对该残差进行压缩（例如量化、稀疏编码等，具体实现在论文中详细说明）。
  4. 将压缩后的残差传输给其他设备。
  5. 接收方解压缩残差并累加到上一步激活值上，恢复完整激活值。
  6. 通过错误反馈机制修正可能出现的累积误差。

## 3. 实验设计

- **数据集/场景**：未在摘要中明确列出具体数据集，但提及“diffusion models produce realistic images and videos”，推测测试了图像和视频生成任务。
- **基准（Benchmark）**：采用 4×L20 GPU 的并行推理环境，对比方法包括：
  - 传统的基于重叠（overlap-based）的方法。
  - 未压缩的直接交换激活值的并行基线。
- **对比方法**：主要对比了 prior overlap-based method 以及无压缩的并行方法。文中还提到 CompactFusion 在慢速网络上针对序列并行（sequence parallelism）实现了 6.7× 加速。

## 4. 资源与算力

- **GPU 型号与数量**：使用了 4 块 NVIDIA L20 GPU（4×L20）。
- **训练时长**：摘要中未明确说明模型训练耗时，仅关注推理服务加速。论文可能涉及模型推理层面的压缩，无需额外训练阶段，故未提及训练算力。
- **其他资源**：实验环境可能包括特定网络带宽设置（慢速网络测试序列并行），但具体参数未在此提供。

## 5. 实验数量与充分性

- **实验数量**：摘要仅给出关键结果：在 4×L20 上实现 3.0× 速度提升，在序列并行场景下相对于重叠方法实现 6.7× 加速。此外提到“broadly across diffusion models and parallel settings”，暗示进行了多组实验。
- **充分性评价**：由于摘要内容有限，无法看到消融实验、多数据集对比、不同压缩率测试等细节。但从已公开的信息看，实验对比了最直接的 baseline（重叠方法），并给出了加速比和保真度提升，初步证明了有效性。但缺少对不同压缩策略、错误反馈影响、以及更多模型（如不同大小、不同步数）的消融分析，充分性有待全文验证。

## 6. 论文的主要结论与发现

- **结论**：CompactFusion 通过残差压缩能够显著减少并行扩散模型推理中的通信量，同时保持甚至提升生成质量（fidelity）。
- **具体发现**：
  - 相邻时间步的激活值高度相似，残差包含的信息量远少于完整激活值。
  - 残差压缩配合错误反馈可以避免质量退化。
  - 方法适用性广，支持多种扩散模型和并行设置，易于集成。
  - 在 4×L20 GPU 上获得 3.0× 加速，在序列并行慢速网络上相比重叠方法获得 6.7× 加速。

## 7. 优点

- **方法创新**：首次利用扩散模型的时间冗余进行残差压缩，而非传统激活值直接传输或通用压缩。
- **高效实用**：无需修改流水线或并行策略，可即插即用至现有系统（如 xDiT）。
- **性能显著**：在加速比和生成质量上均优于先前方法，尤其适用于通信瓶颈严重的慢速网络。
- **理论支撑**：论文提供了经验分析和理论正当性（theoretical justification），保证方法的可靠性。

## 8. 不足与局限

- **实验覆盖有限**：摘要中仅给出 4×L20 单个硬件配置和单一对比基线，未展示在更多 GPU 数量、其他模型架构（如 Stable Diffusion, Sora 等）以及不同采样步数下的全面评测。
- **硬件依赖**：残差压缩可能涉及特定的硬件通信特性（如带宽、延迟），方法虽通用但效果依赖于实际网络环境，不完全满足免训练硬件无关加速要求（如用户提供的元数据评价）。
- **误差累积风险**：尽管引入了错误反馈，但在极长推理序列或极端压缩率下可能存在质量下降风险，论文未讨论边界情况。
- **未公开完整结果**：缺失消融实验（如不同压缩率、有无错误反馈的影响）、与更多 SOTA 通信优化方法的对比，使结论的一般性待验证。

（完）
