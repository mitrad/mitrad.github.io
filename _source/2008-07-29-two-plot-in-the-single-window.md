---
title: '[R] 하나의 윈도에 2개의 그래프를 그리는 방법'
layout: post
permalink: /2008/07/r-%ed%95%98%eb%82%98%ec%9d%98-%ec%9c%88%eb%8f%84%ec%97%90-2%ea%b0%9c%ec%9d%98-%ea%b7%b8%eb%9e%98%ed%94%84%eb%a5%bc-%ea%b7%b8%eb%a6%ac%eb%8a%94-%eb%b0%a9%eb%b2%95/
categories:
  - R
tags:
  - graph
  - R-Tips
comments: true
---

R에서 하나의 윈도에 2개 이상의 그래프를 겹쳐 그리려면 일반적으로 함수 `curve()`나 `points()`를 이용한다. 하지만, 패키지에 따라서는 이 함수들을 제대로 사용할 수 없는 경우가 있다. 경험한 바로는 [ROC (receive operating characteristic)](http://en.wikipedia.org/wiki/Receiver_operating_characteristic) curve를 간단하게 그릴 수 있게 해주는 [ROCR package](http://cran.r-project.org/web/packages/ROCR/index.html)에서는 `points()` 함수를 쓸 수가 없었다. 이 같은 경우 2개의 그래프를 겹쳐 그릴 수 있게 하기 위해서는 함수 `par(new=TRUE)`를 이용한다.


{% highlight r %}
> x1 <- rnorm(25, mean=, sd=1)
> y1 <- dnorm(x1, mean=, sd=1)
>  
> x2 <- rnorm(25, mean=, sd=1)
> y2 <- dnorm(x2, mean=, sd=1)
>  
> # points() 함수를 이용한 경우
> plot(x1, y1, type='p', xlim=range(x1,x2), ylim=range(y1,y2))
> points(x2, y2, type='p', col="red",xlim=range(x1,x2), ylim=range(y1,y2))
{% endhighlight %}

![plot of chunk unnamed-chunk-1](/figure/./_source/2008-07-29-two-plot-in-the-single-window/unnamed-chunk-1-1.png)

{% highlight r %}
> # par(new=TRUE)를 이용한 경우
> plot(x1, y1, type='p', xlim=range(x1,x2), ylim=range(y1,y2))
> par(new=TRUE)
> plot(x2, y2, type='p', col="red", axes=F, xlim=range(x1,x2), ylim=range(y1,y2))
{% endhighlight %}

![plot of chunk unnamed-chunk-1](/figure/./_source/2008-07-29-two-plot-in-the-single-window/unnamed-chunk-1-2.png)

2개 이상의 그래프를 겹쳐 그릴 때는 항상 x, y축의 범위에 주의해야 한다.

 
