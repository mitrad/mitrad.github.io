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


```r
> install.packages('inline')
> install.packages('Rcpp')
```


```r
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
```

```
#> hello world
```

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

```r
> if (!file.exists("~/.R/Makevars")) {
+   cat('CXX=g++ -arch x86_64 -ftemplate-depth-256 -stdlib=libstdc++\n
+        CXXFLAGS="-mtune=native  -O3 -Wall -pedantic -Wconversion"\n', 
+        file="~/.R/Makevars");
+ } else {
+    file.show("~/.R/Makevars");
+ }
```

### rstan 인스톨

```r
> options(repos = c(getOption("repos"), rstan = "http://wiki.rstan-repo.googlecode.com/git/"))
> install.packages('rstan', type = 'source')
```

### rstan을 효율적으로 사용하기 위한 옵션 

```r
> library(rstan)
> set_cppo("fast")  # for best running speed
> set_cppo('debug') # make debug easier
```

## Example
[RStan Getting Started](https://github.com/stan-dev/rstan/wiki/RStan-Getting-Started#how-to-install-rstan)의 예제 1번을 따라해보고 제대로 실행 되는가 확인.


```r
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
```

```
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
#> #  Elapsed Time: 0.029327 seconds (Warm-up)
#> #                0.022298 seconds (Sampling)
#> #                0.051625 seconds (Total)
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
#> #  Elapsed Time: 0.03009 seconds (Warm-up)
#> #                0.030995 seconds (Sampling)
#> #                0.061085 seconds (Total)
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
#> #  Elapsed Time: 0.026121 seconds (Warm-up)
#> #                0.024596 seconds (Sampling)
#> #                0.050717 seconds (Total)
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
#> #  Elapsed Time: 0.026048 seconds (Warm-up)
#> #                0.018627 seconds (Sampling)
#> #                0.044675 seconds (Total)
```

```r
> fit2 <- stan(fit = fit, data = schools_dat, iter = 10000, chains = 4)
```

```
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
#> #  Elapsed Time: 0.389055 seconds (Warm-up)
#> #                0.38591 seconds (Sampling)
#> #                0.774965 seconds (Total)
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
#> #  Elapsed Time: 0.354642 seconds (Warm-up)
#> #                0.280653 seconds (Sampling)
#> #                0.635295 seconds (Total)
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
#> #  Elapsed Time: 0.339721 seconds (Warm-up)
#> #                0.269958 seconds (Sampling)
#> #                0.609679 seconds (Total)
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
#> #  Elapsed Time: 0.344101 seconds (Warm-up)
#> #                0.383891 seconds (Sampling)
#> #                0.727992 seconds (Total)
```

```r
> print(fit2)
```

```
#> Inference for Stan model: schools_code.
#> 4 chains, each with iter=10000; warmup=5000; thin=1; 
#> post-warmup draws per chain=5000, total post-warmup draws=20000.
#> 
#>           mean se_mean   sd   2.5%   25%   50%   75% 97.5% n_eff Rhat
#> mu        7.43    0.37 6.02  -5.33  4.35  7.75 11.09 17.88   262 1.01
#> tau       7.20    0.49 7.07   0.24  2.54  5.40  9.53 25.90   207 1.02
#> eta[1]    0.41    0.01 0.93  -1.50 -0.19  0.44  1.05  2.15  8627 1.00
#> eta[2]    0.02    0.01 0.88  -1.71 -0.56  0.03  0.60  1.76 11158 1.00
#> eta[3]   -0.18    0.01 0.94  -2.00 -0.81 -0.18  0.44  1.68  9981 1.00
#> eta[4]   -0.02    0.01 0.89  -1.81 -0.60 -0.02  0.56  1.74  9862 1.00
#> eta[5]   -0.35    0.01 0.87  -2.02 -0.93 -0.36  0.22  1.44  6432 1.00
#> eta[6]   -0.19    0.01 0.88  -1.95 -0.78 -0.20  0.38  1.53 10352 1.00
#> eta[7]    0.36    0.01 0.89  -1.46 -0.21  0.37  0.94  2.08  9632 1.00
#> eta[8]    0.06    0.01 0.93  -1.78 -0.56  0.08  0.67  1.86 10567 1.00
#> theta[1] 11.60    0.14 8.49  -1.84  6.08 10.38 15.72 31.84  3678 1.00
#> theta[2]  7.90    0.06 6.41  -4.88  3.90  7.84 11.82 20.90 11006 1.00
#> theta[3]  5.99    0.10 8.01 -12.40  1.89  6.58 10.82 20.67  6588 1.00
#> theta[4]  7.47    0.08 6.73  -6.23  3.37  7.49 11.69 20.78  7732 1.00
#> theta[5]  4.96    0.07 6.51  -9.74  1.13  5.51  9.27 16.55  9122 1.00
#> theta[6]  5.97    0.07 6.87  -9.33  1.93  6.43 10.41 18.60  9389 1.00
#> theta[7] 10.71    0.07 6.80  -1.27  6.12 10.09 14.71 26.24  9608 1.00
#> theta[8]  8.21    0.15 8.16  -8.72  3.60  8.03 12.74 25.98  3094 1.00
#> lp__     -4.84    0.05 2.69 -10.79 -6.46 -4.62 -2.94 -0.19  2526 1.00
#> 
#> Samples were drawn using NUTS(diag_e) at Tue Jul 21 23:45:33 2015.
#> For each parameter, n_eff is a crude measure of effective sample size,
#> and Rhat is the potential scale reduction factor on split chains (at 
#> convergence, Rhat=1).
```

```r
> plot(fit2)
```

![plot of chunk unnamed-chunk-7](/figure/./2014-04-10-stan-introduction/unnamed-chunk-7-1.png) 

```r
> la <- extract(fit2, permuted = TRUE) # return a list of arrays 
> mu <- la$mu 
> 
> ### return an array of three dimensions: iterations, chains, parameters 
> a <- extract(fit2, permuted = FALSE) 
> 
> ### use S3 functions as.array (or as.matrix) on stanfit objects
> a2 <- as.array(fit2)
> m <- as.matrix(fit2)
```
