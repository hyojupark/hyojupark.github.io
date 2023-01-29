---
title: Airflow 분 단위 주기 설정
toc: true
categories:
  - Airflow
tags:
  - airflow
  - minute
  - schedule_interval
  - Spark Streaming
  - timedelta
---

Airflow는 스트리밍 솔루션이 아니기 때문에 한 시간 이하의 주기로 동작시키는 경우를 많이 볼 수 없습니다. 그러나 주기만 줄여서 사용하고 싶다면 분 단위까지 낮춰서 사용할 수 있습니다.


 



```
from datetime import datetime, timedelta

from airflow import DAG

with DAG(
    dag_id='my_dag',
    schedule_interval=timedelta(minute=5),
    start_date=datetime(2022, 6, 1),
    ...
) as dag:
	...
```

 


다만, 실제 Task로 작성한 코드가 동작 시간을 제외하고도 Task 실행 전/후로 동작하는 시간 등을 생각했을 때 schedule\_interval을 1분 이하로 사용하는 것은 많이 위험합니다. 더 짧은 주기로 데이터 처리가 필요할 경우 Spark Streaming과 같은 micro-batch 솔루션 사용을 권장합니다.

