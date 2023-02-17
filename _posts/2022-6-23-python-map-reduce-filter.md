---
title: Python map, reduce, filter
toc: true
categories:
  - Python
tags:
  - Filter
  - map
  - Python
  - Reduce
---

## **map**

**map**은 list에 있는 값들을 동일한 포맷으로 만드는데 많이 사용됩니다. **map**을 사용하면 generator 형태의 map object로 반환되고, 아래 2가지 방식으로 결과를 확인할 수 있습니다.

```python
num_list = [1, 2, 3, 4]

num_map = map(lambda x: x * x, num_list)
print(num_map)

print(list(num_map))

num_map = map(lambda x: x * x, num_list)
for num in num_map:
    print(num)
    
    
# 출력
<map object at 0x000001DF37DABE08>
[1, 4, 9, 16]
1
4
9
16
```
<br>

## **reduce**

**reduce**는 aggregation(집합) 연산에 많이 사용됩니다. 문자열에도 활용 가능합니다.

```python
from functools import reduce

num_list = [1, 2, 3, 4]

sum = reduce(lambda x, y: x + y, num_list)
max = reduce(lambda x, y: x if x > y else y, num_list)
min = reduce(lambda x, y: x if x < y else y, num_list)
print('sum: ', sum)
print('max: ', max)
print('min: ', min)

word_list = ['h', 'e', 'l', 'l', 'o', ' ', 'w', 'o', 'r', 'l', 'd']
sentence = reduce(lambda x, y: x + y, word_list)
print(sentence)


# 출력
sum:  10
max:  4
min:  1
hello world
```
<br>

## **filter**

조건문으로 list에서 특정 item을 걸러낼 때 많이 사용됩니다. **map**과 동일하게 generator 형태의 filter object로 반환됩니다.

```python
mix_list = ['44', 'word', 'banana', '1', '100']
filtered_list = list(filter(lambda x: x.isdigit(), mix_list))
print(filtered_list)

member_list = [
    {
        'name': 'john',
        'age': 22
    }, {
        'name': 'harry',
        'age': 17
    }, {
        'name': 'mark',
        'age': 27
    }, {
        'name': 'daniel',
        'age': 19
    }
]
filtered_object = filter(lambda x: x['age'] > 20, member_list)
print(filtered_object)

for item in filtered_object:
    print(item)


# 출력
['44', '1', '100']
<filter object at 0x00000188939CCF08>
{'name': 'john', 'age': 22}
{'name': 'mark', 'age': 27}
```
