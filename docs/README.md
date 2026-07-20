<div class="dpr-home-notice-card dpr-home-panel">
  <div class="dpr-home-notice-header dpr-home-panel-header">
    <h3 class="dpr-home-notice-title">公告与更新</h3>
    <a class="dpr-home-notice-tutorial" href="#/tutorial/README">使用教程 <span aria-hidden="true">›</span></a>
  </div>
  <div class="dpr-home-notice-entry">
    <time class="dpr-home-notice-date" datetime="2026-07-19">07.19</time>
    <div>
      <strong class="dpr-home-notice-entry-title">首页新增社区统计</strong>
      <span class="dpr-home-notice-entry-summary">现在可以看到今天看论文的人数和项目加入人数。</span>
    </div>
  </div>
  <div class="dpr-home-site-stats" data-dpr-site-stats hidden aria-live="polite">
    <span>今天有 <strong class="dpr-home-site-stat-value" data-dpr-daily-readers>--</strong> 人在看论文</span>
    <span class="dpr-home-site-stat-separator" aria-hidden="true">·</span>
    <span>已有 <strong class="dpr-home-site-stat-value" data-dpr-fork-count>--</strong> 人加入 Daily Paper Reader</span>
  </div>
</div>

## 每次日报
- 最新运行日期：2026-07-11 ~ 2026-07-20
- 运行时间：2026-07-20 10:23:52 UTC
- 运行状态：成功
- 本次总论文数：15
- 精读区：6
- 速读区：9

### 今日简报（AI）
本期日报聚焦视频生成与扩散模型加速，两项高分研究分别提出自适应缓存机制与高效推理技术综述。建议重点关注视频生成缓存优化（ACID）与掩码扩散大模型加速两篇精读论文，其中硬件-算法协同设计与选择性注意力复用等方向也值得深入。
- 详情：[/20260711-20260720/README](/20260711-20260720/README)

### 精读区论文标签
1. [ACID: Adaptive Caching for vIDeo generation](/20260711-20260720/2607.12358v2-acid-adaptive-caching-for-video-generation)  
   标签：评分：9.0/10、query:diff-accel
   evidence：视频扩散模型的自适应缓存推理加速
2. [Accelerating Masked Diffusion Large Language Models: A Survey of Efficient Inference Techniques](/20260711-20260720/2607.12829v1-accelerating-masked-diffusion-large-language-models-a-survey-of-efficient-inference-techniques)  
   标签：评分：9.0/10、query:diff-accel
   evidence：扩散大语言模型加速技术综述
3. [Seeing the End at Step Zero: Accelerating Diffusion MLLMs via MLP Sparsity-Aware Truncation](/20260711-20260720/2607.14557v1-seeing-the-end-at-step-zero-accelerating-diffusion-mllms-via-mlp-sparsity-aware-truncation)  
   标签：评分：9.0/10、query:diff-accel
   evidence：通过稀疏性截断实现免训练扩散模型加速
4. [Reflex: Real-Time VLA Control through Streaming Inference](/20260711-20260720/2607.14695v1-reflex-real-time-vla-control-through-streaming-inference)  
   标签：评分：9.0/10、query:diff-accel
   evidence：利用时间步不变性实现免训练的流匹配策略流式推理加速
5. [SPEED: One-Step Pixel Diffusion for High-quality Video Frame Interpolation](/20260711-20260720/2607.15585v1-speed-one-step-pixel-diffusion-for-high-quality-video-frame-interpolation)  
   标签：评分：9.0/10、query:diff-accel
   evidence：一步像素扩散框架加速视频帧插值，解决多步采样延迟问题
6. [FlashDiff: Efficient Regional Execution and Scheduling for Diffusion Model Serving](/20260711-20260720/2607.12121v2-flashdiff-efficient-regional-execution-and-scheduling-for-diffusion-model-serving)  
   标签：评分：8.0/10、query:diff-accel
   evidence：通过自适应区域执行和调度实现扩散模型高效服务

