---
title: Mac에서 Minikube 설치하기
toc: true
categories:
  - kubernetes
tags:
  - minkube
---

금방 할 줄 알았던 **Minikube** 설치에서 삽질하는 과정이 너무 길어져서 기록으로 남깁니다.. 비슷한 이슈가 있는 분들에게 참고가 됐으면 좋겠습니다.


## 설치

아래 과정을 거쳐 **Minikube**와 필요한 것들을 설치합니다. Minikube VM을 띄울 가상환경 driver로 `HyperKit`을, Minikube에서 사용할 container로 `Docker`를 설치합니다.

```zsh
% brew install hyperkit
% brew install docker
% brew install kubernetes-cli
% brew install minikube
```

<br>

## 셋업
미리 CPU와 Memory를 여유있게 설정하고 minikube를 시작합니다. 현재(2023.02.16) 기준 최신 버전인 1.26.1로 설치 시 오류가 발생해, 설치했던 reference를 참고해 1.23.1로 버전을 낮춰서 설치했습니다.

```zsh
% minikube config set cpus 6
% minikube config set memory 12g
% minikube start --kubernetes-version=v1.23.1 --driver=hyperkit
😄  Darwin 12.6.1 의 minikube v1.29.0
✨  유저 환경 설정 정보에 기반하여 hyperkit 드라이버를 사용하는 중
💾  드라이버 docker-machine-driver-hyperkit 다운로드 중 :
    > docker-machine-driver-hyper...:  65 B / 65 B [---------] 100.00% ? p/s 0s
    > docker-machine-driver-hyper...:  8.42 MiB / 8.42 MiB  100.00% 3.79 MiB p/
🔑  The 'hyperkit' driver requires elevated permissions. The following commands will be executed:

    $ sudo chown root:wheel /Users/hyojupark/.minikube/bin/docker-machine-driver-hyperkit 
    $ sudo chmod u+s /Users/hyojupark/.minikube/bin/docker-machine-driver-hyperkit 


Password:
💿  가상 머신 부트 이미지 다운로드 중 ...
    > minikube-v1.29.0-amd64.iso....:  65 B / 65 B [---------] 100.00% ? p/s 0s
    > minikube-v1.29.0-amd64.iso:  276.35 MiB / 276.35 MiB  100.00% 31.85 MiB p
👍  minikube 클러스터의 minikube 컨트롤 플레인 노드를 시작하는 중
💾  쿠버네티스 v1.23.1 을 다운로드 중 ...
    > preloaded-images-k8s-v18-v1...:  399.23 MiB / 399.23 MiB  100.00% 31.58 M
🔥  hyperkit VM (CPUs=2, Memory=4000MB, Disk=20000MB) 를 생성하는 중 ...
🐳  쿠버네티스 v1.23.1 을 Docker 20.10.23 런타임으로 설치하는 중
    ▪ 인증서 및 키를 생성하는 중 ...
    ▪ 컨트롤 플레인이 부팅...
    ▪ RBAC 규칙을 구성하는 중 ...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🔎  Kubernetes 구성 요소를 확인...
🌟  애드온 활성화 : storage-provisioner, default-storageclass
💡  kubectl 을 찾을 수 없습니다. 만약 필요하다면, 'minikube kubectl -- get pods -A'를 시도합니다.
🏄  끝났습니다! kubectl이 "minikube" 클러스터와 "default" 네임스페이스를 기본적으로 사용하도록 구성되었습니다.
```

중간에 `HyperKit` Permission 관련 이슈를 볼 수 있는데, 이 부분은 아래 확인 부분에서 이어서 진행하겠습니다.

<br>

## 확인
kubectl 명령어가 바로 사용되지 않아 minikube에서 kubectl을 사용했습니다. (사소한 이슈같아 이 부분은 그냥 넘어갔습니다.)

아래와 같이 버전 정보와 component 상태를 확인합니다.

```zsh
% minikube kubectl version
    > kubectl.sha256:  64 B / 64 B [-------------------------] 100.00% ? p/s 0s
    > kubectl:  50.65 MiB / 50.65 MiB [------------] 100.00% 22.78 MiB p/s 2.4s
Client Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.1", GitCommit:"86ec240af8cbd1b60bcc4c03c20da9b98005b92e", GitTreeState:"clean", BuildDate:"2021-12-16T11:41:01Z", GoVersion:"go1.17.5", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.1", GitCommit:"86ec240af8cbd1b60bcc4c03c20da9b98005b92e", GitTreeState:"clean", BuildDate:"2021-12-16T11:34:54Z", GoVersion:"go1.17.5", Compiler:"gc", Platform:"linux/amd64"}

% minikube kubectl get componentstatuses
NAME                 STATUS    MESSAGE                         ERROR
scheduler            Healthy   ok                              
etcd-0               Healthy   {"health":"true","reason":""}   
controller-manager   Healthy   ok
```

<br>

마지막으로 **대시보드**까지 확인되면 끝입니다. **대시보드**를 확인하기 위해선 먼저 위 `minikube start` 과정에서 발생한 이슈를 처리하고 `minikube dashboard`를 실행합니다. **대시보드** 웹 페이지가 바로 뜨면 성공입니다.
```zsh
% sudo chown root:wheel /Users/hyojupark/.minikube/bin/docker-machine-driver-hyperkit 
% sudo chmod u+s /Users/hyojupark/.minikube/bin/docker-machine-driver-hyperkit 
% minikube dashboard
🤔  Verifying dashboard health ...
🚀  Launching proxy ...
🤔  Verifying proxy health ...
...
```

<br>

## 설치 오류 발생 시
설치 중 오류 발생 시 설치돼있는 Minikube를 깨끗히 밀고 다시 설치해야합니다.
```zsh
% minikube delete --all --purge
🔥  hyperkit 의 "minikube" 를 삭제하는 중 ...
💀  "minikube" 클러스터 관련 정보가 모두 삭제되었습니다
🔥  모든 프로필이 성공적으로 삭제되었습니다
💀  Successfully purged minikube directory located at - [/Users/hyojupark/.minikube]

% minikube start --kubernetes-version=v1.23.1 --driver=hyperkit
```

<br>

## Reference

- <https://peterica.tistory.com/181>
- <https://velog.io/@ozumi/Docker-desktop-%EC%82%AD%EC%A0%9C%ED%95%98%EA%B3%A0-Hyperkit-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0>
- <https://github.com/kubernetes/minikube/issues/8997>
