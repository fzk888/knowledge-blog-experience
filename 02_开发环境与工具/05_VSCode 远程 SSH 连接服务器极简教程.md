这篇记录 VS Code 通过 Remote - SSH 连接 Linux 服务器的基本流程。示例中的 IP、用户名和路径均为占位符，发布时不要写真实服务器信息。

## 安装插件

在 VS Code 扩展市场搜索并安装：

```text
Remote - SSH
```

## 添加服务器

打开命令面板：

```text
Ctrl+Shift+P
```

macOS 可以使用：

```text
Cmd+Shift+P
```

输入并选择：

```text
Remote-SSH: Add New SSH Host
```

填写连接命令：

```bash
ssh username@example.com
```

也可以使用 IP：

```bash
ssh username@192.0.2.10
```

然后选择 SSH 配置文件保存。

## 连接服务器

再次打开命令面板，选择：

```text
Remote-SSH: Connect to Host
```

选择刚才保存的服务器。首次连接时，根据提示选择服务器系统类型，例如 Linux。

如果使用密码登录，输入服务器密码。更推荐使用 SSH Key 登录。

## 打开服务器目录

连接成功后，可以打开远程目录：

```text
Remote-SSH: Connect to Folder
```

常见路径示例：

```text
/home/username/project
/var/www/app
```

打开后，左侧资源管理器会显示服务器文件，可以像本地项目一样编辑、运行终端命令。

## 常见问题

### 看不到文件或连接异常

可以在服务器终端清理 VS Code Server 后重连：

```bash
rm -rf ~/.vscode-server
```

### SSH Key 权限不正确

Linux 或 macOS 上调整权限：

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
```

### Windows 上无法识别 ssh

确认系统已安装 OpenSSH Client：

```powershell
ssh -V
```

## 发布前提醒

- 不要公开真实服务器 IP。
- 不要公开用户名、密码、私钥。
- 配置示例使用 `example.com`、`192.0.2.10` 等文档保留地址。
