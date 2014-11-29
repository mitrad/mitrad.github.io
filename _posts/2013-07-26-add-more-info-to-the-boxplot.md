---
title: Box plot에 좀더 많은 정보를 담아보자
layout: post
permalink: /2013/07/add-more-info-to-the-boxplot/
dsq_thread_id:
  - 1532131178
categories:
  - R-Tips
tags:
  - Box Plot
  - R-Tips
  - 데이터 시각화
  - 상자 수염 그림
---
데이터 분석할 때 무엇을 가장 먼저 하세요?

저는 우선 데이터의 분포 및 도수를 확인합니다. 데이터의 형태와 종류에 따라 사용할 수 있는 분석 방법이 정해지기 때문이죠. 이상치의 확인 때문이기도 합니다.  
개인적으로 데이터의 분포를 확인할 때 Box plot을 즐겨 사용하는데요. 

Box plot 정확히 상자와 수염 그림(box and whisker plot)은 두 개 이상의 집단의 상대적 비교를 위해서 각 집단의 최대값(max)과 최소값(min) 그리고 중앙값(자료를 크기순으로 나열했을 때 가운데 위치하는 값: median) 및 사분위수(자료를 크기 순서에 따라 늘어놓은 자료를 4등분 했을 때 위치하는 값을 의미함) 제 1사분위수(아래에서 25% 백분위점에 위치하는 수: Q1), 제 3사분위수(아래에서 75% 백분위점에 위치하는 수: Q3)등 다섯 숫자를 요약하여 그래프로 나타내는 방법으로 [John W. Tukey][1]가 제안한 탐색적 데이터 분석 방법입니다. 

Box plot에서 상자 안의 직선은 중위수를 의미하고 상자의 양쪽 끝은 두 개의 사분위수를 그리고 상자의 양쪽에 이상치를 제외한 최대 및 최소값을 잇는 수염을 직선으로 그려줌으로써 데이터의 전체적 분포 양상을 쉽게 파악할 수 있습니다. 

만약 2개의 Group(A, B)과 3가지의 처리(trt1, trt2, trt3) 변수에 대해 두 번 이상 값을 측정했다면 보통 R에서 boxplot() 함수를 이용해 아래와 같은 결과를 얻을 수 있습니다. 

{% highlight r %}
> mydf <- read.csv("../data/sample.csv", head = T)
> boxplot(Value ~ Trt + Group, data = mydf)
{% endhighlight %}

<a href="http://i1.wp.com/farm4.staticflickr.com/3696/9361996043_bca6669b39_o.png" title="Original Box Plot" rel="lightbox"><img src="http://i1.wp.com/farm4.staticflickr.com/3696/9361996043_bca6669b39_o.png?resize=504%2C504" alt="Original Box Plot" title="Original Box Plot" class="aligncenter" data-recalc-dims="1" /></a>

그런데 어느 고갱님께서 좀더 많은 정보를 그래프에 넣을 수 있겠냐고 문의(라 쓰고 명령이라 읽습니다) 하시기에:

*   산술 평균 
*   각 사분위수로 부터 1.5IRQ ~ 3IRQ 사이의 이상치는 +, 3IRQ 보다 크거나 작은 이상치는 *: 단, IQR=Q3-Q1
*   [각 범주에 해당하는 관측치 수][2]
*   각 범주의 라벨

을 넣어보았습니다. 

