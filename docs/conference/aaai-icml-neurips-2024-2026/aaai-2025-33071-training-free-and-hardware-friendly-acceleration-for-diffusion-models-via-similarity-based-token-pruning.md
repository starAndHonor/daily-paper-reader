---
title: Training-Free and Hardware-Friendly Acceleration for Diffusion Models via Similarity-based Token Pruning
title_zh: 基于相似度的令牌剪枝：一种训练无关且硬件友好的扩散模型加速方法
authors: "Evelyn Zhang, Jiayi Tang, Xuefei Ning, Linfeng Zhang"
date: 2025-04-11
pdf: "https://ojs.aaai.org/index.php/AAAI/article/download/33071/35226"
tags: ["query:diff-accel"]
score: 9.0
evidence: 基于相似度的token剪枝实现训练无关加速
tldr: 提出SiTo，一种训练无关的令牌剪枝方法，通过计算token间的相似度自适应剪除冗余令牌，仅使用廉价操作即可最大化与原始预测的相似度，在图像生成任务上实现了显著的加速比且无性能损失。
source: AAAI-2025-Accepted
selection_source: conference_retrieval
figures_json: "[{\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33071/fig-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 847, \"height\": 509, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33071/fig-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 871, \"height\": 303, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33071/fig-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 870, \"height\": 303, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33071/fig-004.webp\", \"caption\": \"\", \"page\": 0, \"index\": 4, \"width\": 1748, \"height\": 1002, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33071/fig-005.webp\", \"caption\": \"\", \"page\": 0, \"index\": 5, \"width\": 832, \"height\": 508, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33071/fig-006.webp\", \"caption\": \"\", \"page\": 0, \"index\": 6, \"width\": 841, \"height\": 374, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33071/fig-007.webp\", \"caption\": \"\", \"page\": 0, \"index\": 7, \"width\": 844, \"height\": 361, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2025-accepted/aaai-2025-33071/fig-008.webp\", \"caption\": \"\", \"page\": 0, \"index\": 8, \"width\": 844, \"height\": 392, \"label\": \"Figure\"}]"
tables_json: "[{\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-33071/table-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 869, \"height\": 216, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-33071/table-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 873, \"height\": 336, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2025-accepted/aaai-2025-33071/table-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 1738, \"height\": 653, \"label\": \"Table\"}]"
motivation: 扩散模型计算量大，现有方法多关注减少步数或压缩网络，忽略了数据冗余。
method: 基于相似度自适应剪枝输入令牌中的冗余部分，使用硬件友好的操作。
result: SiTo在多个扩散模型上取得了显著的加速比，生成质量与原始模型几乎一致。
conclusion: SiTo是一种简单有效的训练无关加速方法，特别适合部署在边缘设备。
---

## Abstract
The excellent performance of diffusion models in image generation is always accompanied by overlarge computation costs, which have prevented the application of diffusion models in edge devices and interactive applications. Previous works mainly focus on using fewer sampling steps and compressing the denoising network of diffusion models, while this paper proposes to accelerate diffusion models by introducing SiTo, a similarity-based token pruning method that adaptive prunes the redundant tokens in the input data. SiTo is designed to maximize the similarity between model prediction with and without token pruning by using cheap and hardware-friendly operations, leading to significant acceleration ratios without performance drop, and even sometimes improvements in the generation quality. For instance, the zero-shot evaluation shows SiTo leads to 1.90x and 1.75x acceleration on COCO30K and ImageNet with 1.33 and 1.15 FID reduction at the same time. Besides, SiTo has no training requirements and does not require any calibration data, making it plug-and-play in real-world applications.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）
- **问题**：扩散模型（如 Stable Diffusion）在图像生成中性能优异，但计算成本极高，难以部署到边缘设备和实时交互应用中。
- **现有方法局限**：已有加速方法主要关注减少采样步数（如 DDIM）或压缩去噪网络（如量化、剪枝、蒸馏），但忽略了输入数据本身存在的冗余（token数量多）。
- **本文目标**：提出一种训练无关、硬件友好的 token 剪枝方法 SiTo，通过自适应地剪除输入数据中的冗余 token，在不降低生成质量的前提下实现显著加速，甚至有时还能提升生成质量。

## 2. 方法论：核心思想、关键技术细节、算法流程
- **核心思想**：利用 token 间的相似度，选择一组“基 token”（base tokens），然后剪掉与基 token 最相似的冗余 token，并在计算后通过复制基 token 来恢复被剪掉的 token，以最小化恢复误差。
- **三阶段流程**：
  1. **基 token 选择（Base Token Selection）**：
     - 计算所有 token 间的余弦相似度。
     - 对每个 token，求和得到相似度分数（SimScore）。
     - 加入高斯噪声（随机性），避免同一 token 在连续时间步被重复剪枝。
     - 将图像划分为不重叠的局部块（patch），在每个块内选择噪声相似度最高的 token 作为基 token，保证空间均匀分布。
  2. **剪枝 token 选择（Pruned Token Selection）**：
     - 对剩余 token，计算每个 token 与所有基 token 的最大余弦相似度。
     - 选择 top-K 个相似度最高的 token 作为剪枝 token（因为这些 token 恢复误差最小）。
  3. **剪枝 token 恢复（Pruned Token Recovery）**：
     - 将未剪枝的 token 送入神经网络层计算。
     - 计算后，将剪枝 token 直接复制其最相似的基 token 的结果。
