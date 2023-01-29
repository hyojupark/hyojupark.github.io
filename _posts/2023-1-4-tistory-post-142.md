---
title: Airflow KubernetesPodOperator xcom 사용 시 발생하는 toomanyrequests 이슈
toc: true
categories:
  - Airflow
tags:
  - airflow
  - airflowLocalSettings
  - airflow_local_settings
  - Alpine
  - KubernetesHook
  - KubernetesPodOperator
  - SIDECAR_CONTAINER
  - toomanyrequests
  - xcom
  - xcom_push
---

평소처럼 KubernetesPodOperator를 잘 활용하다 아래와 같은 오류 메세지가 발생했습니다.



> rpc error: code = Unknown desc = Error response from daemon: toomanyrequests: You have reached your pull rate limit. You may increase the limit by authenticating and upgrading: https://www.docker.com/increase-rate-limit


 


오류의 원인은 DockerHub의 pull 횟수 제한 때문입니다. 하지만 저는 Harbor에 올려놓은 컨테이너 이미지를 사용 중이었습니다. 왜 이 오류가 발생했을까요?


 


오류의 원인은 KubernetesPodOperator에서 xcom을 사용하기 위해 `do_xcom_push=True` 옵션을 적용했기 때문입니다.



```
task = KubernetesPodOperator(
    ...
    do_xcom_push=True
)
```

 


Airflow에서는 KubernetesPodOperator에서도 xcom을 사용하기 위해서 sidecar로 **alpine** 이미지를 함께 띄웁니다. 이때 이 alpine 이미지를 DockerHub에서 가져옵니다.


 


<https://github.com/apache/airflow/blob/main/airflow/providers/cncf/kubernetes/utils/xcom_sidecar.py#L40>



```
...

class PodDefaults:
    """Static defaults for Pods"""

    XCOM_MOUNT_PATH = '/airflow/xcom'
    SIDECAR_CONTAINER_NAME = 'airflow-xcom-sidecar'
    XCOM_CMD = 'trap "exit 0" INT; while true; do sleep 1; done;'
    VOLUME_MOUNT = k8s.V1VolumeMount(name='xcom', mount_path=XCOM_MOUNT_PATH)
    VOLUME = k8s.V1Volume(name='xcom', empty_dir=k8s.V1EmptyDirVolumeSource())
    SIDECAR_CONTAINER = k8s.V1Container(
        name=SIDECAR_CONTAINER_NAME,
        command=['sh', '-c', XCOM_CMD],
        image='alpine',
        volume_mounts=[VOLUME_MOUNT],
        resources=k8s.V1ResourceRequirements(
            requests={
                "cpu": "1m",
            }
        ),
    )
```

 


위 코드를 보면 SIDECAR\_CONTAINER의 image가 alpine으로 고정된 것을 볼 수 있습니다. 때문에 KubernetesPodOperator를 사용하기 전에 **SIDECAR\_CONTAINER.image**를 변경 후 사용해야하는데, 필요한 DAG 마다 각각 변경하지 않고 Airflow 전체적으로 한번에 적용하는 방법이 좋습니다. 


 


 


### **Helm으로 설치한 경우**


values.yaml을 수정해서 **SIDECAR\_CONTAINER.image**를 변경할 수 있습니다.



```
...
airflowLocalSettings: |
  from airflow.providers.cncf.kubernetes.utils.xcom_sidecar import PodDefaults
  PodDefaults.SIDECAR_CONTAINER.image = private.registry.com/alpine
```

 


### **Docker로 설치한 경우**


Airflow Dockerfile에서 수정한 **airflow\_local\_settings.py**를 덮어써서 **SIDECAR\_CONTAINER.image**를 변경할 수 있습니다.



```
FROM apache/airflow:2.1.4

COPY --chown=airflow:root airflow_local_settings.py /opt/airflow/config/airflow_local_settings.py

USER airflow
```

 


 


 


해당 이슈를 조금 더 검색해본 결과 <https://github.com/apache/airflow/pull/26766> 해당 이슈가 약 2달 전에 merge된 것을 확인할 수 있었습니다. 해당 내용은 cncf.kubernetes provider 5.0.0rc3 버전부터 반영되어있어, cncf.kubernetes provider 최신 버전을 사용하면 Airflow Web에서 **KubernetesHook**을 만들고 설정할 때 **xcom\_sidecar\_container\_image**를 입력할 수 있는 것으로 보입니다.


 


 


**Reference**


* <https://github.com/apache/airflow/issues/10605>
* <https://github.com/apache/airflow/pull/26766>
* <https://github.com/apache/airflow/issues/27674>
