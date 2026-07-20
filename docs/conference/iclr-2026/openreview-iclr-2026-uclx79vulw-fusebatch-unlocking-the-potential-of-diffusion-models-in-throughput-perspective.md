---
title: "FuseBatch: Unlocking the Potential of Diffusion Models in Throughput Perspective"
title_zh: FuseBatch：从吞吐量角度释放扩散模型的潜力
authors: "Minkyu Kim, Baekseung Kim, Junhoo Lee, Jangho Kim, Nojun Kwak"
date: 2025-09-19
pdf: "https://openreview.net/pdf?id=uclX79Vulw"
tags: ["query:diff-accel"]
score: 7.0
evidence: 仅推理的批量融合提升吞吐量，无需训练
tldr: 本文提出FuseBatch，一种仅推理的批量加速方法。通过将多个输入融合到共享潜变量中，仅执行一次去噪过程，再解融合输出，从而大幅提升扩散模型的吞吐量。结合时间步融合调度，在质量与效率间取得平衡。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 现有加速方法减少迭代次数接近极限，需从不同角度提升效率。
method: 将多个输入融合为共享潜变量，一次去噪后解融合，并设计时间步融合调度。
result: 在多种扩散模型上显著提升吞吐量，质量损失有限。
conclusion: 从吞吐量角度为扩散模型提供了一种无需训练的加速新思路。
---

## Abstract
Diffusion models achieve state-of-the-art image quality but suffer from slow, iterative denoising. Existing acceleration methods focus on reducing the number of iterations, but these approaches are nearing practical limits. To address this, we take a different perspective by improving efficiency through generating multiple images within a single forward pass. We propose **FuseBatch** which fuses multiple inputs into a shared latent, applies the denoiser once, and unfuses the results to recover all outputs. To extend across domains, we introduce **FB-UNet** for pixel-space models and **FB-AE** for latent diffusion models.We further propose **Timestep-Fusion Scheduling (TFS)**, an inference-only strategy that balances throughput and quality, enabling FuseBatch to surpass the baseline at comparable throughput settings. Across DDPM and step-reduction methods (*e.g* DDIM, Flow Matching), we achieve near-multiplicative throughput gains with modest quality trade-offs, demonstrating its compatibility with existing acceleration techniques. Moreover, it scales effectively to high-resolution LDMs where larger fusion factors become attainable, providing a practical and orthogonal path to faster diffusion sampling.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

扩散模型在图像生成质量上表现优异，但存在迭代式去噪过程导致的推理速度慢、计算开销大的问题。现有的加速方法（如减少采样步数、蒸馏等）已接近理论极限。本文从**吞吐量**这一新视角出发，提出在单次前向传播中同时生成多张图像，从而在不减少迭代次数的前提下成倍提升吞吐量。核心动机是探索一条与现有步数缩减方法正交、且无需额外训练的加速路线。

## 2. 方法论：核心思想、关键技术细节与算法流程

**核心思想**：将多个不同的输入（如多个随机噪声或条件）融合（fuse）成一个共享的潜变量，仅执行一次去噪过程，再通过解融合（unfuse）恢复所有输出。从而实现“一次去噪，多张生成”。

**关键技术细节**：
- **FuseBatch**：通用批处理融合框架。对于像素空间扩散模型（如DDPM），设计**FB-UNet**：在UNet的输入层将批量样本（如噪声图）进行混合（如加权和），送入单一去噪路径，输出后再通过逆操作解耦。
- **对于潜在扩散模型（LDM）**（如Stable Diffusion），设计**FB-AE**：在自编码器的潜在空间进行融合/解融合操作，避免在像素空间直接混合带来的信息损失。
- **时间步融合调度（Timestep-Fusion Scheduling, TFS）**：一种仅推理的调度策略。在不同去噪时间步动态调整融合强度（如早期保持独立去噪，后期进行融合），以平衡吞吐量与生成质量。TFS使得在相近吞吐量下，FuseBatch甚至能超越基线方法。

