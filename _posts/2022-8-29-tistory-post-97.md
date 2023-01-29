---
title: Kubernetes pod와 local간 폴더 복사
toc: true
categories:
  - Kubernetes
tags:
  - copy
  - directory
  - File
  - k8s
  - kubernetes
  - POD
---

### **local to pod**



```
kubectl cp /copy/local_dir {namespace}/{pod-name}:/copy/pod_dir
```

 


### **pod to local**



```
kubectl cp {namespace}/{pod-name}:/copy/pod_dir /copy/local_dir
```

 

