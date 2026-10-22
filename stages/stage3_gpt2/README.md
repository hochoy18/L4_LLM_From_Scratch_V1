# Stage 3 — GPT-2 (2019.02)

- **对应论文**：*Language Models are Unsupervised Multitask Learners*
- **地位**：从「每任务微调」走向「什么都不改，直接用」的第一步。

## 目标

理解两件事：**规模（scale）带来的质变**，和 **prompt（zero/one/few-shot）范式**的出现。

## 从零实现 + 训练

- 不大改架构——沿用 Stage 2b 的 `core/decoder.py`。
- **把 mini-GPT 放大一点**（加宽/加深 + 更大语料），观察 perplexity 下降、能力提升。
- **重点：prompt 模式实验**——同一个模型，只改输入 prompt 的示范数量，看表现如何变化：
  - zero-shot：只给任务描述
  - one-shot：给 1 个例子
  - few-shot：给若干例子
- 观察：模型如何从上下文里的例子**推断出任务**并切换行为（无需改任何权重）。

## 跑现成（不训）

- 用 HF `gpt2`（124M，M1 可跑）**只做前向/生成**，对比你 mini-GPT 的生成质量，理解规模差距。

## 验收

- prompt 模式实验结论：few-shot 明显优于 zero-shot——**这就是今天所有 LLM 应用的直觉来源**。
- 能讲清：GPT-2 的关键不是新架构，是「更大模型 + 不微调直接 prompt」。

## 产出笔记

`notes/stage3-gpt2.html`：zero/one/few-shot 观察记录（含你自己的小模型数据）。