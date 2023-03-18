---
title: Docker image save and load
categories:
  - Docker
tags:
  - Docker
  - docker save
  - docker load
---

일반적으로 Docker는 Public(ex. DockerHub) 혹은 Private(ex. Harbor) Registry에서 Image를 가져오기 때문에 파일로 Docker Image를 옮길 일은 거의 없습니다. 하지만 아래와 같은 경우에 파일로 옮겨야할 경우가 생기게 됩니다.

1. 서로 연결되지 않는 Private Registry에서 Private Registry로 가져오는 경우
2. 폐쇠망에서 가져오려하는 경우

이럴 경우 어렵게 생각할 것 없이 Image를 파일로 저장하고(`save`) 필요한 곳으로 옮긴 뒤 Image를 불러오면(`load`) 됩니다.

### Save
**save**는 `-o` 또는 `--output` 옵션을 사용하여 Docker Image를 파일로 저장할 수 있습니다.
```bash
$ docker save -o /home/user/myimage.tar myimage:tag
```

### Load
**load**는 `-i` 또는 `--input` 옵션을 사용하여 파일로부터 Docker Image를 불러올 수 있습니다. 이때 **save**할 때 지정한 Image의 `이름:태그` 정보로 불러오게 됩니다.
```bash
$ docker load -i /home/user/myimage.tar
```
