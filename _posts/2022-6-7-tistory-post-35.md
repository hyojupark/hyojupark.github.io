---
title: Python 경로 문자열 저장 (원시 문자열)
toc: true
categories:
  - Python
tags:
  - Python
  - 경로
  - 문자열
---

Python에서 윈도우 경로를 문자열로 저장하기 위해서 일반적으로 아래와 같이 사용합니다.



```
main_path = 'C:\\Users\\user\\PycharmProjects\\pythonProject\\main.py'
```

   
위 방법은 문자열 표기를 위해 escape character를 경로마다 추가한 형태입니다. 따라서 복사해서 사용할 때는 하나하나 고쳐주거나 찾아 바꾸기와 같은 기능으로 매번 변경해줘야합니다.  
   
위의 번거로운 과정을 반복하지 않기 위해 문자열 앞에 r을 붙여서 사용합니다.



```
main_path = r'C:\Users\user\PycharmProjects\pythonProject\main.py'
```

   
위 방법은 Python의 원시 문자열로, 문자열에 포함된 escape character를 전부 문자열 그대로 표시합니다. 매번 경로를 복사해서 사용할 때의 불편함을 겪지 않기 위해 위와 같은 방식으로 사용하는 것을 추천합니다.  
   
 

