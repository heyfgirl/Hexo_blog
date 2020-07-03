---
title: docker_logstash
date: 2020-06-23 10:21:47
tags:
---

    docker pull logstash:7.8.0

docker run -d --restart=always --log-driver json-file --log-opt max-size=100m --log-opt max-file=2 -p 5044:5044 --name logstash -v /data/logstash/logstash.yml:/usr/share/logstash/config/logstash.yml -v /var/log/nginx/gif_access.log:/var/log/gif_access.log -v /data/logstash/conf.d/:/usr/share/logstash/conf.d/ logstash:7.8.0


注意：Kafka 从 2.2 版本开始将 kafka-topic.sh 脚本中的 −−zookeeper 参数标注为 “过时”，推荐使用 −−bootstrap-server 参数。若读者依旧使用的是 2.1 及以下版本，请将下述的 --bootstrap-server 参数及其值手动替换为 --zookeeper zk1:2181,zk2:2181,zk:2181。一定要注意两者参数值所指向的集群地址是不同的。


————————————————
版权声明：本文为CSDN博主「Ernest.Wu」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_29116427/java/article/details/80206125