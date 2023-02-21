---
title: PyCharm 원격 개발 환경 구축하기 (Remote Interpreter, Remote Deployment)
toc: true
categories:
  - Python
tags:
  - Automatic
  - deployment
  - interpreter
  - pycharm
  - Python
  - remote
  - venv
  - 원격
---

**\*해당 기능은 PyCharm professional에서 제공되는 기능입니다.\***

**PyCharm**을 이용한 원격 개발 환경을 구축은 **Remote Interpreter** 및 **Remote Deployment**을 각각 설정함으로써 구축할 수 있습니다.

구축이 완료되면 로컬에서 개발하고 서버에서 코드를 실행(**Remote Deployment**)하거나, 빌드 환경을 서버에 올려놓고 사용(**Remote Interpreter**)이 가능합니다.

따라서 원격 개발 환경을 구축하기 위해서는 **Remote Interpreter**와 **Remote Deployment** 각각 설정해야합니다.

<br>

## **Remote Interpreter 설정**

**Remote Interpreter**를 설정하면 서버에 가상 환경을 구축해놓고 사용이 가능합니다.

`PyCharm Settings > Project > Python Interpreter > Add Interpreter > On SSH` 순서로 들어가서 Interpreter를 추가합니다.

그리고 사용중인 서버 IP와 계정/비밀번호 정보를 입력해서 **SSH Connection**을 추가합니다.

다음으로 **Virtualenv Environment**를 추가합니다. 이미 생성한 virtualenv가 있는 경우 바로 잡아도 되고, 없는 경우 추가할 경로와 base interpreter를 잡아도 됩니다. **Location**에는 venv directory가 별도로 생성되지 않기 때문에, 원하는 경로 뒤에 venv를 붙여줍니다. **Base interpreter**는 `/usr/bin/python3.x`로 잡으면 됩니다.

그리고 아래 **Sync folders**를 잡아줘야 합니다. sync folders는 로컬에서 실행한 경로와 서버에서 실행될 경로를 매칭하기 위한 설정입니다. 기본적으로 잡혀있는 `/tmp`를 사용할 경우 코드를 실행할 때 마다 `/tmp` 경로에 코드를 복제 후 실행하기 때문에 경로를 상대적으로 잡아서 쓰는 파일 등의 이슈가 있다면 오류가 발생합니다. 그렇기 때문에 <u>실제로 서버에서 코드를 동작시킬 위치</u>를 잡아줍니다.

![interpreter setting](/assets/images/posts/2022-9-27-pycharm-remote-interpreter-and-remote-deployment/img-1.png){: .align-center}
**interpreter 설정 화면**
{: .text-center}

설정 후 Create를 누르면 Location으로 설정한 경로에 venv과 python 환경이 구축된 것을 확인할 수 있습니다.

![directory tree](/assets/images/posts/2022-9-27-pycharm-remote-interpreter-and-remote-deployment/img-2.png){: .align-center}

<br>

##  **Remote Deployment**

**Remote Deployment**를 설정하면 로컬에서 수정한 코드를 서버에 실시간으로 반영하고, 작성한 코드를 서버에서 실행할 수 있습니다.

**PyCharm** 상단에 `Tools > Deployment > Browse Remote Host`를 누르면 SFTP로 연결된 원격 서버를 관리할 수 있는 화면이 나옵니다. 화면 상단에 `...`을 누르면 Deployment 창(`Tools > Deployment > Configuration...`)이 뜨는데, 상단에 `+`를 누르고 해당 프로젝트 이름으로 추가해줍니다.

여기서 Connection 설정을 해주면 되는데, **Type**은 **SFTP**, **SSH configuration**은 **위에서 설정한 서버 접속 정보**를 잡습니다. **Root path**는 **<u>위에서 sync folders를 설정할 때 잡은 서버 경로</u>**를 사용합니다.

![Deployment connection setting](/assets/images/posts/2022-9-27-pycharm-remote-interpreter-and-remote-deployment/img-3.png){: .align-center}
**Deployment > Connection 설정 화면**
{: .text-center}

그리고 Mappings에 **Deployment path**는 앞에 Connection 설정에서 Root path를 설정했기 때문에 `/`만 입력합니다.

![deployment mappings setting](/assets/images/posts/2022-9-27-pycharm-remote-interpreter-and-remote-deployment/img-4.png){: .align-center}
**Deployment > Mappings 설정 화면**
{: .text-center}

마지막으로 Excluded Paths에 서버에 있는 **venv를 예외처리**합니다.

![deployment excluded paths setting](/assets/images/posts/2022-9-27-pycharm-remote-interpreter-and-remote-deployment/img-5.png){: .align-center}
**Deployment > Excluded Paths 설정 화면**
{: .text-center}

<br>

## **원격 설정 결과 확인**

이제 로컬에서 테스트용 파일을 생성해봅니다. 로컬에서 `test.py`을 생성하고 `hello world`를 출력하는 코드를 실행해보면 서버에서 코드가 실행되는 것을 확인할 수 있습니다.

![result](/assets/images/posts/2022-9-27-pycharm-remote-interpreter-and-remote-deployment/img-6.png){: .align-center}

하단 실행 화면을 보면 생성한 **가상 환경(venv)**의 python을 이용해서 작성한 코드가 복제된 서버 경로에서 실행되는 것을 확인할 수 있습니다.

<br>

## **원격 설정이 잘 안되는 경우**

1. **로컬 수정 사항이 서버에 반영이 자동으로 안되는 경우**

- PyCharm 원격 설정은 기본적으로 <u>하나의 Remote Host만 실시간 반영이 가능</u>(이외의 경우 rsync 설정 필요)합니다. Deployment 설정으로가서 사용중인 Remote Host를 우클릭하고 `Use as Default`를 클릭한 다음, `Tools > Deployment > Automatic Upload (Always)`를 체크 해제하고 다시 체크해서 실시간 반영이 되는지 다시 확인합니다.
