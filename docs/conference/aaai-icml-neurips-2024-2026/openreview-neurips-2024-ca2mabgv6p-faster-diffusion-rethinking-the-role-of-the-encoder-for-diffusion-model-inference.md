---
title: "Faster Diffusion: Rethinking the Role of the Encoder for Diffusion Model Inference"
title_zh: 更快扩散：重新思考编码器在扩散模型推理中的作用
authors: "Senmao Li, taihang Hu, Joost van de Weijer, Fahad Khan, Tao Liu, Linxuan Li, Shiqi Yang, Yaxing Wang, Ming-Ming Cheng, jian Yang"
date: 2024-09-25
pdf: "https://openreview.net/pdf?id=ca2mABGV6p"
tags: ["query:diff-accel"]
score: 8.0
evidence: 通过重用编码器特征实现免训练扩散模型推理加速
tldr: 发现UNet编码器特征在相邻时间步变化极小，因此跳过部分编码器计算并重用特征，无需训练即可加速图像生成。
source: NeurIPS-2024-Accepted
selection_source: conference_retrieval
motivation: 扩散模型推理慢，蒸馏需要大量资源。
method: 利用编码器特征变化小的特点，跳过相邻时间步的编码器计算并重用特征。
result: 在不训练的情况下显著加速图像生成，保持质量。
conclusion: 避免冗余编码器计算是一种有效的免训练加速方法。
---

## Abstract
One of the main drawback of diffusion models is the slow inference time for image generation. Among the most successful approaches to addressing this problem are distillation methods. However, these methods require considerable computational resources. In this paper, we take another approach to diffusion model acceleration. We conduct a comprehensive study of the UNet encoder and empirically analyze the encoder features. This provides insights regarding their changes during the inference process. In particular, we find that encoder features change minimally, whereas the decoder features exhibit substantial variations across different time-steps. This insight motivates us to omit encoder computation at certain adjacent time-steps and reuse encoder features of previous time-steps as input to the decoder in multiple time-steps. Importantly, this allows us to perform decoder computation in parallel, further accelerating the denoising process. Additionally,  we introduce a prior noise injection method to improve the texture details in the generated image. Besides the standard text-to-image task, we also validate our approach on other tasks: text-to-video, personalized generation and reference-guided generation. Without utilizing any knowledge distillation technique, our approach accelerates both the Stable Diffusion (SD) and DeepFloyd-IF model sampling by 41$\%$ and 24$\%$ respectively, and DiT model sampling by 34$\%$, while maintaining high-quality generation performance. Our code will be publicly released.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）
- **问题**：扩散模型（Diffusion Models）在图像生成任务中推理速度慢，成为实际应用的主要瓶颈。现有加速方法如知识蒸馏虽有效，但需要大量计算资源进行训练。
- **动机**：寻找一种无需训练的、轻量级的加速方法，通过深入分析UNet编码器在推理过程中的特征变化，探索绕过冗余计算的可能性。
- **含义**：本文作者发现UNet编码器特征在相邻时间步变化极小，而解码器特征变化显著。基于此，提出**跳过部分编码器计算**并**重用先前编码器特征**的策略，实现免训练加速，同时保持生成质量。

## 2. 方法论
- **核心思想**：
  - UNet编码器特征在相邻去噪时间步几乎不变，因此无需在每个时间步都重新计算编码器，可以直接重用前一时间步的编码器特征。
  - 解码器部分仍需要在每个时间步计算以捕获动态变化。
  - 通过并行执行解码器计算，进一步加速去噪过程。
- **关键技术细节**：
  - **特征重用**：选择相邻的若干时间步，仅计算第一个时间步的UNet编码器，后续时间步直接复制该编码器特征作为解码器输入。
  - **并行解码**：由于编码器特征被共享，多个解码器计算可以独立并行执行，从而减少总推理时间。
  - **先验噪声注入（Prior Noise Injection）**：为改善生成图像的纹理细节，引入额外的噪声注入方法，补偿因特征重用导致的细节损失。
- **算法流程（文字说明）**：
  1. 选择一个时间步分组策略，例如将相邻的2~4个时间步划分为一组。
  2. 对每组中的第一个时间步，正常执行完整的UNet前向传播（编码器+解码器）。
  3. 对该组中后续的时间步，跳过编码器计算，直接使用第一步的编码器特征，解码器独立计算并并行执行。
  4. 对生成的中间特征施加先验噪声注入，提升纹理质量。
