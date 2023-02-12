---
title: Python if문으로 list, dictionary가 none인 경우 예외처리
toc: true
categories:
  - Python
tags:
  - default
  - Dictionary
  - If
  - list
  - OR
  - Python
---

함수에서 list 혹은 dictionary를 인자로 받을 때 [], {} 값을 default로 사용할 수 없기 때문에 일반적으로 none값을 많이 사용합니다. 이 때 함수 안의 if문에서 list, dictionary의 빈값([], {})을 none으로 처리하는 경우, 일반적으로 아래와 같이 예외처리를 많이 합니다.

## 방법 1.

### **1) List 예외처리**

```python
def test_func_list(_test_list):
    if _test_list is None:
        print('1 not in.')
    elif 1 in _test_list:
        print('1 in.')
    else:
        print('1 not in.')


test_list_1 = None
test_list_2 = [1, 2, 3]

test_func_list(test_list_1)
test_func_list(test_list_2)


# 출력
1 not in.
1 in.
```

<br>

### **2) Dictionary 예외처리**

```python
def test_func_dict(_test_dict):
    if _test_dict is None:
        print('1 not in.')
    elif 1 in _test_dict.keys():
        print('1 in.')
    else:
        print('1 not in.')


test_dict_1 = None
test_dict_2 = {1: 'one', 2: 'two', 3: 'three'}

test_func_dict(test_dict_1)
test_func_dict(test_dict_2)


# 출력
1 not in.
1 in.
```

위와 같이 코드를 작성할 경우 if문이 조금 길어지면서 코드가 조금 지저분해지기 시작합니다. 이 부분을 아래 **방법 2**와 같이 개선하면 깔끔하게 작성이 가능합니다.

<br>

## 방법 2. (방법 1 개선)

### **1) List 예외처리**

```python
def test_func_list(_test_list):
    if 1 in (_test_list or []):
        print('1 in.')
    else:
        print('1 not in.')


test_list_1 = None
test_list_2 = [1, 2, 3]

test_func_list(test_list_1)
test_func_list(test_list_2)


# 출력
1 not in.
1 in.
```

### **2) Dictionary 예외처리**

```python
def test_func_dict(_test_dict):
    if 1 in (_test_dict or {}).keys():
        print('1 in.')
    else:
        print('1 not in.')


test_dict_1 = None
test_dict_2 = {1: 'one', 2: 'two', 3: 'three'}

test_func_dict(test_dict_1)
test_func_dict(test_dict_2)


# 출력
1 not in.
1 in.
```

python에서는 or를 기준으로 왼쪽이 none일 경우 오른쪽 값을 사용할 수 있기 때문에 위와 같은 처리가 가능해집니다.
