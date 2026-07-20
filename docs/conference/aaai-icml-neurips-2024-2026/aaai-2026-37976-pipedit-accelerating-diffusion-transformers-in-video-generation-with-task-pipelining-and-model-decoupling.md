---
title: "PipeDiT: Accelerating Diffusion Transformers in Video Generation with Task Pipelining and Model Decoupling"
title_zh: PipeDiT：通过任务流水线和模型解耦加速视频生成中的扩散Transformer
authors: "Sijie Wang, Qiang Wang, Shaohuai Shi"
date: 2026-03-17
pdf: "https://ojs.aaai.org/index.php/AAAI/article/download/37976/41938"
tags: ["query:diff-accel"]
score: 7.0
evidence: 通过任务流水线和模型解耦加速视频扩散模型推理
tldr: 提出PipeDiT框架，针对视频扩散Transformer，利用序列并行流水线和模型解耦，在多GPU上加速推理，降低延迟和内存。
source: AAAI-2026-Accepted
selection_source: conference_retrieval
figures_json: "[{\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37976/fig-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 871, \"height\": 547, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37976/fig-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 885, \"height\": 292, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37976/fig-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 876, \"height\": 577, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37976/fig-004.webp\", \"caption\": \"\", \"page\": 0, \"index\": 4, \"width\": 1649, \"height\": 766, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-37976/fig-005.webp\", \"caption\": \"\", \"page\": 0, \"index\": 5, \"width\": 876, \"height\": 460, \"label\": \"Figure\"}]"
tables_json: "[{\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-37976/table-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 1836, \"height\": 715, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-37976/table-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 1849, \"height\": 514, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-37976/table-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 1807, \"height\": 589, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-37976/table-004.webp\", \"caption\": \"\", \"page\": 0, \"index\": 4, \"width\": 857, \"height\": 533, \"label\": \"Table\"}]"
motivation: 视频扩散Transformer推理慢、内存消耗大。
method: 设计流水线算法PipeSP和VAE解耦模块DeDiVAE，实现多GPU并行加速。
result: 在多GPU设置下显著降低推理延迟和内存占用。
conclusion: 流水线和模型解耦是加速视频扩散推理的有效方法。
---

## Abstract
Video generation has been advancing rapidly, and diffusion transformer (DiT) based models have demonstrated remarkable capabilities. However, their practical deployment is often hindered by slow inference speeds and high memory consumption. In this paper, we propose a novel pipelining framework named PipeDiT to accelerate video generation, which is equipped with three main innovations. First, we design a pipelining algorithm (PipeSP) for sequence parallelism (SP) to enable the computation of latent generation and communication among multiple GPUs to be pipelined, thus reducing the inference latency. Second, we propose DeDiVAE to decouple the diffusion module and the VAE module into two GPU groups whose executions can also be pipelined to reduce the memory consumption and inference latency. Third, to better utilize the GPU resources in the VAE group, we propose an attention co-processing (Aco) method to further reduce the overall video generation latency. We integrate our PipeDiT into both OpenSoraPlan and HunyuanVideo, two state-of-the-art open-source video generation frameworks, and conduct extensive experiments on two 8-GPU systems. Experimental results show that, under many common resolution and timestep configurations, our PipeDiT achieves 1.06× to 4.02× speedups over OpenSoraPlan and HunyuanVideo.

---

## 论文详细总结（自动生成）

# 论文总结：PipeDiT：通过任务流水线和模型解耦加速视频生成中的扩散Transformer

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：基于扩散Transformer（DiT）的视频生成模型在推理时存在两个主要瓶颈：一是去噪阶段的多步迭代导致高延迟，二是VAE解码阶段内存消耗巨大，容易导致显存溢出（OOM）。现有系统级优化（如序列并行SP中的Ulysses）存在计算与通信串行、GPU利用率低的问题；而模型卸载（offloading）虽节省内存但引入大量CPU-GPU传输开销。因此，需要一种在不牺牲视频质量的前提下，同时降低延迟和内存占用的**系统级推理加速框架**。

