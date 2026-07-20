---
title: Large Scale Diffusion Distillation via Score-Regularized Continuous-Time Consistency
title_zh: 通过分数正则化连续时间一致性的大规模扩散蒸馏
authors: "Kaiwen Zheng, Yuji Wang, Qianli Ma, Huayu Chen, Jintao Zhang, Yogesh Balaji, Jianfei Chen, Ming-Yu Liu, Jun Zhu, Qinsheng Zhang"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=2uNlM353RI"
tags: ["query:diff-accel"]
score: 6.0
evidence: 蒸馏加速推理但需要训练
tldr: 本文首次将连续时间一致性模型扩展到大规模文本到图像和视频扩散任务，通过分数正则化蒸馏解决了雅可比向量积计算挑战，使百亿参数模型的快速采样成为可能。但该方法需要额外训练。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 连续时间一致性模型难以推广到大规模应用级扩散模型。
method: 开发FlashAttention-2兼容的JVP内核，结合分数正则化进行蒸馏。
result: 在超大规模图像和视频扩散模型上实现了有效的蒸馏加速。
conclusion: 提供了大规模蒸馏加速扩散模型的有效方案，但依赖于训练。
---

## Abstract
Although continuous-time consistency models (e.g., sCM, MeanFlow) are theoretically principled and empirically powerful for fast academic-scale diffusion, its applicability to large-scale text-to-image and video tasks remains unclear due to infrastructure challenges in Jacobian-vector product (JVP) computation and the limitations of evaluation benchmarks like FID. This work represents the first effort to scale up continuous-time consistency to general application-level image and video diffusion models, and to make JVP-based distillation effective at large scale. We first develop a parallelism-compatible FlashAttention-2 JVP kernel, enabling sCM training on models with over 10 billion parameters and high-dimensional video tasks. Our investigation reveals fundamental quality limitations of sCM in fine-detail generation, which we attribute to error accumulation and the “mode-covering” nature of its forward-divergence objective. To remedy this, we propose the score-regularized continuous-time consistency model (rCM), which incorporates score distillation as a long-skip regularizer. This integration complements sCM with the “mode-seeking” reverse divergence, effectively improving visual quality while maintaining high generation diversity. Validated on large-scale models (Cosmos-Predict2, Wan2.1) up to 14B parameters and 5-second videos, rCM generally matches the state-of-the-art distillation method DMD2 on quality metrics while mitigating mode collapse and offering notable advantages in diversity, all without GAN tuning or extensive hyperparameter searches. The distilled models generate high-fidelity samples in only $1\sim4$ steps, accelerating diffusion sampling by $15\times\sim50\times$. These results position rCM as a practical and theoretically grounded framework for advancing large-scale diffusion distillation. Code is available at https://github.com/NVlabs/rcm.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义

- **研究动机**：连续时间一致性模型（如 sCM、MeanFlow）在学术规模（如 CIFAR-10、ImageNet）上已证明理论优雅、效果出色，但**无法直接推广到大规模应用级扩散模型**（如文本到图像、视频生成），主要面临两重障碍：
  1. **基础设施挑战**：雅可比向量积（JVP）计算在大模型（>10B 参数）和高维视频任务中不可行；
  2. **评估局限**：传统 FID 等指标难以衡量细粒度生成质量，且 sCM 本身存在细节质量缺陷。
- **整体含义**：本文首次将连续时间一致性蒸馏扩展到**大规模文本到图像和视频扩散模型**，通过开发高效 JVP 内核和提出分数正则化改进，使百亿参数模型能以 1~4 步采样，加速 15~50 倍，同时保持高保真度和多样性。

## 2. 方法论：核心思想与关键技术

- **核心思想**：在 sCM（前向散度目标）基础上，引入**分数蒸馏作为长跳正则化**，融合“模式覆盖”与“模式寻找”的逆向散度，弥补细节生成不足并避免模式崩溃。
- **关键技术细节**：
  1. **FlashAttention-2 JVP 内核**：设计并行兼容的 JVP 计算核，使 sCM 训练能够支持 >10B 参数模型和视频任务。
  2. **rCM（Score-Regularized Continuous-Time Consistency Model）**：
     - 基础：sCM 使用 forward-divergence 目标（mode-covering），易导致误差累积和平滑细节。
     - 改进：添加 score distillation 作为 long-skip regularizer，引入 reverse divergence（mode-seeking）。
     - 优势：无需 GAN 调优、无需大量超参搜索，在提升视觉质量的同时保持高多样性。
