---
title: Elasticsearch bucket_script format (+/- 표시, 천단위 끊기)
toc: true
categories:
  - Elastic Stack
tags:
  - bucket_script
  - DecimalFormat
  - Elasticsearch
  - format
  - Java
---

## **예시 코드**

```json
...
"example": {
    "bucket_script": {
        "buckets_path": {
            "var1": "2000",
            "var2": "1000"
        },
        "script": {
        	"lang": "painless",
            "source": "params.var1 - params.var2"
        },
        "format": "+#,###;-#,###"
    }
}
...


# 출력 예시
...
"example": {
	"value": 1000,
    "value_as_string": "+1,000"
}
...
```

### **설명**

`bucket_script`의 `format`은 java의 `DecimalFormat` Class를 사용합니다. `DecimalFormat`에서 `;`를 사용하면 `;`를 기준으로 왼쪽에는 양수, 오른쪽은 음수 `format`을 지정할 수 있습니다. 위 `format`에서는 양수(0 포함)이면 앞에 `+`를 붙이고, 음수면 앞에 `-`를 붙입니다. 그리고 숫자를 천단위로 끊을 때는 `#,###`을 추가하면 끊어서 표현이 가능합니다. 다른 표현은 `DecimalFormat` 문서를 참조 바랍니다.

<br>

## **Reference**

* <https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/text/DecimalFormat.html>
* <https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations-pipeline-bucket-script-aggregation.html>


 


 

