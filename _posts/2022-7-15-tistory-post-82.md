---
title: MLOps level (ML CI/CD/CT Pipeline 자동화)
toc: true
categories:
  - MLOps
tags:
  - CI/CD/CT
  - MLOps
  - MLOps Level
  - pipeline
  - 자동화
---

## **MLOps level 0: Manual process**

![MLOps level 0](/assets/images/posts/2022-7-15-tistory-post-82/img-1.png){: .align-center}
**MLOps level 0 (출처: cloud.google.com)**
{: .text-center}

**MLOps level 0**는 모델을 빌드하고 배포하는 과정이 모두 수동으로 이루어집니다. 이 단계에서는 모델을 만드는 데이터 과학자(Data Scientist)와 모델을 예측 서비스로 제공하는 엔지니어로 분리합니다. 데이터 과학자가 엔지니어에게 모델 아티펙트를 엔지니어에게 전달해서 API로 서빙합니다.

1년에 1~2번 수준으로 <u>모델을 간헐적으로 출시(Release)하는 경우 사용</u>하며, <u>CI/CD와 모니터링을 모두 하지 않고 운영</u>하게 됩니다. <u>ML을 시범적으로 도입하거나 프로토타입 용도로 사용할 경우에 적합</u>합니다.

<br>

## **MLOps level 1: ML pipeline automation**

![MLOps level 1](/assets/images/posts/2022-7-15-tistory-post-82/img-2.png){: .align-center}
**MLOps level 1 (출처: cloud.google.com)**
{: .text-center}

**MLOps level 1**은 ML 파이프라인을 자동화하여 모델을 지속적으로 학습시킬 수 있도록 합니다. 위 그림은 CT(Continuous Training)용 ML 파이프린을 도식화한 그림입니다. 이 단계에서는 빠른 실험을 가능하게하고, 프로덕션으로 빠르게 배포할 수 있습니다. 또한 트리거를 활용하여 모델을 자동으로 학습/검증하고, 결과를 메타데이터 저장소(ML Metadata Store)에 기록하며, 특성 저장소(Feature Store)를 추가 구성하여 데이터 분석에 활용할 수 있습니다.

파이프라인의 새 구현이 자주 배포되지 않고, <u>몇 개의 파이프라인만 관리하는 경우 사용</u>하며, <u>파이프라인 구성 요소를 수동으로 테스트</u>합니다. <u>새로운 데이터를 기반으로 모델을 배포할 경우에 적합</u>합니다.

<br>

##  **MLOps level 2: CI/CD pipeline automation**

![MLOps level 2](/assets/images/posts/2022-7-15-tistory-post-82/img-3.png){: .align-center}
**MLOps level 2 (출처: cloud.google.com)**
{: .text-center}

**MLOps level 2**는 CI/CD 파이프라인을 자동화하여 데이터 과학자가 특성 추출(Feature Extraction), 모델 아키텍처(Model Architecture), 파라미터(Hyperparameter)에 대한 다양한 실험 결과를 빠르게 구현 및 자동화된 파이프라인에 반영할 수 있도록 합니다. 이 단계에서는 파이프라인에서 실행할 코드의 빌드/테스트 및 배포가 자동으로 이루어져 <u>CI/CD/CT 전 단계가 자동화</u>됩니다.

아래 그림은 ML CI/CD/CT 자동화 파이프라인의 단계를 다이어그램으로 보여줍니다.

![Stages of CI/CD automated ML pipeline](/assets/images/posts/2022-7-15-tistory-post-82/img-4.png){: .align-center}
**Stages of the CI/CD automated ML pipeline (출처: cloud.google.com)**
{: .text-center}

위 단계를 간단하게 요약해보면 <u>1) 모델링 코드를 소스 저장소로 푸시</u>, <u>2) 소스 코드 빌드/테스트(CI) 및 파이프라인 구성요소(패키지, 실행파일, 아티팩트) 생성</u>, <u>3) 아티팩트 배포</u>, <u>4) 트리거 혹은 스케줄러 기반 CT</u>, <u>5) 학습된 모델 배포(CD)</u>, <u>6) 서비스 모니터링</u>입니다.

위 단계가 구축되면 데이터 및 비즈니스 환경의 빠른 변화에 대처할 수 있습니다.

<br>

**Reference**

* <https://cloud.google.com/architecture/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning>
