---
title: BentoML Yatai로 모델 관리
toc: true
categories:
  - MLOps/BentoML
tags:
  - BentoML
  - Yatai
---

BentoML은 Yatai를 이용해서 모델을 관리할 수 있습니다. 실행 방법은 아래와 같이 2가지가 있지만, 저는 node.js를 설치하기 귀찮아서 docker를 사용했습니다.


 


**방법 1.** (node.js 설치 필요)



```
bentoml yatai-service-start
```

 


**방법 2.**



```
docker run \
-v ~/bentoml:/bentoml \
-p 3000:3000 \
-p 50051:50051 \
bento/yatai-service:latest \
--db-url=sqlite:///bentoml/storage.db \
--repo-base-url=/bentoml/repository
```

 


실행하면 아래와 같이 출력되면서 Yatai 서버가 실행됩니다. 실행 후 localhost:3000으로 접속해보면 아래와 같이 웹 페이지가 출력되는 것을 확인할 수 있습니다.



```
[2022-06-04 08:14:45,704] WARNING - Option --repo-base-url has been deprecated but is still supported in the current release. Consider using --repository-type and its corresponding options in the upcoming releases.
* Starting BentoML YataiService gRPC Server
* Debug mode: off
* Web UI: running on http://127.0.0.1:3000
* Running on 127.0.0.1:50051 (Press CTRL+C to quit)
* Prometheus: running on http://127.0.0.1:3000/metrics

* Help and instructions: https://docs.bentoml.org/en/latest/guides/yatai_service.html
* Web server log can be found here: /root/bentoml/logs/yatai_web_server.log
-----
* Usage in Python:
* bento_svc.save(yatai_url="127.0.0.1:50051")
* from bentoml.yatai.client import get_yatai_client
* get_yatai_client("127.0.0.1:50051").repository.list()
* Usage in CLI:
* bentoml list --yatai-url=127.0.0.1:50051
* bentoml containerize IrisClassifier:latest --yatai-url=127.0.0.1:50051
* bentoml push IrisClassifier:20200918001645_CD2886 --yatai-url=127.0.0.1:50051
* bentoml pull IrisClassifier:20200918001645_CD2886 --yatai-url=127.0.0.1:50051
* bentoml retrieve IrisClassifier:20200918001645_CD2886 --yatai-url=127.0.0.1:50051 --target_dir="/tmp/foo/bar"
* bentoml delete IrisClassifier:20200918001645_CD2886 --yatai-url=127.0.0.1:50051
```

![yatai web ui](/assets/images/posts/2022-6-4-tistory-post-32/img-1.png)Yatai Web UI




 


Yatai 서버를 실행했을 때 출력되는 문구를 보면 Usage in CLI 부분이 있는데, Yatai 서버를 이용해서 배포관리할 수 있음을 알 수 있습니다. 지금은 서버의 동작을 확인하기 위해 db-url과 repo-base-url에 기존에 생성했놨던 db와 repository를 연결했지만, 별도로 분리해서 사용하면 Git을 사용할 때 local과 remote를 분리해서 사용하듯이 사용할 수 있습니다. 이 부분을 해보기 위해 아래와 같이 docker로 Yatai 서버를 다시 실행해봅니다.



```
docker run \
-p 3000:3000 \
-p 50051:50051 \
bento/yatai-service:latest
```

 


Yatai 서버에 접속해보면 모델 정보가 비어있을 것을 확인할 수 있습니다.


![yatai web ui 2](/assets/images/posts/2022-6-4-tistory-post-32/img-2.png)Yatai Web UI




 


이제 Yatai 서버에 기존에 생성했던 bento service 하나를 배포해보겠습니다. bento service 하나를 선택해서 push 명령어를 이용해 Yatai 서버에 배포합니다.



```
user@ubuntu:~$ bentoml list
BENTO_SERVICE AGE APIS ARTIFACTS LABELS
IrisClassifier:20220529214648_3B586F 5 days and 19 hours predict<DataframeInput:DefaultOutput> model<SklearnModelArtifact>
IrisClassifier:20220529213403_F2631F 5 days and 20 hours predict<DataframeInput:DefaultOutput> model<SklearnModelArtifact>

user@ubuntu:~$ bentoml push IrisClassifier:20220529214648_3B586F --yatai-url=127.0.0.1:50051
[2022-06-04 17:41:59,203] INFO - BentoService bundle 'IrisClassifier:20220529214648_3B586F' saved to: /root/bentoml/repository/IrisClassifier/20220529214648_3B586F
Pushed IrisClassifier:20220529214648_3B586F to 127.0.0.1:50051
```

 


배포된 bento service가 Web UI에 올라오는 것을 확인할 수 있습니다. 


![yatai web ui 3](/assets/images/posts/2022-6-4-tistory-post-32/img-3.png)Yatai Web UI




 


마지막으로 배포된 bento service를 Yatai에서 retrieve해보겠습니다. 



```
user@ubuntu:~$ bentoml retrieve IrisClassifier:20220529214648_3B586F --yatai-url=127.0.0.1:50051 --target_dir="iris_latest_artifact"
Save IrisClassifier:20220529214648_3B586F artifact to directory iris_latest_artifact
user@ubuntu:~$ tree -L 2 iris_latest_artifact/
iris_latest_artifact/
├── bentoml-init.sh
├── bentoml.yml
├── docker-entrypoint.sh
├── Dockerfile
├── docs.json
├── environment.yml
├── IrisClassifier
│   ├── artifacts
│   ├── bentoml.yml
│   ├── init.py
│   ├── iris_classifier.py
│   ├── pycache
│   └── zipimports
├── MANIFEST.in
├── python_version
├── README.md
├── requirements.txt
└── setup.py

4 directories, 14 files
```

 


Yatai를 이용하면 local에서 테스트 후 remote에 배포함으로써 배포/관리가 좀 더 유용함을 확인할 수 있습니다. 

