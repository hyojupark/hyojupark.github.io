---
title: MLflow Git 프로젝트 코드로 실행
toc: true
categories:
  - MLOps/MLflow
tags:
  - Clone
  - GIT
  - Github
  - gitlab
  - MLflow
  - RUN
---

MLflow는 git에 저장된 코드를 가져와서 바로 실행할 수 있습니다. git clone 과정 정도를 생략할 수 있다고 보면 될 것 같습니다.


 



```
mlflow run -e main -P gpus=1 https://github.com/repos/mnist_project.git
```

 
