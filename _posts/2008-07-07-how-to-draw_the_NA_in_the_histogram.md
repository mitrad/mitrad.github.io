---
title: '[R] 결손치를 히스토그램에 나타내는 방법'
layout: post
permalink: /2008/07/r-%ea%b2%b0%ec%86%90%ec%b9%98%eb%a5%bc-%ed%9e%88%ec%8a%a4%ed%86%a0%ea%b7%b8%eb%9e%a8%ec%97%90-%eb%82%98%ed%83%80%eb%82%b4%eb%8a%94-%eb%b0%a9%eb%b2%95/
dsq_thread_id:
  - 308560056
categories:
  - R-Tips
tags:
  - R-Tips
---
기본 R의 histgram에서는 결손치(NA)를 그래프에 표시하지 않는다. 결손치의 수를 그래프에 나타내기 위해서는 약간의 추가 과정이 필요하다.

{% highlight r %}
> sample.data <- as.factor(sample(c(1, 0, NA), 100, replace = TRUE))
> sample.data <- as.character(sample.data)
> sample.data[is.na(sample.data)] <- " NA"
> sample.data <- factor(sample.data)
> plot(sample.data)
{% endhighlight %}


![center](http://i2.wp.com/wsyang.com/wp-content/uploads/2008/07/rplot3.jpg?resize=480%2C480)

`ggplot2` 패키지를 이용하면 좀더 멋진 그래프를 얻을 수 있다.

{% highlight r %}
> qplot(sample.data, geom = "histogram")
{% endhighlight %}

![center](http://i2.wp.com/wsyang.com/wp-content/uploads/2008/07/rplot21.jpg?resize=480%2C480)
