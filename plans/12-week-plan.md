# 12 周学习计划

**起始日期**：2026-03-16  
**结束日期**：2026-06-08  
**主线方向**：LLM 推理部署 / 服务化工程  
**设备约束**：WSL2 + Ubuntu + RTX 3060 6GB，Hugging Face 访问受限  

---

## 整体路线图

```
Week 1    环境固化 & 文档化
Week 2    llama.cpp server → HTTP API
Week 3    FastAPI 网关 + 并发稳定性
Week 4    可观测性（日志 + 指标）
Week 5-6  RAG 最小闭环
Week 7    评测与回归
Week 8    Docker 部署 & 配置化
Week 9-10 工程加分项
Week 11-12 简历打磨 & 投递准备
```

---

## Week 1（2026-03-16 ~ 03-22）：环境固化与文档化

### 目标
把"本地跑通"变成"别人照文档也能复现"，建立可持续维护的项目仓库。

### 任务
- [ ] 在 GitHub 创建公开仓库（建议名：`llm-infer-deploy-lab`）
- [ ] 编写 `README.md`（环境说明、WSL 工作流、模型放置方式）
- [ ] 记录 llama.cpp 编译参数与推理命令
- [ ] 跑 baseline benchmark（至少 2–3 组参数对比）
- [ ] 创建 `scripts/bench.sh`（自动化多组参数测试）

### 产出
- 公开 GitHub 仓库，README 可复现
- `results/bench_baseline.md`：至少 3 组参数对比（ctx / gpu-layers / batch）

### 验收标准
- 其他人照 README 步骤能在类似环境下复现推理
- benchmark 表格含：tokens/s、峰值显存（nvidia-smi 读取）、上下文长度

---

## Week 2（2026-03-23 ~ 03-29）：llama.cpp server → HTTP API

### 目标
从"命令行推理"升级为"可调用的 HTTP 服务（OpenAI 兼容格式）"。

### 任务
- [ ] 用 `llama-server` 启动服务（`--port 8080 --n-gpu-layers N`）
- [ ] 验证 `/v1/chat/completions` 接口（curl 测试）
- [ ] 验证流式输出（`"stream": true`，SSE 格式）
- [ ] 编写 `scripts/run_server.sh`（参数化启动脚本）
- [ ] 更新 `projects/llama-cpp-wsl/README.md`

### 产出
- 可运行的 llama.cpp server + curl 调用示例文档
- `scripts/run_server.sh`

### 验收标准
- curl 调用返回正确 JSON 响应
- 流式输出能逐 token 打印

---

## Week 3（2026-03-30 ~ 04-05）：FastAPI 网关 + 并发稳定性

### 目标
在 llama.cpp server 上加一层 Python 网关，实现请求队列、超时、基础限流，并验证并发稳定性。

### 任务
- [ ] 用 FastAPI 创建 `/v1/chat/completions` 代理接口
- [ ] 实现 asyncio 请求队列（防止 GPU 被并发请求撑满）
- [ ] 实现超时与取消（客户端断开时停止生成）
- [ ] 实现基础限流（最大并发数 N）
- [ ] 编写并发压测脚本（Python asyncio 或 `hey`/`wrk`）
- [ ] 记录并发 1 / 5 / 10 下的 TTFT 与吞吐变化

### 产出
- `gateway/` 目录（FastAPI 网关代码）
- `results/bench_concurrency.md`：并发压测结果表

### 验收标准
- 并发 5 下服务不崩溃（无 OOM，无超时堆积）
- TTFT / 吞吐数据可量化记录

---

## Week 4（2026-04-06 ~ 04-12）：可观测性（日志 + 指标）

### 目标
让服务能回答"为什么慢 / 为什么卡 / 为什么 OOM"，建立基础监控体系。

### 任务
- [ ] 结构化日志：request_id、prompt_tokens、gen_tokens、耗时、状态码
- [ ] 指标采集：QPS、TTFT、TPOT、GPU 显存（通过 nvidia-smi 或 pynvml）
- [ ] （可选）接入 Prometheus + Grafana（或用简单 `/metrics` 端点）
- [ ] 编写"性能监控说明文档"
- [ ] 用真实请求验证日志与指标输出

### 产出
- 可运行的日志 + 指标系统
- `docs/observability.md`：监控方案说明
- 真实压测结果截图或表格

### 验收标准
- 每个请求在日志中有完整记录
- 可通过日志定位 TTFT 异常的具体请求

---

## Week 5–6（2026-04-13 ~ 04-26）：RAG 最小闭环

