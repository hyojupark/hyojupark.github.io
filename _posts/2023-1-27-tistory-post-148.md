---
title: jekyll minimal-mistakes 테마 적용 오류 (ruby 3.2)
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

Ruby 최신버전인 3.2로 jekyll을 설치하고 minimal-mistakes 테마를 적용하려 했으나 `undefined method `tainted?`` 오류가 발생했습니다. 


해당 오류로 검색해보니 전부 ruby 3.1 버전으로 낮춰서 사용하라해서 **ruby 3.1.3**으로 바꿔 설치했더니 바로 해결됐습니다. 아직은 너무 최신 버전을 사용하면 안되나봅니다.


 


 


**Reference**


* <https://github.com/jekyll/jekyll/issues/9233>
* <https://thoughtfulapps.com/articles/jekyll/ruby/undefined-method-tainted-jekyll-error>


 


 

