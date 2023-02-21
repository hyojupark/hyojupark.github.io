---
title: Kubernetes Label 목록 보기, 추가/삭제하기
toc: true
categories:
  - Kubernetes
tags:
  - key
  - kubectl get nodes
  - kubectl label nodes
  - kubernetes
  - Label
  - Node
  - nodeaffinity
  - show-labels
  - VALUE
---

**Kubernetes**에서 `NodeAffinity`를 적용하기 위해 반드시 필요한 내용이 바로 Node에 적용된 **Label 목록 확인 혹은 추가**입니다.

방법은 다음과 같습니다.

## **Node의 Label 보기**

```bash
$ kubectl get nodes --show-labels
NAME      STATUS    ROLES    AGE     VERSION        LABELS
worker0   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker0
worker1   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker1
worker2   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker2
```

<br>

## **Node에 Label 추가**

- kubectl label nodes <your-node-name> <key>=<value>

```bash
$ kubectl label nodes worker0 disktype=ssd
```

<br>

## **Node의 Label 삭제**

- kubectl label nodes <your-node-name> <key>-

```bash
$ kubectl label nodes worker0 disktype-
```

<br>

## **Reference**

- <https://kubernetes.io/docs/tasks/configure-pod-container/assign-pods-nodes/>
