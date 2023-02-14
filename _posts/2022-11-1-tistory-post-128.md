---
title: Bash script 문자열 리스트 Loop
toc: true
categories:
  - Linux
tags:
  - bash
  - Linux
  - loop
  - script
  - string
  - ubuntu
---

모델 성능 실험을 하게되면서 반복적인 실험을 할 일이 많아졌습니다. 수동으로 하다보니 실수도 잦고 시간도 많이뺏겨 script를 작성하게 되었습니다.

```bash
#!/bin/bash
EXP_NAME_LIST="
experiment_1
experiment_2
experiment_3
"

for exp in $EXP_NAME_LIST;do
    echo "==================================="
    echo "Experiment Name: $exp"
    echo "==================================="

    echo "Success"
done
```

![execute result](/assets/images/posts/2022-11-1-tistory-post-128/img-1.png){: .align-center}
**실행 결과**

MobaXTerm과 같은 툴을 사용하면 특정 문자에 Syntax가 적용돼 가독성 높게 출력도 가능합니다.
