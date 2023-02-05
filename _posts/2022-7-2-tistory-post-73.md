---
title: Streamlit - Python으로  데이터 시각화 웹을 가장 빠르게 개발
toc: true
categories:
  - Trend
tags:
  - data visualization
  - Python
  - streamlit
  - 시각화
---

![streamlit logo](/assets/images/posts/2022-7-2-tistory-post-73/img-1.png){: .align-center}

최근 ML 분야가 뜨면서 데이터 분석가, AI 연구자들이 다양한 Task에 맞는 모델을 빠르게 만들어 내면서 시각화하기 위해 프로토타입 개발을 하는 경우가 많아졌습니다. 필요한 프로토타입을 웹으로 개발하는 경우가 많은데 개발자가 아니면 만드는데 알아야할 기술도 많고 시간도 오래걸릴 수 밖에 없습니다. **Streamlit**을 이용하면 단 몇 줄만에 웹 서비스를 개발할 수 있습니다.

![example code](/assets/images/posts/2022-7-2-tistory-post-73/img-2.png){: .align-center}
**< 예제 코드 >**
{: .text-center}

위 코드만 봐도 import를 포함해서 <u>단 3줄</u> 만으로 사진과 같은 웹 서비스를 개발할 수 있습니다. 이 외에 기존에 분석에 사용하는 `pandas`, `matplotlib`, `pyplot`, `plotly` 등과 쉽게 연동이 가능합니다.

<br>

![example code 2](/assets/images/posts/2022-7-2-tistory-post-73/img-3.png){: .align-center}

이런식으로 pandas `DataFrame` 객체를 <u>streamlit 함수 하나만 사용</u>해서 바로 웹으로 표현할 수 있습니다.

최근 Snowflake에 인수된 만큼 지금보다 더 좋은 데이터 솔루션이 되기를 기대합니다.
