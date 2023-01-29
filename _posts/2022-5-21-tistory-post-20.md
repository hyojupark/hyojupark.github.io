---
title: Airflow에서 PyTorch 사용 (KubernetesPodOperator)
toc: true
categories:
  - Airflow
tags:
  - airflow
  - DockerOperator
  - KubernetesPodOperator
  - pytorch
---

Airflow를 Kubernetes에 올려놓고 사용중인데, PyTorch를 사용하기 위한 패키지들을 Airflow에 설치하고 싶지 않아 알아보던 중 KubernetesPodOperator를 이용해서 해결했습니다. Kubernetes에 설치하지 않았다면 DockerOperator를 사용해도 될 것 같습니다.


 


 


### **패키지 설치**


우선 아래 패키지가 필요합니다. 설치되어있지 않다면 설치해줍니다.



```
# KubernetesPodOperator
pip install apache-airflow-providers-cncf-kubernetes

# DockerOperator
pip install apache-airflow-providers-docker
```

 


### **코드**


Operator 코드만 간단하게 정리해봤습니다.



```
task1 = KubernetesPodOperator(
    task_id='kubernetes_pod_operator_test',
    name='test_job',
    namespace='airflow',
    image='pytorch/pytorch:1.11.0-cuda11.3-cudnn8-devel',
    cmds=['/bin/bash', '-c'],
    arguments=["echo test && echo hello"],
    is_delete_operator_pod=True,
    get_logs=True
)
```

 


위 옵션은 최소한 필요한 정보와 활용하기 좋은 옵션들로 정리해봤습니다.


**name**과 **namespace**는 kubernetes에서 pod가 생성될 때의 정보이고, **image**는 Docker Hub에 있는 이미지입니다. 기본적으로 image는 Docker Hub에서 가져오고 Private Docker Registry를 사용하는 경우에는 secret로 관련 정보를 넣어주어야합니다.


**cmds**와 **arguments**는 Docker image를 pull한 후 command 실행이 필요한 경우에 사용합니다. 사용하는 PyTorch 이미지에 코드는 없기 때문에 필요한 경우 github에서 clone해서 사용해야하는데, 이 때 arguments에 필요한 명령어를 넣어주면 됩니다.


**is\_delete\_operator\_pod**는 Operator가 pod를 생성 후 실행이 다 끝나면 pod를 삭제하는 옵션입니다. 테스트 때문이 아니라면 꼭 넣어줍니다.


마지막으로 **get\_logs**는 pod가 동작하면서 생기는 log를 전부 출력해줍니다. airflow에서 pod에서 동작되는 내용을 확인하기 위해 꼭 넣어줍니다.

