---
title: Elasticsearch reindex 특정 기간만 복제
toc: true
categories:
  - Elastic Stack
tags:
  - date
  - Elasticsearch
  - GTE
  - LTE
  - Now
  - Query
  - RANGE
  - reindex
  - Timestamp
  - TIME_ZONE
---

Elasticsearch 기능을 테스트하면서 `reindex`를 종종 사용하고있는데, 원본 index의 document가 아무래도 많다보니까 그냥 `reindex`를 하면 불필요하게 많은 document가 복제되었습니다. 이 부분을 `reindex` 시 query로 **filtering**해서 <u>일부분만 복제</u>가 가능합니다.

```json
POST _reindex
{
  "source": {
    "index": "my-index-000001",
    "query": {
      "bool" :{
        "filter": {
          "range": {
            "@timestamp": {
              "gte": "2022-09-01",
              "lte": "2022-09-01",
              "time_zone": "+09:00"
            }
          }
        }
      }
    }
  },
  "dest": {
    "index": "my-new-index-000001"
  }
}
```

이런식으로 query를 주면 2022년 9월 1일 하루치만 복제할 수 있습니다. 최근 24시간으로 할 경우에도 아래와 같이 query를 주면 가능합니다.

```json
POST _reindex
{
  "source": {
    "index": "my-index-000001",
    "query": {
      "bool" :{
        "filter": {
          "range": {
            "@timestamp": {
              "gte": "now-24h",
            }
          }
        }
      }
    }
  },
  "dest": {
    "index": "my-new-index-000001"
  }
}
```
