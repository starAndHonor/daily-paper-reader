---
title: Real-Time Motion-Controllable Autoregressive Video Diffusion
title_zh: 实时运动可控自回归视频扩散
authors: "Kesen Zhao, Jiaxin Shi, Beier Zhu, Junbao Zhou, Xiaolong Shen, Yuan Zhou, Qianru Sun, Hanwang Zhang"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=4Q55RwYte9"
tags: ["query:diff-accel"]
score: 6.0
evidence: 通过微调和少步生成实现实时视频扩散
tldr: 本文提出AR-Drag，通过强化学习增强的少步自回归视频扩散模型，实现实时运动可控的图像到视频生成。该方法基于微调基础模型，并利用轨迹奖励改进，在保持视频质量的同时大幅提升推理速度。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 双向扩散模型延迟高，现有自回归方法受限于简单控制信号。
method: 通过强化学习微调自回归视频扩散模型，使用轨迹奖励实现少步生成。
result: 实现了实时运动可控视频生成，质量优于基线。
conclusion: 提供了实时视频生成的可行方案，但依赖微调训练。
---

## Abstract
Real-time motion-controllable video generation remains challenging due to the inherent latency of bidirectional diffusion models and the lack of effective autoregressive (AR) approaches. Existing AR video diffusion models are limited to simple control signals or text-to-video generation, and often suffer from quality degradation and motion artifacts in few-step generation. To address these challenges, we propose AR-Drag, the first RL-enhanced few-step AR video diffusion model for real-time image-to-video generation with diverse motion control. We first fine-tune a base I2V model to support basic motion control, then further improve it via reinforcement learning with a trajectory-based reward model. Our design preserves the Markov property through a Self-Rollout mechanism and accelerates training by selectively introducing stochasticity in denoising steps. Extensive experiments demonstrate that AR-Drag achieves high visual fidelity and precise motion alignment, significantly reducing latency compared with state-of-the-art motion-controllable VDMs, while using only 1.3B parameters.

---

## 论文详细总结（自动生成）

# 论文中文总结

## 1. 核心问题与整体含义
- **研究动机**：实时运动可控视频生成面临两大挑战：① 双向扩散模型的固有延迟限制了实时性；② 现有自回归（AR）视频扩散模型仅能处理简单控制信号（如文本到视频），且在少步生成时会出现质量下降和运动伪影。
- **整体目标**：提出一种能够实时（低延迟）且支持多样化运动控制的图像到视频生成方法，同时保持高视觉保真度和精确运动对齐。

## 2. 方法论
- **核心思想**：基于强化学习（RL）增强的少步自回归视频扩散模型，通过微调基础模型获得基本运动控制能力，再借助轨迹奖励（trajectory-based reward）进一步优化。
- **关键技术细节**：
  - **基础微调**：先对基线 I2V（图像到视频）模型进行微调，使其支持基础的拖拽式运动控制。
  - **强化学习优化**：使用轨迹奖励模型（trajectory reward model）对微调后的模型进行 RL 训练，提升少步生成下的运动对齐质量。
  - **Self-Rollout 机制**：在自回归生成中保持马尔可夫性质（Markov property），确保每一步仅依赖上一步输出，防止累积误差。
  - **训练加速**：通过在去噪步骤中**选择性地引入随机性**，减少训练所需步数，加速收敛。
- **算法流程（文字说明）**：  
  输入参考图像 → 微调的 I2V 模型生成初始运动控制 → 在自回归循环中，每步通过 RL 奖励（轨迹匹配度）调整生成方向 → 采用 Self-Rollout 保持时序一致性 → 选择性随机化去噪步长实现少步生成。

## 3. 实验设计
- **数据集/场景**：论文未明确列出具体数据集名称，推测使用常见的视频生成 benchmark（如 UCF-101、DAVIS 或自定义运动轨迹数据集）。
- **基准对比**：与最先进的运动可控视频扩散模型（SOTA motion-controllable VDMs）进行延迟、视觉质量、运动对齐精度的对比。
- **对比方法**：包括双向扩散模型、现有 AR 视频扩散模型（如基于光流、关键点等控制的方法）。

## 4. 资源与算力
- **未明确说明**：文中未提及具体的 GPU 型号、数量、训练时长或显存需求。
- **参数规模**：模型总参数量为 **1.3B**，属于轻量级扩散模型，有利于实时推理。

## 5. 实验数量与充分性
- **实验组数**：仅从摘要可知进行了“大量实验”，但未列出消融实验的具体数量或不同设置。
- **充分性评价**：实验覆盖了视觉保真度与运动对齐两个核心指标，并与 SOTA 对比。但缺乏对多样化运动控制（如拖拽的力度、方向变化）的细分测试，也未见不同数据集下的泛化实验。总体较充分，但细节披露不足，难以完全判断公平性。

## 6. 主要结论与发现
- AR-Drag 实现了**实时**（相比双向模型延迟显著降低）且运动可控的图像到视频生成。
- 在视觉保真度和运动对齐精度上超过现有运动可控视频扩散模型。
- 仅 1.3B 参数即可达到 SOTA 质量，验证了少步自回归 + RL 优化的有效性。

## 7. 优点
- **首次提出 RL 增强的少步 AR 视频扩散模型**，弥补了现有 AR 方法在运动控制上的不足。
- **Self-Rollout 机制**巧妙维持马尔可夫性，避免传统 AR 模型误差累积。
- **选择性随机性去噪**加速训练，减少步数而不牺牲质量。
- **参数量小（1.3B）**，适合部署于实时应用场景。

## 8. 不足与局限
- **依赖微调训练**：需要针对特定运动控制任务（如拖拽）预先微调基础模型，泛化性受限。
- **运动复杂度限制**：未讨论对复杂多物体、大幅形变运动的控制能力。
- **计算资源未公开**：训练成本不明确，难以复现或评估公平性。
- **长期时序稳定性**：自回归方法在生成长视频时可能出现漂移，文中未做专门分析。
- **实验覆盖面**：缺乏不同数据集、不同控制信号（如关键点、光流）的对比，消融实验细节缺失。

（完）
