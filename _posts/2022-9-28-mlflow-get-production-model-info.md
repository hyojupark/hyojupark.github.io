---
title: MLflow Production 모델의 정보만 가져오는 방법
toc: true
categories:
  - MLflow
tags:
  - Archived
  - get_latest_versions
  - MLflow
  - MlflowClient
  - Production
  - run_id
  - stage
  - staging
---

MLflow 가이드를 보면 Production 모델을 가져오는 방법(`mlflow.pyfunc.load_model`)에 대한 설명이 있지만, 모델을 통채로 가져와서 load하기 때문에 속도도 느리고 리소스 낭비가 발생합니다.

현재 사용중인 Production 모델의 정보만 필요한 경우 아래 방법으로 가져올 수 있습니다.

```python
import mlflow

MODEL_NAME = 'mnist_model'
client = mlflow.tracking.MlflowClient()
models = client.get_latest_versions(MODEL_NAME, ['Production'])

print(models[0])

# 출력
<ModelVersion: creation_timestamp=1663914064138, current_stage='Production', description='',
last_updated_timestamp=1663914216446, name='mnist_model', run_id='973a7455b6ccbd6eca32b994a',
run_link='', source='s3://mnist-models/artifacts/1/973a7455b6ccbd6eca32b994a/artifacts/model',
status='READY', status_message='', tags={}, user_id='', version='2'>
```

여기서 사용한 `get_latest_versions`는 특정 stage인 모델들을 가져올 수 있습니다. paramter가 list인 것을 보면 여러 stage인 모델들을 한 번에 가져올 수 있음을 알 수 있습니다. output이 list이기 때문에 <u>Production 모델이 하나일지라도 models[0]으로 접근</u>해야합니다.

