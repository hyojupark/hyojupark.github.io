---
title: Hadoop과 Yarn
categories:
  - Hadoop
tags:
  - Hadoop
  - Yarn
published: false
---

Hadoop 2.0부터 Yarn이 도입됨

Resource Management와 Job Scheduling 및 Monitoring을 분리된 데몬에서 처리
이를 위해 Resource Manager(RM)와 Application Master(AM) 구조 적용

크게 아래 구성요소 존재
- Resource Manager
- Node Manager
- Application Master
- Container



## Yarn의 등장 배경
Hadoop의 초기 버전(1.0)은 MapReduce를 기반으로 분산 데이터 처리를 수행하는 데 중점을 두었습니다. 이러한 구조는 MapReduce 작업이 많은 양의 리소스를 소모하여 여러 작업이 동시에 실행되기 어려웠고, 다양한 유형의 작업을 처리하기 어렵고, 확장성이 떨어졌습니다.

이러한 단점을 해결하기 위해 Hadoop 2.0에서는 Yarn을 도입했습니다.

![hadoop1-vs-hadoop2](/assets/images/posts/2023-4-9-yarn-of-hadoop/hadoop1-vs-hadoop2.png){: .align-center}
**Hadoop 1.0 vs Hadoop 2.0**
{: .text-center}


## Yarn의 구성 요소
Yarn의 기본 아이디어는 리소스 관리(Resource Management)와 작업 스케줄링/모니터링(Job Scheduling/Monitoring)을 별도의 데몬으로 분리하는 것입니다.

YARN은 다음과 같은 구성 요소로 구성됩니다.
1. ResourceManager
  - 클러스터에서 전반적인 리소스 관리를 담당합니다. 리소스 요청에 응답하고, 클러스터 내의 자원 할당을 조정합니다. 또한 실행 중인 애플리케이션의 상태를 모니터링하고, 실패한 애플리케이션을 재시작합니다.

2. NodeManager
  - 각 노드에서 실행되며, 노드에서의 리소스 사용량 및 상태를 관리합니다. NodeManager는 ResourceManager로부터 할당된 컨테이너의 실행을 관리하며, 컨테이너 내에서 애플리케이션을 실행합니다.

3. ApplicationMaster
  - 각 애플리케이션마다 하나의 ApplicationMaster가 생성되며, 자원 할당 및 애플리케이션 실행을 담당합니다. ApplicationMaster는 NodeManager와 ResourceManager 사이에서 통신하며, 애플리케이션 실행 중에 발생하는 문제를 해결합니다.

4. Container
  - 각 애플리케이션의 실행 단위로, 실행되는 애플리케이션의 코드와 실행 환경을 포함합니다. 컨테이너는 NodeManager에서 실행되며, 자원 요청 및 할당, 실행 및 종료를 처리합니다.



