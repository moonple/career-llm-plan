# 关键结论与常见问答（AI 产品经理 · 平台型）

本文件记录历次对话中沉淀的重要决策、方向选择依据和常见概念解释，避免重复讨论。

---

## 方向选择（面向 AI 产品经理）

### Q：AI PM 的 A / B / C 三个方向怎么选？

| 方向 | 内容 | 是否适合当前 |
|------|------|------------|
| **A：AI 应用 / Agent 产品** | 面向具体用户场景（开发、办公、教育等），核心是体验与闭环 | ✅ 用作产品1（Vibe Coding-inspired 属于开发者场景应用） |
| **B：AI 工程化/平台产品** | 评测、可观测、成本/配额、编排、治理、Prompt/策略管理等 | ✅✅ 主攻方向（平台型 AI PM） |
| **C：算法/训练主导型** | 训练、微调、模型结构/论文驱动 | ⚠️ 非主攻（设备与时间成本不匹配作品集最大化） |

**结论（2026-03-21 起）**：主线选 **B（平台型 AI PM）**，产品落地采用：
- **产品1（应用）**：做一个开发者工作流 AI 应用（Vibe Coding-inspired），但把 **平台能力外显**（Eval/Trace/回归）
- **产品2（平台）**：把产品1沉淀出来的能力抽象成 **Eval/Observability 小平台**

---

## 平台型 AI PM 的作品集能力栈（必须覆盖）

1. **Eval-first（评测优先）**
   - 有固定用例集（30→100+）
   - 有版本对比（v1/v2）
   - 指标：成功率、失败类型分布、关键任务完成率

2. **Observability-first（可观测优先）**
   - Trace：request_id、阶段耗时、token 统计、错误码
   - 指标：TTFT、tokens/s、队列等待时间（如果有队列）

3. **可靠性（Reliability）**
   - 超时、取消、重试、降级策略
   - 并发控制/排队（避免卡死与雪崩）

4. **成本与性能（Cost/Latency）**
   - 记录配置与性能关系（ctx/batch/并发）
   - 用数据推动取舍：质量 vs 延迟 vs 成本

5. **版本化与回归（Release/Regression）**
   - 每次改动（prompt/策略/参数）都有回归结果
   - 形成迭代记录：为什么改、改了什么、指标变化

---

## 常见问答

### Q：产品1先做 Vibe Coding 原型，“抄/模仿”合适吗？
合适。价值不在 UI 原创，而在：
- 复现开发者 coding 工作流闭环（输入→计划→生成→验证→复盘）
- 用 Eval/Trace 把质量与可靠性做成可展示的“平台能力”
- 这条路径最容易产出可量化作品集

### Q：产品1和产品2怎么避免重复？
- 产品1：面向用户任务闭环（开发者使用）
- 产品2：面向“开发者/团队的质量体系”（评测、对比、追踪、报告）

---

## 关键术语（精简）
| 术语 | 说明 |
|------|------|
| **TTFT** | 首 token 延迟 |
| **TPOT** | 每 token 平均时间 |
| **Throughput** | tokens/s |
| **Trace** | 请求链路与阶段耗时记录 |
| **Eval** | 固定用例集 + 版本对比 |
| **OOM** | 显存不足崩溃 |
| **SSE** | 流式输出协议 |

---

## 决策记录

| 日期 | 决策内容 |
|------|----------|
| 2026-03-21 | 目标转为 AI 产品经理（平台型） |
| 2026-03-21 | 2027-08-31 前完成 2 个产品：产品1（应用+平台能力外显）+ 产品2（平台化） |
| 2026-03-21 | 12 周计划重排为 2026-03-23 ~ 2026-06-14，聚焦产品1交付 |



## 2026-03-21 对话总结：AI PM 路线 + 产品1（Vibe Coding-inspired）立项

### 当前时间
- 当前日期：2026-03-21

