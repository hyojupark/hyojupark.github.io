---
title: Python math (min, max, abs, pow, round 등)
toc: true
categories:
  - Python
tags:
  - ABS
  - ceil
  - factorial
  - Floor
  - max
  - Min
  - pow
  - Python
  - Round
  - SUM
---

Python의 `math` 모듈은 내장 함수로 import 없이 사용 가능한 것이 있고, import해서 사용하는 함수가 있습니다. 아래 설명에서 import 부분을 참고해서 확인하면 됩니다.

## **min, max**

```python
num_list = [1, 7, 5, 3, 2, 9]

print(max(num_list))
print(min(num_list))


# 출력
9
1
```

위와 같이 list를 입력으로 사용할 수도 있고 아래 예시처럼 파라미터로 여러 개 넣어서 사용할 수도 있습니다.

```python
print(max(1, 7, 5, 3, 2, 9))
print(min(1, 7, 5, 3, 2, 9))


# 출력
9
1
```

<br>

## **abs, pow**

```python
print(abs(-14), abs(14))
print(pow(2, 4), pow(3, 3))


# 출력
14 14
16 27
```

`abs`로 절대값을 계산할 수 있고, `pow`로 제곱값을 계산할 수 있습니다.

<br>

## **round, ceil, floor, trunc (+ pi)**

```python
import math

print(round(math.pi, 2))
print(math.ceil(math.pi))
print(math.floor(math.pi))
print(math.trunc(math.pi))


# 출력
3.14
4
3
3
```

`math` 함수는 `pi`(3.141592..., 15자리까지) 값을 제공합니다. 제공되는 `pi`를 이용하여 위에서 사용된 함수를 예시로 설명했습니다. `round`로 자리수를 파라미터로 입력해서 반올림할 수 있으며, `ceil`로 **올림**, `floor`로 **내림**, `trunc`는 **버림**입니다.

<br>

## **factorial**

```python
import math

print(math.factorial(4))
print(math.factorial(5))


# 출력
24
120
```

`factorial`은 함수 이름 그대로 factorial 계산이 가능합니다.

<br>

## **sum, fsum**

```python
import math

num_list1 = [1, 2, 3, 4, 5, 6]
num_list2 = [0.1, 0.1, 0.1, 0.1, 0.1, 0.1, 0.1, 0.1, 0.1, 0.1]

print(sum(num_list1))
print(sum(num_list2))
print(math.fsum(num_list2))


# 출력
21
0.9999999999999999
1.0
```

정수는 위와 같이 `sum`으로 쉽게 계산이 가능하지만, **실수**는 `fsum`을 사용해야 정확하게 계산이 가능합니다. 이는 부동소수점을 사용할 때 발생하는 문제로, `Decimal`을 사용해서 해결하기도 하지만 위와 같이 `sum`을 할 경우 `fsum`으로 해결이 가능합니다.
