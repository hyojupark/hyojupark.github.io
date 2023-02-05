---
title: Python loop index 사용하기 (enumerate)
toc: true
categories:
  - Python
tags:
  - enumerate
  - index
  - Python
---

`enumerate`를 이용하면 for loop에서 index를 사용할 수 있습니다.

```python
fruit_list = ['apple', 'banana', 'grape', 'tomato', 'melon']

for n, fruit in enumerate(fruit_list):
    print(n, fruit)
    
    
# 출력
0 apple
1 banana
2 grape
3 tomato
4 melon
```

아래와 같이 start index를 조정해서 사용도 가능합니다.

```python
fruit_list = ['apple', 'banana', 'grape', 'tomato', 'melon']

for n, fruit in enumerate(fruit_list, start=1):
    print(n, fruit)
    
    
# 출력
1 apple
2 banana
3 grape
4 tomato
5 melon
```
