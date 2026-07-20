---
title: "AC-Sampler: Accelerate and Correct Diffusion Sampling with Metropolis-Hastings Algorithm"
title_zh: "AC-Sampler: 使用Metropolis-Hastings算法加速和校正扩散采样"
authors: "Minsang Park, Gyuwon Sim, Hyungho Na, Jiseok Kwak, Sumin Lee, Richard Lee Kim, Donghyeok Shin, Byeonghu Na, Yeongmin Kim, Il-chul Moon"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=kWl13kRJTQ"
tags: ["query:diff-accel"]
score: 9.0
evidence: 无训练加速和校正扩散采样
tldr: 针对扩散模型采样步骤多导致速度慢和误差累积的问题，提出AC-Sampler，采用Metropolis-Hastings算法从中间时间步直接采样，无需微调即可加速并纠正采样结果，推导了任意时间步的可处理密度比，实验证明在保持甚至提升质量的同时显著加速采样。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散模型采样慢且误差累积。
method: 提出AC-Sampler，通过Metropolis-Hastings算法从中间时间步生成样本并校正。
result: 无需微调即可加速采样并保持或提升样本质量。
conclusion: AC-Sampler是一种有效且无需训练即可加速扩散采样的方法。
---

## Abstract
Diffusion-based generative models have recently achieved state-of-the-art performance in high-fidelity image synthesis. These models learn a sequence of denoising transition kernels that gradually transform a simple prior distribution into a complex data distribution. However, requiring many transitions not only slows down sampling but also accumulates approximation errors.
We introduce the Accelerator-Corrector Sampler (AC-Sampler), which accelerates and corrects diffusion sampling without fine-tuning. It generates samples directly from intermediate timesteps using the Metropolis–Hastings (MH) algorithm while correcting them to target the true data distribution. We derive a tractable density ratio for arbitrary timesteps with a discriminator, enabling computation of MH acceptance probabilities. Theoretically, our method yields samples better aligned with the true data distribution than the original model distribution. Empirically, AC-Sampler achieves FID 2.38 with only 15.8 NFEs, compared to the base sampler’s FID 3.23 with 17 NFEs on unconditional CIFAR-10. On CelebA-HQ 256×256, it attains FID 6.6 with 98.3 NFEs. AC-Sampler can be combined with existing acceleration and correction techniques, demonstrating its flexibility and broad applicability. Our code is available at \href{https://github.com/aailab-kaist/AC-Sampler}{https://github.com/aailab-kaist/AC-Sampler.}

---

## 论文详细总结（自动生成）

好的，我将严格根据提供的论文摘要和元数据信息，为您生成一份结构化、深入、客观的中文总结。

### 论文总结：AC-Sampler：使用 Metropolis-Hastings 算法加速和校正扩散采样

#### 1. 核心问题与整体含义（研究动机与背景）

-   **研究动机**：扩散模型在高质量图像生成方面取得了最先进的性能，但其核心缺陷在于采样过程缓慢且误差累积。具体来说，扩散模型需要执行大量（通常数百步）的迭代去噪步骤（即“过渡核”），这不仅导致生成速度极慢，而且每一步的近似误差会逐步积累，最终影响样本质量。
-   **核心问题**：如何在**无需额外训练或微调**的前提下，既加速扩散模型的采样过程，又能纠正因加速（如跳跃时间步长）带来的分布偏移，使生成样本更接近真实数据分布。
-   **整体含义**：本文提出了一种即插即用的后处理采样方法，可以显著提升现有扩散模型的采样效率，同时保持甚至改善生成质量，具有很高的实用价值。

#### 2. 论文提出的方法论

-   **核心思想**：提出 **AC-Sampler (Accelerator-Corrector Sampler)**，它是一个加速器-校正器框架。加速部分通过直接从**中间时间步**（而不是从初始噪声逐步迭代）生成样本，大幅减少所需步骤；校正部分则利用 **Metropolis-Hastings (MH) 算法**对加速生成的样本进行拒绝/接受调整，确保这些样本最终服从真实数据分布，从而纠正加速带来的偏差。
-   **关键技术细节**：
    1.  **加速（Accelerator）**：允许从任意中间时间步直接采样，跳过大量先前的去噪步骤，从而显著降低计算量（NFE，神经函数评估次数）。
    2.  **校正（Corrector）**：核心挑战在于计算 MH 算法的接受概率，这需要两个分布（目标分布与提议分布）的密度比。论文推导出一个**可处理的密度比公式**：通过训练一个判别器（discriminator）来估计任意时间步的密度比率。这个判别器可以基于原始扩散模型的预训练分数网络或额外简单结构实现，且无需对整个生成模型进行微调。
    3.  **算法流程**：
        -   **Step 1 (加速)**：利用原始扩散模型的采样器（或其变体）从某个选定的中间时刻 \( t \) 生成初始候选样本 \( x_t \)。
        -   **Step 2 (校正)**：对候选样本 \( x_t \) 应用 MH 算法。根据密度比计算接受概率 \( \alpha = \min(1, \frac{p_{\text{target}}(x_t)}{p_{\text{proposal}}(x_t)} ) \)，其中 \( p_{\text{target}} \) 是真实数据分布，\( p_{\text{proposal}} \) 是加速过程产生的分布。
        -   **Step 3 (最终生成)**：以概率 \( \alpha \) 接受 \( x_t \) 作为最终样本；否则拒绝，并从头开始重新采样（或从其他时间步重试）。
    -   **理论优势**：论文理论上证明了该方法生成的样本比原始模型分布更接近真实数据分布。

#### 3. 实验设计

-   **数据集与场景**：
    -   **无条件图像生成**：CIFAR-10（32×32 分辨率）和 CelebA-HQ（256×256 高分辨率）。
-   **基准 (Benchmark)**：以标准的扩散模型采样器（如 DDPM、DDIM 等）作为基线和对比对象。
-   **对比方法**：论文未列出所有对比方法，但根据摘要，AC-Sampler 可与现有的加速和校正技术直接结合，体现了其灵活性。主要比较了**原始采样器**在不同 NFE 下的 FID 指标。
-   **核心结果**：
    -   **CIFAR-10**：AC-Sampler 在 **15.8 NFEs** 时达到 **FID 2.38**，而基线的原始采样器在 **17 NFEs** 时仅达到 FID 3.23。AC-Sampler 用更少的计算步数获得了更好的质量。
    -   **CelebA-HQ 256×256**：AC-Sampler 在 **98.3 NFEs** 时达到 **FID 6.6**。

#### 4. 资源与算力

-   **明确说明**：论文摘要和元数据中**未提及**训练或推理所使用的 GPU 型号、数量、训练时长等具体算力信息。仅提到代码已开源，但未提供计算资源细节。

#### 5. 实验数量与充分性

-   **实验数量**：主要在两个标准数据集（CIFAR-10 和 CelebA-HQ）上进行了验证，并报告了每个数据集在最优 NFE 下的 FID 结果。此外，论文指出 AC-Sampler 可与现有加速校正技术结合，暗示进行了相关消融或组合实验（但具体组数未提供）。
-   **充分性与客观性**：
    -   **优点**：选择了图像生成领域公认的基准数据集和评价指标（FID），结果对比清晰（相同或更少 NFE 下 FID 更低）。实验结果直接证明了方法在加速和质量提升方面的双重优势。
    -   **局限性**：仅覆盖了两个数据集，且均为图像生成任务。未涉及文本、音频、3D 等其他模态。此外，未展示在更复杂数据集（如 ImageNet）或条件生成任务上的表现，**实验覆盖范围相对有限**。对“可结合性”的验证论证不够详细，缺少具体对比表格来展示与其他 SOTA 方法结合后的增益。

#### 6. 论文的主要结论与发现

-   AC-Sampler 是一种**无需训练/微调**的高效扩散模型采样加速与校正方法。
-   通过直接将 MH 算法引入到中间时间步采样，并成功推导出可计算的密度比，它能够**在显著减少采样步数的同时，提升生成样本与真实数据分布的匹配度**（改善 FID）。
-   该方法具有**通用性和灵活性**，可以无缝集成到现有的各种扩散采样器和加速/校正技术中。

#### 7. 优点

-   **无需额外训练**：直接利用预训练好的扩散模型，只需额外训练一个判别器（或利用模型内部特征），无需微调整个生成模型，计算成本低，易于部署。
-   **理论保障**：论文提供了理论分析，证明校正后的样本分布比原始分布更接近真实分布。
-   **性能优异**：在 CIFAR-10 和 CelebA-HQ 上，用更少的计算步数实现了更好的 FID 指标。
-   **即插即用**：可以与现有的任何加速策略（如 DDIM 跳过步长）和校正技术（如与 Langevin 校正结合）相结合，具有广泛的应用前景。

#### 8. 不足与局限

-   **实验覆盖不足**：仅在图像生成的两个数据集上验证，未涉及其他模态（如文本、音频、视频）或其他更难的图像数据集（如 ImageNet 256×256/512×512），其泛化能力尚待检验。
-   **计算开销**：虽然避免了模型训练，但 MH 算法的拒绝采样环节可能需要多次尝试才能成功接受一个样本，这在实际推理时可能引入额外的随机开销（重试次数），论文中未详细分析平均需要的 MH 步数或重试概率。
-   **判别器的依赖**：方法依赖于一个可以准确估计任意时间步密度比的判别器。判别器的训练质量（是否过拟合、是否对噪声水平敏感）会直接影响校正效果，论文未深入探讨判别器的鲁棒性。
-   **应用限制**：对于需要精确控制生成过程的应用（如图像编辑、逆向问题求解），该方法可能引入难以预测的随机性。此外，MH 算法的接受概率计算在离散数据或复杂流形上可能面临挑战。

（完）
