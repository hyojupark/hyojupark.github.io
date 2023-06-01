---
title: Feature Engineering에서 결측값(Missing Value) 처리하기
categories:
  - Data
tags:
  - Feature Engineering
  - 결측값
  - Missing Value
# published: false
---

Feature Engineering을 하는데 있어 결측값 처리는 굉장히 중요합니다. 이 결측값이 무엇이고 어떻게 처리할 수 있는지 그 방법에 대해 이야기하겠습니다.


## 결측값(Missing Value)이란
결측값은 테이블 형태의 정형 데이터에서의 빈값을 의미합니다. 이 결측값은 대치하거나 삭제 처리해야하는데, 어떤 결측값이 어떻게 처리되냐에 따라 데이터에 큰 영향이 발생합니다. 아래 테이블을 예시로 설명을 해보겠습니다.

![missing value example 1](/assets/images/posts/2023-05-16-processing-missing-value/missing_value_example1.png){: .align-center}
**결측값 예시**
{: .text-center}

위 표에 3개의 결측값이 발생했습니다. 여기서 결측값이 발생한 이유를 추측해보면 성별이 여자일 경우 몸무게 입력을 안한다고 추측할 수 있습니다. 이때 결측값을 삭제해버리면 데이터의 성별 불균형(Imbalance)가 발생할 수 있습니다.
또 마동석이 나이를 입력하지 않았는데, 그냥 이유없이 입력하지 않았을 수도 있고 나이가 많아서 입력하기 싫어진 것 일수도 있습니다. 후자의 경우 위와 마찬가지로 결측값을 삭제해버리면 나이를 기준으로 불균형이 발생할 수 있습니다.
예시를 기준으로 설명한 것 처럼 결측값은 상황에 따라 다른 의미를 가질 수 있습니다. 이러한 특성을 기준으로 결측값은 크게 **완전 무작위 결측(MCAR)**, **무작위 결측(MAR)**, **비무작위 결측(MNAR)** 3가지로 구분합니다.

<br>

## 결측값의 종류

### 완전 무작위 결측(Missing Completely at Random, MCAR)
결측값이 다른 변수나 데이터와 아무런 관계없이 **완전히 무작위로 발생하는 경우**입니다. 결측값이 다른 변수와 의존관계가 없어 삭제해도 편향 등이 발생하지 않아 비교적 처리하기가 쉽습니다.

![missing value example 2](/assets/images/posts/2023-05-16-processing-missing-value/missing_value_example2.png){: .align-center}
**MCAR 예시**
{: .text-center}

위 예시를 보면 결측값에 패턴이 없는 것을 볼 수 있습니다.


### 무작위 결측(Missing at Random, MAR)
결측값이 **다른 변수에 의해 조건부로 발생하는 경우**입니다. 즉, 결측값이 발생한 이유가 다른 변수에 있는 경우입니다. 이 결측값을 잘못 삭제하면 데이터 편향(Bias)이 발생할 수 있습니다.

![missing value example 3](/assets/images/posts/2023-05-16-processing-missing-value/missing_value_example3.png){: .align-center}
**MAR 예시**
{: .text-center}

위 예시를 보면 일부 몸무게 결측값이 성별에 영향을 받은 것을 볼 수 있습니다.


### 비무작위 결측(Missing Not at Random, MNAR)
결측값이 **누락된 변수의 특성에 의해 발생하는 경우**입니다. 즉, 결측값 자체에 의미가 있으며 숨겨진 패턴이나 규칙을 나타낼 수 있습니다. 

![missing value example 4](/assets/images/posts/2023-05-16-processing-missing-value/missing_value_example4.png){: .align-center}
**MNAR 예시**
{: .text-center}

위 예시를 보면 거주지가 서울만 입력된 것으로 보아 서울 외 지역은 입력하지 않은 것으로 볼 수 있습니다.

<br>

이러한 결측값은 종류에 따라 적절하게 특정 값으로 삭제하거나 채워(대치)야합니다.

<br>

## 결측값 처리

### 결측값의 삭제
결측값을 삭제할때는 결측값만 삭제할 수 없기 때문에 열(Column)을 삭제하거나 행(Row)을 삭제해야합니다.

**열 삭제**는 상당히 많은 데이터가 삭제됩니다. 또한 모델의 성능에 상당히 도움되는 feature가 삭제될 수도 있습니다. 때문에 열 삭제를 할때는 모델의 성능을 충분히 고려하면서 진행해야합니다.

**행 삭제**는 결측값이 발생한 행만 삭제하기 때문에 결측이 발생 비중이 적을 경우 효과적입니다. 하지만 무작정 삭제하면 모델 성능에 큰 영향이 발생할 수 있습니다. 만약 결측값이 <u>비무작위 결측(MNAR)인 경우</u>, 예측에 필요한 중요한 정보가 삭제될 수 있고 <u>무작위 결측(MAR)인 경우</u>, 데이터 편향이 발생할 수 있습니다.

### 결측값 대치
대치는 삭제보다 까다롭지만 중요한 정보가 삭제되거나 편향이 발생하는 것을 방지할 수 있습니다. 

결측값 대치를 하는 방법은 범주를 만들어 전부 `none`으로 채우는 방법도 있고, 숫자의 경우 `평균`, `중앙값`, `최빈값` 등으로 채우는 방법도 있습니다. 이때 실제로 있는 값을 입력해서 잘못 예측되는 경우가 없도록 해야합니다. 예를 들어, 결측값을 0으로 대치했는데 실제 데이터가 0이 발생할 수 있을 경우 성능에 악영향을 미칠 수 있습니다.

<br>

결측값 처리는 위에서 설명한 방법들을 적용할 수 있지만 각 방법별로 단점이 있기 때문에 상황에 맞춰 적절한 처리를 하는 것이 중요합니다. 한번에 처리하려고 하기보다는 여러번 실험을 거쳐 영향도를 최소화하는 처리 방법을 찾아서 적용하는 것이 좋습니다.