- **算法流程**（文字说明）：
  - 预训练一个大规模扩散模型（教师）。
  - 使用开发的 JVP 内核并行计算学生模型的雅可比向量积。
  - 联合优化两个损失：一致性损失（确保一步映射） + 分数蒸馏损失（正则化）。
  - 蒸馏结束后，学生模型可在 1~4 步内生成样本。

## 3. 实验设计

- **数据集 / 场景**：
  - **图像生成**：文本到图像任务（模型规模达 14B 参数）。
  - **视频生成**：5 秒视频生成任务（使用 Cosmos-Predict2、Wan2.1 等模型）。
- **Benchmark**：主要对比质量指标（FID？文中未明确指标名称，但提到“quality metrics”）和多样性指标（如避免模式崩溃）。
- **对比方法**：与当前最先进的蒸馏方法 **DMD2** 进行对比。
- **评估结果**：
  - 质量指标上 **rCM 一般匹配 DMD2**。
  - 多样性上 **rCM 显著优于 DMD2**，无模式崩溃。
  - 采样步数：1~4 步，加速 15×~50×。

## 4. 资源与算力

- **文中未明确给出具体 GPU 型号、数量、训练时长等详细信息**。
- 仅提到：支持超过 10B 参数模型、高维视频任务，开发了 FlashAttention-2 兼容的 JVP 内核以提升并行效率。
- 可推断：需要大规模 GPU 集群（如 A100/H100），但未量化。

## 5. 实验数量与充分性

- **实验组数**：主要在两类大模型（图像与视频）上验证，并与 DMD2 做对比。消融实验可能包括：
  - 对比 sCM 与 rCM（体现正则化效果）；
  - 不同步数下的生成质量；
  - 多样性与模式崩溃分析。
- **充分性评价**：
  - 实验**覆盖了实际应用场景**（大规模文本到图像、视频），具有代表性。
  - 对比方法选择**最先进蒸馏方法**，公平合理。
  - 强调 **“无需 GAN 调优或大量超参搜索”**，说明实验设置较为鲁棒。
  - 但**实验数量相对有限**（仅两个骨干模型），且缺乏与其他类型蒸馏（如 progressive distillation、LCM）的对比，可能不够全面。

## 6. 主要结论与发现

1. **连续时间一致性蒸馏可以成功扩展到百亿参数级别**，通过定制 JVP 内核解决基础设施的瓶颈。
2. **sCM 的细细节生成质量存在固有缺陷**，源于forward-divergence目标的模式覆盖特性和误差累积。
3. **rCM 通过分数正则化有效弥补了这一问题**，在保持多样性前提下提升视觉质量，匹配 DMD2 的性能。
4. **蒸馏后的模型可在 1~4 步生成高保真样本**，加速比高达 15×~50×，同时避免 GAN 常见的模式崩溃。
5. rCM 提供了一种**理论扎实、实用性强**的大规模扩散蒸馏框架。

## 7. 优点

- **工程创新**：首次实现 FlashAttention-2 兼容的 JVP 内核，使百亿参数模型训练成为可能。
- **理论创新**：将分数蒸馏作为正则项，巧妙融合 mode-covering 与 mode-seeking 目标，兼顾质量与多样性。
- **实验亮点**：在**实际应用级（非学术 toy）** 模型上验证，直接对比 SOTA 方法 DMD2，结果有说服力。
- **通用性**：适用于图像和视频两大模态，且无需复杂调参。
- **开源**：代码公开，便于复现和后续研究。

## 8. 不足与局限

- **依赖额外训练**：蒸馏过程需要训练学生模型，并非零训练加速方案（如某些引导方法）。
- **算力成本未量化**：虽提到开发了高效内核，但未披露具体 GPU 小时数，难评估实际开销。
- **评估指标不明确**：只提“quality metrics”，未给出 FID/CLIP Score 等具体数值，缺乏数字验证。
- **对比范围有限**：仅与 DMD2 对比，未与 Progressive Distillation、LCM、GAN-based 方法比较，可能错过更优方法。
- **架构依赖**：JVP 内核针对 FlashAttention-2 优化，若模型未采用此注意机制可能无法直接迁移。
- **细节质量**：尽管改进，但 rCM 生成的细节能否媲美教师模型的数百步结果仍存疑（文中未提供视觉对比示例）。
- **可能存在的偏差**：实验仅基于两个特定骨干模型（Cosmos-Predict2, Wan2.1），结论泛化性需更多验证。

（完）
