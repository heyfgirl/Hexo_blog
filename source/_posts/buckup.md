---
title: buckup
date: 2020-06-08 13:57:01
tags:
---

#!/bin/bash
# /Applications/pgAdmin 4.app/Contents/SharedSupport/pg_dump 
# --file "/Users/wqiong/buttons.sql" 
# --host "127.0.0.1" 
# --port "52260" 
# --username "postgres" 
# --no-password --verbose --format=c 
# --blobs "auth_dev_old"

# /Applications/pgAdmin\ 4.app/Contents/SharedSupport/pg_dump \
# --file "/Users/wqiong/buttons.sql" \
# --host "dev.guaishoubobo.com" \
# --port "5432" \
# --username "postgres" \
# --no-password --verbose --format=c \
# --blobs "race_dev" "auth_dev"

# pg_dump --file "/data/www/buttons.sql" --host "dev.guaishoubobo.com" --port "5432" --username "postgres" --no-password --verbose --format=c --blobs "race_dev";

# /Applications/pgAdmin 4.app/Contents/SharedSupport/pg_dump 
# --file "/Users/wqiong/buttons.sql" 
# --host "dev.guaishoubobo.com" 
# --port "5432" 
# --username "postgres" 
# --no-password --verbose --format=c 
# --blobs --table "public.uniques" "auth_dev"


# dir="/data/www/DatabasesBackup/"
# echo "cd ${dir}"
# cd $dir

# echo 'DATE=`date +%Y%m%d-%H%M`
# BACK_DATA=xxapp-data-${DATE}.out  # 这里设置备份文件的名字, 加入日期是为了防止重复
# docker exec pg-db pg_dumpall -U postgres > ${BACK_DATA} # pg-db 是数据库的 docker 名称'

# docker exec pg-db pg_dumpall -U postgres > ${BACK_DATA}

# docker exec postgres pg_dump \
# --file "buttons.sql" \
# --host "dev.guaishoubobo.com" \
# --port "5432" \
# --username "postgres" \
# --no-password --verbose --format=c \
# --blobs "race_dev" -U "postgres" -W
# O8MxsIhH

# docker exec postgres pg_dump -U "postgres"  --blobs "race_dev"  --no-password --verbose --format=c --file "buttons.sql"
# /Applications/pgAdmin\ 4.app/Contents/SharedSupport/pg_dump  --file /root/gresql.sql\
#  --username "postgres" \
#  --no-password  --verbose --format=c \
#  --blobs "race3" 
#  -U "postgres" -W

# //压缩模式二进制模式
# docker exec postgres pg_dump --username "postgres"  --no-password  --verbose --format=c  --blobs "race_dev" -f /data/backup.sql
# docker exec postgres pg_restore --username "postgres" --no-password -c --verbose -d "test"  /data/backup.sql

# # //非压缩模式 ，pgsql模式
# docker exec postgres pg_dump --username "postgres"  --no-password  --verbose   --blobs "race_dev" -f /data/backup.sql
# docker exec postgres psql --username "postgres"  -d test3 -f /data/backup.sql

# docker exec postgres pg_dump -h db -f /shared/backup.sql
#    for(var i in maps){
#         console.log(222)
# 	    addMarker(maps[i]);
# 	}     

# dir="var/lib/postgresql/buckup"
# echo "cd ${dir}"
# cd $dir
# 进入docker bash
# echo "docker exec -it postgres bash"
# docker exec -it postgres bash
# 将用户置为 postgres
# echo "su postgres"
# su postgres
# 进入sql命令行
# echo "psql"
# psql
# 展示所有数据库列表
# \\list

