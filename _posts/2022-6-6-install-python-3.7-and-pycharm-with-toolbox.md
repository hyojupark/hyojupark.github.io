---
title: Python 3.7, PyCharm(w. Toolbox) 설치
toc: true
categories:
  - Python
tags:
  - IntelliJ
  - pycharm
  - Python
  - toolbox
---

Python과 개발툴 설치 과정

## **Python 설치**

먼저 <https://www.python.org/>으로 접속해서 Windows를 클릭합니다. 

![python web main](/assets/images/posts/2022-6-6-install-python-3.7-and-pycharm-with-toolbox/img-1.png){: .align-center}
<br>

그리고 3.7로 검색해서 Windows 링크가 있는 버전을 찾습니다. 3.7 버전은 3.7.10부터는 Windows 버전의 링크가 없어 3.7.9 버전을 설치하겠습니다. 아래 Windows x86-64 executable installer 링크를 클릭합니다.

![other version](/assets/images/posts/2022-6-6-install-python-3.7-and-pycharm-with-toolbox/img-2.png){: .align-center}
<br>

다운로드된 파일을 실행해서 Python을 설치합니다. 설치할 때는 편의를 위해 Add Python 3.7 to PATH를 체크하고 Install Now를 클릭합니다.

![install main](/assets/images/posts/2022-6-6-install-python-3.7-and-pycharm-with-toolbox/img-3.png){: .align-center}
<br>

이제 잘 설치됐는지 확인해보기 위해 cmd(명령 프롬프트)를 열어서 아래와 같이 출력되는지 확인합니다.

![](/assets/images/posts/2022-6-6-install-python-3.7-and-pycharm-with-toolbox/img-4.png){: .align-center}
<br>

## **PyCharm(w. Toolbox) 설치**

Python 개발을 위해 사용할 수 있는 여러 개발툴 중 PyCharm 설치해보겠습니다. 바로 PyCharm을 다운로드받아서 하는 방법도 있지만, Toolbox를 이용해서 설치했을 때 여러가지 이점이 있기 때문에 Toolbox를 이용해서 설치해보겠습니다. <https://www.jetbrains.com/ko-kr/>에 접속해서 Toolbox App을 클릭합니다.

![jetbrains web](/assets/images/posts/2022-6-6-install-python-3.7-and-pycharm-with-toolbox/img-5.png){: .align-center}
<br>

바로 아래 다운로드 버튼을 클릭합니다. 다운로드되면 실행해서 Toolbox를 바로 실행합니다.

![toolbox install page](/assets/images/posts/2022-6-6-install-python-3.7-and-pycharm-with-toolbox/img-6.png){: .align-center}
<br>

Toolbox를 실행하면 우측 하단에 설정 화면이 나오는데, 쭉 넘겨서 언어를 한국어로 설정합니다. 테마도 원하는 걸로 설정해줍니다. 그러면 아래와 같은 초기 화면을 볼 수 있습니다.

![toolbox ui](/assets/images/posts/2022-6-6-install-python-3.7-and-pycharm-with-toolbox/img-7.png){: .align-center}
<br>

여기서 PyCharm Community를 설치해줍니다. (참고로 PyCharm Professional의 경우 학생용 무료 라이센스가 제공되니, 학생이신 경우 Professional을 설치하는 것을 추천합니다. 특히 Flask, Django와 같이 웹 개발이 목적이신 경우 꼭 Professional을 사용하는 것을 추천합니다.)

이제 설치된 PyCharm Community를 클릭하면 PyCharm이 바로 실행됩니다. 설정을 쭉 넘기고 첫 설치에는 Do not import settings를 체크하고 넘어갑니다.

![path setting](/assets/images/posts/2022-6-6-install-python-3.7-and-pycharm-with-toolbox/img-8.png){: .align-center}
<br>

설치되면 아래와 같이 창이 뜨는데, 검은색 테마를 원할 경우 아래와 같이 Customize에서 Dracula를 선택합니다.

![theme setting](/assets/images/posts/2022-6-6-install-python-3.7-and-pycharm-with-toolbox/img-9.png){: .align-center}
<br>

이제 다시 Customize 위에 Projects를 클릭하고 New Project 버턴을 클릭합니다. 테스트용 프로젝트 이름으로 default로 생성된 pythonProject로 하겠습니다. 설치한 Python 3.7을 Base interpreter로 설정해야하는데, PATH가 잘 잡혀있기 때문에 3.7이 바로 잡혀있는 것을 볼 수 있습니다. 바로 Create 버튼을 누릅니다.

![new project](/assets/images/posts/2022-6-6-install-python-3.7-and-pycharm-with-toolbox/img-10.png){: .align-center}
<br>

위에서 Create a main.py welcome script를 체크했기 때문에 welcome script가 생성되어있는 것을 볼 수 있습니다. 바로 실행해봅니다. 실행 방법은 **1)** 아래 13번째 줄에 있는 세모 모양의 실행 버튼을 클릭하거나, **2)** 코드창을 우클릭 후 Run 'main'을 클릭하거나, **3)** Ctrl + Shift + F10을 누릅니다.

![code insert page](/assets/images/posts/2022-6-6-install-python-3.7-and-pycharm-with-toolbox/img-11.png){: .align-center}
![code execute page](/assets/images/posts/2022-6-6-install-python-3.7-and-pycharm-with-toolbox/img-12.png){: .align-center}
<br>

## **Toolbox 사용 팁**

이제 설치 및 코드 동작을 확인했으니 Toolbox를 조금 더 효율적으로 사용하기 위한 두 가지 방법을 확인하겠습니다.

**첫 번째**는 Toolbox를 이용한 프로젝트 검색합니다. 우측 하단에 Toolbox 아이콘을 클릭해서 실행해보면 IntelliJ로 생성된 프로젝트(여기선 PyCharm이지만 IDEA, Android Studio 등의 프로젝트가 모두 조회됩니다.)가 조회되는 것을 볼 수 있으며, 클릭하면 해당 프로젝트가 IDE로 바로 실행됩니다. 프로젝트가 여러 폴더에 흩어져있어도 한곳에서 검색이 가능합니다.

![toolbox tip](/assets/images/posts/2022-6-6-install-python-3.7-and-pycharm-with-toolbox/img-13.png){: .align-center}
<br>

**두 번째**는 Toolbox 단축키 설정입니다. 지금은 우측 하단에 아이콘을 클릭하고 사용했지만, 이 과정을 단축키로 등록이 가능합니다. 육각형 모양의 Toolbox App 메뉴에 들어가서 설정에 들어가 Toolbox App을(를) 여는 전역 단축키를 설정합니다. 저는 Alt + z를 사용중입니다. 이제 아무 화면에서나 단축키를 누르면 Toolbox를 열어볼 수 있습니다.

![toolbox tip 2](/assets/images/posts/2022-6-6-install-python-3.7-and-pycharm-with-toolbox/img-14.png){: .align-center}
![toolbox tip 3](/assets/images/posts/2022-6-6-install-python-3.7-and-pycharm-with-toolbox/img-15.png){: .align-center}
<br>

나중에 PyCharm에서 사용하면 좋은 plugin 등도 정리해서 올려놓도록 하겠습니다.

