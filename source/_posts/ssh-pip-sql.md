---
title: 通过管道链接远程服务器上面的数据库
date: 2019-01-28 10:45:55
tags:
- linux
- 服务器
- 管道
---

服务器的数据库在linux上面不方便管理，又无开启数据库远程访问权限。只有ssh权限的情况下。本地通过ssh管道链接该数据库。。

首先保证服务器数据库开启，
本地链接服务器

ssh -L localhost:8080:www.baidu.com:80 root@12.54.654.255 -p 65422
ssh -L 【本地客户端IP地址】:【本地客户端端口】:【需要访问的地址】:【需要访问的服务端口】 root@115.231.100.92 -p 65422
当前客户端想访问百度地址，但是由于某些原因无法访问，但是可以访问到服务器12.54.654.255，而12.54.654.255可以访问百度，此时需要使用ssh做个管道连接。将当前客户端访问的8080端口映射为。12.54.654.255服务器上访问的百度地址。

ssh -L 20202:localhost:5432 root@12.54.654.255 -p 22
通过ssh -L 管道链接 ：将地址为12.54.654.25服务器上面的 ，本地（localhost）服务端口5432（postgresql服务）映射到，客户端的本地端口20202.

至此，客户端的本地端口20202即为服务端的 5432端口服务。

20202：可配置的将服务端数据库端口映射过来的本地端口。
localhost：在服务器上面的本地服务。
5432：服务器上面的数据库服务端口。
root：服务器的ssh账号。
12.54.654.25：服务器地址。
22：服务器的ssh端口。

