---
title: '[R] R에서 가계도 작성하기'
layout: post
permalink: /2011/04/r%ec%97%90%ec%84%9c-%ea%b0%80%ea%b3%84%eb%8f%84-%ec%9e%91%ec%84%b1%ed%95%98%ea%b8%b0/
dsq_thread_id:
  - 306111892
categories:
  - R-Tips
  - 유전통계학
  - 통계 이야기
tags:
  - linkage analysis
  - pedigree chart
  - R-Tips
  - 가계도
  - 연쇄분석
  - 유전통계학
comments: true
---

유전통계학에서 연쇄분석(linkage analysis)을 하기 위해서는 각 가계 구성원의 가계도(pedigree chart)를 작성하는 것이 필수입니다. 가계 구성원의 수가 많지 않은 가계의 경우 손으로 그리거나, 도표를 그리는 소프트웨어(OmniGraffle, MS Visio등)를 이용하곤 합니다. 그러나 가계 구성원의 수가 많은 경우는 가계도를 그리는 것도 만만치 않은 일입니다.

전문적으로 가계도를 작성해 주는 소프트웨어도 있습니다만, 여기서는 R에서 작성하는 방법을 알아보도록 하겠습니다.  

  
먼저 R에서 가계도를 작성하기 위해서는 `kinship2`이라는 패키지가 필요합니다.


{% highlight r %}
> install.packages("kinship2")
{% endhighlight %}

kinship package를 설치한 후, 함수 pedigree를 이용하여 가계도를 작성합니다.  
입력데이터의 구성은 다음과 같습니다.

*   id : 개인 ID
*   momid : 어머니의 개인 ID
*   dadid : 아버지의 개인 ID
*   sex : 성별 (남자=1, 여자=2, 불명=3)
*   status: 사망여부 (생존=0, 사망=1)
*   affected: 질병여부 (질병 없음=1, 질병있음=2, 불명=0 혹은 NA)


{% highlight r %}
> library(kinship2)
> fam1 <- data.frame(
+       id =      c(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15),
+       mother =  c(0, 0, 0, 2, 2, 2, 0, 3, 3, 0, 6, 0, 10, 10, 11),
+       father =  c(0, 0, 0, 1, 1, 1, 0, 4, 4, 0, 7, 0, 9, 9, 12),
+       gender =  c(1, 2, 2, 1, 2, 2, 1, 2, 1, 2, 2, 1, 2, 2, 1),
+       status =  c(1, 1, 0, 0, 1, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0),
+       affected = c(1, 2, 2, 2, 2, 1, 2, 1, 2, 1, 2, 1, 2, 1, 1)
+ )
> ped <- pedigree(id=fam1$id, dadid=fam1$father, momid=fam1$mother,
+         sex=fam1$gender, affected=fam1$affected, status=fam1$status)
> plot(ped, main="Family1", symbolsize=1.2, cex=0.8)
{% endhighlight %}

![plot of chunk unnamed-chunk-2](/figs/source/2011-04-19-pedigree-tree-in-r/unnamed-chunk-2-1.png) 

가계도를 작성하는 전문 소프트웨어보다는 깨끗하지 못하지만, 연쇄분석을 할 때 이용하는 ped 형식의 파일에서 간단히 가계도를 그려보는 용도로는 편리하게 사용할 수 있습니다.

 
