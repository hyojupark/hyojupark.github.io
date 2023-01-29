---
title: Python 문자열 포맷, F-String
toc: true
categories:
  - Python
tags:
  - f-string
  - format
  - Python
  - 문자열
---

Python에서 문자열 안에 변수 값을 넣는 다양한 방법을 확인해보겠습니다.


 


아래 4가지 방법이 있으며, 모두 동일한 출력 값을 가집니다.



```
a = 10
b = 20

# 10 + 20 = 30
# Method 1
print('' + str(a) + ' + ' + str(b) + ' = ' + str(a + b))

# Method 2
print('{} + {} = {}'.format(a, b, a + b))

# Method 3
print('%d + %d = %d' % (a, b, a + b))

# Method 4
print(f'{a} + {b} = {a + b}')
```

 


![](/assets/images/posts/2022-6-7-tistory-post-34/img-1.png)출력 결과




 


기존에는 불편한 Method 1을 대체하기 위해 Method 2, 3을 사용해왔지만 Python 3.6부터 F-String이 지원되어 Method 4와 같은 형태로 사용하고 있습니다. 사용하는 방법과 표현되는 면에서 가장 좋은 방법으로 현재는 대표적으로 가장 많이 사용됩니다.

