---
title: 线上数据库更改结构工具
date: 2021-11-19 14:55:03
tags: mysql
---

#!/bin/bash
# 1 首先安装 percona-toolkit_3.3.1
--- wget https://downloads.percona.com/downloads/percona-toolkit/3.3.1/binary/debian/xenial/x86_64/percona-toolkit_3.3.1-1.xenial_amd64.deb
解压工具包 dpkg -X ./percona-toolkit_3.3.1-1.xenial_amd64.deb percona
工具使用位置为 /percona/usr/bin/pt-online-schema-change
# 2  按照文档提示使用工具会提示缺少依赖包
安装依赖包 
sudo apt-get install perl-DBD-MySQL
E: dpkg 被中断，您必须手工运行 ‘sudo dpkg --configure -a’ 解决此问题。
# 执行脚本

```
# 数据库配置
table=bucket_
cnn_host='10.0.0.1'
cnn_user='root'
cnn_pwd='xxx'
cnn_db='mydb'
# 工具地址配置
pttoolbash=/usr/yupoo/app/percona/usr/bin/pt-online-schema-change
<!-- 例子 向 bucket_ 表中插入account_id以及自增id -->
for conment in "ADD COLUMN id bigint(20) NOT NULL unique key AUTO_INCREMENT" "ADD COLUMN account_id bigint(20) NOT NULL  DEFAULT '0' COMMENT '用户id'"  "ADD INDEX bucket_account_id_IDX(account_id,bucket_id)"
do
    alter_conment=${conment}
    echo 开始处理表  "$table" 执行脚本  "$alter_conment"
    $pttoolbash --charset=utf8 --no-version-check --user=${cnn_user} --password=${cnn_pwd} --host=${cnn_host}  --port=3306 D=${cnn_db},t=$table --alter "${alter_conment}" --execute
    echo ${var}
done
```

