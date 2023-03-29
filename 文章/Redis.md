# Redis

## 1. Redis 是什么
    Redis 是一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。它通常被称为数据结构服务器，因为值（value）可以是 字符串(String), 哈希(Hash), 列表(list), 集合(sets) 和 有序集合(sorted sets)等类型。

## 2. Redis 为什么这么快
    Redis 的速度快，是因为它是基于内存的，数据存在内存中，读写速度快。Redis 的速度快，是因为它是单线程的，不需要上下文切换，不需要加锁，不需要竞争，所以速度快。
## 3. Redis 为什么这么安全
    Redis 的安全性高，是因为它是单线程的，不需要上下文切换，不需要加锁，不需要竞争，所以安全性高。
## 4. Redis 应用场景
    1. 缓存
    2. 计数器
    3. 消息队列
    4. 排行榜
    5. 分布式锁
    6. 分布式Session
    7. 分布式任务队列
    8. 分布式限流
## 5.redis的数据类型
    1. String (字符串)
    2. Hash (哈希)
    3. List (列表)
    4. Set  (集合)
    5. Sorted Set (有序集合)
   

## Redis 安装


### 1. docker方式安装
docker安装及使用教程：[Docker使用指南](/文章/Docker使用指南)

```bash
       docker pull redis
```
### 2.准备工作
```bash
       # 创建目录
       mkdir -p /data/redis
       # 创建配置文件
       touch /data/redis/redis.conf
```
### 3.配置文件
   可以复制配置文件到/data/redis/redis.conf，也可以直接在/data/redis/redis.conf中写入配置信息
   主要配置信息如下：
```bash
       # redis.conf
       # 端口
       port 6379
       # 密码
       requirepass 123456
       # 绑定ip
       #bind 127.0.0.1 #注释掉此项允许外部访问
       # 客户端超时时间
       timeout 300
```
### 4.启动
```bash
       docker run -p 6379:6379 --name redis -v /data/redis/redis.conf:/etc/redis/redis.conf -v /data/redis/data:/data -d redis redis-server /etc/redis/redis.conf --appendonly yes
```
    -p 6379:6379：将容器的6379端口映射到主机的6379端口

    --name redis：容器名称为redis

    -v /data/redis/redis.conf:/etc/redis/redis.conf：将主机中的/data/redis/redis.conf挂载到容器的/etc/redis/redis.conf

    -v /data/redis/data:/data：将主机中的/data/redis/data 挂载到容器的/data

    -d redis：以后台模式运行redis

    redis-server /etc/redis/redis.conf ：启动redis时使用/etc/redis/redis.conf配置文件

    --appendonly yes：开启redis持久化
    