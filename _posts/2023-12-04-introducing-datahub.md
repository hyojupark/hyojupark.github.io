---
title: DataHub로 데이터를 한곳에 모아서 정리하는 방법
categories:
  - Data
tags:
  - DataHub
  - Data Discovery
published: false
---

[큰 DataHub 로고]

DataHub는 **Data Discovery**(데이터 검색), **Data Observability**(데이터 관측 가능성), **Federated Governance**(연합 거버넌스)을 지원해서 Data Echosystem의 복잡성을 완화하는데 도움을 주는 Metadata Platform입니다. 즉 DataHub는 데이터가 많아지고 복잡해졌을 때 필요한 플랫폼인데, 위 3가지 관점에서 필요한 주요 시점은 다음과 같을 것 같습니다.

1. 찾고싶은 데이터가 어디에 있는지 모를 때, 혹은 이 데이터가 뭔지 모를 때
2. 데이터의 계보(Lineage) 혹은 종속성(Dependency)을 알 수 없을 때
3. 여러 데이터 소스를 한곳으로 모아서 관리하고 싶을 때

<br>

일반적으로 데이터의 스키마를 정의하면서 엑셀에 정리하고 각 데이터의 관계를 그려놓는 방식으로 정리를 많이 해놓는데, 현재 이러한 상황에 속한다면 DataHub의 도입을 검토해볼 필요가 있습니다.


## DataHub 설치
DataHub 설치는 Docker Compose를 이용한 방법과 Helm Chart를 이용한 방법이 있습니다. 각각의 설치 방법은 아래 문서를 참고바랍니다.

- **Docker Compose**
  - 가이드: <https://datahubproject.io/docs/quickstart/>
- **Helm**
  - 가이드: <https://datahubproject.io/docs/deploy/kubernetes/>
  - Chart: <https://github.com/acryldata/datahub-helm>


## DataHub 사용방법
아래부터는 DataHub를 사용하는 순서에 대한 설명입니다. 설명에 사용되는 데이터들은 DataHub에서 제공되는 데모 페이지(<https://demo.datahubproject.io/>)를 기반으로 작성하였습니다.

DataHub를 사용하는 순서는 다음과 같습니다.

1. 데이터 소스 연결
2. 데이터 정의 작성
3. 데이터 Lineage 설정
4. 거버넌스 정의

<br>

### 1. 데이터 소스 연결
![Create new source](/assets/images/posts/2023-12-04-introducing-datahub/new_source_connection.png){: .align-center}
**Source 연결**
{: .text-center}
Ingestion에 들어가면 신규 Source를 추가할 수 있는 버튼을 확인할 수 있습니다. 눌러보면 다양한 타입의 Source가 보이는데, 없어도 Custom을 눌러서 직접 입력해서 사용 가능한 타입이 많기 때문에, <https://datahubproject.io/docs/>에 들어가서 필요한게 지원되는지 직접 확인해보는게 좋습니다.

![Choose source type](/assets/images/posts/2023-12-04-introducing-datahub/new_source_connection_choose_type.png){: .align-center}
**Source 종류**
{: .text-center}

연결이 되면 DataHub에서 데이터를 알아서 전부 읽어서 스키마와 스키마 버전을 표시하게 됩니다.


### 2. 데이터 정의 작성
데이터 소스를 연결하면 DataHub가 데이터를 읽고 스키마를 자동으로 정의하는데요. 메인 페이지에 `Explore your data`를 보면 Datasets가 생겨있는 것을 볼 수 있고, 눌러보면 연결된 DB 리스트를 확인할 수 있습니다.

![DB List](/assets/images/posts/2023-12-04-introducing-datahub/datasets_db_list.png){: .align-center}
**추가된 DB List**
{: .text-center}

여기서 DB를 클릭하면 Table 목록을 확인할 수 있고, 특정 Table을 누르면 정의된 스키마를 확인할 수 있습니다. 여기 보이는 Table에 Description을 정의할 수 있고, Tags를 만들어서 추가할 수 있습니다. Glossary Terms는 용어를 정의할 때 쓰는 부분인데, 책의 뒷부분에 정의되는 것과 비슷한 것이라고 생각하면 됩니다.

그 외에도 오른쪽에 About으로 Table에 대한 내용(About)을 작성하거나, 담당자(Owners), Tags, Glossary Terms, Domain 등을 정의할 수 있습니다. Domain은 주제에 따라 구분해서 보기 위해 사용하는 것인데, 거버넌스 정의 부분에서 다시 설명하겠습니다.

![Schema](/assets/images/posts/2023-12-04-introducing-datahub/schema_page.png){: .align-center}
**Schema 정의**
{: .text-center}

<br>

### 3. 데이터 Lineage 설정
스키마 화면에서 오른쪽 위에 Lineage 버튼을 누르면 Lineage를 시각적으로 확인할 수 있습니다. 어떤 곳 혹은 데이터를 통해서 생성된 데이터인지, 이 데이터로 어떤 데이터가 생성되는지 등의 정보를 한눈에 확인할 수 있습니다.

![Lineage example](/assets/images/posts/2023-12-04-introducing-datahub/lineage_example.png){: .align-center}
**Lineage 예시**
{: .text-center}

<br>

### 4. 거버넌스 정의
...




## Reference
- <https://datahubproject.io/docs/features/#datahub-administration>
- 