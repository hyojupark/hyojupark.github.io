---
title: Kubernetes Controller (Deployment, ReplicaSet, DaemonSet, StatefulSet)
toc: true
categories:
  - Kubernetes
tags:
  - DaemonSet
  - deployment
  - kubernetes
  - ReplicaSet
  - statefulset
---

| **Controller** | **설명** |
| --- | --- |
| Deployment | - 내부에서 Replicaset으로 pod를 관리하는 컨트롤러- Rollback, 무중단 배포 가능 |
| ReplicaSet | - 동일한 pod를 n개로 복제해서 관리하는 컨트롤러- Load balance, Scale out 가능 |
| DaemonSet | - 노드별로 pod를 하나씩만 관리하는 컨트롤러- 모니터링, 로그 수집 등에서 사용 |
| StatefulSet | - 상태가 있는 pod를 관리하는 컨트롤러- 컨테이너가 종료돼도 데이터 보존- 데이터베이스 등에서 사용 |


 

