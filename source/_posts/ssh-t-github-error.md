---
title: SSH连接主机Github
date: 2019-11-27 11:09:21
tags:
- ssh
- git
---

使用 ssh-keygen -t rsa -C "xxxx@xxx.com" 创建github秘钥文件
在github上创建了keys后使用 
ssh -T git@github.com
测试连接报错 Permission denied(publickey)。
此时无法知道 报错具体原因，需要使用
ssh -T -v git@github.com
命令查看具体错误原因

发现
debug1: Will attempt key: /Users/wqiong/.ssh/id_rsa 
debug1: Will attempt key: /Users/wqiong/.ssh/id_dsa 
debug1: Will attempt key: /Users/wqiong/.ssh/id_ecdsa 
debug1: Will attempt key: /Users/wqiong/.ssh/id_ed25519 
debug1: Will attempt key: /Users/wqiong/.ssh/id_xmss 

git一直在查找id_rsa,id_dsa等文件，
而我在创建ssh秘钥文件时候创建了文件名，并未被识别，所以将之前创建的秘钥文件名更改为id_rsa,以及id_rsa.pub即可。
再次使用
ssh -T git@github.com
发现通过