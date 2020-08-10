---
title: docker_kafka
date: 2020-03-23 10:21:21
tags:
---

docker pull zookeeper
docker run -d --name zookeeper -p 2181:2181 -v /data/zookeeper/localtime:/etc/localtime wurstmeister/zookeeper
docker run --privileged=true -d --name zookeeper --publish 2181:2181 -v /data/zookeeper/localtime:/etc/localtime -d zookeeper:latest



/var/lib/docker/overlay2/8d703e1973c54e951f836471d40fab8741bf2aabb88e717d7c244bb2874abe09/merged/etc/localtime


docker pull wurstmeister/kafka

docker run -d --name kafka -p 9092:9092 -e KAFKA_BROKER_ID=0 -e KAFKA_ZOOKEEPER_CONNECT=172.19.112.8:2181/kafka -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://172.19.112.8:9092 -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 -v /data/kafka/localtime:/etc/localtime wurstmeister/kafka


https://www.jianshu.com/p/e8c29cba9fae

--zookeeper 172.19.112.8:2181
nginx-access-kafkaceshi