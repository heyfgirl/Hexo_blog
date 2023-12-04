---
title: k8s_del_pod
date: 2023-12-04 11:28:21
tags:
---


1、先删除pod2、再删除对应的deployment否则只是删除pod是不管用的，还会看到pod，因为deployment.yaml文件中定义了副本数量


实例如下：

删除pod

[root@test2 ~]# kubectl get pod -n <<namespace>>
NAME                        READY     STATUS    RESTARTS   AGE
jenkins2-8698b5449c-grbdm   1/1       Running   0          8s
[root@test2 ~]# kubectl delete pod jenkins2-8698b5449c-grbdm -n jenkins
pod "jenkins2-8698b5449c-grbdm" deleted

查看pod仍然存储

[root@test2 ~]# kubectl get pod -n <<namespace>>
NAME                        READY     STATUS    RESTARTS   AGE
jenkins2-8698b5449c-dbqqb   1/1       Running   0          8s
[root@test2 ~]# 

删除deployment

[root@test2 ~]# kubectl get deployment -n <<namespace>>
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
jenkins2   1         1         1            1           17h
[root@test2 ~]# kubectl delete deployment <<deployment>> -n <<namespace>>

再次查看pod消失

deployment.extensions "jenkins2" deleted
[root@test2 ~]# kubectl get deployment -n jenkins
No resources found.
[root@test2 ~]# 
[root@test2 ~]# kubectl get pod -n jenkins
No resources found.