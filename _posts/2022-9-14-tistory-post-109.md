---
title: FastAPI Kubernetes ingress 연결
toc: true
categories:
  - Server/FastAPI
tags:
  - fastapi
  - Ingress
  - k8s
  - kubernetes
  - root_path
  - SWAGGER
---

FastAPI를 ingress에 연결해서 사용하고 싶은 경우 추가 경로 설정이 필요합니다.


 


1. Kubernetes Ingress 설정
2. root\_path 설정 (Swagger UI 연결용)


 


여기서는 ingress base 경로를 **/api/v1/app\_name**으로 잡고 진행하겠습니다.


 


 


### **Kubernetes Ingress 설정**



```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app_name-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$2
    nginx.ingress.kubernetes.io/use-regex: 'true'
spec:
  rules:
  - http:
      paths:
        - path: /api/v1/app_name(/|$)(.*)
          pathType: Prefix
          backend:
            service:
              name: app_name-svc
              port:
                number: 5000
```

위에서 중요하게 봐야하는 설정은 **.metadata.annotations.nginx.ingress.kubernetes.io/rewrite-target** 부분과 **.spec.rules.http.paths.path**입니다. 위와 같이 적용해야 **/api/v1/app\_name/test**를 **/test**로 요청해 처리할 수 있습니다.


 


 


### **root\_path 설정**


root\_path 설정을 생략해도 API는 동작하지만, Swagger UI를 보기 위해서는 설정이 필요합니다. 



```
from fastapi import FastAPI


app = FastAPI(root_path='/api/v1/app_name')


@app.get('/test')
def test():
return 'testing'
```

 


 


위 설정을 적용해서 Kubernetes에 배포 후 ingress가 연결된 주소에서 **/api/v1/app\_name/docs**를 요청하면 Swagger UI가 뜨고 **/api/v1/app\_name/test**를 요청하면 testing이 출력되는 것을 확인할 수 있습니다.

