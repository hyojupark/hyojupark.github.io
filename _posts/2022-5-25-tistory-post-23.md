---
title: Tetra - Full stack component framework
toc: true
categories:
  - Trend
tags:
  - Alpine.js
  - Component Framework
  - django
  - Full Stack
  - Liveview
  - livewire
  - Tetra
---

![tetra logo](/assets/images/posts/2022-5-25-tistory-post-23/img-1.png)

[www.tetraframework.com](https://www.tetraframework.com/)
Django와 Alpine.js 기반의 풀스택 프레임워크.

최근에 하나의 서버를 규모있게 개발하기보다 micro service를 개발하다보니 Backend 개발자 입장에서 Front를 최대한 쉽게 개발할 수 있는 프레임워크를 찾아서 쓰고있습니다. 그래서 Streamlit을 사용하고 있는데, Streamlit의 특징 때문에 링크를 건다거나, 테이블 안에 태그를 추가한다거나, modal을 띄운다는 등의 디테일한 설정이 어렵습니다. 이 부분을 Tetra를 활용하면 해결할 수 있을 것으로 보입니다.

Tetra는 Laravel Livewire와 Phoenix Liveview에서 영감을 얻어 개발한 프레임워크로, Backend에서 front까지 함께 개발하고, WebSocket/SSE로 실시간 Compoent 업데이트가 가능합니다. 때문에 큰 Architecture를 생각한다면 적합하지 않고 component 수준의 빠른 개발이 필요하다면 활용해볼 만할 것 같습니다. 코드 형태는 React를 공부해본적 있다면 많이 익숙할 것 같은 구조로 되어 있습니다.

![sample code](/assets/images/posts/2022-5-25-tistory-post-23/img-2.png){: .align-center}
Sample code
{: .text-center}
<br>

당연히 사용하기 위해서는 Front 코드의 이해가 필요하겠지만, 필요한 부분을 디테일하게 추가할 수 있다는 장점이 가장 좋은 것 같습니다.

아직 버전이 많이 낮지만, 올 여름에 정식 버전을 release할 계획이기 때문에 기대하면서 기다리고 있습니다.
