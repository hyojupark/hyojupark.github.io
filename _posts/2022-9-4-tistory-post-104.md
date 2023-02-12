---
title: Python dictionary loop
toc: true
categories:
  - Python
tags:
  - Dictionary
  - Item
  - Items
  - key
  - Keys
  - loop
  - Python
  - values
---

Python에서 dictionary item을 하나씩 loop 돌 때 값을 찾는 방법으로 <u>1) item(key, value)</u>, <u>2) key</u>, <u>3) value</u> 각각 가능합니다.

## **방법 1. item(key, value)**

```python
dic1 = {
    'apple': '사과',
    'banana': '바나나',
    'watermelon': '수박',
    'lemon': '레몬',
    'peach': '복숭아',
    'orange': '오렌지'
}


for key, value in dic1.items():
    print(key, value)
    
    
# 출력
apple 사과
banana 바나나
watermelon 수박
lemon 레몬
peach 복숭아
orange 오렌지
```

## **방법 2. key**

```python
dic1 = {
    'apple': '사과',
    'banana': '바나나',
    'watermelon': '수박',
    'lemon': '레몬',
    'peach': '복숭아',
    'orange': '오렌지'
}


for key in dic1.keys():
    print(key)
    
    
# 출력
apple
banana
watermelon
lemon
peach
orange
```

## **방법 3. value**

```python
dic1 = {
    'apple': '사과',
    'banana': '바나나',
    'watermelon': '수박',
    'lemon': '레몬',
    'peach': '복숭아',
    'orange': '오렌지'
}


for value in dic1.values():
    print(value)
    
    
# 출력
사과
바나나
수박
레몬
복숭아
오렌지
```

 

