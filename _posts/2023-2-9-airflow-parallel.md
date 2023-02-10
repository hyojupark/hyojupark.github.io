---
title: Airflow 병렬처리하기
toc: true
categories:
  - Airflow
tags:
  - parallel
  - worker
  - DAG
  - Task
published: false
---

Airflow에서 병렬처리할 때 고려해야할 대상이 <u>Airflow의 Queue와 Worker</u>, <u>DAG의 Run과 Task</u>입니다. 고려해야할 대상이 많다보니 많이 햇갈리는데, 하나씩 살펴보도록 하겠습니다.


## 병렬처리 고려 대상
Airflow에서 병렬차리할 때 고려해야할 대상이 크게 5가지 있습니다.

1. DAG에서 동시에 **스케줄** 가능한 Run 수
2. DAG에서 동시에 **처리** 가능한 Task 수
3. Airflow에서 동시에 **스케줄** 가능한 Task 수
4. Worker가 동시에 **처리** 가능한 Task 수
5. Worker의 수

위 대상에서 **처리**과 **스케줄**를 구분한 이유는 당연히 의미가 다르기 때문입니다. 여기서 의미하는 **처리**는 실제로 자원을 사용하면서 <u>동작하는 Task</u>를 의미하고, **스케줄**은 Queue에서 스케줄러가 동작시켜줄 때 까지 <u>대기하는 Task</u>를 의미합니다.

위 내용을 정확히 이해하고 아래 설명하는 내용을 적용하면 됩니다.

<br>


### 1. DAG에서 동시에 스케줄 가능한 Run 수
- config name: `max_active_runs_per_dag`
- environment variable name: `AIRFLOW__CORE__MAX_ACTIVE_RUNS_PER_DAG`
- default value: `16`

#### # 설명
`max_active_runs_per_dag`는 DAG에서 동시에 실행할 Run 수 입니다. 해당 설정을 변경하면 Airflow 전체 DAG의 `max_active_runs` default 값이 변경되며, Python에서 DAG 작성 시 `max_active_runs` parameter로 수정 가능합니다.
DAG가 시작되면 default value(`16`)를 기준으로 Backfill이 최대로 동작할 경우 최소 16개(Task 병렬처리 여부와 `max_active_tasks`에 따라 증가)의 Task가 스케줄링되면서 Task가 순차적으로 처리됩니다. 이때 DAG는 16개의 Run이 모두 처리될 때 까지 기다렸다가, 모두 처리되면 다음 16개의 Run을 시작합니다.

#### # 추천 설정
일반적으로 Run의 처리속도가 몇 초 만에 처리될 정도로 빠르지 않다면 `max_active_runs`를 크게 잡지 않는 것을 추천합니다. <u>Airflow Queue 공간이 크게 차지</u>하기도도 하고(아래 4.에서 설명), <u>`max_active_tasks`가 작으면 효과가 없기 때문</u>입니다.
`max_active_tasks`를 크게 잡아서 Backfill을 빠르게 처리해야하는 경우에만 `max_active_runs`를 크게 잡는 것이 좋습니다.

<br>

### 2. DAG에서 동시에 처리 가능한 Task 수
- config name: `max_active_tasks_per_dag`
- environment variable name: `AIRFLOW__CORE__MAX_ACTIVE_TASKS_PER_DAG`
- default value: `16`

#### # 설명
`max_active_tasks_per_dag`는 DAG에서 동시에 실행할 Task 수 입니다. 해당 설정을 변경하면 Airflow 전체 DAG의 `max_active_tasks` default 값이 변경되며, Python에서 DAG 작성 시 `max_active_tasks` parameter로 수정 가능합니다.
DAG가 실행되면 default value(`16`)을 기준으로 하나의 Run에서 병렬처리 가능한 만큼 16개 이하의 Task가 동시에 처리됩니다. 동시 처리 가능한 Task 수가 남으면 `max_active_runs`에 따라 실행된 Run의 Task도 16개가 될 때 까지 처리됩니다.
따라서 아래 그림처럼 하나의 Run에서 동시에 처리될 Task의 수 이기도 하고, 여러 Run에 걸쳐 동시에 처리될 Task의 수 이기도 합니다.
![image](/assets/images/posts/2023-2-9-airflow-parallel/dag_active_tasks_example1.png){: .align-center width="70%"}

