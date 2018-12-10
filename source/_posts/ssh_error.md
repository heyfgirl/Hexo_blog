---
title: SSH无法链接linux
date: 2017-09-20 15:34:57
tags: 
- linux
categories:
- [linux]
- ssh
---
## SSH无法链接Linux

---
  某次误操作导致linux虚拟机服务器链接不上，报错/var/empty/sshd must be owned by root and not group or world-writable.
  发现是权限问题
---
###  问题查找
```
  $ rpm -V    检查到ssh软件包正常，但是某个目录属性错误
  经查看发现这个目录的属主不是root，所以启动ssh报错
  修改为root属主，启动成功
```
### 问题解决
```
  chown -R root:root /var/empty/sshd
  chmod 744 /var/empty/sshd
  service sshd restart
```
### 问题总结
*** 自己在处理文件权限的时候失误操作，命令中使用了 /xxx 导致将所有文件所有权更改导致 ***