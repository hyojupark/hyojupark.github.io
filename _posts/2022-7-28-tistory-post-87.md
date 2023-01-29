---
title: FastAPI 설치 및 실행
toc: true
categories:
  - Server/FastAPI
tags:
  - fastapi
  - flask
  - SWAGGER
  - uvicorn
---

![fastapi logo](/assets/images/posts/2022-7-28-tistory-post-87/img-1.png)



 


FastAPI는 Flask만큼 쉬우면서도 고성능을 자랑하는 프레임워크입니다. Starlette과 Pydantic 기반의 ASGI(Asynchronous Server Gateway Interface)와 간편한 유효성 검사를 지원한다는 것이 큰 장점입니다.


 


### **설치**



```
pip install fastapi uvicorn
```

 


 


### **실행**


실행은 uvicorn 명령어를 이용해 실행하는 방법과 uvicorn 코드를 추가해서 실행하는 방법이 있습니다.


 


**방법 1. uvicorn 명령어**



```
# main.py

from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root():
    return {"Hello": "World"}
```


```
$ uvicorn main:app --reload
```

 


**방법 2. uvicorn 코드**



```
# main.py
import uvicorn
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root():
    return {"Hello": "World"}


if __name__ == "__main__" :
    uvicorn.run("main:app", reload=True)
```

 


**실행 화면**


![execute log](/assets/images/posts/2022-7-28-tistory-post-87/img-2.png)
![print web](/assets/images/posts/2022-7-28-tistory-post-87/img-3.png)웹 화면




 


추가로 FastAPI는 Swagger가 기본적으로 내장되어있어 API 명세서를 바로 확인할 수 있습니다.


![swagger ui](/assets/images/posts/2022-7-28-tistory-post-87/img-4.png)Swagger UI




 


 


 


 


**Reference**


* <https://fastapi.tiangolo.com/>
* <https://github.com/tiangolo/fastapi>
