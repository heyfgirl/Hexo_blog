---
title: Iptables 安装与设置
date: 2017-11-09 16:30:49
updated: 2017-11-09 16:30:49
tags: 
- linux 
- 防火墙
---

## 安装
### 先检查是否安装了iptables
``` bash
  $ service iptables status
```
### 安装iptables
``` 
  $ yum install -y iptables 
```
### 升级iptables
```
  $ yum update iptables
```
### 安装iptables-services
```
  $ yum install iptables-services
```
---
  如果系统本身安装了firewalld服务，需要关闭该服务
```
  $ 停止firewalld服务
  systemctl stop firewalld
  $ 禁用firewalld服务
  systemctl mask firewalld
```
---
## 规则设置
### 查看现有规则
```
  $ iptables -L -n
```
### 查看现有规则
```
  $ iptables -L -n
```
### 查看现有规则
```
  $ iptables -L -n
```
### 允许来自于lo接口的数据包(本地访问)
```
  $ iptables -A INPUT -i lo -j ACCEPT
```
### 开放XXXX端口
```
  $ iptables -A INPUT -p tcp --dport XXXX -j ACCEPT
```
### 允许ping
```
  $ iptables -A INPUT -p icmp --icmp-type 8 -j ACCEPT
```
### 要封停一个IP，使用下面这条命令：
```
  $ iptables -I INPUT -s ***.***.***.*** -j DROP
```
### 要解封一个IP，使用下面这条命令:
```
  $ iptables -D INPUT -s ***.***.***.*** -j DROP
```
### 其他入站一律丢弃
```
  $ iptables -P INPUT DROP
```
### 所有出站一律绿灯
```
  $ iptables -P OUTPUT ACCEPT
```
### 所有转发一律丢弃
```
  $ iptables -P FORWARD DROP
```
### 如果要添加内网ip信任（接受其所有TCP请求）
```
  $ iptables -A INPUT -p tcp -s 45.96.174.68 -j ACCEPT
```
### 访问外网
```
  $ iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
```
##  防止DDOS攻击
### 屏蔽 SYN_RECV 的连接
```
  $ iptables -A FORWARD -p tcp -m tcp --tcp-flags FIN,SYN,RST,ACK SYN -m limit --limit 1/sec -j ACCEPT
```
### 限制IP碎片，每秒钟只允许100个碎片，用来防止DoS***
```
  $ iptables -A FORWARD -f -m limit --limit 100/sec --limit-burst 100 -j ACCEPT
```
### 限制ping包每秒一个，10个后重新开始
```
  $ iptables -A FORWARD -p icmp -m limit --limit 1/sec --limit-burst 10 -j ACCEPT
```
###限制ICMP包回应请求每秒一个
```
  $ iptables -A FORWARD -p icmp -m icmp --icmp-type 8 -m limit --limit 1/sec -j ACCEPT
```
## 删除规则
### 删除INPUT规则第三行例子
``` bash
  $ iptables -D INPUT 3
```
