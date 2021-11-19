---
title: nginx_gateway
date: 2021-08-06 18:30:29
tags:
---



nginx 网关代理 访问 k8s

location / {
    <!-- 网关 -->
    proxy_pass http://k8s-ingress.xxx.com:8089; 
    <!-- 域名带来头 -->
    proxy_set_header Host p.x.halfmy.com;
}