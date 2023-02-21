---
title: Airflow Variables 활용하기 (+ Kubernetes 환경 변수로 추가)
toc: true
categories:
  - Airflow
tags:
  - airflow
  - AWS_ACCESS_KEY_ID
  - AWS_SECRET_ACCESS_KEY
  - Environment
  - env_vars
  - kubernetes
  - KubernetesPodOperator
  - variables
---

Airflow Variables를 이용하면 Kubernetes의 ConfigMap, Secret와 유사하게 key, value를 관리할 수 있습니다.

## **Airflow Variables 추가하기**

Airflow webserver에 접속하면 `Admin > Variables`에서 쉽게 추가할 수 있기 때문에 자세한 설명은 생략하겠습니다. 아래 AWS 관련 Key 2개를 추가했습니다.

![](/assets/images/posts/2022-9-27-airflow-variables/img-1.png){: .align-center}
**List Variable**
{: .text-center}

<br>

## **Variables 사용하기**

Airflow에서 Variables를 사용할 때는 **Jinja template**에 맞춰 사용하면 됩니다. Variables는 `var.value`에 담겨있습니다.

```python
...
    aws_access_key_id = '{{ var.value.AWS_ACCESS_KEY_ID }}'
    aws_secret_access_key = '{{ var.value.AWS_SECRET_ACCESS_KEY }}'
...
```

<br>

## **Kubernetes 환경 변수로 추가하기**

Airflow의 **KubernetesPodOperator**를 이용해서 실행할 때 위에서 설정한 값을 아래와 같은 방법으로 환경 변수에 추가할 수 있습니다.



```python
from kubernetes.client import models as k8s
from airflow.providers.cncf.kubernetes.operators.kubernetes_pod import KubernetesPodOperator

...
    
    env_vars = [
    	k8s.V1EnvVar('AWS_ACCESS_KEY_ID', '{{ var.value.AWS_ACCESS_KEY_ID }}',
        k8s.V1EnvVar('AWS_SECRET_ACCESS_KEY', '{{ var.value.AWS_SECRET_ACCESS_KEY }}'
    ]
    test_task = KubernetesPodOperator(
    	task_id='test',
        name='test',
        namespace='default',
        image='...',
        cmds=[...],
        arguments[...],
        env_vars=env_vars,
        is_delete_operator_pod=True,
        get_logs=True
    )
```
