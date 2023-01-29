---
title: Elasticsearch 외부 공개
toc: true
categories:
  - Elastic Stack
tags:
  - Elasticsearch
  - external
  - Security
  - xpack
---

윈도우에서 WSL을 사용하거나 분산 서버 환경을 구축했을 경우, elasticsearch에 접근하기 위해서는 network 설정이 필요(default host가 127.0.0.1이기 때문)합니다. 따라서 외부에서 접근 가능한 IP로 변경이 필요한데, IP를 변경하면 production 모드로 실행되기 때문에 추가 설정이 필요합니다.


 



```
...
network.host: 0.0.0.0
http.port: 9200
...
discovery.seed_hosts: ["127.0.0.1"]
cluster.initial_master_nodes: ["127.0.0.1"]
...
```

 


IP를 0.0.0.0으로 설정하면 IP가 변경되도 설정을 변경하지 않아도 됩니다. discovery.seed\_hosts와 cluster.initial\_master\_nodes는 분산 환경 구축 시 각 노드 별 설정 정보에 대한 내용인데, 단일 노드는 위와 같이 설정해주고, 분단 환경에서는 각 의미에 맞춰 설정하면 됩니다.


 


참고로 **xpack.security.enable** 옵션을 **false**로 주면 마찬가지로 동작하지 않으니 이전 [X-Pack Security  적용 포스트](https://hyostech.tistory.com/12)를 참고해서 적용해놓도록 합니다.

