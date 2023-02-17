---
title: Python pass와 ellipsis(...)로 빈 함수, 변수 정의하기
toc: true
categories:
  - Python
tags:
  - ellipsis
  - Pass
  - Python
---

Python은 스크립트 언어이기 때문에 빈 함수나 변수를 미리 정의하는 경우가 다른 언어에 비해 드문 편입니다. 하지만 제대로 구조를 갖추고 코드를 작성하다보면 필요한 경우가 종종 발생합니다. 이럴 때 FM으로 한다면 함수는 `raise NotImplementedError`로 처리해야하지만, `pass`와 `ellipsis(...)`으로 처리할 수 있습니다.

## **pass**

```python
def test_func():
    pass


res = test_func()
print(type(res), res)


# 출력
<class 'NoneType'> None
```

`pass`는 변수 값으로는 사용이 불가능합니다. 함수나 loop 등의 과정에서 사용이 가능하며, 함수가 `pass`를 return하지 않기 때문에 `NoneType`을 반환하게 됩니다.

## **ellipsis(...)**

```python
def test_func():
    ...


res = test_func()
print(type(res), res)

var1 = ...
print(type(var1), var1)


# 출력
<class 'NoneType'> None
<class 'ellipsis'> Ellipsis
```

함수는 위 `pass`와 동일하게 `ellipsis`를 return하지 않기 때문에 `NoneType`을 반환하게 됩니다. 하지만 변수는 `ellipsis`를 직접 주었기 때문에 `ellipsis type`으로 반환됩니다.
