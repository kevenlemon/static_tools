# static_tools

面向 Linux 环境的便携数据库客户端集合，适合在跳板机、容器、临时运维环境中快速连接 MySQL、Redis 和 MongoDB，而不必额外安装完整数据库软件。仓库产物以单文件可执行形式分发，尽量减少额外系统依赖；体积较大的 `mongosh` 使用 gzip 压缩存储，解压后即可在主流 Linux 发行版中使用。

## 来源与可信性

为提升可用性与可审计性，仓库对各类工具的版本与来源进行了明确说明：

- MySQL 客户端基于 `mysql-boost-5.7.44` 源码构建
- Redis 客户端基于 `redis-8.10.1` 源码构建
- MongoDB 工具版本为 `mongodump 100.18.0` 和 `mongosh 2.10.0`
- MySQL 与 Redis 的构建过程通过 GitHub Actions 执行，便于复查构建配置与产物来源

使用者既可以下载后直接使用（`mongosh` 需先解压），也可以根据明确的上游版本与自动构建配置进行复核。

## 仓库内容

当前仓库提供以下可执行文件：

| 文件名 | 架构 | 用途 |
| --- | --- | --- |
| `mysql_x86_64` | x86_64 | MySQL 命令行客户端 |
| `mysqldump_x86_64` | x86_64 | MySQL 备份导出工具 |
| `redis-cli_x86_64` | x86_64 | Redis 命令行客户端 |
| `mongosh_x86_64.gz` | x86_64 | MongoDB Shell 命令行客户端（gzip 压缩包） |
| `mongodump_x86_64` | x86_64 | MongoDB 备份导出工具 |
| `mysql_aarch64` | aarch64 | MySQL 命令行客户端 |
| `mysqldump_aarch64` | aarch64 | MySQL 备份导出工具 |
| `redis-cli_aarch64` | aarch64 | Redis 命令行客户端 |
| `mongosh_aarch64.gz` | aarch64 | MongoDB Shell 命令行客户端（gzip 压缩包） |
| `mongodump_aarch64` | aarch64 | MongoDB 备份导出工具 |

## 文件特征

- 单文件可执行；`mongosh` 解压后赋予执行权限即可使用
- 无需额外安装 MySQL、Redis 或 MongoDB 官方客户端软件包
- 已尽量消除对常见运行时库的额外依赖，适合主流 `glibc` Linux 环境分发
- 同时提供 `x86_64` 与 `aarch64` 两种架构版本

## 适用场景

- 目标机器没有安装 MySQL、Redis 或 MongoDB 客户端
- 不希望为了临时连接而安装完整数据库软件包
- 需要在精简 Linux 环境中快速携带和分发客户端工具

## 兼容性说明

- MySQL 客户端构建流程已在 Ubuntu、Debian、Rocky Linux、Amazon Linux 和 openSUSE 容器中验证
- 产物面向主流 `glibc` Linux 环境，不适用于基于 `musl libc` 的 Alpine Linux
- 请根据目标机器架构选择对应文件：`x86_64` 或 `aarch64`
- MySQL 客户端已处理常见的 `libssl` 依赖报错问题

如果运行时出现 `exec format error`，通常表示下载了错误架构的可执行文件。

## 下载

可直接从 GitHub Raw 地址下载对应文件，格式如下：

```text
https://raw.githubusercontent.com/kevenlemon/static_tools/main/<文件名>
```

例如：

```text
https://raw.githubusercontent.com/kevenlemon/static_tools/main/mysql_x86_64
https://raw.githubusercontent.com/kevenlemon/static_tools/main/mysqldump_x86_64
https://raw.githubusercontent.com/kevenlemon/static_tools/main/redis-cli_x86_64
https://raw.githubusercontent.com/kevenlemon/static_tools/main/mongosh_x86_64.gz
https://raw.githubusercontent.com/kevenlemon/static_tools/main/mongodump_x86_64
https://raw.githubusercontent.com/kevenlemon/static_tools/main/mysql_aarch64
https://raw.githubusercontent.com/kevenlemon/static_tools/main/mysqldump_aarch64
https://raw.githubusercontent.com/kevenlemon/static_tools/main/redis-cli_aarch64
https://raw.githubusercontent.com/kevenlemon/static_tools/main/mongosh_aarch64.gz
https://raw.githubusercontent.com/kevenlemon/static_tools/main/mongodump_aarch64
```

`mongosh` 下载后需要先解压，以 `x86_64` 为例：

```bash
gzip -d mongosh_x86_64.gz
```

然后赋予对应文件执行权限：

```bash
chmod +x mysql_x86_64 mysqldump_x86_64 redis-cli_x86_64 mongosh_x86_64 mongodump_x86_64
```

## 使用示例

连接 MySQL：

```bash
./mysql_x86_64 -h 127.0.0.1 -P 3306 -u root -p
```

导出 MySQL 数据：

```bash
./mysqldump_x86_64 -h 127.0.0.1 -P 3306 -u root -p database_name > backup.sql
```

连接 Redis：

```bash
./redis-cli_x86_64 -h 127.0.0.1 -p 6379
```

连接 MongoDB：

```bash
./mongosh_x86_64 "mongodb://127.0.0.1:27017"
```

导出 MongoDB 数据：

```bash
./mongodump_x86_64 --uri="mongodb://127.0.0.1:27017" --out=./mongodb-backup
```

## 隐私与历史记录

如果不希望 MySQL 或 Redis 客户端在本地留下命令历史，可在使用前设置以下环境变量：

```bash
export MYSQL_HISTFILE=/dev/null
export REDISCLI_HISTFILE=/dev/null
```

## 构建说明

MySQL 与 Redis 客户端产物由 GitHub Actions 自动编译生成。

MySQL 客户端基于 `mysql-boost-5.7.44` 官方源码构建，提供两套通用 `glibc` Linux 产物：

- `mysql_x86_64` 与 `mysqldump_x86_64`
- `mysql_aarch64` 与 `mysqldump_aarch64`

构建环境采用 `glibc 2.17` 基线，并对 OpenSSL、`libstdc++`、`libgcc` 和终端库做了低依赖处理，以提升跨发行版兼容性。

Redis 客户端基于 `redis-8.10.1` 官方源码构建，对应产物如下：

- `redis-cli_x86_64`
- `redis-cli_aarch64`

MongoDB 工具提供以下版本与产物：

- `mongodump 100.18.0`：`mongodump_x86_64`、`mongodump_aarch64`
- `mongosh 2.10.0`：`mongosh_x86_64.gz`、`mongosh_aarch64.gz`

`mongodump` 为静态链接可执行文件；`mongosh` 解压后为单文件可执行程序，并依赖主流 Linux 系统通常自带的 `glibc` 运行环境。gzip 压缩包使用无文件名、无时间戳模式生成，不包含构建主机信息。

如需进一步确认构建方式，可查看仓库中的 GitHub Actions 工作流配置文件。
