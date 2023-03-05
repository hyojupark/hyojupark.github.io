---
title: FastAPI 설치 및 실행
toc: true
categories:
  - FastAPI
tags:
  - fastapi
  - flask
  - SWAGGER
  - uvicorn
---

![fastapi logo](/assets/images/posts/2022-7-28-fastapi-setup/img-1.png){: .align-center}

**FastAPI**는 <u>Flask만큼 쉬우면서</u>도 <u>고성능을 자랑</u>하는 프레임워크입니다. Starlette과 Pydantic 기반의 ASGI(Asynchronous Server Gateway Interface)와 간편한 유효성 검사를 지원한다는 것이 큰 장점입니다.

## **설치**

```bash
pip install fastapi uvicorn
```

<br>

## **실행**

실행은 `uvicorn` 명령어를 이용해 실행하는 방법과 `uvicorn` 코드를 추가해서 실행하는 방법이 있습니다.

### **방법 1. uvicorn 명령어**

```python
# main.py

from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root():
    return {"Hello": "World"}
```

```bash
$ uvicorn main:app --reload
```

<br>

### **방법 2. uvicorn 코드**

```python
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

<br>

### **실행 화면**

![execute log](/assets/images/posts/2022-7-28-fastapi-setup/img-2.png){: .align-center}
![print web](/assets/images/posts/2022-7-28-fastapi-setup/img-3.png){: .align-center}

추가로 **FastAPI**는 **Swagger**가 기본적으로 내장되어있어 API 명세서를 바로 확인할 수 있습니다.

![swagger ui](/assets/images/posts/2022-7-28-fastapi-setup/img-4.png){: .align-center}
**Swagger UI**
{: .text-center}

<br>

## **Reference**

* <https://fastapi.tiangolo.com/>
* <https://github.com/tiangolo/fastapi>
