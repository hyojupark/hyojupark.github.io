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

Airflow를 병렬처리할 때 고려해야할 대상이 <u>Airflow의 queue와 Worker</u>, <u>DAG의 Run과 Task</u>입니다. 고려해야할 대상이 많다보니 많이 햇갈리는데, 하나씩 살펴보도록 하겠습니다.

우선 위 대상을 기준으로 고려해야할 항목이 크게 5가지 있습니다.

1. Airflow에서 동시에 **스케쥴** 가능한 Task 수
2. Worker가 동시에 **처리** 가능한 Task 수
3. Worker의 수
4. DAG에서 동시에 **스케쥴** 가능한 Run 수
5. DAG에서 동시에 **처리** 가능한 Task 수


