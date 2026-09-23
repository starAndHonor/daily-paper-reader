# 日报 · 2026-09-23

- 生成时间：2026-09-23 22:00:28 UTC
- 当次推荐总数：6
- 精读区：3
- 速读区：3

## 今日简报（AI）
今天从 6 篇论文中筛出 3 篇精读、3 篇速读，聚焦 LLM 推理加速与扩散模型两个方向。
最值得看的是两篇 9.0 分精读：《Decoupling Logical Masks from GPU Execution for Dynamic Block-Sparse Attention》把逻辑掩码与 GPU 执行解耦，以及《Flash-dLLM》用 IO 感知的 KV 缓存与并行解码加速扩散 LLM，都指向"减少无效计算与显存搬运"这一主线。
普通读者若想快速上手，可先从这两篇精读的摘要与实验对比读起，再顺着速读里的 CompKV（长上下文 KV 选择）了解同类思路的取舍。

## 精读区
1. [Decoupling Logical Masks from GPU Execution for Dynamic Block-Sparse Attention](/202609/23/2609.25869v1-decoupling-logical-masks-from-gpu-execution-for-dynamic-block-sparse-attention) （9.0/10）
2. [Flash-dLLM: IO-Aware KV Caching and Parallel Decoding for Fast, Memory-Efficient Diffusion LLMs](/202609/23/2609.26796v1-flash-dllm-io-aware-kv-caching-and-parallel-decoding-for-fast-memory-efficient-diffusion-llms) （9.0/10）
3. [HySparse2: Hybrid Sparse Attention with Two-Level KV Sharing](/202609/23/2609.26368v1-hysparse2-hybrid-sparse-attention-with-two-level-kv-sharing) （8.0/10）

## 速读区
1. [CompKV: Compensation-Aware KV Selection for Long-Context LLM Inference](/202609/23/2609.26300v1-compkv-compensation-aware-kv-selection-for-long-context-llm-inference) （7.0/10）
2. [Limits of Confidence in Diffusion](/202609/23/2609.20581v1-limits-of-confidence-in-diffusion) （6.0/10）
3. [Diffusion Drafts, AR Verifies: Accelerating Document OCR with Self-Speculative Decoding](/202609/23/2609.26638v1-diffusion-drafts-ar-verifies-accelerating-document-ocr-with-self-speculative-decoding) （6.0/10）

---
使用键盘方向键可在日报/论文之间快速切换。
