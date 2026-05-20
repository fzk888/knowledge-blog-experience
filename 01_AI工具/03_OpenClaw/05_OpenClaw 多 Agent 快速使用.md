这篇记录已经配置好多个 Agent 后，如何快速调用和管理它们。

## Agent 分工

| Agent | 适合任务 | 触发方式 |
| --- | --- | --- |
| `coder` | 编程、调试、代码审查 | `@coder` |
| `writer` | 写作、润色、文案 | `@writer` |
| `researcher` | 调研、分析、报告 | `@researcher` |

## 方式一：直接在对话中指定 Agent

```text
@coder 写一个 Python 脚本，批量重命名文件。
@writer 把这段排障记录整理成技术博客。
@researcher 调研三个主流知识库工具的优缺点。
```

这是最简单的用法，适合大多数场景。

## 方式二：使用 CLI 单次调用

```bash
openclaw agent --agent writer --message "整理一篇关于 Obsidian 知识库管理的文章" --timeout 300
```

查看会话：

```bash
openclaw sessions list
openclaw session_status --sessionKey agent:main:main
```

## 方式三：创建持久任务

需要长期保留上下文时，可以创建持久会话。

```typescript
await sessions_spawn({
  task: "负责我的技术博客整理任务",
  label: "writer-assistant",
  runtime: "subagent",
  model: "provider/writing-model",
  mode: "session",
  thread: true
});
```

## 任务拆分示例

复杂任务建议按角色拆分：

```text
第一步：@researcher 收集资料并整理参考链接。
第二步：@coder 根据资料实现脚本或配置。
第三步：@writer 把过程整理成发布版文档。
```

这样做的好处是每个 Agent 的上下文更干净，输出也更稳定。

## 管理命令

```bash
openclaw sessions list --limit 10
openclaw sessions list --activeMinutes 60
openclaw gateway restart
```

如果使用子 Agent API，可以列出或终止子任务：

```typescript
await subagents({ action: "list" });

await subagents({
  action: "kill",
  target: "writer-assistant"
});
```

## 使用建议

- 简单问题直接问默认 Agent。
- 明确任务类型时再指定 `@coder`、`@writer` 或 `@researcher`。
- 长文档、长项目使用持久会话。
- 并行任务不要开太多，先从 2 到 3 个 Agent 开始。
- 输出要落地时，明确要求保存路径和文件格式。

## 相关文档

- [04_OpenClaw 多 Agent 配置指南](./04_OpenClaw%20多%20Agent%20配置指南.md)
- [07_OpenClaw 切换 Agent 对话方法](./07_OpenClaw%20切换%20Agent%20对话方法.md)
