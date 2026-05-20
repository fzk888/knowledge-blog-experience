OpenClaw 可以通过不同 Agent 承担写作、规划、检索、发布等任务。切换方式主要有四种：终端持续对话、终端单次命令、渠道绑定和调度器转发。

## 方法一：终端持续对话

适合需要多轮沟通的场景。

```bash
openclaw tui --session agent:writer:main
```

也可以直接指定 Agent：

```bash
openclaw agent --agent writer
```

说明：

- `tui` 是终端交互界面。
- `--session` 指定要连接的会话。
- 常见格式为 `agent:{agentId}:main`。

## 方法二：终端单次命令

适合一次性请求，例如让 writer 生成文章初稿。

```bash
openclaw agent --agent writer --message "写一篇关于本地知识库管理的文章" --timeout 300
```

常用参数：

| 参数 | 说明 |
| --- | --- |
| `--agent <id>` | 指定 Agent，例如 `scheduler`、`writer`、`planner` |
| `--message <text>` | 发送给 Agent 的消息 |
| `--timeout <秒>` | 等待超时时间 |
| `--deliver` | 将回复发送到绑定渠道 |

## 方法三：修改渠道绑定

如果希望某个渠道默认交给指定 Agent，可以修改绑定配置。

```bash
openclaw config patch '{"bindings":[{"agentId":"scheduler","match":{"channel":"feishu","accountId":"scheduler"}},{"type":"route","agentId":"writer","match":{"channel":"openclaw-weixin"}}]}'
openclaw gateway restart
```

恢复到默认调度器：

```bash
openclaw config patch '{"bindings":[{"agentId":"scheduler","match":{"channel":"feishu","accountId":"scheduler"}},{"type":"route","agentId":"scheduler","match":{"channel":"openclaw-weixin"}}]}'
openclaw gateway restart
```

## 方法四：通过 scheduler 分发

如果不想频繁改绑定，可以保持渠道绑定到 `scheduler`，由调度器根据需求调用 writer、planner 或 researcher。

示例：

```text
请用 writer 帮我整理一篇技术文章，主题是本地知识库管理。
```

## 常见 Agent ID

| Agent ID | 用途 |
| --- | --- |
| `scheduler` | 默认调度中心 |
| `writer` | 文章写作 |
| `planner` | 选题和结构规划 |
| `trend` | 热点抓取 |
| `polisher` | 内容润色 |
| `publisher` | 渠道分发 |
