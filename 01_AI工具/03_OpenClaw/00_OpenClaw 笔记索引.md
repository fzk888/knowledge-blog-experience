这组笔记记录 OpenClaw 的安装、浏览器自动化、多 Agent 配置和高级功能。适合按需查阅，也可以作为个人部署 OpenClaw 的操作手册。

## 阅读顺序

1. [01_OpenClaw 安装配置完全指南](./01_OpenClaw%20安装配置完全指南.md)
2. [02_OpenClaw 常用命令](./02_OpenClaw%20常用命令.md)
3. [03_PinchTab 完整指南](./03_PinchTab%20完整指南.md)
4. [04_OpenClaw 多 Agent 配置指南](./04_OpenClaw%20多%20Agent%20配置指南.md)
5. [05_OpenClaw 多 Agent 快速使用](./05_OpenClaw%20多%20Agent%20快速使用.md)
6. [06_OpenClaw 高级配置](./06_OpenClaw%20高级配置.md)
7. [07_OpenClaw 切换 Agent 对话方法](./07_OpenClaw%20切换%20Agent%20对话方法.md)

## 内容说明

| 文档 | 适用场景 |
| --- | --- |
| [01_OpenClaw 安装配置完全指南](./01_OpenClaw%20安装配置完全指南.md) | 第一次安装 OpenClaw、配置模型和飞书渠道 |
| [02_OpenClaw 常用命令](./02_OpenClaw%20常用命令.md) | 日常启动、停止、诊断、卸载和配置检查 |
| [03_PinchTab 完整指南](./03_PinchTab%20完整指南.md) | 使用 PinchTab 控制浏览器 |
| [04_OpenClaw 多 Agent 配置指南](./04_OpenClaw%20多%20Agent%20配置指南.md) | 配置 coder、writer、researcher 等专属 Agent |
| [05_OpenClaw 多 Agent 快速使用](./05_OpenClaw%20多%20Agent%20快速使用.md) | 快速调用已经配置好的 Agent |
| [06_OpenClaw 高级配置](./06_OpenClaw%20高级配置.md) | 文件系统工具、Chrome 远程调试和安全配置 |
| [07_OpenClaw 切换 Agent 对话方法](./07_OpenClaw%20切换%20Agent%20对话方法.md) | 在终端、渠道和 scheduler 之间切换 Agent |

## 使用建议

- 先完成基础安装，再配置渠道和 Agent。
- 发布配置示例时，不要保留真实 API Key、Token、App Secret。
- 多 Agent 配置适合复杂工作流；简单问答直接使用默认 Agent 即可。
- 涉及浏览器远程调试时，只绑定 `127.0.0.1`，避免把调试端口暴露到公网。

## 相关资源

- OpenClaw 官网：<https://openclaw.ai>
- OpenClaw 文档：<https://docs.openclaw.ai>
