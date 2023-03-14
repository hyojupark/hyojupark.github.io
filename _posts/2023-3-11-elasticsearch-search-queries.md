---
title: Elasticsearch search queries
categories:
  - Elastic Stack
tags:
  - Elasticsearch
---

Elasticsearch에서 특정 키워드로 검색할 때 조건에 따라 적절한 방법을 사용해야합니다. 아래에서 검색할 때 대표적으로 많이 사용되는 `term`, `match`, `match_phrase`, `string_query`을 설명하겠습니다.


## term
**term**은 온전한 단어를 검색할 때 가장 많이 사용하는 방법입니다. 검색하는 단어 앞뒤로 다른 글자가 없는 document를 검색합니다.

아래 간단한 문장을 검색어 예시로 들어 설명하겠습니다.

```json
{
    "content": "엘라스틱서치로 키워드 검색하기"
}
```
**검색어 예시**
{: .text-center}

<br>

위와 같은 문장이 있을 때, 아래와 같이 `키워드`를 검색하면 해당 document를 검색할 수 있습니다.

```json
{
    "query": {
        "term": {
            "content": "키워드"
        }
    }
}
```

하지만 `엘라스틱서치`나 `검색`과 같이 단어가 온전히 있지 않은 경우에는 검색이 되지 않습니다. **term**은 검색어에 대한 분석을 거치지 않기 때문에 tokenizing 과정이 없기 때문입니다.


## match
**match**는 **term**와 달리 유사한 문서를 검색할 때 많이 사용하는 방법입니다. tokenizer를 통해 분석된 단어가 하나라도 포함된 document를 검색합니다.

동일한 검색어 예시를 들어 설명하겠습니다.

```json
{
    "content": "엘라스틱서치로 키워드 검색하기"
}
```
**검색어 예시**
{: .text-center}

<br>

한국어에서의 분석은 주로 형태소분석을 의미합니다. 일반적으로 Elasticsearch에서는 [Nori](https://esbook.kimjmin.net/06-text-analysis/6.7-stemming/6.7.2-nori)를 사용하는데요. Nori를 통해 tokenizing을 하면 아래와 같은 결과를 얻을 수 있습니다.

```json
[
    {
      "token" : "엘라스틱",
      "start_offset" : 0,
      "end_offset" : 4,
      "type" : "word",
      "position" : 0
    },
    {
      "token" : "서치",
      "start_offset" : 4,
      "end_offset" : 6,
      "type" : "word",
      "position" : 1
    },
    ...
]
```

<br>

때문에 아래와 같이 `엘라스틱`으로 검색해도 document를 검색할 수 있게 됩니다. 

```json
{
    "query": {
        "match": {
            "content": "엘라스틱"
        }
    }
}
```

<br>

또한 **match**로 검색할 때는 score가 계산되기 때문에 가장 일치하는 document를 우선순위로 검색할 수 있습니다. 위 예시 검색어를 기준으로 아래와 같이 단어를 추가할 경우 더 높은 score를 낼 수 있습니다.

```json
{
    "query": {
        "match": {
            "content": "엘라스틱 키워드"
        }
    }
}
```


## match_phrase
**match_phrase**는 띄어쓰기가 포함된 문장을 검색할 때 사용하는 방법입니다. **match**는 띄어쓰기를 넣어서 검색하면 단어의 순서와 상관없이 하나만 포함되도 검색이 되는데, 공백이 포함된 정확한 단어를 검색하고 싶을 경우 **match_phrase**를 사용해야합니다.

아래 간단한 문장을 검색어 예시로 들어 설명하겠습니다.

```json
{
    "content": "아이스 라떼랑 따뜻한 아메리카노 주세요."
}
```
**검색어 예시**
{: .text-center}

<br>

```json
{
    "query": {
        "match": {
            "content": "아이스 아메리카노"
        }
    }
}
```

위의 검색어 예시를 **match**를 이용해서 검색할 경우 해당 document가 검색되는 문제가 있습니다. 위 검색어 예시는 `아이스 아메리카노`가 검색되지 않아야하는데요. **match**는 문장 내 단어의 위치나 순서와 상관없이 검색하기 때문에 document가 검색되는 문제가 발생합니다.

이러한 문제를 해결할 때 **match_phrase**를 사용합니다.

```json
{
    "query": {
        "match_phrase": {
            "content": "아이스 아메리카노"
        }
    }
}
```
**검색되지 않는 예시**
{: .text-center}

<br>

```json
{
    "query": {
        "match_phrase": {
            "content": "따뜻한 아메리카노"
        }
    }
}
```
**검색되는 예시**
{: .text-center}

## query_string
**query_string**은 tokenizing되지 않는 단어까지 포함해서 검색할 때 사용하는 방법입니다. 

**match**에서는 검색되려면 단어가 반드시 tokenizing되어야하는 단점이 있습니다. **query_string**을 사용하면 tokenizing되지 않은 단어까지 검색할 수 있는데, 방법이 MySQL의 `like`와 비슷합니다.

아래 간단한 문장을 검색어 예시로 들어 설명하겠습니다.

```json
{
    "content": "카페라떼너무좋아"
}
```
**검색어 예시**
{: .text-center}

위와 같은 문장이 있을 때, 아래와 같이 **query_string**을 사용하면 tokenizing되지 않는 단어도 검색이 가능합니다.

```json
{
    "query": {
        "query_string": {
            "fields": ["content"],
            "query": "*라떼*"
        }
    }
}
```

<br>

## 정리
`term`과 `match`, `match_phrase`, `query_string`은 상황에 따라 각각 검색되는 document가 다르고 검색 속도 차이가 있기 때문에 상황에 따라 적절한 방법을 선택해서 사용해야합니다. `query_string`과 같은 방법을 과도하게 사용하기 보다는 keyword type이나 정확한 단어를 검색할 때는 `term`을 사용하는 것 처럼 적절한 방법을 사용하는 것을 추천합니다.
