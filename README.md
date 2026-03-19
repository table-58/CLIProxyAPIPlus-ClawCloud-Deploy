# CLIProxyAPIPlus ClawCloud 一键部署教程

本仓库整理了 CLIProxyAPIPlus 在 **ClawCloud Run** 上的完整、最稳部署方法（基于真实部署 40+ 次迭代经验）。

支持 Gemini、Claude、Qwen、Kimi + OpenRouter、Groq、DeepSeek 等第三方模型。

---

## 🚀 一键部署步骤（ClawCloud Run）

### 1. 创建应用

| 配置项 | 值 |
|--------|-----|
| 镜像 | `eceasy/cli-proxy-api-plus:latest` |
| Command | `/CLIProxyAPI/CLIProxyAPIPlus --config /data/config.yaml` |
| Arguments | 留空 |
| Environment Variables | `DEPLOY` = `cloud` |

### 2. Configmaps 配置（关键）

在应用的 **Advanced Configuration** 中找到 **Configmaps**：

- **File Name**: `config.yaml`
- **Container Path**: `/data/config.yaml`
- **File Content**: 使用本仓库的 [config.example.yaml](config.example.yaml)

### 3. Local Storage（必须！防止 token 丢失）

| 配置项 | 值 |
|--------|-----|
| Mount Path | `/root/.cli-proxy-api` |
| 大小 | 1 Gi |

### 4. 部署后登录 OAuth

```bash
# 进入应用目录
cd /CLIProxyAPI

# 启动登录（无浏览器模式）
./CLIProxyAPIPlus -no-browser -login
```

然后本地浏览器打开显示的授权链接，授权后复制回调 URL 粘贴到 Web Shell 中。

### 5. 验证

```bash
curl https://你的公网域名/v1/models
```

如果返回模型列表，即部署成功！

---

## 📋 推荐配置 (config.example.yaml)

```yaml
api-keys:
  - "你的API密钥"

server:
  host: "0.0.0.0"
  port: 8317

debug: false

# Token 持久化路径（必须和 Volume 挂载路径一致）
auth-dir: "/root/.cli-proxy-api"

# 远程管理面板
remote-management:
  allow-remote: true
  secret-key: "你的管理密钥"
  disable-control-panel: false

# Plus 版第三方提供商（按需开启）
providers:
  openrouter:
    enabled: true
    api-key: ""
  groq:
    enabled: true
    api-key: ""
  deepseek:
    enabled: true
    api-key: ""
  siliconflow:
    enabled: false
```

---

## ⚠️ 常见问题解决

| 问题 | 解决方案 |
|------|----------|
| standing by / No config found | 检查 Configmaps 路径是否为 `/data/config.yaml` |
| OAuth timeout | Ctrl+C 中断后粘贴完整回调 URL |
| token 突然失效 | 确保挂载了 `/root/.cli-proxy-api` |
| 镜像拉取失败 | 使用 `eceasy/cli-proxy-api-plus:latest` |
| 模型列表为空 | 重新登录 + 检查 auth-dir |

---

## 📄 许可证

MIT License

---

欢迎 Star / Fork / PR！⭐
