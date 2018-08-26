# mysql-8.0 安装教程（自定义配置文件，密码方式已修改）



## 下载：

　　MySQL8.0 For Windows zip包下载地址：https://dev.mysql.com/downloads/file/?id=476233，进入页面后可以不登录。后点击底部“No thanks, just start my download.”即可开始下载。

　　或直接下载：https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.11-winx64.zip

环境：Windows 10

 ## 一，安装
### 1.1，解压zip包到安装目录

　　比如我的安装目录是：C:\Program Files\MySQL

### 1.2，配置文件

　　在Windows系统中，配置文件默认是安装目录下的 my.ini 文件（或my-default.ini），部分配置需要在初始安装时配置，大部分也可以在安装完成后进行更改。当然，极端情况下，所有的都是可以更改的。

　　我们发现解压后的目录并没有my.ini文件，没关系可以自行创建。在安装根目录下添加 my.ini，比如我这里是：C:\Program Files\MySQL\my.ini，写入基本配置：

```
 

[mysqld]
#设置3306端口
port=3306
#设置mysql的安装目录
basedir=C:\Program Files\MySQL
#设置mysql数据库的数据的存放目录
datadir=E:\database\MySQL\Data
#允许最大连接数
max_connections=200
#允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统
max_connect_errors=10
#服务端使用的字符集默认为UTF8
character-set-server=utf8
#创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
#默认使用“mysql_native_password”插件认证
default_authentication_plugin=mysql_native_password
[mysql]
#设置mysql客户端默认字符集
default-character-set=utf8
[client]
#设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8


```



注意，里面的 basedir 是我本地的安装目录，datadir 是我数据库数据文件要存放的位置，各项配置需要根据自己的环境进行配置。

查看所有的配置项，可参考：https://dev.mysql.com/doc/refman/8.0/en/mysqld-option-tables.html

### 1.3，初始化数据库

在MySQL安装目录的 bin 目录下执行命令：

```
mysqld --initialize --console
```

执行完成后，会打印 root 用户的初始默认密码，比如：

```
C:\Users\Administrator>cd C:\Program Files\MySQL\bin

C:\Program Files\MySQL\bin>mysqld --initialize --console
2018-04-28T15:57:17.087519Z 0 [System][MY-013169] [Server] C:\Program Files\MySQL\bin\mysqld.exe (mysqld 8.0.11) initializing of server in progress as process 4984
2018-04-28T15:57:24.859249Z 5 [Note][MY-010454] [Server] A temporary password is generated for root@localhost: rI5rvf5x5G,E
2018-04-28T15:57:27.106660Z 0 [System][MY-013170] [Server] C:\Program Files\MySQL\bin\mysqld.exe (mysqld 8.0.11) initializing of server has completed

C:\Program Files\MySQL\bin>

```

　　注意！执行输出结果里面有一段:  <font color=#32CD32> [Note][MY-010454] [Server] A temporary password is generated for root@localhost: rI5rvf5x5G,E </font> 其中root@localhost:后面的“rI5rvf5x5G,E”就是初始密码（不含首位空格）。在没有更改密码前，需要记住这个密码，后续登录需要用到。

　　要是你手贱，关快了，或者没记住，那也没事，删掉初始化的 datadir 目录，再执行一遍初始化命令，又会重新生成的。当然，也可以使用安全工具，强制改密码，用什么方法，自己随意。

参考：https://dev.mysql.com/doc/refman/8.0/en/data-directory-initialization-mysqld.html

 

### 1.4，安装服务

在MySQL安装目录的 bin 目录下执行命令（以管理员身份打开cmd命令行，或者在安装目录Shift+右键“在此处打开命令行窗口”）：

`mysqld --install [服务名]`

后面的服务名可以不写，默认的名字为 mysql。当然，如果你的电脑上需要安装多个MySQL服务，就可以用不同的名字区分了，比如 mysql5 和 mysql8。

安装完成之后，就可以通过命令net start mysql启动MySQL的服务了。

示例：

```

C:\Program Files\MySQL\bin>mysqld --install
Service successfully installed.

C:\Program Files\MySQL\bin>net start mysql
MySQL 服务正在启动 ..
MySQL 服务已经启动成功。

C:\Program Files\MySQL\bin>


```


参考：https://dev.mysql.com/doc/refman/8.0/en/windows-start-service.html

 

## 二，更改密码和密码认证插件


