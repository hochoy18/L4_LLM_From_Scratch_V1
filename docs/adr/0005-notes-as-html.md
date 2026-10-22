# ADR-0005：阶段笔记用 HTML，不用 Markdown

- **状态**：已采纳
- **日期**：2026-10-22

## 背景

每个阶段的产出笔记放在 `notes/`。本项目多处验收标准是**可视化的**——最典型的是 **Stage 0 的 attention 权重热力图**，以及 Stage 1 要自己画的 Transformer 结构图、Stage 3 的 prompt 模式对比。Markdown 渲染不了图表，只能贴静态图片或退化成纯文字描述，损失了这些交付物的本意。

## 决策

`notes/` 下的阶段笔记一律用 **`.html`**：`notes/stageN-*.html`。各 stage README 里的「产出笔记」路径统一指向 `.html`。

共享样式（若需要）放 `notes/assets/`，多篇笔记共用一套 CSS，让它们看起来像一份连贯的学习文档而不是一堆散页。

## 后果

- ✅ 可内嵌 SVG/Canvas 图表：attention 热力图、结构图、loss 曲线、prompt 对比表都能直接渲染。
- ✅ 浏览器直接打开即可阅读，无需 Markdown 渲染器。
- ⚠️ HTML 不便于纯文本 diff/grep——但笔记是**产出物**不是**协作源文件**，可接受；变更走 git。
- ⚠️ 若笔记变多，建议补一个 `notes/assets/style.css` 共享样式，避免每篇各自为政。