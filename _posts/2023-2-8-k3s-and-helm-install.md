---
title: K3s 및 Helm 설치
toc: true
categories:
  - Kubernetes
tags:
  - k3s
  - helm
---

## K3s 설치
```bash
$ curl -sfL https://get.k3s.io | sh -

# 설치 확인
$ sudo kubectl getn node  
NAME     STATUS   ROLES                  AGE     VERSION
ubuntu   Ready    control-plane,master   8m42s   v1.25.6+k3s1
```

<br>

## Helm 설치
```bash
# 설치된 k3s 버전(위 1.25.6)에 맞춰 알맞은 helm 버전 다운로드
# 참조: https://helm.sh/docs/topics/version_skew/
$ wget https://get.helm.sh/helm-v3.11.0-linux-amd64.tar.gz 
$ tar -zxvf helm-v3.11.0-linux-amd64.tar.gz
$ sudo mv linux-amd64/helm /usr/local/bin/helm

# 권한 이슈를 다 무시하기 위해 여기부터 root로 진행
$ sudo su

# root의 bashrc에 KUBECONFIG 환경변수 추가
$ vi ~/.bashrc
...
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
$ source ~/.bashrc

# 설치 확인
$ helm list -A
NAME       	NAMESPACE  	REVISION	UPDATED                                	STATUS  	CHART                      	APP VERSION
traefik    	kube-system	1       	2023-02-08 12:30:12.650553286 +0000 UTC	deployed	traefik-20.3.1+up20.3.0    	v2.9.4     
traefik-crd	kube-system	1       	2023-02-08 12:30:10.90446012 +0000 UTC 	deployed	traefik-crd-20.3.1+up20.3.0	v2.9.4
```