---
title: X-Pack Security 적용 (Elastic Stack 8.1 기준)
toc: true
categories:
  - Elastic Stack
tags:
  - Elasticsearch
  - kibana
  - Security
  - X-Pack
---

앞의 포스트에서는 편의를 위해 security 옵션을 비활성화하고 진행했는데, 추후 실사용에서는 반드시 적용해야하는 부분입니다. 그리고 Elastic Stack 8.0이 release되면서 빠르게 8.1이 release됐고, major 변경에 따른 필수 설정이 많이 변경된 것으로 확인했습니다. 여기서 Elastic Stack 8.1 기준으로 security를 적용하기 위해 **1) security 옵션 활성화** 및 **2) 계정 셋업** 하는 과정을 확인합니다.


 


### **Elasticsearch security 설정**


우선 security를 활성화하기 위해서는 CA 인증서 파일을 생성하고 certificate 파일을 만들어야합니다. 아래 명령어를 실행하고 경로와 비밀번호는 빈칸으로 입력해서 넘어갑니다.



```
> bin\elasticsearch-certutil ca
...
Please enter the desired output file [elastic-stack-ca.p12]: 
Enter password for elastic-stack-ca.p12 :
```

그리고 아래 명령어를 이어서 입력합니다. 마찬가지로 경로와 비밀번호 다 빈칸으로 입력해서 넘어갑니다.



```
> bin\elasticsearch-certutil cert --ca elastic-stack-ca.p12
Enter password for CA (elastic-stack-ca.p12) :  
Please enter the desired output file [elastic-certificates.p12]: 
Enter password for elastic-certificates.p12 :
```

그러면 Elasticsearch 폴더에 elastic-stack-ca.p12와 elastic-certificates.p12가 생긴 것을 확인할 수 있는데, 이것을 config 폴더 안에 certs 폴더를 만들어서 넣어줍니다.


 


그리고 Elasticsearch keystore에 password 정보를 넣어줍니다. 비밀번호를 따로 입력하지 않았기 때문에 빈칸으로 입력합니다.



```
> bin\elasticsearch-keystore add xpack.security.http.ssl.keystore.secure_password
Enter value for xpack.security.http.ssl.keystore.secure_password:

> bin\elasticsearch-keystore add xpack.security.http.ssl.truststore.secure_password
Enter value for xpack.security.http.ssl.truststore.secure_password:

> bin\elasticsearch-keystore add xpack.security.transport.ssl.keystore.secure_password
Enter value for xpack.security.transport.ssl.keystore.secure_password:

> bin\elasticsearch-keystore add xpack.security.transport.ssl.truststore.secure_password
Enter value for xpack.security.transport.ssl.truststore.secure_password:
```

 


elasticsearch.yml 하단에 아래 설정을 추가합니다.



```
# elasticsearch.yml
...
xpack.security.enabled: true
xpack.security.http.ssl:
  enabled: true
  verification_mode: certificate
  keystore.path: certs/elastic-certificates.p12
  truststore.path: certs/elastic-certificates.p12
xpack.security.transport.ssl:
  enabled: true
  verification_mode: certificate
  keystore.path: certs/elastic-certificates.p12
  truststore.path: certs/elastic-certificates.p12
```

추가했으면 서버가 정상적으로 동작하는지 실행해봅니다. 웹에서 https://localhost:9200으로 접속했을 때 로그인 창이 뜨면 정상적으로 실행된 것입니다.


 


###  **Elasticsearch 계정 생성**


두 번째 작업을 시작하기 전에 elasticsearch의 certs 폴더를 복사해서 kibana config 폴더에 붙여 넣습니다. 그리고 계정 셋업을 진행하면 되는데, 아래와 같이 **elasticsearch-setup-passwords** 실행 후 각 계정별로 비밀번호를 입력하면 됩니다. (Elasticsearch 서버가 켜진 상태에서 진행해야한다.)



```
> bin\elasticsearch-setup-passwords interactive
...
Enternter password for [elastic]:
Reenter password for [elastic]:
Enter password for [apm_system]:
Reenter password for [apm_system]:
Enter password for [kibana_system]:
Reenter password for [kibana_system]:
Enter password for [logstash_system]:
Reenter password for [logstash_system]:
Enter password for [beats_system]:
Reenter password for [beats_system]:
Enter password for [remote_monitoring_user]:
Reenter password for [remote_monitoring_user]:
```

이름에서 알 수 있듯이 각 기능에 대한 계정으로 분리되어있고, elastic이 root 계정이라고 생각하면 됩니다. 


 


### **Kibana 연동**


이제 kibana.yml에 설정한 계정과 비밀번호를 추가합니다. 계정은 **kibana\_system** 계정을 추가해야합니다. (이 부분도 Elastic Stack 8.0 release 이후 **elastic** 계정으로 설정이 불가능해진 것 같습니다.)



```
# kibana.yml
...
elasticsearch.hosts: ["https://localhost:9200"]
...
elasticsearch.username: "kibana_system"
elasticsearch.password: "enter password"
...
elasticsearch.ssl.keystore.path: config\certs\elastic-certificates.p12
elasticsearch.ssl.keystore.password: ""
elasticsearch.ssl.truststore.path: config\certs\elastic-certificates.p12
elasticsearch.ssl.truststore.password: ""
elasticsearch.ssl.verificationMode: certificate
```

 


이제 모든 설정이 끝났습니다. Kibana를 실행하고 localhost:5601로 접속해보면 아래와 같이 로그인 화면이 뜨는 것을 볼 수 있습니다.


 


![login page](/assets/images/posts/2022-4-14-tistory-post-12/img-1.png)Kibana 로그인 화면




 


로그인은 elastic 계정으로 접속해야 이전과 동일한 화면을 볼 수 있습니다. 


 


 


 

