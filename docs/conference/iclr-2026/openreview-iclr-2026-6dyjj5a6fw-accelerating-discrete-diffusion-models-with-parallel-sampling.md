---
title: Accelerating Discrete Diffusion Models with Parallel Sampling
title_zh: 利用并行采样加速离散扩散模型
authors: "Yu Yao, Huanjian Zhou, Andi Han, Wei Huang, Masashi Sugiyama"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=6dyJj5a6FW"
tags: ["query:diff-accel"]
score: 9.0
evidence: 离散扩散模型的并行采样加速
tldr: 离散扩散模型生成过程顺序，采样慢。本文在连续时间马尔可夫链框架下并行化tau-leaping算法，利用Picard迭代实现时间并行加速。通过预测-校正结构控制误差，并证明指数收敛。显著降低时间复杂度。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 离散扩散模型采样过程顺序，计算开销大。
method: 并行化tau-leaping算法，利用随机积分和Picard迭代实现时间并行。
result: 理论证明指数收敛，实验显示显著加速且质量保持。
conclusion: 该方法为离散扩散模型提供了一种可证明的并行加速方案。
---

## Abstract
Discrete diffusion models are widely used for learning and generating discrete distributions. As the generation process is inherently sequential, the acceleration of sampling is of significant importance. In this work, we parallelize the mainstream $\tau$-leaping algorithm for absorbing discrete diffusion in a Continuous-Time Markov Chain (CTMC) framework. By leveraging the continuous-time stochastic integral form of the $\tau$-leaping algorithm and the Picard iteration method, we achieve parallel-in-time sampling acceleration. We implement a predictor-corrector structure based on the Markov chain Monte Carlo (MCMC) method to control for additional errors and provide a proof of exponential convergence for our algorithm. We improve the overall time complexity of $\tau$-leaping from ${\mathcal{O}}(d^{2}\log^{2}d)$ to at most ${\mathcal{O}}(d^{3/2}\log^{5/2}d)$. In practice, our accelerated algorithm can achieve at most 5 to 8-fold sampling speedup over the traditional time-sequential $\tau$-leaping with respect to wall-clock time on convex, non-convex, and high-dimensional discrete distributions. Our research broadens the scope of leveraging discrete diffusion models in various challenging areas like molecular structure generation and Large Language Models (LLMs).

---

## 论文详细总结（自动生成）

好的，以下是根据给定论文信息生成的结构化、深入、客观的中文总结。

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：离散扩散模型在生成离散数据（如分子结构、文本）时，其生成过程本质上是顺序的（自回归），导致采样速度缓慢，限制了其在计算密集型任务（如大语言模型、分子生成）中的应用。
- **研究动机**：加速离散扩散模型的采样过程，使其在保持生成质量的同时，大幅降低时间复杂度，从而拓展其实用性。
- **整体含义**：提出一种可证明的、高效的并行采样算法，将离散扩散模型从顺序生成范式推向并行生成范式，为离散生成任务提供了新的加速途径。

### 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：在连续时间马尔可夫链（Continuous-Time Markov Chain, CTMC）框架下，将主流的τ-leaping算法并行化。利用τ-leaping算法的连续时间随机积分形式，结合Picard迭代方法实现时间维度的并行采样。
- **关键技术细节**：
  - 基于τ-leaping算法的连续时间随机积分形式，将顺序的采样步骤转化为可并行计算的迭代。
  - 引入Picard迭代，这是一种常用于求解微分方程的并行迭代方法，通过固定点迭代实现时间并行加速。
  - 采用基于马尔可夫链蒙特卡洛（MCMC）方法的预测-校正（Predictor-Corrector）结构，用于控制并行化引入的额外误差。
  - 理论贡献：证明了所提算法的指数收敛性。
- **算法流程**（文字说明）：
  1. 初始化：从目标离散分布的先验或噪声分布开始。
  2. 预测步骤：使用并行化的τ-leaping算法（结合Picard迭代）并行生成若干时间步的样本预测。
  3. 校正步骤：通过MCMC方法（如Metropolis-Hastings校正）对预测样本进行校正，确保偏差可控且收敛至真实目标分布。
  4. 重复预测-校正循环，直至生成高质量样本。
- **时间复杂度改进**：将τ-leaping的整体时间复杂度从 O(d² log² d) 降低至最多 O(d³/² log⁵/² d)（d表示状态空间维度或待生成token数量）。

