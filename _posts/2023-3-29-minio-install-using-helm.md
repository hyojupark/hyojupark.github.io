---
title: MinIO 설치(with NFS, Kubernetes, Helm)
categories:
  - MinIO
tags:
  - MinIO
  - NFS
  - Kubernetes
  - Helm
published: false
---


**MinIO**는 Amazon S3와 호환되는 API를 제공하는 오픈소스 오브젝트 스토리지입니다. 일반적으로 S3와 같은 오브젝트 스토리지가 필요할 때 대용으로 가장 많이 사용되는 오픈소스입니다. 영어 그대로 `민아이오`(Minimal Object Storage를 의미하기 때문)라고 읽기도 하고 `민아이오`(MinIO 대소문자를 구분했을 때)라고 읽기도 합니다.

설치 방법은 크게 여러 대의 노드에 분산 저장하는 `distributed mode`와 단일 노드에 저장하는 `standalone mode`로 나뉩니다. 아래 설치는 `standalone mode`에서 NFS에 연결하여 안정성을 높여 설치하는 방법입니다.

### NFS 연결
#### Storage Class 생성
우선 NFS Storage Class를 생성해야합니다. 여기서는 `NFS Client Provisoner`를 이용해서 Dynamic Provisioning하도록 하겠습니다.

```bash
$ helm repo add nfs-subdir-external-provisioner https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner/
$ helm repo update
$ helm install nfs-subdir-external-provisioner nfs-subdir-external-provisioner/nfs-subdir-external-provisioner \
  --set nfs.server=1.2.3.4 \
  --set nfs.path=/minio_storage \
```

#### PVC(Persistent Volume Claim) 생성
##### minio-pvc.yaml
```yaml
apiVersion: v1
kind : PersistentVolumeClaim
metadata:
  namespace: minio-system
  name: minio-dynamic-pvc
spec:
  storageClassName: nfs-client
  accessModes:
    - ReadWriteMany
  resources:
     requests:
       storage: 1000Gi
```

```bash
$ kubectl apply -f minio-pvc.yaml
```

<br>

### MinIO 설치
#### Helm Repository 설치


#### values.yaml 작성



### Reference
- <https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner>
- <https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner/blob/master/charts/nfs-subdir-external-provisioner/README.md>
- 