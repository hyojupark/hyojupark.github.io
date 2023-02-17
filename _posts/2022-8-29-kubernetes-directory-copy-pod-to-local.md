---
title: Kubernetes pod와 local간 폴더 복사
toc: true
categories:
  - Kubernetes
tags:
  - copy
  - directory
  - File
  - kubernetes
  - Pod
---

### **local to pod**

```bash
kubectl cp /copy/local_dir {namespace}/{pod-name}:/copy/pod_dir
```

<br>

### **pod to local**

```bash
kubectl cp {namespace}/{pod-name}:/copy/pod_dir /copy/local_dir
```
