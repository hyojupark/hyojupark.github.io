---
title: Kubernetes Node NotReady (Failed to parse kubelet flag)
toc: true
categories:
  - Kubernetes
tags:
  - kubelet
  - kubernetes
  - network-plugin
  - NotReady
  - unknown flag
---

Kubernetes에서 **Node**가 `NotReady` 상태에 빠지는 경우는 여러 종류가 있습니다. 여기서는 아래 에러의 해결 방법만 소개하겠습니다.

에러 상황은 아래와 같습니다.

```bash
$ kubectl get nodes
NAME	STATUS		ROLES	AGE	VERSION
node1	NotReady	master	120m	v1.19.15

$ systemctl status kubelet
...
Active: activating (auto-restart) (Result: exit-code) ...
...
```
<br>

**Node**의 상태가 `NotReady` 상태에 빠졌고, **kubelet**이 `activating` 상태에 빠졌습니다.
우선 에러 로그를 확인합니다. 넉넉하게 **kubelet** 로그의 마지막 300줄을 보겠습니다.

```bash
$ journalctl -u kubelet| tail -n 300
...
Jun 19 21:10:10 node1 kubelet[24182]: Error: failed to parse kubelet flag: unknown flag: --network-plugin
...
```
<br>

kubelet에서 `--network-plugin` flag를 찾지 못해서 발생하는 에러입니다. 원인은 kubelet 버전이 바뀌면서 해당 flag가 사라졌기 때문이였습니다. (`--network-plugin`은 kubelet 1.24 버전에서 삭제됐습니다.) 위에서 node의 버전이 1.19.15 버전으로는 나와있지만, 실제 kubelet 등의 버전은 1.24.0 버전으로 바뀐 상태인 걸 확인했습니다.

```bash
$ kubectl version --client
Client Version: version.Info{Major:"1", Minor:"24", GitVersion:"v1.24.0", 
GitCommit:"4ce5a8954017644c5420bae81d72b09b735c21f0", GitTreeState:"clean", 
BuildDate:"2022-05-03T13:36:49Z", GoVersion:"go1.18.1", Compiler:"gc", Platform:"linux/amd64"}
```
<br>

제가 해결한 방법은 **kubeadm**, **kubectl**, **kubelet** 세 가지 파일을 구 버전으로 다운그레이드하는 방법이였습니다. `/usr/bin`에 **kubeadm**, **kubectl**, **kubelet** 세 가지 파일의 날짜가 모두 최근 날짜로 변경된 것을 확인하여, 세 가지 모두 구 버전을 받아 바꿔치기했습니다.
저는 기존에 **1.19.15**를 사용중이였기 때문에 해당 버전으로 받았습니다.

```bash
$ mkdir kube_exec
$ cd kube_exec

$ wget https://storage.googleapis.com/kubernetes-release/release/v1.19.15/bin/linux/amd64/kubeadm
$ wget https://storage.googleapis.com/kubernetes-release/release/v1.19.15/bin/linux/amd64/kubectl
$ wget https://storage.googleapis.com/kubernetes-release/release/v1.19.15/bin/linux/amd64/kubelet

$ sudo chmod +x kube*
$ sudo cp kube* /usr/bin

$ kubectl version --client
Client Version: version.Info{Major:"1", Minor:"19", GitVersion:"v1.19.15", 
GitCommit:"58178e7f7aab455bc8de88d3bdd314b64141e7ee", GitTreeState:"clean", 
BuildDate:"2021-09-15T19:23:02Z", GoVersion:"go1.15.15", Compiler:"gc", Platform:"linux/amd64"}
```
<br>

다시 kubectl version이 **1.19.15**로 바뀐 것을 확인했습니다. 이제 kubelet를 재시작하고 다시 node의 상태를 확인합니다.

```bash
$ kubectl get nodes
NAME	STATUS	ROLES	AGE	VERSION
node1	Ready	master	131m	v1.19.15

$ systemctl status kubelet
...
Active:  (active) (running) ...
...
```
