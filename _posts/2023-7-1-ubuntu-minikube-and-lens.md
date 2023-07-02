---
title: Ubuntu 22.04 minikube 설치 및 Lens 연동
categories:
  - Kubernetes
tags:
  - ubuntu
  - minikube
  - lens
published: false
---

이번에 새로 구매한 노트북에 Ubuntu 22.04를 설치하면서 Kubernetes 환경을 구축한 내용을 정리해봤습니다. 목표는 아래와 같습니다.

1. Minikube 및 Helm 설치
2. Lens 설치 및 연동

## 1. Minikube 및 Helm 설치

### docker 설치

```bash
$ sudo apt update

$ sudo apt install apt-transport-https ca-certificates curl software-properties-common

$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

$ echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

$ sudo apt install docker.io
$ sudo usermod -aG docker $USER && newgrp docker

$ sudo service docker status
$ sudo service containerd status
```

설치 후 `docker`와 `containerd` status가 active로 나오면 정상 설치된 것입니다.


### minikube 설치

```bash
$ curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
$ chmod +x minikube
$ sudo install minikube /usr/local/bin/

$ cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

$ cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

$ sudo sysctl --system

$ sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
$ echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

$ sudo apt install kubelet kubeadm kubectl
$ sudo apt-mark hold kubelet kubeadm kubectl

$ minikube start --network-plugin=cni --cni=bridge --container-runtime=containerd --bootstrapper=kubeadm

😄  Ubuntu 22.04 의 minikube v1.30.1
✨  자동적으로 docker 드라이버가 선택되었습니다. 다른 드라이버 목록: none, ssh
❗  With --network-plugin=cni, you will need to provide your own CNI. See --cni flag as a user-friendly alternative
📌  Using Docker driver with root privileges
👍  minikube 클러스터의 minikube 컨트롤 플레인 노드를 시작하는 중
🚜  베이스 이미지를 다운받는 중 ...
💾  쿠버네티스 v1.26.3 을 다운로드 중 ...
> preloaded-images-k8s-v18-v1...:  428.27 MiB / 428.27 MiB  100.00% 1.66 Mi
> [gcr.io/k8s-minikube/kicbase](http://gcr.io/k8s-minikube/kicbase)...:  373.53 MiB / 373.53 MiB  100.00% 1.34 Mi
🔥  Creating docker container (CPUs=2, Memory=3900MB) ...
📦  쿠버네티스 v1.26.3 을 containerd 1.6.20 런타임으로 설치하는 중
▪ 인증서 및 키를 생성하는 중 ...
▪ 컨트롤 플레인이 부팅...
▪ RBAC 규칙을 구성하는 중 ...
🔗  Configuring bridge CNI (Container Networking Interface) ...
▪ Using image [gcr.io/k8s-minikube/storage-provisioner:v5](http://gcr.io/k8s-minikube/storage-provisioner:v5)
🔎  Kubernetes 구성 요소를 확인...
🌟  애드온 활성화 : storage-provisioner, default-storageclass
🏄  끝났습니다! kubectl이 "minikube" 클러스터와 "default" 네임스페이스를 기본적으로 사용하도록 구성되었습니다.

# cni 설치 (weave)
$ kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml

# 동작중인 pods 목록 확인
$ kubectl get pods -A
kube-system    coredns-787d4945fb-svxql              1/1     Running   0               10m
kube-system    etcd-minikube                         1/1     Running   0               10m
kube-system    kube-apiserver-minikube               1/1     Running   0               10m
kube-system    kube-controller-manager-minikube      1/1     Running   0               10m
kube-system    kube-proxy-4mt5w                      1/1     Running   0               10m
kube-system    kube-scheduler-minikube               1/1     Running   0               10m
kube-system    storage-provisioner                   1/1     Running   0               10m
kube-system    weave-net-9nqst                       2/2     Running   0               12m
lens-metrics   kube-state-metrics-59584f8694-wdktp   1/1     Running   0               10m

```

설치 이후 나중에 노트북을 재부팅해야하는 경우에는 위의 `minikube start` 명령어만 실행하면 이전에 사용중이었던 Pods가 동일하게 올라갑니다.

### helm 설치

```bash
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh
에
$ helm version
version.BuildInfo{Version:"v3.12.0", GitCommit:"c9f554d75773799f72ceef38c51210f1842a1dea", GitTreeState:"clean", GoVersion:"go1.20.3"}
```