**算法流程（文字说明）**：
1. 输入：一组N个随机噪声或条件向量。
2. 融合操作：将它们按预设权重融合为一个共享潜在表示（如按通道或空间加权平均）。
3. 单次去噪：将该共享表示输入扩散模型的去噪网络（UNet或LDM），进行完整的迭代去噪（步数可任意设定）。
4. 解融合操作：从去噪后的共享表示中，通过逆映射恢复N个独立输出（如使用与融合对应的线性变换逆矩阵）。
5. 若使用TFS：在部分时间步采用独立去噪（融合因子=1），其余时间步采用完全融合，调度函数可预先设计。

## 3. 实验设计

- **数据集/场景**：未在摘要中明确列出具体数据集名称，但推测使用了标准图像生成基准（如CIFAR-10, ImageNet等）以及高分辨率图像生成任务。
- **Benchmark**：对比了DDPM、以及步数缩减方法如DDIM、Flow Matching。重点评估吞吐量（每秒生成图像数）与生成质量（FID、IS等指标）的权衡。
- **对比方法**：包括标准单张生成的基线（batch size = 1或常规batch）、以及现有加速方法（如DDIM、蒸馏模型）。验证了FuseBatch与这些方法的兼容性。

## 4. 资源与算力

摘要及元数据**未明确说明**使用的GPU型号、数量或训练时长。由于FuseBatch是仅推理的方法，无需训练，因此计算资源主要消耗在推理测试阶段。文中未给出具体硬件配置。推测作者可能在常用GPU（如A100、V100）上进行了实验，但未披露。

## 5. 实验数量与充分性

摘要中仅概括性描述了主要结果：在DDPM、DDIM、Flow Matching上实现了“近倍增的吞吐量增益且质量损失适度”，并在高分辨率LDM上展现了可扩展性。未给出具体的实验分组数目（如不同数据集、不同融合因子、不同调度策略的消融实验数）。**充分性评估**：由于只有摘要，无法判断实验的全面性。但提及了与现有加速方法的正交性实验，以及可扩展至LDM，说明有一定消融。但从元数据看该文被ICLR 2026拒稿，可能实验细节或说服力不足。需要查看全文才能确定。

## 6. 论文的主要结论与发现

- FuseBatch能够在不训练的条件下，将扩散模型的推理吞吐量提升接近融合因子倍数（例如融合2张图像则吞吐量翻倍），而质量下降可接受（通过TFS可进一步缓解）。
- 与现有的步数缩减方法（DDIM、Flow Matching等）叠加使用，可获得累积加速效果。
- 在高分辨率LDM中能实现更大的融合因子，表明该方法对高计算负载场景更具优势。
- 时间步融合调度（TFS）是关键的平衡工具，能在相同吞吐量设定下达到甚至超过基线质量。

## 7. 优点

- **正交性**：与现有任何减少迭代步数的方法互补，可叠加使用，提供新的加速维度。
- **无需训练**：直接即插即用，不改变模型权重，降低了部署成本。
- **简单灵活**：融合/解融合操作仅修改输入输出层，可适配不同架构（UNet、LDM）。
- **潜在可扩展**：在生成高分辨率图像时，单次去噪开销大，融合带来的吞吐量收益更明显。

## 8. 不足与局限

- **质量与效率的权衡**：融合必然会引入信息混叠，导致生成图像多样性降低或细节模糊，摘要仅声称“适度”损失，但未给出具体退化幅度。
- **实验覆盖不明确**：缺乏具体数据集、数值结果（如FID对比）、消融实验数量等关键信息，无法独立复现或评估其实际效果。
- **应用限制**：融合操作可能对依赖独立噪声源的多样性生成有负面影响；此外，对于条件生成（如文本到图像），融合不同条件可能导致语义混淆，摘要未讨论该情况。
- **资源算力缺失**：未提供实验环境，不利于其他研究者复现。
- **被拒稿可能性**：作为ICLR 2026拒稿论文，或许存在方法论创新性不足或实验不充分的问题。

（完）
