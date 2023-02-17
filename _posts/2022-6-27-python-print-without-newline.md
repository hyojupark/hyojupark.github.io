---
title: Python print 개행 없이 출력 (end)
toc: true
categories:
  - Python
tags:
  - END
  - print
  - Python
  - \N
---

Python의 `print`는 기본적으로 개행을 해주기 때문에 여러 개의 `print`를 사용하면 아래와 같이 출력됩니다.

```python
for i in range(5):
    print(i)
    
    
# 출력
0
1
2
3
4
```

여기서 print에 `end=''` 값을 주면 개행 없이 출력이 가능합니다.

```python
for i in range(5):
    print(i, end='')
    
    
# 출력
01234
```

Python `print` 함수의 `end` default 값이 `'\n'`인 부분이기 때문에 개행인 부분을 `''`으로 바꾸면 위와 같이 개행 없이 출력이 가능합니다. `''` 말고 다른 문자를 넣어서 활용도 가능합니다.

```python
def print(self, *args, sep=' ', end='\n', file=None): # known special case of print
    """
    print(value, ..., sep=' ', end='\n', file=sys.stdout, flush=False)
    
    Prints the values to a stream, or to sys.stdout by default.
    Optional keyword arguments:
    file:  a file-like object (stream); defaults to the current sys.stdout.
    sep:   string inserted between values, default a space.
    end:   string appended after the last value, default a newline.
    flush: whether to forcibly flush the stream.
    """
    pass
```
