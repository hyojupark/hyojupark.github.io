---
title: Elasticsearch의 update/delete 불용처리, segment merge
toc: true
categories:
  - Elastic Stack
tags:
  - Elasticsearch
  - reindex
  - segment merge
---

Elasticsearch에서 document update 시 아래 그림처럼 업데이트할 document를 쓰되 기존 document를 바로 삭제하지는 않습니다.

![](/assets/images/posts/2022-6-2-elasticsearch-segment-merge/img-1.png){: .align-center}
**< Document update >**
{: .text-center}
<br>

메모리에는 version=2 document를 올려놓지만 version=1이 바로 삭제되지 않고 불용처리만 합니다. delete할 때도 똑같이 동작하는데, 이것은 Elasticsearch의 segment가 불변(immutable)의 특성을 가지고 있어 데이터를 업데이트하지 않기 때문입니다.

그러면 불용처리된 데이터는 계속 쌓이기만 할까요? 당연히 그렇지 않고, background에서 segment merge 과정이 일어납니다. 검색의 효율을 위해 Elasticsearch의 기반인 Lucene에는 수 많은 segment로 나뉘어져있고 특정 주기마다 background에서 segment merge를 진행합니다.

![](/assets/images/posts/2022-6-2-elasticsearch-segment-merge/img-2.png){: .align-center}
**< 출처: https://fdv.github.io/running-elasticsearch-fun-profit/003-about-lucene/003-about-lucene.html >**
{: .text-center}
<br>

예를 들어 아래 그림처럼 document들이 segment에 쌓여있을 경우의 segment merge 과정을 살펴보겠습니다.

![](/assets/images/posts/2022-6-2-elasticsearch-segment-merge/img-3.png){: .align-center}
**< Segment merge 과정 >**
{: .text-center}
<br>

쉽게 생각하면 불용처리된 document를 전부 제거해서 새로운 segment를 만들어 저장하고 기존 segment를 삭제한다고 생각하면 됩니다. 이렇게 하면 읽고 쓰는 속도를 보장하면서 탐색 속도를 함께 보장할 수 있게 됩니다. Lucene은 순차적으로 모든 segment를 탐색하기 때문에 segment merge를 통해 segment 수를 줄여서 탐색 속도를 보장합니다.

이러한 과정은 Elasticsearch의 refresh_interval과도 중요한 관계가 있기 때문에 성능 개선을 위해 이와 같은 동작을 알아둘 필요가 있습니다.
