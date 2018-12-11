---
title: Hexo发布到Github
date: 2017-11-19 10:23:42
updated: 2017-11-19 10:23:42
tags: 
- Hexo 
- Github
---
首先在配置文件上填写：

```
deploy:
  type: git                                                      # 设置发布类型，如git     
  repository: git@github.com:heyfgirl/heyfgirl.github.io.git   # 设置repository对应的链接
  branch: master                                                 # 设置提交到的分支
  message: Site updated at {{ now("YYYY-MM-DD HH:mm:ss") }}      # 设置我们提交的信息
```
执行发布代码命令：

```
hexo deploy
```
提示 error deployer not found:git（说明没有发布工具）

然后拉去发布工具

```
npm install hexo-deployer-git --save 
```

测试链接Github

```
ssh -T git@github.com
```
Permission denied(publickey)
提示缺少公钥

本地生成公钥
```
ssh-keygen -t rsa -C "xxxx@xxx.com"
```

成功的话会在 ~/下生成 .ssh文件夹，进去，打开 id_rsa.pub，复制里面的key即可。

在github生成密钥，将本地公钥拷贝进去
再先进行发布行为OK