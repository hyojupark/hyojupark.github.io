---
title: python date range list 만들기 (w. pandas)
toc: true
categories:
  - Python
tags:
  - business
  - date
  - date_range
  - Day
  - freq
  - list
  - PANDAS
  - Python
  - RANGE
---


```
import pandas as pd

date_list1 = pd.date_range(start='2022-10-01', end='2022-10-10', freq='D').strftime('%Y-%m-%d').tolist()
date_list2 = pd.date_range(start='2022-10-01', end='2022-10-03', freq='6H').strftime('%Y-%m-%d %H').tolist()

print(date_list1)
print(date_list2)


# 출력
['2022-10-01', '2022-10-02', '2022-10-03', '2022-10-04', '2022-10-05', '2022-10-06', '2022-10-07', '2022-10-08', '2022-10-09', '2022-10-10']
['2022-10-01 00', '2022-10-01 06', '2022-10-01 12', '2022-10-01 18', '2022-10-02 00', '2022-10-02 06', '2022-10-02 12', '2022-10-02 18', '2022-10-03 00']
```

 


위 코드처럼 start date, end date와 freq를 입력해서 date range를 구한 다음 원하는 포맷의 리스트로 저장할 수 있습니다. freq로 B를 입력해서 business day(평일)만 구한다던지 등의 계산도 가능하니 아래 reference를 참조 바랍니다.


 


 


**Reference**


* <https://pandas.pydata.org/docs/user_guide/timeseries.html#offset-aliases>
