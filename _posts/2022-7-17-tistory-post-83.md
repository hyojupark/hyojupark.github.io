---
title: Airflow 기간 내 Task clear (재실행)
toc: true
categories:
  - Airflow
tags:
  - airflow
  - clear
  - DAG
  - TASK
---

Airflow Web UI에서도 task를 clear할 수 있지만, task의 양이 많거나 명령어를 이용해서 자동화하고 싶은 경우 아래 명령어를 이용해서 clear가 가능합니다.


 



```
$ airflow tasks clear {dag_id} -t {task regex} -s 2022-07-10T20:00:00+09:00 -e 2022-07-17T20:00:00+09:00
```

 


* **dag\_id**
	+ clear할 dag id
* **-t, --task-regex**
	+ 재실행할 tasks의 regular expression
* **-s, --start-date**
	+ clear 실행 시작 시간
	+ 위 예시 format 참조
* **-e, --end-date**
	+ clear 실행 종료 시간
	+ 위 예시 format 참조
* **-d, --downstream**
	+ downstream tasks를 포함하여 clear (default: false)
* **-u, --upstream**
	+ upstream tasks를 포함하여 clear (default: false)
* **-f, --only-failed**
	+ 실패한 jobs만 clear (default: false)


 


 


**Reference**


* <https://airflow.apache.org/docs/apache-airflow/stable/cli-and-env-variables-ref.html#clear>
