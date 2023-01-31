---
title: Windows에 ELK Stack 구축 3 (시각화)
toc: true
categories:
  - Elastic Stack
tags:
  - Elastic Stack
  - Elasticsearch
  - elk stack
  - kibana
  - Windows
---
이번엔 Kibana Dashboard를 이용해서 수집한 데이터를 시각화해보겠습니다.

## **시각화**
Kibana 8.0+부터는 Visualize를 만들어놓지 않아도 Dashboard에서 바로 시각화가 가능합니다. 바로 Dashboard 하나를 생성합니다.

![dashboard main](/assets/images/posts/2022-4-3-tistory-post-8/img-1.png)
**< Dashboard 첫 화면 >**
{: .text-center}

첫 화면에 대해서 간단하게만 짚고 넘어가면 상단의 **검색**, **필터**, **기간 설정**은 Dashboard가 구축된 상태에서 디테일하게 시각화할 때 사용합니다. 그리고 **Create visualization**은 데이터를 기반으로 visualize를 추천받을 때, **Select type**은 visualize를 직접 선택해서 구축할 때, **Add from library**는 미리 정의한 visualize를 사용할 때라고 생각하면 됩니다.
여기서는 간단하게 시각화해보기 위해 **Create visualization**으로 진행하겠습니다. 우선 기간을 수집된 데이터에 맞춰 변경해야하는데, 위 화면 기준으로  달력 아이콘이 아닌 Last 1 year 부분을 클릭해서 기간을 정확히 설정합니다. 저는 2021.7.1 ~ 2021.7.31에 맞춰 기간을 설정했습니다. 그리고 **Create visualization**을 진행합니다.

![create visualization main](/assets/images/posts/2022-4-3-tistory-post-8/img-2.png)
**< Create visualization 첫 화면 >**
{: .text-center}

첫 시각화로 일별 기사 수 차트를 만들겠습니다. 우선 **@timestamp**를 화면 가운데로 드래그합니다.

![@timestamp drag](/assets/images/posts/2022-4-3-tistory-post-8/img-3.png)
**< @timestamp 드래그 후 >**
{: .text-center}

하단에 추천과 함께 첫번째 추천 시각화가 화면에 나옵니다. 얼핏보면 벌써 일별 기사 수가 나온 것 같지만, 자세히보면 12시간 단위로 나온 것을 확인할 수 있습니다. 이것을 일단위로 변경하기 위해 우측의 Horizontal axis의 @timestamp를 클릭하고 **Customize time interval**을 켜서 일단위로 변경해줍니다.

![time interval](/assets/images/posts/2022-4-3-tistory-post-8/img-4.png)
**< time interval 변경 후 >**
{: .text-center}

이제 차트가 일별로 잘 나온것을 확인할 수 있습니다. 이제 우측 상단의 **Save and return**을 눌러 저장하고 나갑니다.

만든 시각화가 잘 생성된 것을 확인할 수 있습니다. 추가로, 방금 시각화할 때 추천에 있던 전체 기사 수도 추가해보겠습니다.

![add chart](/assets/images/posts/2022-4-3-tistory-post-8/img-5.png)
**< 차트 추가 후 Dashboard 화면 >**
{: .text-center}

여기서 시각화를 좀 더 진행해보겠습니다. 이번엔 언론사 별 기사 수를 도넛 차트로 시각화해보겠습니다. 동일하게 진행 후 **press.keyword**를 드래그해서 아래 추천 중 도넛 차트를 선택합니다. 

![select donut chart](/assets/images/posts/2022-4-3-tistory-post-8/img-6.png)
**< 도넛 차트 선택 후 >**
{: .text-center}

이렇게보면 Other 비중이 높아 다소 불편하게 시각화됩니다. 여기선 상위 5개만 차트가 생성되는 것의 수량을 늘려 개선하겠습니다. 우측의 Slice by에서 Top values of press.keyword를 클릭해서 **Number of values**를 30으로 조정하겠습니다.

![set number of values](/assets/images/posts/2022-4-3-tistory-post-8/img-7.png)
**< Number of values 조정 후 >**
{: .text-center}

이제 그럴듯하게 시각화 된 것을 확인할 수 있습니다.

![final visualization](/assets/images/posts/2022-4-3-tistory-post-8/img-8.png)
**< 최종 시각화 >**
{: .text-center}

![save dashboard](/assets/images/posts/2022-4-3-tistory-post-8/img-9.png){: .align-center}
**< Dashboard 저장 >**
{: .text-center}

마지막으로 만든 Dashboard를 저장해서 마무리합니다. 저장할때는 반드시 **Store time with dashboard**를 체크해서 설정한 기간이 함께 저장되도록합니다.

여기까지 기본적인 시각화를 진행해봤습니다. 다음에는 기사에서 이슈가된 단어를 추출해서 Word Cloud(Tag Cloud)​로 시각화해보도록 하겠습니다.
