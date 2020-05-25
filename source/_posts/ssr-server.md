---
title: ssr服务器安装
date: 2020-05-20 09:28:37
tags:
---

Python 版 SS 安装，SS容易被封 ：

Debian / Ubuntu:
```
    apt-get install python-pip
    pip install shadowsocks
```
CentOS（7以上版本）:
```
    yum install python-setuptools && easy_install pip
    pip install shadowsocks
```
运行：
```
    ssserver -p 443 -k password -m aes-256-cfb
```
后台运行:
```
    sudo ssserver -p 443 -k password -m aes-256-cfb –user nobody -d start
```
停止:
```
    sudo ssserver -d stop
```
查看 log:
```
    sudo less /var/log/shadowsocks.log
```
说明
```
    -p 端口号

    -k 密码

    -m 加密方式  （rc4-md5，salsa20，chacha20，aes-256-cfb，aes-102-cfb，aes-128-cfb）

    -h 查看命令帮助
```
配置文件创建：
```
    路径 /etc/shadowsocks.json
    {
        “server”:”my_server_ip”,
        “server_port”:8388,
        “local_address”: “127.0.0.1”,
        “local_port”:1080,
        “password”:”mypassword”,
        “timeout”:300,
        “method”:”aes-256-cfb”,
        “fast_open”: false
    }
```
运行:
```
    ssserver -c /etc/shadowsocks.json
```
后台运行:
```
    ssserver -c /etc/shadowsocks.json -d start
    ssserver -c /etc/shadowsocks.json -d stop
```
下面就是对于ssr-server端的一键安装版本，直接将下面的代码复制到你的服务器终端中执行即可（为了防止丢失地址为腾讯云COS链接）：
```
    wget –no-check-certificate -O shadowsocks-all.sh http://index-1251746107.file.myqcloud.com/shadowsocks-all.sh

    chmod +x shadowsocks-all.sh

    ./shadowsocks-all.sh
```

之后我们可以配置一个通过TCP拥塞控制来进行加速的服务：
```
    wget –no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh

    chmod +x bbr.sh

    ./bbr.sh
```
