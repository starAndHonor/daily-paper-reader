---
title: "D^2-DPM: Dual Denoising for Quantized Diffusion Probabilistic Models"
title_zh: D^2-DPM：面向量化扩散概率模型的双去噪方法
authors: "Qian Zeng, Jie Song, Han Zheng, Hao Jiang, Mingli Song"
date: 2025-04-11
pdf: "https://ojs.aaai.org/index.php/AAAI/article/download/33063/35218"
tags: ["query:diff-accel"]
score: 8.0
evidence: 后训练量化加速扩散模型，无需重新训练
tldr: 扩散模型因去噪过程长和计算量大而难以部署。本文提出D2-DPM，一种双去噪机制，用于减轻后训练量化引入的噪声对噪声估计网络的影响。通过分离量化噪声的均值和方差偏差并分别校正，在无需重新训练的情况下加速模型推理。实验表明，该方法在保持生成质量的同时显著提升速度，为扩散模型在资源受限场景下的应用提供了有效方案。
source: AAAI-2025-Accepted
selection_source: conference_retrieval
figures_json: "[{\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33063/fig-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 874, \"height\": 741, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33063/fig-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 1818, \"height\": 540, \"label\": \"Figure\"}]"
tables_json: "[{\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-33063/table-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 882, \"height\": 1520, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-33063/table-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 1840, \"height\": 1066, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-33063/table-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 1842, \"height\": 407, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-33063/table-004.webp\", \"caption\": \"\", \"page\": 0, \"index\": 4, \"width\": 863, \"height\": 442, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-33063/table-005.webp\", \"caption\": \"\", \"page\": 0, \"index\": 5, \"width\": 864, \"height\": 444, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-33063/table-006.webp\", \"caption\": \"\", \"page\": 0, \"index\": 6, \"width\": 879, \"height\": 301, \"label\": \"Table\"}]"
motivation: 扩散模型推理速度慢且计算量大，后训练量化虽能加速但引入量化噪声，影响生成质量。
method: 提出双去噪机制，将量化噪声的影响分解为均值偏差和方差偏差，分别进行校正。
result: 在多个数据集上验证，量化后的模型在保持生成质量的同时推理速度大幅提升。
conclusion: D2-DPM有效克服了量化噪声对扩散模型采样的不利影响，实现了无需重训练的加速。
---

## Abstract
Diffusion models have achieved cutting-edge performance in image generation. However, their lengthy denoising process and computationally intensive score estimation network impede their scalability in low-latency and resource-constrained scenarios. Post-training quantization (PTQ) compresses and accelerates diffusion models without retraining, but it inevitably introduces additional quantization noise, resulting in mean and variance deviations. In this work, we propose D2-DPM, a dual denoising mechanism aimed at precisely mitigating the adverse effects of quantization noise on the noise estimation network. 
Specifically, we first unravel the impact of quantization noise on the sampling equation into two components: the mean deviation and the variance deviation. The mean deviation alters the drift coefficient of the sampling equation, influencing the trajectory trend, while the variance deviation magnifies the diffusion coefficient, impacting the convergence of the sampling trajectory. The proposed D2-DPM is thus devised to denoise the quantization noise at each time step, and then denoise the noisy sample through the inverse diffusion iterations. 
Experimental results demonstrate that D2-DPM achieves superior generation quality, yielding a 1.42 lower FID than the full-precision model while achieving 3.99x compression and 11.67x bit-operation acceleration.

---

## 论文详细总结（自动生成）

# D^2-DPM：面向量化扩散概率模型的双去噪方法 —— 论文详细总结

## 1. 核心问题与整体含义（研究动机和背景）

- **研究动机**：扩散模型在图像生成上性能优异，但其推理过程包含长达1000步的迭代去噪，且每一步都需要运行庞大的噪声估计网络（如U-Net），导致计算量和延迟极高，难以部署到低延迟或资源受限的场景（如边缘设备）。
- **现有加速手段**：一方面通过优化采样器（如DDIM、DPM-Solver）减少迭代步数；另一方面通过模型压缩（剪枝、蒸馏、量化）降低单步计算开销。其中后训练量化（Post-Training Quantization, PTQ）无需重新训练，直接将预训练FP32模型转化为低比特定点模型，实现压缩和加速，但因量化操作引入了额外的量化噪声，导致生成样本的均值和方差偏离原始分布，从而损害生成质量。
- **本文目标**：精准消除量化噪声对噪声估计网络的不利影响，提出双去噪机制（Dual Denoising, D²-DPM），在不重新训练的前提下恢复生成质量，甚至超越全精度模型。

