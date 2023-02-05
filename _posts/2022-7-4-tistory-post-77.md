---
title: Python list indexing과 slicing
toc: true
categories:
  - Python
tags:
  - index
  - indexing
  - list
  - Python
  - slice
  - slicing
---

Python은 `indexing`과 `slicing`을 이용해서 list에서 일부만 추출할 수 있습니다. 사용 방법이 매우 간단하고 pandas에서 더 활용되기도 하니 잘 알아두면 좋습니다.

## **Indexing**

Indexing은 list에서 특정 값 하나를 추출할 때 사용합니다. 일반적으로 많이 사용하는 정수를 이용한 방법과, 음수를 이용해서 뒤에서 부터 찾는 방식이 있습니다.

```python
num_list = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

print(num_list[1])
print(num_list[5])
print(num_list[-1])
print(num_list[-2])


# 출력
2
6
10
9
```

<br>

## **Slicing**

Slicing은 list에서 list 형태로 추출할 때 사용합니다. 예를 들어 앞의 3개와 같이 여러 개를 가져올 때 사용됩니다.

```python
num_list = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

print(num_list[:3])
print(num_list[6:])
print(num_list[2:4])


# 출력
[1, 2, 3]
[7, 8, 9, 10]
[3, 4]
```

`slicing`은 왼쪽은 콜론(:)을 기준으로 왼쪽은 index, 오른쪽은 번호(index+1)로 생각하면 됩니다. 글로 설명하는 것 보다 아래 그림을 보면 더 이해가 잘 됩니다.

![slicing describe](/assets/images/posts/2022-7-4-tistory-post-77/img-1.png){: .align-center}

추가로 뒤에 콜론(`:`)을 하나 더 사용해서 **step**을 설정할 수 있습니다.

```python
num_list = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

print(num_list[:5:2])
print(num_list[2:9:3])


# 출력
[1, 3, 5]
[3, 6, 9]
```

**step**도 아래 그림을 통해서 설명하겠습니다.

![slicing describe 2](/assets/images/posts/2022-7-4-tistory-post-77/img-2.png){: .align-center}

<br>

## **Slicing에 음수 index로 접근**

Slicing에 음수 index를 이용해서 추출할 수 있습니다.

```python
num_list = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

print(num_list[2:-3])
print(num_list[-7:6])


# 출력
[3, 4, 5, 6, 7]
[4, 5, 6]
```

![slicing describe 3](/assets/images/posts/2022-7-4-tistory-post-77/img-3.png){: .align-center}
