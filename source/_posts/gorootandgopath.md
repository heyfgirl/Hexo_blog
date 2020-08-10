---
title: GoRoot 和 GoPath
date: 2019-12-22 14:56:50
tags:
-   GO
-   GoPath
-   GoRoot
---
GO在开发过程中需要在go的patn环境变量中开发。

GOROOT就是go的安装路径。
要执行go命令和go工具, 就要配置go的可执行文件的路径:
操作如下:
在~/.bash_profile中配置如下:
export $PATH:$GOROOT/bin

GOPATH就是你的工作目录。
go install/go get和 go的工具等会用到GOPATH环境变量.
需要把GOPATH中的可执行目录也配置到环境变量中
在~/bash_profile中配置,
export $PATH:$GOPATH/bin
