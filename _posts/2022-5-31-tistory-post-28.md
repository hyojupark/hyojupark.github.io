---
title: Dragonfly - Redis, Memcached의 최신 대체제
toc: true
categories:
  - Trend
tags:
  
---

![](/assets/images/posts/2022-5-31-tistory-post-28/img-1.png){: .align-center}

<https://github.com/dragonflydb/dragonfly>

**기존에 상용화된 저장소보다 훨씬 빠른 속도의 인메모리 저장소.**

Redis보다 25배 빠르다고하는 인메모리 저장소로, 최근 리눅스에 적용된 io-uring(비동기 Block I/O)을 사용해서 성능을 높힌 것으로 보입니다. Redis를 사용하고있는데 속도 문제를 겪고 있다면, 아래 장단점을 고려해서 사용해보면 좋을 것 같습니다.

### **장점**

1. Redis보다 빠른 속도와 좋은 메모리 효율
2. Redis, Memcached에서 사용 가능한 거의 모든 명령어 사용 가능

### **단점**

1. Ubuntu 20.04 이상 버전에서만 사용 가능 (io-uring API 사용을 위해 Linux 5.10 이상 버전 요구)
2. 매우 초기 버전 (2022.05.30 v0.1.0 Release)
