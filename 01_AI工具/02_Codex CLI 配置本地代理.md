这篇记录 Codex CLI 通过本地 OpenAI 兼容代理访问模型服务的配置方式。

## 配置文件位置

Windows 默认配置文件：

```text
C:\Users\你的用户名\.codex\config.toml
```

## 示例配置

```toml
model = "gpt-5.4"
model_provider = "proxy_codex"
model_reasoning_effort = "medium"

[model_providers.proxy_codex]
name = "Codex Proxy"
base_url = "http://127.0.0.1:8080/v1"
wire_api = "responses"
api_key = "<LOCAL_PROXY_API_KEY>"

[projects.'C:\Users\你的用户名']
trust_level = "trusted"

[windows]
sandbox = "elevated"
```

## 关键字段

- `model_provider`：全局模型提供方名称，必须和 `[model_providers.proxy_codex]` 对应。
- `base_url`：本地代理服务地址，一般以 `/v1` 结尾。
- `wire_api`：接口格式，使用 Responses API 时填写 `responses`。
- `api_key`：本地代理鉴权 Key，不要提交真实值。
- `trust_level`：指定项目目录的信任级别。

## 排查顺序

1. 确认本地代理服务已启动。
2. 用浏览器或 `curl` 检查 `http://127.0.0.1:8080/v1` 是否可访问。
3. 确认 `model_provider` 名称和 provider 配置块一致。
4. 确认 `api_key` 与代理服务配置一致。
