这篇整理 OpenClaw 的两个高级能力：文件系统工具和 Chrome 远程调试。前者让 Agent 能读写本地文件，后者让 Agent 能控制浏览器。

## 一、文件系统工具

OpenClaw 默认工具集可能只适合消息处理，不一定允许读写本地文件。如果 Agent 提示“没有文件系统权限”，通常需要切换工具 profile。

查看当前配置：

```bash
openclaw config get tools
```

切换到完整工具集：

```bash
openclaw config set tools.profile full
openclaw gateway restart
```

验证：

```bash
openclaw config get tools
```

推荐同时限制访问范围：

```bash
openclaw config set tools.fs.workspaceOnly true
```

## 二、Chrome 远程调试

Chrome 远程调试可以让 OpenClaw 控制一个已经启动的浏览器实例。适合网页检查、截图、表单测试和资料整理。

### 启动 Chrome

macOS：

```bash
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
  --remote-debugging-port=9222 \
  --user-data-dir="/tmp/chrome-openclaw"
```

Linux：

```bash
google-chrome \
  --remote-debugging-port=9222 \
  --user-data-dir="/tmp/chrome-openclaw"
```

Windows：

```cmd
"C:\Program Files\Google\Chrome\Application\chrome.exe" ^
  --remote-debugging-port=9222 ^
  --user-data-dir="C:\temp\chrome-openclaw"
```

验证端口：

```text
http://localhost:9222/json/version
```

能看到 JSON 输出就说明远程调试端口可用。

### 配置 OpenClaw

```bash
openclaw config set browser.defaultProfile remote-debug
openclaw config set browser.profiles.remote-debug.cdpUrl "http://127.0.0.1:9222"
openclaw config set browser.profiles.remote-debug.color "#0066CC"
openclaw gateway restart
```

配置文件结构示例：

```json
{
  "browser": {
    "defaultProfile": "remote-debug",
    "profiles": {
      "remote-debug": {
        "cdpUrl": "http://127.0.0.1:9222",
        "color": "#0066CC"
      }
    }
  }
}
```

## 三、多浏览器配置

可以给不同用途分配不同端口：

```json
{
  "browser": {
    "defaultProfile": "remote-debug",
    "profiles": {
      "personal": {
        "cdpUrl": "http://127.0.0.1:9222"
      },
      "work": {
        "cdpUrl": "http://127.0.0.1:9223"
      },
      "testing": {
        "cdpUrl": "http://127.0.0.1:9224"
      }
    }
  }
}
```

启动多个 Chrome 实例：

```bash
chrome --remote-debugging-port=9222 --user-data-dir="/tmp/chrome-personal"
chrome --remote-debugging-port=9223 --user-data-dir="/tmp/chrome-work"
chrome --remote-debugging-port=9224 --user-data-dir="/tmp/chrome-testing"
```

## 四、完整配置示例

以下示例只展示结构，不包含真实 Token。

```json
{
  "browser": {
    "defaultProfile": "remote-debug",
    "profiles": {
      "remote-debug": {
        "cdpUrl": "http://127.0.0.1:9222",
        "color": "#0066CC"
      }
    }
  },
  "tools": {
    "profile": "full",
    "fs": {
      "workspaceOnly": true
    }
  },
  "gateway": {
    "port": 18789,
    "bind": "loopback",
    "auth": {
      "mode": "token",
      "token": "<SECURE_TOKEN>"
    }
  }
}
```

## 五、常见问题

### 配置后没有生效

重启 Gateway：

```bash
openclaw gateway restart
```

### Chrome 远程调试连接失败

检查：

```bash
lsof -i :9222
```

也可以直接访问：

```text
http://localhost:9222/json
```

### 文件系统仍然无权限

确认工具配置：

```bash
openclaw config get tools
openclaw gateway status
```


## 相关文档

- [03_PinchTab 完整指南](./03_PinchTab%20完整指南.md)
- [04_OpenClaw 多 Agent 配置指南](./04_OpenClaw%20多%20Agent%20配置指南.md)
- [05_OpenClaw 多 Agent 快速使用](./05_OpenClaw%20多%20Agent%20快速使用.md)
