---
title: 데이터 드리프트 감지 (Drift Detection)
toc: true
categories:
  - MLOps
tags:
  - Data Drift
  - Drift Detection
  - MLOps
  - 데이터 드리프트
  - 드리프트 탐지
---

자동차 주행 데이터로 예를 들어, 평소 도심에 인구가 밀집되어있다가 특정 이벤트(집값 폭등, 원격근무 확대 등)로 인해 지방, 시골에 이동했다고 가정해봅시다. 초기엔 주로 건물, 도로 등을 위주로 데이터가 수집됐었겠지만 나중엔 점점 자연 풍경의 데이터가 수집되기 시작할 것입니다. 이처럼 시간이 지남에 따라 데이터 성향이 변경된 것을 데이터 드리프트라고 합니다.


 


![data drift](/assets/images/posts/2022-7-31-tistory-post-89/img-1.png)출처: https://evidentlyai.com/blog/ml-monitoring-drift-detection-vs-outlier-detection




 


 


데이터 드리프트를 감지하는 방법은 3가지가 있습니다.


 


1. **일변량 통계 테스트 (Univariate statistical tests)**
	* 모델 학습에 사용된 원천 데이터의 특성(Feature) 분포와 대상 데이터의 특성 분포에 대해 통계적 검정을 수행
	* 연속형 특성의 경우 Kolmogorov-Smirnov test, 범주형 특성의 경우 Chi-squared test을 이용해 검정
2. **도메인 분류기 (Domain classifier)**
	* 원본 데이터 세트와 개발 데이터 세트를 구분하는 모델을 학습해 원본 데이터를 구분하는 분류기 생성
3. **결과 해석 (Interpretation of results)**
	* 1, 2와는 반대로 결과를 해석해서 데이터 드리프트를 판단
	* ex) 특정 연령층의 수요 감소, 특정 원자재값 급증으로 인한 수요 변동 등


 


 


드리프트가 감지되면 자동으로 무언가를 수행하기는 어렵습니다. 이때는 새로운 데이터를 기반으로 모델 재학습이 필요하며, 충분한 데이터 특성 분석을 통해 이루어져야 합니다.


 


 


 


 




---


본 게시글은 [Introducing MLOps](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791162245507&orderClick=LAG&Kc=)를 읽고 정리한 내용으로, 더 자세한 내용은 책에서 확인 바랍니다.


 




---


 

