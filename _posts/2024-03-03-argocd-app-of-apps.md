---
title: ArgoCD의 App of Apps 패턴
categories:
  - ArgoCD
tags:
  - ArgoCD
  - App of Apps
# published: false
---

ArgoCD는 쿠버네티스를 위한 대표적인 선언적, GitOps 기반의 CD(Continuous Delivery) 도구입니다. 배포할 쿠버네티스 서비스의 deployment, service, ingress 등을 정의하고 서비스 Repository에서 GitOps 기반의 배포 파이프라인이 동작하면 ArgoCD를 통해서 서비스가 배포됩니다.

이때 배포에 필요한 ArgoCD Application은 사용자가 ArgoCD 웹에 직접 들어가서 정보를 입력하고 배포하는 방식이 일반적입니다. 이렇게 사용하면 앞에서 해놓은 쿠버네티스 서비스의 선언적 관리와 GitOps 기반의 배포 파이프라인까지는 잘 해놓고 ArgoCD Application 배포만 관리하지 않게 되는 이상한(?) 일이 발생합니다.

이러한 Application의 선언적 관리를 할 수 있는 방법 중 하나가 `App of Apps` 패턴입니다.


## App of Apps 패턴
App of Apps 패턴은 ArgoCD의 여러 Application을 선언적으로 관리 및 배포하는 방법 중 하나입니다. 서비스를 배포할 application들을 yaml로 작성하고 application들을 배포할 application 하나를 생성 및 이용해서 배포하는 방식으로 동작합니다.

![ArgoCD Application Delivery Example](/assets/images/posts/2024-03-03-argocd-app-of-apps/argocd_app_of_apps_example1.png){: .align-center}
**ArgoCD App of Apps Example** 
{: .text-center}
(출처: https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-bootstrapping/)
{: .text-center}

<br>

### 장점

이러한 방법으로 배포했을 때의 장점을 정리해보면 다음과 같습니다.

1. **Application Templatization**
  - Application을 배포할 때 ArgoCD 웹에서 진행하면 매번 직접 작성해야하기 때문에 많이 번거롭고 실수가 발생할 수 있습니다. App of Apps 패턴은 이러한 단점을 없애주고 공통 설정(ex. label, syncPolicy)을 가져갈 수 있습니다.
2. **쉽고 빠른 배포**
  - 모든 Application들이 선언형으로 관리되어있기 때문에 ArgoCD를 잘 모르는 사람이라도 쉽고 빠르게 배포할 수 있고, 배포 과정을 자동화할 수도 있습니다.
3. **Cluster별 Application 배포**
  - Application Templatization을 하면서 Cluster별로 동일한 Application을 배포할 수 있습니다. 또한 특정 Cluster를 선택해서 배포하거나 일부만 설정을 다르게 적용해서 배포할 수도 있습니다.

<br>




## Reference
- https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-bootstrapping/