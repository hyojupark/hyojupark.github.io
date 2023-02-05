---
title: Elasticsearch reindex
toc: true
categories:
  - Elastic Stack
tags:
  - Elasticsearch
  - reindex
---

Elasticsearch index를 그대로 복사할 때 `reindex`를 사용합니다.

```json
POST _reindex
{
  "source": {
    "index": "{before index name}"
  },
  "dest": {
    "index": "{copied index name}"
  }
}
```

![reindex](/assets/images/posts/2022-6-28-tistory-post-66/img-1.png){: .align-center}

위 캡쳐 기준으로 2,354개의 데이터가 있는 index를 복사하는데 2.2초(2200ms)가 소요된 것을 확인할 수 있습니다. 

![reindex result](/assets/images/posts/2022-6-28-tistory-post-66/img-2.png){: .align-center}

Index Management에서 확인해보면 동일한 수의 Docs count를 확인할 수 있습니다. Storage size는 이전에 document version이 여러 번 올라가면서 삭제될 document가 아직 남아있어 새로 `reindex`된 index의 용량이 더 작을 수 있습니다.

<br>

**Reference**

* <https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-reindex.html>
