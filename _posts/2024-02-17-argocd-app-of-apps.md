---
title: ArgoCD의 App of Apps 패턴
categories:
  - ArgoCD
tags:
  - ArgoCD
  - App of Apps
published: false
---

ArgoCD는 쿠버네티스를 위한 대표적인 선언적, GitOps 기반의 CD(Continuous Delivery) 도구입니다. 배포할 쿠버네티스 서비스의 deployment, service, ingress 등을 정의하고 서비스 Repository에서 GitOps 기반의 배포 파이프라인이 동작하면 ArgoCD를 통해서 서비스가 배포됩니다.

이때 배포에 필요한 ArgoCD Application은 사용자가 ArgoCD 웹에 직접 들어가서 정보를 입력하고 배포하는 방식이 일반적입니다. 이렇게 사용하면 앞에서 해놓은 쿠버네티스 서비스의 선언적 관리와 GitOps 기반의 배포 파이프라인까지는 잘 해놓고 ArgoCD Application 배포만 관리하지 않게 되는 이상한(?) 일이 발생합니다.

이러한 Application의 선언적 관리를 할 수 있는 방법 중 하나가 `App of Apps` 패턴입니다.


## App of Apps 패턴
App of Apps 패턴은 여러 개의 Application을 선언적으로 관리하는 방법 중 하나입니다. 