---
title: Python 날짜 연산/변환 (datetime)
toc: true
categories:
  - Python
tags:
  - datetime
  - now
  - python
  - strftime
  - strptime
  - timedelta
  - timestamp
  - timezone
  - weekday
---

Python에는 `datetime` 내장 모듈을 이용해 날짜 관련 기능을 사용할 수 있습니다.

## **날짜 객체 (now, datetime)**

```python
import datetime


now = datetime.datetime.now()
print(now)

date_1 = datetime.datetime(2022, 6, 21)
print(date_1)

date_2 = datetime.datetime(2021, 3, 11, 6, 42, 11)
print(date_2)


# 출력
2022-06-21 21:58:40.852324
2022-06-21 00:00:00
2021-03-11 06:42:11
```

`now`로 현재 시간을 출력할 수 있고, `datetime`에 직접 날짜 정보를 입력해서 생성할 수 있습니다.
<br>

## **날짜 계산 (timedelta)**

```python
import datetime


now = datetime.datetime.now()
print(now)
print(now - datetime.timedelta(days=7))
print(now + datetime.timedelta(weeks=2))

date_1 = datetime.datetime(2022, 6, 14) - datetime.datetime(2022, 6, 7)
print(type(date_1), date_1)


# 출력
2022-06-21 22:06:46.784931
2022-06-14 22:06:46.784931
2022-07-05 22:06:46.784931
<class 'datetime.timedelta'> 7 days, 0:00:00
```

`datetime` 객체에 `timedelta`를 이용해서 7일 전, 2주 후 등의 **날짜 계산**을 할 수 있습니다. 또 `date_1`과 같이 `datetime` 객체끼리 계산해서 시간 차를 `timedelta` 객체로 구할 수도 있습니다.
<br>

## **요일**

```python
import datetime


week_day = ['월', '화', '수', '목', '금', '토', '일']
now = datetime.datetime.now()

print(now, week_day[now.weekday()] + '요일')


# 출력
2022-06-21 22:18:23.340046 화요일
```

`weekday` 함수를 이용해서 요일을 계산할 수 있습니다. 요일 값은 **0 ~ 6**까지 **월요일 ~ 일요일**로 표현됩니다.
<br>

## **Format (strftime, strptime)**

```python
import datetime


now = datetime.datetime.now()
print(now.strftime('%Y-%m-%d'))

date_1_str = '2021-04-12 16:42:15'
date_1 = datetime.datetime.strptime(date_1_str, '%Y-%m-%d %H:%M:%S')
print(type(date_1), date_1)


# 출력
2022-06-21
<class 'datetime.datetime'> 2021-04-12 16:42:15
```

`strftime`을 이용해 `datetime` 객체를 문자열로 변환하거나 `strptime`을 이용해 문자열을 `datetime` 객체로 변환할 수 있습니다. 변환할 때 `%Y`, `%m`, `%d` 등의 포맷을 사용해야하는데 많이 사용되는 코드는 아래 표와 같습니다.

| **format code** | **의미** | **예시** |
| --- | --- | --- |
| %Y | 연도 4자리 | 1999, 2021 |
| %y | 연도 2자리 | 99, 21 |
| %m | 월 2자리 | 01, 02 |
| %-m | 월 | 1, 2 |
| %B | 월 영문 이름 | January, February |
| %d | 일 2자리 | 01, 02 |
| %-d | 일 | 1, 2 |
| %H | 시(24시간) 2자리 | 01, 23 |
| %-H | 시(24시간) | 1, 23 |
| %I | 시(12시간) 2자리 | 01, 12 |
| %-I | 시(12시간) | 1, 12 |
| %M | 분 2자리 | 01, 59 |
| %-M | 분 | 1, 59 |
| %S | 초 2자리 | 01, 59 |
| %-S | 초 | 1, 59 |

<br>

## **timestamp**

```python
import datetime


now = datetime.datetime.now()
print(now.timestamp())

date_2 = datetime.datetime.fromtimestamp(1649770583)
print(date_2)


# 출력
1655818685.626752
2022-04-12 22:36:23
```

`timestamp`는 날짜를 숫자로만 표현한 것으로, 여기서는 초로 환산한 결과입니다. 환산은 <https://www.epochconverter.com/>과 같은 웹사이트에서 변환해서 확인할 수 있습니다. `timestamp`를 사용하는 이유는 아래 `timezone`에서 추가로 설명하겠습니다.
<br>

## **timezone**

시간은 국가 별로 차이가 있기 때문에 한국에서 22시여도 국가 별로 시간이 다 다릅니다.

![](/assets/images/posts/2022-6-21-tistory-post-53/img-1.png){: .align-center}

그래서 **UTC**를 이용해서 표준 시간을 계산해야하며, 한국은 **UTC+9(+9시간)**을 사용합니다. 이렇게 나라 별로 시간을 잘못 저장하면 꼬이는 오류가 발생할 수 있고, 문자열로 저장하면 데이터 크기도 많이 커지기 때문에 날짜를 저장할 때는 `timestamp`로 저장 후 변환하는 방식을 많이 선호합니다.

```python
import datetime
from datetime import timedelta, timezone

now = datetime.datetime.now()
print(now.astimezone(timezone.utc))

kst = timezone(timedelta(hours=9))
print(now.astimezone(kst))


# 출력
2022-06-21 13:55:18.579839+00:00
2022-06-21 22:55:18.579839+09:00
```
