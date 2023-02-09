---
title: BentoML v0.13.1에서 v1.0으로 Migration
toc: true
categories:
  - MLOps/BentoML
tags:
  - bentoml 0.13.1
  - bentoml 1.0
  - artifact
  - bentofile
  - BentoML
  - build
  - Migration
  - Runner
  - service
---

**BentoML v0.13.1**이 release된지 정확히 1년이 지난 2022년 7월 13일 **v1.0**이 release되었습니다. v1.0이 release되면서 기존 사용법이 조금 변경되었는데, 이에 따른 <u>v0.13.1에서 v1.0으로 넘어가는 과정</u>을 살펴보겠습니다.

## **모델 저장 (Building Bentos)**

```python
from sklearn import datasets, svm

# Load training data
iris = datasets.load_iris()
X, y = iris.data, iris.target

# Model Training
clf = svm.SVC()
clf.fit(X, y)
```

위와 같은 학습 코드가 있을 때, 기존의 모델 저장 방식은 아래와 같이 Service 정의 후 packing 과정이 필요했습니다. 하지만 v1.0부터 `save_model` 함수를 이용해서 바로 모델 저장이 가능해졌습니다.


### **[v0.13.1]**

```python
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


# Create a iris classifier service instance
iris_classifier_service = IrisClassifier()

# Pack the newly traned model artifact
iris_classifier_service.pack('model', clf)

# Save the prediction service to disk for model serving
saved_path = iris_classifier_service.save()
```

<br>

### **[v1.0]**

```python
bentoml.sklearn.save_model("iris_clf", clf)
```

또한 아래에서 설명할 `bentofile.yaml`을 작성했다면 `bentoml build` 명령어로 모델 저장이 가능합니다.

```bash
$ bentoml build

Building BentoML service "iris_classifier:6otbsmxzq6lwbgxi" from build context "/home/user/gallery/quickstart"
Packing model "iris_clf:zy3dfgxzqkjrlgxi"
Locking PyPI package versions..

██████╗░███████╗███╗░░██╗████████╗░█████╗░███╗░░░███╗██╗░░░░░
██╔══██╗██╔════╝████╗░██║╚══██╔══╝██╔══██╗████╗░████║██║░░░░░
██████╦╝█████╗░░██╔██╗██║░░░██║░░░██║░░██║██╔████╔██║██║░░░░░
██╔══██╗██╔══╝░░██║╚████║░░░██║░░░██║░░██║██║╚██╔╝██║██║░░░░░
██████╦╝███████╗██║░╚███║░░░██║░░░╚█████╔╝██║░╚═╝░██║███████╗
╚═════╝░╚══════╝╚═╝░░╚══╝░░░╚═╝░░░░╚════╝░╚═╝░░░░░╚═╝╚══════╝

Successfully built Bento(tag="iris_classifier:6otbsmxzq6lwbgxi")
```

<br>

### **Service 정의**

**Service 정의**는 기존에 decorator에 의존적인 코드에서 벗어 낫습니다. 기존에 decorator를 통해 작성된 환경 설정 및 종속성은 `bentofile.yaml`에 정의합니다.

### **[v0.13.1]**

```python
import pandas as pd

from bentoml import env, artifacts, api, BentoService
from bentoml.adapters import DataframeInput
from bentoml.frameworks.sklearn import SklearnModelArtifact

@env(infer_pip_packages=True)
@artifacts([SklearnModelArtifact('model')])
class IrisClassifier(BentoService):
    @api(input=DataframeInput(), batch=True)
    def predict(self, df: pd.DataFrame):
        return self.artifacts.model.predict(df)
```

<br> 

### **[v1.0]**

```python
import numpy as np
import pandas as pd

import bentoml
from bentoml.io import NumpyNdarray, PandasDataFrame

iris_clf_runner = bentoml.sklearn.get("iris_clf:latest").to_runner()

svc = bentoml.Service("iris_classifier", runners=[iris_clf_runner])

@svc.api(input=PandasDataFrame(), output=NumpyNdarray())
def predict(input_series: pd.DataFrame) -> np.ndarray:
    result = iris_clf_runner.predict.run(input_series)
    return result
```

```yaml
# bentofile.yaml
service: "service.py:svc"
labels:
owner: bentoml-team
project: gallery
include:
- "*.py"
python:
packages:
    - scikit-learn
    - pandas
```

위 코드를 보면 기존에 artifact를 이용해서 predict를 하였다면 v1.0부터는 runner를 이용해 Service를 생성합니다. 이를 통해 async coroutine inference API 정의가 가능해졌습니다.

<br>

위 사항과 관련하여 새로운 기능을 사용해보시려면 공식 문서를 참조 바랍니다.

<br>

## **Reference**

* <https://docs.bentoml.org/en/latest/guides/migration.html>
* <https://docs.bentoml.org/en/latest/concepts/bento.html#bento-build-options>
