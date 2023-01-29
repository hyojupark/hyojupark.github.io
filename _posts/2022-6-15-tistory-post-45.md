---
title: Airflow DummyOperator(EmptyOperator)
toc: true
categories:
  - Airflow
tags:
  - airflow
  - DummyOperator
  - EmptyOperator
---

Airflow는 특정 Task에서 조건에 따라 skip하거나 멈추게하는 등의 동작을 하기 어렵습니다. 이를 해결하기 위해 branch로 분리해서 기존 flow가 진행되지 않도록 할 수 있는데, 이때 DummyOperator를 활용할 수 있습니다.



```
import pendulum
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.operators.dummy import DummyOperator
from airflow.operators.python import BranchPythonOperator


def _task2():
	return 'task2_1'

with DAG(
    dag_id='dummy_example',
    description='Airflow DummyOperator Example',
    start_date=pendulum.datetime(2022, 1, 1),
    schedule_interval='@daily'
) as dag:
    task1 = BranchPythonOperator(
        task_id='task1',
        python_callable=_task1
    )
    
    task2_1 = BashOperator(task_id='task2_1', bash_command='echo "task 2-2!"')
    task2_2 = DummyOperator(task_id='task2_2')
    
    task1 >> [task2_1, task2_2]
```

 


만약 task2\_1이 특정 조건에 따라 동작할지 말지를 결정하고 싶다면 위의 코드를 참고해서 작성하면 됩니다. 아래 그림처럼 task2\_2가 대신 동작하게되고, task2\_2는 DummyOperator이기 때문에 아무런 동작 없이 종료됩니다.


![](/assets/images/posts/2022-6-15-tistory-post-45/img-1.png)DAG Graph




 


하지만 5월 1일부로 Airflow 2.3이 release되면서 **DummyOperator**의 명칭이 **EmptyOperator**로 조금 더 직관적으로 변경되었습니다. 위 코드를 EmptyOperator로 변경해보면 아래와 같습니다.



```
import pendulum
from airflow import DAG
from airflow.operators.bash import BashOperator
from airflow.operators.empty import EmptyOperator
from airflow.operators.python import BranchPythonOperator


def _task2():
	return 'task2_1'

with DAG(
    dag_id='dummy_example',
    description='Airflow DummyOperator Example',
    start_date=pendulum.datetime(2022, 1, 1),
    schedule_interval='@daily'
) as dag:
    task1 = BranchPythonOperator(
        task_id='task1',
        python_callable=_task1
    )
    
    task2_1 = BashOperator(task_id='task2_1', bash_command='echo "task 2-2!"')
    task2_2 = EmptyOperator(task_id='task2_2')
    
    task1 >> [task2_1, task2_2]
```

 

