---
layout: default
title: 配置MySQL Master-Master复制
description: 配置MySQL Master-Master复制, 提高数据安全性和服务高可用性
category: tech
---

## 概述

我们要在两台主机上设置Mysql双主复制架构，假设两台服务器分别为Server C和Server D

Server C: 3.3.3.3

Server D: 4.4.4.4

### Step 1 - 在Server C上安装Mysql

我的主机系统是Centos7

```
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
sudo rpm -ivh mysql-community-release-el7-5.noarch.rpm
yum update
sudo yum install mysql-server
```

修改Mysql配置文件/etc/my.cnf，将下面列出几项配置改为如下配置：

```
[mysqld]
server-id = 1
log_bin = /var/log/mysql/mysql-bin.log
binlog_do_db = example
# bind-address = 127.0.0.1
```

第一行唯一标识我们的主机

第二行表示我们对数据库和表做的操作都会被记录

第三行设置我们需要复制的数据库，可以指定多个，这里为了简单事例，只写一个

最后一行表示Mysql接受外网的连接

现在，重启Mysql

```
sudo systemctl restart mysqld
```

以root用户登录

```
mysql -u root -p
```

进入之后，建立一个用于复制数据的用户，我们起名叫“replicator”，password随意指定

```
create user 'replicator'@'%' identified by 'password';
```

将复制权限赋予这个用户

```
grant replication slave on *.* to 'replicator'@'%';
```

复制权限不能以数据库粒度赋予，用户只能根据配置文件中制定的来决定复制哪些数据库

执行下面的命令

```
show master status;
```

下面的输出在配置Server D的时候要用到

```
+------------------+----------+--------------+------------------+  
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |  
+------------------+----------+--------------+------------------+  
| mysql-bin.000001 |      120 | example      |                  |  
+------------------+----------+--------------+------------------+  
1 row in set (0.00 sec)
```

### Step 2 - 为Server D安装和配置Mysql

执行与上面同样的步骤，直到下面这条命令（包含）

```
grant replication slave on *.* to 'replicator'@'%';
```

> 注意, server-id的配置项不能再用1了, 改为2

然后，上面的输出就要用到了，在Mysql的命令行里，执行下面的命令：

```
stop slave;
CHANGE MASTER TO MASTER_HOST = '3.3.3.3', MASTER_USER = 'replicator', MASTER_PASSWORD = 'password', MASTER_LOG_FILE = 'mysql-bin.000001', MASTER_LOG_POS = 120;
start slave;
```

上面的Host，password，log_file，pos等，换成你自己的

然后，同样的，执行下面的命令

```
SHOW MASTER STATUS;
```

输出类似这样的

```
+------------------+----------+--------------+------------------+  
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |  
+------------------+----------+--------------+------------------+  
| mysql-bin.000004 |      107 | example      |                  |  
+------------------+----------+--------------+------------------+  
1 row in set (0.00 sec)
```

这些内容我们在回头配置Server C的时候要用到

### Step 3 - 完成Server C的配置

回到Server C，登入Mysql，执行下面命令

```
stop slave;
CHANGE MASTER TO MASTER_HOST = '4.4.4.4', MASTER_USER = 'replicator', MASTER_PASSWORD = 'password', MASTER_LOG_FILE = 'mysql-bin.000004', MASTER_LOG_POS = 107;
start slave;
```

上面的Host，password，log_file，pos等，换成你自己的

配置就完成了，下面我们测试一下是否成功

### Step 4 - 测试Master-Master配置

在Server C上创建example数据库

```
create database example;
```

然后建张表

```
create table example.dummy (`id` varchar(10));
```

到Server D上看一下

```
show tables in example;
```

输出如下

```
+-------------------+  
| Tables_in_example |  
+-------------------+  
| dummy             |  
+-------------------+  
1 row in set (0.00 sec)
```

下面我们在Server D上删除这个表

```
DROP TABLE dummy;
```

回到Server C上， show tables，发现没有了

```
Empty set (0.00 sec)
```

至此，配置完毕

### 可能遇到的问题

* 安装后无法启动Mysql，<http://stackoverflow.com/questions/9083408/fatal-error-cant-open-and-lock-privilege-tables-table-mysql-host-doesnt-ex>
* show master status命令显示empty，<http://serverfault.com/questions/266876/mysql-simple-replication-problem-show-master-status-produces-empty-set>
