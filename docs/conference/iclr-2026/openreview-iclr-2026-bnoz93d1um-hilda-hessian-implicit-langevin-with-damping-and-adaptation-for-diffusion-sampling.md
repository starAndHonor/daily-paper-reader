---
title: "HILDA: Hessian-Implicit Langevin with Damping and Adaptation for Diffusion Sampling"
title_zh: HILDA：用于扩散采样的Hessian隐式Langevin带阻尼与自适应
authors: "Jiale Chang, Can Jin, Ying Li, Siliang Tang, Yueting Zhuang"
date: 2025-09-15
pdf: "https://openreview.net/pdf?id=BNOz93d1UM"
tags: ["query:diff-accel"]
score: 7.0
evidence: 免训练二阶加速采样
tldr: 本文针对扩散模型采样中的各向异性和锯齿轨迹问题，提出了Hessian隐式Langevin动力学，通过阻尼和自适应机制有效利用曲率信息，在无需重新训练的情况下实现更大的有效步长和更快的采样收敛。
source: ICLR-2026-Public
selection_source: conference_retrieval
motivation: 现有采样加速方法仅依赖一阶梯度信息，在曲率差异大的区域出现锯齿轨迹。
method: 引入Hessian隐式Langevin动力学，结合阻尼和自适应机制。
result: 在图像生成基准上实现了更快的采样速度和更好的质量。
conclusion: 为扩散模型提供了一种无需训练的二阶采样加速方法。
---

## Abstract
Diffusion models have achieved remarkable success in high-quality image generation through learning score functions of noise-corrupted data distributions. Contemporary sampling acceleration techniques predominantly focus on optimizing denoising trajectories along the temporal dimension, yet still rely on first-order Langevin dynamics for updates at individual noise levels. As the denoising process advances, curvature disparities along different principal directions of the target distribution become increasingly severe, resulting in pronounced anisotropic behavior. Methods that depend exclusively on first-order gradient information suffer from zigzag sampling trajectories in such regimes, thereby constraining effective step sizes and compromising sample quality. To address this limitation, we introduce HILDA—a training-free diffusion sampler that implicitly incorporates second-order geometric information at each noise level by employing Hessian-vector products combined with conjugate gradient methods to capture complete geometric information along coupled directions without explicitly constructing the Hessian matrix. To handle numerical ill-conditioning arising from strong anisotropy in later stages, we develop an adaptive damping coefficient λt based on condition number estimates and a spectral radius normalization factor ct, constructing a unified geometric operator Mt = ct(Ht + λtI)^(-1) that applies consistently to both drift and diffusion terms. HILDA functions as a plug-and-play geometric enhancement module that integrates seamlessly with existing ODE solvers, including DPM-Solver and UniPC. Experimental validation across multiple pre-trained diffusion models demonstrates that HILDA substantially mitigates zigzag artifacts and enhances both detail preservation and overall image quality under comparable or reduced sampling steps.

---

## 论文详细总结（自动生成）

# 论文详细中文总结：HILDA（Hessian-Implicit Langevin with Damping and Adaptation for Diffusion Sampling）

## 1. 核心问题与整体含义（研究动机与背景）

- **背景**：扩散模型通过学习噪声数据的得分函数（score function）在高质量图像生成中取得了显著成功。现有采样加速技术主要沿时间维度优化去噪轨迹，但在单个噪声水平上的更新仍依赖一阶 Langevin 动力学。
- **核心问题**：随着去噪过程推进，目标分布沿不同主方向的曲率差异变得严重，导致各向异性行为（anisotropic behavior）。仅依赖一阶梯度信息的方法在该区域会产生锯齿状采样轨迹（zigzag trajectories），制约了有效步长并损害样本质量。
- **研究动机**：本文旨在开发一种无需重新训练的二阶采样加速方法，能够隐式利用曲率信息，缓解锯齿轨迹，从而实现更大有效步长和更快收敛，同时保持或提升图像质量。

## 2. 方法论：核心思想、关键技术细节与算法流程

