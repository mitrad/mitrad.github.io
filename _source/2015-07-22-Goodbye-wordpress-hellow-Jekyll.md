---
layout: post
title: "Wordpress에서 Jekyll로..."
date: 2015-07-26
categories: R
tags: [R, servr, knitr, Jekyll]
comments: true
---



## 시작하며

약 10년을 써오던 설치형 [워드프레스](http://wordpress.org)를 버리고 [Github Pages](https://pages.github.com)에서 제공하는 [Jekyll](http://jekyllrb.com) 기반의 블로그로 이동하였다. 많은 이들이 그러하듯 워드프레스의 무거움과 관리의 귀찮음이 워드프레스에서 Jekyll로 이동하는데 결정적인 몫을 하였다. 게다가 거의 모든 글을 markdown으로 작성하고 있고 git도 매일같이 사용하는 도구이기에 이것들을 익히기 위한 학습시간이 불필요하다는 이점도 있고. 

Github Pages와 Jekyll을 이용하여 블로깅을 하는 방법과 장점에 대해서는 인터넷에 잔뜩 널려있으니[^fn-1] 여기서는 자세한 설명을 생략하기로 하고 이 블로그의 주된 주제인 데이터 분석, 특히 R 코드 및 수식이 들어간 글을 쉽게 Jekyll 문법에 맞게 포스팅하는 방법에 대해 정리해보자.

이동 전까지는 R 코드가 포함된 글을 [RStudio](http://www.rstudio.com)에서 rmarkdown을 이용해 작성 -> html 변환 -> 워드프레스 내장 에디터에서 최종 편집하는 과정을 거쳤다. 하지만

* `.Rmd` 파일과 블로그에 올라가는 글과는 차이가 있다.
* 글을 수정하려면 다시 앞 과정을 반복해야 한다. 
* 글의 버전 관리(특히 .Rmd 파일)가 어렵다.

는 불편함이 있었다. 

그동안 원래 글들을 Jekyll로 이주만 시켜 놓고 오랫동안 블로그를 내버려두고 있었는데 얼마 전 R의 [servr + knitr 패키지를 이용해 Jekyll 사이트 만드는 방법](http://yihui.name/knitr-jekyll/2014/09/jekyll-with-knitr.html)을 알게 되어 이를 이용한다. 


## servr + knitr 패키지를 이용해 Jekyll 사이트 만들기

사용방법은 두 패키지를 설치한 다음 


{% highlight r %}
> install.packages(c("knitr", "servr"))
{% endhighlight %}

자기 컴퓨터의 Jekyll 사이트가 저장된 디렉토리를 작업디렉토리로 설정한 후 R 콘솔에서


{% highlight r %}
> servr::jekyll()
{% endhighlight %}
을 실행하면 자기 컴퓨터에 가상의 Jekyll 사이트를 빌드해 준다. 이때 함수 `jekyll()`에 아무 값도 지정해 주지 않는다면 

1.*_source* 디렉토리에 있는 `.Rmd` 파일을 `.md` 파일로 변환
2. 변환된 `.md` 파일을 *_posts* 디렉토리로 이동
3. 그래프는 *figs/source/* 에 저장 
4. Jekyll 사이트 빌드

의 과정을 거치게 된다. 모든 과정이 오류없이 진행되었다면 github pages에 commit 하는 것으로 블로그 포스팅이 완료된다. 

## 참고

###  Mathjax을 이용한 수식표시

일반적으로 Jekyll을 사용하는 많은 이들이 `redcarpet`을 markdown 엔진으로 사용하는데 포스트에 가끔 들어가는 [Mathjax](https://www.mathjax.org)을 이용한 `$$ 수식 $$`이 제대로 표시되지 않는 문제가 있다. 대안으로 `kramdown`을 사용하면 그 문제가 해결되는데 다만 세 개의 {% raw %} backticks ```로 {% endraw %}  시작하는 코드 삽입을 할 수 없고 {% raw %} `{% highlight lang %} {% endhighlight %} {% endraw %}` 으로 코드의 시작과 끝을 알려 주어야 한다. 친절하게도 이에 대한 해결 방법도 제공해 주어서 다음과 같은 내용의 `build.R` 파일을 작성하여 Jekyll 사이트의 루트 디렉토리에 저장하고 `servr::jekyll()`을 실행하면 된다. 


{% highlight r %}
## Serve Jekyll Websites with servr and knitr
## from http://yihui.name/knitr-jekyll/2014/09/jekyll-with-knitr.html

local({
  # fall back on '/' if baseurl is not specified
  baseurl = servr:::jekyll_config('.', 'baseurl', '/')
  knitr::opts_knit$set(base.url = baseurl)
  # fall back on 'kramdown' if markdown engine is not specified
  markdown = servr:::jekyll_config('.', 'markdown', 'kramdown')
  # see if we need to use the Jekyll render in knitr
  if (markdown == 'kramdown') {
    knitr::render_jekyll()
  } else knitr::render_markdown()

  # input/output filenames are passed as two additional arguments to Rscript
  a = commandArgs(TRUE)
  d = gsub('^_|[.][a-zA-Z]+$', '', a[1])
  knitr::opts_chunk$set(
    fig.path   = sprintf('figs/%s/', d),
    cache.path = sprintf('cache/%s/', d)
  )
  knitr::opts_knit$set(width = 70)
  knitr::knit(a[1], a[2], quiet = TRUE, encoding = 'UTF-8', envir = .GlobalEnv)
})
{% endhighlight %}

### Python 3.x와의 충돌

만약 자기 컴퓨터(내 경우는 Mac)에 Python 3.x 버전을 사용 중이고 [Github Pages Jekyll 세팅과 가장 근사한 환경 만들기](https://help.github.com/articles/using-jekyll-with-pages/)로 자기 컴퓨터에 Jekyll을 설치한 경우에 `jekyll server`로 로컬 서버를 기동하려 하면 

```
Liquid Exception: No header received back 
```

라는 에러를 뱉어낸다. 이는 Github pages에서 사용하는 `Pygments`라는 syntax highlighter가 [Python2.7 기반이기 때문](https://github.com/jekyll/jekyll/issues/1181)이라 한다. 해결방법은 [복수 Python 버전을 사용하는 방법](http://wsyang.com/2015/07/19/hellow-python/)을 이용해 Python2 환경으로 만들어 주어야 하는데 만약 [pyenv](https://github.com/yyuu/pyenv)을 이용하고 있다면 Jekyll이 설치된 디렉토리에서

```
# 현재 python 버전 확인
$ python --version
Python 3.4.3 :: Anaconda 2.2.0 (x86_64)

# 현재 디렉토리 및 하위 디렉토리에 사용할 python 버전 변경 
$ pyenv local system

$ python --version
Python 2.7.6
```
로 Python 버전을 변경하고 `jekyll server`를 기동한다. 


[^fn-1]: 추천링크-[난 10년된 워드프레스에서 Jekyll로 마이그레이션](http://ilmol.com/2015/01/워드프레스에서%20Jekyll로%20마이그레이션.html)과 [Using Jekyll with Github Pages](https://help.github.com/articles/using-jekyll-with-pages/) 
