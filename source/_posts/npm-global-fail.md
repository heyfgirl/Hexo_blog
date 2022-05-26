---
title: npm全局安装的包命令不存在
date: 2019-01-16 16:38:28
tags: npm安装
---
//查看全局安装包位置
npm config get prefix
修改npm命令环境前置配置
npm config set prefix "nodejs的全局安装目录"
安装需要的全局包

在机器上安装 zeromq@6.0.0-beta.6 出现权限问题需要 --unsafe-perm=true ，出现无法下载资源包问题，需要 --registry=https://registry.npmmirror.com
npm install zeromq@6.0.0-beta.6 --build-from-source --unsafe-perm=true --registry=https://registry.npmmirror.com