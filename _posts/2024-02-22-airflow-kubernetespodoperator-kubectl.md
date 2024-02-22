---
title: Airflow에서 KubernetesPodOperator로 kubectl image 쉽게 사용하는 방법
categories:
  - Airflow
tags:
  - Airflow
  - KubernetesPodOperator
# published: false
---

Airflow 파이프라인에서 kubectl을 이용해서 deployment rollout을 해야할 상황이 생겼습니다. 여러 방법을 고민해보다 **KubernetesPodOperator**와 **Secret**을 이용하면 쉽게 할 수 있어서 그 방법을 정리합니다.

---

우선 사용할 kubeconfig를 airflow의 **Secret**으로 추가합니다.

```bash
$ kubectl create secret generic kubeconfig -n {airflow_namespace} --from-file=config=.kube/config
```

<br>

그리고 Airflow에서 **KubernetesPodOperator**를 다음과 같이 사용합니다.

```python
from airflow.kubernetes.secret import Secret
from airflow.providers.cncf.kubernetes.operators.kubernetes_pod import KubernetesPodOperator

secret = Secret('volume', '.kube', 'kubeconfig')
rollout_task = KubernetesPodOperator(
    task_id='rollout-task',
    name='service-rollout-task',
    namespace='{airflow_namespace}',
    image='bitnami/kubectl:latest',
    cmds=['/bin/bash', '-c'],
    secrets=[secret],
    is_delete_operator_pod=True,
    get_logs=True
)
```

이렇게하면 Pod에 Container가 생성될 때 `.kube/config` 경로에 Secret에 추가한 kubeconfig가 생성되면서 kubectl 명령어를 바로 사용할 수 있게 됩니다.

<br>

## Reference
- <https://medium.com/@john.shaw.zen/airflow-map-secret-file-in-kubernetespodoperator-66cb62753a2f>
