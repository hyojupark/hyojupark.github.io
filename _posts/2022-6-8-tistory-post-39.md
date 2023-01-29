---
title: Python Type Hint (자료형 명시)
toc: true
categories:
  - Python
tags:
  - ANY
  - ellipsis
  - NoReturn
  - optional
  - Python
  - Type Hint
  - typing
  - Union
---

Python은 기본적으로 변수를 사용할 때 자료형을 명시하지 않고 사용할 수 있습니다. 때문에 함수를 사용할 때도 파라미터나 반환의 자료형을 정의하지 않아도 동작합니다. 이것은 빠르고 가볍게 개발하는 입장에서 장점처럼 느껴지지만, 협업이나 유지보수 측면에서는 단점이 될 수 있습니다. 여기서는 이 단점을 극복하기 위한 Type Hint에 대해 설명하겠습니다.


 


### **Type Hint(자료형 명시)**


Python에서는 자료형을 명시하는 것을 Type Hint라고 합니다. 말 그대로 변수, return 등의 type이 무엇인지 hint를 준다는 의미로 보시면 됩니다. 사용 방법은 아래 예시와 같습니다.



```
def sum_func(a: int, b: int) -> int:
    return a + b


n1: int = 10
n2: int = 20
print(sum_func(n1, n2))
```

 


변수와 함수 parameter의 자료형은 변수 우측에 colon(:)을 쓰고 자료형을 명시해주면 됩니다. 또한 함수의 반환형은 화살표(->)로 나타냅니다. 저렇게 자료형을 명시해놓으면 IDE에서 자료형에 맞지 않을 경우 warning을 띄워줍니다.


 


 


### **Typing**


Type Hint를 사용할 때 위 처럼 기본적으로 제공되는 자료형 외에는 typing에 있는 자료형으로 대체할 수 있습니다. set, list, dict의 경우 typing에 정의되어있어 둘 중 골라서 사용이 가능하고, Generator, Iterator와 같은 형태는 typing을 통해서 사용 가능합니다.



```
from typing import Set, List, Dict, Generator, Iterator


var1: set = ...
var2: list = ...
var3: dict = ...
var4: Set = ...
var5: List = ...
var6: Dict = ...
var7: Generator = ...
var8: Iterator = ...
```

 


참고로 "..."은 ellipsis는 비운다는 의미로, 위의 경우에는 변수를 선언하고 값은 정의하지 않는다는 표현으로 보면 됩니다. 이는 함수에서도 사용 가능한데, pass를 입력하는 것과 동일합니다.


var1, var2, var3은 자료형을 그대로 사용한 형태이고, var4, var5, var6은 typing에서 제공되는 자료형을 사용한 결과입니다. 두 방식의 차이는 없고, 저는 Type Hint를 뚜렷하게 표시하기 위해 후자를 선호합니다. var7, var8은 자료형에 없는 형식을 typing을 통해 명시한 것입니다. 이 외의 다른 클래스도 이와 같은 방식으로 명시할 수 있습니다.


 


 


### **Union**


Python은 C나 Java와 다르게 하나의 함수 파라미터에 다양한 자료형을 입력으로 사용할 수 있기 때문에 Type Hint 또한 이 부분이 지원됩니다. 사용 방법은 아래 예시와 같습니다.



```
from typing import Union


def sum_func(a: Union[int, float], b: Union[int, float]) -> Union[int, float]:
    return a + b


n1: int = 10
n2: float = 20.5
print(sum_func(n1, n2))
```

 


이런식으로 Union을 이용해서 복수의 자료형을 명시할 수 있고, 3개 이상도 명시가 가능합니다.


 


 


### **Optional**


복수의 자료형을 명시할 때 None을 포함하는 경우가 있을 수 있는데, 이 경우에는 Optional을 사용하는 것이 바람직합니다. (이 부분은 Java에서도 동일하게 사용됩니다.)



```
from typing import Optional


def printer(param: Optional[str]):  # Union[None, str]과 동일
    print('input parameter: ', param)


printer(None)
printer('hello')


# 출력
input parameter:  None
input parameter:  hello
```

 


 


### **NoReturn**


함수의 반환형이 없는 경우(void 형과 같은) NoReturn으로 명시가 가능합니다. 위 Optional의 printer 함수의 반환형을 NoReturn으로 명시하면 아래와 같습니다.



```
from typing import Optional, NoReturn


def printer(param: Optional[str]) -> NoReturn:
    print('input parameter: ', param)


printer('hello')
```

 


 


### **Any**


Any는 어떠한 자료형이 들어올지 모르거나, 무엇이 들어와도 상관없는 경우에 사용합니다. 가급적 사용하지 않는 것을 추천하지만, 그럼에도 많이 사용하게 되기도 합니다. 사용 방법은 위에서 설명한 것들과 동일합니다.



```
def sum_func(a: Any, b: Any) -> Any:
    return a + b


n1: int = 10
n2: int = 20
print(sum_func(n1, n2))
```

 


 


주로 많이 사용되는 것들은 이정도이고, 더 궁금하다면 공식 문서(<https://docs.python.org/ko/3/library/typing.html>)에서 조금 더 자세한 내용을 확인할 수 있습니다.


 


 


 


 


 


 

