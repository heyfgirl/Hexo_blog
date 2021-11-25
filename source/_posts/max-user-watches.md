---
title: max_user_watches
date: 2021-11-25 16:53:38
tags:
---

使用npm 或者 nodemon 启动项目发现 报错 System limit for number of file watchers reached
使用node不会报错

经常会报这个错误，出错原因大致意思是文件监控数量超过了系统限制。其实就是打开的文件过多导致的，不管是什么文件，只要有进程在，就是一个file watchers，临时解决办法就是关掉几个进程，再运行npm start，就好了，但是等到系统开启的进程一多起来，再次运行又有可能出现同样的错误，为了永久解决这个问题，必须修改系统参数。
    系统默认的参数可以在/proc/sys/fs/inotify/max_user_watches变量中看到，默认是8192。

    修改/etc/sysctl.conf文件，在末尾增加一行记录：

```
fs.inotify.max_user_watches=524288
```
sudo sysctl -p