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
#> In file included from filee2ca49f69107.cpp:8:
#> In file included from /usr/local/lib/R/3.4/site-library/StanHeaders/include/src/stan/model/model_header.hpp:4:
#> In file included from /usr/local/lib/R/3.4/site-library/StanHeaders/include/stan/math.hpp:4:
#> In file included from /usr/local/lib/R/3.4/site-library/StanHeaders/include/stan/math/rev/mat.hpp:4:
#> In file included from /usr/local/lib/R/3.4/site-library/StanHeaders/include/stan/math/rev/core.hpp:12:
#> In file included from /usr/local/lib/R/3.4/site-library/StanHeaders/include/stan/math/rev/core/gevv_vvv_vari.hpp:5:
#> In file included from /usr/local/lib/R/3.4/site-library/StanHeaders/include/stan/math/rev/core/var.hpp:7:
#> In file included from /usr/local/lib/R/3.4/site-library/BH/include/boost/math/tools/config.hpp:13:
#> In file included from /usr/local/lib/R/3.4/site-library/BH/include/boost/config.hpp:39:
#> /usr/local/lib/R/3.4/site-library/BH/include/boost/config/compiler/clang.hpp:200:11: warning: 'BOOST_NO_CXX11_RVALUE_REFERENCES' macro redefined [-Wmacro-redefined]
#> #  define BOOST_NO_CXX11_RVALUE_REFERENCES
#>           ^
#> <command line>:6:9: note: previous definition is here
#> #define BOOST_NO_CXX11_RVALUE_REFERENCES 1
#>         ^
#> 1 warning generated.
#> 
#> SAMPLING FOR MODEL '31f989ec0d689be2a4996807325a264b' NOW (CHAIN 1).
#> 
#> Gradient evaluation took 3.4e-05 seconds
#> 1000 transitions using 10 leapfrog steps per transition would take 0.34 seconds.
#> Adjust your expectations accordingly!
#> 
#> 
#> Iteration:   1 / 1000 [  0%]  (Warmup)
#> Iteration: 100 / 1000 [ 10%]  (Warmup)
#> Iteration: 200 / 1000 [ 20%]  (Warmup)
#> Iteration: 300 / 1000 [ 30%]  (Warmup)
#> Iteration: 400 / 1000 [ 40%]  (Warmup)
#> Iteration: 500 / 1000 [ 50%]  (Warmup)
#> Iteration: 501 / 1000 [ 50%]  (Sampling)
#> Iteration: 600 / 1000 [ 60%]  (Sampling)
#> Iteration: 700 / 1000 [ 70%]  (Sampling)
#> Iteration: 800 / 1000 [ 80%]  (Sampling)
#> Iteration: 900 / 1000 [ 90%]  (Sampling)
#> Iteration: 1000 / 1000 [100%]  (Sampling)
#> 
#>  Elapsed Time: 0.043705 seconds (Warm-up)
#>                0.029365 seconds (Sampling)
#>                0.07307 seconds (Total)
#> 
#> 
#> SAMPLING FOR MODEL '31f989ec0d689be2a4996807325a264b' NOW (CHAIN 2).
#> 
#> Gradient evaluation took 1.5e-05 seconds
#> 1000 transitions using 10 leapfrog steps per transition would take 0.15 seconds.
#> Adjust your expectations accordingly!
#> 
#> 
#> Iteration:   1 / 1000 [  0%]  (Warmup)
#> Iteration: 100 / 1000 [ 10%]  (Warmup)
#> Iteration: 200 / 1000 [ 20%]  (Warmup)
#> Iteration: 300 / 1000 [ 30%]  (Warmup)
#> Iteration: 400 / 1000 [ 40%]  (Warmup)
#> Iteration: 500 / 1000 [ 50%]  (Warmup)
#> Iteration: 501 / 1000 [ 50%]  (Sampling)
#> Iteration: 600 / 1000 [ 60%]  (Sampling)
#> Iteration: 700 / 1000 [ 70%]  (Sampling)
#> Iteration: 800 / 1000 [ 80%]  (Sampling)
#> Iteration: 900 / 1000 [ 90%]  (Sampling)
#> Iteration: 1000 / 1000 [100%]  (Sampling)
#> 
#>  Elapsed Time: 0.064669 seconds (Warm-up)
#>                0.056368 seconds (Sampling)
#>                0.121037 seconds (Total)
#> 
#> 
#> SAMPLING FOR MODEL '31f989ec0d689be2a4996807325a264b' NOW (CHAIN 3).
#> 
#> Gradient evaluation took 8e-06 seconds
#> 1000 transitions using 10 leapfrog steps per transition would take 0.08 seconds.
#> Adjust your expectations accordingly!
#> 
#> 
#> Iteration:   1 / 1000 [  0%]  (Warmup)
#> Iteration: 100 / 1000 [ 10%]  (Warmup)
#> Iteration: 200 / 1000 [ 20%]  (Warmup)
#> Iteration: 300 / 1000 [ 30%]  (Warmup)
#> Iteration: 400 / 1000 [ 40%]  (Warmup)
#> Iteration: 500 / 1000 [ 50%]  (Warmup)
#> Iteration: 501 / 1000 [ 50%]  (Sampling)
#> Iteration: 600 / 1000 [ 60%]  (Sampling)
#> Iteration: 700 / 1000 [ 70%]  (Sampling)
#> Iteration: 800 / 1000 [ 80%]  (Sampling)
#> Iteration: 900 / 1000 [ 90%]  (Sampling)
#> Iteration: 1000 / 1000 [100%]  (Sampling)
#> 
#>  Elapsed Time: 0.043916 seconds (Warm-up)
#>                0.029342 seconds (Sampling)
#>                0.073258 seconds (Total)
#> 
#> 
#> SAMPLING FOR MODEL '31f989ec0d689be2a4996807325a264b' NOW (CHAIN 4).
#> 
#> Gradient evaluation took 1.4e-05 seconds
#> 1000 transitions using 10 leapfrog steps per transition would take 0.14 seconds.
#> Adjust your expectations accordingly!
#> 
#> 
#> Iteration:   1 / 1000 [  0%]  (Warmup)
#> Iteration: 100 / 1000 [ 10%]  (Warmup)
#> Iteration: 200 / 1000 [ 20%]  (Warmup)
#> Iteration: 300 / 1000 [ 30%]  (Warmup)
#> Iteration: 400 / 1000 [ 40%]  (Warmup)
#> Iteration: 500 / 1000 [ 50%]  (Warmup)
#> Iteration: 501 / 1000 [ 50%]  (Sampling)
#> Iteration: 600 / 1000 [ 60%]  (Sampling)
#> Iteration: 700 / 1000 [ 70%]  (Sampling)
#> Iteration: 800 / 1000 [ 80%]  (Sampling)
#> Iteration: 900 / 1000 [ 90%]  (Sampling)
#> Iteration: 1000 / 1000 [100%]  (Sampling)
#> 
#>  Elapsed Time: 0.047749 seconds (Warm-up)
#>                0.039462 seconds (Sampling)
#>                0.087211 seconds (Total)
{% endhighlight %}



