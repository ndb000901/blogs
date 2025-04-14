# Mysql基础-Mysql部署


## 1. 配置文件

```ini

[client]
# 默认连接端口
port = 3306
# 客户端连接的 socket 文件路径（适用于本地连接）
socket = /tmp/mysql.sock

[mysqld]
###########################
# 基础配置
###########################
# MySQL 服务端口
port = 3306
# 服务运行用户（必须是系统已存在的用户）
user = mysql
# 数据库主目录
datadir = /usr/local/mysql/data
# socket 文件路径
socket = /tmp/mysql.sock
# PID 文件
pid-file = /usr/local/mysql/run/mysqld.pid
# 服务器唯一 ID，主从复制必须配置
server-id = 1
# 启动时默认使用 InnoDB 引擎
default_storage_engine = InnoDB

###########################
# 字符集配置（建议统一为 utf8mb4）
###########################
character-set-server = utf8mb4
collation-server = utf8mb4_general_ci

###########################
# 连接与会话管理
###########################
# 最大连接数
max_connections = 500
# 每个用户最大连接数
max_user_connections = 100
# 空闲连接超时时间（秒）
wait_timeout = 600
# 交互式会话超时（默认 28800 秒）
interactive_timeout = 600
# 连接超时
connect_timeout = 10

###########################
# 缓冲与缓存
###########################
# 查询缓存（MySQL 8.0 已移除 query_cache）
# sort_buffer_size 用于 ORDER BY 操作
sort_buffer_size = 4M
# join_buffer_size 用于非索引连接
join_buffer_size = 4M
# 读写缓存
read_buffer_size = 4M
read_rnd_buffer_size = 4M
# 临时表最大内存大小
tmp_table_size = 64M
max_heap_table_size = 64M

###########################
# 日志配置
###########################
# 错误日志文件
log-error = /usr/local/mysql/logs/error.log
# 慢查询日志开启
slow_query_log = 1
# 慢查询日志文件位置
slow_query_log_file = /usr/local/mysql/logs/slow.log
# 超过 2 秒的 SQL 视为慢查询
long_query_time = 2

###########################
# 二进制日志（binlog）配置
###########################
# 启用二进制日志，用于复制和恢复
log-bin = /usr/local/mysql/logs/mysql-bin
# 二进制日志格式（建议 row）
binlog_format = row
# 同步 binlog 频率（1 为每次事务提交立即同步）
sync-binlog = 1
# binlog 保留天数
expire_logs_days = 7
# 日志文件自动清理
binlog_expire_logs_seconds = 604800

###########################
# GTID 配置（如果启用主从复制建议开启）
###########################
gtid_mode = ON
enforce-gtid-consistency = true

###########################
# InnoDB 配置
###########################
# InnoDB 缓冲池大小，建议占总内存 60~70%
innodb_buffer_pool_size = 10G
# 缓冲池实例个数，推荐 1~8
innodb_buffer_pool_instances = 4
# 启用每张表独立的 .ibd 文件
innodb_file_per_table = 1
# 日志文件大小（建议单个不超过 1G）
innodb_log_file_size = 512M
# 日志缓冲区大小
innodb_log_buffer_size = 64M
# 刷新日志策略，1=最安全
innodb_flush_log_at_trx_commit = 1
# 启用 InnoDB 崩溃恢复
innodb_force_recovery = 0
# 自适应哈希索引（可用于优化索引）
innodb_adaptive_hash_index = ON

###########################
# Undo 日志配置（默认使用 undo 表空间）
###########################
innodb_undo_log_truncate = ON
innodb_max_undo_log_size = 2G
innodb_undo_tablespaces = 2

###########################
# 文件 & 目录限制
###########################
open_files_limit = 65535
table_open_cache = 2000
table_definition_cache = 2000

###########################
# 安全设置
###########################
# 禁用远程 root 登录（推荐）
skip_name_resolve = 1
# 密码策略（需要安装 validate_password 插件后生效）
# validate_password.policy = MEDIUM

###########################
# Performance Schema
###########################
performance_schema = ON

###########################
# 临时文件 & 线程配置
###########################
tmpdir = /tmp
thread_cache_size = 100

[mysqldump]
# 导出时最大允许包大小
max_allowed_packet = 1G
# 添加事务支持（防止导出中断）
quick
quote-names

[mysql]
# 默认字符集
default-character-set = utf8mb4

[myisamchk]
# 索引文件缓存
key_buffer_size = 128M
sort_buffer_size = 128M
read_buffer = 2M
write_buffer = 2M

```