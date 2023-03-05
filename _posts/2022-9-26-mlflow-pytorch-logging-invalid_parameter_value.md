---
title: MLflow PyTorch logging INVALID_PARAMETER_VALUE 관련 오류
toc: true
categories:
  - MLflow
tags:
  - autolog
  - DISABLE_MLFLOW_INTEGRATION
  - INVALID_PARAMETER_VALUE
  - logging
  - MLflow
  - pytorch
  - Transformer
---

**MLflow**를 이용해서 **transformer 모델**을 학습시키는 중 `RestException: INVALID_PARAMETER_VALUE...`관련 에러가 발생하였습니다.

<br>

## **원인**

관련 이슈: <https://github.com/mlflow/mlflow/issues/6177>

MLflow autolog 동작 중 parameter value가 빈값인지 예외처리하는 부분에서 오류가 발생하였습니다.

<br>

### **해결 방법**

**MLflow 1.28** 버전에서 해당 예외처리에 대한 개선이 진행되었습니다. 따라서 <u>1.28 이상 버전을 사용하면 자동으로 해결이 가능</u>합니다. 별도의 Tracking Server를 사용 중일 경우 해당 서버의 버전을 1.28 이상으로 올려야 합니다.

추가로, Transformer 모델을 학습할 때는 `mlflow.pytorch.autolog()` 함수를 사용하지 않아도 자동으로 적용됩니다. 사용하지 않으려면 `mlflow.pytorch.autolog(disable=True)`를 사용하거나, `DISABLE_MLFLOW_INTEGRATION=TRUE` 환경변수를 추가하면 됩니다.
