# Stage 2b — GPT-1 (2018.06) · DECODER 分支 · ★主线核心

- **对应论文**：*Improving Language Understanding by Generative Pre-Training* (Radford et al., OpenAI)
- **地位**：★★ **「预训练 + 微调」范式在此诞生**，是通往 LLM 的起点。★ 重点投入。
- **关键事实**：GPT-1 实际比 BERT **早几个月**（2018.06 vs 2018.10），两者都是 Transformer 的孩子，**不是前后关系**。

## 目标

理解 **decoder-only + 因果掩码 + 下一词预测**，亲手实现**第一个「预训练→微调」范式**。

## 从零实现

复用 `core/decoder.py`，只比 BERT 多两个改动——这正是共享 `core/` 的意义：

- **只保留 decoder 半边**（丢掉 encoder）。
- **因果掩码**替代 MLM 掩码（`core/masks.py`）：每个位置只能看左侧。
- **LM 头**：输出词表 logits + cross-entropy 损失。

> 对照 Stage 2a：你会发现 GPT-1 ≈ BERT 换了个 mask + 换个目标——**这就是 ADR-0001 的用意**。

## 训练

- **mini-GPT**（几层、几百万参数）：小英文语料上做因果语言建模预训练，看 loss/perplexity 下降。
- **微调**：预训练后针对一个下游文本任务微调。亲手复现 GPT-1 的核心主张：**预训练让微调在小数据上收敛更快更好**。

## 验收

- **交叉验证**：GPT 架构灌 `gpt2` 权重（GPT-1/GPT-2 同架构同词表规格），下一词 logits 与 HF 数值对齐。
- 能讲清：因果掩码为什么保证「不偷看未来」？训练时为什么每个位置都在预测？

## 产出笔记

`notes/stage2b-gpt1.md`：预训练→微调范式——为什么它统治了后续所有 NLP 模型。

> 下一步 → Stage 2c（和 BERT 对比）→ Stage 3（GPT-2）。