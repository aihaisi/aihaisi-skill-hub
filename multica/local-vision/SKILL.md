---
name: local-vision
description: 调用本地 MiniCPM-V 多模态模型进行图像识别，无需联网。也可通过 Multica 调用 hermes agent 使用同一能力。
homepage: http://127.0.0.1:8080/v1
---

# Local Vision — 本地图像识别

## 概述

本地运行了一个 MiniCPM-V 多模态模型（MiniCPM-V-4.6-Q4_K_M.gguf），
提供 OpenAI 兼容的 API 接口，支持图像理解任务。

- **API 地址**: http://127.0.0.1:8080/v1
- **模型**: MiniCPM-V-4.6-Q4_K_M.gguf（支持 multimodal，支持中文）
- **调用方式**: OpenAI Chat Completions API（image_url + base64）

## 使用方法

### 1. 直接调用本地 API（适用于本 agent）

发送图片 base64 到 chat completions 接口：

```bash
# 将图片转为 base64（PowerShell）
$b64 = [Convert]::ToBase64String([IO.File]::ReadAllBytes(图片路径.png))

# 发送识别请求
$body = @{
  model = MiniCPM-V-4.6-Q4_K_M.gguf
  messages = @(
    @{
      role = user
      content = @(
        @{ type = text; text = 图片里有什么？ }
        @{ type = image_url; image_url = @{ url = data:image/png;base64,$b64 } }
      )
    }
  )
  max_tokens = 512
} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Uri http://127.0.0.1:8080/v1/chat/completions `
  -Method Post `
  -ContentType application/json `
  -Body $body
```

### 2. 通过 Multica 调用她的 agent

也可以用 `mention://agent/<hermes-agent-id>` 来触发 hermes agent，
该 agent 同样调用同一个本地模型完成图像识别。

### 注意事项

- 模型运行在本地机器，需确保服务已启动（http://127.0.0.1:8080）
- 图片以 base64 形式传输，大图片建议先压缩再发送
- 支持中英文提示词
- 如果本地服务不可用，可考虑使用其他云端多模态模型（如 GPT-4o 等）
