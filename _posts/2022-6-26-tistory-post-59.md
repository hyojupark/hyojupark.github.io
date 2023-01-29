---
title: PyCharm에서 Python main 함수 빠르게 작성하기
toc: true
categories:
  - Python
tags:
  - main
  - pycharm
  - 자동 생성
---

Python의 main 함수는 고작 한줄에 불과하지만, 종종 기억나지 않아 찾아보거나 입력하기 귀찮은 경우가 많습니다.



```
if __name__ == '__main__':
    ...
```

 


PyCharm에서는 이와 같이 자주 사용되는 코드의 자동 완성 기능을 지원합니다. 윈도우 기준 Ctrl + j를 누르면 자동 완성이 가능한 리스트를 확인할 수 있습니다.


![live template list](/assets/images/posts/2022-6-26-tistory-post-59/img-1.png)Ctrl + j (live template list)




 


위 리스트 중 가장 마지막에 있는 main이 우리가 필요한 main 함수임을 확인할 수 있습니다. PyCharm에서 main을 입력하면 아래와 같이 나옵니다. 그대로 엔터를 누르면 바로 main 함수가 생성되는 것을 확인할 수 있습니다.


![main auto complete](/assets/images/posts/2022-6-26-tistory-post-59/img-2.png)
![main auto complete result](/assets/images/posts/2022-6-26-tistory-post-59/img-3.png)



 


이 부분이 익숙해져서 main 함수 작성하는데 머리쓰지 않는 생활 하시길 바랍니다.

