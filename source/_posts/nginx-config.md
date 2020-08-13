---
title: nginx配置介绍
date: 2020-04-24 15:25:33
tags:
---

1.静态文件配置
2.服务器代理配置
3.反向代理配置
4.socket升级协议配置
5.日志分组配置
```
server {
    listen       443 ssl http2;
    listen       [::]:443 ssl http2;
    server_name  yun.x.com;

    ssl_certificate "/etc/nginx/cert/_.x.com.pem";
    ssl_certificate_key "/etc/nginx/cert/_.x.com.key";
    ssl_session_cache shared:SSL:1m;
    ssl_session_timeout  10m;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    add_header Access-Control-Allow-Credentials true;

    include /etc/nginx/default.d/*.conf;
    #从系统时间中正则匹配出年月日
    #if ($time_iso8601 ~ "^(\d{4})-(\d{2})-(\d{2})") {
    #   set $date $1$2$3;
    #}
    # 日期记录日志
    #access_log  /var/log/nginx/$date-access.log;

    if ($time_iso8601 ~ "^(\d{4})-(\d{2})-(\d{2})T(\d{2}):(\d{2}):(\d{2})") {
        #set $date $1$2$3;
        set $year $1;
        set $month $2;
        set $day $3;
        set $hour $4;
        set $minutes $5;
    }
    #错误日志记录
    location /error.gif {
        default_type image/gif;
        #此请求不缓存
        add_header Expires "Fri, 01 Jan 1980 00:00:00 GMT";
        add_header Pragma "no-cache";
        add_header Cache-Control "no-cache, max-age=0, must-revalidate";
        #返回一个1×1的空gif图片
        empty_gif;
        #if ($time_iso8601 ~ "^(\d{4})-(\d{2})-(\d{2})") {
        #if ($time_iso8601 ~ "^(\d{4})-(\d{2})-(\d{2})T(\d{2}):(\d{2}):(\d{2})") {
        #   #set $date $1$2$3;
        #   set $year $1;
        #   set $month $2;
        #   set $day $3;
        #   set $hour $4;
        #   set $minutes $5;
        #}
        # 日期记录日志
        access_log  /var/log/nginx/$year-$month-$day-$hour-error.log main;
    }
    location /log.gif {
        #真实gif图片
        log_subrequest on;
        access_log  /var/log/nginx/gif_access.log  main;
        #此请求不缓存
        default_type image/gif;
        add_header Expires "Fri, 01 Jan 1980 00:00:00 GMT";
        add_header Pragma "no-cache";
        add_header Cache-Control "no-cache, max-age=0, must-revalidate";
        #返回一个1×1的空gif图片
        empty_gif;
        access_log  /var/log/nginx/$year-$month-$day-$hour-access.log main;
    }
```