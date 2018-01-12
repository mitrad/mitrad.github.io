---
title: '[R] dplyr 패키지의 _each 함수들'
layout: post
categories:
  - R
tags:
  - dplyr
  - R
comments: true
---



## 시작하며
요즘 R에서 이루어지는 대부분의 데이터 전처리에 `dplyr` 패키지를 이용하고 있다. 

보통 간단한 집계나 기초통계량은 함수 `summarise()`를 이용하여 새로운 데이터 프레임을 만들거나, 함수 `mutate()` 함수를 이용하여 기존 데이터 프레임에 새로운 열을 추가하곤 한다. 이때 하나의 변수에 대한 처리는 앞 두 함수를 쓰면 문제가 없는데 두 개 이상의 변수에 대한 처리에 유용하게 쓸 수 있는 함수가 있으니 바로 `summarise_each()`와 `mutate_each()` 이다. 

`dplyr` 패키지가 세상에 처음 모습을 드러냈을 때에는 포함 되어 있지 않았던 함수라 [패키지 소개글](http://wsyang.com/2014/02/introduction-to-dplyr/)에는 누락되어 있으니 이번 기회에 정리해 보자.

## summarise_each()의 사용법

예를들어 다음과 같이 하나의 변수에 대해 복수의 통계값을 구한다 하자.


{% highlight r %}
> library(dplyr)
> iris %>% group_by(Species) %>% 
+   summarise(min.sl=min(Sepal.Length),
+   mean.sl=mean(Sepal.Length),
+   median.sl=median(Sepal.Length),
+   max.sl=max(Sepal.Length))
{% endhighlight %}



{% highlight text %}
#> Source: local data frame [3 x 5]
#> 
#>      Species min.sl mean.sl median.sl max.sl
#> 1     setosa    4.3    5.01       5.0    5.8
#> 2 versicolor    4.9    5.94       5.9    7.0
#> 3  virginica    4.9    6.59       6.5    7.9
{% endhighlight %}

하나의 변수라면 이정도야 하고 넘어가겠지만 이것이 여러 변수에 동일한 처리를 하고자 하면 갑자기 암울해 지고 일하기 싫어지기 마련이다. 


{% highlight r %}
> iris %>% group_by(Species) %>% 
+   summarise(min.sl=min(Sepal.Length),
+             mean.sl=mean(Sepal.Length),
+             median.sl=median(Sepal.Length),
+             max.sl=max(Sepal.Length),
+             min.sw=min(Sepal.Width),
+             mean.sw=mean(Sepal.Width),
+             median.sw=median(Sepal.Width),
+             max.sw=max(Sepal.Width)) 
{% endhighlight %}



{% highlight text %}
#> Source: local data frame [3 x 9]
#> 
#>      Species min.sl mean.sl median.sl max.sl min.sw mean.sw median.sw
#> 1     setosa    4.3    5.01       5.0    5.8    2.3    3.43       3.4
#> 2 versicolor    4.9    5.94       5.9    7.0    2.0    2.77       2.8
#> 3  virginica    4.9    6.59       6.5    7.9    2.2    2.97       3.0
#> Variables not shown: max.sw (dbl)
{% endhighlight %}

요즘 우리나라 언론에서 절찬리에 사용되고 있는 Copy & Paste 기술을 시전하여 변수명만 바꾸면 되겠지만 이것도 중간에 빼먹고 바꾸지 않은 변수가 섞여 있기도 쉽고 일단 하다보면 귀찮다. 이럴때 함수 `summarise_each()`를 이용하면 간단하게 처리할 수 있다. 


{% highlight r %}
> iris %>% group_by(Species) %>% 
+   summarise_each(funs(min, mean, median, max), Sepal.Length, Sepal.Width)
{% endhighlight %}



{% highlight text %}
#> Source: local data frame [3 x 9]
#> 
#>      Species Sepal.Length_min Sepal.Width_min Sepal.Length_mean
#> 1     setosa              4.3             2.3              5.01
#> 2 versicolor              4.9             2.0              5.94
#> 3  virginica              4.9             2.2              6.59
#> Variables not shown: Sepal.Width_mean (dbl), Sepal.Length_median
#>   (dbl), Sepal.Width_median (dbl), Sepal.Length_max (dbl),
#>   Sepal.Width_max (dbl)
{% endhighlight %}

함수 `summarise_each()`의 첫번째 인자에 구하고 싶은 함수를 `funs()`안에 넣고 다음에 대상이 되는 변수를 나열하면 된다. 

### mutate_each() 사용법

iris 데이터에서 각 종별로 순위 및 percentile을 구해 원 데이터에 열을 추가하고 싶다면


{% highlight r %}
> iris %>% group_by(Species) %>% 
+   mutate_each(funs(min_rank, cume_dist),  Sepal.Length, Sepal.Width)
{% endhighlight %}



{% highlight text %}
#> Source: local data frame [150 x 9]
#> Groups: Species
#> 
#>    Sepal.Length Sepal.Width Petal.Length Petal.Width Species
#> 1           5.1         3.5          1.4         0.2  setosa
#> 2           4.9         3.0          1.4         0.2  setosa
#> 3           4.7         3.2          1.3         0.2  setosa
#> 4           4.6         3.1          1.5         0.2  setosa
#> 5           5.0         3.6          1.4         0.2  setosa
#> 6           5.4         3.9          1.7         0.4  setosa
#> 7           4.6         3.4          1.4         0.3  setosa
#> 8           5.0         3.4          1.5         0.2  setosa
#> 9           4.4         2.9          1.4         0.2  setosa
#> 10          4.9         3.1          1.5         0.1  setosa
#> ..          ...         ...          ...         ...     ...
#> Variables not shown: Sepal.Length_min_rank (int),
#>   Sepal.Width_min_rank (int), Sepal.Length_cume_dist (dbl),
#>   Sepal.Width_cume_dist (dbl)
{% endhighlight %}


### 추가 Tips
 
 위 예에서 선택한 변수는 모두 *Sepal*이라는 문자열으로 시작하며 iris 데이터의 다른 변수는 *Sepal*로 시작하는 변수는 없다. 


{% highlight r %}
names(iris)
{% endhighlight %}



{% highlight text %}
#> [1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width" 
#> [5] "Species"
{% endhighlight %}

따라서 함수 `starts_with()`를 이용하여 다음과 같이 처리할 수도 있다. 


{% highlight r %}
iris %>% group_by(Species) %>% 
  summarise_each(funs(min, mean, median, max), starts_with("Sepal"))
{% endhighlight %}



{% highlight text %}
#> Source: local data frame [3 x 9]
#> 
#>      Species Sepal.Length_min Sepal.Width_min Sepal.Length_mean
#> 1     setosa              4.3             2.3              5.01
#> 2 versicolor              4.9             2.0              5.94
#> 3  virginica              4.9             2.2              6.59
#> Variables not shown: Sepal.Width_mean (dbl), Sepal.Length_median
#>   (dbl), Sepal.Width_median (dbl), Sepal.Length_max (dbl),
#>   Sepal.Width_max (dbl)
{% endhighlight %}

함수 `starts_with()`와 함께 알아두면 좋을 함수는 다음과 같은 것들이 있다. 단, 이하 함수군은 함수 `select()`, `summarise_each()`, `mutate_each()` 안에서만 사용할 수 있으니 주의해야 한다. 

  |함수 |설명|
  |-|-|
  |starts_with(x) | x로 시작하는 변수만 선택. ignore.case = TRUE를 추가하면 대소문자를 구별하지 않음|
  |ends_with(x) | x로 끝나는 변수만 선택|
  |contains(x) | x를 포함하는 변수를 선택|
  |matches(x) | 정규표현 x에 대응하는 변수 선택|
  |num_range("x", 1:5, width = 2) | 문자열과 숫자의 조합으로 변수 선택. width는 앞에 0을 붙인 자리수. 이 예에서는 x01부터 x05를 선택|
  |one_of("x", "y", "z")| "x", "y", "z" 중 어느 하나라도 해당하는 변수를 선택|
  |everything | 모든 변수를 선택|

정규표현식에 자신이 있는 사람은 변수명 선택에 대단한 효율성을 가지게 될 것같다. (여기서도 결론은 정규표현식인가...) 
아무튼 나같이 영타자가 느리고 귀찮은 것 싫어하는 사람에게 `_each()` 함수의 도입은 감사할 따름이다. 땡큐 [Hadley Wickham](http://priceonomics.com/hadley-wickham-the-man-who-revolutionized-r/?utm_campaign=Data%2BElixir&utm_medium=email&utm_source=Data_Elixir_46)!!
