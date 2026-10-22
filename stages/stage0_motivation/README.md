# Stage 0 — 动机层：Transformer 之前

- **对应论文**：word2vec (2013) · seq2seq (2014) · Attention / Bahdanau (2015)
- **地位**：这是**动机层**，不重写 RNN/LSTM 全部，只求理解「为什么 Transformer 取代了它们」。

## 目标

1. 理解静态词向量的局限：一个词只有一个向量，无法表达上下文。
2. 理解 seq2seq 的**定长瓶颈**问题。
3. 理解 attention 如何解开瓶颈——**这是 Transformer 的直接动机**。

## 从零实现 + 训练

- **mini word2vec**（skip-gram + 负采样）：从零写，在小语料上真训，看近义词/类比关系。这是唯一要真训的模块。
- seq2seq / RNN / Bahdanau attention：**读 + 极简玩具实现**（能跑、能看 attention 权重即可，不追求性能）。

## 验收

- 画出 attention 权重热力图，能用它解释一句里不同词的关联。
- 能口述：RNN 的两个死穴（**不能并行** + **长程依赖/信息瓶颈**）→ Transformer 的解法（全注意力 + 可并行）。

## 交叉验证

不适用（无对应 HF 预训练权重）。

## 产出笔记

`notes/stage0-why-transformer.md`：为什么 RNN 被取代——用自己的话，不抄论文。