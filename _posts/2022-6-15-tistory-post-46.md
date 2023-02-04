---
title: Python 문자열 리스트 구분자로 합치기 (join)
toc: true
categories:
  - Python
tags:
  - JOIN
  - Python
  - Query
---

**for loop**을 이용해서 코드를 작성하다보면 아래와 같은 상황을 종종 보게됩니다.

```python
fruit_list = ['사과', '바나나', '멜론', '포도']

print('과일 목록: ', end='')
for fruit in fruit_list:
    print(fruit + ', ', end='')


# 출력
과일 목록: 사과, 바나나, 멜론, 포도,
```

콤마를 구분자로 리스트를 출력하고 싶은데, 출력 마지막 부분에도 구분자가 또 출력되면서 깔끔하게 출력되지 않습니다. 이를 정석으로 해결해보자면 아래와 같은 방법이 있을 수 있습니다.

```python
fruit_list = ['사과', '바나나', '멜론', '포도']

print('과일 목록: ', end='')
for i, fruit in enumerate(fruit_list, start=1):
    if i < len(fruit_list):
        print(fruit + ', ', end='')
    else:
        print(fruit, end='')
        
        
# 출력
과일 목록: 사과, 바나나, 멜론, 포도
```

하지만 이렇게 작성하면 저 한 부분을 위해 코드가 상당히 지저분해집니다. Python에서는 이를 `join`으로 해결할 수 있습니다.

```python
fruit_list = ['사과', '바나나', '멜론', '포도']

print('과일 목록: ' + ', '.join(fruit_list))


# 출력
과일 목록: 사과, 바나나, 멜론, 포도
```

맨 위의 코드보다 더 깔끔하게 작성하면서 원하는 출력 결과를 낼 수 있습니다. 이러한 부분은 DB 쿼리를 문자열로 작성할 때에도 활용할 수 있습니다.

```python
column_list = ['name', 'age', 'gender']
and_condition = ['age > 15', 'gender="male"']

query = f"SELECT {', '.join(column_list)} FROM member " \
        f"WHERE {' AND '.join(and_condition)}"
print(query)


# 출력
SELECT name, age, gender FROM member WHERE age > 15 AND gender="male"
```
