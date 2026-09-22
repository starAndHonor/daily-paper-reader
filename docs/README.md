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
- 最新运行日期：2026-09-22
- 运行时间：2026-09-22 22:40:14 UTC
- 运行状态：成功
- 本次总论文数：10
- 精读区：3
- 速读区：7

### 今日简报（AI）
今日扫完 10 篇（精读 3、速读 7），主线是视觉生成与扩散模型的高稀疏加速，外加长上下文解码的稀疏注意力优化。

最值得看的是拿下 9.0 分的《SparkDiffusion》——用统一框架缓解高稀疏陷阱、实现最高 265× 单 GPU 视觉生成加速；其次是 8.0 分的 tau-leaping 调度优化，以及速读中 Elastic Threshold Attention、RBS-Attention 两条长上下文稀疏路线。

普通读者可先读 SparkDiffusion 看加速思路，再顺着两篇长上下文稀疏注意力了解推理侧省算力的方向。
- 详情：[/202609/22/README](/202609/22/README)

### 精读区论文标签
1. [SparkDiffusion: Mitigating the High-Sparsity Trap --- A Unified Framework for up to $265\times$ Single-GPU Acceleration of Visual Generation](/202609/22/2609.23153v1-sparkdiffusion-mitigating-the-high-sparsity-trap-----a-unified-framework-for-up-to-265times-single-gpu-acceleration-of-visual-generation)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：视频扩散Transformer中的稀疏注意力与大幅加速
2. [Schedule optimization for tau-leaping in masked discrete diffusion](/202609/22/2609.21960v1-schedule-optimization-for-tau-leaping-in-masked-discrete-diffusion)  
   标签：评分：8.0/10、query:diff-accel
   evidence：调度优化加速掩码离散扩散采样
3. [Block-Sparse Attention with Semantic-Geometric Decoupled Routing](/202609/22/2609.22884v1-block-sparse-attention-with-semantic-geometric-decoupled-routing)  
   标签：评分：8.0/10、query:sparse-attn
   evidence：免训练块稀疏注意力路由，提升长上下文推理效率

### 速读区论文标签
1. [Elastic Threshold Attention: Learned Contextual Sparsity for Long-Context Decoding](/202609/22/2609.20888v1-elastic-threshold-attention-learned-contextual-sparsity-for-long-context-decoding)  
   标签：评分：7.0/10、query:sparse-attn
   evidence：可学习上下文稀疏注意力提升序列建模效率
2. [RBS-Attention: Radius-Bounded Sparse Prefill for Long-Context Large Language Models](/202609/22/2609.20971v1-rbs-attention-radius-bounded-sparse-prefill-for-long-context-large-language-models)  
   标签：评分：7.0/10、query:sparse-attn
   evidence：面向长上下文的免训练稀疏注意力
3. [Leveraging Inference-Time Compute for Diffusion Models via Global Scheduling of Denoising Trajectories](/202609/22/2609.22867v1-leveraging-inference-time-compute-for-diffusion-models-via-global-scheduling-of-denoising-trajectories)  
   标签：评分：7.0/10、query:diff-accel
   evidence：无需重训练的扩散采样推理时计算分配
4. [Quantization-Aware Kalman Estimation for Diffusion Sampling](/202609/22/2609.21407v1-quantization-aware-kalman-estimation-for-diffusion-sampling)  
   标签：评分：6.0/10、query:diff-accel
   evidence：采样阶段校正以加速量化扩散推理
5. [The Weight Is Over - Interactive Diffusion on Consumer GPUs](/202609/22/2609.21849v1-the-weight-is-over---interactive-diffusion-on-consumer-gpus)  
   标签：评分：6.0/10、query:diff-accel
   evidence：扩散推理速度与延迟优化，亚秒级生成
6. [SPLASH: Co-Designing Sparse Attention with High-Bandwidth Flash for Efficient Long-Context Inference](/202609/22/2609.23816v1-splash-co-designing-sparse-attention-with-high-bandwidth-flash-for-efficient-long-context-inference)  
   标签：评分：6.0/10、query:sparse-attn
   evidence：为高效长上下文推理设计的稀疏注意力
7. [ARM: Attention with Routed-Memory for Learnable Sparse Control](/202609/22/2609.24417v1-arm-attention-with-routed-memory-for-learnable-sparse-control)  
   标签：评分：6.0/10、query:sparse-attn
   evidence：带路由记忆的可学习稀疏注意力


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
