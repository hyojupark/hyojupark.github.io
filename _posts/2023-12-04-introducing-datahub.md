---
title: DataHub로 데이터를 한곳에 모아서 정리하는 방법
categories:
  - Data
tags:
  - DataHub
published: false
---

[큰 DataHub 로고]

DataHub는 **Data Discovery**(데이터 검색), **Data Observability**(데이터 관측 가능성), **Federated Governance**(연합 거버넌스)을 지원해서 Data Echosystem의 복잡성을 완화하는데 도움을 주는 Metadata Platform입니다. 즉 DataHub는 데이터가 많아지고 복잡해졌을 때 필요한 플랫폼인데, 위 3가지 관점에서 필요한 주요 시점은 다음과 같을 것 같습니다.

1. 찾고싶은 데이터가 어디에 있는지 모를 때, 혹은 이 데이터가 뭔지 모를 때
2. 데이터의 계보(Lineage) 혹은 종속성(Dependency)을 알 수 없을 때
3. 여러 데이터 소스를 한곳으로 모아서 관리하고 싶을 때

일반적으로 데이터의 스키마를 정의하면서 엑셀에 정리하고 각 데이터의 관계를 그려놓는 방식으로 정리를 많이 해놓는데, 현재 이러한 상황에 속한다면 DataHub의 도입을 검토해볼 필요가 있습니다.


## DataHub 설치
DataHub 설치는 Docker Compose를 이용한 방법과 Helm Chart를 이용한 방법이 있습니다. 각각의 설치 방법은 아래 문서를 참고바랍니다.

- Docker Compose: <https://datahubproject.io/docs/quickstart/>
- Helm Chart: <https://github.com/acryldata/datahub-helm> (!수정 필요)


## DataHub 사용방법
아래부터는 DataHub를 사용하는 순서에 대한 설명입니다. 설명에 사용되는 데이터들은 DataHub에서 제공되는 데모 페이지(<https://demo.datahubproject.io/>)를 기반으로 작성하였습니다.

DataHub를 사용하는 순서는 다음과 같습니다.

1. 데이터 소스 연결
2. 데이터 정의 작성
3. 데이터 관계 설정
4. 거버넌스 정의

### 1. 데이터 소스 연결
...


### 2. 데이터 정의 작성
...


### 3. 데이터 관계 설정
...


### 4. 거버넌스 정의
...




## Reference
- <https://datahubproject.io/docs/features/#datahub-administration>
- 