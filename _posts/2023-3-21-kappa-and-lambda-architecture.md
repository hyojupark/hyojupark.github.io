---
title: 카파, 람다 아키텍처 비교 (Kappa vs Lambda)
categories:
  - ETL
tags:
  - Data
  - DataOps
---

## 카파 아키텍처(Kappa Architecture)

![kappa architecture](/assets/images/posts/2023-3-21-kappa-and-lambda-architecture/kappa_architecture.png){: .align-center}
**Kappa Architecture**
{: .text-center}

- `실시간 스트리밍 데이터 처리`를 위한 아키텍처
- 실시간으로 수집한 데이터를 기반으로 필요에 따른 다양한 서비스를 제공하는 구조

### 장점
1. **Simplicity(단순함)**
  - 비교적 쉽고 단순한 구조의 아키텍처
2. **Operation Efficiency(운영 효율적)**
  - 람다 아키텍처에 비해 구조가 단순하기 때문에 구축 및 운영 비용이 비교적 효율적
3. **Data Consistency(데이터 일관성)**
  - 실시간 데이터를 중심으로 처리하기 때문에 데이터의 일관성을 유지하는 등의 품질 측면에서 유리

### 단점
1. **Data Dependency(데이터 의존성)**
  - Real-Time 데이터에 의존적이기 때문에 유연하고 확장성있는 다양한 서비스가 어려움
  - 스트리밍 데이터 외의 데이터 처리 아키텍처 필요


## 람다 아키텍처(Lambda Architecture)

![lambda architecture](/assets/images/posts/2023-3-21-kappa-and-lambda-architecture/lambda_architecture.png){: .align-center}
**Lambda Architecture**
{: .text-center}

- `실시간 데이터와 배치 데이터 처리를 모두 수용`하기 위한 아키텍처
- 실시간으로 수집한 데이터 외에 별도로 배치 데이터를 수집해 다양한 서비스를 제공하는 구조

### 장점
1. **Scalability(확장성)**
  - 실시간/배치 처리를 모두 고려했기 때문에 확장성이 높음
  - 실시간/배치 처리를 별도로 하기 때문에 데이터 병목현상이 발생할 가능성이 낮음
2. **Fault-Tolerance(내결함성)**
  - 데이터를 분산/복제 저장함으로써 성능 및 내구성이 좋음
3. **Flexibility(유연성)**
  - 실시간/배치 아키텍처가 각각 필요한 환경에도 적용 가능
  - 사용자의 목적에 따라 다양한 데이터 서비스 가능

### 단점
1. **Complexity(복잡성)**
  - 카파 아키텍처에 비해 복잡한 아키텍처 구조
  - 카파 아키텍처에 비해 구축 및 운영 비용이 상대적으로 높음
2. **Data Discrepancies(데이터 불일치)**
  - 데이터를 분산/복제 저장하기 때문에 데이터 일관성 등의 품질 유지가 어려움
3. **Architecture Lock-In**
  - 카파 아키텍처에 비해 각 요소가 분산되어있어 기술 스택 변경이 어려울 수 있음


## **Reference**
- <https://nexocode.com/blog/posts/lambda-vs-kappa-architecture/>
- <https://www.kai-waehner.de/blog/2021/09/23/real-time-kappa-architecture-mainstream-replacing-batch-lambda/>
- 윤선웅, 『차세대 빅데이터 플랫폼 DATA LAKE』, 좋은땅(2021), p63-129.