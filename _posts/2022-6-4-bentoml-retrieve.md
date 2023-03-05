---
title: BentoML Retrieve
toc: true
categories:
  - BentoML
tags:
  - artifact
  - BentoML
  - retrieve
---

BentoML에서 저장한 Artifact를 찾아서 특정 위치에 저장

* Artifact를 가져다 쓸 때 복사가 아닌 해당 명령어를 활용

```bash
user@ubuntu:~$ bentoml list
BENTO_SERVICE AGE APIS ARTIFACTS LABELS
IrisClassifier:20220529214648_3B586F 5 days and 18 hours predict<DataframeInput:DefaultOutput> model<SklearnModelArtifact>
IrisClassifier:20220529213403_F2631F 5 days and 18 hours predict<DataframeInput:DefaultOutput> model<SklearnModelArtifact>

user@ubuntu:~$ bentoml retrieve IrisClassifier:latest --target_dir=iris_latest_artifact
[2022-06-04 15:59:33,059] INFO - Getting latest version IrisClassifier:20220529214648_3B586F
Save IrisClassifier:latest artifact to directory iris_latest_artifact

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
