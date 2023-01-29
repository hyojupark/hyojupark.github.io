---
title: MLflow 설치 및 동작 확인
toc: true
categories:
  - MLOps/MLflow
tags:
  - AI
  - machine learning
  - ML
  - MLflow
  - MLOps
---

![mlflow logo](/assets/images/posts/2022-7-21-tistory-post-84/img-1.png)



 


### **설치 및 mlflow ui 실행**


 


**1. MLflow  설치**


* python 3.6 이상
* 패키지 오류 시 pip, setuptools 업데이트 (pip install --upgrade pip setuptools)



```
$ pip install mlflow sklearn
```

 


**2. artifacts 생성 예제 코드 작성**



```
$ mkdir quickstart && cd quickstart
```

 


* quickstart/mlflow\_tracking.py



```
import os
from random import random, randint
from mlflow import log_metric, log_param, log_artifacts

if __name__ == "__main__":
    # Log a parameter (key-value pair)
    log_param("param1", randint(0, 100))

    # Log a metric; metrics can be updated throughout the run
    log_metric("foo", random())
    log_metric("foo", random() + 1)
    log_metric("foo", random() + 2)

    # Log an artifact (output file)
    if not os.path.exists("outputs"):
        os.makedirs("outputs")
    with open("outputs/test.txt", "w") as f:
        f.write("hello world!")
    log_artifacts("outputs")
```

 


**3. 작성한 mlflow\_tracking.py 코드 실행**



```
quickstart$ python mlflow_tracking.py
```

 


**4. Tracking UI 실행**



```
quickstart$ mlflow ui
```

 


**5. localhost:5000에 접속해서 Tracking UI 확인**


* 예제 코드로 생성한 artifacts 정보 확인


![mlflow ui](/assets/images/posts/2022-7-21-tistory-post-84/img-2.png)



 


 


### **학습 및 배포**


 


**1. 학습 예제 코드 실행**


* 학습 예제 코드는 <https://github.com/mlflow/mlflow-example> 참조
* conda를 사용하지 않을 경우 **--no-conda** 옵션 추가
* 아래 run ID(99bb7adb7ecb4874ae7f24be5145eb20) 복사



```
$ mkdir sklearn_wine && cd sklearn_wine
sklearn_wine$ mlflow run https://github.com/mlflow/mlflow-example.git -P alpha=5.0 --no-conda
==============================================================================================
2022/07/21 18:17:42 INFO mlflow.projects.utils: === Fetching project from https://github.com/mlflow/mlflow-example.git into C:\Users\user\AppData\Local\Temp\tmpr7pldpu9 ===
2022/07/21 18:17:45 INFO mlflow.projects.utils: Fetched 'master' branch
2022/07/21 18:17:46 INFO mlflow.projects.utils: === Created directory C:\Users\user\AppData\Local\Temp\tmp9c0vatqv for downloading remote URIs passed to arguments of type 'path' ===
2022/07/21 18:17:46 INFO mlflow.projects.backend.local: === Running command 'python train.py 5.0 0.1' in run with ID '99bb7adb7ecb4874ae7f24be5145eb20' === 
Elasticnet model (alpha=5.000000, l1_ratio=0.100000):
  RMSE: 0.8594260117338262
  MAE: 0.6480675144220314
  R2: 0.046025292604596424
2022/07/21 18:18:00 INFO mlflow.projects: === Run (ID '99bb7adb7ecb4874ae7f24be5145eb20') succeeded ===
```

 


**2. 모델 배포**



```
sklearn_wine$ mlflow models serve -m runs:/99bb7adb7ecb4874ae7f24be5145eb20/model --port 5001 --no-conda
==============================================================================================
2022/07/21 18:23:21 INFO mlflow.models.cli: Selected backend for flavor 'python_function'
2022/07/21 18:23:21 INFO mlflow.pyfunc.backend: === Running command 'waitress-serve --host=127.0.0.1 --port=5001 --ident=mlflow mlflow.pyfunc.scoring_server.wsgi:app'
INFO:waitress:Serving on http://127.0.0.1:5001
```

 


**3. 테스트**



```
$ curl -X POST -H "Content-Type:application/json; format=pandas-split" -d '{"columns":["fixed acidity", "volatile acidity", "citric acid", "residual sugar", "chlorides", "free sulfur dioxide", "total sulfur dioxide", "density", "pH", "sulphates", "alcohol"],"data":[[7, 0.27, 0.36, 20.7, 0.045, 45, 170, 1.001, 3, 0.45, 8.8]]}' http://localhost:5001/invocations
==============================================================================================
[5.7816894732991795]
```

 


* windows에서는 git bash(MINGW)를 이용해서 위 curl 명령어를 실행하거나, bash에서 아래 명령어로 실행해야합니다.



```
> curl -X POST -H "Content-Type:application/json; format=pandas-split" -d "{\"columns\":[\"fixed acidity\", \"volatile acidity\", \"citric acid\", \"residual sugar\", \"chlorides\", \"free sulfur dioxide\", \"total sulfur dioxide\", \"density\", \"pH\", \"sulph
ates\", \"alcohol\"],\"data\":[[7, 0.27, 0.36, 20.7, 0.045, 45, 170, 1.001, 3, 0.45, 8.8]]}" http://localhost:5001/invocations
==============================================================================================
[5.7816894732991795]
```

 


 


 


### **Reference**


* <https://mlflow.org/docs/latest/quickstart.html>
* <https://github.com/mlflow/mlflow-example>


 


 

