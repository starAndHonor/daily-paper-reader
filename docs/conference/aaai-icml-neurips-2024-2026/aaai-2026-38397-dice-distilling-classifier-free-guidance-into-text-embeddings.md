---
title: "DICE: Distilling Classifier-Free Guidance into Text Embeddings"
title_zh: DICE：将无分类器引导蒸馏到文本嵌入中
authors: "Zhenyu Zhou, Defang Chen, Can Wang, Chun Chen, Siwei Lyu"
date: 2026-03-17
pdf: "https://ojs.aaai.org/index.php/AAAI/article/download/38397/42359"
tags: ["query:diff-accel"]
score: 7.0
evidence: 蒸馏无分类器引导以降低文本到图像扩散计算开销
tldr: 文本到图像扩散模型中，无分类器引导(CFG)提升文本-图像对齐但计算量大。DICE通过蒸馏将CFG方向融入文本嵌入，使采样过程无需CFG，计算复杂度减半。实验表明生成质量与CFG相当，但速度提升。该方法需要额外蒸馏训练，故非完全免训练加速。
source: AAAI-2026-Accepted
selection_source: conference_retrieval
figures_json: "[{\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-38397/fig-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 843, \"height\": 964, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-38397/fig-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 875, \"height\": 1057, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-38397/fig-003.webp\", \"caption\": \"\", \"page\": 0, \"index\": 3, \"width\": 1819, \"height\": 574, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-38397/fig-004.webp\", \"caption\": \"\", \"page\": 0, \"index\": 4, \"width\": 856, \"height\": 612, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-38397/fig-005.webp\", \"caption\": \"\", \"page\": 0, \"index\": 5, \"width\": 1815, \"height\": 717, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-38397/fig-006.webp\", \"caption\": \"\", \"page\": 0, \"index\": 6, \"width\": 858, \"height\": 701, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-38397/fig-007.webp\", \"caption\": \"\", \"page\": 0, \"index\": 7, \"width\": 1832, \"height\": 632, \"label\": \"Figure\"}, {\"url\": \"assets/figures/aaai-2026-accepted/aaai-2026-38397/fig-008.webp\", \"caption\": \"\", \"page\": 0, \"index\": 8, \"width\": 854, \"height\": 574, \"label\": \"Figure\"}]"
tables_json: "[{\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-38397/table-001.webp\", \"caption\": \"\", \"page\": 0, \"index\": 1, \"width\": 865, \"height\": 541, \"label\": \"Table\"}, {\"url\": \"assets/tables/aaai-2026-accepted/aaai-2026-38397/table-002.webp\", \"caption\": \"\", \"page\": 0, \"index\": 2, \"width\": 1845, \"height\": 1067, \"label\": \"Table\"}]"
motivation: CFG提升对齐效果但引入额外计算开销。
method: 蒸馏CFG到文本嵌入，使得采样时免去CFG计算。
result: 采样计算量减半，生成质量与CFG基本持平。
conclusion: 蒸馏方法可降低文本到图像扩散的推理成本。
---

## Abstract
Text-to-image diffusion models are capable of generating high-quality images, but suboptimal pre-trained text representations often result in these images failing to align closely with the given text prompts. Classifier-free guidance (CFG) is a popular and effective technique for improving text-image alignment in the generative process. However, CFG introduces significant computational overhead. In this paper, we present DIstilling CFG by sharpening text Embeddings (DICE) that replaces CFG in the sampling process with half the computational complexity while maintaining similar generation quality. DICE distills a CFG-based text-to-image diffusion model into a CFG-free version by refining text embeddings to replicate CFG-based directions. In this way, we avoid the computational drawbacks of CFG, enabling high-quality, well-aligned image generation at a fast sampling speed. Furthermore, examining the enhancement pattern, we identify the underlying mechanism of DICE that sharpens specific components of text embeddings to preserve semantic information while enhancing fine-grained details. Extensive experiments on multiple Stable Diffusion v1.5 variants, SDXL, and PixArt-\alpha demonstrate the effectiveness of our method.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **核心问题**：文本到图像扩散模型在无分类器引导（CFG）采样时，生成的图像常存在模糊、语义不准确的问题；而使用CFG虽然能显著提升文本-图像对齐质量，但每个采样步需要额外一次模型评估，导致计算开销翻倍。
- **研究动机**：现有蒸馏方法（如Guided Distillation、Plug-and-Play Distillation）虽能减少计算量，但需微调整个模型或训练大型辅助网络，参数量高达数亿，导致训练成本高、泛化性差。
- **整体含义**：本文提出DICE方法，将CFG的效果蒸馏到文本嵌入中，使无引导采样（只调用一次模型）即可达到与CFG相当的质量，从而在保持生成质量的同时将计算复杂度减半。

## 2. 论文提出的方法论

### 核心思想
- 训练一个轻量级的“锐化器”（sharpener）网络，对原始文本嵌入进行微调，使得在无引导采样时，模型预测的方向能逼近CFG引导下的方向。
- 锐化器仅在文本嵌入空间操作，不改变主扩散模型参数，因此推理时只需一次模型评估，且参数量极小（约2M）。

### 关键技术细节
1. **嵌入锐化公式**：  
   `c_ϕ = c + α · r_ϕ(c, c_null)`  
   其中 `c` 为原始文本嵌入，`c_null` 为空文本嵌入，`r_ϕ` 为可训练的锐化器，α 控制锐化强度。

2. **蒸馏损失**：  
   `L(ϕ) = E[ || ε_θ(x_t, c_ϕ) - ε_ω^{c_null}(x_t, c) || ]`  
   其中 `ε_ω^{c_null}` 为CFG的预测（需两次模型调用），`ε_θ` 为无引导预测（一次调用）。通过最小化两者差异，锐化器学会模仿CFG的效果。

