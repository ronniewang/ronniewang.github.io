---
layout: default
title: 在Linux上安装Redis
description: Redis是最流行的Key-Value存储, 学会安装和使用Redis已经是必备技能
category: tech
---

## 环境

Redis Version：3.0.1及以上  
OS：CentOS 7

## 步骤

### 1. 下载到指定目录并解压

```
> cd /usr/src
> wget -c http://download.redis.io/redis-stable.tar.gz
> tar xvzf redis-stable.tar.gz
```

### 2. 构建

```
> cd redis-stable
> make && make install
```

如果由于没有安装gcc导致make报错，在安装make之后重新构建可能会报jemalloc/jemalloc.h相关错误，可参考
<http://unix.stackexchange.com/questions/94479/jemalloc-and-other-errors-making-redis-on-centos-6-4>进行修复

### 3. 运行安装脚本

```
> utils/install_server.sh
```

脚本会有一系列引导

```
Please select the redis port for this instance: [6379]
```
回车选择6379作为默认端口

```
Please select the redis config file name [/etc/redis/6379.conf] 
```
输入`/etc/redis/redis_6379.conf`，回车

```
Please select the redis log file name [/var/log/redis_6379.log]
```
回车，继续

```
Please select the data directory for this instance [/var/lib/redis/6379]
```
回车，继续

```
Please select the redis executable path [/usr/local/bin/redis-server]
```
回车，继续，屏幕输出配置如下，

```
Selected config:
Port           : 6379
Config file    : /etc/redis/redis_6379.conf
Log file       : /var/log/redis_6379.log
Data dir       : /var/lib/redis/6379
Executable     : /usr/local/bin/redis-server
Cli Executable : /usr/local/bin/redis-cli
Is this ok? Then press ENTER to go on or Ctrl-C to abort.
```
确认无误，回车，屏幕继续输出

```
Copied /tmp/6379.conf => /etc/init.d/redis_6379
Installing service...
Successfully added to chkconfig!
Successfully added to runlevels 345!
Starting Redis server...
Installation successful!
```
安装成功

### 4. 验证是否安装成功

```
> /etc/init.d/redis_6379 status
```
输出

```
Redis is running (27186)
```

查看server信息

```
> redis-cli -p 6379 info server
```
看是否有正确输出

测试是否能写入和读取数据

```
> redis-cli
127.0.0.1:6379> set test 1
OK
127.0.0.1:6379> get test
"1"
```

ok，没问题，至此，安装成功
