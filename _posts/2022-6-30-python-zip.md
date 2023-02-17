---
title: Python list 묶어서 loop 처리 (zip)
toc: true
categories:
  - Python
tags:
  - list
  - loop
  - Python
  - zip
---

우선 zip을 사용하지 않을 경우 index 값을 이용해서 처리가 가능합니다.

```python
list1 = ['일', '이', '삼', '사']
list2 = [1, 2, 3, 4]

for i in range(len(list1)):
    print(list1[i], list2[i])
    
    
# 출력
일 1
이 2
삼 3
사 4
```

위 코드를 zip을 사용하면 list 2개를 묶어서 loop 처리가 가능합니다.

```python
list1 = ['일', '이', '삼', '사']
list2 = [1, 2, 3, 4]

for i, j in zip(list1, list2):
    print(i, j)
    
    
# 출력
일 1
이 2
삼 3
사 4
```

만약 두 list 길이가 다를 경우 짧은 쪽으로 맞춰집니다.

```python
list1 = ['일', '이', '삼']
list2 = [1, 2, 3, 4]

for i, j in zip(list1, list2):
    print(i, j)
    
    
# 출력
일 1
이 2
삼 3
```

3개 이상의 list도 묶어서 처리가 가능합니다.

```python
list1 = ['일', '이', '삼', '사']
list2 = [1, 2, 3, 4]
list3 = ['one', 'two', 'three', 'four']

for i, j, k in zip(list1, list2, list3):
    print(i, j, k)
    
    
# 출력
일 1 one
이 2 two
삼 3 three
사 4 four
```

zip은 tuple을 반환하기 때문에 list 수 만큼 변수를 선언하지 않아도 처리가 가능합니다.

```python
list1 = ['일', '이', '삼', '사']
list2 = [1, 2, 3, 4]
list3 = ['one', 'two', 'three', 'four']

for i in zip(list1, list2, list3):
    print(type(i), i)
    
    
# 출력
<class 'tuple'> ('일', 1, 'one')
<class 'tuple'> ('이', 2, 'two')
<class 'tuple'> ('삼', 3, 'three')
<class 'tuple'> ('사', 4, 'four')
```
