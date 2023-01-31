---
title: minimal-mistakes jekyll 테마 적용 (근데 이제 ruby 3.2에서 생긴 오류를 곁들인)
toc: true
categories:
  - 기타
tags:
  - Jekyll
  - minimal-mistakes
  - ruby 3.2
  - tainted
  - undefined method
---

---

**이전 포스트**
- [GitHub 블로그 생성기 (Mac 기준)]({% post_url 2023-1-26-tistory-post-147 %})

---

<br>

## Minimal-Mistakes 테마 설치
테마는 `https://github.com/mmistakes/minimal-mistakes/releases`여기서 최신 버전(4.24.0) source code를 받아서 사용했습니다. 받고나서 압축을 풀고 아래 항목은 삭제합니다.

**삭제 파일 리스트**
- .editorconfig
- .gitattributes
- .github
- /docs
- /test
- CHANGELOG.md
- minimal-mistakes-jekyll.gemspec
- README.md
- screenshot-layouts.png
- screenshot.png

이제 전에 생성한 repository [이전 포스트 참조]({% post_url 2023-1-26-tistory-post-147 %})에 있는 파일을 모두 지우고(.git 제외), 위 파일로 덮어줍니다.

### 오류 해결 (ruby 버전 변경)
이제 아래 명령어를 설치하고 실행하면 되는데, 오류가 발생합니다.
```bash
$ gem install bundle
$ bundle install
$ bundle exec jekyll serve
```

```bash
"Liquid Exception: undefined method untaint' for "Welcome to Jekyll!":String in 
/_layouts/post.html jekyll 3.9.2 | Error:  undefined method untaint' for "Welcome 
to Jekyll!":String ...
```

Ruby 최신버전인 3.2로 Minimal-Mistakes 테마를 적용하려다보니 `undefined method `tainted?`` 오류가 발생했습니다. 
해당 오류로 검색해보니 전부 ruby 3.1 버전으로 낮춰서 사용하라해서 **ruby 3.1.3**으로 바꿔 설치했더니 바로 해결됐습니다. 아직은 너무 최신 버전을 사용하면 안되나봅니다.
```bash
$ rbenv install 3.1.3
$ rbenv global 3.1.3.
$ gem install bundle jekyll
$ bundle install
$ bundle exec jekyll serve
```

`127.0.0.1:4000`으로 접속했을 때 웹서버가 뜨면 성공입니다.

## **Reference**
* <https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/>
* <https://github.com/jekyll/jekyll/issues/9233>
* <https://thoughtfulapps.com/articles/jekyll/ruby/undefined-method-tainted-jekyll-error>


 


 