### 总目标（2026-03-21 ~ 2027-08-31）
- 目标角色：AI 产品经理（技术型/工程落地 / 平台型倾向）
- 2027 年暑假前完成 2 个产品：
  - 产品 1：Vibe Coding-inspired（先模仿/复刻思路与实现）
  - 产品 2：Differentiated（在产品 1 的基础上做差异化）

### 关键决策（本次对话确认）
1. 产品 1 先做 CLI，再升级 FastAPI：
   - 结论：可行且推荐（先把核心能力做成可复用模块，CLI/FastAPI 只是一层入口）
   - 迁移难度：低（新增 API 层即可，核心 `core/` 复用）
2. 产品 1 先不接真实模型，使用 Mock 后端（选项 B）：
   - 目的：优先做出“Vibe Coding 的产品体验”（结构化输出 + 可演示闭环），避免卡在模型/部署/网络/性能
   - 后续：再替换 backend 接入 llama.cpp（OpenAI 兼容接口）或云 API
3. 产品 1 需要单独建一个专用仓库做“可复跑评测/演示”的目标 repo：
   - 新仓库名：`moonple/vibe-cli-sandbox`

### 产品 1 的 MVP 定义（Vibe Coding-inspired 的可落地抽象）
- 核心体验：输入自然语言任务，输出结构化结果：
  - Plan（步骤拆解）
  - Patch（MVP 先做“修改点列表”，后续升级为 unified diff）
  - Commands（可运行的验证命令）
  - Fallback（失败兜底/回滚/排障建议）
- 目标：先做出可 demo、可复跑的最小闭环，而不是做 IDE 级别自动写项目

### CLI 方案（已确定）
- 命令形态（方案一）：
  - `vibe run --repo <path> --task "<text>" --out out.md`
  - 可选：`--json-out out.json`
- 当前选择的 backend：mock（暂不接模型）

### 计划中的仓库结构（产品1：vibe-cli-sandbox）
建议目录结构（核心逻辑可复用，为后续 FastAPI 预留）：
- `src/vibe_cli_sandbox/core/`：核心逻辑（schema / generate / backends）
- `src/vibe_cli_sandbox/cli.py`：CLI 入口（只做参数解析与调用 core）
- `docs/`：输出 schema、demo script
- `eval/`：cases_v0、scoring（先人工评分也可）
- `examples/`：任务输入与输出样例（用于展示）

### MVP 文件清单（需要在 vibe-cli-sandbox 仓库落地）
- `pyproject.toml`（依赖：pydantic/typer/rich，提供 `vibe` 命令）
- `src/vibe_cli_sandbox/core/schema.py`（VibeResult 输出结构）
- `src/vibe_cli_sandbox/core/backends/mock.py`（mock 生成）
- `src/vibe_cli_sandbox/core/generate.py`（core 入口）
- `src/vibe_cli_sandbox/cli.py`（CLI 命令实现）
- `docs/output-schema.md`（输出规范）
- `eval/cases_v0.md`、`eval/scoring.md`（评测用例与评分规则）
- `docs/demo-script.md`（3 分钟 demo 路径）
- `examples/task_bugfix.md`、`examples/output_bugfix.md`（示例）

### 本周可执行下一步（最小闭环）
1. 在 `moonple/vibe-cli-sandbox` 提交上述脚手架文件
2. 本地验证：
   - `python -m pip install -e .`
   - `vibe run --repo . --task "Fix a bug and add tests" --out out.md --json-out out.json`
3. 产出物：
   - `out.md`（结构化输出）
   - `out.json`（结构化 JSON）

### 下个对话的启动方式（给 Copilot 的指令）
请���于上述决策继续推进：
- 先确保 `moonple/vibe-cli-sandbox` 仓库里脚手架文件齐全且可运行
- 然后进入迭代：
  1) 增加 repo 扫描摘要（文件树/关键文件片段作为 context）
  2) Patch 从“修改点列表”升级为 unified diff
  3) 增加 `eval/run_cases.py`（先 mock 打分，后接真实模型）
  4) 预留 FastAPI 版本 `POST /generate`（复用 VibeResult）
