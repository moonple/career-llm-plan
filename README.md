# career-llm-plan

> **这个仓库用来保存我的关键文件内容，以免聊天记录太长而丢失内容。**

每次开启新对话时，将本仓库中的关键文件内容复制到对话开头，即可完整恢复上下文，避免因聊天记录过长导致早期信息丢失。

---

## 仓库结构

```
career-llm-plan/
├── README.md                     # 本文件：仓库说明与工作流指引
├── context/
│   ├── profile.md                # 个人背景、学历、目标岗位
│   ├── constraints.md            # 设备环境、工作流约束
│   └── qa.md                     # 方向选择关键结论与常见问答
├── plans/
│   ├── 12-week-plan.md           # 12 周学习计划（2026-03-16 起）
│   └── weekly-log.md             # 每周进度日志（模板 + 历史记录）
├── prompts/
│   └── prompt.md                 # 新对话启动提示词（直接复制粘贴）
└── projects/
    └── llama-cpp-wsl/
        └── README.md             # llama.cpp WSL 推理记录与命令
```

---

## 推荐工作流

### 开启新对话时
1. 打开 [`prompts/prompt.md`](prompts/prompt.md)，复制全部内容，粘贴到新对话的第一条消息。
2. 再追加当周的日志：打开 [`plans/weekly-log.md`](plans/weekly-log.md)，复制最新一周的记录一并粘贴。
3. 如有必要，附上 [`context/profile.md`](context/profile.md) 或 [`context/constraints.md`](context/constraints.md) 中的相关段落。

> 最简模板：**`prompts/prompt.md` + 最新一周日志** 即可覆盖 90% 的场景。

### 每周维护
1. 在 [`plans/weekly-log.md`](plans/weekly-log.md) 中填写本周完成情况。
2. 如有新的方向决策或重要结论，补充到 [`context/qa.md`](context/qa.md)。
3. 如环境或设备有变化，更新 [`context/constraints.md`](context/constraints.md)。
4. 将改动 commit 并 push 到本仓库（保持 main 分支为最新）。

---

## 文件速查

| 文件 | 用途 |
|------|------|
| `context/profile.md` | 个人身份、专业、学习基础、目标岗位 |
| `context/constraints.md` | 硬件（3060 6GB）、WSL 工作流、Hugging Face 限制 |
| `context/qa.md` | A/B/C 方向对比、关键技术术语解释、决策记录 |
| `plans/12-week-plan.md` | 完整 12 周计划（每周目标/任务/产出/验收） |
| `plans/weekly-log.md` | 每周日志模板 + 已填写的历史记录 |
| `prompts/prompt.md` | 对话启动提示词（包含所有背景的压缩版） |
| `projects/llama-cpp-wsl/README.md` | 推理环境搭建记录、命令、性能数据 |
