---
title: PyTorch image에서 apt update 실패 (NO_PUBKEY)
toc: true
categories:
  - Deep Learning, AI
tags:
  - CUDA
  - docker
  - gpg
  - NO_PUBKEY
  - nvidia
  - pytorch
  - tensorflow
---

이 글을 작성하기 약 한달 전인 2022년 4월 27일 Nvidia에서 보안상의 이유로 CUDA 관련 GPG key가 변경되었습니다. (<https://forums.developer.nvidia.com/t/notice-cuda-linux-repository-key-rotation/212772> 참조)

때문에 기존 Docker 이미지에 git과 같은 패키지를 설치하기 위해 apt update를 실행하면 기존 key를 더 이상 사용할 수 없다고 오류가 발생합니다. 이를 해결하기 위해 기존 key를 삭제하고 새로운 key를 추가해줘야하는데, 아래 방법을 사용할 경우 wget 명령어를 찾을 수 없다는 오류가 발생합니다.

```dockerfile
RUN apt-key del 7fa2af80
RUN wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-keyring_1.0-1_all.deb
RUN dpkg -i cuda-keyring_1.0-1_all.deb
RUN apt update
```

PyTorch와 같은 image는 기본적으로 wget, curl과 같은 패키지가 설치되어있지 않기 때문에 아래 명령어로 사용해야합니다.

```dockerfile
RUN apt-key del 7fa2af80
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/3bf863cc.pub
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/7fa2af80.pub
```

Nvidia에서 언제 또 key를 바꿀지 모르기 때문에 production level에서는 필요한 패키지를 모두 설치한 image를 활용하는 것을 추천합니다.
