---
title: "A-FloPS: Accelerating Diffusion Models via Adaptive Flow Path Sampler"
title_zh: A-FloPS：通过自适应流路径采样器加速扩散模型
authors: "Cheng Jin, Zhenyu Xiao, Yuantao Gu"
date: 2026-03-17
pdf: "https://ojs.aaai.org/index.php/AAAI/article/download/39397/43358"
tags: ["query:diff-accel"]
score: 9.0
evidence: 训练无关的自适应流路径采样器
tldr: 提出A-FloPS，一种训练无关的加速框架，通过将扩散模型的采样轨迹重参数化为流匹配形式，并引入自适应速度分解，避免了传统ODE求解器的数值开销，实现了高效快速采样。
source: AAAI-2026-Accepted
selection_source: conference_retrieval
figures_json: "[{\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-39397/fig-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 1646, \"height\": 1280, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-39397/fig-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 702, \"height\": 1486, \"label\": \"Figure\"}]"
tables_json: "[{\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-39397/table-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 864, \"height\": 259, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-39397/table-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 1837, \"height\": 317, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-39397/table-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 1718, \"height\": 397, \"label\": \"Table\"}]"
motivation: 现有训练无关加速方法受限于低效的采样轨迹。
method: 将采样轨迹重参数化为流匹配形式，并添加自适应速度分解以提升积分友好性。
result: A-FloPS在多种预训练扩散模型上实现了显著的采样加速，且无需重新训练。
conclusion: A-FloPS提供了一种原则性的训练无关加速方案，适用于多种模态。
---

## Abstract
Diffusion models deliver state-of-the-art generative performance across diverse modalities but remain computationally expensive due to their inherently iterative sampling process. Existing training-free acceleration methods typically improve numerical solvers for the reverse-time ODE, yet their effectiveness is fundamentally constrained by the inefficiency of the underlying sampling trajectories.
We propose A-FloPS (Adaptive Flow Path Sampler), a principled, training-free framework that reparameterizes the sampling trajectory of any pre-trained diffusion model into a flow-matching form and augments it with an adaptive velocity decomposition. The reparameterization analytically maps diffusion scores to flow-compatible velocities, yielding integration-friendly trajectories without retraining. The adaptive mechanism further factorizes the velocity field into a linear drift term and a residual component whose temporal variation is actively suppressed, restoring the accuracy benefits of high-order integration even in extremely low-NFE regimes.
Extensive experiments on conditional image generation and text-to-image synthesis show that A-FloPS consistently outperforms state-of-the-art training-free samplers in both sample quality and efficiency. Notably, with as few as 5 function evaluations, A-FloPS achieves substantially lower FID and generates sharper, more coherent images. The adaptive mechanism also improves native flow-based generative models, underscoring its generality. These results position A-FloPS as a versatile and effective solution for high-quality, low-latency generative modeling.

---

## 论文详细总结（自动生成）

# A-FloPS: Accelerating Diffusion Models via Adaptive Flow Path Sampler — 中文总结

## 1. 核心问题与整体含义（研究动机与背景）

- **问题**：扩散模型在多模态生成上表现优异，但迭代采样过程导致推理延迟高、计算成本大。现有训练无关加速方法（如DDIM、DPM-Solver++、UniPC）主要改进反向时间ODE的数值求解器，但它们的性能从根本上受限于原始采样轨迹的低效性。
- **含义**：需要一种无需重新训练、能直接适用于任意预训练扩散模型的方法，通过**重参数化采样轨迹**使其更利于数值积分，从而在极低函数评估次数（NFE）下仍能保持高质量生成。

## 2. 方法论：核心思想、关键技术细节、算法流程

- **核心思想**：将扩散模型的采样轨迹重参数化为流匹配（Flow Matching, FM）形式，并引入自适应速度分解，以抑制残差项的时间变化，恢复高阶积分器在低NFE下的精度优势。
- **关键技术细节**：
  - **扩散到流匹配的映射（定理1）**：推导出扩散模型评分函数与FM速度场的解析对应关系：  
    `(1 − t)v*(x_t, t) = (σ_τ / ᾱ_τ) [x_t + σ_τ ∇log p_τ((ᾱ_τ + σ_τ)x_t)]`，其中 `t = 1/(1 + σ_τ/ᾱ_τ)`。该映射是双射，无需训练即能将任意扩散模型转换为FM轨迹。
  - **Flow Path Sampler (FloPS)**：基于上述映射，在推理时用原扩散模型的评分网络计算FM速度，并用欧拉法积分。对于映射起始时间`t_min`之前的部分，用`t_min`处的速度近似。
  - **自适应机制 (A-FloPS)**：将速度场分解为线性漂移项 `λ_t x_t` 和残差项 `h(x_t, t; λ) = v(x_t, t) − λ_t x_t`，其中 `λ_t` 在每步局部估计，使残差的时间变化最小（通过闭式解 `λ^{(n)} = ⟨Δv, Δx⟩/‖Δx‖²` 计算）。然后使用二阶泰勒展开（公式14-16）进行高阶积分，避免显式计算时间导数。
