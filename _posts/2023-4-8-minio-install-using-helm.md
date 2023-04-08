---
title: MinIO 설치(with NFS, Kubernetes, Helm)
categories:
  - MinIO
tags:
  - MinIO
  - NFS
  - Kubernetes
  - Helm
# published: false
---


**MinIO**는 Amazon S3와 호환되는 API를 제공하는 **오픈소스 오브젝트 스토리지**입니다. 일반적으로 S3와 같은 오브젝트 스토리지가 필요할 때 대용으로 가장 많이 사용되는 오픈소스입니다. 영어 그대로 `미니오`(Minimal Object Storage를 의미하기 때문)라고 읽기도 하고 `민아이오`(MinIO 대소문자를 구분했을 때)라고 읽기도 합니다.

설치 방법은 크게 여러 대의 노드에 분산 저장하는 `distributed mode`와 단일 노드에 저장하는 `standalone mode`로 나뉩니다. 아래 설치는 `standalone mode`에서 NFS에 연결하여 안정성을 높여 설치하는 방법입니다.

## NFS 연결
### Storage Class 생성
우선 NFS Storage Class를 생성해야합니다. 여기서는 `NFS Client Provisoner`를 이용해서 `Dynamic Provisioning`을 하도록 하겠습니다.

```bash
$ helm repo add nfs-subdir-external-provisioner https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner/
$ helm repo update
$ helm install nfs-subdir-external-provisioner nfs-subdir-external-provisioner/nfs-subdir-external-provisioner \
  --set nfs.server=1.2.3.4 \
  --set nfs.path=/minio_storage \
```

### PVC(Persistent Volume Claim) 생성
생성한 NFS Storage Class를 이용해서 PVC를 만들어줍니다.
#### minio-pvc.yaml
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
$ kubectl apply -f minio-dynamic-pvc.yaml
```

<br>

## MinIO 설치
### Helm Chart 추가
MinIO 설치는 ArtifactHub에 있는 MinIO Official Chart를 사용하겠습니다. (<https://artifacthub.io/packages/helm/minio-official/minio>)

```bash
$ helm repo add minio https://charts.min.io/
```

### values.yaml 작성
`standalone mode`에 `NFS`를 연결한 PVC를 붙이는 설정을 작성하겠습니다. 추가로 root 계정 정보와 NodePort 설정을 같이 작성하겠습니다.

```yaml
mode: standalone
rootUser: minioadmin
rootPassword: minioadmin
persistence:
  enable: true
  existingClaim: minio-dynamic-pvc
service:
  type: NodePort
  nodePort: 30061
consoleService:
  type: NodePort
  nodePort: 30062
environment:
  MINIO_BROWSER_REDIRECT_URL: http://kube-domain.com:30062
```

**MinIO Web**에 접속하기 위한 포트를 `30061`로, **MinIO API**에 접근하기 위한 포트를 `30062`로 설정했습니다. (포트는 원하는 포트로 변경 가능합니다.) 기존 서비스들은 전무 Ingress로 띄웠는데, MinIO는 API path rule과 console 버그(Static Resource, Session 등의 이슈)로 인해 Ingress 사용이 불가능해 NodePort를 사용했습니다. MinIO를 위한 doamin이 따로 있어야 사용 가능할 것 같습니다. 

만약 반드시 Ingress를 사용하고싶다면 API는 어렵고, Console은 rewrite를 통해 접근만이라도 Ingress로 할 수 있도록 설정이 가능합니다. (Ingress로 접근 후 기존 URL로 redirect되는 방식입니다.)

```yaml
consoleIngress:
  enabled: true
  annotations:
    kubernetes.io/ingress.class: nginx  # 해당 부분은 사용중인 ingress class에 맞춰 작성합니다.
    nginx.ingress.kubernetes.io/rewrite-target: http://kube-domain.com:30062
    nginx.ingress.kubernetes.io/use-regex: "true"
  path: /minio(/|$)(.*)
  hosts:
    - ""
```

<br>

마지막으로 `environment.MINIO_BROWSER_REDIRECT_URL`을 추가하였는데 여기에 외부에서 접근하는 주소를 입력하면 됩니다. 

### 설치
이제 작성한 value.yaml을 이용해서 MinIO를 설치하겠습니다. 설치 이후 `MINIO_BROWSER_REDIRECT_URL`로 접속했을 때 로그인 페이지가 뜨고 로그인에 성공하면 잘 설치된 것입니다.  
```bash
$ helm install minio minio/minio -n minio-system -f values.yaml
```

<br>

## Reference
- <https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner>
- <https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner/blob/master/charts/nfs-subdir-external-provisioner/README.md>
- <https://artifacthub.io/packages/helm/minio-official/minio>