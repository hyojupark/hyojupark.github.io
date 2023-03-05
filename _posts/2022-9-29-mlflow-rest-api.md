---
title: MLflow 패키지 설치 없이 REST API로 사용하기
toc: true
categories:
  - MLflow
tags:
  - airflow
  - get-latest-versions
  - MLflow
  - package
  - REST API
  - REST-API
  - transition-stage
---

Airflow로 학습 및 배포 스케줄링을 하는데 단지 모델의 버전 확인, Stage 변경과 같은 단순 작업에 MLflow 패키지를 설치해야하는 과정이 발생했습니다. MLflow 패키지는 생각보다 무겁고, Airflow에서 관리상 PythonVirtualenvOperator를 사용하면 <u>매 Task 실행마다 MLflow를 설치하게</u>되는 불상사가 발생했습니다.

이를 해결하기 위해 직접적으로 **MLflow**의 **REST API**를 사용하는 방법을 설명합니다. 아래 예시를 확인하고, 참고 자료에 API 목록에 있는 것들을 활용하시면 됩니다.

<br>

## **사용 예시**

```python
import json
import requests

def model_stage_change():
	mlflow_tracking_uri = '0.0.0.0:5000'
    model_name = 'mnist_model'
    
    # Get staging model infomation
    uri = f'{mlflow_tracking_uri}/api/2.0/mlflow/registered-models/get-latest-versions'
    params = {
    	'name': model_name,
        'stages': ['Staging']
    }
    response = requests.post(uri, data=json.dumps(params)).json()
    model_info = response['model_versions'][0]
    
    print('Model Name: ', model_info['name'])
    print('Model Version: ', model_info['version'])
    print('Model Run ID: ', model_info['run_id'])
    
    
    # Transition model stage
    uri = f'{mlflow_tracking_uri}/api/2.0/mlflow/model-versions/transition-stage'
    params = {
    	'name': model_info['name'],
        'version': model_info['version'],
        'stage': 'Production',
        'archive_existing_versions': True
    }
    response = requests.post(uri, data=json.dumps(params))
    
    assert response.status_code == 200
```

이렇게하면 MLflow 패키지를 설치하지 않아도 <u>REST API만으로 접근 및 컨트롤이 가능</u>합니다.

<br>

## **Reference**

* <https://mlflow.org/docs/latest/rest-api.html>


 