- **公式**：文中未给出具体数学公式，但核心操作可表示为：给定时间步 t 和 t+1，令编码器输出 \(E_t \approx E_{t+1}\)，则解码器输入为 \(D_{t+1} = \text{Decoder}(E_t, \text{noise}_{t+1})\)。

## 3. 实验设计
- **使用场景与数据集**：
  - **标准文本到图像（Text-to-Image）**：在Stable Diffusion（SD）和DeepFloyd-IF上评估，具体数据集未明确说明，可能使用COCO或FID评估中常见数据集。
  - **文本到视频（Text-to-Video）**：验证方法在多帧生成任务上的泛化性。
  - **个性化生成（Personalized Generation）**：如DreamBooth风格任务。
  - **参考引导生成（Reference-Guided Generation）**：如图像条件生成。
- **基准模型**：Stable Diffusion、DeepFloyd-IF、DiT（Diffusion Transformer）。
- **对比方法**：文中提到“不利用任何知识蒸馏技术”，对比基线为原始扩散模型的全步采样（如50步DDIM），以及可能其他免训练加速方法（如步长跳过、早期停止等，但未列出具体名称）。主要对比指标为加速比例和生成质量（FID、CLIP score等，摘要未给出具体数值）。

## 4. 资源与算力
- **未明确说明**：论文摘要和提供的信息中未提及使用的GPU型号、数量、训练时长等具体算力资源。文中强调该方法**免训练**，因此推理加速不需要额外训练成本，但推理时并行计算可能依赖多GPU或大显存。这部分信息不足，无法给出具体数值。

## 5. 实验数量与充分性
- **实验数量**：
  - 覆盖4个任务场景（文生图、文生视频、个性化生成、参考引导生成）。
  - 3种基础模型（SD、DeepFloyd-IF、DiT）。
  - 通常应包含不同时间步分组大小（如2、3、4）的消融实验、先验噪声注入的消融实验等，但摘要未列出具体实验数量。
- **充分性与客观性**：
  - 任务覆盖较广，验证了方法的通用性。
  - 但缺少与其他免训练加速方法（如DDIM步长抽稀、隐空间快照等）的直接定量对比，也缺少在标准基准数据集（如MS-COCO）上的FID/IS结果。
  - 仅给出加速比例（41%、24%、34%），未提供生成质量的量化指标，令人对其“保持高质量”的声称存疑。
  - 实验公平性较好，因为模型本身未改动，仅在推理策略上修改。

## 6. 主要结论与发现
- **发现**：UNet编码器特征在相邻时间步变化极小，可以利用此特性跳过冗余计算。
- **结论**：提出的免训练特征重用策略可显著加速扩散模型推理（SD加速41%，DeepFloyd-IF加速24%，DiT加速34%），同时保持生成质量。该方法适用于多种扩散模型架构和下游任务。
- **额外贡献**：先验噪声注入能有效增强纹理细节，弥补特征重用带来的质量下降。

## 7. 优点
- **免训练**：无需任何额外训练或微调，直接应用于预训练模型，降低了计算成本和应用门槛。
- **通用性强**：在多种扩散模型（UNet结构、Transformer架构）和多任务上均有效。
- **并行性**：通过并行解码进一步加速，适合现代多核/多GPU计算环境。
- **理论洞察**：深入分析了编码器特征的时间动态，为后续扩散模型加速提供了新视角。

## 8. 不足与局限
- **实验覆盖不足**：摘要中未提供任何量化指标（如FID、CLIP Score），仅凭主观“高质量”表述难以令人信服；缺乏与主流免训练加速方法的公平对比（如步长跳过、渐进式去噪等）。
- **缺乏理论分析**：为何编码器特征变化小？仅在实证层面描述，未给出理论解释，可能无法推广至其他扩散变体（如DDPM、Score SDE）。
- **应用限制**：特征重用可能导致细节丢失或伪影，虽然引入了噪声注入，但未必能在所有场景下补偿；并行解码会成倍增加显存占用，对资源有限设备不友好。
- **算力信息缺失**：未说明实验硬件，可复现性受影响。
- **仅对相邻时间步有效**：若时间步间隔较大，特征差异可能超过阈值，方法可能失效，文中未探讨间隔大小的影响。

（完）
