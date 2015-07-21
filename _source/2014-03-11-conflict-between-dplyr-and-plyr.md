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


```r
> library(dplyr)
```

```
#> 
#> Attaching package: 'dplyr'
#> 
#> The following objects are masked from 'package:stats':
#> 
#>     filter, lag
#> 
#> The following object is masked from '.startup':
#> 
#>     n
#> 
#> The following objects are masked from 'package:base':
#> 
#>     intersect, setdiff, setequal, union
```

```r
> iris %.% group_by(Species) %.% summarise(count=length(Species))
```

```
#> Warning: '%.%' is deprecated.
#> Use '%>%' instead.
#> See help("Deprecated")
```

```
#> Warning: '%.%' is deprecated.
#> Use '%>%' instead.
#> See help("Deprecated")
```

```
#> Source: local data frame [3 x 2]
#> 
#>      Species count
#> 1     setosa    50
#> 2 versicolor    50
#> 3  virginica    50
```

```r
> library(plyr)
```

```
#> -------------------------------------------------------------------------
#> You have loaded plyr after dplyr - this is likely to cause problems.
#> If you need functions from both plyr and dplyr, please load plyr first, then dplyr:
#> library(plyr); library(dplyr)
#> -------------------------------------------------------------------------
#> 
#> Attaching package: 'plyr'
#> 
#> The following objects are masked from 'package:dplyr':
#> 
#>     arrange, count, desc, failwith, id, mutate, rename, summarise,
#>     summarize
```

```r
> iris %.% group_by(Species) %.% summarise(count=length(Species))
```

```
#> Warning: '%.%' is deprecated.
#> Use '%>%' instead.
#> See help("Deprecated")
```

```
#> Warning: '%.%' is deprecated.
#> Use '%>%' instead.
#> See help("Deprecated")
```

```
#>   count
#> 1   150
```

**plyr** 패키지를 `detach` 한 후 다시 확인하면 


```r
> detach(package:plyr)
> iris %.% group_by(Species) %.% summarise(count=length(Species))
```

```
#> Warning: '%.%' is deprecated.
#> Use '%>%' instead.
#> See help("Deprecated")
```

```
#> Warning: '%.%' is deprecated.
#> Use '%>%' instead.
#> See help("Deprecated")
```

```
#> Source: local data frame [3 x 2]
#> 
#>      Species count
#> 1     setosa    50
#> 2 versicolor    50
#> 3  virginica    50
```

편리하다고 이것저것 불러다 쓰다 피 볼 수 있다...
