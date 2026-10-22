# L4_LLM_From_Scratch_V1

> 从零实现 LLM 血脉：**Transformer → BERT / GPT-1 → GPT-2 / GPT-3 → LLM**，按阶段把「LLM 是怎么来的」亲手造一遍。

## 为什么有这个项目

日常用的 LLM / agent / RAG 都在**应用层**——调 API、拼 prompt、串工具。这个项目要补的是**应用层底下那一层机制**：自注意力到底怎么算、encoder/decoder 怎么用同一个骨架搭出 BERT 和 GPT、预训练+微调范式为什么长这样、RLHF 在调什么。

**看懂机制，才能判断应用层的问题出在哪**（是模型不会、还是 prompt 没写好、还是检索没喂对）。这才是从「会用 LLM」到「懂 LLM」的跃迁。

## 学习理念

1. **从零实现**：每个模型用裸 PyTorch 手写（`nn.Module`、手写 attention、手写训练循环），不调库。
2. **真训练缩小版**：不追求复现论文指标，只训几百万参数的小模型在小语料上——**loss 曲线怎么动、masking 怎么让 loss 先高后降，只有真训过才懂**。
3. **读原论文**：每阶段配读原论文几小时，别只看代码。
4. **HF 交叉验证**：实现完后拿 HuggingFace 官方权重/实现对拍（数值对齐），抓静默 bug。**HF 只用于验证，不进训练路径。**

## LLM 血脉树

```
🌳 序列建模 / 语言模型
│
├─【A. Transformer 之前：地基（2013–2016）】——提供"为什么要有 Transformer"的动机
│   ├─ 词向量：2013 word2vec · 2014 GloVe（静态表示）
│   ├─ 循环网络：1997 LSTM · 2014 GRU · 2003 神经语言模型
│   ├─ 2014 seq2seq（定长瓶颈）
│   └─ 2015 Attention / Bahdanau（解开瓶颈）
│
└─【B. Transformer (2017.6) "Attention Is All You Need"】★唯一真正的分叉点
    │
    ├─【B1 ENCODER 分支：理解 / 判别式】
    │   ├─ 2018.10 BERT（MLM+NSP）★ → RoBERTa / ALBERT / ELECTRA
    │   └─ 2020 DeBERTa（解耦注意力）
    │
    ├─【B2 DECODER 分支：生成 → ★LLM 主线】
    │   ├─ 2018.06 GPT-1（预训练+微调范式诞生）★
    │   ├─ 2019.02 GPT-2（零样本）
    │   ├─ 2020.05 GPT-3（少样本/上下文学习，"LLM"流行）
    │   └─ 指令微调 / RLHF → 2022.11 ChatGPT
    │
    ├─【B3 ENCODER-DECODER 统一：text-to-text】2019 XLNet · BART · T5
    │
    └─【B4 正交的改进轴】——改进 decoder 的方法论（2021→2024）
        缩放定律 Chinchilla · LoRA · FlashAttention · MoE · 开源权重 LLaMA
```

**看懂树 ≠ 看懂三条线**：
- **A 段是动机层**，不重写 RNN/LSTM，只理解「为什么被取代」→ 对应 **Stage 0**。
- **B1/B2 是分岔后几乎不汇合的两条路**。LLM 活在 B2；BERT 是理解「encoder 为何在理解任务强、生成任务弱」的最佳对照组 → 对应 **Stage 2a/2b/2c**。
- **B4 不是时间线，是「改进 decoder」的方法论轴**，挂在 B2 主线旁 → 对应 **Stage 5/6**。

## 阶段路线（9 阶段，decoder 主线更深）

