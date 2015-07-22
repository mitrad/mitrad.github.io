---
title: '[R] boxplot의 새로운 형태 violin plot'
author: 양우성
layout: post
permalink: /2011/04/r-boxplot%ec%9d%98-%ec%83%88%eb%a1%9c%ec%9a%b4-%ed%98%95%ed%83%9c-violin-plot/
dsq_thread_id:
  - 306119951

categories:
  - 통계 이야기
tags:
  - Boxplot
  - R-Tips
  - violin plot
---
데이터 분석을 할 때 가장 먼저 해야 하는 일은 데이터의 형태(분포)를 확인하는 것입니다. 많은 통계 교과서들이 각종 데이터 분석 기법을 설명하는 과정에서 데이터가 어떤 분포를 따르고 있다는 가정하에서 설명합니다. 따라서 데이터가 어떠한 분포를 따르고 있는지 파악해야만 사용할 수 있는 분석 기법을 결정할 수 있습니다.

개인적으로 데이터의 분포를 확인할 때 가장 많이 쓰는 방법이 boxplot입니다. 무엇보다도 간단하게 그릴 수 있고, 대략적인 이상치(outlier)의 존재를 확인할 수 있기 때문입니다.  

  
boxplot에 분포의 형태를 보다 구체적으로 표현하는 방법으로 violin plot이 있습니다. violin plot은 boxplot 위에 분포 밀도(kernel density)를 좌우 대칭으로 덮어쓰는 방식으로 데이터의 분포를 표현합니다.  
이 violin plot을 R에서 구현하기 위해서는 먼저 &#8220;vioplot&#8221;이라는 패키지를 설치해야 합니다.

{% highlight r %}
> install.packages("vioplot", depend=T)
{% endhighlight %}

여기서는 표준정규분포의 boxplot과 violin plot을, 그리고 자유도 1인 카이제곱분포의 두 plot을 비교해 보도록 하겠습니다.

{% highlight r %}
> library(vioplot)
> par(mfrow=c(1,2))
> x1 < - rnorm(2000,,1)
> boxplot(x1)
> vioplot(x1, col="red")
{% endhighlight %}

![center](http://i0.wp.com/wsyang.com/wp-content/uploads/2011/04/vioplot1.png)


{% highlight r %}
> par(mfrow=c(1,2))
> x2 < - rchisq(2000,df=1)
> boxplot(x2)
> vioplot(x2, col="red")
{% endhighlight %}


![center](http://i0.wp.com/wsyang.com/wp-content/uploads/2011/04/vioplot2.png)

저는 구관이 명관이라고 boxplot을 더 선호하긴 합니다만, 분포를 한 눈에 파악할 수 있다는 점에서 violin plot도 괜찮은 선택이라고 생각합니다.