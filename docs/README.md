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
- 运行时间：2026-10-06 01:11:38 UTC
- 运行状态：成功
- 本次总论文数：8
- 精读区：5
- 速读区：3

### 今日简报（AI）
1) 今日筛完8篇，精读5篇、速读3篇，扩散模型加速与采样求解器拿下两个9分。
2) 最值得看的是扩散模型推理加速：SpectralCache用频谱特征缓存加速扩散世界模型，Adaptive Second-Order Solvers用自适应二阶求解器加速随机扩散采样。
3) 普通读者可先读这两篇9分精读，再按兴趣浏览速读中的ViT Token剪枝、掩码离散扩散奖励微调和线性注意力。
- 详情：[/202610/06/README](/202610/06/README)

### 精读区论文标签
1. [SpectralCache: Accelerating Diffusion-Based World Models via Spectral Feature Caching](/202610/06/2610.02660v1-spectralcache-accelerating-diffusion-based-world-models-via-spectral-feature-caching)  
   标签：评分：9.0/10、query:diff-accel
   evidence：免训练谱缓存加速扩散推理
2. [Adaptive Second-Order Solvers for Fast Stochastic Diffusion Sampling](/202610/06/2610.03034v1-adaptive-second-order-solvers-for-fast-stochastic-diffusion-sampling)  
   标签：评分：9.0/10、query:diff-accel
   evidence：自适应步长求解器加速随机扩散采样
3. [Contextual Flow Matching: Adaptive Step Selection in Flow Models for Efficient Visual Generation](/202610/06/2610.03202v1-contextual-flow-matching-adaptive-step-selection-in-flow-models-for-efficient-visual-generation)  
   标签：评分：9.0/10、query:diff-accel
   evidence：推理时即插即用选择步数，无需重训练，适用图像与视频
4. [Rethinking What to Cache in Few-Step Diffusion Transformers: Solver-Aware Target Selection](/202610/06/2610.03577v1-rethinking-what-to-cache-in-few-step-diffusion-transformers-solver-aware-target-selection)  
   标签：评分：9.0/10、query:diff-accel
   evidence：缓存跳过DiT评估实现免训练采样加速
5. [Rank-Aware Speculative Sampling for Diffusion Draft Trees](/202610/06/2610.02251v1-rank-aware-speculative-sampling-for-diffusion-draft-trees)  
   标签：评分：8.0/10、query:diff-accel
   evidence：投机采样通过草稿树验证加速扩散生成

### 速读区论文标签
1. [DORA: Dynamic Online Reinforcement Agent for Token Pruning in Vision Transformers](/202610/06/2609.34325v1-dora-dynamic-online-reinforcement-agent-for-token-pruning-in-vision-transformers)  
   标签：评分：7.0/10、query:sparse-attn
   evidence：通过输入自适应令牌剪枝降低视觉Transformer自注意力的二次开销
2. [IDRF: Inverse-Distilled Reward Fine-tuning of Masked Discrete Diffusion Models](/202610/06/2610.03641v1-idrf-inverse-distilled-reward-fine-tuning-of-masked-discrete-diffusion-models)  
   标签：评分：7.0/10、query:diff-accel
   evidence：少步掩码离散扩散生成器降低迭代采样开销
3. [Switching Linear Attention](/202610/06/2609.39034v1-switching-linear-attention)  
   标签：评分：6.0/10、query:sparse-attn
   evidence：高效线性注意力序列层


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
