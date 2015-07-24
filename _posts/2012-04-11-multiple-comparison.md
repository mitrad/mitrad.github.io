---
title: 'GWAS로 배우는 유전통계학 &#8211; 4 다중비교 문제'
layout: post
permalink: /2012/04/multiple-comparison/
categories:
  - 유전통계학
tags:
  - Benjamini-Hochberg
  - Bonferroni
  - false discovery rate
  - Genome-wide association analysis
  - GWAS
  - 다중비교문제
comments: true
---
## 4. 다중비교(multiple comparison) 문제

GWAS에서는 보통 50만~250만 SNP를 이용해 관련분석을 하게 되므로 반드시 다중비교의 문제가 발생합니다. 하나의 SNP를 이용한 검정의 유의수준을 $$\alpha $$라고 한다면 한 번의 검정에서 $$\alpha \times 100 $$%의 확률로 잘못된 결론을 내리게 됩니다. 만약 50만 SNP좌위를 이용해 검정을 했을 때 단 한 번이라도 잘못된 결론을 내리게 될 확률, 즉 거짓 양성(false positive)은  

$$
1-(1-\alpha)^{500K} \approx 1  
$$

이 되어 100% 오류를 포함하게 되는 거죠. 이러한 문제를 개선하기 위해 매우 다양한 방법이 고안, 발표되고 있습니다. 이번 포스팅에서는 가장 간단한 방법인 Bonferroni의 보정방법과 FDR(false discovery rate)를 이용한 방법에 대해 알아보도록 하겠습니다.  

### Bonferroni 방법

Bonferroni의 부등식에 기초한 이 방법은 분석 전체의 유의수준을 모든 검정의 수로 나누어 이것을 1회의 검정에서의 유의수준으로 삼는 방법을 말합니다. 즉, 분석에 사용되는 SNP좌위 수를 $$ k $$라고 하면 1회의 검정에서 사용하는 유의수준 $$ \alpha' $$은  

$$  
\alpha' = \frac{\alpha}{k}  
$$  

이 되고 산출된 $$\alpha'$$보다 작은 유의확률(p-value)이 관측된 SNP좌위에 대해 유의성을 인정하는 방법입니다. 예를 들어 100만 SNP좌위의 DNA chip을 이용하여 GWAS를 수행할 때 1회의 검정에 이용되는 유의수준은 $$5 \times 10^{-8} $$이 되게 됩니다.

이 방법은 계산이 간단하다는 장점이 있지만 모든 SNP가 독립이라는 다시 말해 연쇄 평형(linkage equilibrium)이라는 가정하에서 이루어지므로 1회의 검정에서 사용하는 유의수준이 너무나 작아져 검정력이 떨어지게 된다는 단점이 있습니다. 즉, 실제로 형질과 SNP 사이에 관련성이 있어도 관련이 없다고 판단하는 오류를 범할 확률이 커지게 됩니다.

### FDR을 이용한 방법

Bonferroni 방법의 문제점을 개선하기 위해 여러 가지 대안이 있는데 그중에서 형질과 관련성이 있다고 판단된 SNP 중에서 잘못된 판단의 비율을 일정 비율 이하로 억제하는 FDR을 이용한 접근법이 있습니다.

n개의 SNP좌위를 이용해 실험-대조군 연구를 한다고 할 때 관련분석의 결과 유의성이 있는 SNP의 수를 R이라 하겠습니다. 모든 SNP가 형질과 관련이 없다(귀무가설)고 하면 R의 분포는 귀무가설 하에서  

$$  
R \sim B(n,\alpha)  
$$  

의 이항분포를 따르게 됩니다. 여기서 R개의 SNP 중에 진짜로 관련성이 있는 SNP 수를 S, 관련성이 없음에도 불구하고 관련이 있다고 판단된 SNP의 수를 V라고 해보죠. 또한, n개의 SNP중에서 진짜로 관련성이 있는 SNP의 비율을 $$ \pi $$라고 한다면 관련이 없음에도 관련이 있다고 잘못 판단될 비율은 $$ (1-\pi)\alpha $$가 되고, V의 기댓값은 $$ n(1-\pi)\alpha $$가 됩니다. 이를 표로 그려보면 다음과 같이 됩니다.

![](http://farm4.staticflickr.com/3771/9198688743_ee7b47004c_o.jpg)

FDR이라는 것은 위 표의 V/R의 기댓값을 말하게 됩니다. 따라서  

$$
Q = \frac{n \alpha (1-\pi) }{R}  
$$  

이라 하면 이 값(Q-value)을 일정 값 이하로 제어하는 것이 이 방법의 목적입니다. 그러나 $$ \pi $$의 값은 미지의 값(신의 영역이란 건 이럴 때 쓰는 거죠!!)이기 때문에 $$Q \leq n \alpha/R $$의 관계를 이용하여 Q-값을 컨트롤 하게 됩니다.

개인적으로는 FDR을 컨트롤하는 방법 중에 Benjamini-Hochberg법(BH법)을 즐겨 사용합니다. 이 방법은 각 검정으로부터의 유의확률을 크기순으로 늘어놓고 j번째로 작은 유의확률 $$ p_{(j)} $$보다 작은 p 값을 가진 SNP는 관련성이 있다고 판단 할 때, 분석 전체의 유의수준을 $$ \alpha $$라고 하면  

$$  
\frac{n(1-\pi)p_{(j)}}{j} \leq \frac{n p_{(j)}}{j}  
$$  

이므로  

$$  
\frac{n p_{(j)}}{j} \leq \alpha  
$$

$$  
p_{(j)} \leq \alpha \times \frac{j}{n}  
$$  

의 관계를 이용해 p 값이 큰 것으로부터 평가하여 최초로 부등식이 성립되게 될 때, 이보다 작은 p 값을 가지는 SNP는 모두 유의성(관련성)이 있다고 판단하는 방법입니다.

이번 포스팅에서는 다중비교 문제에 대해 비교적 계산이 간단한 두 가지 보정방법에 대해 알아보았습니다만 가장 정확하다고 할 보정방법은 permutation test를 이용한 방법이라 할 수 있겠습니다. 그러나 현실적으로는 사용하기 불가능할 정도로 계산량이 많아서 이를 해결하기 위한 방법 또한 활발히 연구가 진행되고 있습니다.  
요즘 제가 즐겨 쓰는 방법은 SLIDE(a Sliding-window approach for Locally Inter-correlated markers with asymptotic Distribution Errors corrected)라는 방법이 있는데 자세한 사항은 [이곳](http://slide.cs.ucla.edu/)을 참조하시길 바랍니다. 저자가 한국분이신 것 같네요.

## 참고문헌

1.  Benjamini Y, and Hochberg Y. (1995) J. Roy. Stat. Soc. B., 57, 289-300
2.  鎌谷直之 (2007) 遺伝統計学入門, 岩波書店 (카마타니 나오유키 (2007), 유전통계학 입문, 이와나미서점 )