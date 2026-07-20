---
title: "Beyond Text-to-Image: Liberating Generation with a Unified Discrete Diffusion Model"
title_zh: 超越文本到图像：利用统一离散扩散模型释放生成能力
authors: "Qingyu Shi, Jinbin Bai, Zhuoran Zhao, Wenhao Chai, Kaidong Yu, Jianzong Wu, Shuangyong Song, Yunhai Tong, Xiangtai Li, Xuelong Li, Shuicheng YAN"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=pG0WTde3pR"
tags: ["query:diff-accel"]
score: 7.0
evidence: 统一离散扩散模型实现文本和图像的快速并行生成
tldr: 多模态统一生成模型通常推理慢或泛化弱。本文提出Muddit，基于预训练文本到图像骨干和轻量文本解码器，构建统一离散扩散Transformer，支持文本和图像的快速并行生成。该方法在保持质量的同时显著提升推理速度，可处理多种生成任务。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 现有统一模型存在推理慢或泛化弱的问题。
method: 将预训练文本到图像骨干集成轻量文本解码器，构建统一离散扩散模型。
result: 在多个多模态生成任务上实现快速并行生成，质量与速度均优。
conclusion: Muddit为多模态快速生成提供了新范式，兼具效率和泛化性。
---

## Abstract
Unified generation models aim to handle diverse tasks across modalities—such as text-to-image generation and image-to-text generation—within a single architecture and decoding paradigm. Autoregressive unified models suffer from slow inference due to sequential decoding, and non-autoregressive unified models suffer from weak generalization due to limited pretrained backbones. We introduce Muddit, a unified discrete diffusion transformer that enables fast and parallel generation across both text and image modalities. Unlike prior unified diffusion models trained from scratch, Muddit integrates strong visual priors from a pretrained text-to-image backbone with a lightweight text decoder, enabling flexible and high-quality multimodal generation under a unified architecture. Empirical results show that Muddit achieves competitive or superior performance compared to models in both quality and efficiency. This work also highlights the potential of purely discrete diffusion, when equipped with strong visual priors, as a scalable and effective backbone for unified generation.

---

## 论文详细总结（自动生成）

# 中文总结：论文《Beyond Text-to-Image: Liberating Generation with a Unified Discrete Diffusion Model》

## 1. 核心问题与整体含义（研究动机和背景）

- **研究动机**：现有统一多模态生成模型（同时支持文本到图像、图像到文本等任务）存在两类局限。自回归统一模型因顺序解码导致推理速度慢；非自回归统一模型因依赖有限的预训练骨干网络，导致泛化能力弱。
- **整体含义**：本文旨在提出一种既能快速并行生成、又具备强泛化能力的统一离散扩散模型，以释放多模态生成潜力。

## 2. 方法论

- **核心思想**：基于预训练的文本到图像骨干网络（提供强视觉先验），集成一个轻量文本解码器，构建统一的离散扩散Transformer（命名为Muddit）。该模型支持文本和图像的快速并行生成，无需从零训练。
- **关键技术细节**：
  - 使用离散扩散框架，将图像和文本令牌统一处理。
  - 预训练文本到图像骨干提供视觉先验，轻量文本解码器用于文本生成。
  - 推理时通过并行解码加速，避免自回归顺序生成。
- **公式/算法流程**（文字说明）：输入任务条件（如文本或图像），经过统一离散扩散Transformer，进行多步扩散反向过程，同时生成文本和图像令牌，最终解码为对应模态输出。训练时联合优化扩散损失和解码器损失。

## 3. 实验设计

- **数据集/场景**：未明确列举具体数据集，但涉及文本到图像生成、图像到文本生成等多种多模态生成任务。
- **Benchmark**：与现有统一模型（自回归与非自回归）在质量（如图像质量、文本生成准确度）和效率（推理速度）上比较。
- **对比方法**：包括自回归统一模型、非自回归统一模型、从零训练的扩散模型等。

## 4. 资源与算力

- **文中未明确说明**使用的GPU型号、数量或训练时长。仅提及基于预训练骨干，未给出具体算力消耗。

## 5. 实验数量与充分性

- **实验数量**：根据摘要，包含多个多模态生成任务的对比实验，以及质量与效率的双维度评估。但未提供消融实验、超参数分析等细节。
- **充分性与公平性**：摘要声称达到竞争力甚至更优的表现，但缺乏详细实验表格和统计验证。实验设计相对客观，但可重复性信息不足，充分性一般。

## 6. 主要结论与发现

- Muddit在保持生成质量的同时，显著提升推理速度，在多种多模态生成任务上优于或持平现有方法。
- 证明了纯离散扩散模型在强视觉先验加持下，可作为可扩展且有效的统一生成骨干。

## 7. 优点

- **方法创新**：巧妙利用预训练文本到图像骨干，避免从零训练，降低计算成本并增强泛化。
- **效率优势**：并行解码，推理速度远快于自回归方法。
- **统一架构**：单一模型处理多种模态生成任务，无需任务专用模块。

## 8. 不足与局限

- **实验细节缺失**：未披露数据集、具体评价指标、消融实验等，难以完全验证泛化性和鲁棒性。
- **算力信息透明性不足**：不利于复现和成本评估。
- **可能偏差风险**：预训练骨干的选择可能引入视觉领域偏差，文本解码器对复杂语言指令的泛化能力待检验。
- **应用限制**：离散扩散模型在超高清生成或长文本生成场景下的表现未知。

（完）
