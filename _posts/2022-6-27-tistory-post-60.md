---
title: Elasticsearch upsert (update or insert)
toc: true
categories:
  - Elastic Stack
tags:
  - doc_as_upsert
  - Elasticsearch
  - insert
  - Update
  - upsert
---

Elasticsearch에 데이터를 적재할 때 동일한 id가 존재하면 update하고 없으면 insert하고 싶을 때 upsert를 사용하면 가능합니다. update query에 "doc\_as\_upsert": true를 입력하면 됩니다.


![id update](/assets/images/posts/2022-6-27-tistory-post-60/img-1.png)doc\_as\_upsert 없이 없는 id update




 


doc\_as\_upsert 없이 없는 id를 update하면 위와 같이 오류가 발생합니다. 하지만 doc\_as\_uspert 옵션을 줄 경우 아래와 같이 insert가 동작하는 것을 확인할 수 있습니다.


![id update 2](/assets/images/posts/2022-6-27-tistory-post-60/img-2.png)doc\_as\_upsert 옵션을 켜고 없는 id update

![id update 2 check](/assets/images/posts/2022-6-27-tistory-post-60/img-3.png)insert된 document 확인




 


이제 생성된 ducment id에 그대로 gender field를 추가해서 update를 해보면 아래와 같이 update가 동작하는 것을 볼 수 있습니다.


![id update 3](/assets/images/posts/2022-6-27-tistory-post-60/img-4.png)doc\_as\_upsert를 켜고 존재하는 id update

![id update 3 check](/assets/images/posts/2022-6-27-tistory-post-60/img-5.png)update된 document 확인




 


 


정리해보면 아래와 같습니다.




|  |  |  |
| --- | --- | --- |
| **옵션** | **ID 유무** | **동작** |
| doc\_as\_upsert=false | 유 | updqte |
| doc\_as\_upsert=false | 무 | error |
| doc\_as\_upsert=true | 유 | update |
| doc\_as\_upsert=true | 무 | insert |


 

