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
- 最新运行日期：2026-08-24
- 运行时间：2026-08-24 20:50:01 UTC
- 运行状态：成功
- 本次总论文数：6
- 精读区：4
- 速读区：2

### 今日简报（AI）
今日精读聚焦扩散Transformer的精确参考缓存与长上下文稀疏注意力改造，速读覆盖VLM加速与MRI重建；最值得关注的是锚定缓存机制（9.0分）和BF1因果稀疏注意力（8.0分），分别解决生成效率与长序列成本问题；建议优先复现锚定缓存思路，并留意一致性模型在医学影像的潜力。
- 详情：[/202608/24/README](/202608/24/README)

### 精读区论文标签
1. [Anchoring Instruction Outside Mask: Exact Reference Caching for Efficient In-Context Diffusion Transformers](/202608/24/2608.21229v1-anchoring-instruction-outside-mask-exact-reference-caching-for-efficient-in-context-diffusion-transformers)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：扩散Transformer中的结构化稀疏注意力与精确参考缓存直接提升了图像生成的效率。
2. [BF1: A Causal Dyadic Sparse-Attention Retrofit for Efficient Long-Context Transformers](/202608/24/2608.20427v1-bf1-a-causal-dyadic-sparse-attention-retrofit-for-efficient-long-context-transformers)  
   标签：评分：8.0/10、query:sparse-attn
   evidence：确定性二进稀疏注意力改造，降低Transformer长上下文计算成本
3. [DiffVC-ONE: Diffusion-based Generative Video Compression with One-Step Video Diffusion Transformer](/202608/24/2608.20515v1-diffvc-one-diffusion-based-generative-video-compression-with-one-step-video-diffusion-transformer)  
   标签：评分：8.0/10、query:diff-accel
   evidence：提出一步视频扩散Transformer，降低视频扩散模型推理开销
4. [SlimDiffuSE: Towards Efficient Diffusion-Based Speech Enhancement using Slimmable Networks](/202608/24/2608.21188v1-slimdiffuse-towards-efficient-diffusion-based-speech-enhancement-using-slimmable-networks)  
   标签：评分：8.0/10、query:diff-accel
   evidence：可瘦身扩散网络降低语音增强推理复杂度

### 速读区论文标签
1. [Clustering and Token Denoising for Faster and More Robust VLMs](/202608/24/2608.19285v1-clustering-and-token-denoising-for-faster-and-more-robust-vlms)  
   标签：评分：7.0/10、query:sparse-attn
   evidence：一种免训练的令牌剪枝方法，通过聚类和去噪减少注意力计算，用于高效序列建模
2. [Consistency Models for Fast MRI Reconstruction Using Regularization by Denoising](/202608/24/2608.20561v1-consistency-models-for-fast-mri-reconstruction-using-regularization-by-denoising)  
   标签：评分：7.0/10、query:diff-accel
   evidence：一致性模型实现单步扩散采样，直接加速扩散模型推断，尽管面向MRI重建。


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
