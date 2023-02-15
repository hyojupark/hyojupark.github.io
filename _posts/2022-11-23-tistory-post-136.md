---
title: Kubernetes Pod를 특정 Node에 할당하는 방법 (NodeSelector, NodeAffinity)
toc: true
categories:
  - Kubernetes
tags:
  - Node
  - nodeaffinity
  - NodeSelector
  - Pod
---

**Deployment**에 `NodeSelector` 혹은 `NodeAffinity`를 설정하면 Pod를 배포할 때 특정 Node에 할당시킬 수 있습니다. `NodeSelector`는 **반드시** 특정 노드에만 할당시킬 때, `NodeAffinity`는 **조건에 따라** 특정 노드에 할당시킬 때 사용됩니다. 아래 예시는 각 노드에 nodegroup label을 추가했다고 가정했습니다. `kubernetes.io/hostname`과 같은 default label은 환경에따라 key값이 다를 수 있기 때문에 label을 직접 추가해서 사용하는 것을 권장합니다.

<br>

## **NodeSelector**

**NodeSelector**는 설정이 별로 없어 쉽게 적용 가능합니다. 다만 해당 Node가 장애가 발생할 경우 Pod가 할당될 Node가 없는 문제가 발생하기 때문에 잘 사용하지는 않습니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
sepc:
  ...
  template:
    metadata:
      ...
    spec:
      nodeSelector:
        nodegroup: nodegroup01
```

<br>

## **NodeAffinity**

**NodeAffinity**는 크게 두 가지를 상황에 맞춰 적용할 수 있습니다.

|  |  |
| --- | --- |
| **requiredDuringSchedulingIgnoredDuringExecution** | 해당 Node에 반드시 할당되도록 함 (다른 Node에서 동작하면 안될 때) |
| **preferredDuringSchedulingIgnoredDuringExecution** | 해당 Node에 우선시 할당되도록 함 (다른 Node에서 동작해도 될 때) |

**requiredDuringSchedulingIgnoredDuringExecution**으로 아래와 같이 설정하면 nodegroup01의 Node에 Pod를 할당하도록 합니다. 

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
sepc:
  ...
  template:
    metadata:
      ...
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: nodegroup
                operator: In
                values:
                - nodegroup01
```

**preferredDuringSchedulingIgnoredDuringExecution**으로 아래와 같이 설정하면 **weight**(1~100 사이)에 따라서 우선순위가 더 높은 nodegroup01의 Node에 우선적으로 할당하도록 합니다. nodegroup01의 Node에 할당할 수 없을 경우 nodegroup02의 Node에 할당하고, 이것도 할당할 수 없을 경우 다른 Node에 할당합니다.

위 required와 다르게 `nodeSelectorTerms`가 아닌 `preference`를 사용하는 점을 주의합니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
sepc:
  ...
  template:
    metadata:
      ...
    spec:
      affinity:
        nodeAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 50
            preference:
            - matchExpressions:
              - key: nodegroup
                operator: In
                values:
                - nodegroup01
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 20
            preference:
            - matchExpressions:
              - key: nodegroup
                operator: In
                values:
                - nodegroup02
```

<br>

## **Reference**

- <https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#nodeselector>
- <https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#node-affinity>


 

