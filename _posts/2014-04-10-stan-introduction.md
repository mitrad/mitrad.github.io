---
layout: post
title: "베이즈 추정을 위한 Stan 맛보기"
date: 2014-04-10
categories: R
tags: [R, Stan]
comments: true
---



## 시작하며

Bayesian inference using Gibbs sampling:BUGS는 베이즈 추정을 계산기 통계학적으로 수행하는 방법. "계산기 통계학적으로"라는 것은 복잡하고 어려운 함수기술에서 생략할 수 있는 부분은 생략해서 MCMC/Gibbs sampling으로 대체한다는 의미로 생각해도 좋음. 
베이즈 추정을 하기 위해 우도 함수 등을 미리 구해 빡시게 코딩하는 것보다는 BUGS(WinBUGS, OpenBUGS, JAGS)등을 이용해 모델의 기술하고 실행한 후 결과를 확인하는 것이 편리. 그렇다고 해도 계산 시간이 오래 걸린다는 문제점은 남아 있음. 여기서는 Stan이라는 최근 많이 사용되는(것 같은?) 소프트웨어를 R에서 사용하는 방법에 대해 메모. 

## Stan의 개요

Stan의 홈페이지는 [여기](http://mc-stan.org/]). 

Stan의 개요에 대해서는 [이곳](http://www.dynacom.co.jp/tech/tech_column/tech013.html)과 [이곳](http://tjo.hatenablog.com/entry/2014/03/25/194605)(일본어)에서 번역 발췌.

모델의 기술방법에 약간의 차이가 있지만, BUGS와 같이 우선 모델을 기술. Stan은 기술된 모델을 일단 C++ 언어로 변환하여 컴파일하는 흐름. C++로 컴파일함으로 인해 고속처리를 기대할 수 있고 일단 컴파일된 모듈을 다른 데이터에도 이용할 수 있게 됨. BUGS에서는 별도의 데이터를 이용하기 위해서는 모델의 확인 -> 데이터 지정 -> 초기화 -> 컴파일의 과정을 전부 다시 해야함. stan을 이용하면 이 과정이 생략되기 때문에 전체 실행 과정의 고속화를 꾀할 수 있음. 

또한, C++ 이라는 언어의 특성상 모델의 기술이 엄격하게 되어 데이터의 형태 (int, real) 및 변수가 가질 수 있는 범위를 선언할 수 있게 됨. BUGS에서는 왜 샘플링 에러가 발생했는지 등에 대해 어림짐작으로 대응해야 하기 때문에 경험이 중요했음. 이러한 것들이 구체적으로 지적되기 때문에 디버깅이 편리해짐. 

Stan에서 사용하는 난수 샘플링방법은 [Hamiltonian Monte Carlo Method](http://en.wikipedia.org/wiki/Hybrid_Monte_Carlo)를 채용하여 수렴의 속도 및 안정성도 BUGS에 비해 향상됨.


## Rstan

환경설정은 [이곳](https://github.com/stan-dev/rstan/wiki/RStan-Getting-Started)을 참조.

기본적인 흐름은 
* Rtools을 인스톨하여 CRAN에 등록되지 않은 패키지를 설치할 수 있게함
* __Rcpp__ 패키지 및 __inline__ 패키지을 이용해 Rcpp를 이용한 R의 C++화를 가능하게 하여 stan을 실행. 


{% highlight r %}
> install.packages('inline')
> install.packages('Rcpp')
{% endhighlight %}


{% highlight r %}
> # using library inline to compile a C++ code on the fly
> library(inline) 
> library(Rcpp)
> src <- ' 
+   std::vector<std::string> s; 
+   s.push_back("hello");
+   s.push_back("world");
+   return Rcpp::wrap(s);
+ '
> hellofun <- cxxfunction(body = src, includes = '', plugin = 'Rcpp', verbose = FALSE)
> cat(hellofun(), '\n') 
{% endhighlight %}



{% highlight text %}
#> hello world
{% endhighlight %}

## RStan의 설치
### Xcode 5.0.1 + R 3.0.2 + Mac OS X 10.9 (Mavericks) 사용자를 위한 설정

* 실행환경
  * Mac OS X 10.9 (Mavericks),
  * Xcode toolset 5.0.1 & command-line tools for Mavericks (Xcode와는 별도로 다운로드해야 함)
  * R 3.0.3 

다음 파일을 만들고 

```
~/.R/Makevars
```
다음 내용을 입력

```
CXX=g++ -arch x86_64 -ftemplate-depth-256 -stdlib=libstdc++
```

R 에서 다음 코드를 실행

{% highlight r %}
> if (!file.exists("~/.R/Makevars")) {
+   cat('CXX=g++ -arch x86_64 -ftemplate-depth-256 -stdlib=libstdc++\n
+        CXXFLAGS="-mtune=native  -O3 -Wall -pedantic -Wconversion"\n', 
+        file="~/.R/Makevars");
+ } else {
+    file.show("~/.R/Makevars");
+ }
{% endhighlight %}

### rstan 인스톨

{% highlight r %}
> options(repos = c(getOption("repos"), rstan = "http://wiki.rstan-repo.googlecode.com/git/"))
> install.packages('rstan', type = 'source')
{% endhighlight %}

### rstan을 효율적으로 사용하기 위한 옵션 

{% highlight r %}
> library(rstan)
> set_cppo("fast")  # for best running speed
> set_cppo('debug') # make debug easier
{% endhighlight %}

## Example
[RStan Getting Started](https://github.com/stan-dev/rstan/wiki/RStan-Getting-Started#how-to-install-rstan)의 예제 1번을 따라해보고 제대로 실행 되는가 확인.


{% highlight r %}
> schools_code <- '
+   data {
+     int<lower=0> J; // number of schools 
+     real y[J]; // estimated treatment effects
+     real<lower=0> sigma[J]; // s.e. of effect estimates 
+   }
+   parameters {
+     real mu; 
+     real<lower=0> tau;
+     real eta[J];
+   }
+   transformed parameters {
+     real theta[J];
+     for (j in 1:J)
+       theta[j] <- mu + tau * eta[j];
+   }
+   model {
+     eta ~ normal(0, 1);
+     y ~ normal(theta, sigma);
+   }
+ '
> 
> schools_dat <- list(J = 8, 
+                     y = c(28,  8, -3,  7, -1,  1, 18, 12),
+                     sigma = c(15, 10, 16, 11,  9, 11, 10, 18))
> 
> fit <- stan(model_code = schools_code, data = schools_dat, 
+             iter = 1000, chains = 4)
{% endhighlight %}



{% highlight text %}
#> COMPILING THE C++ CODE FOR MODEL 'schools_code' NOW.
#> 
#> SAMPLING FOR MODEL 'schools_code' NOW (CHAIN 1).
#> 
#> Chain 1, Iteration:   1 / 1000 [  0%]  (Warmup)
#> Chain 1, Iteration: 100 / 1000 [ 10%]  (Warmup)
#> Chain 1, Iteration: 200 / 1000 [ 20%]  (Warmup)
#> Chain 1, Iteration: 300 / 1000 [ 30%]  (Warmup)
#> Chain 1, Iteration: 400 / 1000 [ 40%]  (Warmup)
#> Chain 1, Iteration: 500 / 1000 [ 50%]  (Warmup)
#> Chain 1, Iteration: 501 / 1000 [ 50%]  (Sampling)
#> Chain 1, Iteration: 600 / 1000 [ 60%]  (Sampling)
#> Chain 1, Iteration: 700 / 1000 [ 70%]  (Sampling)
#> Chain 1, Iteration: 800 / 1000 [ 80%]  (Sampling)
#> Chain 1, Iteration: 900 / 1000 [ 90%]  (Sampling)
#> Chain 1, Iteration: 1000 / 1000 [100%]  (Sampling)
#> #  Elapsed Time: 0.026008 seconds (Warm-up)
#> #                0.019721 seconds (Sampling)
#> #                0.045729 seconds (Total)
#> 
#> 
#> SAMPLING FOR MODEL 'schools_code' NOW (CHAIN 2).
#> 
#> Chain 2, Iteration:   1 / 1000 [  0%]  (Warmup)
#> Chain 2, Iteration: 100 / 1000 [ 10%]  (Warmup)
#> Chain 2, Iteration: 200 / 1000 [ 20%]  (Warmup)
#> Chain 2, Iteration: 300 / 1000 [ 30%]  (Warmup)
#> Chain 2, Iteration: 400 / 1000 [ 40%]  (Warmup)
#> Chain 2, Iteration: 500 / 1000 [ 50%]  (Warmup)
#> Chain 2, Iteration: 501 / 1000 [ 50%]  (Sampling)
#> Chain 2, Iteration: 600 / 1000 [ 60%]  (Sampling)
#> Chain 2, Iteration: 700 / 1000 [ 70%]  (Sampling)
#> Chain 2, Iteration: 800 / 1000 [ 80%]  (Sampling)
#> Chain 2, Iteration: 900 / 1000 [ 90%]  (Sampling)
#> Chain 2, Iteration: 1000 / 1000 [100%]  (Sampling)
#> #  Elapsed Time: 0.027753 seconds (Warm-up)
#> #                0.032589 seconds (Sampling)
#> #                0.060342 seconds (Total)
#> 
#> 
#> SAMPLING FOR MODEL 'schools_code' NOW (CHAIN 3).
#> 
#> Chain 3, Iteration:   1 / 1000 [  0%]  (Warmup)
#> Chain 3, Iteration: 100 / 1000 [ 10%]  (Warmup)
#> Chain 3, Iteration: 200 / 1000 [ 20%]  (Warmup)
#> Chain 3, Iteration: 300 / 1000 [ 30%]  (Warmup)
#> Chain 3, Iteration: 400 / 1000 [ 40%]  (Warmup)
#> Chain 3, Iteration: 500 / 1000 [ 50%]  (Warmup)
#> Chain 3, Iteration: 501 / 1000 [ 50%]  (Sampling)
#> Chain 3, Iteration: 600 / 1000 [ 60%]  (Sampling)
#> Chain 3, Iteration: 700 / 1000 [ 70%]  (Sampling)
#> Chain 3, Iteration: 800 / 1000 [ 80%]  (Sampling)
#> Chain 3, Iteration: 900 / 1000 [ 90%]  (Sampling)
#> Chain 3, Iteration: 1000 / 1000 [100%]  (Sampling)
#> #  Elapsed Time: 0.030395 seconds (Warm-up)
#> #                0.02032 seconds (Sampling)
#> #                0.050715 seconds (Total)
#> 
#> 
#> SAMPLING FOR MODEL 'schools_code' NOW (CHAIN 4).
#> 
#> Chain 4, Iteration:   1 / 1000 [  0%]  (Warmup)
#> Chain 4, Iteration: 100 / 1000 [ 10%]  (Warmup)
#> Chain 4, Iteration: 200 / 1000 [ 20%]  (Warmup)
#> Chain 4, Iteration: 300 / 1000 [ 30%]  (Warmup)
#> Chain 4, Iteration: 400 / 1000 [ 40%]  (Warmup)
#> Chain 4, Iteration: 500 / 1000 [ 50%]  (Warmup)
#> Chain 4, Iteration: 501 / 1000 [ 50%]  (Sampling)
#> Chain 4, Iteration: 600 / 1000 [ 60%]  (Sampling)
#> Chain 4, Iteration: 700 / 1000 [ 70%]  (Sampling)
#> Chain 4, Iteration: 800 / 1000 [ 80%]  (Sampling)
#> Chain 4, Iteration: 900 / 1000 [ 90%]  (Sampling)
#> Chain 4, Iteration: 1000 / 1000 [100%]  (Sampling)
#> #  Elapsed Time: 0.03016 seconds (Warm-up)
#> #                0.028719 seconds (Sampling)
#> #                0.058879 seconds (Total)
{% endhighlight %}



{% highlight r %}
> fit2 <- stan(fit = fit, data = schools_dat, iter = 10000, chains = 4)
{% endhighlight %}



{% highlight text %}
#> 
#> SAMPLING FOR MODEL 'schools_code' NOW (CHAIN 1).
#> 
#> Chain 1, Iteration:    1 / 10000 [  0%]  (Warmup)
#> Chain 1, Iteration: 1000 / 10000 [ 10%]  (Warmup)
#> Chain 1, Iteration: 2000 / 10000 [ 20%]  (Warmup)
#> Chain 1, Iteration: 3000 / 10000 [ 30%]  (Warmup)
#> Chain 1, Iteration: 4000 / 10000 [ 40%]  (Warmup)
#> Chain 1, Iteration: 5000 / 10000 [ 50%]  (Warmup)
#> Chain 1, Iteration: 5001 / 10000 [ 50%]  (Sampling)
#> Chain 1, Iteration: 6000 / 10000 [ 60%]  (Sampling)
#> Chain 1, Iteration: 7000 / 10000 [ 70%]  (Sampling)
#> Chain 1, Iteration: 8000 / 10000 [ 80%]  (Sampling)
#> Chain 1, Iteration: 9000 / 10000 [ 90%]  (Sampling)
#> Chain 1, Iteration: 10000 / 10000 [100%]  (Sampling)
#> #  Elapsed Time: 0.235765 seconds (Warm-up)
#> #                0.316338 seconds (Sampling)
#> #                0.552103 seconds (Total)
#> 
#> 
#> SAMPLING FOR MODEL 'schools_code' NOW (CHAIN 2).
#> 
#> Chain 2, Iteration:    1 / 10000 [  0%]  (Warmup)
#> Chain 2, Iteration: 1000 / 10000 [ 10%]  (Warmup)
#> Chain 2, Iteration: 2000 / 10000 [ 20%]  (Warmup)
#> Chain 2, Iteration: 3000 / 10000 [ 30%]  (Warmup)
#> Chain 2, Iteration: 4000 / 10000 [ 40%]  (Warmup)
#> Chain 2, Iteration: 5000 / 10000 [ 50%]  (Warmup)
#> Chain 2, Iteration: 5001 / 10000 [ 50%]  (Sampling)
#> Chain 2, Iteration: 6000 / 10000 [ 60%]  (Sampling)
#> Chain 2, Iteration: 7000 / 10000 [ 70%]  (Sampling)
#> Chain 2, Iteration: 8000 / 10000 [ 80%]  (Sampling)
#> Chain 2, Iteration: 9000 / 10000 [ 90%]  (Sampling)
#> Chain 2, Iteration: 10000 / 10000 [100%]  (Sampling)
#> #  Elapsed Time: 0.223928 seconds (Warm-up)
#> #                0.251676 seconds (Sampling)
#> #                0.475604 seconds (Total)
#> 
#> 
#> SAMPLING FOR MODEL 'schools_code' NOW (CHAIN 3).
#> 
#> Chain 3, Iteration:    1 / 10000 [  0%]  (Warmup)
#> Chain 3, Iteration: 1000 / 10000 [ 10%]  (Warmup)
#> Chain 3, Iteration: 2000 / 10000 [ 20%]  (Warmup)
#> Chain 3, Iteration: 3000 / 10000 [ 30%]  (Warmup)
#> Chain 3, Iteration: 4000 / 10000 [ 40%]  (Warmup)
#> Chain 3, Iteration: 5000 / 10000 [ 50%]  (Warmup)
#> Chain 3, Iteration: 5001 / 10000 [ 50%]  (Sampling)
#> Chain 3, Iteration: 6000 / 10000 [ 60%]  (Sampling)
#> Chain 3, Iteration: 7000 / 10000 [ 70%]  (Sampling)
#> Chain 3, Iteration: 8000 / 10000 [ 80%]  (Sampling)
#> Chain 3, Iteration: 9000 / 10000 [ 90%]  (Sampling)
#> Chain 3, Iteration: 10000 / 10000 [100%]  (Sampling)
#> #  Elapsed Time: 0.254845 seconds (Warm-up)
#> #                0.316465 seconds (Sampling)
#> #                0.57131 seconds (Total)
#> 
#> 
#> SAMPLING FOR MODEL 'schools_code' NOW (CHAIN 4).
#> 
#> Chain 4, Iteration:    1 / 10000 [  0%]  (Warmup)
#> Chain 4, Iteration: 1000 / 10000 [ 10%]  (Warmup)
#> Chain 4, Iteration: 2000 / 10000 [ 20%]  (Warmup)
#> Chain 4, Iteration: 3000 / 10000 [ 30%]  (Warmup)
#> Chain 4, Iteration: 4000 / 10000 [ 40%]  (Warmup)
#> Chain 4, Iteration: 5000 / 10000 [ 50%]  (Warmup)
#> Chain 4, Iteration: 5001 / 10000 [ 50%]  (Sampling)
#> Chain 4, Iteration: 6000 / 10000 [ 60%]  (Sampling)
#> Chain 4, Iteration: 7000 / 10000 [ 70%]  (Sampling)
#> Chain 4, Iteration: 8000 / 10000 [ 80%]  (Sampling)
#> Chain 4, Iteration: 9000 / 10000 [ 90%]  (Sampling)
#> Chain 4, Iteration: 10000 / 10000 [100%]  (Sampling)
#> #  Elapsed Time: 0.218341 seconds (Warm-up)
#> #                0.191652 seconds (Sampling)
#> #                0.409993 seconds (Total)
{% endhighlight %}



{% highlight r %}
> print(fit2)
{% endhighlight %}



{% highlight text %}
#> Inference for Stan model: schools_code.
#> 4 chains, each with iter=10000; warmup=5000; thin=1; 
#> post-warmup draws per chain=5000, total post-warmup draws=20000.
#> 
#>           mean se_mean   sd   2.5%   25%   50%   75% 97.5% n_eff Rhat
#> mu        7.98    0.09 5.14  -1.69  4.67  7.86 11.11 18.16  3165    1
#> tau       6.54    0.11 5.74   0.22  2.44  5.21  9.05 20.48  2518    1
#> eta[1]    0.39    0.01 0.95  -1.53 -0.23  0.41  1.03  2.20 13796    1
#> eta[2]    0.00    0.01 0.87  -1.72 -0.56 -0.01  0.57  1.74 14301    1
#> eta[3]   -0.19    0.01 0.94  -2.01 -0.82 -0.21  0.43  1.67 14106    1
#> eta[4]   -0.04    0.01 0.89  -1.78 -0.63 -0.04  0.54  1.73 14952    1
#> eta[5]   -0.36    0.01 0.88  -2.05 -0.93 -0.37  0.20  1.45 12837    1
#> eta[6]   -0.21    0.01 0.89  -1.92 -0.79 -0.22  0.37  1.59 14605    1
#> eta[7]    0.34    0.01 0.89  -1.46 -0.24  0.35  0.93  2.07 13595    1
#> eta[8]    0.05    0.01 0.94  -1.77 -0.58  0.05  0.68  1.90 15318    1
#> theta[1] 11.40    0.10 8.31  -2.12  5.97 10.28 15.54 31.80  7615    1
#> theta[2]  7.96    0.05 6.21  -4.34  4.07  7.92 11.84 20.75 17908    1
#> theta[3]  6.15    0.07 7.70 -11.20  2.01  6.60 10.91 20.43 11897    1
#> theta[4]  7.58    0.05 6.44  -5.46  3.59  7.58 11.57 20.49 14127    1
#> theta[5]  5.14    0.05 6.31  -8.86  1.38  5.62  9.35 16.34 15579    1
#> theta[6]  6.20    0.05 6.69  -8.38  2.33  6.51 10.49 18.69 16132    1
#> theta[7] 10.59    0.06 6.76  -1.06  6.02  9.97 14.52 25.73 13274    1
#> theta[8]  8.37    0.07 7.69  -6.86  3.87  8.20 12.62 24.83 10782    1
#> lp__     -4.89    0.04 2.67 -10.86 -6.50 -4.61 -3.02 -0.33  4894    1
#> 
#> Samples were drawn using NUTS(diag_e) at Tue Jul 21 23:11:53 2015.
#> For each parameter, n_eff is a crude measure of effective sample size,
#> and Rhat is the potential scale reduction factor on split chains (at 
#> convergence, Rhat=1).
{% endhighlight %}



{% highlight r %}
> plot(fit2)
{% endhighlight %}

![plot of chunk unnamed-chunk-7](/figs/source/2014-04-10-stan-introduction/unnamed-chunk-7-1.png) 

{% highlight r %}
> la <- extract(fit2, permuted = TRUE) # return a list of arrays 
> mu <- la$mu 
> 
> ### return an array of three dimensions: iterations, chains, parameters 
> a <- extract(fit2, permuted = FALSE) 
> 
> ### use S3 functions as.array (or as.matrix) on stanfit objects
> a2 <- as.array(fit2)
> m <- as.matrix(fit2)
{% endhighlight %}