{% highlight r %}
> # 각 Group, trt에 대한 최소값 Q1, 평균, 중위수, Q3, 최대값 계산
> summary.tab <- aggregate(mydf["Value"], mydf[c("Group", "Trt")], summary)
> summary.tab <- cbind(summary.tab[, 1:2], as.data.frame(summary.tab$Value))
> 
> IQR.tab <- aggregate(mydf["Value"], mydf[c("Group", "Trt")], IQR)
> 
> summary.tab <- merge(summary.tab, IQR.tab, by = c("Group", "Trt"))
> names(summary.tab) <- c("Group", "Trt", "Min", "Q1", "Median", "Mean", "Q3", 
+     "Max", "IQR")
> 
> summary.tab$IQR15 <- summary.tab$IQR * 1.5
> summary.tab$IQR30 <- summary.tab$IQR * 3
> 
> # Lower Inner Fence (Q1 - 1.5 * IQR)
> summary.tab$Lower.IF <- with(summary.tab, Q1 - IQR15)
> # Lower Outer Fence (Q1 - 3 * IQR)
> summary.tab$Lower.OF <- with(summary.tab, Q1 - IQR30)
> # Upper Inner Fence (Q3 + 1.5 * IQR)
> summary.tab$Upper.IF <- with(summary.tab, Q3 + IQR15)
> # Upper Outter Fence (Q3 + 3 * IQR)
> summary.tab$Upper.OF <- with(summary.tab, Q3 + IQR30)
> 
> # 각 범주별 관측치수 구하기
> n.obs <- aggregate(mydf["Value"], mydf[c("Trt", "Group")], length)
> xlabel <- paste("N=", n.obs$Value, sep = "")
> 
> # Boxplot 그리기
> at <- c(1:3, 5:7)
> labels <- c("trt1", "trt2", "trt3")
> boxplot(Value ~ Trt, data = mydf, subset = Group == "A", axes = F, outline = F, 
+     xlim = c(0, 8), at = c(1:3), ylim = c(0, max(mydf$Value)))
> boxplot(Value ~ Trt, data = mydf, subset = Group == "B", axes = F, outline = F, 
+     xlim = c(5, 7), add = T, at = c(5:7))
> # y축
> axis(2)
> # x축에 관측치 수, N=??, 표시
> axis(1, at = at, labels = xlabel, las = 1, cex.axis = 0.8)
> # Group 라벨
> mtext(c("Group A", "Group B"), side = 1, line = 3, at = c(2, 6))
> # trt 라벨
> mtext(labels, side = 3, at = at, cex = 0.9)
> # Box plot에 평균치 표시
> points(at, summary.tab$Mean, cex = 1.2, pch = 19)
> box()
> 
> # Inner Fence와 Outter Fence의 이상치 그리기
> nn <- 1
> for (i in c("A", "B")) {
+     for (j in c("trt1", "trt2", "trt3")) {
+         sub.df <- subset(mydf, Group == i & Trt == j)
+         Outer.Fence <- sub.df$Value > summary.tab$Upper.OF[nn] | sub.df$Value < 
+             summary.tab$Lower.OF[nn]
+         Inner.Fence <- (sub.df$Value <= summary.tab$Upper.OF[nn] & sub.df$Value > 
+             summary.tab$Upper.IF[nn]) | (sub.df$Value >= summary.tab$Lower.OF[nn] & 
+             sub.df$Value < summary.tab$Lower.IF[nn])
+         indicator <- ifelse(Inner.Fence, TRUE, FALSE)
+         if (sum(indicator) > 0) {
+             points(rep(at[nn], sum(indicator)), sub.df$Value[which(indicator)], 
+                 pch = 3)
+         }
+         indicator <- ifelse(Outer.Fence, TRUE, FALSE)
+         if (sum(indicator) > 0) {
+             points(rep(at[nn], sum(indicator)), sub.df$Value[which(indicator)], 
+                 pch = 8)
+         }
+         
+         nn <- nn + 1
+     }
+ }
{% endhighlight %}

<a href="http://i1.wp.com/farm8.staticflickr.com/7455/9364772170_48922e7c0a_o.png" title="More Informed Box Plot" rel="lightbox"><img src="http://i1.wp.com/farm8.staticflickr.com/7455/9364772170_48922e7c0a_o.png?resize=504%2C504" alt="More Informed Box Plot" title="More Informed Box Plot" class="aligncenter" data-recalc-dims="1" /></a>

결과 R 코드는 복잡해 졌지만, 확실히 그래프만으로 알 수 있는 정보의 양은 늘었네요. 조금 더 일반화시켜서 함수로 만들어 놓고 필요할 때마다 유용하게 쓰게 될 것 같습니다. 

예제에 사용한 데이터 및 R 코드 그리고 이 글의 원본이 되는 Markdown 문서는 [여기](href="https://github.com/mitrad/Boxplot)에서 받을 수 있습니다.

 [1]: http://en.wikipedia.org/wiki/John_Tukey
 [2]: http://wp.me/p1u7fk-8n