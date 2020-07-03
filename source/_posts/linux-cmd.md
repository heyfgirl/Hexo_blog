---
title: linux简单命令
date: 2019-12-11 11:44:37
tags:
- linux
- 命令
---
//查找大于1G的文件
find /opt/ -size +1G 2>/dev/null

一次服务器磁盘占满的数据排查

首先使用df -h发现磁盘占满99%

看到是/var/lib/docker/containers/**里面占用了 超大的数据

怀疑是docker的镜像日志造成删除镜像日志
docker system prune
发现删除之后仍然占存很大
怀疑是进程未释放 删除大文件进程、【https://www.cnblogs.com/healthinfo/p/12402139.html】
通过 命令 lsof -n | grep deleted查找哪些删除进程在未被注销
```
kill -9 进程号
发现依然占满
 但是任凭各个文件目录下  du -sh * 也找不到大文件，于是立马虚心求教大拿，大拿一个命令就解决了，什么命令这么nb+神奇呢?

lsof -n | grep deleted  查看到占用的进程

kill -9 进程号

再df -h就没问题了，以下是公司大拿给出来的解释:

当我们使用rm在linux上删除了大文件，但是如果有进程打开了这个大文件，却没有关闭这个文件的句柄，那么linux内核还是不会释放这个文件的磁盘空间
```
最后到根目录 / 下执行du -sh  *
发现各个目录 大小，然后发现 root目录下  占用很大的数据，
进入root下  使用 du -sh * 却并未有大文件   。找不到 文件在哪【https://blog.csdn.net/bowei026/article/details/87696331】
排除解决，换用命令：
du -sh .[!.]*


最后发现 原来是隐藏文件中存在超大文件数据

删除文件解决


到根目录 / 中使用  du -sh * 
发现