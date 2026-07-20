---
title: Efficient Denoising Diffusion via Probabilistic Masking
title_zh: 通过概率掩码实现高效去噪扩散
authors: "WEIZHONG ZHANG, Zhiwei Zhang, Renjie Pi, Zhongming Jin, Yuan Gao, Jieping Ye, Kani Chen"
date: 2024-05-02
pdf: "https://openreview.net/pdf?id=pktvuR7b5v"
tags: ["query:diff-accel"]
score: 6.0
evidence: 通过概率掩码跳过冗余时间步
tldr: 提出高效去噪扩散方法（EDDPM），在训练过程中通过概率重参数化学习二元掩码，识别并跳过冗余时间步，从而减少推断步数。该方法需要训练，但针对扩散模型采样加速问题，属于加速方法。
source: ICML-2024-Public
selection_source: conference_retrieval
motivation: 扩散模型推断需要大量时间步。
method: 在训练中利用概率掩码确定哪些步骤可跳过。
result: 有效减少推断步数，保持生成质量。
conclusion: EDDPM通过学习方式实现了采样加速。
---

## Abstract
Diffusion models have exhibited remarkable advancements in generating high-quality data. However, a critical drawback is their computationally intensive inference process, which requires a large number of timesteps to generate a single sample. Existing methods address this challenge by decoupling the forward and reverse processes, and they rely on handcrafted rules for sampling acceleration, leading to the risk of discarding important steps. In this paper, we propose an Efficient Denoising Diffusion method via Probabilistic Masking (EDDPM) that can identify and skip the redundant steps during training. To determine whether a timestep should be skipped or not, we employ probabilistic reparameterization to continualize the binary determination mask. The mask distribution parameters are learned jointly with model weights. By incorporating a real-time sparse constraint, our method can effectively identify and eliminate unnecessary steps during the training iterations, thereby improving inference efficiency. Notably, as the model becomes fully trained, the random masks converge to a sparse and deterministic one, retaining only a small number of essential steps. Empirical results demonstrate the superiority of our proposed EDDPM over the state-of-the-art sampling acceleration methods across various domains. EDDPM can generate high-quality samples with only 20% of the steps for time series imputation and achieve 4.89 FID with 5 steps for CIFAR-10. Moreover, when starting from a pretrained model, our method efficiently identifies the most informative timesteps within a single epoch, which demonstrates the potential of EDDPM to be a practical tool to explore large diffusion models with limited resources.

---

## 论文详细总结（自动生成）

# 论文总结：Efficient Denoising Diffusion via Probabilistic Masking (EDDPM)

## 1. 核心问题与整体含义（研究动机与背景）
- **问题**：扩散模型在生成高质量数据方面性能优异，但其推理过程需要大量时间步（timesteps），计算开销极大，限制了实际部署。
- **现有方法局限**：已有采样加速方法通常将前向过程与反向过程解耦，并依赖手工设计的规则来跳过步骤，可能导致重要步骤被错误丢弃，从而损害生成质量。
- **本文动机**：提出一种数据驱动的、自适应识别冗余时间步的方法，在不牺牲生成质量的前提下显著减少推理步数。

## 2. 方法论：核心思想、关键技术细节
### 核心思想
- 通过在训练过程中引入**概率掩码**（Probabilistic Masking），让模型自动学习哪些时间步可以被跳过。
- 掩码是二元的（0/1），代表该步骤在反向过程中是否参与计算。训练过程中，掩码通过**概率重参数化**（Probabilistic Reparameterization）被连续化，从而可以通过梯度下降进行优化。
### 关键技术细节
- **概率重参数化**：将离散的二元掩码 `m_t ∈ {0,1}` 重参数化为连续随机变量，使得掩码分布参数可与模型权重一起通过反向传播学习。
- **实时稀疏约束**：在训练损失中加入稀疏性惩罚，促使掩码逐渐收敛到稀疏且确定的形式（大多数步骤被屏蔽，仅保留少数关键步骤）。
- **联合训练**：模型权重与掩码分布参数在单次训练过程中同步更新。当模型完全训练后，随机掩码收敛为确定性的稀疏掩码，仅保留少数必要时间步。
- **适用性**：方法支持从预训练模型出发微调（fine-tuning），仅需一个epoch即可识别出最具信息量的时间步。

