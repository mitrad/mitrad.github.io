---
layout: post
title: "Mac OS X에 Python 개발환경 만들기"
date: 2015-07-19
comment: true
---

평소에 R 만 쓰다보니 점점 지식이 편협해 지는 것 같아 Python을 해보려 마음먹었다.

나중에 까먹을 것이 분명하므로 일단 Mac에서 수치계산 및 머닝러신을 실행할 수 있는 환경을 구축하는 순서를 정리한다.

* 이하 환경에서 정상 작동확인
    * OS X Yosemite (10.10.4)


## 설치 순서

### Homebrew 설치

Ubuntu의 apt-get과 같이 Mac에서 패키지 관리를 편하게 해주는 Homebrew를 설치

{% highlight ruby%}
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
{% endhighlight %}

### Python의 버전 관리 툴 pyenv 설치

Python의 버전 2.xx과 3.xx의 사양은 큰 차이가 있다고 하나 자세한 것은 잘 모르므로 인터프리터 및 버전을 손쉽게 스위칭할 수 있도록 pyenv를 이용한다.

{% highlight text %}
$ brew install pyenv
{% endhighlight %}

다음에 PATH를 설정

`.bash_profile`에 다음 설정을 추가

{% highlight bash %}
export PATH="$HOME/.pyenv/shims:$PATH"
{% endhighlight%}

### Anaconda 설치

[Anaconda](https://store.continuum.io/cshop/anaconda/)는 Python의 수치계산환경을 구축하기 위한 여러 패키지를 모은 무료 배포판이다. Anaconda를 이용하면 NumPy, SciPy, matplotlib은 물론 기계학습라이브러리 scikit-learn등의 패키지도 설치할 수 있다.

다음 명령어로 설치가능한 Python 버전을 확인할 수 있다.

{% highlight text %}
$ pyenv install -l
{% endhighlight %}

{% highlight text %}
Available versions:

  2.1.3

  2.2.3

  ...

  anaconda3-2.2.0

  ...

  stackless-3.4.1
{% endhighlight %}

출력된 리스트 중 2015/07/19 현재 최신 버젼인 Anaconda(anaconda3-2.2.0)을 다음 명령어로 설치한다.
참고로 anaconda2_x.x.x은 Python 2 계열, anaconda2_x.x.x은 Pythone 3 계열을 의미한다.

{% highlight text %}
$ pyenv install anaconda3-2.2.0
$ pyenv rehash
{% endhighlight %}

{% highlight text %}
$ pyenv versions
* system
  anaconda3-2.2.0 (set by /Users/username/.python-version)
{% endhighlight %}

필수는 아니지만 **pyenv-pip-rehash**를 설치한다. pyenv으로 Python 환경을 스위칭할때 `pyenv rehash`를 자동으로 실행시켜 준다.

{% highlight text %}
# pyenv를 Homebrew를 통해 설치한 경우
$ brew install homebrew/boneyard/pyenv-pip-rehash
{% endhighlight %}

{% highlight text %}
# 기본 설정 상태
$ python --versions
Python 2.7.6

# 버전 스위칭
# 사용할 Python 설정
$ pyenv global anaconda3-2.2.0

# 현재 Python 버전 확인
$ pyenv version
anaconda3-2.2.0 (set by /Users/username/.python-version)

# pyenv-pip-rehash를 설치하지 않은 경우에는
# 버전 스위칭 후 다음 명령어를 반드시 실행
$ pyenv rehash
{% endhighlight %}

Anaconda의 업데이트는 다음 명령어로 실행한다.

{% highlight text %}
$ conda update conda
{% endhighlight %}

마지막으로 python을 실행하여 NumPy, SciPy, matplotlib의 테스트를 실행한다.

{% highlight python %}
>>> import numpy
>>> numpy.test('full')
>>> import scipy
>>> scipy.test('full')
>>> import matplotlib
>>> matplotlib.test()
{% endhighlight %}

scikit-learn의 테스트는 bash에서

{% highlight text %}
$ nosetests --exe sklearn
{% endhighlight %}

마지막에 다음과 같이 "OK" 메시지가 떨어지면 문제 없이 동작한다.

{% highlight text %}
OK (KNOWNFAIL=6, SKIP=8)
{% endhighlight %}

