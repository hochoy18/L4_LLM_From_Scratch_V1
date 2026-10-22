# Stage 5 — 对齐：指令微调 · LoRA · RLHF (2021–2022)

- **对应论文**：*InstructGPT* (Ouyang et al., 2021) · LoRA (Hu et al., 2021)
- **地位**：把语言模型变成「听话的助手」——ChatGPT 的幕后。按 ADR-0003：**LoRA 手写，RLHF 只读+跑**。

## 目标

理解 SFT + 奖励模型 + 强化学习（RLHF）这条对齐 pipeline，以及 LoRA 这种参数高效微调。

## 从零实现

- **LoRA 手写微调**：实现低秩旁路（`W + BA`，B、A 低秩），冻结主模型只训练低秩矩阵，在一个小模型上完成一次微调。**轻量高价值，直接串起 fine-tuning 机制。**

## 只读 + 跑（不手写）

- **RLHF**：读 InstructGPT / ChatGPT 技术报告，画出 **SFT → 奖励模型 → PPO(强化学习)** 三段 pipeline，理解每段在优化什么。
- 跑现成对齐模型（HF instruct 模型）做对话，感受「对齐后」的行为差异。

> **不手写 RLHF**：pipeline 重、边际价值低（见 ADR-0003）。理解到「长什么样、每段干什么」即可。

## 验收

- 画出 RLHF pipeline 图，能讲清：为什么不能只用 SFT？奖励模型在干嘛？
- LoRA 微调实验：对比全参数微调 vs LoRA，参数/效果差异。

## 产出笔记

`notes/stage5-rlhf.html`：RLHF pipeline + LoRA 微调实验。