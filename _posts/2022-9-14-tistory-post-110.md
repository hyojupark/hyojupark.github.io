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

Elasticsearch에는 Scroll API를 사용하면 대량의 데이터를 조회할 수 있는데, 이 부분을 python에서 elasticsearch.helpers.scan을 이용해 사용 가능합니다.


 



```
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

 


default scroll size는 1000으로 설정되어있어 1000개씩 끊어서 generator가 데이터를 조회합니다. 데이터의 순서는 보장되지 않기 때문에 정렬이 필요할 경우 별도의 후처리가 필요합니다.


 


 


 


**Reference**


* <https://elasticsearch-py.readthedocs.io/en/7.10.0/helpers.html>
