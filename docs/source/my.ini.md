# MariaDB 配置文件

路径：C:\wamp\web\data\my.ini

```ini
[client]
port                        = 3306
plugin-dir                  = C:/wamp/base/mariadb/lib/plugin

[mysqld]
port                        = 3306
datadir                     = C:/wamp/web/data
pid-file                    = C:/wamp/base/conf/mariadb.pid

general_log                 = 0
log_error                   = C:/wamp/web/logs/mariadb/err.log
log_warnings                = 0
slow_query_log              = 0

log_bin                     = C:/wamp/web/data/bin-log
log_bin_index               = C:/wamp/web/data/bin-log.index
binlog_format               = mixed
expire_logs_days            = 30
```