## 2. 方法论：核心思想、关键技术细节

### 核心思想
- 将量化噪声的影响分解为**均值偏差**和**方差偏差**两个独立成分。均值偏差改变采样方程的漂移系数（影响轨迹方向），方差偏差放大扩散系数（影响轨迹收敛性和波动）。
- 通过建立量化输出与量化噪声的联合高斯模型，在推理时根据量化输出精确估计该时间步的量化噪声条件分布，然后分别进行均值校正和方差校正。

### 关键技术细节

1. **时间步感知的量化噪声建模（Time Step-aware Quantization Noise Modeling）**
   - 观察到：在每个时间步，量化噪声 $\Delta\epsilon_\theta^{(t)}$ 近似服从高斯分布；量化输出 $\hat{\epsilon}_\theta^{(t)}$ 也近似服从高斯分布。
   - 建立联合高斯分布：$\begin{bmatrix} \hat{E} \\ \Delta E \end{bmatrix} \sim \mathcal{N}\left( \begin{bmatrix} \hat{\mu} \\ \Delta\mu \end{bmatrix}, \begin{bmatrix} \Sigma_{\hat{E},\hat{E}} & \Sigma_{\hat{E},\Delta E} \\ \Sigma_{\Delta E,\hat{E}} & \Sigma_{\Delta E,\Delta E} \end{bmatrix} \right)$。
   - 为降低高维估计难度，假设各元素独立且各向同性（对角协方差），从而导出条件分布 $\Delta E | \hat{E} = \hat{\epsilon} \sim \mathcal{N}(\mu_{\Delta|\hat{\epsilon}}, \Sigma_{\Delta|\hat{\epsilon}})$。

2. **双去噪机制（Dual Denoising）**
   - **随机双去噪（Stochastic Dual Denoising, S-D²）**：从条件分布中采样一个具体的量化噪声估计 $\Delta\epsilon'$，然后从量化输出中减去该噪声得到恢复的扩散噪声 $E' = \hat{E} - \Delta\epsilon'$，再代入含随机项的SDE采样方程进行去噪。该方法同时校正均值和方差，使恢复后的 $E'$ 与原始 $E$ 同分布。
   - **确定双去噪（Deterministic Dual Denoising, D-D²）**：只减去量化噪声的条件均值 $\Delta\mu$，即 $E' = \hat{E} - \Delta\mu$。此时校正了均值，但余留的方差 $\sigma_\Delta^2 I$ 被吸收到扩散项中，从而隐式增强了随机性（等价于构造了更大的Langevin扩散项），适用于低随机性容量的采样器（如ODE采样器）。

3. **算法流程（文字描述）**
   - 校准阶段：使用少量样本收集全精度模型与量化模型在每个时间步的输出对 $(\hat{\epsilon}, \Delta\epsilon)$，估计联合高斯分布的参数。
   - 推理阶段：对于每个时间步，输入量化模型得到 $\hat{\epsilon}_\theta^{(t)}$，根据条件分布计算 $\mu_{\Delta|\hat{\epsilon}}$ 和 $\Sigma_{\Delta|\hat{\epsilon}}$。
     - 若使用S-D²：再采样 $z\sim\mathcal{N}(0,I)$，得到 $\Delta\epsilon' = \mu_{\Delta|\hat{\epsilon}} + \Sigma_{\Delta|\hat{\epsilon}}^{1/2}z$，则 $E' = \hat{\epsilon} - \Delta\epsilon'$；将 $E'$ 代入SDE采样公式。
     - 若使用D-D²：直接取 $E' = \hat{\epsilon} - \mu_{\Delta|\hat{\epsilon}}$，调整扩散项系数以吸收残余方差，代入SDE/ODE采样公式。
   - 最后按照逆扩散迭代得到生成样本。

## 3. 实验设计

- **数据集**：ImageNet 256×256（条件生成）、LSUN-Bedrooms 256×256、LSUN-Churches 256×256（无条件生成）。
- **基准模型**：Latent Diffusion Model（LDM-4 和 LDM-8）。
- **对比方法**：
  - 全精度FP32模型。
  - PTQ方法：PTQ4DM、Q-diffusion、PTQD（当前SOTA）。