- **整体含义**：本文提出PipeDiT，通过**任务流水线**和**模型解耦**，在保持生成质量不变（算法等价）的条件下，显著提升多GPU推理效率，适用于实际视频生成服务场景（多提示词连续生成）。

## 2. 论文提出的方法论

### 核心思想
- 将序列并行（SP）中的通信与计算重叠（PipeSP）；
- 将扩散模块和VAE解码器解耦到不同GPU组，实现模块级流水线（DeDiVAE）；
- 利用VAE GPU组的空闲时间协作执行扩散注意力计算（Aco），进一步提高资源利用率。

### 关键技术细节

#### （1）PipeSP：流水线序列并行
- **动机**：标准Ulysses SP中，每个GPU先计算本地Q、K、V，然后执行三个All-to-All通信交换全序列、部分头，计算所有注意力头后再进行一次All-to-All。计算和通信串行，GPU空闲。
- **方法**：将注意力头划分为多个独立的单头处理单元。每计算完一个头的注意力结果，立即触发All-to-All通信，使通信与后续头的计算重叠。最后通过`view → permute → view`变换恢复张量布局。
- **算法流程**（文字说明）：
  1. 将Q、K、V按注意力头维度分块；
  2. 对每个头j：计算单头注意力，记录事件，等待所有GPU完成该头计算后，执行All-to-All通信；
  3. 收集所有头的结果，进行张量重排（view(-1, h, n, D)、permute(0,2,1,3)、view(-1, n*h, D)）以匹配原始布局。

#### （2）DeDiVAE：扩散–VAE模块解耦
- **动机**：扩散模型和VAE解码器共存在同一GPU上导致高内存占用，且两者串行执行。
- **方法**：将N个GPU分为**去噪组**（N_denoise个GPU）和**解码组**（N_decode个GPU）。扩散模型参数存于去噪组，VAE解码器参数存于解码组。去噪完成后将潜在表示发送至解码组解码。
- **流水线**：利用多提示词服务，将第一个提示的解码与第二个提示的去噪重叠，使两组GPU都能保持忙碌。
- **最优GPU划分**：平衡条件：\( \frac{T_{\text{denoise}}}{N_{\text{denoise}}} \approx T_{\text{decode}} \)，推导出 \( N_{\text{decode}} \approx \left\lfloor \frac{T_{\text{decode}}}{T_{\text{decode}}+T_{\text{denoise}}} N \right\rfloor \)，以最大化重叠。由于PipeSP隐藏了大部分通信开销，可忽略T_comm。

#### （3）Aco：注意力协同处理
- **动机**：解码组在无解码任务时空闲（去噪阶段），造成浪费。
- **方法**：将DiT块的线性投影（Q,K,V计算）保留在去噪组，而注意力核（Attn(Q,K,V)）可以转移到解码组执行。去噪组先进行组内All-to-All，然后通过P2P发送Q,K,V给解码组，两组并行计算注意力，之后去噪组聚合结果。当解码组有解码任务时，去噪组独立完成注意力计算。
- **应对头数不可分情况**：若头数H不能被N_denoise整除，对于仅使用Ulysses的模型（如OpenSoraPlan）需填充；对于支持USP的模型（如HunyuanVideo），可切换到Ring-Attention以避免填充开销。
- **理论加速比**：设t_L为线性投影时间，t_A为注意力时间，则基线延迟T_baseline=t_L+t_A；Aco延迟T_coop=t_L+t_A * (N_denoise/N)。加速比S = T_baseline/T_coop。

## 3. 实验设计

### 使用的模型与框架
- **OpenSoraPlan v1.3.0**（2B参数）
- **HunyuanVideo**（13B参数）
两者均为开源SOTA视频生成框架，分别使用Ulysses SP和USP（Unified SP）。

### Benchmark场景
- 不同视频分辨率：480×352、640×352、800×592、1024×576（帧数多为97，部分测试65、129帧）
- 不同扩散步长：10、30、50
- 多提示词生成：每次10个连续提示词
- 测试多个配置组合（如表1、表2、表3所示）

