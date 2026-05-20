这篇是 OpenClaw 的日常命令速查，适合排障和维护时快速查看。

## 服务管理

| 命令 | 作用 |
| --- | --- |
| `openclaw status` | 查看运行状态 |
| `openclaw gateway start` | 启动 Gateway |
| `openclaw gateway stop` | 停止 Gateway |
| `openclaw gateway restart` | 重启 Gateway |
| `openclaw gateway tui` | 打开 Gateway 交互界面 |
| `openclaw doctor --fix` | 诊断并尝试修复问题 |

## 配置管理

```bash
openclaw configure
openclaw config get
openclaw config get tools
openclaw config get browser
```

直接编辑配置文件：

```bash
code ~/.openclaw/openclaw.json
```

## 模型配置示例

示例仅保留字段结构，不包含真实 Key。

```json
{
  "model": {
    "primary": "provider/model-name",
    "fallbacks": [
      "provider/fallback-model"
    ]
  }
}
```

如果初始向导没有进入模型配置，可以重新运行：

```bash
openclaw configure
```

按提示选择：

- Gateway run：`Local (this machine)`
- Configure sections：`Model`
- Model provider：按实际服务商选择
- Auth method：按账号或 API Key 方式选择
- Endpoint：按地区选择

## 安全检查

```bash
openclaw security audit --deep
```

发布配置前，确认没有包含：

- 真实 API Key
- 真实 Token
- 服务商账号信息
- 内网服务器地址

## 卸载

```bash
openclaw gateway stop
openclaw gateway uninstall
openclaw uninstall
```

如果需要彻底清理配置：

```bash
rm -rf ~/.openclaw
```
