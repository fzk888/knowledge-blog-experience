> 记录一次 Windows 和 macOS 之间共享鼠标键盘的配置过程，也顺手整理 Windows 与 Windows 之间更省心的 PowerToys Mouse Without Borders 方案。

## 为什么需要这类工具

如果桌面上同时放着 Windows 台式机、Windows 笔记本、MacBook 或 Linux 机器，最麻烦的不是性能，而是手总在几套键盘鼠标之间来回切。软件 KVM 的思路就是：不切显示器，只共享输入设备，让鼠标越过屏幕边缘后，键盘焦点也跟着切到另一台电脑。

这类工具适合：

- 一台主力机外接键盘鼠标，旁边放一台笔记本。
- Windows 和 Mac 同时办公，需要复制文本、代码、链接。
- 多台 Windows 机器同处一个局域网，希望一套键鼠操作。

我最后的结论很简单：

- **Windows、macOS、Linux 混用：优先 Deskflow。**
- **Windows 与 Windows 之间：优先 PowerToys 的 Mouse Without Borders。**

## 方案一：Deskflow，适合 Windows、macOS、Linux 混用

Deskflow 是开源的键盘鼠标共享工具，项目地址是：

- GitHub：<https://github.com/deskflow/deskflow>
- 官方说明里提到它支持 Windows、macOS、Linux，并且默认支持 TLS 加密、剪贴板共享。
- 参考文章：<https://zhuanlan.zhihu.com/p/1897047939345528204>

我更建议从官方 GitHub 或官方推荐的包管理器安装，不要优先找来路不明的网盘包。

### 下载与安装

Windows：

1. 打开 <https://github.com/deskflow/deskflow/releases>
2. 下载 Windows 安装包或压缩包。
3. 如果运行时报缺少运行库，先安装 Microsoft Visual C++ Redistributable。

macOS：

推荐用 Homebrew：

```bash
brew tap deskflow/tap
brew install deskflow
```

如果是直接下载 `.app`，macOS 可能提示应用损坏或来源不明，可以尝试：

```bash
xattr -c /Applications/Deskflow.app
```

然后在系统设置里给权限：

- Privacy & Security -> Accessibility
- Privacy & Security -> Input Monitoring
- macOS Sequoia 还可能需要允许 Local Network

源码方式：

```bash
git clone https://github.com/deskflow/deskflow.git
```

源码编译更适合开发者，普通使用直接装发布版就好。

### 基本概念：谁是 Server，谁是 Client

Deskflow 里最容易搞反的是 Server 和 Client。

**Server 是插着键盘鼠标、用来控制别人的那台电脑。**  
**Client 是被控制的那台电脑。**

比如我的局域网里：

- Windows A：`192.168.5.150`
- Windows B 或 Mac：`192.168.5.152`

如果键盘鼠标接在 `192.168.5.150` 上，那 `192.168.5.150` 就是 Server，另一台填 Server 地址 `192.168.5.150` 加入。

如果我想让 `192.168.5.150` 变成 Client，被 `192.168.5.152` 控制，那么 `192.168.5.152` 必须先启动 Deskflow Server，并监听默认端口 `24800`。

Windows 上可以这样测网络：

```powershell
Test-NetConnection -ComputerName 192.168.5.152 -Port 24800
```

如果 `TcpTestSucceeded` 是 `False`，说明不是 Deskflow 配置问题，而是对方没有启动 Server、端口不对，或者防火墙拦了。

### Deskflow 的正常配置流程

1. 两台电脑连到同一个局域网。
2. 主控电脑选择 Server。
3. 被控电脑选择 Client。
4. Client 填 Server 的 IP，例如 `192.168.5.150`。
5. Server 端把 Client 的屏幕放到左边或右边。
6. Windows 防火墙弹窗时允许专用网络访问。
7. 鼠标从屏幕边缘划出去测试切换。

默认端口通常是 `24800`。如果连不上，先测端口，不要先怀疑软件坏了。

## 这次踩坑：Windows 客户端明明填了服务器地址，却报没填

我这次遇到的日志是：

```text
FATAL: a runtime error occurred: No server addresses configured
ERROR: standard exception on thread ... resource deadlock would occur
```

奇怪的是，配置文件里明明有：

```ini
[client]
remoteHost=192.168.5.152
```

但 GUI 仍然提示没有服务器地址。后来发现问题不在 IP，也不在网络，而在 Windows 的 Deskflow 后台服务模式。

Deskflow 在 Windows 上有一个后台服务，也叫 daemon。它的作用是让 Deskflow 能配合 UAC 提示和登录屏幕。但在我的环境里，后台服务用 LocalSystem 身份启动 `deskflow-core.exe`，反复用空地址启动 Client，于是出现循环报错。

判断方法：

```powershell
Get-Service -Name Deskflow
Get-CimInstance Win32_Process | Where-Object { $_.Name -like 'deskflow*' }
```

如果看到很多 `deskflow-core.exe`，并且日志一直刷 `No server addresses configured`，大概率就是后台服务模式在捣乱。

### 解决方式：让 GUI 使用桌面模式启动 core

最稳定的办法是：**用 Deskflow 图形界面，但关闭后台服务模式，让 GUI 自己拉起客户端核心进程。**

配置文件位置：

```text
C:\Users\<用户名>\AppData\Roaming\Deskflow\Deskflow.conf
```

关键配置：

