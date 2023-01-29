---
title: Windows에 ELK Stack 구축 (셋업)
toc: true
categories:
  - Elastic Stack
tags:
  - Elastic Stack
  - Elasticsearch
  - elk stack
  - kibana
  - logstash
  - Windows
---

###  **ELK Stack이란**


**ELK Stack**은 **Elasticsearch**, **Logstash**, **Kibana** 세 가지 프로젝트로 구성된 Stack으로, 데이터 수집, 분석, 시각화 기능을 제공합니다. 최근에는 **Beat**가 Stack에 포함되면서 **Elastic Stack**이라고 부릅니다. 일반적으로 서버의 metric이나 log 분석용으로 많이 사용됩니다.


 


현재 Elastic의 최신 버전인 8.1 버전에서 알람이나 레포트 저장과 같은 추가 기능을 사용하기 위해서는 Gold 이상으로 License upgrade가 필요합니다. 이 글에서는 이러한 추가 기능들 없이 Stack을 구축하기 때문에 Basic License로 충분히 가능하여 최신 버전을 설치하지만, 추가 기능을 무료로 사용하실 분들은 구 버전인 7.10.2 버전으로 Stack을 구축하고 Open Distro에서 제공하는 Plugin을 사용하시면 됩니다.


이는 7.10.2 버전을 기준으로 Elasticsearch의 버전이 갈렸기 때문인데, 관련된 내용이 궁금하시면 AWS와 Elastic의 대립 관련된 글을 찾아서 읽어보시길 추천합니다.


 


 


### **구축 방법**


본 글에서는 Windows 10 기반의 구축 방법을 설명하겠습니다. Linux에서 하는 것과 크게 다르지 않기 때문에 이 글을 읽으면 Linux에서도 쉽게 구축 가능합니다.


 


우선 Windows용 Elasticsearch, Logstash, Kibana 압축파일을 각각 받습니다. 여기서는 8.1 버전으로 진행하며, Logstash는 당장 사용하지는 않지만 미리 같이 받아서 풀어줍니다.


 


* Elasticsearch: <https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.1.0-windows-x86_64.zip>
* ​Logstash: <https://artifacts.elastic.co/downloads/logstash/logstash-8.1.0-windows-x86_64.zip>
* Kibana: <https://artifacts.elastic.co/downloads/kibana/kibana-8.1.0-windows-x86_64.zip>


 


모두 폴더 하나에 풀어서 사용하기 편한 에디터로 열어줍니다. 저는 Visual Studio Code를 사용하겠습니다. 최대한 쉽게 사용해보기 위해 인증/로그인에 필요한 옵션은 끄도록 하겠습니다. Elasticsearch의 'config/elasticsearch.yml' 맨 아래에 아래 옵션을 추가합니다. 실제 배포 환경에서는 반드시 키고 진행해주세요.



```
xpack.security.enabled: false
xpack.security.http.ssl.enabled: false
xpack.security.transport.ssl.enabled: false
```

 


그리고 설치된 폴더에서 터미널을 열어서 아래 명령어를 입력해서 Elasticsearch를 실행합니다.



```
C:\ELK Stack> cd elasticsearch-8.1.0
C:\ELK Stack> bin\elasticsearch.bat
```

![](/assets/images/posts/2022-3-13-tistory-post-5/img-1.png)Elasticsearch 실행 화면




 


사진에 표시한 부분을 보면 기존에 설치되어있는 JDK를 무시하고 Elasticsearch 폴더에 있는 JDK를 사용하는 것을 볼 수 있습니다. Elasticsearch에 설치된 JDK 버전은 17.0.2 버전입니다.


 


localhost:9200으로 request를 보냈을 때 아래와 같은 response가 오면 정상 동작 중입니다.



```
C:\ELK Stack>curl localhost:9200
{
  "name" : "DESKTOP-8PSS2P8",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "NZo7oyW1Sgurpr2yk8dGFw",
  "version" : {
    "number" : "8.1.0",
    "build_flavor" : "default",
    "build_type" : "zip",
    "build_hash" : "3700f7679f7d95e36da0b43762189bab189bc53a",
    "build_date" : "2022-03-03T14:20:00.690422633Z",
    "build_snapshot" : false,
    "lucene_version" : "9.0.0",
    "minimum_wire_compatibility_version" : "7.17.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

 


이제 kibana를 실행해봅니다. 실행 방법은 elasticsearch와 비슷합니다.



```
C:\ELK Stack> cd kibana-8.1.0
C:\ELK Stack\kibana-8.1.0> bin\kibana.bat
```

 


브라우저로 localhost:5601에 접속해서 아래 화면과 같이 뜨면 성공적으로 실행된 것입니다.


![](/assets/images/posts/2022-3-13-tistory-post-5/img-2.png)Kibana 첫 실행 화면




 


 


Elasticsearch와 연동이 잘 되었는지 확인해보도록 하겠습니다. 'Explore on my own'을 누르고 우측 탭을 눌러 'Management'에 'Stack Monitoring'을 들어갑니다. 여기서 **Metricbeat** 없이 X-Pack으로 Monitoring 해볼 것이기 때문에 'Or. set up with self monitoring'을 누르고 'xpack.monitoring.collection.enabled'  옵션이 켜지도록 'Turn on monitoring' 버튼을 누릅니다.


 


이제 아래 화면을 통해 Elasticsearch가 잘 연동된 것을 확인할 수 있습니다.


![](/assets/images/posts/2022-3-13-tistory-post-5/img-3.png)Kibana monitoring 화면




 


여기까지 확인했으면 ELK Stack이 잘 구축된 것입니다. 다음엔 logstash를 이용해 Elasticsearch에 데이터를 적재해보겠습니다.


 


 

