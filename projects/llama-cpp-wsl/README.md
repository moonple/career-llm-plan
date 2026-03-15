# llama.cpp WSL 推理记录

本文件记录在 WSL2 + Ubuntu + RTX 3060 6GB 上使用 llama.cpp 进行 CUDA 推理的完整流程、命令和性能数据。

---

## 环境信息

| 项目 | 配置 |
|------|------|
| 操作系统 | Windows 11 + WSL2 Ubuntu |
| GPU | NVIDIA RTX 3060（6 GB 显存） |
| CUDA 版本 | （待填写，执行 `nvidia-smi` 查看） |
| llama.cpp 版本 | （待填写，执行 `git rev-parse --short HEAD`） |
| 编译方式 | CUDA（`cmake -DGGML_CUDA=ON`） |

---

## 模型信息

| 项目 | 内容 |
|------|------|
| 模型名称 | Qwen2.5-3B Instruct |
| 量化格式 | Q4_K_M GGUF |
| 模型文件路径 | （待填写，如 `/home/<user>/models/qwen2.5-3b-instruct-q4_k_m.gguf`） |
| 文件大小 | （待填写，如 `1.9 GB`） |

---

## 工作流：Windows 下载模型 → WSL

WSL 内无法直连 Hugging Face，采用以下工作流：

```bash
# 1. 在 Windows 浏览器中访问镜像站下载模型
# 镜像站：https://hf-mirror.com

# 2. 将 .gguf 文件从 Windows 路径拷贝到 WSL（示例）
cp /mnt/c/Users/<WindowsUser>/Downloads/qwen2.5-3b-instruct-q4_k_m.gguf \
   ~/models/

# 3. 建议将模型统一放在 WSL 本地（不要放在 /mnt/c/ 下，I/O 性能极差）
mkdir -p ~/models
```

---

## llama.cpp 编译

```bash
# 克隆仓库
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp

# 创建构建目录并编译（CUDA 版）
cmake -B build -DGGML_CUDA=ON
cmake --build build --config Release -j$(nproc)

# 验证编译产物
ls build/bin/
# 期望看到：llama-server, llama-cli 等
```

> **注意**：如果 CMake 找不到 CUDA，检查 `PATH` 是否包含 `/usr/local/cuda/bin`，并确认 `nvidia-smi` 可用。

---

## 命令行推理（验证用）

```bash
./build/bin/llama-cli \
  -m ~/models/qwen2.5-3b-instruct-q4_k_m.gguf \
  -n 256 \
  --n-gpu-layers 35 \
  -c 2048 \
  -p "你好，请介绍一下你自己。"
```

参数说明：
- `-n 256`：最大生成 token 数
- `--n-gpu-layers 35`：将前 35 层卸载到 GPU（3B 模型约 36 层，全卸载到 GPU）
- `-c 2048`：上下文长度
- `-p`：prompt

---

## llama.cpp Server 启动

```bash
./build/bin/llama-server \
  -m ~/models/qwen2.5-3b-instruct-q4_k_m.gguf \
  --n-gpu-layers 35 \
  -c 2048 \
  --host 0.0.0.0 \
  --port 8080 \
  --parallel 2
```

参数说明：
- `--host 0.0.0.0`：允许从 Windows 主机访问
- `--port 8080`：监听端口
- `--parallel 2`：最大并发请求数（受显存限制，3060 6GB 建议 1–2）

### 验证 API

```bash
# 非流式
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen2.5-3b",
    "messages": [{"role": "user", "content": "你好"}],
    "max_tokens": 128
  }'

# 流式输出
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen2.5-3b",
    "messages": [{"role": "user", "content": "你好"}],
    "max_tokens": 128,
    "stream": true
  }'
```

---

## 性能数据（待填写）

> 每次 benchmark 后更新此表。

| 测试编号 | 模型 | 量化 | ctx | gpu_layers | batch | tokens/s | TTFT (ms) | 峰值显存 (MB) | 日期 |
|----------|------|------|-----|-----------|-------|----------|-----------|-------------|------|
| baseline-01 | Qwen2.5-3B | Q4_K_M | 2048 | 35 | 512 | - | - | - | - |
| baseline-02 | Qwen2.5-3B | Q4_K_M | 4096 | 35 | 512 | - | - | - | - |
| baseline-03 | Qwen2.5-3B | Q4_K_M | 2048 | 20 | 512 | - | - | - | - |

---

## 常见问题

### nvidia-smi 不可用
```bash
# 确认 WSL2 的 CUDA 支持已启用
nvidia-smi
# 如果失败，检查 Windows 端 NVIDIA 驱动版本（需 ≥ 470.76）
```

### OOM（显存不足）
- 减少 `--n-gpu-layers`（部分层留在 CPU）
- 降低 `-c`（上下文长度）
- 使用更低量化（如 Q3_K_M）

### 生成速度慢
- 确认 `--n-gpu-layers` 设置正确（太小则大量计算在 CPU 上）
- 检查模型文件是否在 WSL 本地（不在 `/mnt/c/` 路径下）

---

## 后续计划

- [ ] 完成 baseline benchmark 并更新性能数据表
- [ ] 接入 FastAPI 网关（参考 `plans/12-week-plan.md` Week 3）
- [ ] 记录 OOM 排查过程（如遇到）