여기까지 진행됐으면 minikube 및 helm이 잘 설치된 것입니다.

<br>

## 2. Lens 설치 및 연동
minikube의 dashboard addon를 이용하면 kubernetes 내 등록된 서비스와 리소스를 쉽게 모니터링할 수 있습니다. 하지만 Lens의 dashboard가 더 이쁘기도 하고(?), 컨트롤 하는 관점에서 이점이 많기 때문에 Lens를 설치해서 연동하도록 하겠습니다.

### OpenLens 설치
Lens가 유료화되면서 일부 기능들이 유료 기능으로 빠져 커뮤니티 버전에서 쓸 수 없는 기능이 생겼습니다. 없으면 상당히 불편한 기능들이기 때문에 OpenLens를 설치하고 해당 기능들을 extension으로 추가합니다. 우선 OpenLens를 설치는 Release(<https://github.com/MuhammedKalkan/OpenLens/releases>)에서 최신버전의 `.deb`를 받아서 설치합니다.

```bash
$ wget https://github.com/MuhammedKalkan/OpenLens/releases/download/v6.4.15/OpenLens-6.4.15.amd64.deb
$ sudo dpkg -i OpenLens-6.4.15.amd64.deb
```

이제 Lens를 실행해보면 아래와 같이 cluster에 minikube가 추가된 것을 확인할 수 있습니다.
(만약 연결되지 않은 경우, labels의 file 경로와 같이 `~/.kube/config` 경로를 설정해주면 됩니다.)
가
![lens main](/assets/images/posts/2023-7-1-ubuntu-minikube-and-lens/lens_main.png){: .align-center}
**Lens Main**
{: .text-center}

### extension 추가
OpenLens에서는 Lens에서 유료 기능으로 빠진 기능 중 **node-pod-menu**와 **lens-metric**을 추가합니다. 추가 방법은 Lens에서 `Ctrl + Shift + E`를 누르고 아래 방법으로 각각 추가합니다.

#### node-pod-menu
<https://github.com/alebcay/openlens-node-pod-menu>에 올라와있는 `@alebcay/openlens-node-pod-menu`를 입력 후 Install

#### lens-metrics
lens-metrics는 <https://raw.githubusercontent.com/shilazi/openlens-lens-metrics/main/build/shilazi-openlens-lens-metrics-0.1.0.tgz>에서 파일을 받은 후 파일 경로르 추가해서 Install

extension이 전부 설치됐으면 아래와 같이 2개 extension이 추가된 것을 확인할 수 있습니다.

![extensions](/assets/images/posts/2023-7-1-ubuntu-minikube-and-lens/lens_extension_install.png){: .align-center}
**Extension 추가**
{: .text-center}

이제 metrics를 연동해야합니다. clusters에서 minikube cluster의 우측 버튼을 눌러 settings에 들어가서 Extensions에 `Lens Metrics`가 추가된 것을 확인하고 클릭합니다. 그리고 보이는 3개 체크 항목을 모두 체크하고 Apply 버튼을 눌러줍니다. 그러면 minikube에 prometheus와 node-exporter가 설치되면서 metrics 수집이 이루어집니다.

![extensions](/assets/images/posts/2023-7-1-ubuntu-minikube-and-lens/lens_metrics.png){: .align-center}
**Lens Metrics 활성화**
{: .text-center}

### 연동 확인
이제 minikube cluster에 들어가보면 Cluster에서 전체 리소스를 모니터링할 수 있고, Pods에서 Pod를 클릭했을 때 해당 Pod가 사용중인 리소스 모인니터링이 가능한 것을 확인할 수 있습니다.

그리고 추가한 node-pod-menu extension 덕에 Pod를 클릭했을 때 우측 상단에 `Pod Shell`과 `Pod Logs` 버튼이 있어, 해당 Pod에 shell로 직접 접근하거나 log를 보는 작업을 클릭만으로 바로 작업할 수 있습니다.

![extensions](/assets/images/posts/2023-7-1-ubuntu-minikube-and-lens/lens_pods.png){: .align-center}
**Pod 상세 내용**
{: .text-center}

Lens에서는 이 외에도 Service, Deployment 등의 설정 확인 및 수정과 Helm 연동 등의 기능을 지원하기 때문에 잘 활용했을 때의 이점이 많습니다.
