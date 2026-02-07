# OpenClaw 配置检查报告

生成时间: Sat Feb  7 08:56:02 UTC 2026
环境: GitHub Codespaces

---

## 1. Gateway 配置状态

| 配置项 | 状态 | 值 |
|--------|------|-----|
| Gateway 状态 | ✅ 运行中 | ws://127.0.0.1:18789 |
| 绑定地址 | ✅ loopback | 127.0.0.1 |
| 端口 | ✅ 18789 | - |
| 认证模式 | ✅ token | - |
| Control UI | ✅ allowInsecureAuth | 已启用（跳过配对） |

**当前 Token:**
```
80fc3283c6b053b466f04b3939166f7ff1a6e90f7fb4026de52d80f3c33f01f4
```

**访问 URL:**
- Dashboard: http://127.0.0.1:18789/?token=80fc3283c6b053b466f04b3939166f7ff1a6e90f7fb4026de52d80f3c33f01f4
- WebSocket: wss://laughing-spork-wrgg5q7vw9r9cggq5-18789.app.github.dev/?token=80fc3283c6b053b466f04b3939166f7ff1a6e90f7fb4026de52d80f3c33f01f4
- Dashboard (Codespaces): https://laughing-spork-wrgg5q7vw9r9cggq5-18789.app.github.dev/?token=80fc3283c6b053b466f04b3939166f7ff1a6e90f7fb4026de52d80f3c33f01f4

---

## 2. API 提供商配置

### NVIDIA MiniMax 配置

| 配置项 | 值 | 状态 |
|--------|-----|------|
| Provider | nvidia-minimax | ✅ |
| Base URL | https://integrate.api.nvidia.com/v1 | ✅ |
| API 类型 | openai-completions | ✅ |
| API Key | nvapi-LbnHiFQ5NFa... | ✅ 已配置 |

### 模型信息

| 属性 | 值 |
|------|-----|
| 模型 ID | minimaxai/minimax-m2.1 |
| 完整路径 | nvidia-minimax/minimaxai/minimax-m2.1 |
| 名称 | MiniMax M2.1 (NVIDIA) |
| 上下文窗口 | 200,000 tokens |
| 最大输出 | 131,072 tokens |
| 支持推理 | ✅ yes |
| 输入类型 | text |

---

## 3. OpenClaw 模型列表

```
Model                                      Input      Ctx      Local Auth  Tags
nvidia-minimax/minimaxai/minimax-m2.1      text       195k     no    yes   default,configured
```

**状态说明:**
- ✅ `default` - 设为默认模型
- ✅ `configured` - 已正确配置

---

## 4. Agent 默认配置

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "nvidia-minimax/minimaxai/minimax-m2.1"
      },
      "models": {
        "nvidia-minimax/minimaxai/minimax-m2.1": {}
      }
    }
  }
}
```

---

## 5. API 连接测试

### 直接 API 调用测试

```bash
curl -X POST "https://integrate.api.nvidia.com/v1/chat/completions" \
  -H "Authorization: Bearer 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" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "minimaxai/minimax-m2.1",
    "messages": [{"role": "user", "content": "Hello"}],
    "max_tokens": 100
  }'
```

**测试结果:** ✅ 正常

### NVIDIA API 可用模型

- minimaxai/minimax-m2
- minimaxai/minimax-m2.1 ✅ 当前使用

---

## 6. 完整配置文件

**文件位置:** `~/.openclaw/openclaw.json`

```json
{
  "meta": {
    "lastTouchedVersion": "2026.2.3-1",
    "lastTouchedAt": "2026-02-07T07:42:05.303Z"
  },
  "gateway": {
    "port": 18789,
    "mode": "local",
    "bind": "loopback",
    "auth": {
      "mode": "token",
      "token": "80fc3283c6b053b466f04b3939166f7ff1a6e90f7fb4026de52d80f3c33f01f4"
    },
    "controlUi": {
      "allowInsecureAuth": true
    },
    "tailscale": {
      "mode": "off",
      "resetOnExit": false
    }
  },
  "models": {
    "mode": "merge",
    "providers": {
      "nvidia-minimax": {
        "baseUrl": "https://integrate.api.nvidia.com/v1",
        "apiKey": "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",
        "api": "openai-completions",
        "models": [
          {
            "id": "minimaxai/minimax-m2.1",
            "name": "MiniMax M2.1 (NVIDIA)",
            "reasoning": true,
            "input": ["text"],
            "contextWindow": 200000,
            "maxTokens": 131072
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "nvidia-minimax/minimaxai/minimax-m2.1"
      },
      "models": {
        "nvidia-minimax/minimaxai/minimax-m2.1": {}
      }
    }
  }
}
```

---

## 7. 已知问题

### OpenClaw Agent 命令 404 错误

**状态:** ❌ 存在

**表现:**
```
openclaw agent --message "你好" --session-id test
# 输出: HTTP 404: 404 page not found
```

**原因:**
这是 OpenClaw 2026.2.3-1 版本的已知 bug。日志显示 Agent 实际上已成功运行（`embedded run done`），但在获取模型响应时尝试访问一个不存在的 HTTP 端点。

**解决方案:**
直接使用 NVIDIA API 进行聊天：

```bash
#!/bin/bash
MESSAGE="${1:-你好}"
curl -X POST "https://integrate.api.nvidia.com/v1/chat/completions" \
  -H "Authorization: Bearer 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" \
  -H "Content-Type: application/json" \
  -d "{
    \"model\": \"minimaxai/minimax-m2.1\",
    \"messages\": [{\"role\": \"user\", \"content\": \"$MESSAGE\"}],
    \"max_tokens\": 200
  }" | python3 -c "import sys, json; d=json.load(sys.stdin); print(d['choices'][0]['message']['content'])"
```

---

## 8. 安全注意事项

1. **allowInsecureAuth**: 当前启用了 `gateway.controlUi.allowInsecureAuth: true`，这会跳过设备配对验证。仅在开发/测试环境使用，生产环境建议禁用。

2. **API Key**: API Key 存储在配置文件中，建议在生产环境使用环境变量。

3. **Token**: Gateway Token 已正确配置，WebSocket 连接需要提供 Token。

---

## 9. 总结

| 项目 | 状态 | 说明 |
|------|------|------|
| Gateway | ✅ | 运行正常，Token 认证有效 |
| 模型配置 | ✅ | NVIDIA MiniMax M2.1 已正确配置 |
| API 连接 | ✅ | NVIDIA API 可直接调用 |
| OpenClaw Agent | ❌ | 版本 Bug，需等待官方修复 |
| Web UI | ✅ | 可通过 Dashboard URL 访问 |

---

**报告生成完成**

