这篇记录 Claude Code 在 Windows 环境中的基础配置方法，重点是通过环境变量指定兼容服务地址和鉴权 Token。

## 参考资料

- [Claude Code 入门文档](https://claudecn.com/docs/claude-code/getting-started/first-conversation/)
- [智谱 Coding Plan 工具说明](https://docs.bigmodel.cn/cn/coding-plan/tool/claude)
- [CTok Claude Code 社群](https://ctok.ai/)

## 使用 GLM Coding Tool Helper

如果使用 GLM 编码套餐，可以先运行官方辅助工具，让它自动完成工具安装、套餐配置和 MCP 服务管理。

```bash
npx @z_ai/coding-helper
```

按命令行提示完成登录和配置即可。

## 临时设置环境变量

临时环境变量只对当前终端窗口生效，适合测试代理服务是否可用。

PowerShell：

```powershell
$env:ANTHROPIC_BASE_URL = "https://example.com/"
$env:ANTHROPIC_AUTH_TOKEN = "<YOUR_API_KEY>"
```

CMD：

```bat
set ANTHROPIC_BASE_URL=https://example.com/
set ANTHROPIC_AUTH_TOKEN=<YOUR_API_KEY>
```

Linux 或 macOS：

```bash
export ANTHROPIC_BASE_URL="https://example.com/"
export ANTHROPIC_AUTH_TOKEN="<YOUR_API_KEY>"
```

## 永久设置环境变量

Windows 可以使用 `setx` 写入用户环境变量。修改后需要重新打开终端。

```bat
setx ANTHROPIC_BASE_URL "https://example.com/"
setx ANTHROPIC_AUTH_TOKEN "<YOUR_API_KEY>"
```

验证：

```bat
echo %ANTHROPIC_AUTH_TOKEN%
```

## 指定配置文件启动

如果维护了多套 Claude Code 配置，可以通过 `--settings` 指定配置文件。

```powershell
claude --settings C:\Users\你的用户名\.claude\settings.example.json
```

