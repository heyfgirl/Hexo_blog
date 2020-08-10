---
title: go get 找不到 google.golang.org/protobuf 解决办法
date: 2019-12-30 14:52:27
tags:
-   go
-   git
---
使用go get 安装由于被墙找不到google.golang.org/protobuf/proto

首先找到源码位置https://cloud.google.com/go/google.golang.org

将其mark成一个git项目到码云上

已经有网友弄过
地址：https://robinqiwei.coding.net/p/googleprotobuf/git

然后手动安装 ，到本地安装库下 $GOPATH/src/google.golang.org/protobuf
```
mkdir -p $GOPATH/src/google.golang.org
git clone https://e.coding.net/robinqiwei/googleprotobuf.git $GOPATH/src/google.golang.org/protobuf
```