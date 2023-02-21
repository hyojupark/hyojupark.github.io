---
title: Ubuntu 전체 파일 내 문자열 치환 (grep, sed)
toc: true
categories:
  - Linux
tags:
  - grep
  - Linux
  - Regex
  - Regular Expression
  - sed
  - ubuntu
  - xargs
  - 문자열 치환
  - 정규식
---

모델 성능 실험 script를 작성하면서 코드의 특정 값을 반복적으로 치환해야할 경우가 생겼습니다. python을 사용하면 command line arguments로 주입할수도 있지만 여기서는 grep과 sed로 특정 값을 치환하는 방법을 설명하겠습니다.

### **방법 1.**

```bash
grep -rl experiment_1 *.py | xargs sed -i 's/experiment_1/experiment_2/g'
```

grep r옵션으로 recursive를 활성화하고 l옵션으로 파일명만 출력합니다. 그리고 sed에 해당 파일명 리스트를 넘겨 `experiment_1`을 `experiment_2`로 전부 치환합니다. 이 방법의 경우 바꾸기 전 이름(experiment_1)을  알고 있어야한다는 단점이 있습니다.

<br>

### **방법 2.**

```bash
grep -rl experiment_[0-9]* *.py | xargs sed -i 's/experiment_[0-9]*/experiment_2/g'
```

방법 2는 바꾸기 전 이름을 정규식을 이용해 패턴으로 검색하는 방법입니다. 이렇게하면 `experiment_`로 시작해 숫자로 끝나는 코드를 정확하게 찾을 수 있습니다. 예를 들어, 바꾸기 전 이름이 `experiment_100a`일 경우 위 코드를 실행하면 `experiment_2a`가 됩니다. `a`까지 포함하고 싶으면 `experiment_[0-9]\*`을 `experiment_[0-9a-zA-Z]\*`와 같이 수정하면 됩니다. 조금 더 자세한 내용은 리눅스 정규표현식([링크](https://ubuntu.com/blog/regex-basics))를 참조 바랍니다.
