---
title: redis_contenxt
date: 2020-05-09 14:18:19
tags:
---

Redis的基本类型
    1.String字符串：
        格式：set key value
        string类型是二进制安全的。redis的string类型可以包含任何数据，例如图片或者序列化的对象。string类型是Redis的最基本的数据类型，一个键最大能存储512MB。
    2.Hash哈希
        格式：hmset name key value
        redis的hash是一个键值对key value的集合。
        redis的hash是一个string类型的field和value的映射表，hash适合用于存储对象。
        hash = {
            key1:value1,
            key2:value2,
            kay3:value3
        }
    3.List列表
        Redis的列表是简单的字符串列表，按照插入顺序，你可以添加一个元素到列表的头部，或者尾部。
        格式：lpush name value
        在key对应list的头部添加字符串元素。
        格式rpush name value
        在key对应list的尾部添加字符串元素
        格式lrem name index
    4.Set集合
        格式：sadd name value
        Redis的Set是string类型的无序集合。
        集合是通过hash表实现的所以添加删除查找的复杂都是O(1)
    5.Zset 有序集合
        格式 zadd name score value
        Redis zset 和set 一样也是string类型元素的集合，且不允许重复的成员
        不同的是每个元素都会关联一个double类型的分数，redis是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的，分数是可以重复的。
        其中分数score是double 16位超过位数 会转换成科学算法所以最好不要超过16位
Redis持久化
    1.RDB
        rdb是Redis DataBase缩写
        功能核心函数rdbSave(生成RDB文件)和rdbLoad（从文件加载内存）两个函数

    2.AOF
        Aof是Append-only file缩写
        每当执行服务器(定时)任务或者函数时flushAppendOnlyFile 函数都会被调用， 这个函数执行以下两个工作
        aof写入保存：
            WRITE：根据条件，将 aof_buf 中的缓存写入到 AOF 文件
            SAVE：根据条件，调用 fsync 或 fdatasync 函数，将 AOF 文件保存到磁盘中。
    比较：
    1、aof文件比rdb更新频率高，优先使用aof还原数据。
    2、aof比rdb更安全也更大
    3、rdb性能比aof好
    4、如果两个都配了优先加载AOF


## 搭建redis集群 （使用docker搭建redis-cluster环境）
    1.下载redis镜像。
```javascript
        docker pull redis
```
    2.创建docker的虚拟网卡
```javascript
    docker network create redis-net
```
    查看docker的网卡信息
    docker network ls
    查看docker网络详情
    docker network inspect redis-net
可以看到docker虚拟镜像分配的网关以及网络组
    
    创建redis配置文件模板
    本文例子在/home文件夹中操作
    mkdir -p /home/redis-cluster
    cd /home/redis-cluster
    vim redis-cluster.tmpl
    在redis-cluster.tmpl中输入以下内容
```
    port ${PORT}
    cluster-enabled yes
    cluster-config-file nodes.conf
    cluster-node-timeout 5000
    cluster-announce-ip 192.168.168.131
    cluster-announce-port ${PORT}
    cluster-announce-bus-port 1${PORT}
    appendonly yes
```
port：节点端口，即对外提供通信的端口
cluster-enabled：是否启用集群
cluster-config-file：集群配置文件
cluster-node-timeout：连接超时时间
cluster-announce-ip：集群各节点IP地址
cluster-announce-port：集群节点映射端口
cluster-announce-bus-port：集群总线端口
appendonly：持久化模式

cluster-announce-ip:这个IP需要特别注意一下，如果要对外提供访问功能，需要填写宿主机的IP，如果填写docker分配的IP（172.x.x.x），可能会导致部分集群节点在跳转时失败。

    4、创建节点配置文件
      在redis-cluser文件夹中执行以下命令
```
    for port in $(seq 8010 8015); \
    do \
        mkdir -p ./${port}/conf  \
        && PORT=${port} envsubst < ./redis-cluster.tmpl > ./${port}/conf/redis.conf \
        && mkdir -p ./${port}/data; \
    done
```
循环8010 - 8015这6个端口 在当前文件下（即redis-cluster）下创建 8010-8015这6个文件夹，在每个文件夹下面创建conf文件夹和data文件夹然后再在conf文件夹下面创建redis.conf文件。
将redis-cluster.tmpl文件拷贝到各个文件redis.conf配置文件中。

5.使用docker创建redis节点容器
    for port in $(seq 8010 8015); \
do \
   docker run -it -d -p ${port}:${port} -p 1${port}:1${port} \
  --privileged=true -v /home/redis-cluster/${port}/conf/redis.conf:/usr/local/etc/redis/redis.conf \
  --privileged=true -v /home/redis-cluster/${port}/data:/data \
  --restart always --name redis-${port} --net redis-net \
  --sysctl net.core.somaxconn=1024 -d redis:5 redis-server /usr/local/etc/redis/redis.conf; \
done
该命令即创建端口从8010到8015的redis容器并使用之前创建的配置文件


可查看docker给每个节点分配的ip信息
```
docker network inspect redis-net | grep -i -E ”name|ipv4address“
```

6.将以上redis容易创建成一个redis-cluster集群
随便进入一个容器
docker exec -it redis-8010 bash
```
cd /usr/local/bin/

redis-cli --cluster create 【之前的ip】:8010 【】:8011 【】:8012 【】:8013 【】:8014 【】:8015

```

测试是否成功
在其中一个容器内执行
```
    redis-cli -c -h 【ip】 -p 8010
```
set a aaaa
get a 会输出aaaa


在其他地方使用redis-cli测试集群是否可访问同上
转载于 使用docker搭建redis集群[https://www.jianshu.com/p/1fb5e651b053]