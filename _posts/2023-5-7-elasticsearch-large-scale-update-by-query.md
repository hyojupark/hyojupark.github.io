---
title: Python에서 Elasticsearch update_by_query를 안전하게 사용하는 방법
categories:
  - Elastic Stack
tags:
  - Elasticsearch
  - Python
  - update_by_query
# published: false
---

## update_by_query
Elasticsearch에 저장된 document의 값을 일괄적으로 수정할 때 `update_by_query`를 사용하게 됩니다. 아래는 `update_by_query`를 사용해서 document의 field 이름을 변경하는 예시입니다.

```json
POST /my_index/_update_by_query
{
  "script": {
    "source": "ctx._source.new_field_name = ctx._source.old_field_name; ctx._source.remove('old_field_name')",
    "lang": "painless"
  },
  "query": {
    "exists": {
      "field": "old_field_name"
    }
  }
}
```

위 코드를 사용하면 `old_field_name` field 이름을 가지고있는 document의 field 이름을 `new_field_name`으로 일괄 수정할 수 있습니다.

`update_by_query`는 document를 쉽게 수정할 수 있다는 장점이 있지만, 무분별하게 사용하면 오류가 발생할 수 있습니다.

<br>

## ConflictError 오류 발생
위 예시를 기준으로 여러 field 이름을 한번에 변경하고자 할 경우 아래 코드처럼 반복문으로 실행하게 되는데, 이때 ConfilctError가 발생하게 됩니다.

```python
...
  es_client = Elasticsearch(...)

  field_rename_dict = {
    'old_field_name1': 'new_field_name1',
    'old_field_name2': 'new_field_name2',
    'old_field_name3': 'new_field_name3'
  }

  for old_field_name, new_field_name in field_rename_dict.items():
    query_body = {
      "script": {
        "source": f"ctx._source.{new_field_name} = ctx._source.{old_field_name}; ctx._source.remove('{old_field_name}')",
        "lang": "painless"
      },
      "query": {
        "exists": {
          "field": f"{old_field_name}"
        }
      }
    }    
    es_client.update_by_query(index='my_index', body=query_body)
```

```
elasticsearch.exceptions.ConflictError: ConflictError(409, u'{"took":1,"timed_out":false,"total":2,"updated":0,"deleted":0,"batches":1,"version_conflicts":2,"noops":0,"retries":{"bulk":0,"search":0},"throttled_millis":0,"requests_per_second":-1.0,"throttled_until_millis":0,"failures":[{"index":"my_index","type":"my_index","id":"1946","cause":{"type":"version_conflict_engine_exception","reason":"[my_index][1946]: version conflict, current version [10] is different than the one provided [9]","index_uuid":"nrK2h7uxSNaI2EGsFH-RNQ","shard":"0","index":"my_index"},"status":409},{"index":"my_index","type":"my_index","id":"1981","cause":{"type":"version_conflict_engine_exception","reason":"[my_index][1981]: version conflict, current version [17] is different than the one provided [16]","index_uuid":"nrK2h7uxSNaI2EGsFH-RNQ","shard":"0","index":"my_index"},"status":409}]}')
```

오류가 발생하는 원인은 `update_by_query` 함수가 실행되면 작업이 모두 처리될 때 까지 기다리지 않고 다음 쿼리를 실행하기 때문입니다. 이 때문에 동시에 2개의 document를 업데이트하는 경우가 발생하게 되고, 두 document의 version이 달라지면서 version conflict 오류가 발생하게 됩니다.

<br>

## 해결 방법
위 Conflict 오류를 해결하기 위해서는 하나의 document에서 동시에 수정이 발생하지 않도록 `update_by_query`를 사용할 때 `wait_for_completion=false` 옵션을 추가하고 Task가 처리될 때 까지 기다리면서 순서대로 진행해야합니다.

```python
...
  es_client = Elasticsearch(...)

  field_rename_dict = {
    'old_field_name1': 'new_field_name1',
    'old_field_name2': 'new_field_name2',
    'old_field_name3': 'new_field_name3'
  }

  for old_field_name, new_field_name in field_rename_dict.items():
    query_body = {
      "script": {
        "source": f"ctx._source.{new_field_name} = ctx._source.{old_field_name}; ctx._source.remove('{old_field_name}')",
        "lang": "painless"
      },
      "query": {
        "exists": {
          "field": f"{old_field_name}"
        }
      }
    }    
    task_id = es_client.update_by_query(index='my_index', body=query_body, wait_for_completion=False)

    while True:
      status = es_client.tasks.get(task_id=task_id)
      if status['completed']:
        break
      else:
        time.sleep(5)
```

`update_by_query`에 `wait_for_completion=False` 옵션을 주고 실행하면 함수가 실행된 task id를 반환합니다. 해당 task id로 task가 전부 처리됐는지 주기적으로 확인하다가 `completed` 상태가 되면 다음 `update_by_query`를 진행합니다.


## Reference
- <https://www.elastic.co/guide/en/elasticsearch/reference/master/docs-update-by-query.html>
- <https://blog.csdn.net/u010483897/article/details/103526227>