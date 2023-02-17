---
title: Elasticsearch 성능 최적화
toc: true
categories:
  - Elastic Stack
tags:
  - copy_to
  - Elasticsearch
  - Query
  - refresh_interval
  - tuning
  - 성능 최적화
---

##  **Query**

1. **must보단 filter를 최대한 활용**
	* filter 사용 시 score 계산이 되지 않음
	* 자주 호출되는 문서 캐싱 (Node Query Cache)   
	  - indices.queries.cache.size로 조절 가능
2. **처리해야할 query가 쌓일 경우 bulk API 활용**
3. **문서 색인 시 PUT 보다는 POST를 활용**
	* PUT 사용 시 ID가 존재하는지 확인하는 검색 과정 존재
4. **match 대신 term, text 대신 keyword 활용**
	* 불필요한 분석 과정 생략
	* 분석이 필요하지 않는 성별, 직업 등의 문자열은 keyword를 활용

## **Mapping**

1. **여러 필드를 모아서 검색이 필요한 경우 multi_terms보다는 copy_to 활용**
	* multi field를 하나의 field로 묶어서 저장
2. **수치 계산이 필요하지 않은 숫자형 데이터는 keyword 사용**

##  **System Setting**

1. **적절한 refresh_interval 사용**
	* Disk I/O 최소화
	* 시간대에 따라 조절 가능 (새벽 시간에 interval을 늘린다던지...)
2. **레플리카 샤드가 꼭 필요한 경우가 아니라면 프라이머리 샤드만으로 운영**
