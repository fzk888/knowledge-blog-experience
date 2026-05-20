多 Agent 的核心价值是把不同类型的任务交给不同助手处理：编码交给 coder，写作交给 writer，调研交给 researcher。这样可以减少上下文互相污染，也方便为不同任务配置不同模型和工作目录。

## 适用场景

| 场景 | 是否适合多 Agent |
| --- | --- |
| 简单问答 | 不需要 |
| 写代码、写文档、调研同时进行 | 适合 |
| 长周期项目，需要拆分上下文 | 适合 |
| 需要按任务使用不同模型 | 适合 |

## 推荐结构

```text
OpenClaw Gateway
├── main
│   └── 默认助手
├── coder
│   └── 编码、调试、代码审查
├── writer
│   └── 博客、文档、文案
└── researcher
    └── 信息检索、资料整理、报告
```

## 全局配置

编辑配置文件：

```bash
code ~/.openclaw/openclaw.json
```

示例：

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "provider/main-model",
        "fallbacks": [
          "provider/fallback-model"
        ]
      },
      "workspace": "/home/username/.openclaw/workspace",
      "maxConcurrent": 4,
      "subagents": {
        "maxConcurrent": 8
      }
    }
  }
}
```

字段说明：

| 字段 | 说明 |
| --- | --- |
| `model.primary` | 默认主模型 |
| `model.fallbacks` | 备用模型列表 |
| `workspace` | 默认工作目录 |
| `maxConcurrent` | 最大并发 Agent 数 |
| `subagents.maxConcurrent` | 最大并发子 Agent 数 |

## 创建 coder

文件：

```text
~/.openclaw/agents/coder/config.json
```

示例：

```json
{
  "label": "coding-expert",
  "model": "provider/code-model",
  "workspace": "/home/username/.openclaw/agents/coder",
  "systemPrompt": "你是一个软件工程师，负责代码实现、调试、重构和代码审查。",
  "tools": ["full"],
  "maxConcurrent": 2
}
```

调用示例：

```text
@coder 写一个 Python 脚本，统计当前目录下 Markdown 文件的行数。
```

## 创建 writer

文件：

```text
~/.openclaw/agents/writer/config.json
```

示例：

```json
{
  "label": "writing-assistant",
  "model": "provider/writing-model",
  "workspace": "/home/username/.openclaw/agents/writer",
  "systemPrompt": "你是一个写作助手，负责技术文档、博客文章和文案润色。",
  "tools": ["full"],
  "maxConcurrent": 2
}
```

调用示例：

```text
@writer 把这段排障记录整理成适合发布的技术博客。
```

## 创建 researcher

文件：

```text
~/.openclaw/agents/researcher/config.json
```

示例：

```json
{
  "label": "research-assistant",
  "model": "provider/research-model",
  "workspace": "/home/username/.openclaw/agents/researcher",
  "systemPrompt": "你是一个研究助理，负责资料检索、信息整理和报告撰写。",
  "tools": ["web_search", "web_fetch", "read", "write"],
  "maxConcurrent": 3
}
```

调用示例：

```text
@researcher 调研几个主流 AI Agent 框架的定位、优缺点和适用场景。
```

## 管理 Agent

查看会话：

```bash
openclaw sessions list
openclaw sessions list --activeMinutes 60
```

查看状态：

```bash
openclaw sessions status --sessionKey agent:main:main
```

重启 Gateway：

```bash
openclaw gateway restart
```

## 常见问题

| 现象 | 可能原因 | 处理方式 |
| --- | --- | --- |
| Agent 无法启动 | 模型配置错误 | 检查 provider 和 model 名称 |
| 响应很慢 | 并发过高或模型慢 | 降低 `maxConcurrent` |
| 工作成果找不到 | 工作目录分散 | 检查各 Agent 的 `workspace` |
| 上下文混乱 | 多任务塞进同一 Agent | 拆分到专属 Agent |
| 成本过高 | 所有任务都用大模型 | 为简单任务配置便宜模型 |

## 配置建议

- 每个 Agent 使用独立工作目录。
- system prompt 只写职责边界，不要塞太多无关规则。
- 先从 2 到 3 个 Agent 开始，不要一上来拆太细。
- 公开配置时，统一使用占位符，不提交真实模型 Key。

## 相关文档

- [05_OpenClaw 多 Agent 快速使用](./05_OpenClaw%20多%20Agent%20快速使用.md)
- [07_OpenClaw 切换 Agent 对话方法](./07_OpenClaw%20切换%20Agent%20对话方法.md)
