---
title: 'GWAS로 배우는 유전통계학 &#8211; 3.1 코호트 연구와 실험-대조군 연구'
author: 양우성
layout: post
permalink: /2012/04/genome-wide-association-study-1/
tmac_last_id:
  - 274327330288193536
dsq_thread_id:
  - 657504301
categories:
  - 유전통계학
tags:
  - case-control study
  - cohort study
  - Genome-wide association analysis
  - 유전통계학
---
### 3. Genome-wide association study 

관련분석은 유전적 변이와 형질과의 관련성을 검출하는 것이 목적입니다. 이때 관측된 SNP좌위가 형질의 표현형(phenotype)에 직접적인 영향을 미친다는 것을 검출할 수 있다면 가장 바람직스러운 결과일 것 입니다(direct association). 그러나 실제로는 관련성을 시사하고 있다고 한다 해도 관측된 SNP좌위가 표현형과 직접 관련이 있다고는 보장할 수 없습니다. 진짜 원인이 되는 유전자 좌와 연쇄불평형(linkage disequilibrium; LD) 상태에 있는 유전자 좌도 표현형과 간접적인 관련이 있을 때가 많기 때문입니다(indirect association).  
  
<img src="http://i1.wp.com/wsyang.com/wp-content/uploads/2012/04/association.png" alt="Source: direct &#038; indirect association from Kruglyak (2008), Nat. Rev. Genet" class="caption">

관련분석의 대상이 되는 형질에는 앞서 언급한 바와 같이 질적 형질과 양적 형질이 있습니다. 따라서 형질의 형태에 따라 분석 방법이 달라집니다. 또한, 수집된 데이터의 연구디자인에 따라 관련성의 지표 및 결과의 해석방법이 달라짐에도 주의해야 합니다. 이번 포스팅에서는 코호트 연구(cohort study)와 실험-대조군 연구(case-control study)에 대해 알아보고 그 후에 질적 형질에 대한 관련분석법, 양적 형질에 대한 관련분석법에 대해 설명하도록 하겠습니다.

### 3.1 코호트 연구와 실험-대조군 연구 

코호트 연구는 연구의 대상이 되는 집단을 일정 기간에 걸쳐 추적조사를 하는 연구법을 말하여 어떤 인자를 가지고 있는 개체와 가지고 있지 않은 개체가 미래에 어떤 표현형이 되는가에 대해 연구하는 방법을 말합니다. 반면 실험-대조군 연구는 표현형에 따라 실험군과 대조군으로 분류하고 각 군에 대해 특정 인자를 포함하고 있는가를 분석하는 방법입니다. 즉, 모든 사건(event)이 이미 일어난 과거의 일을 분석하게 됩니다. 이 때문에 코호트 연구는 연구의 방향이 전향적(prospective)이고, 실험-대조군 연구는 후향적(retrospective)으로 진행됩니다.

관련성의 척도로써 코호트 연구는 상대위험도(relative risk; RR)를 실험-대조군 연구는 오즈비(odds ratio; OR)를 사용합니다. 관측된 데이터에 대해 관련분석을 할 때 일반적으로 다음과 같은 분할표를 이용합니다.

<img src="http://i0.wp.com/wsyang.com/wp-content/uploads/2012/04/table1.png" alt="<표 1> 관련분석에서의 분할표" class="caption">

개체의 표현형을 D(disease)와 N(non-disease)라 한다면 Type 1의 개체가 질환에 걸릴 확률과 Type 2의 개체가 질환에 걸릴 확률의 비로 정의되는 상대위험도는  

$$  
RR=\frac{\frac{a}{a+c}}{\frac{b}{b+d}} = \frac{a(b+d)}{b(a+c)}  
\$$  

로 계산할 수 있습니다.

<img class="caption" src="http://i0.wp.com/wsyang.com/wp-content/uploads/2012/04/cohort2.png" alt="코호트 연구">

한편 어떤 사건이 일어나지 않은 확률에 대한 사건이 일어난 확률의 비율로 오즈(odds)를 정의한다면 오즈비는 대조군의 오즈에 대한 실험군의 오즈 비율로 정의됩니다.  

$$  
OR = \frac{a/b}{c/d} = \frac{ad}{bc}  
$$

<img src="http://i0.wp.com/wsyang.com/wp-content/uploads/2012/04/case-control1.png" alt="Case-Control Study" class="caption">

상대위험도가 좀 더 알기 쉬운 개념이기는 하지만 실험-대조군 연구에서는 실험군 혹은 대조군의 표본 수를 연구자가 결정하게 되므로 상대위험도를 구할 수 없습니다.

코호트 연구는 원인이 되는 개체간 유전자 다형성의 차이가 처음부터 고정되고 결과가 되는 표현형을 관측하게 되므로 자연의 인과관계와 일치하게 됩니다. 또한, 추적관찰을 하게 되므로 사건의 발생순서를 알 수 있다는 점, 측정의 바이어스가 작다는 점, 복수의 결과인자를 동시에 관찰할 수 있다는 점, 표현형이 발현하는 비율로 정의되는 발병율(침투율)을 추정할 수 있다는 장점이 있습니다. 그러나 실험-대조군 실험에 비해 비용과 시간이 걸린다는 점, 발병율(침투율)이 낮은 표현형의 연구에는 표본크기가 크지 않으면 통계분석을 하기 어렵다는 점등의 문제가 있습니다.

### 참고문헌 

1.  Balding D.J. (2006), Nature reviews Genetics, 7, 10, 781-791.
2.  이재원, 박미라, 유한나 (2005) 생명과학연구를 위한 통계적 방법. 자유아카데미 
3.  鎌谷直之 (2007) 遺伝統計学入門, 岩波書店 (카마타니 나오유키 (2007), 유전통계학 입문, 이와나미서점 )

 [1]: http://i0.wp.com/wsyang.com/wp-content/uploads/2012/04/cohort2.png
 [2]: http://i0.wp.com/wsyang.com/wp-content/uploads/2012/04/case-control1.png