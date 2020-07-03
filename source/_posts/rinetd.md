---
title: rinetd
date: 2020-06-16 16:40:15
tags:
---

wget http://www.rinetd.com/download/rinetd.tar.gz
tar xvf rinetd.tar.gz
cd rinetd
make && make install
echo "0.0.0.0 8080 www.baidu.com 80" > /etc/rinetd.conf
rinetd -c /etc/rinetd.conf

bindaddress bindport connectaddress connectport
源地址       源端口     目的地址      目的端口


日志需要在rinetd.conf文件中加上
logfile /var/log/rinetd.log

关闭killall rinetd
启动 rinetd -c /etc/rinetd.conf