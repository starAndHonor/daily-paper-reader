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
- 最新运行日期：2026-08-16
- 运行时间：2026-08-16 20:05:43 UTC
- 运行状态：成功
- 本次总论文数：19
- 精读区：7
- 速读区：12

### 今日简报（AI）
今日聚焦稀疏注意力与缓存复用，为扩散模型推理加速提供新思路；最值得精读《LoSA》与《Archer》，分别实现近无损视频加速和高效回滚；普通读者可关注这类免训练加速技术，未来或能显著降低生成成本。
- 详情：[/202608/16/README](/202608/16/README)

### 精读区论文标签
1. [LoSA: Near-Lossless Sparse Attention for Training-Free Video Diffusion Acceleration](/202608/16/2608.12032v1-losa-near-lossless-sparse-attention-for-training-free-video-diffusion-acceleration)  
   标签：评分：10.0/10、query:diff-accel
   evidence：免训练稀疏注意力加速视频扩散推理
2. [Archer: Adaptive Reuse of Cached Hidden States for Efficient Rollback in Diffusion Language Models](/202608/16/2608.08086v2-archer-adaptive-reuse-of-cached-hidden-states-for-efficient-rollback-in-diffusion-language-models)  
   标签：评分：9.0/10、query:diff-accel
   evidence：面向扩散语言模型推理加速的免训练KV缓存复用
3. [BAG: Budget-Aware Gating for Diffusion Caching](/202608/16/2608.09231v1-bag-budget-aware-gating-for-diffusion-caching)  
   标签：评分：9.0/10、query:diff-accel
   evidence：面向扩散Transformer推理加速的预算感知缓存策略
4. [Linearized 2-Simplicial Attention](/202608/16/2608.09307v1-linearized-2-simplicial-attention)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：通过随机特征实现线性代价注意力，直接面向高效序列建模
5. [SparSTAR: Sparse Attention for SpaceTime AutoRegressive Video Synthesis](/202608/16/2608.10519v1-sparstar-sparse-attention-for-spacetime-autoregressive-video-synthesis)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：面向视频自回归合成的免训练块稀疏注意力
6. [SCOPE: Subspace Clustering with Online Per-Head Top-K Estimation for Sparse Video Attention](/202608/16/2608.12780v1-scope-subspace-clustering-with-online-per-head-top-k-estimation-for-sparse-video-attention)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：面向视频扩散Transformer的免训练稀疏注意力框架，降低二次注意力开销
7. [From Local Mismatch to Global Impact: Optimizing Cache Reuse Policy for Efficient Diffusion](/202608/16/2608.13043v1-from-local-mismatch-to-global-impact-optimizing-cache-reuse-policy-for-efficient-diffusion)  
   标签：评分：9.0/10、query:diff-accel
   evidence：基于缓存的免训练扩散推理加速

### 速读区论文标签
1. [MotionCraft: Latent World Modeling with Sparse Attention for Visual Upscaling](/202608/16/2608.08553v1-motioncraft-latent-world-modeling-with-sparse-attention-for-visual-upscaling)  
   标签：评分：8.0/10、query:sparse-attn
   evidence：用于视频升尺度的稀疏注意力
2. [RL-Native Distillation: Exploiting Scored Trajectories for Few-Step Image Generation](/202608/16/2608.09226v1-rl-native-distillation-exploiting-scored-trajectories-for-few-step-image-generation)  
   标签：评分：8.0/10、query:diff-accel
   evidence：通过RL原生蒸馏实现少步图像生成，加速扩散文生图推理
3. [MeanSR: Restoration Trajectory Learning for One-Step Perceptual Super-Resolution](/202608/16/2608.09405v1-meansr-restoration-trajectory-learning-for-one-step-perceptual-super-resolution)  
   标签：评分：8.0/10、query:diff-accel
   evidence：单步扩散超分加速图像生成
4. [Making Every Step Count: Spatio-Temporal Information Allocation for Imaging Inverse Problems](/202608/16/2608.11747v1-making-every-step-count-spatio-temporal-information-allocation-for-imaging-inverse-problems)  
   标签：评分：8.0/10、query:diff-accel
   evidence：固定函数评估次数下的时空信息分配，用于免训练的流式逆问题求解
5. [XYZFlow:Scaling Multi dimensional Shortcut Flows for Efficient Generative Modeling](/202608/16/2608.12276v2-xyzflowscaling-multi-dimensional-shortcut-flows-for-efficient-generative-modeling)  
   标签：评分：8.0/10、query:diff-accel
   evidence：通过多维捷径流实现高效生成建模，以解决扩散采样缓慢的问题
6. [The data geometry of masking diffusion: Certified-optimal schedules via unmasking growth complexity](/202608/16/2608.13520v1-the-data-geometry-of-masking-diffusion-certified-optimal-schedules-via-unmasking-growth-complexity)  
   标签：评分：8.0/10、query:diff-accel
   evidence：通过去掩码增长复杂度优化掩码扩散调度以降低离散化误差，实现免训练的文本/序列扩散采样加速
7. [OasisKV: Scaling In-Decode KV Cache Beyond HBM with Lookahead Sparse Prefetching](/202608/16/2608.08097v1-oasiskv-scaling-in-decode-kv-cache-beyond-hbm-with-lookahead-sparse-prefetching)  
   标签：评分：7.0/10、query:sparse-attn
   evidence：利用解码期注意力天然稀疏性，仅保留相关KV条目，面向高效序列建模
8. [Spatially-Grounded Text-to-Video Generation via Inference-Time Gradient-Free Optimization](/202608/16/2608.13037v1-spatially-grounded-text-to-video-generation-via-inference-time-gradient-free-optimization)  
   标签：评分：7.0/10、query:diff-accel
   evidence：文本到视频扩散的免训练无梯度推理时优化
9. [VLZip: Unified Visual and Textual Compression for Interleaved Long-Context Modeling](/202608/16/2608.08630v1-vlzip-unified-visual-and-textual-compression-for-interleaved-long-context-modeling)  
   标签：评分：6.0/10、query:sparse-attn
   evidence：统一图文压缩以缩短注意力序列，实现高效长上下文建模
10. [FiRe: Fixed-Noise Refinement for Visual Counterfactual Explanations](/202608/16/2608.08664v1-fire-fixed-noise-refinement-for-visual-counterfactual-explanations)  
   标签：评分：6.0/10、query:diff-accel
   evidence：避免长去噪轨迹与昂贵递归去噪
11. [Hybrid Gated Attention](/202608/16/2608.11805v1-hybrid-gated-attention)  
   标签：评分：6.0/10、query:sparse-attn
   evidence：门控策略与低秩分解降低注意力计算成本
12. [Prof-K: Probabilistic One-Pass Filtering for Efficient Top-k Selection](/202608/16/2608.12573v1-prof-k-probabilistic-one-pass-filtering-for-efficient-top-k-selection)  
   标签：评分：6.0/10、query:sparse-attn
   evidence：高效的top-k选择算法，带概率保证，可直接用于注意力剪枝与稀疏激活


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
