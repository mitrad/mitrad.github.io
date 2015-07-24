---
layout: post
title: "Windows 7에 일본어 LaTeX 환경 만들기"
date: 2014-3-12
categories: LaTeX
tags: [LaTeX, Sublime Text 3, 일본어]
comment: true
---

나이를 먹어감에 급속히 가속되는 기억의 휘발성을 위해 Windows 7 + 일본어 LaTeX + Sublime Text 3을 이용한 조판 시스템을 만드는 방법을 기록해 두기로 한다.


### 환경
* OS: 일본어 MS Windows 7 
* Editor: Sublime Text 3
* TeX Live 2013

### TeX Live install
[TeX Users Group](http://www.tug.org)의 [TeX Live](http://www.tug.org/texlive/)를 다운로드 하여 설치

### Sublime Text 3 환경 설정
* LaTeXTools install
  1. `Ctl + shift + p`를 입력하여 **Install Package**를 선택
  2. **LaTeXTools**를 설치

* 컴파일 스크립트 편집
  1. `Preferences` -> `Browse Packages`를 선택하여 _/LaTeXTools/LaTeX.sublime-build_를 편집
  2. // *** BEGIN MikTeX 2009 *** 부터 // *** END MikTeX 2009 *** 까지 전부 주석 처리
  3. 다음 코드를 추가
{% highlight text %}
"cmd" : ["latexmk",
  "-e","\\$latex = 'platex %O %S'",
  "-e","\\$dvipdf = 'dvipdfmx %O %S'",
  "-f",
  "-pdfdvi",
  "-silent"
  ],
{% endhighlight %}

### Windows 설정
* latexmk 설정
latexmk은 TeX 문서 빌드를 자동화해주는 도구로 최근 TeX 배포판에는 표준으로 들어가 있다. 하지만 이를 이용하기 위해서는 설정이 필요함. 
latexmk 설정은 Sublime Text 3 뿐 아니라 TeX 전용 에디터를 사용할 때도 이용하게 되므로 한 번 해두면 편리하다.
홈 디렉토리 _C:\Users\username_ 에 `.latexmkrc` 라는 파일을 만들어 다음을 입력한다.
{% highlight text %}
$latex         = 'platex -src-specials -shell-escape -interaction=nonstopmode -synctex=1 -kanji=utf8 -guess-input-enc %O %S';
$bibtex        = 'pbibtex %O %B';
$dvipdf        = 'dvipdfmx %O %S';
$pdf_mode      = 3; # use dvipdf
$pdf_previewer = 'SumatraPDF.exe -reuse-instance';
{% endhighlight %}


