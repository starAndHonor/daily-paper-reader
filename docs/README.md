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
- 最新运行日期：2026-09-29
- 运行时间：2026-09-29 23:41:26 UTC
- 运行状态：成功
- 本次总论文数：19
- 精读区：7
- 速读区：12

### 今日简报（AI）
今天完成19篇论文日报，精读7篇、速读12篇，主线集中在视频扩散与生成模型的推理加速。  
最值得看两篇9.0精读：异构注意力优化视频扩散计算，以及免训练、比蒸馏更少步的因果视频扩散加速；速读中的块稀疏注意力和KV缓存复用也在补全这条效率路线。  
普通读者可先读两篇9.0，再按“注意力稀疏/缓存复用/跨请求复用”挑速读，判断哪种方案更适合自己的视频生成落地。
- 详情：[/202609/29/README](/202609/29/README)

### 精读区论文标签
1. [Where Compute Matters: Heterogeneous Attention for Efficient Video Diffusion](/202609/29/2609.31050v1-where-compute-matters-heterogeneous-attention-for-efficient-video-diffusion)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：异构注意力降低视频扩散生成中时空注意力的二次开销
2. [UnStep: Training-Free Acceleration of Causal Video Diffusion with Fewer Steps Than Distillation](/202609/29/2609.32518v1-unstep-training-free-acceleration-of-causal-video-diffusion-with-fewer-steps-than-distillation)  
   标签：评分：9.0/10、query:diff-accel
   evidence：在推理阶段免训练加速因果视频扩散
3. [DraftAttention2: Fast Video Diffusion with Low-Resolution-Guided Mixed-Precision Attention](/202609/29/2609.32628v1-draftattention2-fast-video-diffusion-with-low-resolution-guided-mixed-precision-attention)  
   标签：评分：9.0/10、query:diff-accel
   evidence：免训练稀疏注意力加速视频扩散
4. [Improving Video Sparse Attention with Fine-grained Router and Sparse Rebasing](/202609/29/2609.32882v1-improving-video-sparse-attention-with-fine-grained-router-and-sparse-rebasing)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：面向视频DiT的可训练稀疏注意力
5. [GeoShrink: Accelerating Diffusion Transformers with Two Lines of Code](/202609/29/2609.33723v1-geoshrink-accelerating-diffusion-transformers-with-two-lines-of-code)  
   标签：评分：9.0/10、query:diff-accel
   evidence：免训练扩散Transformer加速，基于锚点跳步
6. [WaveAlign: Cache-Aware Query-Row Scheduling for Sparse Attention in Long-Video Generation](/202609/29/2609.34814v1-wavealign-cache-aware-query-row-scheduling-for-sparse-attention-in-long-video-generation)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：面向长视频扩散生成动态稀疏注意力的缓存感知查询行调度
7. [TOLA: Text-aware One-Step Latent Adaptation for Diffusion-based Text Image Super-Resolution](/202609/29/2609.29240v1-tola-text-aware-one-step-latent-adaptation-for-diffusion-based-text-image-super-resolution)  
   标签：评分：8.0/10、query:diff-accel
   evidence：一步潜变量适配消除多步扩散的高计算成本与推理延迟

### 速读区论文标签
1. [Block Sparse Attention with Log-Linear Complexity](/202609/29/2609.31093v1-block-sparse-attention-with-log-linear-complexity)  
   标签：评分：8.0/10、query:sparse-attn
   evidence：对数线性选择复杂度的块稀疏注意力用于长上下文序列建模
2. [Carnator: Fast Text-to-Video Generation with Generation-Native Compatibility-Guided Cross-Request Reuse](/202609/29/2609.32420v1-carnator-fast-text-to-video-generation-with-generation-native-compatibility-guided-cross-request-reuse)  
   标签：评分：8.0/10、query:diff-accel
   evidence：跨请求复用的快速文本到视频生成
3. [In-Flight KV Cache with Clean Anchors for Faster Autoregressive Video Diffusion](/202609/29/2609.32540v1-in-flight-kv-cache-with-clean-anchors-for-faster-autoregressive-video-diffusion)  
   标签：评分：8.0/10、query:diff-accel
   evidence：复用缓存加速视频扩散推理
4. [Unlocking Few-Step Diffusion for Faithful Previews](/202609/29/2609.34406v1-unlocking-few-step-diffusion-for-faithful-previews)  
   标签：评分：8.0/10、query:diff-accel
   evidence：通过学习输入修正加速少步采样，无需重训即可跨采样预算迁移
5. [MoSAR: Mixture of Semantic Attention Regimes for Learning Adaptive and Approximable Attention Geometries](/202609/29/2609.31261v1-mosar-mixture-of-semantic-attention-regimes-for-learning-adaptive-and-approximable-attention-geometries)  
   标签：评分：7.0/10、query:sparse-attn
   evidence：学习式自适应稀疏注意力几何
6. [An End-to-End Latent-Rollout Approach for Pushing Few-Step ImageNet-$256$ Generation to FID $1.11$ without Fréchet Losses](/202609/29/2609.32376v1-an-end-to-end-latent-rollout-approach-for-pushing-few-step-imagenet-256-generation-to-fid-111-without-frchet-losses)  
   标签：评分：7.0/10、query:diff-accel
   evidence：少步蒸馏与端到端精炼加速图像生成
7. [From Position Risks to Block Survival: Faster Generation for Diffusion Language Models](/202609/29/2609.33390v1-from-position-risks-to-block-survival-faster-generation-for-diffusion-language-models)  
   标签：评分：7.0/10、query:diff-accel
   evidence：通过并行token预测加速扩散语言模型生成的框架
8. [WorldAttention: An Efficient Attention Architecture for Interactive Video World Models](/202609/29/2609.34606v1-worldattention-an-efficient-attention-architecture-for-interactive-video-world-models)  
   标签：评分：7.0/10、query:sparse-attn
   evidence：面向交互式视频世界模型的高效注意力，缓解注意力二次复杂度
9. [SparSP: Exploiting Communication Sparsity for Sequence-Parallel Video DiTs](/202609/29/2609.32197v1-sparsp-exploiting-communication-sparsity-for-sequence-parallel-video-dits)  
   标签：评分：6.0/10、query:sparse-attn
   evidence：面向视频扩散Transformer的稀疏注意力
10. [RefAdapt-DiT: Adaptive Joint Attention for Reference-Conditioned Diffusion Transformers](/202609/29/2609.32415v1-refadapt-dit-adaptive-joint-attention-for-reference-conditioned-diffusion-transformers)  
   标签：评分：6.0/10、query:diff-accel
   evidence：自适应联合注意力降低DiT条件生成计算量
11. [PulseQuant: Propagation-Guided Subspace Correction for 4-Bit Video Diffusion Transformers](/202609/29/2609.33384v1-pulsequant-propagation-guided-subspace-correction-for-4-bit-video-diffusion-transformers)  
   标签：评分：6.0/10、query:diff-accel
   evidence：面向视频扩散Transformer的4比特训练后量化加速
12. [Chameleon: Dynamic Format Adapter for Efficient Diffusion](/202609/29/2609.33496v1-chameleon-dynamic-format-adapter-for-efficient-diffusion)  
   标签：评分：6.0/10、query:diff-accel
   evidence：通过量化加速扩散模型推理


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