#### # 추천 설정
이 값은 Backfill을 하지 않을 경우엔 하나의 Run에서 병렬처리되는 Task 수 만큼, Backfill을 고려한다면 Airflow Queue가 전부 사용되지 않는 선에서 적당한 값을 설정하는게 좋습니다.


#### # 주의사항
`max_active_runs`와 `max_active_tasks`를 2 이상 설정할 때는 Run이 동시에 실행되기 때문에, 각 Run의 실행 순서, 동시 처리와 상관없이 동작하도록 원자성(Atomicity)을 지켜서 DAG를 설계해야합니다.

<br>

### 3. Airflow에서 동시에 스케줄 가능한 Task 수
- config name: `parallelism`
- environment variable name: `AIRFLOW__CORE__PARALLELISM`
- default value: `32`

#### # 설명
`parallelism`은 Airflow의 <u>queue 크기</u>를 의미한다고 생각하면 됩니다. 아래 예시 그림으로 설명하겠습니다.

![image](/assets/images/posts/2023-2-9-airflow-parallel/airflow_queue_example1.png){: .align-center width="70%"}

그림은 `max_active_tasks`를 `2`로 설정하고, 동시 `max_active_runs`를 `4`로 설정한 DAG입니다. 보는 것과 같이 실제 동작중인 Task는 2개이지만, Queue에는 4개가 들어가게 됩니다. 동시 Run 수(max_active_runs)의 default value가 16이라 잘못 사용하면 Queue가 가득차 스케줄링되지 않는 현상을 볼 수 있습니다. Queue가 가득차면 Worker가 Task를 처리할 수 있는 여유가 있음에도 처리하지 않는 현상이 발생합니다.

#### # 추천 설정
추천하는 방법은 `max_active_runs`를 최소한으로 사용하되 모자라지 않게 적절한 값을 사용하는 것입니다. 예를 들어, `max_active_runs`를 `4`로 설정한 경우, 8개의 DAG가 동시에 처리돼도 문제가 없고, DAG가 더 추가되면 `parallelism`을 `64`정도로 설정을 변경해서 Queue가 가득차는 현상을 방지해야합니다.

<br>

### 4. Worker가 동시에 처리 가능한 Task 수
- config name: `worker_concurrency`
- environment variable name: `AIRFLOW__CELERY__WORKER`
- default value: `16`
- `worker_autoscale` 설정 사용 시 `worker_concurrency` 무시

#### # 설명
`Sequential Executor`를 사용할 경우에는 사용 불가능하며, 하나의 Worker로 동시에 처리할 Task 수를 의미합니다. 위의 `max_active_tasks`에 따라 Worker가 Task를 동시에 처리하게 됩니다.

#### # 추천 설정
전체 DAG의 `max_active_tasks` 값에 따라 설정하면 됩니다. 최대 16개의 Task가 동시에 동작할 경우에 `16` 그대로 사용하면 되며, 만약 아래서 설명할 Worker를 여러 개 사용할 경우 Worker 수 만큼 나눠서 설정(16/Worker 수)해도 됩니다.

<br>

### 5. Worker의 수
- config name: `airflow.replicas` (Helm 기준)

#### # 설명
Worker 수는 Message Broker를 사용해야하기 때문에 주로 `Celery Executor`나 `Kubernetes Executor`를 사용할 때 설정 가능합니다. 
Worker를 여러 개 사용하면 HA(High Availability)가 가능해지고, 리소스가 부족할 때 Worker만 늘리면 되기 때문에 확장이 간편해집니다.

#### # 추천 설정
위의 `worker_concurrency`를 늘리면 Worker의 부담이 커지기 때문에 `worker_concurrency`는 default value를 사용하고 Worker 수를 늘려서 확장하는 방식을 추천합니다. Worker 수가 많을수록 Worker 장애 시 피해가 많이 줄어들기 때문입니다.

#### # 주의사항
Worker를 2개 이상 사용하면 연결된 Task가 동일한 Worker에서 실행을 보장하지 않기 때문에 Disk에 파일을 남겨서 처리하는 방식을 사용하면 안됩니다. 반드시 DB를 활용해서 처리해야합니다.

<br>

## Reference
- https://airflow.apache.org/docs/apache-airflow/stable/configurations-ref.html
- https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/executor/index.html#executor-types
- https://velog.io/@graphy-young/Apache-Airflow%EC%9D%98-%EB%8F%99%EC%8B%9C%EC%84%B1-%EC%84%A4%EC%A0%95-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-%EC%9D%B4%EB%A1%A0%ED%8E%B8