---
title: Elasticsearch query size limit 수정
toc: true
categories:
  - Elastic Stack
tags:
  - Elasticsearch
  - Limit
  - max_result_window
  - size
---

Elasticsearch에서 데이터를 검색하면 최대 10,000개까지 검색이 되는 것을 볼 수 있습니다. 10,000개 이상 검색이 필요한 경우에는 Scroll API([공식 문서](https://www.elastic.co/guide/en/elasticsearch/reference/8.3/scroll-api.html))를 사용하는게 맞지만, 성능 상관없이 검색만 필요한 경우 아래와 같이 limit를 수정하면 10,000개 이상 검색이 가능합니다.


 



```
PUT {index_name}/_settings
{
	"max_result_window": 20000
}
```

 


 


**Reference**


* <https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules.html>
* <https://www.elastic.co/guide/en/elasticsearch/reference/8.3/scroll-api.html>
