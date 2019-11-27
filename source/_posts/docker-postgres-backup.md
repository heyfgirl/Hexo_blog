---
title: Docker上安装的postgres的备份以及恢复
date: 2019-11-27 16:49:35
tags:
- docker
- postgres
- sql
- 增量备份
---


## 在服务器上使用docker安装postgres

先安装docker，安装之后将postgres从[docker hub](https://hub.docker.com/_/postgres)包管理器pull当前到服务器
```javascript
    //拉取postgres版本号为10.5
    docker pull postgres:10.5
```
然后使用docker安装
```javascript
    docker run --name postgres10 \
    //设置镜像名字叫做 postgres10
    -e POSTGRES_PASSWORD=123456 \
    //设置postgres服务器初始密码
    -p 5434:5433 \
    //将docker内部端口5433映射绑定到服务器端口5434
    -v /data/docker/postgres10/data/:/var/lib/postgresql/data \
    //将docker镜像内部文件夹/var/lib/postgresql/data映射绑定到服务器文件夹/data/docker/postgres10/data/
    -v /data/docker/postgres10/buckup:/var/lib/postgresql/buckup \
    -v /data/docker/postgres10/postgresql.conf:/etc/postgresql/postgresql.conf \
    //将docker镜像内部文件/etc/postgresql/postgresql.conf映射绑定到服务器文件夹/data/docker/postgres10/postgresql.conf
    //此文件需提前创建不然docker会认为其实一个文件夹
    -d postgres:10.5 \
    -c 'port=5433' \
    //设置postgres默认端口号为5433此值可在postgresql.conf上配置
    //同理所有config_file文件【postgresql.conf】内部可配置的值都可通过docker -c 选项进行制定
    -c 'config_file=/etc/postgresql/postgresql.conf'
    //指定 postgres使用的配置文件为 /etc/postgresql/postgresql.conf 即上文映射的服务器文件/data/docker/postgres10/postgresql.conf
```
postgresql.conf文件配置
```javascript
    listen_addresses = '*'
    //监听地址，默认*为当前服务器地址
    port = 5433
    //postgres使用端口号默认5432
    max_connections = 1000
    //最大连接数
    shared_buffers = 1024MB
    max_wal_senders = 8
    wal_keep_segments = 1024
    max_worker_processes = 16

    wal_level = replica
    //置保存操作日志的具体程度级别,postgres10之后有选项三个【minimal,replica,logical】设,要设置WAL归档至少要设置为replica
    archive_mode = on
    //是否启用WAL归档 , 设置为on
    archive_command ='cp  %p /var/lib/postgresql/buckup/archive/%f > /var/lib/postgresql/buckup/archive/test.log 2>&1'
    //postgres进行系统归档时候可配置的执行脚本【将当前归档文件拷贝到/var/lib/postgresql/buckup/archive/文件夹】
    //【%p 表示将要归档的WAL文件的包含完整路径信息的文件名，用 %f 代表不包含路径信息的WAL文件的文件名】
    //[注] 该shell脚本的路劲为docker内部路径，非服务器文件路径
```
wal_level和archive_mode参数更改配置之后需要重启服务器生效
archive_command参数不必重启服务器只需要重新获取配置即可

#### 进入数据库方式
```
[root@dev ~]# docker exec -it postgres10 bash //进入镜像
root@964a3684307c:/# su postgres //换用户未postgres
postgres@964a3684307c:/$ psql -p 5433 //进入数据库指定端口为5433

进入数据库必须指定端口不然可能报错
connections on Unix domain socket "/var/run/postgresql/.s.PGSQL.5432"?
未找到pgsql默认socket连接id

```
#### 重新加载部分配置方式
```
  $postgres:  SELECT pg_reload_conf();
```

此时数据库已经开启了增量备份方式，数据库的归档文件会被拷贝到服务器文件夹/data/docker/postgres10/buckup/archive即docker的postgres镜像中的/var/lib/postgresql/buckup/archive/文件夹中

## 测试定点恢复

想要增加备份还需要进行基础备份。
pg10之后推荐使用pg_basebackup进行基础备份

```
$ pg_basebackup -Ft -Pv -Xf -U postgres -p 5433 -D /var/lib/postgresql/buckup/pg_base_buckup
```
之后就可以看到服务器文件夹/data/docker/postgres10/buckup/pg_base_buckup即镜像中的/var/lib/postgresql/buckup/pg_base_buckup文件夹中可以看到基础备份的压缩文件

由于WAL文件是写满16MB才会进行归档，测试阶段可能写入会非常少，可以在执行完 基础备份之后，手动进行一次WAL切换
```
postgres=# select pg_switch_wal();
```
此时已经手动归档一次，可在归档文件夹查看到相关文件

首先，我们创建一张测试表：
```
CREATE TABLE test_restore(
    id SERIAL PRIMARY KEY,
    ival INT NOT NULL DEFAULT 0,
    description TEXT,
    created_time TIMESTAMPTZ NOT NULL DEFAULT now()
);
```
初始化一些测试数据作为基础数据，如下所示：
```
INSERT INTO test_restore (ival) VALUES (1);
INSERT INTO test_restore (ival) VALUES (1);
INSERT INTO test_restore (ival) VALUES (1);
```
```
select pg_switch_wal();
```
下来，创建一个还原点
```
    select pg_create_restore_point('halfmy-1127');
```
接下来我们对数据做一些变更, 我们删除test_restore的所有数据：
```
delete from test_restore;
```
然后关闭postgres服务,然后将基础备份解压到postgres的data文件夹
【即postgres的基础文件夹】【即本文中的postgres安装文件夹】【即本文中的服务器文件夹/data/docker/postgres10/data/】【即本文中的docker镜像中的/var/lib/postgresql/data文件夹】
```
docker stop postgres10
由于此时的docker镜像已经关闭无法进入镜像进行文件夹操作所以使用镜像相对无服务器的映射文件夹
tar -xvf /data/docker/postgres10/buckup/pg_base_buckup.base.tar -C /data/docker/postgres10/data
```
进入/data/docker/postgres10/data文件夹创建[recovery.conf](http://postgres.cn/docs/10/archive-recovery-settings.html)文件
```
    vim recovery.conf
```
```
    restore_command = 'cp /var/lib/postgresql/buckup/archive/%f %p'
    recovery_target_name = 'halfmy-1127'
```
然后进行重启docker的postgres镜像
```
    docker restart postgres
```
对数据库进行数据查看发先已经恢复
到data文件夹中查看发现 recovery.conf已经变成recovery.done
则已经完成 数据库的增量备份以及恢复过程
如经历上述过程中发现无法解决报错等请查阅以下文档
[如何在PostgreSQL中实现增量备份](https://blog.csdn.net/qq_32884717/article/details/78512082)
[Postgresql备份与数据恢复](https://lihaoquan.me/2018/10/14/postgres-backup-wal.html)
[PostgreSQL 最佳实践 - 在线增量备份与任意时间点恢复](https://yq.aliyun.com/articles/59359)
[PostgreSQL 10.1 手册](http://postgres.cn/docs/10/)