```ini
[core]
coreMode=1
processMode=1

[gui]
startCoreWithGui=true

[client]
remoteHost=192.168.5.152

[daemon]
elevate=false
```

其中：

- `coreMode=1` 表示 Client。
- `coreMode=2` 表示 Server。
- `processMode=1` 表示 Desktop 桌面模式。
- `processMode=0` 表示 Service 后台服务模式。
- `remoteHost` 填真正的 Server IP。

然后用管理员 PowerShell 停掉后台服务，并改成手动启动：

```powershell
sc.exe stop Deskflow
sc.exe config Deskflow start= demand
taskkill /F /IM deskflow-core.exe
taskkill /F /IM deskflow-daemon.exe
```

再重新打开：

```powershell
C:\Path\To\Deskflow\deskflow.exe
```

最后确认连接：

```powershell
Get-NetTCPConnection -RemoteAddress 192.168.5.152 -RemotePort 24800
```

如果状态是 `Established`，说明 GUI 已经成功连接。

这个模式的代价是：UAC 弹窗和登录屏幕可能不能被另一台电脑控制。但日常桌面共享鼠标键盘是正常的。

## macOS 侧的注意事项

Deskflow 在 macOS 上最常见的问题不是 IP，而是权限。

需要检查：

- Accessibility：允许 Deskflow 控制电脑。
- Input Monitoring：允许监听输入。
- Local Network：允许访问局域网。
- 如果升级过 Deskflow，旧权限可能失效，删掉旧项后重新添加。

如果 macOS 端是 Client，Windows 端是 Server，那么 macOS 只要填 Windows 的 IP。  
如果 macOS 端是 Server，Windows 端是 Client，那么 Windows 需要能连通 Mac 的 `24800` 端口。

## 方案二：PowerToys Mouse Without Borders，适合 Windows 与 Windows

如果你的设备全是 Windows，其实不用先折腾 Deskflow。微软 PowerToys 里已经集成了 Mouse Without Borders。

项目地址：

- GitHub：<https://github.com/microsoft/PowerToys>
- 文档：<https://learn.microsoft.com/en-us/windows/powertoys/mouse-without-borders>

PowerToys 安装方式：

```powershell
winget install --id Microsoft.PowerToys --source winget
```

或者去 GitHub Releases 下载：

<https://github.com/microsoft/PowerToys/releases>

### Mouse Without Borders 配置流程

1. 两台 Windows 都安装 PowerToys。
2. 打开 PowerToys Settings。
3. 找到 Mouse Without Borders。
4. 第一台电脑点击 New Key 生成安全密钥。
5. 第二台电脑输入第一台的 Security Key 和电脑名。
6. 点击 Connect。
7. 在 Device layout 里拖动电脑位置。
8. 鼠标从屏幕边缘划过去测试。

PowerToys 的优势：

- Windows 原生体验更好。
- 配置逻辑比 Deskflow 简单。
- 支持剪贴板共享。
- 支持文件传输，但有大小和单文件限制。
- 最多控制四台电脑。

如果要控制 UAC 或锁屏界面，需要用管理员身份运行 PowerToys，然后打开 Use Service。不过这和 Deskflow 的后台服务一样，会增加一点安全风险，家庭局域网使用时按需开启。

排障顺序：

```text
同一局域网 -> 安全密钥正确 -> 电脑名正确 -> 防火墙放行 -> 状态变绿
```

## Deskflow 与 PowerToys 怎么选

| 场景 | 推荐 |
| --- | --- |
| Windows + macOS | Deskflow |
| Windows + Linux | Deskflow |
| Windows + Windows | PowerToys Mouse Without Borders |
| 想兼容 Barrier、Input Leap、Synergy 1 | Deskflow |
| 只想最快配置两台 Windows | PowerToys |
| 需要登录屏幕、UAC 场景 | 两者都要考虑服务模式和安全风险 |

我的建议是：

- 纯 Windows：先用 PowerToys。
- 只要跨到 macOS 或 Linux：用 Deskflow。
- 如果 Deskflow 在 Windows 客户端报 `No server addresses configured`，优先检查后台服务模式，不要反复改 IP。

## 最后记一条经验

这次最有价值的经验不是某个按钮在哪，而是排障顺序：

1. 先确认谁是 Server，谁是 Client。
2. 再确认 Server IP 和端口 `24800` 是否通。
3. 再看 Deskflow GUI 是否把地址保存到 `remoteHost`。
4. 最后才查 Windows 后台服务、进程残留和 `processMode`。

很多“我明明输入了地址却说没输入”的问题，本质不是人没输，而是后台服务以另一套上下文启动，没有读到 GUI 保存的地址。把 Windows 端切到 Desktop 模式，让图形界面自己启动核心进程，问题就清爽多了。

## 参考

- Deskflow GitHub：<https://github.com/deskflow/deskflow>
- Deskflow Releases：<https://github.com/deskflow/deskflow/releases>
- Deskflow GUI Settings：<https://github.com/deskflow/deskflow/wiki/GUI-Settings>
- Deskflow Workarounds：<https://github.com/deskflow/deskflow/wiki/Workarounds>
- PowerToys GitHub：<https://github.com/microsoft/PowerToys>
- PowerToys 安装文档：<https://learn.microsoft.com/en-us/windows/powertoys/install>
- Mouse Without Borders 文档：<https://learn.microsoft.com/en-us/windows/powertoys/mouse-without-borders>
- 知乎参考文章：<https://zhuanlan.zhihu.com/p/1897047939345528204>
