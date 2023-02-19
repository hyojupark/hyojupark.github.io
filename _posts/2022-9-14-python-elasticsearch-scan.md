---
title: Python에서 Elasticsearch 대량 조회 (elasticsearch.helpers.scan)
toc: true
categories:
  - Elastic Stack
tags:
  - Elasticsearch
  - helpers.scan
  - Python
  - scan
  - scroll
---

**Elasticsearch**에는 **Scroll API**를 사용하면 대량의 데이터를 조회할 수 있는데, 이 부분을 python에서 `elasticsearch.helpers.scan`을 이용해 사용 가능합니다.

```python
from elasticsearch import Elasticsearch, helpers

es = Elasticsearch(host='127.0.0.1')
query = {
	'query': {
    	'match_all': {}
    }
}

result_gen = helpers.scan(client=es, query=query, index='test_index')
result_list = [result for result in result_gen]
```

<u>default scroll size는 1000</u>으로 설정되어있어 1000개씩 끊어서 generator가 데이터를 조회합니다. <u>데이터의 순서는 보장되지 않기 때문에 정렬이 필요할 경우 별도의 후처리가 필요</u>합니다.

<br>

## **Reference**

* <https://elasticsearch-py.readthedocs.io/en/7.10.0/helpers.html>
