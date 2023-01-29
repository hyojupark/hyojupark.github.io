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

Kubernetes에서 image 혹은 설정값이 바뀌어 재시작이 필요한 경우가 있는데, 이때 아래 명령어로 재시작이 가능합니다.


 


  
**Deployment**



```
kubectl rollout restart deployment {Deployment Name}
```

 


**StatefulSet**



```
kubectl rollout restart statefulset {StatefulSet Name}
```

 

