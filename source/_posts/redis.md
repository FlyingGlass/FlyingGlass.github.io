---
title: redis配置
date: 2018-01-13 21:22:09
categories: 
- [Devops]
---

#### Prerequistes

- Centos

  ```bash
  yum -y install tcl
  ```

- Ubuntu

  ```bash
  apt-get -y install build-essential tcl
  ```

#### redis源码编译安装


``` bash
REDIS_VERSION=4.0.1
wget http://download.redis.io/releases/redis-$REDIS_VERSION.tar.gz
tar -zxvf redis-$REDIS_VERSION.tar.gz
cd redis-$REDIS_VERSION
make
make test
sudo make install
# Once the program has been installed
# Redis comes with a built in script that sets up Redis to run as a background daemon.
cd utils
sudo ./install_server.sh
# Start and stop
sudo service redis_6379 start
sudo service redis_6379 stop
# To set Redis to automatically start at boot, run:
sudo update-rc.d redis_6379 defaults
```

#### /etc/redis/6379.conf 配置
``` bash
➜  ~ grep -v "#" /etc/redis/6379.conf | grep -v "^$"
bind 0.0.0.0
protected-mode yes
port 6379
tcp-backlog 511
timeout 0
tcp-keepalive 300
daemonize yes
supervised no
pidfile /var/run/redis_6379.pid
loglevel notice
logfile /var/log/redis_6379.log
databases 16
always-show-logo yes
save 900 1
save 300 10
save 60 10000
stop-writes-on-bgsave-error yes
rdbcompression yes
rdbchecksum yes
dbfilename dump.rdb
dir /var/lib/redis/6379
slave-serve-stale-data yes
slave-read-only yes
repl-diskless-sync no
repl-diskless-sync-delay 5
repl-disable-tcp-nodelay no
slave-priority 100
lazyfree-lazy-eviction no
lazyfree-lazy-expire no
lazyfree-lazy-server-del no
slave-lazy-flush no
appendonly no
appendfilename "appendonly.aof"
appendfsync everysec
no-appendfsync-on-rewrite no
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
aof-load-truncated yes
aof-use-rdb-preamble no
lua-time-limit 5000
slowlog-log-slower-than 10000
slowlog-max-len 128
latency-monitor-threshold 0
notify-keyspace-events ""
hash-max-ziplist-entries 512
hash-max-ziplist-value 64
list-max-ziplist-size -2
list-compress-depth 0
set-max-intset-entries 512
zset-max-ziplist-entries 128
zset-max-ziplist-value 64
hll-sparse-max-bytes 3000
activerehashing yes
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit slave 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60
hz 10
aof-rewrite-incremental-fsync yes

```

#### 设置redis密码
``` bash
# 搜索requirepass 即可查看密码，并且设置密码
vim /etc/redis/redis.conf  
# 如果之前没有设置过密码可以通过密码命令行设置密码
CONFIG get requirepass;
# 设置密码 
127.0.0.1:6379> CONFIG set requirepass "tutorialspoint"  
OK  
127.0.0.1:6379> CONFIG get requirepass  
1) "requirepass"  
2) "tutorialspoint
```



