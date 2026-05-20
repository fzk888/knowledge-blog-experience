这篇整理 VS Code 中 Git 暂存、储藏、合并和 Pull Request 的常用操作。重点解释一个容易混淆的问题：`stage` 和 `stash` 不是一回事。

## Stage 与 Stash 的区别

| 操作 | 命令 | 作用 | 工作区状态 |
| --- | --- | --- | --- |
| 暂存 | `git add` | 标记哪些修改准备提交 | 文件仍在工作区 |
| 储藏 | `git stash` | 临时保存修改并清空工作区 | 工作区恢复干净 |

合并或切换分支前，如果 Git 提示本地修改会被覆盖，只 `git add` 没用。因为修改仍然在工作区，Git 依然会阻止合并。

正确流程：

```bash
git stash -m "WIP before merge"
git merge origin/main
git stash pop
```

## VS Code 中的 Stash 选项

| VS Code 操作 | 等价命令 | 说明 |
| --- | --- | --- |
| 储藏 | `git stash` | 储藏已跟踪文件的修改 |
| 储藏（包含未跟踪） | `git stash -u` | 同时储藏新文件 |
| 储藏暂存 | `git stash --staged` | 只储藏已暂存内容 |
| 应用最新储藏 | `git stash apply` | 应用但保留 stash 副本 |
| 弹出最新储藏 | `git stash pop` | 应用并删除 stash 副本 |
| 删除储藏 | `git stash drop` | 删除指定 stash |
| 删除所有储藏 | `git stash clear` | 清空所有 stash |

## 场景一：切换分支前保存当前修改

```bash
git stash -u -m "WIP: current changes"
git checkout target-branch
git pull
git checkout original-branch
git stash pop
```

如果使用 VS Code：

1. 打开 Source Control 面板。
2. 选择 `Stash (Include Untracked)`。
3. 输入描述信息。
4. 切换分支或拉取代码。
5. 选择 `Pop Latest Stash` 恢复修改。

## 场景二：合并 main 到当前分支

推荐命令：

```bash
git fetch origin
git stash -u -m "WIP before merging main"
git merge origin/main
git stash pop
```

如果出现冲突：

1. 打开冲突文件。
2. 根据 `<<<<<<<`、`=======`、`>>>>>>>` 标记处理冲突。
3. 在 VS Code 中选择接受当前更改、接受传入更改，或手动编辑。
4. 执行：

```bash
git add .
git commit
```

## 场景三：查看 Stash 内容

```bash
git stash list
git stash show -p stash@{0}
```

只想应用但保留副本：

```bash
git stash apply stash@{0}
```

应用并删除副本：

```bash
git stash pop stash@{0}
```

## VS Code 常用 Git 操作

切换分支：

1. 点击左下角分支名。
2. 选择目标分支。

拉取当前分支：

```bash
git pull
```

合并远程 main：

```bash
git fetch origin
git merge origin/main
```

也可以在命令面板中执行：

```text
Git: Merge Branch
```

## 分支状态说明

| 状态 | 含义 |
| --- | --- |
| Outgoing | 本地有、远程没有的提交 |
| Incoming | 远程有、本地没有的提交 |
| Sync Changes | 同时存在需要 pull 和 push 的提交 |

## 标准提交流程

```bash
git status
git add .
git commit -m "feat: add feature"
git push
```

常见提交类型：

```text
feat: 新功能
fix: 修复问题
docs: 文档更新
refactor: 重构
chore: 工具或构建变动
```

## 创建 Pull Request

1. 推送当前分支到远程。
2. 打开 GitHub 仓库页面。
3. 点击 `Compare & pull request`。
4. 确认：
   - `base` 是目标分支，例如 `main`
   - `compare` 是当前功能分支
5. 填写标题和描述。
6. 点击 `Create pull request`。

## 常用命令速查

```bash
git branch
git checkout main
git checkout -b feature/name

git stash -u -m "WIP"
git stash list
git stash show -p stash@{0}
git stash pop
git stash drop stash@{0}

git fetch origin
git merge origin/main
git rebase origin/main

git status
git diff
git log --oneline -5
git add .
git commit -m "feat: xxx"
git push
```

## 建议

- 合并前先让工作区干净。
- 不确定是否要保留 stash 时，用 `apply`，不要直接 `pop`。
- `stash clear` 会删除所有储藏，执行前先确认。
- 团队协作分支建议优先 merge；个人分支可以按需要 rebase。
