---
title: Python asterisk(*)
toc: true
categories:
  - Python
tags:
  - Asterisk
  - Python
  - Unpacking
  - variable length paramter
---

Python에서 asterisk(`*`)은 일반적인 값의 곱하기 외에 여러 기능을 포함하고 있습니다.

##  **복제**

문자열, 리스트 등의 자료형을 여러 개로 복제할 수 있습니다. 

```python
print('*' * 20)
test_list = [1] * 10
print(test_list)


# 출력
********************
[1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
```
<br>

## **가변인자 (Variable Length Parameter)**

함수의 **parameter**를 다양한 형태로 받을 수 있도록 합니다. asterisk를 하나(`*`) 붙이면 튜플 형태로, 두 개(`**`) 붙이면 **dictionary** 형태로 받습니다.

```python
def print_func_1(*args):
    print(args)


def print_func_2(**kwargs):
    print(kwargs)


print_func_1('john', 22, 'male')
print_func_2(name='john', age=22, gender='male')


# 출력
('john', 22, 'male')
{'name': 'john', 'age': 22, 'gender': 'male'}
```
<br>

## **Unpacking, Packing**

dictionary나 list 형태의 자료형을 풀 수 있습니다. 아래 예시는 dictionary를 풀어서 `print_member(name='john', age=22, 'gender'='male')`과 동일한 형태로 함수가 실행되도록 합니다. 그리고 list를 풀어서 출력해 `[]`가 없이 출력되는 것을 확인할 수 있습니다.

```python
def print_member(name, age, gender):
    print('name: ', name)
    print('age: ', age)
    print('gender: ', gender)


member = {
    'name': 'john',
    'age': 22,
    'gender': 'male'
}
print_member(*member)

number_list = [1, 2, 3, 4]
print(*number_list)



# 출력
name:  name
age:  age
gender:  gender
1 2 3 4
```
<br>

이를 활용하면 list를 합칠 수도 있습니다.

```python
test_list_1 = [1, 2, 3, 4]
test_list_2 = [5, 6, 7, 8]

print([*test_list_1, *test_list_2])


# 출력
[1, 2, 3, 4, 5, 6, 7, 8]
```
<br>

이를 활용하면 이런식으로 packing도 가능합니다.

```python
*test_list, = 1, 2, 3, 4
print(test_list)


# 출력
[1, 2, 3, 4]
```