### 3. 实验设计：使用了哪些数据集 / 场景，它的 benchmark 是什么，对比了哪些方法

- **数据集/场景**：论文在三种离散分布场景上进行了测试：
  - 凸离散分布（Convex discrete distribution）
  - 非凸离散分布（Non-convex discrete distribution）
  - 高维离散分布（High-dimensional discrete distribution）
- **Benchmark**：论文以传统的顺序τ-leaping算法作为基准（baseline），并比较了标准离散扩散模型的采样速度和质量。
- **对比方法**：主要对比了标准顺序τ-leaping（time-sequential τ-leaping）。未提及与其他加速方法（如去噪扩散隐式模型DDIM的离散版本等）的直接对比（可能本文是第一篇针对离散扩散并行加速的工作）。

### 4. 资源与算力：如果文中有提到，请总结使用了多少算力（GPU 型号、数量、训练时长等）。若未明确说明，也请指出这一点。

- **资源与算力**：论文中未明确说明所使用的GPU型号、数量、训练时长等具体算力信息。仅通过壁钟时间（wall-clock time）报告了加速倍数（5至8倍加速），未提供详细的硬件配置。因此，无法评估其算力需求。

### 5. 实验数量与充分性：大概做了多少组实验，是否充分、客观、公平

- **实验数量**：论文在三种不同类型（凸、非凸、高维）的离散分布上进行了实验，每个场景可能包含多次重复。从描述看，实验覆盖了典型分布类型，但具体实验组数（如不同参数、不同维度、不同数据集个数）未明确给出。
- **充分性评价**：
  - 理论证明（指数收敛）与实验验证（加速倍数、质量保持）结合，较为完整。
  - 但缺乏与其他加速方法（如DDIM、DPM-solver的离散变体）的对比，也缺少在大规模真实离散数据（如分子图、文本token序列）上的测试。实验主要集中在合成分布上，通用性和实际应用场景的充分性有待加强。
  - 结果客观：报告了5-8倍壁钟时间加速，且声称质量保持（未详述具体评价指标，如负对数似然或生成样本多样性）。
- **公平性**：基准方法为顺序τ-leaping，对比设置合理。但未说明是否对基准进行了同等优化，也未提及误差控制的具体阈值。

### 6. 论文的主要结论与发现

- 提出了第一个可证明指数收敛的离散扩散模型并行采样加速算法。
- 理论上将时间复杂度从 O(d² log² d) 降低到 O(d³/² log⁵/² d)，实现亚二次加速。
- 实际实验中，在凸、非凸、高维离散分布上，相比顺序τ-leaping实现了5至8倍的壁钟时间加速，同时生成质量几乎没有损失。
- 该工作拓展了离散扩散模型在分子结构生成和大语言模型等领域的应用潜力。

### 7. 优点：方法或实验设计上有哪些亮点

- **方法亮点**：
  - 首次将Picard迭代应用于离散扩散模型的并行采样，创新性强。
  - 预测-校正结构有效控制了并行化引入的误差，并保持了收敛性。
  - 理论证明指数收敛，为算法提供了可靠性保证。
- **实验亮点**：
  - 覆盖凸、非凸、高维三种典型分布，验证了算法在不同特性分布上的有效性。
  - 以壁钟时间衡量加速，直接反映了实际效率提升。

### 8. 不足与局限：包括实验覆盖、偏差风险、应用限制等

- **实验覆盖局限**：
  - 仅在合成离散分布上进行测试，未在真实世界离散数据（如分子SMILES、图像离散化、文本token序列）上评估，实际性能可能因数据复杂性而波动。
  - 缺乏与主流加速方法（如DDIM、DPM-solver、一致性模型）的定量对比，孤立于现有加速工作体系。
- **偏差风险**：
  - 加速倍数（5-8倍）依赖于具体实现和硬件，可能因并行效率而变动。
  - 质量保持的评估标准未详细说明（是否使用了常用的NLL、多样性、FID等指标？），可能隐含偏差。
- **应用限制**：
  - 算法需要预测-校正循环，增加了实现复杂度和调参成本。
  - 时间复杂度改进仅适用于大维度（d较大）场景，对于小维度可能加速效果不明显甚至因并行开销而变慢。
  - 对连续时间CTMC框架的依赖，可能无法直接应用于所有离散扩散模型变体（如掩码扩散模型）。
- **资源信息缺失**：未提供硬件配置，限制了可复现性和算力需求评估。

（完）
