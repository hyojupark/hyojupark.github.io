---
title: ubuntu session timeout 없애기 (TMOUT)
toc: true
categories:
  - Linux
tags:
  - bashrc
  - bash_profile
  - Linux
  - Session
  - timeout
  - tmout
  - ubuntu
---

ubuntu 서버 사용중에 session이 너무 자주 끊기는게 불편해서 timeout을 수정했습니다. `/etc/profile`에서 직접 수정해도 되지만, 안전하게 개인 계정만 적용하는 방법은 아래와 같습니다.

```bash
$ vi ~/.bash_profile
if [ -f ~/.bashrc ]; then
    . ~/.bashrc
fi

TMOUT=0
export TMOUT

$ source ~/.bash_profile
```

기존에 `bash_profile`이 존재할 경우 `TMOUT` 설정인 아래 2줄만 추가하고, 없는 경우에는 위 `bashrc` 설정 부분을 함께 추가합니다. `TMOUT` 값을 초 단위로 설정하면되며, 0으로 설정하면 timeout이 발생하지 않습니다. 필요에 따라 적정값을 설정하면 됩니다.

추가로 `TMOUT: readonly variable`과 같은 오류가 발생하면 `/etc/bash.bashrc`에 들어가서 `readonly TMOUT`을 주석처리하면 됩니다.

```bash
$ vi /etc/bash.bashrc
...
#readonly TMOUT
export TMOUT
```
