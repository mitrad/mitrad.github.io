---
layout: post
title: "[R] dplyr, plyr 함께 쓰다 피 볼 수 있다."
date: 2014-03-11
categories: R
tags: [R, dplyr, plyr]
comments: true
---



**dplyr** 패지지를 사용할 때 그 결과가 이상하다면 **plyr** 패키지를 함께 불러 오지 않았는가 확인하자. 

예를 들면 다음과 같은 경우...


{% highlight r %}
> library(dplyr)
{% endhighlight %}



{% highlight text %}
#> 
#> Attaching package: 'dplyr'
{% endhighlight %}



{% highlight text %}
#> The following objects are masked from 'package:stats':
#> 
#>     filter, lag
{% endhighlight %}



{% highlight text %}
#> The following object is masked from '.startup':
#> 
#>     n
{% endhighlight %}



{% highlight text %}
#> The following objects are masked from 'package:base':
#> 
#>     intersect, setdiff, setequal, union
{% endhighlight %}



{% highlight r %}
> iris %.% group_by(Species) %.% summarise(count=length(Species))
{% endhighlight %}



{% highlight text %}
#> Error in iris %.% group_by(Species) %.% summarise(count = length(Species)): could not find function "%.%"
{% endhighlight %}

{% highlight r %}
> library(plyr)
{% endhighlight %}



{% highlight text %}
#> --------------------------------------------------------------------
{% endhighlight %}



{% highlight text %}
#> You have loaded plyr after dplyr - this is likely to cause problems.
#> If you need functions from both plyr and dplyr, please load plyr first, then dplyr:
#> library(plyr); library(dplyr)
{% endhighlight %}



{% highlight text %}
#> --------------------------------------------------------------------
{% endhighlight %}



{% highlight text %}
#> 
#> Attaching package: 'plyr'
{% endhighlight %}



{% highlight text %}
#> The following objects are masked from 'package:dplyr':
#> 
#>     arrange, count, desc, failwith, id, mutate, rename,
#>     summarise, summarize
{% endhighlight %}



{% highlight r %}
> iris %.% group_by(Species) %.% summarise(count=length(Species))
{% endhighlight %}



{% highlight text %}
#> Error in iris %.% group_by(Species) %.% summarise(count = length(Species)): could not find function "%.%"
{% endhighlight %}

**plyr** 패키지를 `detach` 한 후 다시 확인하면 


{% highlight r %}
> detach(package:plyr)
> iris %.% group_by(Species) %.% summarise(count=length(Species))
{% endhighlight %}



{% highlight text %}
#> Error in iris %.% group_by(Species) %.% summarise(count = length(Species)): could not find function "%.%"
{% endhighlight %}

편리하다고 이것저것 불러다 쓰다 피 볼 수 있다...
