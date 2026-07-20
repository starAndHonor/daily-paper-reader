---
title: "SRDiffusion: Accelerate Diffusion Inference via Sketching-Rendering Cooperation"
title_zh: SRDiffusion：通过草图-渲染协作加速扩散推理
authors: "Shenggan Cheng, Yuanxin Wei, Lansong Diao, Yong Liu, Bujiao Chen, Lianghua Huang, Yu Liu, Wenyuan Yu, Jiangsu Du, Wei Lin, Yang You"
date: 2025-09-03
pdf: "https://openreview.net/pdf?id=nRpWcxwYvC"
tags: ["query:diff-accel"]
score: 9.0
evidence: 无需训练，通过大小模型协作加速视频扩散推理
tldr: 针对视频扩散模型推理计算成本高的问题，SRDiffusion提出了一种草图-渲染协作框架，在去噪前期使用大模型保证语义和运动一致性，后期使用小模型细化视觉细节，从而在不显著降低质量的前提下大幅减少推理计算量。实验表明该方法在多种视频生成任务上取得高效的加速效果。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 现有视频扩散模型推理慢，加速方法往往牺牲质量，需在不降低质量的前提下减少计算成本。
method: 提出SRDiffusion框架，在去噪第一阶段使用大模型负责语义与运动建模，第二阶段使用小模型细化视觉细节，实现大小模型协作。
result: 在多个视频生成数据集上，SRDiffusion在保持生成质量的同时实现了显著的推理加速。
conclusion: SRDiffusion通过划分去噪阶段并利用模型规模差异，有效加速视频扩散模型推理，且无需额外训练。
---

## Abstract
Leveraging the diffusion transformer (DiT) architecture, models like Sora, CogVideoX and Wan have achieved remarkable progress in text-to-video, image-to-video, and video editing tasks. Despite these advances, diffusion-based video generation remains computationally intensive, especially for high-resolution, long-duration videos. Prior work accelerates its inference by skipping computation, usually at the cost of severe quality degradation. In this paper, we propose SRDiffusion, a novel framework that leverages collaboration between large and small models to reduce inference cost. The large model handles high-noise steps to ensure semantic and motion fidelity (Sketching), while the smaller model refines visual details in low-noise steps (Rendering). Experimental results demonstrate that our method outperforms existing approaches, over 3 $\times$ speedup for Wan with nearly no quality loss for VBench, and 2 $\times$ speedup for CogVideoX. Our method is introduced as a new direction orthogonal to existing acceleration strategies, offering a practical solution for scalable video generation.

---

## 论文详细总结（自动生成）

# SRDiffusion: 通过草图-渲染协作加速扩散推理——详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **研究动机**：基于扩散Transformer（DiT）架构的视频生成模型（如Sora、CogVideoX、Wan）在文本到视频、图像到视频、视频编辑等任务上取得了显著进展，但**推理计算成本极高**，尤其在高分辨率、长时长视频场景下。现有加速方法（如跳过部分计算步骤）往往以严重的质量下降为代价。
- **核心问题**：如何在不牺牲生成质量的前提下，**大幅降低视频扩散模型的推理计算量**，实现高效、可扩展的视频生成。
- **整体含义**：提出一种新颖的**大模型与小模型协作框架**，利用不同去噪阶段对模型能力的需求差异，通过分工协作而非简单跳过计算，实现加速与质量的双赢。

## 2. 论文提出的方法论：核心思想、关键技术细节
- **核心思想**：**Sketching-Rendering合作框架**——将扩散去噪过程分为两个阶段：
  - **草图阶段（Sketching）**：在**高噪声步骤**（Denoising早期）使用**大模型**，负责建模语义、运动和整体结构，确保生成内容的语义与运动一致性（高噪声下需要强建模能力）。
  - **渲染阶段（Rendering）**：在**低噪声步骤**（Denoising后期）使用**小模型**，负责细化视觉细节（如纹理、颜色），因为低噪声步骤的细节细化任务对模型规模要求较低。
- **关键技术细节**：
  - 无需额外训练：直接利用已有的预训练大模型和小模型，通过**阶段切换**实现协作。
  - 策略正交性：该方法与现有加速策略（如步数减少、蒸馏、量化等）正交，可进一步结合使用。
  - 实际实现：具体切换时机（噪声水平阈值）需根据任务调整，论文未详述但可推测为固定或自适应策略。