- **误差分析**：基 token 应具有与所有其他 token 的最高相似性，剪枝 token 应具有与基 token 的最高相似性，从而最小化恢复误差。
- **关键操作**：仅使用余弦相似度和求和、加噪声等廉价操作，不依赖注意力分数，硬件友好，支持批量计算。

## 3. 实验设计
- **数据集与场景**：
  - **ImageNet-1k**：生成 2000 张图像（每类 2 张），分辨率 512×512。
  - **COCO30k**：生成 30,000 张图像（每 caption 1 张），分辨率 512×512。
- **基准模型**：Stable Diffusion v1.5 和 v2。
- **对比方法**：主要与 ToMeSD（token merging 方法）进行对比，同时提及 AT-EDM（但未在实验表格中直接比较）。
- **评估指标**：FID（生成质量）、延迟（秒）、加速比、内存压缩比。
- **采样设置**：50 步 PLMS 采样，cfg scale 分别为 7.5（v1.5）和 9.0（v2）。
- **实现细节**：SiTo 和 ToMeSD 仅应用于 UNet 中的第一层和最后一层自注意力层（这些层占大部分计算量）。通过设置不同剪枝/合并比率得到多种加速版本（a-e）。

## 4. 资源与算力
- **文中明确说明**：平均延迟和加速比是在单张 **NVIDIA 4090 GPU** 上测量的。
- **未明确说明**：训练时长（因为 SiTo 是训练无关的，无需训练）、具体 GPU 数量、总计算量等。实验中仅报告了推理延迟。

## 5. 实验数量与充分性
- **实验组数**：
  - **定量比较**：在 ImageNet 和 COCO30k 上，对 SD v1.5 和 v2 分别比较了 5 个不同加速比率（a-e），共 10 组主实验（表1）。
  - **消融实验**：
    - 6 种基 token 选择策略的对比（表3）。
    - 剪枝 vs. 合并操作对比（图5a）。
    - 不同 patch size 的影响（图5b）。
    - 不同层（SA、CA、FFN）应用 SiTo 的效果（表2）。
    - 不同 UNet 块（外、中、内）的影响（图7）。
    - 不同采样步数（30,40,50）下的性能（图6）。
  - **定性分析**：4 组手动设计的 challenging prompt 视觉对比（图2）。
- **评估充分性**：实验覆盖了不同数据集、不同模型版本、不同加速比率、多种消融因素，对比了主流方法（ToMeSD），使用了 FID 和延迟等标准指标，实验设计较为全面。但未与其他 token 剪枝方法（如 AT-EDM）进行直接定量比较，可能存在一定局限。

## 6. 主要结论与发现
- SiTo 在 **ImageNet** 上，以 1.90× 加速比和 2.70× 内存压缩的同时，FID 降低了 1.33（比原始模型更好）。
- 在 **COCO30k** 上，以 1.75× 加速比，FID 比 ToMeSD 更低（11.17 vs. 12.15）。
- **定性比较**：SiTo 生成的图像更逼真、细节更丰富，与文本提示和原始图像对齐更好。
- **消融结论**：
  - 加入高斯噪声可避免同一 token 被重复剪枝，显著提升生成质量。
  - 局部块内选择基 token 优于全局选择（空间均匀性重要）。
  - 剪枝操作优于合并操作，尤其在高剪枝率下。
  - patch size 越大，加速收益边际，但 FID 损失增大。
  - SiTo 仅应用于自注意力层已足够，应用到 CA 和 FFN 收益不大。
  - SiTo 与其他加速方法（如减少步数）正交，可联合使用。

## 7. 优点
- **训练无关**：无需训练，即插即用，便于实际部署。
- **硬件友好**：仅使用余弦相似度、求和、加噪声等简单操作，无复杂图算法或注意力分数依赖，支持批量并行计算。
- **无数据依赖**：无需校准数据，泛化到不同数据集和模型。
- **加速效果显著**：在无性能损失甚至 FID 下降的情况下，实现近 2 倍加速。
- **内存友好**：同时实现内存压缩。
- **与现有方法正交**：可与步数减少方法（如 DDIM）结合使用。

## 8. 不足与局限
- **实验覆盖不足**：
  - 未与当前最先进的 token 剪枝方法 AT-EDM 进行直接定量对比（仅提到其存在缺陷）。
  - 仅在 SD v1.5 和 v2 上测试，未在大规模文本到图像模型（如 SDXL）或其他扩散模型（如 Imagen）上验证。
  - 未评估在不同分辨率、不同采样器下的表现。
- **潜在偏差**：
  - 所有实验均使用 50 步 PLMS 采样，可能对 fast sampler 的泛化性未充分验证。
  - 定性比较仅展示 4 个 prompt，可能代表性不足。
- **应用限制**：
  - 剪枝操作在最深层 UNet 块上应用会导致质量下降（图7），说明并非所有层都适合剪枝。
  - 高剪枝率时（>0.7）FID 可能反弹（表1中 SiTo-e 的 FID 比 SiTo-d 略高）。
  - 方法依赖于 token 间的相似度计算，对于长序列（高分辨率）计算量可能增加，但文中未讨论。
- **理论分析**：对剪枝误差的理论分析较简单（仅给出 e = sum of distances），未深入探讨误差传播或与去噪过程的关系。

（完）
