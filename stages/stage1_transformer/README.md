# Stage 1 — Transformer (2017)

- **对应论文**：*Attention Is All You Need* (Vaswani et al., 2017.6)
- **地位**：★ **整个项目的地基**。`core/` 的 Transformer 骨架在此一次性写好，后续所有 stage 复用。
- **投入**：★ 重点

## 目标

彻底理解并实现 Transformer 的**编解码器**结构：自注意力、多头、位置编码、残差+层归一化、FFN、因果掩码。

## 从零实现（写入 `core/`）

| 模块 | 内容 |
|---|---|
| `core/attention.py` | 缩放点积注意力、多头 |
| `core/embeddings.py` | token embedding + 位置编码 |
| `core/encoder.py` | encoder block / layer / stack |
| `core/decoder.py` | decoder block / layer / stack（带 causal mask） |
| `core/ffn.py` | position-wise 前馈网络 |
| `core/norms.py` | LayerNorm + 残差封装 |
| `core/masks.py` | causal mask / padding mask |
| `core/device.py` | `get_device()`（MPS/CPU 自动选） |

## 训练

- **mini Transformer seq2seq**：小规模平行语料（如 toy 英译中），几层、几百万参数，真训练到收敛。
- 手写训练循环（不借 HF Trainer）——这是理解训练机制的关键。

## 验收

- **交叉验证**：同一份权重、同一输入，你的实现 vs HF `BartModel`/`T5` 同构模型，输出 logits 数值对齐。
- 训到收敛，小翻译任务能看出效果。
- 能讲清：多头在多看什么？位置编码为什么必须有？

## 产出笔记

`notes/stage1-transformer.md`：结构图（自己画）+ 每个模块的作用。

> 下一步 → Stage 2a（BERT，encoder 半边）/ 2b（GPT-1，decoder 半边）。