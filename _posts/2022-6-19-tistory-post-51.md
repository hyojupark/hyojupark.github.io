---
title: Kubernetes Node SchedulingDisabled 상태 해결
toc: true
categories:
  - Kubernetes
tags:
  - k8s
  - kubernetes
  - schedulingdisabled
  - uncordon
---

Kubernetes에서 node가 scheduling을 하지 못하는 경우 아래와 같이 uncordon으로 해결 가능합니다.


 



```
$ kubectl get nodes
NAME	STATUS				ROLES	AGE	VERSION
node1	Ready,SchedulingDisabled	master	220m	v1.19.15

$ kubectl uncordon node1
node1 uncordoned

$ kubectl get nodes
NAME	STATUS	ROLES	AGE	VERSION
node1	Ready	master	220m	v1.19.15
```

 

