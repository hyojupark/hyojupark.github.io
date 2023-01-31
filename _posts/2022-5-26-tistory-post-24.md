---
title: Docker dangling image (none) 제거
toc: true
categories:
  - Docker
tags:
  - dangling
  - docker
  - image
  - Non
  - remove
---

Docker를 사용하다보면 `<none>` 이미지들이 쌓이는걸 볼 수 있습니다. 똑같은 이름:태그의 이미지를 올리거나, container가 있는 상태에서 image를 강제로 삭제한다거나 할 경우 쌓이게 되는데, 용량이나 관리하는 측면에서 정리가 필요합니다. 이럴 때 아래 명령어로 한번에 삭제가 가능합니다.

```bash
docker rmi $(docker images -f "dangling=true" -q)
```
