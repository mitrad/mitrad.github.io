---
title: 두 표본집단의 평균차이에 대한 검정방법들
author: 양우성
layout: post
permalink: /2011/05/%eb%91%90-%ed%91%9c%eb%b3%b8%ec%a7%91%eb%8b%a8%ec%9d%98-%ed%8f%89%ea%b7%a0-%ec%b0%a8%ec%9d%b4%ec%97%90-%eb%8c%80%ed%95%9c-%ea%b2%80%ec%a0%95-%eb%b0%a9%eb%b2%95%eb%93%a4/
tmac_last_id:
  - 274327333052223488
views:
  - 128
dsq_thread_id:
  - 305889019
categories:
  - 통계 이야기
tags:
  - t-test
  - Wilcoxon test
  - 두 집단의 평균차 검정
  - 정리
  - 평균비교
comments: true
---
두 표본집단(2 sample)의 평균 차이에 대한 검정방법으로 가장 많이 쓰이는 것이 아마 t-검정이라 생각됩니다. 하지만 데이터의 형태나 조건에 따라 적용할 수 있는 검정법과 적용할 수 없는 검정법들이 있음을 주의해야 합니다. 말로 길게 설명하는 것보다는 그림이 이해하기가 더 쉬우리라 생각해 플로차트 형식으로 만들어 보았습니다.  

![](/images/2011-05-11-fig1.png)
여기서 가장 중요한 것은 역시 데이터의 정규성입니다. 데이터가 정규분포를 따른다고 가정할 수 있을 때 모수 검정법(parametric test)인 t-검정을, 정규분포의 가정을 할 수 없을 때 비모수 검정법(nonparametric test)인 Wilcoxon의 검정을 사용합니다.

참고로 Wilcoxon의 순위합검정과 Mann-Whitney 검정은 검정을 위한 수식에 약간의 차이가 있을 뿐 검정의 결과는 같습니다.