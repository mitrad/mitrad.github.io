---
title: R에서 SAS의 영구파일 sas7bdat 이용하기
author: 양우성
layout: post
permalink: /2011/07/r%ec%97%90%ec%84%9c-sas%ec%9d%98-%ec%98%81%ea%b5%ac%ed%8c%8c%ec%9d%bc-sas7bdat-%ec%9d%b4%ec%9a%a9%ed%95%98%ea%b8%b0/
dsq_thread_id:
  - 369651193
short_url:
  - http://bit.ly/
tmac_last_id:
  - 274327333052223488
categories:
  - R-Tips
  - SAS
tags:
  - R package
  - R-Tips
  - SAS
  - sas7bdat
comments: true
---
최근 R package가 통계 분석에 많이 사용된다고는 하지만, 기업에서는 SAS나 SPSS를 더 많이 사용하는 것으로 알고 있습니다. 저도 대학이나 연구기관의 의뢰에는 R를 사용하지만, 기업의 데이터 분석에는 SAS를 이용합니다.

간혹 클라이언트로부터 받은 데이터가 SAS의 영구 파일형식인 sas7bdat일 때가 있습니다. 분석할 때 아무래도 손에 익은 R을 선호하게 되는데 SAS를 사용할 수 있는 환경에 있으면 데이터를 일반 ASCII 파일로 변환하여 사용하면 되지만 SAS를 사용할 수 없는 환경에 있을 때도 있습니다.

물론 R에서 SAS 형식의 데이터를 불러오는 함수 read.ssd()가 있긴 하지만, 이도 시스템에 SAS가 설치되어 있어야만 이용할 수 있어서 이래저래 불편했었습니다. 그런데 최근 sas7bdat라는 패키지가 공개되어 간단하게 이 형식의 데이터를 R에 불러올 수 있게 되었습니다.  
  
먼저 sas7bdat 패키지를 R에 인스톨합니다.


{% highlight r %}
> install.packages("sas7bdat")
{% endhighlight %}
예를 들어 SAS에서 제공하는 예제 데이터 `cars.sas7bdat`를 R로 불러 오기 위해서는


{% highlight r %}
> library(sas7bdat)
> cars <- read.sas7bdat("/files/cars.sas7bdat")
{% endhighlight %}



{% highlight text %}
## Warning in file(file, "rb"): cannot open file '/files/cars.sas7bdat':
## No such file or directory
{% endhighlight %}



{% highlight text %}
## Error in file(file, "rb"): cannot open the connection
{% endhighlight %}

와 같이 함수 read.sas7bdat()를 이용하면 R의 데이터프레임 형식으로 변환시킬 수 있습니다.

그 후엔 원하는 분석을 진행하면 되겠지요. 


{% highlight r %}
> summary(cars)
{% endhighlight %}



{% highlight text %}
##      speed           dist    
##  Min.   : 4.0   Min.   :  2  
##  1st Qu.:12.0   1st Qu.: 26  
##  Median :15.0   Median : 36  
##  Mean   :15.4   Mean   : 43  
##  3rd Qu.:19.0   3rd Qu.: 56  
##  Max.   :25.0   Max.   :120
{% endhighlight %}



{% highlight r %}
> with(cars, summary(Weight))
{% endhighlight %}



{% highlight text %}
## Error in summary(Weight): object 'Weight' not found
{% endhighlight %}

아직 대용량 데이터를 대상으로 써보지는 않았지만, SAS가 없어도 직접 sas7bdat 형식의 파일을 R에서 이용할 수 있다는 점에서 유용하게 사용할 수 있을듣 합니다.
