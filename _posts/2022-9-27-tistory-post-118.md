---
title: ubuntu ... not available&#58; NO_PUBKEY 오류
toc: true
categories:
  - Linux
tags:
  - apt
  - keyserver
  - Linux
  - NO_PUBKEY
  - ubuntu
  - Update
---

 


### **오류 상황**


**apt update** 과정에서 **NO\_PUBKEY** 관련 오류 발생


![error situation](/assets/images/posts/2022-9-27-tistory-post-118/img-1.png)



 


 


### **해결 방법**


1. 위 key 그대로 등록



```
$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4EB27DB2A3B88B8B
```

 


2. key server에서 public key 등록


* <https://pgp.mit.edu/>에 접속해서 0x{NO\_PUBKEY} 포맷으로 검색 (여기선 0x4EB27DB2A3B88B8B)
* 표시한 부분을 key로 등록


![search key](/assets/images/posts/2022-9-27-tistory-post-118/img-2.png)




```
$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv D38B4796
```

 

