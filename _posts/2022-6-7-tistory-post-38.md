---
title: Python tqdm (progress bar)
toc: true
categories:
  - Python
tags:
  - progress
  - Python
  - tqdm
---

Python에서는 tqdm을 이용하면 loop의 진행 현황을 확인할 수 있습니다. 간단하게 for 문을 예시로 확인해보겠습니다.



```
import time

from tqdm import tqdm

sum_num = 0
for i in tqdm(range(1, 100)):
    time.sleep(0.1)
    sum_num += i
    
    
# 출력
100%|██████████| 99/99 [00:10<00:00,  9.48it/s]
```

 


위 처럼 간단하게 사용이 가능하고, 다양한 경우에도 활용이 가능합니다. 아래는 파일 용량을 기준으로 파일 전체 내용을 읽는 progress를 tqdm으로 작성한 예시입니다. (sample.txt는 아무 내용이나 텍스트로 채워서 넣었습니다.)



```
import os

from tqdm import tqdm

with tqdm(total=os.path.getsize('sample.txt')) as pbar:
    with open('sample.txt', 'r') as f:
        for line in f:
            pbar.update(len(line) + 1)
            
            
# 출력
100%|██████████| 1622400/1622400 [00:00<00:00, 73678932.09it/s]
```

 


텍스트 문자에서 escape character 처리 관련 문제로 1을 더해서 계산한 내용입니다. 처음에 파일의 사이즈를 읽어서 total로 저장해놓고 읽은 내용 만큼 update를 해서 progress를 채우는 방식입니다.


 


위 두 번째 방식은 다른 여러 라이브러리(pandas, tensorflow, boto3 등)에서 tqdm을 연동해서 사용할 때 많이 사용하는 방식이기 때문에 잘 알아두었다가 적재적소에 사용하면 좋습니다.


 

