---
title: Hadoop과 Hive, Presto 핵심 요약
toc: true
categories:
  - 데이터/데이터베이스
tags:
  - hadoop
  - hdfs
  - hive
  - Hive Metastore
  - HiveQL
  - impala
  - mapreduce
  - Presto
  - Yarn
---

![hadoop icon](/assets/images/posts/2022-9-25-tistory-post-114/img-1.png){: .align-center}

## **Hadoop (데이터 저장 및 조회)**

- **대량의 데이터를 분산 저장 및 처리할 수 있도록 해주는 프레임워크**
- **단일 소프트웨어가 아닌 분산 시스템을 구성하는 아래 3가지 소프트웨어로 구성된 집합체**
  - HDFS(Hadoop Distributed File System): 분산 파일 시스템
  - YARN(Yet Another Resource Negotiator): 리소스 관리자
  - MapReduce: 분산 데이터 처리
- **HDFS를 통해 데이터를 디스크에 저장, YARN을 통해 데이터를 분산 저장**

<br>

![hive icon](/assets/images/posts/2022-9-25-tistory-post-114/img-2.png)

## **Hive (대량의 데이터 처리에 특화된 쿼리 엔진)**

- **SQL같은 쿼리 언어를 Hadoop에서 실행하기 위해 개발**
  - 쿼리를 자동으로 MapReduce 프로그램으로 변환 (HiveQL)
- **대량의 배치 처리를 하기 위한 시스템**
  - 대량의 데이터를 처리하는 작업에 적합하나, 몇 초 안에 끝나버리는 소량의 데이터 처리 혹은 에드 혹 쿼리(필요할 때 마다 조회하는)에는 적합하지 않음
- **데이터 웨어하우스에 적합**

<br>

![presto icon](/assets/images/posts/2022-9-25-tistory-post-114/img-3.png)

## **Presto (속도에 특화된 대화식 쿼리 엔진)**

- **Hive에서 만든 데이터를 집계하는 등의 목적에 적합**
	- Hive Metastore에 등록된 테이블을 가져올 수 있기 때문
- **인메모리 기반 처리**
- **대량의 쿼리를 실행하지 않도록 주의**
	- 한 번 쿼리를 실행하면 중간에 끼어들 수 없기 때문
- **Impala로 대체해서 사용 가능**
