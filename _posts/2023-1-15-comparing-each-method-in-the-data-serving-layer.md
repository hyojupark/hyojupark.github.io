---
title: 데이터 제공 Layer의 각 방법 비교(RESTful API, Direct Connection, Data Forward Service)
toc: true
categories:
  - ETL
tags:
  - Data Forward Service
  - data lake
  - Data Serving Layer
  - data warehouse
  - Direct Connection
  - fluentbit
  - JDBC
  - RESTful API
  - 데이터 분석
  - 데이터 제공
---

**데이터 제공 Layer**는 데이터 소스(Data Lake, Data Warehouse) 내부의 데이터를 가공해서 서비스 애플리케이션, 분석 애플리케이션, 각종 데이터 분석 도구 등에 제공하는 Layer입니다. 이 Layer에서 데이터를 제공하기 위해 사용하는 방법으로 크게 세 가지(**RESTful API**, **Direct Connection**(ex. JDBC), **Data Forward Service**(ex. fluentbit))가 있습니다.

![Data Serving Flow](/assets/images/posts/2023-1-15-comparing-each-method-in-the-data-serving-layer/img-1.png){: .align-center}
**Data Serving Flow**
{: .text-center}

각각의 방법은 서로 장단점을 가지고 있어 한가지 방법만 사용하면 효과적인 구조를 설계할 수 없습니다. 아래 설명을 기준으로 최대한 단점 없이 장점을 가져올 수 있는 방법을 선택해서 적절한 위치에 적용해야 합니다.

<br>

## **1. RESTful API**

도메인 기반의 CRUD 요청을 처리하는 RESTful API의 특성을 이용해 **도메인 기반으로 데이터를 API로 요청**하면 서버에서 JSON 포맷으로 데이터를 전달하는 방식

### **장점**

1. 클라이언트에서 요청할 데이터의 자세한 기술 구조를 몰라도 쉽게 데이터 요청 가능
2. 내부 기술 구조가 공개되지 않아 보안 유지 가능
3. 클라이언트 요청 통제 가능

### **단점**

1. 클라이언트에서 요청할 때 마다 원하는 형태의 API 추가 필요
2. API 요청 주기에 따라 Data Source에 과부하를 줄 가능성이 존재

<br>

## **2. Direct Connection**

JDBC, ODBC 등의 Database Connectivity(데이터베이스 인터페이스)를 이용해 **데이터베이스에 직접 연결**해서 **필요한 데이터를 요청**하고 가져오는 방식

### **장점**

1. 클라이언트에서 직접 필요한 데이터를 원하는 형식으로 수집 가능

### **단점**

1. Data Source에 직접적으로 영향을 줘 과부하를 줄 가능성이 존재
2. 클라이언트에서 내부 기술 구조를 알고 복잡한 쿼리를 직접 작성

<br>

## **3. Data Forward Service**

Filebeat, fluentbit등의 데이터 수집기를 이용해서 주기적으로 **Data Source의 데이터**를 클라이언트에서 **지정한 데이터 저장소 혹은 데이터베이스로 전송**하는 방식

### **장점**

1. 클라이언트에서 직접 필요한 데이터를 원하는 형식으로 수집 가능
2. Data Source 영향도 없음

### **단점**

1. 데이터를 이중으로 저장 (저장 공간을 2배로 사용)
2. 클라이언트에서 내부 기술 구조를 알고 복잡한 쿼리를 직접 작성

<br>

## **Reference**

- 윤선웅, 『차세대 빅데이터 플랫폼 DATA LAKE』, 좋은땅(2021), 96p
