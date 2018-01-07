---
title: R의 출력결과를 LaTeX 테이블로 변환하기
author: 양우성
layout: post
permalink: /2012/03/r%ec%9d%98-%ec%b6%9c%eb%a0%a5%ea%b2%b0%ea%b3%bc%eb%a5%bc-latex-%ed%85%8c%ec%9d%b4%eb%b8%94%eb%a1%9c-%eb%b3%80%ed%99%98%ed%95%98%ea%b8%b0/
dsq_thread_id:
  - 614110932
tmac_last_id:
  - 274327333052223488
categories:
  - R-Tips
tags:
  - LaTeX
  - R-Tips
comments: true
output: html_document
---
통계분석 패키지인 R과 과학문서 작성에 많이 쓰이는 LaTeX는 궁합이 아주 잘 맞습니다. 특히 R의 Sweave라는 패키지를 이용하면 R 환경에서 훌륭한 LaTeX 문서를 만들 수 있습니다. 단지 Sweave를 이용하면 R의 소스코드가 좀 복잡해지기는 한데요. 그래서 저는 R에서 계산한 결과만을 LaTeX 테이블로 변환하는 방법을 즐겨 사용합니다. 이를 가능하게 해주는 것이 R의 xtable이라는 패키지입니다.  

  
우선 xtable 패키지를 인스톨합니다.


{% highlight r %}
> install.packages("xtable")
{% endhighlight %}
인스톨한 패키지를 불러오고, 필요한 계산 혹은 분석을 하고 그 결과를 LaTeX 테이블로 변환하기 위해서는 함수 `xtable()`를 사용합니다.


{% highlight r %}
> library(xtable)
> data(tli)
> fm1 <- aov(tlimth ~ sex + ethnicty + grade + disadvg, data=tli)
> fm1.table <- xtable(fm1)
> print(fm1.table)
{% endhighlight %}



{% highlight text %}
## % latex table generated in R 3.4.3 by xtable 1.8-2 package
## % Sat Jan  6 21:30:41 2018
## \begin{table}[ht]
## \centering
## \begin{tabular}{lrrrrr}
##   \hline
##  & Df & Sum Sq & Mean Sq & F value & Pr($>$F) \\ 
##   \hline
## sex & 1 & 75.37 & 75.37 & 0.38 & 0.5417 \\ 
##   ethnicty & 3 & 2572.15 & 857.38 & 4.27 & 0.0072 \\ 
##   grade & 1 & 36.31 & 36.31 & 0.18 & 0.6717 \\ 
##   disadvg & 1 & 59.30 & 59.30 & 0.30 & 0.5882 \\ 
##   Residuals & 93 & 18682.87 & 200.89 &  &  \\ 
##    \hline
## \end{tabular}
## \end{table}
{% endhighlight %}

화면에 출력된 결과를 LaTeX 문서에 복사 & 붙여 넣기를 하고 컴파일을 하면, 아래와 같은 테이블을 얻을 수 있습니다.

![](/images/2012-03-17-fig1.png)

테이블의 결과를 화면이 아닌 별도의 파일로 저장하기 위해서는


{% highlight r %}
> print(fm1.table, file="fm1.txt")
{% endhighlight %}
을 실행하면 `fm1.txt` 파일에 위 내용이 저장되어 있는 것을 확인할 수 있습니다.

더 많은 예제는 [The xtable gallery(PDF 파일)](http://cran.r-project.org/web/packages/xtable/vignettes/xtableGallery.pdf)을 확인하세요.

