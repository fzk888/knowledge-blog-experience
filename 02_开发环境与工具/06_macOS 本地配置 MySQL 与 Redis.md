# macOS 本地配置 MySQL 与 Redis

这篇记录一次真实的本地开发环境配置过程：在 macOS 上安装 MySQL、Redis，并用 DBeaver / Another Redis Desktop Manager 连接它们。适合后端项目本地启动前准备数据库环境。

## 安装前准备

如果还没装 Homebrew，先安装。装好后确认：

```bash
brew --version
```

后面 MySQL 和 Redis 都用 Homebrew 管理。

## 安装并启动 MySQL

DBeaver 只是数据库客户端，不能自己创建 MySQL 服务。所以要先安装并启动本地 MySQL。

```bash
brew install mysql@8.4
brew services start mysql@8.4
```

确认 MySQL 是否启动：

```bash
nc -zv 127.0.0.1 3306
```

看到类似下面的结果，就说明 MySQL 已经在本机运行：

```bash
Connection to 127.0.0.1 port 3306 succeeded!
```

如果 `mysql` 命令找不到，可以把 MySQL 加到 PATH：

```bash
echo 'export PATH="/opt/homebrew/opt/mysql@8.4/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

测试命令行连接：

```bash
mysql -uroot -e "SHOW DATABASES;"
```

如果 root 设置过密码，用：

```bash
mysql -uroot -p
```

## 用 DBeaver 连接 MySQL

打开 DBeaver，新建连接：

| 配置项 | 值 |
| --- | --- |
| 数据库类型 | MySQL |
| Host | `127.0.0.1` |
| Port | `3306` |
| Username | `root` |
| Password | 本地没设密码就留空 |
| Database | 可以先留空 |

点击 **Test Connection**。

第一次连接时，DBeaver 可能会提示下载 MySQL 驱动，点击下载即可。

这里推荐用 `127.0.0.1`，不要优先用 `localhost`。部分 MySQL 客户端会把 `localhost` 当成本机 socket 连接处理，而 `127.0.0.1` 明确表示 TCP 连接，更少踩坑。

## 用 DBeaver 创建本地项目库

MySQL 连接成功后，在 DBeaver 左侧找到刚刚创建的本地连接，比如 `127.0.0.1` 或 `local mysql`。

操作步骤：

1. 在左侧数据库连接上右键。
2. 选择 **SQL 编辑器**。
3. 点击 **新建 SQL 脚本**。
4. 在打开的 SQL 编辑器里输入：

```sql
CREATE DATABASE rideclaw
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;
```

5. 点击上方的执行按钮，或者使用快捷键：

```text
Command + Enter
```

6. 执行成功后，在左侧连接上右键，选择 **刷新**。
7. 展开连接，应该能看到新建出来的 `rideclaw` 数据库。

如果后续要把项目表结构导入到这个库里，可以先在 SQL 编辑器执行：

```sql
USE rideclaw;
```

确认当前选中的数据库是 `rideclaw` 后，再导入或执行项目里的 SQL 文件。

## 安装并启动 Redis

Redis 也需要先安装服务，再用客户端连接。

```bash
brew install redis
brew services start redis
```

确认 Redis 是否启动：

```bash
redis-cli ping
```

看到下面结果就说明 Redis 正常：

```bash
PONG
```

Redis 默认连接信息：

| 配置项 | 值 |
| --- | --- |
| Host | `127.0.0.1` |
| Port | `6379` |
| Password | 空 |
| Database | `0` |

## 用 Another Redis Desktop Manager 连接 Redis

Redis 图形客户端推荐 Another Redis Desktop Manager，轻量，适合本地开发。

新建连接时填写：

| 配置项 | 值 |
| --- | --- |
| Name | `local redis` |
| Host | `127.0.0.1` |
| Port | `6379` |
| Username | 空 |
| Password | 空 |
| Database | 默认 `0` |
| SSH Tunnel | 关闭 |
| SSL / TLS | 关闭 |

连接成功后，左侧会看到 `DB0`。一开始显示 `No Data` 是正常的，说明项目还没有往 Redis 写 key。

Redis 和 MySQL 有个重要区别：Redis 不需要新建 `rideclaw` 这种数据库名。它默认是编号数据库，比如 `0`、`1`、`2`。本地开发一般直接用 `DB0`。

## 项目里的环境变量

以本地开发为例，`.env` 可以这样配：

```env
DB_HOST=127.0.0.1
DB_PORT=3306
DB_USER=root
DB_PASSWORD=
DB_NAME=rideclaw

REDIS_HOST=127.0.0.1
REDIS_PORT=6379
REDIS_PASSWORD=
REDIS_DB=0

SERVER_PORT=8234
APP_ENV=development
```

如果 MySQL root 有密码，把 `DB_PASSWORD=` 改成对应密码。

## 常用检查命令

检查 MySQL：

```bash
brew services list | grep mysql
nc -zv 127.0.0.1 3306
mysql -uroot -e "SHOW DATABASES;"
```

检查 Redis：

```bash
brew services list | grep redis
redis-cli ping
redis-cli DBSIZE
```

检查项目端口是否被占用：

```bash
lsof -nP -iTCP:8234 -sTCP:LISTEN
```

如果端口被旧进程占用：

```bash
kill <PID>
```

## 小结

本地开发环境的顺序是：

1. 安装并启动 MySQL。
2. 用 DBeaver 连接 MySQL。
3. 创建项目数据库。
4. 安装并启动 Redis。
5. 用 Another Redis Desktop Manager 连接 Redis。
6. 修改项目 `.env`。
7. 启动后端服务。

MySQL 负责持久化业务数据，Redis 负责临时 key、限流、nonce、防重放、队列等高速缓存场景。两个都连通后，本地后端项目基本就具备启动条件了。
