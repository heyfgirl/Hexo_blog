---
title: postgres日志丢失导致无法重启
date: 2019-12-11 11:46:08
tags:
---
Postgresql清理WAL日志
https://blog.csdn.net/dazuiba008/article/details/100659749

docker logs -f -t --tail 100 //检查日志

PostgreSQL错误PANIC：找不到有效的检查点记录
docker 运行postgresql出现 could not locate a valid checkpoint record
the database system is starting up// wal文件丢失导致无法重启
