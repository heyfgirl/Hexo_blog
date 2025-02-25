---
title: kubectl
date: 2024-07-09 10:22:05
tags:
---

kubectl 相关操作
当k8s中有进程挂了，通过 docker logs 查看不到具体原因，
可通过 
`kubectl get pod -n <<namespace>> `
命令查看当前 pod 状态
是否和镜像有关
如需重启可通过
`kubectl rollout restart deployment pod_name -n holdcloud`
命令重启，或通过客户端
`$sed -i "s/:dev/:$COMMIT/g" k8s.yaml$ ./kubectl --server=10.0.1.1:8080 apply -f k8s.yaml`