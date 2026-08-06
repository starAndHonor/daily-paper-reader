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
- 最新运行日期：2026-07-08 ~ 2026-08-06
- 运行时间：2026-08-06 09:24:44 UTC
- 运行状态：成功
- 本次总论文数：26
- 精读区：15
- 速读区：11

### 今日简报（AI）
今日精读15篇、速读11篇，重点聚焦视频生成推理加速与注意力稀疏化方向；最值得关注的是Sol-Attn与SPADE两篇满分论文，均通过输入自适应稀疏注意力实现视频扩散模型高效推理；建议普通读者优先追踪视频生成模型的速度优化进展，以提升实际部署体验。
- 详情：[/20260708-20260806/README](/20260708-20260806/README)

### 精读区论文标签
1. [Sol-Attn: Accelerating Video Generation Inference via On-the-Fly Attention Sparsification](/20260708-20260806/2607.24027v1-sol-attn-accelerating-video-generation-inference-via-on-the-fly-attention-sparsification)  
   标签：评分：10.0/10、query:sparse-attn
   evidence：面向视频生成推理加速的免训练动态稀疏注意力
2. [SPADE: An Input-Adaptive Sparse Attention Engine for Fast Video Diffusion Models Inference](/20260708-20260806/2608.03335v1-spade-an-input-adaptive-sparse-attention-engine-for-fast-video-diffusion-models-inference)  
   标签：评分：10.0/10、query:sparse-attn
   evidence：面向视频扩散Transformer的免训练稀疏注意力引擎，动态选择关键Q/K/V token并实现端到端加速
3. [Sparse Attention for Dense Open-Vocabulary Prediction in CLIP](/20260708-20260806/2607.07135v2-sparse-attention-for-dense-open-vocabulary-prediction-in-clip)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：在CLIP视觉自注意力中用α-entmax替换softmax，实现推理时稀疏注意力
4. [COBS: Cumulant Order Block Sparse Attention](/20260708-20260806/2607.09052v1-cobs-cumulant-order-block-sparse-attention)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：将块稀疏注意力形式化为注意力质量排序以匹配稠密注意力
5. [LiteTopK: Exploiting the Curse of Dimensionality for a Fused Indexer-TopK Kernel in Long-Context Sparse Attention](/20260708-20260806/2607.11976v1-litetopk-exploiting-the-curse-of-dimensionality-for-a-fused-indexer-topk-kernel-in-long-context-sparse-attention)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：针对长上下文稀疏注意力的融合索引器-TopK内核
6. [FVAttn: Adaptive Sparse Attention with Runtime Load Balancing for Video Generation](/20260708-20260806/2607.16190v1-fvattn-adaptive-sparse-attention-with-runtime-load-balancing-for-video-generation)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：面向视频生成的自适应稀疏注意力训练自由路由
7. [ELSAA: Efficient Low-Rank and Sparse Attention Approximation for Training Transformers](/20260708-20260806/2607.20214v1-elsaa-efficient-low-rank-and-sparse-attention-approximation-for-training-transformers)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：注意力得分算子的低秩与稀疏近似
8. [Parameter-free Adaptive Sparse Attention via Compression-Based Content Selection](/20260708-20260806/2607.21752v1-parameter-free-adaptive-sparse-attention-via-compression-based-content-selection)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：用gzip压缩无需参数的自适应稀疏注意力内容选择
9. [OmniCache: Multidimensional Hierarchical Feature Caching For Diffusion Models](/20260708-20260806/2607.23844v1-omnicache-multidimensional-hierarchical-feature-caching-for-diffusion-models)  
   标签：评分：9.0/10、query:diff-accel
   evidence：面向免训练扩散推理加速的层次化特征缓存
10. [PIVOT: Efficient Query-Group Indexing for Token-Level Sparse Attention](/20260708-20260806/2607.24593v1-pivot-efficient-query-group-indexing-for-token-level-sparse-attention)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：面向Token级稀疏注意力的免训练查询组索引
11. [CoSA: Accelerating Long-Context Inference via Proxy-Kernel Co-Designed Sparse Attention](/20260708-20260806/2607.25291v1-cosa-accelerating-long-context-inference-via-proxy-kernel-co-designed-sparse-attention)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：代理与内核协同设计的块稀疏注意力，用于加速长上下文推理
12. [Recall Before You Rank: Similarity-Guided Top-$K$ Reuse for Efficient Long-Context Attention](/20260708-20260806/2607.27692v1-recall-before-you-rank-similarity-guided-top-k-reuse-for-efficient-long-context-attention)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：通过复用历史检索决策来加速动态Top-K稀疏自注意力，降低计算成本
13. [Token Radius Attention for Efficient Video Generation](/20260708-20260806/2608.02504v1-token-radius-attention-for-efficient-video-generation)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：面向高效视频扩散生成的Token半径稀疏注意力
14. [Maglev: Sliding Recurrent Memory](/20260708-20260806/2608.02870v1-maglev-sliding-recurrent-memory)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：基于滑窗稀疏注意力与递归记忆的Transformer架构，可直接用于高效序列建模
15. [Training-Free Hashing-Based Attention via Binary Principal Components](/20260708-20260806/2608.04405v1-training-free-hashing-based-attention-via-binary-principal-components)  
   标签：评分：9.0/10、query:sparse-attn
   evidence：面向长上下文序列建模的免训练数据感知哈希稀疏注意力

