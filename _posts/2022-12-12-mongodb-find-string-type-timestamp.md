---
title: MongoDB string type timestamp 조회하기
toc: true
categories:
  - Database
tags:
  - find
  - gettime
  - ISODate
  - MongoDB
  - Timestamp
  - toString
---

외부 데이터를 그대로 저장하다보니 **timestamp를 string type 그대로 저장**하는 경우가 발생했습니다. 이때 integer type으로 변경하지 않아도 아래와 같이 조회가 가능합니다.

```python
db.test_db.find({
	"timestamp": {
    	$gte: ISODate("2022-12-09T00:00:00+09:00").getTime().toString(),
        $lte: ISODate("2022-12-10T00:00:00+09:00").getTime().toString()
    }
})
```
