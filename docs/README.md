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
- 最新运行日期：2026-08-22
- 运行时间：2026-08-22 20:56:08 UTC
- 运行状态：成功
- 本次总论文数：8
- 精读区：5
- 速读区：3

### 今日简报（AI）
今日精读聚焦自动驾驶世界模型与视频生成效率，速读覆盖3D生成、条件扩散及紧凑图像模型。最值得关注《DriveCache》的动作感知缓存与《SQuad》的次二次注意力蒸馏，两者均获9.0高分。建议普通读者优先了解高效推理技术对降低生成成本的实际影响。
- 详情：[/202608/22/README](/202608/22/README)

### 精读区论文标签
1. [DriveCache: Action-Aware Caching for Driving World Model Inference](/202608/22/2608.16354v1-drivecache-action-aware-caching-for-driving-world-model-inference)  
   标签：评分：9.0/10、query:diff-accel
   evidence：免训练、动作感知的缓存复用方法加速扩散式驾驶视频生成推理
2. [SQuad: Sub-Quadratic Attention Distillation for Efficient Video Generation](/202608/22/2608.16585v1-squad-sub-quadratic-attention-distillation-for-efficient-video-generation)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：通过亚二次注意力蒸馏降低视频扩散Transformer自注意力开销，匹配视频生成中的稀疏注意力需求。
3. [FlashPrefill V2: Block-Sparse Prefill Attention for Long-Context LLM Serving](/202608/22/2608.19758v1-flashprefill-v2-block-sparse-prefill-attention-for-long-context-llm-serving)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：块稀疏预填充注意力直接降低长上下文服务中的注意力计算开销
4. [Learning how to Forget: Fine-tuning for Long-Context Sparse Attention](/202608/22/2608.19920v1-learning-how-to-forget-fine-tuning-for-long-context-sparse-attention)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：面向长上下文稀疏注意力与KV缓存压缩的微调方法
5. [Continuous Adversarial MeanFlow Transfer](/202608/22/2608.19540v1-continuous-adversarial-meanflow-transfer)  
   标签：评分：8.0/10、query:diff-accel
   evidence：通过MeanFlow-Transfer实现扩散/流模型的少步快速采样

### 速读区论文标签
1. [Block3D: Efficient Text-to-3D Generation via Block-Wise Diffusion](/202608/22/2608.19567v1-block3d-efficient-text-to-3d-generation-via-block-wise-diffusion)  
   标签：评分：7.0/10、query:diff-accel
   evidence：分块扩散降低扩散/流匹配生成推理成本
2. [A Plug-in Interpretation of Conditioning in Score-Based Diffusion Models](/202608/22/2608.19504v1-a-plug-in-interpretation-of-conditioning-in-score-based-diffusion-models)  
   标签：评分：6.0/10、query:diff-accel
   evidence：改善基于分数的扩散模型ODE采样质量，支持更快的推理
3. [Swift-Image: Exploring the Performance Frontier of Compact Unified Image Generation Models](/202608/22/2608.20334v1-swift-image-exploring-the-performance-frontier-of-compact-unified-image-generation-models)  
   标签：评分：6.0/10、query:diff-accel
   evidence：面向高效紧凑DiT图像生成，通过渐进训练与蒸馏实现更快的图像生成


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
