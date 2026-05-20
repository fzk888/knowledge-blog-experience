本文记录 OpenClaw 的基础安装、模型配置、飞书渠道接入和卸载方法。示例中的 Key、Secret 和配对码均为占位符，实际使用时替换为自己的配置。

## 参考资料

- OpenClaw 官网：<https://openclaw.ai>
- 参考文章：<https://www.cnblogs.com/catchadmin/p/19545341>
- 参考文章：<https://zhuanlan.zhihu.com/p/2003776974779330870>

## 安装 Node.js

OpenClaw 依赖 Node.js。macOS 或 Linux 推荐用 `nvm` 安装。

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
. "$HOME/.nvm/nvm.sh"
nvm install 24
node -v
npm -v
```

Windows 可以直接从官网下载安装包：

<https://nodejs.org/zh-cn/download>

## 安装 OpenClaw

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

安装后检查状态：

```bash
openclaw status
openclaw doctor --fix
```

## 配置模型

如果首次配置向导没有出现模型配置，可以重新进入向导：

```bash
openclaw configure
```

常见选择：

- Gateway run：`Local (this machine)`
- Sections：`Model`
- Provider：按实际服务商选择
- Auth method：按账号或 API Key 方式选择
- Endpoint：按地区选择

不要把真实 API Key 写入公开笔记。

## 配置飞书渠道

启用飞书插件：

```bash
openclaw plugins list | grep feishu
openclaw plugins enable feishu
```

配置 App ID 和 App Secret：

```bash
openclaw config set --json channels.feishu.appId '"<FEISHU_APP_ID>"'
openclaw config set --json channels.feishu.appSecret '"<FEISHU_APP_SECRET>"'
```

重启 Gateway：

```bash
openclaw gateway restart
```

给飞书机器人发送消息后，会生成配对码。用自己的配对码完成授权：

```bash
openclaw pairing approve feishu <PAIRING_CODE>
```

## 常用权限范围

飞书权限按需开通即可，不建议一开始给过大的权限。常见权限包括：

- 发送和读取机器人消息
- 读取群成员信息
- 读取和写入文档
- 访问表格
- 下载文件

配置完成后，先用测试群验证消息收发和机器人回复是否正常。

## 卸载 OpenClaw

```bash
openclaw gateway stop
openclaw gateway uninstall
npm uninstall -g openclaw
rm -rf ~/.openclaw
```

如果 `gateway uninstall` 提示命令不存在，可以跳过。

## 排查顺序

1. `openclaw status` 检查服务状态。
2. `openclaw doctor --fix` 自动修复常见问题。
3. `openclaw gateway restart` 重启服务。
4. 检查模型配置和渠道配置是否写入。
5. 检查 Key、Secret、配对码是否使用了自己的值。
