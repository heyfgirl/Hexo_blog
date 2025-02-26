---
title: go 内存泄漏排查
date: 2025-02-25 11:47:27
tags:
---

### 发现问题
 通过k8s容器查看到内存使用率不断攀升
 ![alt text](/images/go-memory-leak/image1.png)

### 查找问题

1. 在 服务启动的main函数中加入 pprof 

![alt text](/images//go-memory-leak/image2.png)

2. dockerfile 中安装检查所需工具 curl 以及 ss 命令报包 iproute2 等


3. 获取 pprof 内存图，

`
go tool pprof -seconds=10 -http=:9999 http://localhost:6060/debug/pprof/heap
`

存在网络隔离问题，不能直接从开发机访问测试机、线上机器，或者测试机、线上机器没有安装go，那也可以先在容器中安装 curl 工具，然后将信息下载到本地查看

例如
```
// 该命令主要查看内存信息，可查看具体哪些地方使用内存过高
curl http://localhost:6060/debug/pprof/heap?seconds=600 > heap.out 
<!-- 该命令主要查看 goroutine 信息 go 中内存泄漏大概率由 goroutine 未释放未被垃圾回收引起。首先应该查看 goroutine 个数是否异常 -->
curl http://localhost:6060/debug/pprof/goroutine?debug=1 > goroutine.out 
```
将 pprof 信息输出到文件  heap.out  然后将文件下载到本地执行

```
go tool pprof -http=':8081'           \
   -diff_base xxx.out \  [对照的文件，非必需，可多次进行对比查看具体问题]
   heap.out  [查看的文件]

```

### 分析问题

1. 通过图形![alt text](/images/go-memory-leak/image4.png)分析发现可能跟 es 客户端有关

2. 进入实例中通过命令查看当前连接数
```
time netstat -antlp | grep EST | wc -l
```
发现有大量连接

3. 通过 ss -s 命令查看具体是哪些连接发现全是 estab 占用

查看代码问题，发现原来是 es 客户端忘记对 resp.Body.Close()



## 其他方式

在通过 pprof 图发现 es 客户端可能出现问题时，使用 cursor 将使用到 es 客户端的文件加入 compose。直接询问 api 该文件中的 es 客户端使用哪里有问题让 ai 帮忙查找。
