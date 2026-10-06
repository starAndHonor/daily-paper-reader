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
- 最新运行日期：2026-10-06
- 运行时间：2026-10-06 23:40:11 UTC
- 运行状态：成功
- 本次总论文数：18
- 精读区：7
- 速读区：11

### 今日简报（AI）
2026-10-06 日报：18 篇论文中精读 7 篇、速读 11 篇，重点聚焦扩散采样求解器与流匹配自适应步长。

最值得看的是两篇 9.0 分工作——随机扩散采样的自适应二阶求解器，以及面向高效视觉生成的 Contextual Flow Matching 自适应步选择；速读中稀疏注意力的信息损失与泛化权衡、跨层共享和 LLM 解码加速也值得关注。

普通读者可先读两篇 9 分论文，再按需挑稀疏注意力速读了解效率优化方向。
- 详情：[/202610/06/README](/202610/06/README)

### 精读区论文标签
1. [Adaptive Second-Order Solvers for Fast Stochastic Diffusion Sampling](/202610/06/2610.03034v1-adaptive-second-order-solvers-for-fast-stochastic-diffusion-sampling)  
   标签：评分：9.0/10、query:diff-accel
   evidence：面向快速随机扩散采样的自适应二阶求解器
2. [Contextual Flow Matching: Adaptive Step Selection in Flow Models for Efficient Visual Generation](/202610/06/2610.03202v1-contextual-flow-matching-adaptive-step-selection-in-flow-models-for-efficient-visual-generation)  
   标签：评分：9.0/10、query:diff-accel
   evidence：免训练推理时自适应步数选择加速流匹配扩散生成
3. [Rethinking What to Cache in Few-Step Diffusion Transformers: Solver-Aware Target Selection](/202610/06/2610.03577v1-rethinking-what-to-cache-in-few-step-diffusion-transformers-solver-aware-target-selection)  
   标签：评分：9.0/10、query:diff-accel
   evidence：基于缓存的少步扩散Transformer采样加速
4. [Hybrid-Basis Feature Forecasting for Diffusion Sampling Acceleration](/202610/06/2610.05254v1-hybrid-basis-feature-forecasting-for-diffusion-sampling-acceleration)  
   标签：评分：9.0/10、query:diff-accel
   evidence：免训练即插即用的特征预测扩散采样加速框架
5. [Prism: Dynamic Sparse Attention for Native 2K Joint Video-Audio Generation Model Training](/202610/06/2610.05416v1-prism-dynamic-sparse-attention-for-native-2k-joint-video-audio-generation-model-training)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：面向视频-音频生成的动态稀疏注意力
6. [MC-Sparse: Deconstructing and Closing the Dense-Sparse Attention Gap in Diffusion Transformers](/202610/06/2610.06801v1-mc-sparse-deconstructing-and-closing-the-dense-sparse-attention-gap-in-diffusion-transformers)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：面向视频与三维生成的扩散Transformer免训练稀疏注意力
7. [SpectralCache: Accelerating Diffusion-Based World Models via Spectral Feature Caching](/202610/06/2610.02660v1-spectralcache-accelerating-diffusion-based-world-models-via-spectral-feature-caching)  
   标签：评分：8.0/10、query:diff-accel
   evidence：免训练的谱缓存加速扩散推理

### 速读区论文标签
1. [On the Trade-off Between Information Loss and Generalization in Sparse Attention](/202610/06/2610.04424v1-on-the-trade-off-between-information-loss-and-generalization-in-sparse-attention)  
   标签：评分：8.0/10、query:sparse-attn
   evidence：稀疏注意力机制的理论分析
2. [LatentIndex: Cross-Layer Sharing with Layer-Specific Selection for Sparse Attention](/202610/06/2610.04635v1-latentindex-cross-layer-sharing-with-layer-specific-selection-for-sparse-attention)  
   标签：评分：8.0/10、query:sparse-attn
   evidence：跨层共享与逐层选择实现高效稀疏注意力
3. [More Value per Key: Asymmetric Sparse Attention for Faster LLM Decoding](/202610/06/2610.04753v1-more-value-per-key-asymmetric-sparse-attention-for-faster-llm-decoding)  
   标签：评分：8.0/10、query:sparse-attn
   evidence：非对称稀疏注意力以减少键头实现高效注意力
4. [SpecFold: Folding Multi-Branch Redundancy for Faster Speculative Decoding in Diffusion Language Models](/202610/06/2610.04875v1-specfold-folding-multi-branch-redundancy-for-faster-speculative-decoding-in-diffusion-language-models)  
   标签：评分：8.0/10、query:diff-accel
   evidence：通过多分支冗余折叠加速扩散语言模型推测解码
5. [S2PD: Serial-to-Parallel Diffusion for Physically and Logically Consistent Video Generation](/202610/06/2610.06847v1-s2pd-serial-to-parallel-diffusion-for-physically-and-logically-consistent-video-generation)  
   标签：评分：8.0/10、query:diff-accel
   evidence：串行到并行视频扩散降低采样时间
6. [Rank-Aware Speculative Sampling for Diffusion Draft Trees](/202610/06/2610.02251v1-rank-aware-speculative-sampling-for-diffusion-draft-trees)  
   标签：评分：7.0/10、query:diff-accel
   evidence：秩感知草稿树验证的推测采样加速扩散生成
7. [Level-of-Token Diffusion](/202610/06/2610.05816v1-level-of-token-diffusion)  
   标签：评分：7.0/10、query:diff-accel
   evidence：通过减少计算实现自适应高效扩散生成
8. [IDRF: Inverse-Distilled Reward Fine-tuning of Masked Discrete Diffusion Models](/202610/06/2610.03641v1-idrf-inverse-distilled-reward-fine-tuning-of-masked-discrete-diffusion-models)  
   标签：评分：6.0/10、query:diff-accel
   evidence：面向少步掩码离散扩散生成器的奖励微调以降低迭代采样成本
9. [FLASHSWIN: Unlocking Large Windows and Dense Tokens in Swin Vision Transformers with Memory Efficient Attention](/202610/06/2610.04664v1-flashswin-unlocking-large-windows-and-dense-tokens-in-swin-vision-transformers-with-memory-efficient-attention)  
   标签：评分：6.0/10、query:sparse-attn
   evidence：内存高效窗口注意力降低计算开销
10. [NAMVIS: Next-Scale Autoregressive Multi-View Image Synthesis](/202610/06/2610.04722v1-namvis-next-scale-autoregressive-multi-view-image-synthesis)  
   标签：评分：6.0/10、query:diff-accel
   evidence：无扩散框架避免迭代去噪，降低推理成本
11. [The Unexpired Plan: A Free Monitor for Accelerated Diffusion Policies](/202610/06/2610.05747v1-the-unexpired-plan-a-free-monitor-for-accelerated-diffusion-policies)  
   标签：评分：6.0/10、query:diff-accel
   evidence：免训练扩散策略加速及其监控器


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
