---
title: Python print 출력 그대로 파일에 쓰기
toc: true
categories:
  - Python
tags:
  
---

Python에서 `print`는 file 객체를 받아 **stdout**을 바꿀 수 있기 때문에 `write` 함수를 사용하지 않고 `print` 그대로 파일에 쓸 수 있습니다.

우선 `print`로 출력을 확인합니다.

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

그리고 파일을 열어서 `print`에 파일 객체를 주고 동일하게 실행합니다.

```python
with open('test.txt', 'w', encoding='utf-8') as f:
    for i in range(5):
        print(i, file=f)
```

![](/assets/images/posts/2022-6-27-python-file-write-using-print/img-1.png)

위 `print` 출력과 동일한 것을 확인할 수 있습니다. 이를 활용해서 console에 log를 남기면서 보다가 추후 파일로 남기고 싶을 때 위와 같이 간단하게 바꿔서 처리가 가능합니다.
