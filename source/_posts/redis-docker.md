---
title: docker安装redis
date: 2019-12-11 11:15:58
tags:
- redis
- docker
---
```
docker pull redis:5
```
```javascirpt
docker run \
-p 127.0.0.1:6379:6379 \ //映射端口 容器内到宿主的127.0.0.1:6379
--name redis \ //给容器起名名字叫做redis
-v /data/docker/redis/data:/data \ //映射文件夹
-v /data/docker/redis/redis.conf:/etc/redis/redis.conf \ //映射文件
-d redis:5 \ //使用redis的pull的版本
redis-server /etc/redis/redis.conf \ //使用redis配置文件开启redis服务
--appendonly yes  后台运行
```
redis.conf文件如下
```javascript
    port 6379 //开启端口6379
    bind * //docker内部绑定容器需要使用*号 //不能使用127  127 是相对于母机的如果跨docker没用 docker内部指向不一样
    daemonize no
    pidfile /data/docker/redis/redis.pid
    appendonly yes
```