---
title: Kubernetes MLflow ingress 적용 (UI, API 사용하기)
toc: true
categories:
  - MLflow
tags:
  - Ingress
  - kubernetes
  - MLflow
  - rewrite-target
  - use-regex
---

Kubernetes MLflow ingress 적용을 위해 일반적으로 나와있는 방법을 사용하면 UI는 사용 가능하지만 API 사용이 불가능합니다. API 호출 경로가 ingress path와 상관없이 서비스 상대경로로 적용되기 때문입니다. 따라서 서비스에서 ingress path를 제외하고 호출할 수 있도록 ingress를 설정해줘야합니다.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ...
  namespace: ...
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$2
    nginx.ingress.kubernetes.io/use-regex: 'true'
spec:
  rules:
  - http:
      paths:
      - path: /mlflow(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: mlflow
            port:
              number: 5000
```

위 코드는 ingress 설정 예시입니다. 여기서 중요한 부분은 `annotations`와 `path`, `pathType`입니다.

MLflow가 아니더라도 다른 서비스에도 적용 가능한 부분으로, 알아두면 자주 활용 가능할 것으로 보입니다.
