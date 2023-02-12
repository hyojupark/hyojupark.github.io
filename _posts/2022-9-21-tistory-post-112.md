---
title: Kibana Tag Cloud 단어 빈도수 필터링 (count filtering)
toc: true
categories:
  - Elastic Stack
tags:
  - Elasticsearch
  - kibana
  - min_doc_count
  - Tag Cloud
  - word cloud
---

**Tag Cloud**에서 일정 횟수 이상 등장한 단어만 보여주거나, 일정 횟수 이하의 단어는 보여주지 않고 싶을 때 Kibana에서 관련 설정을 UI에서 제공하지 않기 때문에 <u>직접 설정을 추가</u>해야합니다. 

추가 방법은 `Terms` 설정 부분에 `JSON Input`에 `min_doc_count` 설정을 추가해주면 됩니다.

**JSON Input**

```json
{
	"min_doc_count": 10
}
```

10으로 설정하면 빈도수가 최소 10 이상인 경우에만 나오게 됩니다. 즉, 9개 이하인 단어는 Cloud에 나오지 않습니다.