3. **训练过程**（Algorithm 1）：
   - 冻结主扩散模型 `ε_θ`。
   - 从数据集中采样图像-文本对 `(x_0, c)`。
   - 随机采样时间步 `t`，生成加噪样本 `x_t`。
   - 计算CFG目标 `ε_ω^{c_null}`，以及锐化后的无引导预测 `ε_θ(x_t, c_ϕ)`。
   - 优化锐化器参数 ϕ。

4. **对负提示的支持**：  
   通过替换 `c_null` 为负文本嵌入 `c_n`，并引入语义偏移项 `β(c_n - c_null)`，使DICE也能处理负提示场景。

### 机制分析
- 文本嵌入由语义部分（<SOS> 及后续语义token）和填充部分（padding <EOS> token）组成。
- 实验发现：DICE主要锐化填充部分嵌入（余弦相似度仅0.23），而对语义部分改动很小（余弦相似度0.75）。
- 这解释了为何DICE能保持语义一致性同时增强图像细节。

## 3. 实验设计

### 数据集与场景
- **训练集**：MS-COCO 2017（用于训练锐化器）。
- **评估集**：同样使用MS-COCO 2017，以及DrawBench基准。
- **测试模型**：覆盖多种架构与风格——
  - Stable Diffusion v1.5（SD15）及多个变体（DreamShaper、AbsoluteReality、Anime Pastel Dream、DreamShaper PixelArt、3D Animation Diffusion）
  - SDXL（2.6B参数）
  - PixArt-α（DiT架构）

### Benchmark指标
- **FID**（Fr´echet Inception Distance）↓
- **CLIP Score** (CS) ↑
- **Aesthetic Score** (AS) ↑
- **HPS v2.1**（人类偏好评分）↑
- **DrawBench**（文本-图像对齐评分）

### 对比方法
- 原始无引导采样（ω=1）
- 原始CFG引导采样（ω=5）
- 朴素缩放文本嵌入（Scaling）
- Guided Distillation (GD, Meng et al. 2023)
- Plug-and-Play Distillation (PnP, Hsiao et al. 2024)

## 4. 资源与算力
- **未明确说明**：论文正文未提供具体GPU型号、数量或训练时长。
- 仅提到锐化器参数量极小（2M~5M），训练成本远低于GD（859M参数）和PnP（361M~1.3B参数）。
- 附录可能包含更多细节（原文提示参见Appendix），但当前文本中无额外信息。

## 5. 实验数量与充分性

### 实验数量
- **主实验结果**（Table 1）：在4个基础模型（SD15、DreamShaper、SDXL、PixArt-α）上报告了5项指标，共20组对比。
- **跨模型泛化实验**（Figure 7）：3个锐化器互相交叉应用于3个不同风格模型，共9组结果。
- **消融与机制分析**（Figure 6）：α强度实验（多组）；嵌入成分替换实验（Figure 4）；余弦相似度分析（文本中报告1000对样本统计）。
- **异常/长提示泛化**（Figure 8）：定性示例。

### 充分性评价
- **全面性**：覆盖了不同模型容量（0.6B~2.6B）、架构（U-Net vs DiT）、风格（写实、动漫、像素画等），较充分。
- **公平性**：所有方法使用相同的随机种子和20步DPM-Solver++采样器，对比基线为官方实现或复现。
- **缺失内容**：未报告训练锐化器的具体时长、未提供置信区间或方差（仅在余弦相似度中给出标准差）。消融实验种类相对较少（仅α强度和嵌入成分）。

## 6. 论文的主要结论与发现
1. DICE能在不引入CFG额外计算的情况下，生成质量与CFG引导采样相当，计算量减半。
2. DICE的锐化器参数量极低（<1%主模型），训练高效，且可跨模型、跨提示泛化。
3. 机制揭示：锐化主要作用于文本嵌入的填充部分，增强图像细节而不改变语义。
4. 锐化强度α可替代CFG的引导尺度，提供类似的灵活控制。
5. 在多个主流模型上DICE性能优于或匹敌现有蒸馏方法，且训练/推理开销更低。

## 7. 优点
- **高效性**：仅修改文本嵌入，无需改动扩散模型本身，推理只需一次模型调用。
- **轻量化**：锐化器参数量仅2M~5M，远低于GD（859M）和PnP（361M以上）。
- **强泛化性**：在一个模型上训练的锐化器可迁移到其他模型和未见过的提示。
- **机制可解释**：通过分析嵌入成分的改动，解释了为何保持语义同时提升细节。
- **支持负提示**：可扩展处理负提示场景，提升实用性。

## 8. 不足与局限
- **性能未完全收敛**：作者指出DICE的生成质量尚未完全达到CFG引导的水平，存在信息损失。
- **实验覆盖不足**：仅使用MS-COCO 2017数据集进行训练和评估，未在更大规模或更多样化的数据集（如LAION-5B）上验证。
- **缺乏算力报告**：未提供训练锐化器所需的GPU型号、数量、时间，不利于复现和资源估算。
- **消融实验有限**：仅分析了α强度和嵌入成分，未探索锐化器架构、训练数据量、不同引导强度ω的影响。
- **无统计显著性检验**：报告的所有指标均为单次结果，未给出多次实验的均值方差，可能存在偶然性。
- **依赖CFG教师**：训练仍需使用CFG生成的教师信号，因此并非完全无引导训练，仍需要CFG模型的前向计算（虽然可在训练时离线预计算）。

（完）