### 速读区论文标签
1. [Kaleido: Algorithm-Hardware Co-Design for Video Diffusion Transformers by Exploiting Latent Space Correlations](/20260711-20260720/2607.13770v1-kaleido-algorithm-hardware-co-design-for-video-diffusion-transformers-by-exploiting-latent-space-correlations)  
   标签：评分：8.0/10、query:diff-accel
   evidence：视频扩散Transformer的算法硬件协同加速
2. [DiTango: Cost-Effective Parallel Diffusion Generation with Selective Attention State Reuse](/20260711-20260720/2607.15650v1-ditango-cost-effective-parallel-diffusion-generation-with-selective-attention-state-reuse)  
   标签：评分：8.0/10、query:diff-accel
   evidence：通过选择性注意力状态重用实现高效的并行扩散生成
3. [Adaptive Multi-Step Lookahead Decoding for Diffusion Language Models](/20260711-20260720/2607.15655v1-adaptive-multi-step-lookahead-decoding-for-diffusion-language-models)  
   标签：评分：8.0/10、query:diff-accel
   evidence：针对掩码扩散语言模型的自适应多步前瞻解码提升生成效率
4. [Efficient Difficulty-Aware Dynamic Routing for Diffusion-Based Real-World Image Super-Resolution](/20260711-20260720/2607.15711v1-efficient-difficulty-aware-dynamic-routing-for-diffusion-based-real-world-image-super-resolution)  
   标签：评分：8.0/10、query:diff-accel
   evidence：基于难度的动态路由减少扩散超分辨率中的计算量
5. [DSTAR: Accelerating Diffusion Transformers via Spatial and Temporal Redundancy Reduction](/20260711-20260720/2607.15846v1-dstar-accelerating-diffusion-transformers-via-spatial-and-temporal-redundancy-reduction)  
   标签：评分：8.0/10、query:diff-accel
   evidence：通过时空冗余减少加速扩散变换器
6. [FVAttn: Adaptive Sparse Attention with Runtime Load Balancing for Video Generation](/20260711-20260720/2607.16190v1-fvattn-adaptive-sparse-attention-with-runtime-load-balancing-for-video-generation)  
   标签：评分：8.0/10、query:diff-accel
   evidence：视频扩散模型训练无关的稀疏注意力加速
7. [From Draft to Draft-Free: One-Step Video Object Removal via Privileged Distillation and Fast Planting](/20260711-20260720/2607.14976v1-from-draft-to-draft-free-one-step-video-object-removal-via-privileged-distillation-and-fast-planting)  
   标签：评分：7.0/10、query:diff-accel
   evidence：通过蒸馏实现视频扩散单步加速
8. [MeanFlowNFT: Bringing Forward-Process RL to Average-Velocity Generators](/20260711-20260720/2607.15273v1-meanflownft-bringing-forward-process-rl-to-average-velocity-generators)  
   标签：评分：7.0/10、query:diff-accel
   evidence：使用前向过程RL实现平均速度生成器的快速少步采样
9. [Hierarchical Denoising For Multi-Step Visual Reasoning](/20260711-20260720/2607.15278v1-hierarchical-denoising-for-multi-step-visual-reasoning)  
   标签：评分：7.0/10、query:diff-accel
   evidence：层次化去噪降低视频推理中的推理成本


<div class="dpr-home-promo-card dpr-home-panel">
  <div class="dpr-home-panel-header">
    <h3 class="dpr-home-promo-title">社区与支持</h3>
  </div>
  <p class="dpr-home-promo-copy">欢迎通过 Star、Fork、Issue 或 PR 一起完善 Daily Paper Reader。</p>
  <div class="dpr-home-promo-meta">
    <span>QQ群 <strong>583867967</strong></span>
    <span class="dpr-home-promo-separator" aria-hidden="true">·</span>
    <span>已有 <strong>1,491</strong> 人参与交流</span>
  </div>
</div>