{% highlight r %}
> fit2 <- stan(fit = fit, data = schools_dat, iter = 10000, chains = 4)
{% endhighlight %}



{% highlight text %}
#> 
#> SAMPLING FOR MODEL '31f989ec0d689be2a4996807325a264b' NOW (CHAIN 1).
#> 
#> Gradient evaluation took 1.1e-05 seconds
#> 1000 transitions using 10 leapfrog steps per transition would take 0.11 seconds.
#> Adjust your expectations accordingly!
#> 
#> 
#> Iteration:    1 / 10000 [  0%]  (Warmup)
#> Iteration: 1000 / 10000 [ 10%]  (Warmup)
#> Iteration: 2000 / 10000 [ 20%]  (Warmup)
#> Iteration: 3000 / 10000 [ 30%]  (Warmup)
#> Iteration: 4000 / 10000 [ 40%]  (Warmup)
#> Iteration: 5000 / 10000 [ 50%]  (Warmup)
#> Iteration: 5001 / 10000 [ 50%]  (Sampling)
#> Iteration: 6000 / 10000 [ 60%]  (Sampling)
#> Iteration: 7000 / 10000 [ 70%]  (Sampling)
#> Iteration: 8000 / 10000 [ 80%]  (Sampling)
#> Iteration: 9000 / 10000 [ 90%]  (Sampling)
#> Iteration: 10000 / 10000 [100%]  (Sampling)
#> 
#>  Elapsed Time: 0.333191 seconds (Warm-up)
#>                0.385849 seconds (Sampling)
#>                0.71904 seconds (Total)
#> 
#> 
#> SAMPLING FOR MODEL '31f989ec0d689be2a4996807325a264b' NOW (CHAIN 2).
#> 
#> Gradient evaluation took 1.1e-05 seconds
#> 1000 transitions using 10 leapfrog steps per transition would take 0.11 seconds.
#> Adjust your expectations accordingly!
#> 
#> 
#> Iteration:    1 / 10000 [  0%]  (Warmup)
#> Iteration: 1000 / 10000 [ 10%]  (Warmup)
#> Iteration: 2000 / 10000 [ 20%]  (Warmup)
#> Iteration: 3000 / 10000 [ 30%]  (Warmup)
#> Iteration: 4000 / 10000 [ 40%]  (Warmup)
#> Iteration: 5000 / 10000 [ 50%]  (Warmup)
#> Iteration: 5001 / 10000 [ 50%]  (Sampling)
#> Iteration: 6000 / 10000 [ 60%]  (Sampling)
#> Iteration: 7000 / 10000 [ 70%]  (Sampling)
#> Iteration: 8000 / 10000 [ 80%]  (Sampling)
#> Iteration: 9000 / 10000 [ 90%]  (Sampling)
#> Iteration: 10000 / 10000 [100%]  (Sampling)
#> 
#>  Elapsed Time: 0.345219 seconds (Warm-up)
#>                0.344488 seconds (Sampling)
#>                0.689707 seconds (Total)
#> 
#> 
#> SAMPLING FOR MODEL '31f989ec0d689be2a4996807325a264b' NOW (CHAIN 3).
#> 
#> Gradient evaluation took 1.1e-05 seconds
#> 1000 transitions using 10 leapfrog steps per transition would take 0.11 seconds.
#> Adjust your expectations accordingly!
#> 
#> 
#> Iteration:    1 / 10000 [  0%]  (Warmup)
#> Iteration: 1000 / 10000 [ 10%]  (Warmup)
#> Iteration: 2000 / 10000 [ 20%]  (Warmup)
#> Iteration: 3000 / 10000 [ 30%]  (Warmup)
#> Iteration: 4000 / 10000 [ 40%]  (Warmup)
#> Iteration: 5000 / 10000 [ 50%]  (Warmup)
#> Iteration: 5001 / 10000 [ 50%]  (Sampling)
#> Iteration: 6000 / 10000 [ 60%]  (Sampling)
#> Iteration: 7000 / 10000 [ 70%]  (Sampling)
#> Iteration: 8000 / 10000 [ 80%]  (Sampling)
#> Iteration: 9000 / 10000 [ 90%]  (Sampling)
#> Iteration: 10000 / 10000 [100%]  (Sampling)
#> 
#>  Elapsed Time: 0.348285 seconds (Warm-up)
#>                0.395506 seconds (Sampling)
#>                0.743791 seconds (Total)
#> 
#> 
#> SAMPLING FOR MODEL '31f989ec0d689be2a4996807325a264b' NOW (CHAIN 4).
#> 
#> Gradient evaluation took 1.2e-05 seconds
#> 1000 transitions using 10 leapfrog steps per transition would take 0.12 seconds.
#> Adjust your expectations accordingly!
#> 
#> 
#> Iteration:    1 / 10000 [  0%]  (Warmup)
#> Iteration: 1000 / 10000 [ 10%]  (Warmup)
#> Iteration: 2000 / 10000 [ 20%]  (Warmup)
#> Iteration: 3000 / 10000 [ 30%]  (Warmup)
#> Iteration: 4000 / 10000 [ 40%]  (Warmup)
#> Iteration: 5000 / 10000 [ 50%]  (Warmup)
#> Iteration: 5001 / 10000 [ 50%]  (Sampling)
#> Iteration: 6000 / 10000 [ 60%]  (Sampling)
#> Iteration: 7000 / 10000 [ 70%]  (Sampling)
#> Iteration: 8000 / 10000 [ 80%]  (Sampling)
#> Iteration: 9000 / 10000 [ 90%]  (Sampling)
#> Iteration: 10000 / 10000 [100%]  (Sampling)
#> 
#>  Elapsed Time: 0.355314 seconds (Warm-up)
#>                0.375528 seconds (Sampling)
#>                0.730842 seconds (Total)
{% endhighlight %}



