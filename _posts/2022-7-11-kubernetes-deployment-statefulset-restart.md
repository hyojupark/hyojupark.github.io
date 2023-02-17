---
title: Kubernetes Deployment, StatefulSet restart
toc: true
categories:
  - Kubernetes
tags:
  - deployment
  - kubernetes
  - restart
  - Rollout
  - statefulset
---

Kubernetes에서 image 혹은 설정값이 바뀌어 **재시작**이 필요한 경우가 있는데, 이때 아래 명령어로 **재시작**이 가능합니다.

<br>

**Deployment**

```bash
kubectl rollout restart deployment {Deployment Name}
```

**StatefulSet**

```bash
kubectl rollout restart statefulset {StatefulSet Name}
```