### 目标
让服务能用私有知识库回答问题，完成完整的"摄入→检索→生成"工程闭环。

### 任务
- [ ] 文档加载与切分（PyMuPDF / langchain TextSplitter）
- [ ] Embedding 生成（本地模型或 API）
- [ ] 向量库构建（FAISS 本地部署）
- [ ] 实现 `/ingest` 端点（导入文档）
- [ ] 实现 `/chat` 端点（带知识库检索的对话）
- [ ] 准备 20 条测试问题，记录命中率与失败案例

### 产出
- `rag/` 目录（完整 RAG 模块）
- `eval/rag_test_20.md`：20 条测试问答与结果

### 验收标准
- 20 条测试中命中率 ≥ 60%（以所选知识库为准）
- 失败案例有分析（切分问题 / 检索问题 / 生成问题）

---

## Week 7（2026-04-27 ~ 05-03）：评测与回归

### 目标
建立可重复运行的评测脚本，让项目迭代有数据支撑而非"感觉变好了"。

### 任务
- [ ] 建立评测数据集（≥ 50 条 Q/A 或判断题）
- [ ] 编写自动评测脚本（命中率 / 答案相关性 / 引用片段正确性）
- [ ] 记录基线评测结果
- [ ] 与上周 RAG 效果对比，分析改进点

### 产出
- `eval/` 目录（数据集 + 评测脚本）
- `eval/report.md`：基线评测报告

### 验收标准
- 评测脚本可一键运行并输出指标
- 报告中有改进方向分析

---

## Week 8（2026-05-04 ~ 05-10）：Docker 部署 & 配置化

### 目标
把项目打包成可一键启动的标准化服务，体现交付能力。

### 任务
- [ ] 编写 `Dockerfile`（含 Python 依赖，注意 GPU 直通）
- [ ] 编写 `docker-compose.yml`（服务编排）
- [ ] 使用 `.env` 管理配置（模型路径、ctx、并发、端口等）
- [ ] 编写一键启动文档（`make run` 或 `docker compose up`）
- [ ] 验证 Docker 内 GPU 可用（`--gpus all`）

### 产出
- `Dockerfile` + `docker-compose.yml` + `.env.example`
- `docs/deploy.md`：部署说明

### 验收标准
- `docker compose up` 后服务可用（或有明确的 WSL GPU 限制说明）
- `.env.example` 覆盖所有关键配置项

---

## Week 9–10（2026-05-11 ~ 05-24）：工程加分项

### 目标
提升项目的工程化程度，增加简历含金量。

从以下选项中选择 **2–3 个**完成：

- [ ] **OpenAI 兼容 API 完整实现**（`/v1/models`、`/v1/completions`、`/v1/embeddings`）
- [ ] **多模型切换**（配置不同 GGUF / 量化等级，热切换或按名称路由）
- [ ] **会话管理**（history 截断策略、token 预算控制）
- [ ] **API Key 鉴权**（简单的 Bearer token 验证）
- [ ] **Prompt/Embedding 缓存**（相同请求秒回，减少 GPU 压力）
- [ ] **前端 Chat UI**（可选，用 Gradio 或 Streamlit 快速实现）

### 产出
- 所选加分项的代码实现 + 说明文档

### 验收标准
- 每项加分项有独立说明文档和测试示例

---

## Week 11–12（2026-05-25 ~ 06-08）：简历打磨 & 投递准备

### 目标
把 12 周的成果"翻译成招聘语言"，开始实际投递。

### 任务
- [ ] 整理项目 README（面试官 10 秒能看懂你能做什么）
- [ ] 简历 1 页（项目用"指标 + 动词"写）
- [ ] 准备 2–3 个故障排查故事（OOM / 并发卡顿 / TTFT 变高）
- [ ] 整理 benchmark 对比图表（用于面试展示）
- [ ] 投递目标：一线 / 二线城市 AI 应用 / 平台 / 工具链岗位（不只投大厂）
- [ ] 整理面试常见题目（推理优化、服务化架构、KV Cache 等）

### 产出
- 简历（PDF）
- 面试准备文档（`docs/interview-prep.md`）

### 验收标准
- 至少投递 10 家目标公司
- 简历中项目条目有具体指标（tokens/s、TTFT、并发数等）

---

## 计划说明

- 每周工作量估算：约 15–20 小时（含兜底后端每周 6–8 小时）
- 如某周进度延误，优先保证"有产出"而非"完整功能"（宁可少做一个模块，也要把已做的文档写清楚）
- 每周日更新 `plans/weekly-log.md` 记录进度
