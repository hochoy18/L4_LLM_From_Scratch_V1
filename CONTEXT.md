# CONTEXT — 术语表

> 本项目的统一语言。写代码注释、issue 标题、笔记、commit message 时，**用这里的词，不要飘移到同义词**。
> 缺失的词 = 真实缺口，记给 `/domain-modeling` 补录。

## 表示基础

| 术语 | 英文 | 一句话 |
|---|---|---|
| token | token | 文本切分后的最小单元（subword），模型实际吃的东西 |
| 词向量 | embedding / representation | token → 可学习的稠密向量；**静态**（word2vec，每个词一个）vs **上下文相关**（Transformer，每个位置一个） |
| 隐藏状态 | hidden state | 某层对每个 token 输出的向量表示 |
| 位置编码 | positional encoding | 注入"位置/顺序"信息，否则注意力对词序无感（Transformer 不用 RNN 的顺序） |
| 掩码 | mask | 控制"能看哪些位置"的规则；是本项目最核心的概念之一 |

## 注意力（Stage 1）

| 术语 | 英文 | 一句话 |
|---|---|---|
| 注意力 | attention | 用 query 和 key 的相似度给每个位置加权求和 value |
| 自注意力 | self-attention | Q/K/V 都来自同一序列（Transformer 的核心） |
| 查询/键/值 | query / key / value | 注意力的三角色 |
| 缩放点积 | scaled dot-product | `softmax(QKᵀ/√d_k)V`——除 `√d_k` 防 softmax 饱和 |
| 多头 | multi-head | 并行多组 QKV，各自关注不同关系，再拼接 |
| 因果掩码 | causal mask | 只允许看**左侧**（含自己）的 token——decoder 生成必需，保证不看未来 |
| 前馈网络 | FFN | 每个位置独立过的小 MLP，注意力之外的逐位置变换 |
| 残差 / 层归一化 | residual / LayerNorm | 深层可训练的基础设施 |

## 架构三型（Stage 2）

| 术语 | 英文 | 一句话 |
|---|---|---|
| 编码器 | encoder | 双向看全句，产出理解型表示（BERT 系） |
| 解码器 | decoder | 因果只看左侧，逐 token 生成（GPT 系 → LLM） |
| 编解码器 | encoder-decoder | 两段都有，原版 Transformer / T5 / BART |

## 预训练与微调（Stage 2a/2b）

| 术语 | 英文 | 一句话 |
|---|---|---|
| 预训练 | pretraining | 在海量无标注文本上先学语言规律（下一个词 / 掩码词） |
| 微调 | fine-tuning | 预训练后在小规模有标注数据上针对任务继续训练 |
| 掩码语言建模 | MLM / masked LM | BERT 的预训练目标：遮住若干 token 让模型猜（双向） |
| 下一词预测 | next-token / causal LM | GPT 的预训练目标：给定前缀预测下一个词（单向） |
| NSP | next sentence prediction | BERT 的辅助任务，预测两句是否相邻（后被证明可省） |
| logits | logit | 最后一层未归一化的分数，过 softmax 变概率 |
| 困惑度 | perplexity | 语言模型在留出集上的预测不确定性，越低越好 |

## 规模与能力（Stage 3/4）

| 术语 | 英文 | 一句话 |
|---|---|---|
| 零/少样本 | zero/one/few-shot | 不改权重，靠 prompt 里的例子让模型会新任务 |
| 上下文学习 | in-context learning | GPT-3 现象：任务示范放进上下文即可学会（不更新参数） |
| 涌现 | emergence | 规模变大后突然出现的能力（也可能是评测假象） |
| 缩放定律 | scaling law | 参数量/数据量/算力如何配套放大（Chinchilla：约 20 token/参数） |

## 对齐与现代（Stage 5/6）

| 术语 | 英文 | 一句话 |
|---|---|---|
| 指令微调 | SFT / instruction tuning | 用「指令→回答」示范数据微调，让模型听话 |
| 奖励模型 | reward model | 给回答打分，训 RL 的"目标" |
| RLHF | reinforcement learning from human feedback | 用人类反馈的奖励模型 + 强化学习做对齐（ChatGPT 幕后） |
| LoRA | low-rank adaptation | 冻结主模型、用低秩旁路做参数高效微调 |
| 混合专家 | MoE / mixture-of-experts | 路由到部分"专家"子网，稀疏激活、参数多但算力省 |
| 开源权重 | open-weight | 公开训练后权重的模型（LLaMA / Llama / Mistral 等） |

---

> 相关：决策记录见 [`docs/adr/`](./docs/adr/)，学习路线见 [`README.md`](./README.md)。