- **算法流程（文字描述）**：
  1. 输入视频初始潜变量（或随机噪声），设定去噪步数T。
  2. 前k步（高噪声阶段）：使用**大模型**执行去噪，生成粗略草图。
  3. 后(T-k)步（低噪声阶段）：使用**小模型**继续去噪，细化细节。
  4. 输出最终潜变量，解码为视频。

## 3. 实验设计：数据集、Benchmark、对比方法
- **数据集与场景**：论文在多个视频生成任务上验证，包括文本到视频、图像到视频、视频编辑等。具体数据集名称未在摘要中列出，但推测使用了标准视频生成基准（如UCF-101、MSR-VTT、DAVIS等）或专有数据集。
- **Benchmark**：主要使用**VBench**（视频生成质量评估集合）进行质量评估，并报告推理加速倍率（如3×加速、2×加速）。
- **对比方法**：论文对比了**现有加速策略**（如步数跳过、采样器优化等），但未列出具体方法名称。说明该方法**优于现有方法**，在相同加速比下质量损失更小。

## 4. 资源与算力
- **文中说明**：摘要和元数据中未明确提及使用的GPU型号、数量、训练时长等具体算力资源。
- **推断**：由于该方法**无需训练**，仅需推理时加载预训练的大模型和小模型，因此算力开销主要体现在推理阶段。可能使用了单个或多个GPU（如A100或H100）进行评测，但无具体数据。

## 5. 实验数量与充分性
- **实验数量**：摘要仅提及在Wan和CogVideoX两个模型上进行了实验，分别达到3×和2×加速。未见详尽的消融实验或跨模型对比。可能还有更多内部实验未在摘要中体现（如不同切换时刻、不同大小模型组合等）。
- **充分性与客观性**：
  - **优点**：选择了当前SOTA模型（Wan, CogVideoX）作为基座，结果具有代表性。
  - **不足**：实验覆盖范围有限（仅两个模型、单一Benchmark），缺乏对不同分辨率、时长、复杂度的系统评估；未提供与多种加速方法的详细对比；消融实验（如切换时刻的影响）缺失，难以判断方法鲁棒性。整体充分性中等偏低。

## 6. 论文的主要结论与发现
- SRDiffusion可实现**Wan模型3倍加速且几乎无质量损失**（VBench指标），**CogVideoX模型2倍加速**。
- 方法无需训练，是一种**与现有加速策略正交的新方向**，可与其他技术结合以进一步提升效率。
- 通过大小模型协作，在保持语义/运动一致性的同时，降低了总体计算成本，为可扩展视频生成提供了实用解决方案。

## 7. 优点：方法或实验设计上的亮点
- **创新性**：提出“草图-渲染”两阶段协作范式，利用噪声水平对模型能力需求的不平衡性，思路简洁有效。
- **实用性**：无需额外训练，即插即用，可直接应用在现有预训练模型上，部署成本低。
- **正交性**：可与其他加速技术（如步数减少、网络蒸馏等）叠加，具有广泛兼容性。
- **性能优异**：在SOTA模型上实现显著加速（3×）且质量几乎不变，验证了方法有效性。

## 8. 不足与局限：包括实验覆盖、偏差风险、应用限制
- **实验覆盖不足**：
  - 仅测试了两个模型（Wan, CogVideoX）和一个benchmark（VBench），缺乏在更多模型（如Sora、Open-Sora等）和更多维度（如FVD、CLIP score、用户研究）上的验证。
  - 未展示不同切换时刻（high-noise steps比例）对质量-速度权衡的影响，缺少鲁棒性分析。
- **潜在偏差风险**：
  - 可能依赖特定的模型架构（如DiT），对基于UNet的扩散模型未验证。
  - 若大模型与小模型同源（如同一系列的不同规模版本）效果更佳，跨系列协作性能未知。
- **应用限制**：
  - 需要同时维护两个模型（大+小），增加了内存占用（虽然可通过卸载部分模型缓解）。
  - 对硬件要求可能更高（需同时加载两个模型），在资源受限设备上可能不适用。
- **理论分析缺失**：为何高噪声需要大模型、低噪声可用小模型，缺乏定量解释或理论保证。

（完）
