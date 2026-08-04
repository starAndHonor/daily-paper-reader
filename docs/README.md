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
- 最新运行日期：2026-08-04
- 运行时间：2026-08-04 21:16:34 UTC
- 运行状态：成功
- 本次总论文数：11
- 精读区：6
- 速读区：5

### 今日简报（AI）
今日精读11篇扩散模型论文，两篇最高分9.0聚焦“分歧加速”闭环与MoE推理优化。最值得关注这两个方向：前者提升生成效率，后者优化计算分配、降低推理成本。普通读者可先读DiffusionGemma技术报告，快速了解扩散语言模型的实际落地效果。
- 详情：[/202608/04/README](/202608/04/README)

### 精读区论文标签
1. [Disagree to Accelerate: Closing the Loop on Diffusion Feature Forecasts](/202608/04/2608.01740v1-disagree-to-accelerate-closing-the-loop-on-diffusion-feature-forecasts)  
   标签：评分：9.0/10、query:diff-accel
   evidence：基于特征预测分歧的免训练自适应信任机制，加速扩散采样
2. [REFLEX: Rethinking MoE Inference as Refinement-Aware Compute Allocation in Diffusion Language Models](/202608/04/2608.01784v1-reflex-rethinking-moe-inference-as-refinement-aware-compute-allocation-in-diffusion-language-models)  
   标签：评分：9.0/10、query:diff-accel
   evidence：精炼感知计算分配降低扩散语言模型推理中的每token专家计算量
3. [DAVET: Denoising-Aware Visual Evidence Trajectory Allocation for Diffusion Vision-Language Models](/202608/04/2608.01821v1-davet-denoising-aware-visual-evidence-trajectory-allocation-for-diffusion-vision-language-models)  
   标签：评分：9.0/10、query:diff-accel
   evidence：自适应视觉证据分配以加速扩散视觉语言模型推理
4. [WorldDynCache: Risk-Controlled Latent Dynamics Approximation for Diffusion World Model](/202608/04/2608.01845v1-worlddyncache-risk-controlled-latent-dynamics-approximation-for-diffusion-world-model)  
   标签：评分：9.0/10、query:diff-accel
   evidence：通过风险控制的缓存与潜动力学近似加速扩散世界模型推理
5. [Token Radius Attention for Efficient Video Generation](/202608/04/2608.02504v1-token-radius-attention-for-efficient-video-generation)  
   标签：评分：9.0/10、query:diff-accel
   evidence：面向视频扩散Transformer的训练无关注意力加速框架
6. [xPress: Parallel Refinement for Diffusion Drafters in Speculative Decoding](/202608/04/2608.02438v1-xpress-parallel-refinement-for-diffusion-drafters-in-speculative-decoding)  
   标签：评分：8.0/10、query:diff-accel
   evidence：针对投机解码中的块扩散草稿模型提出免训练并行精炼，提升文本生成速度

### 速读区论文标签
1. [WAM-Diff2: Hierarchical AR-to-Diffusion Distillation for Highly Efficient Autonomous Driving VLA](/202608/04/2608.01035v1-wam-diff2-hierarchical-ar-to-diffusion-distillation-for-highly-efficient-autonomous-driving-vla)  
   标签：评分：7.0/10、query:diff-accel
   evidence：将自回归VLA蒸馏为并行扩散模型，实现低延迟高效推理
2. [Conditioning Tree-Based Diffusions and Flows for Probabilistic Tabular Regression](/202608/04/2607.28864v1-conditioning-tree-based-diffusions-and-flows-for-probabilistic-tabular-regression)  
   标签：评分：6.0/10、query:diff-accel
   evidence：流匹配训练器支持少步确定性ODE采样
3. [DiffusionGemma Technical Report](/202608/04/2608.00146v1-diffusiongemma-technical-report)  
   标签：评分：6.0/10、query:diff-accel
   evidence：通过微调实现并行块扩散文本生成以加速推理
4. [A Unified Kullback--Leibler Divergence Analysis of Generative Diffusion Models via Entropy Production Rate](/202608/04/2608.02406v1-a-unified-kullback--leibler-divergence-analysis-of-generative-diffusion-models-via-entropy-production-rate)  
   标签：评分：6.0/10、query:diff-accel
   evidence：统一误差分析给出扩散采样器O(h^2)收敛率
5. [Computational and Statistical Guarantees of the \textit{c}-Rectified flow](/202608/04/2608.02487v1-computational-and-statistical-guarantees-of-the-textitc-rectified-flow)  
   标签：评分：6.0/10、query:diff-accel
   evidence：研究成本感知的修正流，这是加速扩散图像生成的框架


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
