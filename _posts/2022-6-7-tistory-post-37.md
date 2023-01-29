---
title: Python Lambda
toc: true
categories:
  - Python
tags:
  - lambda
  - map
  - Python
  - sort
---

Python에서 lambda를 사용하면 함수를 추가 작성하지 않아도 돼서 편리합니다. 아래 대표적인 sort와 map 예시로 lambda 동작을 확인해 볼 수 있습니다.


 



```
def sort_key(x):
    return x[1]


num_list = [('구', 9), ('사', 4), ('이', 2), ('칠', 7), ('오', 5), ('삼', 3), ('일', 1), ('육', 6), ('팔', 8)]

print(sorted(num_list, key=sort_key))
print(sorted(num_list, key=lambda x: x[1]))


# 출력
[('일', 1), ('이', 2), ('삼', 3), ('사', 4), ('오', 5), ('육', 6), ('칠', 7), ('팔', 8), ('구', 9)]
[('일', 1), ('이', 2), ('삼', 3), ('사', 4), ('오', 5), ('육', 6), ('칠', 7), ('팔', 8), ('구', 9)]
```

 



```
num_list = [1, 2, 3, 4, 5]

print(list(map(lambda x: x * x, num_list)))


# 출력
[1, 4, 9, 16, 25]
```

 

