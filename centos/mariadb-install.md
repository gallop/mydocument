# mariaDB 安装和设置

>CentOS 6 或早期的版本中提供的是 MySQL 的服务器/客户端安装包，但 CentOS 7 已使用了 MariaDB 替代了默认的 MySQL。MariaDB数据库管理系统是MySQL的一个分支，主要由开源社区在维护，采用GPL授权许可 MariaDB的目的是完全兼容MySQL，包括API和命令行，使之能轻松成为MySQL的代替品。

## 0. 全部删除MySQL/MariaDB
MySQL 已经不再包含在 CentOS 7 的源中，而改用了 MariaDB;

1.使用rpm -qa | grep mariadb搜索 MariaDB 现有的包：  
如果存在，使用yum remove mysql mysql-server mysql-libs compat-mysql51全部删除；
```
[root@localhost bin]# rpm -qa | grep mariadb
mariadb-libs-5.5.60-1.el7_5.x86_64
[root@localhost bin]# rpm -e --nodeps mariadb-*
错误：未安装软件包 mariadb-*
[root@localhost bin]# yum remove mysql mysql-server mysql-libs compat-mysql51
已加载插件：fastestmirror
参数 mysql 没有匹配
参数 mysql-server 没有匹配
参数 compat-mysql51 没有匹配
正在解决依赖关系
--> 正在检查事务
---> 软件包 mariadb-libs.x86_64.1.5.5.60-1.el7_5 将被 删除
--> 正在处理依赖关系 libmysqlclient.so.18()(64bit)，它被软件包 2:postfix-2.10.1-7.el7.x86_64 需要
--> 正在处理依赖关系 libmysqlclient.so.18(libmysqlclient_18)(64bit)，它被软件包 2:postfix-2.10.1-7.el7.x86_64 需要
--> 正在检查事务
---> 软件包 postfix.x86_64.2.2.10.1-7.el7 将被 删除
--> 解决依赖关系完成

依赖关系解决

========================================================================================================================
 Package                      架构                   版本                               源                         大小
========================================================================================================================
正在删除:
 mariadb-libs                 x86_64                 1:5.5.60-1.el7_5                   @anaconda                 4.4 M
为依赖而移除:
 postfix                      x86_64                 2:2.10.1-7.el7                     @anaconda                  12 M

事务概要
========================================================================================================================
移除  1 软件包 (+1 依赖软件包)

安装大小：17 M
是否继续？[y/N]：y
Downloading packages:
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  正在删除    : 2:postfix-2.10.1-7.el7.x86_64                                                                       1/2
  正在删除    : 1:mariadb-libs-5.5.60-1.el7_5.x86_64                                                                2/2
  验证中      : 2:postfix-2.10.1-7.el7.x86_64                                                                       1/2
  验证中      : 1:mariadb-libs-5.5.60-1.el7_5.x86_64                                                                2/2

删除:
  mariadb-libs.x86_64 1:5.5.60-1.el7_5                                                                                  

作为依赖被删除:
  postfix.x86_64 2:2.10.1-7.el7                                                                                         

完毕！
[root@localhost bin]#
```

## 1. 安装MariaDB
通过yum安装就行了。简单快捷，安装mariadb-server，默认依赖安装mariadb，一个是服务端、一个是客户端。
```
[root@localhost bin]# yum -y install mariadb-server
```

## 2. 配置MariaDB

1. 安装完成后首先要把MariaDB服务开启，并设置为开机启动

```
[root@localhost bin]# systemctl start mariadb //开启服务
[root@localhost bin]# ps -ef |grep mysql
mysql      7136      1  0 14:34 ?        00:00:00 /bin/sh /usr/bin/mysqld_safe --basedir=/usr
mysql      7298   7136  3 14:34 ?        00:00:00 /usr/libexec/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib64/mysql/plugin --log-error=/var/log/mariadb/mariadb.log --pid-file=/var/run/mariadb/mariadb.pid --socket=/var/lib/mysql/mysql.sock
root       7334   6807  0 14:34 pts/0    00:00:00 grep --color=auto mysql
[root@localhost bin]# systemctl enable mariadb //设置为开机自启动服务
Created symlink from /etc/systemd/system/multi-user.target.wants/mariadb.service to /usr/lib/systemd/system/mariadb.service.
[root@localhost bin]#
```
2. 首次安装需要进行数据库的配置，命令都和mysql的一样

```
[root@localhost bin]# mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none): //首次设置直接按回车
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] y
New password:
Re-enter new password:
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] y
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
[root@localhost bin]#
```
3. 测试是否能够登录成功

