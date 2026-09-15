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
- 最新运行日期：2026-09-15
- 运行时间：2026-09-15 22:14:34 UTC
- 运行状态：成功
- 本次总论文数：12
- 精读区：4
- 速读区：8

### 今日简报（AI）
2026-09-15日报：12篇论文中精读4篇、速读8篇，RouteRelay与SAS以8.0分领跑长上下文稀疏注意力方向。
最值得看的是跨层路由复用与端到端上下文排序稀疏化，速读里的CEDAR和Sample-Guided Top-K也在处理误差控制与精确Top-K选择。
普通读者可先精读两篇8分文章抓主线，再按需速览长上下文加速或视频扩散蒸馏。
- 详情：[/202609/15/README](/202609/15/README)

### 精读区论文标签
1. [RouteRelay: Event-Triggered Cross-Layer Route Reuse for Efficient Dynamic Sparse Attention](/202609/15/2609.07306v1-routerelay-event-triggered-cross-layer-route-reuse-for-efficient-dynamic-sparse-attention)  
   标签：评分：8.0/10、query:sparse-attn
   evidence：高效动态稀疏注意力降低计算开销
2. [SAS: Simple Attention Sparsification via End-to-End Optimization of Context Ranking](/202609/15/2609.13141v1-sas-simple-attention-sparsification-via-end-to-end-optimization-of-context-ranking)  
   标签：评分：8.0/10、query:sparse-attn
   evidence：后训练注意力稀疏化以降低二次注意力开销
3. [CrossDistill: Balancing Quality and Diversity via Trajectory-Level Hybrid Few-Step Distillation](/202609/15/2609.14725v1-crossdistill-balancing-quality-and-diversity-via-trajectory-level-hybrid-few-step-distillation)  
   标签：评分：8.0/10、query:diff-accel
   evidence：少步蒸馏框架，直接加速扩散模型采样
4. [Temporal Self-Distillation: Faster Inference in Discrete Diffusion Language Models](/202609/15/2609.15177v1-temporal-self-distillation-faster-inference-in-discrete-diffusion-language-models)  
   标签：评分：8.0/10、query:diff-accel
   evidence：加速离散扩散语言模型推理的时间自蒸馏方法

### 速读区论文标签
1. [CEDAR: Error-Bounded Residual Routing for Efficient Long-Context Attention](/202609/15/2609.07237v1-cedar-error-bounded-residual-routing-for-efficient-long-context-attention)  
   标签：评分：7.0/10、query:sparse-attn
   evidence：事后稀疏注意力路由，冻结语言模型，免训练
2. [Sample-Guided Exact Top-K Selection for Long-Context Sparse Attention](/202609/15/2609.08450v1-sample-guided-exact-top-k-selection-for-long-context-sparse-attention)  
   标签：评分：7.0/10、query:sparse-attn
   evidence：面向长上下文稀疏注意力的采样引导精确Top-K选择
3. [Mask Forcing: Improving Autoregressive Video Diffusion Distillation via Dual-Noise Masking Rollout](/202609/15/2609.09123v1-mask-forcing-improving-autoregressive-video-diffusion-distillation-via-dual-noise-masking-rollout)  
   标签：评分：7.0/10、query:diff-accel
   evidence：面向实时生成的视频扩散模型蒸馏加速
4. [Uncertainty DMD: Restoring Diversity in Few-Step Autoregressive Video Distillation](/202609/15/2609.11265v1-uncertainty-dmd-restoring-diversity-in-few-step-autoregressive-video-distillation)  
   标签：评分：6.0/10、query:diff-accel
   evidence：少步蒸馏提升自回归视频生成效率
5. [Fixed State, Long Reach: What a Constant-Size Cache Buys Block Diffusion at Scale](/202609/15/2609.11998v1-fixed-state-long-reach-what-a-constant-size-cache-buys-block-diffusion-at-scale)  
   标签：评分：6.0/10、query:diff-accel
   evidence：免训练块扩散缓存加速推理
6. [DNF-SR: Dual-Input and Negative-Aware Feature Fine-Tuning for Real-World Image Super-Resolution](/202609/15/2609.15120v1-dnf-sr-dual-input-and-negative-aware-feature-fine-tuning-for-real-world-image-super-resolution)  
   标签：评分：6.0/10、query:diff-accel
   evidence：面向高效真实世界图像超分的一步扩散模型
7. [MedDiME: Efficient Latent Diffusion with Adaptive Masking for Medical Counterfactual Generation](/202609/15/2609.15647v1-meddime-efficient-latent-diffusion-with-adaptive-masking-for-medical-counterfactual-generation)  
   标签：评分：6.0/10、query:diff-accel
   evidence：高效潜扩散降低计算与内存开销
8. [VC-Attention: Value Smoothing and Softmax Casting for Low-bit Attention](/202609/15/2609.15810v1-vc-attention-value-smoothing-and-softmax-casting-for-low-bit-attention)  
   标签：评分：6.0/10、query:diff-accel
   evidence：低位注意力内核降低视频扩散Transformer开销


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
