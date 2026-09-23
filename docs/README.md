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
- 最新运行日期：2026-09-23
- 运行时间：2026-09-23 22:00:28 UTC
- 运行状态：成功
- 本次总论文数：6
- 精读区：3
- 速读区：3

### 今日简报（AI）
今日精读3篇、速读3篇，聚焦LLM推理加速：块稀疏注意力解耦GPU执行、扩散LLM的IO感知KV缓存与并行解码双双拿下9.0分。最值得看的是这两篇9分工作揭示的同一思路——把注意力/缓存的计算与显存调度拆开优化，长上下文与扩散模型的推理瓶颈正被逐层击破。普通读者可先读这两篇精读的摘要与方法图，速读篇目留作扩展即可。
- 详情：[/202609/23/README](/202609/23/README)

### 精读区论文标签
1. [Decoupling Logical Masks from GPU Execution for Dynamic Block-Sparse Attention](/202609/23/2609.25869v1-decoupling-logical-masks-from-gpu-execution-for-dynamic-block-sparse-attention)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：面向视频扩散Transformer的动态块稀疏注意力
2. [Flash-dLLM: IO-Aware KV Caching and Parallel Decoding for Fast, Memory-Efficient Diffusion LLMs](/202609/23/2609.26796v1-flash-dllm-io-aware-kv-caching-and-parallel-decoding-for-fast-memory-efficient-diffusion-llms)  
   标签：评分：9.0/10、query:diff-accel
   evidence：面向扩散大语言模型的免训练推理加速
3. [HySparse2: Hybrid Sparse Attention with Two-Level KV Sharing](/202609/23/2609.26368v1-hysparse2-hybrid-sparse-attention-with-two-level-kv-sharing)  
   标签：评分：8.0/10、query:sparse-attn
   evidence：两级KV共享的混合稀疏注意力架构

### 速读区论文标签
1. [CompKV: Compensation-Aware KV Selection for Long-Context LLM Inference](/202609/23/2609.26300v1-compkv-compensation-aware-kv-selection-for-long-context-llm-inference)  
   标签：评分：7.0/10、query:sparse-attn
   evidence：面向LLM推理的补偿感知稀疏注意力KV选择
2. [Limits of Confidence in Diffusion](/202609/23/2609.20581v1-limits-of-confidence-in-diffusion)  
   标签：评分：6.0/10、query:diff-accel
   evidence：离散扩散语言模型并行解码的理论分析
3. [Diffusion Drafts, AR Verifies: Accelerating Document OCR with Self-Speculative Decoding](/202609/23/2609.26638v1-diffusion-drafts-ar-verifies-accelerating-document-ocr-with-self-speculative-decoding)  
   标签：评分：6.0/10、query:diff-accel
   evidence：扩散并行起草配合自回归验证以加速解码


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
