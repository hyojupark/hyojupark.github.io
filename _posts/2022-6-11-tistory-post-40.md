---
title: Python 경로 관련 함수
toc: true
categories:
  - Python
tags:
  - directory
  - path
  - Python
  - Split
---

## **현재 경로 (os.getcwd)**

현재 실행중인 코드의 경로를 확인할 수 있습니다. 이 부분은 linux의 **pwd**와 같습니다.

```python
import os

print(os.getcwd())

# 출력
C:\Users\user\PycharmProjects\testProject
```

## **경로 생성 (os.path.join)**

Windows와 Linux는 경로 구분 기호가 다릅니다. 따라서 Windows에서 경로를 입력해서 개발한 코드를 Linux에서 돌리면 오류가 발생합니다. **os.path.join**을 사용하면 개발 환경에 맞는 경로로 생성해주기 때문에 특정 운영체제에 종속되지 않게 할 수 있습니다. 물론 이 방법을 사용하는게 코드를 이해하기도 쉽습니다.

```python
import os

path = os.path.join('project', 'resource', 'asset')
print(path)


# 출력
project\resource\asset
```

추가로 경로 구분 기호는 **os.sep**를 사용할 수도 있습니다.

```python
import os

print('os path separator: ', os.sep)

# 출력
os path separator:  \
```

## **경로 확인 (os.path.(exists, isdir, isfile))**

다음과 같은 방법으로 경로의 유무(**exists**)와 경로가 디렉토리인지(**isdir**) 파일인지(**isfile**) 확인할 수 있습니다. 아래는 `venv` 경로를 만들어놓고 코드를 실행한 결과입니다.

```python
import os

print(os.path.exists('venv'))
print(os.path.isdir('venv'))
print(os.path.isfile('venv'))

# 출력
True
True
False
```

## **추출 (os.path.(split, **splitdrive**, dirname, basename, splitext))**

아래와 같은 방법으로 앞의 경로(**split**)나 드라이브(**splitdrive**)를 분리하거나, 앞의 경로(**dirname**)나 파일 명(**basename**)을 추출할 수 있습니다.

```python
import os

path = os.path.join('project', 'resource', 'asset', 'asset.js')
print(os.path.split(path))
print(os.path.dirname(path))
print(os.path.basename(path))

# 출력
('project\\resource\\asset', 'asset.js')
project\resource\asset
asset.js
```

추가로 **splitext**를 사용해서 파일 명과 확장자를 분리할 수 있습니다. 문자열을 `.` 기준으로 분리하는 방법보다는 아래 방법을 사용하는 것을 권장합니다.

```python
import os

print(os.path.splitext('test.txt'))

# 출력
('test', '.txt')
```

## **디렉토리 생성 (os.mkdir, os.makedirs)**

Python에서 디렉토리를 만드는 방법은 두 가지가 있습니다. **os.mkdir**은 디렉토리를 만들어주지만, 상위 디렉토리가 만드시 존재해야합니다.

```python
import os

path = os.path.join('resource', 'asset')
try:
    os.mkdir(path)
except FileNotFoundError as e:
    print(str(e))

os.mkdir('resource')
os.mkdir(path)
print(os.path.exists(path))

# 출력
[WinError 3] 지정된 경로를 찾을 수 없습니다: 'resource\\asset'
True
```

위와 같이 디렉토리를 한 번에 다 만들려고하면 오류가 발생하는 것을 볼 수 있습니다. 하지만 **os.makedirs**를 사용하면 경로를 한 번에 생성할 수 있습니다.

```python
import os

path = os.path.join('resource', 'asset')
os.makedirs(path)
print(os.path.exists(path))

# 출력
True
```

코드의 가독성과 정확한 동작을 위해 os.path 등의 경로 관련 함수를 적극 활용하기를 바랍니다.
