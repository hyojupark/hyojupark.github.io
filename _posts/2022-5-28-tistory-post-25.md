---
title: KubernetesPodOperator를 이용해서 GPU 할당 받기
toc: true
categories:
  - Airflow
tags:
  - airflow
  - CUDA_VISIBLE_DEVICES
  - GPU
  - KubernetesPodOperator
  - resource
---

Airflow에서 딥러닝 서비스나 학습 파이프라인을 구축할 경우 필요한 만큼의 GPU만 할당해서 사용할 수 있도록 해야합니다. 그냥 사용하면 서버에 있는 전체 GPU를 잡아서 사용할 수 있습니다. 멀티 GPU 서버에서 단일 GPU만 할당받아서 사용하는 방법은 아래와 같습니다.


 


### **방법 1. CUDA\_VISIBLE\_DEVICES 환경변수 설정 (비추천)**



```
task1 = KubernetesPodOperator(
    task_id='kubernetes_pod_operator_test',
    name='test_job',
    namespace='airflow',
    image='pytorch/pytorch:1.11.0-cuda11.3-cudnn8-devel',
    cmds=['/bin/bash', '-c'],
    arguments=["CUDA_VISIBLE_DEVICES=0"],
    is_delete_operator_pod=True,
    get_logs=True
)
```

이 방법을 사용하면 쉽게 단일 GPU 사용이 가능하지만, 특정 GPU 번호를 잡아줘야하고 그로인해 중복 사용 위험성이 있고 사용중인 GPU 관리가 어렵습니다.


 


 


### **방법 2. Kubernetes resource limit 설정 (추천)**



```
from kubernetes.client import models as k8s

...

resources = k8s.V1ResourceRequirements(limits={'nvidia.com/gpu': '1'})

task1 = KubernetesPodOperator(
    task_id='kubernetes_pod_operator_test',
    name='test_job',
    namespace='airflow',
    image='pytorch/pytorch:1.11.0-cuda11.3-cudnn8-devel',
    cmds=['/bin/bash', '-c'],
    resources=resources,
    is_delete_operator_pod=True,
    get_logs=True
)
```

이 방법을 사용하면 kubernetes에서 남는 GPU 하나를 할당하기 때문에 관리나 중복 사용을 피할 수 있습니다.


 


 


### **3. Kubernetes extended resource 설정 (Advanced)**


단일 GPU도 memory를 나눠서 사용해야할 경우 kubernetes에서 extended resource 설정을 해줘야합니다. 이 방법은 위 두 가지 방법보다 많이 복잡합니다. 아직 해보지 못해서, 꼭 필요한 경우에 이 키워드로 검색해서 적용하면 됩니다.