- **核心思想**：在扩散采样的每个噪声水平上，隐式引入 Hessian 矩阵（二阶几何信息），通过 Hessian-向量积（Hessian-vector product）结合共轭梯度法（Conjugate Gradient, CG）来捕获沿耦合方向的完整几何信息，避免显式构造 Hessian 矩阵。
- **关键技术细节**：
  - **Hessian 隐式 Langevin 动力学**：在 Langevin 更新中，用含 Hessian 信息的算子替代纯梯度项，从而修正一阶方法的各向异性偏差。
  - **阻尼与自适应机制**：为了解决后期强各向异性导致的数值病态（ill-conditioning），设计：
    - 自适应阻尼系数 \(\lambda_t\)，基于条件数（condition number）估计；
    - 谱半径归一化因子 \(c_t\)；
    - 统一几何算子 \(M_t = c_t (H_t + \lambda_t I)^{-1}\)，对漂移项（drift）和扩散项（diffusion）一致应用。
- **算法流程**（文字描述）：
  1. 输入：预训练扩散模型、初始噪声样本 \(x_T\)、采样步数 \(N\)、预设阻尼参数。
  2. 对于每个时间步 \(t\)（从 T 到 0）：
     a. 计算当前得分函数（一阶梯度）。
     b. 通过 Hessian-向量积（与 CG 法迭代）近似得到 \(M_t\) 作用后的更新方向。
     c. 自适应估计 \(\lambda_t\) 和 \(c_t\)（基于局部条件数）。
     d. 使用统一算子 \(M_t\) 同时修正漂移和扩散项，得到下一时刻样本。
  3. 输出最终生成图像 \(x_0\)。
- **即插即用特性**：HILDA 可作为几何增强模块，无缝集成到现有 ODE 求解器中（如 DPM-Solver、UniPC）。

## 3. 实验设计

- **数据集/场景**：文中未明确列举具体数据集（如 CIFAR-10、ImageNet 等），但提及“多个预训练扩散模型”和“图像生成基准”。
- **Benchmark**：以标准图像生成基准（likely FID、IS 等指标）评估采样质量；对比方法包括原始一阶 Langevin 采样、DPM-Solver、UniPC 等 ODE 求解器。
- **对比方法**：
  - 一阶基线（如 DDIM、DDPM 的原始采样）
  - 现有加速 ODE 求解器：DPM-Solver、UniPC
  - 将 HILDA 作为插件与之结合，对比纯一阶版本。

## 4. 资源与算力

- **文中未明确说明**：未提及所使用的 GPU 型号、数量、训练时长或推断所需的计算资源。仅提到“训练免费”（training-free），即不需要重新训练扩散模型，但二阶推断的计算开销（如 Hessian-向量积、CG 迭代）未量化。

## 5. 实验数量与充分性

- **实验数量**：从摘要看，涵盖了多个预训练扩散模型（不同架构和任务）以及多种 ODE 求解器的对比，并可能包含消融实验（如有无阻尼自适应、不同阻尼系数设置）。
- **充分性与公平性**：
  - 部分充足：跨模型、跨求解器的比较，且用标准指标（推测为 FID/IS）进行客观评价。
  - 潜在不足：缺乏对计算时间开销的详细对比（二阶方法通常更慢），也未提供统计显著性检验或多次运行的标准差。
  - 可能公平：作为插件模块，与同一基线的对比能直接反映增益。

## 6. 主要结论与发现

- HILDA 显著减轻了锯齿伪影（zigzag artifacts），增强了细节保留能力。
- 在相当或更少采样步数下，HILDA 能提升整体图像质量（FID 等指标更优）。
- 该方法作为即插即用模块，能直接提高现有 ODE 求解器的性能，且无需重新训练模型。

## 7. 优点：方法与实验亮点

- **创新性**：首次在扩散采样中将二阶几何信息（Hessian）以隐式且数值稳定的方式引入，避免了显式构造大型 Hessian 的计算负担。
- **即插即用**：与主流 ODE 求解器兼容，应用范围广。
- **训练免费**：不依赖额外训练，适合资源有限场景。
- **自适应机制**：通过条件数估计动态调整阻尼和归一化，解决数值病态，提升了稳健性。
- **实验设计**：对比了多种先进解决器，展示了通用性。

## 8. 不足与局限

- **计算开销**：二阶信息（Hessian-向量积 + CG 迭代）每一步会引入额外计算，文中未报告实际时间成本，可能限制实时或大规模部署。
- **理论分析不足**：未提供收敛性证明或误差界，仅凭实验验证。
- **实验覆盖有限**：缺少在更高分辨率（如 512×512 以上）或视频/3D 数据上的测试，通用性待验证。
- **基准指标单一**：仅依赖 FID 等传统指标，缺乏用户研究或感知质量评估。
- **数值稳定性依赖超参数**：阻尼系数 \(\lambda_t\) 的估计方式可能对初始设定敏感，未给出鲁棒性分析。

（完）
