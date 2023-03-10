---
title: Kubernetes GPU 할당 상태 확인 (krew view-allocations)
toc: true
categories:
  - Kubernetes
tags:
  - GPU
  - krew
  - kubernetes
  - plugin
  - view-allocations
---

Kubernetes GPU 할당 상태는 krew(Kubernetes Plugin Manager)로 **view-allocations plugin**을 설치해서 확인할 수 있습니다.

## **krew 설치**

```bash
user@node1:~$ sudo apt install git

user@node1:~$ (
  set -x; cd "$(mktemp -d)" &&
  OS="$(uname | tr '[:upper:]' '[:lower:]')" &&
  ARCH="$(uname -m | sed -e 's/x86_64/amd64/' -e 's/\(arm\)\(64\)\?.*/\1\2/' -e 's/aarch64$/arm64/')" &&
  KREW="krew-${OS}_${ARCH}" &&
  curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/${KREW}.tar.gz" &&
  tar zxvf "${KREW}.tar.gz" &&
  ./"${KREW}" install krew
)

user@node1:~$ echo 'export PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH"' >> ~/.bashrc

user@node1:~$ source ~/.bashrc
```

<br>

## **view-allocations plugin 설치 및 확인**

```bash
user@node1:~$ kubectl krew install view-allocations

user@node1:~$ kubectl view-allocations -r gpu
 Resource                              Requested      Limit  Allocatable  Free
  nvidia.com/gpu                       (33%) 2.0  (33%) 2.0          4.0   2.0
  └─ node1                           (33%) 2.0  (33%) 2.0          4.0   2.0
     ├─ service1-5857475c4b-q6lvm          1.0        1.0           __    __
     └─ service2-7d7d995df-ltfcv           1.0        1.0           __    __
```

<br>

## **Reference**

* <https://krew.sigs.k8s.io/docs/user-guide/setup/install/>
* <https://github.com/davidB/kubectl-view-allocations>
