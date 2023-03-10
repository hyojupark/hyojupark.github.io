---
title: Airflow S3에 있는 파일을 디스크를 안거치고 메모리로 가져오기
toc: true
categories:
  - Airflow
tags:
  - airflow
  - BytesIO
  - download_file
  - download_fileobj
  - io.BytesIO
  - read_parquet
  - S3
  - S3Hook
---

S3에 있는 파일을 디스크에 저장 후 작업을 진행하면 파일을 삭제하기 전에 예외 상황이 발생할 수 있고, 이렇게 되면 파일이 삭제되지 않고 디스크에 남아버립니다. 때문에 특별한 경우가 아니라면 디스크에 저장하지 않고 바로 가져와서 사용하는 것이 좋습니다.

## **Before (파일을 디스크에 저장 후 사용)**

```python
from airflow.providers.amazon.aws.hooks.s3 import S3Hook
import pandas as pd


s3_hook = S3Hook('s3_hook_name')
bucket_name = 'data_source'
s3_key = 'data-000001.gz.parquet'

local_path = '.'  # current directory
file_name = s3_hook.download_file(key=key, bucket_name=bucket_name, local_path=local_path)

df = pd.read_parquet(file_name)
os.remove(file_name)

...
```

## **After (파일을 메모리에 올려서 사용)**

```python
import io

from airflow.providers.amazon.aws.hooks.s3 import S3Hook
import pandas as pd


s3_hook = S3Hook('s3_hook_name')
bucket_name = 'data_source'
s3_key = 'data-000001.gz.parquet'

with io.BytesIO() as f:
    s3_obj = s3_hook.get_key(key=s3_key, bucket_name=bucket_name)
    s3_obj.download_fileobj(f)

    df = pd.read_parquet(bytes_object)
    
...
```

S3Hook에서 `download_fileobj`를 직접적으로 지원하지 않기 때문에 **S3 Object**를 이용해서 사용해야합니다. 실제로 S3Hook에서 지원하는 `download_file`에서도 `download_fileobj`를 이용합니다. (<https://airflow.apache.org/docs/apache-airflow-providers-amazon/stable/_modules/airflow/providers/amazon/aws/hooks/s3.html#S3Hook.download_file>)

<br>

## **Reference**

- <https://airflow.apache.org/docs/apache-airflow-providers-amazon/stable/_api/airflow/providers/amazon/aws/hooks/s3/index.html>


 

