---
title: 새로운 SNP case-control 연관분석 방법 OMTT
layout: post
permalink: /2008/08/%ec%83%88%eb%a1%9c%ec%9a%b4-snp-case-control-%ec%97%b0%ea%b4%80%eb%b6%84%ec%84%9d-%eb%b0%a9%eb%b2%95-omtt/

dsq_thread_id:
  - 306641421
categories:
  - 유전통계학
  - 통계 이야기
tags:
  - Association Study
  - 연관분석
  - 통계 이야기
---
*   [Optimal dose-effect mode trend test for SNP genotype tables.](http://www3.interscience.wiley.com/journal/121372688/abstract?CRETRY=1&SRETRY=0), An Genetic Epidemiology Published Online: 7 Aug 2008

*   요점 정리 
    *   SNP를 이용한 Case-Control 연관분석을 하고자 할 때, 가법 형식(additive mode)에 대해 trend 검정을, 우성 형식(dominant mode)에 대해 우성 검정을, 열성 형식(recessive mode)에 대해 열성 검정을 할 때가 있다. 또한, 어떤 유전 형식을 따르는지 알 수 없지만, 그 중 하나와 일치한다면 연관이 있다고 생각하여, 세 가지 검정 중 가장 유의한 검정결과를 채택하는 방법을 사용할 때도 있다.
    *   Risk allele의 동형접합체(homozygote)와 non risk allele의 동형접합체의 리스크 가중치를 각각 1, 0으로 설정할 때, 우성, 열성, 가법 형식은 이형접합체(heterozygote)의 리스크 가중치를 1, 0, 0.5로 설정한 경우이므로, 우성과 열성의 사이는 이형접합체의 가중치를 0 이상 1 이하로 생각할 수 있다. Optimal dose trend test(OMTT)는 이러한 우성, 열성 간의 형식을 연속적인 대상으로 간주하여 검정하는 방법이다.
    *   실제 계산은 2X3 분할표로부터 자유도 2의 카이제곱 통계치와 우성 형식, 열성 형식으로 부터의 2X2 분할표를 만들어 자유도 1인 카이제곱 통계치를 계산하여 그 중 하나를 검정통계량으로 사용한다.
    *   자세한 해설과 Java로 만든 애플리케이션의 다운로드는 [이곳](http://func-gen.hgc.jp/wiki/index.php/Optimal_dose-effect_mode_trend_test)에서