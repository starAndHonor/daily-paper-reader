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
- 运行时间：2026-07-20 08:29:45 UTC
- 运行状态：成功
- 本次总论文数：14
- 精读区：6
- 速读区：8

### 今日简报（AI）
1) 日报精读6篇，聚焦扩散模型推理加速，视频生成缓存优化和高分论文。
2) 最值得关注方向：ACIC自适应缓存提升视频生成吞吐，以及MLP稀疏截断加速扩散多模态LLM。
3) 若想入门，从速读中扩散语言模型多点预测解码和高效推理综述看起，再深入精读。
- 详情：[/20260711-20260720/README](/20260711-20260720/README)

### 精读区论文标签
1. [ACID: Adaptive Caching for vIDeo generation](/20260711-20260720/2607.12358v1-acid-adaptive-caching-for-video-generation)  
   标签：评分：9.0/10、query:diff-accel
   evidence：无训练缓存的视频扩散加速
2. [Seeing the End at Step Zero: Accelerating Diffusion MLLMs via MLP Sparsity-Aware Truncation](/20260711-20260720/2607.14557v1-seeing-the-end-at-step-zero-accelerating-diffusion-mllms-via-mlp-sparsity-aware-truncation)  
   标签：评分：9.0/10、query:diff-accel
   evidence：无需训练的扩散模型加速框架，利用MLP稀疏性截断
3. [SPEED: One-Step Pixel Diffusion for High-quality Video Frame Interpolation](/20260711-20260720/2607.15585v1-speed-one-step-pixel-diffusion-for-high-quality-video-frame-interpolation)  
   标签：评分：9.0/10、query:diff-accel
   evidence：提出一步像素扩散用于快速视频帧插值
4. [FdAudio: MeanFlow-Anchored Fréchet-Distance Post-Training for One-Step Text-to-Audio Generation](/20260711-20260720/2607.10421v1-fdaudio-meanflow-anchored-frchet-distance-post-training-for-one-step-text-to-audio-generation)  
   标签：评分：8.0/10、query:diff-accel
   evidence：一步生成后训练提升少步质量
5. [FlashDiff: Efficient Regional Execution and Scheduling for Diffusion Model Serving](/20260711-20260720/2607.12121v1-flashdiff-efficient-regional-execution-and-scheduling-for-diffusion-model-serving)  
   标签：评分：8.0/10、query:diff-accel
   evidence：通过自适应区域执行实现无需重训的扩散模型服务加速
6. [Kaleido: Algorithm-Hardware Co-Design for Video Diffusion Transformers by Exploiting Latent Space Correlations](/20260711-20260720/2607.13770v1-kaleido-algorithm-hardware-co-design-for-video-diffusion-transformers-by-exploiting-latent-space-correlations)  
   标签：评分：8.0/10、query:diff-accel
   evidence：算法-硬件协同设计加速视频扩散Transformer

### 速读区论文标签
1. [Adaptive Multi-Step Lookahead Decoding for Diffusion Language Models](/20260711-20260720/2607.15655v1-adaptive-multi-step-lookahead-decoding-for-diffusion-language-models)  
   标签：评分：8.0/10、query:diff-accel
   evidence：自适应多步前瞻加速扩散语言模型解码
2. [Accelerating Masked Diffusion Large Language Models: A Survey of Efficient Inference Techniques](/20260711-20260720/2607.12829v1-accelerating-masked-diffusion-large-language-models-a-survey-of-efficient-inference-techniques)  
   标签：评分：7.0/10、query:diff-accel
   evidence：遮蔽扩散LLM加速技术综述
3. [Integration Matters: Rollout-Based Training for Constrained Diffusion Models](/20260711-20260720/2607.14398v1-integration-matters-rollout-based-training-for-constrained-diffusion-models)  
   标签：评分：7.0/10、query:diff-accel
   evidence：面向少步约束生成的微调框架
4. [CODA: Algorithm-Hardware Co-design for Edge Video Diffusion via NMP-Enabled Compute-Cache Operator Disaggregation](/20260711-20260720/2607.14908v1-coda-algorithm-hardware-co-design-for-edge-video-diffusion-via-nmp-enabled-compute-cache-operator-disaggregation)  
   标签：评分：7.0/10、query:diff-accel
   evidence：使用缓存算子分解的边缘视频扩散算法-硬件协同加速
5. [MeanFlowNFT: Bringing Forward-Process RL to Average-Velocity Generators](/20260711-20260720/2607.15273v1-meanflownft-bringing-forward-process-rl-to-average-velocity-generators)  
   标签：评分：7.0/10、query:diff-accel
   evidence：利用MeanFlow和前向过程RL实现快速少步采样
6. [DiTango: Cost-Effective Parallel Diffusion Generation with Selective Attention State Reuse](/20260711-20260720/2607.15650v1-ditango-cost-effective-parallel-diffusion-generation-with-selective-attention-state-reuse)  
   标签：评分：7.0/10、query:diff-accel
   evidence：通过选择性注意力状态重用加速并行扩散生成
7. [DSTAR: Accelerating Diffusion Transformers via Spatial and Temporal Redundancy Reduction](/20260711-20260720/2607.15846v1-dstar-accelerating-diffusion-transformers-via-spatial-and-temporal-redundancy-reduction)  
   标签：评分：7.0/10、query:diff-accel
   evidence：通过时空冗余减少加速扩散Transformer
8. [FVAttn: Adaptive Sparse Attention with Runtime Load Balancing for Video Generation](/20260711-20260720/2607.16190v1-fvattn-adaptive-sparse-attention-with-runtime-load-balancing-for-video-generation)  
   标签：评分：7.0/10、query:diff-accel
   evidence：无训练稀疏注意力加速视频生成


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
