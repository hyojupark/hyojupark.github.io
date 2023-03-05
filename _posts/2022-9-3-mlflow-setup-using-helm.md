---
title: MLflow 설치 (using Helm)
toc: true
categories:
  - MLflow
tags:
  - artifact hub
  - artifacts-destination
  - helm
  - Ingress
  - MLflow
  - PostgreSQL
  - S3
  - serveArtifacts
  - static-prefix
  - tracking server
---

본 설치는 **MLflow 설계 시나리오 5** 기반으로 작성하였습니다.

<https://www.mlflow.org/docs/latest/tracking.html#scenario-5-mlflow-tracking-server-enabled-with-proxied-artifact-storage-access>

![mlflow scenario 5](/assets/images/posts/2022-9-3-mlflow-setup-using-helm/img-1.png){: .align-center}
**MLflow Scenario 5**
{: .text-center}

<br>

## **1. PostgreSQL 설치**

**Artifact Hub 주소**: <https://artifacthub.io/packages/helm/bitnami/postgresql>

### **1) postgresql-values.yaml 작성**

```yaml
# postgresql-values.yaml
global:
  postgresql:
    auth:
      username=mlflow-user
      password=topsecret
      database=mlflow
```

### **2) repo 추가 및 postgresql 설치**

```bash
$ helm repo add bitnami https://charts.bitnami.com/bitnami
$ helm repo update
$ helm install mlflow-test-postgresql bitnami/postgresql -n mlflow-system -f postgresql-values.yaml
```

<br>

## **2. MLflow 설치**

**Artifact Hub 주소**: <https://artifacthub.io/packages/helm/community-charts/mlflow>

### **1) mlflow-values.yaml 작성**

```yaml
# mlflow-values.yaml
backendStore:
  postgres:
    enabled: true
    host: "mlflow-test-postgresql.mlflow-system.svc.cluster.local"
    port: "5432"
    database: "mlflow"
    user: "mlflow-user"
    password: "topsecret"

artifactRoot:
  s3:
    enabled: true
    bucket: "mlflow-test"
    path: "artifacts"
    awsAccessKeyId: "..."
    awsSecretAccessKey: "..."

extraArgs:
  artifacts-destination: s3://mlflow-test/artifacts

extraFlags:
  [serveArtifacts]

extraEnvVars:
  # MLFLOW_S3_IGNORE_TLS: true
  # MLFLOW_S3_UPLOAD_EXTRA_ARGS: '{"ServerSideEncryption": "aws:kms", "SSEKMSKeyId": "1234"}'
  # AWS_DEFAULT_REGION: my_region
  MLFLOW_S3_ENDPOINT_URL: http://...
  # AWS_CA_BUNDLE: /some/ca/bundle.pem

livenessProbe:
  httpGet:
    path: /
    port: 5000

readinessProbe:
  httpGet:
    path: /
    port: 5000
```

- artifactRoot는 사용중인 s3에 맞춰서 작성하면 됩니다.
- 별도의 s3(ex. minio)를 사용하실 경우 `extraEnvVars`의 `MLFLOW_S3_ENDPOINT_URL`에 해당 저장소 주소를 넣어주면 됩니다.
- `extraArgs`에서 `artifacts-destination` 경로는 `s3://{bucket_name}/{path}` 순으로 작성하면 됩니다.
- 추가로 사용중인 S3 설정에 맞춰 `extraEnvVars` 변수를 추가하시면 됩니다.

### **2) repo 추가 및 mlflow 설치**

```bash
$ helm repo add community-charts https://community-charts.github.io/helm-charts
$ helm repo update
$ helm install mlflow-test community-charts/mlflow -n mlflow-system -f mlflow-values.yaml
```

이제 사용중인 kubernetes 환경에 맞춰 mlflow service에 접속해서 ui가 정상적으로 뜨는 것을 확인하면 됩니다. ingress 사용이 필요한 경우 mlflow values에 ingress 관련 설정을 추가하고, ingress 경로를 extraArgs.static-prefix에 추가하면 됩니다.
