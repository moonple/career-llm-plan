# 设备环境与约束

## 硬件环境

| 项目 | 配置 |
|------|------|
| GPU | NVIDIA RTX 3060（6 GB 显存） |
| 系统 | Windows 11 + WSL2（Ubuntu） |
| CUDA | WSL 内 `nvidia-smi` 可用，CUDA 推理已验证 |
| 网络 | WSL 无法直连 Hugging Face |

---

## 已验证推理环境

- **框架**：llama.cpp（CUDA 编译版）
- **模型**：Qwen2.5-3B Instruct，Q4_K_M GGUF 格式
- **推理方式**：命令行或 llama.cpp 自带 server
- **显存占用**：约 3–4 GB（Q4_K_M，ctx=2048），具体以实测为准

---

## 工作流约束

### Hugging Face 访问问题
WSL2 内无法直连 Hugging Face，采用以下替代工作流：

```
Windows 浏览器 / 工具（如 HF-Mirror、aria2）
    ↓ 下载 GGUF 模型文件到 Windows 本地路径
    ↓ 拷贝到 WSL（例如 /home/<user>/models/）
WSL 内 llama.cpp 加载推理
```

> 常用镜像站：`https://hf-mirror.com`（可在 Windows 环境直接使用）

### 显存约束
- 6 GB 显存限制了可运行模型规模：
  - Q4_K_M GGUF：3B 参数模型约占 3–4 GB → 可用
  - 7B Q4_K_M：约 5–6 GB → 勉强可用（需调低 ctx）
  - 13B+：显存不足，无法直接运行（除非极低量化 + 部分 offload 到 CPU）
- **训练大模型不现实**，专注推理/服务化/工程化。

### 上下文长度约束
- ctx 过大（如 8192+）会显著增加显存占用，3060 6GB 建议：
  - 日常测试：ctx = 2048
  - 最大可用：ctx = 4096（视量化等级而定，需实测）

---

## 开发约束

| 约束 | 说明 |
|------|------|
| 主语言 | Python（优先），C++/Java 暂时搁置 |
| 包管理 | pip / conda（WSL 内） |
| 容器 | Docker 可用（WSL2 支持），GPU 直通需注意 `--gpus all` |
| 版本控制 | Git + GitHub（公开仓库） |
| 编辑器 | VS Code（Remote-WSL 插件） |

---

## 注意事项

1. **WSL2 文件系统性能**：模型文件放在 WSL 本地（如 `/home/<user>/models/`），不要放在 `/mnt/c/`（Windows 路径），否则 I/O 性能极差。
2. **GPU 驱动**：每次更新 Windows 驱动后，确认 WSL 内 `nvidia-smi` 仍可用。
3. **内存**：确保 WSL2 分配足够内存（建议 `.wslconfig` 中 `memory=16GB` 或更高）。
