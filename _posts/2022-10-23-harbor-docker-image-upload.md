---
title: Harbor에 Docker image 업로드 방법
toc: true
categories:
  - Docker
tags:
  - docker
  - harbor
  - image
  - Push
  - tag
---

로컬 환경에서 만든 Docker image를 Horbor에 업로드하려면 **tag를 변경하고 업로드**하면 됩니다.

```bash
$ docker image tag test_image:0.1 HARBOR_REGISTRY_URL/test/test_image:0.1
$ docker push HARBOR_REGISTRY_URL/test/test_image:0.1
```