### 算法流程说明（文字描述）
1. 初始化扩散模型参数θ和掩码分布参数φ（例如每个时间步对应一个伯努利分布的logits）。
2. 在每个训练迭代中，从掩码分布中采样一个二元掩码向量 `m`，用于屏蔽不需要参与计算的时间步。
3. 前向过程按标准方式添加噪声；反向过程仅计算掩码中 `m_t=1` 的步骤（被屏蔽的步骤跳过，使用上一步的输出直接作为下一步输入）。
4. 优化目标：原始的扩散损失（如噪声预测损失）加上掩码的稀疏正则项（例如L1惩罚）。
5. 通过重参数化技巧，对φ和θ同时进行梯度更新。
6. 随着训练进行，掩码分布逐渐峰值化（温度降低或通过Gumbel-Softmax技巧），最终得到确定性的稀疏掩码。
7. 推理时仅运行掩码保留的步骤。

## 3. 实验设计
### 数据集与场景
- 时间序列插补（文中提到仅用20%步骤即可）。
- CIFAR-10（图像生成，5步FID达到4.89）。
- 其他领域（文中称“various domains”，但未详细列出）。
### 基准对比
- 对比了当前最先进的采样加速方法（state-of-the-art sampling acceleration methods）。
- 具体方法名称未在摘要中列出，但声称在多个领域优于它们。
### 评价指标
- FID（Fréchet Inception Distance）用于图像生成质量评估。
- 用于时间序列插补的指标未明确说明（可能为MSE或MAE）。

## 4. 资源与算力
- **未明确说明**：论文摘要及元数据未提及使用的GPU型号、数量、训练时长等算力信息。仅在方法中提及“单epoch即可找到信息最丰富的步骤”，但未给出具体硬件配置。

## 5. 实验数量与充分性
- **实验数量**：摘要仅简要列举了时间序列插补和CIFAR-10两个代表性结果。完整的实验组数（如不同数据集、不同噪声调度、消融研究等）未在摘要中呈现。
- **充分性**：基于摘要信息难以判断实验的全面性。声称“优于SOTA”，但缺乏详细对比表格和统计显著性验证。实验设计可能还包括：
  - 不同步数下的生成质量对比（例如1步、5步、10步 vs 完整步骤）。
  - 从零训练 vs 从预训练模型微调的性能对比。
  - 掩码稀疏程度的消融研究。
- **客观性与公平性**：未提及是否使用相同种子、是否公平比较基线方法（如DDIM、DPM-Solver等）。需要阅读全文确认。

## 6. 主要结论与发现
- EDDPM能有效识别并跳过冗余时间步，在保持生成质量的同时大幅度减少推理步数。
- 时间序列插补任务仅需20%的步骤即可达到较好效果。
- CIFAR-10上仅用5步即可获得FID 4.89，优于现有加速方法。
- 从预训练模型出发，单个epoch即可收敛到稀疏掩码，表明该方法对资源有限场景具有实用价值。

## 7. 优点（方法与实验亮点）
- **自动化学习掩码**：无需手工设计规则，模型自适应确定哪些步骤重要，避免了人为丢弃重要步骤的风险。
- **概率重参数化**：巧妙地将离散选择连续化，使得掩码可通过梯度下降优化，易于与标准扩散训练框架结合。
- **稀疏约束**：确保最终掩码稀疏，在推理时显著减少计算量。
- **兼容预训练模型**：支持微调，适用于大型扩散模型的加速探索。
- **训练效率较高**：从预训练模型出发仅需一个epoch，降低了额外训练成本。

## 8. 不足与局限
- **需要额外训练**：相比无需训练的方法（如DDIM），EDDPM需要重新训练或至少微调，可能增加资源开销。
- **实验细节缺失**：摘要未提供完整数据集、基线方法、超参数设定、硬性资源需求等，导致无法全面评估方法的实用性。
- **泛化性验证有限**：仅提及时间序列插补和CIFAR-10两个场景，缺乏更高分辨率图像（如ImageNet）、文本、音频等模态的验证。
- **潜在偏差风险**：稀疏掩码的收敛可能依赖初始掩码分布或正则项强度，不同设定下结果可能不稳定。
- **与最新加速方法的比较**：未提及与DPM-Solver++、FastDPM等近年方法的直接对比，时效性存疑。

（完）
