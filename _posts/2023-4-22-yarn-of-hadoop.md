---
title: Hadoop의 Yarn
categories:
  - Hadoop
tags:
  - Hadoop
  - Yarn
# published: false
---

## Yarn의 등장 배경
Hadoop의 초기 버전(1.0)은 MapReduce를 기반으로 분산 데이터 처리를 수행하는데 중점을 두었습니다. 이러한 구조는 MapReduce 작업이 많은 양의 리소스를 소모하여 여러 작업이 동시에 실행되기 어려웠고, 다양한 유형의 작업을 처리하기 어렵고, 확장성이 떨어졌습니다.

이러한 단점을 해결하기 위해 Hadoop 2.0에서는 Yarn을 도입했습니다.

![hadoop1-vs-hadoop2](/assets/images/posts/2023-4-9-yarn-of-hadoop/hadoop1_vs_hadoop2.png){: .align-center}
**Hadoop 1.0 vs Hadoop 2.0**
{: .text-center}


## Yarn의 구성 요소
Yarn의 기본 아이디어는 **리소스 관리(Resource Management)**와 **작업 스케줄링/모니터링(Job Scheduling/Monitoring)**을 <u>별도의 데몬으로 분리</u>하는 것입니다.

YARN은 다음과 같은 구성 요소로 구성됩니다.
1. ResourceManager
  - <u>클러스터의 리소스 관리를 담당</u>합니다. 리소스 요청에 응답하고, 클러스터 내의 자원 할당을 조정합니다. 또한 실행 중인 애플리케이션의 상태를 모니터링하고, 실패한 애플리케이션을 재시작합니다.

2. NodeManager
  - 각 노드에서 실행되며, <u>노드의 리소스 사용량 및 상태를 관리</u>합니다. NodeManager는 ResourceManager로부터 할당된 컨테이너의 실행을 관리하며, 컨테이너 내에서 애플리케이션을 실행합니다.

3. ApplicationMaster
  - 각 애플리케이션마다 생성되며, <u>자원 할당 및 애플리케이션 실행을 담당</u>합니다. ApplicationMaster는 NodeManager와 ResourceManager 사이에서 통신하며, 애플리케이션 실행 중에 발생하는 문제를 해결합니다.

4. Container
  - 각 애플리케이션의 실행 단위로, 실행되는 <u>애플리케이션의 코드와 실행 환경을 포함</u>합니다. 컨테이너는 NodeManager에서 실행되며, 자원 요청 및 할당, ApplicationMaster 혹은 Task의 실행 및 종료를 합니다. 

<br>

## Yarn Architecture

위의 각 구성요소를 포함하는 아키텍처의 모습은 아래와 같습니다.

![yarn-architecture](/assets/images/posts/2023-4-9-yarn-of-hadoop/yarn_architecture.png){: .align-center}
**Yarn Architecture**
{: .text-center}

![yarn-architecture-flow](/assets/images/posts/2023-4-9-yarn-of-hadoop/yarn_architecture_flow.png){: .align-center}
**Yarn Architecture Flow**
{: .text-center}

아키텍처의 흐름을 보면 아래와 같습니다.
1. `Client`가 `ResourceManager`에 `Job`을 전달하면 노드에 `Container`를 생성하고 `ApplicationMaster`를 실행합니다. 
2. 실행된 `ApplicationMaster`가 `ResourceManager`에 자원을 요청하면 `ResourceManager`는 여러 노드에 걸쳐 `Container`를 생성하고 `Task`를 할당합니다. 
3. 이제 `ApplicationMaster`는 각각의 `NodeManager`를 통해 `Container`의 `Task`를 실행합니다. 
4. `ApplicationMaster`는 실행중인 `Task`를 모니터링하다 완료되면 `ResourceManager`에 알리고 `Client`에 결과를 전달하면서 `Job`을 종료합니다.

<br>

## Reference
- <https://www.hdfstutorial.com/blog/hadoop-1-vs-hadoop-2-differences/>
- <https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/YARN.html>
- Bunjamin Memishi, María Pérez, Gabriel Antoniu. Feedback-Based Resource Allocation in MapReduce-Based Systems. Scientific Programming, 2016, 2016, pp.1-13. ⟨[10.1155/2016/7241928](https://www.hindawi.com/journals/sp/2016/7241928/)⟩. ⟨[hal-03360807](https://hal.science/hal-03360807)⟩