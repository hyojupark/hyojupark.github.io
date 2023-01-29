---
title: Kubernetes Pod DNS(CoreDNS) Naming Rule
toc: true
categories:
  - Kubernetes
tags:
  - coredns
  - dns
  - FQDN
  - kubernetes
  - namespace
  - service
---

Kubernetes에서 각 Service와 통신할 때 IP를 일일이 확인하고 입력하지 않습니다. 각 Service를 IP로 직접 연결하면 아래 단점이 있기 때문입니다.


 


**[단점]**


1. IP만 봐서는 어떤 Service였는지 알기 어려움 (ex. 172.1.1.2?? 이게 어떤 Service??)
2. 향후 연결할 Service의 IP를 미리 알기 어려움 (더미 IP 입력 후 연결할 Service가 생성되면 수정해야 함)
3. Service의 IP가 변경되면 연결된 Service 전체에서 IP를 변경해줘야 함


 


때문에 Kubernetes에서 각 Service를 연결할 때는 DNS를 사용합니다. Kubernetes 1.12 이상부터는 **CoreDNS** 사용을 권장하기도 하고 자동으로 설정되기 때문에 대부분 CoreDNS를 사용합니다. 이 CoreDNS로 각 Service를 연결할 때는 아래와 같은 Naming Rule을 기반으로 연결하면 됩니다.


 




|  |
| --- |
| **<Service Name>**.**<Namespace>**.svc.cluster.local |


 


일반적인 Kubernetes 구성에서는 위 형태를 벗어나지 않기 때문에 Service 이름과 Namespace 이름만 알면 Kubernetes에서 DNS를 사용할 수 있습니다.


추가로 Service를 찾을 때는 Namespace까지 구분해서 찾아야하는데 이러한 유형의 DNS를 FQDN(Full Qualified Domain Name)이라고 합니다.


 


다시봐도 정말 쉬운 내용이지만, 매번 기억나지 않아 기록으로 남겨봅니다. 😂


 


 


 


**Reference**


* <https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/>


 


 