```
[root@localhost bin]# mysql -u root -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 10
Server version: 5.5.60-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> select user,password from mysql.user;
+------+-------------------------------------------+
| user | password                                  |
+------+-------------------------------------------+
| root | *6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9 |
| root | *6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9 |
| root | *6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9 |
+------+-------------------------------------------+
3 rows in set (0.00 sec)

MariaDB [(none)]>
```
4. 设置MariaDB字符集为utf-8
>##### 注意：mysql真正的utf8是utf8mb4才是有效的utf8

查看/etc/my.cnf文件内容，其中包含一句!includedir /etc/my.cnf.d 说明在该配置文件中引入/etc/my.cnf.d 目录下的配置文件。  
1). 使用vi命令编辑/etc/my.cnf.d 目录下server.cnf文件，在[mysqld]标签下添加

```
# this is only for the mysqld standalone daemon
[mysqld]
character_set_server=utf8mb4
collation-server=utf8mb4_unicode_ci
init_connect='SET NAMES utf8mb4'
#跳过mysql程序起动时的字符参数设置 ，使用服务器端字符集设置
skip-character-set-client-handshake=true

```
>如果/etc/my.cnf.d 目录下无server.cnf文件，则直接在/etc/my.cnf文件的[mysqld]标签下添加以上内容。

2). 文件/etc/my.cnf.d/client.cnf
在[client]中添加
```
[client]
default-character-set=utf8mb4
```
3). /etc/my.cnf.d/mysql-clients.cnf  文件
在  [mysql]  标签下添加
```
default-character-set=utf8mb4
```
4). 重启服务并查看字符集设置
```
[root@localhost my.cnf.d]# systemctl restart mariadb
[root@localhost my.cnf.d]# mysql -u root -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 2
Server version: 5.5.60-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show variables like "%character%";show variables like "%collation%";
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8mb4                    |
| character_set_connection | utf8mb4                    |
| character_set_database   | utf8mb4                    |
| character_set_filesystem | binary                     |
| character_set_results    | utf8mb4                    |
| character_set_server     | utf8mb4                    |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.01 sec)

+----------------------+--------------------+
| Variable_name        | Value              |
+----------------------+--------------------+
| collation_connection | utf8mb4_unicode_ci |
| collation_database   | utf8mb4_unicode_ci |
| collation_server     | utf8mb4_unicode_ci |
+----------------------+--------------------+
3 rows in set (0.00 sec)

MariaDB [(none)]>
```
### 3. 添加用户，设置权限
创建用户命令
```
mysql>create user username@localhost identified by 'password';
```
直接创建用户并授权的命令
```
mysql>grant all on *.* to username@localhost indentified by 'password';
```
授予外网登陆权限
```
MariaDB [(none)]> select host,user from mysql.user;
+-----------+------+
| host      | user |
+-----------+------+
| 127.0.0.1 | root |
| ::1       | root |
| localhost | root |
+-----------+------+
3 rows in set (0.00 sec)

MariaDB [(none)]> grant all privileges on *.* to root@'%' identified by '123456';
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> select host,user from mysql.user;
+-----------+------+
| host      | user |
+-----------+------+
| %         | root |
| 127.0.0.1 | root |
| ::1       | root |
| localhost | root |
+-----------+------+
4 rows in set (0.00 sec)

MariaDB [(none)]> flush privileges;
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]>

```

授予权限并且可以授权
```
mysql>grant all privileges on *.* to username@'hostname' identified by 'password' with grant option;

```
### 4. 远程链接mariadb数据库
mariadb默认是拒绝 root 远程登录的。这里用的是 navicat 软件连接数据库  
1) 关闭防火墙
```
[root@mini ~]# systemctl stop firewalld
```
2)  在不关闭防火墙的情况下，允许某端口的外来链接。步骤如下，开启3306端口，重启防火墙
```
[root@mini ~]# firewall-cmd --query-port=3306/tcp  # 查看3306端口是否开启
no
[root@mini ~]# firewall-cmd --zone=public --add-port=3306/tcp --permanent  # 开启3306端口
success
[root@mini ~]# firewall-cmd --reload  # 重启防火墙
success
[root@mini ~]# firewall-cmd --query-port=3306/tcp  # 查看3306端口是否开启
yes
```

### 5. 忘记root用户名和密码
首先用 killall -TERM mysqld  向mysqld server 发送kill命令关掉mysqld server(不是 kill -9),你必须是UNIX的root用户或者是你所运行的SERVER上的同等用户，才能执行这个操作

然后  /usr/bin/mysqld_safe  --skip-grant-tables --skip-networking &

登录 : mysql -p或者使用mysql无密码登录

>use mysql
>update user set password=password("new_pass") where user="root";
>
>flush privileges;

exit;

修改完成之后重启数据库,即可用修改好 root 密码登录 .
