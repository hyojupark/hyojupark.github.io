---
title: Tistory code style 변경 (highlight.js custom 적용)
toc: true
categories:
  - 기타
tags:
  - highlight
  - highlight.js
  - syntax
  - Tistory
  - vs2015
  - 코드 문법 강조
  - 티스토리
---

Tistory 플러그인에서 highlight.js 기반의 코드 문법 강조(Syntax Highlight)를 제공하지만, 단 7개 테마밖에 제공하지 않습니다. 저는 플러그인 테마가 별로여서 **VS 2015** 테마를 직접 적용했는데, 적용하는 방법을 알려드리겠습니다.

<br>

## **테마 선택**

테마는 <https://highlightjs.org/static/demo/> 여기에서 하나씩 선택해보면서 적절한 것을 찾으면 됩니다.

<br>

## **테마 적용**

<https://highlightjs.org/>에 들어가서 Get version 버튼(지금 기준 11.6.0)을 누릅니다. 그리고 `cndjs`에 link와 script를 복사합니다.

![highlight.js main](/assets/images/posts/2022-9-29-tistory-post-121/img-1.png){: .align-center}

이제 티스토리 관리 페이지에서 꾸미기 > 스킨편집에 들어가서 좌측 상단에 **홈**으로 되어있는 부분을 **글**로 변경합니다. 그리고 우측에 **html 편집** 버튼을 누릅니다. 그러면 HTML 코드가 보이는데, <head> 부분에 복사한 link와 script를 붙여넣습니다. 추가로 `<script>hljs.highlightAll();</script>`를 이어서 넣어줍니다.

![html edit](/assets/images/posts/2022-9-29-tistory-post-121/img-2.png){: .align-center}

마지막으로 link 마지막 부분에 `dfeault.min.css` 부분의 **default**를 찾은 테마 이름으로 바꿔줍니다. 이름은 공백을 `-`로 바꿔서 쓰면 됩니다. 저는 **vs2015**을 적용했습니다.

![edit header](/assets/images/posts/2022-9-29-tistory-post-121/img-3.png){: .align-center}

이제 스타일이 잘 적용된 것을 확인합니다. (지금은 블로그를 옮겨서 스타일이 보이는 것과 다릅니다.)

```python
def test_func_list(_test_list):
    if _test_list is None:
        print('1 not in.')
    elif 1 in _test_list:
        print('1 in.')
    else:
        print('1 not in.')


test_list_1 = None
test_list_2 = [1, 2, 3]

test_func_list(test_list_1)
test_func_list(test_list_2)


# 출력
1 not in.
1 in.
```
