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

1. minikube 및 helm 설치
2. lens 설치 및 연동

## 1. minikube 및 helm 설치

### docker 설치

```bash
sudo apt update

sudo apt install apt-transport-https ca-certificates curl software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt install docker.io
sudo usermod -aG docker $USER && newgrp docker

sudo service docker status
sudo service containerd status
```

설치 후 `docker`와 `containerd` status가 active로 나오면 정상 설치된 것입니다.


### minikube 설치

```bash
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
chmod +x minikube
sudo install minikube /usr/local/bin/

cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sudo sysctl --system

sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt install kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

minikube start --network-plugin=cni --cni=bridge --container-runtime=containerd --bootstrapper=kubeadm

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
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml

# 동작중인 pods 목록 확인
kubectl get pods -A
```

### helm 설치

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh

helm version
```

여기까지 진행됐으면 minikube 및 helm이 잘 설치된 것입니다.


## 2. lens 설치 및 연동
minikube의 addons를 이용하면 metric 수집 및 dashboard를 쉽게 확인할 수 있지만, Pod에 쉽게 붙고 보기 편한 Lens를 이용하겠습니다.
