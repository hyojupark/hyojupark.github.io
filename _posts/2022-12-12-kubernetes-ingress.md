---
title: Kubernetes Ingress 쉽게 적용하기
toc: true
categories:
  - Kubernetes
tags:
  - Ingress
  - rewrite-target
  - use-regex
---

Ingress를 적용할 때 path만 설정할 경우, **외부에서 서비스로 접근하는 URI**와 **내부에서 실제 서비스로 요청하는 URI**가 달라지면서 오류가 발생하는 경우를 볼 수 있습니다. 여기서 아래와 같이 설정하면 위 두 URI를 일치시킬 수 있습니다.

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

위 설정은 MLflow의 ingress를 설정했을 때 적용했던 코드입니다. `use-regex`로 **정규식 설정**을 켜고, `path`를 `/mlflow(/|$)(.\*)`과 같이 설정합니다.

`(/|$)`은 URI에 `/`가 있는 경우와 없는 경우를 **동일하게 처리**하기 위한 내용이고, `(.\*)`은 `rewrite-target`에 해당 부분의 경로를 전달하기 위한 부분입니다. `/$2`를 입력함으로써 `path`의 두번째 괄호(첫번째는 `(/|$)`, 두번째는 `(.\*)`) 이후를 전부 `rewrite-target`에 전달함으로써 **외부에서 서비스로 접근하는 URI**와 **내부에서 실제 서비스로 요청하는 URI**가 동일하게 처리됩니다.

<br>

위 설정을 이해했다면 아래와 같이 `path`가 추가돼도 `rewrite-target`을 동일하게 처리해야함을 알 수 있습니다.

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
      - path: /subpath/mlflow(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: mlflow
            port:
              number: 5000
```