| 阶段 | 对应论文 | 从零实现 + 训练什么 | 验收 |
|---|---|---|---|
| **0 动机** | word2vec · seq2seq · Bahdanau | mini word2vec（skip-gram+负采样）真训；RNN 瓶颈/attention 理解 | 画出 attention 热力图，讲清 RNN 为何被取代 |
| **1 Transformer**★ | Attention Is All You Need | `core/` 全套（自注意力/多头/位置编码/encoder/decoder）+ mini seq2seq | HF 同权重输出对齐；训到收敛做小翻译 |
| **2a BERT** | BERT | `core`-encoder + MLM 头 + [CLS]/[SEP]；mini-BERT 预训练 + 分类微调 | `bert-base` 权重 MLM logits 对齐 |
| **2b GPT-1**★ | GPT-1 | `core`-decoder + causal LM 头；mini-GPT 预训练 + 微调 | `gpt2` 权重 logits 对齐；见预训练加速微调 |
| **2c 对比** | 复习 BERT vs GPT-1 | 同一 `core` 两 wrapper 实验（只换 mask/头/目标） | 一页 encoder-vs-decoder 对比笔记 |
| **3 GPT-2** | GPT-2 | 放大 mini-GPT + zero/one/few-shot prompt 实验 | prompt 模式实验；跑 HF gpt2 生成 |
| **4 GPT-3** | GPT-3 | in-context learning 复现（不改权重也学新任务） | 「不改权重也能学」的演示笔记 |
| **5 对齐** | InstructGPT | **LoRA 手写**微调小模型；RLHF 只读+跑（pipeline 太重） | 画出 SFT→RM→PPO pipeline |
| **6 现代** | Chinchilla·LLaMA·MoE | 只读+跑；开源权重谱系 | 一页现代 LLM 技术地图 |

★ = 投入更多。每个 stage 一个 git commit，commit message 带论文名。

## 目录结构

```
L4_LLM_From_Scratch_V1/
├── README.md              ← 本文件：学习地图
├── CONTEXT.md             ← 术语表（统一语言）
├── pyproject.toml         ← 工程配置（uv）
├── .python-version        ← 3.12
│
├── core/                  ← 共享 Transformer 核心（写一次，各 stage 复用）
│   ├── attention.py       ← 缩放点积 + 多头
│   ├── embeddings.py      ← token / 位置编码
│   ├── encoder.py  decoder.py  ffn.py  norms.py
│   ├── masks.py           ← causal / padding / MLM 掩码
│   └── device.py          ← MPS/CPU get_device() 助手
│
├── stages/                ← 每阶段一个目录
│   ├── stage0_motivation/
│   ├── stage1_transformer/
│   ├── stage2a_bert/
│   ├── stage2b_gpt1/
│   ├── stage2c_compare/
│   ├── stage3_gpt2/
│   ├── stage4_gpt3/
│   ├── stage5_align_rlhf/
│   └── stage6_modern/     ← 每个目录：README + model/train/data/hf_check
│
├── data/                   ← 语料（缓存已 gitignore）
├── notes/                  ← 各阶段学习笔记（HTML，可内嵌图表/交互）
└── docs/
    ├── adr/               ← 关键决策记录
    └── agents/            ← agent 配置（issue tracker / labels / domain）
```

**核心设计**：BERT 和 GPT-1 本质是**同一个 Transformer**，区别只在①用哪半边②什么掩码③什么目标。用共享 `core/` + 各 stage 薄包装，把这个洞察**编码进代码结构**——写 BERT 时你会发现它就是上一阶段的 GPT 换了个 mask。

## 工程管理

- **uv** · **Python 3.12**（`.python-version` / `requires-python`）· 单 `.venv`
- 首次拉起环境（代码阶段再执行）：
  ```bash
  uv venv --python 3.12
  uv sync
  ```
- torch 在 M1 上走 **MPS** 后端；`core/device.py` 的 `get_device()` 自动选 `mps`/`cpu`。
- 每 stage 一个 commit，可复现：`uv.lock` 锁定依赖版本。

## 当前状态

- ✅ 文档与工程配置已落地（README / CONTEXT / ADR / 各 stage 说明 / pyproject）
- ⬜ 代码未开始（`core/`、`stages/*.py` 待写）
- ⬜ 环境未创建（`uv venv` / `uv sync` 待执行）

> `main.py`（PyCharm 样板）待清理，可在写代码时删除。

## 相关文档

- 术语表：[`CONTEXT.md`](./CONTEXT.md)
- 决策记录：[`docs/adr/`](./docs/adr/)
- agent 规范：[`docs/agents/`](./docs/agents/)、[`AGENTS.md`](./AGENTS.md)