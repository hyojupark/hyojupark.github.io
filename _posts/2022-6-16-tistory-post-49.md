---
title: Python dictionary
toc: true
categories:
  - Python
tags:
  - Dictionary
  - Python
---

dictionary는 python에서 제공되는 자료형 중 하나로, key/value 쌍으로 데이터를 저장할 수 있습니다.



```
member_1 = {
    'name': 'john',
    'age': 22
    'gender': 'male'
}
```

 


위 데이터를 기준으로 'name'이라는 key로 'john'이라는 value, 'age'라는 key로 22라는 value를 가지고 있습니다.


 


아래와 예시와 같이 dictionary 안에 list 혹은 dictionary를 추가할 수 있습니다.



```
member_1 = {
    'name': 'john',
    'age': 22,
    'gender': 'male',
    'items': ['phone', 'card', 'bag'],
    'family': {
        'mother': 'lily',
        'father': 'daniel'
    }
}
```

 


또 하나의 dictionary에 여러 개의 dictionary를 가질 수 있습니다.



```
member_dict = {
    'john': {
        'age': 22,
        'gender': 'male'
    },
    'lily': {
        'age': 48,
        'gender': 'female'
    },
    'daniel': {
        'age': 49,
        'gender': 'male'
    }
}

print(len(member_dict))
for key, value in member_dict.items():
    print(key, ' - ', value)

print('john: ', member_dict['john'])


# 출력
3
john  -  {'age': 22, 'gender': 'male'}
lily  -  {'age': 48, 'gender': 'female'}
daniel  -  {'age': 49, 'gender': 'male'}
john:  {'age': 22, 'gender': 'male'}
```

 


dictionary는 위와 같은 방식으로 갯수 확인과 for loop에서 items로 내부 값을 조회할 수 있습니다. 그리고 member\_dict['john']과 같이 직접 key 값을 줘서 조회할 수 있습니다.

