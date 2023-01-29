---
title: Python 파일 읽기/쓰기 (TEXT, JSON)
toc: true
categories:
  - Python
tags:
  - JSON
  - Python
  - Read
  - TEXT
  - Write
  - 파일입출력
---

 


### **TEXT 읽기/쓰기**



```
with open('test.txt', 'w', encoding='utf-8') as f:
    f.write('hello!')

with open('test.txt', 'r', encoding='utf-8') as f:
    text = f.read()
    print(text)


# 출력
hello!
```

 


1. with를 사용하면 해당 context(바로 밑 들여쓰기 부분)를 벗어나면 해당 자원을 반납합니다. 여기서는 open 후 close가 자원 반납에 해당하며, 별도로 close하지 않아도 자동으로 처리된다고 생각하면 됩니다.


2. w, r은 파일 쓰기, 읽기 모드를 의미하며 자세한 설명은 아래와 같습니다.




|  |  |
| --- | --- |
| **모드** | **설명** |
| r | 읽기 모드 |
| w | 쓰기 모드 - 기존 파일 내용 삭제 후 쓰기 |
| a | 추가 모드 - 기존 파일 마지막(맨 밑)에 이어서 쓰기 |
|  |  |


3. encoding은 가급적이면 쓰기 때 부터 'utf-8'로 맞춰서 사용해서 한글 등이 깨지는 일을 방지합니다.


4. with open(...) as f 문법은 f가 open(...) 객체를 가진다고 보면 됩니다.


5. f.write, f.read는 의미 그대로 쓰고 읽을 때 사용하는 함수며, 텍스트를 줄 단위로 읽고 쓸때는 아래와 같은 형태로도 사용 가능합니다.



```
with open('test.txt', 'w', encoding='utf-8') as f:
    f.writelines('\n'.join(['hello', 'world', 'hi', 'bye']))

with open('test.txt', 'r', encoding='utf-8') as f:
    lines = f.readlines()
    print(lines)
    for line in lines:
        print(line.rstrip())
        
        
# 출력
['hello\n', 'world\n', 'hi\n', 'bye']
hello
world
hi
bye
```

 


6. f.writelines는 list를 입력으로 받아서 쓰기를 합니다. join을 이용하면 각 문자열 사이에 개행을 넣어줄 수 있습니다.


7. f.readlines는 파일을 개행 단위로 list로 읽습니다. 읽을때는 개행문자(\n)가 포함되는데, 이는 rstrip을 이용해서 제거할 수 있습니다.


 


 


### **JSON 읽기/쓰기**



```
import json
from pprint import pprint

data = {
    'name': '홍길동',
    'age': 22,
    'address': '서울시 강남구 테헤란로'
}

with open('test.json', 'w', encoding='utf-8') as f:
    json.dump(data, f, indent=4, ensure_ascii=False)

with open('test.json', 'r', encoding='utf-8') as f:
    res = json.load(f)
    print(res)
    pprint(data, width=40)


# 출력
{'name': '홍길동', 'age': 22, 'address': '서울시 강남구 테헤란로'}
{'address': '서울시 강남구 테헤란로',
 'age': 22,
 'name': '홍길동'}
```

1. json은 위와 같이 json.dump와 json.load를 이용해서 읽고 쓸 수 있습니다. 


2. json.dump에서 indent는 들여쓰기를 의미하며, 파일에 들여쓰기까지 된 상태로 쓸 때 사용합니다.


3. json.dump에서 ensure\_ascii는 한글이 hex 형태로 써지는 것을 막아줍니다.


4. pprint는 list, dictionary 등의 포맷을 읽기 쉬운 형태로 출력해줍니다.


 


 


 


 

