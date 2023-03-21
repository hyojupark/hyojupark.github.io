---
title: 데이터 수집 전처리하기 (태그, 특수문자 코드)
categories:
  - ETL
tags:
  - Data
---

웹에 있는 데이터를 수집하다보면 보이는 텍스트와 달리 태그나 특수문자 코드 등이 그대로 함께 수집되는 경우가 있습니다. 예를 들면 아래와 같은 형태로 수집될 수 있습니다.

```python
text = '&lt;p&gt;hi&lt;p&#47;&gt;'
```

이 부분을 전처리할 때는 `특수문자 코드 전처리`와 `태그 전처리` **2단계**에 걸쳐 진행해야합니다.


### 1. 특수문자 코드 전처리
특수문자 코드를 전처리할 때는 html 모듈을 사용하면 됩니다. 사용 방법은 아래와 같습니다.

```python
import html

text = '&lt;p&gt;hi&lt;p&#47;&gt;'
print(html.unescape(text))

# 출력
<p>hi<p/>
```

### 2. 태그 전처리
태그를 전처리할 때는 대표적으로 많이 사용되는 BeautifulSoup을 이용하면 됩니다. 최종적으로 아래와 같이 전처리됩니다.

```python
import html

from bs4 import BeautifulSoup

text = '&lt;p&gt;hi&lt;p&#47;&gt;'
soup = BeautifulSoup(html.unescape(text), 'html.parser')
print(soup.get_text(strip=True))

# 출력
hi
```
