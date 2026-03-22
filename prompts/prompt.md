```markdown
# 新对话启动提示词（AI PM 平台型）

> 使用方法：开启新对话时，将本文件全部内容复制粘贴到第一条消息，并追加当周的 `plans/weekly-log.md` 最新一周日志，以及（如有）产品1的最新 demo 输出 `out.md`/`out.json` 摘要。

---

你好！请你先阅读以下背景信息，然后按我的指示继续推进我的产品计划与学习计划。

---

## 【我的身份与背景】

- 身份：浙江科技大学信息管理与信息系统专业学生，就业取向
- 当前日期：2026-03-22
- 目标角色：**AI 产品经理（平台型：Eval / Observability / 成本与可靠性 / 治理）**
- 总目标：在 **2027-08-31** 前完成 2 个可展示产品（作品集最大化）

---

## 【设备与约束】

- GPU：RTX 3060 6GB（WSL2 内 `nvidia-smi` 可用）
- 系统：Windows 11 + WSL2 + Ubuntu
- 网络约束（重要）：
  - WSL2 默认可能无法直连 GitHub / Hugging Face，需要通过 Windows 侧代理或开启 "Allow LAN" / 防火墙放行后再让 WSL 走代理。
  - 若出现 `curl: (28) Connection timed out` 或 `git clone RPC failed`，优先排查：WSL 是否能访问 Windows 代理端口（宿主机 IP + 端口），以及 git/curl 是否配置了代理。
- 主语言：Python（优先）

---

## 【两产品规划（到 2027-08-31）】

### 产品1（应用）：Vibe Coding-inspired（开发者工作流）
- 目标：做出可 demo 的 coding 工作流闭环（先 CLI，后 FastAPI）
- 重点展示：**Eval-first + Observability-first + 回归迭代 + 可靠性**
- 当前产品1演示仓库：`https://github.com/moonple/vibe-cli-sandbox`

### 产品2（平台）：Eval + Observability 小平台（差异化）
- 目标：把产品1沉淀的评测与可观测能力平台化
- 输出：版本对比报告、trace 检索、数据集管理

---

## 【产品1（Vibe Coding-inspired）当前实现状态（2026-03-22 更新）】

- 已完成：
  - `vibe` CLI（Typer + Rich）能运行：
    - `vibe run --repo . --task "..." --out out.md --json-out out.json`
  - 输出结构：`TaskResult(success,message,changes[])`，每个 change 有 `file/summary/diff`
  - 已补齐 `.gitignore`
  - 修复：mock diff 中 `task_description` 的 f-string 插值
- 当前仍为 mock runner：暂不接真实模型，先保证闭环可 demo、可复跑。

---

## 【本次对话目标】

请你以“平台型 AI PM + 工程落地”的角色辅助我，默认输出结构为：
1) 任务拆解（按 1-2 天粒度）
2) 验收标准（可量化）
3) 风险点与备选方案
4) 指标建议（Eval/Trace/性能/可靠性）

我会在这里填写具体任务，例如：
> 我现在在推进产品1的 Week N，本周要交付：xxx
> 我遇到的问题：xxx
> 我希望你输出：任务拆解 + 验收标准 + 风险点 + 指标

---

## 【最新进度日志】
（粘贴 `plans/weekly-log.md` 的最新一周）

---

## 【参考仓库】
- 背景/计划仓库：`https://github.com/moonple/career-llm-plan`
- 推理实验仓库：`https://github.com/moonple/llm-infer-deploy-lab`
- 产品1演示仓库：`https://github.com/moonple/vibe-cli-sandbox`

---

以上是我的背景信息。请确认你已理解，然后等待我的具体指令。
```