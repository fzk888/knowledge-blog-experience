这篇整理 Windows Subsystem for Linux 的常用命令和使用习惯。WSL 适合在 Windows 上运行 Linux 开发环境，尤其适合 Python、Node.js、Docker 和命令行工具链。

## WSL 管理命令

以下命令在 PowerShell 中执行。

查看已安装发行版：

```powershell
wsl -l -v
```

启动指定发行版：

```powershell
wsl -d Ubuntu-22.04
```

关闭所有 WSL 实例：

```powershell
wsl --shutdown
```

更新 WSL：

```powershell
wsl --update
```

删除发行版：

```powershell
wsl --unregister Ubuntu-22.04
```

删除发行版会清空该发行版内的数据，执行前需要备份。

## Linux 基础命令

查看当前位置：

```bash
pwd
```

查看文件：

```bash
ls
ls -la
```

切换目录：

```bash
cd ~
cd ..
cd /path/to/project
```

创建目录：

```bash
mkdir projects
```

删除文件或目录：

```bash
rm file.txt
rm -r folder
```

## 文件系统路径

Windows 盘符在 WSL 中挂载到：

```bash
/mnt/c
/mnt/d
```

Linux 用户目录一般在：

```bash
/home/你的用户名
```

长期开发项目建议放在 Linux 文件系统内，例如：

```bash
~/projects
```

这样通常比放在 `/mnt/c` 或 `/mnt/d` 下更快，也更少遇到权限和换行符问题。

## 软件安装

更新软件源：

```bash
sudo apt update
```

安装软件：

```bash
sudo apt install git
sudo apt install python3
```

## 开发常用命令

Git：

```bash
git clone <repo-url>
git status
git pull
```

Python：

```bash
python3 --version
pip3 install <package>
```

查看端口：

```bash
ss -tulpn
```

Docker：

```bash
docker ps
docker images
```

## Windows 与 WSL 互通

在 Windows 资源管理器中打开 WSL：

```text
\\wsl$
```

在 WSL 中用资源管理器打开当前目录：

```bash
explorer.exe .
```

用 VS Code 打开当前项目：

```bash
code .
```

需要先安装 VS Code 的 Remote - WSL 扩展。

## 最常用命令

```bash
wsl -l -v
wsl --shutdown
cd ~
pwd
ls -la
mkdir projects
sudo apt update
git clone <repo-url>
code .
```
