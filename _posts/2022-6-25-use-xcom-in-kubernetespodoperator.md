---
title: KubernetesPodOperator에서 XCOM 사용하기
toc: true
categories:
  - Airflow
tags:
  - airflow
  - KubernetesPodOperator
  - xcom
---

`DockerOperator`나 `KubernetesPodOperator`는 다른 Operator와 XCOM을 사용하는 방식이 조금 다릅니다. 여기서는 `KubernetesPodOperator` 기준으로 XCOM **push**와 **pull** 방법을 설명하겠습니다.

## **XCOM Push**

먼저 아래와 같이 `KubernetesPodOperator`에 `do_xcom_push` 옵션은 추가합니다.

```python
task1 = KubernetesPodOperator(
    task_id='kubernetes_pod_operator_test',
    name='test_job',
    namespace='airflow',
    image='pytorch/pytorch:1.11.0-cuda11.3-cudnn8-devel',
    cmds=['/bin/bash', '-c'],
    arguments=["echo test && echo hello"],
    do_xcom_push=True,
    is_delete_operator_pod=True,
    get_logs=True
)
```

그리고 pod에서 아래와 같이 `/airflow/xcom` 경로에 `return.json` 파일을 생성해서 dictionary 포맷으로 공유할 값을 저장합니다.

```python
import json
import os

...

xcom_dict = {
	'share_value': 'hello world'
}
os.makedirs('/airflow/xcom', exist_ok=True)
with open('/airflow/xcom/return.json', 'w', encoding='utf-8') as f:
	json.dump(xcom_dict, f, ensure_ascii=False)
```

이 과정을 Kubernetes에서 직접 확인해보면 Pod에서 `KubernetesPodOperator`의 image로 container를 생성하고 추가로 alpine image로 container를 생성해서 `/airflow/xcom/return.json`을 airflow XCOM으로 올려줍니다.

## **XCOM Pull**

`KubernetesPodOperator`를 통해 저장된 XCOM 값은 `return_value`를 key으로 저장됩니다. 저장된 값을 사용하는 방법은 아래와 같습니다.

```python
task_2 = BashOperator(
    task_id='task_2',
    bash_command='echo "share_value: {{ ti.xcom_pull(task_ids="kubernetes_pod_operator_test")["share_value"] }}"'
}
```

위 task가 실행되면 "share_value: hello world"가 log로 저장된 것을 확인할 수 있습니다. `xcom_pull`에 task_ids만 사용해서 `return_value` 값을 dictionary 형태로 값을 가져오게되고, `share_value`를 key으로 줘서 저장된 값을 가져오게 됩니다.
