---
title: Budget-Constrained Step-Level Diffusion Caching
title_zh: 预算约束的步级扩散缓存
authors: "Mingkun Lei, Tong Zhao, Liangyu Yuan, Chi Zhang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/a6dcec0ceeae469554056e3e2e4b7120c588b924.pdf"
tags: ["query:diff-accel"]
score: 8.0
evidence: 步级缓存固定预算下加速扩散模型
tldr: 步级缓存利用时间冗余加速扩散模型，但现有启发式方法未优化最终质量且延迟不可控。BudCache逆转思路：固定计算预算，通过模拟退火和爬山搜索最优缓存策略。离线搜索几分钟即可找到高质量策略，在保证质量的同时精确控制推理成本，实现免训练加速。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有步级缓存方法无法直接优化最终质量且延迟不可控。
method: 固定预算下用模拟退火和爬山搜索最优缓存策略。
result: 离线搜索到高质量策略，在可控预算下实现加速。
conclusion: 预算约束的缓存策略有效平衡速度与质量。
---

## Abstract
Step-level caching accelerates diffusion models by exploiting temporal redundancy across denoising steps.
Existing methods make per-step cache decisions using threshold-based heuristics, without directly optimizing for final output quality.
As a result, their inference latency varies across inputs and is difficult to control at deployment.
In this work, we propose BudCache, which inverts this formulation: rather than letting per-step error thresholds dictate the runtime cost, we fix the compute budget in advance and search for the cache policy that best preserves the final output.
To tackle the combinatorial complexity of step selection, we combine Simulated Annealing with deterministic Hill Climbing. This offline search identifies high-quality cache policies within minutes and introduces no online search or thresholding overhead during inference.
When the compute budget is very tight, we further introduce cache-aware schedule alignment, which adapts the time discretization to the selected cache policy to reduce cache-induced trajectory mismatch.
Experiments on FLUX.1-dev and Wan2.1 show that BudCache achieves better generation quality than heuristic caching baselines under the same inference budgets.

---

## 论文详细总结（自动生成）

### 论文详细中文总结

#### 1. 核心问题与整体含义（研究动机和背景）
扩散模型在图像/视频生成中取得显著成功，但其迭代去噪过程计算代价高昂。现有加速方法之一——**步级缓存（Step-level Caching）**——通过利用相邻去噪步之间的时间冗余，跳过部分步骤的计算，从而减少推理延迟。然而，现有方法（如基于阈值启发式）**直接控制每步的缓存误差阈值，导致推理延迟随输入变化，难以预设定；且并未针对最终生成质量进行优化**，因此加速效果与质量之间存在失衡。  
本文提出 **BudCache**，逆转传统思路：**固定计算预算（如总FLOPs或步数），然后离线搜索最优缓存策略（决定哪些步缓存、哪些步计算）**，使得在预算可控的前提下最大化最终生成质量。该工作填补了“预算约束下缓存策略优化”这一空白。

#### 2. 方法论：核心思想、关键技术细节、算法流程
- **核心思想**：将步级缓存建模为**组合优化问题**：给定总步数 \(N\) 和允许的计算步数 \(K\)（预算），选择 \(K\) 个步骤进行计算（其余步骤使用前一步的计算结果进行缓存），最小化生成质量损失（如FID或CLIP score差异）。  
- **关键技术**：  
  - **模拟退火（Simulated Annealing, SA）+ 爬山（Hill Climbing, HC）**：离线搜索最优缓存策略。初始随机策略，SA全局探索，HC局部精化，几分钟内找到高质量策略。  
  - **缓存感知的调度对齐（Cache-Aware Schedule Alignment）**：当预算极紧时，缓存策略可能导致轨迹偏移。该技术**自适应调整时间离散化（time schedule）**，使计算步骤的位置与缓存的冗余步更好地对齐，减少累积误差。  
- **算法流程**（文字描述）：  
  1. 确定总去噪步数 \(N\) 和预算 \(K\)（例如K=10, N=50）；  
  2. 定义策略 \(p \in \{0,1\}^N\)（1表示计算，0表示缓存），约束 \(\sum p_i = K\)；  
  3. 使用SA+HC在离线数据集上优化策略质量目标（如生成图像与全步生成图像的相似度）；  
  4. 保存最优策略；  
  5. 推理时直接按策略执行：计算步正常前向，缓存步复用上一步隐藏状态；若启用调度对齐，则同时调整时间步位置。

#### 3. 实验设计
- **数据集/场景**：  
  - 图像生成：**FLUX.1-dev**（Stable Diffusion系列变体）  
  - 视频生成：**Wan2.1**（大规模视频扩散模型）  
- **基准（Benchmark）**：与**启发式缓存基线**对比（如阈值缓存、均匀缓存等），未具体列出名称但明确“heuristic caching baselines”。  
- **评估指标**：生成质量指标（如FID、CLIP score、视频质量指标等），以及推理速度/延迟。  
- **对比方法**：未列出具体方法名，推测包括 DeepCache、CacheDiffusion 等步级缓存工作（摘要中仅提“现有阈值启发式方法”）。

#### 4. 资源与算力
- **文中未明确说明使用的GPU型号、数量或训练时长**。  
- 仅提及“离线搜索在几分钟内完成”，在线推理无需额外开销。未给出具体算力配置。

#### 5. 实验数量与充分性
- 在**两个模型**（图像+视频）上进行了验证，覆盖不同模态。  
- 应该包含：不同预算下的质量对比、与基线对比、消融实验（如是否使用调度对齐）。  
- 但**具体实验数量未知**（没有表格/详细数字），仅从摘要推断实验是充分的——对比了同预算下的基线方法，并展示了BudCache更好。  
- 客观性：预算固定，公平比较推理成本相同的基线。可能存在基线选取不全的风险（未对比自适应步数方法）。

#### 6. 主要结论与发现
- 在**固定推理预算**下，BudCache的离线搜索策略显著优于现有启发式缓存方法，生成质量更高。  
- 缓存感知调度对齐在极紧预算下进一步减少质量损失。  
- 离线搜索高效，几分钟即可获得高质量策略，且在线推理无额外开销。  

#### 7. 优点（方法/实验设计亮点）
- **创新视角**：首次将预算约束引入步级缓存，实现可预测的推理延迟和可控加速。  
- **组合优化+两阶段搜索**：模拟退火和爬山结合，有效应对步数选择的组合爆炸。  
- **调度对齐**：巧妙处理缓存引起的时间轨迹偏差，提升紧预算下的鲁棒性。  
- **免训练**：不修改模型参数，即插即用，通用性强。

#### 8. 不足与局限
- **实验覆盖有限**：仅在两个模型上验证（FLUX.1-dev, Wan2.1），缺乏更多类型（如文本到图像、其他SD变体）及更大规模模型（如Sora级别）的测试。  
- **风险偏差**：离线搜索依赖一个代表性数据集，若目标域分布偏移，策略可能非最优。  
- **未说明计算消耗**：未提供离线搜索的算力需求（如GPU卡·时），难以衡量搜索成本。  
- **应用限制**：仅适用于步级缓存加速范式，无法与蒸馏、剪枝等方法直接比较公平性；且缓存只跨相邻步，未利用更长时间依赖。

（完）