### 对比方法
- **基线（base）**：原始框架使用offloading策略。
- **PipeDiT（opt）**：包含PipeSP + DeDiVAE + 可选Aco。
- 消融实验对比了A/B/C/D四种组合：
  - A：基线offloading
  - B：DeDiVAE
  - C：PipeSP
  - D：Aco

### 性能指标
- 每步延迟（per-timestep latency）
- 整体生成10个视频的总延迟
- 峰值GPU内存
- 加速比（speedup）

## 4. 资源与算力

- **硬件**：两个8-GPU系统：
  - 8× NVIDIA RTX A6000 48GB（NVLink互联）
  - 8× NVIDIA L40 48GB
- **文中未明确说明训练时长**，仅讨论推理优化，无训练过程。
- 文中指出A6000具有更高通信带宽（NVLink），因此PipeDiT在A6000上的加速效果优于L40。

## 5. 实验数量与充分性

- **实验组数**：端到端性能表（表1）涵盖2个框架 × 2个硬件平台 × 4种分辨率 × 3种步长 = 24组（实际展示了部分结果，扩展版本含更多）。PipeSP单独评估表（表2）8种配置。DeDiVAE内存对比（表4）针对3种分辨率。消融研究（表3）针对3种分辨率、2个框架。热力图（图5）对比Aco有无的延迟差异。
- **充分性**：覆盖了不同分辨率、帧数、步长、硬件、模型大小，进行多组对比。消融实验系统分析了每个组件贡献。实验设计较为全面，结果客观。但缺少与其他同类加速方法（如DistriFusion、PipeFusion、TeaCache等）的直接对比，仅与各自基线（offloading）比较，略显不足。

## 6. 论文的主要结论与发现

- PipeDiT在多数配置下取得**1.06×至4.02×**的加速比（端到端生成10个视频）。
- PipeSP在中等分辨率（如640×352×129）下表现最佳（最多15%每步加速），过低或过高分辨率收益下降。
- DeDiVAE显著降低峰值内存（OpenSoraPlan上最高降低41.5%），避免OOM；但在HunyuanVideo中因文本编码器共置导致内存略高于offloading（但能生成更高分辨率视频）。
- Aco在高计算负载（大步长、高分辨率）下进一步改善延迟；在低负载时可能无增益甚至轻微倒退，但通过条件启动可以规避。
- 整体框架**不改变生成质量**（算法等价），因此质量保持与基线一致。

## 7. 优点

- **系统性优化**：从计算-通信重叠、模块级流水线、协同计算三个层面对视频生成推理进行端到端加速，思路清晰且实用。
- **保留生成质量**：所有优化不改变数学结果，视频质量无损失，适合工业部署。
- **多提示词流水线**：设计的DeDiVAE与Aco特别适用于多查询的服务场景，通过并行解码与去噪实现高吞吐。
- **适应不同模型架构**：能处理Ulysses和USP两种SP方案，并针对头数不可分情况提供两种策略。
- **全面的消融实验**：清楚分解每个组件的贡献，展示协同效应。

## 8. 不足与局限

- **实验对比的局限性**：仅与各自基线（offloading）比较，未与其他现有并行推理加速方法（如DistriFusion/PipeFusion、Cache方法等）直接对比，削弱了方法的相对优势证明。
- **硬件限制**：评估仅限8卡环境，未探讨更大规模（如16/32 GPU）下的扩展性；未评估单GPU场景。
- **Aco适用条件**：Aco仅在解码组空闲且注意力头数可整除或支持切换时有效，否则引入填充开销；低负载下可能无效甚至退化。
- **HunyuanVideo内存优化效果不明显**：DeDiVAE在HunyuanVideo上内存下降比率较低（13.6% vs 卸载的38.8%），主要由于文本编码器共置于解码组，表明方法依赖模型结构。
- **未讨论更广泛的数据集与长视频**：实验主要采用固定文本提示（10个），未评估连续生成大量提示或超长视频（>129帧）的场景。
- **未提供代码可复现性声明**：虽基于开源框架，但未明确提供PipeDiT实现代码，限制了第三方验证。

（完）
