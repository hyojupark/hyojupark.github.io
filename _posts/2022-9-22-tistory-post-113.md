---
title: Kibana Tag Cloud 특정 단어 제거 혹은 특정 단어만 보기
toc: true
categories:
  - Elastic Stack
tags:
  - Elasticsearch
  - elk stack
  - exclude
  - include
  - kibana
  - Tag Cloud
  - 단어 제외
---

 


### **특정 단어 제거하기**


![word cloud 1](/assets/images/posts/2022-9-22-tistory-post-113/img-1.png)Exclude 단어 추가 전




위와 같은 형태로 워드 클라우드를 구축했을 때 "이", "시", "자" 등과 같이 무의미해 보이는 단어를 제거하려면 Exclude에 해당 단어를 추가하면 됩니다. 각 단어는 "|"(or)를 기준으로 구분하여 작성 가능합니다.


 


![word cloud 2](/assets/images/posts/2022-9-22-tistory-post-113/img-2.png)Exclude 단어 추가 후




 


 


### **특정 단어만 보기**


특정 단어만 선택하여 워드 클라우드를 구축하고 싶을 때는 Include에 단어를 추가하면 됩니다. 추가하면 해당 단어 외의 단어는 등장하지 않습니다. 각 단어는 위와 동일하게 "|"(or)를 기준으로 구분하여 작성 가능합니다.


 


![word cloud 3](/assets/images/posts/2022-9-22-tistory-post-113/img-3.png)Include 추가 후




 
