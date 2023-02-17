---
title: Kibana 시각화에서 새로 추가한 field가 보이지 않을 때 (7.x 이하)
toc: true
categories:
  - Elastic Stack
tags:
  - Field
  - kibana
  - Refresh
---

7.x 이하 버전을 사용할 때, **index**에 새로 추가한 **field**로 **Visualize(시각화)**를 추가하려하면 추가된 **field**가 보이지 않습니다. 이는 **index pattern**의 **mapping**에 반영되지 않았기 때문인데, 아래와 같이 **index pattern**으로 가서 **refresh**를 진행하면 추가된 것을 확인할 수 있습니다.

![kibana refresh ui](/assets/images/posts/2022-6-16-kibana-invisible-new-field/img-1.png){: .align-center}
**< Refresh field list >**
{: .text-center}
<br>

참고로 8.0 이상의 버전부터는 **index pattern**이 **data view**로 바뀌면서 **refresh** 버전이 사라지고 **field**가 새로 추가되면 바로 반영되도록 변경되었습니다.

