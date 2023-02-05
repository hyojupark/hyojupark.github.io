---
title: Elasticsearch termvectors로 analyzer가 적용된 document 내용 보기
toc: true
categories:
  - Elastic Stack
tags:
  - Elasticsearch
  - termvectors
---

Elasticsearch에서 그냥 search를 하면 analyzer가 적용된 내용을 확인할 수 없습니다. 이때 `termvectors`로 확인이 가능합니다.

```json
GET /{index}/_termvectors/{id}
{
	"fields": [{analyzed field}]
}
```

![field result](/assets/images/posts/2022-6-28-tistory-post-65/img-1.png){: .align-center}

<br>

**Reference**

* <https://www.elastic.co/guide/en/elasticsearch/reference/8.1/docs-termvectors.html>
