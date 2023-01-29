---
title: Kibana 시각화 index 변경
toc: true
categories:
  - Elastic Stack
tags:
  
---

Kibana에서는 dashboard의 시각화에 사용된 index를 변경하는 것이 어렵습니다. 이 때문에 초기 구축 단계에서 dashboard를 다 만들었는데 index 명을 변경하게되면 dashboard를 처음부터 다시 구축하는 참사가 일어날 수 있습니다.


이럴때 아래와 같은 방법으로 dashboard의 시각화에 사용된 index를 변경할 수 있습니다.


 


 


### **1. 새로 변경할 data view의 id 추출**


![index view](/assets/images/posts/2022-6-16-tistory-post-48/img-1.png)



 


Data Views에서 새로 추가될 data view로 들어가면 위 주소에 빨간색으로 표시한 부분에 해당 data view의 id를 확인할 수 있습니다. 확인된 id 값를 복사합니다.


 


 


### **2. data view의 id 변경**


Saved Objects에 들어가서 변경할 dashboard의 우측 actions를 눌러 Inspect를 누릅니다.


![object id extract](/assets/images/posts/2022-6-16-tistory-post-48/img-2.png)Dashboard inspect




 


스크롤을 내리면 references에 type으로 index-pattern으로 되어있는 부분의 id(빨간색으로 표시한 부분)를 복사한 id로 변경하면 해당 dashboard의 data view가 전부 변경됩니다. (특정 시각화만 변경해야할 경우 기존 data view의 id를 복사했다가 위 화면에서 동일한 id를 검색해 바꾸면 됩니다.)


 


 


### **7.x 이하 버전에서의 index pattern 변경**


위 설명은 8.0 이상 버전에서의 내용으로, 7.x 이하 버전에서는 Saved Objects에서 dashboard가 아닌 visualization을 선택해서 변경해야합니다.


![visualization setting](/assets/images/posts/2022-6-16-tistory-post-48/img-3.png)출처:&nbsp;https://logit.io/blog/post/how-can-i-export-data-from-a-kibana-search




 


 


 


 

