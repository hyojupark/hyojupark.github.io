---
title: MLflow Artifact 삭제 (Permanently delete runs)
toc: true
categories:
  - MLflow
tags:
  - artifact delete
  - Delete
  - experiment-ids
  - gc
  - metadata
  - MLflow
  - older-than
  - permanently
  - Runs
---

MLflow를 사용하다 반복되는 실험으로 S3에 artifact가 쌓이고있어 삭제 방법을 찾게 되었습니다. Tracking Server의 Web UI에서는 **delete가 hide로 동작**하기 때문에 S3에 쌓인 artifact와 PostgreSQL에 쌓인 metadata를 지우기 위해서는 command line 명령어를 직접 실행해야합니다.

## **mlflow gc**

기본적인 삭제 방법은 run id를 찾아서 직접 입력해서 삭제하는 방법입니다.

```bash
$ mlflow gc --backend-store-uri postgresql://mlflow-user:mlflow-password@mlflow-postgresql.mlflow-system.svc.cluster.local:5432 --run-ids abaa0cga8cad45ef13ab6ec7d172c82e
```

추가로 오래된 artifact를 삭제하는 옵션(`--older-than`, v1.28 이상)과 특정 실험 내 artifact를 전부 삭제하는 옵션(`--experiment-ids`, v1.30 이상)을 활용해서 삭제가 가능합니다.
