### [PDO::__construct(): Server sent charset (255) unknown to the client. Please, report to the developers]

微擎出错信息：

**Fatal error**: Uncaught exception 'PDOException' with message 'SQLSTATE[HY000] Server sent charset unknown to the client. Please, report to the developers' in /data/backup/weifang/framework/class/db.class.php:55 Stack trace: #0 /data/backup/weifang/framework/class/db.class.php(55): PDO->construct('mysql:dbname=we...', 'root', 'gPYD3t24', Array) #1 /data/backup/weifang/framework/class/db.class.php(25): DB->connect(Array) #2 /data/backup/weifang/framework/function/pdo.func.php(19): DB->construct(Array) #3 /data/backup/weifang/framework/function/pdo.func.php(65): pdo() #4 /data/backup/weifang/framework/function/cache.mysql.func.php(9): pdo_getcolumn('core_cache', Array, 'value') #5 /data/backup/weifang/framework/function/cache.func.php(39): cache_read('setting') #6 /data/backup/weifang/framework/model/setting.mod.php(34): cache_load('setting') #7 /data/backup/weifang/framework/bootstrap.inc.php(129): setting_load() #8 /data/backup/weifang/index.php(6): require('/data/backup/we...') #9 {main} thrown in /data/backup/weifang/framework/class/db.class.php on line 55



mysql 8.0 其默认编码修改成了**utfmb4**，需要修改配置文件：

```
[client]
default-character-set=utf8

[mysql]
default-character-set=utf8


[mysqld]
collation-server = utf8_unicode_ci
character-set-server = utf8
```

