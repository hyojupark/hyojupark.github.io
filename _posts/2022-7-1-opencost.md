---
title: OpenCost - Kubernetes 자원 사용 비용 모니터링
toc: true
categories:
  - Trend
tags:
  - Billing
  - kubernetes
  - Price
  - resource
  - Usage
---

![opencost logo](/assets/images/posts/2022-7-1-opencost/img-1.png){: .align-center}

최근 **MLOps**가 크게 중요해지면서 서버 자원을 최대한 효율적으로 사용해 비용을 절감하는 것이 크게 중요해졌습니다. 비용 절감을 하기 위해서는 어느 부분에서 비용이 많이 발생하는지 모니터링하면서 확인해야하는데, 이를 위해 **OpenCost**가 공개되었습니다.

**OpenCost**는 kubecost에서 개발되었으며, 2022년 2월 중순부터 빠르게 업데이트(현 시점 기준, 매 달 업데이트 버전 release)되고 있습니다. 

![opencost ui](/assets/images/posts/2022-7-1-opencost/img-2.png){: .align-center}
**< OpenCost 예시 화면 >**
{: .text-center}

AWS, Azure, GCP billing API에 연동하여 사용 가능하며, **Prometheus**와 연동도 가능합니다.

Kubernetes 1.8+ 버전부터 사용 가능하니 Kubernetes를 도입 예정일 경우 함께 설치해서 사용해 보는 것을 추천합니다.
