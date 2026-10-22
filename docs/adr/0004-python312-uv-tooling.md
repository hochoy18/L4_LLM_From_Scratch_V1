# ADR-0004：Python 3.12 + uv 做工程管理，每 stage 一个 git commit

- **状态**：已采纳
- **日期**：2026-10-22

## 背景

本项目要在 M1 / 8GB 上反复装依赖（尤其 torch）。原先环境是 miniconda base，全局共享，不适合堆项目依赖。多阶段学习还需要**环境可复现**——回到任意历史 stage 时能还原当时的依赖。

## 决策

- **Python 3.12**：`.python-version` + `pyproject.toml` 的 `requires-python = ">=3.12,<3.13"` 锁定。选 3.12 而非 3.13 是因为 torch 等包对 3.12 支持更成熟。
- **uv**：单一工具管 venv + 依赖 + lock。根目录**一个共享 `.venv`**（各 stage 依赖相同，不切环境）。`uv.lock` 锁定精确版本并提交进 git。
- **git**：每个 stage 一个 commit，commit message 带对应论文名。commit 历史 = 学习血缘时间线。

## 后果

- ✅ 项目环境与 miniconda base 隔离，不污染全局、不与其它项目打架。
- ✅ `uv.lock` + 每 stage commit ⇒ 实验可复现，随时回退。
- ✅ uv 装 torch 在 M1 上很快，迭代体验好。
- ⚠️ 需要在写代码阶段执行 `uv venv --python 3.12 && uv sync` 拉起环境。