---
title: BentoML 설치 및 동작 확인
toc: true
categories:
  - MLOps/BentoML
tags:
  - AI
  - BentoML
  - machine learning
  - MLOps
---

![bentoml logo](/assets/images/posts/2022-5-29-tistory-post-26/img-1.png){: .align-center}

## **설치 및 Service 생성**

### **1. BentoML 설치**

* python3.7 이상
* 패키지 오류 시 pip, setuptools 업데이트 (pip install --upgrade pip setuptools)

```bash
pip install bentoml sklearn pandas
```

### **2. BENTOML_HOME 경로 설정**

* BentoML에서 만드는 Artifact의 저장 경로
* default는 ~/bentoml

```bash
export BENTOML_HOME='custom path'
```

### **3. 모델 학습 및 저장 코드 작성**

* main.py

```python
from iris_classifier import IrisClassifier

from sklearn import svm, datasets

import bentoml


if __name__ == "__main__":
	# Load training data
    iris = datasets.load_iris()
    x, y = iris.data, iris.target
    
    # Model Training
    clf = svm.SVC()
    clf.fit(x, y)
    
    # Create a iris classifier service instance
    iris_classifier_service = IrisClassifier()
    
    # Pack the newly traned model artifact
    iris_classifier_service.pack('model', clf)
    
    # Save the prediction service to disk for model serving
    saved_path = iris_classifier_service.save()
```

### **4. Prediction Service Class 생성**

* iris_classifier.py

```python
import pandas as pd

from bentoml import env, artifacts, api, BentoService
from bentoml.adapters import DataframeInput
from bentoml.frameworks.sklearn import SklearnModelArtifact

@env(infer_pip_packages=True)
@artifacts([SklearnModelArtifact('model')])
class IrisClassifier(BentoService):
    """
    A minimum prediction service exposing a Scikit-learn model
    """

    @api(input=DataframeInput(), batch=True)
    def predict(self, df: pd.DataFrame):
        """
        An inference API named `predict` with Dataframe input adapter, which codifies
        how HTTP requests or CSV files are converted to a pandas Dataframe object as the
        inference API function input
        """
        return self.artifacts.model.predict(df)
```

### **5. 학습 및 저장 코드(main.py) 실행**

```bash
python main.py
```

### **6. 정상 실행 결과 확인**

* 아래 문구가 나오면 정상 동작

```bash
[2022-05-29 21:34:03,777] INFO - BentoService bundle 'IrisClassifier:20220529213403_F2631F' saved to :/home/user/bentoml/repository/IrisClassifier/20220529213403_F2631F
```

## **서비스 코드 확인 및 실행**

BENTOML_HOME 경로에서 아래 명령어로 구조를 확인합니다. (default 경로는 ~/bentoml 입니다.)

```bash
user@ubuntu:~/bentoml$ tree -L 4
.
├── logs
│   ├── active.log
│   ├── feedback.log
│   └── prediction.log
├── repository
│   └── IrisClassifier
│       └── 20220529213403_F2631F
│           ├── bentoml-init.sh
│           ├── bentoml.yml
│           ├── docker-entrypoint.sh
│           ├── Dockerfile
│           ├── docs.json
│           ├── environment.yml
│           ├── IrisClassifier
│           ├── MANIFEST.in
│           ├── python_version
│           ├── README.md
│           ├── requirements.txt
│           └── setup.py
└── storage.db

5 directories, 15 files
```

repository 아래 지금 생성한 IrisClassifier가 생성된 것을 볼 수 있고, service에 필요한 코드가 담긴 IrisClassifier, docker로 실행할 수 있는 Dockerfile 등이 자동으로 생성된 것을 볼 수 있습니다. 

아래 명령어를 실행해보면 docker image의 list를 보는 것 처럼 생성된 service list를 확인할 수 있습니다.

```bash
user@ubuntu:~/bentoml_example$ bentoml list
BENTO_SERVICE				AGE				APIS					ARTIFACTS			LABEL
IrisClassifier:20220529213403_F2631F	10 minutes and 17.1 seconds	predict<DataframeInput:DefaultOutput>	model<SklearnModelArtifact>
```

조금 더 쉽게 이해해보기 위해 학습 및 저장 코드를 한 번 더 실행하고 다시 확인해봅시다.

```bash
user@ubuntu:~/bentoml_example$ python main.py
[2022-05-29 21:46:48,724] INFO - BentoService bundle 'IrisClassifier:20220529214648_3B586F' saved to: /home/user/bentoml/repository/IrisClassifier/20220529214648_3B586F

user@ubuntu:~/bentoml_example$ tree -L 4 ~/bentoml
/home/user/bentoml
├── logs
│   ├── active.log
│   ├── feedback.log
│   └── prediction.log
├── repository
│   └── IrisClassifier
│   ├── 20220529213403_F2631F
│   │   ├── bentoml-init.sh
│   │   ├── bentoml.yml
│   │   ├── docker-entrypoint.sh
│   │   ├── Dockerfile
│   │   ├── docs.json
│   │   ├── environment.yml
│   │   ├── IrisClassifier
│   │   ├── MANIFEST.in
│   │   ├── python_version
│   │   ├── README.md
│   │   ├── requirements.txt
│   │   └── setup.py
│   └── 20220529214648_3B586F
│   ├── bentoml-init.sh
│   ├── bentoml.yml
│   ├── docker-entrypoint.sh
│   ├── Dockerfile
│   ├── docs.json
│   ├── environment.yml
│   ├── IrisClassifier
│   ├── MANIFEST.in
│   ├── python_version
│   ├── README.md
│   ├── requirements.txt
│   └── setup.py
└── storage.db

7 directories, 26 files

user@ubuntu:~/bentoml_example$ bentoml list
BENTO_SERVICE				AGE				APIS					ARTIFACTS			LABEL
IrisClassifier:20220529214648_3B586F	50.36 seconds			predict<DataframeInput:DefaultOutput>	model<SklearnModelArtifact>
IrisClassifier:20220529213403_F2631F	13 minutes and 35.3 seconds	predict<DataframeInput:DefaultOutput>	model<SklearnModelArtifact>
```