　　在MySQL安装目录的 bin 目录下执行命令：

`mysql -u root -p`


　　这时候会提示输入密码，记住了上面第1.3步安装时的密码，填入即可登录成功，进入MySQL命令模式。

在MySQL8.0.4以前，执行

SET PASSWORD=PASSWORD('[修改的密码]');
就可以更改密码，但是MySQL8.0.4开始，这样默认是不行的。因为之前，MySQL的密码认证插件是“mysql_native_password”，而现在使用的是“caching_sha2_password”。

　　因为当前有很多数据库工具和链接包都不支持“caching_sha2_password”，为了方便，我暂时还是改回了“mysql_native_password”认证插件。

修改用户密码，在MySQL中执行命令：

`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';`


修改密码验证插件，同时修改密码。

　　如果想默认使用“mysql_native_password”插件认证，可以在配置文件中配置default_authentication_plugin项。

```
[mysqld]
default_authentication_plugin=mysql_native_password
```

示例：

```
C:\Program Files\MySQL\bin>mysql -u root -p
Enter password: ************
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.11

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';
Query OK, 0 rows affected (0.06 sec)

mysql>

```


参考：https://dev.mysql.com/doc/refman/8.0/en/upgrading-from-previous-series.html#upgrade-caching-sha2-password

 

　　到此，安装部署就完成了。官方说测试速度MySQL8比5快**两倍**。

 

　　可以用 命令查看一下默认安装的数据库：

**show databases;**

**use mysql;**

**show tables;**

```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.01 sec)

mysql>

```


看到默认初始化了mysql数据库，其中user表里面存储MySQL用户信息。我们可以看一下默认MySQL用户：

 **select user,host,authentication_string from mysql.user;**

```
mysql> select user,host,authentication_string from mysql.user;
+------------------+-----------+-------------------------------------------+
| user             | host      | authentication_string                     |
+------------------+-----------+-------------------------------------------+
| mysql.infoschema | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
| mysql.session    | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
| mysql.sys        | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
| root             | localhost | *27C237A977F4F44D3F551F1A673BE14DFD232961 |
+------------------+-----------+-------------------------------------------+
4 rows in set (0.00 sec)

mysql>
```





管理员root的host是localhost，代表仅限localhost登录访问。如果要允许开放其他ip登录，则需要添加新的host。如果要允许所有ip访问，可以直接修改成“**%**”

创建用户：

`CREATE USER 'xxh'@'%' IDENTIFIED WITH mysql_native_password BY 'xxh123!@#';`

#(需要注意：mysql8.0加密方式修改了)
#检查用户

`select user, host, plugin, authentication_string from user\G;`

授权远程数据库

#授权所有权限 
`GRANT ALL PRIVILEGES ON *.* TO 'xxh'@'%'；`
#授权基本的查询修改权限，按需求设置
`GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER ON *.* TO 'xxh'@'%';`

查看用户权限
`show grants for 'xxh'@'%';`

 

示例：

 

```
mysql> use mysql;
Database changed
mysql> CREATE USER 'xxh'@'%' IDENTIFIED WITH mysql_native_password BY 'xxh123!@#'; #创建用户(注意：mysql8.0加密方式修改了)
Query OK, 0 rows affected (0.07 sec)
mysql> 

```





查看密码加密方式：

 

```
mysql> select user, host, plugin, authentication_string from user;
+------------------+-----------+-----------------------+-------------------------------------------+
| user             | host      | plugin                | authentication_string                     |
+------------------+-----------+-----------------------+-------------------------------------------+
| xxh              | %         | mysql_native_password | *70FD6FB4F675E08FF785A754755B5EBA6DA62851 |
| mysql.infoschema | localhost | mysql_native_password | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
| mysql.session    | localhost | mysql_native_password | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
| mysql.sys        | localhost | mysql_native_password | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
| root             | localhost | mysql_native_password | *27C237A977F4F44D3F551F1A673BE14DFD232961 |
+------------------+-----------+-----------------------+-------------------------------------------+
5 rows in set (0.00 sec)

mysql>

```



　　另外，如果需要新增账户，或者本机以外的其他人访问MySQL则还需要设置内置账户的host，具体可以参考：[MySQL创建用户与授权](https://www.cnblogs.com/sos-blue/p/6852945.html)







[转载自](http://www.cnblogs.com/xiongzaiqiren/p/8970203.html)
