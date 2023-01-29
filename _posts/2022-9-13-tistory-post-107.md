---
title: Python 텍스트 파일 읽을 때 개행문자 없애기
toc: true
categories:
  - Python
tags:
  - File
  - Python
  - Read
  - readlines
  - splitlines
  - Strip
---

설명을 위해 아래 텍스트 파일로 설명하겠습니다.



```
# test.txt
apple
melon
banana
peach
```

 


텍스트 파일을 읽을 때 **readlines**를 사용하면 아래와 같이 개행문자가 함께 저장되기 때문에 일반적으로 **strip**을 추가합니다.



```
with open('test.txt') as f:
    lines = f.readlines()
    print(lines)
    
    lines = [line.strip() for line in lines]
    print(lines)
    
    
# 출력
['apple\n', 'melon\n', 'banana\n', 'peach']
['apple', 'melon', 'banana', 'peach']
```

 


저는 **strip**을 사용하는 방식을 많이 추천하지만, **splitlines**를 이용하면 개행문자 없이 저장이 가능합니다.



```
with open('test.txt') as f:
    lines = f.read().splitlines()
    print(lines)
    
    
# 출력
['apple', 'melon', 'banana', 'peach']
```

 

