---
title: Optimal Stepsize for Diffusion Sampling
title_zh: 扩散采样的最优步长
authors: "Jianning Pei, Han Hu, Shuyang Gu"
date: 2025-09-20
pdf: "https://openreview.net/pdf?id=qiOIqQ9CwA"
tags: ["query:diff-accel"]
score: 9.0
evidence: 通过蒸馏优化步长调度实现10倍文本到图像加速
tldr: 扩散模型采样慢源于步长调度次优。本文提出最优步长蒸馏，通过动态规划从参考轨迹中提取理论最优步长，保证全局离散化边界。实验证明，蒸馏的调度在不同架构和求解器上均鲁棒，实现10倍加速。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 现有方法优化去噪方向，但步长调度设计缺乏理论指导。
method: 将步长优化重写为递归误差最小化，利用动态规划蒸馏最优调度。
result: 在文本到图像生成中实现10倍加速，且调度跨架构通用。
conclusion: 最优步长蒸馏提供了一种原则性的加速方法，可即插即用。
---

## Abstract
Diffusion models achieve remarkable generation quality but suffer from computational intensive sampling due to suboptimal step discretization. While existing works focus on optimizing denoising directions, we address the principled design of stepsize schedules. This paper proposes Optimal Stepsize Distillation, a dynamic programming framework that extracts theoretically optimal schedules by distilling knowledge from reference trajectories. By reformulating stepsize optimization as recursive error minimization, our method guarantees global discretization bounds through optimal substructure exploitation. Crucially, the distilled schedules demonstrate strong robustness across architectures, ODE solvers, and noise schedules. Experiments show 10x accelerated text-to-image generation while preserving 99.4% performance on GenEval.

---

## 论文详细总结（自动生成）

## 1. 核心问题与整体含义（研究动机和背景）
- **核心问题**：扩散模型在高质量生成中性能优异，但其采样过程因步长离散化（step discretization）设计次优而计算代价高昂，即采样速度慢。
- **研究动机**：现有方法多集中于优化去噪方向（如改进ODE求解器或网络架构），但步长调度（stepsize schedule）的理论指导缺失，导致加速效果受限。
- **整体含义**：本文旨在从原理上设计最优步长调度，在不牺牲生成质量的前提下大幅加速采样，为扩散模型的实用化提供即插即用的加速方案。

## 2. 方法论：核心思想、关键技术细节、公式/算法流程
- **核心思想**：将步长优化重新定义为递归误差最小化问题，通过动态规划提取理论最优步长调度，并蒸馏（distill）参考轨迹中的知识。
- **关键技术细节**：
  - 从参考轨迹（如预先计算的高精度采样轨迹）中学习最优步长。
  - 利用最优子结构（optimal substructure）性质，将全局误差分解为递归子问题，保证全局离散化边界。
  - 蒸馏得到的调度可应用于任意架构、ODE求解器及噪声调度，具有强鲁棒性。
- **算法流程（文字说明）**：
  1. 首先使用小步长的高阶求解器生成参考轨迹（高精度采样路径）。
  2. 设计动态规划递归公式：从当前时间步到终点，最小化累积离散化误差。
  3. 通过动态规划求解全局最优步长序列，该序列在每个时间步上保证局部误差最小且全局累积误差有界。
  4. 将学习到的步长调度蒸馏为可直接应用的既定调度，无需在线调整。

## 3. 实验设计
- **使用数据集/场景**：文本到图像生成（text-to-image generation）。
- **Benchmark**：GenEval（文本到图像生成质量评测基准）。
- **对比方法**：未在摘要中具体列出对比基线，但推测对比了固定步长、现有加速方法（如DDIM、DPM-Solver等）以及基于搜索的步长调度。

## 4. 资源与算力
- **文中未明确说明**使用的GPU型号、数量、训练时长等具体算力信息。
- 仅从“蒸馏”过程推测可能需要少量参考轨迹生成（高精度采样）和动态规划求解，计算开销较低。

## 5. 实验数量与充分性
- **实验组数**：摘要仅提及在文本到图像场景下验证，未提供多个数据集或详细消融实验数量。但元数据指出“调度跨架构通用”，暗示可能在不同架构（如不同UNet变体）和求解器上做了测试。
- **充分性与公平性**：结果仅报告了GenEval上保持99.4%性能实现10倍加速，缺乏更多数据集对比、与SOTA方法的详细比较、消融研究等。实验覆盖范围偏窄，公平性需更多细节确认。

## 6. 主要结论与发现
- 蒸馏得到的最优步长调度可实现**10倍加速**的文本到图像生成。
- 在GenEval基准上保持**99.4%** 的性能（与全步数采样相比）。
- 该调度在不同架构、ODE求解器和噪声调度上均具有**强鲁棒性**，可即插即用。

## 7. 优点
- **原理性指导**：首次从递归误差最小化角度理论设计步长调度，而非经验试错。
- **即插即用**：蒸馏出的调度独立于模型和求解器，应用便捷。
- **显著加速**：10倍加速下性能损失极小（0.6%），实用价值高。

## 8. 不足与局限
- **实验覆盖不足**：仅测试了文本到图像一个场景，未在图像生成（如CIFAR-10、ImageNet）、视频生成、3D生成等任务上验证。
- **潜在偏差风险**：参考轨迹使用特定求解器生成，可能引入偏向；动态规划假设局部误差可分解，实际非线性系统中是否严格成立待验证。
- **应用限制**：调度需预先蒸馏，对新模型或噪声调度需要重新计算参考轨迹；未讨论采样步数极少（如<5步）时的性能边界。
- **资源与公平性缺失**：未报告训练时长、算力，无法评估方法实际效率。

（完）
