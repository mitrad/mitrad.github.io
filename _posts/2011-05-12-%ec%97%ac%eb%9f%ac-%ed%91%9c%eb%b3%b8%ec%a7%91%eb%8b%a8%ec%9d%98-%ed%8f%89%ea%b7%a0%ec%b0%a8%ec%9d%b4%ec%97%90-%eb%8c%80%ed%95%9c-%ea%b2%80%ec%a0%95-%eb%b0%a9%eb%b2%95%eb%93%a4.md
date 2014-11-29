---
title: 여러 표본집단의 평균차이에 대한 검정방법들
author: 양우성
layout: post
permalink: /2011/05/%ec%97%ac%eb%9f%ac-%ed%91%9c%eb%b3%b8%ec%a7%91%eb%8b%a8%ec%9d%98-%ed%8f%89%ea%b7%a0%ec%b0%a8%ec%9d%b4%ec%97%90-%eb%8c%80%ed%95%9c-%ea%b2%80%ec%a0%95-%eb%b0%a9%eb%b2%95%eb%93%a4/
views:
  - 88
dsq_thread_id:
  - 305896580
tmac_last_id:
  - 274327333052223488
categories:
  - 통계 이야기
tags:
  - Friedman 검정
  - Kruskal-Wallis 검정
  - 분산분석
  - 평균비교
---
둘 이상 여러 표본집단의 평균차이에 대한 검정에서 많이 사용되는 방법은 분산분석(analysis of variance; ANOVA)입니다. 하지만 두 표본집단의 검정과 마찬가지로 데이터의 정규성을 확인하여 모수적 방법인 분산분석을 이용할지 비모수 검정을 이용할지 판단해야 합니다. 또한, 관심 있는 요인(factor)의 수에 따라서도 사용 가능한 방법이 다름에 주의하여야 합니다.  
<!--more-->

  
[<img class="aligncenter size-full wp-image-2120" title="many_sample" src="http://i0.wp.com/wsyang.com/wp-content/uploads/2011/05/many_sample.png?resize=487%2C387" alt="" data-recalc-dims="1" />][1]

위 그림에서는 단순히 분산분석이라고 적어 놓았지만, 분석 이전에 데이터를 얻는 실험계획방법에 따라 그 결과의 해석이 달라짐에도 주의해야만 합니다.

또한, 여러 평균을 한 번의 분석에 비교하게 되므로 다중비교(multiple comparison)의 문제도 고려해야 하고, 2개의 요인에 대해 복수의 관측값이 존재할 때는 교호작용(interaction)의 유의성에 대해서도 고려해야 합니다.

되도록 간단하게 정리하려 했는데, 너무 많은 것을 생략하지 않았나 싶습니다만 대략의 흐름을 파악하는 데는 도움이 되리라 생각합니다.

 [1]: http://i0.wp.com/wsyang.com/wp-content/uploads/2011/05/many_sample.png