위 과정을 통해 repository/IrisClassifier 하위에 버전 별로 directory가 생성되고, service 실행에 필요한 코드가 각각 생성되는 것을 확인할 수 있습니다. 또한, bentoml list에서 AGE를 기준으로 최신 모델이 무엇인지 확인이 가능하다는 것을 알 수 있습니다.

이제 마지막 과정인 service를 실행하는 방법은 아래와 같습니다.

```bash
user@ubuntu:~/bentoml_example$ bentoml serve IrisClassifier:latest
[2022-05-29 21:59:18,951] INFO - Getting latest version IrisClassifier:20220529214648_3B586F
[2022-05-29 21:59:18,968] INFO - Starting BentoML API proxy in development mode..
[2022-05-29 21:59:18,970] INFO - Starting BentoML API server in development mode..
[2022-05-29 21:59:19,085] INFO - Micro batch enabled for API predict max-latency: 20000 max-batch-size 4000
[2022-05-29 21:59:19,086] INFO - Your system nofile limit is 204800, which means each instance of microbatch service is able to hold this number of connections at same time. You can increase the number of file descriptors for the server process, or launch more microbatch instances to accept more concurrent connection.
======== Running on http://0.0.0.0:5000 ========
(Press CTRL+C to quit)
* Serving Flask app 'IrisClassifier' (lazy loading)
* Environment: production
WARNING: This is a development server. Do not use it in a production deployment.
Use a production WSGI server instead.
* Debug mode: off
* Running on http://127.0.0.1:62629 (Press CTRL+C to quit
```

service를 실행하면 flask 기반의 서버가 동작하는 것을 확인할 수 있습니다. 동작한 서버(localhost:5000)에 접속해보면 swagger 기반으로 API 상세를 확인해볼 수 있습니다.

![swagger ui](/assets/images/posts/2022-5-29-tistory-post-26/img-2.png){: .align-center}
<br>

위 방식은 flask 실행 문구에서도 알 수 있듯이 development server이고, production deployment를 위해서는 아래 명령어를 실행하면 됩니다.

* Tutorial에는 bentoml serve IrisClassifier:latest --production으로 나오는데, 나중에 변경된 것 같습니다.

```bash
user@ubuntu:~/bentoml_example$ bentoml serve-gunicorn IrisClassifier:latest
[2022-05-29 22:18:03,182] INFO - Getting latest version IrisClassifier:20220529214648_3B586F
[2022-05-29 22:18:03,203] INFO - Starting BentoML proxy in production mode..
[2022-05-29 22:18:03,205] INFO - Starting BentoML API server in production mode..
[2022-05-29 22:18:03,267] INFO - Running micro batch service on :5000
[2022-05-29 22:18:03 +0900] [6339] [INFO] Starting gunicorn 20.1.0
[2022-05-29 22:18:03 +0900] [6339] [INFO] Listening at: http://0.0.0.0:38909 (6339)
[2022-05-29 22:18:03 +0900] [6339] [INFO] Using worker: sync
[2022-05-29 22:18:03 +0900] [6340] [INFO] Booting worker with pid: 6340
[2022-05-29 22:18:03 +0900] [6253] [INFO] Starting gunicorn 20.1.0
[2022-05-29 22:18:03 +0900] [6253] [INFO] Listening at: http://0.0.0.0:5000 (6253)
[2022-05-29 22:18:03 +0900] [6253] [INFO] Using worker: aiohttp.worker.GunicornWebWorker
[2022-05-29 22:18:03 +0900] [6341] [INFO] Booting worker with pid: 6341
[2022-05-29 22:18:03,390] INFO - Micro batch enabled for API predict max-latency: 20000 max-batch-size 4000
[2022-05-29 22:18:03,391] INFO - Your system nofile limit is 204800, which means each instance of microbatch service is able to hold this number of connections at same time. You can increase the number of file descriptors for the server process, or launch more microbatch instances to accept more concurrent connection.
```

여기까지가 모델 학습 및 생성, bento service 생성 및 배포까지의 과정입니다.

## **이슈**

1. TypeError: Descriptors cannot not be created directly.   
If this call came from a _pb2.py file, your generated code is out of date and must be regenerated with protoc >= 3.19.0.  
....  

	* pip install protobuf==3.20.1 (protobuf 버전 downgrade)

## **Reference**

* <https://github.com/bentoml/gallery/tree/main/quickstart>
* <https://docs.bentoml.org/en/0.13-lts/quickstart.html>
* <https://zzsza.github.io/mlops/2021/04/18/bentoml-basic/>
