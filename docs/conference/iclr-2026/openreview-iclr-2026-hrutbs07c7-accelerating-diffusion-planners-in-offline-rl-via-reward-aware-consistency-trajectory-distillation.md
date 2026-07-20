---
title: Accelerating Diffusion Planners in Offline RL via Reward-Aware Consistency Trajectory Distillation
title_zh: 通过奖励感知一致性轨迹蒸馏加速离线RL中的扩散规划器
authors: "Xintong Duan, Yutong He, Fahim Tajwar, Ruslan Salakhutdinov, J Zico Kolter, Jeff Schneider"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=hRuTBS07C7"
tags: ["query:diff-accel"]
score: 6.0
evidence: 奖励感知的一致性轨迹蒸馏实现单步扩散规划
tldr: 针对离线强化学习中扩散规划器推理速度慢的问题，提出奖励感知的一致性轨迹蒸馏方法，通过解耦训练和无噪声奖励信号实现单步生成更高奖励的动作轨迹，显著加速推理。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散规划器推理速度慢，现有一致性方法需要复杂训练。
method: 提出奖励感知的一致性轨迹蒸馏，直接优化奖励实现单步采样。
result: 实现单步生成更高奖励的动作轨迹，推理加速显著。
conclusion: 该方法有效加速扩散规划器，可推广至其他决策任务。
---

## Abstract
Although diffusion models have achieved strong results in decision-making tasks, their slow inference speed remains a key limitation. While consistency models offer a potential solution, existing applications to decision-making either struggle with suboptimal demonstrations under behavior cloning or rely on complex concurrent training of multiple networks under the actor-critic framework. In this work, we propose a novel approach to consistency distillation for offline reinforcement learning that directly incorporates reward optimization into the distillation process. Our method achieves single-step sampling while generating higher-reward action trajectories through decoupled training and noise-free reward signals. Empirical evaluations on the Gym MuJoCo, FrankaKitchen, and long horizon planning benchmarks demonstrate that our approach can achieve a $9.7$% improvement over previous state-of-the-art while offering up to $142\times$ speedup over diffusion counterparts in inference time.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **研究动机**：扩散模型在决策任务（如离线强化学习中的规划器）中表现优异，但其推理速度极慢，严重限制了实际部署。现有加速方法（如一致性模型）在应用至决策任务时存在两个主要问题：
  - 在行为克隆框架下，只能复现次优演示，无法生成优于演示数据的行为；
  - 在 actor-critic 框架下，需要同时对多个网络进行复杂的并发训练，实现难度高。
- **整体含义**：论文旨在提出一种简便有效的方法，在保持扩散规划器质量的同时大幅加速推理，使其能够实时生成高奖励的动作轨迹，从而提升离线 RL 在实际场景中的可用性。

## 2. 论文提出的方法论

- **核心思想**：将奖励优化直接纳入一致性蒸馏过程，通过**奖励感知的一致性轨迹蒸馏**（Reward-Aware Consistency Trajectory Distillation）实现单步生成高奖励动作轨迹。
- **关键技术细节**：
  - **解耦训练**：将扩散模型的训练与一致性模型的蒸馏解耦，避免复杂的多网络并发训练。
  - **无噪声奖励信号**：在蒸馏过程中直接利用无噪声的真实奖励信号（而非带有扩散噪声的近似奖励）来引导一致性模型优化，使得模型能够学习生成更高奖励的轨迹。
- **公式/算法流程**（文字说明）：
  1. 首先预训练一个扩散规划器（作为 teacher 模型）。
  2. 构建一个一致性模型（作为 student 模型），该模型通过单步前向传播即可从噪声直接映射到动作轨迹。
  3. 在蒸馏阶段，同时最小化 student 输出与 teacher 多步扩散输出之间的分布差异，并最大化 student 输出对应的奖励（即加入奖励正则项）。
  4. 经过蒸馏后，student 模型可在推理时仅需一次前向传播即可生成完整动作轨迹。

## 3. 实验设计

- **使用的数据集/场景**：
  - **Gym MuJoCo**：包括 HalfCheetah、Hopper、Walker2d 等经典连续控制任务。
  - **FrankaKitchen**：机器人操作任务，涉及多阶段目标。
  - **长 horizon 规划基准**（long horizon planning benchmarks）：未具体说明名称，可能包括 D4RL 中的 Maze 或 AntMaze 等。
- **基准 (Benchmark)**：离线强化学习标准评估环境，使用 D4RL 数据集。
- **对比方法**：
  - 扩散规划器（baseline，多步采样）。
  - 之前的一致性模型应用于决策的最先进方法（如 Behavior Cloning 或 actor-critic 框架下的同类工作）。
  - 其他离线 RL 方法（如 CQL、IQL 等，但摘要中未明确，可能包含在实验中）。

## 4. 资源与算力

- 文中**未明确说明**使用的 GPU 型号、数量或训练时长。摘要和元数据中未提及相关细节。因此无法总结具体算力消耗。

## 5. 实验数量与充分性

- **实验数量**：至少覆盖了三个不同的任务域（MuJoCo、FrankaKitchen、长 horizon 规划），每个域可能包含多个子任务（如 MuJoCo 中三个环境）。但具体每组实验的重复次数、消融实验的组数未在提取内容中说明。
- **充分性评价**：
  - **优点**：跨不同难度和环境类型的任务进行验证，涵盖短 horizon 和长 horizon 场景，具有一定广度。
  - **不足**：缺少消融实验、超参数敏感性分析、以及与其他加速方法（如蒸馏变体、扩散步数削减）的对比细节。由于文中信息有限，无法判断是否存在统计显著性报告和随机种子重复。
  - **客观性**：摘要中声称推理加速高达 142 倍且性能提升 9.7%，但未提供方差或置信区间，需谨慎解读。

## 6. 论文的主要结论与发现

- 所提出的**奖励感知一致性轨迹蒸馏**能够实现**单步采样**，同时生成比原始扩散规划器及之前一致性方法**更高奖励的动作轨迹**。
- 在三个基准上，相较于之前最先进的一致性模型方法，奖励性能平均提升 **9.7%**。
- 推理速度相比多步扩散规划器获得 **高达 142 倍** 的加速（理论上单步 vs 多步）。
- 方法避免了复杂并发训练，具有较好的实用性和通用性。

## 7. 优点

- **方法新颖**：直接将奖励优化嵌入一致性蒸馏，简洁有效，解决了现有方法次优演示和复杂训练两大痛点。
- **推理加速显著**：142 倍加速使扩散规划器从离线训练走向实时部署成为可能。
- **性能提升**：不仅在加速上取胜，还能生成奖励更高的轨迹，实现了质量和速度的双赢。
- **解耦设计**：训练过程与 actor-critic 框架解耦，降低了实现难度。

## 8. 不足与局限

- **实验覆盖**：未涉及更多实际机器人任务（如真实机器人控制、自动驾驶等），泛化性有待验证。
- **奖励信号假设**：依赖离线数据集中的奖励（无噪声），但实际环境可能奖励函数不准确或稀疏，方法对此的鲁棒性未讨论。
- **消融缺失**：未明确分析奖励感知项的重要性，以及蒸馏过程中 teacher 质量的影响。
- **算力信息缺失**：未报告训练资源，导致可复现性和效率评估不完整。
- **偏差风险**：仅用单步采样可能影响轨迹的多样性或鲁棒性，文中未对此进行讨论。
- **应用限制**：方法适用于奖励可得的离线 RL 设置，不适用于在线或奖励未知的场景。

（完）