{% highlight r %}
> print(fit2)
{% endhighlight %}



{% highlight text %}
#> Inference for Stan model: 31f989ec0d689be2a4996807325a264b.
#> 4 chains, each with iter=10000; warmup=5000; thin=1; 
#> post-warmup draws per chain=5000, total post-warmup draws=20000.
#> 
#>           mean se_mean   sd   2.5%   25%   50%   75% 97.5% n_eff Rhat
#> mu        7.95    0.05 5.06  -1.73  4.67  7.84 11.11 18.32  9000    1
#> tau       6.49    0.07 5.56   0.22  2.39  5.13  9.08 20.48  7301    1
#> eta[1]    0.38    0.01 0.94  -1.52 -0.24  0.40  1.02  2.18 20000    1
#> eta[2]   -0.01    0.01 0.87  -1.70 -0.58 -0.01  0.56  1.74 20000    1
#> eta[3]   -0.20    0.01 0.93  -2.02 -0.82 -0.20  0.42  1.67 20000    1
#> eta[4]   -0.03    0.01 0.88  -1.76 -0.62 -0.04  0.54  1.71 20000    1
#> eta[5]   -0.34    0.01 0.88  -2.03 -0.92 -0.36  0.23  1.49 20000    1
#> eta[6]   -0.21    0.01 0.89  -1.93 -0.80 -0.22  0.37  1.59 20000    1
#> eta[7]    0.34    0.01 0.88  -1.48 -0.22  0.35  0.92  2.06 20000    1
#> eta[8]    0.06    0.01 0.94  -1.80 -0.56  0.07  0.70  1.90 20000    1
#> theta[1] 11.32    0.07 8.25  -2.04  5.90 10.19 15.47 31.17 12989    1
#> theta[2]  7.87    0.04 6.21  -4.41  4.02  7.83 11.72 20.50 20000    1
#> theta[3]  6.14    0.06 7.68 -11.14  2.12  6.71 10.79 20.25 17666    1
#> theta[4]  7.66    0.05 6.46  -5.23  3.72  7.65 11.58 20.78 20000    1
#> theta[5]  5.15    0.04 6.36  -8.80  1.35  5.66  9.41 16.35 20000    1
#> theta[6]  6.21    0.05 6.64  -8.44  2.33  6.51 10.45 18.77 20000    1
#> theta[7] 10.61    0.05 6.75  -1.25  6.04 10.04 14.52 25.78 20000    1
#> theta[8]  8.45    0.06 7.91  -7.29  3.90  8.20 12.70 25.51 16545    1
#> lp__     -4.89    0.03 2.66 -10.77 -6.48 -4.66 -3.03 -0.34  6407    1
#> 
#> Samples were drawn using NUTS(diag_e) at Sat Jan  6 21:31:55 2018.
#> For each parameter, n_eff is a crude measure of effective sample size,
#> and Rhat is the potential scale reduction factor on split chains (at 
#> convergence, Rhat=1).
{% endhighlight %}



{% highlight r %}
> plot(fit2)
{% endhighlight %}



{% highlight text %}
#> 'pars' not specified. Showing first 10 parameters by default.
{% endhighlight %}



{% highlight text %}
#> ci_level: 0.8 (80% intervals)
{% endhighlight %}



{% highlight text %}
#> outer_level: 0.95 (95% intervals)
{% endhighlight %}

![plot of chunk unnamed-chunk-7](/figure/./_source/2014-04-10-stan-introduction/unnamed-chunk-7-1.png)

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
