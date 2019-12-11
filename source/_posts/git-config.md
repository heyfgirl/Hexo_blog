---
title: git代理
date: 2019-12-11 11:19:57
tags:
- git
- 代理
---
使用git代理配置git调用时候的账号邮箱，或服务代理等
```
//例子
git config --global https.proxy https://proxyuser:proxypassword@ip/域名:port

也可以使用在用户目录下的.gitconfig文件（如果不存在则创建）中写入
[http]
        proxy = https://proxyuser:proxypassword@ip/域名:port //代理配置
[credential]
        helper = store //凭证配置【这个配置表明将会在第一次使用用户密码之后记住该密码，即下次不用在输入】
        如果你的凭证位置没有指定，则去用户目录下查看，发现多了一个.git_credentials文件，打开文件可以看到里面以明文存放你的用户名和密码
[user]
        name = robot //用户配置
        email = robot@mland.club
[push]
        default = simple
```