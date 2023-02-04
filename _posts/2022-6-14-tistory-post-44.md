---
title: Python CSV 읽기/쓰기
toc: true
categories:
  - Python
tags:
  - csv
  - Python
---

csv는 **c**omma-**s**eparated **v**alues의 약자로, 말 그대로 comma(`,`)로 데이터를 구분하는 포맷을 말합니다. 예를 들어 아래 표 처럼 데이터가 있을 때 아래와 같이 표현됩니다.

| item        | price | count |
| :---------: | :---: | :---: |
| apple       | 1000  | 20    |
| banana      | 1200  | 10    |
| melon,mango | 5000  | 2     |

```json
item,price,count
apple,1000,20
banana,1200,10
"melon,mango",8000,2
```

그냥 쉽게 표현할 수 있을 것 처럼 보이지만, `"melon,mango"`와 같이 comma(`,`)가 포함된 문자열인 경우 큰따옴표(`"`)로 문자열을 묶어서 표현합니다. 물론 전처리를 통해 쉽게 해결할 수 있지만, 속도와 가독성을 위해 제공되는 `csv` 모듈을 사용하는 것이 좋습니다.

```python
import csv

with open('test.csv', 'w', newline='') as f:
    writer = csv.writer(f)
    writer.writerow(['item', 'price', 'count'])
    writer.writerow(['apple', 1000, 20])
    writer.writerow(['banana', 1200, 10])
    writer.writerow(['melon,mango', 5000, 2])

with open('test.csv', 'r') as f:
    reader = csv.reader(f)
    for line in reader:
        print(line)
        
        
# 출력
['item', 'price', 'count']
['apple', '1000', '20']
['banana', '1200', '10']
['melon,mango', '5000', '2']
```

**csv 포맷**은 데이터 타입(integer, float, string 등)을 구분하지 못하기 때문에 전부 문자열로 출력됩니다. 따라서 변환이 필요한 경우에는 아래와 같은 방식을 활용해야 합니다.

```python
import csv

with open('test.csv', 'w', newline='') as f:
    writer = csv.writer(f)
    writer.writerow(['item', 'price', 'count'])
    writer.writerow(['apple', 1000, 20])
    writer.writerow(['banana', 1200, 10])
    writer.writerow(['melon,mango', 5000, 2])

with open('test.csv', 'r') as f:
    reader = csv.reader(f)
    for line in reader:
        print([int(cell) if cell.isdigit() else cell for cell in line])
        
        
# 출력
['item', 'price', 'count']
['apple', 1000, 20]
['banana', 1200, 10]
['melon,mango', 5000, 2]
```

위와 같이 `isdigit` 함수를 통해 문자열이 숫자로 표현될 수 있는지 확인이 가능합니다. 


 

