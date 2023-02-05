---
title: Kubernetes Pod, Service, Deployment
toc: true
categories:
  - Kubernetes
tags:
  - deployment
  - kubernetes
  - POD
  - service
---

Kubernetes에서 하나의 서비스를 운영할 때 일반적으로 **Pod**와 **Service**, **Deployment**가 운영되는 것을 확인할 수 있습니다. 아래서 각각 역할의 차이에 대해 가볍게 설명하겠습니다.

<br>

## **Pod**

**Pod**는 하나 이상의 컨테이너화된 앱(containerized app), 그리고 필요에 따라 볼륨(volume)을 가지는 그룹입니다. **Pod**는 Kubernetes에서 사용하는 <u>최소 단위</u>로, 하나의 작은 서비스라고 생각하면 됩니다.

![pod describe](/assets/images/posts/2022-7-3-tistory-post-76/img-1.png){: .align-center}
**< 출처: <https://kubernetes.io/> >**
{: .text-center}

<br>

## **Service**

**Service**는 사용자가 서비스를 사용하기 위해 <u>Pod에 접근할 수 있도록 도와주는 역할</u>(접근 정책 관리)을 합니다. 외부에서 Pod에 직접 접근할 수 있게 하거나, `Load Balancing`과 같은 역할을 수행합니다.

![service describe](/assets/images/posts/2022-7-3-tistory-post-76/img-2.png){: .align-center}
**< 출처: <https://medium.com/swlh/discovering-running-pods-by-using-dns-and-headless-services-in-kubernetes-7002a50747f4> >**
{: .text-center}

<br>

## **Deployment**

**Deployment**는 <u>Pod와 ReplicaSet의 관리 및 업데이트</u>를 합니다. 일반적인 서비스는 무중단 서비스 운영과 대량의 트래픽 수용을 위해 최소 2개 이상의 Pod를 띄워 운영하게 되는데, 이때 Pod의 수량과 업데이트 방식(`Rolling`, `Blue` `Green`, `Canary`)을 관리합니다.

![deployment describe](/assets/images/posts/2022-7-3-tistory-post-76/img-3.png){: .align-center}
**< 출처: <https://www.ianlewis.org/en/bluegreen-deployments-kubernetes> >**
{: .text-center}
