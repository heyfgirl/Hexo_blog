---
title: docker简单命令
date: 2019-12-11 11:29:22
tags:
- docker
- 命令
---
```
    docker使用内部不存在的命令需要进行安装，
    docker内部默认ubuntu环境，所以需要
    docker内部使用  ping命令需要使用apt-get安装

    docker system dh 查看docker系统内部存储

    docker system prune //清理docker来自动清理空间
    该指令默认会清除所有如下资源：
    已停止的容器（container）
    未被任何容器所使用的卷（volume）
    未被任何容器所关联的网络（network）
    所有悬空镜像（image）。
    该指令默认只会清除悬空镜像，未被使用的镜像不会被删除。
    添加 -a 或 --all 参数后，可以一并清除所有未使用的镜像和悬空镜像。
```