### 速读区论文标签
1. [AVQ-Attention: Adaptive Vector-Quantized Attention](/20260708-20260806/2607.12789v1-avq-attention-adaptive-vector-quantized-attention)  
   标签：评分：8.0/10、query:sparse-attn
   evidence：通过自适应码本分配将注意力复杂度从O(N^2)降至O(MN)，属于面向高效序列建模的稀疏化注意力机制
2. [Thresholded Cross-Attention for Reliable Intensity-Chromaticity Fusion in Low-Light Image Enhancement](/20260708-20260806/2607.13925v1-thresholded-cross-attention-for-reliable-intensity-chromaticity-fusion-in-low-light-image-enhancement)  
   标签：评分：8.0/10、query:sparse-attn
   evidence：阈值交叉注意力改进Top-K稀疏注意力用于图像增强
3. [RIS-Kernel: A Model-Agnostic Architecture for Long-Context LLM Inference via Sparse Attention](/20260708-20260806/2607.21927v1-ris-kernel-a-model-agnostic-architecture-for-long-context-llm-inference-via-sparse-attention)  
   标签：评分：8.0/10、query:sparse-attn
   evidence：模型无关的稀疏注意力架构，将长上下文LLM推理复杂度降至O(N log N)
4. [Causal Discovery with Inverted Self-attention for Multivariate Time Series](/20260708-20260806/2607.28212v1-causal-discovery-with-inverted-self-attention-for-multivariate-time-series)  
   标签：评分：8.0/10、query:sparse-attn
   evidence：提出倒置自注意力并通过稀疏化注意力分数进行因果发现
5. [LongCat Sparse Attention: Taming the Lightning via Streaming-aware Hierarchical Cross-Layer Indexing](/20260708-20260806/2608.01662v2-longcat-sparse-attention-taming-the-lightning-via-streaming-aware-hierarchical-cross-layer-indexing)  
   标签：评分：8.0/10、query:sparse-attn
   evidence：软硬协同设计的稀疏注意力与跨层索引
6. [ResKV: Reconstructing Omitted Attention Contributions for Fixed-Budget KV Cache Compression](/20260708-20260806/2607.29591v1-reskv-reconstructing-omitted-attention-contributions-for-fixed-budget-kv-cache-compression)  
   标签：评分：7.0/10、query:sparse-attn
   evidence：通过固定预算KV缓存压缩重建被遗漏的注意力贡献，提升推理效率
7. [Perspectives on Tsallis Statistics for Artificial Intelligence](/20260708-20260806/2608.01223v1-perspectives-on-tsallis-statistics-for-artificial-intelligence)  
   标签：评分：7.0/10、query:sparse-attn
   evidence：综述sparsemax与α-entmax等稀疏注意力机制
8. [Heterogeneous LLM Serving with General-Purpose Processing-Near-Memory for Retrieval-Based Sparse Attention](/20260708-20260806/2608.03555v1-heterogeneous-llm-serving-with-general-purpose-processing-near-memory-for-retrieval-based-sparse-attention)  
   标签：评分：7.0/10、query:sparse-attn
   evidence：基于检索的稀疏注意力服务与近内存计算
9. [The Key to Going Linear: Analysis-Driven Transformer Linearization](/20260708-20260806/2607.07706v1-the-key-to-going-linear-analysis-driven-transformer-linearization)  
   标签：评分：6.0/10、query:sparse-attn
   evidence：通过线性化降低因果自注意力的二次复杂度，目标与注意力稀疏一致
10. [Chimera: Designing and Chinchilla-Scaling Hybrid Visual Diffusion Transformers](/20260708-20260806/2607.28611v1-chimera-designing-and-chinchilla-scaling-hybrid-visual-diffusion-transformers)  
   标签：评分：6.0/10、query:sparse-attn
   evidence：面向图像/视频生成的亚二次注意力混合视觉扩散骨干
11. [SMM Transformer: Leveraging Spiking Neural Networks for Multimodal Tasks](/20260708-20260806/2608.01622v1-smm-transformer-leveraging-spiking-neural-networks-for-multimodal-tasks)  
   标签：评分：6.0/10、query:sparse-attn
   evidence：将密集注意力替换为基于脉冲的稀疏标记混合机制，属于深度学习中的稀疏化注意力机制


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