- **算法流程**：  
  1) 从标准正态采样 `x_0`。  
  2) 设定步长 `Δt = 1/N`。  
  3) 对每一步 `n`：  
     - 根据 `t_n` 映射到扩散时间 `τ`，计算速度 `v_{t_n}`（当 `t_n < t_min` 时用`t_min`处的速度）。  
     - 对于 `n > 0`，计算自适应系数 `λ^{(n)}` 以及系数 `a, b`。  
     - 用二阶更新公式（14）得到 `x_{t_{n+1}}`（第一步用欧拉更新）。  
  4) 返回 `x_1`。

## 3. 实验设计

- **数据集与应用场景**：
  - **条件图像生成**：ImageNet 256×256，使用DiT（扩散Transformer）作为基础模型。
  - **文本到图像生成**：COCO 5K提示，使用Stable Diffusion v3.5（变形为流匹配模型）。
- **基准与对比方法**：
  - 对比DDIM、DPM-Solver++、UniPC（均为训练无关方法）。
  - 消融实验：无自适应机制的FloPS vs DDIM；自适应机制在原生FM模型上（A-Euler vs 标准Euler）。
- **评估指标**：FID、IS、Precision/Recall（条件生成）；CLIP分数、ImageReward (IR)、GPT-based评估（文本-图像一致性和感知质量）。
- **协议**：使用固定随机种子，NFE从5到10变化，重点评估低NFE场景。

## 4. 资源与算力

- **硬件**：8 × NVIDIA A100 GPU（每块40GB显存）。
- **说明**：论文未明确提及训练时长或总计算量，因为A-FloPS是**推理阶段方法**，无需重新训练模型。实验仅涉及推理采样，算力消耗主要受采样步数和模型规模影响。

## 5. 实验数量与充分性

- **实验组数**：
  - 主实验（表1）：在DiT上，5种NFE (5~10)下对比4种方法，含FID/IS/Precision/Recall共20个数值。
  - 消融实验1（表2）：FloPS vs DDIM，6种NFE，含FID/IS/Prec/Rec。
  - 消融实验2（表3）：A-Euler vs Euler在SDv3.5上，3种NFE，含CLIP/IR/GPT分数。
  - 定性示例（图1、2）。
- **充分性评价**：是。实验覆盖了扩散模型和原生FM模型，对比了主流的训练无关求解器，消融实验验证了各组件的贡献。但缺少与蒸馏等其他加速范式的比较（论文明确限定于训练无关方法，合理）。数据集和指标选择标准，随机种子固定保证公平。

## 6. 主要结论与发现

- A-FloPS在所有设置下一致优于DDIM、DPM-Solver++、UniPC，尤其 **NFE=5时FID降至6.977**，远优于第二名（19.165），图像更清晰、结构更连贯。
- **FloPS单独（无自适应）** 也显著优于DDIM（如NFE=5时FID 14.9 vs 38.1），证明轨迹重参数化本身就能提升效率。
- **自适应机制可泛化到原生FM模型**：在SDv3.5上A-Euler在低NFE下大幅提升IR和GPT分数，图像质量更锐利。
- 结论：A-FloPS是一种即插即用、训练无关的通用加速方案，适用于扩散模型和流匹配模型，在低NFE下实现高质量生成。

## 7. 优点

- **训练无关**：无需重新训练或修改网络参数，可直接应用于任意预训练扩散模型。
- **理论严谨**：推导了扩散→FM的解析映射，并证明双射性；自适应系数通过闭合形式优化残差平滑性。
- **兼容性强**：支持任意噪声调度和预测参数化（分数、噪声、速度），并可直接适配至原生FM模型。
- **低NFE优势明显**：在5~10步内大幅领先现有训练无关求解器，适合实时或资源受限场景。
- **定性结果一致**：生成图像纹理清晰、边缘锐利，与定量指标吻合。

## 8. 不足与局限

- **实验覆盖不足的方面**：
  - 仅测试了图像模态（条件生成和文生图），未在音频、视频或3D等模态上验证。
  - 对比方法仅限于训练无关类，未与蒸馏（Progressive Distillation）或一致性模型等训练相关方法比较（虽非论文定位，但可提及）。
  - 未分析对超参数（如λ约束范围[-1,1]）的敏感性，可能影响可复现性。
- **潜在偏差**：
  - GPT-based评估的细节未充分说明（仅称“appendix”），可能存在未控制的偏见。
  - CLIP分数在A-Euler上略降，作者以“saturation and stylistic bias”解释，但可能需进一步论证。
- **应用限制**：
  - 需要预训练扩散模型提供完整的噪声调度参数（σ_τ, ᾱ_τ），某些黑盒模型可能不公开。
  - 自适应机制每步需额外计算内积和除法，增加微小开销（但相对NFE减少可忽略）。

（完）