- **量化设置**：采用BRECQ作为PTQ基线，权重使用Adaround量化，激活使用均匀量化。第一层和最后一层固定8-bit，其余层量化到目标比特（W8A8和W4A8）。使用两种量化配置：W8A8（3.99x压缩，11.67xBOPs加速）和W4A8（7.95x压缩，23.33xBOPs加速）。
- **评价指标**：FID、sFID、Inception Score (IS)、Precision、Recall。使用OpenAI官方评估器生成50,000个样本计算。
- **采样参数设置**：
  - 条件生成：$\{scale=3.0, \eta=0.0|1.0, steps=20\}$ 和 $\{scale=1.5, \eta=0.0|1.0, steps=250\}$。
  - 无条件生成：$\{\eta=0.0|1.0, steps=200\}$。
- **消融实验**：在ImageNet上对S-D²和D-D²的各组件进行消融（随机联合校正SJC、确定性均值校正DMC、确定性方差校正DVC）。

## 4. 资源与算力

论文正文未明确说明使用的GPU型号、数量及训练时长。仅在实验设置中提到采用BRECQ进行PTQ，校准过程需要收集少量采样数据，但未给出具体硬件配置或时间开销。因此，无法量化算力消耗。

## 5. 实验数量与充分性

- **实验数量**：共进行了多组实验：
  - 条件生成：ImageNet上两种采样步数（20步和250步）、两种随机性容量（η=0.0和1.0）、两种量化比特（W8A8和W4A8），共约8个主要表格结果。
  - 无条件生成：LSUN-Bedroom和LSUN-Church两个数据集，各两种量化比特，共约4个表格结果。
  - 消融实验：1个表格，包含SJC、DMC、DVC的逐步贡献。
- **充分性与客观性**：
  - 覆盖了不同采样步数、不同随机性容量、不同量化位宽、条件与无条件任务，对比方法包括三种主流PTQ方法（PTQ4DM、Q-diffusion、PTQD），评价指标全面（FID、sFID、IS、Precision、Recall）。
  - 实验设计较为公平：所有对比方法均使用相同的量化策略和校准数据集，且本文方法不依赖额外训练数据。
  - 消融实验验证了各组件必要性，分析合理。
  - 局限性：所有实验仅在LDM架构上进行，未涉及其他扩散模型（如DDPM、score-based SDE模型），也未在更低比特（如W2A4）或更高分辨率图像上验证。

## 6. 主要结论与发现

1. 量化噪声在每个时间步近似服从高斯分布，其均值和方差分别独立影响采样方程中的漂移项和扩散项。
2. 提出的S-D²能同时校正均值和方差，恢复原始分布，尤其适用于高随机性容量（SDE）采样器。
3. 提出的D-D²仅校正均值，将残余方差吸收为额外随机性，在低随机性容量（ODE）采样器上效果显著，甚至优于S-D²。
4. 在W4A8条件下，D²-DPM在ImageNet 20步采样中FID比全精度模型低1.42（即生成质量更好），同时实现3.99x压缩和11.67x BOPs加速；在LSUN-Bedroom上FID相对于PTQD降低1~2。
5. 消融实验表明，均值校正和方差校正均有独立贡献，联合使用效果最优。

## 7. 优点

- **创新性**：首次从SDE框架理论推导量化噪声对采样方程的影响，并解耦均值与方差，针对性设计两种去噪变体，思想清晰且数学严密。
- **实用性**：采用PTQ，无需重新训练，只需少量校准数据（如50步采样的激活值），即可获得显著压缩和加速，适合实际部署。
- **性能优异**：在多种设置下均超越了现有PTQ方法（PTQ4DM、Q-diffusion、PTQD），甚至部分指标优于全精度模型——说明合理校正噪声后，量化误差反而提供了有益随机性，起到正则化作用。
- **广泛适用性**：方法不依赖特定任务类型，可扩展至视频生成、文本建模、分子设计等领域。

## 8. 不足与局限

- **实验覆盖有限**：仅测试了LDM-4和LDM-8两种架构，未在更大规模模型（如Stable Diffusion、Imagen）或更多任务（文生图、超分、修复）上验证，泛化性有待加强。
- **未报告算力消耗**：校准过程需要收集多步的激活值并估计联合高斯协方差，这部分计算开销未量化，也未与QAT等方法对比训练成本。
- **低比特限制**：实验中最低比特为W4A8，未探索W2或W4A4等极低比特，且固定第一/最后一层为8-bit，可能限制了压缩上限。
- **假设偏差风险**：假设各元素独立且各向同性（对角协方差），实际中可能存在通道间相关性，若相关性较强，模型可能不够精确，导致校正偏差。
- **与先进采样器结合**：实验仅使用DDIM/DDPM采样器，未测试结合DPM-Solver等快速ODE求解器时效果是否依然保持。
- **缺少对校准数据量的敏感性分析**：未讨论校准样本数对建模精度的影响。

（完）
