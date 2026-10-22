# Stage 2a — BERT (2018.10) · ENCODER 分支

- **对应论文**：*BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding* (Devlin et al.)
- **地位**：B1 encoder 分支。理解「双向编码」的代表作，也是 GPT 主线的最佳对照组。

## 目标

理解 **encoder-only + 双向 + 掩码语言建模（MLM）**，以及「预训练 + 微调」范式在理解任务上的样子。

## 从零实现

复用 `core/encoder.py`，加 BERT 特有的部分：

- **MLM 头**：词表输出头 + 掩码预测损失。
- **输入格式**：`[CLS]` … `[SEP]`，segment embedding。
- **MLM 掩码策略**（`core/masks.py`）：随机遮 15%，其中 80%→`[MASK]`、10%→随机词、10%→保持原词。这是 BERT 精妙之处。
- NSP（下一句预测）作为可选辅助任务——**后被证明可省**，亲手体验其可有可无。

## 训练

- **mini-BERT**（2–4 层，几百万参数）：在小英文语料上做 MLM 预训练，看 loss 从随机掩码的高位下降。
- **微调**：预训练后接分类头，在小标注集（情感/意图）上微调，观察「有预训练 vs 无预训练」的收敛差距——**这就是预训练的价值**。

## 验收

- **交叉验证**：拿 `bert-base-uncased` 官方权重灌进你的 encoder，MLM logits 与 HF 数值对齐。
- 一页对比笔记：MLM（双向）与 next-token（单向）的目标差异。

## 产出笔记

`notes/stage2a-bert.md`：MLM 掩码策略为什么这么设计。

> 下一步 → Stage 2c（和 GPT-1 对比）→ Stage 2b（GPT-1 主线）。