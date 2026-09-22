# 日报 · 2026-09-22

- 生成时间：2026-09-22 22:40:14 UTC
- 当次推荐总数：10
- 精读区：3
- 速读区：7

## 今日简报（AI）
今日从10篇候选中精读3篇、速读7篇，主线集中在扩散模型推理加速与长上下文稀疏注意力优化。

最值得看的是9.0分的《SparkDiffusion》——用统一框架缓解高稀疏陷阱、实现单GPU最高265倍视觉生成加速，以及8.0分的tau-leaping调度优化，为掩码离散扩散的步长安排提供新思路。

普通读者可先从SparkDiffusion的加速结论入手，再顺着Elastic Threshold Attention、RBS-Attention等速读工作了解长上下文解码的稀疏化落地场景。

## 精读区
1. [SparkDiffusion: Mitigating the High-Sparsity Trap --- A Unified Framework for up to $265\times$ Single-GPU Acceleration of Visual Generation](/202609/22/2609.23153v1-sparkdiffusion-mitigating-the-high-sparsity-trap-----a-unified-framework-for-up-to-265times-single-gpu-acceleration-of-visual-generation) （9.0/10）
2. [Schedule optimization for tau-leaping in masked discrete diffusion](/202609/22/2609.21960v1-schedule-optimization-for-tau-leaping-in-masked-discrete-diffusion) （8.0/10）
3. [Block-Sparse Attention with Semantic-Geometric Decoupled Routing](/202609/22/2609.22884v1-block-sparse-attention-with-semantic-geometric-decoupled-routing) （8.0/10）

## 速读区
1. [Elastic Threshold Attention: Learned Contextual Sparsity for Long-Context Decoding](/202609/22/2609.20888v1-elastic-threshold-attention-learned-contextual-sparsity-for-long-context-decoding) （7.0/10）
2. [RBS-Attention: Radius-Bounded Sparse Prefill for Long-Context Large Language Models](/202609/22/2609.20971v1-rbs-attention-radius-bounded-sparse-prefill-for-long-context-large-language-models) （7.0/10）
3. [Leveraging Inference-Time Compute for Diffusion Models via Global Scheduling of Denoising Trajectories](/202609/22/2609.22867v1-leveraging-inference-time-compute-for-diffusion-models-via-global-scheduling-of-denoising-trajectories) （7.0/10）
4. [Quantization-Aware Kalman Estimation for Diffusion Sampling](/202609/22/2609.21407v1-quantization-aware-kalman-estimation-for-diffusion-sampling) （6.0/10）
5. [The Weight Is Over - Interactive Diffusion on Consumer GPUs](/202609/22/2609.21849v1-the-weight-is-over---interactive-diffusion-on-consumer-gpus) （6.0/10）
6. [SPLASH: Co-Designing Sparse Attention with High-Bandwidth Flash for Efficient Long-Context Inference](/202609/22/2609.23816v1-splash-co-designing-sparse-attention-with-high-bandwidth-flash-for-efficient-long-context-inference) （6.0/10）
7. [ARM: Attention with Routed-Memory for Learnable Sparse Control](/202609/22/2609.24417v1-arm-attention-with-routed-memory-for-learnable-sparse-control) （6.0/10）

---
使用键盘方向键可在日报/论文之间快速切换。
