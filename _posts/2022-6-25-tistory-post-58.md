---
title: Python random
toc: true
categories:
  - Python
tags:
  - choice
  - Python
  - randint
  - random
  - randrange
  - sample
  - SEED
  - shuffle
  - Uniform
---

 


### **random**



```
import random

print(random.random())
print(random.random())
print(random.random())


# 출력
0.21860865421826092
0.29930835369396347
0.24794564848054634
```

random 함수는 0~1 사이의 랜덤값을 생성합니다. 일단적으로는 아래와 같이 랜덤값을 활용할 수 있습니다.


 



```
import random


for i in range(5):
    if random.random() > 0.5:
        print('Up')
    else:
        print('Down')
        
        
# 출력
Up
Up
Down
Down
Up
```

 


 


### **randint**



```
import random

print(random.randint(1, 10))
print(random.randint(1, 10))
print(random.randint(1, 10))


# 출력
1
6
10
```

randint는 입력한 정수 사이의 랜덤한 정수 값을 생성합니다.


 


 


### **uniform**



```
import random


print(random.uniform(0, 10))
print(random.uniform(0, 10))
print(random.uniform(0, 10))


# 출력
2.326346256350856
4.4973820432484075
0.013371016445407058
```

randint가 정수 값을 생성한다면 uniform은 실수 값을 생성합니다.


 


 


### **randrange**



```
import random


print(random.randrange(0, 10, 2))
print(random.randrange(0, 10, 2))
print(random.randrange(0, 10, 2))


# 출력
4
2
0
```

randrange는 randint에서 추가로 step을 지정할 수 있습니다. 위와 같이 step을 2로 설정하면 랜덤한 짝수 값을 생성할 수 있습니다.


 


 


### **choice**



```
import random

num_list = [1, 2, 3, 4]
print(random.choice(num_list))
print(random.choice(num_list))
print(random.choice(num_list))


# 출력
1
4
2
```

choice는 list에 있는 값 중 하나를 랜덤하게 선택할 수 있습니다.


 


 


### **sample**



```
import random


print(random.sample([1, 2, 3, 4, 5, 6, 7, 8, 9, 10], 4))


# 출력
[6, 5, 7, 8]
```

sample은 choice가 랜덤한 값 하나를 선택한다면 n개를 선택할 수 있는 함수입니다. 위와 같이 4를 입력하면 랜덤한 4개 값을 선택합니다.


 


 


### **shuffle**



```
import random


num_list = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
random.shuffle(num_list)
print(num_list)


# 출력
[10, 9, 8, 4, 7, 2, 3, 5, 1, 6]
```

 


 


### **seed**



```
import random


random.seed(42)
print(random.random())
print(random.random())
print(random.random())


# 출력
0.6394267984578837
0.025010755222666936
0.27502931836911926
```

seed는 seed로 사용되는 값에 따라 매 실행 마다 랜덤값을 동일하게 유지해줍니다. 각 랜덤값들은 다르지만, 매 실행마다 동일한 랜덤값이 생성되는 것을 확인할 수 있습니다.


 

