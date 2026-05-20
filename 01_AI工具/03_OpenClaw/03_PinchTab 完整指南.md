PinchTab 是一个轻量级浏览器自动化工具，可以通过命令行控制 Chrome 打开网页、提取文本、截图、输入内容和点击元素。它适合和 OpenClaw 的 Skill 机制配合，用来补充网页浏览能力。

## 安装

```bash
npm install -g pinchtab
```

## 启动服务

```bash
pinchtab server
```

服务启动后，再在另一个终端执行浏览器控制命令。

## 常用命令

```bash
pinchtab nav <url>              # 打开网页
pinchtab text                   # 提取页面文本
pinchtab screenshot             # 截图
pinchtab type "描述" "文本"     # 向指定元素输入文本
pinchtab click "描述"           # 点击指定元素
```

## 基础示例

```bash
pinchtab nav https://example.com
pinchtab text
pinchtab screenshot
```

表单操作示例：

```bash
pinchtab type "用户名" "admin"
pinchtab type "密码" "<PASSWORD>"
pinchtab click "登录"
```

## 在 OpenClaw 中封装 Skill

可以创建 Skill 文件：

```text
~/.openclaw/workspace/skills/browser-pinchtab/SKILL.md
```

示例内容：

```markdown
# browser-pinchtab

使用 PinchTab 控制浏览器。

## 激活条件

当用户需要打开网页、填写表单、截图或提取页面内容时使用。

## 常用命令

- `pinchtab nav <url>`：打开网页
- `pinchtab text`：提取页面文本
- `pinchtab type "描述" "文本"`：输入内容
- `pinchtab click "描述"`：点击元素
- `pinchtab screenshot`：截图
```

