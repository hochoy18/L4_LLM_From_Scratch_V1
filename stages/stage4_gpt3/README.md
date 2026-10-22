# Stage 4 — GPT-3 (2020.05) · in-context learning

- **对应论文**：*Language Models are Few-Shot Learners*
- **地位**：「不改权重也能学会新任务」的震撼演示，**「LLM」一词从此流行**。

## 目标

理解 **in-context learning（上下文学习）**：任务示范放进 prompt，模型不更新任何参数就能完成新任务——这与之前「每任务微调」范式形成鲜明对比。

## 实践

- **规模现实**：175B 无法训练（M1/8GB 更是远不够）。本阶段重点是**理解现象，不是复现规模**。
- **复现 in-context learning**：在你的 mini-GPT（或一个小 HF 模型）上，设计一个 few-shot prompt，展示「不训练、只给例子」就完成任务切换。
- **理解「涌现」讨论**：能力随规模出现的现象，也可能是评测指标的假象——理解这个争议。
- **和微调对比**：Stage 2b 的微调（改权重）vs 本阶段的 in-context（不改权重），同一任务两种解法的取舍。

## 可选：中文小实验

按 ADR-0002，主线英文；若想摸中文，可在你的 mini 模型上加一个**中文语料的预训练/生成**小实验（不进主线验收）。

## 验收

- 亲手演示一次 in-context learning，产出一段可复现的 prompt + 输出。
- 能讲清：in-context learning 和 fine-tuning 的区别，以及它们各自适合什么场景。

## 产出笔记

`notes/stage4-gpt3.md`：in-context learning 复现记录 + 和微调的对比。