---
title: BranchPythonOperator로 Task 선택
toc: true
categories:
  - Airflow
tags:
  - airflow
  - branch
  - BranchPythonOperator
---

BranchPythonOperator를 사용하면 여러 개의 Task 중 하나를 선택해서 flow를 진행할 수 있습니다. 조건에 따라 하나를 선택해서 사용하는 경우에 적용하면 유지 관리 측면에서도 효과를 볼 수 있습니다.

아래 예제 코드로 살펴보겠습니다.

```python
import pendulum
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.operators.python import PythonOperator, BranchPythonOperator
from airflow.utils.task_group import TaskGroup
from airflow.utils.trigger_rule import TriggerRule


def _task1(**kwargs):
    ti = kwargs['ti']

    ti.xcom_push('branch', 'task2_2')


def _task2(**kwargs):
    ti = kwargs['ti']

    branch_task = ti.xcom_pull(task_ids='task1', key='branch')

    return branch_task


with DAG(
    dag_id='branch_example',
    description='Airflow BranchPythonOperator Example',
    start_date=pendulum.datetime(2022, 1, 1),
    schedule_interval='@daily'
) as dag:
    task1 = PythonOperator(
        task_id='task1',
        python_callable=_task1
    )

    with TaskGroup('group_task2', prefix_group_id=False) as group:
        task2 = BranchPythonOperator(
            task_id='t2',
            python_callable=_task2
        )

        task2_1 = BashOperator(task_id='task2_1', bash_command='echo "task 2-1!"')
        task2_2 = BashOperator(task_id='task2_2', bash_command='echo "task 2-2!"')
        task2_3 = BashOperator(task_id='task2_3', bash_command='echo "task 2-3!"')

    task3 = BashOperator(
        task_id='task3',
        bash_command='echo "task 3!"',
        trigger_rule=TriggerRule.ONE_SUCCESS
    )

    task1 >> task2 >> [task2_1, task2_2, task2_3] >> task3
    task2 >> task3
```

위 코드를 Graph로 확인해보면 아래와 같습니다.

![](/assets/images/posts/2022-6-7-select-task-using-branchpythonoperator/img-1.png){: .align-center}
**< DAG Graph >**
{: .text-center}
<br>

코드를 간단히 설명하면, XCOM을 이용해서 task1에서 선택한 task id를 t2에서 받아와 해당하는 task id를 반환합니다. BranchPythonOperator를 이용하면 반환된 task id를 실행시키고 나머지는 모두 skip할 수 있습니다. 또한 위 그래프와 같이 바로 task3로 연결해서 [task2_1, task2_2, task2_3]을 모두 건너뛸 수도 있습니다. 그리고 task3를 실행하기 위해 trigger_rule을 설정해서 3개의 task 중 하나만 성공해도 task3가 동작할 수 있도록 정의합니다.

group_task2는 시각적으로 보기 편하기 위해 사용한 것으로, 특별한 기능은 따로 없습니다.

Airflow flow 작성에 도움이 되길 바랍니다.
