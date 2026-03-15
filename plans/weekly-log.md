# 每周进度日志

每周完成后填写，并 commit 到本仓库。新对话时附上最新一周的记录，帮助快速恢复上下文。

---

## 使用说明

1. 在新一周开始时，将"周模板"复制到本文件末尾，填写"本周目标"。
2. 每周结束时填写"完成情况"、"遇到的问题"、"下周计划"。
3. Commit 并 push 到 GitHub。

---

## 周模板（可直接复制）

```markdown
## Week N（YYYY-MM-DD ~ MM-DD）：[本周主题]

### 本周目标
- 

### 完成情况
- ✅ 
- ⚠️ （部分完成，说明原因）
- ❌ （未完成，说明原因）

### 遇到的问题
- **问题**：
  **原因**：
  **解决方案**：

### 关键数据 / 产出
| 指标 | 数值 |
|------|------|
|  |  |

### 下周计划
- 

### 备注
- 
```

---

## Week 1（2026-03-16 ~ 03-22）：环境固化与文档化

### 本周目标
- 建立 `llm-infer-deploy-lab` GitHub 公开仓库
- 编写可复现的 llama.cpp WSL 推理文档
- 完成 baseline benchmark（≥ 3 组参数对比）
- 创建 `scripts/bench.sh` 自动化脚本

### 完成情况
- ⬜ 待填写

### 遇到的问题
- ⬜ 待填写

### 关键数据 / 产出
| 指标 | 数值 |
|------|------|
| tokens/s（ctx=2048, Q4_K_M） | - |
| 峰值显存（ctx=2048） | - |
| tokens/s（ctx=4096, Q4_K_M） | - |
| 峰值显存（ctx=4096） | - |

### 下周计划
- 用 llama.cpp server 跑通 HTTP API（`/v1/chat/completions`）
- 验证流式输出（SSE）

### 备注
- 参考：[`projects/llama-cpp-wsl/README.md`](../projects/llama-cpp-wsl/README.md)
