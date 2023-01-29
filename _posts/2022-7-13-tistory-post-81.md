---
title: pandas timestamp를 datetime으로 변환
toc: true
categories:
  - Python
tags:
  - Datetime
  - PANDAS
  - Python
  - Timestamp
  - to_datetime
---


```
import pandas as pd

...

pd.to_datetime(df['timestamp'], unit='s')

pd.to_datetime(df['timestamp'], unit='ms')
```

 


unit은 timestamp 포맷에 따라 변경해주면 됩니다. timestamp가 16자리면 ns, 13자리면 ms, 그 이하는 s라고 생각하면 됩니다.

