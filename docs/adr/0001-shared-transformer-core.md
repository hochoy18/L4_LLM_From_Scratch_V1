# ADR-0001：共享 Transformer 核心，各 stage 薄包装

- **状态**：已采纳
- **日期**：2026-10-22

## 背景

本项目沿 LLM 血脉分阶段学习（Transformer → BERT/GPT-1 → …）。一个关键事实：**BERT 和 GPT-1 本质是同一个 Transformer**，区别只在三点——① 用哪半边（encoder vs decoder）② 什么掩码（causal vs MLM）③ 什么训练目标（next-token vs masked-token）。

如果每个 stage 从零重写一遍 Transformer，这个洞察就丢了；读者/作者只会觉得「BERT 又是个新架构」。

## 决策

采用 **共享核心 + 薄包装** 结构：

- `core/` 写一次自注意力、embedding、encoder、decoder、FFN、masks，各 stage 复用。
- 每个 `stages/stageN_*/model.py` 只是对 `core` 的不同组装——换掩码、换头（task head）、换配置。

不采用：每 stage 完全独立目录，或单一代码库原地演进。两者都会把「共享骨架」这件事藏起来。

## 后果

- ✅ BERT 与 GPT-1 在代码里共享同一套 Transformer，看得出只差 mask/头/目标——**把教学洞察编码进结构**。
- ✅ 修一处 `core` 缺陷，所有 stage 受益。
- ⚠️ 交叉验证某 stage 时，若 `core` 有 bug 会波及所有 stage——需要 HF 对拍来兜底。
- ⚠️ 过度共享会让 `core` 抽象变复杂；原则是「共享结构，不共享策略」。