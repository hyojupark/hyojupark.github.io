---
title: Lens로 Kubernetes 자원 사용량 클릭만으로 쉽게 보기
toc: true
categories:
  - Kubernetes
tags:
  - kube-state-metrics
  - kubernetes
  - lens
  - lens-metrics
  - Metrics
  - monitoring
  - Node-exporter
  - OpenLens
  - prometheus
---

Kubernetes에서 사용중인 자원 사용량을 **Lens**를 사용하면 클릭만으로 쉽게 볼 수 있습니다. **Lens**는 <u>Kubernetes 전용 관리도구</u>로, 관리에 필요한 기능과 다양한 플러그인을 함께 지원합니다. 

**Lens**는 개인 혹은 연 수익 $10M미만 기업에 무료로 제공되므로, 무료 사용이 어려운 경우 **OpenLens**([링크](https://github.com/MuhammedKalkan/OpenLens/releases))를 받아서 사용하면 됩니다. (Release에서 OS에 맞는 파일을 받아서 설치하면 됩니다.) 

<br>

**Lens** 설치 후 Kubernetes와 연결하기 위해 config 파일을 가져와야합니다. 이때 쓰기권한이 있는 config를 사용해야합니다. 서버에 접속해서 `~/.kube/config`에 있는 파일을 가져와 **Catalog** -> **Clusters**에 추가해줍니다. 추가하고 접속한 뒤에 아래처럼 추가한 클러스터 이름을 클릭해서 Settings에 들어갑니다.

![lens cluster click](/assets/images/posts/2022-11-19-lens-show-kubernetes-resource/img-1.png){: .align-center}

그리고 **Extensions**에 **Lens Metrics**를 들어갑니다.

![lens settings](/assets/images/posts/2022-11-19-lens-show-kubernetes-resource/img-2.png){: .align-center}
![lens metrics](/assets/images/posts/2022-11-19-lens-show-kubernetes-resource/img-3.png){: .align-center}

들어가면 보이는 항목을 모두 체크하고 Apply를 누릅니다. 그러면 해당 Kubernetes Cluster에 `lens-metrics` namespace가 추가되면서 metric 수집에 필요한 `prometheus`, `kube-state-metrics`, `node-exporter` **Pod**가 실행됩니다.

<br>

마지막으로 **Settings** -> **Metrics**에 Prometheus로 Lens가 사용중인지 확인합니다.

![lens metrics 2](/assets/images/posts/2022-11-19-lens-show-kubernetes-resource/img-4.png){: .align-center}

<br>

이제 클러스터에 들어가보면 사용중인 자원을 확인할 수 있습니다.

![lens cluster resources](/assets/images/posts/2022-11-19-lens-show-kubernetes-resource/img-5.png){: .align-center}
**결과 화면**
{: .text-center}