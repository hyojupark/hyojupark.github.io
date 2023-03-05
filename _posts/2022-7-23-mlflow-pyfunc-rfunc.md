---
title: MLflow v1.0+ CLI pyfunc, rfunc 명령어를 models로 통일
toc: true
categories:
  - MLflow
tags:
  - MLflow
  - models
  - pyfunc
  - rfunc
---

일부 작성한지 오래된 튜토리얼의 **CLI에서 pyfunc, rfunc를** 사용하고 있습니다. 해당 명령어가 **v1.0 정식 버전이 출시되면서 models로 통일**되었기 때문에 해당 부분을 공유합니다.

![](/assets/images/posts/2022-7-23-mlflow-pyfunc-rfunc/img-1.png){: .align-center}
**MLflow v1.0 release note 일부**
{: .text-center}

* mlflow pyfnc serve -m runs:/99bb7adb7ecb4874ae7f24be5145eb20/model --port 5001 --no-conda [X]
* mlflow models serve -m runs:/99bb7adb7ecb4874ae7f24be5145eb20/model --port 5001 --no-conda [O]

MLflow 전체에서 `pyfunc`, `rfunc`가 `models`로 대체된 것이 아니라 **CLI만 해당한다는 점** 참고 바랍니다.

최신 버전으로 튜토리얼을 진행할 경우 <https://www.mlflow.org/docs/latest/quickstart.html>의 내용을 병행해서 보는 것을 추천합니다.
