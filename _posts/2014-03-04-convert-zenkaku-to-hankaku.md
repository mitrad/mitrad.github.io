---
layout: post
title: "[R] 일본어 전각 문자를 반각 문자로 변환"
date: 2014-03-04
categories: R
tags: [R, Nippon package]
image: keyboard.png
---




일본어가 섞여 있는 데이터를 분석하다 보면 숫자데이터에도 전각 반각 문자가 뒤죽박죽되어 있는 경우가 많다. 

전각 숫자문자는 데이터 분석에 이용할 수 없어서 모두 반각 문자로 바꾸어 주어야 하는데 이미 ***Nippon***이라는 R 패키지가 존재하고 있었다. 

<!--more-->

{% highlight r %}
> library(Nippon)
> zen2han("１２３４５ＡＢＣ")
{% endhighlight %}



{% highlight text %}
#> [1] "12345ABC"
{% endhighlight %}


전각 반각이 섞여 있어도 전부 반각문자로 바꾸어 준다. 
편리!!


{% highlight r %}
> # 3과 B가 반각, 나머지 전각문자
> zen2han("１２3４５ＡBＣ")
{% endhighlight %}



{% highlight text %}
#> [1] "12345ABC"
{% endhighlight %}


전각 문자와 반각 문자에 대한 위키백과 설명은 [여기](http://ko.wikipedia.org/wiki/%EC%A0%84%EA%B0%81_%EB%AC%B8%EC%9E%90%EC%99%80_%EB%B0%98%EA%B0%81_%EB%AC%B8%EC%9E%90)
