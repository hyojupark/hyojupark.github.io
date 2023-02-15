---
title: Airflow MongoDB Provider의 replace_many
toc: true
categories:
  - Airflow
tags:
  - airflow
  - MongoDB
  - MongoHook
  - multiple
  - provider
  - pymongo
  - Replace
  - ReplaceOne
  - replace_many
  - replace_one
---

Airflow에서 MongoDB에 연결하기 위해 사용하는 **MongoDB Provider**에는 **PyMongo**에 없는 `replace_many` 함수가 있습니다. **PyMongo**에서 지원되는 관련 함수 목록은 아래와 같습니다.

## **PyMongo의 Operation 목록 일부**

* `insert_one`
* `insert_many`
* `replace_one`
* `update_one`
* `update_many`
* `delete_one`
* `delete_many`

**MongoDB Provider**에서는 `replace_many`만 없는게 불편했는지 `replace_many`를 만들어줬습니다. 

```python
def replace_many(mongo_collection, docs, filter_docs=None,
    mongo_db=None, upsert=False, collation=None, **kwargs):
	...
```

구현은 **multiple** `replace_one`을 `bulk_write`로 처리하도록 구현되어있습니다. `docs`는 `list[dict]` 형태로, `replace_one`에 들어가는 dictionary를 list로 넣으면 됩니다.

<br>

## **Reference**

- [https://pymongo.readthedocs.io/en/stable/api/pymongo/collection.html](https://pymongo.readthedocs.io/en/stable/api/pymongo/collection.htm)
- <https://airflow.apache.org/docs/apache-airflow-providers-mongo/stable/_api/airflow/providers/mongo/hooks/mongo/index.html>


 

