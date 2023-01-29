---
title: Python dictionary 추가, 합차기
toc: true
categories:
  - Python
tags:
  - Dictionary
  - Merge
  - Python
  - Update
---

 


### **dictionary 추가**


dictionary를 추가하는 방법은 직접적으로 key에 접근하면서 value를 추가하는 방법과 dictionary item을 update시키는 방법이 있습니다.



```
dic1 = {}
dic1['apple'] = '사과'
dic1.update({'banana': '바나나'})
print(dic1)


# 출력
{'apple': '사과', 'banana': '바나나'}
```

 


 


### **dictionary 합치기**


dictionary가 2개 있을 때 이것을 합치고 싶은 경우 아래 2가지 방법으로 가능합니다.


 


**방법 1.**



```
dic1 = {
    'apple': '사과',
    'banana': '바나나',
    'watermelon': '수박'
}

dic2 = {
    'lemon': '레몬',
    'peach': '복숭아',
    'orange': '오렌지'
}


dic1.update(dic2)
print(dic1)


# 출력
{'apple': '사과', 'banana': '바나나', 'watermelon': '수박', 'lemon': '레몬', 'peach': '복숭아', 'orange': '오렌지'}
```

 


**방법 2.**



```
dic1 = {
    'apple': '사과',
    'banana': '바나나',
    'watermelon': '수박'
}

dic2 = {
    'lemon': '레몬',
    'peach': '복숭아',
    'orange': '오렌지'
}


dic3 = {**dic1, **dic2}
print(dic3)


# 출력
{'apple': '사과', 'banana': '바나나', 'watermelon': '수박', 'lemon': '레몬', 'peach': '복숭아', 'orange': '오렌지'}
```

 


**방법3.**



```
dic1 = {
    'apple': '사과',
    'banana': '바나나',
    'watermelon': '수박'
}

dic2 = {
    'lemon': '레몬',
    'peach': '복숭아',
    'orange': '오렌지'
}


dic3 = dict(dic1.items() | dic2.items())
print(dic3)


# 출력
{'lemon': '레몬', 'peach': '복숭아', 'watermelon': '수박', 'orange': '오렌지', 'apple': '사과', 'banana': '바나나'}
```
